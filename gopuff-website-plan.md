# Gopuff High-Converting Website — Full Structure Plan
### Built with Google AI Studio (Gemini API + Firebase + Vertex AI)

---

## 0. EXECUTIVE SUMMARY

This plan covers a full-stack, high-converting Gopuff delivery website using:
- **Frontend**: React (Vite) + Tailwind CSS + Framer Motion
- **Backend**: Node.js (Express) + Firebase (Firestore, Auth, Storage)
- **AI Layer**: Google AI Studio (Gemini 1.5 Pro) via Vertex AI
- **Payments**: Stripe
- **Maps/Delivery**: Google Maps Platform
- **Hosting**: Firebase Hosting + Cloud Run

**Conversion Goal**: 4%+ cart-to-order rate, <3s page load, <10min perceived delivery promise.

---

## 1. SITE ARCHITECTURE & PAGE MAP

```
/                        → Landing / Hero Page
/products                → Product Catalog (Browse All)
/products/:category      → Category Page (Snacks, Drinks, Alcohol, etc.)
/products/:id            → Product Detail Page (PDP)
/search                  → Search Results (AI-powered)
/cart                    → Cart Review Page
/checkout                → Checkout Flow (3-step)
/checkout/confirmation   → Order Confirmation
/account                 → User Dashboard
/account/orders          → Order History
/account/addresses       → Saved Addresses
/track/:orderId          → Real-time Order Tracking
/deals                   → Promotions & Deals Hub
/gopuff-fam              → Subscription/Loyalty Page
/store-locator           → Coverage Map
/404                     → Error Page
```

---

## 2. FRONTEND STRUCTURE

### 2.1 Tech Stack
| Tool | Purpose |
|------|---------|
| React 18 + Vite | Core framework |
| Tailwind CSS v3 | Utility styling |
| Framer Motion | Animations & transitions |
| Zustand | Global state (cart, user, location) |
| React Query (TanStack) | Server state + caching |
| React Hook Form + Zod | Form validation |
| Google Maps JS API | Address input, delivery map |
| Stripe.js | Payment UI |

### 2.2 Folder Structure
```
/src
├── assets/              # Icons, images, brand assets
├── components/
│   ├── ui/              # Atoms: Button, Badge, Input, Modal, Skeleton
│   ├── layout/          # Navbar, Footer, MobileNav, SideCart
│   ├── product/         # ProductCard, ProductGrid, ProductCarousel
│   ├── cart/            # CartDrawer, CartItem, CartSummary
│   ├── checkout/        # AddressStep, PaymentStep, ReviewStep
│   ├── ai/              # AISuggestions, SmartSearch, ChatAssistant
│   └── tracking/        # OrderMap, DeliveryTimeline
├── pages/               # Route-level components
├── hooks/               # useCart, useLocation, useAI, useAuth
├── store/               # Zustand slices: cart, user, ui
├── services/            # API clients: products, orders, ai, stripe
├── utils/               # formatPrice, timeEstimate, validators
└── constants/           # Categories, promotions config
```

### 2.3 High-Converting Page Designs

#### 🏠 Landing Page (/)
**Conversion elements:**
- [ ] Hero: Full-width video/animation + "Delivered in 30 min" headline
- [ ] Address capture bar (above the fold) — primary CTA
- [ ] Live delivery ETA badge (pulls from Google Maps Distance Matrix)
- [ ] Social proof strip: "10M+ orders delivered · 4.8★ app rating"
- [ ] Category quick-links (horizontal scroll, icon + label)
- [ ] "Tonight's Top Picks" AI-curated product carousel
- [ ] Urgency nudge: "12 people ordered this in the last hour"
- [ ] Gopuff Fam membership upsell block
- [ ] Footer with app store badges + trust signals

```jsx
// Hero CTA Component
<AddressCapture
  placeholder="Enter your delivery address"
  onConfirm={(addr) => {
    setUserLocation(addr);
    navigate('/products');
  }}
  ctaLabel="See what's available near you →"
/>
```

#### 🛍 Product Catalog (/products, /products/:category)
**Conversion elements:**
- [ ] Sticky filter bar (category, dietary, price range, in-stock toggle)
- [ ] Product grid (3-col desktop, 2-col mobile)
- [ ] ProductCard: image, name, price, add-to-cart button, "Low stock" badge
- [ ] Skeleton loaders for perceived performance
- [ ] Infinite scroll (not pagination — reduces friction)
- [ ] AI-powered "You might also like" row
- [ ] Pinned "Deal of the Hour" countdown timer

#### 🛒 Product Detail Page (/products/:id)
**Conversion elements:**
- [ ] High-res image gallery with zoom
- [ ] Prominent price + "Add to Cart" (sticky on mobile scroll)
- [ ] Quantity selector with +/- controls
- [ ] Scarcity indicator: "Only 8 left"
- [ ] Delivery promise: "Order in next 14min → arrives by 9:47 PM"
- [ ] AI-generated product description (Gemini)
- [ ] Frequently bought together bundle
- [ ] Reviews section (star ratings)

#### 🧾 Cart + Checkout
**Conversion elements:**
- [ ] Slide-in cart drawer (no page redirect)
- [ ] Progress bar: "Add $8.23 more for free delivery"
- [ ] Promo code field (collapsed by default to reduce distraction)
- [ ] Express checkout: Apple Pay / Google Pay first
- [ ] 3-step checkout: Address → Payment → Review (no account required)
- [ ] Trust badges: SSL lock, "No hidden fees", "Cancel anytime"
- [ ] Order summary always visible (right column, sticky)

---

## 3. BACKEND STRUCTURE

### 3.1 Tech Stack
| Tool | Purpose |
|------|---------|
| Node.js + Express | REST API server |
| Firebase Firestore | Primary database (NoSQL) |
| Firebase Auth | User authentication (email, Google, phone) |
| Firebase Storage | Product images, receipts |
| Cloud Run | Containerized API hosting |
| Vertex AI / Gemini | AI features |
| Stripe API | Payments + webhooks |
| Google Maps Platform | Geocoding, ETA, routing |
| Redis (Upstash) | Caching, rate limiting, sessions |

### 3.2 API Folder Structure
```
/api
├── server.js            # Express entry point
├── routes/
│   ├── auth.js          # /api/auth/*
│   ├── products.js      # /api/products/*
│   ├── cart.js          # /api/cart/*
│   ├── orders.js        # /api/orders/*
│   ├── ai.js            # /api/ai/*
│   ├── delivery.js      # /api/delivery/*
│   └── webhooks.js      # /api/webhooks/stripe
├── controllers/         # Business logic per route
├── middleware/
│   ├── auth.js          # Firebase token verification
│   ├── rateLimit.js     # Redis-based rate limiting
│   ├── errorHandler.js  # Global error handling
│   └── validate.js      # Zod schema validation
├── services/
│   ├── firestore.js     # DB helpers
│   ├── stripe.js        # Payment logic
│   ├── maps.js          # Geocoding + ETA
│   ├── gemini.js        # AI Studio client
│   └── inventory.js     # Stock management
├── models/              # Firestore schema definitions (JSDoc)
└── utils/               # helpers, logger, constants
```

### 3.3 Firestore Data Models

```javascript
// PRODUCT
{
  id: "prod_abc123",
  name: "Lay's Classic Chips",
  category: "snacks",
  subcategory: "chips",
  price: 4.99,
  originalPrice: 5.99,       // for strike-through
  images: ["gs://..."],
  stock: 47,
  lowStockThreshold: 10,
  tags: ["salty", "gluten-free"],
  nutritionFacts: { ... },
  popularity: 9420,           // for AI ranking
  createdAt: Timestamp,
}

// USER
{
  uid: "usr_xyz",
  email: "jane@example.com",
  phone: "+1...",
  savedAddresses: [
    { label: "Home", lat: 40.71, lng: -74.00, full: "..." }
  ],
  isFamMember: true,
  orderCount: 14,
  preferences: ["vegan", "low-sugar"],  // for AI personalization
  stripeCustomerId: "cus_...",
}

// ORDER
{
  id: "ord_001",
  userId: "usr_xyz",
  items: [{ productId, qty, priceAtOrder }],
  subtotal: 24.50,
  deliveryFee: 2.99,
  tip: 3.00,
  total: 30.49,
  promoCode: "SAVE5",
  discount: 5.00,
  status: "preparing", // placed → preparing → out_for_delivery → delivered
  address: { lat, lng, full },
  driverId: "drv_999",
  estimatedDelivery: Timestamp,
  createdAt: Timestamp,
}

// CART (server-side, synced)
{
  userId: "usr_xyz",
  items: [{ productId, qty }],
  updatedAt: Timestamp,
}
```

### 3.4 API Endpoints

#### Auth
```
POST   /api/auth/register          Create account (email+password)
POST   /api/auth/login             Firebase token exchange
POST   /api/auth/google            Google OAuth
POST   /api/auth/phone/send-otp    Phone auth step 1
POST   /api/auth/phone/verify      Phone auth step 2
DELETE /api/auth/logout            Invalidate session
```

#### Products
```
GET    /api/products               List (filter, sort, paginate)
GET    /api/products/:id           Single product
GET    /api/products/category/:cat Category products
GET    /api/products/search        Full-text + AI semantic search
GET    /api/products/trending      Trending by location
POST   /api/products               [Admin] Create product
PUT    /api/products/:id           [Admin] Update product
```

#### Cart
```
GET    /api/cart                   Get user cart
POST   /api/cart/add               Add item
PUT    /api/cart/update            Update qty
DELETE /api/cart/remove/:productId Remove item
DELETE /api/cart/clear             Clear cart
POST   /api/cart/validate          Check stock + pricing before checkout
```

#### Orders
```
POST   /api/orders                 Place order (triggers Stripe PaymentIntent)
GET    /api/orders                 User order history
GET    /api/orders/:id             Single order detail
GET    /api/orders/:id/track       Real-time tracking data
PUT    /api/orders/:id/status      [Driver/Admin] Update status
POST   /api/orders/:id/cancel      Cancel (if within window)
```

#### Delivery
```
POST   /api/delivery/estimate      ETA + fee estimate for address
GET    /api/delivery/coverage      Is address in delivery zone?
GET    /api/delivery/zones         GeoJSON of coverage areas
```

#### AI Endpoints
```
POST   /api/ai/search              Semantic search (Gemini Embeddings)
POST   /api/ai/recommendations     Personalized product recs
POST   /api/ai/describe            Generate product description
POST   /api/ai/chat                Shopping assistant chatbot
POST   /api/ai/upsell              "Complete your order" suggestions
```

#### Webhooks
```
POST   /api/webhooks/stripe        Handle payment events
```

---

## 4. GOOGLE AI STUDIO INTEGRATION

### 4.1 AI Features Map

| Feature | Model | Where Used |
|---------|-------|------------|
| Smart Search | `text-embedding-004` + cosine similarity | Search bar |
| Product Descriptions | `gemini-1.5-flash` | PDP, admin panel |
| Personalized Recs | `gemini-1.5-pro` + user profile | Homepage, cart |
| Shopping Assistant | `gemini-1.5-pro` (chat) | Floating chatbot |
| Upsell Suggestions | `gemini-1.5-flash` | Cart drawer |
| Demand Forecasting | `gemini-1.5-pro` | Inventory (backend) |

### 4.2 Gemini Service (Backend)

```javascript
// services/gemini.js
import { GoogleGenerativeAI } from "@google/generative-ai";

const genAI = new GoogleGenerativeAI(process.env.GEMINI_API_KEY);

// Product recommendation prompt
export async function getRecommendations(userPreferences, cartItems, availableProducts) {
  const model = genAI.getGenerativeModel({ model: "gemini-1.5-flash" });

  const prompt = `
    You are a Gopuff shopping assistant. Suggest 6 products to add to cart.
    
    User preferences: ${JSON.stringify(userPreferences)}
    Current cart: ${JSON.stringify(cartItems)}
    Available products (sample): ${JSON.stringify(availableProducts.slice(0, 50))}
    
    Return ONLY a JSON array of product IDs: ["id1", "id2", ...]
    Prioritize: complementary items, deals, user preferences.
  `;

  const result = await model.generateContent(prompt);
  return JSON.parse(result.response.text());
}

// Shopping assistant (streaming)
export async function streamChatResponse(conversationHistory) {
  const model = genAI.getGenerativeModel({ model: "gemini-1.5-pro" });
  const chat = model.startChat({ history: conversationHistory });

  return chat.sendMessageStream(
    "You are a helpful Gopuff shopping assistant. Help users find products quickly."
  );
}
```

### 4.3 Semantic Search Implementation

```javascript
// Embed product catalog on write, query at search time
export async function embedProduct(product) {
  const model = genAI.getGenerativeModel({ model: "text-embedding-004" });
  const result = await model.embedContent(
    `${product.name} ${product.category} ${product.tags.join(' ')}`
  );
  return result.embedding.values; // float array
}

// Store embeddings in Firestore alongside product
// At search time: embed query → cosine similarity → top N results
```

---

## 5. PAYMENT FLOW (Stripe)

```
1. User clicks "Place Order"
2. Frontend: POST /api/orders → backend creates PaymentIntent
3. Backend returns { clientSecret }
4. Frontend: stripe.confirmPayment(clientSecret, { payment_method })
5. Stripe webhook → POST /api/webhooks/stripe
6. On payment_intent.succeeded:
   - Mark order as "placed"
   - Trigger inventory deduction
   - Send confirmation email (Firebase Extensions / SendGrid)
   - Push order to driver dispatch queue
```

**Supported methods**: Credit/debit card, Apple Pay, Google Pay, Link (Stripe's 1-click)

---

## 6. REAL-TIME DELIVERY TRACKING

```javascript
// Firestore real-time listener on frontend
import { doc, onSnapshot } from "firebase/firestore";

function TrackingPage({ orderId }) {
  const [order, setOrder] = useState(null);

  useEffect(() => {
    const unsub = onSnapshot(doc(db, "orders", orderId), (snap) => {
      setOrder(snap.data());
    });
    return unsub;
  }, [orderId]);

  // Renders: status timeline + Google Maps with driver pin
}
```

**Driver location**: Updated every 10s by driver app → Firestore → customer sees live map pin.

---

## 7. PERFORMANCE & CONVERSION OPTIMIZATION

### Speed
- [ ] Vite code splitting + lazy loading per route
- [ ] React Query caching (staleTime: 5min for products)
- [ ] Image optimization: WebP format, lazy loading, blur placeholder
- [ ] Firebase CDN for all static assets
- [ ] Prefetch next-likely route on hover
- [ ] Redis cache for trending/catalog API calls (TTL: 60s)

### Conversion Rate Optimization (CRO)
- [ ] Persistent cart (Firestore sync — survives page refresh)
- [ ] Guest checkout (no account required)
- [ ] One-tap reorder from order history
- [ ] Push notifications (Firebase Cloud Messaging) for order updates
- [ ] Abandoned cart email (30min delay via Cloud Functions)
- [ ] Exit-intent modal with promo code
- [ ] A/B test hero CTA copy (Gemini generates variants)
- [ ] Heatmap integration (Hotjar or Microsoft Clarity)

### Urgency & Social Proof
- [ ] "X people viewing this item right now" (Firestore presence tracking)
- [ ] "Low stock" badges (< threshold in Firestore)
- [ ] Delivery countdown: "Order in next HH:MM for delivery by XX:XX"
- [ ] Live order counter in footer: "4,231 orders delivered today"

---

## 8. SECURITY

| Layer | Implementation |
|-------|---------------|
| Auth | Firebase Auth JWT (verified server-side on every request) |
| API | All routes protected by `middleware/auth.js` |
| Payments | Stripe handles card data — PCI compliant by default |
| Input | Zod validation on all API inputs |
| Rate Limiting | Redis: 100 req/min per IP, 10 req/min for checkout |
| CORS | Whitelist only your domain |
| Secrets | Google Secret Manager (never in code) |
| HTTPS | Enforced via Firebase Hosting + Cloud Run |

---

## 9. GOOGLE AI STUDIO SETUP STEPS

```bash
# 1. Create project in Google AI Studio
#    → aistudio.google.com → Get API Key

# 2. Enable APIs in Google Cloud Console:
#    - Vertex AI API
#    - Generative Language API
#    - Maps JavaScript API
#    - Geocoding API
#    - Distance Matrix API

# 3. Install SDK
npm install @google/generative-ai

# 4. Set environment variables
GEMINI_API_KEY=your_key
GOOGLE_MAPS_API_KEY=your_key
FIREBASE_PROJECT_ID=gopuff-clone
STRIPE_SECRET_KEY=sk_...
STRIPE_WEBHOOK_SECRET=whsec_...

# 5. Initialize Firebase
firebase init hosting functions firestore storage

# 6. Deploy
firebase deploy
```

---

## 10. DEVELOPMENT ROADMAP

### Phase 1 — MVP (Weeks 1–3)
- [ ] Firebase project setup + Auth
- [ ] Product catalog (Firestore + seed data)
- [ ] Homepage + Product browse + Cart
- [ ] Checkout with Stripe (card only)
- [ ] Basic order tracking (status only)
- [ ] Deploy to Firebase Hosting

### Phase 2 — AI & Conversion (Weeks 4–5)
- [ ] Gemini-powered search
- [ ] Product recommendations on homepage + PDP
- [ ] Shopping assistant chatbot
- [ ] AI product descriptions (auto-generate on upload)
- [ ] Urgency signals (stock, viewer count, countdown)

### Phase 3 — Full Conversion Stack (Weeks 6–7)
- [ ] Google Pay / Apple Pay
- [ ] Real-time driver tracking (Google Maps)
- [ ] Abandoned cart emails (Cloud Functions + SendGrid)
- [ ] Push notifications (FCM)
- [ ] Gopuff Fam subscription (Stripe Subscriptions)
- [ ] A/B test framework

### Phase 4 — Scale (Week 8+)
- [ ] Admin dashboard (inventory, order management)
- [ ] Demand forecasting (Gemini + historical data)
- [ ] Multi-warehouse routing
- [ ] Analytics dashboard (GA4 + BigQuery)

---

## 11. KEY PROMPTS FOR GOOGLE AI STUDIO

Use these as system prompts when building in AI Studio:

**For the frontend build:**
> "You are a senior React developer building Gopuff's high-converting delivery website. Use React 18, Tailwind CSS, and Zustand. Every component must optimize for conversion: clear CTAs, minimal friction, urgency cues, and mobile-first design. Follow these specs: [paste section 2]"

**For the backend build:**
> "You are a senior Node.js/Firebase engineer. Build the REST API for a Gopuff delivery app using Express, Firestore, Firebase Auth, and Stripe. Implement proper error handling, Zod validation, and Redis caching. Follow these endpoint specs: [paste section 3.4]"

**For AI features:**
> "You are integrating Google Gemini into a food delivery app. Build the AI service layer using @google/generative-ai SDK. Implement: semantic search with embeddings, personalized recommendations, and a streaming shopping assistant chatbot. Follow these specs: [paste section 4]"
