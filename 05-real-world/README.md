# 🌍 Real-World Architecture Case Studies

Four production-level system designs with complete Mermaid diagrams and detailed design decisions.

---

## Case Study 1: URL Shortener (like bit.ly)

### Overview
A URL shortener takes a long URL and returns a short one (e.g., `bit.ly/abc123`). When a user visits the short URL, they're redirected to the original. The key challenge is handling **billions of redirects** with sub-10ms latency.

### Key Design Decisions

| Decision | Choice | Reason |
|---|---|---|
| ID generation | Base62 encoding | 6 chars = 56 billion unique URLs |
| Storage | PostgreSQL + Redis | SQL for durability, Redis for fast reads |
| Redirect type | 301 vs 302 | 302 (temporary) — allows analytics tracking |
| Scaling | Horizontal app servers | Each is stateless, easy to scale |
| Analytics | Async via Kafka | Don't slow down the redirect path |
| Custom domains | Multi-tenant routing | Enterprise feature (CNAME support) |

### Architecture Diagram

```mermaid
flowchart LR
    subgraph Clients ["👥 Clients"]
        Browser[🌐 Browser]
        API[⚙️ API Consumer]
    end

    subgraph Edge ["🌍 Edge Layer"]
        DNS[Route 53\nGeoDNS]
        CF[☁️ CloudFront\nCDN]
    end

    subgraph LB ["⚖️ Load Balancer"]
        ALB[AWS ALB]
    end

    subgraph App ["🟢 App Servers (Auto Scaling Group)"]
        App1[App Server 1]
        App2[App Server 2]
        App3[App Server 3]
    end

    subgraph DataLayer ["🗄️ Data Layer"]
        Redis[("⚡ Redis Cluster\n(short_code → long_url)\nTTL: 24h")]
        PG[("🐘 PostgreSQL\nurls table\nclick_logs table")]
        Kafka["📨 Kafka\nclick_events topic"]
    end

    subgraph Analytics ["📊 Analytics Pipeline"]
        Consumer[Event Consumer\nNode.js Worker]
        CH[("📈 ClickHouse\nAggregated click data")]
        Grafana[📉 Grafana\nDashboard]
    end

    subgraph Admin ["🛠️ Admin"]
        AdminUI[Admin Panel\nReact App]
    end

    Browser & API --> DNS --> CF --> ALB
    ALB --> App1 & App2 & App3
    App1 & App2 & App3 --> Redis
    App1 & App2 & App3 --> PG
    App1 & App2 & App3 --> Kafka
    Kafka --> Consumer --> CH --> Grafana
    AdminUI --> ALB
```

### Short URL Redirect Flow

```mermaid
sequenceDiagram
    participant Browser
    participant CDN as CloudFront CDN
    participant App as App Server
    participant Cache as Redis
    participant DB as PostgreSQL
    participant Kafka

    Browser->>CDN: GET /abc123
    CDN->>App: Cache miss (first visit)
    App->>Cache: GET url:abc123
    Cache-->>App: null (cache miss)
    App->>DB: SELECT long_url WHERE code='abc123'
    DB-->>App: https://very-long-url.com/path
    App->>Cache: SET url:abc123 "https://..." EX 86400
    App->>Kafka: Produce click event {code, ip, referrer, timestamp}
    App-->>CDN: 302 Location: https://very-long-url.com/path
    CDN-->>Browser: 302 Redirect (cached for next 60s)
    Browser->>Browser: Navigate to destination
```

---

## Case Study 2: Chat Application (like WhatsApp)

### Overview
A real-time messaging app supporting billions of users. The core challenge: **persistent WebSocket connections** across a distributed server fleet, ensuring messages are delivered even when sender and receiver connect to different servers.

### Key Design Decisions

| Decision | Choice | Reason |
|---|---|---|
| Transport | WebSocket | Bidirectional, low-overhead persistent connection |
| Message routing | Redis PubSub | Fan-out messages across server instances |
| Message storage | Cassandra | Write-heavy, time-series, scales horizontally |
| Offline delivery | Push notifications | APNS + FCM when WebSocket is closed |
| Media | Separate service + S3 | Keep chat service lightweight |
| Message ordering | Snowflake IDs | Globally unique, time-sortable IDs |
| End-to-end encryption | Signal Protocol | Client-side encryption, server can't read |

### Architecture Diagram

```mermaid
flowchart TD
    subgraph Clients ["📱 Clients"]
        iOS[iOS App]
        Android[Android App]
        Web[Web App]
    end

    subgraph Gateway ["🚪 Connection Layer"]
        GeoDNS[🌍 GeoDNS\nRoute to nearest region]
        WSGateway["🔌 WebSocket Gateway\n(Node.js cluster)\nHolds persistent connections"]
    end

    subgraph Services ["⚙️ Core Services"]
        AuthSvc[🔐 Auth Service\nJWT validation]
        ChatSvc[💬 Chat Service\nMessage routing]
        PresenceSvc[🟢 Presence Service\nonline/offline status]
        MediaSvc[🖼️ Media Service\nupload/compress]
        PushSvc[🔔 Push Service\noffline delivery]
        SearchSvc[🔍 Search Service\nmessage search]
    end

    subgraph Messaging ["📨 Messaging Infrastructure"]
        Redis["⚡ Redis Cluster\nPubSub for fan-out\nSession store\nPresence data"]
        Kafka["📨 Kafka\nmessage.sent events\nuser.status events"]
    end

    subgraph Storage ["🗄️ Storage"]
        Cassandra[("💬 Cassandra\nMessages\nPartition: chat_id\nSort: message_id")]
        UserDB[("👤 PostgreSQL\nUsers & Contacts")]
        S3["🗃️ S3\nImages, Videos\nAudio messages"]
        Elastic[("🔍 Elasticsearch\nMessage search index")]
    end

    subgraph Push ["📲 Push Notifications"]
        APNS[🍎 Apple APNS]
        FCM[🤖 Google FCM]
    end

    iOS & Android & Web --> GeoDNS
    GeoDNS --> WSGateway
    WSGateway --> AuthSvc
    WSGateway --> Redis
    WSGateway --> ChatSvc
    ChatSvc --> Kafka
    ChatSvc --> Cassandra
    ChatSvc --> PresenceSvc
    PresenceSvc --> Redis
    Kafka --> PushSvc
    Kafka --> SearchSvc
    PushSvc --> APNS & FCM
    SearchSvc --> Elastic
    MediaSvc --> S3
    AuthSvc --> UserDB
```

---

## Case Study 3: Video Streaming (like Netflix)

### Overview
A video streaming platform that serves high-quality video to 200M+ users globally. The key challenge: **content delivery at massive scale** with adaptive bitrate streaming and zero buffering.

### Key Design Decisions

| Decision | Choice | Reason |
|---|---|---|
| Video encoding | Multiple bitrates | Adaptive bitrate (ABR) — adjusts to connection speed |
| Format | MPEG-DASH / HLS | Industry standard streaming protocols |
| Storage | S3 + CDN | Store once, serve globally from edge |
| CDN strategy | Multi-CDN | Akamai + CloudFront + own ISP caches (Open Connect) |
| Recommendation | ML pipeline | User behavior → personalized homepage |
| Metadata | Cassandra | Movie catalog, user watch history |
| Search | Elasticsearch | Full-text search across 15,000+ titles |

### Architecture Diagram

```mermaid
flowchart LR
    subgraph UserDevices ["📺 User Devices"]
        TV[Smart TV App]
        WebBrowser[Web Browser]
        MobileApp[Mobile App]
    end

    subgraph CDNLayer ["☁️ Content Delivery"]
        CDN["🌍 Multi-CDN\nAkamai + CloudFront\n+ ISP Open Connect Appliances"]
    end

    subgraph APIGateway ["🚪 API Layer"]
        Zuul["🚪 API Gateway (Zuul)\nRouting + Auth + Rate Limit"]
    end

    subgraph CoreServices ["⚙️ Core Microservices"]
        AuthSvc[🔐 Auth & Profile]
        CatalogSvc[🎬 Catalog Service\nMovie metadata]
        SearchSvc[🔍 Search Service]
        RecoSvc[🤖 Recommendation\nML Service]
        PlaybackSvc[▶️ Playback Service\nDRM + license]
        BillingSvc[💳 Billing Service]
    end

    subgraph ContentPipeline ["🎬 Content Ingestion Pipeline"]
        Ingest[📥 Content Ingest\nStudio uploads]
        Transcoder["⚙️ Transcoding Farm\n(AWS MediaConvert)\n4K, 1080p, 720p, 480p, 360p\nH.264, H.265, AV1"]
        QC[✅ Quality Control\nAutomated QC checks]
        PackageCDN[📦 Package &\nPush to CDN]
    end

    subgraph DataStores ["🗄️ Data Stores"]
        MySQL[("🐬 MySQL\nUser accounts\nBilling")]
        Cassandra[("📊 Cassandra\nWatch history\nRatings\nResume position")]
        Elastic[("🔍 Elasticsearch\nContent catalog search")]
        S3["🗃️ S3\nEncoded video\nThumbnails\nSubtitles"]
    end

    subgraph Analytics ["📊 Analytics & ML"]
        Kafka["📨 Kafka\nPlay events\nSearch events"]
        Spark["⚡ Apache Spark\nBatch processing"]
        MLPlatform["🤖 ML Platform\nCollaborative filtering\nContent-based reco"]
    end

    UserDevices --> CDN
    CDN --> Zuul
    Zuul --> AuthSvc & CatalogSvc & SearchSvc & RecoSvc & PlaybackSvc & BillingSvc
    AuthSvc --> MySQL
    BillingSvc --> MySQL
    CatalogSvc --> Cassandra & Elastic
    SearchSvc --> Elastic
    RecoSvc --> MLPlatform
    PlaybackSvc --> Cassandra
    PlaybackSvc --> CDN

    Ingest --> Transcoder --> QC --> S3
    S3 --> PackageCDN --> CDN

    Zuul --> Kafka
    Kafka --> Spark --> MLPlatform
```

---

## Case Study 4: Ride-Sharing (like Uber)

### Overview
A ride-sharing platform connecting riders and drivers in real-time. The core challenge: **geospatial matching at scale** — finding the nearest available driver to a rider in milliseconds, across millions of concurrent drivers worldwide.

### Key Design Decisions

| Decision | Choice | Reason |
|---|---|---|
| Location tracking | Driver app sends location every 5s | Balance accuracy vs battery/bandwidth |
| Driver location index | Redis Geospatial (GEOADD) | O(log N) radius searches, in-memory speed |
| Matching algorithm | Bipartite graph matching | Optimal assignment of riders to drivers |
| Surge pricing | ML model | Demand/supply ratio → dynamic pricing |
| Trip data | PostgreSQL | ACID transactions for financial records |
| Maps | In-house (Uber) or Google Maps API | Navigation, ETAs, routing |
| Payments | Internal + Stripe | PCI compliance, multi-currency |

### Architecture Diagram

```mermaid
flowchart TD
    subgraph Users ["👥 Users"]
        RiderApp[📱 Rider App\niOS/Android]
        DriverApp[📱 Driver App\niOS/Android]
        WebApp[🌐 Web App]
    end

    subgraph Gateway ["🚪 API Layer"]
        ALB[⚖️ Load Balancer]
        APIGateway[🚪 API Gateway\nAuth + Routing]
        WSGateway[🔌 WebSocket Server\nReal-time updates]
    end

    subgraph CoreServices ["⚙️ Core Services"]
        RiderSvc[👤 Rider Service]
        DriverSvc[🚗 Driver Service]
        TripSvc[🗺️ Trip Service\nLifecycle management]
        MatchSvc["🎯 Matching Service\nFinds nearest driver"]
        LocationSvc["📍 Location Service\nReal-time tracking"]
        PricingSvc["💰 Pricing Service\nSurge calculation"]
        PaymentSvc[💳 Payment Service]
        NotifySvc[🔔 Notification Service]
        MapSvc[🗺️ Map Service\nETA + routing]
    end

    subgraph DataStores ["🗄️ Data Stores"]
        Redis[("⚡ Redis\nDriver locations\nGEOADD commands\nActive trips")]
        PostgreSQL[("🐘 PostgreSQL\nUsers, Trips\nPayments, Ratings")]
        Kafka["📨 Kafka\nlocation.updates\ntrip.events\npayment.events"]
        S3["🗃️ S3\nTrip history\nAnalytics data"]
    end

    subgraph External ["🌐 External"]
        GoogleMaps[🗺️ Google Maps\nAPI]
        Stripe[💳 Stripe]
        APNS[🍎 APNS]
        FCM[🤖 FCM]
        Twilio[📱 Twilio SMS]
    end

    RiderApp & DriverApp & WebApp --> ALB
    ALB --> APIGateway & WSGateway
    APIGateway --> RiderSvc & DriverSvc & TripSvc & PricingSvc & PaymentSvc
    WSGateway --> LocationSvc

    DriverApp -.->|"location every 5s\nWebSocket"| WSGateway

    LocationSvc --> Redis
    LocationSvc --> Kafka

    TripSvc --> MatchSvc
    MatchSvc --> Redis
    MatchSvc --> PricingSvc

    PricingSvc --> Kafka

    TripSvc --> PostgreSQL
    TripSvc --> MapSvc
    MapSvc --> GoogleMaps

    PaymentSvc --> Stripe
    PaymentSvc --> PostgreSQL

    Kafka --> NotifySvc
    Kafka --> S3

    NotifySvc --> APNS & FCM & Twilio
    WSGateway -.->|"real-time location\nupdates to rider"| RiderApp
```

### The Matching Algorithm Flow

```mermaid
sequenceDiagram
    participant Rider as 📱 Rider App
    participant API as ⚙️ API Gateway
    participant Trip as 🗺️ Trip Service
    participant Match as 🎯 Matching Service
    participant Redis as ⚡ Redis Geo Index
    participant Driver as 📱 Driver App
    participant WS as 🔌 WebSocket Server

    Rider->>API: POST /trips/request {pickup, dropoff}
    API->>Trip: Create trip request
    Trip->>Match: FindNearestDrivers(pickup_lat, pickup_lng)

    Match->>Redis: GEORADIUS drivers:available {lat} {lng} 5 km
    Redis-->>Match: [driver_456 (0.8km), driver_789 (1.2km), driver_123 (2.1km)]

    Match->>Match: Score drivers (distance, rating, car_type)
    Match->>WS: Send trip offer to driver_456

    WS-->>Driver: Push: New trip offer {pickup, fare, ETA}
    Note over Driver: 15-second accept window

    Driver->>API: POST /trips/accept {trip_id}
    API->>Trip: Update trip status: DRIVER_ASSIGNED
    Trip->>Redis: Remove driver_456 from available pool

    Trip->>WS: Notify rider — driver found
    WS-->>Rider: Push: Driver en route {driver_name, car, ETA}

    Note over Driver,Rider: Driver navigates to pickup
    Driver->>API: POST /trips/arrived
    WS-->>Rider: Push: Driver has arrived!

    Rider->>API: POST /trips/start (or driver swipes)
    Trip->>Trip: Update status: IN_PROGRESS, start timer

    Note over Driver,Rider: Trip in progress — location streamed
    Driver->>WS: Location updates every 5s
    WS-->>Rider: Real-time driver position on map

    Driver->>API: POST /trips/complete
    Trip->>Trip: Calculate fare (time + distance + surge)
    Trip->>API: Charge rider via Stripe
    WS-->>Rider: Trip complete — receipt sent
```

---

## Summary: Design Pattern Comparison

| System | Read/Write | Key Challenge | Solution |
|---|---|---|---|
| **URL Shortener** | Read-heavy (1000:1) | Low latency redirects | Redis cache + CDN |
| **Chat App** | Write-heavy, real-time | Message fan-out at scale | WebSocket + Redis PubSub |
| **Video Streaming** | Read-heavy, large files | Global content delivery | Multi-CDN + ABR encoding |
| **Ride-sharing** | Real-time, geospatial | Driver-rider matching | Redis Geo + WebSocket |

---

> 💡 **Want to design your own?** Use the [AI prompt templates](../04-ai-prompts/README.md) and the [SOP framework](../04-ai-prompts/sop.md) to design any system step-by-step.
