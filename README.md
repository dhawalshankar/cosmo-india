# Cosmo India Prakashan

> A full-stack book publishing and e-commerce platform built to revive Indian literature and cultural heritage.

[![Live Site](https://img.shields.io/badge/live-cosmoindiaprakashan.in-c0392b?style=flat-square)](https://cosmoindiaprakashan.in)
[![Frontend](https://img.shields.io/badge/frontend-React%20%2B%20Vite-61dafb?style=flat-square)](https://react.dev)
[![Deployment](https://img.shields.io/badge/deployment-Vercel-black?style=flat-square)](https://vercel.com)

---

## About

**Cosmo India Prakashan** was founded in 1982 and distributed books and magazines all across India. After going dormant, the platform has been revived as a modern digital publishing and e-commerce system.

**Founded by:** Shri Rajkumar Ratnapriya 
**Revived & built by:** Dhawal Shukla  
**Live:** [https://cosmoindiaprakashan.in](https://cosmoindiaprakashan.in)  
**MSME Registered** | **ISBN Published**

## Product scope

Cosmo India combines a customer storefront with serverless commerce APIs:

- Product discovery for books and publications
- OTP-backed account creation and password recovery
- Guest and authenticated cart workflows
- Razorpay checkout with server-side signature verification
- Transactional customer and business email notifications
- Authenticated order history and digital-content access
- Product ratings, responsive UI, and theme support

The public storefront is intentionally separated from the internal operations
console. Inventory, employee administration, audit logs, and fulfillment are
handled by the companion `cip-admin` and `cip-backend` services.

---

## Screenshots

| Page | Preview |
|------|---------|
| Marketplace | `screenshots/marketplace.png` |
| Checkout | `screenshots/checkout.png` |
| Order Confirmation Email | `screenshots/email-confirmation.png` |
| Order History | `screenshots/orders.png` |

---

## Features

- Browse and purchase books from a curated catalog
- User authentication via JWT (HTTP-only cookies) + OTP email verification
- Forgot password flow via email
- Shopping cart — guests use localStorage, logged-in users sync to MongoDB
- Razorpay payment gateway with cryptographic signature verification
- Branded order confirmation email with logo sent to customer on purchase
- Order history for logged-in users
- Members-only section for digital content
- Interactive star ratings per product
- Dark / light theme toggle
- Responsive design

---

## Tech Stack

| Layer | Technology |
|-------|-----------|
| Frontend | React 18 (Vite), TailwindCSS, React Router v6, Context API |
| Backend | Node.js serverless functions (Vercel) |
| Database | MongoDB Atlas + Mongoose |
| Auth | JWT (HTTP-only cookies) + bcrypt |
| Payments | Razorpay |
| Email | Nodemailer (Gmail SMTP) |
| Deployment | Vercel |

---

## Project Structure

```
cosmo-india/
├── api/                          # Serverless API endpoints
│   ├── lib/
│   │   ├── db.js                 # MongoDB connection
│   │   ├── jwt.js                # Sign / verify tokens
│   │   ├── hash.js               # bcrypt helpers
│   │   └── mailer.js             # Nodemailer + OTP email
│   ├── models/
│   │   ├── user.js               # User schema (cart, orders embedded)
│   │   └── otp.js                # OTP schema
│   ├── payment/
│   │   ├── create-order.js       # Razorpay order creation
│   │   └── verify.js             # Signature verify + save order + send emails
│   ├── cart.js                   # Cart sync
│   ├── forgot-password.js        # Password reset flow
│   ├── orders.js                 # Customer order history
│   └── user.js                   # Auth, profile, OTP
│
└── src/
    ├── assets/
    ├── components/               # Reusable UI components
    ├── context/
    │   ├── AuthContext.jsx
    │   ├── CartContext.jsx
    │   └── DarkModeContext.jsx
    ├── pages/
    │   ├── Marketplace.jsx
    │   ├── Checkout.jsx
    │   ├── Orders.jsx
    │   └── ...
    ├── policies/                 # Privacy, Terms, Shipping, Refund, Contact
    ├── App.jsx
    ├── App.css
    ├── index.css
    └── main.jsx
```

---

## API Reference

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/user?action=send-otp` | Send OTP for signup |
| POST | `/api/user?action=verify-otp` | Verify OTP + create account |
| POST | `/api/user?action=login` | Login |
| GET | `/api/user?action=me` | Current user |
| POST | `/api/user?action=logout` | Logout |
| PUT | `/api/user?action=update` | Update profile (name, phone, address) |
| PUT | `/api/user?action=change-password` | Change password |
| POST | `/api/forgot-password` | Send password reset email |
| GET | `/api/orders` | Customer order history |
| GET | `/api/cart` | Get cart |
| POST | `/api/cart` | Sync cart |
| POST | `/api/payment/create-order` | Create Razorpay order |
| POST | `/api/payment/verify` | Verify payment + save order + send emails |

---

## Order Flow

```
Customer pays via Razorpay
        ↓
verify.js — signature verified
        ↓
Order saved to MongoDB (status: "paid")
        ↓
Branded confirmation email → customer
Business notification email → admin
        ↓
Customer can view order in Order History
```

## System architecture

```text
Customer browser
      │
      ▼
React/Vite storefront on Vercel
      │
      ├── Vercel serverless commerce APIs
      │       ├── JWT + OTP authentication
      │       ├── Cart and order operations
      │       └── Razorpay verification
      │
      └── MongoDB Atlas
              └── users, carts, orders, OTP records

Operations workflow
      │
      ▼
cip-admin on Vercel → cip-backend on Render
                              ├── Supabase PostgreSQL
                              │     └── inventory, employees, audit logs
                              └── MongoDB Atlas
                                    └── customer orders
```

The split provides a clear trust boundary: public commerce APIs serve
customers, while authenticated operations APIs manage fulfillment and stock.
Payment secrets and database credentials remain server-side.

---

## Environment Variables

```env
MONGODB_URI=
JWT_SECRET=
RAZORPAY_KEY_ID=
RAZORPAY_KEY_SECRET=
BUSINESS_EMAIL=
BUSINESS_EMAIL_PASSWORD=
VITE_BACKEND_URL=
VITE_RAZORPAY_KEY=
```

`RAZORPAY_KEY_SECRET`, `JWT_SECRET`, SMTP credentials, and database credentials
must never be exposed through `VITE_*` variables or committed to source
control. The publishable Razorpay key may be used by the browser; payment
signature verification remains server-side.

---

## Related Repositories

| Repo | Description |
|------|-------------|
| [cip-admin](https://github.com/dhawalshankar/cip-admin) | Internal admin panel frontend (React + TypeScript) |
| [cip-backend](https://github.com/dhawalshankar/cip-backend) | Internal admin API (Express + Supabase + MongoDB) |

---

## Production ecosystem

This repository is the public customer-facing storefront in the wider Cosmo
India Prakashan ecosystem. The React/Vite application and its Vercel
serverless API functions handle browsing, authentication, carts, checkout,
payments, and customer orders.

The separately deployed `cip-backend` service is the internal operations
control plane for products, inventory, employees, audit logs, and order
fulfillment. The storefront persists customer/order data in MongoDB Atlas,
while the operations service uses Supabase PostgreSQL for administrative and
inventory data and reads the shared MongoDB order records. Razorpay payment
verification is the boundary that turns a customer checkout into a persisted
business order and downstream fulfillment workflow.

---

## Contributions

This is a **production business platform**, not a tutorial project.

- Contributions welcome from developers comfortable working independently
- Business stability and security take priority over experimentation
- No production secrets will be shared
- Read **CONTRIBUTING.md** before opening a PR

---

## Content & Data Notice

The MIT License applies to source code only.

All books, publications, product data, images, logos, and branding associated with Cosmo India Prakashan are proprietary and protected by copyright. They may not be reused or redistributed without explicit written permission.

---

## Contact

**Dhawal Shukla**

- GitHub: [github.com/dhawalshankar](https://github.com/dhawalshankar)
- LinkedIn: [linkedin.com/in/dhawalshukl](https://linkedin.com/in/dhawalshukl)
- Email: [dhawalmannu@gmail.com](mailto:dhawalmannu@gmail.com)
- Portfolio: [dhawalshukl.vercel.app](https://dhawalshukl.vercel.app)

---

*Built with the goal of reviving Indian literature and culture through modern digital infrastructure.*