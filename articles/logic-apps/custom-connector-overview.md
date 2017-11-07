---
title: "사용자 지정 커넥터 개요 - Azure Logic Apps | Microsoft Docs"
description: "통합 시나리오를 지원 및 확장하기 위해 사용자 지정 커넥터 만들기 개요"
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 0515b21bc7c7cc737e14fda016606bbea1aab476
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="custom-connectors-overview"></a>사용자 지정 커넥터 개요

코드를 작성하지 않고 [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/), [Microsoft Flow](https://flow.microsoft.com) 또는 [Microsoft PowerApps](https://powerapps.microsoft.com)를 사용하여 워크플로 또는 앱을 빌드할 수 있습니다. 이러한 서비스는 데이터 및 비즈니스 프로세스를 통합하는 데 도움을 주기 위해 Azure 및 SQL Server와 같은 Microsoft 서비스 및 제품뿐만 아니라 GitHub, Salesforce, Twitter 등의 기타 서비스를 위해 [100개 이상의 커넥터](../connectors/apis-list.md)를 제공합니다. 

그러나 경우에 따라 미리 빌드된 커넥터로 사용할 수 없는 API, 서비스 및 시스템을 호출할 수도 있습니다. 사용자의 비즈니스 및 생산성 요구에 보다 잘 맞게 조정된 시나리오를 지원하려는 경우 자체 트리거 및 작업을 갖는 *사용자 지정 커넥터*를 빌드할 수 있습니다.
사용자 지정 커넥터는 서비스 및 제품의 통합, 연결, 검색 기능 및 사용을 확장하여 고객 채택을 높이고 가속화하는 데 도움을 줄 수 있습니다.

예를 들어 이 다이어그램에는 웹 API, 해당 API에 대해 생성된 사용자 지정 Logic Apps 커넥터 및 사용자 지정 커넥터를 사용하여 해당 API로 작동되는 논리 앱 간의 상호 작용을 보여 줍니다.

![웹 API, 사용자 지정 커넥터 및 논리 앱의 개념적 개요](./media/custom-connector-overview/custom-connector-conceptual.png)

이 개요에서는 커넥터 생성, 보안 유지, 등록, 및 사용은 물론, 경우에 따라 공유 또는 인증을 위한 일반적인 개요 작업을 대략적으로 설명합니다.

![사용자 지정 커넥터 작성 단계](./media/custom-connector-overview/authoring-steps.png)

## <a name="prerequisites"></a>필수 조건

처음부터 끝까지 커넥터를 빌드하려면 다음 항목이 필요합니다.

* Azure 구독. 구독이 없는 경우 [무료 Azure 계정](https://azure.microsoft.com/free/)으로 시작할 수 있습니다. 그렇지 않으면 [종량제 구독](https://azure.microsoft.com/pricing/purchase-options/)에 등록합니다.

* 몇 가지 유형의 인증 액세스가 있는 RESTful API. 자세한 내용은 [웹 API에서 사용자 지정 커넥터 만들기](../logic-apps/custom-connector-build-web-api-app-tutorial.md)를 참조하세요. 커넥터의 트리거 및 작업에 사용할 수 있는 패턴에 대해서는 [논리 앱 워크플로에서 호출할 수 있는 사용자 지정 API 만들기](../logic-apps/logic-apps-create-api-app.md)를 참조하세요.

* 다음의 모든 항목:

  * [OpenAPI 2.0 파일](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/2.0.md)(이전의 Swagger)
  * OpenAPI 정의에 대한 URL
  * API에 대한 [Postman 컬렉션](../logic-apps/custom-connector-api-postman-collection.md) 

  이러한 항목 중 하나라도 없는 경우 제공되는 지침을 참조합니다.

* 선택 사항: 사용자 지정 커넥터에 대한 아이콘으로 사용할 이미지

## <a name="1-build-your-restful-api"></a>1. RESTful API 빌드

기술적으로 커넥터는 OpenAPI(이전의 Swagger)를 기준으로 하는 REST API에 대한 래퍼로, 기본 서비스가 Logic Apps, Flow 또는 PowerApps와 통신할 수 있도록 합니다. 먼저 사용자 지정 커넥터를 만들기 전에 완벽하게 작동하는 API가 필요합니다. 

API에 대해 사용하는 언어 및 플랫폼에는 제한이 없습니다. Microsoft 기술의 경우는 다음 플랫폼 중 하나가 권장됩니다.

* [Azure 기능](https://azure.microsoft.com/services/functions/)
* [Azure Web Apps](https://azure.microsoft.com/services/app-service/web/)
* [Azure API Apps](https://azure.microsoft.com/services/app-service/api/)

예를 들어 이 자습서에서는 [웹 API에서 사용자 지정 커넥터를 빌드하는 방법](../logic-apps/custom-connector-build-web-api-app-tutorial.md)을 보여줍니다. 커넥터의 트리거 및 작업에 사용할 수 있는 패턴에 대해서는 [논리 앱 워크플로에서 호출할 수 있는 사용자 지정 API 만들기](../logic-apps/logic-apps-create-api-app.md)를 참조하세요.

## <a name="2-secure-your-api"></a>2. API 보안

API 및 커넥터에 대해 다음 인증 표준을 사용할 수 있습니다.

   * [OAuth 2.0](https://oauth.net/2/)([Azure Active Directory](https://azure.microsoft.com/develop/identity/) 또는 SalesForce, GitHub, Dropbox 등의 특정 서비스 포함)
   * 제네릭 OAuth 2.0
   * [기본 인증](https://swagger.io/docs/specification/authentication/basic-authentication/)
   * [API 키](https://swagger.io/docs/specification/authentication/api-keys/)

코드를 통해 인증을 구현할 필요가 없도록 Azure Portal에서 API에 대해 Azure AD(Azure Active Directory) 인증을 설정할 수 있습니다. 또는 API 코드를 통해 인증을 요구하고 적용할 수 있습니다. 

자세한 내용은 해당 자습서를 따르세요.

* [Logic Apps: 사용자 지정 커넥터 보안](../logic-apps/custom-connector-azure-active-directory-authentication.md)
* [Flow: 사용자 지정 커넥터 보안](https://ms.flow.microsoft.com/documentation/customapi-azure-resource-manager-tutorial/)
* [PowerApps: 사용자 지정 커넥터 보안](https://powerapps.microsoft.com/tutorials/customapi-azure-resource-manager-tutorial/)

## <a name="3-describe-your-api"></a>3. API 설명 

API가 특정 유형의 인증된 액세스 권한을 가진다고 가정할 경우 Logic Apps, Flow 또는 PowerApps가 API와 통신할 수 있도록 API의 인터페이스 및 작업을 설명해야 합니다.
다음과 같은 업계 표준 정의 중 하나를 사용합니다.

* [OpenAPI 2.0 파일](https://swagger.io/) 기존 OpenAPI 파일로 빌드하여 시작할 수 있습니다.

  OpenAPI를 처음 접하는 경우 swagger.io 사이트의 [Swagger 시작](http://swagger.io/getting-started/)을 참조하세요.
  예제 OpenAPI 파일을 보려면 [Text Analytics API 설명서](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/export?DocumentFormat=Swagger&ApiName=Azure)를 참조하세요. 

* OpenAPI 파일을 자동으로 생성하는 Postman 컬렉션. OpenAPI 파일이 아직 없고 만들지 않으려는 경우에도 Postman 컬렉션을 사용하여 사용자 지정 커넥터를 쉽게 만들 수 있습니다.

  Postman을 처음 접하는 경우 해당 사이트에서 [Postman 앱을 설치](https://www.getpostman.com/apps)합니다. 자세한 내용은 [Postman을 사용하여 사용자 지정 커넥터 설명](../logic-apps/custom-connector-api-postman-collection.md)을 참조하세요.

> [!IMPORTANT]
> 파일 크기는 1MB 미만이어야 합니다.

내부적으로 Logic Apps, Flow 및 PowerApps는 궁극적으로 OpenAPI를 사용하고, Postman 컬렉션을 구문 분석하고, 해당 컬렉션을 OpenAPI 정의 파일로 변환합니다. OpenAPI 2.0 및 Postman 컬렉션이 다른 형식을 사용하지만 둘 다 API의 작업 및 매개 변수를 설명하는 언어 중립적이고 컴퓨터가 읽을 수 있는 문서입니다. API에서 사용하는 언어 및 플랫폼에 따라 다양한 도구에서 이러한 문서를 생성할 수 있습니다. 커넥터를 등록할 때 OpenAPI 파일을 빌드할 수도 있습니다.

예를 들어 다음을 비롯한 REST API 끝점에서 OpenAPI 파일 또는 Postman 컬렉션을 만들 수 있습니다.

* 공개적으로 사용할 수 있는 커넥터(예: [Spotify](https://developer.spotify.com/), [Slack](https://api.slack.com/), [Rackspace](http://docs.rackspace.com/) 등)

* 만든 후 클라우드 호스팅 공급자에 배포한 API(예: Azure, Heroku, Google Cloud 등)

* 네트워크에 배포되었으나 공용 인터넷에만 노출되는 사용자 지정 LOB(기간 업무) API

## <a name="4-register-your-connector"></a>4. 커넥터 등록

등록 프로세스는 Logic Apps, Flow 또는 PowerApps가 설명, 필수 인증 및 작업과 각 작업에 대한 매개 변수 및 출력을 비롯한 API의 특성을 이해하는 데 도움을 줍니다. 등록 프로세스를 시작할 때 OpenAPI 파일 또는 Postman 컬렉션을 제공할 수 있습니다. 그러면 등록 마법사의 메타데이터 필드가 자동으로 채워집니다. 언제든지 이러한 필드의 값을 편집할 수 있습니다.

![API 설명](./media/custom-connector-overview/choose-api-definition-file.png)

커넥터를 등록하려면 해당 자습서를 따릅니다.

* [Logic Apps: 커넥터 등록](../logic-apps/logic-apps-custom-connector-register.md)
* [Flow: 커넥터 등록](https://ms.flow.microsoft.com/documentation/register-custom-api/#register-your-custom-connector)
* [PowerApps: 커넥터 등록](https://powerapps.microsoft.com/tutorials/register-custom-api/#register-your-custom-connector)

## <a name="5-use-your-connector-in-a-logic-app-flow-or-app"></a>5. 논리 앱, 흐름 또는 앱에서 커넥터 사용 

Microsoft에서 관리하는 커넥터를 사용하는 것과 동일한 방식으로 커넥터를 사용할 수 있습니다. 예를 들어 논리 앱 워크플로에서 사용자 지정 커넥터를 추가하여 API에 대한 연결을 만들고, Microsoft에서 관리하는 커넥터에 대한 작업을 호출하는 것과 같은 방식으로 API가 제공하는 작업을 호출합니다.

## <a name="6-share-your-connector"></a>6. 커넥터 공유 

Logic Apps, Flow 또는 PowerApps에서 리소스를 공유하는 것과 같은 방식으로 조직의 사용자와 커넥터를 공유할 수 있습니다. 공유는 선택 사항이지만 다른 사용자와 커넥터를 공유하려는 시나리오가 있을 수 있습니다.

등록되었으나 인증되지 않은 사용자 지정 커넥터는 Microsoft에서 관리하는 커넥터처럼 작동하지만 커넥터의 작성자와 논리 앱이 배포된 지역에서 논리 앱에 대해 동일한 Azure Active Directory 테넌트 및 Azure 구독을 가지는 사용자*만* 보고 사용할 수 있습니다. 다음 단계에서는 이러한 경계 외부의 사용자(예: 모든 Logic Apps, Flow 및 PowerApps 사용자)와 커넥터를 공유하는 방법을 설명합니다.

> [!IMPORTANT]
> 커넥터를 공유하는 경우 다른 사용자가 해당 커넥터에 종속될 수 있습니다. 
> ***커넥터를 삭제하면 해당 커넥터에 대한 모든 연결이 삭제됩니다.***

* [Logic Apps: 커넥터 공유](../logic-apps/logic-apps-custom-connector-register.md)
* [Flow: 커넥터 공유](https://ms.flow.microsoft.com/documentation/register-custom-api/#share-your-custom-connector)
* [PowerApps: 커넥터 공유](https://powerapps.microsoft.com/tutorials/register-custom-api/#share-your-custom-connector)

## <a name="7-certify-your-connector"></a>7. 커넥터 인증

경우에 따라 Logic Apps, Flow 및 PowerApps에서 모든 사용자와 커넥터를 공유하려면 Microsoft 인증을 위해 커넥터를 제출합니다. 이 프로세스는 Microsoft에서 해당 커넥터를 게시할 수 있도록 하기 위해 먼저 커넥터를 검토하고, 기술 및 콘텐츠 규정 준수 여부를 검사하고, Logic Apps, Flow 및 PowerApps 기능이 유효한지 검사합니다. 자세한 내용은 [Microsoft 인증을 위해 커넥터를 제출하는 방법](../logic-apps/custom-connector-submit-certification.md)을 참조하세요.

## <a name="get-support"></a>지원 받기

* 등록 및 개발에 대해 지원을 받으려면 [condevhelp@microsoft.com](mailto:condevhelp@microsoft.com)으로 전자 메일을 보내세요. Microsoft는 이 계정에서 개발자 질문 및 문제점을 잘 모니터링한 후 해당 팀으로 전송합니다. 

* 자주 묻는 질문에 대해서는 [사용자 지정 커넥터 FAQ](../logic-apps/custom-connector-faq.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [웹 API에서 사용자 지정 커넥터 빌드](../logic-apps/custom-connector-build-web-api-app-tutorial.md)
* [사용자 지정 커넥터에 대한 인증 설정](../logic-apps/custom-connector-azure-active-directory-authentication.md)
* [ 컬렉션으로 사용자 지정 커넥터 설명](../logic-apps/custom-connector-api-postman-collection.md)
* [Microsoft 인증을 위해 사용자 지정 커넥터 제출](../logic-apps/custom-connector-submit-certification.md)
