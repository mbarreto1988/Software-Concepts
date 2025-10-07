
## Install Dependencies
```c#
Microsoft.EntityFrameworkCore
Microsoft.EntityFrameworkCore.SqlServer
Microsoft.EntityFrameworkCore.Tools
```

## Scaffold without folders 
```python
Scaffold-DbContext "Server={SERVER};Database={DATA_BASE_NAME};User Id={USER};Password={PASSWORD};TrustServerCertificate=True;" Microsoft.EntityFrameworkCore.SqlServer -Context ContextFile
```

## Scaffold with folders 
```python
Scaffold-DbContext "Server={SERVER};Database={DATA_BASE_NAME};User Id={USER};Password={PASSWORD};TrustServerCertificate=True;" Microsoft.EntityFrameworkCore.SqlServer -Context ContextFile -OutputDir Domain/Entities -ContextDir Infrastructure/Context
```

## config  Program.cs
```python
builder.Services.AddDbContext<ContextFile>(options => options.UseSqlServer(builder.Configuration.GetConnectionString("DefaultConnection")));
```

## Scaffold DDD:
```python
APP
│
├─ Domain
│   └─ Entities/
│
├─ Infrastructure
│   ├─ Context/
│   └─ Repositories/
│
├─ Application
│   └─ Services/
│
└─ Interface
     └─ Controllers/
```