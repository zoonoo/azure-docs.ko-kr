---
title: "SSMS: Azure SQL Database에서 데이터 연결 및 쿼리 | Microsoft Docs"
description: "SQL Server Management Studio(SSMS)를 사용하여 SQL 데이터베이스에 연결하는 방법을 알아봅니다. 그런 다음 Transact-SQL(T-SQL) 문을 실행하여 데이터를 쿼리하고 편집합니다."
metacanonical: 
keywords: "SQL 데이터베이스에 연결, SQL Server Management Studio"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 7cd2a114-c13c-4ace-9088-97bd9d68de12
ms.service: sql-database
ms.custom: development
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 03/15/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 9f149c3959f1b249a15f2c2714d12c7c9be94bbb
ms.lasthandoff: 03/21/2017


---
# <a name="azure-sql-database-use-sql-server-management-studio-to-connect-and-query-data"></a>Azure SQL Database: SQL Server Management Studio를 사용하여 데이터에 연결 및 쿼리

SSMS([SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx))를 사용하여 사용자 인터페이스 또는 스크립트에서 SQL Server 리소스를 만들고 관리합니다. 이 가이드는 SSMS를 사용하여 Azure SQL Database에 연결하고 쿼리, 삽입, 업데이트 및 삭제 문을 실행하는 방법을 자세히 설명합니다.

이 빠른 시작은 다음과 같은 빠른 시작 중 하나에서 만들어진 리소스를 시작 지점으로 사용합니다.

- [DB 만들기 - 포털](sql-database-get-started-portal.md)
- [DB 만들기 - CLI](sql-database-get-started-cli.md)
- [DB 만들기 - PowerShell](sql-database-get-started-powershell.md) 

시작하기 전에 최신 버전의 [SSMS](https://msdn.microsoft.com/library/mt238290.aspx)를 설치했는지 확인합니다. 

## <a name="get-connection-information"></a>연결 정보 가져오기

Azure Portal에 있는 Azure SQL Database 서버의 정규화된 서버 이름을 가져옵니다. 정규화된 서버 이름을 사용하여 SQL Server Management Studio를 사용하는 서버에 연결합니다.

1. [Azure 포털](https://portal.azure.com/)에 로그인합니다.
2. 왼쪽 메뉴에서 **SQL Database**를 선택하고 **SQL Database** 페이지에서 데이터베이스를 클릭합니다. 
3. 데이터베이스의 경우 Azure Portal의 **Essentials** 창에서 **서버 이름**을 찾고 복사합니다.

    <img src="./media/sql-database-connect-query-ssms/connection-information.png" alt="connection information" style="width: 780px;" />

## <a name="connect-to-the-server"></a>서버에 연결

SQL Server Management Studio를 사용하여 Azure SQL Database 서버에 연결합니다.

1. Windows 검색 상자에서 **SSMS**를 입력하고 **Enter** 키를 클릭하여 SSMS를 엽니다.

2. **서버에 연결** 대화 상자에서 다음 정보를 입력합니다.
   - **서버 유형**: 데이터베이스 엔진을 지정합니다.
   - **서버 이름**: **mynewserver20170313.database.windows.net**과 같은 정규화된 서버 이름을 입력합니다.
   - **인증**: SQL Server 인증을 지정합니다.
   - **로그인**: 서버 관리자 계정을 입력합니다.
   - **암호**: 서버 관리자 계정의 암호를 입력합니다.
 
    <img src="./media/sql-database-connect-query-ssms/connect.png" alt="connect to server" style="width: 780px;" />

3. **Connect**를 클릭합니다. SSMS에서 개체 탐색기 창이 열립니다. 

    <img src="./media/sql-database-connect-query-ssms/connected.png" alt="connected to server" style="width: 780px;" />

4. 개체 탐색기에서 **데이터베이스**를 확장한 다음 **mySampleDatabase**를 확장하여 샘플 데이터베이스에 있는 개체를 봅니다.

## <a name="query-data"></a>쿼리 데이터

[SELECT](https://msdn.microsoft.com/library/ms189499.aspx) Transact-SQL 문을 사용하여 Azure SQL Database에서 데이터를 쿼리합니다.

1. 개체 탐색기에서 **mySampleDatabase**를 마우스 오른쪽 단추로 클릭하고 **새 쿼리**를 클릭합니다. 데이터베이스에 연결된 비어 있는 쿼리 창이 열립니다.
2. 쿼리 창에서 다음 쿼리를 입력합니다.

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

3. 도구 모음에서 **실행**을 클릭하여 Product 및 ProductCategory 테이블에서 데이터를 검색합니다.

    <img src="./media/sql-database-connect-query-ssms/query.png" alt="query" style="width: 780px;" />

## <a name="insert-data"></a>데이터 삽입

[INSERT](https://msdn.microsoft.com/library/ms174335.aspx) Transact-SQL 문을 사용하여 Azure SQL Database에서 데이터를 삽입합니다.

1. 도구 모음에서 **새 쿼리**를 클릭합니다. 데이터베이스에 연결된 비어 있는 쿼리 창이 열립니다.
2. 쿼리 창에서 다음 쿼리를 입력합니다.

   ```sql
   INSERT INTO [SalesLT].[Product]
           ( [Name]
           , [ProductNumber]
           , [Color]
           , [ProductCategoryID]
           , [StandardCost]
           , [ListPrice]
           , [SellStartDate]
           )
     VALUES
           ('myNewProduct'
           ,123456789
           ,'NewColor'
           ,1
           ,100
           ,100
           ,GETDATE() );
   ```

3. 도구 모음에서 **실행**을 클릭하여 Product 테이블에서 새 행을 삽입합니다.

    <img src="./media/sql-database-connect-query-ssms/insert.png" alt="insert" style="width: 780px;" />

## <a name="update-data"></a>데이터 업데이트

[UPDATE](https://msdn.microsoft.com/library/ms177523.aspx) Transact-SQL 문을 사용하여 Azure SQL Database에서 데이터를 업데이트합니다.

1. 도구 모음에서 **새 쿼리**를 클릭합니다. 데이터베이스에 연결된 비어 있는 쿼리 창이 열립니다.
2. 쿼리 창에서 다음 쿼리를 입력합니다.

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

3. 도구 모음에서 **실행**을 클릭하여 Product 테이블에서 지정된 행을 업데이트합니다.

    <img src="./media/sql-database-connect-query-ssms/update.png" alt="update" style="width: 780px;" />

## <a name="delete-data"></a>데이터 삭제

[DELETE](https://msdn.microsoft.com/library/ms189835.aspx) Transact-SQL 문을 사용하여 Azure SQL Database에서 데이터를 삭제합니다.

1. 도구 모음에서 **새 쿼리**를 클릭합니다. 데이터베이스에 연결된 비어 있는 쿼리 창이 열립니다.
2. 쿼리 창에서 다음 쿼리를 입력합니다.

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

3. 도구 모음에서 **실행**을 클릭하여 Product 테이블에서 지정된 행을 삭제합니다.

    <img src="./media/sql-database-connect-query-ssms/delete.png" alt="delete" style="width: 780px;" />

## <a name="next-steps"></a>다음 단계

- SSMS에 대한 자세한 내용은 [SQL Server Management Studio 사용](https://msdn.microsoft.com/library/ms174173.aspx)을 참조하세요.
- Visual Studio Code를 사용하여 데이터를 쿼리 및 편집하는 방법에 대한 정보는 [Visual Studio Code](https://code.visualstudio.com/docs)를 참조하세요.

