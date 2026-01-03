# 🎓 BiteBuddy - Viva Preparation Guide

This guide covers everything you need to know for your exam, including architecture, core features, and live modification tips.

## 🏗️ Architecture Overview

- **Frontend**: React.js with Vite. Styled using Vanilla CSS and Tailwind (for glassmorphism). Icons by `lucide-react`.
- **Backend**: Node.js with Express.
- **Database**: MongoDB (Atlas) using Mongoose ODM.
- **State Management**: React `useState` and `useEffect`.
- **API Calls**: Custom Axios configuration in `frontend/src/config/axiosConfig.js`.

---

## 🛠️ Feature Breakdown

### 1. Login & Registration
- **Backend Logic**: Uses `bcrypt` for password comparison and `jwt` for token generation.
- **Key Code (`authController.js`)**:
  ```javascript
  // Comparing passwords during login
  const match = await bcrypt.compare(password, user.password);
  
  // Generating JWT token
  const token = jwt.sign({ id: user._id, role: user.role }, process.env.JWT_SECRET);
  ```

### 2. Password Reset
- **Logic**: Generates a random token and checks for expiry.
- **Key Code (`authController.js`)**:
  ```javascript
  // Generating token (Forgot Password)
  const token = crypto.randomBytes(20).toString("hex");
  user.resetPasswordExpires = Date.now() + 3600000; // 1 hour expiry
  
  // Verifying token (Reset Password)
  const user = await User.findOne({
    resetPasswordToken: token,
    resetPasswordExpires: { $gt: Date.now() }
  });
  ```

### 3. Cart System
- **Logic**: Persists items in MongoDB.
- **Key Code**:
  ```javascript
  // Frontend Calculation (Cart.jsx)
  const subTotal = cart.reduce((sum, i) => sum + i.price * i.qty, 0);

  // Backend Fetch (cartControllers.js)
  const cart = await Cart.findOne({ userId: req.user.id });
  ```

### 4. Checkout & Order Placement
- **Logic**: Validates inputs and creates a new Order.
- **Key Code (`orderControllers.js`)**:
  ```javascript
  const newOrder = new Order({
    orderId: generateShortId(), // e.g. "AB12CD34"
    customerId: req.user.id,
    items: orderItems,
    totalPrice: totalPrice,
    status: "Pending"
  });
  await newOrder.save();
  ```

### 5. Admin Order Assignment
- **Logic**: Updates the order status and assigns a partner.
- **Key Code (`orderControllers.js`)**:
  ```javascript
  export const assignOrder = async (req, res) => {
    const { deliveryPartnerId } = req.body;
    order.deliveryPartnerId = deliveryPartnerId;
    order.status = "Assigned";
    await order.save();
  };
  ```

### 6. Delivery Flow
- **Logic**: Updates order status as it moves through stages.
- **Key Code (`deliveryController.js`)**:
  ```javascript
  // Status Cycle: Assigned -> Picked Up -> On the Way -> Delivered
  order.status = status;
  await order.save();
  ```

---

## 🌗 Conditional Rendering (Showing/Hiding Elements)

A common question is: "How do you hide the Cart or Profile when the user isn't logged in?"

- **Logic**: We check if a JWT token exists in `localStorage`.
- **Location**: `Navbar.jsx`.
- **Code snippet**:
  ```jsx
  {localStorage.getItem("token") ? (
    // Show this if logged in
    <Link to="/cart">Cart</Link>
  ) : (
    // Show this if NOT logged in
    <Link to="/register">Register</Link>
  )}
  ```
- **Explanation**: This is called a **Ternary Operator**. If the condition (token exists) is true, it renders the first part; otherwise, it renders the second part.

---

## 💳 Payment Gateway (How it works)

If they ask: "Did you use a real payment gateway (like Stripe or SSLCommerz)?"

- **The Answer**: No, we implemented a **Payment Simulation**.
- **Why?**: To demonstrate the complete User Experience (UX) flow without needing real bank accounts or exposing API keys during a viva.
- **How it works**:
  1. User selects "Pay Online" (Bkash/Nagad/Rocket).
  2. The app triggers a sequence of `toast` messages (e.g., "Redirecting...", "Processing...").
  3. We use `setTimeout()` to mimic the time it takes for a real server to respond.
  4. Once "Payment" is successful, we send the order data to our own Backend (`POST /orders`).
- **If they ask how you "installed" it**:
  - Say: "I didn't install an external gateway library. I built a custom simulation logic using React state and asynchronous functions to demonstrate how the flow would look to a real customer."

---

## 📦 Order Assignment & Delivery Flow

This is a multi-step process involving the Admin and the Delivery Partner.

### 1. Admin Assigning Dashboard (`AdminDashboard.jsx`)
- **Purpose**: To manage logistics.
- **How it works**:
  - The admin fetches all orders from `GET /orders/all`.
  - They also fetch all users with the "delivery" role from `GET /auth/partners`.
  - When the admin selects a partner from the dropdown, it calls `PUT /orders/assign/:orderId`.
  - **Backend Logic**: In `orderControllers.js`, the `assignOrder` function updates the `deliveryPartnerId` field in the Order document and changes the status to **"Assigned"**.

### 2. Delivery Panel (`DeliveryDashboard.jsx`)
- **Purpose**: A dedicated workspace for delivery riders.
- **How it works**:
  - When a delivery rider logs in, they are redirected to `/delivery`.
  - The page calls `GET /delivery/assigned`. This endpoint (in `deliveryController.js`) uses `req.user.id` to find only the orders assigned to that specific rider.
- **Updating Status**:
  - The rider moves the order through its lifecycle by clicking buttons.
  - **Flow**: `Assigned` → `Picked Up` → `On the Way` → `Delivered`.
  - Each button calls `PUT /delivery/status/:orderId` with the new status string.

### 3. Key Backend Controllers
- `orderControllers.js`: Handles the admin-side assignment.
- `deliveryController.js`: Handles the rider-side status updates and filtering.

---

## 🛡️ Middleware & Security (Route Protection)

If they ask: "How do you stop a user from accessing the Admin or Delivery pages without logging in?"

- **Logic**: We use **Middleware**. A middleware is a function that runs *before* the main logic.
- **Key Code (`authMiddleware.js`)**:
  ```javascript
  export const auth = (req, res, next) => {
    const token = req.cookies.token || req.headers.authorization;
    const decoded = jwt.verify(token, process.env.JWT_SECRET);
    req.user = decoded;
    next(); // Move to the next function
  };
  ```
- **Explanation**: The `auth` middleware checks if the token is valid. If it's valid, it calls `next()`. If not, it sends a `403 Forbidden` error.

---

## 🌐 Frontend-Backend Connection (Axios)

If they ask: "How does the React app know where the server is?"

- **Logic**: We use an **Axios Instance**.
- **Location**: `frontend/src/config/axiosConfig.js`.
- **Key Code**:
  ```javascript
  const api = axios.create({
    baseURL: import.meta.env.VITE_API_URL,
    withCredentials: true,
  });
  ```
- **Explanation**:
  - `baseURL`: The address of our Node.js server (e.g., localhost:5000).
  - `withCredentials`: This is CRITICAL. It tells the browser to send cookies (tokens) back and forth.

---

## 🔑 Environment Variables (.env)

If they ask: "What is an .env file?"

- **Definition**: It is a file used to store sensitive information (like Database URLs and Secret Keys) outside the source code.
- **Examples in your project**:
  - `PORT`: The port the server runs on.
  - `MONGO_URI`: The connection string for MongoDB.
  - `JWT_SECRET`: The "password" used to encrypt/decrypt user tokens.
  - `VITE_API_URL`: The URL used by the frontend to find the backend.

---

## 📊 Database Schema (Models)

If they ask: "How is your data structured in MongoDB?"

- **User Model**: Stores `name`, `email`, `password` (hashed), `phone`, and `role` (user/admin/delivery).
- **Cart Model**: Links a `userId` to an array of `items` (name, price, qty). This ensures the cart is saved even if the user logs out.
- **Order Model**:
  ```javascript
  // Key Fields
  orderId: String (Unique)
  customerId: String
  items: Array (name, qty, price)
  totalPrice: Number
  status: Enum (Pending, Assigned, Delivered, etc.)
  deliveryPartnerId: ObjectId (Ref to User model)
  ```

---

## ⚛️ Core React Concepts

If they ask: "What React features did you use?"

1.  **useState**: Used to manage local data (like the email you type in a form, or the list of items in the cart).
2.  **useEffect**: Used to perform "side effects" like fetching data from the API when a page first loads.
3.  **useNavigate**: Used to move the user between pages (e.g., from Login to the Dashboard).
4.  **useParams**: Used to get the `orderId` or `token` from the URL address bar.

---

## ⚡ Live Modification "Cheat Sheet"

| If they tell you to... | Go to this file... | Look for... |
| :--- | :--- | :--- |
| **Change a Price** | `backend/controllers/menuControllers.js` | `updateMenuItem` |
| **Add a field to User** | `backend/models/User.js` | `userSchema` |
| **Change Min Phone Length** | `frontend/src/utils/validation.js` | `validatePhone` |
| **Change Delivery Fee** | `frontend/src/pages/Cart.jsx` | `deliveryCharge` |
| **Modify Order ID format** | `backend/controllers/orderControllers.js` | `generateShortId` |
| **Change Toast Position** | `frontend/src/main.jsx` | `<Toaster position="..." />` |
| **Change Redirect** | `frontend/src/pages/Login.jsx` | `navigate("/...")` |
| **Change Token Expiry** | `backend/controllers/authController.js` | `expiresIn: "7d"` |

---

## 🗣️ Common Viva Questions

1.  **Q: How do you handle authentication?**
    *   *A: We use JWT (JSON Web Tokens). When a user logs in, the backend sends a token stored in a HTTP-only cookie and localStorage. This token is sent with every request to identify the user.*
2.  **Q: How does the cart sync with the database?**
    *   *A: Instead of just local state, we have a Cart model in MongoDB. When a user adds an item, an API call is made to the backend to update the database record.*
3.  **Q: How do you handle file uploads for food images?**
    *   *A: Images are currently handled via URLs (or Base64 if applicable). In a production app, we would use Cloudinary or AWS S3.*
4.  **Q: What is `withCredentials: true` in your Axios calls?**
    *   *A: It allows the browser to send cookies (like our auth token) along with the request to the backend.*
