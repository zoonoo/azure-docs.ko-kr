---
title: .NET Core를 사용하여 Azure SQL Database 쿼리 | Microsoft Docs
description: 이 항목에서는 .NET Core를 사용하여 Azure SQL Database에 연결하고 Transact-SQL 문을 통해 쿼리하는 프로그램을 만드는 방법을 보여 줍니다.
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
ms.date: 12/10/2018
ms.openlocfilehash: 471d2b0b8d98651d4b9ef4e88df0e863715b0c88
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/13/2018
ms.locfileid: "53341782"
---
# <a name="quickstart-use-net-core-c-to-query-an-azure-sql-database"></a>빠른 시작: .NET Core(C#)를 사용하여 Azure SQL 데이터베이스 쿼리

이 빠른 시작에서는 [.NET Core](https://www.microsoft.com/net/) 및 C# 코드를 사용하여 Azure SQL 데이터베이스에 연결하고 Transact-SQL 문을 실행해서 데이터를 쿼리하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 조건

이 자습서에서는 다음이 필요합니다.

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

- 컴퓨터의 공용 IP 주소에 대한 [서버 수준 방화벽 규칙](sql-database-get-started-portal-firewall.md)

- [해당 운영 체제용 .NET Core](https://www.microsoft.com/net/core)가 설치됨 

> [!NOTE]
> 이 빠른 시작에서는 *mySampleDatabase* 데이터베이스를 사용합니다. 다른 데이터베이스를 사용하려면 데이터베이스 참조를 변경하고 C# 코드에서 `SELECT` 쿼리를 수정해야 합니다.


## <a name="get-sql-server-connection-information"></a>SQL Server 연결 정보 가져오기

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

#### <a name="get-adonet-connection-information-optional"></a>ADO.NET 연결 정보 가져오기(선택 사항)

1. **mySampleDatabase** 페이지로 이동한 다음, **설정** 아래에서 **연결 문자열**을 선택합니다.

2. 전체 **ADO.NET** 연결 문자열을 검토합니다.

    ![ADO.NET 연결 문자열](./media/sql-database-connect-query-dotnet/adonet-connection-string2.png)

3. 사용하려는 경우 **ADO.NET** 연결 문자열을 복사합니다.
  
## <a name="create-a-new-net-core-project"></a>새로운 .NET Core 프로젝트 만들기

1. 명령 프롬프트를 열고 **sqltest**라는 폴더를 만듭니다. 이 폴더로 이동한 후 다음 명령을 실행합니다.

    ```cmd
    dotnet new console
    ```
    초기 C# 코드 파일(**Program.cs**), XML 구성 파일(**sqltest.csproj**) 및 필요한 이진을 포함하는 새로운 앱 프로젝트 파일이 생성됩니다.

2. 텍스트 편집기에서 **sqltest.csproj**를 열고 `<Project>` 태그 사이에 다음 XML을 붙여넣습니다. 그러면 `System.Data.SqlClient`가 종속성으로 추가됩니다.

    ```xml
    <ItemGroup>
        <PackageReference Include="System.Data.SqlClient" Version="4.6.0" />
    </ItemGroup>
    ```

## <a name="insert-code-to-query-sql-database"></a>SQL 데이터베이스 쿼리 코드 삽입

1. 텍스트 편집기에서 **Program.cs**를 엽니다.

2. 내용을 다음 코드로 바꾸고 서버, 데이터베이스, 사용자 이름 및 암호에 대해 적절한 값을 추가합니다.

> [!NOTE]
> ADO.NET 연결 문자열을 사용하려면 서버, 데이터베이스, 사용자 이름 및 암호를 설정하는 코드의 4줄을 아래 줄로 바꿉니다. 문자열에서 사용자 이름과 암호를 설정합니다.
>
>    `builder.ConnectionString="<your_ado_net_connection_string>";`

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

                builder.DataSource = "<your_server.database.windows.net>"; 
                builder.UserID = "<your_username>";            
                builder.Password = "<your_password>";     
                builder.InitialCatalog = "<your_database>";
         
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
            Console.WriteLine("\nDone. Press enter.");
            Console.ReadLine(); 
        }
    }
}
```

## <a name="run-the-code"></a>코드 실행

1. 프롬프트에서 다음 명령을 실행합니다.

   ```cmd
   dotnet restore
   dotnet run
   ```

2. 상위 20개 행이 반환되는지 확인합니다.

   ```text
   Query data example:
   =========================================

   Road Frames HL Road Frame - Black, 58
   Road Frames HL Road Frame - Red, 58
   Helmets Sport-100 Helmet, Red
   Helmets Sport-100 Helmet, Black
   Socks Mountain Bike Socks, M
   Socks Mountain Bike Socks, L
   Helmets Sport-100 Helmet, Blue
   Caps AWC Logo Cap
   Jerseys Long-Sleeve Logo Jersey, S
   Jerseys Long-Sleeve Logo Jersey, M
   Jerseys Long-Sleeve Logo Jersey, L
   Jerseys Long-Sleeve Logo Jersey, XL
   Road Frames HL Road Frame - Red, 62
   Road Frames HL Road Frame - Red, 44
   Road Frames HL Road Frame - Red, 48
   Road Frames HL Road Frame - Red, 52
   Road Frames HL Road Frame - Red, 56
   Road Frames LL Road Frame - Black, 58
   Road Frames LL Road Frame - Black, 60
   Road Frames LL Road Frame - Black, 62

   Done. Press enter.
   ```
3. **Enter** 키를 눌러 애플리케이션 창을 닫습니다.

## <a name="next-steps"></a>다음 단계

- [명령줄을 사용하여 Windows/Linux/macOS에서 .NET Core를 시작하는 방법](/dotnet/core/tutorials/using-with-xplat-cli)을 알아봅니다.
- [.NET Framework 및 Visual Studio를 사용하여 Azure SQL 데이터베이스를 연결하고 쿼리하는 방법](sql-database-connect-query-dotnet-visual-studio.md)을 알아봅니다.  
- [SSMS를 사용하여 첫 번째 Azure SQL 데이터베이스를 디자인](sql-database-design-first-database.md)하거나 [Azure SQL 데이터베이스를 디자인하고 C# 및 ADO.NET에 연결](sql-database-design-first-database-csharp.md)하는 방법을 알아봅니다.
- .NET에 대한 자세한 내용은 [.NET 설명서](https://docs.microsoft.com/dotnet/)를 참조하세요.
