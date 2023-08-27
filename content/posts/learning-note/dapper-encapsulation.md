---
title: "封裝 Dapper : 簡化對資料庫的操作"
date: 2023-05-24
lastmod: 2023-05-25
draft: false
authors: ["nofear195"]
description: ""
categories: ["learning-note"]
tags: ["Dapper","軟體研發"]
lightgallery: true

---

Mastering Dapper Encapsulation : Tips and Techniques for Simplified Database Access.
<!--more-->

## Reference

1. [Dapper Tutorial](https://dapper-tutorial.net/dapper)
2. [Using Dapper In ASP.NET Core Web API](https://www.c-sharpcorner.com/article/using-dapper-in-asp-net-core-web-api/)

## Preface

最近因工作所需，開始接觸了 .Net 的專案，發現 LINQ 與 EF Core 都不是這麼好用，於是開始了 Dapper 的學習之旅，加上為了使 Dapper 方便好用，就將 Dapper 做封裝，方便之後的專案做參考與使用。

## Steps

### 1. 準備要操作的資料庫

1. 下載並啟動資料庫服務
2. 下載 .Net 為了支援各類型資料庫所製作的套件包 (以 PostgresSQL 為例)

    ```Bash
        NuGet\Install-Package Npgsql 
    ```

3. 在 appsettings.json 中，添加資料庫的連線字串 (ConnectionStrings)

    ```json
    "ConnectionStrings": {
        "DefaultConnection": "Server=localhost;Port=5432;Database=postgres;User Id=postgres;Password=;"
    }
    ```

### 2. 下載 Dapper 套件

```Bash
    # using Package Manager 
    NuGet\Install-Package Dapper

    # using CLI
    $ dotnet add package Dapper

    # clone Dapper GitHub repository
    $ git clone https://github.com/StackExchange/Dapper.git
```

- Nuget 資源 : [https://www.nuget.org/packages/Dapper/](https://www.nuget.org/packages/Dapper/)

### 3. 定義 Dapper 使用介面

```cs
using System.Data.Common;
using Dapper;

public interface IDapperORM : IDisposable
    {
        DbConnection GetDbconnection();
        List<T> GetAll<T>(string sql, DynamicParameters? dynamicParameters = null);
        T? Get<T>(string sql, DynamicParameters? dynamicParameters = null);
        int Insert<T>(string sql, DynamicParameters? dynamicParameters = null);
        int Update<T>(string sql, DynamicParameters? dynamicParameters = null);
        int Delete<T>(string sql, DynamicParameters? dynamicParameters = null);
    }
```

### 4. 實作 Dapper 使用介面

1. 於建構子中，獲取連線字串 (ConnectionStrings)
    - .Net 6 之後的服務入口檔案由 Program.cs 取代 Startup.cs ，因此 appsettings.json 會直接註冊到全域中，可以直接提取 json 裡面的內容
    - [Configuration in ASP.NET Core 7.0](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/configuration/?view=aspnetcore-7.0)

    ```cs
    using System.Data;
    using System.Data.Common;
    using Dapper;
    using Npgsql;

    public class DapperORM : IDapperORM
    {
        private readonly IConfiguration _config;
        private string Connectionstring;

        public DapperORM(IConfiguration config)
        {
            _config = config;
            var defaultConnection = _config["ConnectionStrings:DefaultConnection"];
            Connectionstring = defaultConnection == null ? "" : defaultConnection;
        }
        //...
    }

    ```

2. 實作建立連線方法
    - 連線方法因資料庫類型而異

    ```cs
    public DbConnection GetDbconnection()
    {
        return new NpgsqlConnection(Connectionstring);
    }
    ```

3. 實作 GET, GETALL 函式
    - db Query 的方法主要用於從資料庫獲取檔案

    ```cs
    public T? Get<T>(string sql, DynamicParameters? dynamicParameters = null)
    {
        using IDbConnection db = GetDbconnection();
        return db.Query<T>(sql, dynamicParameters).FirstOrDefault();
    }

    public List<T> GetAll<T>(string sql, DynamicParameters? dynamicParameters = null)
    {
        using IDbConnection db = GetDbconnection();
        return db.Query<T>(sql, dynamicParameters).ToList();
    }

    ```

4. 實作 Transaction 機制
    - db Execute 的方法主要用於 INSERT, UPDATE, DELETE 等 sql 指令，並回傳指令影響到的 row 個數

    ```cs
    private int Transaction<T>(string sp, DynamicParameters? dynamicParameters = null)
    {
        int rowsAffected;
        using IDbConnection db = GetDbconnection();
        try
        {
            if (db.State == ConnectionState.Closed)
                db.Open();

            using var tran = db.BeginTransaction();
            try
            {
                rowsAffected = db.Execute(sp, dynamicParameters, transaction: tran);
                tran.Commit();
            }
            catch (Exception ex)
            {
                tran.Rollback();
                Console.WriteLine(ex);
                throw;
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine(ex);
            throw;
        }
        finally
        {
            if (db.State == ConnectionState.Open)
                db.Close();
        }

        return rowsAffected;
    }
    ```

5. 實作 INSERT, UPDATE, DELETE 函式

    ```cs
    public int Insert<T>(string sql, DynamicParameters? dynamicParameters = null)
    {
        return Transaction<T>(sql, dynamicParameters);
    }

    public int Update<T>(string sql, DynamicParameters? dynamicParameters = null)
    {
        return Transaction<T>(sql, dynamicParameters);
    }

    public int Delete<T>(string sql, DynamicParameters? dynamicParameters = null)
    {
        return Transaction<T>(sql, dynamicParameters);
    }

    ```

## Actual Usage

### 定義 Mapping 至資料庫的 Model

```cs
public class Student
{
    public int Id { get; set; }

    public String? Name { get; set; }

    public int Age { get; set; }
}
```

### 製作 API Controller

1. 將 Controller 建構子中，注入 Dapper

    ```cs
    [Route("api/[controller]")]
    public class StudentController : Controller
    {
        private readonly IDapperORM _dapper;
        private readonly string _tableName;

        public StudentController(IDapperORM dapper)
        {
            _dapper = dapper;
            _tableName = "Student";
        }
        //...
    }
    ```

2. 建立 GetAll API
    - 因為 GetAll 比較單純，只需帶入一個參數即可執行

    ```cs
    [HttpGet]
    public async Task<List<Student>> Get()
    {
        string sql = $"Select * from {_tableName}";

        var result = await Task.FromResult(_dapper.GetAll<Student>(sql));
        return result;
    }
    ```

3. 建立 Get API
    - 在 sql 指令中用 `@` 取代要輸入的變數，搭配 DynamicParameters 可防止 SQL injection
    - DynamicParameters Add() 函式可逐一加入實際要放入的變數

    ```cs
    [HttpGet("{id}")]
    public async Task<Student> Get(int id)
    {
        string sql = $@"Select * from {_tableName} WHERE ID=@id";

        DynamicParameters dynamicParameters = new DynamicParameters();
        dynamicParameters.Add("@id", id);

        var result = await Task.FromResult(_dapper.Get<Student>(sql, dynamicParameters));
        return result == null ? new Student() : result;
    }
    ```

4. 建立 Post API
    - dynamicParameters AddDynamicParams() 函式可一次性加入物件裡面所有的 key-value 對

    ```cs
    [HttpPost]
    public async Task<int> Post(Student data)
    {
        string sql = $@"INSERT INTO {_tableName} VALUES (@ID,@Name,@Age)";

        DynamicParameters dynamicParameters = new DynamicParameters();
        dynamicParameters.AddDynamicParams(data);

        var result = await Task.FromResult(_dapper.Insert<int>(sql,dynamicParameters));
        return result;
    }
    ```

5. 建立 Put API

    ```cs
    [HttpPut]
    public async Task<int> Put(Student data)
    {
        string sql = $@"UPDATE {_tableName} SET Name=@Name,Age=@Age WHERE ID=@Id";

        DynamicParameters dynamicParameters = new DynamicParameters();
        dynamicParameters.AddDynamicParams(data);

        var result = await Task.FromResult(_dapper.Update<int>(sql, dynamicParameters));
        return result;
    }
    ```

6. 建立 Delete API

    ```cs
    [HttpDelete("{id}")]
    public async Task<int> Delete(int id)
    {
        string sql = $@"DELETE FROM {_tableName} WHERE ID=@id";

        DynamicParameters dynamicParameters = new DynamicParameters();
        dynamicParameters.Add("@id", id);

        var result = await Task.FromResult(_dapper.Delete<int>(sql, dynamicParameters));
        return result;
    }
    ```

7. 使用 Swagger 查看成果
![dapper-encapsulation-1](images/dapper-encapsulation-1.png)

## Example Code

[dotnet-project-template](https://github.com/nofear195/dotnet-project-template)
