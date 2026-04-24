# VELOUR Chatbot — Website Chat Widget

[![Live Demo](https://img.shields.io/badge/Live%20Demo-VELOUR%20Chatbot-C8A876?style=for-the-badge)](https://shahyanahmedkiani.github.io/VeLOUR_Chatbot/)

> **Wear the Mood** · Premium Streetwear · Lahore, Pakistan

A fully integrated, floating chat widget embedded in the VELOUR clothing brand website. Visitors ask questions about products, prices, sizing, delivery, and more — and get instant replies. Built with Python Flask (backend) + HTML/CSS/Vanilla JavaScript (frontend), then merged into a single self-contained file.

---

## Table of Contents

1. [Project Overview](#project-overview)
2. [Architecture](#architecture)
3. [File Structure](#file-structure)
4. [How It Works](#how-it-works)
5. [Features](#features)
6. [Chatbot Topics & Responses](#chatbot-topics--responses)
7. [Running the Flask Version](#running-the-flask-version)
8. [Running the Standalone Version](#running-the-standalone-version)
9. [Tech Stack](#tech-stack)
10. [Brand Info](#brand-info)

---

## Project Overview

VELOUR is a fictional premium streetwear brand based in Lahore, Pakistan (est. 2021). This project delivers a floating chat assistant embedded directly in the brand website. It answers customer queries about:

- Product prices and descriptions
- Size guide (XS–XXL)
- Delivery timelines and charges
- Payment methods
- Exchange and return policy
- New arrival drops
- Order tracking

No database is required. All responses are hardcoded from the brand's test data.

---

## Architecture

```
Visitor types a message
        │
        ▼
JavaScript captures the text (Enter key or Send button)
        │
        ▼
POST /chat  ──►  Flask backend receives JSON { "message": "..." }
                        │
                        ▼
                 get_response(message)
                 — keyword matching against brand data —
                        │
                        ▼
                 Returns JSON { "reply": "..." }
        │
        ◄──────────────────────────────────────────
        │
        ▼
JavaScript appends the bot reply to the chat window
```

In the **standalone HTML version**, the Flask `/chat` route logic is ported directly to JavaScript (`getResponse()`), so no server is needed at all.

---

## File Structure

```
VelourChatbot/
├── chatbot-backend/
│   ├── backend.py              # Flask server — serves / and handles /chat
│   └── templates/
│       └── index.html          # Minimal Flask template (entry point)
│
├── velour_frontend/
│   ├── templates/
│   │   └── index.html          # Full VELOUR website + chat widget HTML
│   └── static/
│       ├── style.css           # All brand + chat widget styles
│       └── chatbot.js          # Toggle, send, receive, typing animation logic
│
└── velour-chatbot.html         # ★ Fully integrated standalone file (this output)
```

---

## How It Works

### Flask Backend (`backend.py`)

| Route | Method | Description |
|-------|--------|-------------|
| `/` | GET | Serves the frontend HTML page |
| `/chat` | POST | Accepts `{ "message": "..." }`, returns `{ "reply": "..." }` |

The `get_response(message)` function lowercases the incoming text and checks it against keyword lists. The first matching branch wins and returns the corresponding response string.

### Frontend (`chatbot.js`)

| Function | Purpose |
|----------|---------|
| `openChat()` | Opens the chat window, fires automatic greeting on first open |
| `closeChat()` | Closes the chat window |
| `sendMessage()` | Reads input, shows user bubble, shows typing indicator, calls `/chat` |
| `appendMessage()` | Creates a styled message bubble and appends it |
| `showTyping()` / `hideTyping()` | Animates the three-dot typing indicator |
| `scrollToBottom()` | Keeps the latest message in view |

### Standalone Integration (`velour-chatbot.html`)

The `fetch('/chat', ...)` network call is replaced by a direct JavaScript function call to `getResponse()`, which contains the same keyword-matching logic as the Python backend. A simulated delay of 350–700 ms is added for a natural feel.

---

## Features

- **Floating chat bubble** — fixed bottom-right, with a pulsing gold ping indicator
- **Auto-greeting** — bot says hello on first open, no user input needed
- **Typing animation** — three bouncing gold dots while the reply is "processing"
- **Quick reply chips** — appear after greeting inside the chat (Prices, Size Guide, Delivery, Returns)
- **Page quick-links** — buttons above the footer open the chat and auto-send the question
- **Enter key support** — send messages without clicking the button
- **Escape key** — closes the chat window
- **CORS enabled** — `flask-cors` allows the browser JS to call the Flask server without being blocked
- **Responsive** — chat window adapts to mobile screen widths
- **Smooth animations** — spring-physics open/close, pop-in message bubbles, marquee scrolling product names

---

## Chatbot Topics & Responses

| Trigger Keywords | Topic | Sample Reply |
|-----------------|-------|-------------|
| hi, hello, hey, salam | Greeting | Welcome message + topic list |
| size, sizing, fit, chest, xl, medium | Size Guide | XS–XXL chest & waist measurements |
| price, cost, how much, pkr, kitna | Pricing | Full product price list in PKR |
| deliver, shipping, dispatch, days | Delivery | City-wise timelines + charges |
| return, exchange, refund | Returns | 7-day exchange policy details |
| pay, jazzcash, easypaisa, cod, cash | Payment | Accepted payment methods |
| new, drop, restock, collection | New Arrivals | Friday drop schedule + Instagram |
| order, track, parcel, status | Order Tracking | Prompt to share order ID |
| discount, sale, promo, offer | Discounts | Seasonal sales info |
| about, brand, who, lahore, founded | Brand Info | VELOUR background & founding |
| *(anything else)* | Fallback | Redirect to Instagram @shopvelour |

---

## Running the Flask Version

### Prerequisites

- Python 3.8+
- pip

### Setup

```bash
# 1. Clone / unzip the project
cd VelourChatbot/chatbot-backend

# 2. Create and activate virtual environment
python -m venv venv
source venv/bin/activate        # macOS / Linux
venv\Scripts\activate           # Windows

# 3. Install dependencies
pip install flask flask-cors python-dotenv

# 4. Run the server
python backend.py
```

The server starts at **http://127.0.0.1:5000**

Make sure `templates/index.html` is the full frontend HTML (from `velour_frontend/templates/index.html`) and the `static/` folder contains `style.css` and `chatbot.js`.

### API Test (curl)

```bash
curl -X POST http://127.0.0.1:5000/chat \
  -H "Content-Type: application/json" \
  -d '{"message": "What are your prices?"}'
```

Expected response:

```json
{
  "reply": "Here are our product prices:\n\n• Oversized Tee: PKR 1,800 – 2,200\n..."
}
```

---

## Running the Standalone Version

No installation required. Just open the file in any browser:

```bash
open velour-chatbot.html        # macOS
start velour-chatbot.html       # Windows
xdg-open velour-chatbot.html    # Linux
```

Or drag and drop `velour-chatbot.html` into your browser window.

> All backend logic runs in-browser via JavaScript. No server, no Python, no installation.

---

## Tech Stack

| Layer | Technology |
|-------|-----------|
| Backend | Python 3 · Flask · Flask-CORS · python-dotenv |
| Frontend | HTML5 · CSS3 · Vanilla JavaScript (ES2020) |
| Fonts | Cormorant Garamond (display) · DM Sans (body) via Google Fonts |
| Pattern | REST API — JS `fetch()` → Flask POST `/chat` → JSON response |
| Deployment | Local / any static host (standalone version) |

---

## Brand Info

| Field | Value |
|-------|-------|
| Brand | VELOUR |
| Tagline | Wear the Mood |
| Founded | 2021, Lahore, Pakistan |
| Category | Premium Streetwear & Casual Clothing |
| Target | 18–32, urban Pakistan |
| Instagram | @shopvelour |
| Website | velour.pk *(fictional)* |

---

*Built as Task 02 — Website Chatbot · Python Flask + HTML/CSS/JS*
