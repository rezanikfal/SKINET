# E-Commerce App

This [project](https://github.com/TryCatchLearn/Skinet) was generated with [Angular CLI](https://github.com/angular/angular-cli) version 9.1.1 along with the [bootstrap](https://getbootstrap.com/) version 4.5.0 based on the Udemy's course, [Learn to build an e-commerce app with .Net Core and Angular](https://www.udemy.com/course/learn-to-build-an-e-commerce-app-with-net-core-and-angular/) by [Neil Cummings](https://www.udemy.com/user/neil-cummings-2/). 

## Create Solution & Web API
- Create solution file with the folder name: `dotnet new sln`
- Create web api project (i.e. API): `dotnet new webapi -o API`
- Create API project to the solution: `dotnet sln add API`
- Check project under the solution: `dotnet sln list`

## Trusting HTTPS, Add string endponits
- Let the Postman trust https: `file > Setting > SSL certificate verification = OFF`
- Let the Browser trust https: [Microsoft Website](https://docs.microsoft.com/en-us/aspnet/core/security/enforcing-ssl?view=aspnetcore-3.0&branch=pr-en-us-14568&tabs=visual-studio#windows---certificate-not-trusted)

## Setup EF, Product Entity & StoreContext : DbContext
- Get Dot Net Runtime version: `dotnet --info` => `Host (useful for support):  Version: 3.1.6`
- `dotnet add package Microsoft.EntityFrameworkCore --version 3.1.6`
- `dotnet add package Microsoft.EntityFrameworkCore.Design --version 3.1.6`
- SQlite: `dotnet add package Microsoft.EntityFrameworkCore.Sqlite --version 3.1.6`
- SQL Server: `dotnet add package Microsoft.EntityFrameworkCore.SqlServer --version 3.1.6`
- In Memory: `dotnet add package Microsoft.EntityFrameworkCore.InMemory`

## Setup connection string for SQlite/SQL Server - Add DbContext Service to the Startup
```json
"ConnectionStrings": 
{
    "DefaultConnection":"Data Source=skinet.db"
}
"ConnectionStrings":
{
    "CommanderConnection":"Server=11.111.11.111;Initial Catalog=GISapi;User Id=Admin;Password=*****;"
}
```

## Fix Startup -> IConfiguration injection
- VSCODE -> File -> Preferences -> settings -> search `private` -> add `_`
- VSCODE -> File -> Preferences -> settings -> search `this` -> C# Extensions -> Uncheck

## Add Model to create the Tabel Structure 
- Create **Models** folder and add the Entity class (like Feature, Product, ...)
```c#
[Key]
public int Id { get; set; }
[Required]
public string   Name { get; set; }
[Required]
public string State { get; set; }
public DateTime Created { get; set; }
```

## Add DbContext Service to the Startup
- SQL Server: `services.AddDbContext<FeatureContext>(opt => opt.UseSqlServer(Configuration.GetConnectionString("??????")));`
- In Memory (No Connection String): `services.AddDbContext<TodoContext>(opt => opt.UseInMemoryDatabase("TodoList"));`
- SQLite: `services.AddDbContext<ApplicationDbContext>(options => options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));`

## Setup EF tool / Add migration
- Get the SDK verion: `dotnet --info` =>  
    `.NET Core SDKs installed:`   
        `2.2.203 [C:\Program Files\dotnet\sdk]`  
        `3.1.301 [C:\Program Files\dotnet\sdk]`
- Pick the second version number and install (if needed):   
    `dotnet tool install --global dotnet-ef --version 3.1.301` Not worked, I installed with no version
- `dotnet ef migrations add InitialCreate -o Data/Migrations`
- `dotnet ef database update`

## Add Products Controller 
- Create ProductsController and Inject ProductContext to it

## [Scaffold a controller](https://docs.microsoft.com/en-us/aspnet/core/tutorials/first-web-api?view=aspnetcore-3.1&tabs=visual-studio-code#scaffold-a-controller)
```
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

## Make Products/Product Endpoints ASYNC
The return type of the GetProducts and GetProduct methods is ActionResult<T> type. ASP.NET Core automatically serializes the object to JSON and writes the JSON into the body of the response message.

## Add new classlib (2 new projects based on the Architecture)
- `dotnet new classlib -o Core`
- `dotnet new classlib -o Infrastructure`
- `dotnet sln add .\Core\`
- `dotnet sln add .\Infrastructure\`
- `API> dotnet add reference ..\Infrastructure\`
- `Infrastructure> dotnet add reference ..\Core\`
- `skinet> dotnet restore`

## Refactor Data & Context - Exclude folders
- VSCODE -> File -> Preferences -> settings -> search `exclude` -> add pattern -> **/obj, **/bin

## System Architecture

    API (Controller)  →  INFRASTRUCTURE (Repository, DbContext, Services)
             ↓            ↓                  ↓
    CORE (Entity, Interfaces)          Database, stripe

## The Repository Pattern [DbConext --- (Repositort) --- Controller]
- Decouple business code from data access
- Separation of concern
- Minimise duplicate query logic
- Testability

## Add IRepository Interface, Repository ans modify Startup
- Create Repository to inject in the controller

## Inject IProductRepository in the Controller
- Switch `IProductRepository` in the `ProductController` with `StoreContext`  
- Use `ProductRepository` and its methods to retrieve data

## Add DB tables with Relationship
- Create `ProductType` and `ProductBrand` tables. They are FK to `Product` table.

## Drop old Db/Migration and Create a new one 
- `dotnet ef database drop -p Infrastructure -s API`  
- `dotnet ef migrations remove -p Infrastructure -s API` 
- `dotnet ef migrations add InitialCreate -p Infrastructure -s API -o Data/Migrations`
- `-p|--project <PROJECT>                 The project to use.`
- `-s|--startup-project <PROJECT>         The startup project to use.`

## Configuring migrations (Entities)
- Create a Config file to configure every DB entity's specs like  
    `builder.Property(p => p.Name).IsRequired().HasMaxLength(100);`  
    `builder.HasOne(b=>b.ProductBrand).WithMany().HasForeignKey(p=>p.ProductBrandId);`  

## Update Database /Auto Migrate
- Update Directly: `dotnet ef database update -p Infrastructure -s API`
- Drop Directly: `dotnet ef database drop -p Infrastructure -s API`
- Add needed code to `API/Startup` to auto-create DB once you run

## Seed Data
- Add `StoreContextSeed` class
- Add needed code to `API/Startup` to auto-seed DB once you run

## New End Points (Product Brands/Types)
- Add new methods pattern to the repo interface i.e. `Core/Interfaces/IProductRepository.cs`
- Implement the interface i.e. `Infrastructure/Data/ProductRepository.cs`
- Create end points methods in the Controller i.e. `API/Controllers/ProductsController.cs`

## Add product Type/Brand (Id & name) to the Product Endpoint
- include a JSON object inside the main JSON endpoint  
```json
{
    "name": "Core Purple Boots",
    "description": "Pellentesque habitant morbi tristique senectus et netus et malesuada fames ac turpis egestas. Proin pharetra nonummy pede. Mauris et orci.",
    "price": 199.99,
    "pictureUrl": "images/products/boot-core1.png",
    "productType": {
        "name": "Boots",
        "id": 3
    },
    "productTypeId": 3,
    "productBrand": {
        "name": "NetCore",
        "id": 2
    },
    "productBrandId": 2,
    "id": 2
}
```
       
## Add Generic Repository
- Create Generic Interface
- Implement the Generic Interface as the Generic Repo
- Add `AddScoped` to the `Startup.cs`
- Inject the Generic Repo to the Controller & Use it

## Creating a specification
- Add specification Class and specification evaluator
- Create a generic way to add `include` to handel user defined types

## Shaping the data to return with DTOs
- Create a flat object to return to the client with just needed properties
- Map the properties from original entity to Dto `Manually`

## AutoMapper
- `dotnet add package AutoMapper.Extensions.Microsoft.DependencyInjection`
- Add Helper Class (`MappingProfiles.cs`)
- Adjust the Startup (add `AddAutoMapper.cs` service)
- Add `IMapper` to the Controller
- Configure the helper for User defined types by `ForMember` method

## Custom value resolver 
- Add `https://localhost:5001/` to the beginning of Picture Url

## Store static files in *wwwroot* folder
- Add image folder
- Add `UseStaticFiles` middleware

