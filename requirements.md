# Backend Requirements Specification

This document defines the detailed requirements for key backend features of the Property Rental Management System.

---

## 1. User Authentication

### **Description**
The authentication system enables Guests, Hosts, and Admins to register, log in, and manage their session securely using JWT-based authentication.

### **API Endpoints**
- `POST /api/auth/register`
- `POST /api/auth/login`
- `POST /api/auth/logout`
- `GET /api/auth/profile`

### **Input/Output Specifications**
**Register Request (POST /api/auth/register)**
```json
{
  "name": "John Doe",
  "email": "john@example.com",
  "password": "StrongPass123",
  "role": "guest"
}
```
**Register Response**
```json
{
  "message": "User registered successfully",
  "userId": 1
}
```

**Login Request (POST /api/auth/login)**
```json
{
  "email": "john@example.com",
  "password": "StrongPass123"
}
```
**Login Response**
```json
{
  "token": "jwt-token",
  "expiresIn": 3600
}
```

### **Validation Rules**
- Email must be unique and valid format.
- Password must be at least 8 characters, contain uppercase, lowercase, and a number.
- Role must be one of [guest, host, admin].

### **Performance Criteria**
- Authentication response within 200ms.
- JWT token expiration: 1 hour.
- Secure password storage using bcrypt hashing.

---

## 2. Property Management

### **Description**
Allows Hosts to create, update, delete, and manage their property listings. Guests can browse property details.

### **API Endpoints**
- `POST /api/properties`
- `GET /api/properties`
- `GET /api/properties/{id}`
- `PUT /api/properties/{id}`
- `DELETE /api/properties/{id}`

### **Input/Output Specifications**
**Create Property Request (POST /api/properties)**
```json
{
  "title": "Modern Apartment",
  "description": "2-bedroom apartment near city center",
  "location": "Ibanda, Bukavu",
  "price": 50,
  "hostId": 2
}
```
**Create Property Response**
```json
{
  "message": "Property created successfully",
  "propertyId": 10
}
```

**Get Property Response (GET /api/properties/10)**
```json
{
  "id": 10,
  "title": "Modern Apartment",
  "description": "2-bedroom apartment near city center",
  "location": "Ibanda, Bukavu",
  "price": 50,
  "hostId": 2,
  "availability": true
}
```

### **Validation Rules**
- Title must not exceed 100 characters.
- Price must be a positive number.
- Location is required.
- HostId must exist in User DB and have role = host.

### **Performance Criteria**
- Property retrieval query must execute within 300ms.
- Support filtering by location, price range, and availability.
- Index `location` and `price` fields for faster queries.

---

## 3. Booking System

### **Description**
Allows Guests to request bookings, Hosts to confirm/cancel them, and the system to track status.

### **API Endpoints**
- `POST /api/bookings`
- `GET /api/bookings/{id}`
- `GET /api/bookings/user/{userId}`
- `PUT /api/bookings/{id}/status`

### **Input/Output Specifications**
**Create Booking Request (POST /api/bookings)**
```json
{
  "guestId": 1,
  "propertyId": 10,
  "checkInDate": "2025-09-10",
  "checkOutDate": "2025-09-15"
}
```
**Create Booking Response**
```json
{
  "message": "Booking created successfully",
  "bookingId": 5,
  "status": "pending"
}
```

**Booking Status Update Request (PUT /api/bookings/5/status)**
```json
{
  "status": "confirmed"
}
```
**Booking Status Response**
```json
{
  "bookingId": 5,
  "status": "confirmed"
}
```

### **Validation Rules**
- GuestId and PropertyId must exist.
- Check-in and check-out dates must be valid and check-in < check-out.
- Property must be available for selected dates.
- Status must be one of [pending, confirmed, cancelled].

### **Performance Criteria**
- Prevent double bookings using transaction locks.
- Booking confirmation should complete in < 500ms.
- Use database indexes on `guestId`, `propertyId`, and `checkInDate`.

---
