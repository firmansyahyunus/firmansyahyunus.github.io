---
layout: post
title: 'Blazor WebAssembly & Web API on .NET 6 C# (Part 2)'
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


1. Buat Project baru untuk Model Class Library
Pilih ClassLibrary
beri nama project ShopCart.Models
pilih Framework .NET 6
![ShopCart.Models](</assets/img/Blazor Web Api/28.10.2023_11.51.07_REC.png>)


2. Buat file berikut pada Shop.Models project, folder Dtos
![Alt text](</assets/img/Blazor Web Api/28.10.2023_12.04.03_REC.png>)

`CartItemDto.cs`
```c#
namespace ShopCart.Models.Dtos
{
    public class CartItemDto
    {
        public int Id { get; set; }
        public int ProductId { get; set; }
        public int CartId { get; set; }
        public string ProductName { get; set; }
        public string ProductDescription { get; set; }
        public string ProductImageURL { get; set; }
        public decimal Price { get; set; }
        public decimal TotalPrice { get; set; }
        public int Qty { get; set; }
    }
}
```

`CartItemQtyUpdateDto.cs`
```c#
namespace ShopCart.Models.Dtos
{
    public class CartItemQtyUpdateDto
    {
        public int CartItemId { get; set; }
        public int Qty { get; set; }
    }
}
```

`CartItemToAddDto.cs`
```c#
namespace ShopCart.Models.Dtos
{
    public class CartItemToAddDto
    {
        public int CartId { get; set; }
        public int ProductId { get; set; }
        public int Qty { get; set; }
    }
}
```

`ProductDto.cs`
```c#
namespace ShopCart.Models.Dtos
{
    public class ProductDto
    {
        public int Id { get; set; }
        public string Name { get; set; }
        public string Description { get; set; }
        public string ImageURL { get; set; }
        public decimal Price { get; set; }
        public int Qty { get; set; }
        public int CategoryId { get; set; }
        public string CategoryName { get; set; }
    }
}
```

3. Membuat folder Repository untuk menggunakan repository pattern, buat file di dalam folder tersebut, file interface IProductRepository dan file ProductRepository untuk meng-implementasikan interface .
   
`IProductRepository.cs`
```c#
using ShopCart.Api.Entities;

namespace ShopCart.Api.Repositories.Contracts
{
    public interface IProductRepository
    {
        Task<IEnumerable<Product>> GetItems();
        Task<IEnumerable<ProductCategory>> GetCategories();
        Task<Product> GetItem(int id);
        Task<ProductCategory> GetCategory(int id);    
    }
}
```
`ProductRepository.cs`
```c#
using Microsoft.EntityFrameworkCore;
using ShopCart.Api.Data;
using ShopCart.Api.Entities;
using ShopCart.Api.Repositories.Contracts;

namespace ShopCart.Api.Repositories
{
    public class ProductRepository : IProductRepository
    {
        private readonly ShopCartDbContext context;

        public ProductRepository(ShopCartDbContext context)
        {
            this.context = context;
        }
        public async Task<IEnumerable<ProductCategory>> GetCategories()
        {
            var categories = await this.context.ProductCategories.ToListAsync();
            return categories;
        }

        public Task<ProductCategory> GetCategory(int id)
        {
            throw new NotImplementedException();
        }

        public Task<Product> GetItem(int id)
        {
            throw new NotImplementedException();
        }

        public async Task<IEnumerable<Product>> GetItems()
        {
            var products = await this.context.Products.ToListAsync();

            return products;
        }
    }
}
```
4. Tambahkan kode independency injection berikut pada file Program.cs agar repository bisa digunakan.
`builder.Services.AddScoped<IProductRepository, ProductRepository>();`
![Alt text](</assets/img/Blazor Web Api/29.10.2023_00.41.17_REC.png>)

5. Buat Folder Extensions, dan didalamnya buat file DtoConversions.cs, file ini digunakan untuk konversi hasil data dari database ke Swagger UI.

`DtoConversions.cs`
```c#
using ShopCart.Api.Entities;
using ShopCart.Models.Dtos;

namespace ShopCart.Api.Extensions
{
    public static class DtoConversions
    {
        public static IEnumerable<ProductDto> ConvertToDto(this IEnumerable<Product> products,
                                                                IEnumerable<ProductCategory> productCategories)
        {
            return (from product in products
                    join productCategory in productCategories
                    on product.CategoryId equals productCategory.Id
                    select new ProductDto
                    {
                        Id = product.Id,
                        Name = product.Name,
                        Description = product.Description,
                        ImageURL = product.ImageURL,
                        Price = product.Price,
                        Qty = product.Qty,
                        CategoryId = product.CategoryId,
                        CategoryName = productCategory.Name

                    }).ToList();
        }
    }
}
```

6. Buat file ProductController untuk memanggil repository yang sudah dibuat.
`ProductController.cs`
```c#
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using ShopCart.Api.Extensions;
using ShopCart.Api.Repositories.Contracts;
using ShopCart.Models.Dtos;

namespace ShopCart.Api.Controllers
{
    [Route("api/[controller]")]
    [ApiController]
    public class ProductController : ControllerBase
    {
        private readonly IProductRepository productRepository;

        public ProductController(IProductRepository productRepository)
        {
            this.productRepository = productRepository;
        }

        [HttpGet]
        public async Task<ActionResult<IEnumerable<ProductDto>>> GetItems()
        {
            try
            {
                var products = await this.productRepository.GetItems();
                var productcategories = await this.productRepository.GetCategories();

                if (products == null || productcategories == null)
                {
                    return NotFound();
                }
                else
                {
                    var productDtos = products.ConvertToDto(productcategories);

                    return Ok(productDtos);
                }
            }
            catch (Exception ex)
            {
                return StatusCode(StatusCodes.Status500InternalServerError,
                                "Error retrieving data from database");
            }
        }
    }
}

```  

7. Jalankan Project Solution pilih Menu Debug -> Start Debugging atau dengan shortcut F5 pada keyboard, maka akan tampil halaman Swagger, Product dan Method Get /api/Product, karena sebelumnya sudah dibuat pada Step 6, file ProductController.cs 
![Alt text](</assets/img/Blazor Web Api/29.10.2023_01.14.51_REC.png>)

Coba jalankan method Get /api/Product dengan cara, Pilih Try it out -> Pilih Execute,
maka data product akan tampil <br>

![Alt text](</assets/img/Blazor Web Api/29.10.2023_01.24.41_REC.png>)

Struktur Project Solution <br>
![Alt text](</assets/img/Blazor Web Api/29.10.2023_01.30.56_REC.png>)