---
title: '자습서: 응용 프로그램 등록'
titleSuffix: Azure AD B2C
description: Azure Portal를 사용하여 Azure Active Directory B2C에서 웹 애플리케이션을 등록하는 방법을 알아봅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 10/16/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a688f5e75f7513d0ea4308b751f87f75a2c9510a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183094"
---
# <a name="tutorial-register-an-application-in-azure-active-directory-b2c"></a>자습서: Azure Active Directory B2C에 응용 프로그램 등록

응용 [프로그램이](application-types.md) Azure Active Directory B2C(Azure AD B2C)와 상호 작용하려면 먼저 관리하는 테넌트에 등록해야 합니다. 이 자습서에서는 Azure Portal을 사용하여 웹 애플리케이션을 등록하는 방법을 보여줍니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * 웹 애플리케이션 등록
> * 클라이언트 비밀 만들기

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

아직 자체 [Azure AD B2C 테넌트를](tutorial-create-tenant.md)만들지 않은 경우 지금 생성합니다. 기존 Azure AD B2C 테넌트를 사용해도 됩니다.

## <a name="register-a-web-application"></a>웹 애플리케이션 등록

Azure AD B2C 테넌트에 애플리케이션을 등록하기 위해 현재 **애플리케이션** 환경 또는 새로운 통합 **앱 등록(미리 보기)** 환경을 사용할 수 있습니다. [새 환경에 대해 자세히 알아보세요](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[애플리케이션](#tab/applications/)

1. [Azure 포털에](https://portal.azure.com)로그인합니다.
1. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택한 다음 Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.
1. Azure 포털에서 **Azure AD B2C를**검색하고 선택합니다.
1. **애플리케이션**을 선택하고 **추가**를 선택합니다.
1. 애플리케이션의 이름을 입력합니다. 예를 들어 *webapp1*과 같습니다.
1. **웹앱/웹 API 포함** 및 **암시적 흐름 허용**에 **예**를 선택합니다.
1. **회신 URL**에는 Azure AD B2C에서 애플리케이션이 요청한 토큰을 반환하는 엔드포인트를 입력합니다. 예를 들어 `https://localhost:44316`에서 로컬로 수신하도록 설정할 수 있습니다. 포트 번호를 아직 모르는 경우 자리 표시자 값을 입력한 후 나중에 변경할 수 있습니다.

    이 자습서와 같은 테스트 목적을 `https://jwt.ms` 위해 검사를 위해 토큰의 내용을 표시하는 것으로 설정할 수 있습니다. 이 자습서의 경우 **회신 URL을** 으로 설정합니다. `https://jwt.ms`

    회신 URL에는 다음과 같은 제한 사항이 적용됩니다.

    * 회신 URL은 스키마로 `https`시작해야 합니다.
    * 회신 URL은 대/소문자를 구분합니다. 해당 케이스는 실행 중인 응용 프로그램의 URL 경로의 대/소문자와 일치해야 합니다. 예를 들어 응용 프로그램이 경로의 `.../abc/response-oidc`일부로 포함된 경우 `.../ABC/response-oidc` 회신 URL에 지정하지 마십시오. 웹 브라우저는 경로를 대/소문자 구분으로 처리하므로 대/소문자 가 일치하지 않는 `.../abc/response-oidc` `.../ABC/response-oidc` URL로 리디렉션되는 경우 연결된 쿠키가 제외될 수 있습니다.

1. 응용 프로그램 등록을 완료하려면 **만들기를** 선택합니다.

#### <a name="app-registrations-preview"></a>[앱 등록(미리 보기)](#tab/app-reg-preview/)

1. [Azure 포털에](https://portal.azure.com)로그인합니다.
1. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택한 다음 Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.
1. Azure 포털에서 **Azure AD B2C를**검색하고 선택합니다.
1. **앱 등록(미리 보기)** 을 선택한 다음, **새 등록**을 선택합니다.
1. 애플리케이션의 **이름**을 입력합니다. 예를 들어 *webapp1*과 같습니다.
1. **모든 조직 디렉터리 또는 ID 공급자에서 계정을**선택합니다.
1. **URI 리디렉션에서** **웹을**선택한 다음 `https://jwt.ms` URL 텍스트 상자에 입력합니다.

    리디렉션 URI는 사용자와의 상호 작용을 완료한 후 권한 부여 서버(이 경우 Azure AD B2C)에서 사용자가 전송되고 성공적인 권한 부여 시 액세스 토큰 또는 권한 부여 코드가 전송되는 끝점입니다. 프로덕션 응용 프로그램에서는 일반적으로 `https://contoso.com/auth-response`앱이 실행 중인 공개적으로 액세스할 수 있는 끝점입니다. 이 자습서와 같은 테스트 목적을 위해 `https://jwt.ms`토큰의 디코딩된 내용을 표시하는 Microsoft 소유 웹 응용 프로그램(토큰의 내용은 브라우저를 떠나지 마십시오)으로 설정할 수 있습니다. 앱 을 개발하는 동안 `https://localhost:5000`응용 프로그램이 로컬에서 수신하는 끝점을 추가할 수 있습니다. 등록된 응용 프로그램에서 언제든지 리디렉션 URI를 추가하고 수정할 수 있습니다.

    리디렉션 URI에는 다음과 같은 제한 사항이 적용됩니다.

    * 회신 URL은 스키마로 `https`시작해야 합니다.
    * 회신 URL은 대/소문자를 구분합니다. 해당 케이스는 실행 중인 응용 프로그램의 URL 경로의 대/소문자와 일치해야 합니다. 예를 들어 응용 프로그램이 경로의 `.../abc/response-oidc`일부로 포함된 경우 `.../ABC/response-oidc` 회신 URL에 지정하지 마십시오. 웹 브라우저는 경로를 대/소문자 구분으로 처리하므로 대/소문자 가 일치하지 않는 `.../abc/response-oidc` `.../ABC/response-oidc` URL로 리디렉션되는 경우 연결된 쿠키가 제외될 수 있습니다.

1. **사용 권한** 아래에서 *openid 및 offline_access 권한에 대한 관리자 동의 허용* 확인란을 선택합니다.
1. **등록을**선택합니다.

응용 프로그램 등록이 완료되면 암시적 권한 부여 흐름을 사용하도록 설정합니다.

1. **관리**에서 **인증**을 선택합니다.
1. **새 환경을 체험해 보세요.**(표시되는 경우)를 선택합니다.
1. **암시적 부여에서** **Access 토큰과** **ID 토큰 확인란을** 모두 선택합니다.
1. **저장**을 선택합니다.

* * *

## <a name="create-a-client-secret"></a>클라이언트 비밀 만들기

응용 프로그램이 액세스 토큰에 대한 권한 부여 코드를 교환하는 경우 응용 프로그램 암호를 만들어야 합니다.

#### <a name="applications"></a>[애플리케이션](#tab/applications/)

1. Azure **AD B2C - 응용 프로그램** 페이지에서 *웹app1*과 같은 만든 응용 프로그램을 선택합니다.
1. **키를** 선택한 다음 **키 생성을**선택합니다.
1. **저장**을 선택하여 키를 확인합니다. **앱 키** 값을 기록해 둡니다. 이 값을 응용 프로그램 코드의 응용 프로그램 암호로 사용합니다.

#### <a name="app-registrations-preview"></a>[앱 등록(미리 보기)](#tab/app-reg-preview/)

1. Azure **AD B2C - 앱 등록(미리 보기)** 페이지에서 *웹app1*과 같은 만든 응용 프로그램을 선택합니다.
1. **관리**에서 **인증서 및 암호**를 선택합니다.
1. **새 클라이언트 비밀**을 선택합니다.
1. **설명** 상자에 클라이언트 암호에 대한 설명을 입력합니다. 예: *clientsecret1*.
1. **만료**에서 암호가 유효한 기간을 선택한 다음 **추가**를 선택합니다.
1. 비밀의 **값**을 기록합니다. 이 값을 응용 프로그램 코드의 응용 프로그램 암호로 사용합니다.

* * *

## <a name="next-steps"></a>다음 단계

이 문서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 웹 애플리케이션 등록
> * 클라이언트 비밀 만들기

그런 다음 사용자가 프로필을 등록하고 로그인하고 관리할 수 있도록 사용자 흐름을 만드는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [Azure Active Directory B2C >사용자 흐름 만들기](tutorial-create-user-flows.md)
