---
title: Azure를 통한 SaaS 판매 - API | Microsoft Docs
description: Marketplace API를 통해 SaaS 제안을 만드는 방법을 설명합니다.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: pbutlerm
ms.openlocfilehash: c4cf59e6aa7e6edc73db2e22b9fa8ce40301b07c
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/27/2018
ms.locfileid: "53790336"
---
<a name="saas-sell-through-azure---apis"></a>Azure 통한 SaaS 판매 - API
==============================

이 문서에서는 API를 사용하여 SaaS 제안을 만드는 방법을 설명합니다. 이 API는 Azure를 통한 판매를 선택한 경우 SaaS 제안 구독을 허용하는 데 필요합니다. 상거래가 설정되지 않은 일반적인 SaaS 목록을 작성하려는 경우 [SaaS 응용 프로그램 기술 게시 가이드](./cloud-partner-portal-saas-offers-tech-publishing-guide.md)를 참조하세요.

이 문서는 다음 두 개의 섹션으로 구분됩니다.

-   SaaS 서비스와 Azure Marketplace 간의 서비스 간 인증
-   API 메서드 및 엔드포인트

다음 API는 Azure와 SaaS 서비스를 통합하는 데 도움을 주기 위해 제공됩니다.

-   해결
-   구독
-   변환
-   구독 취소

다음 다이어그램은 새 고객에게 구독 흐름과 이러한 API가 사용되는 경우를 보여 줍니다.

![SaaS 제안 API 흐름](media/saas-offer-publish-with-subscription-apis/saas-offer-publish-api-flow.png)

<a name="service-to-service-authentication-between-saas-service-and-azure-marketplace"></a>SaaS 서비스와 Azure Marketplace 간의 서비스 간 인증
----------------------------------------------------------------------------

Azure는 해당 최종 사용자에게 SaaS 서비스가 노출하는 인증을 제한하지 않습니다. 그러나 SaaS 서비스가 Azure Marketplace API와 통신하게 되면 Azure AD(Azure Active Directory) 애플리케이션의 컨텍스트에서 인증이 수행됩니다.

다음 섹션에서는 Azure AD 애플리케이션을 만드는 방법을 설명합니다.

### <a name="register-an-azure-ad-application"></a>Azure AD 애플리케이션 등록

Azure AD의 기능을 사용하려는 모든 애플리케이션이 먼저 Azure AD 테넌트에 등록되어야 합니다. 이 등록 프로세스는 애플리케이션이 위치한 URL, 사용자가 인증된 후 회신을 보낼 URL, 앱을 식별하는 URI 등과 같이 애플리케이션에 대한 Azure AD 세부 정보의 제공이 포함됩니다.

Azure Portal을 사용하여 새 애플리케이션을 등록하려면 다음 단계를 수행합니다.

1.  [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2.  계정이 하나 이상의 액세스를 제공하는 경우 오른쪽 위 모서리에 있는 계정을 클릭하여 원하는 Azure AD 테넌트로 포털 세션을 설정합니다.
3.  왼쪽의 탐색 창에서 **Azure Active Directory** 서비스, **앱 등록** 및 **새 애플리케이션 등록**을 차례로 클릭합니다.

    ![SaaS AD 앱 등록](media/saas-offer-publish-with-subscription-apis/saas-offer-app-registration.png)

4.  만들기 페이지에서 애플리케이션의 등록\' 정보를 입력합니다.
    -   **이름**: 의미 있는 애플리케이션 이름을 입력합니다.
    -   **애플리케이션 형식**: 
        - 장치에 로컬로 설치된 [클라이언트 애플리케이션](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#client-application)의 경우 **네이티브**를 선택합니다. 이 설정은 OAuth 공개 [네이티브 클라이언트](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#native-client)에 사용됩니다.
        - 보안 서버에 설치된 [클라이언트 애플리케이션](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#client-application) 및 [리소스/API 애플리케이션](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#resource-server)의 경우 **웹앱/API**를 선택합니다. 이 설정은 OAuth 기밀 [웹 클라이언트](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#web-client) 및 공용 [사용자 에이전트 기반 클라이언트](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#user-agent-based-client)에 사용됩니다.
        동일한 애플리케이션이 클라이언트와 리소스/API를 모두 노출할 수도 있습니다.
    -   **로그온 URL**: 웹앱/API 애플리케이션에서 앱의 기준 URL을 제공합니다.웹 애플리케이션의 경우 앱의 기준 URL을 제공합니다. 예를 들어 **http://localhost:31544**은 로컬 컴퓨터에서 실행 중인 웹앱의 URL일 수 있습니다. 사용자는 이 URL을 사용하여 웹 클라이언트 애플리케이션에 로그인합니다.
    -   **리디렉션 URI**: 네이티브 애플리케이션의 경우 Azure AD에서 토큰 응답을 반환하는 데 사용하는 URI를 제공합니다. 애플리케이션에 고유하게 해당되는 값을 입력합니다(예: **http://MyFirstAADApp**).

        ![SaaS AD 앱 등록](media/saas-offer-publish-with-subscription-apis/saas-offer-app-registration-2.png) 웹 애플리케이션 또는 네이티브 애플리케이션에 대한 구체적인 예제를 보려면 [Azure AD 개발자 가이드](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide#get-started)의 시작하기 섹션에서 사용할 수 있는 빠른 시작 안내 설정을 확인하세요.

5.  작업을 마쳤으면 **만들기**를 클릭합니다. Azure AD가 애플리케이션에 고유한 애플리케이션 ID를 할당하면 애플리케이션의 기본 등록 페이지로 이동합니다. 애플리케이션이 웹 또는 네이티브 애플리케이션인지에 따라 애플리케이션에 기능을 추가하기 위해 다른 옵션이 제공됩니다.

    **참고:** 기본적으로 새로 등록된 애플리케이션은 동일한 테넌트의 사용자만이 애플리케이션에 로그인할 수 있도록 구성됩니다.

<a name="api-methods-and-endpoints"></a>API 메서드 및 엔드포인트
-------------------------

다음 섹션에서는 SaaS 제안 구독을 사용하도록 설정하는 데 사용할 수 있는 API 메서드 및 엔드포인트에 대해 설명합니다.

### <a name="get-a-token-based-on-the-azure-ad-app"></a>Azure AD 앱에 따라 토큰 가져오기

HTTP 메서드

`GET`

*요청 URL*

**https://login.microsoftonline.com/*{tenantId}*/oauth2/token**

*URI 매개 변수*

|  **매개 변수 이름**  | **필수**  | **설명**                               |
|  ------------------  | ------------- | --------------------------------------------- |
| tenantId             | True          | 등록된 AAD 애플리케이션의 테넌트 ID입니다.   |
|  |  |  |


*요청 헤더*

|  **헤더 이름**  | **필수** |  **설명**                                   |
|  --------------   | ------------ |  ------------------------------------------------- |
|  콘텐츠 형식     | True         | 요청과 연결된 콘텐츠 형식입니다. 기본값은 `application/x-www-form-urlencoded`입니다.  |
|  |  |  |


*요청 본문*

| **속성 이름**   | **필수** |  **설명**                                                          |
| -----------------   | -----------  | ------------------------------------------------------------------------- |
|  Grant_type         | True         | 권한 부여 유형입니다. 기본값은 `client_credentials`입니다.                    |
|  Client_id          | True         |  Azure AD 앱과 연결된 클라이언트/앱 식별자입니다.                  |
|  client_secret      | True         |  Azure AD 앱과 연결된 암호입니다.                               |
|  리소스           | True         |  토큰이 요청된 대상 리소스입니다. 기본값은 `62d94f6c-d599-489b-a797-3e10e42fbe22`입니다. |
|  |  |  |


*응답*

|  **Name**  | **형식**       |  **설명**    |
| ---------- | -------------  | ------------------- |
| 200 정상    | TokenResponse  | 요청 성공   |
|  |  |  |

*TokenResponse*

샘플 응답 토큰:

``` json
  {
      "token_type": "Bearer",
      "expires_in": "3600",
      "ext_expires_in": "0",
      "expires_on": "15251…",
      "not_before": "15251…",
      "resource": "62d94f6c-d599-489b-a797-3e10e42fbe22",
      "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayIsImtpZCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayJ9…"
  }               
```

### <a name="marketplace-api-endpoint-and-api-version"></a>Marketplace API 엔드포인트 및 API 버전

Azure Marketplace API의 끝점은 `https://marketplaceapi.microsoft.com`입니다.

현재 API 버전은 `api-version=2017-04-15`입니다.


### <a name="resolve-subscription"></a>구독 확인

엔드포인트 확인에 대한 POST 작업을 사용하여 토큰을 영구 리소스 ID로 확인할 수 있습니다.

*요청*

**POST**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=2017-04-15**

|  **매개 변수 이름** |     **설명**                                      |
|  ------------------ |     ---------------------------------------------------- |
|  api-version        |  이 요청에 사용할 작업의 버전입니다.   |
|  |  |


*헤더*

| **헤더 키**     | **필수** | **설명**                                                                                                                                                                                                                  |
|--------------------|--------------|-----------------------------------------------------------|
| x-ms-requestid     | 아니요           | 클라이언트의 요청을 추적하기 위한 고유한 문자열 값(기본적으로 GUID)입니다. 이 값을 제공하지 않으면 값이 하나 생성된 후 응답 헤더에 제공됩니다.  |
| x-ms-correlationid | 아니요           | 클라이언트의 작업에 대한 고유한 문자열 값입니다. 이 값은 클라이언트 작업의 모든 이벤트를 서버 쪽의 이벤트와 상호 연결합니다. 이 값을 제공하지 않으면 값이 하나 생성된 후 응답 헤더에 제공됩니다. |
| Content-type       | 예          | `application/json`                                        |
| authorization      | 예          | JWT(JSON Web Token) 전달자 토큰입니다.                    |
| x-ms-marketplace-token| 예| 사용자가 Azure의 SaaS ISV 웹 사이트로 리디렉션될 때 URL에 포함된 토큰 쿼리 매개 변수입니다. **참고:** 이 토큰은 1시간 동안만 유효합니다. 또한 URL은 토큰 값을 사용하기 전에 브라우저에서 디코드합니다.|
|  |  |  |
  

*응답 본문*

 ``` json       
    { 
        “id”: “”, 
        “subscriptionName”: “”,
        “offerId”:””, 
         “planId”:””
    }     
```

| **매개 변수 이름** | **데이터 형식** | **설명**                       |
|--------------------|---------------|---------------------------------------|
| id                 | 문자열        | SaaS 구독의 ID입니다.          |
| subscriptionName| 문자열| SaaS 서비스를 구독하는 동안 Azure의 사용자가 설정한 SaaS 구독의 이름입니다.|
| OfferId            | 문자열        | 사용자가 구독한 제안 ID입니다. |
| planId             | 문자열        | 사용자가 구독한 요금제 ID입니다.  |
|  |  |  |


*응답 코드*

| **HTTP 상태 코드** | **오류 코드**     | **설명**                                                                         |
|----------------------|--------------------| --------------------------------------------------------------------------------------- |
| 200                  | `OK`                 | 토큰이 확인되었습니다.                                                            |
| 400                  | `BadRequest`         | 필요한 헤더가 없거나 잘못된 api-version을 지정했습니다. 토큰이 잘못된 형식을 갖거나 만료되었으므로 토큰을 확인하지 못했습니다(토큰은 생성된 후 1시간 동안만 유효함). |
| 403                  | `Forbidden`          | 호출자는 이 작업을 수행할 권한이 없습니다.                                 |
| 429                  | `RequestThrottleId`  | 서비스가 요청을 처리 중입니다. 나중에 다시 시도하세요.                                |
| 503                  | `ServiceUnavailable` | 서비스가 일시적으로 가동 중단되었습니다. 나중에 다시 시도하세요.                                        |
|  |  |  |


*응답 헤더*

| **헤더 키**     | **필수** | **설명**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | 예          | 클라이언트에서 받은 요청 ID입니다.                                                                   |
| x-ms-correlationid | 예          | 클라이언트가 전달한 경우 상관관계 ID이고, 그렇지 않은 경우 서버 상관관계 ID입니다.                   |
| x-ms-activityid    | 예          | 서비스의 요청을 추적하기 위한 고유한 문자열 값입니다. 모든 조정에 사용됩니다. |
| Retry-After        | 아니요           | 이 값은 429 응답에 대해서만 설정됩니다.                                                                   |
|  |  |  |


### <a name="subscribe"></a>구독

구독 엔드포인트를 사용하면 지정된 요금제로 SaaS 서비스 구독을 시작하고, 상거래 시스템에서 청구를 사용하도록 설정할 수 있습니다.

**PUT**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{subscriptionId}*?api-version=2017-04-15**

| **매개 변수 이름**  | **설명**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | 확인 API를 통해 토큰을 확인한 후 획득한 SaaS 구독의 고유 ID입니다.                              |
| api-version         | 이 요청에 사용할 작업의 버전입니다. |
|  |  |

*헤더*

|  **헤더 키**        | **필수** |  **설명**                                                  |
| ------------------     | ------------ | --------------------------------------------------------------------------------------- |
| x-ms-requestid         |   아니요         | 클라이언트의 요청을 추적하기 위한 고유한 문자열 값(기본적으로 GUID)입니다. 이 값을 제공하지 않으면 값이 하나 생성된 후 응답 헤더에 제공됩니다. |
| x-ms-correlationid     |   아니요         | 클라이언트의 작업에 대한 고유한 문자열 값입니다. 클라이언트 작업의 모든 이벤트를 서버 쪽의 이벤트와 상호 연결하기 위한 값입니다. 이 값을 제공하지 않으면 값이 하나 생성된 후 응답 헤더에 제공됩니다. |
| If-Match/If-None-Match |   아니요         |   강력한 유효성 검사기 ETag 값입니다.                                                          |
| content-type           |   예        |    `application/json`                                                                   |
|  authorization         |   예        |    JWT(JSON Web Token) 전달자 토큰입니다.                                               |
| x-ms-marketplace-session-mode| 아니요 | SaaS 제안을 구독하는 동안 시험 실행 모드를 사용하도록 설정하기 위한 플래그입니다. 설정되면 구독 요금이 청구되지 않습니다. ISV 테스트 시나리오에 유용합니다. 이 항목을 **'dryrun'** 으로 설정하세요.|
|  |  |  |

*본문*

``` json
  { 
      “planId”:””
   }      
```

| **요소 이름** | **데이터 형식** | **설명**                      |
|------------------|---------------|--------------------------------------|
| planId           | (필수) 문자열        | 사용자가 구독하는 SaaS 서비스의 요금제 ID입니다.  |
|  |  |  |

*응답 코드*

| **HTTP 상태 코드** | **오류 코드**     | **설명**                                                           |
|----------------------|--------------------|---------------------------------------------------------------------------|
| 202                  | `Accepted`           | 지정된 요금제에 대해 수신된 SaaS 구독 활성화입니다.                   |
| 400                  | `BadRequest`         | 필수 헤더가 누락되었거나 JSON 본문의 형식이 잘못되었습니다. |
| 403                  | `Forbidden`          | 호출자는 이 작업을 수행할 권한이 없습니다.                   |
| 404                  | `NotFound`           | 지정된 ID를 갖는 구독이 없습니다.                                  |
| 409                  | `Conflict`           | 구독에 대해 다른 작업이 진행 중입니다.                     |
| 429                  | `RequestThrottleId`  | 서비스가 요청을 처리 중입니다. 나중에 다시 시도하세요.                  |
| 503                  | `ServiceUnavailable` | 서비스가 일시적으로 가동 중단되었습니다. 나중에 다시 시도하세요.                          |
|  |  |  |

202 응답의 경우 ‘Operation-location’ 헤더에서 요청 작업에 따라 후속 작업을 수행하세요. 인증은 다른 Marketplace API와 동일합니다.

*응답 헤더*

| **헤더 키**     | **필수** | **설명**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | 예          | 클라이언트에서 받은 요청 ID입니다.                                                                   |
| x-ms-correlationid | 예          | 클라이언트가 전달한 경우 상관관계 ID이고, 그렇지 않은 경우 서버 상관관계 ID입니다.                   |
| x-ms-activityid    | 예          | 서비스의 요청을 추적하기 위한 고유한 문자열 값입니다. 이 값은 모든 조정에 사용됩니다. |
| Retry-After        | 예          | 클라이언트가 상태를 확인할 수 있는 간격입니다.                                                       |
| Operation-Location | 예          | 작업 상태를 가져오기 위해 리소스에 대한 링크입니다.                                                        |
|  |  |  |

### <a name="change-plan-endpoint"></a>요금제 엔드포인트 변경

엔드포인트 변경을 통해 현재 구독한 요금제를 새 요금제로 변환할 수 있습니다.

**PATCH**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{subscriptionId}*?api-version=2017-04-15**

| **매개 변수 이름**  | **설명**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | SaaS 구독의 ID입니다.                              |
| api-version         | 이 요청에 사용할 작업의 버전입니다. |
|  |  |

*헤더*

| **헤더 키**          | **필수** | **설명**                                                                                                                                                                                                                  |
|-------------------------|--------------|---------------------------------------------------------------------------------------------------------------------|
| x-ms-requestid          | 아니요           | 클라이언트의 요청을 추적하기 위한 고유한 문자열 값입니다. GUID를 사용하는 것이 좋습니다. 이 값을 제공하지 않으면 값이 하나 생성된 후 응답 헤더에 제공됩니다.   |
| x-ms-correlationid      | 아니요           | 클라이언트의 작업에 대한 고유한 문자열 값입니다. 클라이언트 작업의 모든 이벤트를 서버 쪽의 이벤트와 상호 연결하기 위한 값입니다. 이 값을 제공하지 않으면 값이 하나 생성된 후 응답 헤더에 제공됩니다. |
| If-Match /If-None-Match | 아니요           | 강력한 유효성 검사기 ETag 값입니다.                              |
| content-type            | 예          | `application/json`                                        |
| authorization           | 예          | JWT(JSON Web Token) 전달자 토큰입니다.                    |
|  |  |  |


*본문*

``` json
                { 
                    “planId”:””
                } 
```


|  **요소 이름** |  **데이터 형식**  | **설명**                              |
|  ---------------- | -------------   | --------------------------------------       |
|  planId           |  (필수) 문자열         | 사용자가 구독하는 SaaS 서비스의 요금제 ID입니다.          |
|  |  |  |

*응답 코드*

| **HTTP 상태 코드** | **오류 코드**     | **설명**                                                           |
|----------------------|--------------------|---------------------------------------------------------------------------|
| 202                  | `Accepted`           | 지정된 요금제에 대해 수신된 SaaS 구독 활성화입니다.                   |
| 400                  | `BadRequest`         | 필수 헤더가 누락되었거나 JSON 본문의 형식이 잘못되었습니다. |
| 403                  | `Forbidden`          | 호출자는 이 작업을 수행할 권한이 없습니다.                   |
| 404                  | `NotFound`           | 지정된 ID를 갖는 구독이 없습니다.                                  |
| 409                  | `Conflict`           | 구독에 대해 다른 작업이 진행 중입니다.                     |
| 429                  | `RequestThrottleId`  | 서비스가 요청을 처리 중입니다. 나중에 다시 시도하세요.                  |
| 503                  | `ServiceUnavailable` | 서비스가 일시적으로 가동 중단되었습니다. 나중에 다시 시도하세요.                          |
|  |  |  |

*응답 헤더*

| **헤더 키**     | **필수** | **설명**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | 예          | 클라이언트에서 받은 요청 ID입니다.                                                                   |
| x-ms-correlationid | 예          | 클라이언트가 전달한 경우 상관관계 ID이고, 그렇지 않은 경우 서버 상관관계 ID입니다.                   |
| x-ms-activityid    | 예          | 서비스의 요청을 추적하기 위한 고유한 문자열 값입니다. 이 값은 모든 조정에 사용됩니다. |
| Retry-After        | 예          | 클라이언트가 상태를 확인할 수 있는 간격입니다.                                                       |
| Operation-Location | 예          | 작업 상태를 가져오기 위해 리소스에 대한 링크입니다.                                                        |
|  |  |  |

### <a name="delete-subscription"></a>구독 삭제

구독 엔드포인트에 대한 삭제 작업을 사용하여 지정된 ID를 갖는 구독을 삭제할 수 있습니다.

*요청*

**DELETE**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{subscriptionId}*?api-version=2017-04-15**

| **매개 변수 이름**  | **설명**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | SaaS 구독의 ID입니다.                              |
| api-version         | 이 요청에 사용할 작업의 버전입니다. |
|  |  |

*헤더*

| **헤더 키**     | **필수** | **설명**                                                                                                                                                                                                                  |
|--------------------|--------------| ----------------------------------------------------------|
| x-ms-requestid     | 아니요           | 클라이언트의 요청을 추적하기 위한 고유한 문자열 값입니다. GUID를 사용하는 것이 좋습니다. 이 값을 제공하지 않으면 값이 하나 생성된 후 응답 헤더에 제공됩니다.                                                           |
| x-ms-correlationid | 아니요           | 클라이언트의 작업에 대한 고유한 문자열 값입니다. 클라이언트 작업의 모든 이벤트를 서버 쪽의 이벤트와 상호 연결하기 위한 값입니다. 이 값을 제공하지 않으면 값이 하나 생성된 후 응답 헤더에 제공됩니다. |
| 권한 부여      | 예          | JWT(JSON Web Token) 전달자 토큰입니다.                    |
|  |  |  |
 

*응답 코드*

| **HTTP 상태 코드** | **오류 코드**     | **설명**                                                           |
|----------------------|--------------------|---------------------------------------------------------------------------|
| 202                  | `Accepted`           | 지정된 요금제에 대해 수신된 SaaS 구독 활성화입니다.                   |
| 400                  | `BadRequest`         | 필수 헤더가 누락되었거나 JSON 본문의 형식이 잘못되었습니다. |
| 403                  | `Forbidden`          | 호출자는 이 작업을 수행할 권한이 없습니다.                   |
| 404                  | `NotFound`           | 지정된 ID를 갖는 구독이 없습니다.                                  |
| 429                  | `RequestThrottleId`  | 서비스가 요청을 처리 중입니다. 나중에 다시 시도하세요.                  |
| 503                  | `ServiceUnavailable` | 서비스가 일시적으로 가동 중단되었습니다. 나중에 다시 시도하십시오.                          |
|  |  |  |

202 응답의 경우 ‘Operation-location’ 헤더에서 요청 작업에 따라 후속 작업을 수행하세요. 인증은 다른 Marketplace API와 동일합니다.

*응답 헤더*

| **헤더 키**     | **필수** | **설명**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | 예          | 클라이언트에서 받은 요청 ID입니다.                                                                   |
| x-ms-correlationid | 예          | 클라이언트가 전달한 경우 상관관계 ID이고, 그렇지 않은 경우 서버 상관관계 ID입니다.                   |
| x-ms-activityid    | 예          | 서비스의 요청을 추적하기 위한 고유한 문자열 값입니다. 모든 조정에 사용됩니다. |
| Retry-After        | 예          | 클라이언트가 상태를 확인할 수 있는 간격입니다.                                                       |
| Operation-Location | 예          | 작업 상태를 가져오기 위해 리소스에 대한 링크입니다.                                                        |
|   |  |  |

### <a name="get-operation-status"></a>작업 상태 가져오기

이 엔드포인트를 사용하면 트리거된 비동기 작업(구독/구독 취소/요금제 변경)의 상태를 추적할 수 있습니다.

*요청*

**GET**

**https://marketplaceapi.microsoft.com/api/saas/operations/*{operationId}*?api-version=2017-04-15**

| **매개 변수 이름**  | **설명**                                       |
|---------------------|-------------------------------------------------------|
| operationId         | 트리거된 작업의 고유 ID입니다.                |
| api-version         | 이 요청에 사용할 작업의 버전입니다. |
|  |  |


*헤더*

| **헤더 키**     | **필수** | **설명**                                                                                                                                                                                                                  |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | 아니요           | 클라이언트의 요청을 추적하기 위한 고유한 문자열 값입니다. GUID를 사용하는 것이 좋습니다. 이 값을 제공하지 않으면 값이 하나 생성된 후 응답 헤더에 제공됩니다.   |
| x-ms-correlationid | 아니요           | 클라이언트의 작업에 대한 고유한 문자열 값입니다. 클라이언트 작업의 모든 이벤트를 서버 쪽의 이벤트와 상호 연결하기 위한 값입니다. 이 값을 제공하지 않으면 값이 하나 생성된 후 응답 헤더에 제공됩니다.  |
| 권한 부여      | 예          | JWT(JSON Web Token) 전달자 토큰입니다.                    |
|  |  |  | 
  

*응답 본문*

``` json
  { 
      “id”: “”, 
      “status”:””, 
       “resourceLocation”:””, 
      “created”:””, 
      “lastModified”:”” 
  } 
```

| **매개 변수 이름** | **데이터 형식** | **설명**                                                                                                                                               |
|--------------------|---------------|-------------------------------------------------------------------------------------------|
| id                 | 문자열        | 작업의 ID입니다.                                                                      |
| status             | 열거형          | 작업 상태로, `In Progress`, `Succeeded` 또는 `Failed` 중 하나입니다.          |
| resourceLocation   | 문자열        | 만들었거나 수정한 구독에 대한 링크입니다. 이 링크는 클라이언트가 업데이트된 상태 게시 작업을 가져오는 데 도움이 됩니다. `Unsubscribe` 작업에 대해서는 이 값이 설정되지 않습니다. |
| created            | Datetime      | 작업 생성 시간(UTC)입니다.                                                           |
| lastModified       | Datetime      | 마지막으로 작업을 업데이트한 시간(UTC)입니다.                                                      |
|  |  |  |

*응답 코드*

| **HTTP 상태 코드** | **오류 코드**     | **설명**                                                              |
|----------------------|--------------------|------------------------------------------------------------------------------|
| 200                  | `OK`                 | 가져오기 요청을 확인했으며 본문에 응답이 포함됩니다.    |
| 400                  | `BadRequest`         | 필요한 헤더가 없거나 잘못된 api-version을 지정했습니다. |
| 403                  | `Forbidden`          | 호출자는 이 작업을 수행할 권한이 없습니다.                      |
| 404                  | `NotFound`           | 지정된 ID를 갖는 구독이 없습니다.                                     |
| 429                  | `RequestThrottleId`  | 서비스가 요청을 처리 중입니다. 나중에 다시 시도하세요.                     |
| 503                  | `ServiceUnavailable` | 서비스가 일시적으로 가동 중단되었습니다. 나중에 다시 시도하세요.                             |
|  |  |  |

*응답 헤더*

| **헤더 키**     | **필수** | **설명**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | 예          | 클라이언트에서 받은 요청 ID입니다.                                                                   |
| x-ms-correlationid | 예          | 클라이언트가 전달한 경우 상관관계 ID이고, 그렇지 않은 경우 서버 상관관계 ID입니다.                   |
| x-ms-activityid    | 예          | 서비스의 요청을 추적하기 위한 고유한 문자열 값입니다. 모든 조정에 사용됩니다. |
| Retry-After        | 예          | 클라이언트가 상태를 확인할 수 있는 간격입니다.                                                       |
|  |  |  |

### <a name="get-subscription"></a>구독 가져오기

구독 엔드포인트에 대한 가져오기 작업을 통해 지정된 리소스 식별자를 갖는 구독을 검색할 수 있습니다.

*요청*

**GET**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions/*{subscriptionId}*?api-version=2017-04-15**

| **매개 변수 이름**  | **설명**                                       |
|---------------------|-------------------------------------------------------|
| subscriptionId      | SaaS 구독의 ID입니다.                              |
| api-version         | 이 요청에 사용할 작업의 버전입니다. |
|  |  |

*헤더*

| **헤더 키**     | **필수** | **설명**                                                                                           |
|--------------------|--------------|-----------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | 아니요           | 클라이언트의 요청을 추적하기 위한 고유한 문자열 값(기본적으로 GUID)입니다. 이 값을 제공하지 않으면 값이 하나 생성된 후 응답 헤더에 제공됩니다.                                                           |
| x-ms-correlationid | 아니요           | 클라이언트의 작업에 대한 고유한 문자열 값입니다. 클라이언트 작업의 모든 이벤트를 서버 쪽의 이벤트와 상호 연결하기 위한 값입니다. 이 값을 제공하지 않으면 값이 하나 생성된 후 응답 헤더에 제공됩니다. |
| 권한 부여      | 예          | JWT(JSON Web Token) 전달자 토큰입니다.                                                                    |
|  |  |  |

*응답 본문*

``` json
  { 
      “id”: “”, 
      “saasSubscriptionName”:””, 
      “offerId”:””, 
       “planId”:””, 
      “saasSubscriptionStatus”:””, 
      “created”:””, 
      “lastModified”: “” 
  }
```
| **매개 변수 이름**     | **데이터 형식** | **설명**                               |
|------------------------|---------------|-----------------------------------------------|
| id                     | 문자열        | Azure에서 SaaS 구독 리소스의 ID입니다.    |
| offerId                | 문자열        | 사용자가 구독한 제안 ID입니다.         |
| planId                 | 문자열        | 사용자가 구독한 요금제 ID입니다.          |
| saasSubscriptionName   | 문자열        | SaaS 구독의 이름입니다.                |
| saasSubscriptionStatus | 열거형          | 작업 상태입니다.  다음 중 하나  <br/> - `Subscribed`: 구독이 활성화되어 있습니다.  <br/> - `Pending`: 사용자가 리소스를 만들지만, ISV에서 활성화하지 않았습니다.   <br/> - `Unsubscribed`: 사용자가 구독을 취소했습니다.   <br/> - `Suspended`: 사용자가 구독을 일시 중단했습니다.   <br/> - `Deactivated`:  Azure 구독이 일시 중단되었습니다.  |
| created                | Datetime      | 구독 생성 타임스탬프 값(UTC)입니다. |
| lastModified           | Datetime      | 구독 수정 타임스탬프 값(UTC)입니다. |
|  |  |  |

*응답 코드*

| **HTTP 상태 코드** | **오류 코드**     | **설명**                                                              |
|----------------------|--------------------|------------------------------------------------------------------------------|
| 200                  | `OK`                 | 가져오기 요청을 확인했으며 본문에 응답이 포함됩니다.    |
| 400                  | `BadRequest`         | 필요한 헤더가 없거나 잘못된 api-version을 지정했습니다. |
| 403                  | `Forbidden`          | 호출자는 이 작업을 수행할 권한이 없습니다.                      |
| 404                  | `NotFound`           | 지정된 ID를 갖는 구독이 없습니다.                                     |
| 429                  | `RequestThrottleId`  | 서비스가 요청을 처리 중입니다. 나중에 다시 시도하세요.                     |
| 503                  | `ServiceUnavailable` | 서비스가 일시적으로 가동 중단되었습니다. 나중에 다시 시도하세요.                             |
|  |  |  |

*응답 헤더*

| **헤더 키**     | **필수** | **설명**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | 예          | 클라이언트에서 받은 요청 ID입니다.                                                                   |
| x-ms-correlationid | 예          | 클라이언트가 전달한 경우 상관관계 ID이고, 그렇지 않은 경우 서버 상관관계 ID입니다.                   |
| x-ms-activityid    | 예          | 서비스의 요청을 추적하기 위한 고유한 문자열 값입니다. 모든 조정에 사용됩니다. |
| Retry-After        | 아니요           | 클라이언트가 상태를 확인할 수 있는 간격입니다.                                                       |
| eTag               | 예          | 작업 상태를 가져오기 위해 리소스에 대한 링크입니다.                                                        |
|  |  |  |


### <a name="get-subscriptions"></a>구독 가져오기

구독 엔드포인트의 가져오기 작업을 사용하면 ISV의 모든 제안에 대한 모든 구독을 검색할 수 있습니다.

*요청*

**GET**

**https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=2017-04-15**

| **매개 변수 이름**  | **설명**                                       |
|---------------------|-------------------------------------------------------|
| api-version         | 이 요청에 사용할 작업의 버전입니다. |
|  |  |

*헤더*

| **헤더 키**     | **필수** | **설명**                                           |
|--------------------|--------------|-----------------------------------------------------------|
| x-ms-requestid     | 아니요           | 클라이언트의 요청을 추적하기 위한 고유한 문자열 값입니다. GUID를 사용하는 것이 좋습니다. 이 값을 제공하지 않으면 값이 하나 생성된 후 응답 헤더에 제공됩니다.             |
| x-ms-correlationid | 아니요           | 클라이언트의 작업에 대한 고유한 문자열 값입니다. 클라이언트 작업의 모든 이벤트를 서버 쪽의 이벤트와 상호 연결하기 위한 값입니다. 이 값을 제공하지 않으면 값이 하나 생성된 후 응답 헤더에 제공됩니다. |
| 권한 부여      | 예          | JWT(JSON Web Token) 전달자 토큰입니다.                    |
|  |  |  |


*응답 본문*

``` json
  { 
      “id”: “”, 
      “saasSubscriptionName”:””, 
      “offerId”:””, 
       “planId”:””, 
      “saasSubscriptionStatus”:””, 
      “created”:””, 
      “lastModified”: “”
  }
```

| **매개 변수 이름**     | **데이터 형식** | **설명**                               |
|------------------------|---------------|-----------------------------------------------|
| id                     | 문자열        | Azure에서 SaaS 구독 리소스의 ID입니다.    |
| offerId                | 문자열        | 사용자가 구독한 제안 ID입니다.         |
| planId                 | 문자열        | 사용자가 구독한 요금제 ID입니다.          |
| saasSubscriptionName   | 문자열        | SaaS 구독의 이름입니다.                |
| saasSubscriptionStatus | 열거형          | 작업 상태입니다.  다음 중 하나  <br/> - `Subscribed`: 구독이 활성화되어 있습니다.  <br/> - `Pending`: 사용자가 리소스를 만들지만, ISV에서 활성화하지 않았습니다.   <br/> - `Unsubscribed`: 사용자가 구독을 취소했습니다.   <br/> - `Suspended`: 사용자가 구독을 일시 중단했습니다.   <br/> - `Deactivated`:  Azure 구독이 일시 중단되었습니다.  |
| created                | Datetime      | 구독 생성 타임스탬프 값(UTC)입니다. |
| lastModified           | Datetime      | 구독 수정 타임스탬프 값(UTC)입니다. |
|  |  |  |

*응답 코드*

| **HTTP 상태 코드** | **오류 코드**     | **설명**                                                              |
|----------------------|--------------------|------------------------------------------------------------------------------|
| 200                  | `OK`                 | 가져오기 요청을 확인했으며 본문에 응답이 포함됩니다.    |
| 400                  | `BadRequest`         | 필요한 헤더가 없거나 잘못된 api-version을 지정했습니다. |
| 403                  | `Forbidden`          | 호출자는 이 작업을 수행할 권한이 없습니다.                      |
| 404                  | `NotFound`           | 지정된 ID를 갖는 구독이 없습니다.                                     |
| 429                  | `RequestThrottleId`  | 서비스가 요청을 처리 중입니다. 나중에 다시 시도하세요.                     |
| 503                  | `ServiceUnavailable` | 서비스가 일시적으로 가동 중단되었습니다. 나중에 다시 시도하십시오.                             |
|  |  |  |

*응답 헤더*

| **헤더 키**     | **필수** | **설명**                                                                                        |
|--------------------|--------------|--------------------------------------------------------------------------------------------------------|
| x-ms-requestid     | 예          | 클라이언트에서 받은 요청 ID입니다.                                                                   |
| x-ms-correlationid | 예          | 클라이언트가 전달한 경우 상관관계 ID이고, 그렇지 않은 경우 서버 상관관계 ID입니다.                   |
| x-ms-activityid    | 예          | 서비스의 요청을 추적하기 위한 고유한 문자열 값입니다. 모든 조정에 사용됩니다. |
| Retry-After        | 아니요           | 클라이언트가 상태를 확인할 수 있는 간격입니다.                                                       |
|  |  |  |

### <a name="saas-webhook"></a>SaaS 웹후크

SaaS 웹후크는 SaaS 서비스에 변경 내용을 미리 알리는 데 사용됩니다. 이 POST API는 인증되지 않은 것으로 예상되며 Microsoft 서비스에서 호출됩니다. SaaS 서비스는 웹후크 알림에 대한 작업을 수행하기 전에 작업 API를 호출하여 유효성을 검사하고 권한을 부여해야 합니다. 


*본문*

``` json
  { 
    "id": "be750acb-00aa-4a02-86bc-476cbe66d7fa",
    "activityId": "be750acb-00aa-4a02-86bc-476cbe66d7fa",
    "subscriptionId":"cd9c6a3a-7576-49f2-b27e-1e5136e57f45",
    "offerId": "sampleSaaSOffer", // Provided with "Update" action
    "publisherId": "contoso", 
    "planId": "silver",     // Provided with "Update" action
    "action": "Activate", // Activate/Delete/Suspend/Reinstate/Update
    "timeStamp": "2018-12-01T00:00:00"
  }
```

| **매개 변수 이름**     | **데이터 형식** | **설명**                               |
|------------------------|---------------|-----------------------------------------------|
| id  | 문자열       | 트리거된 작업의 고유 ID입니다.                |
| activityId   | 문자열        | 서비스의 요청을 추적하기 위한 고유한 문자열 값입니다. 모든 조정에 사용됩니다.               |
| subscriptionId                     | 문자열        | Azure에서 SaaS 구독 리소스의 ID입니다.    |
| offerId                | 문자열        | 사용자가 구독한 제안 ID입니다. “업데이트” 작업에만 제공됩니다.        |
| publisherId                | 문자열        | SaaS 제안의 게시자 ID입니다.         |
| planId                 | 문자열        | 사용자가 구독한 요금제 ID입니다. “업데이트” 작업에만 제공됩니다.          |
| action                 | 문자열        | 이 알림을 트리거하는 작업입니다. 가능한 값 - 활성화, 삭제, 일시 중단, 복구, 업데이트          |
| timeStamp                 | 문자열        | 이 알림이 트리거된 타임스탬프 값(UTC)입니다.          |
|  |  |  |
