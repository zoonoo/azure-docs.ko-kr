---
title: "VS Code: Azure SQL Database에서 데이터 연결 및 쿼리 | Microsoft Docs"
description: "Visual Studio Code를 사용하여 Azure에서 SQL Database에 연결하는 방법을 알아봅니다. 그런 다음 Transact-SQL(T-SQL) 문을 실행하여 데이터를 쿼리하고 편집합니다."
metacanonical: 
keywords: "SQL Database에 연결"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 676bd799-a571-4bb8-848b-fb1720007866
ms.service: sql-database
ms.custom: manage
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 03/17/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 9192ed7b69e52c59efe33981e1e557b634679196
ms.lasthandoff: 03/21/2017


---
# <a name="azure-sql-database-use-visual-studio-code-to-connect-and-query-data"></a>Azure SQL Database: Visual Studio Code를 사용하여 데이터에 연결 및 쿼리

[Visual Studio Code](https://code.visualstudio.com/docs)는 확장을 지원하는 Windows, macOS, Linux용 그래픽 코드 편집기입니다. [mssql 확장](https://aka.ms/mssql-marketplace)인 Visual Studio Code를 사용하여 Azure SQL Database를 연결하고 쿼리합니다. 이 가이드는 Visual Studio Code를 사용하여 Azure SQL Database에 연결하고 쿼리, 삽입, 업데이트 및 삭제 문을 실행하는 방법을 자세히 설명합니다.

이 빠른 시작은 다음과 같은 빠른 시작 중 하나에서 만들어진 리소스를 시작 지점으로 사용합니다.

- [DB 만들기 - 포털](sql-database-get-started-portal.md)
- [DB 만들기 - CLI](sql-database-get-started-cli.md)
- [DB 만들기 - PowerShell](sql-database-get-started-powershell.md) 

시작하기 전에 최신 버전의 [Visual Studio Code](https://code.visualstudio.com/Download)를 설치하고 했는지 [mssql 확장](https://aka.ms/mssql-marketplace)을 로드했는지 확인합니다. mssql 확장에 대한 설치 지침은 [VS Code 설치](https://docs.microsoft.com/sql/linux/sql-server-linux-develop-use-vscode#install-vs-code)를 참조하세요. 

## <a name="get-connection-information"></a>연결 정보 가져오기

Azure Portal에 있는 Azure SQL Database 서버의 정규화된 서버 이름을 가져옵니다. 정규화된 서버 이름을 사용하여 Visual Studio Code를 사용하는 서버에 연결합니다.

1. [Azure 포털](https://portal.azure.com/)에 로그인합니다.
2. 왼쪽 메뉴에서 **SQL Database**를 선택하고 **SQL Database** 페이지에서 데이터베이스를 클릭합니다. 
3. 데이터베이스의 경우 Azure Portal의 **Essentials** 창에서 **서버 이름**을 찾고 복사하여 이 빠른 시작에서 나중에 사용합니다.

    <img src="./media/sql-database-connect-query-ssms/connection-information.png" alt="connection information" style="width: 780px;" />

## <a name="set-language-mode-to-sql"></a>언어 모드를 SQL로 설정

Visual Studio Code에서 언어 모드를 **SQL**로 설정하여 mssql 명령 및 T-SQL IntelliSense를 사용하도록 설정합니다.

1. 새 Visual Studio Code 창을 엽니다. 

2. **CTRL+K, M**을 누르고 **SQL**을 입력한 다음 **ENTER** 키를 눌러서 언어 모드를 SQL로 설정합니다. 

<img src="./media/sql-database-connect-query-vscode/vscode-language-mode.png" alt="SQL language mode" style="width: 780px;" />

## <a name="connect-to-the-server"></a>서버에 연결

Visual Studio Code를 사용하여 Azure SQL Database 서버에 연결합니다.

1. VS Code에서 **CTRL+SHIFT+P**(또는 **F1** 키)를 눌러서 명령 팔레트를 엽니다.

2. **sqlcon**을 입력하고 **ENTER** 키를 누릅니다.

3. **예**를 클릭하여 언어를 **SQL**로 설정합니다.

4. **ENTER** 키를 눌러서 **연결 프로필 만들기**를 선택합니다. 그러면 SQL Server 인스턴스의 연결 프로필을 만듭니다.

5. 프롬프트에 따라 새 연결 프로필의 연결 속성을 지정합니다. 각 값을 지정한 후에 **ENTER** 키를 눌러서 계속합니다. 

   다음 테이블에서는 연결 프로필 속성을 설명합니다.

   | 설정 | 설명 |
   |-----|-----|
   | **서버 이름** | **mynewserver20170313.database.windows.net**과 같은 정규화된 서버 이름을 입력합니다. |
   | **데이터베이스 이름** | **mySampleDatabase**와 같은 데이터베이스 이름을 입력합니다. |
   | **인증** | SQL 로그인 선택 |
   | **사용자 이름** | 서버 관리자 계정을 입력합니다. |
   | **암호(SQL 로그인)** | 서버 관리자 계정의 암호를 입력합니다. | 
   | **암호를 저장하시겠습니까?** | **예** 또는 **아니요**를 선택합니다. |
   | **[옵션]이 프로필의 이름 입력** | **mySampleDatabase**와 같은 연결 프로필 이름을 입력합니다. 

6. **ESC** 키를 누르면 프로필을 만들고 연결하도록 사용자에게 알려 주는 정보 메시지가 닫힙니다.

7. 상태 표시줄에서 연결을 확인합니다.

   <img src="./media/sql-database-connect-query-vscode/vscode-connection-status.png" alt="Connection status" style="width: 780px;" />

## <a name="query-data"></a>쿼리 데이터

[SELECT](https://msdn.microsoft.com/library/ms189499.aspx) Transact-SQL 문을 사용하여 Azure SQL Database에서 데이터를 쿼리합니다.

1. **편집기** 창의 빈 쿼리 창에서 다음 쿼리를 입력합니다.

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

3. **CTRL+SHIFT+E**를 눌러서 Product 및 ProductCategory 테이블에서 데이터를 검색합니다.

    <img src="./media/sql-database-connect-query-vscode/query.png" alt="Query" style="width: 780px;" />

## <a name="insert-data"></a>데이터 삽입

[INSERT](https://msdn.microsoft.com/library/ms174335.aspx) Transact-SQL 문을 사용하여 Azure SQL Database에서 데이터를 삽입합니다.

1. **편집기** 창에서 이전 쿼리를 삭제하고 다음 쿼리를 입력합니다.

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

3. **CTRL+SHIFT+E**를 눌러서 Product 테이블에서 새 행을 삽입합니다.

## <a name="update-data"></a>데이터 업데이트

[UPDATE](https://msdn.microsoft.com/library/ms177523.aspx) Transact-SQL 문을 사용하여 Azure SQL Database에서 데이터를 업데이트합니다.

1.  **편집기** 창에서 이전 쿼리를 삭제하고 다음 쿼리를 입력합니다.

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

3. **CTRL+SHIFT+E**를 눌러서 Product 테이블에서 지정된 행을 업데이트합니다.

## <a name="delete-data"></a>데이터 삭제

[DELETE](https://msdn.microsoft.com/library/ms189835.aspx) Transact-SQL 문을 사용하여 Azure SQL Database에서 데이터를 삭제합니다.

1. **편집기** 창에서 이전 쿼리를 삭제하고 다음 쿼리를 입력합니다.

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

3. **CTRL+SHIFT+E**를 눌러서 Product 테이블에서 지정된 행을 삭제합니다.

## <a name="next-steps"></a>다음 단계

- Visual Studio Code에 대한 자세한 내용은 [Visual Studio Code](https://code.visualstudio.com/docs)를 참조하세요.
- SQL Server Management Studio를 사용하여 데이터를 쿼리 및 편집하는 방법에 대한 정보는 [SSMS](https://msdn.microsoft.com/library/ms174173.aspx)를 참조하세요.
