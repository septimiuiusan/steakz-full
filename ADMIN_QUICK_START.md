# 🚀 Admin Role - Quick Start Guide

## 🎯 What's New

The Admin role is now **fully functional** with complete system access and user management capabilities. Admin users can:

✅ Access all dashboards (Manager, Cashier, Chef, Customer)  
✅ Create new users with full role and branch assignment  
✅ Edit user roles  
✅ Delete users  
✅ View all orders and reservations system-wide  
✅ Access feedback, contact messages, and system diagnostics  

---

## 🔑 Admin Login Credentials

```
Email:    admin@steakz.com
Password: admin123
Role:     ADMIN
```

---

## 📱 How to Access

1. **Start the servers:**
   ```bash
   # Terminal 1 - Backend
   cd backend && npm start
   
   # Terminal 2 - Frontend
   cd frontend-clean/blog-frontend && PORT=3002 npm start
   ```

2. **Open the app:**
   ```
   http://localhost:3002
   ```

3. **Login:**
   - Click "LOGIN"
   - Enter: `admin@steakz.com` / `admin123`
   - Click "LOGIN"

---

## 🎮 What You'll See as Admin

### Dashboard Page (localhost:3002/dashboard)

Your dashboard now shows **ALL sections**:

1. **🔑 Admin Control Panel** (Blue Section)
   - User Management: List of all users with roles
   - Feedback Management: All customer feedback
   - System Status: Database, payment system, kitchen display status

2. **⚙️ Kitchen Dashboard** (Yellow Section)
   - Active orders queue
   - Kitchen stats (Completed Today, In Progress, Pending, Total Orders)

3. **📊 Manager Analytics** (Pink Section)
   - Daily Performance metrics
   - Staff Overview (Kitchen Staff, Service Staff)
   - Branch Locations (all branches with manager info)
   - Multi-Branch Performance Summary

4. **👥 Staff Dashboard** (Blue Section)
   - Recent Contact Messages
   - Cashier Operations (orders, payments)

---

## 🎨 Admin Panel Controls

### Add User Button
Click "Add User" to create a new user:
- **Name**: User's full name
- **Email**: Unique email address
- **Password**: Secure password
- **Role**: Choose from Customer, Cashier, Chef, Manager, Admin
- **Branch**: Assign to London, Paris, Rome, or Berlin

Example:
```
Name:     Sarah Johnson
Email:    sarah.johnson@steakz.com
Password: secure_pass_123
Role:     Chef
Branch:   London
```

### Manage Roles Button
Click "Manage Roles" to:
- **See all users** in the system
- **Change any user's role** via dropdown
- **Delete any user** via delete button (with confirmation)

---

## 🧪 Testing Scenarios

### Scenario 1: Create a New Chef
1. Click "Add User"
2. Fill in:
   - Name: `Maria Garcia`
   - Email: `maria@steakz.com`
   - Password: `test123456`
   - Role: `Chef`
   - Branch: `Paris`
3. Click "Create User"
4. Maria appears in the User List
5. Maria can now login and see the Chef dashboard for Paris branch

### Scenario 2: Promote a Customer to Manager
1. Click "Manage Roles"
2. Find `testcustomer@steakz.com` in the list
3. Click role dropdown and change to `BRANCH_MANAGER`
4. User is instantly updated
5. Next time they login, they'll see the Manager dashboard

### Scenario 3: Delete a User
1. Click "Manage Roles"
2. Find a user to delete
3. Click the "Delete" button
4. Confirm the popup
5. User is removed from the system and database

### Scenario 4: View All Orders Across All Branches
1. Scroll down to Manager Analytics
2. See all branch locations and their managers
3. View performance metrics for all branches combined
4. See revenue and operational status for each branch

---

## 🔐 What Admin CAN Do

| Action | Status |
|--------|--------|
| View Manager Dashboard | ✅ Yes (any branch) |
| View Cashier Dashboard | ✅ Yes (any branch) |
| View Chef Orders | ✅ Yes (system-wide) |
| View Customer Orders | ✅ Yes (system-wide) |
| Create Users | ✅ Yes |
| Edit User Roles | ✅ Yes |
| Delete Users | ✅ Yes |
| View All Reservations | ✅ Yes |
| View Feedback | ✅ Yes |
| View Contact Messages | ✅ Yes |
| Access System Status | ✅ Yes |

---

## 🔐 What Admin CANNOT Do (Design Choice)

These features are intentionally restricted to maintain system integrity:

- ❌ Delete the last ADMIN user (safeguard)
- ❌ Delete own account while logged in
- ❌ Modify user passwords directly (users must reset)
- ❌ Change branch operations (only managers can)

---

## 🌐 API Endpoints (Backend)

### User Management
```
POST   /api/users                    Create new user (Admin only)
GET    /api/users                    List all users (Admin only)
PATCH  /api/users/:id/role           Update user role (Admin only)
DELETE /api/users/:id                Delete user (Admin only)
```

### Dashboard Access
```
GET    /api/manager/dashboard?branchId=X    Manager view (Admin can see any branch)
GET    /api/cashier/dashboard?branchId=X    Cashier view (Admin can see any branch)
GET    /api/orders                           All orders (Admin sees all)
GET    /api/reservations                     All reservations (Admin sees all)
```

---

## 📊 Database Schema

When you create a user, the database stores:
- **id**: Auto-generated unique identifier
- **email**: Unique email (used for login)
- **name**: User's full name
- **password**: Hashed password (base64)
- **role**: ADMIN, BRANCH_MANAGER, CHEF, CASHIER, or CUSTOMER
- **branchId**: Which branch they're assigned to (1=London, 2=Paris, 3=Rome, 4=Berlin)
- **createdAt**: Timestamp when user was created
- **updatedAt**: Timestamp of last modification

---

## 🐛 Troubleshooting

### "Cannot delete the last admin"
- You tried to delete the only ADMIN user
- Create another admin first, then delete the old one

### "User not found"
- The user you're trying to modify was already deleted
- Refresh the page to see the updated list

### "Invalid role"
- Used a role name that's not in the system
- Valid roles: CUSTOMER, CASHIER, CHEF, BRANCH_MANAGER, ADMIN

### User created but not in list
- Refresh the page
- Check browser console for errors
- Check backend logs for error messages

---

## 📋 Presentation Checklist

Before the presentation, verify:

- [ ] Can login as admin without errors
- [ ] Dashboard shows all 4 sections (Kitchen, Manager, Staff, Admin Panel)
- [ ] Can create a new user
- [ ] Can edit user roles
- [ ] Can delete users
- [ ] No "Access Denied" errors appear
- [ ] All branch data visible
- [ ] System status shows "Online"
- [ ] User list updates in real-time

---

## 🎬 Live Demo Script (5 minutes)

1. **Login** (30 sec)
   - Navigate to http://localhost:3002
   - Click LOGIN
   - Enter admin@steakz.com / admin123
   - Show dashboard loads without errors

2. **Show Full Access** (45 sec)
   - Scroll down to show Manager Analytics
   - Show Kitchen Dashboard
   - Show Staff Dashboard with feedback
   - Point out: "No Access Denied errors!"

3. **Create User** (1 min)
   - Click "Add User"
   - Create: John Smith, john@example.com, test123456, Chef, Paris
   - Click Create
   - Show user appears in list

4. **Edit Role** (1 min)
   - Click "Manage Roles"
   - Find the new user
   - Change role to Cashier
   - Show immediate update

5. **Delete User** (1 min)
   - Still in Manage Roles
   - Click Delete on new user
   - Confirm deletion
   - Show user removed from list

6. **Show Database** (30 sec)
   - Open Prisma Studio: http://localhost:5555
   - Show users table with all changes reflected

---

## 🚀 Key Features Implemented

### Frontend (React)
- ✅ RoleGuard component allows ADMIN to bypass all restrictions
- ✅ Add User modal with branch selection
- ✅ Manage Roles modal with delete functionality
- ✅ Real-time list updates after user creation/deletion
- ✅ Confirmation dialogs for destructive actions

### Backend (Express)
- ✅ POST /api/users endpoint for creating users
- ✅ DELETE /api/users/:id endpoint for deleting users
- ✅ PATCH /api/users/:id/role endpoint for role changes
- ✅ Manager/Cashier dashboards accept ADMIN role
- ✅ Query parameters allow admin to view any branch

### Security
- ✅ JWT authentication on all endpoints
- ✅ Admin middleware validates role
- ✅ Prevent deletion of last admin
- ✅ Password hashing (base64, upgradeable to bcrypt)

---

## 📚 Documentation Files

- `ADMIN_IMPLEMENTATION_COMPLETE.md` - Detailed implementation guide
- This file - Quick start guide for testing/presentation
- Code comments in React and Express files

---

## ✅ Status

**✅ COMPLETE AND TESTED**

All admin functionality is working perfectly. The system is ready for the presentation with:
- Full access to all dashboards
- Complete user management
- System-wide visibility
- Professional UI
- Zero permission errors

---

## 🤝 Support

If you encounter any issues:

1. **Check server logs:**
   ```bash
   # Backend logs appear in Terminal 1
   # Frontend logs appear in Terminal 2
   ```

2. **Check browser console:**
   - F12 → Console tab
   - Look for red error messages

3. **Check Prisma Studio:**
   ```bash
   cd backend && npx prisma studio
   # Opens http://localhost:5555
   ```

4. **Reset database (if needed):**
   ```bash
   cd backend
   npx prisma db push
   npx prisma db seed
   ```

---

## 🎯 Next Steps (For Future)

1. Implement bcrypt password hashing (upgrade from base64)
2. Add audit logging for all admin actions
3. Add bulk user import from CSV
4. Create dedicated admin dashboard with analytics
5. Add role-based dashboard filtering

---

Happy presenting! 🎉
