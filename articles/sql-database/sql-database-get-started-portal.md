---
title: "Azure Portal: SQL Database 만들기 | Microsoft Docs"
description: "Azure Portal에서 SQL Database 논리 서버, 서버 수준 방화벽 규칙 및 데이터베이스를 만드는 방법을 알아봅니다. Azure Portal을 사용하여 Azure SQL Database를 쿼리하는 방법도 알아봅니다."
keywords: "SQL 데이터베이스 자습서, SQL 데이터베이스 만들기"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: aeb8c4c3-6ae2-45f7-b2c3-fa13e3752eed
ms.service: sql-database
ms.custom: quick start
ms.workload: data-management
ms.tgt_pltfrm: portal
ms.devlang: na
ms.topic: hero-article
ms.date: 03/13/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 1c087f3ecbdd6956b50a8ef9b7e81340f83ff0cf
ms.lasthandoff: 03/21/2017


---
# <a name="create-and-query-a-single-azure-sql-database-in-the-azure-portal"></a>Azure Portal에서 단일 Azure SQL Database 만들기 및 쿼리

Azure Portal을 통해 Azure SQL Database를 만들 수 있습니다. 이 메서드는 Azure SQL Database 및 관련된 모든 Azure 리소스를 만들고 구성하기 위한 브라우저 기반 사용자 인터페이스를 제공합니다.

## <a name="log-in-to-the-azure-portal"></a>Azure 포털에 로그인

[Azure 포털](https://portal.azure.com/)에 로그인합니다.

## <a name="create-a-sql-database"></a>SQL 데이터베이스 만들기

Azure SQL Database는 일련의 정의된 [계산 및 저장소 리소스](sql-database-service-tiers.md)를 사용하여 만들어집니다. 데이터베이스는 [Azure 리소스 그룹](../azure-resource-manager/resource-group-overview.md) 및 [Azure SQL Database 논리 서버](sql-database-features.md)에서 만들어집니다. 

다음 단계에 따라서 Adventure Works LT 샘플 데이터를 포함하는 SQL Database를 만듭니다. 

1. Azure Portal의 왼쪽 위에 있는 **새로 만들기** 단추를 클릭합니다.

2. **새로 만들기** 페이지에서 **데이터베이스**를 선택하고 **데이터베이스** 페이지에서 **SQL Database**를 선택합니다.

3. SQL Database 양식에 필요한 정보를 채웁니다. 
   - 데이터베이스 이름: 데이터베이스 이름을 입력합니다.
   - 구독: 사용자의 구독을 선택합니다.
   - 리소스 그룹: 신규 또는 기존 리소스 그룹을 선택합니다.
   - 원본: **샘플(AdventureWorksLT)**을 선택합니다.
   - 서버: 새 서버를 만듭니다(**서버** 이름은 전역적으로 고유해야 함)
   - 탄력적 풀: 이 빠른 시작에 대해 **나중에**를 선택합니다.
   - 가격 책정 계층: **20DTU** 및 **250**GB의 저장소를 선택합니다.
   - 데이터 정렬: 샘플 데이터베이스를 가져올 때는 이 값을 변경할 수 없습니다. 
   - 대시보드에 고정: 이 확인란을 선택합니다.

      ![데이터베이스 만들기](./media/sql-database-get-started/create-database-s1.png)

4. 작업을 완료했으면 **만들기**를 클릭합니다. 프로비전하는 데 몇 분이 걸립니다.
5. SQL Database 배포가 끝났으면 대시보드에서 **SQL Database**를 선택하거나 왼쪽 메뉴에서 **SQL Databases**를 선택하고 **SQL Database** 페이지에서 새 데이터베이스를 클릭합니다. 데이터베이스에 대한 개요 페이지가 열리고 이 페이지에 정규화된 서버 이름(예: **mynewserver20170313.database.windows.net**)이 표시되며 추가 구성을 위한 옵션도 제공됩니다.

      ![새 sql 데이터베이스](./media/sql-database-get-started/new-database-s1-overview.png) 

## <a name="create-a-server-level-firewall-rule"></a>서버 수준 방화벽 규칙 만들기

SQL Database 서비스는 외부 응용 프로그램 및 도구가 서버 및 데이터베이스로 연결하지 못하도록 하는 방화벽을 만듭니다. 다음 단계에 따라 IP 주소에 대한 [SQL Database 서버 수준 방화벽 규칙](sql-database-firewall-configure.md)을 만들어 SQL Database 방화벽을 통해 외부 연결을 사용하도록 설정합니다. 

1. 데이터베이스에 대한 도구 모음에서 **서버 방화벽 설정**을 클릭합니다. SQL Database 서버에 대한 **방화벽 설정** 페이지가 열립니다. 

      ![서버 방화벽 규칙](./media/sql-database-get-started/server-firewall-rule.png) 

2. 도구 모음에서 **클라이언트 IP 추가**를 클릭하고 **저장**을 클릭합니다. 현재 IP 주소에 대한 서버 수준 방화벽 규칙이 생성됩니다.

3. **확인**을 클릭한 후 **X**를 클릭하여 방화벽 설정 페이지를 닫습니다.

이제 SQL Server Management Studio 또는 선택한 다른 도구를 사용하여 데이터베이스와 해당 서버에 연결할 수 있습니다.

## <a name="query-the-sql-database"></a>SQL Database 쿼리

이 단계에 따라 Azure Portal에서 쿼리 편집기를 사용하여 데이터베이스를 쿼리합니다. 

1. 데이터베이스에 대한 SQL Database 페이지의 도구 모음에서 **도구**를 클릭합니다. **쿼리 편집기** 미리 보기 페이지가 열립니다.

     ![도구 메뉴](./media/sql-database-get-started/tools-menu.png) 

2. **쿼리 편집기(미리 보기)**를 클릭하고 **미리 보기 약관** 확인란을 클릭한 후 **확인**을 클릭합니다. 쿼리 편집기가 열립니다.

3. **로그인**을 클릭한 후 메시지가 표시되면 **SQL Server 인증**을 선택한 후 서버 관리자 로그인 및 암호를 제공합니다.
4. **확인**을 클릭하여 로그인합니다.

5. 인증되면 쿼리 창에 다음 쿼리와 같은 원하는 쿼리를 입력합니다.

   ```
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM SalesLT.ProductCategory pc
   JOIN SalesLT.Product p
   ON pc.productcategoryid = p.productcategoryid;
   ```

6. **실행**을 클릭한 후 **결과** 창에서 쿼리 결과를 검토합니다.

    ![쿼리 편집기 결과](./media/sql-database-get-started/query-editor-results.png)

7. **X**를 클릭하여 쿼리 편집기 페이지를 닫습니다.

## <a name="clean-up-resources"></a>리소스 정리

이 컬렉션에서 빠른 시작에 **연결** 및 자습서 컬렉션에서 자습서는 이 빠른 시작을 기반으로 빌드됩니다. 다음 빠른 시작 또는 자습서를 사용하여 계속하려는 경우 이 빠른 시작에서 만든 리소스를 정리하지 않습니다. 계속하지 않으려는 경우 다음 단계에 따라 Azure Portal에서 이 빠른 시작에서 만든 모든 리소스를 삭제합니다.

1. Azure Portal의 왼쪽 메뉴에서 **리소스 그룹**과 **myResourceGroup**을 차례로 클릭합니다. 
2. 리소스 그룹 페이지에서 **삭제**를 클릭하고 텍스트 상자에 **myResourceGroup**을 입력한 후 **삭제**를 클릭합니다.

## <a name="next-steps"></a>다음 단계

- SQL Server Management Studio를 사용하여 연결 및 쿼리하려면 [SSMS를 사용하여 연결 및 쿼리](sql-database-connect-query-ssms.md)를 참조하세요.
- Visual Studio를 사용하여 연결하려면 [Visual Studio를 사용하여 연결 및 쿼리](sql-database-connect-query.md)를 참조하세요.
- SQL Database에 대한 기술적 개요는 [SQL Database 서비스 정보](sql-database-technical-overview.md)를 참조하세요.

