---
title: Azure Active Directory B2C 및 Azure API Management를 사용 하 여 OAuth 2.0로 SPA 백 엔드를 보호 합니다.
description: JavaScript SPA에서 호출할 수 있는 Azure Active Directory B2C, Azure API Management 및 간편한 인증을 사용 하 여 OAuth 2.0로 API를 보호 합니다.
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
ms.openlocfilehash: daf38baf9daff5fd192091be977a996c9bd5cfc2
ms.sourcegitcommit: 163be411e7cd9c79da3a3b38ac3e0af48d551182
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/21/2020
ms.locfileid: "77539865"
---
# <a name="protect-spa-backend-with-oauth-20-azure-active-directory-b2c-and-azure-api-management"></a>OAuth 2.0, Azure Active Directory B2C 및 Azure API Management를 사용 하 여 SPA 백 엔드 보호

이 시나리오에서는 API를 보호 하도록 Azure API Management 인스턴스를 구성 하는 방법을 보여 줍니다.
EasyAuth를 사용 하 여 Azure Functions 백 엔드를 보호 하기 위해 API Management와 함께 Openid connect Connect 프로토콜을 사용 하 여 Azure AD B2C 합니다.

## <a name="aims"></a>목표
Azure Functions 및 Azure AD B2C를 사용 하 여 간소화 된 시나리오에서 API Management를 사용 하는 방법을 살펴보겠습니다. Azure AD B2C를 사용 하 여 사용자를 로그인 하는 API를 호출 하는 JavaScript (JS) 앱을 만듭니다. 그런 다음 API Management의 jwt 유효성 검사 기능을 사용 하 여 백 엔드 API를 보호 합니다.

심층 방어를 위해 EasyAuth를 사용 하 여 백 엔드 API 내에서 토큰의 유효성을 다시 검사 합니다.

## <a name="prerequisites"></a>사전 요구 사항
이 문서의 단계를 따르려면 다음이 있어야 합니다.
* 프런트 엔드 JS 단일 페이지 앱을 호스트 하는 Azure (StorageV2) 범용 V2 저장소 계정
* Azure API Management 인스턴스 
* 호출 된 API를 호스트 하는 빈 Azure 함수 앱 (Windows 소비 계획에서 V2 .NET Core 런타임 실행)
* 구독에 연결 된 Azure AD B2C 테 넌 트 

실제로 프로덕션 워크 로드에서 동일한 지역의 리소스를 사용 하는 경우에도이 방법 문서에서는 배포 지역이 중요 하지 않습니다.

## <a name="overview"></a>개요
다음은 사용 중인 구성 요소와이 프로세스가 완료 된 후 이러한 구성 요소 간의 흐름을 보여 주는 그림입니다.
![사용 중인 구성 요소 및 흐름](../api-management/media/howto-protect-backend-frontend-azure-ad-b2c/image-arch.png "사용 중인 구성 요소 및 흐름")

다음은 단계에 대한 간략한 개요입니다.

1. 범위를 포함 하는 Azure AD B2C (프런트 엔드, API Management) 및 API 응용 프로그램을 만들고 API 액세스를 부여 합니다.
1. 사용자가 Azure AD B2C를 사용 하 여 로그인 할 수 있도록 등록 또는 로그인 정책 만들기 
1. 개발자 콘솔에서 OAuth2 사용자 권한 부여를 사용 하도록 새 Azure AD B2C 클라이언트 Id 및 키를 사용 하 여 API Management 구성
1. 함수 API 빌드
1. 새 Azure AD B2C 클라이언트 ID 및 키로 EasyAuth를 사용 하도록 설정 하 고 APIM VIP로 잠금을 해제 하도록 함수 API를 구성 합니다. 
1. API Management에서 API 정의를 빌드합니다.
1. API Management API 구성에 대해 Oauth2 설정
1. **CORS** 정책을 설정 하 고 **jwt 유효성 검사** 정책을 추가 하 여 들어오는 모든 요청에 대 한 OAuth 토큰의 유효성을 검사 합니다.
1. API를 사용 하는 호출 응용 프로그램 빌드
1. JS SPA 샘플 업로드
1. 새 Azure AD B2C 클라이언트 ID 및 키를 사용 하 여 샘플 JS 클라이언트 앱 구성 
1. 클라이언트 응용 프로그램 테스트

## <a name="configure-azure-ad-b2c"></a>Azure AD B2C 구성 
포털에서 Azure AD B2C 블레이드를 열고 다음 단계를 수행 합니다.
1. **응용 프로그램** 탭을 선택 합니다. 
1. ' 추가 ' 단추를 클릭 하 고 다음 용도로 세 가지 응용 프로그램을 만듭니다.
   * 프런트 엔드 클라이언트입니다.
   * 백엔드 함수 API입니다.
   * 필드 API Management 개발자 포털 (소비 계층에서 Azure API Management를 실행 하는 경우가 아니면 나중에이 시나리오에 대 한 자세한 정보).
1. WebApp/Web API를 설정 하 고 암시적 흐름 허용을 예로 설정 합니다.
1. 이제 앱 ID URI를 설정 하 고, 생성 되는 서비스와 관련 된 고유한 항목을 선택 합니다.
1. 현재 https://localhost와 같이 회신 url에 대 한 자리 표시자를 사용 하 여 나중에 해당 url을 업데이트 합니다.
1. ' 만들기 '를 클릭 한 다음 위의 세 앱 각각에 대해 2-5 단계를 반복 하 여 세 앱 모두에 대해 나중에 사용할 AppID URI, 이름 및 응용 프로그램 ID를 기록 합니다.
1. 응용 프로그램 목록에서 백 엔드 API를 열고 *키* 탭 (일반)을 선택한 다음 ' 키 생성 '을 클릭 하 여 인증 키를 생성 합니다.
1. 저장을 클릭 하면 나중에 사용 하기 위해 안전 하 게 키를 기록 합니다 .이 위치는이 키를 보고 복사 하는 유일한 기회입니다.
1. 이제 게시 된 *범위* 탭 (API 액세스 아래)을 선택 합니다.
1. 함수 API에 대 한 범위를 만들고 이름을 지정한 후 범위를 기록 하 고 전체 범위 값을 채운 후 ' 저장 '을 클릭 합니다.
   > [!NOTE]
   > Azure AD B2C 범위는 응용 프로그램에서 API 액세스 블레이드를 통해 다른 응용 프로그램에 대 한 액세스를 요청할 수 있는 API 내에서 효과적으로 사용 되는 권한으로, 호출 된 API에 대 한 응용 프로그램 권한을 효과적으로 만들었습니다.
1. 다른 두 응용 프로그램을 열고 *API 액세스* 탭에서 확인 합니다.
1. 백 엔드 API 범위에 대 한 액세스 권한을 부여 하 고 이미 여기에 있는 기본 항목 ("사용자 프로필 액세스")을 부여 합니다.
1. ' 일반 ' 아래의 *키* 탭을 선택 하 여 각 키를 생성 하 여 인증 키를 생성 하 고 나중에 안전 하 게 해당 키를 기록 합니다.

## <a name="create-a-sign-up-or-sign-in-user-flow"></a>"등록 또는 로그인" 사용자 흐름 만들기
1. Azure AD B2C 블레이드의 루트 (또는 ' 개요 ')로 돌아갑니다. 
1. "사용자 흐름 (정책)"을 선택 하 고 "새 사용자 흐름"을 클릭 합니다.
1. ' 등록 및 로그인 ' 사용자 흐름 유형 선택
1. 정책에 이름을 지정 하 고 나중에 기록 합니다.
1. 그런 다음 ' Id 공급자 ' 아래에서 ' 사용자 ID 등록 '을 선택 하 고 확인을 클릭 합니다. 
1. ' 사용자 특성 및 클레임 '에서 ' 자세히 표시 ... '를 클릭 합니다. 그런 다음 사용자가 입력 하 고 토큰에서 반환 하려는 클레임 옵션을 선택 합니다. ' 표시 이름 ' 및 ' 전자 메일 주소 '를 확인 하 여 수집 하 여 반환 하 고 ' 확인 '을 클릭 한 다음 ' 만들기 '를 클릭 합니다.
1. 목록에서 만든 정책을 선택한 다음 ' 사용자 흐름 실행 ' 단추를 클릭 합니다.
1. 이 작업은 사용자 흐름 실행 블레이드를 열고, 프런트 엔드 응용 프로그램을 선택한 다음, ' 도메인 선택 ' 드롭다운 아래에 표시 되는 b2clogin.com 도메인의 주소를 기록 합니다.
1. 맨 위에 있는 링크를 클릭 하 여 ' 잘 알려진 openid connect 구성 끝점 '을 열고, authorization_endpoint 및 token_endpoint 값을 기록 하 고, 잘 알려진 openid connect 구성 끝점으로 링크 자체의 값을 기록 합니다.

   > [!NOTE]
   > B2C 정책을 사용 하면 다양 한 방식으로 다양 한 데이터 구성 요소 및 로그인 사용자를 캡처할 수 있도록 Azure AD B2C 로그인 끝점을 노출할 수 있습니다. 이 경우에는 잘 알려진 구성 끝점을 노출 하는 등록 또는 로그인 끝점을 구성 했습니다. 특히 생성 된 정책은 p = 매개 변수에 의해 URL에서 식별 되었습니다.
   > 
   > 이 작업이 완료 되 면 이제 사용자에 게 여러 응용 프로그램에 로그인 하는 기능을 갖춘 비즈니스 id 플랫폼이 있습니다. 
   > 원하는 경우 여기에서 ' 사용자 흐름 실행 ' 단추를 클릭 하 고 (등록 또는 로그인 프로세스를 진행 하는 경우), 앱이 아직 배포 되지 않았기 때문에 끝에 있는 리디렉션 단계가 실패 합니다.

## <a name="build-the-function-api"></a>함수 API 빌드
1. Azure Portal의 함수 앱 블레이드로 이동 하 여 빈 함수 앱을 연 다음 퀵 스타트를 통해 포털 내 새 ' Webhook + API ' 함수를 만듭니다.
1. 아래에 있는 샘플 코드를 표시 되는 기존 코드에 대해 csx에 붙여넣습니다.

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
   > 방금 붙여 넣은 c # 스크립트 함수 코드는 단순히 함수 로그에 줄을 기록 하 고 일부 동적 데이터 (날짜 및 시간)를 사용 하 여 "Hello World" 텍스트를 반환 합니다.

3. 왼쪽 블레이드의 "통합"을 선택한 다음 창의 오른쪽 위 모퉁이에서 ' 고급 편집기 '를 선택 합니다.
4. 아래 샘플 코드를 기존 json 위에 붙여넣습니다.

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

5. HttpTrigger1 탭으로 다시 전환 하 고 ' 함수 URL 가져오기 '를 클릭 한 다음 표시 되는 URL을 복사 합니다.

   > [!NOTE]
   > 방금 만든 바인딩은 방금 복사한 URL에 대 한 익명 http GET 요청에 응답 하도록 함수에 지시 합니다. (현재 https://yourfunctionappname.azurewebsites.net/api/hello?code=secretkey) 매우 간단한 페이로드를 반환할 수 있는 확장 되지 않은 서버를 사용 하는 https API가 있습니다.
   > 이제 위의 URL을 사용 하 여 웹 브라우저에서이 API 호출을 테스트할 수 있습니다. 또한 URL의? code = secret 부분을 제거 하 고 Azure Functions에서 401 오류를 반환 한다는 것을 입증할 수 있습니다.

## <a name="configure-and-secure-the-function-api"></a>함수 API 구성 및 보안
1. 함수 앱의 두 추가 영역을 구성 해야 합니다 (인증 및 네트워크 제한).
1. 먼저 인증/권한 부여를 구성 해 보겠습니다. 그러면 함수 앱의 이름 (&lt;Z&gt; 함수 아이콘 옆에 있는)을 클릭 하 여 개요 페이지를 표시 합니다.
1. 그런 다음 ' 플랫폼 기능 ' 탭을 선택 하 고 ' 인증/권한 부여 '를 선택 합니다.
1. App Service 인증 기능을 설정 합니다.
1. ' 인증 공급자 '에서 ' Azure Active Directory '를 선택 하 고 관리 모드 스위치에서 ' 고급 '을 선택 합니다.
1. 백 엔드 API의 응용 프로그램 ID를 Azure AD B2C에서 ' 클라이언트 ID ' 상자로 붙여넣습니다.
1. 등록 또는 로그인 정책에서 잘 알려진 오픈 id 구성 끝점을 발급자 URL 상자에 붙여넣습니다 (이전에이 구성을 기록 함).
1. Azure AD B2C 응용 프로그램에 대해 이전에 기록한 토큰을 허용 된 토큰 대상에 기록한 3 개 (API Management 사용 모델을 사용 하는 경우 두 개의 응용 프로그램 Id를 추가 합니다 .이 설정은 수신 된 토큰에 허용 되는 AUD 클레임 값을 EasyAuth에 알려 줍니다.
1. 확인을 선택 하 고 저장을 클릭 합니다.

   > [!NOTE]
   > 이제 함수 API가 배포 되 고, 권한 없는 요청에 대해 401 또는 403 오류를 throw 하 고, 유효한 요청이 표시 될 때 데이터를 반환 해야 합니다.
   > 그러나 여전히 IP 보안이 없습니다. 유효한 키가 있는 경우 어디에서 나 호출할 수 있습니다. 모든 요청을 API Management를 통해 적용 하는 것이 좋습니다.
   > 또한 API Management 소비 계층을 사용 하는 경우 해당 계층에 대 한 전용 고정 IP가 없기 때문에 VIP를 사용 하 여이 잠금을 수행할 수 없습니다. 공유 비밀 기능 키를 통해 API 호출을 잠그는 방법을 사용 해야 합니다. 따라서 11-14 단계를 수행할 수 없습니다.

1. ' 인증/권한 부여 ' 블레이드를 닫습니다. 
1. ' 네트워킹 '을 선택한 후 ' 액세스 제한 '을 선택 합니다.
1. 그런 다음 API Management 인스턴스 VIP에 대해 허용 되는 함수 앱 Ip를 잠급니다. 이 VIP는 포털의 API management-개요 섹션에 표시 됩니다.
1. 계속 해 서 함수 포털과 상호 작용 하 고 아래에서 선택적 단계를 수행 하려는 경우 사용자 고유의 공용 IP 주소 또는 CIDR 범위만 추가 해야 합니다.
1. 목록에 허용 항목이 있으면 Azure는 다른 모든 주소를 차단 하는 암시적인 거부 규칙을 추가 합니다. 

IP 제한 패널에 CIDR 형식 주소 블록을 추가 해야 합니다. API Management VIP와 같은 단일 주소를 추가 해야 하는 경우에는 xx. xx. xx/32 형식으로 추가 해야 합니다.

   > [!NOTE]
   > 이제 API management 나 사용자의 주소를 제외한 모든 위치에서 함수 API를 호출할 수 없습니다.

## <a name="import-the-function-app-definition"></a>함수 앱 정의 가져오기
1. API Management 포털 블레이드를 열고 API Management 인스턴스를 선택 합니다.
1. 인스턴스의 API Management 섹션에서 Api 블레이드를 선택 합니다.
1. ' 새 API 추가 ' 창에서 ' 함수 앱 '를 선택한 다음 팝업 위쪽에서 ' 전체 '를 선택 합니다.
1. 찾아보기를 클릭 하 고 내에서 API를 호스트 하는 함수 앱을 선택한 다음 선택을 클릭 합니다.
1. API Management의 내부 사용에 대 한 이름 및 설명을 API에 제공 하 고 ' 제한 없음 ' 제품에 추가 합니다.
1. 나중에 사용할 수 있도록 기준 URL을 기록한 다음 만들기를 클릭 합니다.

## <a name="configure-oauth2-for-api-management"></a>API Management에 대 한 Oauth2 구성
1. 구독에서 항목을 다시 구성 하 고 *API Management 블레이드*를 연 후 *인스턴스*를 열 수 있도록 AZURE PORTAL에서 표준 Azure AD 테 넌 트로 다시 전환 합니다.
1. 그런 다음 보안 탭에서 Oauth 2.0 블레이드를 선택 하 고 ' 추가 '를 클릭 합니다.
1. 추가 된 Oauth 끝점에 대 한 *표시 이름* 및 *설명* 값을 지정 합니다. 이러한 값은 다음 단계에서 Oauth2 끝점으로 표시 됩니다.
1. 이 값은 사용 되지 않으므로 클라이언트 등록 페이지 URL에 값을 입력할 수 있습니다.
1. *암시적 인증* 권한 유형을 확인 하 고 필요에 따라 인증 코드 권한 유형을 선택 취소 합니다.
1. *권한 부여* 및 *토큰* 끝점 필드로 이동 하 고 앞의 잘 알려진 구성 xml 문서에서 캡처한 값을 입력 합니다.
1. 아래로 스크롤하여 ' resource ' 라는 *추가 본문 매개 변수* 를 Azure AD B2C 앱 등록의 함수 API 클라이언트 ID로 채웁니다.
1. ' 클라이언트 자격 증명 '을 선택 하 고, 클라이언트 ID를 개발자 콘솔 앱의 앱 ID로 설정 합니다. 소비 API Management 모델을 사용 하는 경우이 단계를 건너뜁니다.
1. 클라이언트 암호를 앞에서 기록한 키로 설정 합니다. 소비 API Management 모델을 사용 하는 경우이 단계를 건너뜁니다.
1. 마지막으로, 나중에 사용할 API Management에서 인증 코드의 redirect_uri를 기록 합니다.

## <a name="set-up-oauth2-for-your-api"></a>API에 대 한 Oauth2 설정
1. API는 포털의 왼쪽에 있는 ' 모든 Api ' 섹션 아래에 표시 되며 API를 클릭 하 여 엽니다.
1. ' 설정 ' 탭을 선택 합니다.
1. 사용자 권한 부여 라디오 단추에서 "Oauth 2.0"을 선택 하 여 설정을 업데이트 합니다.
1. 이전에 정의한 Oauth 서버를 선택 합니다.
1. ' 범위 재정의 ' 확인란을 선택 하 고 이전에 백 엔드 API 호출에 대해 기록한 범위를 입력 합니다.

   > [!NOTE]
   > 이제 Azure AD B2C에서 액세스 토큰을 가져와서 요청에 권한을 부여 하 고 Oauth2 Azure Active Directory B2C 구성을 이해 하는 방법을 알고 있는 API Management 인스턴스가 있습니다.

## <a name="set-up-the-cors-and-validate-jwt-policies"></a>**CORS** 설정 및 jwt 정책 **유효성 검사**

> 사용 중인 APIM 계층에 관계 없이 다음 섹션을 따라야 합니다. 

1. 디자인 탭으로 다시 전환 하 고 "모든 Api"를 선택한 다음 코드 보기 단추를 클릭 하 여 정책 편집기를 표시 합니다.
1. 인바운드 섹션을 편집 하 고 다음과 같이 읽도록 xml을 붙여넣습니다.

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
1. 등록 또는 로그인 정책에 대해 잘 알려진 Azure AD B2C 끝점과 일치 하도록 openid connect url을 편집 합니다.
1. 백 엔드 API 응용 프로그램에 대 한 클라이언트 ID 라고도 하는 유효한 응용 프로그램 ID와 일치 하도록 클레임 값을 편집 하 고 저장 합니다.
1. "모든 Api" 아래에서 api 작업을 선택 합니다.

   > [!NOTE]
   > 이제 API management는 JS SPA 앱에 대 한 원본 간 요청에 응답할 수 있으며, 요청을 함수 API로 전달 하기 전에 전달 되는 JWT 인증 토큰의 제한, 요율 제한 및 사전 유효성 검사를 수행 합니다.

   > [!NOTE]
   > 다음 섹션은 선택 사항이 며 개발자 포털을 지원 하지 않는 **소비** 계층에는 적용 되지 않습니다.
   > 개발자 포털을 사용 하지 않으려는 경우 또는 소비 계층을 사용 하 고 있으므로 사용할 수 없는 경우이 단계를 건너뛰고 바로 ["API를 사용 하는 JAVASCRIPT SPA 빌드"](##build-the-javascript-spa-to-consume-the-api)로 이동 하세요.

## <a name="optional-configure-the-developer-portal"></a>필드 개발자 포털 구성

1. Azure AD B2C 블레이드를 열고 개발자 포털에 대 한 응용 프로그램 등록으로 이동 합니다.
1. 이전 API Management에서 인증 코드 권한 부여의 redirect_uri를 구성할 때 기록한 항목에 ' 회신 URL ' 항목을 설정 합니다.

   `Echo API`에서 OAuth 2.0 사용자 권한 부여를 사용 하도록 설정 했으므로 개발자 콘솔은 API를 호출 하기 전에 사용자에 대 한 액세스 토큰을 가져옵니다.

1. 개발자 포털의 `Echo API`에 있는 작업으로 이동 하 고 **체험** 을 선택 하 여 개발자 콘솔로 이동 합니다.
1. 방금 추가한 권한 부여 서버에 해당하는 **권한 부여** 섹션의 새 항목을 참고합니다.
1. 권한 부여 드롭다운 목록에서 **권한 부여 코드** 를 선택 하면 Azure AD 테 넌 트에 로그인 하 라는 메시지가 표시 됩니다. 계정을 사용 하 여 이미 로그인 한 경우에는 메시지가 표시 되지 않을 수 있습니다.
1. 성공적으로 로그인 하면 `Authorization: Bearer` 헤더가 Base64로 인코딩된 Azure AD B2C의 액세스 토큰과 함께 요청에 추가 됩니다. 
1. **보내기** 를 선택 하면 API를 성공적으로 호출할 수 있습니다.

   > [!NOTE]
   > 이제 API management는 개발자 포털에 대 한 토큰을 획득 하 여 API를 테스트 하 고 해당 정의를 이해 하 고 개발 포털에서 적절 한 테스트 페이지를 렌더링할 수 있습니다.

1. API Management 포털의 개요 블레이드에서 ' 개발자 포털 '을 클릭 하 여 API의 관리자로 로그인 합니다.
1. 여기에서 사용자 및 API의 다른 선택 된 소비자는 콘솔에서 테스트 하 고 호출할 수 있습니다.
1. ' Products '를 선택 하 고 ' 제한 없음 '을 선택한 다음 이전에 만든 API를 선택 하 고 ' 사용해 보세요 '를 클릭 합니다.
1. API 구독 키를 숨기기 취소 하 고 나중에 필요한 요청 url과 함께 safe 어딘가에 복사 합니다.
1. 또한 oauth 인증 드롭다운에서 암시적을 선택 하 고 팝업을 사용 하 여 여기에서 인증 해야 할 수도 있습니다.
1. ' 보내기 '를 클릭 합니다. 모두 적절 한 경우 함수 앱는 200 OK 메시지 및 일부 JSON을 사용 하 여 API management를 통해 hello 메시지에 다시 응답 해야 합니다.

   > [!NOTE]
   > 축 하 합니다. 이제 Azure AD B2C API Management 하 고 함께 작업 하 여 API를 게시, 보호 및 사용할 Azure Functions 있습니다. 이 메서드를 사용 하 여 API가 실제로 두 번 보호 되는 것을 알 수 있습니다 .이 메서드는 API Management 된 Ocp 구독-키 헤더를 사용 하 고 한 번은 권한 부여: 전달자 JWT를 사용 하 여 한 번
   > 이 예제가 JavaScript 단일 페이지 응용 프로그램 이기 때문에 API Management 키만 사용 하 여 요금 제한 및 청구 호출에만 사용 합니다.
   > 실제 권한 부여 및 인증은 Azure AD B2C에 의해 처리 되 고 JWT에 캡슐화 됩니다. JWT는 API Management를 통해 한 번, Azure Functions 하 여 두 번의 유효성을 검사 합니다.

## <a name="build-the-javascript-spa-to-consume-the-api"></a>JavaScript SPA를 빌드하여 API를 사용 합니다.
1. Azure Portal에서 storage 계정 블레이드를 엽니다. 
1. 만든 계정을 선택 하 고 설정 섹션에서 ' 정적 웹 사이트 ' 블레이드를 선택 합니다. ' 정적 웹 사이트 ' 옵션이 표시 되지 않으면 V2 계정을 만들었는지 확인 합니다.
1. 정적 웹 호스팅 기능을 ' 사용 '으로 설정 하 고 인덱스 문서 이름을 ' c l o s t '로 설정한 후 ' 저장 '을 클릭 합니다.
1. 기본 끝점의 콘텐츠를 적어 둡니다 .이 위치는 프런트 엔드 사이트가 호스트 되는 위치입니다. 

   > [!NOTE]
   > Azure Blob Storage + CDN 재작성 또는 Azure App Service 중 하나를 사용할 수 있지만 Blob Storage의 정적 웹 사이트 호스팅 기능을 사용 하면 Azure Storage에서 정적 웹 콘텐츠/h t t p/d i s/css를 제공 하는 기본 컨테이너를 사용할 수 있으며, 작업에 대 한 기본 페이지를 유추할 수 있습니다.

## <a name="upload-the-js-spa-sample"></a>JS SPA 샘플 업로드
1. 저장소 계정 블레이드의 Blob Service 섹션에서 ' Blob ' 블레이드를 선택 하 고 오른쪽 창에 표시 되는 $web 컨테이너를 클릭 합니다.
1. 아래 코드를 파일의 로컬에 있는 파일에 인덱스 html로 저장 한 다음 파일 색인과 $web 컨테이너에 업로드 합니다.

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

1. 마지막 섹션에서 이전에 저장 한 정적 웹 사이트 기본 끝점으로 이동 합니다.

   > [!NOTE]
   > 축 하 합니다. api에 대 한 키를 사용 하 여 JS 앱을 구성 하지 않았거나 Azure AD B2C 세부 정보를 사용 하 여 JS 앱을 구성 하지 않았기 때문에 Azure Storage에 JavaScript 단일 페이지 앱을 배포 했습니다. 페이지를 연 경우에는 페이지가 아직 작동 하지 않습니다.

## <a name="configure-the-js-spa-for-azure-ad-b2c"></a>Azure AD B2C에 대 한 JS SPA 구성
1. 이제 모든 것이 무엇 인지 알 수 있습니다. 적절 한 API Management API 주소 및 올바른 Azure AD B2C 응용 프로그램/클라이언트 Id를 사용 하 여 SPA를 구성할 수 있습니다.
1. Azure Portal 저장소 블레이드로 돌아가서 html.actionlink를 클릭 한 다음 ' Blob 편집 '을 선택 합니다. 
1. 이전에 B2C에 등록 한 프런트 엔드 응용 프로그램과 일치 하도록 인증 세부 정보를 업데이트 합니다 .이는 ' b2cScopes ' 값이 API 백 엔드에 대 한 것입니다.
1. API 작업에 대 한 API Management 테스트 창에서 webApi 키 및 api url을 찾을 수 있습니다.
1. ' 구독 '을 선택 하 고 ' 구독 추가 '를 클릭 한 다음 레코드를 저장 하 여 API Management에 대 한 API Management 블레이드에 APIM 구독 키를 만듭니다. 만든 행 옆의 줄임표 (...)를 클릭 하면 기본 키를 복사할 수 있도록 키를 표시할 수 있습니다.
1. 아래 코드와 같이 표시 됩니다.  

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

## <a name="set-the-redirect-uris-for-the-azure-ad-b2c-frontend-app"></a>Azure AD B2C 프런트 엔드 앱에 대 한 리디렉션 Uri 설정
1. Azure AD B2C 블레이드를 열고 JavaScript 프런트 엔드 응용 프로그램에 대 한 응용 프로그램 등록으로 이동 합니다.
1. 위의 정적 웹 사이트 기본 끝점을 이전에 설정 했을 때 기록한 것으로 리디렉션 URL을 설정 합니다.

   > [!NOTE] 
   > 이 구성을 사용 하면 프런트 엔드 응용 프로그램 클라이언트가 Azure AD B2C의 적절 한 클레임으로 액세스 토큰을 받습니다.
   > SPA는 백엔드 API 호출에서이를 https 헤더에 전달자 토큰으로 추가할 수 있습니다.
   > API Management은 요청을 수신 하는 Azure Function API에 전달 하기 전에 토큰, 구독자 키를 기준으로 끝점에 대 한 요율 제한 호출의 유효성을 미리 검사 합니다.
   > SPA는 브라우저에서 응답을 렌더링 합니다.

   > *축 하 합니다. 완벽 한 조화에서 작동 하도록 Azure AD B2C, Azure API Management, Azure Functions, Azure App Service 권한 부여를 구성 했습니다.*

   > [!NOTE]
   > 이제 간단한 보안 API를 사용 하 여 간단한 앱을 만들어 테스트 해 보겠습니다.

## <a name="test-the-client-application"></a>클라이언트 응용 프로그램 테스트
1. 이전에 만든 저장소 계정에서 적어 둔 샘플 앱 URL을 엽니다.
1. 오른쪽 위 모서리에서 "로그인"을 클릭 하면 Azure AD B2C 등록 또는 로그인 프로필을 팝업 합니다.
1. 화면의 "로그인 한 사용자" 섹션에 대 한 사후 로그인이 JWT에서 채워집니다.
1. 이제 "Web Api 호출"을 클릭 하면 보안 API에서 다시 보낸 값으로 페이지를 업데이트 해야 합니다.

## <a name="and-were-done"></a>완료 되었습니다.
위의 단계를 조정 하 고 편집 하 여 API Management와 Azure AD B2C를 다양 하 게 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계
* [Azure Active Directory 및 OAuth2.0](../active-directory/develop/authentication-scenarios.md)에 대해 자세히 알아봅니다.
* API Management에 대한 추가 [비디오](https://azure.microsoft.com/documentation/videos/index/?services=api-management) 를 확인합니다.
* 백 엔드 서비스를 보호하는 다른 방법은 [상호 인증서 인증](api-management-howto-mutual-certificates.md)을 참조하세요.
* Azure AD Graph API를 사용 하 여 사용자 지정 클레임을 할당 하 고 API Management 정책을 사용 하 여 토큰에 있는지 확인 하는 것이 좋습니다.

* [API Management 서비스 인스턴스 만들기](get-started-create-service-instance.md)

* [첫 번째 API 관리](import-and-publish.md)
