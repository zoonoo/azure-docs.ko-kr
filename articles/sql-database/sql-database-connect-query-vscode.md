---
title: 'VS Code: Azure SQL Database에서 데이터 연결 및 쿼리 | Microsoft Docs'
description: Visual Studio Code를 사용하여 Azure에서 SQL Database에 연결하는 방법을 알아봅니다. 그런 다음 Transact-SQL(T-SQL) 문을 실행하여 데이터를 쿼리하고 편집합니다.
keywords: SQL Database에 연결
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 12/06/2018
ms.openlocfilehash: 994f7d300b1e9a76e109e73467a2e7f0d4c1584d
ms.sourcegitcommit: e68df5b9c04b11c8f24d616f4e687fe4e773253c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/20/2018
ms.locfileid: "53652118"
---
# <a name="quickstart-use-visual-studio-code-to-connect-and-query-an-azure-sql-database"></a>빠른 시작: Visual Studio Code를 사용하여 Azure SQL Database 연결 및 쿼리

[Visual Studio Code](https://code.visualstudio.com/docs)는 Windows, macOS, Linux용 그래픽 코드 편집기입니다. Microsoft SQL Server, Azure SQL Database 및 SQL Data Warehouse를 쿼리하는 [mssql 확장](https://aka.ms/mssql-marketplace)을 비롯한 여러 확장을 지원합니다. 이 빠른 시작에서는 Visual Studio Code를 사용하여 Azure SQL Database에 연결한 다음, Transact-SQL 문을 실행하여 데이터를 쿼리, 삽입, 업데이트 및 삭제합니다.

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음이 필요합니다.

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

#### <a name="install-visual-studio-code"></a>Visual Studio Code 설치

최신 [Visual Studio Code](https://code.visualstudio.com/Download)를 설치하고 [mssql 확장](https://aka.ms/mssql-marketplace)을 로드했는지 확인합니다. mssql 확장 프로그램 설치에 대한 지침은 [VS Code 설치](https://docs.microsoft.com/sql/linux/sql-server-linux-develop-use-vscode#install-vs-code) 및 [Visual Studio Code용 mssql](https://marketplace.visualstudio.com/items?itemName=ms-mssql.mssql)을 참조하세요. 

## <a name="configure-visual-studio-code"></a>Visual Studio Code 구성 

### <a name="mac-os"></a>**Mac OS**
macOS의 경우 mssql 확장에서 사용하는 .NET Core의 필수 구성 요소인 OpenSSL을 설치해야 합니다. 터미널을 열고 다음 명령을 입력하여 **brew** 및 **OpenSSL**을 설치합니다. 

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew update
brew install openssl
mkdir -p /usr/local/lib
ln -s /usr/local/opt/openssl/lib/libcrypto.1.0.0.dylib /usr/local/lib/
ln -s /usr/local/opt/openssl/lib/libssl.1.0.0.dylib /usr/local/lib/
```

### <a name="linux-ubuntu"></a>**Linux(Ubuntu)**

특별한 구성이 필요 없습니다.

### <a name="windows"></a>**Windows**

특별한 구성이 필요 없습니다.

## <a name="sql-server-connection-information"></a>SQL 서버 연결 정보

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

## <a name="set-language-mode-to-sql"></a>언어 모드를 SQL로 설정

Visual Studio Code에서 언어 모드를 **SQL**로 설정하여 mssql 명령 및 T-SQL IntelliSense를 사용합니다.

1. 새 Visual Studio Code 창을 엽니다. 

2. **Ctrl**+**N**을 누릅니다. 새 일반 텍스트 파일이 열립니다. 

3. 상태 표시줄의 오른쪽 아래 모서리에 있는 **일반 텍스트**를 선택합니다.

4. 열리는 **언어 모드 선택** 드롭다운 메뉴에서 **SQL**을 선택합니다. 

## <a name="connect-to-your-database"></a>데이터베이스 연결

Visual Studio Code를 사용하여 Azure SQL Database 서버에 연결합니다.

> [!IMPORTANT]
> 계속하기 전에 서버 및 로그인 정보를 준비했는지 확인합니다. 연결 프로필 정보를 입력하기 시작하면 Visual Studio Code에서 포커스를 변경하는 경우 프로필 만들기를 다시 시작해야 합니다.
>

1. Visual Studio Code에서 **Ctrl+Shift+P**(또는 **F1** 키)를 눌러서 명령 팔레트를 엽니다.

2. **MS SQL:Connect**를 선택하고 **Enter**를 선택합니다.

3. **연결 프로필 만들기**를 선택합니다.

4. 프롬프트에 따라 새 프로필의 연결 속성을 지정합니다. 각 값을 지정한 후에 **Enter**를 선택하여 계속합니다. 

   | 자산       | 제안 값 | 설명 |
   | ------------ | ------------------ | ------------------------------------------------- | 
   | **서버 이름** | 정규화된 서버 이름 | 예: **mynewserver20170313.database.windows.net**. |
   | **데이터베이스 이름** | mySampleDatabase | 연결할 데이터베이스입니다. |
   | **인증** | SQL 로그인| 이 자습서에서는 SQL 인증을 사용합니다. |
   | **사용자 이름** | 사용자 이름 | 서버를 만드는 데 사용되는 서버 관리자 계정의 사용자 이름입니다. |
   | **암호(SQL 로그인)** | 암호 | 서버를 만드는 데 사용되는 서버 관리자 계정의 암호입니다. |
   | **암호를 저장하시겠습니까?** | 예 또는 아니요 | 매번 암호를 입력하지 않으려면 **예**를 선택합니다. |
   | **이 프로필의 이름 입력** | **mySampleProfile**과 같은 프로필 이름 | 프로필을 저장할 경우 후속 로그인의 연결 속도가 빨라집니다. | 

   성공하면 프로필이 생성되고 연결되었다는 알림이 나타납니다.

## <a name="query-data"></a>쿼리 데이터

다음 [SELECT](https://msdn.microsoft.com/library/ms189499.aspx) Transact-SQL 문을 실행하여 범주별 상위 20개 제품을 쿼리합니다.

1. 편집기 창에서 다음 SQL 쿼리를 붙여넣습니다.

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

2. **Ctrl**+**Shift**+**E**를 눌러 쿼리를 실행하고 `Product` 및 `ProductCategory` 테이블의 결과를 표시합니다.

    ![2개 테이블에서 데이터를 검색하는 쿼리](./media/sql-database-connect-query-vscode/query.png)

## <a name="insert-data"></a>데이터 삽입

다음 [INSERT](https://msdn.microsoft.com/library/ms174335.aspx) Transact-SQL 문을 실행하여 `SalesLT.Product` 테이블에 새 제품을 추가합니다.

1. 이전 쿼리를 다음 쿼리로 바꿉니다.

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

2. **Ctrl**+**Shift**+**E**를 눌러 `Product` 테이블에 새 행을 삽입합니다.

## <a name="update-data"></a>데이터 업데이트

다음 [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx) Transact-SQL 문을 실행하여 추가된 제품을 업데이트합니다.

1. 이전 쿼리를 다음 쿼리로 바꿉니다.

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. **Ctrl**+**Shift**+**E**를 눌러 `Product` 테이블에서 지정된 행을 업데이트합니다.

## <a name="delete-data"></a>데이터 삭제

다음 [DELETE](https://docs.microsoft.com/sql/t-sql/statements/delete-transact-sql) Transact-SQL 문을 실행하여 새 제품을 제거합니다.

1. 이전 쿼리를 다음 쿼리로 바꿉니다.

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. **Ctrl**+**Shift**+**E**를 눌러 `Product` 테이블에서 지정된 행을 삭제합니다.

## <a name="next-steps"></a>다음 단계

- SQL Server Management Studio를 사용하여 연결 및 쿼리하려면 [빠른 시작: SQL Server Management Studio를 사용하여 Azure SQL Database에 연결 및 데이터 쿼리](sql-database-connect-query-ssms.md)를 참조하세요.
- Azure Portal을 사용하여 연결 및 쿼리하려면 [빠른 시작: Azure Portal에서 SQL 쿼리 편집기를 사용하여 데이터 연결 및 쿼리](sql-database-connect-query-portal.md)를 참조하세요.
- Visual Studio Code를 사용하는 MSDN 잡지 문서는 [MSSQL 확장을 사용하여 데이터베이스 IDE 만들기 블로그 게시물](https://msdn.microsoft.com/magazine/mt809115)을 참조하세요.
