# 🔁 Sequence Diagram Examples

Four real-world sequence diagrams showing service communication patterns.

---

## 1. REST API Request with Caching

A standard REST API call with Redis caching layer.

```mermaid
sequenceDiagram
    participant Client as 🖥️ Client Browser
    participant API as ⚙️ API Server
    participant Cache as 🗄️ Redis Cache
    participant DB as 🐘 PostgreSQL

    Client->>API: GET /api/products/123
    activate API

    API->>Cache: GET product:123
    activate Cache
    Cache-->>API: null (cache miss)
    deactivate Cache

    API->>DB: SELECT * FROM products WHERE id=123
    activate DB
    DB-->>API: {id: 123, name: "Widget", price: 9.99}
    deactivate DB

    API->>Cache: SET product:123 {data} EX 300
    Note over Cache: TTL = 5 minutes

    API-->>Client: 200 OK {id: 123, name: "Widget", price: 9.99}
    deactivate API

    Note over Client,API: Second request (cache hit)

    Client->>API: GET /api/products/123
    activate API
    API->>Cache: GET product:123
    activate Cache
    Cache-->>API: {id: 123, name: "Widget", price: 9.99}
    deactivate Cache
    API-->>Client: 200 OK {id: 123, name: "Widget"} ⚡ from cache
    deactivate API
```

---

## 2. OAuth 2.0 Authorization Code Flow

The complete OAuth flow used by "Login with Google/GitHub".

```mermaid
sequenceDiagram
    participant User as 👤 User
    participant App as 🌐 Your App
    participant Auth as 🔐 Auth Provider (Google)
    participant API as 📡 Your API

    User->>App: Click "Login with Google"
    App->>App: Generate state + PKCE code_verifier
    App->>User: Redirect to Google OAuth URL<br/>(client_id, redirect_uri, scope, state, code_challenge)

    User->>Auth: User sees Google login screen
    User->>Auth: Enter Google credentials + grant permissions

    Auth->>Auth: Validate credentials
    Auth->>User: Redirect to redirect_uri with code & state

    User->>App: GET /callback?code=abc123&state=xyz

    App->>App: Verify state matches (CSRF protection)

    App->>Auth: POST /token<br/>(code, redirect_uri, code_verifier, client_secret)
    activate Auth
    Auth->>Auth: Validate code + PKCE
    Auth-->>App: {access_token, refresh_token, id_token, expires_in}
    deactivate Auth

    App->>Auth: GET /userinfo (Bearer access_token)
    activate Auth
    Auth-->>App: {sub, email, name, picture}
    deactivate Auth

    App->>API: POST /auth/oauth-callback<br/>{provider: "google", user_info, tokens}
    activate API
    API->>API: Find or create user in DB
    API->>API: Issue app JWT token
    API-->>App: {jwt_token, user}
    deactivate API

    App->>App: Store JWT in httpOnly cookie
    App->>User: Redirect to /dashboard (logged in!)
```

---

## 3. Microservices Order Flow

How multiple microservices coordinate to process an order.

```mermaid
sequenceDiagram
    participant Client as 🖥️ Client
    participant Gateway as 🚪 API Gateway
    participant Order as 📦 Order Service
    participant Inventory as 🏭 Inventory Service
    participant Payment as 💳 Payment Service
    participant Notify as 📧 Notification Service
    participant MQ as 📨 Message Queue

    Client->>Gateway: POST /orders {items, payment_method}
    Gateway->>Gateway: Authenticate JWT
    Gateway->>Order: Forward request

    activate Order
    Order->>Inventory: gRPC: CheckAvailability(items)
    activate Inventory
    Inventory-->>Order: {available: true, reserved_until: T+10min}
    deactivate Inventory

    Order->>Order: Create order record (status: PENDING)

    Order->>Payment: gRPC: ChargePayment(amount, method)
    activate Payment
    Payment->>Payment: Call Stripe API
    Payment-->>Order: {success: true, charge_id: "ch_xyz"}
    deactivate Payment

    Order->>Inventory: gRPC: ConfirmReservation(items)
    activate Inventory
    Inventory-->>Order: {confirmed: true}
    deactivate Inventory

    Order->>Order: Update order status: CONFIRMED

    Order->>MQ: Publish: order.confirmed {order_id, user_email, items}
    Order-->>Gateway: {order_id: "ord_123", status: "CONFIRMED"}
    deactivate Order

    Gateway-->>Client: 201 Created {order_id: "ord_123"}

    Note over MQ,Notify: Async processing via message queue

    MQ->>Notify: Consume: order.confirmed event
    activate Notify
    Notify->>Notify: Render email template
    Notify->>Client: Send order confirmation email 📧
    deactivate Notify

    Note over Order,Inventory: Error scenario
    rect rgb(255, 230, 230)
        Order->>Inventory: gRPC: CheckAvailability(items)
        Inventory-->>Order: {available: false, reason: "out_of_stock"}
        Order->>Order: Update order status: FAILED
        Order->>MQ: Publish: order.failed {reason}
        Order-->>Gateway: 409 Conflict {error: "Items out of stock"}
    end
```

---

## 4. WebSocket Real-time Chat

How WebSocket connections work in a chat application.

```mermaid
sequenceDiagram
    participant Alice as 👩 Alice (Browser)
    participant Server as ⚙️ Chat Server
    participant Bob as 👨 Bob (Browser)
    participant Redis as 🗄️ Redis PubSub

    Note over Alice,Bob: Connection Phase

    Alice->>Server: HTTP GET /chat (Upgrade: websocket)
    Server-->>Alice: 101 Switching Protocols ✅
    Note over Alice,Server: WebSocket connection established

    Bob->>Server: HTTP GET /chat (Upgrade: websocket)
    Server-->>Bob: 101 Switching Protocols ✅

    Alice->>Server: WS: {type: "auth", token: "jwt_abc"}
    Server->>Server: Validate JWT → user: Alice
    Server->>Redis: SADD online_users "alice"
    Server-->>Alice: WS: {type: "auth_ok", user_id: "alice"}

    Bob->>Server: WS: {type: "auth", token: "jwt_xyz"}
    Server->>Server: Validate JWT → user: Bob
    Server->>Redis: SADD online_users "bob"
    Server-->>Bob: WS: {type: "auth_ok", user_id: "bob"}

    Note over Alice,Bob: Messaging Phase

    Alice->>Server: WS: {type: "message", to: "bob", text: "Hey Bob!"}
    Server->>Server: Save message to DB
    Server->>Redis: PUBLISH chat:bob {from: "alice", text: "Hey Bob!"}
    Redis->>Server: Deliver to Bob's server instance
    Server-->>Bob: WS: {type: "message", from: "alice", text: "Hey Bob!"}
    Server-->>Alice: WS: {type: "delivered", message_id: "msg_123"}

    Bob->>Server: WS: {type: "typing", to: "alice"}
    Server->>Redis: PUBLISH chat:alice {type: "typing", from: "bob"}
    Redis->>Server: Deliver to Alice's instance
    Server-->>Alice: WS: {type: "typing", from: "bob"}
    Note over Alice: "Bob is typing..." indicator shown

    Bob->>Server: WS: {type: "message", to: "alice", text: "Hi Alice! 👋"}
    Server->>Server: Save message to DB
    Server->>Redis: PUBLISH chat:alice {from: "bob", text: "Hi Alice! 👋"}
    Redis->>Server: Deliver to Alice's instance
    Server-->>Alice: WS: {type: "message", from: "bob", text: "Hi Alice! 👋"}

    Note over Alice,Bob: Disconnection

    Alice->>Server: WS Close frame
    Server->>Redis: SREM online_users "alice"
    Server->>Redis: PUBLISH user:status {user: "alice", status: "offline"}
    Redis->>Server: Notify Bob's instance
    Server-->>Bob: WS: {type: "user_offline", user: "alice"}
```

---

> 💡 **Tip:** Sequence diagrams are perfect for documenting APIs. Include them in your API docs to show the exact message flow.
