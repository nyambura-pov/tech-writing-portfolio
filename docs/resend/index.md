# Resend Email API: Developer Onboarding & Reference Guide

A complete developer integration guide and OpenAPI-aligned reference for the Resend transactional email platform.


## 1. Overview & architecture
<!-- Brief explanation of Resend, transactional messaging, and the REST architecture -->

## 2. Authentication & prerequisites
<!-- How to get an API key, header formatting (Bearer token), and security practices -->

## 3. 5-minute quickstart
<!-- Copyable cURL and Python snippets for zero-to-first-successful-email -->

## 4. Endpoints reference
### 4.1 Send a single email (`POST /emails`)
<!-- Parameter table, payload requirements, and example request/response -->

### 4.2 Retrieve email delivery status (`GET /emails/{id}`)
<!-- Path parameters, response fields, and status state machine -->

### 4.3 Send batch emails (`POST /emails/batch`)
<!-- Array payload structure, batch limits, and bulk response arrays -->

## 5. Status codes & error catalog
<!-- 200, 401, 422, 429 explanation and structured error schemas -->

## 6. Rate limits & best practices
<!-- Header inspection, exponential backoff, and production recommendations -->