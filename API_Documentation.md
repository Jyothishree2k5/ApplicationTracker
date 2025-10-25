# Hybrid Application Tracker - API Documentation

## Base URL
```
http://localhost:5000/api
```

## Authentication
All protected endpoints require a JWT token in the Authorization header:
```
Authorization: Bearer <your_jwt_token>
```

## Response Format
All API responses follow this structure:
```json
{
  "message": "Success message",
  "data": {}, // Response data
  "error": "Error message (if any)"
}
```

---

## Authentication Endpoints

### Register User
**POST** `/auth/register`

**Request Body:**
```json
{
  "username": "johndoe",
  "email": "john@example.com",
  "password": "password123",
  "role": "applicant", // "applicant" | "admin" | "bot"
  "profile": {
    "firstName": "John",
    "lastName": "Doe",
    "phone": "+1234567890"
  }
}
```

**Response:**
```json
{
  "message": "User registered successfully",
  "token": "jwt_token_here",
  "user": {
    "id": "user_id",
    "username": "johndoe",
    "email": "john@example.com",
    "role": "applicant",
    "profile": {...}
  }
}
```

### Login User
**POST** `/auth/login`

**Request Body:**
```json
{
  "email": "john@example.com",
  "password": "password123"
}
```

**Response:**
```json
{
  "message": "Login successful",
  "token": "jwt_token_here",
  "user": {
    "id": "user_id",
    "username": "johndoe",
    "email": "john@example.com",
    "role": "applicant"
  }
}
```

### Get Current User
**GET** `/auth/me`

**Headers:** `Authorization: Bearer <token>`

**Response:**
```json
{
  "user": {
    "id": "user_id",
    "username": "johndoe",
    "email": "john@example.com",
    "role": "applicant",
    "profile": {...}
  }
}
```

---

## Job Roles Endpoints

### Create Job Role
**POST** `/job-roles` (Admin only)

**Request Body:**
```json
{
  "title": "Senior Software Engineer",
  "description": "Full-stack development position",
  "department": "Engineering",
  "type": "technical", // "technical" | "non-technical"
  "requirements": ["React", "Node.js", "5+ years experience"],
  "isActive": true
}
```

### Get All Job Roles
**GET** `/job-roles`

**Query Parameters:**
- `type`: Filter by type ("technical" | "non-technical")
- `isActive`: Filter by active status (true | false)

**Response:**
```json
{
  "jobRoles": [
    {
      "id": "role_id",
      "title": "Senior Software Engineer",
      "description": "Full-stack development position",
      "department": "Engineering",
      "type": "technical",
      "requirements": ["React", "Node.js"],
      "isActive": true,
      "createdBy": {
        "username": "admin"
      },
      "createdAt": "2024-01-01T00:00:00.000Z"
    }
  ]
}
```

### Get Single Job Role
**GET** `/job-roles/:id`

### Update Job Role
**PUT** `/job-roles/:id` (Admin only)

### Delete Job Role
**DELETE** `/job-roles/:id` (Admin only)

---

## Applications Endpoints

### Create Application
**POST** `/applications` (Applicant only)

**Request Body:**
```json
{
  "jobRole": "job_role_id",
  "resume": "https://example.com/resume.pdf",
  "coverLetter": "I am excited to apply...",
  "experience": 5,
  "skills": ["React", "Node.js", "MongoDB"],
  "expectedSalary": 120000,
  "availableFrom": "2024-02-01"
}
```

### Get Applications
**GET** `/applications`

**Access Control:**
- **Applicant**: Only their own applications
- **Bot**: Only technical role applications
- **Admin**: All applications

**Response:**
```json
{
  "applications": [
    {
      "id": "app_id",
      "status": "applied",
      "resume": "https://example.com/resume.pdf",
      "experience": 5,
      "skills": ["React", "Node.js"],
      "expectedSalary": 120000,
      "isAutomated": false,
      "jobRole": {
        "title": "Senior Software Engineer",
        "type": "technical",
        "department": "Engineering"
      },
      "applicant": {
        "username": "johndoe",
        "email": "john@example.com"
      },
      "createdAt": "2024-01-01T00:00:00.000Z"
    }
  ]
}
```

### Get Single Application
**GET** `/applications/:id`

**Response includes activity log:**
```json
{
  "application": {...},
  "activityLog": [
    {
      "action": "Application submitted",
      "newStatus": "applied",
      "comment": null,
      "performedBy": {
        "username": "johndoe"
      },
      "performedByRole": "applicant",
      "isAutomated": false,
      "createdAt": "2024-01-01T00:00:00.000Z"
    }
  ]
}
```

### Update Application Status
**PUT** `/applications/:id/status` (Admin/Bot only)

**Request Body:**
```json
{
  "status": "reviewed", // "applied" | "reviewed" | "interview" | "offer" | "rejected" | "withdrawn"
  "comment": "Application looks promising"
}
```

### Get Dashboard Statistics
**GET** `/applications/stats`

**Response:**
```json
{
  "stats": {
    "totalApplications": 10,
    "statusBreakdown": {
      "applied": 3,
      "reviewed": 2,
      "interview": 2,
      "offer": 2,
      "rejected": 1
    },
    "recentActivity": 5
  }
}
```

---

## Bot Operations Endpoints

### Process Automated Updates
**POST** `/bot/process` (Bot/Admin only)

**Description:** Processes all technical role applications for automated status updates.

**Response:**
```json
{
  "message": "Processed 3 automated updates",
  "updates": [
    {
      "applicationId": "app_id",
      "jobTitle": "Senior Software Engineer",
      "previousStatus": "applied",
      "newStatus": "reviewed",
      "comment": "Application reviewed by automated system"
    }
  ]
}
```

### Get Bot Activity
**GET** `/bot/activity` (Bot/Admin only)

**Response:**
```json
{
  "stats": {
    "totalAutomatedActions": 25,
    "todayActions": 5,
    "recentActivities": [
      {
        "action": "Automated status update to reviewed",
        "application": {...},
        "performedBy": {
          "username": "bot"
        },
        "createdAt": "2024-01-01T00:00:00.000Z"
      }
    ]
  }
}
```

### Trigger Application Update
**POST** `/bot/trigger/:applicationId` (Bot/Admin only)

**Request Body:**
```json
{
  "comment": "Manual bot trigger for testing"
}
```

**Response:**
```json
{
  "message": "Application updated successfully",
  "application": {
    "id": "app_id",
    "previousStatus": "applied",
    "newStatus": "reviewed"
  }
}
```

---

## Status Codes

- **200**: Success
- **201**: Created
- **400**: Bad Request
- **401**: Unauthorized
- **403**: Forbidden
- **404**: Not Found
- **500**: Internal Server Error

---

## Error Response Format

```json
{
  "message": "Error description",
  "error": "Detailed error information"
}
```

---

## Application Status Flow

### Technical Roles (Automated)
```
Applied → Reviewed → Interview → Offer
```

### Non-Technical Roles (Manual)
```
Applied → [Admin manages] → Reviewed → Interview → Offer/Rejected
```

---

## Sample Credentials

### Admin User
- **Email**: admin@demo.com
- **Password**: admin123

### Bot User
- **Email**: bot@demo.com
- **Password**: bot123

### Applicant User
- **Email**: user@demo.com
- **Password**: user123

---

## Postman Collection

Import the following collection to test all endpoints:

```json
{
  "info": {
    "name": "Hybrid Application Tracker API",
    "schema": "https://schema.getpostman.com/json/collection/v2.1.0/collection.json"
  },
  "auth": {
    "type": "bearer",
    "bearer": [
      {
        "key": "token",
        "value": "{{jwt_token}}",
        "type": "string"
      }
    ]
  },
  "variable": [
    {
      "key": "base_url",
      "value": "http://localhost:5000/api"
    },
    {
      "key": "jwt_token",
      "value": ""
    }
  ]
}
```

---

## Interactive API Documentation

Access the Swagger UI documentation at:
```
http://localhost:5000/api-docs
```

This provides an interactive interface to test all API endpoints directly from your browser.