Airbnb Clone - Backend Requirement Specifications
Key Features:

User Authentication

Property Management

Booking System

1. User Authentication
Functional Requirements
User Registration

API: POST /api/auth/register

Input:

json
{
  "email": "string, unique, valid format",
  "password": "string, min 8 chars, special chars",
  "name": "string, optional",
  "role": "enum: guest/host"
}
Output:

json
{
  "id": "string",
  "email": "string",
  "verification_token": "string (sent via email)"
}
Validation:

Reject duplicate emails.

Password strength enforcement.

User Login

API: POST /api/auth/login

Input:

json
{ "email": "string", "password": "string" }
Output:

json
{
  "token": "JWT",
  "user_id": "string",
  "role": "string"
}
Validation:

Block after 5 failed attempts (30-min lock).

Technical Requirements
Performance:

Response time < 500ms under 100 concurrent requests.

Security:

JWT tokens with 24h expiry.

Password hashing (bcrypt).

2. Property Management
Functional Requirements
Create Property Listing

API: POST /api/properties (Host role required)

Input:

json
{
  "title": "string, max 100 chars",
  "description": "string, max 500 chars",
  "price_per_night": "number > 0",
  "address": {
    "street": "string",
    "city": "string",
    "country": "string"
  },
  "amenities": ["array of strings"]
}
Output:

json
{
  "id": "string",
  "host_id": "string",
  "status": "pending/approved"
}
Validation:

Require at least 3 photos (handled via multipart/form-data).

Search Properties

API: GET /api/properties?location=...&min_price=...

Output:

json
{
  "results": [
    {
      "id": "string",
      "title": "string",
      "price": "number",
      "average_rating": "number (1-5)"
    }
  ],
  "total": "number"
}
Technical Requirements
Performance:

Search results in < 1s (indexed DB on location, price).

Storage:

Images stored in AWS S3 (max 5MB/file).

3. Booking System
Functional Requirements
Create Booking

API: POST /api/bookings

Input:

json
{
  "property_id": "string",
  "check_in": "ISO date",
  "check_out": "ISO date",
  "guests": "number > 0"
}
Output:

json
{
  "booking_id": "string",
  "total_price": "number",
  "payment_due": "boolean"
}
Validation:

Reject if dates are unavailable.

Auto-calculate total: (nights * price) + fees.

Confirm Payment

API: POST /api/payments

Input:

json
{
  "booking_id": "string",
  "card_token": "string (Stripe/PayPal)"
}
Output:

json
{ "status": "success/failed", "receipt_url": "string" }
Technical Requirements
Performance:

Booking creation < 300ms.

Idempotency:

Prevent duplicate payments (unique booking_id check).
