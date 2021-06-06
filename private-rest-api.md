**Table of Contents**
- [General API Information](#general-api-information)
- [Error Codes](#error-codes)
- [General Information on Endpoints](#general-information-on-endpoints)
- [Endpoint security type](#endpoint-security-type)
- [SIGNED (TRADE and USER_DATA) Endpoint security](#signed-trade-and-user_data-endpoint-security)
    - [Timing Security](#timing-security)
    - [SIGNED Endpoint](#signed-endpoint-examples-for-post-getinfo)
- [Private API Endpoint](#private-api-endpoint)
    - [ENUM definitions](#enum-definitions)
    - [Private API endpoints](#private-api-endpoints)

## General API Information
* The base endpoint is: **https://papi.cointrade.id**
* All endpoints return either a JSON object or array.
* Data is returned in **descending** order. newest first, oldest last.
* All time and timestamp related fields are in **milliseconds**.

## Error Codes
* Any endpoint can return an ERROR

Sample Payload below:
```javascript
{
    "success": 0,
    "error": "Invalid credentials. API not found or session has expired.",
    "error_code": "invalid_credentials"
}
```
