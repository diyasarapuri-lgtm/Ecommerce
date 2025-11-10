# Technical Architecture Documentation: Diligent E-Commerce Frontend

## 1. Overview and Goal

This project delivers the frontend for a clean and responsive E-Commerce website, as required by the Diligent internship task. The primary goal was to create a single-page application (SPA) capable of product exploration, detail viewing, and persistent shopping cart management.

## 2. Technology Stack Selection

Based on the recommendation for React/Next.js, the core frontend was implemented using **React (Functional Components and Hooks)** to ensure a modern, component-based structure.

| Layer | Technology | Rationale |
| :--- | :--- | :--- |
| **Frontend Framework** | React | Meets the explicit recommendation and allows for efficient, reusable UI components and state management. |
| **Styling** | Tailwind CSS (Assumed via className) | Used for rapid, responsive and utility-first styling, ensuring the site is clean and functional on all screen sizes (mobile, tablet, desktop). |
| **Data Persistence/Backend Simulation** | Firebase Firestore | **Fulfills the "lightweight backend to handle cart operations and basic state management" requirement.** Firestore provides real-time, persistent storage, allowing cart contents to survive page reloads and ensuring data integrity without building a separate Node.js API endpoint for cart logic. |

## 3. Application Structure and Data Flow

The entire application is contained within a single `EcommerceApp.jsx` file, utilizing React features to simulate multi-page navigation and state management.

### Key Components:
* **`App`:** The main container component, managing the global view state (`'list'` vs. `'details'`) and coordinating data flow.
* **`useFirebase` Hook:** Initializes Firebase Auth and Firestore and manages the user authentication state (`userId`).
* **`useCartStore` Hook:** The core business logic layer. It manages the real-time connection to the Firestore database via `onSnapshot` and provides methods (`addToCart`, `updateCartQuantity`, `removeFromCart`) that interact with the database.
* **`Header` and `CartSidebar`:** Handle UI elements and display real-time cart counts and total costs.

### Cart Persistence (Simulated Backend)

Instead of temporary browser storage (like `localStorage`), the cart uses **Firestore** for reliability:

1.  **Authentication:** The application initializes Firebase and signs the user in **anonymously** (`signInAnonymously`) if no token is present, generating a unique `userId`.
2.  **Data Path:** Cart items are stored in a unique path specific to the authenticated user (e.g., `/artifacts/{appId}/users/{userId}/diligent_cart`).
3.  **Real-Time Updates:** The `onSnapshot` listener ensures that any changes (adding, removing, or updating quantity) are instantly reflected across the application and saved permanently in the database.
4.  **Transaction Handling:** The `addToCart` function uses a Firestore transaction (`runTransaction`) to safely read the current quantity and update the cart item, preventing potential race conditions if a user clicks the "Add" button quickly multiple times.

## 4. Navigation and State Management

* **View Switching:** Instead of using a heavy library like React Router, navigation between the Product List and Product Details is handled by a simple React state variable (`view`). This satisfies the requirement for a clean UI while keeping the solution contained in one file.
* **State Management:** Local state (`useState`) handles the UI elements (e.g., `isCartOpen`), while the custom `useCartStore` hook abstracts and manages all persistent data logic, keeping the main `App` component clean and focused on rendering.