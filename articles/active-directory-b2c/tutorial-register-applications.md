---
title: 자습서-응용 프로그램 등록-Azure Active Directory B2C
description: Azure Portal를 사용하여 Azure Active Directory B2C에서 웹 애플리케이션을 등록하는 방법을 알아봅니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 10/16/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 3a5d5099c91736276aca56c8b94b846b53a190c1
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73642424"
---
# <a name="tutorial-register-an-application-in-azure-active-directory-b2c"></a>자습서: Azure Active Directory B2C에 응용 프로그램 등록

[응용 프로그램](active-directory-b2c-apps.md) 은 Azure Active Directory B2C (Azure AD B2C)와 상호 작용 하기 전에 관리 하는 테 넌 트에 등록 되어야 합니다. 이 자습서에서는 Azure Portal을 사용하여 웹 애플리케이션을 등록하는 방법을 보여줍니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * 웹 애플리케이션 등록
> * 클라이언트 비밀 만들기

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>필수 조건

아직 고유한 [Azure AD B2C 테넌트](tutorial-create-tenant.md)를 만들지 않았다면 지금 만듭니다. 기존 Azure AD B2C 테넌트를 사용해도 됩니다.

## <a name="register-a-web-application"></a>웹 애플리케이션 등록

Azure AD B2C 테 넌 트에 응용 프로그램을 등록 하려면 현재 **응용 프로그램** 환경 또는 새로운 통합 **앱 등록 (미리 보기)** 환경을 사용할 수 있습니다. [새 환경에 대해 자세히 알아보세요](https://aka.ms/b2cappregintro).

#### <a name="applicationstabapplications"></a>[애플리케이션](#tab/applications/)

1. Azure AD B2C 테넌트를 포함하는 디렉터리를 사용하려면 위쪽 메뉴에서 **디렉터리 + 구독** 필터를 선택하고 테넌트가 포함된 디렉터리를 선택합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
1. **애플리케이션**을 선택하고 **추가**를 선택합니다.
1. 애플리케이션의 이름을 입력합니다. 예를 들어 *webapp1*과 같습니다.
1. **웹앱/웹 API 포함** 및 **암시적 흐름 허용**에 **예**를 선택합니다.
1. **회신 URL**에는 Azure AD B2C에서 애플리케이션이 요청한 토큰을 반환하는 엔드포인트를 입력합니다. 예를 들어 `https://localhost:44316`에서 로컬로 수신 대기 하도록 설정할 수 있습니다. 포트 번호를 아직 모르는 경우 자리 표시자 값을 입력한 후 나중에 변경할 수 있습니다.

    이 자습서와 같은 테스트를 위해 검사할 토큰의 내용을 표시 하는 `https://jwt.ms`로 설정할 수 있습니다. 이 자습서에서는 **회신 URL** 을 `https://jwt.ms`로 설정 합니다.

    회신 Url에는 다음과 같은 제한 사항이 적용 됩니다.

    * 회신 URL은 `https`스키마로 시작 해야 합니다.
    * 회신 URL은 대/소문자를 구분 합니다. 해당 사례는 실행 중인 응용 프로그램의 URL 경로에 대 한 대/소문자와 일치 해야 합니다. 예를 들어 응용 프로그램의 경로 `.../abc/response-oidc`일부로를 포함 하는 경우 회신 URL에 `.../ABC/response-oidc`를 지정 하지 마십시오. 웹 브라우저에서 경로를 대/소문자를 구분 하므로 `.../abc/response-oidc`와 연결 된 쿠키는 대/소문자가 일치 하지 않는 `.../ABC/response-oidc` URL로 리디렉션되는 경우 제외 될 수 있습니다.

1. **만들기** 를 클릭 하 여 응용 프로그램 등록을 완료 합니다.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[앱 등록 (미리 보기)](#tab/app-reg-preview/)

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
1. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택 하 고 Azure AD B2C 테 넌 트가 포함 된 디렉터리를 선택 합니다.
1. 왼쪽 메뉴에서 **Azure AD B2C**를 선택 합니다. 또는 **모든 서비스** 를 선택 하 고 **Azure AD B2C**을 검색 하 고 선택 합니다.
1. **앱 등록 (미리 보기)** 를 선택 하 고 **새 등록**을 선택 합니다.
1. 응용 프로그램의 **이름을** 입력 합니다. 예를 들어 *webapp1*과 같습니다.
1. **모든 조직 디렉터리 또는 모든 id 공급자에서 계정을**선택 합니다.
1. **URI 리디렉션**에서 **웹**을 선택한 다음 URL 텍스트 상자에 `https://jwt.ms`을 입력 합니다.

    리디렉션 URI는 사용자와의 상호 작용을 완료 한 후 권한 부여 서버에서 사용자를 보내고, 성공적인 권한 부여 시 액세스 토큰 또는 권한 부여 코드를 전송 하는 끝점입니다 (이 경우에는 Azure AD B2C). 프로덕션 응용 프로그램에서는 일반적으로 앱이 실행 되는 `https://contoso.com/auth-response`와 같이 공개적으로 액세스할 수 있는 끝점입니다. 이 자습서와 같은 테스트를 위해 토큰을 디코딩된 토큰 콘텐츠를 표시 하는 Microsoft 소유의 웹 응용 프로그램 `https://jwt.ms`(토큰의 내용이 브라우저에서 벗어나면 안 됨)로 설정할 수 있습니다. 앱을 개발 하는 동안 응용 프로그램이 로컬에서 수신 대기 하는 끝점 (예: `https://localhost:5000`)을 추가할 수 있습니다. 언제 든 지 등록 된 응용 프로그램에서 리디렉션 Uri를 추가 하 고 수정할 수 있습니다.

    리디렉션 Uri에는 다음과 같은 제한 사항이 적용 됩니다.

    * 회신 URL은 `https`스키마로 시작 해야 합니다.
    * 회신 URL은 대/소문자를 구분 합니다. 해당 사례는 실행 중인 응용 프로그램의 URL 경로에 대 한 대/소문자와 일치 해야 합니다. 예를 들어 응용 프로그램의 경로 `.../abc/response-oidc`일부로를 포함 하는 경우 회신 URL에 `.../ABC/response-oidc`를 지정 하지 마십시오. 웹 브라우저에서 경로를 대/소문자를 구분 하므로 `.../abc/response-oidc`와 연결 된 쿠키는 대/소문자가 일치 하지 않는 `.../ABC/response-oidc` URL로 리디렉션되는 경우 제외 될 수 있습니다.

1. **사용 권한**아래에서 *openid connect and offline_access Permissions에 관리자 동의 부여* 확인란을 선택 합니다.
1. **등록**을 선택합니다.

응용 프로그램 등록이 완료 되 면 암시적 권한 부여 흐름을 사용 하도록 설정 합니다.

1. **관리**아래에서 **인증**을 선택 합니다.
1. **새 환경 사용해 보기** (표시 되는 경우)를 선택 합니다.
1. **암시적 권한 부여**에서 **액세스 토큰** 및 **ID 토큰** 확인란을 모두 선택 합니다.
1. **저장**을 선택합니다.

* * *

## <a name="create-a-client-secret"></a>클라이언트 비밀 만들기

응용 프로그램에서 토큰에 대 한 코드를 교환 하는 경우 응용 프로그램 암호를 만들어야 합니다.

#### <a name="applicationstabapplications"></a>[애플리케이션](#tab/applications/)

1. **응용 프로그램 Azure AD B2C** 페이지에서 사용자가 만든 응용 프로그램을 선택 합니다 (예: *webapp1*).
1. **키** 를 선택 하 고 **키 생성**을 선택 합니다.
1. **저장**을 선택하여 키를 확인합니다. **앱 키** 값을 기록해 둡니다. 응용 프로그램 코드에서이 값을 응용 프로그램 암호로 사용 합니다.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[앱 등록 (미리 보기)](#tab/app-reg-preview/)

1. **Azure AD B2C-앱 등록 (미리 보기)** 페이지에서 만든 응용 프로그램을 선택 합니다 (예: *webapp1*).
1. **관리**아래에서 **인증서 & 암호**를 선택 합니다.
1. **새 클라이언트 비밀**을 선택합니다.
1. **설명** 상자에 클라이언트 암호에 대 한 설명을 입력 합니다. 예를 들면 *clientsecret1*입니다.
1. **만료**에서 비밀이 유효한 기간을 선택 하 고 **추가**를 선택 합니다.
1. 비밀 **값**을 기록 합니다. 응용 프로그램 코드에서이 값을 응용 프로그램 암호로 사용 합니다.

* * *

## <a name="next-steps"></a>다음 단계

이 문서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 웹 애플리케이션 등록
> * 클라이언트 비밀 만들기

다음으로 사용자가 자신의 프로필에 등록, 로그인 및 관리할 수 있도록 사용자 흐름을 만드는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [Azure Active Directory B2C >에서 사용자 흐름 만들기](tutorial-create-user-flows.md)
