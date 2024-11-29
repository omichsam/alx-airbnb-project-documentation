
# Technical and Functional Requirements

## 📚 Introduction
This document outlines the detailed technical and functional requirements for key backend features of the **ALX Airbnb Project**. Each section includes API endpoints, input/output specifications, validation rules, and performance criteria for the following features:
1. **User Authentication**
2. **Property Management**
3. **Booking System**

---

## 1. **User Authentication**

### 🎯 Objective
Enable users to securely register, log in, and manage their sessions. Differentiate roles (Guest, Host, Admin) with appropriate permissions.

### 🚀 API Endpoints
#### 1.1 **Register User**
- **Endpoint**: `POST /api/auth/register`
- **Input**:
  ```json
  {
    "email": "user@example.com",
    "password": "StrongPassword123!",
    "role": "guest" // or "host"
  }
  ```
- **Output (Success)**:
  ```json
  {
    "message": "User registered successfully",
    "userId": "12345"
  }
  ```
- **Output (Error)**:
  ```json
  {
    "error": "Email already in use"
  }
  ```
- **Validation Rules**:
  - Email must be unique and valid (`regex: /^[^\s@]+@[^\s@]+\.[^\s@]+$/`).
  - Password must be at least 8 characters, with a mix of uppercase, lowercase, numbers, and special characters.
  - Role must be either `guest` or `host`.

#### 1.2 **Login User**
- **Endpoint**: `POST /api/auth/login`
- **Input**:
  ```json
  {
    "email": "user@example.com",
    "password": "StrongPassword123!"
  }
  ```
- **Output (Success)**:
  ```json
  {
    "token": "jwt-token",
    "message": "Login successful"
  }
  ```
- **Output (Error)**:
  ```json
  {
    "error": "Invalid email or password"
  }
  ```
- **Validation Rules**:
  - Email and password must match the stored credentials.
  - Ensure the account is active.

#### 1.3 **Get User Profile**
- **Endpoint**: `GET /api/auth/profile`
- **Headers**:
  - Authorization: `Bearer jwt-token`
- **Output (Success)**:
  ```json
  {
    "userId": "12345",
    "email": "user@example.com",
    "role": "guest",
    "createdAt": "2024-01-01T00:00:00Z"
  }
  ```
- **Output (Error)**:
  ```json
  {
    "error": "Unauthorized"
  }
  ```
- **Validation Rules**:
  - Ensure JWT token is valid and not expired.

### 💻 Performance Criteria
- Ensure registration and login API calls complete within **200ms** under normal load.
- Support **500 concurrent users** for authentication.

---

## 2. **Property Management**

### 🎯 Objective
Allow hosts to manage property listings by adding, updating, or deleting listings and enabling guests to browse available properties.

### 🚀 API Endpoints
#### 2.1 **Add Property**
- **Endpoint**: `POST /api/properties`
- **Headers**:
  - Authorization: `Bearer jwt-token`
- **Input**:
  ```json
  {
    "title": "Luxury Villa",
    "description": "A beautiful villa with a pool",
    "location": "Paris, France",
    "price": 200,
    "amenities": ["Wi-Fi", "Pool"],
    "availability": {
      "startDate": "2024-12-01",
      "endDate": "2024-12-31"
    },
    "images": ["image1.jpg", "image2.jpg"]
  }
  ```
- **Output (Success)**:
  ```json
  {
    "message": "Property added successfully",
    "propertyId": "56789"
  }
  ```
- **Output (Error)**:
  ```json
  {
    "error": "Validation failed"
  }
  ```
- **Validation Rules**:
  - `title`: Required, maximum 100 characters.
  - `price`: Must be a positive number.
  - `availability.startDate` and `availability.endDate`: Must be valid dates.

#### 2.2 **Edit Property**
- **Endpoint**: `PUT /api/properties/{propertyId}`
- **Headers**:
  - Authorization: `Bearer jwt-token`
- **Input**:
  ```json
  {
    "price": 250,
    "availability": {
      "startDate": "2024-12-10",
      "endDate": "2024-12-31"
    }
  }
  ```
- **Output (Success)**:
  ```json
  {
    "message": "Property updated successfully"
  }
  ```
- **Output (Error)**:
  ```json
  {
    "error": "Property not found"
  }
  ```
- **Validation Rules**:
  - Ensure only the host who owns the property can edit it.

#### 2.3 **Search Properties**
- **Endpoint**: `GET /api/properties/search`
- **Query Parameters**:
  - `location`: e.g., `Paris`
  - `priceMin`: e.g., `100`
  - `priceMax`: e.g., `300`
  - `amenities`: e.g., `Wi-Fi,Pool`
- **Output (Success)**:
  ```json
  [
    {
      "propertyId": "56789",
      "title": "Luxury Villa",
      "location": "Paris, France",
      "price": 200
    }
  ]
  ```
- **Validation Rules**:
  - Validate query parameters to prevent SQL injection.

### 💻 Performance Criteria
- Allow up to **1,000 property additions per second**.
- Ensure search results are delivered within **300ms** under normal load.

---

## 3. **Booking System**

### 🎯 Objective
Enable guests to book properties and manage their reservations while ensuring availability and preventing double bookings.

### 🚀 API Endpoints
#### 3.1 **Create Booking**
- **Endpoint**: `POST /api/bookings`
- **Headers**:
  - Authorization: `Bearer jwt-token`
- **Input**:
  ```json
  {
    "propertyId": "56789",
    "startDate": "2024-12-15",
    "endDate": "2024-12-20",
    "guestCount": 2
  }
  ```
- **Output (Success)**:
  ```json
  {
    "message": "Booking created successfully",
    "bookingId": "98765"
  }
  ```
- **Output (Error)**:
  ```json
  {
    "error": "Property is unavailable for the selected dates"
  }
  ```
- **Validation Rules**:
  - `startDate` and `endDate` must be valid and available.
  - Ensure guest count does not exceed property capacity.

#### 3.2 **Cancel Booking**
- **Endpoint**: `DELETE /api/bookings/{bookingId}`
- **Headers**:
  - Authorization: `Bearer jwt-token`
- **Output (Success)**:
  ```json
  {
    "message": "Booking canceled successfully"
  }
  ```
- **Output (Error)**:
  ```json
  {
    "error": "Booking not found or already canceled"
  }
  ```

#### 3.3 **View Booking**
- **Endpoint**: `GET /api/bookings/{bookingId}`
- **Headers**:
  - Authorization: `Bearer jwt-token`
- **Output (Success)**:
  ```json
  {
    "bookingId": "98765",
    "propertyId": "56789",
    "startDate": "2024-12-15",
    "endDate": "2024-12-20",
    "status": "confirmed"
  }
  ```

### 💻 Performance Criteria
- Support up to **500 bookings per second** under peak load.
- Ensure booking confirmations are processed within **500ms**.

---

## 📄 Conclusion
This document provides the foundational requirements for developing the backend features of the Airbnb Clone. Proper implementation and adherence to these requirements will ensure the system is scalable, secure, and user-friendly.
   ```json
     {
       "error":
   ```json
     {
       "error":
