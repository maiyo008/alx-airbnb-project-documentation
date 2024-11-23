# Requirement Specifications for Backend Features
## 1. User Authentication
**Objective:** Ensure secure and efficient user authentication for guests and hosts.

**Functional Requirements:**
* Users can register with their email, password, and role (guest or host).
* Users can log in via email/password or OAuth providers like Google, Meta, and X (formerly Twitter).
* Users can reset their password via a secure token-based link.

**API Endpoints**
POST /api/auth/register
Sample input
```
{
  "email": "user@example.com",
  "password": "securepassword123",
  "role": "guest" // or "host"
}
```
Sample output (Incase of a success)
```
{
  "message": "Registration successful",
  "userId": "123456"
}
```
Validation rules:
* Email must be valid and unique.
* Password must be at least 8 characters with a mix of letters, numbers, and symbols.
* Role must be either "guest" or "host"

POST /api/auth/login
Sample input
```
{
  "email": "user@example.com",
  "password": "securepassword123"
}
```
Sample output (Incase of a success)
```
{
  "message": "Login successful",
  "token": "jwt-token",
  "role": "guest"
}
```
Validation rules:
* Email must be registered.
* Password must match the stored hash for the email.

POST /api/auth/oauth-login
* Input: OAuth token from the provider.
* Output: Same as /api/auth/login.

**Performance Criteria:**
* Login response time: ≤ 300ms.
* Token expiration: 24 hours for access tokens, 7 days for refresh tokens.

## 2. Property Management
**Objective:** Allow hosts to create, edit, and delete property listings.

**Functional Requirements:**
* Hosts can add details about properties, including title, description, photos, amenities, and pricing.
* Hosts can update existing property details.
* Hosts can delete properties no longer available.

**API Endpoints:**
POST /api/properties
Sample input
```
{
  "hostId": "123456",
  "title": "Cozy Apartment in Downtown",
  "description": "2-bedroom apartment with a beautiful city view",
  "photos": ["photo1.jpg", "photo2.jpg"],
  "amenities": ["WiFi", "Air Conditioning"],
  "pricePerNight": 50,
  "location": "Downtown, New York"
}
```
Sample output (If successful)
```
{
  "message": "Property added successfully",
  "propertyId": "98765"
}
```
**Validation rules:**
* Title: max 100 characters.
* Description: max 500 characters.
* Price: must be a positive number.
* Location: required.

PUT /api/properties/{propertyId}
Input: Same as POST /api/properties but updates existing details.
Output (Success):
```
{
  "message": "Property updated successfully"
}
```

DELETE /api/properties/{propertyId}
Input: Host authentication token.
Output (Success)
```
{
  "message": "Property deleted successfully"
}
```
Performance Criteria:

* API should handle 500 concurrent property operations.
* Data retrieval for property search ≤ 200ms.


## 3. Booking System
**Objective:** Enable guests to search, book, and cancel properties.

**Functional Requirements:**
* Guests can search properties by location, price range, amenities, and availability.
* Guests can make a booking by providing payment details and stay dates.
* Guests can cancel bookings based on the host's cancellation policy.

**API Endpoints:**
GET /api/properties/search
Sample Input (Query Parameters):
`location=New York&priceMin=50&priceMax=200&amenities=WiFi,AirConditioning`

Sample Output
```
[
  {
    "propertyId": "98765",
    "title": "Cozy Apartment in Downtown",
    "pricePerNight": 50,
    "location": "Downtown, New York",
    "photos": ["photo1.jpg"],
    "availability": true
  }
]
```
**Validation Rules:**
* Location is required.
* Price range must be valid (min ≤ max).

POST /api/bookings
Sample input
```
{
  "guestId": "123456",
  "propertyId": "98765",
  "checkIn": "2024-12-01",
  "checkOut": "2024-12-05",
  "paymentToken": "stripe-token"
}
```
Sample output (Success)
```
{
  "message": "Booking confirmed",
  "bookingId": "654321"
}
```

DELETE /api/bookings/{bookingId}

Sample Input: Guest authentication token.
Sample Output (Success):
```
{
  "message": "Booking cancelled successfully"
}
```
**Performance Criteria:**

* Booking confirmation within ≤ 400ms.
* Handle cancellation policies dynamically based on host rules.