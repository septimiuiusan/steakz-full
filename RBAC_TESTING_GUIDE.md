# RBAC Testing Guide

## Quick API Endpoints Reference

### Authentication
```bash
# Login (get JWT token)
POST /api/auth/login
{ "email": "admin@steakz.com", "password": "password" }
Response: { "token": "jwt...", "user": { "id", "email", "role", "branchId" } }
```

## Test Scenarios by Role

### 1. ADMIN Testing
```bash
# Create a new user
POST /api/users
Authorization: Bearer {admin_token}
{ 
  "name": "New Manager",
  "email": "manager@steakz.com",
  "password": "secure123",
  "role": "BRANCH_MANAGER",
  "branchId": 1
}

# View all users
GET /api/users
Authorization: Bearer {admin_token}

# View all orders (all branches)
GET /api/orders
Authorization: Bearer {admin_token}

# Update user role
PATCH /api/users/:userId/role
Authorization: Bearer {admin_token}
{ "role": "CHEF" }

# Assign user to branch
PATCH /api/users/:userId/branch
Authorization: Bearer {admin_token}
{ "branchId": 2 }

# View all contacts
GET /api/contacts
Authorization: Bearer {admin_token}
```

### 2. BRANCH_MANAGER Testing
```bash
# View branch users
GET /api/users/branch/1
Authorization: Bearer {manager_token}
# Expected: 403 (unauthorized)

# View own branch orders
GET /api/orders
Authorization: Bearer {manager_token}
# Expected: Only orders from their branch (branchId=1)

# Update order status
PATCH /api/orders/:orderId
Authorization: Bearer {manager_token}
{ "status": "completed" }
# Expected: Success if order is from their branch, 403 if cross-branch

# View branch contacts
GET /api/contacts
Authorization: Bearer {manager_token}
# Expected: Only contacts from their branch

# Try to create user (should fail)
POST /api/users
Authorization: Bearer {manager_token}
{ "name": "Test", "email": "test@test.com", "password": "123", "role": "CUSTOMER" }
# Expected: 403 Forbidden
```

### 3. CHEF Testing
```bash
# View orders
GET /api/orders
Authorization: Bearer {chef_token}
# Expected: Only orders from their branch

# Update order with state transition (valid)
PATCH /api/orders/:orderId
Authorization: Bearer {chef_token}
{ "status": "preparing" }
# Valid: If current status is PENDING, status becomes PREPARING ✓

# Try arbitrary status update (invalid)
PATCH /api/orders/:orderId
Authorization: Bearer {chef_token}
{ "status": "completed" }
# Expected: 403 - Invalid state transition (must go PENDING→PREPARING→READY→COMPLETED)

# Try to view reservations (should fail)
GET /api/reservations
Authorization: Bearer {chef_token}
# Expected: 403 Forbidden - CHEF cannot view reservations

# Try to create order (should fail)
POST /api/orders
Authorization: Bearer {chef_token}
{ "branchId": 1, "items": [...] }
# Expected: 403 Forbidden - Only CUSTOMER/ADMIN can create
```

### 4. CASHIER Testing
```bash
# View orders
GET /api/orders
Authorization: Bearer {cashier_token}
# Expected: Only orders from their branch

# Mark order as cancelled
PATCH /api/orders/:orderId
Authorization: Bearer {cashier_token}
{ "status": "cancelled" }
# Expected: 200 Success

# Mark order as confirmed
PATCH /api/orders/:orderId
Authorization: Bearer {cashier_token}
{ "status": "confirmed" }
# Expected: 200 Success

# Try to set arbitrary status (invalid)
PATCH /api/orders/:orderId
Authorization: Bearer {cashier_token}
{ "status": "preparing" }
# Expected: 403 - CASHIER can only use cancelled/confirmed

# Try to view reservations (should fail)
GET /api/reservations
Authorization: Bearer {cashier_token}
# Expected: 403 Forbidden

# Try to create order (should fail)
POST /api/orders
Authorization: Bearer {cashier_token}
{ "branchId": 1, "items": [...] }
# Expected: 403 Forbidden
```

### 5. CUSTOMER Testing
```bash
# Create order
POST /api/orders
Authorization: Bearer {customer_token}
{
  "branchId": 1,
  "items": [
    { "id": 1, "name": "Steak", "price": 25.99, "quantity": 1 }
  ]
}
# Expected: 201 Created

# View own orders
GET /api/orders
Authorization: Bearer {customer_token}
# Expected: Only their own orders

# View specific own order
GET /api/orders/:orderId
Authorization: Bearer {customer_token}
# Expected: Success if it's their order, 403 if belongs to other customer

# Try to view other customer's order (cross-customer access)
GET /api/orders/:otherCustomerId_orderId
Authorization: Bearer {customer_token}
# Expected: 403 Forbidden

# Create reservation
POST /api/reservations
Authorization: Bearer {customer_token}
{
  "branchId": 1,
  "reservationDate": "2025-02-01T19:00:00Z",
  "partySize": 4
}
# Expected: 201 Created

# View own reservations
GET /api/reservations
Authorization: Bearer {customer_token}
# Expected: Only their own reservations

# Create review
POST /api/reviews
Authorization: Bearer {customer_token}
{
  "branchId": 1,
  "rating": 5,
  "comment": "Excellent steaks and great service!"
}
# Expected: 201 Created

# View all reviews (public)
GET /api/reviews
# No auth needed
# Expected: All reviews from all branches

# Update own review
PATCH /api/reviews/:reviewId
Authorization: Bearer {customer_token}
{ "rating": 4, "comment": "Updated comment" }
# Expected: 200 Success if it's their review

# Delete own review
DELETE /api/reviews/:reviewId
Authorization: Bearer {customer_token}
# Expected: 200 Success if it's their review

# Try to create user (should fail)
POST /api/users
Authorization: Bearer {customer_token}
{ "name": "Test", "email": "test@test.com", "password": "123", "role": "CUSTOMER" }
# Expected: 403 Forbidden
```

## Cross-Branch Access Tests

### Branch Manager Testing Cross-Branch
```bash
# View order from different branch
GET /api/orders/99  # order from branch 2
Authorization: Bearer {branch1_manager_token}
# Expected: 403 Forbidden

# View contact from different branch
GET /api/contacts/99  # contact from branch 3
Authorization: Bearer {branch1_manager_token}
# Expected: 403 Forbidden

# Try to view users from different branch
GET /api/users/branch/2
Authorization: Bearer {branch1_manager_token}
# Expected: 403 Forbidden
```

## Public Endpoints (No Auth Required)

### Contact Form Submission
```bash
POST /api/contact
{
  "name": "John Doe",
  "email": "john@example.com",
  "message": "I have a question about your menu",
  "branchId": 1
}
# Expected: 201 Created

# Can also submit without specifying branch
POST /api/contact
{
  "name": "Jane Smith",
  "email": "jane@example.com",
  "message": "General inquiry"
}
# Expected: 201 Created with branchId=null
```

## Error Code Reference

| Code | Scenario | Role |
|------|----------|------|
| 403 | User tries to access admin-only endpoint | CUSTOMER, BRANCH_MANAGER, CHEF, CASHIER |
| 403 | CHEF/CASHIER try to view reservations | CHEF, CASHIER |
| 403 | CHEF tries arbitrary status update | CHEF |
| 403 | Staff member views cross-branch data | BRANCH_MANAGER, CHEF, CASHIER |
| 403 | Non-admin tries to manage users | Non-ADMIN |
| 403 | Chef uses invalid state transition | CHEF |
| 404 | Resource not found | Any (legitimate not found) |
| 400 | Invalid role provided | ADMIN creating user |
| 400 | Missing required field | Any |
| 401 | Missing/invalid JWT token | Any (if endpoint requires auth) |

## Chef State Transition Rules (Cheat Sheet)

Only these transitions are allowed for CHEF:
```
PENDING → PREPARING ✓
PREPARING → READY ✓
READY → COMPLETED ✓

Any other combination: 403 FORBIDDEN ✗
Examples of invalid transitions:
- PENDING → READY (skip preparing)
- PREPARING → COMPLETED (skip ready)
- READY → PREPARING (going backwards)
- PENDING → CANCELLED (only CASHIER can do this)
```

## Postman Collection Setup

### Environment Variables
```json
{
  "admin_token": "value_from_admin_login",
  "manager_token": "value_from_manager_login",
  "chef_token": "value_from_chef_login",
  "cashier_token": "value_from_cashier_login",
  "customer_token": "value_from_customer_login",
  "base_url": "http://localhost:3001/api"
}
```

### Request Template Headers
```
Authorization: Bearer {{admin_token}}
Content-Type: application/json
```

## Quick Validation Checklist

- [ ] ADMIN can access all routes
- [ ] BRANCH_MANAGER limited to own branch
- [ ] CHEF has state transition restrictions
- [ ] CASHIER limited to cancelled/confirmed only
- [ ] CUSTOMER can only access own data
- [ ] CHEF/CASHIER cannot view reservations
- [ ] Cross-branch access returns 403
- [ ] Public contact form works without auth
- [ ] All non-public endpoints require valid JWT

## Troubleshooting

### "Access denied" error on valid request
- Check token is valid and not expired
- Verify user role in token matches endpoint requirements
- Confirm user's branchId matches resource's branchId

### 404 errors on valid resource
- Confirm resource exists in database
- Check branchId matches your assigned branch
- For CUSTOMER: resource must belong to logged-in user

### State transition errors for CHEF
- Only follow: PENDING → PREPARING → READY → COMPLETED
- Cannot skip steps
- Cannot go backwards
- Use allowed transitions only

### Cross-branch access denied
- Ensure you're using correct token for the branch
- BRANCH_MANAGER tokens limited to assigned branch
- ADMIN tokens can access all branches
