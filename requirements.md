## **Requirements for ALX Airbnb Project**

This document outlines the technical and functional requirements for three key backend features: **User Authentication**, **Property Management**, and **Booking System**.


## **1. User Authentication**

### **Functional Requirements**
1. Users must be able to sign up as guests or hosts.
2. Users must be able to log in using their email and password.
3. Support for third-party OAuth (Google, Facebook).
4. Passwords must be securely hashed and stored.
5. Sessions must be authenticated using JWT.
6. Users must be able to update profile details such as name, profile picture, and contact info.

### **Technical Requirements**
- **API Endpoints**:
  1. **POST /api/auth/register**
      - **Input**:
        ```json
        {
          "email": "user@gmail.com",
          "password": "Password123",
          "role": "guest/host"
        }
        ```
      - **Output**:
        - Success: `201 Created`
          ```json
          {
            "message": "User registered successfully",
            "user_id": 1
          }
          ```
        - Error: `400 Bad Request`
          ```json
          {
            "error": "Email already exists"
          }
          ```

  2. **POST /api/auth/login**
      - **Input**:
        ```json
        {
          "email": "user@gmail.com",
          "password": "Password123"
        }
        ```
      - **Output**:
        - Success: `200 OK`
          ```json
          {
            "token": "jwt-token",
            "user": {
              "id": 1,
              "email": "user@gmail.com",
              "role": "guest"
            }
          }
          ```
        - Error: `401 Unauthorized`
          ```json
          {
            "error": "Invalid credentials"
          }
          ```

  3. **GET /api/auth/profile**
      - **Headers**:
        - Authorization: `Bearer jwt-token`
      - **Output**:
        - Success: `200 OK`
          ```json
          {
            "id": 1,
            "email": "user@gmail.com",
            "name": "beth",
            "role": "guest",
            "profile_picture": "profile-pic-url"
          }
          ```
        - Error: `403 Forbidden`
          ```json
          {
            "error": "Unauthorized"
          }
          ```

- **Validation Rules**:
  - Password: At least 8 characters, 1 uppercase letter, 1 number.
  - Email: Valid email format.

- **Performance Criteria**:
  - Registration requests should be processed in under 500ms.
  - JWT tokens should expire after 24 hours.

---

## **2. Property Management**

### **Functional Requirements**
1. Hosts can add properties with details such as title, description, location, price, amenities, and availability.
2. Hosts can edit or delete their property listings.
3. Admins can moderate property listings.

### **Technical Requirements**
- **API Endpoints**:
  1. **POST /api/properties**
      - **Input**:
        ```json
        {
          "title": "Beachfront Villa",
          "description": "A luxurious villa by the beach.",
          "location": "Miami, FL",
          "price": 500,
          "amenities": ["Wi-Fi", "Pool", "Pet-Friendly"],
          "availability": {
            "start_date": "2024-01-01",
            "end_date": "2024-12-31"
          }
        }
        ```
      - **Output**:
        - Success: `201 Created`
          ```json
          {
            "message": "Property added successfully",
            "property_id": 1
          }
          ```
        - Error: `400 Bad Request`
          ```json
          {
            "error": "Missing required fields"
          }
          ```

  2. **PUT /api/properties/{property_id}**
      - **Input**:
        ```json
        {
          "price": 450,
          "amenities": ["Wi-Fi", "Pool"]
        }
        ```
      - **Output**:
        - Success: `200 OK`
          ```json
          {
            "message": "Property updated successfully"
          }
          ```
        - Error: `404 Not Found`
          ```json
          {
            "error": "Property not found"
          }
          ```

  3. **DELETE /api/properties/{property_id}**
      - **Output**:
        - Success: `200 OK`
          ```json
          {
            "message": "Property deleted successfully"
          }
          ```
        - Error: `404 Not Found`
          ```json
          {
            "error": "Property not found"
          }
          ```

- **Validation Rules**:
  - Title: Required, max 100 characters.
  - Price: Positive integer.
  - Availability: Start date must be before end date.

- **Performance Criteria**:
  - Property addition/editing should take less than 800ms.
  - Support up to 1 million property records.

---

## **3. Booking System**

### **Functional Requirements**
1. Guests can book properties for specified dates.
2. Double bookings must be prevented using date validation.
3. Guests or hosts can cancel bookings as per cancellation policies.
4. Booking statuses include: pending, confirmed, canceled, completed.

### **Technical Requirements**
- **API Endpoints**:
  1. **POST /api/bookings**
      - **Input**:
        ```json
        {
          "property_id": 1,
          "guest_id": 2,
          "check_in": "2024-03-01",
          "check_out": "2024-03-05"
        }
        ```
      - **Output**:
        - Success: `201 Created`
          ```json
          {
            "message": "Booking created successfully",
            "booking_id": 123
          }
          ```
        - Error: `400 Bad Request`
          ```json
          {
            "error": "Property is unavailable for selected dates"
          }
          ```

  2. **PUT /api/bookings/{booking_id}/cancel**
      - **Output**:
        - Success: `200 OK`
          ```json
          {
            "message": "Booking canceled successfully"
          }
          ```
        - Error: `403 Forbidden`
          ```json
          {
            "error": "Cancellation not allowed after check-in date"
          }
          ```

  3. **GET /api/bookings/{booking_id}**
      - **Output**:
        - Success: `200 OK`
          ```json
          {
            "booking_id": 123,
            "property_id": 1,
            "guest_id": 2,
            "status": "confirmed",
            "check_in": "2024-03-01",
            "check_out": "2024-03-05"
          }
          ```
        - Error: `404 Not Found`
          ```json
          {
            "error": "Booking not found"
          }
          ```

- **Validation Rules**:
  - Dates: Check-in must be before check-out.
  - Booking overlaps: No overlapping bookings for the same property.

- **Performance Criteria**:
  - Booking creation requests should take under 1 second.
  - Support up to 500,000 concurrent bookings.

