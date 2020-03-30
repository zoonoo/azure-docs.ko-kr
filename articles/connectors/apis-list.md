---
title: Azure Logic Apps용 커넥터
description: 기본 제공, 관리형, 온-프레미스, 통합 계정, ISE 및 엔터프라이즈 커넥터와 같은 Azure Logic Apps용 커넥터로 워크플로 자동화
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 03/05/2020
ms.openlocfilehash: 3010f3c99a5b214c2503f890321cbb73427e3c20
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247333"
---
# <a name="connectors-for-azure-logic-apps"></a>Azure Logic Apps용 커넥터

커넥터를 사용하면 Azure Logic Apps에서 다른 앱, 서비스, 시스템, 프로토콜 및 플랫폼에 걸쳐 이벤트, 데이터 및 작업에 빠르게 액세스할 수 있습니다. 이미 만들어 사용하고 있는 데이터로 작업을 수행할 수 있도록, 논리 앱에서 커넥터를 사용하여 클라우드 및 온-프레미스 앱에 대한 기능을 확장합니다.

Logic Apps는 [수백 개의 커넥터를](https://docs.microsoft.com/connectors)제공하지만 이 문서에서는 수천 개의 *앱과* 데이터 및 정보 처리를 위해 수백만 번의 실행에서 성공적으로 사용되는 인기 있고 일반적으로 사용되는 커넥터에 대해 설명합니다. 트리거, 작업 및 제한과 같은 커넥터 및 각 커넥터의 참조 정보의 전체 목록을 찾으려면 커넥터 개요 아래의 커넥터 참조 페이지를 [검토하십시오.](https://docs.microsoft.com/connectors) 또한 [트리거 및 작업,](#triggers-actions) [논리 앱 가격 책정 모델](../logic-apps/logic-apps-pricing.md)및 논리 앱 가격 [세부 정보에](https://azure.microsoft.com/pricing/details/logic-apps/)대해 자세히 알아봅니다.

> [!TIP]
> 커넥터가 없는 서비스 또는 API와 통합하려면 HTTP와 같은 프로토콜을 통해 서비스를 직접 호출하거나 [사용자 지정 커넥터를](#custom)만들 수 있습니다.

## <a name="connector-types"></a>커넥터 유형

커넥터는 기본 제공 트리거 및 작업 또는 관리형 커넥터로 사용할 수 있습니다.

<a name="built-in"></a>

* [**기본 제공**](#built-ins): 기본 제공 트리거 및 작업은 Azure Logic Apps에 "기본"이며 논리 앱에 대해 이러한 작업을 수행하는 데 도움이 됩니다.

  * 사용자 지정 및 고급 일정에서 실행합니다.

  * 루프 및 조건과 같은 논리 앱의 워크플로를 구성 및 제어하고 변수 및 데이터 작업으로 작업할 수도 있습니다.

  * 다른 끝점과 통신합니다.

  * 요청을 수신하고 응답합니다.

  * Azure 함수, Azure API 앱(웹 앱), Azure API 관리로 관리 및 게시된 사용자 고유의 API 및 요청을 받을 수 있는 중첩논리 앱을 호출합니다.

<a name="managed-connectors"></a>

* [**관리형 커넥터**](#managed-api-connectors): Microsoft에서 배포 및 관리하는 이 커넥터는 Office 365, Azure Blob Storage, SQL Server, Dynamics, Salesforce, SharePoint 등을 포함하여 클라우드 서비스, 온-프레미스 시스템 또는 둘 다에 액세스하기 위한 트리거 및 작업을 제공합니다. 일부 커넥터는 특히 B2B(비즈니스 간) 통신 시나리오를 지원하며 논리 앱에 연결된 [통합 계정이](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) 필요합니다. 특정 커넥터를 사용하기 전에 먼저 Azure Logic Apps에서 관리하는 연결을 만들어야 할 수 있습니다.

  예를 들어 Microsoft BizTalk Server를 사용하는 경우 논리 앱은 BizTalk Server [온-프레미스 커넥터를](#on-premises-connectors)사용하여 BizTalk 서버에 연결하고 통신할 수 있습니다. 그런 다음, [통합 계정 커넥터](#integration-account-connectors)를 사용하여 논리 앱에서 BizTalk와 비슷한 작업을 확장하거나 수행할 수 있습니다.

  커넥터는 표준 또는 엔터프라이즈로 분류됩니다. [엔터프라이즈 커넥터는](#enterprise-connectors) 추가 비용으로 SAP, IBM MQ 및 IBM 3270과 같은 엔터프라이즈 시스템에 대한 액세스를 제공합니다. 커넥터가 표준 또는 엔터프라이즈인지 확인하려면 [커넥터 개요](https://docs.microsoft.com/connectors)아래의 각 커넥터 참조 페이지의 기술 세부 정보를 참조하십시오.

  일부 커넥터는 여러 범주를 교차할 수 있지만 이러한 범주를 사용하여 커넥터를 식별할 수도 있습니다. 예를 들어 SAP는 엔터프라이즈 커넥터및 온-프레미스 커넥터입니다.

  |   |   |
  |---|---|
  | [**관리형 커넥터**](#managed-api-connectors) | Azure Blob Storage, Office 365, Dynamics, Power BI, OneDrive, Salesforce, SharePoint Online 등과 같은 서비스를 사용하는 논리 앱을 만듭니다. |
  | [**온-프레미스 커넥터**](#on-premises-connectors) | [온-프레미스 데이터 게이트웨이][gateway-doc]가 설치 및 설정되면, 이러한 커넥터를 통해 논리 앱에서 온-프레미스 시스템(예: SQL Server, SharePoint Server, Oracle DB, 파일 공유 등)에 액세스할 수 있습니다. |
  | [**통합 계정 커넥터**](#integration-account-connectors) | 통합 계정을 만들고 지불할 때 사용할 수 있는 이러한 커넥터는 AS2, EDIFACT 및 X12 프로토콜을 사용하여 XML 변환 및 유효성 검사, 플랫 파일 인코딩 및 디코딩, B2B(Business-to-Business) 메시지 처리를 수행합니다. |
  |||

<a name="integration-service-environment"></a>

### <a name="connect-from-an-integration-service-environment"></a>통합 서비스 환경에서 연결

Azure 가상 네트워크의 리소스에 직접 액세스해야 하는 논리 앱의 경우 전용 리소스에서 논리 앱을 빌드, 배포 및 실행할 수 있는 격리된 [통합 서비스 환경(ISE)을](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) 만들 수 있습니다. 논리 앱 디자이너에서 ISE의 논리 앱에 사용할 커넥터를 탐색할 때 **CORE** 레이블이 기본 제공 트리거 및 작업에 나타나고 **ISE** 레이블은 일부 커넥터에 나타납니다.

* **CORE**: 이 레이블이 있는 기본 제공 트리거 및 작업은 논리 앱과 동일한 ISE에서 실행됩니다.

  ![ISE 커넥터 예제](./media/apis-list/example-core-connector.png)

* **ISE**: 이 레이블이 있는 관리형 커넥터는 논리 앱과 동일한 ISE에서 실행됩니다.

  ![ISE 커넥터 예제](./media/apis-list/example-ise-connector.png)

  Azure 가상 네트워크에 연결된 온-프레미스 시스템이 있는 경우 ISE를 사용하면 논리 앱이 [온-프레미스 데이터 게이트웨이](../logic-apps/logic-apps-gateway-connection.md)없이 해당 시스템에 직접 액세스할 수 있습니다. 대신 사용 가능한 경우 해당 시스템의 **ISE** 커넥터, HTTP 작업 또는 [사용자 지정 커넥터를](#custom)사용할 수 있습니다. **ISE** 커넥터가 없는 온-프레미스 시스템의 경우 온-프레미스 데이터 게이트웨이를 사용합니다. 사용 가능한 ISE 커넥터를 검토하려면 [ISE 커넥터를](#ise-connectors)참조하십시오.

* **CORE** 또는 **ISE** 레이블이 없는 다른 모든 커넥터는 전역 다중 테넌트 논리 앱 서비스에서 계속 사용할 수 있습니다.

  ![다중 테넌트 커넥터 예제](./media/apis-list/example-multi-tenant-connector.png)

ISE 및 해당 커넥터에서 실행되는 논리 앱은 커넥터가 실행되는 위치에 관계없이 고정 가격 책정 계획과 소비 기반 가격 책정 계획을 따릅니다. 자세한 내용은 다음 페이지를 참조하십시오.

* [Logic Apps 가격 책정 모델](../logic-apps/logic-apps-pricing.md)
* [논리 앱 가격 세부 정보](https://azure.microsoft.com/pricing/details/logic-apps/)
* [Azure Logic Apps에서 Azure 가상 네트워크에 연결](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)

<a name="built-ins"></a>

## <a name="built-in"></a>기본 제공

Logic Apps는 기본 제공 트리거 및 작업을 제공하므로 일정 기반 워크플로를 만들고, 논리 앱이 다른 앱 및 서비스와 통신할 수 있도록 돕고, 논리 앱을 통해 워크플로를 제어하고, 데이터를 관리하거나 조작할 수 있습니다.

|   |   |   |   |
|---|---|---|---|
| [![API][schedule-icon]<br>아이콘**일정**][schedule-doc] | - 되풀이 [ **Recurrence** 트리거를][schedule-recurrence-doc]사용하여 기본 일정부터 고급 일정에 이르기까지 지정된 되풀이에서 논리 앱을 실행합니다. <p>- [ **슬라이딩 윈도우** 트리거와][schedule-sliding-window-doc]연속 청크에서 데이터를 처리해야하는 논리 응용 프로그램을 실행합니다. <p>- [ **지연** 작업으로][schedule-delay-doc]지정된 기간 동안 논리 앱을 일시 중지합니다. <p>- [지연이 **실행될 때까지** ][schedule-delay-until-doc]지정된 날짜및 시간까지 논리 앱을 일시 중지합니다. | [![API][batch-icon]<br>아이콘**일괄 처리**][batch-doc] | - **일괄 처리 메시지** 트리거를 사용하여 메시지를 일괄적으로 처리합니다. <p>- **일괄 처리로 메시지 보내기** 작업을 사용하여 기존 일괄 처리 트리거가 있는 논리 앱을 호출합니다. |
| [![API][http-icon]<br>아이콘**HTTP**][http-doc] | HTTP에 대한 트리거 및 작업을 통해 HTTP 또는 HTTPS 끝점을 호출합니다. 다른 HTTP 내장 트리거 및 작업에는 [HTTP + Swagger][http-swagger-doc] 및 [HTTP + Webhook이][http-webhook-doc]포함됩니다. | [![API][http-request-icon]<br>아이콘**요청**][http-request-doc] | - 논리 앱을 다른 앱이나 서비스에서 호출할 수 있게 하고, Event Grid 리소스 이벤트를 트리거하거나, **요청** 트리거를 사용하여 Azure Security Center 경고에 대한 응답을 트리거합니다. <p>- **응답** 작업을 사용하여 앱이나 서비스에 응답을 보냅니다. |
| [![API][azure-api-management-icon]<br>아이콘**Azure API <br>관리**][azure-api-management-doc] | Azure API Management를 통해 관리하고 게시하는 사용자 고유의 API로 정의된 트리거 및 작업을 호출합니다. | [![API][azure-app-services-icon]<br>아이콘**Azure 앱 <br>서비스**][azure-app-services-doc] | Azure App Service에서 호스팅되는 Azure API Apps 또는 Web Apps를 호출합니다. Swagger가 포함되는 경우 이러한 앱에서 정의된 트리거 및 작업은 다른 모든 고급 트리거 및 작업처럼 나타납니다.|
| [![API][azure-logic-apps-icon]<br>아이콘**Azure 논리 <br>앱**][nested-logic-app-doc] | **요청** 트리거로 시작하는 다른 논리 앱을 호출합니다. |
|||||

### <a name="run-code-from-logic-apps"></a>논리 앱에서 코드 실행

Logic Apps는 논리 앱의 워크플로에서 사용자 고유의 코드를 실행하기 위한 기본 제공 작업을 제공합니다.

|   |   |   |   |
|---|---|---|---|
| [![API][azure-functions-icon]<br>아이콘**Azure 함수**][azure-functions-doc] | 논리 앱에서 사용자 지정 코드 조각(C# 또는 Node.js)을 실행하는 Azure 함수를 호출합니다. | [![API][inline-code-icon]<br>아이콘**인라인 코드**][azure-functions-doc] | 논리 앱에서 JavaScript 코드 조각을 추가하고 실행합니다. |
|||||

### <a name="control-workflow"></a>워크플로 제어

Logic Apps는 논리 앱 워크플로에서 작업을 구조화하고 제어하기 위한 기본 제공 작업을 제공합니다.

|   |   |   |   |
|---|---|---|---|
| [![기본 제공][condition-icon]<br>아이콘**조건**][condition-doc] | 조건을 평가하고, 조건이 true 또는 false인지에 따라 별도의 작업을 실행합니다. | [![][for-each-icon]<br>**각 아이콘에 대한** 기본 제공 아이콘][for-each-doc] | 배열의 모든 항목에 대해 동일한 작업을 수행합니다. |
| [![기본 제공][scope-icon]<br>아이콘**범위**][scope-doc] | 범위에 속한 작업의 실행이 완료되면 해당 상태를 가져오는 *범위*로 작업을 그룹화합니다. | [![내장 아이콘][switch-icon]<br>**스위치**][switch-doc] | 기본 사례를 제외하고는 고유한 값이 할당된 *사례*로 작업을 그룹화합니다. 할당된 값이 식, 개체 또는 토큰의 결과와 일치하는 사례만 실행합니다. 일치하는 사례가 없는 경우 기본 사례를 실행합니다. |
| [![기본 제공][terminate-icon]<br>아이콘**종료**][terminate-doc] | 활발히 실행 중인 논리 앱 워크플로를 중지합니다. | [![내장][until-icon]<br>**아이콘까지**][until-doc] | 지정된 조건이 true이거나 일부 상태가 변경될 때까지 작업을 반복합니다. |
|||||

### <a name="manage-or-manipulate-data"></a>데이터 관리 또는 조작

Logic Apps는 데이터 출력 및 해당 형식으로 작업하기 위한 기본 제공 작업을 제공합니다.

|   |   |
|---|---|
| [![기본 제공][data-operations-icon]<br>아이콘**데이터 작업**][data-operations-doc] | 데이터를 사용하여 수행하는 작업: <p>- **작성**: 다양한 형식의 여러 입력에서 하나의 출력을 만듭니다. <br>- **CSV 테이블 만들기**: JSON 개체를 사용하여 배열에서 CSV(쉼표로 구분된 값) 테이블을 만듭니다. <br>- **HTML 테이블 만들기**: JSON 개체를 사용하여 배열에서 HTML 테이블을 만듭니다. <br>- **배열 필터링**: 조건을 충족하는 다른 배열의 항목에서 배열을 만듭니다. <br>- **조인**: 배열의 모든 항목에서 문자열을 만들고, 지정한 구분 기호를 사용하여 해당 항목을 구분합니다. <br>- **JSON 분석**: 워크플로에서 이러한 속성을 사용할 수 있도록 JSON 콘텐츠의 속성 및 해당 값에서 사용자 친화적인 토큰을 만듭니다. <br>- **선택**: 다른 배열의 항목이나 값을 변환하고 해당 항목을 지정한 속성에 매핑하여 JSON 개체가 포함된 배열을 만듭니다. |
| ![기본 제공 아이콘][date-time-icon]<br>**날짜 시간** | 타임스탬프를 사용하여 수행하는 작업: <p>- **시간에 추가**: 타임스탬프에 지정한 단위 수를 추가합니다. <br>- **표준 시간대 변환**: 타임스탬프를 원본 표준 시간대에서 대상 표준 시간대로 변환합니다. <br>- **현재 시간**: 현재 타임스탬프를 문자열로 반환합니다. <br>- **미래 시간 가져오기**: 현재 타임스탬프에 지정한 시간 단위를 더한 값을 반환합니다. <br>- **과거 시간 가져오기**: 현재 타임스탬프에서 지정한 시간 단위를 뺀 값을 반환합니다. <br>- **시간에서 빼기**: 타임스탬프에서 시간 단위 수를 뺍니다. |
| [![기본 제공][variables-icon]<br>아이콘**변수**][variables-doc] | 변수를 사용하여 수행하는 작업: <p>- **배열 변수에 추가**: 값을 변수로 저장되는 배열의 마지막 항목으로 삽입합니다. <br>- **문자열 변수에 추가**: 값을 변수로 저장되는 문자열의 마지막 문자로 삽입합니다. <br>- **변수 감소**: 변수를 상수 값만큼 줄입니다. <br>- **변수 증가**: 변수를 상수 값만큼 늘립니다. <br>- **변수 초기화**: 변수를 만들고 해당 데이터 형식과 초기 값을 선언합니다. <br>- **변수 설정**: 기존 변수에 다른 값을 할당합니다. |
|  |  |

<a name="managed-api-connectors"></a>

## <a name="managed-connectors"></a>관리되는 커넥터

Logic Apps는 다음과 같은 서비스 또는 시스템으로 작업, 프로세스 및 워크플로를 자동화하기 위한 인기 있는 표준 커넥터를 제공합니다.

|   |   |   |   |
|---|---|---|---|
| [![API][azure-service-bus-icon]<br>아이콘**Azure 서비스 버스**][azure-service-bus-doc] | Logic Apps에서 가장 일반적으로 사용되는 커넥터를 사용하여 비동기 메시지, 세션 및 토픽 구독을 관리합니다. | [![API][sql-server-icon]<br>아이콘**SQL 서버**][sql-server-doc] | 기록을 관리하거나 저장 프로시저를 실행하거나 쿼리를 수행할 수 있도록 클라우드의 SQL Server 또는 Azure SQL Database에 연결합니다. |
| [![API][azure-blob-storage-icon]<br>아이콘**Azure<br>Blob 저장소**][azure-blob-storage-doc] | Blob 콘텐츠를 만들고 관리할 수 있도록 저장소 계정에 연결합니다. | [![API][office-365-outlook-icon]<br>아이콘**사무실<br>365 전망**][office-365-outlook-doc] | Office 365 전자 메일 계정에 연결하여 이메일, 작업, 일정 이벤트 및 모임, 연락처, 요청 등을 만들고 관리할 수 있습니다. |
| [![API][sftp-ssh-icon]<br>아이콘**SFTP-SSH**][sftp-ssh-doc] | 파일 및 폴더로 작업할 수 있도록 SSH를 사용하여 인터넷에서 액세스할 수 있는 SFTP 서버에 연결합니다. | [![API][sharepoint-online-icon]<br>아이콘**쉐어포인트<br>온라인**][sharepoint-online-doc] | 파일, 첨부 파일, 폴더 등을 관리할 수 있도록 SharePoint Online에 연결합니다. | 
| [![API][dynamics-365-icon]<br>아이콘**역학 365<br> **][dynamics-365-doc] | 레코드, 항목 등을 만들고 관리할 수 있도록 Dynamics 365 계정에 연결합니다. | [![API][azure-queues-icon]<br>아이콘**Azure <br>큐**][azure-queues-doc] | 큐 및 메시지를 만들고 관리할 수 있도록 Azure Storage 계정에 연결 |
| [![API][ftp-icon]<br>아이콘**FTP**][ftp-doc] | 파일 및 폴더로 작업할 수 있도록 인터넷에서 액세스할 수 있는 FTP 서버에 연결합니다. | [![API][file-system-icon]<br>아이콘**파일 <br>시스템**][file-system-doc] | 파일을 만들고 관리할 수 있도록 온-프레미스 파일 공유에 연결합니다. |
| [![API][azure-event-hubs-icon]<br>아이콘**Azure 이벤트 허브**][azure-event-hubs-doc] | Event Hub를 통해 이벤트를 사용하고 게시합니다. 예를 들어 Event Hubs를 사용하여 논리 앱에서 출력을 가져온 다음, 해당 출력을 실시간 분석 공급자에게 보냅니다. | [![API][azure-event-grid-icon]<br>아이콘**Azure 이벤트**<br>**그리드**][azure-event-grid-doc] | Azure 리소스 또는 타사 리소스가 변경되는 경우와 같이 Event Grid에서 게시한 이벤트를 모니터링합니다. |
| [![API][salesforce-icon]<br>아이콘**세일즈포스**][salesforce-doc] | 레코드, 작업, 개체 등과 같은 항목을 만들고 관리할 수 있도록 Salesforce 계정에 연결합니다. | [![API][twitter-icon]<br>아이콘**트위터**][twitter-doc] | 트위터 계정에 연결하여 트윗, 팔로워, 타임라인 등을 관리할 수 있습니다. 트윗을 SQL, Excel 또는 SharePoint에 저장합니다. |
|||||

<a name="on-premises-connectors"></a>

## <a name="on-premises-connectors"></a>온-프레미스 커넥터

다음은 로직 앱이 온-프레미스 시스템의 데이터 및 리소스에 액세스하기 위해 제공하는 일반적으로 사용되는 표준 커넥터입니다. 온-프레미스 시스템에 대한 연결을 만들려면 먼저 [온-프레미스 데이터 게이트웨이를 다운로드, 설치 및 설정][gateway-doc]해야 합니다. 이 게이트웨이는 필요한 네트워크 인프라를 설정하지 않고도 보안 통신 채널을 제공합니다.

|   |   |   |   |   |
|---|---|---|---|---|
| [![API][biztalk-server-icon]<br>아이콘**비즈토크** <br> **서버**][biztalk-server-doc] | [![API][file-system-icon]<br>아이콘**파일 <br>시스템**][file-system-doc] | [![API][ibm-db2-icon]<br>아이콘**IBM DB2**][ibm-db2-doc] | [![API][ibm-informix-icon]<br>아이콘**IBM** <br> **인포믹스**][ibm-informix-doc] | [![API][mysql-icon]<br>아이콘**MySQL**][mysql-doc] |
| [![API][oracle-db-icon]<br>아이콘**오라클 DB**][oracle-db-doc] | [![API][postgre-sql-icon]<br>아이콘**포스트그레SQL**][postgre-sql-doc] | [![API][sharepoint-server-icon]<br>아이콘**쉐어포인트 <br>서버**][sharepoint-server-doc] | [![API][sql-server-icon]<br>아이콘**SQL <br>서버**][sql-server-doc] | [![API][teradata-icon]<br>아이콘**테라데이타**][teradata-doc] |
|||||

<a name="integration-account-connectors"></a>

## <a name="integration-account-connectors"></a>통합 계정 커넥터

Logic Apps는 Azure의 EIP(엔터프라이즈 통합 팩)를 통해 사용할 수 있는 [통합 계정을](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)만들고 비용을 지불할 때 논리 앱으로 B2B(비즈니스 간) 솔루션을 빌드하기 위한 표준 커넥터를 제공합니다. 이 계정을 사용하여 거래 파트너, 계약, 맵, 스키마, 인증서 등과 같은 B2B 아티팩트를 만들고 저장할 수 있습니다. 이러한 아티팩트를 사용하려면 논리 앱을 통합 계정과 연결합니다. 현재 BizTalk Server를 사용하는 경우 이러한 커넥터는 이미 익숙한 것처럼 보일 수 있습니다.

|   |   |   |   |
|---|---|---|---|
| [![API][as2-icon]<br>아이콘**AS2 <br>디코딩**][as2-doc] | [![API][as2-icon]<br>아이콘**AS2 <br>인코딩**][as2-doc] | [![API][edifact-icon]<br>아이콘**EDIFACT <br>디코딩**][edifact-decode-doc] | [![API][edifact-icon]<br>아이콘**EDIFACT <br>인코딩**][edifact-encode-doc] |
| [![API][flat-file-decode-icon]<br>아이콘**플랫 <br>파일 디코딩**][flat-file-decode-doc] | [![API][flat-file-encode-icon]<br>아이콘**플랫 <br>파일 인코딩**][flat-file-encode-doc] | [![API][integration-account-icon]<br>아이콘**통합 <br>계정**][integration-account-doc] | [![API][liquid-icon]<br>아이콘**액체** <br> **변환**][json-liquid-transform-doc] |
| [![API][x12-icon]<br>아이콘**X12 <br>디코딩**][x12-decode-doc] | [![API][x12-icon]<br>아이콘**X12 <br>인코딩**][x12-encode-doc] | [![API][xml-transform-icon]<br>아이콘**XML** <br> **변환**][xml-transform-doc] | [![API][xml-validate-icon]<br>아이콘**XML <br>유효성 검사**][xml-validate-doc] |  
|||||

<a name="enterprise-connectors"></a>

## <a name="enterprise-connectors"></a>엔터프라이즈 커넥터

Logic Apps는 SAP 및 IBM MQ와 같은 엔터프라이즈 시스템에 액세스하기 위한 엔터프라이즈 커넥터를 제공합니다.

|   |   |   |
|---|---|---|
| [![API][ibm-3270-icon]<br>아이콘**IBM 3270**][ibm-3270-doc] | [![API][ibm-mq-icon]<br>아이콘**IBM MQ**][ibm-mq-doc] | [![API][sap-icon]<br>아이콘**SAP**][sap-connector-doc] |
||||

<a name="ise-connectors"></a>

## <a name="ise-connectors"></a>ISE 커넥터

[격리된 통합 서비스 환경(ISE)을](#integration-service-environment)만들고 실행하는 논리 앱의 경우 Logic App Designer는 **CORE** 레이블을 사용하여 ISE에서 실행되는 기본 제공 트리거 및 작업을 식별합니다. ISE에서 실행되는 관리형 커넥터는 **ISE** 레이블을 표시하지만 전역 다중 테넌트 논리 앱 서비스에서 실행되는 커넥터는 두 레이블 중 하나를 표시하지 않습니다. 이 목록에는 현재 ISE 버전이 있는 커넥터가 표시됩니다.

|   |   |   |   |   |
|---|---|---|---|---|
[![API][as2-icon]<br>아이콘**AS2**][as2-doc] | [![API][azure-blob-storage-icon]<br>아이콘**Azure<br>Blob 저장소**][azure-blob-storage-doc] | [![API][azure-cosmos-db-icon]<br>아이콘**Azure <br> 코스모스 DB**][azure-cosmos-db-doc] | [![API][azure-event-hubs-icon]<br>아이콘**Azure 이벤트 <br>허브**][azure-event-hubs-doc] | [![API][azure-file-storage-icon]<br>아이콘**Azure 파일<br>저장소**][azure-file-storage-doc] |
| [![API][azure-service-bus-icon]<br>아이콘**Azure 서비스 <br>버스**][azure-service-bus-doc] | [![API][azure-sql-data-warehouse-icon]<br>아이콘**Azure <br>SQL 데이터 웨어하우스**][azure-sql-data-warehouse-doc] | [![API][azure-table-storage-icon]<br>아이콘**Azure 테이블 <br>저장소**][azure-table-storage-doc] | [![API][azure-queues-icon]<br>아이콘**Azure <br>큐**][azure-queues-doc] | [![API][edifact-icon]<br>아이콘**EDIFACT**][edifact-doc] |
| [![API][file-system-icon]<br>아이콘**파일 <br>시스템**][file-system-doc] | [![API][ftp-icon]<br>아이콘**FTP**][ftp-doc] | [![API][ibm-3270-icon]<br>아이콘**IBM 3270**][ibm-3270-doc] | [![API][ibm-db2-icon]<br>아이콘**IBM DB2**][ibm-db2-doc] | [![API][ibm-mq-icon]<br>아이콘**IBM MQ**][ibm-mq-doc] |
| [![API][sap-icon]<br>아이콘**SAP**][sap-connector-doc] | [![API][sftp-ssh-icon]<br>아이콘**SFTP-SSH**][sftp-ssh-doc] | [![API][smtp-icon]<br>아이콘**SMTP**][smtp-doc] | [![API][sql-server-icon]<br>아이콘**SQL <br>서버**][sql-server-doc] | [![API][x12-icon]<br>아이콘**X12**][x12-doc] |
||||||

자세한 내용은 다음 항목을 참조하세요.

* [Azure 논리 앱에서 Azure 가상 네트워크 리소스에 대한 액세스](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)
* [Logic Apps 가격 책정 모델](../logic-apps/logic-apps-pricing.md)
* [Azure Logic Apps에서 Azure 가상 네트워크에 연결](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)

<a name="triggers-actions"></a>

## <a name="triggers-and-action-types"></a>트리거 및 작업 유형

커넥터는 트리거, *작업*또는 둘 *다를*제공할 수 있습니다. *트리거는* 모든 논리 앱의 첫 번째 단계로, 일반적으로 트리거를 실행하고 논리 앱 실행을 시작하는 이벤트를 지정합니다. 예를 들어 FTP 커넥터에는 "파일이 추가되거나 수정될 때"로직 앱을 시작하는 트리거가 있습니다. 일부 트리거는 지정된 이벤트 또는 데이터를 정기적으로 확인한 다음 지정된 이벤트 또는 데이터를 감지하면 발생합니다. 다른 트리거는 대기하지만 특정 이벤트가 발생하거나 새 데이터를 사용할 수 있을 때 즉시 발생합니다. 트리거는 필요한 데이터를 논리 앱으로 전달합니다. 논리 앱은 워크플로 전체에서 해당 데이터를 읽고 사용할 수 있습니다. 예를 들어 트위터 커넥터에는 "새 트윗이 게시되면"이라는 트리거가 있어 트윗의 콘텐츠를 로직 앱의 워크플로에 전달합니다.

트리거가 실행된 후 Azure Logic Apps는 논리 앱의 인스턴스를 만들고 논리 앱의 워크플로에서 *작업을* 실행하기 시작합니다. 작업은 트리거를 수행하고 논리 앱의 워크플로에서 작업을 수행하는 단계입니다. 예를 들어 SQL 데이터베이스에서 고객 데이터를 가져옵니다 및 이후 작업에서 해당 데이터를 처리 하는 논리 응용 프로그램을 만들 수 있습니다.

Azure 논리 앱에서 제공하는 일반적인 트리거 종류는 다음과 같습니다.

* *되풀이 트리거*: 이 트리거는 지정된 일정에 따라 실행되며 특정 서비스 또는 시스템과 밀접하게 관련되지 않습니다.

* *폴링 트리거*: 이 트리거는 지정된 일정에 따라 특정 서비스 또는 시스템을 정기적으로 폴링하여 새 데이터를 확인하거나 특정 이벤트가 발생했는지 여부를 확인합니다. 새 데이터를 사용할 수 있거나 특정 이벤트가 발생한 경우 트리거는 논리 앱의 새 인스턴스를 만들고 실행하여 이제 전달된 데이터를 입력으로 사용할 수 있습니다.

* *푸시 트리거*: 이 트리거는 새 데이터 또는 이벤트가 발생할 때까지 대기하고 수신 대기합니다. 새 데이터를 사용할 수 있거나 이벤트가 발생하면 트리거가 논리 앱의 새 인스턴스를 만들고 실행하여 이제 입력으로 전달된 데이터를 사용할 수 있습니다.

<a name="connections"></a>

## <a name="connector-configuration"></a>커넥터 구성

각 커넥터의 트리거 및 작업은 구성할 수 있는 고유한 속성을 제공합니다. 또한 많은 커넥터에서는 논리 앱에서 트리거 또는 작업을 사용하기 전에 먼저 대상 서비스 또는 시스템에 *대한 연결을* 만들고 인증 자격 증명 또는 기타 구성 세부 정보를 제공해야 합니다. 예를 들어 데이터에 액세스하거나 귀하를 대신하여 게시할 수 있도록 트위터 계정에 대한 연결을 승인해야 합니다.

Azure Active Directory(Azure AD) OAuth를 사용하는 커넥터의 경우 연결을 만드는 것은 액세스 토큰이 [암호화되어](../security/fundamentals/encryption-overview.md) Azure 비밀 저장소에 안전하게 저장되는 Office 365, Salesforce 또는 GitHub와 같은 서비스에 로그인하는 것을 의미합니다. FTP 및 SQL과 같은 다른 커넥터에는 서버 주소, 사용자 이름 및 암호와 같은 구성 세부 정보가 있는 연결이 필요합니다. 이러한 연결 구성 세부 정보 또한 암호화된 후 안전하게 저장됩니다. [Azure 에서 암호화에](../security/fundamentals/encryption-overview.md)대해 자세히 알아봅니다.

연결은 해당 서비스 또는 시스템이 허용하는 한 대상 서비스 또는 시스템에 액세스할 수 있습니다. Office 365 및 역학과 같은 Azure AD OAuth 연결을 사용하는 서비스의 경우 Azure Logic Apps는 액세스 토큰을 무기한 새로 고칩니다. 다른 서비스에는 Azure Logic Apps에서 새로 고침 없이 토큰을 사용할 수 있는 기간에 제한이 있을 수 있습니다. 일반적으로 일부 작업은 암호 변경과 같은 모든 액세스 토큰을 무효화합니다.

<a name="custom"></a>

## <a name="custom-apis-and-connectors"></a>사용자 지정 API 및 커넥터

사용자 지정 코드를 실행하거나 커넥터로 사용할 수 없는 API를 호출하려면 [사용자 지정 API Apps를 만들어](../logic-apps/logic-apps-create-api-app.md) Logic Apps 플랫폼을 확장할 수 있습니다. 또한 *모든* REST 또는 SOAP 기반 API에 대한 [사용자 지정 커넥터를 만들면](../logic-apps/custom-connector-overview.md) Azure 구독의 모든 논리 앱에서 해당 API를 사용할 수 있습니다. Azure에서 모든 사용자가 사용할 수 있도록 사용자 지정 API 앱 또는 커넥터를 공개하려면 [Microsoft 인증을 위해 커넥터를 제출](../logic-apps/custom-connector-submit-certification.md)할 수 있습니다.

> [!NOTE]
> [ISE(통합 서비스 환경)에서](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) 배포하고 실행하는 논리 앱은 Azure 가상 네트워크의 리소스에 직접 액세스할 수 있습니다. 온-프레미스 데이터 게이트웨이가 필요한 사용자 지정 커넥터가 있고 ISE 외부에서 해당 커넥터를 만든 경우 ISE의 논리 앱도 이러한 커넥터를 사용할 수 있습니다.
>
> ISE 내에서 생성된 사용자 지정 커넥터는 온-프레미스 데이터 게이트웨이에서 작동하지 않습니다. 그러나 이러한 커넥터는 ISE를 호스팅하는 Azure 가상 네트워크에 연결된 온-프레미스 데이터 원본에 직접 액세스할 수 있습니다. 따라서 ISE의 논리 앱은 이러한 리소스와 통신할 때 데이터 게이트웨이가 필요하지 않을 가능성이 높습니다.
>
> ISEs 를 만드는 자세한 내용은 [Azure 논리 앱에서 Azure 가상 네트워크에 연결 을](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)참조하십시오.

## <a name="next-steps"></a>다음 단계

* 전체 [커넥터 목록](https://docs.microsoft.com/connectors) 보기
* [첫 번째 논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [논리 앱에 대한 사용자 지정 커넥터 만들기](https://docs.microsoft.com/connectors/custom-connectors/)
* [논리 앱에 대한 사용자 지정 API 만들기](../logic-apps/logic-apps-create-api-app.md)

<!--Misc doc links-->
[gateway-doc]: ../logic-apps/logic-apps-gateway-connection.md "온-프레미스 데이터 게이트웨이를 사용하여 논리 앱에서 온-프레미스 데이터 원본에 연결"

<!--Built-in doc links-->
[azure-api-management-doc]: ../api-management/get-started-create-service-instance.md "API 관리 및 게시를 위한 Azure API Management 서비스 인스턴스 만들기"
[azure-app-services-doc]: ../logic-apps/logic-apps-custom-hosted-api.md "App Service API Apps과 논리 앱 통합"
[azure-functions-doc]: ../logic-apps/logic-apps-azure-functions.md "Azure 함수와 논리 앱 통합"
[batch-doc]: ../logic-apps/logic-apps-batch-process-send-receive-messages.md "그룹 또는 일괄 처리로 메시지 처리"
[condition-doc]: ../logic-apps/logic-apps-control-flow-conditional-statement.md "조건을 평가하고 조건이 true인지 거짓인지에 따라 다른 작업을 실행합니다."
[for-each-doc]: ../logic-apps/logic-apps-control-flow-loops.md#foreach-loop "배열의 모든 항목에 대해 동일한 작업을 수행합니다."
[http-doc]: ./connectors-native-http.md "논리 앱에서 HTTP 또는 HTTPS 끝점 호출"
[http-request-doc]: ./connectors-native-reqres.md "논리 앱에서 HTTP 요청 받기"
[http-response-doc]: ./connectors-native-reqres.md "논리 앱의 HTTP 요청에 응답"
[http-swagger-doc]: ./connectors-native-http-swagger.md "논리 앱에서 REST 끝점 호출"
[http-webhook-doc]: ./connectors-native-webhook.md "HTTP 또는 HTTPS 끝점에서 특정 이벤트 대기"
[nested-logic-app-doc]: ../logic-apps/logic-apps-http-endpoint.md "중첩된 워크플로와 논리 앱 통합"
[query-doc]: ../logic-apps/logic-apps-perform-data-operations.md#filter-array-action "쿼리 작업을 사용하여 배열 선택 및 필터링"
[schedule-doc]: ../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md "일정에 따라 논리 앱 실행"
[schedule-delay-doc]: ./connectors-native-delay.md "다음 작업 실행 지연"
[schedule-delay-until-doc]: ./connectors-native-delay.md "다음 작업 실행 지연"
[schedule-recurrence-doc]:  ./connectors-native-recurrence.md "되풀이 일정에 따라 논리 앱 실행"
[schedule-sliding-window-doc]: ./connectors-native-sliding-window.md "연속 된 청크에서 데이터를 처리 해야 하는 논리 애플 리 케이 션을 실행"
[scope-doc]: ../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md "그룹에 속한 작업의 실행이 완료되면 해당 상태를 가져오는 그룹으로 작업을 구성"
[switch-doc]: ../logic-apps/logic-apps-control-flow-switch-statement.md "작업을 고유한 값이 할당된 서비스 케이스로 구성합니다. 식, 개체 또는 토큰의 결과와 일치하는 값의 경우만 실행합니다. 일치하는 항목이 없는 경우 기본 케이스를 실행합니다."
[terminate-doc]: ../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action "논리 앱에 대해 활발히 실행되는 워크플로 중지 또는 취소"
[until-doc]: ../logic-apps/logic-apps-control-flow-loops.md#until-loop "지정된 조건이 true이거나 일부 상태가 변경될 때까지 작업을 반복합니다."
[data-operations-doc]: ../logic-apps/logic-apps-perform-data-operations.md "배열 필터링이나 CSV 및 HTML 테이블 생성과 같은 데이터 작업 수행"
[variables-doc]: ../logic-apps/logic-apps-create-variables-store-values.md "변수를 사용하여 작업 수행(예: 초기화, 설정, 증가, 감소 및 문자열 또는 배열 변수에 추가)"

<!--Managed connector doc links-->
[azure-blob-storage-doc]: ./connectors-create-api-azureblobstorage.md "Azure Blob Storage 커넥터와 Blob 컨테이너의 파일 관리"
[azure-cosmos-db-doc]: https://docs.microsoft.com/connectors/documentdb/ "문서 및 저장 프로시저에 액세스할 수 있도록 Azure Cosmos DB에 연결"
[azure-event-grid-doc]: ../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md "예를 들어 Azure 리소스 또는 타사 리소스가 변경될 때 이벤트 그리드에서 게시한 이벤트를 모니터링합니다."
[azure-event-hubs-doc]: ./connectors-create-api-azure-event-hubs.md "논리 앱과 이벤트 허브 간에 이벤트를 수신하고 보낼 수 있도록 Azure 이벤트 허브에 연결"
[azure-file-storage-doc]: https://docs.microsoft.com/connectors/azurefile/ "파일을 생성, 업데이트, 다운로드 및 삭제할 수 있도록 Azure Storage 계정에 연결"
[azure-queues-doc]: https://docs.microsoft.com/connectors/azurequeues/ "큐 및 메시지를 만들고 관리할 수 있도록 Azure Storage 계정에 연결"
[azure-service-bus-doc]: ./connectors-create-api-servicebus.md "Service Bus 큐 및 항목에서 메시지를 보내고 Service Bus 큐 및 구독에서 메시지를 받습니다."
[azure-sql-data-warehouse-doc]: https://docs.microsoft.com/connectors/sqldw/ "데이터를 볼 수 있도록 Azure SQL 데이터 웨어하우스에 연결"
[azure-table-storage-doc]: https://docs.microsoft.com/connectors/azuretables/ "Azure Storage 계정에 연결하여 테이블 등을 생성, 업데이트 및 쿼리할 수 있습니다."
[biztalk-server-doc]: https://docs.microsoft.com/connectors/biztalk/ "BizTalk 서버에 연결하여 Azure 논리 앱과 함께 BizTalk 기반 응용 프로그램을 나란히 실행할 수 있습니다."
[box-doc]: ./connectors-create-api-box.md "상자에 연결합니다. 업로드, 받기, 삭제, 파일 목록 등"
[dropbox-doc]: ./connectors-create-api-dropbox.md "Dropbox에 연결합니다. 업로드, 받기, 삭제, 파일 목록 등"
[dynamics-365-doc]: ./connectors-create-api-crmonline.md "CRM 온라인 데이터로 작업할 수 있도록 다이내믹스 CRM 온라인에 연결"
[facebook-doc]: ./connectors-create-api-facebook.md "페이스 북에 연결합니다. 타임라인에 게시하고, 페이지 피드를 받는 등"
[file-system-doc]: ../logic-apps/logic-apps-using-file-connector.md "온-프레미스 파일 시스템에 연결"
[ftp-doc]: ./connectors-create-api-ftp.md "파일 업로드, 가져오기, 삭제 등과 같은 FTP 태스크의 경우 FTP / FTPS 서버에 연결"
[github-doc]: ./connectors-create-api-github.md "GitHub에 연결 및 문제 추적"
[google-calendar-doc]: ./connectors-create-api-googlecalendar.md "Google 캘린더에 연결하여 캘린더를 관리할 수 있습니다."
[google-drive-doc]: ./connectors-create-api-googledrive.md "데이터 로 작업할 수 있도록 GoogleDrive에 연결"
[google-sheets-doc]: ./connectors-create-api-googlesheet.md "시트를 수정할 수 있도록 Google 시트에 연결"
[google-tasks-doc]: ./connectors-create-api-googletasks.md "작업을 관리할 수 있도록 Google 작업에 연결"
[ibm-3270-doc]: ./connectors-run-3270-apps-ibm-mainframe-create-api-3270.md "IBM 메인프레임에서 3270개의 앱에 연결"
[ibm-db2-doc]: ./connectors-create-api-db2.md "클라우드 또는 온-프레미스에서 IBM DB2에 연결합니다. 행 업데이트, 테이블 받기 등"
[ibm-informix-doc]: ./connectors-create-api-informix.md "클라우드 또는 온-프레미스에서 Informix에 연결합니다. 행 읽기, 테이블 목록 등"
[ibm-mq-doc]: ./connectors-create-api-mq.md "온-프레미스 또는 Azure에서 IBM MQ에 연결하여 메시지 송수신"
[instagram-doc]: ./connectors-create-api-instagram.md "인스 타 그램에 연결합니다. 이벤트 트리거 또는 행동"
[mailchimp-doc]: ./connectors-create-api-mailchimp.md "MailChimp 계정에 연결합니다. 메일 관리 및 자동화"
[mandrill-doc]: ./connectors-create-api-mandrill.md "통신을 위해 Mandrill에 연결"
[mysql-doc]: https://docs.microsoft.com/connectors/mysql/ "온-프레미스 MySQL 데이터베이스에 연결하여 데이터를 읽고 쓸 수 있습니다."
[office-365-outlook-doc]: ./connectors-create-api-office365-outlook.md "Office 365 계정에 연결하여 이메일을 보내고 받고, 캘린더 및 연락처를 관리하는 등"
[office-365-users-doc]: ./connectors-create-api-office365-users.md
[onedrive-doc]: ./connectors-create-api-onedrive.md "개인 Microsoft OneDrive에 연결하여 파일을 업로드, 삭제, 목록 지정 등을 수행할 수 있습니다."
[onedrive-for-business-doc]: ./connectors-create-api-onedriveforbusiness.md "비즈니스 Microsoft OneDrive에 연결하여 파일을 업로드, 삭제, 나열 등을 수행할 수 있습니다."
[oracle-db-doc]: ./connectors-create-api-oracledatabase.md "Oracle 데이터베이스에 연결하여 행을 추가, 삽입, 삭제 할 수 있습니다."
[outlook.com-doc]: ./connectors-create-api-outlook.md "전자 메일, 일정, 연락처 등을 관리할 수 있도록 Outlook 사서함에 연결"
[postgre-sql-doc]: https://docs.microsoft.com/connectors/postgresql/ "테이블에서 데이터를 읽을 수 있도록 PostgreSQL 데이터베이스에 연결"
[project-online-doc]: ./connectors-create-api-projectonline.md "프로젝트, 작업, 리소스 등을 관리할 수 있도록 Microsoft 프로젝트 온라인에 연결"
[rss-doc]: ./connectors-create-api-rss.md "RSS 피드에 새 항목이 게시될 때 피드 항목 게시 및 검색, 작업 트리거"
[salesforce-doc]: ./connectors-create-api-salesforce.md "Salesforce 계정에 연결합니다. 계정, 잠재 고객, 기회 등을 관리"
[sap-connector-doc]: ../logic-apps/logic-apps-using-sap-connector.md "온-프레미스 SAP 시스템에 연결"
[sendgrid-doc]: ./connectors-create-api-sendgrid.md "SendGrid에 연결합니다. 이메일 보내기 및 수신자 목록 관리"
[sftp-ssh-doc]: ./connectors-sftp-ssh.md "SSH를 사용하여 SFTP 계정에 연결합니다. 파일 업로드, 받기, 삭제 등"
[sharepoint-server-doc]: ./connectors-create-api-sharepointserver.md "SharePoint 온-프레미스 서버에 연결합니다. 문서 관리, 목록 항목 등"
[sharepoint-online-doc]: ./connectors-create-api-sharepointonline.md "공유점 온라인에 연결합니다. 문서 관리, 목록 항목 등"
[slack-doc]: ./connectors-create-api-slack.md "슬랙에 연결하고 슬랙 채널에 메시지 게시"
[smtp-doc]: ./connectors-create-api-smtp.md "SMTP 서버에 연결 및 첨부 파일이 포함된 이메일 보내기"
[sparkpost-doc]: ./connectors-create-api-sparkpost.md "통신을 위해 SparkPost에 연결"
[sql-server-doc]: ./connectors-create-api-sqlazure.md "Azure SQL 데이터베이스 또는 SQL 서버에 연결합니다. SQL 데이터베이스 테이블에서 항목 만들기, 업데이트, 받기 및 삭제"
[teradata-doc]: https://docs.microsoft.com/connectors/teradata/ "Teradata 데이터베이스에 연결하여 테이블에서 데이터를 읽습니다."
[trello-doc]: ./connectors-create-api-trello.md "트렐로에 연결합니다. 프로젝트를 관리하고 누구와도 무엇이든 구성"
[twilio-doc]: ./connectors-create-api-twilio.md "트윌리오에 연결합니다. 메시지 보내기 및 받기, 사용 가능한 번호 받기, 수신 전화 번호 관리 등"
[twitter-doc]: ./connectors-create-api-twitter.md "트위터에 연결합니다. 타임라인, 트윗 게시 등"
[yammer-doc]: ./connectors-create-api-yammer.md "Yammer에 연결합니다. 메시지 게시, 새 메시지 받기 등"
[youtube-doc]: ./connectors-create-api-youtube.md "유튜브에 연결합니다. 동영상 및 채널 관리"

<!--Enterprise Intregation Pack doc links-->
[as2-doc]: ../logic-apps/logic-apps-enterprise-integration-as2.md "AS2 프로토콜을 사용하는 메시지 인코딩 및 디코딩"
[edifact-doc]: ../logic-apps/logic-apps-enterprise-integration-edifact.md "EDIFACT 프로토콜을 사용하는 메시지 인코딩 및 디코딩"
[edifact-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-decode.md "EDIFACT 프로토콜을 사용하는 메시지 디코딩"
[edifact-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-encode.md "EDIFACT 프로토콜을 사용하는 메시지 인코딩"
[flat-file-decode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "엔터프라이즈 통합 플랫 파일에 대해 알아보기"
[flat-file-encode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "엔터프라이즈 통합 플랫 파일에 대해 알아보기"
[integration-account-doc]: ../logic-apps/logic-apps-enterprise-integration-metadata.md "통합 계정 아티팩트에 대한 메타데이터 관리"
[json-liquid-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-liquid-transform.md "액체 템플릿으로 JSON 변환"
[x12-doc]: ../logic-apps/logic-apps-enterprise-integration-x12.md "X12 프로토콜을 사용하는 메시지 인코딩 및 디코딩"
[x12-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-decode.md "X12 프로토콜을 사용하는 메시지 디코딩"
[x12-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-encode.md "X12 프로토콜을 사용하는 메시지 인코딩"
[xml-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-transform.md "XML 메시지 변환"
[xml-validate-doc]: ../logic-apps/logic-apps-enterprise-integration-xml-validation.md "XML 메시지 유효성 검사"

<!-- Built-ins icons -->
[azure-api-management-icon]: ./media/apis-list/azure-api-management.png
[azure-app-services-icon]: ./media/apis-list/azure-app-services.png
[azure-functions-icon]: ./media/apis-list/azure-functions.png
[azure-logic-apps-icon]: ./media/apis-list/azure-logic-apps.png
[batch-icon]: ./media/apis-list/batch.png
[condition-icon]: ./media/apis-list/condition.png
[data-operations-icon]: ./media/apis-list/data-operations.png
[date-time-icon]: ./media/apis-list/date-time.png
[for-each-icon]: ./media/apis-list/for-each-loop.png
[http-icon]: ./media/apis-list/http.png
[http-request-icon]: ./media/apis-list/request.png
[http-response-icon]: ./media/apis-list/response.png
[http-swagger-icon]: ./media/apis-list/http-swagger.png
[http-webhook-icon]: ./media/apis-list/http-webhook.png
[inline-code-icon]: ./media/apis-list/inline-code.png
[schedule-icon]: ./media/apis-list/recurrence.png
[scope-icon]: ./media/apis-list/scope.png
[switch-icon]: ./media/apis-list/switch.png
[terminate-icon]: ./media/apis-list/terminate.png
[until-icon]: ./media/apis-list/until.png
[variables-icon]: ./media/apis-list/variables.png

<!--Managed connector icons-->
[appfigures-icon]: ./media/apis-list/appfigures.png
[asana-icon]: ./media/apis-list/asana.png
[azure-automation-icon]: ./media/apis-list/azure-automation.png
[azure-blob-storage-icon]: ./media/apis-list/azure-blob-storage.png
[azure-cognitive-services-text-analytics-icon]: ./media/apis-list/azure-cognitive-services-text-analytics.png
[azure-cosmos-db-icon]: ./media/apis-list/azure-cosmos-db.png
[azure-data-lake-icon]: ./media/apis-list/azure-data-lake.png
[azure-document-db-icon]: ./media/apis-list/azure-document-db.png
[azure-event-grid-icon]: ./media/apis-list/azure-event-grid.png
[azure-event-grid-publish-icon]: ./media/apis-list/azure-event-grid-publish.png
[azure-event-hubs-icon]: ./media/apis-list/azure-event-hubs.png
[azure-file-storage-icon]: ./media/apis-list/azure-file-storage.png
[azure-ml-icon]: ./media/apis-list/azure-ml.png
[azure-queues-icon]: ./media/apis-list/azure-queues.png
[azure-resource-manager-icon]: ./media/apis-list/azure-resource-manager.png
[azure-service-bus-icon]: ./media/apis-list/azure-service-bus.png
[azure-sql-data-warehouse-icon]: ./media/apis-list/azure-sql-data-warehouse.png
[azure-table-storage-icon]: ./media/apis-list/azure-table-storage.png
[basecamp-3-icon]: ./media/apis-list/basecamp.png
[bitbucket-icon]: ./media/apis-list/bitbucket.png
[bitly-icon]: ./media/apis-list/bitly.png
[biztalk-server-icon]: ./media/apis-list/biztalk.png
[blogger-icon]: ./media/apis-list/blogger.png
[box-icon]: ./media/apis-list/box.png
[campfire-icon]: ./media/apis-list/campfire.png
[common-data-service-icon]: ./media/apis-list/common-data-service.png
[dropbox-icon]: ./media/apis-list/dropbox.png
[dynamics-365-icon]: ./media/apis-list/dynamics-crm-online.png
[dynamics-365-financials-icon]: ./media/apis-list/dynamics-365-financials.png
[dynamics-365-operations-icon]: ./media/apis-list/dynamics-365-operations.png
[easy-redmine-icon]: ./media/apis-list/easyredmine.png
[facebook-icon]: ./media/apis-list/facebook.png
[file-system-icon]: ./media/apis-list/file-system.png
[ftp-icon]: ./media/apis-list/ftp.png
[github-icon]: ./media/apis-list/github.png
[google-calendar-icon]: ./media/apis-list/google-calendar.png
[google-drive-icon]: ./media/apis-list/google-drive.png
[google-sheets-icon]: ./media/apis-list/google-sheet.png
[google-tasks-icon]: ./media/apis-list/google-tasks.png
[hipchat-icon]: ./media/apis-list/hipchat.png
[ibm-3270-icon]: ./media/apis-list/ibm-3270.png
[ibm-db2-icon]: ./media/apis-list/ibm-db2.png
[ibm-informix-icon]: ./media/apis-list/ibm-informix.png
[ibm-mq-icon]: ./media/apis-list/ibm-mq.png
[insightly-icon]: ./media/apis-list/insightly.png
[instagram-icon]: ./media/apis-list/instagram.png
[instapaper-icon]: ./media/apis-list/instapaper.png
[jira-icon]: ./media/apis-list/jira.png
[mailchimp-icon]: ./media/apis-list/mailchimp.png
[mandrill-icon]: ./media/apis-list/mandrill.png
[mysql-icon]: ./media/apis-list/mysql.png
[office-365-outlook-icon]: ./media/apis-list/office-365.png
[office-365-users-icon]: ./media/apis-list/office-365-users.png
[onedrive-icon]: ./media/apis-list/onedrive.png
[onedrive-for-business-icon]: ./media/apis-list/onedrive-business.png
[oracle-db-icon]: ./media/apis-list/oracle-db.png
[outlook.com-icon]: ./media/apis-list/outlook.png
[pagerduty-icon]: ./media/apis-list/pagerduty.png
[pinterest-icon]: ./media/apis-list/pinterest.png
[postgre-sql-icon]: ./media/apis-list/postgre-sql.png
[project-online-icon]: ./media/apis-list/projecton-line.png
[redmine-icon]: ./media/apis-list/redmine.png
[rss-icon]: ./media/apis-list/rss.png
[salesforce-icon]: ./media/apis-list/salesforce.png
[sap-icon]: ./media/apis-list/sap.png
[send-grid-icon]: ./media/apis-list/sendgrid.png
[sftp-ssh-icon]: ./media/apis-list/sftp.png
[sharepoint-online-icon]: ./media/apis-list/sharepoint-online.png
[sharepoint-server-icon]: ./media/apis-list/sharepoint-server.png
[slack-icon]: ./media/apis-list/slack.png
[smartsheet-icon]: ./media/apis-list/smartsheet.png
[smtp-icon]: ./media/apis-list/smtp.png
[sparkpost-icon]: ./media/apis-list/sparkpost.png
[sql-server-icon]: ./media/apis-list/sql.png
[teradata-icon]: ./media/apis-list/teradata.png
[todoist-icon]: ./media/apis-list/todoist.png
[trello-icon]: ./media/apis-list/trello.png
[twilio-icon]: ./media/apis-list/twilio.png
[twitter-icon]: ./media/apis-list/twitter.png
[vimeo-icon]: ./media/apis-list/vimeo.png
[visual-studio-team-services-icon]: ./media/apis-list/visual-studio-team-services.png
[wordpress-icon]: ./media/apis-list/wordpress.png
[yammer-icon]: ./media/apis-list/yammer.png
[youtube-icon]: ./media/apis-list/youtube.png

<!-- Enterprise Integration Pack icons -->
[as2-icon]: ./media/apis-list/as2.png
[edifact-icon]: ./media/apis-list/edifact.png
[flat-file-encode-icon]: ./media/apis-list/flat-file-encoding.png
[flat-file-decode-icon]: ./media/apis-list/flat-file-decoding.png
[integration-account-icon]: ./media/apis-list/integration-account.png
[liquid-icon]: ./media/apis-list/liquid-transform.png
[x12-icon]: ./media/apis-list/x12.png
[xml-validate-icon]: ./media/apis-list/xml-validation.png
[xml-transform-icon]: ./media/apis-list/xsl-transform.png
