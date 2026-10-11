# Spark Commerce

Spark Commerce is a multi-category e-commerce platform focused on electronics, fashion, and home products, with an integrated shopping assistant for product discovery and customer support.

This repository is structured as a three-part system:
- `client/` — frontend storefront and admin experience
- `server/` — Express API, auth, orders, MongoDB integration, and business logic
- `chatbot/` — Python-based AI assistant with RAG, LangGraph orchestration, and product recommendations

The product specification in [`ecommerce-rag-spec/`](ecommerce-rag-spec/README.md) is the source of truth for architecture, scope, and delivery plan.

## Project Analysis

### Business Goal
The goal of Spark Commerce is to provide a complete online shopping experience with:
- product browsing across multiple categories
- customer authentication and account flows
- cart and order management
- AI-assisted shopping recommendations
- admin-side catalog and order management

The product is designed to combine a traditional commerce stack with an intelligent shopping assistant without compromising business rules or security.

### Target Users
| Role | Capabilities |
|---|---|
| Guest | Browse catalog, ask recommendation questions |
| Customer | Add to cart, checkout with Cash on Delivery, track orders, cancel or return items, use assistant actions |
| Admin | Manage products, categories, images, order states, sales-related information |

Out of scope for v1:
- online payments
- guest cart
- reviews
- coupons
- wishlists
- notifications
- dark mode
- admin actions through the chatbot

### Product Direction
The app is intentionally designed as a practical commerce platform that balances:
- storefront usability
- secure backend logic
- service separation between product APIs and AI components

This is not just a frontend project — it is a multi-service system with clear ownership boundaries and a phased build plan.

## Architecture

### System Boundaries
| Folder | Owns | Must not |
|---|---|---|
| `client/` | UI, routes, client-side state, user experience | Secrets, source-of-truth pricing |
| `server/` | Auth, business rules, MongoDB, Cloudinary, API routes, chatbot proxy | LLM logic or embedding generation |
| `chatbot/` | Gemini, retrieval, LangGraph, FAISS, SQLite | MongoDB logic and client-trusted pricing |

### Request Flow
- Web flow: `client -> server (cookie JWT) -> MongoDB`
- Chat flow: `client -> server /api/chat (SSE) -> chatbot -> Express API with user token`
- Catalog sync: admin product changes -> chatbot ingest -> FAISS + SQLite metadata

### Why This Structure Works
The architecture separates responsibilities so that:
- the frontend focuses on user experience
- the server enforces business and security rules
- the chatbot handles retrieval and generation independently

This reduces coupling and makes it easier to evolve the AI layer without exposing sensitive server logic to the client.

## Tech Stack

### Frontend
- React 18
- Vite
- JavaScript
- Tailwind CSS
- React Router
- TanStack Query

### Backend
- Node.js
- Express
- Mongoose
- Zod
- JWT stored in an `httpOnly` cookie

### AI / Chatbot
- Python 3.11
- FastAPI
- LangGraph
- FAISS
- SQLite
- Gemini + Groq integrations

### Commerce Rules
- Currency is tracked in integer paise
- Free shipping above ₹999 INR
- Standard shipping charge: ₹49 INR
- Payments: Cash on Delivery only

## Repository Layout

```text
client/src/
  components/ui|shop|admin|chat
  layouts/
  pages/shop|account|admin|auth
  hooks/ api/ context/ styles/

server/src/
  config/ models/ routes/ controllers/
  services/ middleware/ validators/ utils/ scripts/

chatbot/
  app/api|agent|tools|rag|clients
  tests/ knowledge/ eval/ scripts/ data/

ecommerce-rag-spec/
  context/
  reference/spark-admin-theme/
