# MVC Music Store

## Overview

The MVC Music Store is an ASP.NET MVC web application that demonstrates a complete e-commerce solution for selling music albums online. This application showcases the Model-View-Controller (MVC) pattern using ASP.NET MVC 4 on .NET Framework 4.8, integrating Entity Framework for data access, authentication and authorization, shopping cart functionality, and order processing.

## Current Architecture

### Technology Stack

| Component | Technology | Version |
|-----------|-----------|---------|
| **Framework** | .NET Framework | 4.8 |
| **Web Framework** | ASP.NET MVC | 4.0 |
| **Web API** | ASP.NET Web API | 4.0 |
| **ORM** | Entity Framework | 5.0 |
| **Database** | SQL Server LocalDB | (LocalDB)\MSSQLLocalDB |
| **Authentication** | Forms Authentication | Built-in |
| **Frontend Libraries** | jQuery | 1.7.1 |
| | jQuery UI | 1.8.20 |
| | Knockout.js | 2.1.0 |
| | Modernizr | 2.5.3 |
| **OAuth** | DotNetOpenAuth | 4.0.3 |

### Architecture Pattern

The application follows the **Model-View-Controller (MVC)** architectural pattern:

```
┌─────────────────────────────────────────────────────────┐
│                    Client Browser                        │
└────────────────┬────────────────────────────────────────┘
                 │ HTTP Request/Response
                 ▼
┌─────────────────────────────────────────────────────────┐
│              ASP.NET MVC Pipeline                        │
│  ┌──────────┐  ┌──────────┐  ┌──────────────────────┐  │
│  │  Routing │─▶│Controller│─▶│ View Engine (Razor)  │  │
│  └──────────┘  └─────┬────┘  └──────────────────────┘  │
└─────────────────────┼──────────────────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────────────────┐
│                Business Logic Layer                      │
│  ┌──────────┐  ┌─────────────┐  ┌─────────────────┐    │
│  │  Models  │  │ ViewModels  │  │ ShoppingCart    │    │
│  └──────────┘  └─────────────┘  └─────────────────┘    │
└─────────────────────┼──────────────────────────────────┘
                      │ Entity Framework 5
                      ▼
┌─────────────────────────────────────────────────────────┐
│              Data Access Layer                           │
│  ┌──────────────────────────────────────────────────┐   │
│  │         MusicStoreEntities (DbContext)           │   │
│  └──────────────────────────────────────────────────┘   │
└─────────────────────┼──────────────────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────────────────┐
│         SQL Server LocalDB (MvcMusicStore.mdf)          │
└─────────────────────────────────────────────────────────┘
```

## Project Structure

```
MvcMusicStore/
│
├── App_Start/                    # Application configuration
│   ├── AppConfig.cs              # Custom app initialization
│   ├── AuthConfig.cs             # OAuth/authentication config
│   ├── BundleConfig.cs           # Script/CSS bundling
│   ├── FilterConfig.cs           # Global action filters
│   ├── RouteConfig.cs            # URL routing configuration
│   └── WebApiConfig.cs           # Web API routes
│
├── Controllers/                  # MVC Controllers (handles requests)
│   ├── AccountController.cs      # User authentication & registration
│   ├── CheckoutController.cs     # Order checkout process
│   ├── HomeController.cs         # Homepage
│   ├── ShoppingCartController.cs # Shopping cart management
│   ├── StoreController.cs        # Browse albums/genres
│   └── StoreManagerController.cs # Admin album management
│
├── Models/                       # Domain models (business entities)
│   ├── AccountModels.cs          # User account models
│   ├── Album.cs                  # Album entity
│   ├── Artist.cs                 # Artist entity
│   ├── Cart.cs                   # Shopping cart item
│   ├── Genre.cs                  # Music genre entity
│   ├── MusicStoreEntities.cs     # EF DbContext (data access)
│   ├── Order.cs                  # Order entity
│   ├── OrderDetail.cs            # Order line items
│   ├── SampleData.cs             # Database seed data
│   └── ShoppingCart.cs           # Shopping cart logic
│
├── ViewModels/                   # View-specific data models
│   ├── ShoppingCartViewModel.cs
│   └── ShoppingCartRemoveViewModel.cs
│
├── Views/                        # Razor views (UI templates)
│   ├── Account/                  # Login/register views
│   ├── Checkout/                 # Checkout process views
│   ├── Home/                     # Homepage views
│   ├── Shared/                   # Layout & partial views
│   ├── ShoppingCart/             # Cart views
│   ├── Store/                    # Store browsing views
│   └── StoreManager/             # Admin management views
│
├── Filters/                      # Custom action filters
│   └── InitializeSimpleMembershipAttribute.cs
│
├── Content/                      # Static content (CSS, images)
├── Scripts/                      # JavaScript files
├── App_Data/                     # Database files (LocalDB)
│
├── Global.asax.cs                # Application lifecycle events
├── Web.config                    # Application configuration
└── packages.config               # NuGet package dependencies
```

## Core Components

### 1. Data Layer

**Entity Framework 5 Code First Approach**

The application uses Entity Framework 5 with Code First methodology to define the database schema through C# classes.

**DbContext**:
```csharp
public class MusicStoreEntities : DbContext
{
    public DbSet<Album>       Albums { get; set; }
    public DbSet<Genre>       Genres { get; set; }
    public DbSet<Artist>      Artists { get; set; }
    public DbSet<Cart>        Carts { get; set; }
    public DbSet<Order>       Orders { get; set; }
    public DbSet<OrderDetail> OrderDetails { get; set; }
}
```

**Connection Strings**:
- `MusicStoreEntities`: Main application database (MvcMusicStore.mdf)
- `DefaultConnection`: Membership/authentication database

**Database**: SQL Server LocalDB with file-based storage in `App_Data/`

### 2. Domain Models

#### Core Entities

**Album** - Music album information
- Properties: AlbumId, Title, Price, AlbumArtUrl
- Relationships: Genre (many-to-one), Artist (many-to-one)
- Validation: Required fields, price range, string length

**Genre** - Music categories
- Properties: GenreId, Name, Description
- Relationships: Albums (one-to-many)

**Artist** - Music artists
- Properties: ArtistId, Name
- Relationships: Albums (one-to-many)

**Order** - Customer order header
- Properties: OrderId, OrderDate, Username, Total
- Relationships: OrderDetails (one-to-many)

**OrderDetail** - Order line items
- Properties: OrderDetailId, Quantity, UnitPrice
- Relationships: Order (many-to-one), Album (many-to-one)

**Cart** - Shopping cart items
- Properties: RecordId, CartId, AlbumId, Count, DateCreated
- Relationships: Album (many-to-one)

### 3. Controllers

#### StoreController
- **Purpose**: Public store browsing functionality
- **Actions**:
  - `Index()` - List all genres
  - `Browse(genre)` - List albums by genre
  - `Details(id)` - Album details
  - `GenreMenu()` - Popular genres (child action)

#### ShoppingCartController
- **Purpose**: Shopping cart management
- **Actions**:
  - `Index()` - View cart contents
  - `AddToCart(id)` - Add album to cart
  - `RemoveFromCart(id)` - Remove item from cart (AJAX)
  - `CartSummary()` - Cart summary widget

#### CheckoutController
- **Purpose**: Order processing
- **Actions**:
  - `AddressAndPayment()` - Collect shipping/payment info
  - `Complete(id)` - Order confirmation

#### StoreManagerController
- **Purpose**: Administrative album management
- **Actions**:
  - `Index()` - List all albums
  - `Create()` / `Edit(id)` - CRUD operations
  - `Delete(id)` - Remove albums
- **Authorization**: Requires Administrator role

#### AccountController
- **Purpose**: User authentication
- **Actions**:
  - `Login()` / `LogOff()` - Authentication
  - `Register()` - User registration
  - OAuth integration (Facebook, Google, etc.)

### 4. Business Logic

**ShoppingCart Class**
- Session-based cart management
- Cart operations: Add, Remove, EmptyCart
- Cart calculations: GetTotal(), GetCount()
- Order migration: CreateOrder()

**Sample Data Seeding**
- `SampleData.cs` initializes the database with:
  - Genres (10 music genres)
  - Artists
  - Albums with pricing and artwork

### 5. Views & UI

**View Engine**: Razor (`.cshtml`)

**Layout Structure**:
- `_Layout.cshtml` - Master page template
- `_ViewStart.cshtml` - Default layout specification
- Partial views for reusable components

**Frontend Features**:
- jQuery for DOM manipulation
- jQuery UI for enhanced interactions
- Knockout.js for MVVM binding (cart operations)
- AJAX for cart updates without page refresh
- Client-side validation (jQuery Validation)

**Bundling & Minification**:
- CSS bundling via `BundleConfig`
- JavaScript bundling and minification
- WebGrease for optimization

### 6. Authentication & Authorization

**Authentication Mode**: Forms Authentication

**Membership System**:
- SimpleMembership provider
- Database-backed user authentication
- OAuth integration via DotNetOpenAuth:
  - Google
  - Facebook
  - Microsoft
  - Twitter

**Authorization**:
- Role-based security (Administrator role)
- `[Authorize]` attribute on protected actions
- Default admin credentials in configuration

### 7. Configuration

**Web.config Settings**:

```xml
<connectionStrings>
  <add name="MusicStoreEntities" 
       connectionString="Data Source=(LocalDB)\MSSQLLocalDB;
                         AttachDbFilename=|DataDirectory|\MvcMusicStore.mdf;
                         Integrated Security=True" />
</connectionStrings>

<appSettings>
  <add key="DefaultAdminUsername" value="Administrator"/>
  <add key="DefaultAdminPassword" value="YouShouldChangeThisPassword"/>
  <add key="webpages:Version" value="2.0.0.0"/>
  <add key="ClientValidationEnabled" value="true"/>
  <add key="UnobtrusiveJavaScriptEnabled" value="true"/>
</appSettings>
```

**Routing**:
- Default route: `{controller}/{action}/{id}`
- Conventional routing (not attribute-based)
- Default controller: Home, Default action: Index

### 8. Web API

The application includes ASP.NET Web API 4.0 configuration for potential REST API endpoints, though primary functionality uses traditional MVC actions.

## Key Features

### Customer Features
✅ **Browse Albums** - View albums by genre  
✅ **Search** - Find albums and artists  
✅ **Shopping Cart** - Add/remove items, view cart  
✅ **Checkout** - Process orders with shipping info  
✅ **User Registration** - Create account  
✅ **OAuth Login** - Sign in with external providers  

### Administrative Features
✅ **Album Management** - CRUD operations (admin only)  
✅ **Genre Management** - Categorize albums  
✅ **Artist Management** - Manage artist catalog  

### Technical Features
✅ **AJAX Cart Updates** - Seamless cart operations  
✅ **Session Management** - Persistent shopping cart  
✅ **Data Validation** - Client & server-side validation  
✅ **Responsive UI** - jQuery UI enhancements  
✅ **Database Migrations** - EF Code First migrations  

## Application Flow

### Public User Journey

```
1. Browse Store (Home/Store) 
   └─▶ View Genres
       └─▶ Browse Albums by Genre
           └─▶ View Album Details
               └─▶ Add to Cart
                   └─▶ View Shopping Cart
                       └─▶ Checkout (requires login)
                           └─▶ Complete Order
```

### Administrative Journey

```
1. Login as Administrator
   └─▶ Access Store Manager
       ├─▶ Create New Album
       ├─▶ Edit Existing Album
       ├─▶ Delete Album
       └─▶ View All Albums
```

## Database Schema

```
┌─────────────┐
│   Genres    │
│─────────────│         ┌──────────────┐
│ GenreId (PK)│◀───────│    Albums     │
│ Name        │         │──────────────│
│ Description │         │ AlbumId (PK) │
└─────────────┘         │ Title        │
                        │ Price        │
                        │ GenreId (FK) │
┌─────────────┐         │ ArtistId (FK)│
│   Artists   │         │ AlbumArtUrl  │
│─────────────│         └──────┬───────┘
│ ArtistId(PK)│◀───────────────┘
│ Name        │
└─────────────┘                │
                               │
┌──────────────┐               │
│    Orders    │         ┌─────▼────────────┐
│──────────────│         │  OrderDetails    │
│ OrderId (PK) │◀────────│──────────────────│
│ OrderDate    │         │ OrderDetailId(PK)│
│ Username     │         │ OrderId (FK)     │
│ FirstName    │         │ AlbumId (FK)     │
│ LastName     │         │ Quantity         │
│ Address      │         │ UnitPrice        │
│ Total        │         └──────────────────┘
└──────────────┘

┌──────────────┐
│    Carts     │
│──────────────│         ┌──────────────┐
│ RecordId (PK)│         │    Albums    │
│ CartId       │         │──────────────│
│ AlbumId (FK) │────────▶│ AlbumId (PK) │
│ Count        │         └──────────────┘
│ DateCreated  │
└──────────────┘
```

## Dependencies

### NuGet Packages

| Package | Version | Purpose |
|---------|---------|---------|
| **Microsoft.AspNet.Mvc** | 4.0 | MVC framework |
| **Microsoft.AspNet.WebApi** | 4.0 | REST API support |
| **EntityFramework** | 5.0 | ORM/data access |
| **jQuery** | 1.7.1 | DOM manipulation |
| **jQuery.UI.Combined** | 1.8.20 | UI components |
| **knockoutjs** | 2.1.0 | MVVM binding |
| **DotNetOpenAuth** | 4.0.3 | OAuth authentication |
| **Newtonsoft.Json** | 4.5.6 | JSON serialization |
| **Modernizr** | 2.5.3 | Feature detection |
| **WebGrease** | 1.1.0 | Asset optimization |

## Security Considerations

⚠️ **Current Security Status** (as of .NET Framework 4.8):

- **Forms Authentication** - Session-based, cookies
- **Password Storage** - SimpleMembership with hashing
- **CSRF Protection** - ValidateAntiForgeryToken on POST actions
- **SQL Injection** - Mitigated via Entity Framework parameterization
- **XSS Protection** - Razor automatic encoding

⚠️ **Known Security Concerns**:
- Outdated jQuery version (1.7.1) - Known vulnerabilities
- Newtonsoft.Json version (4.5.6) - Potential vulnerabilities
- DotNetOpenAuth (deprecated)
- Default admin credentials in config

## Deployment Requirements

### Server Requirements
- Windows Server 2012 R2 or later (for .NET Framework 4.8)
- IIS 7.5 or later
- .NET Framework 4.8 Runtime
- SQL Server 2012 or later (for production)

### Azure App Service Requirements
- ⚠️ **Minimum Version**: .NET Framework 4.8.1 (see upgrade guide)
- Windows-based App Service Plan
- SQL Azure database (recommended for production)

### Configuration Changes for Production
1. Update connection strings to point to production database
2. Change default admin credentials
3. Enable SSL/HTTPS
4. Configure production OAuth credentials
5. Disable debug mode in Web.config
6. Configure custom error pages

## Known Limitations

📌 **Legacy Technology Stack**:
- Based on .NET Framework (not cross-platform)
- Entity Framework 5 (older version)
- ASP.NET MVC 4 (older framework)
- Outdated JavaScript libraries

📌 **Scalability**:
- Session-based cart (not suitable for web farms without sticky sessions)
- LocalDB not suitable for production
- File-based database in App_Data

📌 **Modern Features Missing**:
- No REST API for mobile apps
- No responsive design
- No modern SPA framework (React, Angular, Vue)
- No containerization support

## Modernization Opportunities

See `MODERNIZATION_GUIDE.md` for detailed upgrade paths:

### Short-term (Azure Compliance)
- ✅ Upgrade to .NET Framework 4.8.1
- ✅ Update vulnerable packages
- ✅ Deploy to Azure App Service

### Long-term (Full Modernization)
- ✅ Migrate to .NET 8/9/10
- ✅ Upgrade to ASP.NET Core MVC
- ✅ Replace Entity Framework 5 with EF Core
- ✅ Modernize frontend (React/Vue/Blazor)
- ✅ Containerize with Docker
- ✅ Implement REST API
- ✅ Add responsive design
- ✅ Implement modern authentication (ASP.NET Core Identity)

## Getting Started

### Prerequisites
1. Visual Studio 2022 or later
2. .NET Framework 4.8 SDK
3. SQL Server LocalDB (included with Visual Studio)

### Running the Application

1. **Clone/Open the solution**
   ```bash
   git clone <repository-url>
   cd MvcMusicStore
   ```

2. **Restore NuGet packages**
   - Visual Studio will automatically restore packages
   - Or manually: `Tools > NuGet Package Manager > Restore`

3. **Build the solution**
   ```
   Build > Build Solution (Ctrl+Shift+B)
   ```

4. **Run the application**
   ```
   Debug > Start Debugging (F5)
   ```

5. **Database initialization**
   - On first run, Entity Framework will create the database
   - Sample data will be seeded automatically
   - Database file: `App_Data\MvcMusicStore.mdf`

### Default Admin Credentials

⚠️ **Change these immediately in production!**

- **Username**: Administrator
- **Password**: YouShouldChangeThisPassword

### Testing the Application

**Browse as Customer**:
1. Navigate to `/Store`
2. Browse genres and albums
3. Add items to cart
4. Checkout (requires registration/login)

**Manage as Administrator**:
1. Login with admin credentials
2. Navigate to `/StoreManager`
3. Create, edit, or delete albums

## Contributing

When making changes to this application:

1. Follow the existing MVC pattern
2. Use Entity Framework for data access
3. Implement proper validation (client and server)
4. Add XML documentation to controllers
5. Test both authenticated and anonymous scenarios
6. Ensure CSRF protection on state-changing operations

## Support & Documentation

- **ASP.NET MVC 4 Documentation**: https://learn.microsoft.com/aspnet/mvc/
- **Entity Framework 5 Documentation**: https://learn.microsoft.com/ef/ef6/
- **Modernization Guide**: See `MODERNIZATION_GUIDE.md`

---

**Application Version**: 1.0  
**Target Framework**: .NET Framework 4.8  
**Last Updated**: 2024  
**License**: Educational/Sample Application
