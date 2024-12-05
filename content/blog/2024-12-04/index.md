---
title: Structures API
layout: post
date: 2024-12-04T18:09:59.885Z
path: "/2024-12-04/"
category: "technical-writing"
description: A sample request/response doc for a fictional API.
---

> **_NOTE:_**  This is a fictional API created to demonstrate API documentation and some links are non-functional.

In order to generate estimates on resource and emission metrics, we first need to upload your Structure data into the database.

There are two endpoints we can use to upload data into the system:

1. `/structures` - JSON endpoint for up to 100 structure entities
2. `/structures/bulk` - File endpoint for bulk uploads

## Prerequisites

- `client_app` and `client_secret`

If you do not have the client_app and client_secret, refer to the section on [Obtaining Client Authentication](#) for guidance.


### Authenticating Requests
```jsx
curl -v -X POST \
  https://example-app/api/tokens/xapp_token \
  -d client_id=[your client id] \
  -d client_secret=[your client secret]
```

| Parameter | Description |
| --- | --- |
| client_id	 | Your application's client ID. |
| client_secret | Your application's client secret. |

#### Response

```jsx
{
  "type" : "xapp_token",
  "token" : "...",
  "expires_at" : "2024-12-04T12:39:09.200Z"
}
```

| Field | Description |
| --- | --- |
| type | Always "xapp_token". |
| token | Authentication token. |
| expires_at | Token expiration date/time. |

Make requests by placing the `token` into an `X-Xapp-Token` header.



## Creating Structures

### `/structures`

This endpoint is ideal for organizing a small list of structures in a JSON format.

```jsx
curl -v "https://example-app/api/structures" \
  -H "X-XAPP-Token: XAPP_TOKEN" \
  -H "Content-Type: application/json" \
  -X POST \
  -d '[
    {
      "name": "Building 1",
      "address_line_1": "123 Main St.",
      "city": "San Diego",
      "state": "California",
      "country": "USA",
      "postal_code": "92101",
      "structure_status": "active"
    },
    {
      "name": "Building 2",
      "address_line_1": "456 Elm St.",
      "city": "Los Angeles",
      "state": "California",
      "country": "USA",
      "postal_code": "90001",
      "structure_status": "inactive"
    }
  ]'
```
| Parameter | Description | Required
| --- | --- | --- | 
| name	| The name of the structure. | Yes. |
| address_line_1 | The primary address line of the structure. | Yes. |
| city | The city where the structure is located. | Yes. |
| state	| The state where the structure is located. | Yes. |
| country | The country where the structure is located. | Yes. |
| postal_code |	The postal code for the structure's address. | Yes. |
| structure_status | The status of the structure. | No. |

### `/structures/bulk`

This endpoint is best suited for managing a large collection of structures. Refer to the provided [spreadsheet template](#) for guidance.

```jsx
curl -v "https://example-app/api/structures/bulk" \
  -H "X-XAPP-Token: XAPP_TOKEN" \
  -F "file=@your_file.xlsx" 
```

#### Response
Both create structure endpoints return the same response format.

```jsx
{
  "status": "partial_success",
  "message": "Some resources were successfully created, but some failed.",
  "created": [
    {
      "id": "4d8b92b34eb68a1b2c0003f4",
      "name": "Building 1",
      "address_line_1": "123 Main St.",
      "city": "San Diego",
      "state": "California",
      "country": "USA",
      "postal_code": "92101",
      "structure_status": "active",
      "created_at": "2024-12-04T22:49:11+00:00",
      "updated_at": "2024-12-04T22:49:11+00:00"
    },
    {
      "id": "5a9d92c34ea69b2c10004b2b",
      "name": "Building 2",
      "address_line_1": "456 Elm St.",
      "city": "Los Angeles",
      "state": "California",
      "country": "USA",
      "postal_code": "90001",
      "structure_status": "under_construction",
      "created_at": "2024-12-04T22:55:00+00:00",
      "updated_at": "2024-12-04T22:55:00+00:00"
    }
  ],
  "failed": [
    {
      "name": "Building 3",
      "error": "Missing required field: address_line_1"
    },
    {
      "name": "Building 4",
      "error": "Invalid postal code format"
    }
  ]
}

```

#### **Create Resource Status**

| Status | Description |
| --- | --- |
| success | All structures created successfully. |
| partial_success | Some structures created successfully. |
| failed | No structures created successfully. |

## Retrieving Structures

```jsx
curl -v "https://example-app/api/structures?structure_status=active" -H "X-XAPP-Token: XAPP_TOKEN"
```

This endpoint accepts the following parameters.

| Name | Value |
| --- | --- |
| structure_status | Retrieve structures with a specific status (see below). |

### **Structure Status**

| Status | Description |
| --- | --- |
| active | Currently active. |
| under_construction | Currently under construction. |
| closed | Closed. |
| null | No status provided/unkown. |

#### Response

```jsx
[
  {
    "id": "4d8b92b34eb68a1b2c0003f4",
    "created_at": "2010-08-23T14:15:30+00:00",
    "updated_at": "2024-12-04T22:49:11+00:00",
    "name": "building name",
    "address_line_1": "123 Main St.",
    "city": "San Diego",
    "state": "California",
    "country": "USA",
    "postal_code": "92101",
    "structure_status": "active"
  }
]
```

## **Retrieving a Structure**

Users can retrieve a specific structure by ID

```jsx
curl -v "https://example-app/api/structures/{id}" -H "X-XAPP-Token: XAPP_TOKEN"
```

#### Response

```jsx
  {
    "id": "4d8b92b34eb68a1b2c0003f4",
    "created_at": "2010-08-23T14:15:30+00:00",
    "updated_at": "2024-12-04T22:49:11+00:00",
    "name": "building name",
    "address_line_1": "123 Main St.",
    "city": "San Diego",
    "state": "California",
    "country": "USA",
    "postal_code": "92101",
    "structure_status": "active"
  }
```

## Errors
The following are sample error types that the API may return.

- Bad Request
- Unauthorized
- Not Found
- Unprocessable Entity
- Internal Server Error

```jsx
  {
    "error": "Unauthorized",
    "message": "Invalid or missing X-XAPP-Token.",
    "code": 401
  }
```