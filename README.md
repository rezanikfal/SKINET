# E-Commerce App

This project was generated with [Angular CLI](https://github.com/angular/angular-cli) version 9.1.1 along with the [bootstrap](https://getbootstrap.com/) version 4.5.0 based on the Udemy's course, [Learn to build an e-commerce app with .Net Core and Angular](https://www.udemy.com/course/learn-to-build-an-e-commerce-app-with-net-core-and-angular/) by [Neil Cummings](https://www.udemy.com/user/neil-cummings-2/). 

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
- `dotnet add package Microsoft.EntityFrameworkCore.Sqlite --version 3.1.6`

## Setup connection string for SQlite

    "ConnectionStrings": {
        "DefaultConnection":"Data Source=skinet.db"
    }

## Fix Startup -> IConfiguration injection

- VSCODE -> File -> Preferences -> settings -> search `private` -> add `_`
- VSCODE -> File -> Preferences -> settings -> search `this` -> C# Extensions -> Uncheck

## Add Connectionstring to the Startup

## Setup EF tool / Add migration

- Get the SDK verion: `dotnet --info` =>  
    `.NET Core SDKs installed:`   
        `2.2.203 [C:\Program Files\dotnet\sdk]`  
        `3.1.301 [C:\Program Files\dotnet\sdk]`
- Pick the second version number and install (if needed):   
    `dotnet tool install --global dotnet-ef --version 3.1.301` Not worked, I installed the latest
- `dotnet ef database update`
- `dotnet ef migrations add InitialCreate -o Data/Migrations`

## Get Products Endpoint

## Get Products/Product Endpoints ASYNC

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

## The Repository Pattern
- Decouple business code from data access
- Separation of concern
- Minimise duplicate query logic
- Testability

## Add IRepository Interface and Repository Class
- Create Repository Interface in Core folder and implement it in Infrastructure folder

## Use IProductRepository in the Controller
- Inject `IProductRepository` in the `ProductController` instead of `StoreContext`  
- Use `ProductRepository` and its methods to retrieve data

## Add DB tables with Relationship
- Create `ProductType` and `ProductBrand` tables. They are FK to `Product` table.

