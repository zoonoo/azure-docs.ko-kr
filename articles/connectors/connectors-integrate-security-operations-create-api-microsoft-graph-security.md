---
title: Microsoft Graph Security와 보안 작업 통합 - Azure Logic Apps
description: Microsoft Graph Security 및 Azure Logic Apps에서 보안 작업을 관리하여 앱의 위협 방지, 탐지 및 대응 기능 향상
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: preetikr
ms.author: preetikr
ms.reviewer: klam, estfan, LADocs
ms.topic: article
ms.date: 01/30/19
tags: connectors
ms.openlocfilehash: 647df9e73804c8f261b58d5ede7c4b030d448fed
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55513308"
---
# <a name="improve-threat-protection-by-integrating-security-operations-with-microsoft-graph-security--azure-logic-apps"></a>Microsoft Graph Security 및 Azure Logic Apps와 보안 작업을 통합하여 위협 방지 향상

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) 및 [Microsoft Graph Security](https://docs.microsoft.com/graph/security-concept-overview) 커넥터를 사용하면 Microsoft 보안 제품, 서비스 및 파트너를 통합하는 자동화된 워크플로를 만들어 앱이 위협을 탐지 및 방지하고 이에 대응하는 방식을 향상할 수 있습니다. 예를 들어 Microsoft Graph Security 엔터티(예: 경고)를 모니터링하고 관리하는 [Azure Security Center 플레이북](../security-center/security-center-playbooks.md)를 만들 수 있습니다. Microsoft Graph Security 커넥터가 지원하는 몇 가지 시나리오는 다음과 같습니다.

* 쿼리 또는 경고 ID를 기준으로 경고를 가져옵니다. 예를 들어 높은 심각도 경고를 포함하는 목록을 가져올 수 있습니다.
* 경고를 업데이트합니다. 예를 들어 경고 할당을 업데이트하거나, 경고에 주석을 추가하거나, 경고에 태그를 지정할 수 있습니다.
* [경고 구독(웹후크)](https://docs.microsoft.com/graph/api/resources/webhooks)을 만들어 경고가 생성되거나 변경된 시기를 모니터링합니다.
* 경고 구독을 관리합니다. 예를 들어 활성 구독을 가져오거나, 구독의 만료 시간을 확장하거나, 구독을 삭제할 수 있습니다.

논리 앱의 워크플로는 Microsoft Graph Security 커넥터에서 응답을 가져오고 해당 출력을 워크플로의 다른 작업에 제공하는 작업을 사용할 수 있습니다. 워크플로의 다른 작업에서 Microsoft Graph Security 커넥터 작업의 결과물을 사용하게 할 수도 있습니다. 예를 들어 Microsoft Graph Security 커넥터를 통해 높은 심각도 경고를 가져오는 경우 Outlook 커넥터를 사용하여 해당 경고를 메일 메시지로 보낼 수 있습니다. 

Microsoft Graph Security에 대한 자세한 내용은 [Microsoft Graph 보안 API 개요](https://aka.ms/graphsecuritydocs)를 참조하세요. 논리 앱을 처음 접하는 경우 [Azure Logic Apps란?](../logic-apps/logic-apps-overview.md)을 검토합니다. Microsoft Flow 또는 PowerApps를 찾고 있는 경우 [Flow란?](https://flow.microsoft.com/) 또는 [PowerApps란?](https://powerapps.microsoft.com/)을 참조하세요.

## <a name="prerequisites"></a>필수 조건

* Azure 구독. Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다. 

* Microsoft Graph Security 커넥터를 사용하려면 [Microsoft Graph Security 인증 요구 사항](https://aka.ms/graphsecurityauth)의 일부인 ‘명시적으로 제공된’ Azure AD(Active Directory) 테넌트 관리자 동의가 있어야 합니다. 이 동의에는 [Azure Portal](https://portal.azure.com)에서 확인할 수 있는 Microsoft Graph Security 커넥터의 애플리케이션 ID 및 이름이 필요합니다.

   | 자산 | 값 |
   |----------|-------|
   | **애플리케이션 이름** | `MicrosoftGraphSecurityConnector` |
   | **애플리케이션 ID** | `c4829704-0edc-4c3d-a347-7c4a67586f3c` |
   |||

   커넥터에 대한 동의를 부여하기 위해 Azure AD 테넌트 관리자는 다음 단계 중 하나를 수행할 수 있습니다.

   * [Azure AD 애플리케이션에 대한 테넌트 관리자 동의를 부여](../active-directory/develop/v2-permissions-and-consent.md)합니다.

   * 논리 앱을 처음 실행하는 동안 앱이 [애플리케이션 동의 환경](../active-directory/develop/application-consent-experience.md)을 통해 Azure AD 테넌트 관리자의 동의를 요청할 수 있습니다.
   
* [논리 앱 만드는 방법](../logic-apps/quickstart-create-first-logic-app-workflow.md)에 관한 기본 지식

* Microsoft Graph Security 엔터티(예: 경고)에 액세스하려는 논리 앱. 현재 이 커넥터에는 트리거가 없습니다. 따라서 Microsoft Graph Security 작업을 사용하려면 **되풀이** 트리거 등의 트리거를 통해 논리 앱을 시작합니다.

## <a name="connect-to-microsoft-graph-security"></a>Microsoft Graph Security에 연결 

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. [Azure Portal](https://portal.azure.com/)에 로그인하고, 아직 열리지 않은 경우 Logic App Designer에서 논리 앱을 엽니다.

1. 빈 논리 앱의 경우 Microsoft Graph Security 작업을 추가하기 전에 원하는 트리거 및 기타 작업을 추가합니다.

   또는

   기존 논리 앱의 경우 Microsoft Graph Security 작업을 추가하려는 마지막 단계에서 **새 단계**를 선택합니다.

   또는

   단계 사이에서 작업을 추가하려면 단계 사이에 있는 화살표 위로 포인터를 이동합니다. 
   표시되는 더하기 기호(+)를 선택한 다음, **작업 추가**를 선택합니다.

1. 검색 상자에 “microsoft graph security”를 필터로 입력합니다. 작업 목록에서 원하는 작업을 선택합니다.

1. Microsoft Graph Security 자격 증명으로 로그인합니다.

1. 선택한 작업에 대해 필요한 세부 정보를 입력하고 논리 앱의 워크플로를 계속 빌드합니다.

## <a name="add-actions"></a>작업 추가

Microsoft Graph Security 커넥터에서 사용 가능한 다양한 작업의 사용 방법에 대한 세부 정보는 다음과 같습니다.

### <a name="manage-alerts"></a>경고 관리

가장 최근 결과를 필터링, 정렬 또는 가져오려면 [Microsoft Graph에서 지원하는 ODATA 쿼리 매개 변수](https://docs.microsoft.com/graph/query-parameters)‘만’ 제공합니다. 전체 기준 URL이나 HTTP 작업(예: `https://graph.microsoft.com/v1.0/security/alerts`, `GET` 또는 `PATCH` 작업)을 ‘지정하지 마세요’. 높은 심각도 경고 목록을 원하는 경우 **경고 가져오기** 작업의 매개 변수를 표시하는 특정 예제는 다음과 같습니다.

`Filter alerts value as Severity eq 'high'`

이 커넥터에서 사용할 수 있는 쿼리에 대한 자세한 내용은 [Microsoft Graph Security 경고 참조 문서](https://docs.microsoft.com/graph/api/alert-list)를 참조하세요. 이 커넥터를 사용하여 향상된 환경을 빌드하려면 커넥터가 지원하는 [스키마 속성 경고](https://docs.microsoft.com/graph/api/resources/alert)를 자세히 알아보세요.

| 조치 | 설명 |
|--------|-------------|
| **경고 가져오기** | 하나 이상의 [경고 속성](https://docs.microsoft.com/graph/api/resources/alert)을 기준으로 필터링된 경고를 가져옵니다. 예: <p>`Provider eq 'Azure Security Center' or 'Palo Alto Networks'` | 
| **ID로 경고 가져오기** | 경고 ID를 기준으로 특정 경고를 가져옵니다. | 
| **경고 업데이트** | 경고 ID를 기준으로 특정 경고를 업데이트합니다. <p>요청에 필수 속성과 편집 가능한 속성을 전달하려면 [경고의 편집 가능한 속성](https://docs.microsoft.com/graph/api/alert-update)을 참조하세요. 예를 들어 조사할 수 있도록 보안 분석가에게 경고를 할당하려면 경고의 **할당 대상** 속성을 업데이트할 수 있습니다. |
|||

### <a name="manage-alert-subscriptions"></a>경고 구독 관리

Microsoft Graph는 [‘구독’](https://docs.microsoft.com/graph/api/resources/subscription) 또는 [‘웹후크’](https://docs.microsoft.com/graph/api/resources/webhooks)를 지원합니다. 구독을 가져오거나, 업데이트 또는 삭제하려면 [Microsoft Graph에서 지원하는 ODATA 쿼리 매개 변수](https://docs.microsoft.com/graph/query-parameters)를 Microsoft Graph 엔터티 구문에 제공하고 `security/alerts` 및 ODATA 쿼리를 차례로 포함합니다. 
기준 URL(예: `https://graph.microsoft.com/v1.0`)을 ‘포함하지 마세요’. 대신, 다음 예제의 형식을 사용합니다.

`security/alerts?$filter=status eq 'New'`

| 조치 | 설명 |
|--------|-------------|
| **구독 만들기** | 변경 내용에 대해 알리는 [구독을 만듭니다](https://docs.microsoft.com/graph/api/subscription-post-subscriptions). 원하는 특정 경고 유형에 대해 이 구독을 필터링할 수 있습니다. 예를 들어 높은 심각도 경고에 대해 알리는 구독을 만들 수 있습니다. |
| **활성 구독 가져오기** | [만료되지 않은 구독을 가져옵니다](https://docs.microsoft.com/graph/api/subscription-list). | 
| **구독 업데이트** | 구독 ID를 제공하여 [구독을 업데이트](https://docs.microsoft.com/graph/api/subscription-update)합니다. 예를 들어 구독을 연장하려면 구독의 `expirationDateTime` 속성을 업데이트할 수 있습니다. | 
| **구독 삭제** | 구독 ID를 제공하여 [구독을 삭제](https://docs.microsoft.com/graph/api/subscription-delete)합니다. | 
||| 

## <a name="connector-reference"></a>커넥터 참조

커넥터의 OpenAPI(이전의 Swagger) 설명서에 설명된 트리거, 작업 및 제한에 대한 기술 정보는 커넥터의 [참조 페이지](https://aka.ms/graphsecurityconnectorreference)를 검토하세요.

## <a name="get-support"></a>지원 받기

질문이 있는 경우 [Azure Logic Apps 포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)을 방문해 보세요.
기능 아이디어를 제출하거나 투표하려면 [Logic Apps 사용자 의견 사이트](https://aka.ms/logicapps-wish)를 방문하세요.

## <a name="next-steps"></a>다음 단계

다른 [Logic Apps 커넥터](../connectors/apis-list.md)에 대해 알아봅니다.
