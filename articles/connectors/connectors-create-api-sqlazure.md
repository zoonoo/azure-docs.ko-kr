---
title: SQL Server, Azure SQL Database 또는 Azure SQL에 연결 Managed Instance
description: Azure Logic Apps를 사용하여 온-프레미스 또는 클라우드에서 SQL 데이터베이스에 대한 작업 자동화
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, jonfan, logicappspm
ms.topic: conceptual
ms.date: 06/06/2020
tags: connectors
ms.openlocfilehash: a50a171536d7f81de42da415960398d31ec64827
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91326782"
---
# <a name="automate-workflows-for-a-sql-database-by-using-azure-logic-apps"></a>Azure Logic Apps를 사용 하 여 SQL database에 대 한 워크플로 자동화

이 문서에서는 SQL Server 커넥터가 있는 논리 앱 내에서 SQL 데이터베이스의 데이터에 액세스하는 방법에 대해 설명합니다. 이런 식으로 논리 앱을 만들어 SQL 데이터 및 리소스를 관리하는 작업, 프로세스 및 워크플로를 자동화할 수 있습니다. SQL Server 커넥터는 [SQL Server](/sql/sql-server/sql-server-technical-documentation) 뿐만 아니라 [AZURE SQL DATABASE](../azure-sql/database/sql-database-paas-overview.md) 및 [Azure SQL Managed Instance](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)에 대해 작동 합니다.

SQL 데이터베이스 또는 다른 시스템(예: Dynamics CRM Online)의 이벤트로 트리거될 때 실행할 수 있는 논리 앱을 만들 수 있습니다. 논리 앱은 기존 SQL 쿼리 및 저장 프로시저를 실행하여 데이터를 가져오고 삽입하고 삭제할 수 있습니다. 예를 들어 Dynamics CRM Online에서 새 레코드를 자동으로 확인하고, 새 레코드에 대한 항목을 SQL 데이터베이스에 추가한 다음, 추가된 항목에 관한 메일 경고를 보내는 논리 앱을 만들 수 있습니다.

논리 앱을 처음 접하는 경우 [Azure Logic Apps란?](../logic-apps/logic-apps-overview.md) 및 [빠른 시작: 첫 번째 논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)를 검토하세요. 커넥터 관련 기술 정보, 제한 사항 및 알려진 문제에 대해서는 [SQL Server 커넥터 참조 페이지](/connectors/sql/)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 구독이 없는 경우 [Azure 체험 계정에 등록](https://azure.microsoft.com/free/)합니다.

* [SQL Server 데이터베이스](/sql/relational-databases/databases/create-a-database), [AZURE SQL DATABASE](../azure-sql/database/single-database-create-quickstart.md)또는 [Azure SQL Managed Instance](../azure-sql/managed-instance/instance-create-quickstart.md).

  작업을 호출할 때 논리 앱에서 결과를 반환할 수 있도록 테이블에 데이터가 있어야 합니다. Azure SQL Database 사용 하는 경우 포함 된 예제 데이터베이스를 사용할 수 있습니다.

* SQL 서버 이름, 데이터베이스 이름, 사용자 이름 및 암호. 이러한 자격 증명은 SQL 서버에 액세스할 수 있는 권한을 논리에 부여하는 데 필요합니다.

  * 온-프레미스 SQL Server의 경우 연결 문자열에서 이러한 세부 정보를 찾을 수 있습니다.

    `Server={your-server-address};Database={your-database-name};User Id={your-user-name};Password={your-password};`

  * Azure SQL Database의 경우 연결 문자열에서 이러한 세부 정보를 찾을 수 있습니다.
  
    예를 들어 Azure Portal에서이 문자열을 찾으려면 데이터베이스를 엽니다. 데이터베이스 메뉴에서 **연결 문자열** 또는 **속성**을 선택 합니다.

    `Server=tcp:{your-server-name}.database.windows.net,1433;Initial Catalog={your-database-name};Persist Security Info=False;User ID={your-user-name};Password={your-password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;`

<a name="multi-tenant-or-ise"></a>

* 논리 앱이 전역 다중 테 넌 트 Azure 또는 [ISE (통합 서비스 환경)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)에서 실행 되 고 있는지 여부에 따라 온-프레미스 SQL Server에 연결 하기 위한 다른 요구 사항은 다음과 같습니다.

  * 온-프레미스 SQL Server에 연결 하는 전역 다중 테 넌 트 Azure의 논리 앱의 경우 [온-프레미스 데이터 게이트웨이](../logic-apps/logic-apps-gateway-install.md) 를 로컬 컴퓨터에 설치 하 고 [azure에서 이미 생성 된 데이터 게이트웨이 리소스](../logic-apps/logic-apps-gateway-connection.md)를 포함 해야 합니다.

  * 온-프레미스 SQL Server에 연결 하 고 Windows 인증을 사용 하는 ISE의 논리 앱의 경우 ISE 버전의 SQL Server 커넥터는 Windows 인증을 지원 하지 않습니다. 따라서 데이터 게이트웨이와 비 ISE SQL Server 커넥터를 계속 사용 해야 합니다. 다른 인증 유형의 경우에는 데이터 게이트웨이를 사용할 필요가 없으며 ISE 버전의 커넥터를 사용할 수 있습니다.

* SQL 데이터베이스에 액세스해야 하는 논리 앱. SQL 트리거를 통해 논리 앱을 시작하려면 [빈 논리 앱](../logic-apps/quickstart-create-first-logic-app-workflow.md)이 필요합니다.

<a name="create-connection"></a>

## <a name="connect-to-your-database"></a>데이터베이스 연결

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

이제 다음 단계를 계속 합니다.

* [클라우드 기반 Azure SQL Database 또는 Managed Instance에 연결](#connect-azure-sql-db)
* [온-프레미스 SQL Server에 연결](#connect-sql-server)

<a name="connect-azure-sql-db"></a>

### <a name="connect-to-azure-sql-database-or-managed-instance"></a>Azure SQL Database 또는 Managed Instance에 연결

[Sql 트리거](#add-sql-trigger) 또는 [sql 작업](#add-sql-action)을 처음 추가할 때 이전에 데이터베이스에 대 한 연결을 만들지 않은 경우 다음 단계를 완료 하 라는 메시지가 표시 됩니다.

1. **인증 유형**의 경우 Azure SQL Database 또는 Azure SQL Managed Instance에서 데이터베이스에 대해 필수 및 활성화 된 인증을 선택 합니다.

   | 인증 | Description |
   |----------------|-------------|
   | [**Azure AD 통합**](../azure-sql/database/authentication-aad-overview.md) | -비 ISE 및 ISE SQL Server 커넥터를 모두 지원 합니다. <p><p>-데이터베이스에 액세스할 수 있는 유효한 Azure Active Directory (Azure AD) id가 필요 합니다. <p>자세한 내용은 다음 항목을 참조하세요. <p>- [Azure SQL 보안 개요-인증](../azure-sql/database/security-overview.md#authentication) <br>- [Azure SQL에 대 한 데이터베이스 액세스 권한 부여-인증 및 권한 부여](../azure-sql/database/logins-create-manage.md#authentication-and-authorization) <br>- [Azure SQL-Azure AD 통합 인증](../azure-sql/database/authentication-aad-overview.md) |
   | [**SQL Server 인증**](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication) | -비 ISE 및 ISE SQL Server 커넥터를 모두 지원 합니다. <p><p>-데이터베이스에 생성 되 고 저장 되는 유효한 사용자 이름과 강력한 암호가 필요 합니다. <p>자세한 내용은 다음 항목을 참조하세요. <p>- [Azure SQL 보안 개요-인증](../azure-sql/database/security-overview.md#authentication) <br>- [Azure SQL에 대 한 데이터베이스 액세스 권한 부여-인증 및 권한 부여](../azure-sql/database/logins-create-manage.md#authentication-and-authorization) |
   |||

   이 예제는 **AZURE AD 통합**을 계속 합니다.

   ![열린 "인증 유형" 목록과 "Azure AD 통합"이 선택 된 "SQL Server" 연결 창을 보여 주는 스크린샷](./media/connectors-create-api-sqlazure/select-azure-ad-authentication.png)

1. **AZURE AD 통합**을 선택한 후 **로그인**을 선택 합니다. Azure SQL Database 또는 Azure SQL Managed Instance를 사용 하는지 여부에 따라 인증을 위해 사용자 자격 증명을 선택 합니다.

1. 데이터베이스에 대해 다음 값을 선택 합니다.

   | 속성 | 필수 | Description |
   |----------|----------|-------------|
   | **서버 이름** | 예 | SQL server의 주소입니다 (예:). `Fabrikam-Azure-SQL.database.windows.net` |
   | **데이터베이스 이름** | 예 | SQL 데이터베이스의 이름입니다 (예:). `Fabrikam-Azure-SQL-DB` |
   | **테이블 이름** | 예 | 사용 하려는 테이블 (예:) `SalesLT.Customer` |
   ||||

   > [!TIP]
   > 데이터베이스의 연결 문자열에서이 정보를 찾을 수 있습니다. 예를 들어 Azure Portal에서 데이터베이스를 찾아서 엽니다. 데이터베이스 메뉴에서이 문자열을 찾을 수 있는 **연결 문자열** 또는 **속성** 을 선택 합니다.
   >
   > `Server=tcp:{your-server-address}.database.windows.net,1433;Initial Catalog={your-database-name};Persist Security Info=False;User ID={your-user-name};Password={your-password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;`

   이 예제에서는 이러한 값이 어떻게 표시 되는지 보여 줍니다.

   ![SQL database에 대 한 연결 만들기](./media/connectors-create-api-sqlazure/azure-sql-database-create-connection.png)

1. 이제 [sql 트리거 추가](#add-sql-trigger) 또는 [sql 작업 추가](#add-sql-action)에서 아직 완료 하지 않은 단계를 계속 진행 합니다.

<a name="connect-sql-server"></a>

### <a name="connect-to-on-premises-sql-server"></a>온-프레미스 SQL Server에 연결

[Sql 트리거](#add-sql-trigger) 또는 [sql 작업](#add-sql-action)을 처음 추가할 때 이전에 데이터베이스에 대 한 연결을 만들지 않은 경우 다음 단계를 완료 하 라는 메시지가 표시 됩니다.

1. 온-프레미스 데이터 게이트웨이가 필요한 온-프레미스 SQL server에 연결 하는 경우 [이러한 필수 구성 요소를 완료](#multi-tenant-or-ise)했는지 확인 합니다.

   그렇지 않으면 연결을 만들 때 데이터 게이트웨이 리소스가 **연결 게이트웨이** 목록에 표시 되지 않습니다.

1. **인증 유형**에 대해 필요한 인증을 선택 하 고 SQL Server에 사용 하도록 설정 합니다.

   | 인증 | Description |
   |----------------|-------------|
   | [**Windows 인증**](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-windows-authentication) | -다중 테 넌 트 Azure 또는 ISE를 사용 하는지 여부에 관계 없이 Azure에서 이전에 만든 데이터 게이트웨이 리소스를 필요로 하는 비 ISE SQL Server 커넥터만 지원 합니다. <p><p>-Windows 계정을 통해 id를 확인 하려면 올바른 Windows 사용자 이름 및 암호가 필요 합니다. <p>자세한 내용은 [Windows 인증](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-windows-authentication) 을 참조 하세요. |
   | [**SQL Server 인증**](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication) | -비 ISE 및 ISE SQL Server 커넥터를 모두 지원 합니다. <p><p>-SQL Server에 생성 되 고 저장 되는 유효한 사용자 이름과 강력한 암호가 필요 합니다. <p>자세한 내용은 [SQL Server 인증](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication)을 참조 하세요. |
   |||

   이 예제는 **Windows 인증**을 사용 하 여 계속 합니다.

   ![사용할 인증 유형 선택](./media/connectors-create-api-sqlazure/select-windows-authentication.png)

1. SQL 데이터베이스에 대해 다음 값을 선택 하거나 제공 합니다.

   | 속성 | 필수 | Description |
   |----------|----------|-------------|
   | **SQL server 이름** | 예 | SQL server의 주소입니다 (예:). `Fabrikam-Azure-SQL.database.windows.net` |
   | **SQL 데이터베이스 이름** | 예 | SQL Server 데이터베이스의 이름입니다 (예:). `Fabrikam-Azure-SQL-DB` |
   | **사용자 이름** | 예 | SQL server 및 데이터베이스의 사용자 이름 |
   | **암호** | 예 | SQL server 및 데이터베이스의 암호 |
   | **구독** |  예, Windows 인증의 경우 | Azure에서 이전에 만든 데이터 게이트웨이 리소스에 대 한 Azure 구독 |
   | **연결 게이트웨이** | 예, Windows 인증의 경우 | Azure에서 이전에 만든 데이터 게이트웨이 리소스의 이름 <p><p>**팁**: 게이트웨이가 목록에 표시 되지 않으면 게이트웨이를 올바르게 [설정](../logic-apps/logic-apps-gateway-connection.md)했는지 확인 합니다. |
   |||

   > [!TIP]
   > 데이터베이스의 연결 문자열에서이 정보를 찾을 수 있습니다.
   > 
   > * `Server={your-server-address}`
   > * `Database={your-database-name}`
   > * `User ID={your-user-name}`
   > * `Password={your-password}`

   이 예제에서는 이러한 값이 어떻게 표시 되는지 보여 줍니다.

   ![SQL Server 연결 만들기 완료](./media/connectors-create-api-sqlazure/sql-server-create-connection-complete.png)

1. 준비가 되면 **만들기**를 선택합니다.

1. 이제 [sql 트리거 추가](#add-sql-trigger) 또는 [sql 작업 추가](#add-sql-action)에서 아직 완료 하지 않은 단계를 계속 진행 합니다.

<a name="add-sql-trigger"></a>

## <a name="add-a-sql-trigger"></a>SQL 트리거 추가

1. [Azure Portal](https://portal.azure.com) 또는 Visual Studio에서 논리 앱 디자이너를 여는 빈 논리 앱을 만듭니다. 이 예에서는 Azure Portal을 계속 진행 합니다.

1. 디자이너의 검색 상자에을 입력 `sql server` 합니다. 트리거 목록에서 원하는 SQL 트리거를 선택합니다. 이 예제에서는 **항목을 만들 때** 트리거를 사용 합니다.

   ![“항목이 만들어진 경우” 트리거 선택](./media/connectors-create-api-sqlazure/select-sql-server-trigger.png)

1. 처음으로 SQL database에 연결 하는 경우 [sql database 연결](#create-connection)을 만들라는 메시지가 표시 됩니다. 이 연결을 만든 후에는 다음 단계를 계속 진행할 수 있습니다.

1. 트리거에서 트리거가 테이블을 검사 하는 빈도에 대 한 간격 및 빈도를 지정 합니다.

1. 이 트리거에 사용할 수 있는 다른 속성을 추가 하려면 **새 매개 변수 추가** 목록을 엽니다.

   이 트리거는 선택한 테이블에서 한 개의 행만 반환 하 고 다른 행은 반환 하지 않습니다. 다른 작업을 수행 하려면 논리 앱 워크플로에서 원하는 다음 작업을 수행 하는 [다른](../connectors/apis-list.md) 작업 또는 [SQL connector 작업](#add-sql-action) 을 추가 하 여 계속 합니다.
   
   예를 들어 이 행의 데이터를 보려면 반환된 행의 필드를 포함하는 파일을 만드는 다른 작업을 추가하고 메일 알림을 보낼 수 있습니다. 이 커넥터에 대해 사용할 수 있는 다른 작업에 대해 알아보려면 [커넥터의 참조 페이지](/connectors/sql/)를 참조하세요.

1. 디자이너 도구 모음에서 **저장**을 선택합니다.

   이 단계에서는 논리 앱을 자동으로 사용 하도록 설정 하 고 Azure에 게시 하지만, 논리 앱에서 현재 사용 하는 유일한 동작은 지정 된 간격 및 빈도에 따라 데이터베이스를 확인 하는 것입니다.

<a name="add-sql-action"></a>

## <a name="add-a-sql-action"></a>SQL 작업 추가

이 예에서는 논리 앱은 [되풀이 트리거](../connectors/connectors-native-recurrence.md)를 통해 시작하고 SQL 데이터베이스에서 행을 가져오는 작업을 호출합니다.

1. [Azure Portal](https://portal.azure.com) 또는 Visual Studio의 논리 앱 디자이너에서 논리 앱을 엽니다. 이 예에서는 Azure Portal을 계속 진행 합니다.

1. SQL 작업을 추가하려는 트리거 또는 작업에서 **새 단계**를 선택합니다.

   ![논리 앱에 작업 추가](./media/connectors-create-api-sqlazure/select-new-step-logic-app.png)

   또는 기존 단계 사이에 작업을 추가 하려면 연결 하는 화살표 위로 마우스를 이동 합니다. 표시되는 더하기 기호( **+** )를 선택한 다음, **작업 추가**를 선택합니다.

1. **작업 선택** 아래의 검색 상자에 `sql server`을 입력합니다. 작업 목록에서 원하는 SQL 작업을 선택합니다. 이 예에서는 단일 레코드를 가져오는 **행 가져오기** 작업을 사용합니다.

   ![SQL "행 가져오기" 작업을 선택 합니다.](./media/connectors-create-api-sqlazure/select-sql-get-row-action.png)

1. 처음으로 SQL database에 연결 하는 경우 [sql database 연결](#create-connection)을 만들라는 메시지가 표시 됩니다. 이 연결을 만든 후에는 다음 단계를 계속 진행할 수 있습니다.

1. 이 예에서는 **테이블 이름을**선택 `SalesLT.Customer` 합니다. 원하는 레코드의 **행 ID** 를 입력 합니다.

   ![테이블 이름을 선택 하 고 행 ID를 지정 합니다.](./media/connectors-create-api-sqlazure/specify-table-row-id.png)

   이 작업에서는 선택한 테이블에서 한 행만 반환하며 그 외에는 아무것도 반환하지 않습니다. 따라서이 행의 데이터를 보려면 반환 된 행에서 필드를 포함 하는 파일을 만드는 다른 작업을 추가 하 고 클라우드 저장소 계정에 해당 파일을 저장 합니다. 이 커넥터에 대해 사용할 수 있는 다른 작업에 대해 알아보려면 [커넥터의 참조 페이지](/connectors/sql/)를 참조하세요.

1. 완료되면 디자이너 도구 모음에서 **저장**을 선택합니다.

   이 단계에서는 Azure에서 사용할 수 있는 논리 앱을 자동으로 활성화하고 게시합니다.

## <a name="handle-bulk-data"></a>대량 데이터 처리

커넥터에서 모든 결과를 동시에 반환할 수 없을 만큼 너무 큰 결과 집합을 사용하거나 결과 집합의 크기와 구조를 더 효율적으로 제어하려는 경우가 있습니다. 이러한 큰 결과 집합을 처리할 수 있는 몇 가지 방법은 다음과 같습니다.

* 더 작은 집합으로 결과를 관리하는 데 도움이 있도록 *페이지 매김*을 설정합니다. 자세한 내용은 [페이지 매김을 사용하여 대량 데이터, 레코드 및 항목 가져오기](../logic-apps/logic-apps-exceed-default-page-size-with-pagination.md)를 참조하세요.

* 원하는 방식으로 결과를 구성하는 저장 프로시저를 만듭니다.

  여러 행을 가져오거나 삽입하는 경우 논리 앱은 이 [제한](../logic-apps/logic-apps-limits-and-config.md) 내에서 [‘until 루프’](../logic-apps/logic-apps-control-flow-loops.md#until-loop)를 사용하여 이러한 행을 반복할 수 있습니다. 하지만 논리 앱에서 수천 또는 수백만 개의 행과 같이 너무 큰 레코드 집합을 사용하여 데이터베이스에 대한 호출로 인한 비용을 최소화하려는 경우가 있습니다.

  원하는 방식으로 결과를 구성하려면 SQL 인스턴스에서 실행되는 [‘저장 프로시저’](/sql/relational-databases/stored-procedures/stored-procedures-database-engine)를 만들고, **SELECT - ORDER BY** 문을 사용할 수 있습니다. 이 솔루션을 사용하면 결과의 크기와 구조를 더 많이 제어할 수 있습니다. 논리 앱은 SQL Server 커넥터의 **저장 프로시저 실행** 작업을 사용하여 저장 프로시저를 호출합니다.

  솔루션에 대한 자세한 내용은 다음 문서를 참조하세요.

  * [Logic Apps를 사용한 대량 데이터 전송에 대한 SQL 페이지 매김](https://social.technet.microsoft.com/wiki/contents/articles/40060.sql-pagination-for-bulk-data-transfer-with-logic-apps.aspx)

  * [SELECT - ORDER BY 절](/sql/t-sql/queries/select-order-by-clause-transact-sql)

### <a name="handle-dynamic-bulk-data"></a>동적 대량 데이터 처리

SQL Server 커넥터를 사용 하 여 저장 프로시저를 호출 하는 경우 반환 되는 출력은 종종 동적입니다. 이 시나리오에서는 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)의 Logic Apps 디자이너에서 논리 앱을 엽니다.

1. 테스트 실행을 수행 하 여 출력 형식을 봅니다. 샘플 출력을 복사 하 고 저장 합니다.

1. 디자이너에서 저장 프로시저를 호출하는 작업 아래에서 **새 단계**를 선택합니다.

1. **작업 선택**에서 [**JSON 구문 분석**](../logic-apps/logic-apps-perform-data-operations.md#parse-json-action) 작업을 찾아 선택 합니다.

1. **JSON 구문 분석** 작업에서 **샘플 페이로드를 사용하여 스키마 생성**을 선택합니다.

1. **샘플 JSON 페이로드 입력 또는 붙여넣기** 상자에 샘플 출력을 붙여넣고 **완료**를 선택 합니다.

   > [!NOTE]
   > Logic Apps가 스키마를 생성할 수 없다는 오류 메시지가 표시되는 경우 샘플 출력의 구문의 형식이 올바르게 지정되었는지 확인합니다. 여전히 스키마를 생성할 수 없는 경우 **스키마 상자에서** 스키마를 수동으로 입력 합니다.

1. 디자이너 도구 모음에서 **저장**을 선택합니다.

1. JSON 콘텐츠 속성을 참조 하려면 동적 콘텐츠 목록이 표시 되도록 해당 속성을 참조 하려는 편집 상자 내부를 클릭 합니다. 목록의 [**Json 구문 분석**](../logic-apps/logic-apps-perform-data-operations.md#parse-json-action) 제목 아래에서 원하는 json 콘텐츠 속성에 대 한 데이터 토큰을 선택 합니다.

## <a name="connector-specific-details"></a>커넥터 관련 세부 정보

이 커넥터의 트리거, 작업 및 제한에 대 한 기술 정보는 Swagger 설명에서 생성 된 [커넥터의 참조 페이지](/connectors/sql/)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

* [Azure Logic Apps용 다른 커넥터](../connectors/apis-list.md)에 대해 자세히 알아보기

