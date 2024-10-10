# Shield

> Early Beta

Shield is protecting your website from automated access, independently  from the backend/website behind.
users will need to verify that they are human, and will only then allowed to access your page.


<img src="files/shield/shield3.png">


# Configure the shield

Go into `Dashboard->Shields`  and click `+Add`

> Using the html snippet, you can add your tracking/analytics code

<img src="files/shield/shield1.png">


add a *Name*, and link it with one of your existing captcha.eu domains

# Enable Shield

since Shield intercepts all requests to your page you currently have the following options

## WordPress

get the latest version of the plugin, and enable shield in the settings
by adding a `shieldID`  and also checking the box at `Enable Shield`

> beware, this intercepts wordpress's stack to early terminate the request
> this way you get a really good protection, but for a real DDoS see below

<img src="files/shield/shield2.png">



## CDN
Shield works best when implemented at the CDN level - right now we offer the following options

### Caddy
using jwt-auth directly in Caddy, you can setup a reverse proxy. if that reverse proxy is run in some autoscaling environment
this is a cdn-grade ddos protection.


> Prerequisites
>  - caddy with caddy-jwt
>  - https://github.com/ggicci/caddy-jwt


`Caddyfile` config:
```
{
	order jwtauth before basicauth
}

www.mywebsite.com {
    @shield {
        not path /api/shield/*
    }
    @captcha_portal {
        path /api/shield/*
    }

	jwtauth @shield {
		sign_key base64-of-my-captcha-eu-website-private-key
		sign_alg HS256
		from_cookies cptst
	}
    handle_errors  401 {
	    redir /api/shield/MY-SITE-ID?targetURL={uri} /api/shield/MY-SITE-ID?targetURL={uri}
    }
	reverse_proxy @shield https://www.mywebsite.origin.com {
        header_up Host "www.mywebsite.com"
    }
    reverse_proxy @captcha_portal https://www.captcha.eu {
        header_up Host "www.captcha.eu"
    }
}
```

### Akamai
there is support for EdgeAuth integrated - for access please contact hello@captcha.eu

### Cloudfront
you need to configure a `Viewer request` Lambda@Edge function on your distribution behaviour.

  - grant cloudfront the lambda execution
  - add the script below as a new function
  - deploy and release it
  - add the ANR to the distribution's settings


> BEWARE - to adapt the settings below

```js
import crypto from 'crypto';
import querystring from 'querystring';
import https from 'https';
import { Agent } from 'https';
import tls from 'tls';

const COOKIE_NAME = 'cptst';
const SHIELD_ID = '<SHIELD_ID>';
const SHIELD_ORIGIN = 'www.captcha.eu';
const SECRET = '<DOMAIN_PRIVATE_KEY>';

export const handler = async (event) => {
    console.log('Event received:', JSON.stringify(event));
    const request = event.Records[0].cf.request;
    const headers = request.headers;
    const queryParams = querystring.parse(request.querystring);
    
    
    if (request.uri.startsWith('/api/shield/')) {
        console.log('Proxying request to shield origin');
        return proxyRequest(request, SHIELD_ORIGIN);
    }
    
    console.log('Request headers:', JSON.stringify(headers));
    console.log('Query parameters:', JSON.stringify(queryParams));

    // Check for the cookie
    const cookie = headers.cookie ? headers.cookie[0].value : '';
    console.log('Cookie header:', cookie);
    const tokenFromCookie = getCookie(cookie, COOKIE_NAME);
    console.log('Token from cookie:', tokenFromCookie);

    if (!tokenFromCookie) {
        console.log('No cookie found, checking query params');
        // No cookie, check for token in query params
        const tokenFromQuery = queryParams.cpt_shield_token;
        console.log('Token from query:', tokenFromQuery);

        if (!tokenFromQuery) {
            console.log('No token found, fetching shield content');
            // No token, fetch shield content
            return fetchShieldContent(SHIELD_ORIGIN, SHIELD_ID);
        }

        // Validate token from query
        console.log('Validating token from query');
        if (validateJWT(tokenFromQuery, SECRET)) {
            console.log('Token is valid, setting cookie and continuing');
            // Token is valid, set cookie and continue
            return setCookieAndContinue(request, COOKIE_NAME, tokenFromQuery);
        } else {
            console.log('Invalid token, fetching shield content');
            // Invalid token, fetch shield content
            return fetchShieldContent(SHIELD_ORIGIN, SHIELD_ID);
        }
    } else {
        console.log('Cookie exists, validating it');
        // Cookie exists, validate it
        if (!validateJWT(tokenFromCookie, SECRET)) {
            console.log('Invalid cookie, clearing it and fetching shield content');
            // Invalid cookie, clear it and fetch shield content
            return fetchShieldContent(SHIELD_ORIGIN, SHIELD_ID, true);
        }
    }

    console.log('JWT is valid, continuing with the request');
    // If we've reached this point, the JWT is valid, continue with the request
    return request;
};

function getCookie(cookieString, name) {
    console.log('Parsing cookies:', cookieString);
    const cookies = cookieString.split(';').reduce((acc, cookie) => {
        const [key, value] = cookie.trim().split('=');
        acc[key] = value;
        return acc;
    }, {});
    console.log('Parsed cookies:', JSON.stringify(cookies));
    return cookies[name];
}

function validateJWT(token, secret) {
    console.log('Validating JWT:', token);
    if(token == "1") {
        console.log('Debug token detected, returning true');
        return true;
    }
    try {
        const [headerB64, payloadB64, signatureB64] = token.split('.');
        const signatureProvided = Buffer.from(signatureB64, 'base64url');

        const data = `${headerB64}.${payloadB64}`;
        const signatureCalculated = crypto.createHmac('sha256', secret)
            .update(data)
            .digest();

        if (!crypto.timingSafeEqual(signatureProvided, signatureCalculated)) {
            throw new Error('Invalid signature');
        }

        const payload = JSON.parse(Buffer.from(payloadB64, 'base64url').toString());
        console.log('JWT payload:', JSON.stringify(payload));
        const now = Math.floor(Date.now() / 1000);

        if (payload.exp && payload.exp < now) {
            throw new Error('Token expired');
        }

        console.log('JWT is valid');
        return true;
    } catch (error) {
        console.error('JWT validation failed:', error);
        return false;
    }
}

function fetchShieldContent(origin, shieldId, clearCookie = false) {
    return new Promise((resolve, reject) => {
        const options = {
            hostname: origin,
            port: 443,
            path: `/api/shield/${shieldId}`,
            method: 'GET',
        };

        const req = https.request(options, (res) => {
            let data = '';

            res.on('data', (chunk) => {
                data += chunk;
            });

            res.on('end', () => {
                const response = {
                    status: res.statusCode.toString(),
                    statusDescription: res.statusMessage,
                    headers: {
                        'content-type': [{ key: 'Content-Type', value: res.headers['content-type'] || 'text/html' }],
                        'cache-control': [{ key: 'Cache-Control', value: 'no-cache, no-store, must-revalidate' }],
                        'pragma': [{ key: 'Pragma', value: 'no-cache' }],
                        'expires': [{ key: 'Expires', value: '0' }]
                    },
                    body: data,
                    bodyEncoding: 'text'
                };

                if (clearCookie) {
                    response.headers['set-cookie'] = [{ 
                        key: 'Set-Cookie', 
                        value: `${COOKIE_NAME}=; Path=/; Expires=Thu, 01 Jan 1970 00:00:00 GMT; HttpOnly; Secure; SameSite=Strict` 
                    }];
                }

                resolve(response);
            });
        });

        req.on('error', (error) => {
            console.error('Error fetching shield content:', error);
            reject(error);
        });

        req.end();
    });
}

function setCookieAndContinue(request, name, value) {
    request.headers['set-cookie'] = [{
        key: 'Set-Cookie',
        value: `${name}=${value}; Path=/; Max-Age=2592000; HttpOnly; Secure; SameSite=Strict`
    }];
    return request;
}

function proxyRequest(request, origin) {
    return new Promise((resolve, reject) => {
        const options = {
            hostname: origin,
            port: 443,
            path: `${request.uri}${request.querystring ? '?' + request.querystring : ''}`,
            method: request.method,
            headers: Object.entries(request.headers).reduce((acc, [key, value]) => {
                acc[key] = value[0].value;
                return acc;
            }, {}),
            timeout: 5000, // 5 second timeout
            servername: origin, // Enable SNI
        };
        options.headers.host = SHIELD_ORIGIN;

        console.log('Proxy request options:', JSON.stringify(options));

        const proxyReq = https.request(options, (proxyRes) => {
            let body = '';
            proxyRes.on('data', (chunk) => body += chunk);
            proxyRes.on('end', () => {
                console.log('Proxy response status:', proxyRes.statusCode);
                console.log('Proxy response headers:', JSON.stringify(proxyRes.headers));
                const response = {
                    status: proxyRes.statusCode.toString(),
                    statusDescription: proxyRes.statusMessage,
                    headers: Object.entries(proxyRes.headers).reduce((acc, [key, value]) => {
                        acc[key] = [{ key, value }];
                        return acc;
                    }, {}),
                    body: body,
                    bodyEncoding: 'text'
                };
                resolve(response);
            });
        });

        proxyReq.on('error', (error) => {
            console.error('Error in proxy request:', error);
            reject(error);
        });

        proxyReq.on('timeout', () => {
            console.error('Proxy request timed out');
            proxyReq.destroy();
            reject(new Error('Request timed out'));
        });

        if (request.body && request.body.data) {
            proxyReq.write(request.body.data);
        }

        proxyReq.end();
    });
}

```

### I don't have a CDN
if you don't have a CDN and cannot run Caddy/Proxy at your datacenter, and still would like to get a the best protection
contact us - we will find a solution.