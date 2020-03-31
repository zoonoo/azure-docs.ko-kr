---
title: 'SSMS: 연결 및 데이터 쿼리'
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
ms.date: 03/10/2020
ms.openlocfilehash: 31bd47128a272e75d7021180b536fe6bf7420f55
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2020
ms.locfileid: "79299297"
---
# <a name="quickstart-use-sql-server-management-studio-to-connect-and-query-an-azure-sql-database"></a>빠른 시작: SQL Server Management Studio를 사용하여 Azure SQL 데이터베이스 연결 및 쿼리

이 빠른 시작에서는 SSMS(SQL Server Management Studio)를 사용하여 Azure SQL 데이터베이스에 연결하고 일부 쿼리를 실행하는 방법을 알아봅니다.

## <a name="prerequisites"></a>사전 요구 사항

이 빠른 시작을 완료하려면 다음 항목이 필요합니다.

- [SSMS(SQL Server Management Studio)](/sql/ssms/download-sql-server-management-studio-ssms/).
- AdventureWorksLT 샘플 데이터베이스. AdventureWorksLT 데이터베이스의 작업 복사본이 필요한 경우 [Azure SQL 데이터베이스 만들기](sql-database-single-database-get-started.md) 빠른 시작을 완료하여 만듭니다.
    - 이 문서의 스크립트는 AdventureWorksLT 데이터베이스를 사용하도록 작성되었습니다. 관리형 인스턴스를 사용하는 경우 AdventureWorks 데이터베이스를 인스턴스 데이터베이스로 가져오거나 이 문서의 스크립트를 수정하여 Wide World Importers 데이터베이스를 사용해야 합니다.

SSMS를 설치하지 않고 일부 임시 쿼리를 실행하려는 경우 [빠른 시작: Azure Portal의 쿼리 편집기를 사용하여 SQL 데이터베이스 쿼리](sql-database-connect-query-portal.md)를 참조하세요.

## <a name="get-sql-server-connection-information"></a>SQL Server 연결 정보 가져오기

데이터베이스에 연결하는 데 필요한 연결 정보를 가져옵니다. 이 빠른 시작을 완료하려면 정규화된 서버 이름이나 호스트 이름, 데이터베이스 이름 및 로그인 정보가 필요합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

2. 쿼리하려는 **SQL 데이터베이스** 또는 **SQL 관리형 인스턴스**로 이동합니다.

3. **개요** 페이지에서 정규화된 서버 이름을 복사합니다. 단일 데이터베이스에 대한 **서버 이름** 옆에 있거나 관리형 인스턴스에 대한 **호스트** 옆에 있는 정규화된 서버 이름 옆에 있습니다. 정규화된 이름은 실제 서버 이름을 포함하는 것을 제외하고는 *servername.database.windows.net*과 같습니다.

## <a name="connect-to-your-database"></a>데이터베이스 연결

SSMS에서 Azure SQL Database 서버에 연결합니다.

> [!IMPORTANT]
> Azure SQL Database 서버는 포트 1433에서 수신 대기합니다. 회사 방화벽 뒤에서 SQL Database 서버에 연결하려면 방화벽에서 이 포트가 열려 있어야 합니다.

1. SSMS를 엽니다.

2. **서버에 연결** 대화 상자가 표시됩니다. 다음 정보를 입력합니다.

   | 설정      | 제안 값    | Description |
   | ------------ | ------------------ | ----------- |
   | **서버 유형** | 데이터베이스 엔진 | 필수 값. |
   | **서버 이름** | 정규화된 서버 이름 | 예: **servername.database.windows.net** |
   | **인증** | SQL Server 인증 | 이 자습서에서는 SQL 인증을 사용합니다. |
   | **로그인** | 서버 관리자 계정 사용자 ID | 서버를 만드는 데 사용되는 서버 관리자 계정의 사용자 ID입니다. |
   | **암호** | 서버 관리자 계정 암호 | 서버를 만드는 데 사용되는 서버 관리자 계정의 암호입니다. |
   ||||

   ![서버에 연결](./media/sql-database-connect-query-ssms/connect.png)  

3. **서버에 연결** 대화 상자에서 **옵션**을 선택합니다. **데이터베이스에 연결** 드롭다운 메뉴에서 **mySampleDatabase**를 선택합니다. [필수 구성 요소](#prerequisites) 섹션에서 빠른 시작을 완료하면 mySampleDatabase라는 AdventureWorksLT 데이터베이스가 만들어집니다. AdventureWorks 데이터베이스의 작업 복사본 이름이 mySampleDatabase와 다른 경우 이를 대신 선택합니다.

   ![서버에서 db에 연결](./media/sql-database-connect-query-ssms/options-connect-to-db.png)  

4. **연결**을 선택합니다. 개체 탐색기 창이 열립니다.

5. 데이터베이스의 개체를 보려면 **데이터베이스**를 확장한 다음, 데이터베이스 노드를 확장합니다.

   ![mySampleDatabase 개체](./media/sql-database-connect-query-ssms/connected.png)  

## <a name="query-data"></a>쿼리 데이터

이 [SELECT](https://msdn.microsoft.com/library/ms189499.aspx) Transact-SQL 코드를 실행하여 범주별 상위 20개 제품을 쿼리합니다.

1. 개체 탐색기에서 **mySampleDatabase**를 마우스 오른쪽 단추로 클릭하고 **새 쿼리**를 선택합니다. 데이터베이스에 연결된 새 쿼리 창이 열립니다.

2. 쿼리 창에서 다음 SQL 쿼리를 붙여 넣습니다.

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

3. 도구 모음에서 **실행**을 선택하여 쿼리를 실행하고 `Product` 및 `ProductCategory` 테이블에서 데이터를 검색합니다.

    ![Product 및 ProductCategory 테이블에서 데이터를 검색하는 쿼리](./media/sql-database-connect-query-ssms/query2.png)

### <a name="insert-data"></a>데이터 삽입

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

#### <a name="view-the-result"></a>결과 보기

1. 이전 쿼리를 다음 쿼리로 바꿉니다.

   ```sql
   SELECT * FROM [SalesLT].[Product]
   WHERE Name='myNewProduct'
   ```

2. **실행**을 선택합니다. 다음과 같은 결과가 나타납니다.

   ![Product 테이블 쿼리 결과](./media/sql-database-connect-query-ssms/result.png)

### <a name="update-data"></a>데이터 업데이트

이 [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx) Transact-SQL 코드를 실행하여 새 제품을 수정합니다.

1. 이전 쿼리를 이전에 만든 새 레코드를 반환하는 쿼리로 바꿉니다.

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. **실행**을 선택하여 `Product` 테이블에서 지정된 행을 업데이트합니다. **메시지** 창에 **(영향을 받는 행 1개)** 가 표시됩니다.

### <a name="delete-data"></a>데이터 삭제

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
