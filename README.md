# JFXAI4OHS — Mercado Libre MCP Integration Architecture

## AI-Powered B2B E-Commerce, Industrial Procurement & Open Hardware Platform

> **Repository:** `robotics-intelligent-systems/jfxai4ohs`  
> **Integration target:** Official Mercado Libre MCP Server  
> **Official MCP endpoint:** `https://mcp.mercadolibre.com/mcp`
>
> **Architecture objective:** extend JFXAI4OHS with a standards-based Mercado Libre integration while preserving the project's open, modular, replaceable B2B-commerce architecture.

---

# 1. Project Context

JFXAI4OHS is an open-source reference platform for:

- B2B e-commerce;
- industrial marketplaces;
- supplier management;
- procurement;
- product catalogs;
- RFQ/RFP workflows;
- auctions and negotiation;
- ERP integration;
- order management;
- AI-assisted commerce;
- open hardware;
- manufacturing;
- robotics;
- IoT;
- aerospace;
- automotive;
- agricultural technology;
- CAD/CAM/CAS;
- Model-Based Systems Engineering.

The existing project lifecycle is:

```text
Discover
   ↓
Compare
   ↓
Configure
   ↓
Negotiate
   ↓
Request Quote
   ↓
Auction
   ↓
Purchase
   ↓
Manufacture / Assemble
   ↓
Deliver
   ↓
Install
   ↓
Operate
   ↓
Maintain
```

The Mercado Libre integration adds a major Latin American marketplace boundary to this lifecycle.

---

# 2. Important Current MCP Capability Boundary

The official Mercado Libre MCP Server is currently primarily a **developer-integration and documentation MCP service**.

Current official tools documented by Mercado Libre are:

```text
search_documentation
get_documentation_page
```

These tools allow an agentic IDE or MCP-compatible client to:

- search Mercado Libre developer documentation;
- retrieve complete documentation pages;
- identify relevant API endpoints;
- understand parameters and error cases;
- generate integration code;
- accelerate implementation against Mercado Libre APIs.

Therefore:

> **Do not model the current official Mercado Libre MCP server as if it already exposes complete seller operations such as orders, listing creation, stock updates, questions, claims, or shipments.**

Those runtime seller capabilities should remain behind the **Mercado Libre REST/API Adapter** until Mercado Libre officially exposes equivalent MCP tools.

---

# 3. Target Integration Model

```text
                         JFXAI4OHS
                              │
                              ▼
                    AI COMMERCE ORCHESTRATOR
                              │
             ┌────────────────┼────────────────┐
             ▼                ▼                ▼
       Product Agent    Procurement Agent  Supplier Agent
             │                │                │
             └────────────────┼────────────────┘
                              ▼
                         MCP GATEWAY
                              │
               ┌──────────────┼──────────────┐
               ▼              ▼              ▼
        Internal Tools   Mercado Libre    Other MCP
                         MCP Server       Servers
                              │
                              ▼
                Developer Documentation
                Integration Knowledge
                              │
                              ▼
                    ML API Adapter Layer
                              │
                              ▼
                   Mercado Libre APIs
```

---

# 4. Architectural Principle

The Mercado Libre MCP server should be treated as an **external, replaceable integration service**.

```text
OPEN JFXAI4OHS CORE
────────────────────────────────────────
Commerce
Catalog
Procurement
RFQ
Auctions
Supplier Management
ERP Integration
AI Agents
RAG
B2B APIs
Open Hardware Catalog
MBSE / CAD / CAM / CAS

             ↕ MCP / REST adapter

EXTERNAL MARKETPLACE
────────────────────────────────────────
Mercado Libre MCP Server
Mercado Libre APIs
Mercado Libre seller ecosystem
Optional Mercado Pago integration
```

This prevents marketplace vendor lock-in.

---

# 5. Official Mercado Libre MCP Server

Mercado Libre currently hosts its MCP service at:

```text
https://mcp.mercadolibre.com/mcp
```

Current Mercado Libre developer documentation states that the MCP server supports MCP-compatible clients such as:

- Cursor;
- Windsurf;
- Cline;
- Claude Desktop;
- ChatGPT;
- other MCP-compatible clients.

The current documentation describes OAuth 2.0 authorization initiated by the MCP client.

---

# 6. Recommended MCP Configuration

Conceptual configuration:

```json
{
  "mcpServers": {
    "mercadolibre-mcp-server": {
      "url": "https://mcp.mercadolibre.com/mcp"
    }
  }
}
```

For clients that require a remote MCP bridge:

```json
{
  "mcpServers": {
    "mercadolibre-mcp-server": {
      "command": "npx",
      "args": [
        "-y",
        "mcp-remote",
        "https://mcp.mercadolibre.com/mcp"
      ]
    }
  }
}
```

Authentication should follow the current Mercado Libre OAuth flow supported by the selected MCP client.

---

# 7. MCP Development-Time Flow

The strongest immediate use of the official MCP server inside JFXAI4OHS is:

```text
Developer / AI Coding Agent
          ↓
Question about Mercado Libre integration
          ↓
Official Mercado Libre MCP
          ↓
search_documentation
          ↓
Relevant API Documentation
          ↓
get_documentation_page
          ↓
Endpoint / Schema / Error Cases
          ↓
Generate Adapter Code
          ↓
Test
          ↓
JFXAI4OHS Integration Module
```

---

# 8. Example Developer Workflow

Example request:

```text
"Implement an integration for synchronizing
JFXAI4OHS product inventory with Mercado Libre."
```

Agentic development flow:

```text
AI Coding Agent
       ↓
Mercado Libre MCP
       ↓
Search current inventory/listing documentation
       ↓
Retrieve official documentation pages
       ↓
Generate API contract
       ↓
Create adapter
       ↓
Unit tests
       ↓
Integration tests
       ↓
Human review
```

This reduces the risk of generating code from obsolete API assumptions.

---

# 9. Runtime Marketplace Integration

Because the current official MCP server is documentation-oriented, runtime marketplace operations should use:

```text
JFXAI4OHS
    ↓
Commerce Service
    ↓
Mercado Libre Adapter
    ↓
OAuth / Authorization
    ↓
Mercado Libre REST APIs
```

The MCP server assists in **discovering and implementing** the correct API integration.

---

# 10. Dual-Control Architecture

The architecture should distinguish:

```text
CONTROL / KNOWLEDGE PLANE
────────────────────────────────
Mercado Libre MCP
Documentation discovery
API integration guidance
Current specifications
Code-generation context

DATA / TRANSACTION PLANE
────────────────────────────────
Mercado Libre REST APIs
Products
Listings
Orders
Inventory
Questions
Shipping
Claims
Reputation
Seller operations
```

This is the recommended architecture until the official MCP tool surface expands.

---

# 11. Mercado Libre Adapter Boundary

```text
JFXAI4OHS Domain
       ↓
Canonical Marketplace API
       ↓
MercadoLibreAdapter
       ↓
Authentication
       ↓
Rate / Retry Policy
       ↓
Mercado Libre APIs
```

Adapter responsibilities:

- API version isolation;
- canonical data mapping;
- OAuth handling;
- pagination;
- retries;
- rate-limit handling;
- idempotency;
- audit metadata;
- marketplace-specific error translation;
- observability;
- country/site mapping.

---

# 12. Canonical Marketplace Interface

Recommended JFXAI4OHS abstraction:

```text
MarketplaceProvider
├── authenticate()
├── getAccount()
├── listProducts()
├── getProduct()
├── publishProduct()
├── updateProduct()
├── updateInventory()
├── updatePrice()
├── listOrders()
├── getOrder()
├── listQuestions()
├── answerQuestion()
├── getShipment()
├── listClaims()
├── getSellerReputation()
└── syncCatalog()
```

Not every marketplace implementation must support every capability.

---

# 13. Capability Discovery

```yaml
marketplace_provider:
  id: mercadolibre
  capabilities:
    documentation_mcp: true
    product_read: api_dependent
    product_write: api_dependent
    order_read: api_dependent
    stock_write: api_dependent
    price_write: api_dependent
    questions: api_dependent
    shipments: api_dependent
    reputation: api_dependent
    claims: api_dependent
```

The capability registry prevents agents from assuming unsupported operations.

---

# 14. Mercado Libre Sites

JFXAI4OHS should treat marketplace geography as configuration.

Example site identifiers include:

```text
MLA → Argentina
MLB → Brazil
MLM / MEX → Mexico, depending on API context/documentation
MCO → Colombia
MLC → Chile
MLU → Uruguay
```

Actual site identifiers and API behavior should be resolved against current official documentation through the Mercado Libre MCP service.

---

# 15. Product Catalog Mapping

JFXAI4OHS already distinguishes commercial and technical attributes.

Recommended mapping:

```text
JFXAI4OHS Product
        │
        ├── Internal SKU
        ├── Title
        ├── Description
        ├── Category
        ├── Price
        ├── Currency
        ├── Stock
        ├── Supplier
        ├── Lead Time
        ├── Warranty
        ├── Technical Specs
        ├── CAD / BOM
        ├── Compliance
        └── Digital Product Passport
                 │
                 ▼
        Marketplace Mapper
                 │
                 ▼
        Mercado Libre Listing
```

---

# 16. Product Mapping Layer

```yaml
product_mapping:
  internal_product_id: OHS-00192
  marketplace: mercadolibre
  external_item_id: null
  site_id: MLA
  title: "Industrial Embedded Computer"
  category_mapping:
    internal: industrial_computers
    marketplace: null
  commercial:
    price: 900
    currency: USD
    available_quantity: 100
  technical_attributes:
    processor: "..."
    memory: "8 GB"
    can_bus: true
    os: "Linux"
```

The marketplace category and attribute mapping should be resolved using current API documentation.

---

# 17. Category Mapping Agent

```text
Internal Product Taxonomy
          ↓
Category Mapping Agent
          ↓
Mercado Libre Documentation MCP
          ↓
Relevant Category/API Guidance
          ↓
Mercado Libre API Adapter
          ↓
Candidate Category
          ↓
Human / Rule Validation
```

This is especially valuable for the JFXAI4OHS open-hardware catalog.

---

# 18. Open Hardware Marketplace Flow

```text
Open Hardware Project
        ↓
BOM + CAD + Firmware + Datasheet
        ↓
JFXAI4OHS Digital Product Passport
        ↓
Commercial Product Definition
        ↓
Marketplace Mapping
        ↓
Mercado Libre Listing
        ↓
Order
        ↓
ERP
        ↓
Manufacturing / Fulfillment
```

---

# 19. Digital Product Passport

For engineering products:

```text
Digital Product Passport
├── SKU
├── Product Version
├── BOM
├── CAD Files
├── Firmware
├── Software
├── Datasheet
├── Safety Information
├── Certifications
├── Manufacturing Process
├── Supplier
├── Warranty
├── Repairability
├── Lifecycle State
└── Marketplace References
```

Mercado Libre receives only the appropriate commercial/public subset.

---

# 20. Data Minimization

Do not expose unnecessary engineering IP to external marketplaces.

```text
INTERNAL
────────────────────────────
Full CAD
Full BOM
Supplier costing
Manufacturing process
Internal design notes
Simulation models

PUBLIC MARKETPLACE
────────────────────────────
Public technical specifications
Commercial description
Compatibility
Images
Warranty
Availability
Approved documentation
```

---

# 21. AI Product Assistant + Mercado Libre

Existing JFXAI4OHS concept:

```text
Buyer
  ↓
Natural Language Request
  ↓
AI Product Assistant
  ↓
Product Search
  ↓
Technical Filtering
  ↓
Supplier Matching
  ↓
Commercial Comparison
  ↓
Recommendation
```

Extended architecture:

```text
Buyer Request
     ↓
JFXAI4OHS AI Product Agent
     ↓
Internal Catalog
     +
Marketplace Providers
     ↓
Mercado Libre Adapter
     ↓
Canonical Product Results
     ↓
Technical Normalization
     ↓
Comparison
     ↓
Recommendation
```

Marketplace search availability must follow the currently permitted Mercado Libre APIs.

---

# 22. Procurement Agent

```text
Procurement Need
       ↓
Specification Agent
       ↓
Internal Suppliers
       +
Marketplace Sources
       ↓
Mercado Libre Integration
       ↓
Commercial Candidates
       ↓
Normalization
       ↓
Supplier / Offer Comparison
       ↓
Human Procurement Review
```

---

# 23. B2B Constraint

Mercado Libre is primarily a marketplace platform and may not reproduce every specialized B2B mechanism in JFXAI4OHS.

JFXAI4OHS remains authoritative for:

- RFQs;
- RFPs;
- negotiated procurement;
- reverse auctions;
- complex B2B contracts;
- configurable industrial products;
- engineering validation;
- buyer approval workflows.

Mercado Libre is treated as:

```text
External Sales / Procurement Channel
```

not as the core B2B process engine.

---

# 24. Supplier Intelligence

JFXAI4OHS can normalize external seller information into a supplier profile where permitted.

```text
Marketplace Seller
      ↓
Authorized Marketplace Data
      ↓
Supplier Adapter
      ↓
Canonical Supplier Profile
      ↓
Internal Performance Data
      ↓
Supplier Intelligence
```

Potential dimensions:

- price;
- historical internal delivery performance;
- quality;
- marketplace reputation, where available;
- availability;
- location;
- lead time;
- order history.

---

# 25. Supplier Scoring

```text
Supplier Score
     =
Internal Quality
+ Internal Delivery Reliability
+ Price Competitiveness
+ Availability
+ External Reputation Signal
+ Commercial Risk
```

The score should be:

- explainable;
- configurable;
- evidence-backed;
- subject to buyer policy.

---

# 26. Avoid Unsupported AI Inference

Do not infer:

- personal traits of sellers;
- protected characteristics;
- creditworthiness without lawful evidence;
- hidden relationships;
- arbitrary reputation categories.

Marketplace seller data should remain commercial and operational.

---

# 27. Order Integration

Canonical flow:

```text
Mercado Libre Order
        ↓
Marketplace Adapter
        ↓
Canonical Order
        ↓
JFXAI4OHS Order Service
        ↓
ERP
        ↓
Inventory
        ↓
Accounting
        ↓
Logistics
```

---

# 28. Canonical Order Model

```yaml
order:
  id: internal-order-id
  channel: mercadolibre
  external_order_id: "..."
  buyer_reference: "..."
  items:
    - product_id: "..."
      external_item_id: "..."
      quantity: 2
      unit_price: 100
  currency: "..."
  status: "..."
  fulfillment:
    mode: "..."
  payment_reference: "..."
  created_at: "..."
```

---

# 29. Inventory Synchronization

```text
ERP Inventory
      ↓
Canonical Inventory Service
      ↓
Marketplace Sync Policy
      ↓
Mercado Libre Adapter
      ↓
External Listing Stock
```

Important safeguards:

- source-of-truth definition;
- reservation handling;
- idempotency;
- retry;
- conflict detection;
- inventory floor;
- audit trail.

---

# 30. Multi-Channel Inventory

```text
                        ERP STOCK
                           │
                           ▼
                  Inventory Allocator
                    ┌──────┼──────┐
                    ▼      ▼      ▼
                  B2B    Webshop  Mercado Libre
                  Portal           Channel
```

Do not publish total physical stock blindly across every channel.

---

# 31. Price Synchronization

```text
ERP / Pricing Engine
       ↓
B2B Pricing Policy
       ↓
Channel Pricing Rules
       ↓
Mercado Libre Adapter
       ↓
Marketplace Price
```

Possible policy variables:

- marketplace fee;
- taxes;
- logistics;
- promotional strategy;
- currency;
- minimum margin;
- volume constraints.

---

# 32. Pricing Agent

```text
Cost
  +
Marketplace Fees
  +
Inventory
  +
Target Margin
  +
Demand Signals
       ↓
Pricing Recommendation
       ↓
Business Rules
       ↓
Human Approval
       ↓
Marketplace Update
```

Do not permit unrestricted autonomous repricing without policy limits.

---

# 33. Questions and Customer Interaction

If/when enabled through current Mercado Libre APIs:

```text
Buyer Question
      ↓
Mercado Libre
      ↓
Marketplace Adapter
      ↓
JFXAI4OHS Customer Interaction Service
      ↓
RAG
      ↓
AI Draft Answer
      ↓
Human / Policy Review
      ↓
Marketplace Response
```

This is especially useful for technically complex open-hardware products.

---

# 34. Technical Support RAG

Knowledge sources:

```text
Product Datasheets
Manuals
Compatibility Tables
FAQ
CAD-derived public metadata
Firmware Documentation
Warranty Policies
Installation Guides
```

Flow:

```text
Question
   ↓
Product Identity
   ↓
RAG
   ↓
Technical Evidence
   ↓
AI Draft
   ↓
Human Review
```

---

# 35. Shipping Integration

```text
Order
 ↓
Fulfillment Policy
 ↓
Marketplace Shipping Data
 ↓
ERP / WMS
 ↓
Warehouse
 ↓
Shipment
 ↓
Tracking
 ↓
Customer
```

Marketplace-specific shipping semantics remain inside the Mercado Libre adapter.

---

# 36. Claims / Post-Sale Architecture

```text
Claim / Post-Sale Event
        ↓
Marketplace Adapter
        ↓
Case Management
        ↓
Product / Order Context
        ↓
AI Case Summary
        ↓
Human Support Agent
        ↓
Resolution
```

This can later integrate with the JFXAI4CRM project.

---

# 37. Cross-Project Integration with JFXAI4CRM

```text
JFXAI4OHS
Marketplace Commerce
      ↓
Orders / Buyers / Interactions
      ↓
Canonical Event Bus
      ↓
JFXAI4CRM
Customer 360
      ↓
Retention / Support / Sales Intelligence
```

Mercado Libre-specific details should not leak into CRM domain logic.

---

# 38. Cross-Project Integration with JFXBSC

```text
Mercado Libre Channel
       ↓
Commerce KPIs
       ↓
JFXBSC / OpenBSC AI
       ↓
Balanced Scorecard
```

Example KPIs:

- marketplace revenue;
- order volume;
- conversion;
- fulfillment performance;
- cancellation rate;
- claim rate;
- inventory synchronization failures;
- contribution margin;
- channel ROI.

---

# 39. AI Commerce Orchestrator

```text
                        AI ORCHESTRATOR
                              │
       ┌──────────────┬───────┼───────────┬─────────────┐
       ▼              ▼       ▼           ▼             ▼
    Product       Supplier  Pricing     Order        Support
     Agent         Agent     Agent       Agent         Agent
       │              │       │           │             │
       └──────────────┴───────┼───────────┴─────────────┘
                              ▼
                         Tool Gateway
                              │
          ┌───────────────────┼────────────────────┐
          ▼                   ▼                    ▼
      Internal API      Mercado Libre MCP    Marketplace API
```

---

# 40. MCP Gateway

The internal MCP gateway should register external services with explicit capability metadata.

```yaml
mcp_server:
  id: mercadolibre_official
  type: remote
  endpoint: https://mcp.mercadolibre.com/mcp
  auth: oauth2
  trust: external_vendor
  capabilities:
    - search_documentation
    - get_documentation_page
```

This configuration can expand when Mercado Libre officially adds new tools.

---

# 41. Dynamic Tool Discovery

A future-compatible architecture should not hard-code the Mercado Libre tool surface.

```text
MCP Server
    ↓
tools/list
    ↓
Capability Registry
    ↓
Policy Filter
    ↓
Agent Tool Catalog
```

New tools should require:

- schema validation;
- security review;
- write/read classification;
- authorization policy;
- human-in-the-loop rules.

---

# 42. Tool Risk Classes

```text
READ-ONLY DOCUMENTATION
Low risk

READ-ONLY SELLER DATA
Moderate risk

WRITE PRODUCT / STOCK / PRICE
High operational risk

CANCEL / REFUND / CLAIM ACTION
High financial / customer risk
```

Recommended policy:

```text
Low risk
→ automatic use permitted

Moderate risk
→ role authorization

High risk
→ explicit human confirmation
```

---

# 43. Human-in-the-Loop Policy

```text
AI Suggestion
     ↓
Tool Risk Classification
     ↓
Policy Engine
     ↓
Human Approval if Required
     ↓
Marketplace Action
```

Require explicit approval for:

- listing creation;
- price changes;
- stock reductions beyond policy;
- listing deletion;
- order cancellation;
- claims resolution;
- refunds;
- financial actions.

---

# 44. OAuth Architecture

```text
User / Service
      ↓
Mercado Libre OAuth
      ↓
Authorized Session / Token
      ↓
Credential Vault
      ↓
Marketplace Adapter / MCP Client
```

Controls:

- never place secrets in source control;
- use secret management;
- isolate credentials per tenant/account;
- audit authorization;
- implement token lifecycle according to current Mercado Libre guidance.

---

# 45. Multi-Tenant Marketplace Integration

```text
Tenant A
  └── Mercado Libre Account A

Tenant B
  └── Mercado Libre Account B

Tenant C
  └── No Mercado Libre Connection
```

Never share marketplace credentials across tenants.

---

# 46. Security Boundary

```text
External MCP / API
       ↓
Zero-Trust Integration Gateway
       ↓
Schema Validation
       ↓
Policy Enforcement
       ↓
Canonical Domain
       ↓
JFXAI4OHS
```

Recommended controls:

- OAuth2;
- TLS;
- RBAC;
- ABAC;
- request validation;
- response schema validation;
- audit logs;
- secrets vault;
- rate limiting;
- timeout/circuit breaker;
- retry policy;
- data minimization.

---

# 47. Prompt-Injection Defense

Documentation returned through MCP is still external content.

Recommended agent architecture:

```text
External Documentation
       ↓
Content Boundary
       ↓
Instruction Sanitization
       ↓
Trusted System Policy
       ↓
Agent Reasoning
```

Never allow external text to override:

- system policy;
- credential controls;
- tool authorization;
- approval requirements.

---

# 48. Provenance

Every marketplace-derived fact used by AI should retain its source.

```yaml
provenance:
  provider: mercadolibre
  source_type: api
  site_id: MLA
  retrieved_at: "..."
  resource_id: "..."
  adapter_version: "..."
```

Documentation provenance:

```yaml
provenance:
  provider: mercadolibre
  source_type: mcp_documentation
  tool: search_documentation
  retrieved_at: "..."
  path: "..."
```

---

# 49. FACT vs INFERENCE

```text
MARKETPLACE FACT
Returned by Mercado Libre API

DOCUMENTATION FACT
Returned by official Mercado Libre MCP/docs

INTERNAL FACT
ERP / JFXAI4OHS data

INFERENCE
AI interpretation

PREDICTION
ML forecast

RECOMMENDATION
Proposed business action
```

Do not merge these categories.

---

# 50. Event-Driven Integration

Recommended events:

```text
MarketplaceConnectionAuthorized
ProductMapped
ListingPublished
ListingUpdated
InventorySynchronized
PriceChanged
OrderReceived
OrderUpdated
ShipmentUpdated
QuestionReceived
ClaimReceived
SellerSignalUpdated
MarketplaceSyncFailed
```

---

# 51. Event Bus Architecture

```text
Mercado Libre
      ↓
Adapter
      ↓
Canonical Event
      ↓
Kafka / Redpanda / RabbitMQ
      ↓
┌───────────┬───────────┬────────────┐
▼           ▼           ▼            ▼
Orders    Inventory     CRM          BI
```

Use the project's preferred event platform; do not make one broker mandatory.

---

# 52. ERP Integration

Existing JFXAI4OHS ERP targets include:

- Odoo;
- Tryton;
- Frappe / ERPNext;
- OpenERP references.

Extended architecture:

```text
Mercado Libre
      ↓
Marketplace Adapter
      ↓
Canonical Commerce Model
      ↓
ERP Adapter
      ↓
Odoo / Tryton / ERPNext
```

---

# 53. ERP Source-of-Truth Matrix

| Data Domain | Preferred Authority |
|---|---|
| Product master | ERP/PIM |
| Technical product data | JFXAI4OHS engineering catalog |
| Marketplace listing | Mercado Libre channel record |
| Inventory | ERP/WMS |
| Orders | Canonical commerce + ERP |
| Accounting | ERP |
| Marketplace reputation | Mercado Libre |
| CAD/BOM | Engineering PLM/JFXAI4OHS |
| AI recommendation | AI layer, never source of truth |

---

# 54. PIM Layer

For industrial commerce, introduce a Product Information Management boundary:

```text
Engineering Data
      ↓
PIM
      ↓
Canonical Product
      ↓
Channel Mappers
  ┌─────┼──────┐
  ▼     ▼      ▼
Web   B2B     Mercado Libre
```

---

# 55. PIM Validation

Before publishing externally:

```text
Product Draft
     ↓
Required Attribute Validation
     ↓
Category Validation
     ↓
Commercial Policy
     ↓
Safety / Compliance
     ↓
Marketplace Mapping
     ↓
Human Approval
     ↓
Publish
```

---

# 56. Marketplace Connector Interface

Pseudo-contract:

```typescript
interface MarketplaceConnector {
  provider(): string;
  capabilities(): Promise<MarketplaceCapabilities>;

  authenticate(): Promise<AuthState>;

  listProducts(filter?: ProductFilter): Promise<ProductRef[]>;
  getProduct(id: string): Promise<MarketplaceProduct>;

  syncProduct(product: CanonicalProduct): Promise<SyncResult>;
  syncInventory(stock: InventoryUpdate[]): Promise<SyncResult>;
  syncPrices(prices: PriceUpdate[]): Promise<SyncResult>;

  listOrders(filter?: OrderFilter): Promise<CanonicalOrder[]>;
  getOrder(id: string): Promise<CanonicalOrder>;
}
```

Implementation:

```text
MercadoLibreConnector
```

---

# 57. MCP Documentation Client

Separate from the runtime connector:

```typescript
interface MarketplaceDocumentationProvider {
  searchDocumentation(
    query: string,
    language: string,
    siteId?: string
  ): Promise<DocumentationResult[]>;

  getDocumentationPage(
    path: string,
    language: string,
    siteId?: string
  ): Promise<DocumentationPage>;
}
```

Implementation:

```text
MercadoLibreMcpDocumentationProvider
```

---

# 58. Why Separate the Two Interfaces

```text
MercadoLibreMcpDocumentationProvider
         ≠
MercadoLibreConnector
```

The first helps the developer/agent understand integration specifications.

The second performs production marketplace operations.

This separation makes the project accurate against the current official MCP tool surface.

---

# 59. Automated Integration Maintenance

A valuable JFXAI4OHS workflow:

```text
Scheduled Compatibility Check
       ↓
Mercado Libre MCP Documentation
       ↓
Compare API Guidance
       ↓
Integration Contract Tests
       ↓
Detect Breaking Change
       ↓
GitHub Issue
       ↓
Developer Review
```

---

# 60. Documentation Drift Agent

```text
Integration Code
      ↓
Referenced API Paths
      ↓
Mercado Libre MCP Documentation Search
      ↓
Current Official Documentation
      ↓
Diff / Compatibility Analysis
      ↓
Alert
```

This is an especially strong use case for the official MCP server.

---

# 61. Code Generation Agent

```text
Feature Request
      ↓
Mercado Libre Documentation MCP
      ↓
Retrieve Current Guidance
      ↓
Generate Adapter Code
      ↓
Static Analysis
      ↓
Unit Tests
      ↓
Sandbox / Test Account
      ↓
Human Review
```

---

# 62. Testing Strategy

## Unit Tests

Test:

- mappings;
- normalization;
- price policy;
- inventory policy;
- error conversion;
- tool schemas.

## Contract Tests

Test:

```text
JFXAI4OHS ↔ Mercado Libre Adapter
```

## MCP Tests

Test:

```text
MCP connection
tool discovery
search_documentation
get_documentation_page
OAuth flow
```

## Integration Tests

Use authorized test/sandbox mechanisms supported by Mercado Libre.

---

# 63. Resilience

```text
Marketplace Request
      ↓
Timeout
      ↓
Retry Policy
      ↓
Circuit Breaker
      ↓
Dead-Letter / Retry Queue
      ↓
Operational Alert
```

Avoid infinite retries.

---

# 64. Observability

Recommended telemetry:

```text
marketplace_request_count
marketplace_request_latency
marketplace_error_rate
mcp_tool_latency
mcp_tool_error_rate
inventory_sync_lag
price_sync_failures
order_import_lag
mapping_failures
oauth_refresh_failures
```

---

# 65. Audit Trail

```yaml
audit_event:
  action: inventory_sync
  provider: mercadolibre
  product_id: OHS-00192
  external_item_id: "..."
  old_value: 95
  new_value: 80
  actor: system
  approved_by: policy
  timestamp: "..."
```

---

# 66. AI Agent Permissions

Example:

```yaml
agent:
  id: marketplace_product_agent
  permissions:
    mcp:
      mercadolibre:
        - search_documentation
        - get_documentation_page
    commerce:
      - read_product
      - draft_listing
      - validate_mapping
  prohibited:
    - publish_without_approval
    - change_price_without_policy
    - delete_listing
```

---

# 67. Procurement Permissions

```text
Procurement Agent
✓ search internal catalog
✓ compare supplier data
✓ prepare RFQ
✓ retrieve marketplace data where permitted
✓ recommend supplier

✗ autonomous purchase
✗ autonomous payment
✗ bypass purchasing approval
```

---

# 68. Auction Integration

JFXAI4OHS should retain its own auction/negotiation engine.

```text
Supplier Candidates
       ↓
JFXAI4OHS Auction Engine
       ↓
Bid Evaluation
       ↓
Winning Offer
```

Mercado Libre can contribute external price/product context but should not replace specialized B2B procurement logic.

---

# 69. Local AI Integration

Optional architecture:

```text
Mercado Libre Data
        ↓
Data Policy
        ↓
Canonical Commerce Context
        ↓
Local LLM / gpt-oss
        ↓
Product / Procurement / Support Agent
```

Benefits:

- lower exposure of internal procurement data;
- private reasoning;
- local RAG;
- controlled model deployment.

---

# 70. RAG Architecture

```text
Internal Catalog
Datasheets
Contracts
Supplier Policies
Marketplace Integration Docs
Public Product Manuals
        ↓
Ingestion
        ↓
Embeddings
        ↓
Qdrant
        ↓
AI Commerce Agent
```

The Mercado Libre MCP server should remain a live source for current integration documentation rather than being blindly copied into a permanent vector index.

---

# 71. MCP + RAG Hybrid

```text
Stable Internal Knowledge
        ↓
Local RAG
        │
        ├──────────────┐
        ▼              ▼
Internal Answer   Current Marketplace Question
                       ↓
                Mercado Libre MCP
                       ↓
                Current Documentation
                       ↓
                 Combined Context
```

---

# 72. Recommended Technology Stack

| Layer | Technology |
|---|---|
| B2B Commerce | Frappe Webshop / Broadleaf / Odoo |
| ERP | ERPNext / Odoo / Tryton |
| Database | PostgreSQL |
| Vector DB | Qdrant |
| Search | OpenSearch |
| API | FastAPI / REST / GraphQL |
| Events | Redpanda / Kafka / RabbitMQ |
| Agent Integration | MCP |
| Marketplace Documentation | Official Mercado Libre MCP |
| Marketplace Runtime | Mercado Libre API Adapter |
| Identity | Keycloak / OIDC |
| Secrets | Vault / Kubernetes Secrets |
| Containers | Docker |
| Orchestration | Kubernetes / k3s |
| BI | Superset / Metabase / Grafana |
| Observability | OpenTelemetry / Prometheus / Grafana |

---

# 73. Software Dependency Classification

| Component | Role | Classification |
|---|---|---|
| Mercado Libre MCP Server | Official developer documentation MCP | External Integration |
| Mercado Libre API | Marketplace runtime | External Integration |
| MercadoLibreAdapter | Canonical marketplace bridge | Core Integration |
| MCP Gateway | Agent tool gateway | Core |
| Frappe Webshop | Commerce platform | Core Candidate |
| Broadleaf Commerce | Java commerce | Core Candidate |
| Odoo | ERP/e-commerce | Core Candidate |
| ERPNext | ERP | Core Candidate |
| Tryton | ERP | Optional |
| JADE | Multi-agent auctions | Research/Core Candidate |
| PostgreSQL | Transaction data | Core |
| Qdrant | AI/RAG | Core |
| OpenSearch | Search/analytics | Optional |
| Superset/Metabase | BI | Optional |

---

# 74. Mercado Libre MCP Classification

```yaml
dependency:
  name: Mercado Libre MCP Server
  source: https://github.com/mercadolibre/mercadolibre-mcp-server
  endpoint: https://mcp.mercadolibre.com/mcp
  owner: Mercado Libre
  classification: External Integration
  protocol: MCP
  auth: OAuth 2.0
  current_tools:
    - search_documentation
    - get_documentation_page
  production_role:
    - developer documentation
    - integration guidance
    - current API discovery
```

---

# 75. Community MCP Extensions

Community MCP servers exist that expose broader seller-oriented tool sets.

They may be useful as:

```text
Research
Reference Implementation
Prototype
Compatibility Study
```

They should **not** be described as official Mercado Libre capabilities unless maintained and documented by Mercado Libre.

Recommended classification:

```text
Community Mercado Libre MCP
→ Research / Optional
```

---

# 76. Future Official MCP Expansion

The architecture anticipates future tools such as:

```text
list_orders
list_questions
get_seller_reputation
get_product
update_stock
update_price
```

but these should only be enabled in the official adapter after Mercado Libre publishes them.

---

# 77. Future Tool Activation Flow

```text
Official MCP adds new tool
        ↓
Capability Discovery
        ↓
Security Classification
        ↓
Schema Tests
        ↓
Policy Mapping
        ↓
Sandbox Validation
        ↓
Enable for Agents
```

---

# 78. Marketplace-Agnostic Architecture

```text
                    Marketplace API
                         │
        ┌────────────────┼────────────────┐
        ▼                ▼                ▼
 Mercado Libre       Marketplace B    Marketplace C
        │                │                │
        └────────────────┼────────────────┘
                         ▼
                 Canonical Commerce
                         ▼
                     JFXAI4OHS
```

Mercado Libre becomes one provider, not the domain model.

---

# 79. Marketplace Provider Registry

```yaml
providers:
  - id: mercadolibre
    type: marketplace
    region: latam
    mcp:
      endpoint: https://mcp.mercadolibre.com/mcp
      purpose: developer_documentation
    runtime:
      type: rest_api_adapter

  - id: internal_b2b
    type: native
    region: global
```

---

# 80. Business Strategy

Mercado Libre can provide JFXAI4OHS with:

- LATAM channel access;
- product visibility;
- sales channel diversification;
- external pricing signals;
- supplier discovery context;
- market validation;
- transaction volume;
- regional marketplace integration experience.

JFXAI4OHS contributes:

- B2B procurement;
- complex RFQ;
- engineering products;
- open hardware;
- multi-agent negotiation;
- ERP integration;
- manufacturing lifecycle;
- AI product intelligence.

---

# 81. Open Hardware Commercialization Strategy

```text
Open Hardware Repository
       ↓
Engineering Validation
       ↓
Commercial Productization
       ↓
JFXAI4OHS Catalog
       ↓
Marketplace Readiness
       ↓
Mercado Libre Channel
       ↓
Regional Customers
```

---

# 82. Product Readiness Gate

Before an open-hardware product reaches an external marketplace:

```text
Design Complete
      ↓
Safety Review
      ↓
Manufacturing Readiness
      ↓
Documentation Complete
      ↓
Support Plan
      ↓
Commercial Pricing
      ↓
Marketplace Mapping
      ↓
Publish
```

---

# 83. B2B-to-B2C Bridge

JFXAI4OHS can support both:

```text
Industrial B2B
        ↓
Bulk / RFQ / Contract

and

Marketplace Channel
        ↓
Standardized Product Sale
```

Same engineering catalog, different commercial workflow.

---

# 84. Financial Architecture

```text
Marketplace Order
       ↓
Payment Reference
       ↓
Order Ledger
       ↓
ERP Accounting
       ↓
Reconciliation
       ↓
Channel Profitability
```

Payment handling should remain isolated from AI agents.

---

# 85. Optional Mercado Pago Boundary

Future architecture may add Mercado Pago as a separate payment integration:

```text
Commerce
   ↓
Payment Gateway Adapter
   ↓
Mercado Pago
```

Do not assume the Mercado Libre MCP server automatically provides Mercado Pago transaction functionality.

---

# 86. BI Metrics

Marketplace dashboard:

```text
Revenue
Orders
Units Sold
Average Order Value
Gross Margin
Channel Fees
Cancellation Rate
Claim Rate
Stockout Rate
Inventory Sync Lag
Listing Error Rate
Order Import Lag
```

---

# 87. BSC Integration

```text
Marketplace Metrics
       ↓
JFXBSC Semantic Metrics
       ↓
Objectives
       ↓
Initiatives
       ↓
Strategic Review
```

Example strategic objective:

```yaml
objective:
  name: Expand LATAM Digital Sales
  kpis:
    - marketplace_revenue
    - contribution_margin
    - order_growth
    - fulfillment_rate
```

---

# 88. MVP Architecture

Start small:

```text
JFXAI4OHS
   ↓
Canonical Product API
   ↓
MercadoLibreAdapter
   ↓
Mercado Libre API

Developer Agent
   ↓
Official Mercado Libre MCP
```

MVP capabilities:

- MCP connection;
- documentation search;
- documentation retrieval;
- OAuth setup;
- product/category integration research;
- one read-only marketplace API workflow;
- canonical product mapping;
- audit logs;
- contract tests.

---

# 89. MVP Phase 1 — Documentation MCP

Implement:

- MCP gateway;
- remote Mercado Libre MCP connection;
- `search_documentation`;
- `get_documentation_page`;
- agent prompts;
- provenance.

---

# 90. MVP Phase 2 — Read-Only Marketplace Adapter

Implement only currently approved/readable resources required by the initial business use case.

Examples:

```text
Product / Listing Read
Category Read
Seller Context
```

Exact endpoints should be selected from current official documentation.

---

# 91. MVP Phase 3 — Catalog Synchronization

Implement:

```text
Internal Product
     ↓
Mapping
     ↓
Validation
     ↓
Draft Marketplace Representation
     ↓
Human Approval
     ↓
Write API
```

---

# 92. MVP Phase 4 — Inventory & Orders

Add:

```text
Inventory Sync
Order Import
ERP Integration
Event Bus
```

---

# 93. MVP Phase 5 — AI Agent

Add:

```text
Product Agent
Supplier Agent
Support Agent
```

All write operations remain policy-controlled.

---

# 94. MVP Phase 6 — Open Hardware

Add:

- BOM metadata;
- technical documents;
- digital product passport;
- configurable-product mapping;
- public/private engineering data separation.

---

# 95. MVP Phase 7 — BI / BSC

Add:

- channel dashboards;
- profitability;
- fulfillment KPIs;
- strategic objectives.

---

# 96. Recommended Repository Structure

```text
jfxai4ohs/
├── README.md
│
├── docs/
│   ├── architecture/
│   ├── marketplace/
│   │   └── mercadolibre/
│   │       ├── architecture.md
│   │       ├── mcp.md
│   │       ├── oauth.md
│   │       ├── mappings.md
│   │       ├── security.md
│   │       └── testing.md
│   ├── procurement/
│   ├── open-hardware/
│   └── ai/
│
├── integrations/
│   ├── marketplace/
│   │   ├── canonical/
│   │   └── mercadolibre/
│   │       ├── api/
│   │       ├── mapper/
│   │       ├── oauth/
│   │       └── events/
│   │
│   └── mcp/
│       ├── gateway/
│       └── mercadolibre/
│
├── src/
│   ├── commerce/
│   ├── catalog/
│   ├── procurement/
│   ├── marketplace/
│   ├── ai/
│   ├── agents/
│   └── events/
│
├── schemas/
│   ├── marketplace-provider.yaml
│   ├── marketplace-product.yaml
│   ├── canonical-order.yaml
│   └── product-mapping.yaml
│
├── tests/
│   ├── mercadolibre/
│   ├── mcp/
│   ├── marketplace/
│   ├── security/
│   └── contract/
│
└── MBSE/
    ├── Capella/
    ├── CAD/
    ├── CAM/
    └── CAS/
```

---

# 97. MBSE → CAD → CAM → CAS

```text
MBSE
Marketplace integration requirements
System boundaries
Trust model
        ↓
CAD
Product/catalog model
Marketplace mapping
Workflow design
        ↓
CAM
Commerce services
Adapters
Deployment
        ↓
CAS
Marketplace simulations
Catalog validation
Order load tests
Agent-policy tests
Failure scenarios
        ↓
Production
```

---

# 98. CAS Scenarios

Simulate:

- marketplace unavailable;
- OAuth expired;
- price update rejected;
- inventory conflict;
- duplicate order;
- category mapping failure;
- retry storm;
- partial ERP outage;
- AI generates invalid listing content;
- MCP documentation service unavailable.

---

# 99. Failure Mode — MCP Unavailable

The production commerce path must continue:

```text
Mercado Libre MCP unavailable
          ↓
Developer assistance degraded
          ↓
Runtime Marketplace Adapter unaffected
```

This is another reason to separate documentation MCP from transaction APIs.

---

# 100. Failure Mode — Marketplace API Unavailable

```text
API unavailable
      ↓
Circuit breaker
      ↓
Queue pending operations
      ↓
Operational alert
      ↓
Retry according to policy
```

Never invent successful marketplace writes.

---

# 101. Agent Fallback

```text
MCP unavailable
     ↓
Use cached internal integration documentation
     ↓
Mark potentially stale
     ↓
Require verification before code change
```

---

# 102. DevSecOps

Pipeline:

```text
Commit
 ↓
Static Analysis
 ↓
Unit Tests
 ↓
Schema Tests
 ↓
MCP Contract Tests
 ↓
Marketplace Adapter Tests
 ↓
Security Tests
 ↓
Container Build
 ↓
SBOM
 ↓
Deploy
```

---

# 103. Secret Handling

Never commit:

```text
access_token
refresh_token
client_secret
authorization_code
session cookies
```

Use a dedicated secret store.

---

# 104. CI Test Credentials

CI should use:

- dedicated test application;
- restricted scopes;
- non-production seller/account;
- short-lived credentials where possible;
- masked logs.

---

# 105. Logging Policy

Do not log:

- access tokens;
- refresh tokens;
- payment secrets;
- unnecessary customer PII.

Log:

- correlation IDs;
- endpoint category;
- response status;
- latency;
- retry count;
- tenant;
- operation type.

---

# 106. Privacy

Marketplace customer data should be minimized to the business purpose.

```text
Marketplace Data
       ↓
Purpose Filter
       ↓
Canonical Order / Customer Reference
       ↓
ERP / CRM
```

Do not turn marketplace data into unrelated profiling data.

---

# 107. AI Safety

AI may:

- summarize;
- classify;
- recommend;
- draft;
- compare;
- explain.

AI should not autonomously:

- issue refunds;
- cancel paid orders;
- change contractual terms;
- publish unsafe products;
- bypass marketplace policies;
- modify prices outside policy.

---

# 108. Compliance-by-Design

```text
Marketplace Policy
      ↓
Integration Rules
      ↓
Code
      ↓
Automated Tests
      ↓
Runtime Enforcement
```

Use the official MCP documentation service to keep developer guidance close to current Mercado Libre requirements.

---

# 109. Marketplace Documentation as Live Dependency

Treat documentation as:

```text
Dynamic External Knowledge
```

not as immutable application logic.

Integration behavior should be implemented in version-controlled code and tested.

---

# 110. Community Reference Implementations

There are community implementations of Mercado Libre MCP integrations with larger tool surfaces.

They can be useful to study:

- OAuth patterns;
- seller tool design;
- tool schemas;
- HITL gates;
- rate limiting;
- error handling.

But production decisions should be based on:

```text
Official Mercado Libre Documentation
+
Approved APIs
+
Security Review
```

---

# 111. Future Agentic Commerce

Long-term:

```text
Buyer Intent
    ↓
Procurement Agent
    ↓
Product Requirements
    ↓
Multi-Marketplace Search
    ↓
Supplier / Product Comparison
    ↓
Policy Evaluation
    ↓
Human Approval
    ↓
Purchase Workflow
```

---

# 112. Industrial Agentic Commerce

For industrial components:

```text
Engineering Requirement
      ↓
MBSE Requirement
      ↓
Technical Product Search
      ↓
Compatibility Check
      ↓
Supplier / Marketplace Discovery
      ↓
Cost / Lead-Time Evaluation
      ↓
Procurement Approval
```

This is a core differentiator of JFXAI4OHS.

---

# 113. Engineering Compatibility Agent

```text
System Requirement
       ↓
Required Interface / Voltage / Dimensions
       ↓
Candidate Product
       ↓
Technical Attribute Match
       ↓
CAD / BOM Compatibility
       ↓
Recommendation
```

Marketplace data alone is insufficient; the internal engineering catalog remains authoritative.

---

# 114. Open-Hardware Supply Chain

```text
Design
  ↓
BOM
  ↓
Components
  ↓
Supplier Sources
  ↓
Marketplace Channels
  ↓
Procurement
  ↓
Manufacturing
```

Mercado Libre can function as one source/channel within this graph.

---

# 115. Supply Risk Graph

```text
Component
  ├── Supplier A
  ├── Supplier B
  └── Mercado Libre Offers
         ↓
Availability / Price / Geography
         ↓
Supply Risk
```

---

# 116. Knowledge Graph

Potential entities:

```text
Product
Component
Supplier
Marketplace
Listing
Order
Manufacturer
CAD Model
BOM
Certification
Category
Site
```

Relations:

```text
Product HAS_COMPONENT Component
Supplier OFFERS Product
Product LISTED_ON Marketplace
Listing MAPS_TO Product
Product HAS_CAD CADModel
Product HAS_CERTIFICATION Certification
```

---

# 117. AI Product Matching

```text
Natural Language Need
       ↓
Structured Requirements
       ↓
Vector / Semantic Search
       ↓
Technical Filters
       ↓
Marketplace Candidates
       ↓
Rules
       ↓
Ranked Products
```

---

# 118. Commercial vs Engineering Ranking

```text
Overall Product Fit
      =
Technical Compatibility
+ Availability
+ Price
+ Supplier Reliability
+ Delivery
+ Lifecycle Risk
```

Technical compatibility should normally have hard constraints for engineering use cases.

---

# 119. Example Scenario

User:

```text
"Find a Linux industrial computer for a robot,
minimum 8 GB RAM, CAN bus, 12–24 V input,
and availability in Argentina."
```

Flow:

```text
AI Product Agent
       ↓
Parse constraints
       ↓
Internal JFXAI4OHS Catalog
       +
Authorized Marketplace Search
       ↓
Technical Normalization
       ↓
Hard Constraint Validation
       ↓
Commercial Comparison
       ↓
Recommendation
```

---

# 120. Example Development Scenario Using MCP

Developer:

```text
"How should this adapter authenticate
and retrieve the relevant marketplace resource?"
```

Flow:

```text
Coding Agent
    ↓
search_documentation
    ↓
get_documentation_page
    ↓
Current Mercado Libre Guidance
    ↓
Generate Code
```

---

# 121. Recommended Component Priority

| Component | Priority |
|---|---:|
| Canonical marketplace model | 5/5 |
| Mercado Libre official MCP | 5/5 |
| Mercado Libre REST adapter | 5/5 |
| OAuth/security | 5/5 |
| Product mapping | 5/5 |
| Order integration | 5/5 |
| Inventory sync | 5/5 |
| ERP integration | 5/5 |
| AI product agent | 4/5 |
| Technical RAG | 4/5 |
| Supplier intelligence | 4/5 |
| Automated pricing | 3/5 |
| Full autonomous purchasing | 1/5 |

---

# 122. Recommended Final Stack

```text
JFXAI4OHS
   │
   ├── Commerce Core
   │     Frappe Webshop / Broadleaf / Odoo
   │
   ├── ERP
   │     ERPNext / Odoo / Tryton
   │
   ├── Data
   │     PostgreSQL
   │
   ├── Search
   │     OpenSearch
   │
   ├── RAG
   │     Qdrant
   │
   ├── Agents
   │     MCP Tool Gateway
   │
   ├── Mercado Libre
   │     ├── Official MCP Server
   │     └── REST API Adapter
   │
   ├── Events
   │     Kafka / Redpanda / RabbitMQ
   │
   └── BI
         Superset / Metabase / Grafana
```

---

# 123. Final Integrated Architecture

```text
┌──────────────────────────────────────────────────────────────────────┐
│                             JFXAI4OHS                                │
│                                                                      │
│ B2B Commerce | Procurement | Open Hardware | Engineering Catalog     │
└───────────────────────────────┬──────────────────────────────────────┘
                                │
                                ▼
┌──────────────────────────────────────────────────────────────────────┐
│                    CANONICAL COMMERCE LAYER                          │
│ Products | Suppliers | Listings | Orders | Inventory | Pricing      │
└───────────────────────────────┬──────────────────────────────────────┘
                                │
            ┌───────────────────┼───────────────────┐
            ▼                   ▼                   ▼
       ERP Adapter          AI Agents        Marketplace Gateway
            │                   │                   │
            │                   │         ┌─────────┴─────────┐
            │                   │         ▼                   ▼
            │                   │  Mercado Libre MCP   Mercado Libre API
            │                   │  Documentation       Runtime Adapter
            │                   │         │                   │
            │                   │         └─────────┬─────────┘
            │                   │                   ▼
            │                   │            Mercado Libre
            │                   │
            │                   ▼
            │              RAG / Qdrant
            │                   │
            └───────────────────┼───────────────────┐
                                ▼                   ▼
                         PostgreSQL             BI / BSC
```

---

# 124. Strategic Recommendation

The recommended design is **not**:

```text
JFXAI4OHS
   ↓
Mercado Libre MCP
   ↓
Everything
```

because the current official MCP server does not expose the entire seller transaction surface.

The recommended architecture is:

```text
JFXAI4OHS
      +
Canonical Marketplace Model
      +
Official Mercado Libre MCP
for current developer documentation
      +
Mercado Libre REST/API Adapter
for approved runtime operations
      +
OAuth
      +
ERP / Inventory / Orders
      +
AI Agents with Human Approval
```

This design is accurate against the current Mercado Libre MCP capabilities and is ready to adopt new official tools as Mercado Libre expands the server.

---

# 125. Key Design Principle

> **Use the official Mercado Libre MCP server as a live, trusted integration-knowledge interface; use canonical JFXAI4OHS adapters for production marketplace operations; and activate new MCP transaction tools only after they are officially documented, security-reviewed, and policy-mapped.**

---

# 126. Current Official References

## JFXAI4OHS

- https://github.com/robotics-intelligent-systems/jfxai4ohs

## Official Mercado Libre MCP Repository

- https://github.com/mercadolibre/mercadolibre-mcp-server

## Official MCP Endpoint

- https://mcp.mercadolibre.com/mcp

## Official Mercado Libre Developer Documentation

- https://developers.mercadolibre.com.ar/mcp-server

---

# 127. Current Official MCP Tools

As documented by Mercado Libre at the time of this architecture:

```text
search_documentation
get_documentation_page
```

The tool surface may change over time.

JFXAI4OHS should discover and validate current MCP tools rather than assuming a static list.

---

# 128. Disclaimer

This document is an integration architecture proposal.

Mercado Libre:

- API capabilities;
- MCP tools;
- OAuth behavior;
- site identifiers;
- seller permissions;
- rate limits;
- marketplace policies;
- transaction endpoints;

may change over time.

Production implementation must always verify current Mercado Libre documentation and authorization requirements.

The official MCP server should not be represented as supporting seller write operations unless those tools are officially exposed and documented.
