---
layout: post
title: Blazor WebAssembly & Web API on .NET 6
date: '2023-10-20 09:56:00-0400'
description: >-
  Learn Blazor WebAssembly and Web API on .NET 6 by building a shopping cart
  application using C#
tags: false
categories: sample-posts
giscus_comments: false
related_posts: false
related_publications: false
published: true
---
Membuat aplikasi Shopping Cart dengan Blazor WebAssembly dan Web API di .NET 6 C#

Dalam artikel ini, kami akan membuat panduan melalui proses langkah demi langkah dalam membuat shopping cart interaktif menggunakan Web API di .NET 6 untuk mengelola data produk, keranjang belanja, dan transaksi.

1. Buat Project, Pilih Blazor WebAssembly App
![27.10.2023_22.28.56_REC.png]({{site.baseurl}}/_posts/27.10.2023_22.28.56_REC.png)

2. Berikan nama project  ShopCart.Web
![27.10.2023_22.39.59_REC.png]({{site.baseurl}}/_posts/27.10.2023_22.39.59_REC.png)

3. Pilih Framework .NET 6
![27.10.2023_22.41.07_REC.png]({{site.baseurl}}/_posts/27.10.2023_22.41.07_REC.png)

4. Add New Project pada Solution, klik kanan ke Solution -> Add -> New Project
![Screenshot 2023-10-27 225341.png]({{site.baseurl}}/_posts/Screenshot 2023-10-27 225341.png)

5. Pilih Project ASP.NET Core Web API
![27.10.2023_22.55.36_REC.png]({{site.baseurl}}/_posts/27.10.2023_22.55.36_REC.png)

6. Buat nama project ShopCart.Api

7. Pilih Framework .Net 6, Authentication Type None
![27.10.2023_23.05.20_REC.png]({{site.baseurl}}/_posts/27.10.2023_23.05.20_REC.png)


8. Buat File berikut pada Shop.Api Project, Folder Entities

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

9. Entity Relationship Diagram
![ERD]({{site.baseurl}}/_posts/27.10.2023_23.26.48_REC.png)





Covered :
- Create the Database using EF Core Code First Database Migrations
- Retrieve Product Data from Database (Web API component)
- Create Classes for Data Transfer Objects (DTOs)
- Create ProductRepository Class (Repository Design Pattern)
- Create ProductController Class
- Create DtoConversion Class (DTO Conversion Extension methods)
