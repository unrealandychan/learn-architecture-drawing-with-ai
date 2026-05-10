# 🔀 Flowchart Examples

Five real-world flowchart examples with complete Mermaid code.

---

## 1. User Authentication Flow

A complete login/registration flow with JWT tokens.

```mermaid
flowchart TD
    Start([User visits site]) --> CheckToken{JWT in localStorage?}
    CheckToken -- Yes --> ValidateToken{Token valid & not expired?}
    CheckToken -- No --> ShowLogin[Show Login Page]

    ValidateToken -- Yes --> ShowDashboard[Show Dashboard]
    ValidateToken -- No --> ClearToken[Clear expired token]
    ClearToken --> ShowLogin

    ShowLogin --> UserAction{User action?}
    UserAction -- Login --> EnterCreds[Enter email + password]
    UserAction -- Register --> EnterRegInfo[Enter name, email, password]
    UserAction -- Social --> OAuthFlow[Redirect to OAuth Provider]

    EnterCreds --> ValidateCreds{Credentials valid?}
    ValidateCreds -- No --> ShowError[Show error message]
    ShowError --> ShowLogin
    ValidateCreds -- Yes --> Check2FA{2FA enabled?}
    Check2FA -- Yes --> Enter2FA[Enter 2FA code]
    Check2FA -- No --> IssueToken[Issue JWT token]
    Enter2FA --> Valid2FA{Code valid?}
    Valid2FA -- No --> TwoFAError[Show 2FA error]
    TwoFAError --> Enter2FA
    Valid2FA -- Yes --> IssueToken

    EnterRegInfo --> ValidateReg{Valid email & strong password?}
    ValidateReg -- No --> RegError[Show validation errors]
    RegError --> EnterRegInfo
    ValidateReg -- Yes --> CreateUser[Create user in DB]
    CreateUser --> SendVerify[Send verification email]
    SendVerify --> IssueToken

    OAuthFlow --> OAuthCallback[OAuth callback with code]
    OAuthCallback --> ExchangeCode[Exchange code for user info]
    ExchangeCode --> FindOrCreate[Find or create user]
    FindOrCreate --> IssueToken

    IssueToken --> StoreToken[Store JWT in localStorage]
    StoreToken --> ShowDashboard
```

---

## 2. CI/CD Pipeline Flow

A typical GitHub Actions CI/CD pipeline for a Node.js app.

```mermaid
flowchart TD
    Push([Developer pushes code]) --> Trigger{Branch?}
    Trigger -- feature/* --> PR[Open Pull Request]
    Trigger -- main --> MainPipeline[Run Main Pipeline]
    Trigger -- release/* --> ReleasePipeline[Run Release Pipeline]

    PR --> CIPipeline[Run CI Pipeline]
    CIPipeline --> Lint[Run ESLint & Prettier]
    Lint --> LintOK{Lint passed?}
    LintOK -- No --> FailCI[❌ Fail CI - Fix linting]
    LintOK -- Yes --> UnitTests[Run unit tests]

    UnitTests --> TestsOK{Tests passed?}
    TestsOK -- No --> FailCI
    TestsOK -- Yes --> BuildApp[Build Docker image]

    BuildApp --> BuildOK{Build success?}
    BuildOK -- No --> FailCI
    BuildOK -- Yes --> ScanImage[Security scan with Trivy]

    ScanImage --> ScanOK{No critical CVEs?}
    ScanOK -- No --> FailCI
    ScanOK -- Yes --> PassCI[✅ CI Passed - PR Ready for Review]

    PassCI --> Review{Code review approved?}
    Review -- No --> RequestChanges[Request changes]
    Review -- Yes --> MergeToMain[Merge to main]

    MergeToMain --> MainPipeline
    MainPipeline --> BuildProd[Build production image]
    BuildProd --> PushRegistry[Push to Docker Registry]
    PushRegistry --> DeployStaging[Deploy to Staging]
    DeployStaging --> SmokeTest[Run smoke tests]
    SmokeTest --> SmokeOK{Smoke tests pass?}
    SmokeOK -- No --> RollbackStaging[Rollback staging]
    SmokeOK -- Yes --> ManualApprove{Manual approval for prod?}
    ManualApprove -- Approved --> DeployProd[Deploy to Production]
    ManualApprove -- Rejected --> HoldDeploy[Hold deployment]

    DeployProd --> HealthCheck[Health check /api/health]
    HealthCheck --> HealthOK{Healthy?}
    HealthOK -- No --> RollbackProd[Rollback production]
    HealthOK -- Yes --> Done([✅ Deployment complete])
```

---

## 3. Payment Processing Flow

An e-commerce payment flow with Stripe integration.

```mermaid
flowchart TD
    Checkout([User clicks Checkout]) --> CartCheck{Cart not empty?}
    CartCheck -- No --> BackToShop[Return to shop]
    CartCheck -- Yes --> AddressForm[Show address form]

    AddressForm --> AddressOK{Address valid?}
    AddressOK -- No --> AddressError[Highlight errors]
    AddressError --> AddressForm
    AddressOK -- Yes --> PaymentForm[Show payment form]

    PaymentForm --> PayMethod{Payment method?}
    PayMethod -- Credit Card --> StripeForm[Stripe card element]
    PayMethod -- PayPal --> PayPalRedirect[Redirect to PayPal]
    PayMethod -- Apple Pay --> ApplePaySheet[Show Apple Pay sheet]

    StripeForm --> TokenizeCard[Tokenize card → Stripe token]
    TokenizeCard --> CardOK{Tokenization OK?}
    CardOK -- No --> CardError[Show card error]
    CardError --> PaymentForm
    CardOK -- Yes --> CreateOrder

    PayPalRedirect --> PayPalApprove{User approved?}
    PayPalApprove -- No --> PaymentForm
    PayPalApprove -- Yes --> CreateOrder

    ApplePaySheet --> AppleApprove{Biometric approved?}
    AppleApprove -- No --> PaymentForm
    AppleApprove -- Yes --> CreateOrder

    CreateOrder[Create order in DB - status: pending] --> LockInventory[Lock inventory]
    LockInventory --> ChargePayment[Call Stripe/PayPal charge API]

    ChargePayment --> ChargeResult{Charge result?}
    ChargeResult -- Success --> UpdateOrder[Update order status: paid]
    ChargeResult -- Insufficient funds --> FundsError[Show funds error]
    ChargeResult -- Card declined --> DeclineError[Show decline error]
    ChargeResult -- Network error --> RetryCharge{Retry < 3 times?}

    RetryCharge -- Yes --> ChargePayment
    RetryCharge -- No --> FailOrder[Update order: failed]
    FailOrder --> ReleaseInventory[Release locked inventory]
    FundsError --> PaymentForm
    DeclineError --> PaymentForm

    UpdateOrder --> SendReceipt[Send receipt email]
    SendReceipt --> TriggerFulfillment[Trigger fulfillment webhook]
    TriggerFulfillment --> ShowConfirmation([Show order confirmation page])
```

---

## 4. Error Handling & Retry Logic

A resilient API call with exponential backoff.

```mermaid
flowchart TD
    Request([Incoming API Request]) --> Validate{Request valid?}
    Validate -- No --> Return400[Return 400 Bad Request]
    Validate -- Yes --> Authenticate{Auth token valid?}

    Authenticate -- No --> Return401[Return 401 Unauthorized]
    Authenticate -- Yes --> Authorize{User has permission?}
    Authorize -- No --> Return403[Return 403 Forbidden]
    Authorize -- Yes --> CheckCache{Cache hit?}

    CheckCache -- Yes --> ReturnCached[Return cached response]
    CheckCache -- No --> CallDB[Query database]

    CallDB --> DBResult{DB response?}
    DBResult -- Success --> ProcessData[Process & transform data]
    DBResult -- Timeout --> HandleTimeout{Retry count < 3?}
    DBResult -- Connection error --> HandleConnErr{Retry count < 3?}
    DBResult -- Not found --> Return404[Return 404 Not Found]

    HandleTimeout -- Yes --> Backoff1[Wait exponential backoff]
    HandleConnErr -- Yes --> Backoff1
    Backoff1 --> IncrRetry[Increment retry counter]
    IncrRetry --> CallDB

    HandleTimeout -- No --> LogError[Log error to Sentry]
    HandleConnErr -- No --> LogError
    LogError --> Return503[Return 503 Service Unavailable]

    ProcessData --> Validate2{Data passes business rules?}
    Validate2 -- No --> Return422[Return 422 Unprocessable Entity]
    Validate2 -- Yes --> WriteCache[Write to cache - TTL 5min]
    WriteCache --> Return200[Return 200 OK with data]
```

---

## 5. Deployment Decision Tree

Deciding how to deploy a new feature safely.

```mermaid
flowchart TD
    NewFeature([New feature ready to deploy]) --> TrafficImpact{High traffic impact?}

    TrafficImpact -- Yes --> FeatureFlag{Use feature flag?}
    TrafficImpact -- No --> SimpleChange{Breaking change?}

    FeatureFlag -- Yes --> DeployFF[Deploy with flag OFF]
    FeatureFlag -- No --> BlueGreen{Blue-green available?}

    DeployFF --> MonitorFF[Enable flag for 1% traffic]
    MonitorFF --> FFMetrics{Metrics OK after 30 min?}
    FFMetrics -- Yes --> Rollout[Gradually roll out to 100%]
    FFMetrics -- No --> DisableFF[Disable flag - investigate]

    BlueGreen -- Yes --> DeployBlue[Deploy to Blue environment]
    DeployBlue --> TestBlue[Run integration tests on Blue]
    TestBlue --> TestOK{Tests pass?}
    TestOK -- Yes --> SwitchTraffic[Switch load balancer to Blue]
    TestOK -- No --> KeepGreen[Keep Green live - fix issues]
    SwitchTraffic --> MonitorBlue[Monitor for 15 min]
    MonitorBlue --> BlueOK{No errors spike?}
    BlueOK -- Yes --> DecommGreen[Decommission old Green]
    BlueOK -- No --> RollbackGreen[Rollback to Green]

    BlueGreen -- No --> Canary[Canary deployment - 5% traffic]
    Canary --> CanaryOK{Error rate OK?}
    CanaryOK -- Yes --> FullDeploy[Deploy to 100%]
    CanaryOK -- No --> CanaryRollback[Rollback canary]

    SimpleChange -- Yes --> MigrationPlan{DB migration needed?}
    SimpleChange -- No --> DirectDeploy[Direct deploy - low risk]

    MigrationPlan -- Yes --> BackwardCompat{Migration backward compatible?}
    BackwardCompat -- Yes --> MigrateFirst[Run migration first]
    MigrateFirst --> DeployCode[Deploy new code]
    BackwardCompat -- No --> Expand[Expand: add new columns]
    Expand --> DeployCode
    DeployCode --> Contract[Contract: remove old columns later]

    MigrationPlan -- No --> DirectDeploy
    DirectDeploy --> Done([✅ Deployed])
    Rollout --> Done
    DecommGreen --> Done
    FullDeploy --> Done
    Contract --> Done
```

---

> 💡 **Tip:** Copy any of these code blocks into https://mermaid.live to preview and customize them.
