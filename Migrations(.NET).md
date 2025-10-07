
## Install Dependencies
```python
Microsoft.EntityFrameworkCore
Microsoft.EntityFrameworkCore.SqlServer
Microsoft.EntityFrameworkCore.Tools
```

## create entities (example)
```c#
namespace codeFirst.Entities
{
    public class User
    {
        public int Id {get; set;}
        public string FirstName {get; set;}
        public string LastName {get; set;}
        public string Pass {get; set;}
    }
}
```

## create context 
```c#
using codeFirst.Entities;
using Microsoft.EntityFrameworkCore;

namespace codeFirst.Context
{
    public class AppDbContext : DbContext
    {
        public AppBdContext(DbContextOptions<AppDbContext> options) : base(options){}
        public DbSet<User> User {get; set;}
    }
}
```


## config  Program.cs
```python
builder.Services.AddDbContext<AppDbContext>(options => options.UseSqlServer("Server=(localdb)\\mssqllocaldb;Database={DATA_BASE_NAME};Trusted_Connection=True;"));

```
---

## execute migrations
```python
dotnet ef migrations add InicialMigration
```
```python
dotnet ef database update
```
---

## if you want to update the tables
```python
# remove last migration
dotnet ef migrations remove
```
```python
dotnet ef migrations add {distinct_migration_name}
```
```python
dotnet ef database update
```