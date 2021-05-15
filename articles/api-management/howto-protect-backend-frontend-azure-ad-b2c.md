---
title: Active Directory B2C를 사용하여 Azure API Management에서 SPA 백 엔드 보호
description: PKCE 사용 SPA 인증 흐름을 사용하여 JavaScript SPA에서 호출될 Azure Active Directory B2C, Azure API Management 및 Easy Auth를 사용하여 OAuth 2.0로 API를 보호합니다.
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
ms.date: 02/18/2021
ms.author: wieastbu
ms.custom: fasttrack-new, fasttrack-update, devx-track-js
ms.openlocfilehash: baa6a0a6995e206924d14de25b98700e450f3a0c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104954924"
---
# <a name="protect-spa-backend-with-oauth-20-azure-active-directory-b2c-and-azure-api-management"></a>OAuth 2.0, Azure Active Directory B2C 및 Azure API Management를 사용하여 SPA 백 엔드 보호

이 시나리오는 Azure API Management 인스턴스를 구성하여 API를 보호하는 방법을 보여줍니다.
EasyAuth를 사용하여 Azure Functions 백 엔드를 보호하는 API Management와 함께 Azure AD B2C SPA(Auth Code + PKCE) flow를 사용하여 토큰을 가져옵니다.

## <a name="aims"></a>목표

Azure Functions 및 Azure AD B2C를 사용하여 간소화된 시나리오에서 API Management를 사용하는 방법을 살펴보겠습니다. Azure AD B2C를 사용하여 사용자에게 로그인하는 API를 호출하는 JavaScript(JS) 앱을 만듭니다. 그런 다음 키 정책 기능을 통해 API Management의 validate-jwt, CORS 및 속도 제한을 사용하여 백 엔드 API를 보호합니다.

심층 방어를 위해 EasyAuth를 사용하여 백 엔드 API 내에서 토큰의 유효성을 다시 검사하고 API Management가 Azure Functions 백 엔드를 호출할 수 있는 유일한 서비스인지 확인합니다.

## <a name="what-will-you-learn"></a>학습할 내용

> [!div class="checklist"]
> * Azure Active Directory B2C의 단일 페이지 앱 및 백 엔드 API 설정
> * Azure Functions 백 엔드 API 만들기
> * Azure API Management에 Azure Functions API 가져오기
> * Azure API Management의 API 보호
> * MSAL.js(Microsoft ID 플랫폼 라이브러리)를 통해 Azure Active Directory B2C 권한 부여 엔드포인트 호출
> * HTML/Vanilla JS 단일 페이지 애플리케이션을 저장하고 Azure Blob Storage 엔드포인트에서 서비스 제공

## <a name="prerequisites"></a>필수 구성 요소

이 문서의 단계를 따르려면 다음이 있어야 합니다.

* 프런트 엔드 JS 단일 페이지 앱을 호스트하는 Azure(StorageV2) 범용 V2 스토리지 계정.
* Azure API Management 인스턴스('소비'를 비롯한 모든 계층은 작동하지만, 전체 시나리오에 적용되는 특정 기능을 이 계층에서 사용할 수 없는 경우(rate-limit-by-key 및 전용 가상 IP) 이러한 제한은 해당하는 문서에서 아래에 설명되어 있습니다).
* 호출된 API를 호스트하는 빈 Azure 함수 앱(사용 플랜에서 V3.1.NET Core 런타임 실행)
* 구독에 연결된 Azure AD B2C 테넌트입니다.

실제로 프로덕션 워크로드에서 동일한 지역의 리소스를 사용하는 경우에도 이 방법 문서에서는 배포 지역이 중요하지 않습니다.

## <a name="overview"></a>개요

다음은 사용중인 구성 요소와 이 프로세스가 완료된 후 이러한 구성 요소 간의 흐름을 보여 주는 그림입니다.
![사용 중인 구성 요소 및 흐름](../api-management/media/howto-protect-backend-frontend-azure-ad-b2c/image-arch.png "사용 중인 구성 요소 및 흐름")

다음은 단계에 대한 간략한 개요입니다.

1. 범위를 포함하는 Azure AD B2C 호출(프런트 엔드, API Management) 및 API 애플리케이션을 만들고 API 액세스 부여
1. 사용자가 Azure AD B2C로 로그인할 수 있도록 등록 및 로그인 정책 만들기 
1. 개발자 콘솔에서 OAuth2사용자 권한 부여를 사용하도록 새 Azure AD B2C 클라이언트 ID 및 키를 사용하여 API Management 구성
1. 함수 API 빌드
1. 새 Azure AD B2C 클라이언트 ID 및 키로 EasyAuth를 사용하도록 설정하고 APIM VIP로 잠금을 해제하도록 함수 API 구성
1. API Management에서 API 정의 빌드
1. API Management API 구성에 대해 Oauth2 설정
1. **CORS** 정책을 설정하고 **validate-jwt** 정책을 추가하여 들어오는 요청마다 OAuth 토큰의 유효성 검사
1. API를 사용하는 호출 애플리케이션 빌드
1. JS SPA 샘플 업로드
1. 새 Azure AD B2C 클라이언트 ID 및 키를 사용하여 샘플 JS 클라이언트 앱 구성 
1. 클라이언트 애플리케이션 테스트

   > [!TIP]
   > 이 문서를 살펴볼 때 많은 정보와 키 등을 캡처합니다. 다음 구성 항목을 일시적으로 저장하기 위해 텍스트 편집기를 여는 것이 편리할 수 있습니다.  
   >
   > B2C 백 엔드 클라이언트 ID:  
   > B2C 백 엔드 클라이언트 암호 키:  
   > B2C 백 엔드 API 범위 URI:  
   > B2C 프런트 엔드 클라이언트 ID:  
   > B2C 사용자 흐름 엔드포인트 URI:  
   > B2C 잘 알려진 OPENID CONNECT 엔드포인트:   
   > B2C 정책 이름: Frontendapp_signupandsignin 함수 URL:  
   > APIM API 기준 URL: 스토리지 기본 엔드포인트 URL:  

## <a name="configure-the-backend-application"></a>백 엔드 애플리케이션 구성

포털에서 Azure AD B2C 블레이드를 열고 다음 단계를 수행합니다.

1. **앱 등록** 탭 선택
1. '새 등록' 단추를 클릭합니다.
1. 리디렉션 URI 선택 상자에서 '웹'을 선택합니다.
1. 이제 표시 이름을 설정하고, 생성되는 서비스와 관련된 고유한 항목을 선택합니다. 이 예제에서는 "Backend Application"이라는 이름을 사용합니다.
1. 회신 URL에 'https://jwt.ms (Microsoft 소유의 토큰 디코딩 사이트)'와 같은 자리 표시자를 사용합니다. 이 URL은 나중에 업데이트합니다.
1. 모든 ID 공급자 또는 조직 디렉터리의 계정(사용자 흐름에서 사용자를 인증하는 용도) 옵션을 선택했는지 확인합니다.
1. 이 샘플의 경우 지금은 offline_access 권한이 필요하지 않으므로 "관리자 동의 허용" 확인란을 선택 취소합니다.
1. '등록'을 클릭합니다.
1. 나중에 사용하기 위해 백 엔드 애플리케이션 클라이언트 ID를 기록합니다('애플리케이션 (클라이언트) ID' 아래에 표시됨).
1. *인증서 및 비밀* 탭(관리 아래)을 선택하고 '새 클라이언트 암호'를 클릭하여 인증 키를 생성합니다(기본 설정을 적용하고 '추가' 클릭).
1. '추가'를 클릭하고 나중에 '백 엔드 클라이언트 암호'로 사용하기 위해 안전하게 '값' 아래의 키를 복사합니다. 이 대화 상자는 이 키를 복사해야 하는 유일한 기회입니다.
1. 이제 *API 노출* 탭(관리 아래)을 선택합니다.
1. AppID URI를 설정하라는 메시지가 표시되면 기본값을 선택하고 기록합니다.
1. 함수 API에 대한 "Hello" 범위 만들기 및 이름 지정할 수 있는 모든 옵션에 대해 'Hello' 구문을 사용하여 채워진 전체 범위 값 URI를 기록한 다음 '범위 추가'를 클릭할 수 있습니다.
1. 포털의 왼쪽 위에 있는 'Azure AD B2C' 이동 경로를 선택하여 Azure AD B2C 블레이드의 루트로 돌아갑니다.

   > [!NOTE]
   > Azure AD B2C 범위는 애플리케이션에서 API 액세스 블레이드를 통해 다른 애플리케이션에 대한 액세스를 요청할 수 있는 API 내에서 효과적으로 사용되는 권한으로, 호출된 API에 대한 애플리케이션 권한을 효과적으로 만들었습니다.

## <a name="configure-the-frontend-application"></a>프런트 엔드 애플리케이션 구성

1. **앱 등록** 탭 선택
1. '새 등록' 단추를 클릭합니다.
1. 리디렉션 URI 선택상자에서 'SPA(단일 페이지 애플리케이션)'를 선택합니다.
1. 이제 표시 이름 및 AppID URI를 설정하고, 이 AAD B2C 앱 등록을 사용하는 프런트 엔드 애플리케이션과 관련된 고유한 항목을 선택합니다. 이 예제에서는 "프런트 엔드 애플리케이션"을 사용할 수 있습니다.
1. 첫 번째 앱 등록에 따라 지원되는 계정 유형 선택 항목을 기본값으로 둡니다(사용자 흐름으로 사용자 인증).
1. 회신 URL에 'https://jwt.ms (Microsoft 소유의 토큰 디코딩 사이트)'와 같은 자리 표시자를 사용합니다. 이 URL은 나중에 업데이트합니다.
1. 관리자 동의 허용 상자를 그대로 둠
1. '등록'을 클릭합니다.
1. 나중에 사용할 프런트 엔드 애플리케이션 클라이언트 ID를 기록합니다('애플리케이션(클라이언트) ID' 아래에 표시됨).
1. *API 권한* 탭으로 전환합니다.
1. '권한 추가', '내 API', '백 엔드 애플리케이션', '권한'을 차례로 선택하고, 이전 섹션에서 만든 범위를 선택하고, '권한 추가'를 클릭하여 백 엔드 애플리케이션에 대한 액세스 권한을 부여합니다.
1. {Tenant}에 대해 '관리자 동의 허용'을 클릭하고 팝업 대화 상자에서 '예'를 클릭합니다. 이 팝업을 사용하면 앞에서 만든 "백 엔드 애플리케이션"에 정의된 권한 "hello"를 사용하는 "프런트 엔드 애플리케이션"에 동의합니다.
1. 이제 앱의 모든 권한이 상태 열에서 녹색 체크 표시로 표시됩니다.

## <a name="create-a-sign-up-and-sign-in-user-flow"></a>"가입 및 로그인" 사용자 흐름 만들기

1. Azure AD B2C 이동 경로를 선택하여 B2C 블레이드의 루트로 돌아갑니다.
1. '사용자 흐름(정책 아래의)'으로 전환합니다.
1. "새 사용자 흐름" 클릭
1. '등록 및 로그인' 사용자 흐름 유형을 선택하고 '권장'과 '만들기'를 차례로 선택
1. 정책에 이름을 지정하고 나중에 사용할 수 있도록 기록합니다. 이 예에서는 "Frontendapp_signupandsignin"을 사용할 수 있습니다. "B2C_1_" 접두사를 추가하여 "B2C_1_Frontendapp_signupandsignin"로 만듭니다.
1. 'ID 공급자' 및 "로컬 계정"에서 '이메일 등록(또는 B2C 테넌트의 구성에 따라 '사용자 ID 등록')'을 선택하고 확인을 클릭합니다. 이 구성을 사용하는 이유는 사용자의 기존 소셜 미디어 계정을 사용하기 위해 다른 ID 공급자(예: 소셜 ID 공급자)로 지연되지 않고 로컬 B2C 계정을 등록할 수 있기 때문입니다.
1. MFA 및 조건부 액세스 설정은 기본값으로 둡니다.
1. '사용자 특성 및 클레임'에서 '자세히 표시...'를 클릭합니다. 그런 다음 사용자가 입력하고 토큰에서 반환하려는 클레임 옵션을 선택합니다. 반환할 '표시 이름' 및 '이메일 주소'를 선택하고, '표시 이름' 및 '이메일 주소'를 사용하여(emailaddress 단수형을 수집하고 이메일 주소를 반환하도록 요청하는 사실에 주의해야 합니다.) '확인'을 클릭한 다음 '만들기'를 클릭합니다.
1. 목록에서 만든 사용자 흐름을 클릭한 다음 '사용자 흐름 실행' 단추를 클릭합니다.
1. 이 작업을 사용하면 사용자 흐름 실행 블레이드를 열고, 프런트 엔드 애플리케이션을 선택하고, 사용자 흐름 엔드포인트를 복사하여 나중에 사용할 수 있도록 저장합니다.
1. 맨 위에 있는 링크를 복사하고 나중에 사용하기 위해 '잘 알려진 OpenID Connect 구성 엔드포인트'로 기록하여 저장합니다.

   > [!NOTE]
   > B2C 정책을 사용하면 다양한 방식으로 다양한 데이터 구성 요소 및 로그인 사용자를 캡처할 수 있도록 Azure AD B2C 로그인 엔드포인트를 노출할 수 있습니다.
   > 
   > 여기에서는 등록 또는 로그인 흐름(정책)을 구성했습니다. 이 경우 잘 알려진 구성 엔드포인트도 노출합니다. 두 경우 모두 생성된 정책은 URL에서 "p =" 쿼리 문자열 매개 변수로 식별되었습니다.  
   >
   > 이 작업이 완료되면 이제 여러 애플리케이션에 사용자를 로그인하는 기능을 갖춘 소비자 ID 플랫폼을 사용할 수 있습니다.

## <a name="build-the-function-api"></a>함수 API 빌드

1. 구독에서 항목을 다시 구성할 수 있도록 Azure Portal에서 표준 Azure AD 테넌트로 다시 전환합니다.
1. Azure Portal의 함수 앱 블레이드로 이동하여 빈 함수 앱을 연 다음 '함수'를 클릭하고 '추가'를 클릭합니다.
1. 표시되는 플라이 아웃에서 '포털에서 개발'을 선택한 다음 '템플릿 선택' 아래에서 'HTTP 트리거'를 선택하고 템플릿 세부 정보 아래에서 이름은 'hello'로, 권한 부여 수준은 '함수'로 지정한 다음 추가를 선택합니다.
1. 코드 + 테스트 블레이드로 전환하고 표시되는 *기존 코드 위에* 아래의 샘플 코드를 복사하여 붙여넣습니다.
1. [저장]을 선택합니다.

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

   > [!TIP]
   > 방금 붙여넣은 C# 스크립트 함수 코드는 단순히 함수 로그에 줄을 로그하고 일부 동적 데이터(날짜 및 시간)를 사용하여 "Hello World" 텍스트를 반환합니다.

1. 왼쪽 블레이드에서 "통합"을 선택하고 '트리거' 상자 내에서 http(필수) 링크를 클릭합니다.
1. '선택한 HTTP 메서드' 드롭다운에서 http POST 메서드를 선택 취소하고, GET 선택 항목만 남기고 저장을 클릭합니다.
1. 코드 + 테스트 탭으로 다시 전환하고 '함수 URL 가져오기'를 클릭한 다음 표시되는 URL을 복사하여 나중에 저장합니다.

   > [!NOTE]
   > 방금 만든 바인딩은 방금 복사한 URL(`https://yourfunctionappname.azurewebsites.net/api/hello?code=secretkey`)에 대한 익명 http GET 요청에 응답하도록 함수에 지시합니다. 이제 매우 간단한 페이로드를 반환할 수 있는 확장되지 않은 서버를 사용하는 https API가 생겼습니다.
   >
   > 이제 방금 복사하고 저장한 위의 URL 버전을 사용하여 웹 브라우저에서 이 API 호출을 테스트할 수 있습니다. 또한 URL의 쿼리 문자열 매개 변수 "?code=secretkey" 부분을 제거하고 다시 테스트하여 Azure Functions에서 401오류를 반환하는지 증명할 수도 있습니다.

## <a name="configure-and-secure-the-function-api"></a>함수 API 구성 및 보안

1. 함수 앱의 두 추가 영역을 구성해야 합니다(권한 부여 및 네트워크 제한).
1. 먼저 이동 경로를 통해 함수 앱의 루트 블레이드로 다시 이동할 수 있도록 인증/권한 부여를 구성합니다.
1. 그런 다음 '설정' 아래에서 '인증/권한 부여'를 선택합니다.
1. App Service 인증 기능을 설정합니다.
1. 요청이 인증되지 않았을 때 수행할 작업 드롭다운을 "Azure Active Directory로 로그인"으로 설정합니다.
1. '인증 공급자' 아래에서 'Azure Active Directory'를 선택합니다.
1. 관리 모드 스위치에서 '고급'을 선택합니다.
1. 백 엔드 애플리케이션의 [Application] 클라이언트 ID(Azure AD B2C에서)를 '클라이언트 ID' 상자에 붙여넣습니다.
1. 등록 및 로그인 정책에서 잘 알려진 오픈 ID 구성 엔드포인트를 발급자 URL 상자에 붙여넣습니다(앞서 이 구성을 기록했습니다).
1. '암호 표시'를 클릭하고 백 엔드 애플리케이션의 클라이언트 암호를 해당 상자에 붙여넣습니다.
1. 확인을 선택하여 ID 공급자 선택 블레이드/화면으로 돌아갑니다.
1. 고급 설정에서 [토큰 저장소](../app-service/overview-authentication-authorization.md#token-store)를 사용하도록 설정된 상태로 둡니다(기본값).
1. '저장(블레이드의 왼쪽 위)'을 클릭합니다.

   > [!IMPORTANT]
   > 이제 함수 API가 배포되고, 권한 부여: 전달자 헤더로 수정된 JWT가 제공되지 않는 경우 401 응답을 throw하고, 유효한 요청이 제시되면 데이터를 반환합니다.  
   > 인증되지 않은 요청을 처리하기 위해 'Azure AD를 사용하여 로그인' 옵션을 구성하여 EasyAuth에서 심층 방어 보안을 추가했습니다. EasyAuth는 백 엔드 함수 앱과 프런트 엔드 SPA 간에 인증되지 않은 요청 동작을 변경합니다. 이는 401 권한이 부여되지 않은 응답 대신 AAD에 302리디렉션을 실행하므로 나중에 API Management를 사용하여 해결할 수 있습니다.  
   >
   > 아직 IP 보안이 적용되지 않았습니다. 유효한 키 및 OAuth2토큰이 있는 경우 누구나 이를 호출할 수 있습니다. 모든 요청을 API Management를 통해 적용하는 것이 좋습니다.  
   > 
   > APIM Consumption 계층을 사용하는 경우, 함수 액세스 제한에 대한 [전용 Azure API Management 가상 IP가](./api-management-howto-ip-addresses.md#ip-addresses-of-consumption-tier-api-management-service) 허용 목록에 없습니다. Azure API Management Standard SKU 이상에서 VIP는 [리소스의 수명 동안 지속되는 단일 테넌트](./api-management-howto-ip-addresses.md#changes-to-the-ip-addresses)입니다. Azure API Management Consumption 계층의 경우 위에서 복사한 URI의 일부에 있는 공유 비밀 기능 키를 통해 API 호출을 잠글 수 있습니다. 또한 Consumption 계층에 대해 아래의 12-17단계는 적용되지 않습니다.

1. '인증/권한 부여' 블레이드를 닫습니다. 
1. *포털의 API Management 블레이드* 를 연 후 *인스턴스* 를 엽니다.
1. 개요 탭에 표시된 개인 VIP를 기록합니다.
1. *포털의 Azure Functions 블레이드* 로 돌아간 후 *인스턴스* 를 다시 엽니다.
1. '네트워킹'을 선택한 후 '액세스 제한 구성'을 선택합니다.
1. '규칙 추가'를 클릭하고 위의 3단계에서 복사한 VIP를 xx.xx.xx.xx/32형식으로 입력합니다.
1. 계속해서 함수 포털과 상호 작용하고 아래에서 선택적 단계를 수행하려는 경우, 여기에도 사용자 고유의 공용 IP 주소 또는 CIDR 범위를 추가해야 합니다.
1. 목록에 허용 항목이 있는 경우 Azure는 다른 모든 주소를 차단하는 암시적 거부 규칙을 추가합니다.

IP 제한 패널에 CIDR 형식 주소 블록을 추가해야 합니다. API Management VIP와 같이 단일 주소를 추가해야 하는 경우에는 xx.xx.xx.xx/32형식으로 추가해야 합니다.

   > [!NOTE]
   > 이제 API Management나 사용자의 주소를 제외한 모든 위치에서 함수 API를 호출할 수 없습니다.

1. *API Management 블레이드* 를 연 후 *인스턴스* 를 엽니다.
1. API 블레이드(API 아래)를 선택합니다.
1. '새 API 추가' 창에서 '함수 앱'을 선택한 다음 팝업 위쪽에서 '전체'를 선택합니다.
1. 찾아보기를 클릭하고 안에서 API를 호스트하는 함수 앱을 선택한 다음 선택을 클릭합니다. 다음으로, 다시 선택을 클릭합니다.
1. API Management의 내부 사용에 대한 이름 및 설명을 API에 제공하고 '제한 없음' 제품에 추가합니다.
1. API의 '기본 URL'을 복사하고, 기록하고, '만들기'를 클릭합니다.
1. 이 경우 Oauth JWT 토큰을 사용하여 속도를 제한할 것이기 때문에 '설정' 탭을 클릭한 다음 구독 아래에서 '구독 필요' 확인란을 선택 취소합니다. Consumption 계층을 사용하는 경우에도 프로덕션 환경에서 필요합니다. 

   > [!TIP]
   > APIM의 Consumption 계층을 사용하는 경우 무제한 제품을 기본 제공으로 사용할 수 없습니다. 대신 "API"의 "제품"으로 이동하고 "추가"를 누릅니다.  
   > 제품 이름 및 설명으로 "제한 없음"을 입력하고 화면 왼쪽 아래에 있는 "+" API 설명선에서 방금 추가한 API를 선택합니다. "게시 됨" 확인란을 선택합니다. 나머지는 기본값으로 둡니다. 마지막으로 "만들기" 단추를 누릅니다. 이렇게 하면 "제한 없음" 제품이 생성되어 API에 할당됩니다. 나중에 새 제품을 사용자 지정할 수 있습니다.

## <a name="configure-and-capture-the-correct-storage-endpoint-settings"></a>올바른 스토리지 엔드포인트 설정 구성 및 캡처

1. Azure Portal에서 스토리지 계정 블레이드를 엽니다. 
1. 만든 계정을 선택하고 설정 섹션에서 '정적 웹 사이트' 블레이드를 선택합니다. '정적 웹 사이트' 옵션이 표시되지 않으면 V2계정을 만들었는지 확인합니다.
1. 정적 웹 호스팅 기능을 '사용'으로 설정하고 인덱스 문서 이름을 'index.html'로 설정한 후 '저장'을 클릭합니다.
1. 이 위치는 프런트 엔드 사이트가 호스팅될 위치이므로 나중에 사용할 수 있도록 '기본 엔드포인트'의 내용을 적어둡니다.

   > [!TIP]
   > Azure Blob Storage + CDN 재작성 또는 Azure App Service를 사용하여 SPA를 호스트할 수 있지만 Blob Storage의 정적 웹 사이트 호스팅 기능을 사용하면 Azure Storage에서 정적 웹 콘텐츠/html/js/css를 제공하는 기본 컨테이너를 제공하고, 작업할 필요 없이 기본 페이지를 유추할 수 있습니다.

## <a name="set-up-the-cors-and-validate-jwt-policies"></a>**CORS** 및 **validate-jwt** 정책 설정

> 사용 중인 APIM 계층과 관계없이 다음 섹션을 따라야 합니다. 스토리지 계정 URL은 이 문서의 맨 위에 있는 사전 요구 사항에서 사용할 수 있도록 스토리지 계정에서 제공됩니다.
1. 포털의 API Management 블레이드로 전환하고 인스턴스를 엽니다.
1. API를 선택한 다음 "모든 API"를 선택합니다.
1. "인바운드 처리" 아래에서 코드 보기 단추 "</>"를 클릭하여 정책 편집기를 표시합니다.
1. 인바운드 섹션을 편집하고 다음과 같이 읽도록 xml을 붙여넣습니다.
1. 정책에서 다음 매개 변수 교체
1. {PrimaryStorageEndpoint}(이전 섹션에서 복사한 '기본 스토리지 엔드포인트'), {b2cpolicy-well-known-openid}(앞에서 복사한 '잘 알려진 OpenID Connect 구성 엔드포인트'), 앞서 저장한 정확한 값을 사용하는 {backend-api-application-client-id}(**백 엔드 API** 에 대한 B2C 애플리케이션/클라이언트 ID).
1. Azure API Management의 Consumption 계층을 사용하는 경우 이 정책을 사용할 수 없으므로 rate-limit-by-key 정책을 모두 제거해야 합니다.

   ```xml
   <inbound>
      <cors allow-credentials="true">
            <allowed-origins>
                <origin>{PrimaryStorageEndpoint}</origin>
            </allowed-origins>
            <allowed-methods preflight-result-max-age="120">
                <method>GET</method>
            </allowed-methods>
            <allowed-headers>
                <header>*</header>
            </allowed-headers>
            <expose-headers>
                <header>*</header>
            </expose-headers>
        </cors>
      <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid." require-expiration-time="true" require-signed-tokens="true" clock-skew="300">
         <openid-config url="{b2cpolicy-well-known-openid}" />
         <required-claims>
            <claim name="aud">
               <value>{backend-api-application-client-id}</value>
            </claim>
         </required-claims>
      </validate-jwt>
      <rate-limit-by-key calls="300" renewal-period="120" counter-key="@(context.Request.IpAddress)" />
      <rate-limit-by-key calls="15" renewal-period="60" counter-key="@(context.Request.Headers.GetValueOrDefault("Authorization","").AsJwt()?.Subject)" />
   </inbound>
   ```

   > [!NOTE]
   > 이제 Azure API Management는 JavaScript SPA 앱의 원본 간 요청에 응답할 수 있으며, 요청을 함수 API로 전달하기 전에 전달되는 JWT 인증 토큰의 제한, 속도 제한 및 사전 유효성 검사를 수행합니다.
   > 
   > 축하합니다. 이제 Azure AD B2C, API Management, Azure Functions이 게시, 보호 및 API를 사용할 수 있게 되었습니다.

   > [!TIP]
   > API Management Consumption 계층을 사용하는 경우 JWT 주체 또는 들어오는 IP 주소(현재 "소비" 계층의 키 정책에 대한 호출율 제한)에 의한 속도 제한 대신 통화 요금 할당량으로 제한할 수 있습니다. [여기](./api-management-access-restriction-policies.md#LimitCallRate)를 참조하십시오.  
   > 이 예제는 JavaScript 단일 페이지 애플리케이션이기 때문에 API Management 키를 사용하여 속도 제한 및 청구 호출에만 사용합니다. 실제 권한 부여 및 인증은 Azure AD B2C에 의해 처리되고 JWT에 캡슐화되며, API Management에 의해 한 번, 그다음에 백 엔드 Azure 함수에서 유효성을 두 번 확인합니다.

## <a name="upload-the-javascript-spa-sample-to-static-storage"></a>정적 스토리지에 JavaScript SPA 샘플 업로드

1. 스토리지 계정 블레이드의 Blob Service 섹션에서 '컨테이너' 블레이드를 선택하고 오른쪽 창에 표시되는 $web 컨테이너를 클릭합니다.
1. 아래 코드를 컴퓨터에서 로컬로 파일에 index.html로 저장한 다음, index.html 파일을 $web 컨테이너에 업로드합니다.

   ```html
    <!doctype html>
    <html lang="en">
    <head>
         <meta charset="utf-8">
         <meta http-equiv="X-UA-Compatible" content="IE=edge">
         <meta name="viewport" content="width=device-width, initial-scale=1">
         <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.0.0-beta2/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-BmbxuPwQa2lc/FVzBcNJ7UAyJxM6wuqIj61tLrc4wSX0szH/Ev+nYRRuWlolflfl" crossorigin="anonymous">
         <script type="text/javascript" src="https://alcdn.msauth.net/browser/2.11.1/js/msal-browser.min.js"></script>
    </head>
    <body>
         <div class="container-fluid">
             <div class="row">
                 <div class="col-md-12">
                    <nav class="navbar navbar-expand-lg navbar-dark bg-dark">
                        <div class="container-fluid">
                            <a class="navbar-brand" href="#">Azure Active Directory B2C with Azure API Management</a>
                            <div class="navbar-nav">
                                <button class="btn btn-success" id="signinbtn"  onClick="login()">Sign In</a>
                            </div>
                        </div>
                    </nav>
                 </div>
             </div>
             <div class="row">
                 <div class="col-md-12">
                     <div class="card" >
                        <div id="cardheader" class="card-header">
                            <div class="card-text"id="message">Please sign in to continue</div>
                        </div>
                        <div class="card-body">
                            <button class="btn btn-warning" id="callapibtn" onClick="getAPIData()">Call API</a>
                            <div id="progress" class="spinner-border" role="status">
                                <span class="visually-hidden">Loading...</span>
                            </div>
                        </div>
                     </div>
                 </div>
             </div>
         </div>
         <script lang="javascript">
                // Just change the values in this config object ONLY.
                var config = {
                    msal: {
                        auth: {
                            clientId: "{CLIENTID}", // This is the client ID of your FRONTEND application that you registered with the SPA type in AAD B2C
                            authority:  "{YOURAUTHORITYB2C}", // Formatted as https://{b2ctenantname}.b2clogin.com/tfp/{b2ctenantguid or full tenant name including onmicrosoft.com}/{signuporinpolicyname}
                            redirectUri: "{StoragePrimaryEndpoint}", // The storage hosting address of the SPA, a web-enabled v2 storage account - recorded earlier as the Primary Endpoint.
                            knownAuthorities: ["{B2CTENANTDOMAIN}"] // {b2ctenantname}.b2clogin.com
                        },
                        cache: {
                            cacheLocation: "sessionStorage",
                            storeAuthStateInCookie: false 
                        }
                    },
                    api: {
                        scopes: ["{BACKENDAPISCOPE}"], // The scope that we request for the API from B2C, this should be the backend API scope, with the full URI.
                        backend: "{APIBASEURL}/hello" // The location that we will call for the backend api, this should be hosted in API Management, suffixed with the name of the API operation (in the sample this is '/hello').
                    }
                }
                document.getElementById("callapibtn").hidden = true;
                document.getElementById("progress").hidden = true;
                const myMSALObj = new msal.PublicClientApplication(config.msal);
                myMSALObj.handleRedirectPromise().then((tokenResponse) => {
                    if(tokenResponse !== null){
                        console.log(tokenResponse.account);
                        document.getElementById("message").innerHTML = "Welcome, " + tokenResponse.account.name;
                        document.getElementById("signinbtn").hidden = true;
                        document.getElementById("callapibtn").hidden = false;
                    }}).catch((error) => {console.log("Error Signing in:" + error);
                });
                function login() {
                    try {
                        myMSALObj.loginRedirect({scopes: config.api.scopes});
                    } catch (err) {console.log(err);}
                }
                function getAPIData() {
                    document.getElementById("progress").hidden = false; 
                    document.getElementById("message").innerHTML = "Calling backend ... "
                    document.getElementById("cardheader").classList.remove('bg-success','bg-warning','bg-danger');
                    myMSALObj.acquireTokenSilent({scopes: config.api.scopes, account: getAccount()}).then(tokenResponse => {
                        const headers = new Headers();
                        headers.append("Authorization", `Bearer ${tokenResponse.accessToken}`);
                        fetch(config.api.backend, {method: "GET", headers: headers})
                            .then(async (response)  => {
                                if (!response.ok)
                                {
                                    document.getElementById("message").innerHTML = "Error: " + response.status + " " + JSON.parse(await response.text()).message;
                                    document.getElementById("cardheader").classList.add('bg-warning');
                                }
                                else
                                {
                                    document.getElementById("cardheader").classList.add('bg-success');
                                    document.getElementById("message").innerHTML = await response.text();
                                }
                                }).catch(async (error) => {
                                    document.getElementById("cardheader").classList.add('bg-danger');
                                    document.getElementById("message").innerHTML = "Error: " + error;
                                });
                    }).catch(error => {console.log("Error Acquiring Token Silently: " + error);
                        return myMSALObj.acquireTokenRedirect({scopes: config.api.scopes, forceRefresh: false})
                    });
                    document.getElementById("progress").hidden = true;
             }
            function getAccount() {
                var accounts = myMSALObj.getAllAccounts();
                if (!accounts || accounts.length === 0) {
                    return null;
                } else {
                    return accounts[0];
                }
            }
        </script>
     </body>
    </html>
   ```

1. 마지막 섹션에서 이전에 저장한 정적 웹 사이트 기본 엔드포인트로 이동합니다.

   > [!NOTE]
   > 축하합니다. Azure Storage 정적 콘텐츠를 호스팅하는 JavaScript 단일 페이지 앱을 배포했습니다.  
   > 아직 Azure AD B2C 세부 정보를 사용하여 JS 앱을 구성하지 않았기 때문에 페이지를 열 때 페이지가 작동하지 않습니다.

## <a name="configure-the-javascript-spa-for-azure-ad-b2c"></a>Azure AD B2C에 대한 JavaScript SPA 구성

1. 이제 모든 것이 어디에 있는지 알 수 있습니다. 적절한 API Management API 주소 및 올바른 Azure AD B2C 애플리케이션/클라이언트 ID로 SPA를 구성할 수 있습니다.
1. Azure Portal 스토리지 블레이드로 돌아가기 
1. '컨테이너'('설정' 아래) 선택 
1. 목록에서 '$web' 컨테이너 선택
1. 목록에서 index.html Blob 선택 
1. '편집' 클릭 
1. 이전에 B2C에 등록한 *프런트 엔드* 애플리케이션과 일치하도록 msal 구성 섹션의 인증 값을 업데이트합니다. 구성 값을 확인하는 방법에 대한 힌트는 코드 주석을 사용합니다.
*권한* 값은 다음 형식이어야 합니다. - https://{b2ctenantname}.b2clogin.com/tfp/{b2ctenantname}.onmicrosoft.com}/{signupandsigninpolicyname}. 샘플 이름을 사용하고 B2C 테넌트가 'contoso'인 경우 권한이 'https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com}/Frontendapp_signupandsignin '일 수 있습니다.
1. 백 엔드 주소와 일치하도록 API 값을 설정합니다(앞에서 기록한 API 기반 URL 및 *백 엔드 애플리케이션* 에 대해 'b2cScopes' 값이 이전에 기록됨).
1. 저장을 클릭합니다.

## <a name="set-the-redirect-uris-for-the-azure-ad-b2c-frontend-app"></a>Azure AD B2C 프런트 엔드 앱에 대한 리디렉션 URI 설정

1. Azure AD B2C 블레이드를 열고 JavaScript 프런트 엔드 애플리케이션에 대한 애플리케이션 등록으로 이동합니다.
1. '리디렉션 URI'를 클릭하고 앞에서 입력한 자리 표시자 'https://jwt.ms '를 삭제합니다.
1. 기본(스토리지) 엔드포인트에 대한 새 URI를 추가합니다(후행 슬래시 제외).

   > [!NOTE]
   > 이 구성을 사용하면 프런트 엔드 애플리케이션 클라이언트가 Azure AD B2C의 적절한 클레임으로 액세스 토큰을 받습니다.  
   > SPA는 백 엔드 API 호출에서 이를 https 헤더에 전달자 토큰으로 추가할 수 있습니다.  
   > 
   > API Management는 토큰의 유효성을 검사하고, Azure ID(사용자)에서 발급한 JWT의 주체와 호출자의 IP 주소(API Management의 서비스 계층에 따라)를 사용하여 엔드포인트에 대한 호출의 유효성을 검사합니다. 요청을 통해 수신되는 Azure Function API에 전달하기 전에 함수 보안 키를 추가합니다.  
   > SPA는 브라우저에서 응답을 렌더링합니다.
   >
   > *축하합니다. Azure AD B2C, Azure API Management, Azure Functions, Azure App Service Authorization이 완벽히 조화롭게 작동하도록 구성했습니다.*

이제 간단한 보안 API가 포함된 간단한 앱을 만들어 테스트해 보겠습니다.

## <a name="test-the-client-application"></a>클라이언트 애플리케이션 이름

1. 이전에 만든 스토리지 계정에서 적어 둔 샘플 앱 URL을 엽니다.
1. 오른쪽 위 모서리에서 "로그인"을 클릭하면 Azure AD B2C 등록 또는 로그인 프로필이 팝업됩니다.
1. 앱은 B2C 프로필 이름으로 시작해야 합니다.
1. 이제 "API 호출"을 클릭하면 해당 페이지는 보안 API에서 다시 전송된 값으로 업데이트됩니다.
1. API 호출 단추를 *반복적으로* 클릭하고 API Management 개발자 계층 이상에서 실행하는 경우 솔루션은 API 속도를 제한하기 시작하고, 이 기능은 적절한 메시지와 함께 앱에 보고되어야 합니다.

## <a name="and-were-done"></a>모든 작업이 끝났습니다

위의 단계를 조정하고 편집하여 API Management와 Azure AD B2C를 다양하게 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Azure Active Directory 및 OAuth2.0](../active-directory/develop/authentication-vs-authorization.md)에 대해 자세히 알아봅니다.
* API Management에 대한 추가 [비디오](https://azure.microsoft.com/documentation/videos/index/?services=api-management) 를 확인합니다.
* 백 엔드 서비스를 보호하는 다른 방법은 [상호 인증서 인증](api-management-howto-mutual-certificates.md)을 참조하세요.
* [API Management 서비스 인스턴스 만들기](get-started-create-service-instance.md)
* [첫 번째 API 관리](import-and-publish.md)