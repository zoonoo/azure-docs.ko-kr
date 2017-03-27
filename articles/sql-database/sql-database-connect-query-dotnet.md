---
title: ".NET을 사용하여 Azure SQL Database에 연결(C#) | Microsoft Docs"
description: "이 빠른 시작에 포함된 샘플 코드를 사용하여 C#으로 최신 응용 프로그램을 개발하고 Azure SQL 데이터베이스로 클라우드에서 강력한 관계형 데이터베이스를 통해 지원할 수 있습니다."
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 7faca033-24b4-4f64-9301-b4de41e73dfd
ms.service: sql-database
ms.custom: development
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 03/16/2017
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: c54ccef3098502c9fbaad13c5fe35ed15bf93f29
ms.lasthandoff: 03/22/2017


---
# <a name="azure-sql-database-use-net-c-to-connect-and-query-data"></a>Azure SQL Database: .NET(C#)을 사용하여 데이터에 연결 및 쿼리

[C# 및 ADO.NET](https://msdn.microsoft.com/library/kb9s9ks0.aspx)를 사용하여 Azure SQL Database에 연결하고 쿼리합니다. 이 가이드는 C#을 사용하여 Azure SQL Database에 연결하고 쿼리, 삽입, 업데이트 및 삭제 문을 실행하는 방법을 자세히 설명합니다.

이 빠른 시작은 다음과 같은 빠른 시작 중 하나에서 만들어진 리소스를 시작 지점으로 사용합니다.

- [DB 만들기 - 포털](sql-database-get-started-portal.md)
- [DB 만들기 - CLI](sql-database-get-started-cli.md)
- [DB 만들기 - PowerShell](sql-database-get-started-powershell.md) 

시작하기 전에 C#에서 개발 환경을 구성했는지 확인합니다. [무료로 Visual Studio Community 설치](https://www.visualstudio.com/)를 참조하거나 [SQL Server용 ADO.NET 드라이버](https://www.microsoft.com/net/download)를 설치합니다.

## <a name="connect-to-database-and-query-data"></a>데이터베이스 및 쿼리 데이터에 연결

Azure Portal에서 연결 문자열을 가져옵니다. 연결 문자열을 사용하여 Azure SQL Database에 연결합니다.

1. [Azure 포털](https://portal.azure.com/)에 로그인합니다.
2. 왼쪽 메뉴에서 **SQL Database**를 선택하고 **SQL Database** 페이지에서 데이터베이스를 클릭합니다. 
3. 데이터베이스의 **Essentials** 창에서 **데이터베이스 연결 문자열 표시**를 찾아 클릭합니다.
4. **ADO.NET** 연결 문자열을 복사합니다.

    <img src="./media/sql-database-connect-query-dotnet/connection-strings.png" alt="connection strings" style="width: 780px;" />

5. Visual Studio를 열고 콘솔 응용 프로그램을 만듭니다.
6. ```using System.Data.SqlClient```를 코드 파일([System.Data.SqlClient 네임스페이스](https://msdn.microsoft.com/library/system.data.sqlclient.aspx))에 추가합니다. 

7. [SELECT](https://msdn.microsoft.com/library/ms189499.aspx) Transact-SQL 문과 [SqlCommand.ExecuteReader](https://msdn.microsoft.com/library/system.data.sqlclient.sqlcommand.executereader.aspx)를 사용하여 Azure SQL Database에서 데이터를 쿼리합니다.

    ```csharp
    string strConn = "<connection string>";
    using (var connection = new SqlConnection(strConn))
    {
   connection.Open();

    SqlCommand selectCommand = new SqlCommand("", connection);
    selectCommand.CommandType = CommandType.Text;

    selectCommand.CommandText = @"SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
        FROM [SalesLT].[ProductCategory] pc
        JOIN [SalesLT].[Product] p
        ON pc.productcategoryid = p.productcategoryid";

    SqlDataReader reader = selectCommand.ExecuteReader();

    while (reader.Read())
    {
        // show data
        Console.WriteLine($"{reader.GetString(0)}\t{reader.GetString(1)}");
    }
    reader.Close();
    }
    ```

## <a name="insert-data"></a>데이터 삽입

[INSERT](https://msdn.microsoft.com/library/ms174335.aspx) Transact-SQL 문과 [SqlCommand.ExecuteNonQuery](https://msdn.microsoft.com/library/system.data.sqlclient.sqlcommand.executenonquery.aspx)를 사용하여 Azure SQL Database에서 데이터를 삽입합니다.

```csharp
SqlCommand insertCommand = new SqlCommand("", connection);
insertCommand.CommandType = CommandType.Text;
insertCommand.CommandText = @"INSERT INTO[SalesLT].[Product]
            ( [Name]
            , [ProductNumber]
            , [Color]
            , [StandardCost]
            , [ListPrice]
            , [SellStartDate]
            )
VALUES
(
            @Name,
            @ProductNumber,
            @Color,
            @StandardCost,
            @ListPrice,
            @SellStartDate)";

            insertCommand.Parameters.AddWithValue("@Name", "BrandNewProduct");
            insertCommand.Parameters.AddWithValue("@ProductNumber", "200989");
            insertCommand.Parameters.AddWithValue("@Color", "Blue");
            insertCommand.Parameters.AddWithValue("@StandardCost", 75);
            insertCommand.Parameters.AddWithValue("@ListPrice", 80);
            insertCommand.Parameters.AddWithValue("@SellStartDate", "7/1/2016");

int newrows = insertCommand.ExecuteNonQuery();
Console.WriteLine($"Inserted {newrows.ToString()} row(s).");
```

## <a name="update-data"></a>데이터 업데이트

[UPDATE](https://msdn.microsoft.com/library/ms177523.aspx) Transact-SQL 문과 [SqlCommand.ExecuteNonQuery](https://msdn.microsoft.com/library/system.data.sqlclient.sqlcommand.executenonquery.aspx)를 사용하여 Azure SQL Database에서 데이터를 업데이트합니다.

```csharp
SqlCommand updateCommand = new SqlCommand("", connection);
updateCommand.CommandType = CommandType.Text;
updateCommand.CommandText = @"UPDATE SalesLT.Product SET ListPrice = @ListPrice WHERE Name = @Name";
updateCommand.Parameters.AddWithValue("@Name", "BrandNewProduct");
updateCommand.Parameters.AddWithValue("@ListPrice", 500);

int updatedrows = updateCommand.ExecuteNonQuery();
Console.WriteLine($"Updated {updatedrows.ToString()} row(s).");
```

## <a name="delete-data"></a>데이터 삭제

[DELETE](https://msdn.microsoft.com/library/ms189835.aspx) Transact-SQL 문과 [SqlCommand.ExecuteNonQuery](https://msdn.microsoft.com/library/system.data.sqlclient.sqlcommand.executenonquery.aspx)를 사용하여 Azure SQL Database에서 데이터를 삭제합니다.

```csharp
SqlCommand deleteCommand = new SqlCommand("", connection);
deleteCommand.CommandType = CommandType.Text;
deleteCommand.CommandText = @"DELETE FROM SalesLT.Product WHERE Name = @Name";
deleteCommand.Parameters.AddWithValue("@Name", "BrandNewProduct");

int deletedrows = deleteCommand.ExecuteNonQuery();
Console.WriteLine($"Deleted {deletedrows.ToString()} row(s).");
```

## <a name="complete-script"></a>전체 스크립트

다음 스크립트는 단일 코드 블록에 있는 이전 단계 모두를 포함합니다.

```csharp
using System;
using System.Data;
using System.Data.SqlClient;

namespace ConsoleApplication1
{
    class Program
    {
        static void Main(string[] args)
        {

            string strConn = "<connection string>";

            using (var connection = new SqlConnection(strConn))
            {
                connection.Open();

                Console.WriteLine("Query data example:");
                Console.WriteLine("\n=========================================\n");

                SqlCommand selectCommand = new SqlCommand("", connection);
                selectCommand.CommandType = CommandType.Text;

                selectCommand.CommandText = @"SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
                   FROM [SalesLT].[ProductCategory] pc
                   JOIN [SalesLT].[Product] p
                   ON pc.productcategoryid = p.productcategoryid";

                SqlDataReader reader = selectCommand.ExecuteReader();

                while (reader.Read())
                {
                    // show data columns
                    Console.WriteLine($"{reader.GetString(0)}\t{reader.GetString(1)}");
                }
                reader.Close();
                Console.WriteLine("\nPress any key to continue ...");
                Console.ReadLine();

                Console.WriteLine("\nInsert data example:");
                Console.WriteLine("=========================================\n");
                SqlCommand insertCommand = new SqlCommand("", connection);
                insertCommand.CommandType = CommandType.Text;
                insertCommand.CommandText = @"INSERT INTO[SalesLT].[Product]
                          ( [Name]
                          , [ProductNumber]
                          , [Color]
                          , [StandardCost]
                          , [ListPrice]
                          , [SellStartDate]
                          )
                VALUES
                (
                            @Name,
                            @ProductNumber,
                            @Color,
                            @StandardCost,
                            @ListPrice,
                            @SellStartDate)";

                insertCommand.Parameters.AddWithValue("@Name", "BrandNewProduct");
                insertCommand.Parameters.AddWithValue("@ProductNumber", "200989");
                insertCommand.Parameters.AddWithValue("@Color", "Blue");
                insertCommand.Parameters.AddWithValue("@StandardCost", 75);
                insertCommand.Parameters.AddWithValue("@ListPrice", 80);
                insertCommand.Parameters.AddWithValue("@SellStartDate", "7/1/2016");

                int newrows = insertCommand.ExecuteNonQuery();
                Console.WriteLine($"Inserted {newrows.ToString()} row(s).");
                Console.WriteLine("\nPress any key to continue ...");
                Console.ReadLine();

                Console.WriteLine("\nUpdate data example:");
                Console.WriteLine("======================\n");
                SqlCommand updateCommand = new SqlCommand("", connection);
                updateCommand.CommandType = CommandType.Text;
                updateCommand.CommandText = @"UPDATE SalesLT.Product SET ListPrice = @ListPrice WHERE Name = @Name";
                updateCommand.Parameters.AddWithValue("@Name", "BrandNewProduct");
                updateCommand.Parameters.AddWithValue("@ListPrice", 500);

                int updatedrows = updateCommand.ExecuteNonQuery();
                Console.WriteLine($"Updated {updatedrows.ToString()} row(s).");
                Console.WriteLine("\nPress any key to continue ...");
                Console.ReadLine();

                Console.WriteLine("\nDelete data example:");
                Console.WriteLine("======================\n");
                SqlCommand deleteCommand = new SqlCommand("", connection);
                deleteCommand.CommandType = CommandType.Text;
                deleteCommand.CommandText = @"DELETE FROM SalesLT.Product WHERE Name = @Name";
                deleteCommand.Parameters.AddWithValue("@Name", "BrandNewProduct");

                int deletedrows = deleteCommand.ExecuteNonQuery();
                Console.WriteLine($"Deleted {deletedrows.ToString()} row(s).");
                Console.WriteLine("\nPress any key to continue ...");
                Console.ReadLine();
            }
        }
    }
}
```

## <a name="next-steps"></a>다음 단계

- .NET 설명서는 [.NET 설명서](https://docs.microsoft.com/dotnet/)를 참조하세요.
- Visual Studio Code를 사용하여 데이터를 쿼리 및 편집하는 방법에 대한 정보는 [Visual Studio Code](https://code.visualstudio.com/docs)를 참조하세요.

