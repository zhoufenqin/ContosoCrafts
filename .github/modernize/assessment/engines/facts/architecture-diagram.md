# Architecture Diagram

ContosoCrafts is an ASP.NET Core 3.1 web application that serves as a product catalog with rating capabilities, using a JSON file as a lightweight data store.

## Application Architecture

```mermaid
flowchart TD
    subgraph Client["Client Layer"]
        Browser["Web Browser"]
    end
    subgraph App["Application Layer - ASP.NET Core 3.1"]
        RazorPages["Razor Pages"]
        BlazorComp["Blazor Server Components"]
        WebAPI["Web API Controllers"]
    end
    subgraph Service["Service Layer"]
        ProductSvc["JsonFileProductService"]
    end
    subgraph Data["Data Layer"]
        JsonFile[("products.json (wwwroot/data)")]
    end
    subgraph External["External Services"]
        FontAwesome["Font Awesome CDN"]
    end

    Browser -->|"HTTP/HTTPS requests"| RazorPages
    Browser -->|"SignalR (Blazor)"| BlazorComp
    Browser -->|"REST API calls"| WebAPI
    RazorPages -->|"injects"| ProductSvc
    BlazorComp -->|"injects"| ProductSvc
    WebAPI -->|"injects"| ProductSvc
    ProductSvc -->|"reads/writes JSON"| JsonFile
    Browser -->|"loads icons"| FontAwesome
```

### Technology Stack Summary

| Layer | Technology | Version | Purpose |
|-------|-----------|---------|---------|
| Presentation | ASP.NET Core Razor Pages | 3.1 | Server-side HTML rendering for main pages |
| Presentation | Blazor Server | 3.1 | Interactive product listing component with real-time updates |
| API | ASP.NET Core Web API | 3.1 | REST endpoint for products and ratings |
| Service | JsonFileProductService | N/A | Business logic for product retrieval and rating |
| Data | JSON file (products.json) | N/A | Flat-file data store for product catalog |
| Runtime | .NET Core | 3.1 | Application runtime |

### Data Storage & External Services

The application uses a simple JSON file (`wwwroot/data/products.json`) as its data store, read and written directly by `JsonFileProductService`. There is no relational database, cache, or message broker. The only external service dependency is the Font Awesome CDN used for star rating icons in the Blazor component.

### Key Architectural Decisions

- Uses a flat JSON file as a lightweight data store instead of a relational database, keeping the application self-contained with no external database dependencies.
- Combines Razor Pages (for standard page rendering) and Blazor Server (for the interactive product list component) within the same ASP.NET Core application, with a REST API layer for PATCH operations (rating submission).
- Dependency injection is used throughout: `JsonFileProductService` is registered as `Transient` and injected into Razor Pages, Blazor components, and API controllers.

## Component Relationships

```mermaid
flowchart LR
    subgraph Presentation["Presentation"]
        IndexPage["IndexModel (Razor Page)"]
        ErrorPage["ErrorModel (Razor Page)"]
        PrivacyPage["PrivacyModel (Razor Page)"]
        ProductListComp["ProductList (Blazor Component)"]
    end
    subgraph API["API Layer"]
        ProductsCtrl["ProductsController"]
    end
    subgraph BusinessLogic["Business Logic"]
        ProductSvc["JsonFileProductService"]
    end
    subgraph DataAccess["Data Access"]
        JsonFile["products.json"]
    end
    subgraph Models["Models"]
        ProductModel["Product"]
        RatingReq["RatingRequest"]
    end

    IndexPage -->|"injects and calls"| ProductSvc
    ProductListComp -->|"injects and calls"| ProductSvc
    ProductsCtrl -->|"injects and calls"| ProductSvc
    ProductSvc -->|"reads/writes"| JsonFile
    ProductSvc -->|"deserializes"| ProductModel
    ProductsCtrl -->|"uses"| RatingReq
    IndexPage -->|"exposes"| ProductModel
```

### Component Inventory

| Component | Layer | Type | Responsibility |
|-----------|-------|------|----------------|
| IndexModel | Presentation | Razor Page | Home page — loads and exposes the product list |
| ErrorModel | Presentation | Razor Page | Error handling page |
| PrivacyModel | Presentation | Razor Page | Privacy policy page |
| ProductList | Presentation | Blazor Server Component | Interactive product listing with modal detail view and star rating |
| ProductsController | API | MVC API Controller | REST endpoints: GET all products, PATCH to add a rating |
| JsonFileProductService | Business Logic | Service | Reads/writes product data from JSON file; adds ratings |
| Product | Models | Entity/DTO | Product data model (Id, Maker, Image, Url, Title, Description, Ratings) |
| RatingRequest | Models | DTO | Request payload for rating submission (ProductId, Rating) |
