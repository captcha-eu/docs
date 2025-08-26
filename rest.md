# REST API Documentation

This guide explains how to use the Captcha.eu REST API to fetch statistics for your domains.

## Table of Contents

- [Authentication](#authentication)
- [Fetching Statistics for a Single Domain](#fetching-statistics-for-a-single-domain)
- [Fetching Statistics for All Domains](#fetching-statistics-for-all-domains)
- [Fetching Real-time Statistics](#fetching-real-time-statistics)
- [Notes](#notes)

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

## Fetching Real-time Statistics

Get detailed real-time statistics for recent time periods (minutes, hours, or days) showing accepted, rejected, and blocked requests.

### Request Format

- **Endpoint:** `https://www.captcha.eu/api/stats-per-customer/{timeframe}`
- **Method:** GET
- **Headers:** 
  - `Rest-Key: <YOUR_REST_KEY>`
- **Path Parameters:**
  - `timeframe` (required): Time period in format `{number}{unit}`
    - Units: `m` (minutes), `h` (hours), `d` (days)
    - Examples: `60m`, `2h`, `24h`, `7d`
- **Query Parameters:**
  - `domain` (optional): Filter by domain ID or `all`
  - `category` (optional): Filter by category name

### Example Requests

```sh
# Last 60 minutes
curl --location 'https://www.captcha.eu/api/stats-per-customer/60m' \
--header 'Rest-Key: <YOUR_REST_KEY>'

# Last 24 hours with category filter
curl --location 'https://www.captcha.eu/api/stats-per-customer/24h?category=global' \
--header 'Rest-Key: <YOUR_REST_KEY>'

# Last 7 days
curl --location 'https://www.captcha.eu/api/stats-per-customer/7d' \
--header 'Rest-Key: <YOUR_REST_KEY>'
```

### Response Format

```json
{
  "timeframe": "24h",
  "start_time": 1756105877,
  "end_time": 1756192277,
  "summary": {
    "challenges": {
      "served": 1243,
      "blocked": 1
    },
    "validations": {
      "success": 1144,
      "failed": 163
    }
  },
  "hourly_data": [
    {
      "date": "2025-08-26",
      "hour": 14,
      "timestamp": 1756166400,
      "metrics": {
        "challenges": {
          "served": 325,
          "blocked": 22
        },
        "validations": {
          "success": 313,
          "failed": 12
        }
      }
    }
  ]
}
```

### Response Fields

- `timeframe`: The requested time period
- `start_time`: Unix timestamp of the start of the period
- `end_time`: Unix timestamp of the end of the period
- `summary`: Aggregated totals for the entire timeframe
  - `challenges.served`: Total challenges presented to users
  - `challenges.blocked`: Total challenges blocked (bots detected)
  - `validations.success`: Total successful validations (accepted)
  - `validations.failed`: Total failed validations (rejected)
- `hourly_data`: Array of hourly breakdowns with the same metrics

## Notes

- If the `ym` parameter is not provided, the API will return statistics for all months
- The `category` parameter can be used to filter results for specific categories
- All responses are in JSON format
- The `value` field in responses represents the number of requests/captchas processed (billed validations only)
- The real-time stats endpoint provides more detailed metrics including accepted/rejected/blocked breakdowns
