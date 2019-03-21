---
title: 'Azure Portal: 쿼리 편집기를 사용하여 Azure SQL Database 쿼리 | Microsoft Docs'
description: SQL 쿼리 편집기를 사용하여 Azure Portal에서 SQL Database에 연결하는 방법을 알아봅니다. 그런 다음 Transact-SQL(T-SQL) 문을 실행하여 데이터를 쿼리하고 편집합니다.
keywords: SQL Database에 연결, Azure Portal, 포털, 쿼리 편집기
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: AyoOlubeko
ms.author: ayolubek
ms.reviewer: carlrab
manager: craigg
ms.date: 02/12/2019
ms.openlocfilehash: b2109afe6760eb87269a33ed74784149c2bf3e03
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58105797"
---
# <a name="quickstart-use-the-azure-portals-sql-query-editor-to-connect-and-query-data"></a>빠른 시작: Azure Portal의 SQL 쿼리 편집기를 사용하여 데이터 연결 및 쿼리

SQL 쿼리 편집기는 Azure SQL Database 또는 Azure SQL Data Warehouse에서 SQL 쿼리를 쉽게 실행하는 방법을 제공하는 Azure Portal 브라우저 도구입니다. 이 빠른 시작에서는 쿼리 편집기를 사용하여 SQL Database에 연결한 다음, Transact-SQL 문을 실행하여 데이터를 쿼리, 삽입, 업데이트 및 삭제합니다.

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음이 필요합니다.

- Azure SQL 데이터베이스입니다. 다음 빠른 시작 중 하나를 사용하여 Azure SQL Database에서 데이터베이스를 만들고 구성할 수 있습니다.

  || 단일 데이터베이스 |
  |:--- |:--- |
  | 생성| [포털](sql-database-single-database-get-started.md) | 
  || [CLI](scripts/sql-database-create-and-configure-database-cli.md) | 
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) | 
  | 구성 | [서버 수준 IP 방화벽 규칙](sql-database-server-level-firewall-rule.md)| 
  |||

> [!NOTE]
> SQL Server 방화벽 설정에서 **Azure 서비스 방문 허용** 옵션이 **켜기**로 설정되어 있는지 확인합니다. 이 옵션을 사용하면 SQL 쿼리 편집기에서 데이터베이스 및 데이터 웨어하우스에 액세스할 수 있습니다.

## <a name="sign-in-the-azure-portal"></a>Azure Portal에 로그인

[Azure Portal](https://portal.azure.com/)에 로그인합니다.

## <a name="connect-using-sql-authentication"></a>SQL 인증을 사용하여 연결

1. 왼쪽 메뉴에서 **SQL 데이터베이스**를 선택한 다음, **mySampleDatabase**를 선택합니다.

2. 왼쪽 메뉴에서 **쿼리 편집기(미리 보기)** 를 찾아 선택합니다. **로그인** 페이지가 나타납니다.

    ![쿼리 편집기 찾기](./media/sql-database-connect-query-portal/find-query-editor.PNG)

3. **인증 형식** 드롭다운 메뉴에서 **SQL Server 인증**을 선택하고 데이터베이스를 만드는 데 사용되는 서버 관리자 계정의 사용자 ID 및 암호를 입력합니다.

    ![로그인](./media/sql-database-connect-query-portal/login-menu.png) 

4. **확인**을 선택합니다.


## <a name="connect-using-azure-active-directory"></a>Azure Active Directory를 사용하여 연결

AD(Active Directory) 관리자를 구성하면 단일 ID를 사용하여 Azure Portal 및 SQL 데이터베이스에 로그인할 수 있습니다. 다음 단계에 따라 SQL Server에 대한 AD 관리자를 구성합니다.

> [!NOTE]
> * 메일 계정(예: outlook.com, gmail.com, yahoo.com 등)은 아직 AD 관리자로 지원되지 않습니다. Azure AD에서 기본적으로 만들어지거나 Azure AD에 페더레이션된 사용자를 선택해야 합니다.
> * Azure AD 관리자 로그인은 2단계 인증이 사용되는 계정에서 작동하지 않습니다.

1. 왼쪽 메뉴에서 **모든 리소스**를 선택한 다음, SQL Server를 선택합니다.

2. SQL Server의 **설정** 메뉴에서 **Active Directory 관리자**를 선택합니다.

3. AD 관리자 페이지 도구 모음에서 **관리자 설정**을 선택하고 사용자 또는 그룹을 AD 관리자로 선택합니다.

    ![Active Directory 선택](./media/sql-database-connect-query-portal/select-active-directory.png)

4. AD 관리자 페이지 도구 모음에서 **저장**을 선택합니다.

5. **mySampleDatabase** 데이터베이스로 이동하고 왼쪽 메뉴에서 **쿼리 편집기(미리 보기)** 를 선택합니다. **로그인** 페이지가 나타납니다. AD 관리자인 경우 오른쪽에 있는 **Active Directory Single Sign-On**에 로그인되었다는 메시지가 나타납니다. 
   
6. **확인**을 선택합니다.


## <a name="view-data"></a>데이터 보기

1. 인증을 받은 후 쿼리 편집기에 다음 SQL을 붙여넣어 범주별 상위 20개 제품을 검색합니다.

   ```sql
    SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
    FROM SalesLT.ProductCategory pc
    JOIN SalesLT.Product p
    ON pc.productcategoryid = p.productcategoryid;
   ```

2. 도구 모음에서 **실행**을 선택한 다음, **결과** 창에서 출력을 검토합니다.

![쿼리 편집기 결과](./media/sql-database-connect-query-portal/query-editor-results.png)

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


2. **실행**을 선택하여 `Product` 테이블에서 새 행을 삽입합니다. **메시지** 창에 **쿼리 성공: 영향을 받는 행: 1**이 표시됩니다.


## <a name="update-data"></a>데이터 업데이트

다음 [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx) Transact-SQL 문을 실행하여 새 제품을 수정합니다.

1. 이전 쿼리를 다음 쿼리로 바꿉니다.

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. **실행**을 선택하여 `Product` 테이블에서 지정된 행을 업데이트합니다. **메시지** 창에 **쿼리 성공: 영향을 받는 행: 1**이 표시됩니다.

## <a name="delete-data"></a>데이터 삭제

다음 [DELETE](https://msdn.microsoft.com/library/ms189835.aspx) Transact-SQL 문을 실행하여 새 제품을 제거합니다.

1. 이전 쿼리를 다음 쿼리로 바꿉니다.

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. **실행**을 선택하여 `Product` 테이블에서 지정된 행을 삭제합니다. **메시지** 창에 **쿼리 성공: 영향을 받는 행: 1**이 표시됩니다.


## <a name="query-editor-considerations"></a>쿼리 편집기에 대한 고려 사항

쿼리 편집기를 사용할 때 알아야 할 몇 가지 사항이 있습니다.

* 가상 네트워크에서 SQL Server 데이터베이스를 쿼리하는 데는 쿼리 편집기를 사용할 수 없습니다.

* F5 키를 누르면 쿼리 편집기 페이지가 새로 고쳐지고 작업 중인 모든 쿼리가 손실됩니다.

* 쿼리 편집기는 `master` 데이터베이스 연결을 지원하지 않습니다.

* 쿼리 실행에 대한 제한 시간은 5분입니다.

* 쿼리 편집기는 지리 데이터 형식에 대한 원통 도법만 지원합니다.

* 데이터베이스 테이블 및 뷰에 대한 IntelliSense는 지원되지 않습니다. 그러나 이미 입력된 이름에 대한 자동 완성은 편집기에서 지원합니다.


## <a name="next-steps"></a>다음 단계

Azure SQL 데이터베이스에서 지원되는 Transact-SQL에 대해 자세히 알아보려면 [SQL Database로 마이그레이션 중 Transact-SQL 차이점 해결](sql-database-transact-sql-information.md)을 참조하세요.
