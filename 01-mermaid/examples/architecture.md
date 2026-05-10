# 🏗️ System Architecture Examples

Three production-ready system architecture diagrams using Mermaid flowcharts.

---

## 1. URL Shortener (like bit.ly)

A scalable URL shortener with analytics.

```mermaid
flowchart LR
    subgraph Clients
        Web[🌐 Web Browser]
        Mobile[📱 Mobile App]
        API_Client[⚙️ API Client]
    end

    subgraph CDN ["CDN / Edge Layer"]
        CF[☁️ Cloudflare CDN]
    end

    subgraph LB ["Load Balancer"]
        NLB[⚖️ NGINX Load Balancer]
    end

    subgraph AppLayer ["Application Layer (Auto-scaled)"]
        App1[🟢 App Server 1]
        App2[🟢 App Server 2]
        App3[🟢 App Server 3]
    end

    subgraph DataLayer ["Data Layer"]
        Redis[⚡ Redis\nShort URL Cache]
        PG[(🐘 PostgreSQL\nURL Mappings)]
        Kafka[📨 Kafka\nClick Events]
    end

    subgraph Analytics ["Analytics Pipeline"]
        Consumer[📊 Event Consumer]
        Clickhouse[(📈 ClickHouse\nAnalytics DB)]
        Dashboard[📉 Analytics Dashboard]
    end

    subgraph Storage ["Storage"]
        S3[🗃️ S3\nBackups & Exports]
    end

    Web & Mobile & API_Client --> CF
    CF --> NLB
    NLB --> App1 & App2 & App3
    App1 & App2 & App3 --> Redis
    App1 & App2 & App3 --> PG
    App1 & App2 & App3 --> Kafka
    Kafka --> Consumer
    Consumer --> Clickhouse
    Clickhouse --> Dashboard
    PG --> S3
```

**Key flows:**
- **Shorten URL:** `POST /shorten` → App → Generate 6-char code → Store in PostgreSQL → Cache in Redis → Return short URL
- **Redirect:** `GET /:code` → App → Check Redis → (if miss) check PostgreSQL → 301 redirect → Emit click event to Kafka
- **Analytics:** Kafka consumer aggregates click events into ClickHouse for real-time dashboards

---

## 2. Real-time Chat Application (like WhatsApp)

A distributed chat system supporting millions of concurrent connections.

```mermaid
flowchart TD
    subgraph Clients
        iOSApp[📱 iOS App]
        AndroidApp[🤖 Android App]
        WebApp[🌐 Web App]
    end

    subgraph Edge ["Edge / Gateway"]
        DNS[🌍 DNS with GeoDNS]
        WSGateway[🚪 WebSocket Gateway\nNginx + Node.js]
    end

    subgraph Services ["Core Services"]
        AuthSvc[🔐 Auth Service]
        ChatSvc[💬 Chat Service]
        UserSvc[👤 User Service]
        PresenceSvc[🟢 Presence Service]
        MediaSvc[🖼️ Media Service]
    end

    subgraph Messaging ["Messaging Infrastructure"]
        Kafka[📨 Kafka Cluster\n3 brokers]
        Redis[⚡ Redis Cluster\nPubSub + Sessions]
    end

    subgraph Storage ["Data Storage"]
        UserDB[(👤 PostgreSQL\nUsers & Profiles)]
        MessageDB[(💬 Cassandra\nMessages)]
        MediaStore[🗃️ S3\nImages & Videos]
        CDN[☁️ CloudFront CDN]
    end

    subgraph Push ["Push Notifications"]
        PushSvc[🔔 Push Service]
        APNS[🍎 Apple APNS]
        FCM[🤖 Google FCM]
    end

    iOSApp & AndroidApp & WebApp --> DNS
    DNS --> WSGateway
    WSGateway --> AuthSvc
    WSGateway --> Redis
    WSGateway --> ChatSvc
    ChatSvc --> Kafka
    ChatSvc --> MessageDB
    ChatSvc --> PresenceSvc
    PresenceSvc --> Redis
    Kafka --> PushSvc
    PushSvc --> APNS & FCM
    UserSvc --> UserDB
    MediaSvc --> MediaStore
    MediaStore --> CDN
    CDN --> iOSApp & AndroidApp & WebApp
```

**Key design decisions:**
- **WebSocket Gateway** holds persistent connections; stateless app servers communicate via Redis PubSub
- **Cassandra** for messages: optimized for write-heavy workloads, time-series partitioning by chat room
- **GeoDNS** routes users to nearest data center (US, EU, Asia)
- **Kafka** decouples chat events from push notifications

---

## 3. E-Commerce Platform (like Shopify)

A microservices-based e-commerce platform.

```mermaid
flowchart LR
    subgraph Buyer ["Buyer Side"]
        Browser[🌐 Browser]
        MobileApp[📱 Mobile App]
    end

    subgraph Seller ["Seller Side"]
        AdminPanel[🛠️ Admin Panel]
    end

    subgraph API_Gateway ["API Gateway Layer"]
        Gateway[🚪 Kong API Gateway\nAuth + Rate Limit + Routing]
    end

    subgraph Microservices ["Microservices"]
        ProductSvc[📦 Product Service]
        OrderSvc[🛒 Order Service]
        UserSvc[👤 User Service]
        PaymentSvc[💳 Payment Service]
        InventorySvc[🏭 Inventory Service]
        SearchSvc[🔍 Search Service]
        ReviewSvc[⭐ Review Service]
        ShippingSvc[🚚 Shipping Service]
        NotifySvc[📧 Notification Service]
    end

    subgraph EventBus ["Event Bus"]
        Kafka[📨 Kafka]
    end

    subgraph Databases ["Databases (per service)"]
        ProductDB[(📦 PostgreSQL\nProducts)]
        OrderDB[(🛒 PostgreSQL\nOrders)]
        UserDB[(👤 PostgreSQL\nUsers)]
        InventoryDB[(🏭 Redis + MySQL\nInventory)]
        SearchIdx[(🔍 Elasticsearch\nProduct Index)]
        ReviewDB[(⭐ MongoDB\nReviews)]
    end

    subgraph External ["External Services"]
        Stripe[💳 Stripe]
        FedEx[🚚 FedEx API]
        SendGrid[📧 SendGrid]
        Twilio[📱 Twilio SMS]
    end

    Browser & MobileApp & AdminPanel --> Gateway

    Gateway --> ProductSvc & OrderSvc & UserSvc & PaymentSvc
    Gateway --> InventorySvc & SearchSvc & ReviewSvc & ShippingSvc

    ProductSvc --> ProductDB
    ProductSvc --> Kafka

    OrderSvc --> OrderDB
    OrderSvc --> Kafka

    UserSvc --> UserDB

    PaymentSvc --> Stripe
    PaymentSvc --> Kafka

    InventorySvc --> InventoryDB

    SearchSvc --> SearchIdx
    ProductSvc --> SearchIdx

    ReviewSvc --> ReviewDB

    ShippingSvc --> FedEx
    ShippingSvc --> Kafka

    Kafka --> NotifySvc
    Kafka --> InventorySvc
    NotifySvc --> SendGrid & Twilio
```

**Key design decisions:**
- **Kong API Gateway** handles auth, rate limiting, and routing for all services
- **Database-per-service** pattern: each microservice owns its data store
- **Kafka** as event bus: order.created → trigger inventory deduction, payment, shipping, notifications
- **Elasticsearch** for product search with faceted filtering
- **MongoDB** for reviews: flexible schema for different product types

---

## Diagram Tips for Architecture

1. **Use `subgraph`** to group logical layers (clients, services, databases)
2. **Add emojis** to labels — makes components visually scannable
3. **Use `LR`** (left-right) for pipeline/layered architectures
4. **Use `TD`** (top-down) for hierarchical systems
5. **Label arrows** to show protocol or data type: `-->|gRPC|`, `-->|REST|`, `-->|events|`
6. **Keep it at the right abstraction level** — don't show every micro-detail

---

> ➡️ See [05-real-world/README.md](../../05-real-world/README.md) for detailed case studies with design decisions.
