---
title: 자습서-응용 프로그램 등록-Azure Active Directory B2C
description: Azure Portal를 사용하여 Azure Active Directory B2C에서 웹 애플리케이션을 등록하는 방법을 알아봅니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 08/23/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 06b2c273f41bfa74ee968b6fd6676e83767ce8b2
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71063262"
---
# <a name="tutorial-register-an-application-in-azure-active-directory-b2c"></a>자습서: Azure Active Directory B2C에서 애플리케이션 등록

[응용 프로그램](active-directory-b2c-apps.md) 은 Azure Active Directory B2C (Azure AD B2C)와 상호 작용 하기 전에 관리 하는 테 넌 트에 등록 되어야 합니다. 이 자습서에서는 Azure Portal을 사용하여 웹 애플리케이션을 등록하는 방법을 보여줍니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * 웹 애플리케이션 등록
> * 클라이언트 비밀 만들기

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>필수 구성 요소

아직 고유한 [Azure AD B2C 테넌트](tutorial-create-tenant.md)를 만들지 않았다면 지금 만듭니다. 기존 Azure AD B2C 테넌트를 사용해도 됩니다.

## <a name="register-a-web-application"></a>웹 애플리케이션 등록

1. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택 하 고 테 넌 트가 포함 된 디렉터리를 선택 하 여 Azure AD B2C 테 넌 트를 포함 하는 디렉터리를 사용 하 고 있는지 확인 합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
1. **애플리케이션**을 선택하고 **추가**를 선택합니다.
1. 애플리케이션의 이름을 입력합니다. 예를 들어 *webapp1*과 같습니다.
1. **웹앱/웹 API 포함** 및 **암시적 흐름 허용**에 **예**를 선택합니다.
1. **회신 URL**에는 Azure AD B2C에서 애플리케이션이 요청한 토큰을 반환하는 엔드포인트를 입력합니다. 예를 들어에서 `https://localhost:44316`로컬로 수신 하도록 설정할 수 있습니다. 포트 번호를 아직 모르는 경우 자리 표시자 값을 입력한 후 나중에 변경할 수 있습니다.

    이 자습서와 같은 테스트를 위해 검사를 위해 토큰 `https://jwt.ms` 의 내용을 표시 하는로 설정할 수 있습니다. 이 자습서에서는 **회신 URL** 을로 `https://jwt.ms`설정 합니다.

    회신 Url에는 다음과 같은 제한 사항이 적용 됩니다.

    * 회신 URL은 스키마 `https`로 시작 해야 합니다.
    * 회신 URL은 대/소문자를 구분 합니다. 해당 사례는 실행 중인 응용 프로그램의 URL 경로에 대 한 대/소문자와 일치 해야 합니다. 예를 들어 응용 프로그램의 경로 `.../abc/response-oidc`를 포함 하는 경우 회신 URL에를 지정 `.../ABC/response-oidc` 하지 마십시오. 웹 브라우저에서 경로를 대/소문자를 구분 하므로에 연결 `.../abc/response-oidc` 된 쿠키는 대/소문자가 일치 하지 않는 `.../ABC/response-oidc` URL로 리디렉션되는 경우 제외 될 수 있습니다.

1. **만들기** 를 클릭 하 여 응용 프로그램 등록을 완료 합니다.

## <a name="create-a-client-secret"></a>클라이언트 비밀 만들기

응용 프로그램에서 토큰에 대 한 코드를 교환 하는 경우 응용 프로그램 암호를 만들어야 합니다.

1. **응용 프로그램 Azure AD B2C** 페이지에서 사용자가 만든 응용 프로그램을 선택 합니다 (예: *webapp1*).
1. **키** 를 선택 하 고 **키 생성**을 선택 합니다.
1. **저장**을 선택하여 키를 확인합니다. **앱 키** 값을 기록해 둡니다. 응용 프로그램 코드에서이 값을 응용 프로그램 암호로 사용 합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 웹 애플리케이션 등록
> * 클라이언트 비밀 만들기

다음으로 사용자가 자신의 프로필에 등록, 로그인 및 관리할 수 있도록 사용자 흐름을 만드는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [Azure Active Directory B2C >에서 사용자 흐름 만들기](tutorial-create-user-flows.md)
