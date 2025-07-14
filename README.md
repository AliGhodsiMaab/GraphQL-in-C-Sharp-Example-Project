# Simple GraphQL API for Courses

This project is a basic implementation of an API for managing courses using ASP.NET Core and Entity Framework Core. Although it is currently implemented as a RESTful API, it is structured to be extended to support GraphQL.

---

## Requirements

To run this project, you will need the following:

- .NET SDK (compatible with the `.sln` file)
- EF Core and other required NuGet packages
- Microsoft SQL Server Management Studio

**Important:**  
> ⚠ Make sure .NET is installed on your system and that the appropriate environment variables are added to your system's `PATH` so that it can be recognized.

> ⚠ Setting up these tools may take some time, but it's a natural and essential part of learning the .NET ecosystem.

---

## Setup & Installation

After installing all requirements, follow these steps to set up the database:

### Step 1: Create Initial Migration

```bash
dotnet ef migrations add InitialCreate
```

You can replace `InitialCreate` with any custom name.

### Step 2: Apply Migration to Create the Database Tables

```bash
dotnet ef database update
```

If you're using **SQL Server LocalDB**, your connection string should look like this:

```
"Server=.;Database=MyDb;Trusted_Connection=True;Encrypt=False;"
```

---

### Step 3: Navigate to the project directory and run:

```bash
dotnet restore
dotnet build
dotnet run
```

---

## IDE Recommendation

You can use any of the following IDEs:

- [JetBrains Rider](https://www.jetbrains.com/rider/)
- [Visual Studio](https://visualstudio.microsoft.com/)

---

## Project Structure

### `Models/Course.cs`

Defines the `Course` class model with all necessary properties such as `Id`, `Name`, `Description`, `Review`, and `DateUpdated`.

### `AppDbContext.cs`

Contains the EF Core DbContext class. It defines a `DbSet<Course>` which represents the Courses table in the database.

### `Repositories/CoursesRepository.cs`

This class handles all data operations related to courses.  
It includes methods for:

- Retrieving all courses (`GetAllCourses`)
- Retrieving a course by ID (`GetCourseById`)
- Adding a new course (`AddCourse`)
- Updating an existing course (`UpdateCourse`)
- Deleting a course (`DeleteCourse`)

### `Controllers/CoursesController.cs`

An API controller that exposes HTTP endpoints for course operations.  
It maps HTTP verbs to the repository methods:

- `GET /api/courses` → Get all courses  
- `GET /api/courses/{id}` → Get a course by ID  
- `POST /api/courses` → Add a new course  
- `PUT /api/courses/{id}` → Update an existing course  
- `DELETE /api/courses/{id}` → Delete a course

---

## GraphQL Integration

This project extends its capabilities by integrating **GraphQL** to allow more flexible querying and data retrieval.

### `GraphQL/Queries/CourseQuery.cs`

Defines the available **GraphQL queries** for the API:

- **`courses`** (returns `List<Course>`):  
  Retrieves all courses from the repository.

- **`course(id: Int!)`** (returns `Course`):  
  Retrieves a specific course by its ID using a non-nullable `IntGraphType`.

This class inherits from `ObjectGraphType` and resolves data via the injected `CoursesRepository`.

### `GraphQL/Types/CourseType.cs`

Defines the **GraphQL object type** for the `Course` model.  
Each field from the `Course` class is explicitly exposed with its type and description:

- `id: ID`
- `name: String`
- `description: String`
- `review: Int`
- `dateAdded: DateTime`
- `dateUpdated: DateTime`

These fields are declared using `.Field()` method from `ObjectGraphType<Course>`.

### `GraphQL/AppSchema.cs`

Defines the **GraphQL schema** for the application.  
It inherits from `Schema` and assigns the root query to an instance of `CourseQuery`:

```csharp
public AppSchema(CourseQuery query, CourseMutation mutation)
        {
            this.Query = query;
            this.Mutation = mutation;
        }
```

This schema is later registered in the `Startup.cs` for use in the GraphQL server.

---

## Program and Startup Configuration

### `Program.cs`

The entry point of the ASP.NET Core application:

```csharp
public static void Main(string[] args)
{
    CreateHostBuilder(args).Build().Run();
}
```

It initializes the host using `UseStartup<Startup>()`.

### `Startup.cs`

#### Constructor:

Reads the SQL connection string from configuration and assigns it to a field.

#### `ConfigureServices(IServiceCollection services)`:

Registers all required services including:

- `DbContext` with SQL Server
- `CoursesRepository` for data access
- `CourseQuery` and `AppSchema` for GraphQL
- GraphQL services using `AddGraphQL().AddSystemTextJson()`
- Swagger for REST API testing and documentation

#### `Configure(IApplicationBuilder app, IWebHostEnvironment env)`:

Configures the HTTP request pipeline:

- Enables developer tools and Swagger (in development)
- Enables HTTPS redirection
- Maps GraphQL endpoint using:

```csharp
app.UseGraphQL<AppSchema>();
app.UseGraphQLGraphiQL("/ui/graphql");
```

- Maps REST controllers via `endpoints.MapControllers()`

---

## API Testing & Final Notes

After completing all the steps under **Setup & Installation**, connecting to the database, and running the project, your browser should automatically open the following URL:

```
https://localhost:7240/swagger/index.html
```

This page provides access to the **Swagger UI**, where you can interact with the available **REST API endpoints**. You can test different methods by clicking **"Try it out"** on each section.

A **200 OK** response in the "Responses" section indicates a successful request. At this point, you can also observe the changes in your database tables using **SQL Server Management Studio (SSMS)**.

---

## GraphQL Playground Access

Since the following line is added in your `Startup.cs` file:

```csharp
app.UseGraphQLGraphiQL("/ui/graphql");
```

You can access the **GraphQL Playground** by navigating to:

```
https://localhost:7240/ui/graphql
```

This provides a user-friendly interface to send **GraphQL queries and mutations** directly to your server.

To learn more about GraphQL syntax and structure, visit:

[https://graphql.org/](https://graphql.org/)

---

## Closing

That wraps up this project and our complete explanation.

We hope this guide and implementation details were helpful and informative for you.

Good luck, and enjoy your coding journey! 
