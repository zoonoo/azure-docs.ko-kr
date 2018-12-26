---
title: Visual Studio와 .NET 및 C#을 사용하여 Azure SQL Database 쿼리 | Microsoft Docs
description: Visual Studio를 사용하여 Azure SQL Database에 연결하고 Transact-SQL 문을 사용하여 쿼리하는 C# 앱을 만듭니다.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: dotnet
ms.topic: quickstart
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 12/11/2018
ms.openlocfilehash: 04a0abd0fba7ec53aebeb481ac80d36653d118b6
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/14/2018
ms.locfileid: "53384941"
---
# <a name="quickstart-use-net-and-c-in-visual-studio-to-connect-to-and-query-an-azure-sql-database"></a>빠른 시작: Visual Studio에서 .NET과 C#을 사용하여 Azure SQL 데이터베이스 연결 및 쿼리

이 빠른 시작에서는 Visual Studio에서 [.NET Framework](https://www.microsoft.com/net/) 및 C# 코드를 사용하여 Transact-SQL 문으로 Azure SQL 데이터베이스를 쿼리하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 조건

이 빠른 시작을 완료하려면 다음이 필요합니다.

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]
  
- 사용 중인 컴퓨터의 공용 IP 주소를 허용하는 [서버 수준 방화벽 규칙](sql-database-get-started-portal-firewall.md)
  
- [Visual Studio 2017](https://www.visualstudio.com/downloads/) Community, Professional 또는 Enterprise 버전

## <a name="get-sql-server-connection-information"></a>SQL Server 연결 정보 가져오기

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

## <a name="create-code-to-query-the-sql-database"></a>SQL 데이터베이스를 쿼리할 코드 만들기

1. Visual Studio에서 **파일** > **새로 만들기** > **프로젝트**를 선택합니다. 
   
1. **새 프로젝트** 대화 상자에서 **Visual C#** 을 선택한 다음, **콘솔 앱(.NET Framework)** 을 선택합니다.
   
1. 프로젝트 이름으로 *sqltest*를 입력한 다음, **확인**을 선택합니다. 새 프로젝트가 만들어집니다. 
   
1. **프로젝트** > **NuGet 패키지 관리**를 선택합니다. 
   
1. **NuGet 패키지 관리자**에서 **찾아보기** 탭을 선택한 다음, **System.Data.SqlClient**를 검색하여 선택합니다.
   
1. **System.Data.SqlClient** 페이지에서 **설치**를 선택합니다. 
   - 메시지가 표시되면 **확인**을 선택하여 설치를 계속 진행합니다. 
   - **라이선스 승인** 창이 표시되면 **동의**를 선택합니다.
   
1. 설치가 완료되면 **NuGet 패키지 관리자**를 닫을 수 있습니다. 
   
1. 코드 편집기에서 **Program.cs** 내용을 다음 코드로 바꿉니다. `<server>`, `<username>`, `<password>` 및 `<database>`의 값을 대체합니다.
   
   >[!IMPORTANT]
   >이 예제의 코드에서는 데이터베이스를 만들 때 원본으로 선택할 수 있는 샘플 AdventureWorksLT 데이터를 사용합니다. 사용자 데이터베이스에 다른 데이터가 있는 경우 SELECT 쿼리에 해당 데이터베이스의 테이블을 사용합니다. 
   
   ```csharp
   using System;
   using System.Data.SqlClient;
   using System.Text;
   
   namespace sqltest
   {
       class Program
       {
           static void Main(string[] args)
           {
               try 
               { 
                   SqlConnectionStringBuilder builder = new SqlConnectionStringBuilder();
                   builder.DataSource = "<server>.database.windows.net"; 
                   builder.UserID = "<username>";            
                   builder.Password = "<password>";     
                   builder.InitialCatalog = "<database>";
   
                   using (SqlConnection connection = new SqlConnection(builder.ConnectionString))
                   {
                       Console.WriteLine("\nQuery data example:");
                       Console.WriteLine("=========================================\n");
                       
                       connection.Open();       
                       StringBuilder sb = new StringBuilder();
                       sb.Append("SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName ");
                       sb.Append("FROM [SalesLT].[ProductCategory] pc ");
                       sb.Append("JOIN [SalesLT].[Product] p ");
                       sb.Append("ON pc.productcategoryid = p.productcategoryid;");
                       String sql = sb.ToString();
   
                       using (SqlCommand command = new SqlCommand(sql, connection))
                       {
                           using (SqlDataReader reader = command.ExecuteReader())
                           {
                               while (reader.Read())
                               {
                                   Console.WriteLine("{0} {1}", reader.GetString(0), reader.GetString(1));
                               }
                           }
                       }                    
                   }
               }
               catch (SqlException e)
               {
                   Console.WriteLine(e.ToString());
               }
               Console.ReadLine();
           }
       }
   }
   ```

## <a name="run-the-code"></a>코드 실행

1. 앱을 실행하려면**디버그** > **디버그 시작**을 선택하거나 도구 모음에서 **시작**을 선택하거나 **F5** 키를 누릅니다.
1. 상위 20개의 Category/Product 행이 데이터베이스에서 반환되는지 확인하고 앱 창을 닫습니다.

## <a name="next-steps"></a>다음 단계

- Windows/Linux/macOS에서 [.NET Core를 사용하여 Azure SQL 데이터베이스를 연결 및 쿼리하는 방법](sql-database-connect-query-dotnet-core.md)을 알아봅니다.  
- [명령줄을 사용하여 Windows/Linux/macOS에서 .NET Core를 시작하는 방법](/dotnet/core/tutorials/using-with-xplat-cli)을 알아봅니다.
- [SSMS를 사용하여 첫 번째 Azure SQL Database를 설계하는 방법](sql-database-design-first-database.md) 또는 [.NET을 사용하여 첫 번째 Azure SQL Database를 설계하는 방법](sql-database-design-first-database-csharp.md)을 알아봅니다.
- .NET에 대한 자세한 내용은 [.NET 설명서](https://docs.microsoft.com/dotnet/)를 참조하세요.
- 다시 시도 논리 예제: [ADO.NET으로 SQL에 탄력적으로 연결][step-4-connect-resiliently-to-sql-with-ado-net-a78n]


<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-to-sql-with-ado-net

