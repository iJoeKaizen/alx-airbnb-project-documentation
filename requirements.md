# Backend Requirement Specifications

This document outlines the technical and functional specifications for three key backend features of the Property Booking Platform, implemented in Python (e.g., FastAPI or Django REST Framework).

---

##  1. User Authentication

###  Functional Requirements

* Secure user registration and login.
* JWT-based authentication.
* Passwords stored using bcrypt or equivalent.
* Protected access to authenticated routes.

### 🔌 API Endpoints

| Method | URL                | Description       | Auth Required |
| ------ | ------------------ | ----------------- | ------------- |
| POST   | `/api/auth/signup` | Register new user | ❌             |
| POST   | `/api/auth/login`  | Log in user       | ❌             |
| GET    | `/api/auth/me`     | Fetch user info   | ✅             |

###  Input/Output

#### **Signup**

```json
Input:
{
  "first_name": "Jane",
  "last_name": "Doe",
  "email": "jane@example.com",
  "password": "StrongPass123!"
}

Output:
{
  "message": "User registered successfully",
  "token": "JWT_TOKEN_HERE"
}
```

#### **Login**

```json
Input:
{
  "email": "jane@example.com",
  "password": "StrongPass123!"
}

Output:
{
  "token": "JWT_TOKEN_HERE"
}
```

###  Validation Rules

* Email: Valid, unique.
* Password: Min 8 chars, includes number and capital letter.
* Required fields: first\_name, last\_name, email, password.

###  Performance Criteria

* Response within <300ms.
* Token expiry: 15 mins access, optional refresh.

---

##  2. Property Management

###  Functional Requirements

* Hosts can create, edit, delete properties.
* Guests can view and filter properties.
* Property includes price, location, availability.

###  API Endpoints

| Method | URL                    | Description            | Auth | Role       |
| ------ | ---------------------- | ---------------------- | ---- | ---------- |
| POST   | `/api/properties/`     | Create property        | ✅    | Host       |
| GET    | `/api/properties/`     | List/search properties | ❌    | All        |
| GET    | `/api/properties/{id}` | View property details  | ❌    | All        |
| PUT    | `/api/properties/{id}` | Update property        | ✅    | Host       |
| DELETE | `/api/properties/{id}` | Delete property        | ✅    | Host/Admin |

###  Input/Output

#### **Create Property**

```json
Input:
{
  "name": "Oceanfront Villa",
  "description": "Beachside property",
  "price_per_night": 120,
  "location": "Cape Coast",
  "available_from": "2025-06-01",
  "available_to": "2025-06-30"
}

Output:
{
  "message": "Property created successfully",
  "property_id": "prop_001"
}
```

###  Validation Rules

* price\_per\_night: float > 0
* location: valid DB entry
* available\_from < available\_to

###  Performance Criteria

* Paginated search
* Listing response in <500ms

---

##  3. Booking System

###  Functional Requirements

* Users can book available properties.
* Availability and date validation.
* Calculate total price based on date range.

###  API Endpoints

| Method | URL                  | Description          | Auth | Role        |
| ------ | -------------------- | -------------------- | ---- | ----------- |
| POST   | `/api/bookings/`     | Create booking       | ✅    | Guest       |
| GET    | `/api/bookings/`     | View user's bookings | ✅    | All         |
| DELETE | `/api/bookings/{id}` | Cancel a booking     | ✅    | Guest/Admin |

###  Input/Output

#### **Create Booking**

```json
Input:
{
  "property_id": "prop_001",
  "start_date": "2025-06-05",
  "end_date": "2025-06-10"
}

Output:
{
  "message": "Booking confirmed",
  "booking_id": "book_123",
  "total_price": 600
}
```

###  Validation Rules

* Dates must not overlap existing bookings.
* Must be at least 1 day ahead.
* Guests can't book their own properties.

###  Performance Criteria

* Availability check in <200ms
* Booking confirmation in <500ms
