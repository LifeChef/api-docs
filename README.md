# LifeChef API Documentation

## Table of Contents

1. [Introduction](#introduction)
2. [Platform Overview](#platform-overview)
3. [API Overview](#api-overview)
4. [Glossary of Terms](#glossary-of-terms)
5. [API Endpoints](#api-endpoints)
   - [Create or Update Referral](#create-or-update-referral)
   - [Get Bulk Users List](#get-bulk-users-list)
   - [Get Available Programs](#get-available-programs)
   - [Get Available Allergens](#get-available-allergens)
   - [Get Available Coupons](#get-available-coupons)
6. [Detailed Descriptions](#detailed-descriptions)
   - [Referral Status](#referral-status)
   - [Program](#program)
   - [Flag Skipped](#flag-skipped)
   - [Flag Swapped](#flag-swapped)
7. [Using the API](#using-the-api)
   - [Test Environment](#test-environment)
   - [Authorization](#authorization)
   - [Checking Available Values](#checking-available-values)
   - [Creating a Referral](#creating-a-referral)
8. [Error Handling and Limitations](#error-handling-and-limitations)
9. [Default or Predefined Values](#default-or-predefined-values)
10. [Rate Limiting Information](#rate-limiting-information)
11. [Versioning Information](#versioning-information)
12. [Security Best Practices](#security-best-practices)
13. [Interactive API Explorer](#interactive-api-explorer)
14. [Contact Information](#contact-information)
15. [Change Log](#change-log)

## Introduction

Welcome to the LifeChef API documentation. This document provides partners with the necessary information to interact with the LifeChef platform programmatically, including creating referrals, querying patient statuses, and retrieving product order details.

## Platform Overview

LifeChef is a subscription-based service providing medically tailored meals (MTM) based on programs selected by patients. Our platform offers four distinct programs designed for diabetic patients, heart patients, weight loss, and the Healthy Life diet. Each program ensures balanced meals with a focus on taste and diversity.

### User Journey Overview

1. **Program Selection**:
   - Patients select a program that suits their medical and dietary needs.
   - Available programs: Diabetes, Heart, Weight Loss, Healthy Life.

2. **Personalization**:
   - Patients enter allergens or intolerances.
   - They provide shipping and billing information, phone number, and email for notifications and updates.

3. **Delivery Day Selection**:
   - Patients choose their preferred delivery day from two weekly options - Tuesday or Friday.

4. **Meal Selection Method**:
   - Patients choose between automated (Smart Bite) or manual meal selection.

5. **Dashboard and Meal Management**:
   - Patients access a dashboard to manage their schedule, program details, and meal selections.

6. **Notifications and Product Order Tracking**:
   - Patients receive notifications at various stages of the product order process.

## API Overview

Partners are provided with a unique set of API keys that identify them from other partners. Using these keys, partners can perform various operations such as looking up coupons, querying patient statuses, and retrieving product order details. The API does not permit modification of existing patient records but allows the creation of new subscriptions through the creation of referrals. Modification is permitted only for created referrals who have not yet used the referral URL.

## Glossary of Terms

- **Referral**: A potential patient who has started but not completed the signup process.
- **Patient**: A referral who has completed the signup process.
- **Product Order**: An order created from a subscription at a specific time for a particular patient.
- **Order Modification Date**: The date when a product order is created from a subscription.
- **Delivery Date**: The date when the product order is shipped.
- **Program**: The patient's meal plan/goal/diet.

### Key API Operations

1. **Look Up Coupons**:
   - Partners can look up coupons assigned to them, which are unique and not used by others.

2. **Query Patient Status**:
   - Partners can query the status of patients they have signed up to determine if they are still referrals or have converted into full patients.

3. **Create Referrals**:
   - Partners can create new referrals by supplying shipping, billing, contact details, and program selection. The platform then provides a unique URL for the patient to complete the signup process.

## API Endpoints

### Create or Update Referral

- **Endpoint**: `POST /v1/referral`
- **Description**: This endpoint is used to create a new referral in the LifeChef system. A referral represents a potential patient who has been referred to LifeChef but has not yet completed the full signup process.
- **Request Parameters**: The request should include all known details about the referral, such as contact information, shipping and billing addresses, and program preferences. Mandatory fields are marked with "*".
- **Response**: Upon successful creation, the API will return a referral ID and a unique URL that the referral can use to complete their signup process.

**Request Example**:

```json
{
  "firstName": "John",
  "lastName": "Doe",
  "email": "john.doe@example.com",
  "phone": "+12345678910",
  "shippingAddress": {
    "street": "123 Main St",
    "city": "Anytown",
    "state": "CA",
    "zipCode": "12345"
  },
  "billingAddress": {
    "street": "123 Main St",
    "city": "Anytown",
    "state": "CA",
    "zipCode": "12345"
  },
  "program": "diabetes-friendly",
  "coupon": "coupon_name",
  "allergens": [
    "peanuts",
    "gluten"
  ],
  "clientIdentifier": "partner123",
  "days": "5",
  "breakfasts": true,
  "deliveryDay": "tuesday"
}
```

**Response Example**:

```json
{
  "referralId": "referral123",
  "status": "Referral",
  "completionUrl": "lifechef.com/programs/confirm?referral=12345"
}
```

### Get Bulk Users List

- **Endpoint**: `GET /v1/users`
- **Description**: Retrieves a list of users and their data with pagination.

**Response Example of Referral**:

```json
[
  {
    "clientIdentifier": "partner123",
    "referralId": "ref_12345",
    "status": "Referral",
    "program": "diabetes-friendly",
    "registrationDateReferral": "2024-07-24T16:32:33.778Z"
  }
]
```

**Response Example of Subscription**:

```json
[
  {
    "clientIdentifier": "partner123",
    "referralId": "ref_12345",
    "subsid": "subs_123",
    "status": "Subs (ACTIVE)",
    "registrationDateReferral": "2024-07-22T12:32:56.313Z",
    "registrationDateSubs": "2024-07-22T12:33:13.105Z",
    "programData": {
      "program": "low-carbohydrate",
      "meals": 14,
      "breakfasts": 7,
      "allergens": [
        "wheat"
      ],
      "smartBite": true
    },
    "coupons": [
      "50dtest"
    ],
    "shippingAddress": {
      "street": "123 Main St",
      "city": "Anytown",
      "state": "CA",
      "zipCode": "12345"
    },
    "billingAddress": {
      "street": "123 Main St",
      "city": "Anytown",
      "state": "CA",
      "zipCode": "12345"
    },
    "deliveryDay": "tuesday"
  }
]
```

### Get Available Programs

- **Endpoint**: `GET /v1/programs`
- **Description**: Retrieves a list of available meal programs.

**Response Example**:

```json
[
  {
    "id": "mediterranean",
    "title": "Healthy Life™ Program"
  },
  {
    "id": "diabetes-friendly",
    "title": "Diabetes Program"
  },
  {
    "id": "dashlow-sodium",
    "title": "Heart Program"
  },
  {
    "id": "low-carbohydrate",
    "title": "Weight Loss Program"
  }
]
```

### Get Available Allergens

- **Endpoint**: `GET /allergens`
- **Description**: Retrieves a list of allergens that can be excluded from meal plans.

**Response Example**:

```json
[
  {
    "id": "cows-milk",
    "title": "Cow's Milk"
  },
  {
    "id": "eggs",
    "title": "Eggs"
  },
  {
    "id": "peanuts",
    "title": "Peanuts"
  },
  {
    "id": "fish",
    "title": "Fish"
  },
  {
    "id": "shellfish",
    "title": "Shellfish"
  },
  {
    "id": "tree-nuts",
    "title": "Tree Nuts"
  },
  {
    "id": "wheat",
    "title": "Wheat"
  },
  {
    "id": "soy",
    "title": "Soy"
  },
  {
    "id": "gluten",
    "title": "Gluten"
  },
  {
    "id": "sesame",
    "title": "Sesame"
  }
]
```

### Get Available Coupons

- **Endpoint**: `GET /coupons`
- **Description**: Retrieves a list of available coupons for the partner.

**Response Example**:

```json
[
  "coupon_name_1",
  "coupon_name_2",
  "coupon_name_3"
]
```

## Detailed Descriptions

### Referral Status

- **Referral**: The initial status when a subscription is created with all known details (shipping and billing addresses, program settings, cart composition). The user has not yet completed the signup process.
- **Subs (ACTIVE)**: Assigned immediately after the subscription is completed. Product orders are generated from this subscription.
- **Subs (PAUSED)**: The subscription is paused, and no product orders are created. The dates of the nearest week are shifted by +7 from the current date.

### Program

A program refers to the user's MTM. The user can have only one program. Available options include:

- **Healthy Life** (mediterranean)
- **Diabetes Program** (diabetes-friendly)
- **Heart Program** (dashlow-sodium)
- **Weight Loss Program** (low-carbohydrate)

The program also has additional parameters, based on which the cart composition is generated: allergens, additional diets, meals servings per week and dislike meal components.

## Using the API

### Test Environment

- **Test Environment URL**: [Test Environment](https://api-uat.lifechef.com/api-docs/)
- **Production Environment**: Requires authorization with a key obtained from a LifeChef representative.

### Authorization

1. Click on **Authorize**.
2. Enter the key obtained from a LifeChef representative.
3. Click **Authorize** in the modal window.
4. Close the window. The lock icon on the right side will appear closed, indicating successful authorization.

### Checking Available Values

1. **Programs**: Execute the `GET /v1/programs` request to check available program values.
2. **Allergens**: Execute the `GET /v1/allergens` request to check available allergen values.
3. **Coupons**: Execute the `GET /v1/coupons` request to check available coupon values.

## Error Handling and Limitations

1. **Unauthorized Access**: Error 401 - UNAUTHORIZED if the user is not authorized or uses an incorrect key.
2. **Invalid Values**: Errors for invalid values in parameters such as `clientIdentifier`, `email`, `phone`, `allergens`, `zip code`, `program`, `days`, and `coupon`.
3. **Client Already Exists**: Error if a referral is initiated for a user who already has a subscription.
4. **Referral Not Found**: Error if a referral record is not found in LifeChef DB or value is incorrect.
5. **Rate Limit**: 100 requests per minute per IP address. Exceeding this limit results in error 429.

## Default or Predefined Values

1. Predefine `program` value set as - "mediterranean".
2. Predefine `days` value set as - "5".
3. Predefine selection of `breakfasts` set as - "true".
4. Predefine `deliveryDay` set as - "tuesday".
5. Predefine `allergens` set as - "string". If need to add allergens "string" should be replaced by correct.
6. If the `coupon` value is not set, the default coupon will be set automatically. In case several coupons are available, the default coupon will be the top one.

**Predefined Example**:

```json
{
  "firstName": "",
  "lastName": "",
  "email": "",
  "phone": "",
  "shippingAddress": {
    "street": "",
    "city": "",
    "state": "",
    "zipCode": ""
  },
  "billingAddress": {
    "street": "",
    "city": "",
    "state": "",
    "zipCode": ""
  },
  "program": "mediterranean",
  "coupon": "",
  "allergens": [
    "string"
  ],
  "clientIdentifier": "",
  "days": "5",
  "breakfasts": true,
  "deliveryDay": "tuesday"
}
```

## Rate Limiting Information

- **Rate Limit**: 100 requests per minute per IP address.
- **Monitoring Usage**: Partners should implement monitoring to track their API usage and avoid hitting the rate limit.
- **Handling Rate Limit Exceeded**: If you receive a 429 error, reduce the frequency of your requests and wait before making additional requests.

## Versioning Information

- **Current Version**: v1.1.7
- **Versioning Strategy**: The API follows semantic versioning. Updates that introduce breaking changes will increment the major version number.

## Security Best Practices

- **API Key Storage**: Store API keys securely and do not hard-code them in your application.
- **Sensitive Data Handling**: Ensure that sensitive data such as patient information is handled securely and in compliance with relevant regulations.

## Interactive API Explorer

- **Test Environment URL**: [Test Environment](https://api-uat.lifechef.com/api-docs/#/)
- Partners can use the interactive API explorer to test endpoints and see example responses.

## Contact Information

- **Support Email**: support@lifechef.com
- **Phone Number**: 1-855-932-4048
- **Office Hours**: Monday - Friday, 9 AM - 5 PM EST

## Change Log

- **v1.1.7**: Public release of the LifeChef API spec.
