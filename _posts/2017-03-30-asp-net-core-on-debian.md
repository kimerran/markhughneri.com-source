---
layout: post
title: ASP.NET Core Changing DB from SQL Server to Postgres (that should run on Linux!)
---

![](http://i.imgur.com/ghXWB7G.png)
> ASP.NET Core app running on Debian with Postgres database

I am currently developing an ASP.NET Core + React.js application. I have two ASP.NET Core Web applications - one runs the Web API and one runs the front-end UI. I am using Entity Framework Core for my SQL Server database.

## I'd like to try running it on a Linux box
Yup you read that right, your ASP.NET now runs on Linux but unfortunately, the requirements to run SQL Server for Linux is just too big.

> You need at least 3.25GB of memory to run SQL Server on Linux. SQL Server Engine has been tested up to 1 TB of memory at this time.

[source](https://docs.microsoft.com/en-us/sql/linux/sql-server-linux-setup-ubuntu)

## So, what's the plan?
What I want to do is run the entire application on a Linux box. Not much problem installing .NET Core since popular distros are supported (though I am not yet successful installing it on my Manjaro machine). For the database I'll cross-out SQL Server and I will be using Postgres. 

## Entity Framework Core
You should be using Entity Framework Core for this to work. I onced use a non-Core version of Entity Framework together with ASP.NET Core project only to find out that I will not be able to run that on a Linux machine.


You need to install this dependency for every projects that uses Entity Framework Core

```
PM>  Install-Package Npgsql.EntityFrameworkCore.PostgreSQL
```

## Few gotchas
1) PostgreSql doesnt have `IDENTITY` property, so you should have the following lines of codes for every `DbSet` that needs an “auto-generated” ID. 

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
  modelBuilder.HasSequence<long>("Students");
  modelBuilder.HasSequence<long>("Subjects");
}
```
<br />

2) The data type for the property in #1 should be long. If not, you will get an error telling you that `SERIAL` should be `long (bigint)`

<br />

3) On `Startup.cs`, instead of `AddEntityFrameworkSqlServer` you should use `AddEntityFrameworkNpgsql`

```csharp
//services.AddEntityFrameworkSqlServer();
services.AddEntityFrameworkNpgsql();
```
<br />

4) On your `DbContext.cs`, instead of `UseSqlServer` you should use `UseNpgsql`

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
  optionsBuilder.UseNpgsql("Host=localhost;Database=dbName;Username=userName;Password=password");
  //optionsBuilder.UseSqlServer(@"Data Source=.\SQLEXPRESS;MultipleActiveResultSets=True;Initial Catalog=dbName;User Id=userName;Password=password;");
}
```

## Migrations just works
You might need to remove existing migrations file and just start with a new one. But, other than those changes above, I didn’t encountered any issues changing from SQL Server to PostgreSQL

```
dotnet ef migrations add postgres-initial-migration
dotnet ef database update
```

**Note:** Of course, you need to setup your credentials and at least create the database that you will connect to. The user that you will create should have READ and WRITE permissions to the database, I create my development database using the commands below.

```sql
CREATE DATABASE dbName;
GRANT ALL PRIVILEGES ON DATABASE dbName to userName;
```


## Hosting with Linode
I have an existing Linode boxes so I just spin off a small Debian box that comes with `1 CPU core` and `1GB RAM` -- SQL Server for Linux will not even run on this :sad: 

Before anything else, make sure you run the update command.

```
sudo apt-get update
```

# Installing .NET Core and Postgres
I will not detail on how I installed .NET Core and Postgres here.

[Install .NET Core on Debian](https://www.microsoft.com/net/core#linuxdebian)

[Install Postgres on Debian](https://wiki.debian.org/PostgreSql)


## Node JS and NPM
Since I have a `React JS` project as well, I would need to install `NPM` and `Node JS` to run `webpack` to compile it. You should be able to install it with just a few commands.

```
curl -sL https://deb.nodesource.com/setup_6.x | sudo -E bash -
sudo apt-get install -y nodejs
```

### Further reading:
[EF Core value generation](http://www.npgsql.org/efcore/value-generation.html)

[EF Core relational sequence](https://docs.microsoft.com/en-us/ef/core/modeling/relational/sequences)

[EF Core Postgres](https://docs.microsoft.com/en-us/ef/core/providers/npgsql/)


That's it! Fin.