# LifeChef API Documentation

## Table of Contents

1. [Introduction](#introduction)
2. [Platform Overview](#platform-overview)
3. [API Overview](#api-overview)
4. [API Endpoints](#api-endpoints)
   - [Create or Update Referral](#create-or-update-referral)
   - [Get User Data](#get-user-data)
   - [Get Bulk Users List](#get-bulk-users-list)
   - [Get Available Programs](#get-available-programs)
   - [Get Available Allergens](#get-available-allergens)
   - [Get Available Coupons](#get-available-coupons)
5. [Detailed Descriptions](#detailed-descriptions)
   - [Referral Status](#referral-status)
   - [Dates](#dates)
   - [Program](#program)
   - [Flag Skipped](#flag-skipped)
6. [Using the API](#using-the-api)
   - [Test Environment](#test-environment)
   - [Authorization](#authorization)
   - [Checking Available Values](#checking-available-values)
   - [Creating a Referral](#creating-a-referral)
7. [Error Handling and Limitations](#error-handling-and-limitations)
8. [Examples and Use Cases](#examples-and-use-cases)
9. [Rate Limiting Information](#rate-limiting-information)
10. [Glossary of Terms](#glossary-of-terms)
11. [Versioning Information](#versioning-information)
12. [Security Best Practices](#security-best-practices)
13. [Interactive API Explorer](#interactive-api-explorer)
14. [Contact Information](#contact-information)
15. [Change Log](#change-log)

## Introduction

Welcome to the LifeChef API documentation. This document provides partners with the necessary information to interact with the LifeChef platform programmatically, including creating referrals, querying patient statuses, and retrieving product order details.

## Platform Overview

LifeChef is a subscription-based service providing medically tailored meals based on programs selected by patients. Our platform offers four distinct programs designed for diabetic patients, heart patients, weight loss, and the Mediterranean diet. Each program ensures balanced meals with a focus on taste and diversity.

### User Journey Overview

1. **Program Selection**:
   - Patients select a program that suits their medical and dietary needs.
   - Available programs: Diabetes, Heart, Weight Loss, Mediterranean.

2. **Personalization**:
   - Patients enter allergens or intolerances.
   - They provide shipping and billing information, phone number, and email for notifications and updates.

3. **Delivery Day Selection**:
   - Patients choose their preferred delivery day from two weekly options.

4. **Meal Selection Method**:
   - Patients choose between automated (Smart Bite) or manual meal selection.

5. **Dashboard and Meal Management**:
   - Patients access a dashboard to manage their schedule, program details, and meal selections.

6. **Notifications and Product Order Tracking**:
   - Patients receive notifications at various stages of the product order process.

## API Overview

Partners are provided with a unique set of API keys that identify them from other partners. Using these keys, partners can perform various operations such as looking up coupons, querying patient statuses, and retrieving product order details. The API does not permit modification of existing patient records but allows the creation of new subscriptions through the creation of referrals.

### Key API Operations

1. **Look Up Coupons**:
   - Partners can look up coupons assigned to them, which are unique and not used by others.

2. **Query Patient Status**:
   - Partners can query the status of patients they have signed up to determine if they are still referrals or have converted into full patients.

3. **Retrieve Patient Product Orders**:
   - Partners can look up what a patient is getting delivered this week and view historical product orders, including nutritional information for meals.

4. **Get Scheduled Weeks**:
   - Partners can get a list of weeks a patient is scheduled to receive deliveries.

5. **Create Referrals**:
   - Partners can create new referrals by supplying shipping, billing, contact details, and program selection. The platform then provides a unique URL for the patient to complete the signup process.

## API Endpoints

### Create or Update Referral

- **Endpoint**: `POST /referral`
- **Description**: This endpoint is used to create a new referral in the LifeChef system. A referral represents a potential patient who has been referred to LifeChef but has not yet completed the full signup process.
- **Request Parameters**: The request should include all known details about the referral, such as contact information, shipping and billing addresses, and program preferences. Mandatory fields are marked with "*".
- **Response**: Upon successful creation, the API will return a referral ID and a unique URL that the referral can use to complete their signup process.

**Request Example**:
```json
{
  "clientIdentifier": "partner123",
  "email": "john.doe@example.com",
  "phone": "123-456-7890",
  "program": "diabetes-friendly",
  "allergens": ["peanuts", "gluten"],
  "shippingAddress": {
    "street": "123 Main St",
    "city": "Anytown",
    "state": "CA",
    "zip": "12345"
  },
  "billingAddress": {
    "street": "123 Main St",
    "city": "Anytown",
    "state": "CA",
    "zip": "12345"
  },
  "deliveryDay": "Monday"
}
```

**Response Example**:
```json
{
  "referralId": "referral123",
  "status": "Referral",
  "completionUrl": "https://lifechef.com/complete-signup/referral123"
}
```

### Get User Data

- **Endpoint**: `GET /user/{id}`
- **Description**: Retrieves user data based on LifeChef internal ID, partner identifier, or email.

**Request Example**:
```json
{
  "id": "user@example.com"
}
```

**Response Example**:
```json
{
  "userId": "user123",
  "email": "john.doe@example.com",
  "phone": "123-456-7890",
  "program": "diabetes-friendly",
  "allergens": ["peanuts", "gluten"],
  "shippingAddress": {
    "street": "123 Main St",
    "city": "Anytown",
    "state": "CA",
    "zip": "12345"
  },
  "billingAddress": {
    "street": "123 Main St",
    "city": "Anytown",
    "state": "CA",
    "zip": "12345"
  },
  "deliveryDay": "Monday",
  "status": "Active"
}
```

### Get Bulk Users List

- **Endpoint**: `GET /users`
- **Description**: Retrieves a list of users with pagination.

### Get Available Programs

- **Endpoint**: `GET /programs`
- **Description**: Retrieves a list of available meal programs.

### Get Available Allergens

- **Endpoint**: `GET /allergens`
- **Description**: Retrieves a list of allergens that can be excluded from meal plans.

### Get Available Coupons

- **Endpoint**: `GET /coupons`
- **Description**: Retrieves a list of available coupons for the partner.

## Detailed Descriptions

### Referral Status

- **Referral**: The initial status when a subscription is created with all known details (shipping and billing addresses, program settings, cart composition). The user has not yet completed the checkout process.
- **Subs (ACTIVE)**: Assigned immediately after the subscription is completed. Product orders are generated from this subscription.
- **Subs (PAUSED)**: The subscription is paused, and no product orders are created. The dates of the nearest week are shifted by +7 from the current date.

### Dates

- **Order Modification Date**: The date when a product order is created from a subscription. For Monday deliveries, the order creation date is Thursday 8am EST; for Thursday deliveries, it is Monday 8am EST.
- **Delivery Date**: The date when the product order is shipped. The user sees an approximate delivery date (+1 day from the shipping date).

### Program

A program refers to the user's plan/goal. The user can have only one program. Available options include:
- **Healthy Life (mediterranean)**
- **Diabetes Program (diabetes-friendly)**
- **Heart Program (dashlow-sodium)**
- **Weight Loss Program (low-carbohydrate)**

The program also has additional parameters, based on which the cart composition is generated: allergens, additional diets, number of days, meals.

### Flag Skipped

The user has the option to skip one or more upcoming deliveries (weeks). When a week is skipped, a product order is not created for that week, and the composition is not saved for the next week. The parameter has several values:
- **True**: The user skipped the week (does not expect delivery).
- **False**: The week is active (the user expects to receive a product order on a delivery date).

## Using the API

### Test Environment

- **Test Environment URL**: [Test Environment](https://api-uat.lifechef.com/api-docs/#/)
- **Production Environment**: Requires authorization with a key obtained from a LifeChef representative.

### Authorization

1. Click on **Authorize**.
2. Enter the key.
3. Click **Authorize** in the modal window.
4. Close the window. The lock icon will appear closed, indicating successful authorization.

### Checking Available Values

1. **Programs**: Execute the `GET /programs` request to check available program values.
2. **Allergens**: Execute the `GET /allergens` request to check available allergen values.
3. **Coupons**: Execute the `GET /coupons` request to check available coupon values.

### Creating a Referral

1. **Endpoint**: `POST /referral`
2. **Description**: This endpoint is used to create a new referral in the LifeChef system. A referral represents a potential patient who has been referred to LifeChef but has not yet completed the full signup process.
3. **Request Parameters**: The request should include all known details about the referral, such as contact information, shipping and billing addresses, and program preferences. Mandatory fields are marked with "*".
4. **Response**: Upon successful creation, the API will return a referral ID and a unique URL that the referral can use to complete their signup process.

**Request Example**:
```json
{
  "clientIdentifier": "partner123",
  "email": "john.doe@example.com",
  "phone": "123-456-7890",
  "program": "diabetes-friendly",
  "allergens": ["peanuts", "gluten"],
  "shippingAddress": {
    "street": "123 Main St",
    "city": "Anytown",
    "state": "CA",
    "zip": "12345"
  },
  "billingAddress": {
    "street": "123 Main St",
    "city": "Anytown",
    "state": "CA",
    "zip": "12345"
  },
  "deliveryDay": "Monday"
}
```

**Response Example**:
```json
{
  "referralId": "referral123",
  "status": "Referral",
  "completionUrl": "https://lifechef.com/complete-signup/referral123"
}
```

## Error Handling and Limitations

1. **Unauthorized Access**: Error 401 - UNAUTHORIZED if the user is not authorized or uses an incorrect key.
2. **Invalid Values**: Errors for invalid values in parameters such as `clientIdentifier`, `email`, `phone`, `allergens`, `zip code`, `program`, `days`, and `coupon`.
3. **Client Already Exists**: Error if a referral is initiated for a user who already has a subscription.
4. **Rate Limit**: 100 requests per minute per IP address. Exceeding this limit results in error 429.

## Examples and Use Cases

### Example: Creating a Referral

**Request**:
```json
{
  "clientIdentifier": "partner123",
  "email": "john.doe@example.com",
  "phone": "123-456-7890",
  "program": "diabetes-friendly",
  "allergens": ["peanuts", "gluten"],
  "shippingAddress": {
    "street": "123 Main St",
    "city": "Anytown",
    "state": "CA",
    "zip": "12345"
  },
  "billingAddress": {
    "street": "123 Main St",
    "city": "Anytown",
    "state": "CA",
    "zip": "12345"
  },
  "deliveryDay": "Monday"
}
```

**Response**:
```json
{
  "referralId": "referral123",
  "status": "Referral",
  "completionUrl": "https://lifechef.com/complete-signup/referral123"
}
```

### Example: Retrieving User Data

**Request**:
```json
{
  "id": "user@example.com"
}
```

**Response**:
```json
{
  "userId": "user123",
  "email": "john.doe@example.com",
  "phone": "123-456-7890",
  "program": "diabetes-friendly",
  "allergens": ["peanuts", "gluten"],
  "shippingAddress": {
    "street": "123 Main St",
    "city": "Anytown",
    "state": "CA",
    "zip": "12345"
  },
  "billingAddress": {
    "street": "123 Main St",
    "city": "Anytown",
    "state": "CA",
    "zip": "12345"
  },
  "deliveryDay": "Monday",
  "status": "Active"
}
```

## Rate Limiting Information

- **Rate Limit**: 100 requests per minute per IP address.
- **Monitoring Usage**: Partners should implement monitoring to track their API usage and avoid hitting the rate limit.
- **Handling Rate Limit Exceeded**: If you receive a 429 error, reduce the frequency of your requests and wait before making additional requests.

## Glossary of Terms

- **Referral**: A potential patient who has started but not completed the signup process.
- **Patient**: A referral who has completed the signup process.
- **Product Order**: An order created from a subscription at a specific time for a particular patient.
- **Order Modification Date**: The date when a product order is created from a subscription.
- **Delivery Date**: The date when the product order is shipped.
- **Program**: The patient's meal plan/goal/diet.
- **Flag Skipped**: Indicates whether a patient has skipped an upcoming week.

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
