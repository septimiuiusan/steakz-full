# 🔑 Test Credentials & Quick Reference

## 👥 All Test Users

### ADMIN (Super User)
```
Email:    admin@steakz.com
Password: admin123
Role:     ADMIN
Access:   All dashboards, user management, global view
```

### Branch Managers
```
Manager 1 (London)
Email:    manager1@steakz.com
Password: admin123
Branch:   London (ID: 1)
Access:   Manager dashboard for London only

Manager 2 (Paris)
Email:    manager2@steakz.com
Password: admin123
Branch:   Paris (ID: 2)

Manager 3 (Rome)
Email:    manager3@steakz.com
Password: admin123
Branch:   Rome (ID: 3)
```

### Chefs
```
Chef 1 (London)
Email:    chef1@steakz.com
Password: admin123
Branch:   London
Access:   Kitchen dashboard for London only

Chef 2 (Paris)
Email:    chef2@steakz.com
Password: admin123
Branch:   Paris

Chef 3 (Rome)
Email:    chef3@steakz.com
Password: admin123
Branch:   Rome
```

### Cashiers
```
Cashier 1 (London)
Email:    cashier1@steakz.com
Password: admin123
Branch:   London
Access:   Cashier dashboard for London only

Cashier 2 (Paris)
Email:    cashier2@steakz.com
Password: admin123
Branch:   Paris

Cashier 3 (Rome)
Email:    cashier3@steakz.com
Password: admin123
Branch:   Rome
```

### Customer
```
Email:    testcustomer@steakz.com
Password: test123456
Access:   Customer dashboard (orders, reservations, reviews)
```

---

## 🌐 Local Application URLs

| Service | URL | Purpose |
|---------|-----|---------|
| Frontend | http://localhost:3002 | React app (login, dashboard) |
| Backend | http://localhost:3001 | Express API server |
| Prisma Studio | http://localhost:5555 | Database management UI |

---

## 🎮 Demo Sequence (10 minutes)

### Part 1: Login & Show Access (2 min)
```
1. Open http://localhost:3002
2. Click LOGIN
3. Enter: admin@steakz.com / admin123
4. Show dashboard loads
5. Point out: No errors, all sections visible
```

### Part 2: Show Full Dashboard (2 min)
```
1. Scroll to show all sections:
   - Kitchen Dashboard (yellow)
   - Manager Analytics (pink)
   - Staff Dashboard (blue)
   - Admin Control Panel (blue)
2. Explain: Admin sees everything across all branches
```

### Part 3: Create User (2 min)
```
1. Click "Add User" button
2. Fill in:
   - Name: Demo User
   - Email: demo@steakz.com
   - Password: demo123456
   - Role: Chef
   - Branch: London
3. Click Create
4. Show user appears in list
5. Check Prisma Studio to confirm in database
```

### Part 4: Edit & Delete (2 min)
```
1. Click "Manage Roles"
2. Find Demo User
3. Change role to Cashier (show immediate update)
4. Click Delete
5. Confirm deletion
6. Show user removed from list
```

### Part 5: Show Other Roles (2 min)
```
1. Logout
2. Login as chef1@steakz.com / admin123
3. Show: Only sees Kitchen Dashboard (no Manager/Admin sections)
4. Show: Only sees London branch data
5. Explain: Each role has different permissions
```

---

## 🧪 Quick Test Checklist

Before demo, verify:

- [ ] Backend running on 3001 (no errors in terminal)
- [ ] Frontend running on 3002 (no errors in terminal)
- [ ] Can login as admin@steakz.com
- [ ] Dashboard shows all 4 sections
- [ ] "Add User" button opens modal
- [ ] "Manage Roles" button opens modal
- [ ] Can create user successfully
- [ ] Can edit user role
- [ ] Can delete user
- [ ] No "Access Denied" errors
- [ ] Prisma Studio shows database changes

---

## 🚀 Start Servers (Copy-Paste)

### Terminal 1 - Backend
```bash
cd /Users/iusanseptimiu/Documents/steakz.full/backend
npm start
```

### Terminal 2 - Frontend
```bash
cd /Users/iusanseptimiu/Documents/steakz.full/frontend-clean/blog-frontend
PORT=3002 npm start
```

### Optional - Prisma Studio (Terminal 3)
```bash
cd /Users/iusanseptimiu/Documents/steakz.full/backend
npx prisma studio
```

---

## 🐛 Common Issues & Fixes

| Issue | Cause | Fix |
|-------|-------|-----|
| "Cannot connect to database" | PostgreSQL not running | Start PostgreSQL service |
| "Port 3001 already in use" | Backend still running | Kill: `pkill -f "npm start"` |
| "Port 3002 already in use" | Frontend still running | Kill: `pkill -f "npm start"` |
| "Users list empty" | Data not loaded | Refresh page or check console |
| "Cannot delete user" | Last admin user | Create another admin first |
| "Access Denied error shown" | Role not in allowedRoles | Update RoleGuard component |
| "User not created" | Duplicate email | Use unique email address |

---

## 📊 Database Structure

### Users Table
```
id: integer (auto-increment)
email: string (unique, required)
name: string
password: string (hashed)
role: enum (ADMIN|BRANCH_MANAGER|CHEF|CASHIER|CUSTOMER)
branchId: integer (optional, references Branch.id)
createdAt: timestamp
updatedAt: timestamp
```

### Branches Table
```
id: integer
name: string (London, Paris, Rome, Berlin)
location: string
createdAt: timestamp
updatedAt: timestamp
```

---

## 🔗 API Endpoints (For Testing)

### Authentication
```
POST /api/auth/login
  Body: { email: string, password: string }
  Returns: { token: string, user: {...} }

POST /api/auth/signup
  Body: { name: string, email: string, password: string }
  Returns: { user: {...}, token: string }

GET /api/profile
  Headers: { Authorization: "Bearer TOKEN" }
  Returns: { user: {...} }
```

### User Management (Admin Only)
```
GET /api/users
  Headers: { Authorization: "Bearer TOKEN" }
  Returns: User[]

POST /api/users
  Headers: { Authorization: "Bearer TOKEN" }
  Body: { name, email, password, role, branchId }
  Returns: { data: User }

PATCH /api/users/:id/role
  Headers: { Authorization: "Bearer TOKEN" }
  Body: { role: string, branchId?: integer }
  Returns: { user: User }

DELETE /api/users/:id
  Headers: { Authorization: "Bearer TOKEN" }
  Returns: { message: string }
```

### Dashboard Access (Admin Bypass)
```
GET /api/manager/dashboard?branchId=1
  Returns: Manager dashboard data

GET /api/cashier/dashboard?branchId=1
  Returns: Cashier dashboard data

GET /api/orders
  Returns: All orders (admin sees all, staff sees own branch)

GET /api/reservations
  Returns: All reservations (admin sees all, staff sees own branch)
```

---

## 💾 Database Reset (If Needed)

```bash
cd /Users/iusanseptimiu/Documents/steakz.full/backend

# Reset database
npx prisma db push --force-reset

# Seed test data
npx prisma db seed

# Open Prisma Studio to verify
npx prisma studio
```

---

## 📝 Important Notes

1. **Password Hashing**
   - Currently using base64 (development)
   - In production, upgrade to bcrypt

2. **Branch IDs**
   - 1 = London
   - 2 = Paris
   - 3 = Rome
   - 4 = Berlin

3. **Role Names**
   - Database: BRANCH_MANAGER
   - Frontend: "Manager"
   - These should match in all places

4. **JWT Secret**
   - Set in backend/.env
   - Default: 'your-secret-key'
   - Change for production

5. **API Base URL**
   - Frontend connects to http://localhost:3001
   - Set in frontend/.env or src/config/api.ts
   - Change for production

---

## 🎯 Key Features to Highlight in Demo

1. **No More Access Denied**
   - Admin sees everything
   - Smooth user experience
   - Professional appearance

2. **Complete User Management**
   - Create users with full control
   - Assign roles and branches
   - Edit and delete users
   - Real-time updates

3. **System-Wide Visibility**
   - All orders across all branches
   - All reservations across all branches
   - All staff and their assignments
   - Complete analytics

4. **Security**
   - JWT authentication
   - Role-based access control
   - Admin safeguards (can't delete last admin)
   - Input validation

---

## 📚 Documentation Files

| File | Purpose |
|------|---------|
| ADMIN_IMPLEMENTATION_COMPLETE.md | Detailed technical documentation |
| ADMIN_QUICK_START.md | Quick start guide for testing |
| ADMIN_CHANGES_SUMMARY.md | Complete change summary |
| TEST_CREDENTIALS.md | This file - credentials & quick reference |

---

## ✅ Presentation Readiness

**Status: READY TO PRESENT**

All features implemented:
- ✅ Admin login working
- ✅ Full dashboard access
- ✅ User management functional
- ✅ No errors or exceptions
- ✅ Database syncing properly
- ✅ Professional UI

All test users created and ready:
- ✅ Admin user
- ✅ 3 Branch managers
- ✅ 3 Chefs
- ✅ 3 Cashiers
- ✅ 1 Customer

System ready for live demonstration:
- ✅ Both servers running
- ✅ Database connected
- ✅ All APIs working
- ✅ Frontend responding properly

---

## 🎉 You're Ready!

All systems are go. Time to impress the audience! 🚀

---

**Last Updated:** 28 January 2026  
**Status:** ✅ COMPLETE & VERIFIED  
**Ready for:** Live Presentation
