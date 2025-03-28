# REST API Documentation

This guide explains how to use the Captcha.eu REST API to fetch statistics for your domains.

## Authentication

All API requests require authentication using a REST API key. You'll need:
- A domain-specific REST key for single domain queries
- A parent REST key for queries across all domains

## Fetching Statistics for a Single Domain

Get usage statistics for a specific domain using its REST key.

### Request Format

- **Endpoint:** `https://www.captcha.eu/api/stats`
- **Method:** GET
- **Headers:** 
  - `Rest-Key: <YOUR_DOMAIN_REST_KEY>`
- **Query Parameters:**
  - `ym` (optional): Month and year in format `MM-YYYY` (e.g., `03-2024`)
  - `category` (optional): Filter results by category

### Example Request

```sh
curl --location 'https://www.captcha.eu/api/stats?ym=03-2024' \
--header 'Rest-Key: <YOUR_DOMAIN_REST_KEY>'
```

### Response Format

```json
{
    "Period": null,
    "History": [
        {
            "category": "(global)",
            "month": 3,
            "value": 28,
            "year": 2025
        }
    ]
}
```

## Fetching Statistics for All Domains

Get usage statistics across all domains in your account using the parent REST key.

### Request Format

- **Endpoint:** `https://www.captcha.eu/api/stats-per-customer`
- **Method:** GET
- **Headers:** 
  - `Rest-Key: <YOUR_PARENT_REST_KEY>`

### Example Request

```sh
curl --location 'https://www.captcha.eu/api/stats-per-customer' \
--header 'Rest-Key: <YOUR_PARENT_REST_KEY>'
```

### Response Format

```json
{
    "History": [
        {
            "domain": "www.yyyyy.com",
            "month": 11,
            "value": 817,
            "websiteId": "yyyy",
            "year": 2024
        },
        {
            "domain": "www.xxx.com",
            "month": 10,
            "value": 629,
            "websiteId": "xxx",
            "year": 2024
        }
    ]
}
```

## Notes

- If the `ym` parameter is not provided, the API will return statistics for all months
- The `category` parameter can be used to filter results for specific categories
- All responses are in JSON format
- The `value` field in responses represents the number of requests/captchas processed
