---
title: Azure Logic Apps용 커넥터
description: 기본 제공, 관리되는, 온-프레미스, 통합 계정, ISE 및 엔터프라이즈 커넥터를 포함하여 Azure Logic Apps용 커넥터를 사용하여 워크플로를 자동화합니다.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: article
ms.date: 02/12/2021
ms.openlocfilehash: 4b431220dbab49b74f38a8f37be8aac1a0c5c460
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100382891"
---
# <a name="connectors-for-azure-logic-apps"></a>Azure Logic Apps용 커넥터

커넥터를 사용하면 Azure Logic Apps에서 다른 앱, 서비스, 시스템, 프로토콜 및 플랫폼에 걸쳐 이벤트, 데이터 및 작업에 빠르게 액세스할 수 있습니다. 이미 만들어 사용하고 있는 데이터로 작업을 수행할 수 있도록, 논리 앱에서 커넥터를 사용하여 클라우드 및 온-프레미스 앱에 대한 기능을 확장합니다.

Logic Apps는 [수백 개의 커넥터](/connectors)를 제공하지만 이 문서에서는 수천 개의 앱 및 수백만 개의 실행에서 데이터와 정보를 처리하는 데 성공적으로 사용되는 매우 일반적이며 인기 있는 커넥터에 대해 설명합니다. 커넥터의 전체 목록과 트리거, 작업 및 제한과 같은 각 커넥터의 참조 정보는 [커넥터 개요](/connectors)의 커넥터 참조 페이지를 검토하세요. 또한 [트리거 및 작업](#triggers-actions), [Logic Apps 가격 책정 모델](../logic-apps/logic-apps-pricing.md), [Logic Apps 가격 책정 세부 정보](https://azure.microsoft.com/pricing/details/logic-apps/)에 대해 자세히 알아보세요.

> [!TIP]
> 커넥터가 없는 서비스 또는 API를 통합하려면 HTTP와 같은 프로토콜을 통해 서비스를 직접 호출하거나 [사용자 지정 커넥터](#custom)를 만들 수 있습니다.

## <a name="connector-types"></a>커넥터 유형

커넥터는 기본 제공 트리거 및 작업으로 사용하거나 관리되는 커넥터로 사용할 수 있습니다.

<a name="built-in"></a>

* [**기본 제공**](#built-ins): 기본 제공 트리거 및 작업은 Azure Logic Apps에서 기본적으로 실행되므로 사용하기 전에 연결을 생성할 필요가 없으며 논리 앱에 대해 이러한 작업을 수행할 수 있습니다.

  * 사용자 지정 및 고급 일정에서 실행합니다.

  * 루프 및 조건과 같은 논리 앱의 워크플로를 구성 및 제어하고 변수와 데이터 작업을 수행합니다.

  * 다른 엔드포인트와 통신합니다.

  * 요청을 수신하고 응답합니다.

  * Azure 함수, Azure API Apps(Web Apps), Azure API Management로 관리 및 게시되는 자체 API, 요청을 수신할 수 있는 중첩된 논리 앱을 호출합니다.

<a name="managed-connectors"></a>

* [**관리되는 커넥터**](#managed-api-connectors): Microsoft에서 배포 및 관리하는 이러한 커넥터는 Office 365, Azure Blob Storage, SQL Server, Dynamics, Salesforce, SharePoint 등을 포함하여 클라우드 서비스, 온-프레미스 시스템 또는 둘 다에 액세스하기 위한 트리거와 작업을 제공합니다. 일부 커넥터는 B2B(business-to-business) 통신 시나리오를 구체적으로 지원하고 논리 앱에 연결된 [통합 계정](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)이 필요합니다. 특정 커넥터를 사용하기 전에 먼저 Azure Logic Apps에서 관리되는 연결을 만들어야 할 수 있습니다.

  예를 들어 Microsoft BizTalk Server를 사용하는 경우 [BizTalk Server 온-프레미스 커넥터](#on-premises-connectors)를 사용하여 논리 앱에서 BizTalk Server와 연결하여 통신할 수 있습니다. 그런 다음, [통합 계정 커넥터](#integration-account-connectors)를 사용하여 논리 앱에서 BizTalk와 비슷한 작업을 확장하거나 수행할 수 있습니다.

  커넥터는 표준 또는 엔터프라이즈 중 하나로 분류됩니다. [엔터프라이즈 커넥터](#enterprise-connectors)는 추가 비용으로 SAP, IBM MQ, IBM 3270과 같은 엔터프라이즈 시스템에 대한 액세스를 제공합니다. 커넥터가 표준 또는 엔터프라이즈인지 확인하려면 [커넥터 개요](/connectors)에서 각 커넥터의 참조 페이지의 기술 세부 정보를 참조하세요.

  또한 이러한 범주를 사용하여 커넥터를 식별할 수 있습니다. 하지만 일부 커넥터는 여러 범주에 속할 수 있습니다. 예를 들어 SAP는 엔터프라이즈 커넥터 및 온-프레미스 커넥터입니다.

  | 범주 | Description |
  |----------|-------------|
  | [**관리되는 커넥터**](#managed-api-connectors) | Azure Blob Storage, Office 365, Dynamics, Power BI, OneDrive, Salesforce, SharePoint Online 등과 같은 서비스를 사용하는 논리 앱을 만듭니다. |
  | [**온-프레미스 커넥터**](#on-premises-connectors) | [온-프레미스 데이터 게이트웨이][gateway-doc]가 설치 및 설정되면, 이러한 커넥터를 통해 논리 앱에서 온-프레미스 시스템(예: SQL Server, SharePoint Server, Oracle DB, 파일 공유 등)에 액세스할 수 있습니다. |
  | [**통합 계정 커넥터**](#integration-account-connectors) | 통합 계정을 만들고 지불할 때 사용할 수 있는 이러한 커넥터는 AS2, EDIFACT 및 X12 프로토콜을 사용하여 XML 변환 및 유효성 검사, 플랫 파일 인코딩 및 디코딩, B2B(Business-to-Business) 메시지 처리를 수행합니다. |
  |||

<a name="integration-service-environment"></a>

### <a name="connect-from-an-integration-service-environment-ise"></a>ISE(통합 서비스 환경)에서 연결

Azure 가상 네트워크의 리소스에 직접 액세스해야 하는 논리 앱의 경우 전용 리소스에서 논리 앱을 빌드, 배포 및 실행할 수 있는 전용 [ISE(통합 서비스 환경)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)를 만들 수 있습니다. Logic App Designer에서 ISE의 논리 앱에 사용하려는 커넥터를 검색할 때 기본 제공 트리거 및 작업에 대해 **CORE** 레이블이 표시되고, 일부 커넥터에는 **ISE** 레이블이 표시됩니다.

> [!NOTE]
> ISE와 해당 커넥터에서 실행되는 논리 앱은 이러한 커넥터가 실행되는 위치에 관계 없이 고정 요금제 및 소비 기반 요금제를 따릅니다. 자세한 내용은 [Logic Apps 가격 책정 모델](../logic-apps/logic-apps-pricing.md) 및 [Logic Apps 가격 책정 세부 정보](https://azure.microsoft.com/pricing/details/logic-apps/)를 참조하세요.

| 레이블 | 예제 | Description |
|-------|---------|-------------|
| **CORE** | ![CORE 커넥터 예시](./media/apis-list/example-core-connector.png) | 이 레이블이 포함된 기본 제공 트리거는 논리 앱과 동일한 ISE에서 실행됩니다. |
| **ISE** | ![ISE 커넥터 예시](./media/apis-list/example-ise-connector.png) | 이 레이블이 포함된 관리되는 커넥터는 논리 앱과 동일한 ISE에서 실행됩니다. Azure 가상 네트워크에 연결된 온-프레미스 시스템이 있는 경우 ISE를 사용하면 논리 앱이 [온-프레미스 데이터 게이트웨이](../logic-apps/logic-apps-gateway-connection.md) 없이 해당 시스템에 직접 액세스할 수 있습니다. 대신 사용 가능한 경우 해당 시스템의 **ISE** 커넥터를 사용하거나, HTTP 작업을 사용하거나, [사용자 지정 커넥터](#custom)를 사용할 수 있습니다. **ISE** 커넥터가 없는 온-프레미스 시스템의 경우 온-프레미스 데이터 게이트웨이를 사용합니다. 사용 가능한 ISE 커넥터를 검토하려면 [ISE 커넥터](#ise-connectors)를 참조하세요. |
| 레이블 없음 | ![다중 테넌트 커넥터 예시](./media/apis-list/example-multi-tenant-connector.png) | **CORE** 또는 **ISE** 레이블이 없는 모든 커넥터는 계속 사용할 수 있으며 전역 다중 테넌트 Logic Apps 서비스에서 실행됩니다. |
|||

<a name="built-ins"></a>

## <a name="built-in"></a>기본 제공

Logic Apps는 기본 제공 트리거 및 작업을 제공하므로 일정 기반 워크플로를 만들고, 논리 앱에서 다른 앱 및 서비스와 통신하고, 논리 앱을 통해 워크플로를 제어하고, 데이터를 관리하거나 조작할 수 있습니다.

| 이름 | Description |
|------|-------------|
| [![기본 제공 커넥터의 일정][schedule-icon]<br>**일정**][schedule-doc] | - [**되풀이**][schedule-recurrence-doc] 트리거를 사용하여 기본에서 고급 일정에 이르는 지정된 되풀이에서 논리 앱을 실행합니다. <br>- [**슬라이딩 윈도우** 트리거][schedule-sliding-window-doc]로 연속 청크에 있는 데이터를 처리해야 하는 논리 앱을 실행합니다. <br>- [**지연** 작업][schedule-delay-doc]을 사용하여 지정한 기간 동안 논리 앱을 일시 중지합니다. <br>- [**다음 기간까지 지연** 작업][schedule-delay-until-doc]을 사용하여 지정한 날짜와 시간까지 논리 앱을 일시 중지합니다. |
| [![기본 제공 커넥터 일괄 처리][batch-icon]<br>**일괄 처리**][batch-doc] | - **일괄 처리 메시지** 트리거를 사용하여 메시지를 일괄적으로 처리합니다. <br>- **일괄 처리로 메시지 보내기** 작업을 사용하여 기존 일괄 처리 트리거가 있는 논리 앱을 호출합니다. |
| [![기본 제공 커넥터 일괄 처리][http-icon]<br>**일괄 처리**][http-doc] | HTTP에 대한 트리거 및 작업을 사용하여 HTTP 또는 HTTPS 엔드포인트를 호출합니다. 다른 HTTP 기본 제공 트리거 및 작업에는 [HTTP + Swagger 기본 제공 커넥터][http-swagger-doc] 및 [HTTP + 웹후크][http-webhook-doc]가 있습니다. |
| [![기본 제공 커넥터 요청][http-request-icon]<br>**요청**][http-request-doc] | - 논리 앱을 다른 앱이나 서비스에서 호출할 수 있게 하고, Event Grid 리소스 이벤트를 트리거하거나, **요청** 트리거를 사용하여 Azure Security Center 경고에 대한 응답을 트리거합니다. <br>- **응답** 작업을 사용하여 앱이나 서비스에 응답을 보냅니다. |
| [![Azure API Management 기본 제공 커넥터][azure-api-management-icon]<br>**Azure API <br>Management**][azure-api-management-doc] | Azure API Management를 통해 관리하고 게시하는 사용자 고유의 API로 정의된 트리거 및 작업을 호출합니다. |
| [![Azure App Services 기본 제공 커넥터][azure-app-services-icon]<br>**Azure App <br>Services**][azure-app-services-doc] | Azure App Service에서 호스팅되는 Azure API Apps 또는 Web Apps를 호출합니다. Swagger가 포함되는 경우 이러한 앱에서 정의된 트리거 및 작업은 다른 모든 고급 트리거 및 작업처럼 나타납니다. |
| [![Azure Logic Apps 기본 제공 커넥터][azure-logic-apps-icon]<br>**Azure Logic <br>Apps**][nested-logic-app-doc] | **요청** 트리거를 시작하는 다른 논리 앱을 호출합니다. |
|||

### <a name="run-code-from-logic-apps"></a>논리 앱에서 코드 실행

Logic Apps에서는 논리 앱의 워크플로에서 고유한 코드를 실행하기 위한 기본 제공 동작을 제공합니다.

| 이름 | Description |
|------|-------------|
| [![Azure Functions 기본 제공 커넥터][azure-functions-icon]<br>**Azure Functions**][azure-functions-doc] | 논리 앱에서 사용자 지정 코드 조각(C# 또는 Node.js)을 실행하는 Azure 함수를 호출합니다. |
| [![인라인 코드 기본 제공 커넥터][inline-code-icon]<br>**인라인 코드**][inline-code-doc] | Logic Apps에서 JavaScript 코드 조각 추가 및 실행합니다. |
|||

### <a name="control-workflow"></a>워크플로 제어

Logic Apps에서는 논리 앱의 워크플로에서 작업을 구성하고 제어하기 위한 다음과 같은 기본 제공 동작을 제공합니다.

| 이름 | Description |
|------|-------------|
| [![Condition 기본 제공 작업][condition-icon]<br>**Condition**][condition-doc] | 조건을 평가하고, 조건이 true 또는 false인지에 따라 별도의 작업을 실행합니다. |
| [![ 기본 제공 작업][for-each-icon]<br>**For Each**][for-each-doc] | 배열의 모든 항목에 대해 동일한 작업을 수행합니다. |
| [![Scope 기본 제공 작업][scope-icon]<br>**Scope**][scope-doc] | 범위에 속한 작업의 실행이 완료되면 해당 상태를 가져오는 *범위* 로 작업을 그룹화합니다. |
| [![Switch 기본 제공 작업][switch-icon]<br>**Switch**][switch-doc] | 기본 사례를 제외하고는 고유한 값이 할당된 *사례* 로 작업을 그룹화합니다. 할당된 값이 식, 개체 또는 토큰의 결과와 일치하는 사례만 실행합니다. 일치하는 사례가 없는 경우 기본 사례를 실행합니다. |
| [![Terminate 기본 제공 작업][terminate-icon]<br>**Terminate**][terminate-doc] | 활발히 실행 중인 논리 앱 워크플로를 중지합니다. |
| [![Until 기본 제공 작업][until-icon]<br>**Until**][until-doc] | 지정된 조건이 true이거나 일부 상태가 변경될 때까지 작업을 반복합니다. |
|||

### <a name="manage-or-manipulate-data"></a>데이터 관리 또는 조작

Logic Apps에서는 데이터 출력 및 해당 형식으로 작업하기 위한 기본 제공 동작을 제공합니다.

| 이름 | Description |
|------|-------------|
| [![Data Operations 기본 제공 작업][data-operations-icon]<br>**Data Operations**][data-operations-doc] | 데이터를 사용하여 수행하는 작업: <p>- **작성**: 다양한 형식의 여러 입력에서 하나의 출력을 만듭니다. <br>- **CSV 테이블 만들기**: JSON 개체를 사용하여 배열에서 CSV(쉼표로 구분된 값) 테이블을 만듭니다. <br>- **HTML 테이블 만들기**: JSON 개체를 사용하여 배열에서 HTML 테이블을 만듭니다. <br>- **배열 필터링**: 조건을 충족하는 다른 배열의 항목에서 배열을 만듭니다. <br>- **조인**: 배열의 모든 항목에서 문자열을 만들고, 지정한 구분 기호를 사용하여 해당 항목을 구분합니다. <br>- **JSON 구문 분석**: JSON 콘텐츠의 속성 및 해당 값에서 사용자에게 친숙한 토큰을 만들어 워크플로에서 이러한 속성을 사용할 수 있습니다. <br>- **선택**: 다른 배열의 항목이나 값을 변환하고 해당 항목을 지정한 속성에 매핑하여 JSON 개체가 포함된 배열을 만듭니다. |
| ![Date Time 기본 제공 작업][date-time-icon]<br>**날짜/시간 삽입** | 타임스탬프를 사용하여 수행하는 작업: <p>- **시간에 추가**: 타임스탬프에 지정한 단위 수를 추가합니다. <br>- **표준 시간대 변환**: 타임스탬프를 원본 표준 시간대에서 대상 표준 시간대로 변환합니다. <br>- **현재 시간**: 현재 타임스탬프를 문자열로 반환합니다. <br>- **미래 시간 가져오기**: 현재 타임스탬프에 지정한 시간 단위를 더한 값을 반환합니다. <br>- **과거 시간 가져오기**: 현재 타임스탬프에서 지정한 시간 단위를 뺀 값을 반환합니다. <br>- **시간에서 빼기**: 타임스탬프에서 시간 단위 수를 뺍니다. |
| [![Variables 기본 제공 작업][variables-icon]<br>**Variables**][variables-doc] | 변수를 사용하여 수행하는 작업: <p>- **배열 변수에 추가**: 값을 변수로 저장되는 배열의 마지막 항목으로 삽입합니다. <br>- **문자열 변수에 추가**: 값을 변수로 저장되는 문자열의 마지막 문자로 삽입합니다. <br>- **변수 감소**: 변수를 상수 값만큼 줄입니다. <br>- **변수 증가**: 변수를 상수 값만큼 늘립니다. <br>- **변수 초기화**: 변수를 만들고 해당 데이터 형식과 초기 값을 선언합니다. <br>- **변수 설정**: 기존 변수에 다른 값을 할당합니다. |
|||

<a name="managed-api-connectors"></a>

## <a name="managed-connectors"></a>관리되는 커넥터

Logic Apps는 이러한 서비스 또는 시스템을 사용하여 작업, 프로세스 및 워크플로를 자동화하는 데 있어 일반적인 표준 커넥터를 제공합니다.

| 이름 | Description |
|------|-------------|
| [![Azure Service Bus 관리 커넥터][azure-service-bus-icon]<br>**Azure Service Bus**][azure-service-bus-doc] | Logic Apps에서 가장 일반적으로 사용되는 커넥터를 사용하여 비동기 메시지, 세션 및 토픽 구독을 관리합니다. |
| [![SQL Server 관리 커넥터][sql-server-icon]<br>**SQL Server**][sql-server-doc] | 온-프레미스의 SQL Server 또는 클라우드의 Azure SQL Database에 연결하여 레코드를 관리하고 저장 프로시저를 실행하거나 쿼리를 수행할 수 있습니다. |
| [![Azure Blob Storage 관리 커넥터][azure-blob-storage-icon]<br>**Azure Blob<br>Storage**][azure-blob-storage-doc] | 스토리지 계정에 연결하여 Blob 콘텐츠를 만들고 관리할 수 있습니다. |
| [![Office 365 Outlook 관리 커넥터][office-365-outlook-icon]<br>**Office 365<br>Outlook**][office-365-outlook-doc] | 회사 또는 학교 이메일 계정에 연결하여 이메일, 작업, 일정 이벤트 및 회의, 연락처, 요청 등을 만들고 관리할 수 있습니다. |
| [![SFTP-SSH 관리 커넥터][sftp-ssh-icon]<br>**SFTP-SSH**][sftp-ssh-doc] | SSH를 사용하여 인터넷에서 액세스할 수 있는 SFTP 서버에 연결하여 파일 및 폴더를 사용할 수 있습니다. |
| [![SharePoint Online 관리 커넥터][sharepoint-online-icon]<br>**SharePoint<br>Online**][sharepoint-online-doc] | SharePoint Online에 연결하여 파일, 첨부 파일, 폴더 등을 관리할 수 있습니다. |
| [![Azure Queues 관리 커넥터][azure-queues-icon]<br>**Azure <br>Queues**][azure-queues-doc] | Azure Storage 계정에 연결하여 큐와 메시지를 만들고 관리할 수 있습니다. |
| [![FTP 관리 커넥터][ftp-icon]<br>**FTP**][ftp-doc] | 인터넷에서 액세스할 수 있는 FTP 서버에 연결하여 파일 및 폴더를 사용할 수 있습니다. |
| [![File System 관리 커넥터][file-system-icon]<br>**File <br>System**][file-system-doc] | 파일을 만들고 관리할 수 있도록 온-프레미스 파일 공유에 연결합니다. |
| [![Azure Event Hubs 관리 커넥터][azure-event-hubs-icon]<br>**Azure Event Hubs**][azure-event-hubs-doc] | Event Hub를 통해 이벤트를 사용하고 게시합니다. 예를 들어 Event Hubs를 사용하여 논리 앱에서 출력을 가져온 다음, 해당 출력을 실시간 분석 공급자에게 보냅니다. |
| [![Azure Event Grid 관리 커넥터][azure-event-grid-icon]<br>**Azure Event**<br>**Grid**][azure-event-grid-doc] | Azure 리소스 또는 타사 리소스가 변경되는 경우와 같이 Event Grid에서 게시한 이벤트를 모니터링합니다. |
| [![Salesforce 관리 커넥터][salesforce-icon]<br>**Salesforce**][salesforce-doc] | Salesforce 계정에 연결하여 레코드, 작업, 개체 등의 항목을 만들고 관리할 수 있습니다. |
|||

<a name="on-premises-connectors"></a>

## <a name="on-premises-connectors"></a>온-프레미스 커넥터

온-프레미스 시스템에 대한 연결을 만들려면 먼저 [온-프레미스 데이터 게이트웨이를 다운로드, 설치 및 설정][gateway-doc]해야 합니다. 이 게이트웨이는 필요한 네트워크 인프라를 설정하지 않고도 보안 통신 채널을 제공합니다. 

다음은 Logic Apps가 온-프레미스 시스템의 데이터 및 리소스에 액세스하기 위해 제공하여 일반적으로 사용되는 몇 가지 표준 커넥터입니다. 온-프레미스 커넥터 목록은 [지원되는 데이터 원본](../logic-apps/logic-apps-gateway-connection.md#supported-connections)을 참조하세요.

:::row:::
    :::column:::
        [![BizTalk Server 커넥터][biztalk-server-icon]<br>**BizTalk** <br>**Server**][biztalk-server-doc]
    :::column-end:::
    :::column:::
        [![File System 커넥터][file-system-icon]<br>**File <br>System**][file-system-doc]
    :::column-end:::
    :::column:::
        [![DB2 커넥터][ibm-db2-icon]<br>**IBM DB2**][ibm-db2-doc]
    :::column-end:::
    :::column:::
        [![Informix 커넥터][ibm-informix-icon]<br>**IBM** <br>**Informix**][ibm-informix-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![MySQL 커넥터][mysql-icon]<br>**MySQL**][mysql-doc]
    :::column-end:::
    :::column:::
        [![Oracle DB 커넥터][oracle-db-icon]<br>**Oracle DB**][oracle-db-doc]
    :::column-end:::
    :::column:::
        [![PostgreSQL 커넥터][postgre-sql-icon]<br>**PostgreSQL**][postgre-sql-doc]
    :::column-end:::
    :::column:::
        [![SharePoint Server 커넥터][sharepoint-server-icon]<br>**SharePoint <br>Server**][sharepoint-server-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![SQL Server 커넥터][sql-server-icon]<br>**SQL <br>Server**][sql-server-doc]
    :::column-end:::
    :::column:::
        [![Teradata 커넥터][teradata-icon]<br>**Teradata**][teradata-doc]
    :::column-end:::
    :::column:::
        
    :::column-end:::
    :::column:::
        
    :::column-end:::
:::row-end:::

<a name="integration-account-connectors"></a>

## <a name="integration-account-connectors"></a>통합 계정 커넥터

Logic Apps는 Azure에서 EIP(엔터프라이즈 통합 팩)를 통해 사용할 수 있는 [통합 계정](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)을 만들고 지불하는 경우 논리 앱과 B2B(Business-to-Business) 솔루션을 구축하기 위한 표준 커넥터를 제공합니다. 이 계정을 사용하여 거래 파트너, 계약, 맵, 스키마, 인증서 등과 같은 B2B 아티팩트를 만들고 저장할 수 있습니다. 이러한 아티팩트를 사용하려면 논리 앱을 통합 계정과 연결합니다. 현재 BizTalk Server를 사용하는 경우 이러한 커넥터는 이미 익숙한 것처럼 보일 수 있습니다.

:::row:::
    :::column:::
        [![AS2 디코딩 작업][as2-icon]<br>**AS2 <br>디코딩**][as2-doc]
    :::column-end:::
    :::column:::
        [![AS2 디코딩 작업][as2-icon]<br>**AS2 <br>디코딩**][as2-doc]
    :::column-end:::
    :::column:::
        [![EDIFACT 디코딩 작업][edifact-icon]<br>**EDIFACT <br>디코딩**][edifact-decode-doc]
    :::column-end:::
    :::column:::
        [![EDIFACT 인코딩 작업][edifact-icon]<br>**EDIFACT <br>인코딩**][edifact-encode-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![플랫 파일 디코딩 작업][flat-file-decode-icon]<br>**플랫 파일 <br>디코딩**][flat-file-decode-doc]
    :::column-end:::
    :::column:::
        [![플랫 파일 인코딩 작업][flat-file-encode-icon]<br>**플랫 파일 <br>인코딩**][flat-file-encode-doc]
    :::column-end:::
    :::column:::
        [![통합 계정 작업][integration-account-icon]<br>**통합 <br>계정**][integration-account-doc]
    :::column-end:::
    :::column:::
        [![Liquid 변환 작업][liquid-icon]<br>**Liquid** <br>**변환**][json-liquid-transform-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![X12 디코딩 작업][x12-icon]<br>**X12 <br>디코딩**][x12-decode-doc]
    :::column-end:::
    :::column:::
        [![X12 인코딩 작업][x12-icon]<br>**X12 <br>인코딩**][x12-encode-doc]
    :::column-end:::
    :::column:::
        [![XML 변환 작업][xml-transform-icon]<br>**XML** <br>**변환**][xml-transform-doc]
    :::column-end:::
    :::column:::
        [![XML 유효성 검사 작업][xml-validate-icon]<br>**XML <br>유효성 검사**][xml-validate-doc]
    :::column-end:::
:::row-end:::

<a name="enterprise-connectors"></a>

## <a name="enterprise-connectors"></a>엔터프라이즈 커넥터

Logic Apps는 SAP 및 IBM MQ와 같은 엔터프라이즈 시스템에 액세스하기 위한 엔터프라이즈 커넥터를 제공합니다.

:::row:::
    :::column:::
        [![IBM 3270 커넥터][ibm-3270-icon]<br>**IBM 3270**][ibm-3270-doc]
    :::column-end:::
    :::column:::
        [![MQ 커넥터][ibm-mq-icon]<br>**IBM MQ**][ibm-mq-doc]
    :::column-end:::
    :::column:::
        [![SAP 커넥터][sap-icon]<br>**SAP**][sap-connector-doc]
    :::column-end:::
    :::column:::
        
    :::column-end:::
:::row-end:::

<a name="ise-connectors"></a>

## <a name="ise-connectors"></a>ISE 커넥터

전용 [ISE(통합 서비스 환경)](#integration-service-environment)에서 만들고 실행하는 논리 앱의 경우 Logic App Designer는 **CORE** 레이블을 사용하여 ISE에서 실행되는 미리 빌드한 트리거 및 작업을 식별합니다. ISE에서 실행되는 관리 커넥터는 **ISE** 레이블이 표시되고, 전역 다중 테넌트 Logic Apps 서비스에서 실행되는 커넥터의 경우 두 레이블 모두 표시되지 않습니다. 이 목록은 현재 ISE 버전이 있는 커넥터입니다.

:::row:::
    :::column:::
        [![AS2 ISE 커넥터][as2-icon]<br>**AS2**][as2-doc]
    :::column-end:::
    :::column:::
        [![Azure Automation ISE 커넥터][azure-automation-icon]<br>**Azure <br>Automation**][azure-automation-doc]
    :::column-end:::
    :::column:::
        [![Azure Blob Storage ISE 커넥터][azure-blob-storage-icon]<br>**Azure Blob<br>Storage**][azure-blob-storage-doc]
    :::column-end:::
    :::column:::
        [![Azure Cosmos DB ISE 커넥터][azure-cosmos-db-icon]<br>**Azure Cosmos <br> DB**][azure-cosmos-db-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Azure Event Hubs ISE 커넥터][azure-event-hubs-icon]<br>**Azure Event <br>Hubs**][azure-event-hubs-doc]
    :::column-end:::
    :::column:::
        [![Azure Event Grid ISE 커넥터][azure-event-grid-icon]<br>**Azure Event <br>Grid**][azure-event-grid-doc]
    :::column-end:::
    :::column:::
        [![Azure File Storage ISE 커넥터][azure-file-storage-icon]<br>**Azure File<br>Storage**][azure-file-storage-doc]
    :::column-end:::
    :::column:::
        [![Azure Key Vault ISE 커넥터][azure-key-vault-icon]<br>**Azure Key <br>Vault**][azure-key-vault-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Azure Monitor Logs ISE 커넥터][azure-monitor-logs-icon]<br>**Azure Monitor <br>Logs**][azure-monitor-logs-doc]
    :::column-end:::
    :::column:::
        [![Azure Service Bus ISE 커넥터][azure-service-bus-icon]<br>**Azure Service <br>Bus**][azure-service-bus-doc]
    :::column-end:::
    :::column:::
        [![Azure Synapse Analytics ISE 커넥터][azure-sql-data-warehouse-icon]<br>**Azure SQL Data <br>Warehouse**][azure-sql-data-warehouse-doc]
    :::column-end:::
    :::column:::
        [![Azure Table Storage ISE 커넥터][azure-table-storage-icon]<br>**Azure Table <br>Storage**][azure-table-storage-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Azure Queues ISE 커넥터][azure-queues-icon]<br>**Azure <br>Queues**][azure-queues-doc]
    :::column-end:::
    :::column:::
        [![EDIFACT ISE 커넥터][edifact-icon]<br>**EDIFACT**][edifact-doc]
    :::column-end:::
    :::column:::
        [![File System ISE 커넥터][file-system-icon]<br>**File <br>System**][file-system-doc]
    :::column-end:::
    :::column:::
        [![FTP ISE 커넥터][ftp-icon]<br>**FTP**][ftp-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![IBM 3270 ISE 커넥터][ibm-3270-icon]<br>**IBM 3270**][ibm-3270-doc]
    :::column-end:::
    :::column:::
        [![DB2 ISE 커넥터][ibm-db2-icon]<br>**IBM DB2**][ibm-db2-doc]
    :::column-end:::
    :::column:::
        [![MQ ISE 커넥터][ibm-mq-icon]<br>**IBM MQ**][ibm-mq-doc]
    :::column-end:::
    :::column:::
        [![SAP ISE 커넥터][sap-icon]<br>**SAP**][sap-connector-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![SFTP-SSH ISE 커넥터][sftp-ssh-icon]<br>**SFTP-SSH**][sftp-ssh-doc]
    :::column-end:::
    :::column:::
        [![SMTP ISE 커넥터][smtp-icon]<br>**SMTP**][smtp-doc]
    :::column-end:::
    :::column:::
        [![SQL Server ISE 커넥터][sql-server-icon]<br>**SQL <br>Server**][sql-server-doc]
    :::column-end:::
    :::column:::
        [![X12 ISE 커넥터][x12-icon]<br>**X12**][x12-doc]
    :::column-end:::
:::row-end:::

자세한 내용은 다음 항목을 참조하세요.

* [Azure Logic Apps에서 Azure 가상 네트워크 리소스에 액세스](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)
* [Logic Apps 가격 책정 모델](../logic-apps/logic-apps-pricing.md)
* [Azure Logic Apps에서 Azure 가상 네트워크에 연결](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)

<a name="triggers-actions"></a>

## <a name="triggers-and-action-types"></a>트리거 및 작업 유형

커넥터는 트리거, 작업 또는 두 가지 모두 제공할 수 있습니다. 트리거는 논리 앱의 첫 번째 단계로, 일반적으로 트리거를 발생시키고 논리 앱 실행을 시작하는 이벤트를 지정합니다. 예를 들어 FTP 커넥터에는 "파일이 추가 또는 수정된 경우" 논리 앱을 시작하는 트리거가 있습니다. 일부 트리거는 지정된 이벤트 또는 데이터를 정기적으로 확인한 다음 지정된 이벤트 또는 데이터를 감지할 때 발생합니다. 대기 상태에서 특정 이벤트가 발생하거나 새 데이터를 사용할 수 있는 경우 즉시 발생하는 트리거도 있습니다. 또한 트리거는 논리 앱에 필요한 데이터를 함께 전달합니다. 논리 앱은 워크플로 전체에서 해당 데이터를 읽고 사용할 수 있습니다. 예를 들어 Office 365 Outlook 커넥터에는 해당 이메일의 콘텐츠를 논리 앱의 워크플로로 전달할 수 있는 "새 이메일이 도착한 경우" 트리거가 있습니다.

트리거가 발동된 후 Azure Logic Apps는 논리 앱의 인스턴스를 만들고 논리 앱의 워크플로에서 작업 실행을 시작합니다. 작업은 트리거를 따르고 논리 앱의 워크플로에서 작업을 수행하는 단계입니다. 예를 들어 SQL 데이터베이스에서 고객 데이터를 가져오고 이후 작업에서 해당 데이터를 처리하는 논리 앱을 만들 수 있습니다.

Azure Logic Apps에서 제공하는 일반적인 트리거 종류는 다음과 같습니다.

* 되풀이 트리거: 이 트리거는 지정된 일정에 따라 실행되고 특정 서비스 또는 시스템과 긴밀하게 연결되지 않습니다.

* 폴링 트리거: 이 트리거는 지정된 일정에 따라 특정 서비스 또는 시스템을 정기적으로 폴링하여 새 데이터를 확인 하거나 특정 이벤트가 발생했는지 여부를 확인합니다. 새 데이터를 사용할 수 있거나 특정 이벤트가 발생한 경우 트리거는 논리 앱의 새 인스턴스를 만들고 실행하며, 이제 입력으로 전달된 데이터를 사용할 수 있습니다.

* 푸시 트리거: 이 트리거는 새 데이터 또는 이벤트 발생을 대기하고 수신합니다. 새 데이터를 사용할 수 있거나 이벤트가 발생한 경우 트리거는 논리 앱의 새 인스턴스를 만들고 실행하며, 이제 입력으로 전달된 데이터를 사용할 수 있습니다.

<a name="connections"></a>

## <a name="connector-configuration"></a>커넥터 구성

각 커넥터의 트리거 및 작업은 사용자가 구성할 수 있는 고유한 속성을 제공합니다. 또한 대부분의 커넥터에서는 먼저 대상 서비스 또는 시스템에 대한 연결을 만들고 인증 자격 증명 또는 기타 구성 정보를 제공해야 논리 앱에서 트리거 또는 작업을 사용할 수 있습니다. 예를 들어 Office 365 Outlook 이메일 계정에 액세스하여 작업을 수행하려면 먼저 해당 계정에 대한 연결에 권한을 부여해야 합니다.

Azure AD(Azure Active Directory) OAuth를 사용하는 커넥터의 경우 연결을 만드는 것은 액세스 토큰을 [암호화](../security/fundamentals/encryption-overview.md)한 후 Azure 암호 저장소에 안전하게 저장하는 서비스(예: Office 365, Salesforce 또는 GitHub)에 로그인하는 것을 의미합니다. FTP 및 SQL과 같은 다른 커넥터에는 서버 주소, 사용자 이름 및 암호와 같은 구성 세부 정보를 포함하는 연결이 필요합니다. 이러한 연결 구성 세부 정보 또한 암호화된 후 안전하게 저장됩니다. [Azure에서 암호화](../security/fundamentals/encryption-overview.md)에 대해 자세히 알아봅니다.

서비스 또는 시스템이 허용하는 한 연결에서 대상 서비스 또는 시스템에 액세스할 수 있습니다. Office 365 및 Dynamics와 같은 Azure AD OAuth 연결을 사용하는 서비스의 경우 Azure Logic Apps는 액세스 토큰을 무기한으로 새로 고칩니다. 다른 서비스의 경우 Azure Logic Apps에서 새로 고치지 않고 토큰을 사용할 수 있는 기간에 제한이 있을 수 있습니다. 일반적으로 일부 작업은 암호 변경과 같이 모든 액세스 토큰을 무효화합니다.

<a name="recurrence-behavior"></a>

## <a name="recurrence-behavior"></a>되풀이 동작

[되풀이 트리거](../connectors/connectors-native-recurrence.md)와 같이 기본적으로 Azure Logic Apps에서 실행되는 되풀이 기본 제공 트리거의 동작은 SQL 커넥터 트리거와 같은 연결을 먼저 만들어야 하는 되풀이 연결 기반 트리거의 동작과 다릅니다.

하지만 두 종류의 트리거 모두 되풀이에서 특정 시작 날짜와 시간을 지정하지 않는 경우에는 트리거의 되풀이 설정에도 불구하고 논리 앱을 저장하거나 배포할 때 첫 번째 되풀이가 즉시 실행됩니다. 이 동작을 방지하려면 첫 번째 되풀이를 실행할 시작 날짜와 시간을 제공합니다.

<a name="recurrence-built-in"></a>

### <a name="recurrence-for-built-in-triggers"></a>기본 제공 트리거의 되풀이

되풀이 기본 제공 트리거는 사용자가 지정하는 표준 시간대를 포함하여 사용자가 설정한 일정을 적용합니다. 하지만 되풀이에서 이후 되풀이를 실행하는 특정 시간과 같은 기타 고급 일정 옵션을 지정하지 않는 경우 해당 되풀이는 마지막 트리거 실행을 기준으로 합니다. 결과적으로 해당 되풀이에 대한 시작 시간은 스토리지 호출 중의 대기 시간과 같은 요소로 인해 드리프트될 수 있습니다. 또한 표준 시간대를 선택하지 않으면 트리거가 실행될 때 DST(일광 절약 시간)에 영향을 줄 수 있습니다. 예를 들어 DST가 시작될 때 시작 시간을 1시간 앞으로 이동하고, DST가 종료되면 1시간 뒤로 이동합니다.

논리 앱이 지정된 시작 시간에 실행되고 되풀이를 누락하지 않도록 하려면 특히 빈도가 며칠 이상일 때 다음 해결 방법을 시도해 보세요.

* 논리 앱이 지정된 시작 시간에 실행되도록 표준 시간대를 선택해야 합니다. 그렇지 않은 경우 트리거가 실행될 때 DST에 영향을 줄 수 있습니다. 예를 들어 DST가 시작될 때 시작 시간을 1시간 앞으로 이동하고, DST가 종료되면 1시간 뒤로 이동합니다.

  작업을 예약할 때 Logic Apps는 메시지를 처리하기 위해 큐에 배치하고 마지막 작업이 실행된 UTC 시간과 다음 작업이 실행되도록 예약된 UTC 시간을 기준으로 메시지를 사용할 수 있는 시기를 지정합니다. 시간대를 지정하면 논리 앱에 대한 UTC 시간도 계절적 시간 변경에 대응하도록 전환됩니다. 그러나 시간이 이동하면 일부 기간에서 문제가 발생할 수 있습니다. 자세한 내용 및 예제는 [일광 절약 시간 및 표준 시간에 대한 되풀이](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#daylight-saving-standard-time)를 참조하세요.

* 되풀이 트리거를 사용하고 **일** 및 **주** 빈도에 대해서만 사용할 수 있는 **시간 선택** 과 **분 선택** 으로 명명된 속성을 사용하여 되풀이의 시작 날짜와 시간 및 후속 되풀이를 실행할 특정 시간을 지정합니다.

* 되풀이 트리거가 아니라 [슬라이딩 윈도우 트리거](../connectors/connectors-native-sliding-window.md)를 사용합니다.

<a name="recurrence-connection-based"></a>

### <a name="recurrence-for-connection-based-triggers"></a>연결 기반 트리거의 되풀이

SQL 또는 SFTP-SSH와 같은 되풀이 연결 기반 트리거에서 일정은 실행을 제어하는 유일한 드라이버는 아니며, 표준 시간대는 최초 시작 시간만 결정합니다. 후속 실행은 되풀이 일정, 마지막 트리거 실행 및 런타임 시 예기치 않은 동작을 발생시킬 수 있는 기타 요인에 따라 달라지며, 예를 들면 다음과 같습니다.

* 트리거가 더 많은 데이터가 있는 서버에 액세스하는지 여부에 따라 트리거가 즉시 페치를 시도합니다.

* 트리거가 발생하는 오류 또는 다시 시도.

* 스토리지 호출 동안의 대기 시간.

* DST(일광 절약 시간)가 시작 및 종료될 때 지정된 일정을 유지하지 않습니다.

* 다음 런타임이 발생할 때 영향을 줄 수 있는 다른 요인.

이러한 문제를 해결하려면 다음 해결 방법을 사용하세요.

* DST가 적용될 때 되풀이 시간이 바뀌지 않도록 되풀이를 수동으로 조정하면 논리 앱이 예상 시간에 계속 실행됩니다. 그렇지 않으면 DST가 시작될 때 시작 시간이 1시간 앞으로 이동하고, DST가 종료되면 1시간 뒤로 이동합니다.

* 되풀이 트리거를 사용하면 시간대, 시작 날짜 및 시간 및 **일**, **주** 빈도에 대해서만 사용할 수 있는 **시간 선택** 과 **분 선택** 으로 명명된 속성을 사용하여 후속 되풀이를 실행할 특정 시간을 지정할 수 있습니다. 그러나 시간이 이동하면 일부 기간에서 문제가 발생할 수 있습니다. 자세한 내용 및 예제는 [일광 절약 시간 및 표준 시간에 대한 되풀이](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#daylight-saving-standard-time)를 참조하세요.

* 되풀이 누락을 방지하도록 되풀이 트리거 대신 [슬라이딩 윈도우 트리거](../connectors/connectors-native-sliding-window.md)를 사용합니다.

<a name="custom"></a>

## <a name="custom-apis-and-connectors"></a>사용자 지정 API 및 커넥터

사용자 지정 코드를 실행하거나 커넥터로 사용할 수 없는 API를 호출하려면 [사용자 지정 API Apps를 만들어](../logic-apps/logic-apps-create-api-app.md) Logic Apps 플랫폼을 확장할 수 있습니다. 또한 *모든* REST 또는 SOAP 기반 API에 대한 [사용자 지정 커넥터를 만들면](../logic-apps/custom-connector-overview.md) Azure 구독의 모든 논리 앱에서 해당 API를 사용할 수 있습니다. Azure에서 모든 사용자가 사용할 수 있도록 사용자 지정 API 앱 또는 커넥터를 공개하려면 [Microsoft 인증을 위해 커넥터를 제출](/connectors/custom-connectors/submit-certification)할 수 있습니다.

> [!NOTE]
> [ISE(통합 서비스 환경)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)에서 배포 및 실행하는 논리 앱은 Azure 가상 네트워크의 리소스에 직접 액세스할 수 있습니다. 온-프레미스 데이터 게이트웨이를 필요로 하는 사용자 지정 커넥터를 사용하고, ISE 외부에서 커넥터를 만든 경우 ISE의 논리 앱에서도 이러한 커넥터를 사용할 수 있습니다.
>
> ISE 내부에서 만든 사용자 지정 커넥터는 온-프레미스 데이터 게이트웨이에서 작동하지 않습니다. 하지만 이러한 커넥터는 ISE를 호스트하는 Azure 가상 네트워크에 연결된 온-프레미스 데이터 원본에 직접 액세스할 수 있습니다. 따라서 ISE의 논리 앱은 이러한 리소스와 통신할 때 데이터 게이트웨이가 필요하지 않을 수 있습니다.
>
> ISE 만들기에 대한 자세한 내용은 [Azure Logic Apps에서 Azure 가상 네트워크에 연결](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)을 참조하세요.

## <a name="get-ready-for-deployment"></a>배포 준비 완료

논리 앱 내에서 연결을 만들더라도 연결은 자체 리소스 정의가 있는 별도의 Azure 리소스입니다. 이러한 연결 리소스 정의를 검토하려면 [Azure에서 Visual Studio로 논리 앱을 다운로드](../logic-apps/manage-logic-apps-with-visual-studio.md)합니다. 이는 일반적으로 배포할 준비가 된 유효한 매개 변수가 있는 논리 앱 템플릿을 만드는 가장 쉬운 방법입니다.

<a name="block-connections"></a>

## <a name="block-creating-connections"></a>연결 만들기 차단

Azure Logic Apps의 커넥터를 사용하여 특정 리소스에 연결하는 것이 조직에서 허용되지 않는 경우 [Azure Policy](../governance/policy/overview.md)를 사용하여 논리 앱 워크플로의 특정 커넥터에 대해 [이러한 연결을 만드는 기능을 차단](../logic-apps/block-connections-connectors.md)할 수 있습니다. 자세한 내용은 [Azure Logic Apps의 특정 커넥터에서 만든 연결 차단](../logic-apps/block-connections-connectors.md)을 참조하세요.

## <a name="known-issues"></a>알려진 문제

#### <a name="error-badgateway-client-request-id-guid"></a>오류: BadGateway. 클라이언트 요청 ID: '{GUID}'

이 오류는 하나 이상의 연결이 SFTP AD SQL과 같은 Azure Active Directory(Azure AD) OAuth 인증을 지원하지 않아 해당 연결을 중단하는 논리 앱의 태그를 업데이트하면서 발생합니다. 이 동작을 방지하려면 해당 태그의 업데이트를 방지합니다.

## <a name="next-steps"></a>다음 단계

* [전체 커넥터 목록](/connectors) 보기
* [첫 번째 논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [논리 앱에 대한 사용자 지정 커넥터 만들기](/connectors/custom-connectors/)
* [논리 앱에 대한 사용자 지정 API 만들기](../logic-apps/logic-apps-create-api-app.md)

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
[azure-devops-icon]: ./media/apis-list/azure-devops.png
[azure-document-db-icon]: ./media/apis-list/azure-document-db.png
[azure-event-grid-icon]: ./media/apis-list/azure-event-grid.png
[azure-event-grid-publish-icon]: ./media/apis-list/azure-event-grid-publish.png
[azure-event-hubs-icon]: ./media/apis-list/azure-event-hubs.png
[azure-file-storage-icon]: ./media/apis-list/azure-file-storage.png
[azure-key-vault-icon]: ./media/apis-list/azure-key-vault.png
[azure-ml-icon]: ./media/apis-list/azure-ml.png
[azure-monitor-logs-icon]: ./media/apis-list/azure-monitor-logs.png
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
[campfire-icon]: ./media/apis-list/campfire.png
[common-data-service-icon]: ./media/apis-list/common-data-service.png
[dynamics-365-financials-icon]: ./media/apis-list/dynamics-365-financials.png
[dynamics-365-operations-icon]: ./media/apis-list/dynamics-365-operations.png
[easy-redmine-icon]: ./media/apis-list/easyredmine.png
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
[mandrill-icon]: ./media/apis-list/mandrill.png
[mysql-icon]: ./media/apis-list/mysql.png
[office-365-outlook-icon]: ./media/apis-list/office-365.png
[onedrive-icon]: ./media/apis-list/onedrive.png
[onedrive-for-business-icon]: ./media/apis-list/onedrive-business.png
[oracle-db-icon]: ./media/apis-list/oracle-db.png
[outlook.com-icon]: ./media/apis-list/outlook.png
[pagerduty-icon]: ./media/apis-list/pagerduty.png
[pinterest-icon]: ./media/apis-list/pinterest.png
[postgre-sql-icon]: ./media/apis-list/postgre-sql.png
[project-online-icon]: ./media/apis-list/projecton-line.png
[redmine-icon]: ./media/apis-list/redmine.png
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
[twilio-icon]: ./media/apis-list/twilio.png
[vimeo-icon]: ./media/apis-list/vimeo.png
[wordpress-icon]: ./media/apis-list/wordpress.png
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

<!--Other doc links-->
[gateway-doc]: ../logic-apps/logic-apps-gateway-connection.md "온-프레미스 데이터 게이트웨이를 사용하여 논리 앱에서 온-프레미스 데이터 원본에 연결"

<!--Built-in doc links-->
[azure-api-management-doc]: ../api-management/get-started-create-service-instance.md "API 관리 및 게시를 위한 Azure API Management 서비스 인스턴스 만들기"
[azure-app-services-doc]: ../logic-apps/logic-apps-custom-api-host-deploy-call.md "App Service API Apps과 논리 앱 통합"
[azure-functions-doc]: ../logic-apps/logic-apps-azure-functions.md "논리 앱을 Azure Functions와 통합"
[batch-doc]: ../logic-apps/logic-apps-batch-process-send-receive-messages.md "그룹 또는 일괄 처리로 메시지 처리"
[condition-doc]: ../logic-apps/logic-apps-control-flow-conditional-statement.md "조건 평가 및 조건이 true인지 false인지에 따라 별도의 작업 실행"
[for-each-doc]: ../logic-apps/logic-apps-control-flow-loops.md#foreach-loop "배열의 모든 항목에 대해 동일한 작업 수행"
[http-doc]: ./connectors-native-http.md "논리 앱에서 HTTP 또는 HTTPS 엔드포인트 호출"
[http-request-doc]: ./connectors-native-reqres.md "논리 앱에서 HTTP 요청 수신"
[http-response-doc]: ./connectors-native-reqres.md "논리 앱에서 HTTP 요청에 응답"
[http-swagger-doc]: ./connectors-native-http-swagger.md "논리 앱에서 REST 엔드포인트 호출"
[http-webhook-doc]: ./connectors-native-webhook.md "HTTP 또는 HTTPS 엔드포인트의 특정 이벤트 대기"
[inline-code-doc]: ../logic-apps/logic-apps-add-run-inline-code.md "논리 앱에서 JavaScript 코드 조각 추가 및 실행"
[nested-logic-app-doc]: ../logic-apps/logic-apps-http-endpoint.md "중첩된 워크플로와 논리 앱 통합"
[query-doc]: ../logic-apps/logic-apps-perform-data-operations.md#filter-array-action "쿼리 작업을 사용하여 배열 선택 및 필터링"
[schedule-doc]: ../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md "일정에 따라 논리 앱 실행"
[schedule-delay-doc]: ./connectors-native-delay.md "다음 작업 실행 지연"
[schedule-delay-until-doc]: ./connectors-native-delay.md "다음 작업 실행 지연"
[schedule-recurrence-doc]:  ./connectors-native-recurrence.md "되풀이 일정에 따라 논리 앱 실행"
[schedule-sliding-window-doc]: ./connectors-native-sliding-window.md "연속 청크로 데이터를 처리해야 하는 논리 앱 실행"
[scope-doc]: ../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md "그룹에 속한 작업의 실행이 완료되면 해당 상태를 가져오는 그룹으로 작업을 구성"
[switch-doc]: ../logic-apps/logic-apps-control-flow-switch-statement.md "고유 값이 할당된 사례로 작업 구성. 값이 식, 개체, 토큰의 결과와 일치하는 사례만 실행. 일치하는 항목이 없으면 기본 사례 실행"
[terminate-doc]: ../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action "논리 앱에 대해 활발히 실행되는 워크플로 중지 또는 취소"
[until-doc]: ../logic-apps/logic-apps-control-flow-loops.md#until-loop "지정된 조건이 true이거나 일부 상태가 변경될 때까지 작업 반복"
[data-operations-doc]: ../logic-apps/logic-apps-perform-data-operations.md "배열 필터링이나 CSV 및 HTML 테이블 생성과 같은 데이터 작업 수행"
[variables-doc]: ../logic-apps/logic-apps-create-variables-store-values.md "변수를 사용하여 작업 수행(예: 초기화, 설정, 증가, 감소 및 문자열 또는 배열 변수에 추가)"

<!--Managed connector doc links-->
[azure-automation-doc]: /connectors/azureautomation/ "클라우드 및 온-프레미스 인프라를 위한 자동화 작업 만들기 및 관리"
[azure-blob-storage-doc]: ./connectors-create-api-azureblobstorage.md "Azure Blob Storage 커넥터와 Blob 컨테이너의 파일 관리"
[azure-cosmos-db-doc]: /connectors/documentdb/ "문서 및 저장 프로시저에 액세스할 수 있도록 Azure Cosmos DB에 연결"
[azure-event-grid-doc]: ../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md "Azure 리소스 또는 타사 리소스가 변경되는 경우와 같이 Event Grid에서 게시한 이벤트 모니터링"
[azure-event-hubs-doc]: ./connectors-create-api-azure-event-hubs.md "논리 앱과 Event Hubs 간에 이벤트를 수신하고 보낼 수 있도록 Azure Event Hubs에 연결"
[azure-file-storage-doc]: /connectors/azurefile/ "파일을 만들고 업데이트하고 가져오고 삭제할 수 있도록 Azure Storage 계정에 연결"
[azure-key-vault-doc]: /connectors/keyvault/ "비밀 및 키를 관리할 수 ​​있도록 Azure Key Vault에 연결"
[azure-monitor-logs-doc]: /connectors/azuremonitorlogs/ "Log Analytics 작업 영역 및 Application Insights 구성 요소에서 Azure Monitor 로그에 대해 쿼리 실행"
[azure-queues-doc]: /connectors/azurequeues/ "큐와 메시지를 만들고 관리할 수 있도록 Azure Storage 계정에 연결"
[azure-service-bus-doc]: ./connectors-create-api-servicebus.md "Service Bus 큐 및 항목에서 메시지를 보내고 Service Bus 큐 및 구독에서 메시지를 받습니다."
[azure-sql-data-warehouse-doc]: /connectors/sqldw/ "데이터를 볼 수 있도록 Azure Synapse Analytics에 연결"
[azure-table-storage-doc]: /connectors/azuretables/ "테이블 등을 만들고 업데이트하고 쿼리할 수 ​​있도록 Azure Storage 계정에 연결"
[biztalk-server-doc]: /connectors/biztalk/ "BizTalk Server에 연결하여 Azure Logic Apps와 나란히 BizTalk 기반 애플리케이션을 실행할 수 있음"
[file-system-doc]: ../logic-apps/logic-apps-using-file-connector.md "온-프레미스 파일 시스템에 연결"
[ftp-doc]: ./connectors-create-api-ftp.md "파일 업로드, 가져오기, 삭제 등과 같은 FTP 태스크의 경우 FTP / FTPS 서버에 연결"
[github-doc]: ./connectors-create-api-github.md "GitHub에 연결 및 문제 추적"
[google-calendar-doc]: ./connectors-create-api-googlecalendar.md "Google 달력에 연결하고 달력을 관리할 수 있음"
[google-sheets-doc]: ./connectors-create-api-googlesheet.md "Google Sheets에 연결하여 시트를 수정할 수 있음"
[google-tasks-doc]: ./connectors-create-api-googletasks.md "작업을 관리할 수 ​​있도록 Google Tasks에 연결"
[ibm-3270-doc]: ./connectors-run-3270-apps-ibm-mainframe-create-api-3270.md "IBM 메인프레임에서 3270 앱에 연결"
[ibm-db2-doc]: ./connectors-create-api-db2.md "클라우드 또는 온-프레미스에서 IBM DB2에 연결. 행 업데이트, 테이블 가져오기 등의 작업 수행"
[ibm-informix-doc]: ./connectors-create-api-informix.md "클라우드 또는 온-프레미스에서 Informix에 연결. 행 읽기, 테이블 나열 등의 작업 수행"
[ibm-mq-doc]: ./connectors-create-api-mq.md "온-프레미스 또는 Azure에서 IBM MQ에 연결하여 메시지 송수신"
[instagram-doc]: ./connectors-create-api-instagram.md "Instagram에 연결. 이벤트 트리거 또는 조치"
[mandrill-doc]: ./connectors-create-api-mandrill.md "통신을 위해 Mandrill에 연결"
[mysql-doc]: /connectors/mysql/ "데이터를 읽고 쓸 수 있도록 온-프레미스 MySQL 데이터베이스에 연결"
[office-365-outlook-doc]: ./connectors-create-api-office365-outlook.md "직장 또는 학교 계정에 연결하여 이메일을 보내고 받고 캘린더 및 연락처를 관리하는 등의 작업을 할 수 있음"
[onedrive-doc]: ./connectors-create-api-onedrive.md "개인 Microsoft OneDrive에 연결하여 파일을 업로드, 삭제, 나열하는 등의 작업을 수행할 수 있음"
[onedrive-for-business-doc]: ./connectors-create-api-onedriveforbusiness.md "비즈니스 Microsoft OneDrive에 연결하여 파일을 업로드, 삭제, 나열하는 등의 작업을 수행할 수 있음"
[oracle-db-doc]: ./connectors-create-api-oracledatabase.md "행 추가, 삽입, 삭제 등을 수행할 수 있도록 Oracle 데이터베이스에 연결"
[outlook.com-doc]: ./connectors-create-api-outlook.md "이메일, 일정, 연락처 등을 관리할 수 ​​있도록 Outlook 사서함에 연결"
[postgre-sql-doc]: /connectors/postgresql/ "테이블에서 데이터를 읽을 수 있도록 PostgreSQL 데이터베이스에 연결"
[salesforce-doc]: ./connectors-create-api-salesforce.md "Salesforce 계정에 연결. 계정, 잠재 고객, 영업 기회 등 관리"
[sap-connector-doc]: ../logic-apps/logic-apps-using-sap-connector.md "온-프레미스 SAP 시스템에 연결"
[sendgrid-doc]: ./connectors-create-api-sendgrid.md "SendGrid에 연결. 이메일 보내기 및 수신자 목록 관리"
[sftp-ssh-doc]: ./connectors-sftp-ssh.md "SSH를 사용하여 SFTP 계정에 연결. 파일 업로드, 가져오기, 삭제 등"
[sharepoint-server-doc]: ./connectors-create-api-sharepoint.md "SharePoint 온-프레미스 서버에 연결. 문서, 목록 항목 등 관리"
[sharepoint-online-doc]: ./connectors-create-api-sharepoint.md "SharePoint Online에 연결. 문서, 목록 항목 등 관리"
[slack-doc]: ./connectors-create-api-slack.md "Slack에 연결하고 Slack 채널에 메시지 게시"
[smtp-doc]: ./connectors-create-api-smtp.md "SMTP 서버에 연결 및 첨부 파일이 포함된 이메일 보내기"
[sparkpost-doc]: ./connectors-create-api-sparkpost.md "통신을 위해 SparkPost에 연결"
[sql-server-doc]: ./connectors-create-api-sqlazure.md "Azure SQL Database 또는 SQL Server에 연결. SQL 데이터베이스 테이블에서 항목 생성, 업데이트, 가져오기 및 삭제"
[teradata-doc]: /connectors/teradata/ "Teradata 데이터베이스에 연결하여 테이블에서 데이터 읽기"
[twilio-doc]: ./connectors-create-api-twilio.md "Twilio에 연결. 메시지 보내기 및 받기, 사용 가능한 번호 받기, 수신 전화 번호 관리 등의 작업 수행"
[youtube-doc]: ./connectors-create-api-youtube.md "YouTube에 연결. 비디오 및 채널 관리"

<!--Enterprise Intregation Pack doc links-->
[as2-doc]: ../logic-apps/logic-apps-enterprise-integration-as2.md "AS2 프로토콜을 사용하는 메시지 인코딩 및 디코딩"
[edifact-doc]: ../logic-apps/logic-apps-enterprise-integration-edifact.md "EDIFACT 프로토콜을 사용하는 메시지 인코딩 및 디코딩"
[edifact-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-decode.md "EDIFACT 프로토콜을 사용하는 메시지 디코딩"
[edifact-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-encode.md "EDIFACT 프로토콜을 사용하는 메시지 인코딩"
[flat-file-decode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "엔터프라이즈 통합 플랫 파일에 대해 알아봅니다."
[flat-file-encode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "엔터프라이즈 통합 플랫 파일에 대해 알아봅니다."
[integration-account-doc]: ../logic-apps/logic-apps-enterprise-integration-metadata.md "통합 계정 아티팩트에 대한 메타데이터 관리"
[json-liquid-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-liquid-transform.md "Liquid 템플릿으로 JSON 변환"
[x12-doc]: ../logic-apps/logic-apps-enterprise-integration-x12.md "X12 프로토콜을 사용하는 메시지 인코딩 및 디코딩"
[x12-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-decode.md "X12 프로토콜을 사용하는 메시지 디코딩"
[x12-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-encode.md "X12 프로토콜을 사용하는 메시지 인코딩"
[xml-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-transform.md "XML 메시지 변환"
[xml-validate-doc]: ../logic-apps/logic-apps-enterprise-integration-xml-validation.md "XML 메시지 유효성 검사"
