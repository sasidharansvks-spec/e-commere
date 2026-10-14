# Final eCommerce Store — MERN Stack Capstone Project

A full-stack e-commerce application built with **MongoDB, Express.js, React, and Node.js** (MERN) — built to satisfy the "Capstone Project: Final eCommerce Store" brief: user auth, product listing, cart, checkout with payment, order confirmation, and admin product management.

## ✨ Features

- User registration & login with JWT authentication (passwords hashed with bcrypt)
- Protected routes (checkout, orders) and an admin-only product management area
- Product listing with search and category filters, plus a product details page
- Persistent shopping cart (add, update quantity, remove) — saved in the browser
- Full checkout flow: shipping address + sandbox/test-mode payment
- Server-side price calculation, stock validation, and stock deduction on purchase (prices are never trusted from the client)
- Order confirmation / order summary page, and an order history page
- Admin dashboard to add, edit, and delete products

## 🧱 Tech Stack

| Layer      | Technology                                      |
|------------|--------------------------------------------------|
| Frontend   | React 18, React Router 6, Axios, Vite             |
| Backend    | Node.js, Express.js                               |
| Database   | MongoDB with Mongoose                             |
| Auth       | JSON Web Tokens (JWT), bcrypt password hashing    |
| Payments   | Simulated sandbox/test-mode payment gateway       |

## 📁 Project Structure

```
mern-ecommerce-capstone/
├── backend/   # Express + MongoDB REST API
│   ├── config/          # DB connection
│   ├── models/          # User, Product, Order (Mongoose schemas)
│   ├── middleware/      # JWT auth guard, admin guard, error handling
│   ├── controllers/     # Route logic
│   ├── routes/          # Express routers
│   ├── utils/           # Token signing, sandbox payment gateway
│   └── seed/            # Sample products + admin user loader
└── frontend/  # React (Vite) client
    └── src/
        ├── api/         # Axios instance (attaches the JWT)
        ├── context/     # Auth + Cart global state (React Context)
        ├── components/  # Navbar, ProductCard, route guards, etc.
        └── pages/        # Home, product details, cart, checkout, orders, admin
```

## 🚀 Getting Started Locally

### 1. Prerequisites
- Node.js 18+
- A MongoDB database — either install MongoDB locally, or create a **free MongoDB Atlas cluster** at https://www.mongodb.com/cloud/atlas (takes about 5 minutes)

### 2. Backend setup
```bash
cd backend
cp .env.example .env     # then fill in MONGO_URI and a JWT_SECRET
npm install
npm run seed              # loads 10 sample products + a demo admin user
npm run dev                # starts the API on http://localhost:5000
```

Demo admin account created by the seed script:
- **Email:** `admin@example.com`
- **Password:** `admin123`

### 3. Frontend setup
In a second terminal:
```bash
cd frontend
cp .env.example .env     # VITE_API_URL=http://localhost:5000/api
npm install
npm run dev
```
Visit **http://localhost:5173**

Both `npm install`s and a full `vite build` were already run and verified while generating this project, so the code is confirmed to install and compile cleanly — you're just pointing it at your own database.

## 💳 Testing the Payment Flow

This project uses a **simulated sandbox payment gateway** (`backend/utils/mockPaymentGateway.js`) — no real card processor is ever called, and full card numbers/CVVs are never stored, only the brand and last 4 digits (for the order confirmation screen). This mirrors how real test-mode gateways like Stripe work, matching the assignment's note that you "may use sandbox or test mode for the payment gateway."

At checkout, use:
- `4242 4242 4242 4242` → payment succeeds
- `4000 0000 0000 0002` → payment is declined (any name/expiry/CVV)

To swap in a real provider later (Stripe, Razorpay, etc., in test mode), replace the body of `processSandboxPayment` with a call to that provider's test-mode SDK — the rest of the checkout flow in `orderController.js` doesn't need to change.

## ☁️ Deploying

1. **Database:** Create a free cluster on MongoDB Atlas, add a database user, and allow network access from anywhere (`0.0.0.0/0`) for simplicity. Copy the connection string into `MONGO_URI`.
2. **Backend:** Push this repo to GitHub, then deploy the `backend` folder to [Render](https://render.com) (or Railway/Cyclic) as a Web Service — build command `npm install`, start command `npm start`. Add the environment variables from `.env.example` in the dashboard.
3. **Frontend:** Deploy the `frontend` folder to [Vercel](https://vercel.com) or [Netlify](https://netlify.com) — build command `npm run build`, output directory `dist`. Set `VITE_API_URL` to your deployed backend URL + `/api`.
4. Update `CLIENT_URL` on the backend to your deployed frontend URL so CORS allows it.

## 🗺️ API Overview

| Method | Endpoint                | Access   | Description                                     |
|--------|--------------------------|----------|---------------------------------------------------|
| POST   | `/api/auth/register`      | Public   | Create an account                                  |
| POST   | `/api/auth/login`          | Public   | Log in, receive a JWT                              |
| GET    | `/api/auth/profile`         | Private  | Current user's profile                             |
| GET    | `/api/products`               | Public  | List products (`?keyword=`, `?category=`)          |
| GET    | `/api/products/:id`             | Public  | Product details                                    |
| POST   | `/api/products`                   | Admin  | Create a product                                   |
| PUT    | `/api/products/:id`                 | Admin | Update a product                                   |
| DELETE | `/api/products/:id`                   | Admin | Delete a product                                   |
| POST   | `/api/orders`                           | Private | Checkout — creates an order & runs sandbox payment |
| GET    | `/api/orders/myorders`                    | Private | The logged-in user's orders                        |
| GET    | `/api/orders/:id`                           | Private | One order's details (its owner or an admin)         |
| GET    | `/api/orders`                                 | Admin | All orders                                          |
| PUT    | `/api/orders/:id/status`                        | Admin | Update an order's fulfillment status                |

The admin order endpoints (`GET /api/orders`, `PUT /api/orders/:id/status`) are ready on the backend; wiring up an admin "all orders" screen in the frontend is a natural next feature if you want to extend the project further.

## 📄 For Your Assignment Submission

The brief asks for **one single file** (PDF, Word, or slide deck) as the actual submission, separate from the code. `PROJECT_REPORT_TEMPLATE.md` in this folder is a ready-to-fill draft that follows the suggested structure (cover page, overview, features, tech stack, system flow, screenshots, links, challenges, reflection) with the technical sections already written — just fill in your GitHub link, deployment link, and screenshots once it's live, then export it to PDF or Word.
