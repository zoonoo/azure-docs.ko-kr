---
title: SQL 서버 또는 Azure SQL 데이터베이스에 연결
description: Azure Logic Apps를 사용하여 온프레미스 또는 클라우드에서 SQL 데이터베이스에 대한 작업을 자동화합니다.
services: logic-apps
ms.suite: integration
ms.reviewer: klam; logicappspm
ms.topic: conceptual
ms.date: 11/08/2019
tags: connectors
ms.openlocfilehash: 93b63d332f00c31a352c11e483fc3ce5cb45a922
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789201"
---
# <a name="automate-workflows-for-sql-server-or-azure-sql-database-by-using-azure-logic-apps"></a>Azure 논리 앱을 사용하여 SQL Server 또는 Azure SQL 데이터베이스에 대한 워크플로 자동화

이 문서에서는 SQL Server 커넥터가 있는 논리 앱 내에서 SQL 데이터베이스의 데이터에 액세스하는 방법에 대해 설명합니다. 이렇게 하면 논리 앱을 만들어 SQL 데이터 및 리소스를 관리하는 작업, 프로세스 또는 워크플로를 자동화할 수 있습니다. SQL Server 커넥터는 [온-프레미스 SQL Server](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation) 및 [클라우드 기반 Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview)모두에서 작동합니다.

SQL 데이터베이스 또는 다른 시스템(예: Dynamics CRM Online)의 이벤트로 트리거될 때 실행할 수 있는 논리 앱을 만들 수 있습니다. 논리 앱은 SQL 쿼리 및 저장 프로시저 실행과 함께 데이터를 다운로드, 삽입 및 삭제할 수도 있습니다. 예를 들어 Dynamics CRM Online에서 새 레코드를 자동으로 확인하고 새 레코드에 대한 항목을 SQL 데이터베이스에 추가한 다음 추가된 항목에 대한 전자 메일 경고를 보내는 논리 앱을 빌드할 수 있습니다.

논리 앱을 처음 사용하는 경우 [Azure Logic Apps](../logic-apps/logic-apps-overview.md) 및 [빠른 시작: 첫 번째 논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)를 검토합니다. 커넥터 관련 기술 정보, 제한 사항 및 알려진 문제에 대해서는 [SQL Server 커넥터 참조 페이지를](https://docs.microsoft.com/connectors/sql/)참조하십시오.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 구독이 없는 경우 [Azure 체험 계정에 등록](https://azure.microsoft.com/free/)합니다.

* [SQL Server 데이터베이스](https://docs.microsoft.com/sql/relational-databases/databases/create-a-database) 또는 Azure SQL [데이터베이스](../sql-database/sql-database-get-started-portal.md)

  작업을 호출할 때 논리 앱에서 결과를 반환할 수 있도록 테이블에 데이터가 있어야 합니다. Azure SQL Database를 만드는 경우 포함된 샘플 데이터베이스를 사용할 수 있습니다.

* SQL 서버 이름, 데이터베이스 이름, 사용자 이름 및 암호. 이러한 자격 증명은 SQL 서버에 액세스할 수 있는 권한을 논리에 부여하는 데 필요합니다.

  * SQL Server의 경우 연결 문자열에서 다음과 같은 세부 정보를 찾을 수 있습니다.

    `Server={your-server-address};Database={your-database-name};User Id={your-user-name};Password={your-password};`

  * Azure SQL Database의 경우 연결 문자열 또는 SQL Database 속성 아래의 Azure Portal에서 다음과 같은 세부 정보를 찾을 수 있습니다.

    `Server=tcp:{your-server-name}.database.windows.net,1433;Initial Catalog={your-database-name};Persist Security Info=False;User ID={your-user-name};Password={your-password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;`

* 로컬 컴퓨터에 설치된 [온-프레미스 데이터 게이트웨이](../logic-apps/logic-apps-gateway-install.md) 및 이러한 시나리오에 대해 [Azure 포털에서 만든 Azure 데이터 게이트웨이 리소스:](../logic-apps/logic-apps-gateway-connection.md)

  * 논리 앱은 [ISE(통합 서비스 환경)에서](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)실행되지 않습니다.

  * 논리 *앱은* 통합 서비스 환경에서 실행되지만 SQL Server 연결에 Windows 인증을 사용해야 합니다. 이 시나리오에서는 ISE 버전이 Windows 인증을 지원하지 않으므로 데이터 게이트웨이와 함께 SQL Server 커넥터의 비 ISE 버전을 사용합니다.

* SQL 데이터베이스에 액세스해야 하는 논리 앱. SQL 트리거를 통해 논리 앱을 시작하려면 [빈 논리 앱](../logic-apps/quickstart-create-first-logic-app-workflow.md)이 필요합니다.

<a name="add-sql-trigger"></a>

## <a name="add-a-sql-trigger"></a>SQL 트리거 추가

Azure Logic Apps에서 모든 논리 앱은 특정 이벤트가 발생하거나 특정 조건이 충족할 때 실행되는 [트리거](../logic-apps/logic-apps-overview.md#logic-app-concepts)를 통해 시작되어야 합니다. 트리거가 발생할 때마다 Logic Apps 엔진은 논리 앱 인스턴스를 만들고 논리 앱의 워크플로를 실행하기 시작합니다.

1. Azure Portal 또는 Visual Studio에서 빈 논리 앱을 만들어 논리 앱 디자이너를 엽니다. 이 예에서는 Azure Portal을 사용합니다.

1. 디자이너에서 검색 상자에 "sql server"를 필터로 입력합니다. 트리거 목록에서 원하는 SQL 트리거를 선택합니다.

   이 예제에서는 **항목이 생성될 때 트리거를** 사용합니다.

   !['아이템이 생성된 시' 트리거 선택](./media/connectors-create-api-sqlazure/select-sql-server-trigger.png)

1. 연결을 만들라는 메시지가 표시되면 [지금 SQL 연결을 만듭니다.](#create-connection) 연결이 있는 경우 **테이블 이름을**선택합니다.

   ![원하는 테이블을 선택합니다.](./media/connectors-create-api-sqlazure/azure-sql-database-table.png)

1. 논리 앱에서 테이블을 확인하는 빈도를 지정하는 **간격** 및 **빈도** 속성을 설정합니다.

   이 트리거는 선택한 테이블에서 하나의 행만 반환합니다. 다른 작업을 수행하려면 원하는 작업을 수행하는 다른 작업을 추가합니다. 예를 들어 이 행의 데이터를 보려면 반환된 행의 필드를 포함하는 파일을 만드는 다른 작업을 추가한 다음 전자 메일 경고를 보낼 수 있습니다. 이 커넥터에 대해 사용 가능한 다른 작업에 대한 자세한 내용은 [커넥터의 참조 페이지를](https://docs.microsoft.com/connectors/sql/)참조하십시오.

1. 작업이 완료되면 디자이너 도구 모음에서 **저장을**선택합니다.

   이 단계에서는 Azure에서 사용할 수 있는 논리 앱을 자동으로 활성화하고 게시합니다.

<a name="add-sql-action"></a>

## <a name="add-a-sql-action"></a>SQL 작업 추가

Azure Logic Apps에서 [작업](../logic-apps/logic-apps-overview.md#logic-app-concepts)은 트리거 또는 다른 작업을 수행하는 워크플로의 한 단계입니다. 이 예에서는 논리 앱은 [되풀이 트리거](../connectors/connectors-native-recurrence.md)를 통해 시작하고 SQL 데이터베이스에서 행을 가져오는 작업을 호출합니다.

1. Azure Portal 또는 Visual Studio에서 논리 앱 디자이너에서 논리 앱을 엽니다. 이 예에서는 Azure Portal을 사용합니다.

1. SQL 작업을 추가할 트리거 또는 작업에서 새 **단계를**선택합니다.

   ![논리 앱에 새 단계 추가](./media/connectors-create-api-sqlazure/select-new-step-logic-app.png)

   기존 단계 간에 작업을 추가하려면 연결 화살표 위로 마우스를 이동합니다. 표시되는 더하기**+** 기호 ()를 선택한 다음 **작업 추가를**선택합니다.

1. **작업 선택에서**검색 상자에 "sql server"를 필터로 입력합니다. 작업 목록에서 원하는 SQL 작업을 선택합니다.

   이 예제에서는 단일 레코드를 가져옵니다 행 **받기** 작업을 사용합니다.

   ![SQL "행 받기" 작업 찾기 및 선택](./media/connectors-create-api-sqlazure/find-select-sql-get-row-action.png)

   이 작업은 선택한 테이블에서 하나의 행만 반환합니다. 이 행의 데이터를 보려면 반환된 행의 필드를 포함하는 파일을 만드는 다른 작업을 추가하고 해당 파일을 클라우드 저장소 계정에 저장할 수 있습니다. 이 커넥터에 대해 사용 가능한 다른 작업에 대한 자세한 내용은 [커넥터의 참조 페이지를](https://docs.microsoft.com/connectors/sql/)참조하십시오.

1. 연결을 만들라는 메시지가 표시되면 [지금 SQL 연결을 만듭니다.](#create-connection) 연결이 있는 경우 **테이블 이름을**선택하고 원하는 레코드의 **행 ID를** 입력합니다.

   ![테이블 이름 및 행 ID 입력](./media/connectors-create-api-sqlazure/specify-table-row-id-property-value.png)

1. 작업이 완료되면 디자이너 도구 모음에서 **저장을**선택합니다.

   이 단계에서는 Azure에서 사용할 수 있는 논리 앱을 자동으로 활성화하고 게시합니다.

<a name="create-connection"></a>

## <a name="connect-to-your-database"></a>데이터베이스 연결

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Create a connection to SQL Server or Azure SQL Database](../../includes/connectors-create-api-sqlazure.md)]

## <a name="handle-bulk-data"></a>대량 데이터 처리

경우에 따라 커넥터가 모든 결과를 동시에 반환하지 않거나 결과 집합의 크기와 구조를 더 잘 제어할 수 있도록 결과 집합을 너무 크게 사용하여 작업해야 하는 경우가 있습니다. 이러한 대규모 결과 집합을 처리할 수 있는 몇 가지 방법은 다음과 같습니다.

* 결과를 더 작은 집합으로 관리하려면 *페이지 매김을*켭니다. 자세한 내용은 [페이지 조정을 사용하여 대량 데이터, 레코드 및 항목 받기를](../logic-apps/logic-apps-exceed-default-page-size-with-pagination.md)참조하십시오.

* 원하는 방식으로 결과를 구성하는 저장 프로시저를 만듭니다.

  여러 행을 얻거나 삽입할 때 논리 앱은 이러한 [제한](../logic-apps/logic-apps-limits-and-config.md)내에서 [*until 루프를*](../logic-apps/logic-apps-control-flow-loops.md#until-loop) 사용하여 이러한 행을 반복할 수 있습니다. 그러나 논리 앱이 레코드 집합(예: 수천 또는 수백만 개의 행)으로 작업해야 하는 경우 데이터베이스 호출로 인한 비용을 최소화하려고 합니다.

  원하는 방식으로 결과를 구성하려면 SQL 인스턴스에서 실행되고 **SELECT - ORDER BY** 문을 사용하는 저장 [*프로시저를*](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) 만들 수 있습니다. 이 솔루션을 사용하면 결과의 크기와 구조를 더 많이 제어할 수 있습니다. 논리 앱은 SQL Server 커넥터의 **저장 프로시저 실행** 작업을 사용하여 저장 프로시저를 호출합니다.

  자세한 솔루션 세부 정보는 다음 문서를 참조하세요.

  * [Logic Apps를 사용한 대량 데이터 전송에 대한 SQL 페이지 매김](https://social.technet.microsoft.com/wiki/contents/articles/40060.sql-pagination-for-bulk-data-transfer-with-logic-apps.aspx)

  * [SELECT - ORDER BY 절](https://docs.microsoft.com/sql/t-sql/queries/select-order-by-clause-transact-sql)

## <a name="connector-specific-details"></a>커넥터 관련 세부 정보

이 커넥터의 트리거, 작업 및 제한에 대한 기술 정보는 [커넥터의 참조 페이지를](https://docs.microsoft.com/connectors/sql/)참조하십시오.

## <a name="next-steps"></a>다음 단계

* [Azure 논리 앱의](../connectors/apis-list.md) 다른 커넥터에 대해 알아보기
