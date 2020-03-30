---
title: Azure Active Directory B2C 및 Azure API 관리를 사용하여 OAuth 2.0으로 SPA 백엔드를 보호합니다.
description: Azure Active Directory B2C, Azure API 관리 및 Easy Auth를 사용하여 JavaScript SPA에서 호출하여 OAuth 2.0으로 API를 보호합니다.
services: api-management, azure-ad-b2c, app-service
documentationcenter: ''
author: WillEastbury
manager: alberts
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2020
ms.author: wieastbu
ms.custom: fasttrack-new
ms.openlocfilehash: 55acea360de11c5fcc699d65daf92cf24dfd691d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79475479"
---
# <a name="protect-spa-backend-with-oauth-20-azure-active-directory-b2c-and-azure-api-management"></a>OAuth 2.0, Azure Active Directory B2C 및 Azure API 관리로 SPA 백엔드 보호

이 시나리오에서는 API를 보호하도록 Azure API 관리 인스턴스를 구성하는 방법을 보여 줍니다.
Azure AD B2C와 함께 OAuth 2.0 프로토콜을 API 관리와 함께 사용하여 EasyAuth를 사용하여 Azure 함수 백엔드를 보호합니다.

## <a name="aims"></a>목표로
Azure Functions 및 Azure AD B2C를 사용하여 간소화된 시나리오에서 API 관리를 사용하는 방법을 살펴보겠습니다. Azure AD B2C를 사용하여 사용자에 서명하는 API를 호출하는 JavaScript(JS) 앱을 만듭니다. 그런 다음 API 관리의 유효성 검사 jwt 정책 기능을 사용하여 백 엔드 API를 보호합니다.

심층 방어를 위해 EasyAuth를 사용하여 백 엔드 API 내에서 토큰의 유효성을 다시 검사합니다.

## <a name="prerequisites"></a>사전 요구 사항
이 문서의 단계를 따르려면 다음이 있어야 합니다.
* 프런트 엔드 JS 단일 페이지 앱을 호스트하는 Azure(StorageV2) 범용 V2 저장소 계정
* Azure API 관리 인스턴스 
* 빈 Azure Function 앱(Windows 소비 계획에서 V2 .NET Core 런타임 실행) 호출된 API를 호스트합니다.
* 구독에 연결된 Azure AD B2C 테넌트 

실제로프로덕션 워크로드에서 동일한 리전의 리소스를 사용하지만 이 방법 문서에서는 배포 영역이 중요하지 않습니다.

## <a name="overview"></a>개요
다음은 사용 중 구성 요소와 이 프로세스가 완료되면 해당 구성 요소 간의 흐름에 대한 그림입니다.
![사용 및 흐름 구성 요소](../api-management/media/howto-protect-backend-frontend-azure-ad-b2c/image-arch.png "사용 및 흐름 구성 요소")

다음은 단계에 대한 간략한 개요입니다.

1. 범위가 있는 Azure AD B2C 호출(프런트 엔드, API 관리) 및 API 응용 프로그램 만들기 및 API 액세스 권한 부여
1. 사용자가 Azure AD B2C로 로그인할 수 있도록 등록 또는 로그인 정책을 만듭니다. 
1. 개발자 콘솔에서 OAuth2 사용자 권한 부여를 사용하도록 설정하는 새 Azure AD B2C 클라이언트 아이디 및 키로 API 관리 구성
1. 함수 API 빌드
1. 함수 API를 구성하여 새로운 Azure AD B2C 클라이언트 ID 및 키로 EasyAuth를 활성화하고 APIM VIP로 잠급전합니다. 
1. API 관리에서 API 정의 빌드
1. API 관리 API 구성에 대한 Oauth2 설정
1. **CORS** 정책을 설정하고 유효성 **검사 jwt** 정책을 추가하여 들어오는 모든 요청에 대해 OAuth 토큰의 유효성을 검사합니다.
1. 호출 응용 프로그램을 빌드하여 API를 사용합니다.
1. JS SPA 샘플 업로드
1. 새 Azure AD B2C 클라이언트 ID 및 키로 샘플 JS 클라이언트 앱 구성 
1. 클라이언트 응용 프로그램 테스트

## <a name="configure-azure-ad-b2c"></a>Azure AD B2C 구성 
포털에서 Azure AD B2C 블레이드를 열고 다음 단계를 수행합니다.
1. 응용 **프로그램** 탭 선택 
1. '추가' 버튼을 클릭하고 다음 용도로 세 개의 응용 프로그램을 만듭니다.
   * 프런트 엔드 클라이언트입니다.
   * 백 엔드 함수 API입니다.
   * [선택 사항] API 관리 개발자 포털(소비 계층에서 Azure API Management를 실행하지 않는 한 나중에 이 시나리오에서 자세히 실행).
1. 3개 응용 프로그램 모두에 대해 WebApp/Web API를 설정하고 프런트 엔드 클라이언트에 대해서만 '암시적 흐름 허용'을 예로 설정합니다.
1. 이제 앱 ID URI를 설정하고 생성되는 서비스와 관련이 있는 고유하고 관련이 있는 것을 선택합니다.
1. 지금은 다음과 같은 https://localhost회신 URL에 자리 표시자를 사용하여 나중에 해당 URL을 업데이트합니다.
1. '만들기'를 클릭한 다음 위의 세 앱 각각에 대해 2-5단계를 반복하여 AppID URI, 이름 및 응용 프로그램 ID를 기록하여 세 앱 모두에 나중에 사용할 수 있습니다.
1. 응용 프로그램 목록에서 API 관리 개발자 포털 응용 프로그램을 열고 *키* 탭(일반 아래)을 선택한 다음 '키 생성'을 클릭하여 인증 키를 생성합니다.
1. 저장을 클릭하면 나중에 사용할 수 있도록 안전한 곳에 키를 기록하십시오 .이 장소는이 키를보고 복사 할 수있는 유일한 기회입니다.
1. 이제 게시된 범위 탭(API 액세스 *중)을 선택합니다.*
1. 함수 API의 범위를 만들고 이름을 지정하고 범위 및 채워진 전체 범위 값을 기록한 다음 '저장'을 클릭합니다.
   > [!NOTE]
   > Azure AD B2C 범위는 다른 응용 프로그램이 응용 프로그램에서 API 액세스 블레이드를 통해 액세스를 요청할 수 있는 API 내에서 효과적으로 권한을 부여하며, 호출된 API에 대한 응용 프로그램 권한을 효과적으로 생성했습니다.
1. 다른 두 응용 프로그램을 연 다음 *API 액세스* 탭 아래를 봅니다.
1. 백 엔드 API 범위와 이미 있는 기본 범위에 대한 액세스 권한을 부여합니다("사용자 프로필 에 액세스").
1. '일반' 아래의 *키* 탭을 선택하여 각각 키를 생성하여 인증 키를 생성하고 나중에 안전한 곳에 해당 키를 기록합니다.

## <a name="create-a-sign-up-or-sign-in-user-flow"></a>'로그인 또는 로그인' 사용자 흐름 만들기
1. Azure AD B2C 블레이드의 루트(또는 '개요')로 돌아갑니다. 
1. 그런 다음 "사용자 흐름(정책)"을 선택하고 "새 사용자 흐름"을 클릭합니다.
1. '로그인 및 로그인' 사용자 흐름 유형 선택
1. 정책에 이름을 지정하고 나중에 기록합니다.
1. 그런 다음 'ID 공급자'에서 '사용자 ID 등록'('이메일 등록'이라고 표시)을 확인하고 확인을 클릭합니다. 
1. '사용자 속성 및 소유권 주장'에서 '더 보기'를 클릭합니다. 그런 다음 사용자가 토큰에 입력하고 반환할 클레임 옵션을 선택합니다. 최소한 '이름 표시'와 '이메일 주소'를 확인하여 수집 및 반환한 다음 '확인'을 클릭한 다음 '만들기'를 클릭합니다.
1. 목록에서 만든 정책을 선택한 다음 '사용자 흐름 실행' 단추를 클릭합니다.
1. 이 작업은 실행 된 사용자 흐름 블레이드를 열고 프런트 엔드 응용 프로그램을 선택한 다음 '도메인 선택'에 대한 드롭다운 아래에 표시되는 b2clogin.com 도메인의 주소를 기록합니다.
1. 상단의 링크를 클릭하여 '잘 알려진 openid 구성 끝점'을 열고 잘 알려진 openid 구성 끝점으로 링크 자체의 값뿐만 아니라 authorization_endpoint 및 token_endpoint 값을 기록합니다.

   > [!NOTE]
   > B2C 정책을 사용하면 Azure AD B2C 로그인 끝점을 노출하여 다양한 데이터 구성 요소를 캡처하고 다양한 방법으로 사용자를 로그인할 수 있습니다. 이 경우 잘 알려진 구성 끝점을 노출하는 끝점에 등록 또는 로그인을 구성했는데, 특히 생성된 정책이 url에서 p= 매개 변수로 식별되었습니다.
   > 
   > 이 작업이 완료되면 이제 여러 응용 프로그램에 사용자를 서명하는 기능적인 비즈니스- 소비자 ID 플랫폼이 생겼습니다. 
   > 원하는 경우 여기에서 '사용자 흐름 실행' 단추를 클릭하고(등록 또는 로그인 프로세스를 거치도록) 실제로 수행할 작업을 수행할 수 있지만 앱이 아직 배포되지 않았기 때문에 마지막의 리디렉션 단계가 실패합니다.

## <a name="build-the-function-api"></a>함수 API 빌드
1. 구독에서 항목을 다시 구성할 수 있도록 Azure 포털에서 표준 Azure AD 테넌트로 다시 전환 
1. Azure 포털의 함수 앱 블레이드로 이동하여 빈 함수 앱을 연 다음 빠른 시작을 통해 새로운 Portal 'Webhook + API' 함수를 만듭니다.
1. 아래에서 샘플 코드를 Run.csx에 붙여 넣은 기존 코드 위에 나타납니다.

   ```csharp
   
   using System.Net;
   using Microsoft.AspNetCore.Mvc;
   using Microsoft.Extensions.Primitives;
   
   public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
   {
      log.LogInformation("C# HTTP trigger function processed a request.");
      
      return (ActionResult)new OkObjectResult($"Hello World, time and date are {DateTime.Now.ToString()}");
   }
   
   ```

   > [!NOTE]
   > 방금 붙여넣은 c# 스크립트 함수 코드는 함수 로그에 한 줄을 기록하고 동적 데이터(날짜 및 시간)가 있는 "Hello World"라는 텍스트를 반환합니다.

3. 왼쪽 블레이드에서 "통합"을 선택한 다음 창의 오른쪽 상단 모서리에 있는 '고급 편집기'를 선택합니다.
4. 아래 샘플 코드를 기존 json 위에 붙여 넣습니다.

   ```json
   {
      "bindings": [
       {
        "authLevel": "function",
        "name": "req",
        "type": "httpTrigger",
        "direction": "in",
        "methods": [
           "get"
        ],
        "route": "hello"
       },
       {
         "name": "$return",
         "type": "http",
         "direction": "out"
       }
     ]
   }
   ```

5. HttpTrigger1 탭으로 다시 전환한 다음 '기능 URL 받기'를 클릭한 다음 나타나는 URL을 복사합니다.

   > [!NOTE]
   > 방금 만든 바인딩은 방금 복사한 URL에 익명 http GET 요청에 응답하도록 함수에 알리기만 하면 됩니다. (이제https://yourfunctionappname.azurewebsites.net/api/hello?code=secretkey) 우리는 매우 간단한 페이로드를 반환 할 수있는 확장 가능한 서버리스 HTTPS API를 가지고 있습니다.
   > 이제 위의 URL을 사용하여 웹 브라우저에서 이 API를 호출하는 테스트를 수행할 수 있으며 URL의 ?code=비밀 부분을 제거하고 Azure Functions가 401 오류를 반환한다는 것을 증명할 수도 있습니다.

## <a name="configure-and-secure-the-function-api"></a>기능 API 구성 및 보안
1. 함수 앱의 두 가지 추가 영역을 구성해야 합니다(인증 및 네트워크 제한).
1. 먼저 인증 / 권한 부여를 구성하자, 그래서 개요 페이지를 표시하려면 &lt;&gt; 기능 응용 프로그램의 이름 (Z 기능 아이콘 옆에)를 클릭합니다.
1. 다음으로 '플랫폼 기능' 탭을 선택하고 '인증/권한 부여'를 선택합니다.
1. 앱 서비스 인증 기능을 켭니다.
1. '인증 공급자'에서 'Azure Active Directory'를 선택하고 관리 모드 스위치에서 '고급'을 선택합니다.
1. 백 엔드 함수 API의 응용 프로그램 ID를 붙여 넣기 (Azure AD B2C에서 '클라이언트 ID' 상자에)
1. 등록에서 잘 알려진 오픈 ID 구성 끝점을 붙여넣거나 정책에 로그인하여 발급자 URL 상자에 붙여넣습니다(이 구성을 이전에 기록했습니다).
1. 확인을 선택합니다.
1. 요청이 인증된 드롭다운이 인증되지 않은 경우 취할 작업을 "Azure Active Directory로 로그인"으로 설정한 다음 저장을 클릭합니다.

   > [!NOTE]
   > 이제 Function API가 배포되고 올바른 키가 제공되지 않으면 401 응답을 throw해야 하며 유효한 요청이 표시될 때 데이터를 반환해야 합니다.
   > 인증되지 않은 요청을 처리하기 위해 'Azure AD로 로그인' 옵션을 구성하여 EasyAuth에 심층 방어 보안을 추가했습니다. EasyAuth가 401 승인되지 않은 응답 대신 AAD로 302 리디렉션을 발행하므로 백 엔드 함수 앱과 프런트 엔드 SPA 간의 무단 요청 동작이 변경되며 나중에 API 관리를 사용하여 이 문제를 해결합니다.
   > 유효한 키와 OAuth2 토큰이 있는 경우 누구나 어디서나 호출할 수 있으므로 모든 요청이 API 관리를 통해 강제로 제공되기를 원합니다.
   > API 관리 소비 계층을 사용하는 경우 해당 계층에 대한 전용 정적 IP가 없으므로 VIP에서 이 잠금을 수행할 수 없으므로 공유 비밀 함수 키를 통해 API 호출을 잠그는 방법에 의존해야 합니다. , 그래서 단계 11-13은 가능하지 않습니다.

1. '인증/권한 부여' 블레이드 닫기 
1. '네트워킹'을 선택한 다음 '액세스 제한'을 선택합니다.
1. 다음으로 허용된 함수 앱 IP를 API 관리 인스턴스 VIP에 잠급전합니다. 이 VIP는 포털의 API 관리 - 개요 섹션에 표시됩니다.
1. 함수 포털과 계속 상호 작용하고 아래 의 선택적 단계를 수행하려면 여기에 자체 공용 IP 주소 또는 CIDR 범위를 추가해야 합니다.
1. 목록에 허용 항목이 있으면 Azure는 암시적 거부 규칙을 추가하여 다른 모든 주소를 차단합니다. 

IP 제한 패널에 CIDR 형식의 주소 블록을 추가해야 합니다. API 관리 VIP와 같은 단일 주소를 추가해야 하는 경우 xx.xx.xx.xx 형식에 추가해야 합니다.

   > [!NOTE]
   > 이제 함수 API는 API 관리 또는 주소를 통해 다른 곳에서 호출할 수 없습니다.
   
## <a name="import-the-function-app-definition"></a>함수 앱 정의 가져오기
1. API *관리 블레이드를*연 다음 *인스턴스를 엽니다.*
1. 인스턴스의 API 관리 섹션에서 API 블레이드를 선택합니다.
1. '새 API 추가' 창에서 '함수 앱'을 선택한 다음 팝업 상단에서 '전체'를 선택합니다.
1. 찾아보기를 클릭하고 API를 호스팅하는 함수 앱을 선택한 다음 선택을 클릭합니다.
1. API에 API 관리의 내부 사용에 대한 이름과 설명을 지정하고 '무제한' 제품에 추가합니다.
1. 나중에 사용할 기본 URL을 기록한 다음 만들기를 클릭합니다.

## <a name="configure-oauth2-for-api-management"></a>API 관리를 위한 Oauth2 구성

1. 다음으로 보안 탭에서 Oauth 2.0 블레이드를 선택하고 '추가'를 클릭합니다.
1. 추가된 Oauth 끝점에 대한 *표시 이름* 및 *설명값을* 지정합니다(이러한 값은 다음 단계에서 Oauth2 끝점으로 표시됩니다).
1. 이 값은 사용되지 않기 때문에 클라이언트 등록 페이지 URL에 모든 값을 입력할 수 있습니다.
1. *암시적 인증* 부여 형식을 확인하고 권한 부여 코드 부여 형식을 확인한 상태로 둡니다.
1. *권한 부여* 및 *토큰* 끝점 필드로 이동하고 잘 알려진 구성 xml 문서에서 캡처한 값을 이전에 입력합니다.
1. 아래로 스크롤하여 Azure AD B2C 앱 등록에서 백엔드 함수 API 클라이언트 ID를 사용 하 여 '리소스'라는 *추가 본문 매개 변수를* 채웁니다.
1. '클라이언트 자격 증명'을 선택하고 클라이언트 ID를 개발자 콘솔 앱앱 앱 ID로 설정 - 소비 API 관리 모델을 사용하는 경우 이 단계를 건너뜁니다.
1. 이전에 기록한 키로 클라이언트 보안을 설정합니다 .
1. 마지막으로 나중에 사용할 수 있는 API 관리에서 인증 코드 부여의 redirect_uri 기록합니다.

## <a name="set-up-oauth2-for-your-api"></a>API에 대해 Oauth2 설정
1. API는 '모든 API' 섹션 아래 포털의 왼쪽에 표시되며, API를 클릭하여 엽니다.
1. '설정' 탭을 선택합니다.
1. 사용자 권한 부여 라디오 단추에서 "Oauth 2.0"을 선택하여 설정을 업데이트합니다.
1. 이전에 정의한 Oauth 서버를 선택합니다.
1. '재정의 범위' 확인란을 확인하고 백 엔드 API 호출에 대해 이전에 기록한 범위를 입력합니다.

   > [!NOTE]
   > 이제 요청을 승인하기 위해 Azure AD B2C에서 액세스 토큰을 얻는 방법을 알고 있는 API 관리 인스턴스가 있으며 Oauth2 Azure Active Directory B2C 구성을 이해합니다.

## <a name="set-up-the-cors-and-validate-jwt-policies"></a>**CORS** 설정 및 **유효성 검사 jwt** 정책

> 사용 중인 APIM 계층에 관계없이 다음 섹션을 따라야 합니다. 

1. 디자인 탭으로 다시 전환하고 "모든 API"를 선택한 다음 코드 보기 버튼을 클릭하여 정책 편집기를 표시합니다.
1. 인바운드 섹션을 편집하고 아래 xml을 붙여 넣기하면 다음과 같이 읽습니다.

   ```xml
   <inbound>
      <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
         <openid-config url="https://tenant.b2clogin.com/tenant.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=B2C_1_MyDefaultPolicy" />
         <required-claims>
            <claim name="aud">
               <value>your-backend-api-application-client-id</value>
            </claim>
         </required-claims>
      </validate-jwt>
      <cors>
         <allowed-origins>
            <origin>*</origin>
         </allowed-origins>
         <allowed-methods>
           <method>GET</method>
         </allowed-methods>
         <allowed-headers>
            <header>*</header>
         </allowed-headers>
         <expose-headers>
           <header>*</header>
         </expose-headers>
       </cors>
   </inbound>
   ```
1. 등록 또는 로그인 정책에 대해 잘 알려진 Azure AD B2C 끝점과 일치하도록 openid-config URL을 편집합니다.
1. 백 엔드 API 응용 프로그램 및 저장에 대한 클라이언트 ID라고도 하는 유효한 응용 프로그램 ID와 일치하도록 클레임 값을 편집합니다.

   > [!NOTE]
   > 이제 API 관리는 JS SPA 앱에 대한 교차 원본 요청에 응답할 수 있으며, 함수 API에 요청을 전달하기 전에 전달되는 JWT 인증 토큰의 제한, 속도 제한 및 사전 유효성 검사를 수행합니다.

   > [!NOTE]
   > 다음 섹션은 선택 사항이며 개발자 포털을 지원하지 않는 **소비** 계층에는 적용되지 않습니다.
   > 개발자 포털을 사용하지 않으려는 경우 또는 소비 계층을 사용하기 때문에 사용할 수 없는 경우 이 단계를 건너뛰고 ["API를 사용하기 위해 JavaScript SPA 빌드"로](#build-the-javascript-spa-to-consume-the-api)바로 이동하십시오.

## <a name="optional-configure-the-developer-portal"></a>[선택 사항] 개발자 포털 구성

1. Azure AD B2C 블레이드를 열고 개발자 포털의 응용 프로그램 등록으로 이동합니다.
1. API 관리에서 인증 코드 부여의 redirect_uri 이전에 구성할 때 기록한 '회신 URL' 항목을 설정합니다.

   이제 OAuth 2.0 사용자 권한 부여가 `Echo API`활성화되었으므로 개발자 콘솔은 API를 호출하기 전에 사용자에 대한 액세스 토큰을 얻습니다.

1. 개발자 `Echo API` 포털에서 모든 작업을 찾아보고 개발자 콘솔로 가져오려면 **시도를** 선택합니다.
1. 방금 추가한 권한 부여 서버에 해당하는 **권한 부여** 섹션의 새 항목을 참고합니다.
1. 권한 부여 드롭다운 목록에서 **권한 부여 코드를** 선택하면 Azure AD 테넌트에 로그인하라는 메시지가 표시됩니다. 이미 계정으로 로그인한 경우 메시지가 표시되지 않을 수 있습니다.
1. 로그인에 성공하면 `Authorization: Bearer` Azure AD B2C의 액세스 토큰이 Base64에서 인코딩된 채 요청에 헤더가 추가됩니다. 
1. **보내기를** 선택하면 API를 성공적으로 호출할 수 있습니다.

   > [!NOTE]
   > 이제 API 관리는 개발자 포털에 대한 토큰을 획득하여 API를 테스트할 수 있으며, API의 정의를 이해하고 개발 포털에서 적절한 테스트 페이지를 렌더링할 수 있습니다.

1. API 관리 포털의 개요 블레이드에서 '개발자 포털'을 클릭하여 API 관리자로 로그인합니다.
1. 여기서 사용자 및 API의 선택한 다른 소비자는 콘솔에서 테스트하고 호출할 수 있습니다.
1. '제품'을 선택한 다음 '무제한'을 선택한 다음 이전에 만든 API를 선택하고 'TRY IT'를 클릭합니다.
1. API 구독 키를 숨기지 않고 나중에 필요한 요청 URL과 함께 안전한 곳에 복사합니다.
1. 또한 암시를 선택, oauth 인증 드롭 다운에서 당신은 팝업여기 인증 해야 할 수 있습니다.
1. '보내기'를 클릭하고 모든 것이 잘되면 함수 앱이 200 OK 메시지와 일부 JSON으로 API 관리를 통해 hello 메시지로 응답해야합니다.

   > [!NOTE]
   > 이제 Azure AD B2C, API 관리 및 Azure 함수가 함께 작동하여 API를 게시, 보안 및 사용할 수 있습니다. API 관리 Ocp-구독-키 헤더를 사용 하 여 한 번, 권한 부여: Bearer JWT를 사용 하 여 API를 실제로 두 번 보안 된 것으로 나타났습니다 수 있습니다.
   > 이 예제는 JavaScript 단일 페이지 응용 프로그램이기 때문에 속도 제한 및 청구 호출에대해서만 API 관리 키를 사용합니다.
   > 실제 권한 부여 및 인증은 Azure AD B2C에서 처리되며 JWT에 캡슐화되어 API 관리에서 두 번, 한 번, Azure 함수에 의해 유효성을 검사합니다.

## <a name="build-the-javascript-spa-to-consume-the-api"></a>API를 사용 하 고 자바 스크립트 SPA를 구축
1. Azure 포털에서 저장소 계정 블레이드 열기 
1. 만든 계정을 선택하고 설정 섹션에서 '정적 웹 사이트' 블레이드를 선택합니다('정적 웹 사이트' 옵션이 표시되지 않으면 V2 계정을 만들었는지 확인하십시오).
1. 정적 웹 호스팅 기능을 '사용'으로 설정하고 인덱스 문서 이름을 'index.html'으로 설정한 다음 '저장'을 클릭합니다.
1. 프런트 엔드 사이트가 호스팅되는 위치이기 때문에 기본 끝점의 내용을 기록합니다. 

   > [!NOTE]
   > Azure Blob Storage + CDN 다시 쓰기 또는 Azure 앱 서비스를 사용할 수 있지만 Blob Storage의 정적 웹 사이트 호스팅 기능은 Azure Storage에서 정적 웹 콘텐츠/ html/js/css를 제공하는 기본 컨테이너를 제공하며 제로 작업에 대한 기본 페이지를 유추합니다.

## <a name="upload-the-js-spa-sample"></a>JS SPA 샘플 업로드
1. 저장소 계정 블레이드에서 Blob 서비스 섹션에서 'Blob' 블레이드를 선택하고 오른쪽 창에 나타나는 $web 컨테이너를 클릭합니다.
1. 아래 코드를 index.html로 컴퓨터에 로컬로 저장한 다음 파일 index.html을 $web 컨테이너에 업로드합니다.

   ```html
   <!doctype html>
   <html lang="en">
   <head>
        <meta charset="utf-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1">
        <title>Sample JS SPA</title>
        <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css" integrity="sha384-Gn5384xqQ1aoWXA+058RXPxPg6fy4IWvTNh0E263XmFcJlSAwiGgFAW/dAiS6JXm" crossorigin="anonymous">
   </head>
   <body>
        <div class="container-fluid">
            <div class="row">
                <div class="col-md-12">
                    <nav class="navbar navbar-expand-lg navbar-light bg-light navbar-dark bg-dark">
                        <a class="navbar-brand" href="#">Sample Code</a>
                        <ul class="navbar-nav ml-md-auto">
                            <li class="nav-item dropdown">
                                <a class="btn btn-large btn-success" onClick="login()">Sign In</a>
                            </li>
                        </ul>
                    </nav>
                </div>
            </div>
            <div class="row">
                <div class="col-md-12">
                    <div class="jumbotron">
                        <h2>
                    <div id="message">Hello, world!</div>
                    </h2>
                        <p>
                            <a class="btn btn-primary btn-large" onClick="GetAPIData()">Call API</a>
                        </p>
                    </div>
                </div>
            </div>
        </div>
        <script src="https://code.jquery.com/jquery-3.2.1.min.js"></script>
        <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.12.9/umd/popper.min.js" integrity="sha384-ApNbgh9B+Y1QKtv3Rn7W3mgPxhU9K/ScQsAP7hUibX39j7fakFPskvXusvfa0b4Q" crossorigin="anonymous"></script>
        <script src="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/js/bootstrap.min.js" integrity="sha384-JZR6Spejh4U02d8jOt6vLEHfe/JQGiRRSQQxSfFWpi1MquVdAyjUar5+76PVCmYl" crossorigin="anonymous"></script>
        <script src="https://secure.aadcdn.microsoftonline-p.com/lib/1.0.0/js/msal.js"></script>
        <script lang="javascript">
            var applicationConfig = {
                clientID: "clientidgoeshere",
                authority: "https://tenant.b2clogin.com/tfp/tenant/policy",
                b2cScopes: ["https://tenant/app/scope"],
                webApi: 'http://functionurl',
                subKey: 'apimkeygoeshere'
            };
            var msalConfig = {
                auth: {
                        clientId: applicationConfig.clientID, 
                        authority:  applicationConfig.authority,
                        validateAuthority: false
                },
                cache: {
                        cacheLocation: "localStorage",
                        storeAuthStateInCookie: true
                }
            };
            var clientApplication = new Msal.UserAgentApplication(msalConfig);
            function login() {
                var loginRequest = {
                    scopes: applicationConfig.b2cScopes
                };
                clientApplication.loginPopup(loginRequest).then(function (loginResponse) {
                    var tokenRequest = {
                        scopes: applicationConfig.b2cScopes
                    };
                    clientApplication.acquireTokenSilent(tokenRequest).then(function (tokenResponse) {
                        document.getElementById("signinbtn").innerHTML = "Logged in as: " + clientApplication.account.name;
                        document.getElementById("callapibtn").hidden = false
                        }).catch(function (error) {
                            clientApplication.acquireTokenPopup(tokenRequest).then(function (tokenResponse) {
                                }).catch (function (error) {
                                    console.log("Error acquiring the popup:\n" + error);
                                });
                        })
                    }).catch (function (error) {
                        console.log("Error during login:\n" + error);
                });
            }
            function GetAPIData() {
                var tokenRequest = {
                    scopes: applicationConfig.b2cScopes
                }
                clientApplication.acquireTokenSilent(tokenRequest).then(function (tokenResponse) {
                    callApiWithAccessToken(tokenResponse.accessToken);
                    }).catch(function (error) {
                        clientApplication.acquireTokenPopup(tokenRequest).then(function (tokenResponse) {
                            callApiWithAccessToken(tokenResponse.accessToken);
                            
                        }).catch(function (error) {
                            console.log("Error acquiring the access token to call the Web api:\n" + error);
                        });
                    })
            }
            function callApiWithAccessToken(token)
            {
                console.log("calling "  + applicationConfig.webApi +  " with " + token);
                    // Make the api call here
                $.ajax({
                    type: "get",
                    headers: {'Authorization': 'Bearer ' + token, 'Ocp-Apim-Subscription-Key': applicationConfig.subKey},                   url: applicationConfig.webApi
                }
                ).done(function (body) {
                    document.getElementById("message").innerHTML = "The API Said " + body;
                });
            }
        </script>
    </body>
   </html>
   
   ```

1. 마지막 섹션의 앞에서 저장한 정적 웹 사이트 기본 끝점을 찾아봅습니다.

   > [!NOTE]
   > 축하합니다, 당신은 방금 Azure 저장소에 자바 스크립트 단일 페이지 응용 프로그램을 배포 하지 않은 이후 Api에 대 한 키와 JS 애플 리 케이 션을 구성 하지 않은 또는 Azure AD B2C 세부 정보와 JS 애플 리 케이 션을 구성- 페이지는 아직 그것을 열 면 작동 하지 않습니다.

## <a name="configure-the-js-spa-for-azure-ad-b2c"></a>Azure AD B2C에 대한 JS SPA 구성
1. 이제 모든 것이 어디에 있는지 알고 있습니다: 적절한 API 관리 API 주소와 올바른 Azure AD B2C 응용 프로그램/클라이언트 아이디로 SPA를 구성할 수 있습니다.
1. Azure 포털 저장소 블레이드로 돌아가 index.html을 클릭한 다음 'Blob 편집'을 선택합니다. 
1. 이전에 B2C에 등록한 프런트 엔드 응용 프로그램과 일치하도록 인증 세부 정보를 업데이트하여 'b2cScopes' 값이 API 백 엔드용임을 확인합니다.
1. webApi 키 및 API URL은 API 작업에 대한 API 관리 테스트 창에서 찾을 수 있습니다.
1. API 관리 블레이드로 돌아가 '구독'을 선택하고 '구독 추가'를 클릭한 다음 레코드를 저장하여 APIM 구독 키를 만듭니다. 생성된 행 옆에 있는 타원(...)을 클릭하면 기본 키를 복사할 수 있도록 키를 표시할 수 있습니다.
1. 아래 코드와 같이 보일 것입니다.  

   ```javascript
   var applicationConfig =
      clientID: "{aadb2c-clientid-goeshere}",
      authority: "https://{tenant}.b2clogin.com/{tenant}/{policy}",
      b2cScopes: ["https://{tenant}/{app}/{scope}"], 
      webApi: 'http://{apim-url-for-your-function}',
      subKey: '{apim-subscription-key-goes-here}'
   };
   ```

1. 저장을 클릭합니다.

## <a name="set-the-redirect-uris-for-the-azure-ad-b2c-frontend-app"></a>Azure AD B2C 프런트 엔드 앱에 대한 리디렉션 URI 설정
1. Azure AD B2C 블레이드를 열고 JavaScript 프런트 엔드 응용 프로그램의 응용 프로그램 등록으로 이동합니다.
1. 위의 정적 웹 사이트 기본 끝점을 이전에 설정할 때 아래로 표시된 URL로 리디렉션 URL을 설정합니다.

   > [!NOTE] 
   > 이 구성으로 인해 프런트 엔드 응용 프로그램의 클라이언트는 Azure AD B2C에서 적절한 클레임이 있는 액세스 토큰을 받습니다.
   > SPA는 백 엔드 API에 대한 호출의 https 헤더에 이를 보유자 토큰으로 추가할 수 있습니다.
   > API 관리는 요청을 수신 Azure Function API로 전달하기 전에 구독자 키로 토큰, 속도 제한 호출의 유효성을 검사합니다.
   > SPA는 브라우저에서 응답을 렌더링합니다.

   > *축하합니다, 당신은 완벽한 조화에서 작동하도록 Azure AD B2C, Azure API 관리, Azure 함수, Azure 앱 서비스 권한 부여를 구성했습니다!*

   > [!NOTE]
   > 이제 우리는 간단한 보안 API와 간단한 응용 프로그램을 가지고, 그것을 테스트 할 수 있습니다.

## <a name="test-the-client-application"></a>클라이언트 응용 프로그램 테스트
1. 이전에 만든 저장소 계정에서 아래로 기록한 샘플 앱 URL을 엽니다.
1. 오른쪽 상단 모서리에서 "로그인"을 클릭하면 이 클릭으로 Azure AD B2C 가입 또는 프로필 로그인이 나타납니다.
1. 화면의 "로그인" 섹션에 게시 로그인은 JWT에서 채워집니다.
1. 이제 "웹 Api 호출"을 클릭하면 보안 API에서 다시 전송된 값으로 페이지가 업데이트됩니다.

## <a name="and-were-done"></a>그리고 우리는 끝났습니다.
위의 단계는 API 관리를 통해 Azure AD B2C의 다양한 용도를 허용하도록 조정 및 편집할 수 있습니다.

## <a name="next-steps"></a>다음 단계
* [Azure Active Directory 및 OAuth2.0](../active-directory/develop/authentication-scenarios.md)에 대해 자세히 알아봅니다.
* API Management에 대한 추가 [비디오](https://azure.microsoft.com/documentation/videos/index/?services=api-management) 를 확인합니다.
* 백 엔드 서비스를 보호하는 다른 방법은 [상호 인증서 인증](api-management-howto-mutual-certificates.md)을 참조하세요.
* [API 관리 서비스 인스턴스를 만듭니다.](get-started-create-service-instance.md)
* [첫 번째 API 관리](import-and-publish.md)
