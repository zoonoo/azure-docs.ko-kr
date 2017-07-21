---
title: "Azure Active Directory B2C: 응용 프로그램 등록 | Microsoft Docs"
description: "Azure Active Directory B2C로 응용 프로그램 등록하는 방법"
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: 20e92275-b25d-45dd-9090-181a60c99f69
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 6/13/2017
ms.author: parakhj
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: 3499ff57e650c70679dfa018eec5dbe1a6173a33
ms.contentlocale: ko-kr
ms.lasthandoff: 07/04/2017



---
# <a name="azure-active-directory-b2c-register-your-application"></a>Azure Active Directory B2C: 응용 프로그램 등록

> [!IMPORTANT]
> Azure Portal의 Azure AD B2C 블레이드에서 만든 응용 프로그램은 동일한 위치에서 관리되어야 합니다. PowerShell 또는 다른 포털을 사용하여 B2C 응용 프로그램을 편집하는 경우 해당 응용 프로그램이 지원되지 않게 되며 Azure AD B2C와 함께 작동하지 않습니다. [아래](#faulted-apps)에서 자세히 알아봅니다.
>

## <a name="prerequisite"></a>필수 요소

소비자 등록 및 로그인을 수락하는 응용 프로그램을 만들려면 먼저 Azure Active Directory B2C 테넌트를 사용하여 해당 응용 프로그램을 등록해야 합니다. [Azure AD B2C 테넌트 만들기](active-directory-b2c-get-started.md)에 요약한 단계를 사용하여 자신의 테넌트를 가져옵니다. 해당 문서의 모든 단계를 수행한 후 시작 보드에 고정된 B2C 기능 블레이드가 있을 것입니다.

[!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="navigate-to-the-b2c-features-blade"></a>B2C 기능 블레이드로 이동

시작 보드에 고정된 B2C 기능 블레이드가 있다면 [Azure 포털](https://portal.azure.com/) 에 B2C 테넌트의 전역 관리자 권한으로 로그인하는 즉시 해당 블레이드가 나타납니다.

또한 [Azure Portal](https://portal.azure.com/)의 왼쪽 탐색 창에서 **추가 서비스**를 클릭한 다음 **Azure AD B2C**를 검색하여 블레이드에 액세스할 수도 있습니다.

> [!IMPORTANT]
> B2C 기능 블레이드에 액세스하려면 B2C 테넌트의 전역 관리자가 되어야 합니다. 다른 테넌트의 전역 관리자 또는 사용자는 액세스할 수 없습니다.  Azure Portal의 오른쪽 위 모서리에 있는 테넌트 전환기를 사용하여 B2C 테넌트로 전환할 수 있습니다.
>
>

## <a name="register-a-web-application"></a>웹 응용 프로그램 등록

1. Azure 포털의 B2C 기능 블레이드에서 **응용 프로그램**을 클릭합니다.
1. 블레이드의 위쪽에서 **+추가** 를 클릭합니다.
1. 소비자에게 응용 프로그램을 설명하는 응용 프로그램의 **이름** 을 입력합니다. 예를 들어 "Contoso B2C 앱"을 입력할 수 있습니다.
1. **웹앱/ 웹 API 포함** 스위치를 **예**로 설정합니다.
1. Azure AD B2C에서 응용 프로그램이 요청한 토큰을 반환하는 끝점인 **회신 URL**에 대한 [적절한](#limitations) 값을 입력합니다. 예를 들어 `https://localhost:44316/`을 입력합니다.
1. **만들기** 를 클릭하여 응용 프로그램을 등록합니다.
1. 방금 만든 응용 프로그램을 클릭하고, 나중에 코드에서 사용할 전역적으로 고유한 **응용 프로그램 클라이언트 ID** 를 적어둡니다.
1. 웹 응용 프로그램이 Azure AD B2C로 보호되는 웹 API를 호출하는 경우 다음을 수행하십시오.
    1. **키** 블레이드로 이동하여 **키 생성** 단추를 클릭하여 **응용 프로그램 비밀**을 만듭니다.
    1. **API 액세스**를 클릭하고 **추가**를 클릭하여 웹 API와 범위(사용 권한)을 선택합니다.

> [!NOTE]
> **응용 프로그램 암호** 는 중요한 보안 자격 증명이며 적절하게 보호해야 합니다.
>

## <a name="register-a-web-api"></a>웹 API 등록

1. Azure 포털의 B2C 기능 블레이드에서 **응용 프로그램**을 클릭합니다.
1. 블레이드의 위쪽에서 **+추가** 를 클릭합니다.
1. 소비자에게 응용 프로그램을 설명하는 응용 프로그램의 **이름** 을 입력합니다. 예를 들어 "Contoso B2C api"를 입력할 수 있습니다.
1. **웹앱/ 웹 API 포함** 스위치를 **예**로 설정합니다.
1. Azure AD B2C에서 응용 프로그램이 요청한 토큰을 반환하는 끝점인 **회신 URL**에 대한 [적절한](#choosing-a-web-app/api-reply-url) 값을 입력합니다. 예를 들어 `https://localhost:44316/`을 입력합니다.
1. **앱 ID URI**를 입력합니다. Web API에 사용되는 식별자입니다. 예를 들어 '참고'를 입력합니다. 아래에서 전체 식별자 URI를 생성합니다.
1. **만들기** 를 클릭하여 응용 프로그램을 등록합니다.
1. 방금 만든 응용 프로그램을 클릭하고, 나중에 코드에서 사용할 전역적으로 고유한 **응용 프로그램 클라이언트 ID** 를 적어둡니다.
1. **게시된 범위**를 클릭합니다. 다른 응용 프로그램에 부여할 수 있는 사용 권한(범위)을 정의한 위치입니다.
1. 필요에 따라 더 많은 범위를 추가합니다. 기본적으로 "user_impersonation" 범위를 정의합니다. 이렇게 하면 로그인한 사용자를 대신하여 다른 응용 프로그램이 이 API에 액세스할 수 있는 기능을 부여합니다. 원하는 경우 제거할 수 있습니다.
1. **Save**를 클릭합니다.

## <a name="register-a-mobilenative-application"></a>모바일/네이티브 응용 프로그램 등록

1. Azure 포털의 B2C 기능 블레이드에서 **응용 프로그램**을 클릭합니다.
1. 블레이드의 위쪽에서 **+추가** 를 클릭합니다.
1. 소비자에게 응용 프로그램을 설명하는 응용 프로그램의 **이름** 을 입력합니다. 예를 들어 "Contoso B2C 앱"을 입력할 수 있습니다.
1. **네이티브 클라이언트 포함** 스위치를 **예**로 설정합니다.
1. 사용자 지정 체계로 **리디렉션 URI**를 입력합니다. 예를 들어 com.onmicrosoft.contoso.appname://redirect/경로입니다. [적절한 리디렉션 URI](#choosing-a-native-application-redirect-uri)를 선택하고 밑줄 등의 특수 문자를 포함하지 마십시오.
1. **저장**을 클릭하여 응용 프로그램을 등록합니다.
1. 방금 만든 응용 프로그램을 클릭하고, 나중에 코드에서 사용할 전역적으로 고유한 **응용 프로그램 클라이언트 ID** 를 적어둡니다.
1. 네이티브 응용 프로그램이 Azure AD B2C로 보호되는 웹 API를 호출하는 경우 다음을 수행하십시오.
    1. **키** 블레이드로 이동하여 **키 생성** 단추를 클릭하여 **응용 프로그램 비밀**을 만듭니다.
    1. **API 액세스**를 클릭하고 **추가**를 클릭하여 웹 API와 범위(사용 권한)을 선택합니다.

> [!NOTE]
> **응용 프로그램 암호** 는 중요한 보안 자격 증명이며 적절하게 보호해야 합니다.
>

## <a name="limitations"></a>제한 사항

### <a name="choosing-a-web-appapi-reply-url"></a>웹앱/api 회신 URL 선택

현재, Azure AD B2C에 등록된 앱은 제한된 회신 URL 값 집합으로 제한됩니다. 웹앱 및 서비스에 대한 회산 URL은 스키마 `https`로 시작해야 하고 모든 회신 URL 값은 단일 DNS 도메인을 공유해야 합니다. 예를 들어 다음 회신 URL 중 하나가 있는 웹앱은 등록할 수 없습니다.

`https://login-east.contoso.com`

`https://login-west.contoso.com`

등록 시스템은 기존 회신 URL의 전체 DNS 이름을 편집하려는 회신 URL의 DNS 이름과 비교합니다. 다음 조건 중 하나라도 충족되는 경우 DNS 이름을 추가하는 요청이 실패하게 됩니다.

* 새 회신 URL의 전체 DNS 이름이 기존 회신 URL의 DNS 이름과 일치하지 않는 경우
* 새 회신 URL의 전체 DNS 이름이 기존 회신 URL의 하위 도메인이 아닌 경우

예를 들어 앱에 다음 회신 URL이 있는 경우

`https://login.contoso.com`

다음과 같이 추가할 수 있습니다.

`https://login.contoso.com/new`

이 경우 DNS 이름이 정확히 일치합니다. 또는 다음을 수행할 수 있습니다.

`https://new.login.contoso.com`

이 경우 login.contoso.com의 DNS 하위 도메인을 참조합니다. 회신 URL로 login-east.contoso.com 및 login-west.contoso.com을 사용하는 앱을 원하는 경우 다음 회신 URL을 순서대로 추가해야 합니다.

`https://contoso.com`

`https://login-east.contoso.com`

`https://login-west.contoso.com`

뒤의 두 개는 첫 번째 회신 URL의 하위 도메인이므로 추가할 수 있습니다.

### <a name="choosing-a-native-application-redirect-uri"></a>네이티브 응용 프로그램 리디렉션 URI 선택

모바일/네이티브 응용 프로그램에 대한 리디렉션 URI를 선택하는 경우 두 가지 중요한 고려 사항이 있습니다.

* **고유**: 리디렉션 URI의 체계는 모든 응용 프로그램에 대해 고유해야 합니다. 예제에서는(com.onmicrosoft.contoso.appname://redirect/path) 체계로 com.onmicrosoft.contoso.appname을 사용합니다. 이 패턴을 따르는 것이 좋습니다. 두 개의 응용 프로그램이 동일한 체계를 공유하는 경우 "앱 선택" 대화 상자가 나타납니다. 사용자가 잘못 선택하는 경우 로그인이 실패합니다.
* **전체**: 리디렉션 URI에는 체계 및 경로가 있어야 합니다. 경로는 도메인 뒤에 하나 이상의 슬래시를 포함해야 합니다(예: //contoso/는 작동, //contoso는 실패).

리디렉션 uri에 밑줄과 같은 특수 문자가 없는지 확인합니다.

### <a name="faulted-apps"></a>오류가 발생한 앱

B2C 응용 프로그램은 다음에서 편집할 수 없습니다.

* [Azure 클래식 포털](https://manage.windowsazure.com/) 및 [응용 프로그램 등록 포털](https://apps.dev.microsoft.com/)과 같은 다른 응용 프로그램 관리 포털.
* Graph API 또는 PowerShell 사용

위에서 설명한 대로 B2C 응용 프로그램을 편집하고 Azure Portal의 Azure AD B2C 기능 블레이드에서 다시 편집하려는 경우 오류가 발생한 앱이 되고 응용 프로그램은 Azure AD B2C와 함께 더 이상 사용할 수 없습니다. 응용 프로그램을 삭제하고 다시 만들어야 합니다.

앱을 삭제하려면 [응용 프로그램 등록 포털](https://apps.dev.microsoft.com/)로 이동하고 거기에서 응용 프로그램을 삭제합니다. 응용 프로그램을 표시하려면 응용 프로그램의 소유자여야 합니다(테넌트의 관리자가 아님).

## <a name="next-steps"></a>다음 단계

Azure AD B2C로 등록된 응용 프로그램이 있다면 작동할 [빠른 시작 자습서](active-directory-b2c-overview.md#get-started) 중 하나를 완료하여 실행할 수 있습니다.

