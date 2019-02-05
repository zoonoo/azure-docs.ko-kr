---
title: 'Azure Portal: SQL Database 방화벽 규칙 만들기 | Microsoft Docs'
description: SQL Database 서버 수준 방화벽 규칙 만들기
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 589d3fa8c0ee8c8f374cd4f34f17401caa46d265
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55462259"
---
# <a name="quickstart-create-a-server-level-firewall-rule-for-your-sql-database-using-the-azure-portal"></a>빠른 시작: Azure Portal을 사용하여 SQL 데이터베이스에 대한 서버 수준 방화벽 규칙 만들기

이 빠른 시작에서는 온-프레미스 리소스에서 데이터베이스에 연결할 수 있도록 Azure SQL 데이터베이스에 대한 서버 수준 방화벽 규칙을 만드는 방법을 안내합니다.

## <a name="prerequisites"></a>필수 조건

이 빠른 시작에서는 [Azure Portal에서 Azure SQL 데이터베이스 만들기](sql-database-get-started-portal.md)에서 만든 리소스를 시작 지점으로 사용합니다.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure Portal](https://portal.azure.com/)에 로그인합니다.

## <a name="create-a-server-level-firewall-rule"></a>서버 수준 방화벽 규칙 만들기

SQL Database 서비스는 서버 수준에서 방화벽을 만듭니다. 방화벽을 여는 방화벽 규칙을 만들지 않는 한 이 방화벽은 애플리케이션과 도구에서 서버 또는 서버 데이터베이스에 연결하지 못하도록 차단합니다. Azure 외부의 IP 주소에서 연결하려면 특정 IP 주소 또는 주소 범위에 대한 방화벽 규칙을 만듭니다. 방화벽 규칙에 대한 자세한 내용은 [SQL Database 서버 수준 방화벽 규칙](sql-database-firewall-configure.md)을 참조하세요.

> [!NOTE]
> SQL Database는 포트 1433을 통해 통신합니다. 회사 네트워크 내에서 연결하려는 경우 1433 포트를 통한 아웃바운드 트래픽이 네트워크 방화벽에서 허용되지 않을 수 있습니다. 이 경우 IT 부서에서 1433 포트를 열지 않으면 Azure SQL Database 서버에 연결할 수 없습니다.
>

다음 단계에 따라 클라이언트의 IP 주소에 대한 서버 수준 방화벽 규칙을 만들고, IP 주소에 대해서만 SQL Database 방화벽을 통해 외부 연결을 사용하도록 설정합니다.

1. [필수 조건의 Azure SQL 데이터베이스](#prerequisites) 배포가 완료되면 왼쪽 메뉴에서 **SQL 데이터베이스**를 선택한 다음, **SQL 데이터베이스** 페이지에서 **mySampleDatabase**를 선택합니다. 데이터베이스에 대한 개요 페이지가 열려 정규화된 서버 이름(예: **mynewserver-20170824.database.windows.net**)을 표시하고 추가 구성을 위한 옵션을 제공합니다.

2. 다른 빠른 시작에서 서버 및 해당 데이터베이스에 연결하는 데 사용하기 위해 이 정규화된 서버 이름을 복사합니다.

   ![서버 이름](./media/sql-database-get-started-portal/server-name.png)

3. 도구 모음에서 **서버 방화벽 설정**을 선택합니다. SQL Database 서버에 대한 **방화벽 설정** 페이지가 열립니다.

   ![서버 방화벽 규칙](./media/sql-database-get-started-portal/server-firewall-rule.png)

4. 도구 모음에서 **클라이언트 IP 추가**를 선택하여 현재 IP 주소를 새 방화벽 규칙에 추가합니다. 방화벽 규칙은 단일 IP 주소 또는 IP 주소의 범위에 1433 포트를 열 수 있습니다.

   > [!IMPORTANT]
   > SQL Database 방화벽을 통한 액세스는 기본적으로 모든 Azure 서비스에 대해 사용됩니다. 이 페이지에서 **끄기**를 선택하여 모든 Azure 서비스에 대해 사용하지 않도록 설정합니다.
   >

5. **저장**을 선택합니다. SQL Database 서버의 1433 포트를 여는 현재 IP 주소에 서버 수준 방화벽 규칙이 생성됩니다.

6. **방화벽 설정** 페이지를 닫습니다.

이제 SQL Server Management Studio 또는 원하는 다른 도구를 사용하여 이전에 만든 서버 관리자 계정을 통해 이 IP 주소에서 SQL Database 서버 및 해당 데이터베이스에 연결할 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

[다음 단계](#next-steps)로 이동하여 다양한 방법으로 데이터베이스에 연결하고 쿼리하는 방법을 알아보려면 이러한 리소스를 저장합니다. 그러나 이 빠른 시작에서 만든 리소스를 삭제하려면 다음 단계를 사용합니다.

1. Azure Portal의 왼쪽 메뉴에서 **리소스 그룹**을 선택한 다음, **myResourceGroup**을 선택합니다.
2. 리소스 그룹 페이지에서 **삭제**를 선택하고, 텍스트 상자에서 **myResourceGroup**을 입력한 다음, **삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

- 이제 데이터베이스가 생겼으니, 다음을 포함하여 자주 사용하는 도구 또는 언어 중 하나를 사용하여 [연결하고 쿼리](sql-database-connect-query.md)할 수 있습니다.
  - [SQL Server Management Studio를 사용하여 연결 및 쿼리](sql-database-connect-query-ssms.md)
  - [Azure Data Studio를 사용하여 연결 및 쿼리](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- 첫 번째 데이터베이스를 디자인하고, 테이블을 만들고, 데이터를 삽입하는 방법을 알아보려면 다음 자습서 중 하나를 참조하세요.
  - [SSMS를 사용하여 첫 번째 Azure SQL Database 디자인](sql-database-design-first-database.md)
  - [Azure SQL Database 설계 및 C#과 ADO.NET에 연결T](sql-database-design-first-database-csharp.md)
