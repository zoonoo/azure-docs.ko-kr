---
title: Active Directory B2C를 사용 하 여 Azure API Management에서 SPA 백 엔드 보호
description: PKCE 사용 SPA 인증 흐름을 사용 하 여 JavaScript SPA에서 호출 될 Azure Active Directory B2C, Azure API Management 및 Easy Auth를 사용 하 여 OAuth 2.0로 API를 보호 합니다.
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
ms.openlocfilehash: 812b54d10ea3cc3c405f534e36ac66abf3466808
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102449291"
---
# <a name="protect-spa-backend-with-oauth-20-azure-active-directory-b2c-and-azure-api-management"></a>OAuth 2.0, Azure Active Directory B2C 및 Azure API Management를 사용 하 여 SPA 백 엔드 보호

이 시나리오에서는 API를 보호 하도록 Azure API Management 인스턴스를 구성 하는 방법을 보여 줍니다.
EasyAuth를 사용 하 여 Azure Functions 백 엔드를 보호 하는 API Management와 함께 Azure AD B2C SPA (Auth Code + PKCE) flow를 사용 하 여 토큰을 가져옵니다.

## <a name="aims"></a>목표

Azure Functions 및 Azure AD B2C를 사용 하 여 간소화 된 시나리오에서 API Management를 사용 하는 방법을 살펴보겠습니다. Azure AD B2C를 사용 하 여 사용자에 게 로그인 하는 API를 호출 하는 JavaScript (JS) 앱을 만듭니다. 그런 다음 키 정책 기능을 통해 API Management의 jwt, CORS 및 요율 제한을 사용 하 여 백 엔드 API를 보호 합니다.

심층 방어를 위해 EasyAuth를 사용 하 여 백 엔드 API 내에서 토큰의 유효성을 다시 검사 하 고 API management가 Azure Functions 백 엔드를 호출할 수 있는 유일한 서비스 인지 확인 합니다.

## <a name="what-will-you-learn"></a>학습할 내용

> [!div class="checklist"]
> * Azure Active Directory B2C의 단일 페이지 앱 및 백 엔드 API 설정
> * Azure Functions 백 엔드 API 만들기
> * Azure API Management에 Azure Functions API 가져오기
> * Azure API Management에서 API 보호
> * MSAL.js (Microsoft Id 플랫폼 라이브러리)를 통해 Azure Active Directory B2C 권한 부여 끝점 호출
> * HTML/바닐라 JS 단일 페이지 응용 프로그램을 저장 하 고 Azure Blob Storage 끝점에서 서비스 제공

## <a name="prerequisites"></a>필수 구성 요소

이 문서의 단계를 따르려면 다음이 있어야 합니다.

* 프런트 엔드 JS 단일 페이지 앱을 호스트 하는 Azure (StorageV2) 범용 V2 저장소 계정.
* Azure API Management 인스턴스 (' 소비 '를 비롯 한 모든 계층은 작동 하지만, 전체 시나리오에 적용 되는 특정 기능을이 계층에서 사용할 수 없는 경우 (요금 제한-키 단위 및 전용 가상 IP) 이러한 제한은 해당 하는 문서에서 아래에 설명 되어 있습니다.
* 호출 된 API를 호스트 하는 빈 Azure 함수 앱 (소비 계획에서 V 3.1 .NET Core 런타임 실행)
* 구독에 연결 된 Azure AD B2C 테 넌 트입니다.

실제로 프로덕션 워크 로드에서 동일한 지역의 리소스를 사용 하는 경우에도이 방법 문서에서는 배포 지역이 중요 하지 않습니다.

## <a name="overview"></a>개요

다음은 사용 중인 구성 요소와이 프로세스가 완료 된 후 이러한 구성 요소 간의 흐름을 보여 주는 그림입니다.
![사용 중인 구성 요소 및 흐름](../api-management/media/howto-protect-backend-frontend-azure-ad-b2c/image-arch.png "사용 중인 구성 요소 및 흐름")

단계에 대 한 간략 한 개요는 다음과 같습니다.

1. 범위를 포함 하는 Azure AD B2C (프런트 엔드, API Management) 및 API 응용 프로그램을 만들고 API 액세스를 부여 합니다.
1. 사용자가 Azure AD B2C를 사용 하 여 로그인 할 수 있도록 등록 및 로그인 정책 만들기 
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

   > [!TIP]
   > 이 문서를 살펴볼 때 많은 정보 및 키를 캡처할 예정입니다. 다음 구성 항목을 일시적으로 저장 하기 위해 텍스트 편집기를 여는 것이 편리할 수 있습니다.  
   >
   > B2C 백 엔드 클라이언트 ID:  
   > B2C 백엔드 클라이언트 비밀 키:  
   > B2C 백엔드 API 범위 URI:  
   > B2C 프런트 엔드 클라이언트 ID:  
   > B2C 사용자 흐름 끝점 URI:  
   > B2C 잘 알려진 OPENID CONNECT 끝점:   
   > B2C 정책 이름: Frontendapp_signupandsignin 함수 URL:  
   > APIM API 기준 URL: 저장소 기본 끝점 URL:  

## <a name="configure-the-backend-application"></a>백 엔드 응용 프로그램 구성

포털에서 Azure AD B2C 블레이드를 열고 다음 단계를 수행 합니다.

1. **앱 등록** 탭을 선택 합니다.
1. ' 새 등록 ' 단추를 클릭 합니다.
1. 리디렉션 URI 선택 상자에서 ' 웹 '을 선택 합니다.
1. 이제 표시 이름을 설정 하 고, 생성 되는 서비스와 관련 된 고유한 항목을 선택 합니다. 이 예제에서는 "백 엔드 응용 프로그램" 이름을 사용 합니다.
1. 회신 url에 대 한 자리 표시자를 사용 하 여 ' https://jwt.ms ' (Microsoft 소유의 토큰 디코딩 사이트) 나중에 해당 url을 업데이트 합니다.
1. "모든 id 공급자 또는 조직 디렉터리의 계정 (사용자 흐름을 사용 하 여 사용자 인증)" 옵션을 선택 했는지 확인 합니다.
1. 이 샘플의 경우 현재 offline_access 권한이 필요 하지 않으므로 "관리자 동의 부여" 확인란의 선택을 취소 합니다.
1. '등록'을 클릭합니다.
1. 나중에 사용 하기 위해 백 엔드 응용 프로그램 클라이언트 ID를 기록 합니다 (' 응용 프로그램 (클라이언트) ID ' 아래에 표시 됨).
1. *인증서 및 비밀* 탭 (관리 아래)을 선택 하 고 ' 새 클라이언트 암호 '를 클릭 하 여 인증 키를 생성 합니다 (기본 설정을 적용 하 고 ' 추가 ' 클릭).
1. ' 추가 '를 클릭 하 고 나중에 ' 백 엔드 클라이언트 암호 '로 사용 하기 위해 안전 하 게 ' 값 ' 아래의 키를 복사 합니다 .이 대화 상자는이 키를 복사 해야 하는 유일한 기회입니다.
1. 이제 *API* 탭 표시 (관리 아래)를 선택 합니다.
1. AppID URI를 설정 하 라는 메시지가 표시 되 면 기본값을 선택 하 고 기록 합니다.
1. 함수 API에 대 한 "Hello" 범위 만들기 및 이름 지정할 수 있는 모든 옵션에 대해 ' Hello ' 구문을 사용 하 여 채워진 전체 범위 값 URI를 기록한 다음 ' 범위 추가 '를 클릭할 수 있습니다.
1. 포털의 왼쪽 위에 있는 ' Azure AD B2C ' 이동 경로를 선택 하 여 Azure AD B2C 블레이드의 루트로 돌아갑니다.

   > [!NOTE]
   > Azure AD B2C 범위는 응용 프로그램에서 API 액세스 블레이드를 통해 다른 응용 프로그램에 대 한 액세스를 요청할 수 있는 API 내에서 효과적으로 사용 되는 권한으로, 호출 된 API에 대 한 응용 프로그램 권한을 효과적으로 만들었습니다.

## <a name="configure-the-frontend-application"></a>프런트 엔드 응용 프로그램 구성

1. **앱 등록** 탭을 선택 합니다.
1. ' 새 등록 ' 단추를 클릭 합니다.
1. 리디렉션 URI 선택 상자에서 ' SPA (단일 페이지 응용 프로그램) '를 선택 합니다.
1. 이제 표시 이름 및 AppID URI를 설정 하 고,이 AAD B2C 앱 등록을 사용 하는 프런트 엔드 응용 프로그램과 관련 된 고유한 항목을 선택 합니다. 이 예제에서는 "프런트 엔드 응용 프로그램"을 사용할 수 있습니다.
1. 첫 번째 앱 등록에 따라 지원 되는 계정 유형 선택 항목을 기본값으로 둡니다. 사용자 흐름을 사용 하 여 사용자를 인증 합니다.
1. 회신 url에 대 한 자리 표시자를 사용 하 여 ' https://jwt.ms ' (Microsoft 소유의 토큰 디코딩 사이트) 나중에 해당 url을 업데이트 합니다.
1. 관리자 동의 허용 상자를 그대로 선택.
1. '등록'을 클릭합니다.
1. 나중에 사용할 프런트 엔드 응용 프로그램 클라이언트 ID를 기록 합니다 (' 응용 프로그램 (클라이언트) ID ' 아래에 표시 됨).
1. *API 사용 권한* 탭으로 전환 합니다.
1. ' 권한 추가 ', ' 내 Api ', ' 백 엔드 응용 프로그램 ', ' 사용 권한 '을 차례로 선택 하 고, 이전 섹션에서 만든 범위를 선택 하 고, ' 권한 추가 '를 클릭 하 여 백 엔드 응용 프로그램에 대 한 액세스 권한을 부여 합니다.
1. ' {Tenant}에 대해 관리자 동의 부여를 클릭 하 고 팝업 대화 상자에서 ' 예 '를 클릭 합니다. 이 팝업은 앞에서 만든 "백 엔드 응용 프로그램"에 정의 된 사용 권한 "hello"를 사용 하는 "프런트 엔드 응용 프로그램"을 동의 합니다.
1. 이제 모든 권한이 상태 열에서 녹색 틱으로 앱에 대해 표시 됩니다.

## <a name="create-a-sign-up-and-sign-in-user-flow"></a>"등록 및 로그인" 사용자 흐름 만들기

1. Azure AD B2C 이동 경로를 선택 하 여 B2C 블레이드의 루트로 돌아갑니다.
1. [정책] 탭에서 ' 사용자 흐름 '로 전환 합니다.
1. "새 사용자 흐름"을 클릭 합니다.
1. ' 등록 및 로그인 ' 사용자 흐름 유형을 선택 하 고 ' 권장 ', ' 만들기 '를 차례로 선택 합니다.
1. 정책에 이름을 지정 하 고 나중에 기록 합니다. 이 예에서는 "Frontendapp_signupandsignin"을 사용할 수 있습니다. "B2C_1_Frontendapp_signupandsignin"를 만들기 위해 "B2C_1_" 접두사가 접두사로 추가 됩니다.
1. ' Id 공급자 ' 및 "로컬 계정"에서 ' 전자 메일 등록 ' (또는 B2C 테 넌 트의 구성에 따라 ' 사용자 ID 등록 ')을 선택 하 고 확인을 클릭 합니다. 이 구성은 사용자의 기존 소셜 미디어 계정을 사용 하기 위해 다른 id 공급자 (예: 소셜 id 공급자)로 지연 되지 않고 로컬 B2C 계정을 등록할 수 있기 때문입니다.
1. MFA 및 조건부 액세스 설정은 기본값으로 둡니다.
1. ' 사용자 특성 및 클레임 '에서 ' 자세히 표시 ... '를 클릭 합니다. 그런 다음 사용자가 입력 하 고 토큰에서 반환 하려는 클레임 옵션을 선택 합니다. 반환할 ' 표시 이름 ' 및 ' 전자 메일 주소 '를 선택 하 고, ' 표시 이름 ' 및 ' 전자 메일 주소 '를 사용 하 여 (emailaddress, 단수형을 수집 하 고 전자 메일 주소를 반환 하도록 요청 하는 사실에 주의 해야 합니다.) ' 확인 '을 클릭 한 다음 ' 만들기 '를 클릭 합니다.
1. 목록에서 만든 사용자 흐름을 클릭 한 다음 ' 사용자 흐름 실행 ' 단추를 클릭 합니다.
1. 이 작업은 사용자 흐름 실행 블레이드를 열고, 프런트 엔드 응용 프로그램을 선택 하 고, 사용자 흐름 끝점을 복사 하 여 나중에 저장 합니다.
1. 나중에 사용 하기 위해 ' 잘 알려진 openid connect 구성 끝점 '으로 기록 하 여 맨 위에 있는 링크를 복사 하 고 저장 합니다.

   > [!NOTE]
   > B2C 정책을 사용 하면 다양 한 방식으로 다양 한 데이터 구성 요소 및 로그인 사용자를 캡처할 수 있도록 Azure AD B2C 로그인 끝점을 노출할 수 있습니다.
   > 
   > 이 경우 등록 또는 로그인 흐름 (정책)을 구성 했습니다. 이는 잘 알려진 구성 끝점도 노출 합니다. 두 경우 모두 생성 된 정책은 URL에서 "p =" 쿼리 문자열 매개 변수로 식별 되었습니다.  
   >
   > 이 작업이 완료 되 면 이제 여러 응용 프로그램에 사용자를 로그인 하는 기능을 갖춘 소비자 id 플랫폼을 사용할 수 있습니다.

## <a name="build-the-function-api"></a>함수 API 빌드

1. 구독에서 항목을 다시 구성할 수 있도록 Azure Portal에서 표준 Azure AD 테 넌 트로 다시 전환 합니다.
1. Azure Portal의 함수 앱 블레이드로 이동 하 여 빈 함수 앱을 연 다음 ' 함수 '를 클릭 하 고 ' 추가 '를 클릭 합니다.
1. 표시 되는 플라이 아웃에서 ' 포털에서 개발 '을 선택한 다음 ' 템플릿 선택 ' 아래에서 ' HTTP 트리거 '를 선택 하 고 템플릿 세부 정보 아래에서 권한 부여 수준 ' Function '이 포함 된 ' hello '로 이름을 지정한 다음 추가를 선택 합니다.
1. 코드 + 테스트 블레이드로 전환 하 고 아래에 있는 샘플 코드를 복사 하 여 아래에 표시 되는 *기존 코드 위에* 붙여넣습니다.
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
   > 방금 붙여 넣은 c # 스크립트 함수 코드는 단순히 함수 로그에 줄을 기록 하 고 일부 동적 데이터 (날짜 및 시간)를 사용 하 여 "헬로 월드" 텍스트를 반환 합니다.

1. 왼쪽 블레이드에서 "통합"을 선택 하 고 ' 트리거 ' 상자 내에서 http (요청) 링크를 클릭 합니다.
1. ' 선택한 HTTP 메서드 ' 드롭다운에서 http POST 메서드를 선택 취소 하 고, 선택 항목만 남기고 나 서, 저장을 클릭 합니다.
1. 코드 + 테스트 탭으로 다시 전환 하 고 ' 함수 URL 가져오기 '를 클릭 한 다음 표시 되는 URL을 복사 하 여 나중에 저장 합니다.

   > [!NOTE]
   > 방금 만든 바인딩은 방금 복사한 URL ()에 대 한 익명 http GET 요청에 응답 하도록 함수에 지시 `https://yourfunctionappname.azurewebsites.net/api/hello?code=secretkey` 합니다. 이제 매우 간단한 페이로드를 반환할 수 있는 확장 되지 않은 서버를 사용 하는 https API가 있습니다.
   >
   > 이제 방금 복사 하 고 저장 한 위의 URL 버전을 사용 하 여 웹 브라우저에서이 API 호출을 테스트할 수 있습니다. URL의 쿼리 문자열 매개 변수 "? code = secretkey" 부분을 제거 하 고 다시 테스트 하 여 Azure Functions에서 401 오류를 반환 하는지 증명할 수도 있습니다.

## <a name="configure-and-secure-the-function-api"></a>함수 API 구성 및 보안

1. 함수 앱의 두 추가 영역을 구성 해야 합니다 (권한 부여 및 네트워크 제한).
1. 먼저 인증/권한 부여를 구성 했으므로 이동 경로를 통해 함수 앱의 루트 블레이드로 다시 이동 합니다.
1. 그런 다음 ' 설정 ' 아래에서 ' 인증/권한 부여 '를 선택 합니다.
1. App Service 인증 기능을 설정 합니다.
1. 요청이 인증 되지 않은 경우에 수행할 동작을 "Azure Active Directory 사용 하 여 로그인"으로 설정 합니다.
1. ' 인증 공급자 '에서 ' Azure Active Directory '를 선택 합니다.
1. 관리 모드 스위치에서 ' 고급 '을 선택 합니다.
1. 백 엔드 응용 프로그램의 [Application] 클라이언트 ID (Azure AD B2C에서)를 ' 클라이언트 ID ' 상자에 붙여넣습니다.
1. 등록 및 로그인 정책에서 잘 알려진 오픈 id 구성 끝점을 발급자 URL 상자에 붙여넣습니다 (이전에이 구성을 기록 함).
1. ' 암호 표시 '를 클릭 하 고 백 엔드 응용 프로그램의 클라이언트 암호를 해당 상자에 붙여넣습니다.
1. 확인을 선택 하 여 id 공급자 선택 블레이드/화면으로 돌아갑니다.
1. 고급 설정에서 [토큰 저장소](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization#token-store) 를 사용 하도록 설정 된 상태로 둡니다 (기본값).
1. 블레이드의 왼쪽 위에서 ' 저장 '을 클릭 합니다.

   > [!IMPORTANT]
   > 이제 함수 API가 배포 되 고 올바른 JWT가 권한 부여: 전달자 헤더로 제공 되지 않는 경우 401 응답을 throw 하 고 유효한 요청이 표시 될 때 데이터를 반환 해야 합니다.  
   > 인증 되지 않은 요청을 처리 하기 위해 ' Azure AD를 사용 하 여 로그인 ' 옵션을 구성 하 여 EasyAuth에서 심층 방어 보안을 추가 했습니다. EasyAuth는 백 엔드 함수 앱와 프런트 엔드 SPA 간에 인증 되지 않은 요청 동작을 변경 합니다 .이는 401 권한이 부여 되지 않은 응답 대신 AAD에 302 리디렉션을 실행 하므로 나중에 API Management를 사용 하 여 해결할 수 있습니다.  
   >
   > 여전히 IP 보안이 적용 되지 않습니다. 유효한 키 및 OAuth2 토큰이 있는 경우 누구나이를 호출할 수 있습니다. 모든 요청을 API Management를 통해 적용 하는 것이 좋습니다.  
   > 
   > APIM 소비 계층을 사용 하는 경우, 함수 액세스 제한에 대 한 [전용 Azure API Management 가상 IP가](./api-management-howto-ip-addresses.md#ip-addresses-of-consumption-tier-api-management-service) 허용 목록에 없습니다. Azure API Management Standard SKU 이상에서 VIP는 [단일 테 넌 트이 고 리소스의 수명 동안](./api-management-howto-ip-addresses.md#changes-to-the-ip-addresses)입니다. Azure API Management 소비 계층의 경우 위에서 복사한 URI의 부분에 있는 공유 비밀 기능 키를 통해 API 호출을 잠글 수 있습니다. 또한 소비 계층-아래 12-17 단계는 적용 되지 않습니다.

1. ' 인증/권한 부여 ' 블레이드를 닫습니다. 
1. *포털의 API Management 블레이드* 를 연 후 *인스턴스* 를 엽니다.
1. 개요 탭에 표시 된 개인 VIP를 기록 합니다.
1. *포털의 Azure Functions 블레이드* 로 돌아간 후 *인스턴스* 를 다시 엽니다.
1. ' 네트워킹 '을 선택한 후 ' 액세스 제한 구성 '을 선택 합니다.
1. ' 규칙 추가 '를 클릭 하 고 위의 3 단계에서 복사한 VIP를 xx. xx. xx/32 형식으로 입력 합니다.
1. 계속 해 서 함수 포털과 상호 작용 하 고 아래에서 선택적 단계를 수행 하려는 경우 사용자 고유의 공용 IP 주소 또는 CIDR 범위만 추가 해야 합니다.
1. 목록에 허용 항목이 있으면 Azure는 다른 모든 주소를 차단 하는 암시적인 거부 규칙을 추가 합니다.

IP 제한 패널에 CIDR 형식 주소 블록을 추가 해야 합니다. API Management VIP와 같은 단일 주소를 추가 해야 하는 경우에는 xx. xx. xx/32 형식으로 추가 해야 합니다.

   > [!NOTE]
   > 이제 API management 나 사용자의 주소를 제외한 모든 위치에서 함수 API를 호출할 수 없습니다.

1. *API Management 블레이드* 를 연 후 *인스턴스* 를 엽니다.
1. Api 블레이드 아래에서 Api 블레이드를 선택 합니다.
1. ' 새 API 추가 ' 창에서 ' 함수 앱 '를 선택한 다음 팝업 위쪽에서 ' 전체 '를 선택 합니다.
1. 찾아보기를 클릭 하 고 내에서 API를 호스트 하는 함수 앱을 선택한 다음 선택을 클릭 합니다. 다음으로, 선택을 다시 클릭 합니다.
1. API Management의 내부 사용에 대 한 이름 및 설명을 API에 제공 하 고 ' 제한 없음 ' 제품에 추가 합니다.
1. API의 ' 기본 URL '을 복사 하 고 기록 하 고 ' 만들기 '를 클릭 합니다.
1. 이 경우 Oauth JWT 토큰을 사용 하는 경우 ' 설정 ' 탭을 클릭 한 다음 구독-' 구독 필요 ' 확인란을 선택 취소 합니다. 소비 계층을 사용 하는 경우 프로덕션 환경에서 여전히 필요 합니다. 

   > [!TIP]
   > APIM의 소비 계층을 사용 하는 경우 무제한 제품을 기본 제공으로 사용할 수 없습니다. 대신 "Api"의 "제품"으로 이동 하 고 "추가"를 누릅니다.  
   > 제품 이름 및 설명으로 "제한 없음"을 입력 하 고 화면 왼쪽 아래에 있는 "+" Api 설명선에서 방금 추가한 API를 선택 합니다. "게시 됨" 확인란을 선택 합니다. 나머지는 기본값으로 둡니다. 마지막으로 "만들기" 단추를 누릅니다. 이렇게 하면 "제한 없음" 제품이 생성 되어 API에 할당 됩니다. 나중에 새 제품을 사용자 지정할 수 있습니다.

## <a name="configure-and-capture-the-correct-storage-endpoint-settings"></a>올바른 저장소 끝점 설정 구성 및 캡처

1. Azure Portal에서 storage 계정 블레이드를 엽니다. 
1. 만든 계정을 선택 하 고 설정 섹션에서 ' 정적 웹 사이트 ' 블레이드를 선택 합니다. ' 정적 웹 사이트 ' 옵션이 표시 되지 않으면 V2 계정을 만들었는지 확인 합니다.
1. 정적 웹 호스팅 기능을 ' 사용 '으로 설정 하 고 인덱스 문서 이름을 ' index.html '로 설정한 후 ' 저장 '을 클릭 합니다.
1. 이 위치는 프런트 엔드 사이트가 호스팅될 위치 이므로 나중에 대해 ' 기본 끝점 '의 내용을 적어둡니다.

   > [!TIP]
   > Azure Blob Storage + CDN 재작성 또는 Azure App Service를 사용 하 여 SPA를 호스트할 수 있지만 Blob Storage의 정적 웹 사이트 호스팅 기능을 사용 하면 Azure Storage에서 정적 웹 콘텐츠/h t t p/s u p t/css를 제공 하는 기본 컨테이너를 제공 하 고, 0 작업에 대해 기본 페이지를 유추할 수 있습니다.

## <a name="set-up-the-cors-and-validate-jwt-policies"></a>**CORS** 설정 및 jwt 정책 **유효성 검사**

> 사용 중인 APIM 계층에 관계 없이 다음 섹션을 따라야 합니다. 저장소 계정 URL은이 문서의 맨 위에 있는 전제 조건에서 사용할 수 있도록 저장소 계정에서 제공 됩니다.
1. 포털의 API management 블레이드로 전환 하 고 인스턴스를 엽니다.
1. Api를 선택한 다음 "모든 Api"를 선택 합니다.
1. "인바운드 처리"에서 코드 보기 단추 "</>"를 클릭 하 여 정책 편집기를 표시 합니다.
1. 인바운드 섹션을 편집 하 고 다음과 같이 읽도록 xml을 붙여넣습니다.
1. 정책에서 다음 매개 변수를 바꿉니다.
1. {PrimaryStorageEndpoint} (이전 섹션에서 복사한 ' 기본 저장소 끝점 '), {b2cpolicy-openid connect} (이전에 복사한 ' 잘 알려진 openid connect 구성 끝점 ') 및 {} ( **백 엔드 api** 에 대 한 B2C 응용 프로그램/클라이언트 id)를 사용 하 여 이전에 저장 한 올바른 값이 있습니다.
1. API Management의 소비 계층을 사용 하는 경우 Azure API Management의 소비 계층을 사용 하는 경우이 정책을 사용할 수 없으므로 rate limit key 정책을 모두 제거 해야 합니다.

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
   > 이제 Azure API management는 JavaScript SPA 앱의 원본 간 요청에 응답할 수 있으며, 요청을 함수 API로 전달 하기 전에 전달 되는 JWT 인증 토큰의 제한, 요율 제한 및 사전 유효성 검사를 수행 합니다.
   > 
   > 축 하 합니다. 이제 Azure AD B2C API Management 하 고 함께 작업 하 여 API를 게시, 보호 및 사용할 Azure Functions 있습니다.

   > [!TIP]
   > API Management 소비 계층을 사용 하는 경우 JWT 주체 또는 들어오는 IP 주소 (현재 "소비" 계층에 대 한 키 정책에의 한 호출 율 제한)에의 한 요금 제한 대신 통화 요금 할당량으로 제한할 수 있습니다. [여기](./api-management-access-restriction-policies.md#LimitCallRate)를 참조 하십시오.  
   > 이 예제에서는 JavaScript 단일 페이지 응용 프로그램 이기 때문에 API Management 키를 사용 하 여 요금 제한 및 청구 호출에만 사용 합니다. 실제 권한 부여 및 인증은 Azure AD B2C에 의해 처리 되 고 JWT에 캡슐화 되며, API Management에 의해 한 번, 그 다음에 백 엔드 Azure 함수에서 유효성을 두 번 확인 합니다.

## <a name="upload-the-javascript-spa-sample-to-static-storage"></a>정적 저장소에 JavaScript SPA 샘플 업로드

1. 저장소 계정 블레이드의 Blob Service 섹션에서 ' 컨테이너 ' 블레이드를 선택 하 고 오른쪽 창에 표시 되는 $web 컨테이너를 클릭 합니다.
1. 아래 코드를 컴퓨터에서 로컬로 파일에 index.html로 저장 한 다음, index.html 파일을 $web 컨테이너에 업로드 합니다.

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

1. 마지막 섹션에서 이전에 저장 한 정적 웹 사이트 기본 끝점으로 이동 합니다.

   > [!NOTE]
   > 축 하 합니다. 정적 콘텐츠 호스팅을 Azure Storage 하는 JavaScript 단일 페이지 앱을 배포 했습니다.  
   > Azure AD B2C 세부 정보를 사용 하 여 JS 앱을 아직 구성 하지 않았기 때문에 페이지를 열 때 페이지가 아직 작동 하지 않습니다.

## <a name="configure-the-javascript-spa-for-azure-ad-b2c"></a>Azure AD B2C에 대 한 JavaScript SPA 구성

1. 이제 모든 것이 무엇 인지 알 수 있습니다. 적절 한 API Management API 주소 및 올바른 Azure AD B2C 응용 프로그램/클라이언트 Id로 SPA를 구성할 수 있습니다.
1. Azure Portal 저장소 블레이드로 돌아가기 
1. ' 설정 ' 아래에서 ' 컨테이너 '를 선택 합니다. 
1. 목록에서 ' $web ' 컨테이너를 선택 합니다.
1. 목록에서 index.html blob을 선택 합니다. 
1. ' 편집 '을 클릭 합니다. 
1. 이전에 B2C에 등록 한 *프런트 엔드* 응용 프로그램과 일치 하도록 msal 구성 섹션의 인증 값을 업데이트 합니다. 구성 값을 확인 하는 방법에 대 한 힌트는 코드 주석을 사용 합니다.
*권한* 값은 다음 형식 이어야 합니다.-https://{b2ctenantname}. b2clogin/tfp/{b2ctenantname}. onmicrosoft}/{signupandsigninpolicyname}. 샘플 이름을 사용 하 고 테 넌 트가 ' contoso ' 라고 하면 권한이 ' '이 됩니다. https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com}/Frontendapp_signupandsignin
1. 백 엔드 주소와 일치 하도록 api 값을 설정 합니다 (앞에서 기록한 API 기본 Url 및 *백 엔드 응용 프로그램* 에 대해 ' b2cScopes ' 값이 이전에 기록 됨).
1. 저장을 클릭합니다.

## <a name="set-the-redirect-uris-for-the-azure-ad-b2c-frontend-app"></a>Azure AD B2C 프런트 엔드 앱에 대 한 리디렉션 Uri 설정

1. Azure AD B2C 블레이드를 열고 JavaScript 프런트 엔드 응용 프로그램에 대 한 응용 프로그램 등록으로 이동 합니다.
1. ' 리디렉션 Uri '를 클릭 하 고 앞에서 입력 한 자리 표시자 ' '를 삭제 https://jwt.ms 합니다.
1. 기본 (저장소) 끝점에 대 한 새 URI를 추가 합니다 (후행 슬래시 제외).

   > [!NOTE]
   > 이 구성을 사용 하면 프런트 엔드 응용 프로그램 클라이언트가 Azure AD B2C의 적절 한 클레임으로 액세스 토큰을 받습니다.  
   > SPA는 백엔드 API 호출에서이를 https 헤더에 전달자 토큰으로 추가할 수 있습니다.  
   > 
   > API Management는 토큰의 유효성을 검사 하 고, Azure ID (사용자)에서 발급 한 JWT의 주체와 호출자의 IP 주소 (API Management의 서비스 계층에 따라)를 사용 하 여 끝점에 대 한 호출의 유효성을 검사 합니다. 요청을 통해 수신 되는 Azure Function API에 전달 하기 전에 함수 보안 키를 추가 합니다.  
   > SPA는 브라우저에서 응답을 렌더링 합니다.
   >
   > *축 하 합니다. 완벽 한 조화에서 작동 하도록 Azure AD B2C, Azure API Management, Azure Functions, Azure App Service 권한 부여를 구성 했습니다.*

이제 간단한 보안 API를 사용 하 여 간단한 앱을 만들어 테스트 해 보겠습니다.

## <a name="test-the-client-application"></a>클라이언트 응용 프로그램 테스트

1. 이전에 만든 저장소 계정에서 적어 둔 샘플 앱 URL을 엽니다.
1. 오른쪽 위 모서리에서 "로그인"을 클릭 하면 Azure AD B2C 등록 또는 로그인 프로필을 팝업 합니다.
1. 앱은 B2C 프로필 이름으로 시작 해야 합니다.
1. 이제 "API 호출"을 클릭 하면 해당 페이지는 보안 API에서 다시 전송 된 값으로 업데이트 됩니다.
1. API 호출 단추를 *반복적* 으로 클릭 하 고 API Management 개발자 계층 이상에서 실행 하는 경우, 솔루션은 api를 제한 하기 시작 하 고이 기능은 적절 한 메시지와 함께 앱에 보고 되어야 합니다.

## <a name="and-were-done"></a>완료 되었습니다.

위의 단계를 조정 하 고 편집 하 여 API Management와 Azure AD B2C를 다양 하 게 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Azure Active Directory 및 OAuth2.0](../active-directory/develop/authentication-vs-authorization.md)에 대해 자세히 알아봅니다.
* API Management에 대한 추가 [비디오](https://azure.microsoft.com/documentation/videos/index/?services=api-management) 를 확인합니다.
* 백 엔드 서비스를 보호하는 다른 방법은 [상호 인증서 인증](api-management-howto-mutual-certificates.md)을 참조하세요.
* [API Management 서비스 인스턴스를 만듭니다](get-started-create-service-instance.md).
* [첫 번째 API 관리](import-and-publish.md)
