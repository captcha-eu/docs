# Frequently Asked Questions

## What is a bot? 🤖

A bot is an automated process that submits your forms or executes actions on websites. The goals of bots might include:

- **DDoS attacks** - [Learn more on Wikipedia](https://en.wikipedia.org/wiki/Denial-of-service_attack)
- **Gaming systems** - Enhance chances of winning sweepstakes
- **Data manipulation** - Skew poll results or reviews
- **Content scraping** - Search engines and crawlers

Bot techniques vary greatly, from simple scripts to sophisticated systems that mimic human behavior and attempt to evade detection.

**Note**: Search engine bots are the exception - they are generally considered "good" bots but are still automated systems.

For more details, see our [machine learning approach to bot detection](https://www.captcha.eu/technology).

## GDPR Compliance 🇪🇺

As a European company, we strictly adhere to GDPR regulations. Our privacy-first approach includes:

### Data Minimization
- Collect only data necessary for bot classification
- No long-term storage of personal information
- Process data in real-time when possible

### Privacy Features
- **No cookies or localStorage** - Zero tracking mechanisms
- **No cross-page tracking** - Each request is independent  
- **Client-side anonymization** - Behavioral data is anonymized before transmission
- **IP address anonymization** - Connection data is stored with reduced precision
  - IPv4 example: `8.8.8.8` → `8.8.*.*`
  - IPv6 example: `2001:db8:3333:4444:5555:6666:7777:8888` → `2001:db8:3333:4444:5555:6666:*:*`

## Bot Detection Methods

We use multiple detection layers to identify automated traffic. All data is anonymized before storage:

### Detection Techniques
- **Browser Classification** - Client-side environment analysis
- **Behavioral Analysis** - Mouse movement patterns and keyboard timing
- **System Timing** - Performance characteristics of different components  
- **Machine Learning** - AI-generated likelihood-to-be-human scoring

All detection happens in real-time with minimal data collection to maintain user privacy while ensuring effective protection.