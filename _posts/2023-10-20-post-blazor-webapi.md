---
layout: post
title: 'Blazor WebAssembly & Web API on .NET 6 C# (Part 1)'
date: '2023-10-20 09:56:00-0400'
description: >-
  Learn Blazor WebAssembly and Web API on .NET 6 by building a shopping cart
  application using C#
tags: false
categories: blazor-posts
giscus_comments: false
related_posts: false
related_publications: false
published: true
toc:
  sidebar: left
---
Membuat aplikasi Shopping Cart dengan Blazor WebAssembly dan Web API di .NET 6 C#

Dalam artikel ini, kami akan membuat panduan melalui proses langkah demi langkah dalam membuat shopping cart interaktif menggunakan Web API di .NET 6 untuk mengelola data produk, keranjang belanja, dan transaksi.


## Buat Project Blazor WebAssembly App
{:data-toc-text="Buat Project Blazor WebAssembly App"}

<div class="row mt-3">
   {% include figure.html path="assets/img/Blazor Web Api/27.10.2023_22.28.56_REC.png" class="img-fluid rounded z-depth-1" zoomable=true %}
</div>

### Buat nama project ShopCart.Web
<div class="row mt-3">
   {% include figure.html path="assets/img/Blazor Web Api/27.10.2023_22.39.59_REC.png" class="img-fluid rounded z-depth-1" zoomable=true %}
</div>

### Pilih Framework .NET 6
<div class="row mt-3">
   {% include figure.html path="assets/img/Blazor Web Api/27.10.2023_22.41.07_REC.png" class="img-fluid rounded z-depth-1" zoomable=true %}
</div>

## Buat Project baru pada Solution yang sama
{:data-toc-text="Buat Project ASP.NET Core Web API"}
klik kanan ke Solution -> Add -> New Project
<div class="row mt-3">
        {% include figure.html path="assets/img/Blazor Web Api/Screenshot 2023-10-27 225341.png" class="img-fluid rounded z-depth-1" zoomable=true %}
</div>

### Pilih Project ASP.NET Core Web API
<div class="row mt-3">
        {% include figure.html path="assets/img/Blazor Web Api/27.10.2023_22.55.36_REC.png" class="img-fluid rounded z-depth-1" zoomable=true %}
</div>

### Buat nama project ShopCart.Api
Pilih Framework .Net 6, Authentication Type None
<div class="row mt-3">
        {% include figure.html path="assets/img/Blazor Web Api/27.10.2023_23.05.20_REC.png" class="img-fluid rounded z-depth-1" zoomable=true %}
</div>

## Buat class model entities
Buat class berikut pada Shop.Api Project, Folder Entities

`Cart.cs`
```c#
namespace ShopCart.Api.Entities
{
    public class User
    {
        public int Id { get; set; }
        public string UserName { get; set; }
    }
}
```

`CartItem.cs`
```c#
namespace ShopCart.Api.Entities
{
    public class CartItem
    {
        public int Id { get; set; }
        public int CartId { get; set; }
        public int ProductId { get; set; }
        public int Qty { get; set; }
    }
}
```

`Product.cs`
```c#
namespace ShopCart.Api.Entities
{
    public class Product
    {
        public int Id { get; set; }
        public string Name { get; set; }
        public string Description { get; set; }
        public string ImageURL { get; set; }
        public decimal Price { get; set; }
        public int Qty { get; set; }
        public int CategoryId { get; set; }
    }
}
```

`ProductCategory.cs`
```c#
namespace ShopCart.Api.Entities
{
     public class ProductCategory
    {
        public int Id { get; set; }
        public string Name { get; set; }
    }
}
```


`User.cs`
```c#
namespace ShopCart.Api.Entities
{
    public class User
    {
        public int Id { get; set; }
        public string UserName { get; set; }
    }
}

```

## ERD
Entity Relationship Diagram
<div class="row mt-3">
        {% include figure.html path="assets/img/Blazor Web Api/27.10.2023_23.26.48_REC.png" class="img-fluid rounded z-depth-1" zoomable=true %}
</div>


## Tambah library nuget 
Tambah dependency library melalui Nuget Package pada project ShoptCart.Api, install versi 6
`Microsoft.EntityFrameworkCore.SqlServer`
`Microsoft.EntityFrameworkCore.Tools`

## Tambah connectionString
Tambahkan ConnectionString untuk koneksi ke Database SQL pada file appsettings.json
`appsettings.json`
```yml
{
  "ConnectionStrings": {
    "ShopCartConnection": "Server=MYLOCAL;Database=ShopCart;Trusted_Connection=True"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft.AspNetCore": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

## Tambah datacontext
Buat file datacontext pada project Api dan folder Data
`ShopCartDbContext.cs`
```c#
using Microsoft.EntityFrameworkCore;
using ShopCart.Api.Entities;

namespace ShopCart.Api.Data
{
    public class ShopCartDbContext : DbContext
    {
        public ShopCartDbContext(DbContextOptions<ShopCartDbContext> options) : base(options)
        {

        }

        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            base.OnModelCreating(modelBuilder);

            //Products
            //Beauty Category
            modelBuilder.Entity<Product>().HasData(new Product
            {
                Id = 1,
                Name = "Glossier - Beauty Kit",
                Description = "A kit provided by Glossier, containing skin care, hair care and makeup products",
                ImageURL = "/Images/Beauty/Beauty1.png",
                Price = 100,
                Qty = 100,
                CategoryId = 1

            });
            modelBuilder.Entity<Product>().HasData(new Product
            {
                Id = 2,
                Name = "Curology - Skin Care Kit",
                Description = "A kit provided by Curology, containing skin care products",
                ImageURL = "/Images/Beauty/Beauty2.png",
                Price = 50,
                Qty = 45,
                CategoryId = 1

            });
            modelBuilder.Entity<Product>().HasData(new Product
            {
                Id = 3,
                Name = "Cocooil - Organic Coconut Oil",
                Description = "A kit provided by Curology, containing skin care products",
                ImageURL = "/Images/Beauty/Beauty3.png",
                Price = 20,
                Qty = 30,
                CategoryId = 1

            });
            modelBuilder.Entity<Product>().HasData(new Product
            {
                Id = 4,
                Name = "Schwarzkopf - Hair Care and Skin Care Kit",
                Description = "A kit provided by Schwarzkopf, containing skin care and hair care products",
                ImageURL = "/Images/Beauty/Beauty4.png",
                Price = 50,
                Qty = 60,
                CategoryId = 1

            });
            modelBuilder.Entity<Product>().HasData(new Product
            {
                Id = 5,
                Name = "Skin Care Kit",
                Description = "Skin Care Kit, containing skin care and hair care products",
                ImageURL = "/Images/Beauty/Beauty5.png",
                Price = 30,
                Qty = 85,
                CategoryId = 1

            });
            //Electronics Category
            modelBuilder.Entity<Product>().HasData(new Product
            {
                Id = 6,
                Name = "Air Pods",
                Description = "Air Pods - in-ear wireless headphones",
                ImageURL = "/Images/Electronic/Electronics1.png",
                Price = 100,
                Qty = 120,
                CategoryId = 3

            });
            modelBuilder.Entity<Product>().HasData(new Product
            {
                Id = 7,
                Name = "On-ear Golden Headphones",
                Description = "On-ear Golden Headphones - these headphones are not wireless",
                ImageURL = "/Images/Electronic/Electronics2.png",
                Price = 40,
                Qty = 200,
                CategoryId = 3

            });
            modelBuilder.Entity<Product>().HasData(new Product
            {
                Id = 8,
                Name = "On-ear Black Headphones",
                Description = "On-ear Black Headphones - these headphones are not wireless",
                ImageURL = "/Images/Electronic/Electronics3.png",
                Price = 40,
                Qty = 300,
                CategoryId = 3

            });
            modelBuilder.Entity<Product>().HasData(new Product
            {
                Id = 9,
                Name = "Sennheiser Digital Camera with Tripod",
                Description = "Sennheiser Digital Camera - High quality digital camera provided by Sennheiser - includes tripod",
                ImageURL = "/Images/Electronic/Electronic4.png",
                Price = 600,
                Qty = 20,
                CategoryId = 3

            });
            modelBuilder.Entity<Product>().HasData(new Product
            {
                Id = 10,
                Name = "Canon Digital Camera",
                Description = "Canon Digital Camera - High quality digital camera provided by Canon",
                ImageURL = "/Images/Electronic/Electronic5.png",
                Price = 500,
                Qty = 15,
                CategoryId = 3

            });
            modelBuilder.Entity<Product>().HasData(new Product
            {
                Id = 11,
                Name = "Nintendo Gameboy",
                Description = "Gameboy - Provided by Nintendo",
                ImageURL = "/Images/Electronic/technology6.png",
                Price = 100,
                Qty = 60,
                CategoryId = 3
            });
            //Furniture Category
            modelBuilder.Entity<Product>().HasData(new Product
            {
                Id = 12,
                Name = "Black Leather Office Chair",
                Description = "Very comfortable black leather office chair",
                ImageURL = "/Images/Furniture/Furniture1.png",
                Price = 50,
                Qty = 212,
                CategoryId = 2
            });

            modelBuilder.Entity<Product>().HasData(new Product
            {
                Id = 13,
                Name = "Pink Leather Office Chair",
                Description = "Very comfortable pink leather office chair",
                ImageURL = "/Images/Furniture/Furniture2.png",
                Price = 50,
                Qty = 112,
                CategoryId = 2
            });
            modelBuilder.Entity<Product>().HasData(new Product
            {
                Id = 14,
                Name = "Lounge Chair",
                Description = "Very comfortable lounge chair",
                ImageURL = "/Images/Furniture/Furniture3.png",
                Price = 70,
                Qty = 90,
                CategoryId = 2
            });
            modelBuilder.Entity<Product>().HasData(new Product
            {
                Id = 15,
                Name = "Silver Lounge Chair",
                Description = "Very comfortable Silver lounge chair",
                ImageURL = "/Images/Furniture/Furniture4.png",
                Price = 120,
                Qty = 95,
                CategoryId = 2
            });
            modelBuilder.Entity<Product>().HasData(new Product
            {
                Id = 16,
                Name = "Porcelain Table Lamp",
                Description = "White and blue Porcelain Table Lamp",
                ImageURL = "/Images/Furniture/Furniture6.png",
                Price = 15,
                Qty = 100,
                CategoryId = 2
            });
            modelBuilder.Entity<Product>().HasData(new Product
            {
                Id = 17,
                Name = "Office Table Lamp",
                Description = "Office Table Lamp",
                ImageURL = "/Images/Furniture/Furniture7.png",
                Price = 20,
                Qty = 73,
                CategoryId = 2
            });
            //Shoes Category
            modelBuilder.Entity<Product>().HasData(new Product
            {
                Id = 18,
                Name = "Puma Sneakers",
                Description = "Comfortable Puma Sneakers in most sizes",
                ImageURL = "/Images/Shoes/Shoes1.png",
                Price = 100,
                Qty = 50,
                CategoryId = 4
            });
            modelBuilder.Entity<Product>().HasData(new Product
            {
                Id = 19,
                Name = "Colorful Trainers",
                Description = "Colorful trainsers - available in most sizes",
                ImageURL = "/Images/Shoes/Shoes2.png",
                Price = 150,
                Qty = 60,
                CategoryId = 4
            });
            modelBuilder.Entity<Product>().HasData(new Product
            {
                Id = 20,
                Name = "Blue Nike Trainers",
                Description = "Blue Nike Trainers - available in most sizes",
                ImageURL = "/Images/Shoes/Shoes3.png",
                Price = 200,
                Qty = 70,
                CategoryId = 4
            });
            modelBuilder.Entity<Product>().HasData(new Product
            {
                Id = 21,
                Name = "Colorful Hummel Trainers",
                Description = "Colorful Hummel Trainers - available in most sizes",
                ImageURL = "/Images/Shoes/Shoes4.png",
                Price = 120,
                Qty = 120,
                CategoryId = 4
            });
            modelBuilder.Entity<Product>().HasData(new Product
            {
                Id = 22,
                Name = "Red Nike Trainers",
                Description = "Red Nike Trainers - available in most sizes",
                ImageURL = "/Images/Shoes/Shoes5.png",
                Price = 200,
                Qty = 100,
                CategoryId = 4
            });
            modelBuilder.Entity<Product>().HasData(new Product
            {
                Id = 23,
                Name = "Birkenstock Sandles",
                Description = "Birkenstock Sandles - available in most sizes",
                ImageURL = "/Images/Shoes/Shoes6.png",
                Price = 50,
                Qty = 150,
                CategoryId = 4
            });

            //Add users
            modelBuilder.Entity<User>().HasData(new User
            {
                Id = 1,
                UserName = "Bob"

            });
            modelBuilder.Entity<User>().HasData(new User
            {
                Id = 2,
                UserName = "Sarah"

            });

            //Create Shopping Cart for Users
            modelBuilder.Entity<Cart>().HasData(new Cart
            {
                Id = 1,
                UserId = 1

            });
            modelBuilder.Entity<Cart>().HasData(new Cart
            {
                Id = 2,
                UserId = 2

            });
            //Add Product Categories
            modelBuilder.Entity<ProductCategory>().HasData(new ProductCategory
            {
                Id = 1,
                Name = "Beauty"
            });
            modelBuilder.Entity<ProductCategory>().HasData(new ProductCategory
            {
                Id = 2,
                Name = "Furniture"
            });
            modelBuilder.Entity<ProductCategory>().HasData(new ProductCategory
            {
                Id = 3,
                Name = "Electronics"
            });
            modelBuilder.Entity<ProductCategory>().HasData(new ProductCategory
            {
                Id = 4,
                Name = "Shoes"
            });
        }
    
        public DbSet<Cart> Carts { get; set; }
        public DbSet<CartItem> CartItems { get; set; }
        public DbSet<Product> Products { get; set; }
        public DbSet<ProductCategory> ProductCategories { get; set; }
        public DbSet<User> Users { get; set; }
    }
}

```

## Tambah service SQL Server
Tambahan code pada Program.cs service koneksi ke SQLServer
`Program.cs`
```c#
using Microsoft.EntityFrameworkCore;
using ShopCart.Api.Data;

var builder = WebApplication.CreateBuilder(args);

// Add services to the container.

builder.Services.AddControllers();
// Learn more about configuring Swagger/OpenAPI at https://aka.ms/aspnetcore/swashbuckle
builder.Services.AddEndpointsApiExplorer();
builder.Services.AddSwaggerGen();

builder.Services.AddDbContext<ShopCartDbContext>(options =>
    options.UseSqlServer(builder.Configuration.GetConnectionString("ShopCartConnection"))
    );

var app = builder.Build();

// Configure the HTTP request pipeline.
if (app.Environment.IsDevelopment())
{
    app.UseSwagger();
    app.UseSwaggerUI();
}

app.UseHttpsRedirection();

app.UseAuthorization();

app.MapControllers();

app.Run();

```

## Menggunakan migration EF Core
Untuk generate Migration EF Core, menggunakan Package Manager Console, di Menu Tools - Nuget Package Manager
Ketik code berikut untuk menjalankan migrasi EF Core ke database dan pilihdefault Project ke ShopCart.Api

`Add-Migration InitialCreate`
`Update-Database`

<div class="row mt-3">
    {% include figure.html path="assets/img/Blazor Web Api/28.10.2023_00.12.15_REC.png" class="img-fluid rounded z-depth-1" zoomable=true %}
</div>

## Hasil migrasi pada database SQL ShopCart

<div class="row mt-3">
    {% include figure.html path="assets/img/Blazor Web Api/28.10.2023_11.39.51_REC.png" class="img-fluid rounded z-depth-1" zoomable=true %}
</div>    

Progres sampai saat ini <br>
Table Users
<div class="row mt-3">
    {% include figure.html path="assets/img/Blazor Web Api/28.10.2023_23.51.31_REC.png" class="img-fluid rounded z-depth-1" zoomable=true %}
</div>   

Table Products
<div class="row mt-3">
    {% include figure.html path="assets/img/Blazor Web Api/28.10.2023_23.55.49_REC.png" class="img-fluid rounded z-depth-1" zoomable=true %}
</div>   

Table ProductCategories
<div class="row mt-3">
    {% include figure.html path="assets/img/Blazor Web Api/28.10.2023_23.52.25_REC.png" class="img-fluid rounded z-depth-1" zoomable=true %}
</div>   

Table Cart
<div class="row mt-3">
    {% include figure.html path="assets/img/Blazor Web Api/28.10.2023_23.52.58_REC.png" class="img-fluid rounded z-depth-1" zoomable=true %}
</div>   

Struktur Project Solution <br>
<div class="row mt-3">
    {% include figure.html path="assets/img/Blazor Web Api/29.10.2023_00.01.00_REC.png" class="img-fluid rounded z-depth-1" zoomable=true %}
</div>   