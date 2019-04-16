---
title: 'SSMS: Azure SQL 데이터베이스에서 데이터 연결 및 쿼리 | Microsoft Docs'
description: SQL Server Management Studio(SSMS)를 사용하여 SQL Database에 연결하는 방법을 알아봅니다. 그런 다음 Transact-SQL(T-SQL) 문을 실행하여 데이터를 쿼리하고 편집합니다.
keywords: SQL Database에 연결, SQL Server Management Studio
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 03/25/2019
ms.openlocfilehash: 86f29f07df6174ecead852fada73ac05f8682fca
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/09/2019
ms.locfileid: "59359988"
---
# <a name="quickstart-use-sql-server-management-studio-to-connect-and-query-an-azure-sql-database"></a>빠른 시작: SQL Server Management Studio를 사용하여 Azure SQL 데이터베이스 연결 및 쿼리

이 빠른 시작에서는 [SSMS(SQL Server Management Studio)][ssms-install-latest-84g]를 사용하여 Azure SQL 데이터베이스에 연결합니다. 그 후 Transact-SQL 문을 실행하여 데이터를 쿼리, 삽입, 업데이트 및 삭제합니다. SSMS를 사용하여 Microsoft Windows의 SQL Server에서 SQL Database에 이르는 모든 SQL 인프라를 관리할 수 있습니다.  

## <a name="prerequisites"></a>필수 조건

- Azure SQL 데이터베이스입니다. 다음 빠른 시작 중 하나를 사용하여 Azure SQL Database에서 데이터베이스를 만들고 구성할 수 있습니다.

  || 단일 데이터베이스 | Managed Instance |
  |:--- |:--- |:---|
  | 생성| [포털](sql-database-single-database-get-started.md) | [포털](sql-database-managed-instance-get-started.md) |
  || [CLI](scripts/sql-database-create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) | [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) |
  | 구성 | [서버 수준 IP 방화벽 규칙](sql-database-server-level-firewall-rule.md)| [VM에서 연결](sql-database-managed-instance-configure-vm.md)|
  |||[사이트에서 연결](sql-database-managed-instance-configure-p2s.md)
  |데이터 로드|Adventure Works(빠른 시작마다 로드됨)|[Wide World Importers 복원](sql-database-managed-instance-get-started-restore.md)
  |||[GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)의 [BACPAC](sql-database-import.md) 파일에서 Adventure Works 복원 또는 가져오기|
  |||

  > [!IMPORTANT]
  > 이 문서의 스크립트는 Adventure Works 데이터베이스를 사용하도록 작성되었습니다. 관리되는 인스턴스의 경우 Adventure Works 데이터베이스를 인스턴스 데이터베이스로 가져오거나 이 문서의 스크립트를 수정하여 Wide World Importors 데이터베이스를 사용해야 합니다.

## <a name="install-the-latest-ssms"></a>최신 SSMS 설치

시작하기 전에 최신 [SSMS][ssms-install-latest-84g]를 설치했는지 확인합니다.

## <a name="get-sql-server-connection-information"></a>SQL Server 연결 정보 가져오기

Azure SQL 데이터베이스에 연결하는 데 필요한 연결 정보를 가져옵니다. 다음 절차를 수행하려면 정규화된 서버 이름이나 호스트 이름, 데이터베이스 이름 및 로그인 정보가 필요합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

2. **SQL 데이터베이스** 또는 **SQL 관리되는 인스턴스** 페이지로 이동합니다.

3. **개요** 페이지에서 단일 데이터베이스에 대한 **서버 이름** 옆에 있는 정규화된 서버 이름 또는 관리되는 인스턴스에 대한 **호스트** 옆에 있는 정규화된 서버 이름을 검토합니다. 서버 이름이나 호스트 이름을 복사하려면 마우스로 해당 이름 위를 가리키고 **복사** 아이콘을 선택합니다.

## <a name="connect-to-your-database"></a>데이터베이스 연결

SMSS에서 Azure SQL Database 서버에 연결합니다.

> [!IMPORTANT]
> Azure SQL Database 서버는 포트 1433에서 수신 대기합니다. 회사 방화벽 뒤에서 SQL Database 서버에 연결하려면 방화벽에서 이 포트가 열려 있어야 합니다.
>

1. SSMS를 엽니다. **서버에 연결** 대화 상자가 표시됩니다.

2. 다음 정보를 입력합니다.

   | 설정      | 제안 값    | 설명 |
   | ------------ | ------------------ | ----------- |
   | **서버 유형** | 데이터베이스 엔진 | 필수 값. |
   | **서버 이름** | 정규화된 서버 이름 | 예: **mynewserver20170313.database.windows.net**. |
   | **Authentication** | SQL Server 인증 | 이 자습서에서는 SQL 인증을 사용합니다. |
   | **로그인** | 서버 관리자 계정 사용자 ID | 서버를 만드는 데 사용되는 서버 관리자 계정의 사용자 ID입니다. |
   | **암호** | 서버 관리자 계정 암호 | 서버를 만드는 데 사용되는 서버 관리자 계정의 암호입니다. |
   ||||

   ![서버 연결](./media/sql-database-connect-query-ssms/connect.png)  

3. **서버에 연결** 대화 상자에서 **옵션**을 선택합니다. **데이터베이스에 연결** 드롭다운 메뉴에서 **mySampleDatabase**를 선택합니다.

   ![서버에서 db에 연결](./media/sql-database-connect-query-ssms/options-connect-to-db.png)  

4. **연결**을 선택합니다. 개체 탐색기 창이 열립니다.

5. 데이터베이스의 개체를 보려면 **데이터베이스**를 확장한 다음, **mySampleDatabase**를 확장합니다.

   ![데이터베이스 개체 보기](./media/sql-database-connect-query-ssms/connected.png)  

## <a name="query-data"></a>쿼리 데이터

이 [SELECT](https://msdn.microsoft.com/library/ms189499.aspx) Transact-SQL 코드를 실행하여 범주별 상위 20개 제품을 쿼리합니다.

1. 개체 탐색기에서 **mySampleDatabase**를 마우스 오른쪽 단추로 클릭하고 **새 쿼리**를 선택합니다. 데이터베이스에 연결된 새 쿼리 창이 열립니다.

2. 쿼리 창에서 이 SQL 쿼리를 붙여넣습니다.

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

3. 도구 모음에서 **실행**을 선택하여 `Product` 및 `ProductCategory` 테이블에서 데이터를 검색합니다.

    ![두 테이블에서 데이터를 검색하는 쿼리](./media/sql-database-connect-query-ssms/query2.png)

## <a name="insert-data"></a>데이터 삽입

이 [INSERT](https://msdn.microsoft.com/library/ms174335.aspx) Transact-SQL 코드를 실행하여 `SalesLT.Product` 테이블에서 새 제품을 만듭니다.

1. 이전 쿼리를 다음 쿼리로 바꿉니다.

   ```sql
   INSERT INTO [SalesLT].[Product]
           ( [Name]
           , [ProductNumber]
           , [Color]
           , [ProductCategoryID]
           , [StandardCost]
           , [ListPrice]
           , [SellStartDate] )
     VALUES
           ('myNewProduct'
           ,123456789
           ,'NewColor'
           ,1
           ,100
           ,100
           ,GETDATE() );
   ```

2. **실행**을 선택하여 `Product` 테이블에서 새 행을 삽입합니다. **메시지** 창에 **(영향을 받는 행 1개)** 가 표시됩니다.

## <a name="view-the-result"></a>결과 보기

1. 이전 쿼리를 다음 쿼리로 바꿉니다.

   ```sql
   SELECT * FROM [SalesLT].[Product]
   WHERE Name='myNewProduct'
   ```

2. **실행**을 선택합니다. 다음과 같은 결과가 나타납니다.

   ![result](./media/sql-database-connect-query-ssms/result.png)

## <a name="update-data"></a>데이터 업데이트

이 [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx) Transact-SQL 코드를 실행하여 새 제품을 수정합니다.

1. 이전 쿼리를 다음 쿼리로 바꿉니다.

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. **실행**을 선택하여 `Product` 테이블에서 지정된 행을 업데이트합니다. **메시지** 창에 **(영향을 받는 행 1개)** 가 표시됩니다.

## <a name="delete-data"></a>데이터 삭제

이 [DELETE](https://msdn.microsoft.com/library/ms189835.aspx) Transact-SQL 코드를 실행하여 새 제품을 제거합니다.

1. 이전 쿼리를 다음 쿼리로 바꿉니다.

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. **실행**을 선택하여 `Product` 테이블에서 지정된 행을 삭제합니다. **메시지** 창에 **(영향을 받는 행 1개)** 가 표시됩니다.

## <a name="next-steps"></a>다음 단계

- SSMS에 대한 자세한 내용은 [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx)를 참조하세요.
- Azure Portal을 사용하여 연결하고 쿼리하려면 [Azure Portal SQL 쿼리 편집기를 사용하여 연결 및 쿼리](sql-database-connect-query-portal.md)를 참조하세요.
- Visual Studio 코드를 사용하여 연결 및 쿼리하려면 [Visual Studio 코드를 사용하여 연결 및 쿼리](sql-database-connect-query-vscode.md)를 참조하세요.
- .NET을 사용하여 연결 및 쿼리하려면 [.NET을 사용하여 연결 및 쿼리](sql-database-connect-query-dotnet.md)를 참조하세요.
- PHP를 사용하여 연결 및 쿼리하려면 [PHP를 사용하여 연결 및 쿼리](sql-database-connect-query-php.md)를 참조하세요.
- Node.js를 사용하여 연결 및 쿼리하려면 [Node.js를 사용하여 연결 및 쿼리](sql-database-connect-query-nodejs.md)를 참조하세요.
- Java를 사용하여 연결 및 쿼리하려면 [Java를 사용하여 연결 및 쿼리](sql-database-connect-query-java.md)를 참조하세요.
- Python을 사용하여 연결 및 쿼리하려면 [Python을 사용하여 연결 및 쿼리](sql-database-connect-query-python.md)를 참조하세요.
- Ruby를 사용하여 연결 및 쿼리하려면 [Ruby를 사용하여 연결 및 쿼리](sql-database-connect-query-ruby.md)를 참조하세요.

<!-- Article link references. -->

[ssms-install-latest-84g]: https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms