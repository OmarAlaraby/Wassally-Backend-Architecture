# Wassally Project - Code Architecture Documentation

**CONFIDENTIAL: FOR INTERNAL USE ONLY**

This document contains proprietary information about the Wassally platform architecture.
It is intended for authorized personnel only and should not be shared publicly.

## Table of Contents

1. [Overview](#overview)
2. [Technical Stack](#technical-stack)
3. [Project Structure](#project-structure)
4. [Core Modules](#core-modules)
   - [Wassally Core](#wassally-core)
   - [Accounts](#accounts)
   - [Orders & Delivery](#orders--delivery)
   - [Financial Management](#financial-management)
   - [Shop Ecosystem](#shop-ecosystem)
5. [Data Models](#data-models)
   - [Core Models](#core-models)
   - [Financial Models](#financial-models)
6. [Authentication & Authorization](#authentication--authorization)
7. [API Design](#api-design)
8. [Financial Processing](#financial-processing)
9. [Notification System](#notification-system)
10. [Deployment Architecture](#deployment-architecture)
11. [Testing Strategy](#testing-strategy)
12. [Best Practices](#best-practices)

## Overview

Wassally is a comprehensive delivery management platform that connects shops, customers, and delivery crews. The system enables order placement, delivery request management, profit calculation, and user transaction tracking. The platform features a multi-tenant architecture supporting different user types including customers, shop owners, delivery crew members, and administrators.

The system handles two primary workflows:
1. **Product Orders**: Customers ordering products from shops with delivery services
2. **Delivery Requests**: Direct delivery service requests without product purchases

## Technical Stack

- **Backend Framework**: Django with Django REST Framework
- **Database**: PostgreSQL (with transaction support)
- **Authentication**: Token-based authentication
- **File Storage**: Cloud-based storage for media files
- **API Documentation**: Automatic API documentation generation
- **Frontend Serving**: Static file serving
- **Cross-Origin Resource Sharing**: CORS handling for frontend integration
- **Deployment**: Multiple deployment environments
- **Notifications**: Push notification system
- **Filtering**: Advanced query filtering
- **Email**: Email service for password reset and notifications

## Project Structure

The project follows a domain-driven design with multiple Django apps, each responsible for specific business functionality:

```
Wassally/
├── accounts/            # User management, authentication, and profile handling
├── app_version/         # Application versioning for mobile app compatibility
├── delivery_discounts/  # Special discounts for delivery services
├── delivery_request/    # Direct delivery service request management
├── location/            # Geographic location and address management
├── news/                # Platform announcements and updates
├── notifications/       # Push notification system
├── offers/              # Special offers and promotions management
├── orders/              # Product order processing
├── products/            # Product catalog and inventory
├── profits/             # Profit calculation and distribution
├── project/             # Core project settings and configurations
├── shops/               # Shop management and properties
├── transactions/        # Financial transaction recording
└── wassally/            # Core business logic and base models
```

This modular approach ensures:
- Clear separation of concerns
- Independent development and testing
- Focused responsibility for each component
- Maintainable and scalable codebase

## Core Modules

### Wassally Core

The central module that contains the base models and core business logic:

- **Key Components**:
  - `WassallyOrder`: Abstract base class for orders and delivery requests
  - Core utility functions for business logic
  - Common constants and application settings
  - Base profit calculation logic

- **Design Patterns**:
  - Template Method pattern for order processing
  - Factory pattern for creating appropriate order types
  - Observer pattern for status change notifications

### Accounts

Manages all user-related functionality with a sophisticated user model:

- **User Types**:
  - Regular Users (customers)
  - Shop Owners
  - Delivery Crew (with subtypes: Independent and Dependent)
  - Staff/Admin Users

- **Key Features**:
  - Custom user model extending Django's AbstractUser
  - Phone number authentication
  - Role-based permissions
  - Profile management
  - Balance management for delivery crews
  - User verification workflows

- **Password Reset Flow**:
  - Token-based password reset
  - Email notification
  - Time-limited tokens
  - Secure validation

### Orders & Delivery

Handles two main types of delivery workflows:

1. **Orders Module**:
   - Shopping cart management
   - Multi-shop orders
   - Product selection and quantity
   - Order status tracking
   - Payment integration
   - Delivery assignment

2. **Delivery Requests Module**:
   - Direct delivery service requests
   - Custom pickup and delivery locations
   - Pricing calculation
   - Delivery crew assignment
   - Status tracking
   - Delivery confirmation

### Financial Management

Comprehensive system for tracking all financial aspects:

- **Transactions**:
  - Recording all financial activities
  - Balance adjustments for delivery crews
  - Transaction history and reporting
  - Multiple transaction types (order payment, balance recharge, etc.)

- **Profits**:
  - Calculation of platform profits
  - Distribution of earnings to shops and delivery crews
  - Commission calculations based on crew types
  - Financial reporting and analytics

### Shop Ecosystem

Complete shop management system:

- **Shops**:
  - Shop registration and verification
  - Location-based services
  - Shop profile management
  - Rating and feedback

- **Products**:
  - Product catalog management
  - Categories and tags
  - Pricing and availability
  - Cloud-based image handling

- **Offers**:
  - Promotional campaigns
  - Discount management
  - Limited-time offers
  - Notification integration

## Data Models

### Core Models

#### User Model

The user model extends Django's AbstractUser with additional fields for the platform's needs:

- Phone number (unique identifier)
- Profile picture
- Optional email
- Role-based user classification

User types are implemented through model inheritance:

- WassallyUser: Regular platform users
- DeliveryCrew: Delivery personnel with balance tracking and crew type classification
- ShopOwner: Shop owners with shop management capabilities

#### WassallyOrder

Base model for both standard orders and delivery requests:

- Unique order code
- Delivery crew assignment
- Receiver information
- Location reference
- Delivery status tracking
- Creation and delivery timestamps

#### Location

Geographic location management:

- Text address
- Precise coordinates (latitude/longitude)
- Location validation

### Financial Models

#### Transaction

Records all financial activities:

- User association
- Transaction amount
- Transaction type classification
- Optional descriptive details
- Transaction timestamp

#### Profit Models

Tracks various types of platform profits:

- Profit categorization
- Amount tracking
- Shop association (when applicable)
- Delivery crew association (when applicable)
- Timestamp recording

## Authentication & Authorization

### Authentication System

The project uses a hybrid authentication approach:

1. **Token Authentication**:
   - Primary authentication method for API access
   - Used by mobile apps and frontend SPA
   - Token generation on login
   - Token invalidation on logout

2. **Session Authentication**:
   - Used for admin interface
   - Cookie-based sessions
   - CSRF protection

### Authorization Framework

Sophisticated permission system combining multiple approaches:

1. **Role-Based Access Control**:
   - User roles (Customer, Shop Owner, Delivery Crew, Staff)
   - Role-specific permissions

2. **Object-Level Permissions**:
   - Users can only access their own data
   - Shop owners can only manage their shops
   - Delivery crews can only access assigned orders

3. **Custom Permission Classes**:
   - Administrative permission classes
   - Role-based permission classes
   - Custom access control based on user attributes
   - Object ownership validation

4. **Group-Based Permissions**:
   - Django groups for broader permission categories
   - Reusable permission sets

## API Design

### RESTful Architecture

The API follows RESTful principles with consistent patterns:

- Resource-based URLs
- Appropriate HTTP methods (GET, POST, PUT, DELETE)
- Proper status codes
- Pagination for list endpoints
- Filtering and sorting

### Standard Response Format

All API endpoints return a standardized response format:

```json
{
  "status": "success",
  "message": {
    "en": "English message",
    "ar": "Arabic message"
  },
  "data": { /* Response data */ }
}
```

### Error Handling

Consistent error responses:

```json
{
  "status": "error",
  "message": {
    "en": "Error description",
    "ar": "Arabic error description"
  },
  "errors": { /* Detailed error information */ }
}
```

### Filtering and Search

Advanced filtering capabilities:

- Field filtering
- Search functionality
- Date range filtering
- Pagination
- Ordering

### Swagger Documentation

Automatic API documentation with drf-yasg:

- Interactive API explorer
- Request/response schemas
- Authentication documentation
- Operation descriptions

## Financial Processing

### Balance Management

Delivery crew balance system:

- Initial balance (can be negative)
- Balance recharging by administrators
- Balance deduction when picking up orders
- Transaction history
- Balance threshold enforcement

### Profit Calculation

Sophisticated profit distribution system:

1. **For Product Orders**:
   - Platform fee calculation
   - Shop commission calculation
   - Delivery fee distribution
   - Service fee allocation

2. **For Delivery Requests**:
   - Delivery fee calculation
   - Platform cut determination
   - Crew share based on crew type (Independent vs. Dependent)

### Transaction Recording

Comprehensive transaction logging:

- Creation timestamp
- Transaction type
- Amount (positive or negative)
- Associated user
- Detailed description
- Related order/request reference

### Decimal Handling

Special attention to financial calculations:

- Using `Decimal` type to avoid floating-point errors
- Consistent rounding to 2 decimal places
- Database fields configured with appropriate precision
- String-to-decimal conversion with error handling

## Notification System

### Push Notifications

Push notification system integration:

- Device token management
- Multi-device support per user
- Topic-based notifications
- Direct notifications

### Notification Types

Various notification categories:

- Order status updates
- New order notifications for shops
- Delivery request assignments
- Balance updates
- Special offers and promotions
- System announcements

### Implementation

Notification delivery system with:
- Multiple device targeting
- Customizable notification content
- Support for additional payload data
- Background processing for large notification volumes

## Deployment Architecture

### Environment Configuration

Multiple deployment environments:

1. **Development**:
   - Local development settings
   - Debug mode enabled
   - Local database
   - Reduced security restrictions

2. **Testing Server**:
   - Separate testing environment
   - Test database
   - External services integration
   - Similar to production but with test data

3. **Production**:
   - Full security measures
   - Production database
   - Performance optimization
   - SSL enforcement

### Environment Variables

Configuration through environment variables:

- Database credentials
- API keys
- Secret keys
- Service endpoints
- Feature flags

### Security Measures

Comprehensive security setup including:

- HTTPS enforcement
- Secure cookies
- CSRF protection
- Trusted origins configuration
- Production-only security enhancements

## Testing Strategy

### Test Categories

1. **Unit Tests**:
   - Model validation
   - Utility function testing
   - Business logic verification

2. **Integration Tests**:
   - API endpoint testing
   - Database interaction
   - Permission verification

3. **Financial Tests**:
   - Balance calculation accuracy
   - Transaction recording correctness
   - Profit distribution verification

### Test Utilities

Helper functions for testing:

- User creation utilities with authentication tokens
- Authenticated test client setup
- Test data generation
- Test environment configuration

### Test Data Generation

Fixtures and factory functions:

- Random data generation
- Test database population
- Common test scenarios

## Best Practices

### Atomic Transactions

Database integrity protection:

- Transaction decorators for critical operations
- Automatic rollback on errors
- Consistent database state
- Isolation level configuration

### Decimal Handling

Proper financial calculations:

- Using `Decimal` type to avoid floating-point errors
- Consistent rounding to ensure accuracy
- Type conversion with error handling
- Configurable commission rates based on crew types

### Caching Strategy

Performance optimization:

- Cache-first approach for frequently accessed settings
- Database fallback on cache misses
- Automatic cache population and refreshing
- Configurable cache timeout

### Error Handling

Robust error management:

- Type validation for all inputs
- Consistent error response format
- Descriptive error messages
- Appropriate HTTP status codes
- Multi-language error messaging

### Serialization Pattern

Custom serialization for complex objects:

- Custom representations for complex nested structures
- Dynamic serialization based on request context
- Shop-based grouping of order items
- Calculated totals and subtotals
- Optimized queries to prevent N+1 problems

---

This architecture document provides a comprehensive overview of the Wassally platform's design and implementation. The modular approach allows for maintainable code and clear separation of concerns, while the consistent API design ensures a unified experience for frontend integration.
