---
title: Finnik API Documentation
---

# Finnik API Documentation

## Base URL

**Production:**
```
https://fin-fa-backend-prod-weu.azurewebsites.net
```

**Development:**
```
https://fin-fa-backend-dev-v2-weu.azurewebsites.net
```

## Authentication

This API uses OAuth 2.0 Client Credentials flow. You need to authenticate first to receive an access token, then use that token to access protected endpoints.

### Step 1: Get Access Token

**Endpoint:** `POST /api/authorize`

**Request:**
```http
POST /api/authorize HTTP/1.1
Content-Type: application/json

{
    "client": "YOUR_CLIENT_ID",
    "client_secret": "YOUR_CLIENT_SECRET"
}
```

**Response (200 OK):**
```json
{
    "access_token": "YOUR_ACCESS_TOKEN",
    "token_type": "Bearer",
    "expires_in": 60
}
```

**Important Notes:**
- The access token expires in **60 minutes** (3600 seconds)
- Store the token securely and refresh it before expiration
- Include the token in the `Authorization` header for all protected endpoints

**Error Responses:**
- `400 Bad Request` - Invalid request format or missing required fields
- `401 Unauthorized` - Invalid client credentials

### Step 2: Use Access Token

Include the access token in the `Authorization` header for all protected endpoints:

```http
Authorization: Bearer {access_token}
```

## Endpoints

### Get Car Report

Retrieve comprehensive vehicle information for a given license plate number.

**Endpoint:** `GET /api/CbCarReport`

**Headers:**
```http
Authorization: Bearer {access_token}
```

**Query Parameters:**
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `licensePlateNumber` | string | Yes | License plate number (e.g., "ABC123D") |

**Example Request:**
```http
GET /api/CbCarReport?licensePlateNumber=ABC123D HTTP/1.1
Authorization: Bearer YOUR_ACCESS_TOKEN
```

**Response (200 OK):**
```json
{
    "top_speed": 250,
    "color": "zwart",
    "power": {
        "kw": 441.0,
        "hp": 599.5924200000001
    },
    "apk": {
        "expiry_date": "2026-01-29T00:00:00",
        "message": "Op 29-01-2026 is je auto weer aan de beurt voor een APK."
    },
    "year": {
        "construction_year": 2025,
        "age": 0
    },
    "new_value": 211086.0,
    "autozine_plus_points": [
        "uiterst comfortabel",
        "zeer luxueus",
        "extreem snel"
    ],
    "vehicle_count_make_model": 1,
    "vehicle_count_variant_last_year": 0,
    "average_owner_age": 33.0,
    "gender_distribution": null,
    "spot_count": 196
}
```

**Response Fields:**

| Field | Type | Description |
|-------|------|-------------|
| `top_speed` | integer (nullable) | Maximum speed in km/h |
| `color` | string (nullable) | Vehicle color |
| `power` | object | Power information |
| `power.kw` | number (nullable) | Power in kilowatts |
| `power.hp` | number (nullable) | Power in horsepower |
| `apk` | object | APK (MOT) information |
| `apk.expiry_date` | datetime (nullable) | APK expiration date (ISO 8601) |
| `apk.message` | string (nullable) | APK reminder message |
| `year` | object | Year information |
| `year.construction_year` | integer (nullable) | Vehicle construction year |
| `year.age` | integer (nullable) | Vehicle age in years |
| `new_value` | number (nullable) | New vehicle value in EUR |
| `autozine_plus_points` | array[string] | List of positive vehicle characteristics |
| `vehicle_count_make_model` | integer | Number of vehicles with same make/model |
| `vehicle_count_variant_last_year` | integer | Number of vehicles with same variant in last year |
| `average_owner_age` | number (nullable) | Average age of vehicle owners |
| `gender_distribution` | object (nullable) | Gender distribution of owners |
| `gender_distribution.male_percentage` | number (nullable) | Percentage of male owners |
| `gender_distribution.female_percentage` | number (nullable) | Percentage of female owners |
| `spot_count` | integer | Number of times this vehicle has been spotted |

**Error Responses:**
- `400 Bad Request` - Missing or invalid license plate number
- `401 Unauthorized` - Missing or invalid bearer token
- `404 Not Found` - Vehicle not found
- `500 Internal Server Error` - Server error

## Code Examples

### cURL

**Get Access Token:**
```bash
curl -X POST "https://fin-fa-backend-prod-weu.azurewebsites.net/api/authorize" \
  -H "Content-Type: application/json" \
  -d '{
    "client": "YOUR_CLIENT_ID",
    "client_secret": "YOUR_CLIENT_SECRET"
  }'
```

**Get Car Report:**
```bash
curl -X GET "https://fin-fa-backend-prod-weu.azurewebsites.net/api/CbCarReport?licensePlateNumber=ABC123D" \
  -H "Authorization: Bearer YOUR_ACCESS_TOKEN"
```

## Best Practices

1. **Token Management**
   - Store access tokens securely
   - Implement token refresh logic before expiration (tokens expire in 60 minutes)
   - Handle token expiration gracefully

2. **Error Handling**
   - Always check HTTP status codes
   - Handle 401 Unauthorized by re-authenticating
   - Implement retry logic for transient errors
   
3. **Security**
   - Never expose client credentials in client-side code
   - Use HTTPS for all API requests
   - Store credentials securely (use environment variables or secure vaults)

