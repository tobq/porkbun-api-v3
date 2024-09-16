## Introduction

The Porkbun API allows programmatic access to Porkbun's domain management services. With this API, you can manage domains, DNS records, SSL certificates, and more directly through HTTP requests.

This document provides guidance on setting up authentication, using the API endpoints, and optionally generating client code using the provided OpenAPI specification.

## Table of Contents

- [Authentication](#authentication)
  - [Obtaining API Keys](#obtaining-api-keys)
  - [Testing Authentication](#testing-authentication)
- [Using the API](#using-the-api)
  - [General Information](#general-information)
  - [Common Endpoints](#common-endpoints)
    - [Check Domain Pricing](#check-domain-pricing)
    - [Update Name Servers](#update-name-servers)
    - [Retrieve DNS Records](#retrieve-dns-records)
- [Error Handling](#error-handling)
- [Code Generation (Optional)](#code-generation-optional)
  - [Generating Client Code](#generating-client-code)
- [Additional Resources](#additional-resources)
- [Support](#support)

## Authentication

To interact with the Porkbun API, you need to authenticate your requests using API keys. Authentication is performed by including your `apikey` and `secretapikey` in the JSON payload of each request.

### Obtaining API Keys

1. **Log in to your Porkbun account** at [porkbun.com](https://porkbun.com).
2. **Navigate to the API Management page**: [porkbun.com/account/api](https://porkbun.com/account/api).
3. **Generate a new API key pair**. You will receive:
   - **API Key (`apikey`)**
   - **Secret API Key (`secretapikey`)**

> **Note**: Keep your `secretapikey` secure. Do not share it publicly or commit it to version control systems.

### Testing Authentication

You can test your API keys by sending a request to the `/ping` endpoint. This endpoint will confirm if your credentials are valid and return your IP address.

#### Request

**Endpoint**: `https://api.porkbun.com/api/json/v3/ping`  
**Method**: `POST`  
**Headers**:
- `Content-Type: application/json`

**JSON Payload**:

```json
{
  "secretapikey": "YOUR_SECRET_API_KEY",
  "apikey": "YOUR_API_KEY"
}
```

#### Response

A successful response will look like:

```json
{
  "status": "SUCCESS",
  "yourIp": "YOUR_IP_ADDRESS"
}
```

If you receive a `"status": "SUCCESS"`, your API keys are working correctly.

## Using the API

### General Information

- **API Base URL**: `https://api.porkbun.com/api/json/v3`
- **All requests must be made over HTTPS**.
- **HTTP Method**: All endpoints use the `POST` method.
- **Content Type**: `application/json`
- **Authentication**: Include `apikey` and `secretapikey` in the JSON payload.

### Common Endpoints

#### Check Domain Pricing

Retrieve default pricing information for all supported TLDs.

**Endpoint**: `/pricing/get`

**Request Payload**:

No additional parameters required.

**Example Request**:

```json
{
  "secretapikey": "YOUR_SECRET_API_KEY",
  "apikey": "YOUR_API_KEY"
}
```

**Example Response**:

```json
{
  "status": "SUCCESS",
  "pricing": {
    "com": {
      "registration": "9.68",
      "renewal": "9.68",
      "transfer": "9.68"
    },
    "net": {
      "registration": "10.56",
      "renewal": "10.56",
      "transfer": "10.56"
    },
    // ...
  }
}
```

#### Update Name Servers

Update the name servers for a specific domain.

**Endpoint**: `/domain/updateNs/{domain}`

**Path Parameter**:
- `domain`: The domain name you wish to update (e.g., `example.com`).

**Request Payload**:

Include the list of name servers in the `ns` array.

```json
{
  "secretapikey": "YOUR_SECRET_API_KEY",
  "apikey": "YOUR_API_KEY",
  "ns": [
    "ns1.example.com",
    "ns2.example.com"
  ]
}
```

**Example Response**:

```json
{
  "status": "SUCCESS"
}
```

#### Retrieve DNS Records

Retrieve all editable DNS records associated with a domain.

**Endpoint**: `/dns/retrieve/{domain}`

**Path Parameter**:
- `domain`: The domain name (e.g., `example.com`).

**Request Payload**:

```json
{
  "secretapikey": "YOUR_SECRET_API_KEY",
  "apikey": "YOUR_API_KEY"
}
```

**Example Response**:

```json
{
  "status": "SUCCESS",
  "records": [
    {
      "id": "123456789",
      "name": "example.com",
      "type": "A",
      "content": "192.0.2.1",
      "ttl": "600",
      "prio": "0",
      "notes": ""
    },
    {
      "id": "987654321",
      "name": "www.example.com",
      "type": "CNAME",
      "content": "example.com",
      "ttl": "600",
      "prio": "0",
      "notes": ""
    }
    // ...
  ]
}
```

### Additional Operations

The Porkbun API supports a variety of domain and DNS management operations, including:

- **Domain Management**:
  - List all domains
  - Add URL forwarding
  - Delete URL forwarding
- **DNS Management**:
  - Create DNS records
  - Edit DNS records
  - Delete DNS records
  - Retrieve specific DNS records
- **SSL Management**:
  - Retrieve SSL certificate bundle

For detailed information on each endpoint and its parameters, refer to the [OpenAPI Specification](#code-generation-optional) or the [Porkbun API Documentation](https://porkbun.com/api/json/v3/documentation).

## Error Handling

Errors are indicated by:

- **HTTP Response Codes**:
  - Any code other than `200 OK` signifies an error.
  - A `403 Forbidden` response may indicate additional authentication is required (e.g., two-factor authentication).
- **Response Body**:
  - The `status` field will be `"ERROR"`.
  - An additional `message` field may provide more details.

**Example Error Response**:

```json
{
  "status": "ERROR",
  "message": "Invalid API key or secret."
}
```

## Code Generation (Optional)

To simplify interaction with the API, you can generate client libraries in various programming languages using the provided OpenAPI specification.

### Generating Client Code

1. **Save the OpenAPI Specification**:

   Save the OpenAPI YAML content to a file, e.g., `porkbun.yaml`.

2. **Install OpenAPI Generator**:

   You can use [OpenAPI Generator](https://openapi-generator.tech/) or [Swagger Codegen](https://swagger.io/tools/swagger-codegen/).

   **Using OpenAPI Generator CLI**:

   - Install via Homebrew (macOS):

     ```bash
     brew install openapi-generator
     ```

   - Install via npm:

     ```bash
     npm install @openapitools/openapi-generator-cli -g
     ```

3. **Generate Client Code**:

   Replace `LANGUAGE` with your desired programming language (e.g., `python`, `java`, `ruby`).

   ```bash
   openapi-generator generate -i porkbun.yaml -g LANGUAGE -o ./porkbun-api-client
   ```

   **Example for Python**:

   ```bash
   openapi-generator generate -i porkbun.yaml -g python -o ./porkbun-api-python
   ```

4. **Use the Generated Client**:

   Refer to the generated client's README or documentation for usage instructions.

> **Note**: The generated client libraries may require additional configuration for authentication headers or request formatting.

## Additional Resources

- **API Documentation**: Detailed API documentation is available on the Porkbun website.
- **Knowledge Base**: [Getting started with the Porkbun DNS API](https://kb.porkbun.com/article/60-getting-started-with-the-porkbun-dns-api)
- **Example Code**: Sample applications demonstrating API usage are available in the knowledge base.

## Support

If you need assistance or have questions about the Porkbun API:

- **Email**: [support@porkbun.com](mailto:support@porkbun.com)
- **Phone**: 1.855.PORKBUN (1.855.767.5286)
  - **Hours**:
    - 6 AM - 5 PM Pacific Time, Monday - Friday
    - 9 AM - 5 PM Pacific Time, Saturday - Sunday
- **Knowledge Base**: [Porkbun Knowledge Base](https://kb.porkbun.com)

## About Porkbun

Porkbun is an ICANN-accredited domain name registrar based in the Pacific Northwest, USA. We offer a variety of services, including domain registration, web hosting, SSL certificates, and more.

**Address**:

```
Porkbun LLC
21370 SW Langer Farms Parkway, Suite 142-429
Sherwood, OR 97140, USA
```

---

*Made in the USA 🇺🇸*

*Porkbun is a Top Level Design Company.*

---

*This README is intended to help you get started with the Porkbun API. For the most up-to-date information, always refer to the official Porkbun API documentation.*
