---
title: 자습서 - Azure Active Directory B2C에서 애플리케이션 등록 | Microsoft Docs
description: Azure Portal를 사용하여 Azure Active Directory B2C에서 애플리케이션을 등록하는 방법을 알아봅니다.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 01/11/2019
ms.author: davidmu
ms.openlocfilehash: 99ad1bbaa732b1207ead9da8da36f345d4978241
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54856029"
---
# <a name="tutorial-register-your-applications-in-azure-active-directory-b2c"></a>자습서: Azure Active Directory B2C에서 애플리케이션 등록

[애플리케이션](active-directory-b2c-apps.md)이 Azure AD(Azure Active Directory) B2C와 상호 작용하려면 먼저 사용자가 관리하는 테넌트에 등록되어야 합니다. 이 자습서에서는 Azure Portal을 사용하여 애플리케이션을 등록하는 방법을 보여 줍니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * 웹 애플리케이션 등록
> * 웹 API 등록
> * 모바일 또는 원시 앱 등록

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>필수 조건

아직 고유한 [Azure AD B2C 테넌트](tutorial-create-tenant.md)를 만들지 않았다면 지금 만듭니다. 기존 테넌트를 사용할 수 있습니다.

## <a name="register-a-web-application"></a>웹 애플리케이션 등록

1. Azure AD B2C 테넌트를 포함하는 디렉터리를 사용하려면 위쪽 메뉴에서 **디렉터리 및 구독 필터**를 클릭하고 테넌트가 포함된 디렉터리를 선택합니다.

    ![구독 디렉터리로 전환](./media/tutorial-register-applications/switch-directories.png)

2. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
3. **응용 프로그램**을 선택하고 **추가**를 선택합니다.

    ![애플리케이션 추가](./media/tutorial-register-applications/add-application.png)

4. 애플리케이션의 이름을 입력합니다. 예를 들어 *webapp1*과 같습니다.
5. **웹앱/웹 API 포함** 및 **암시적 흐름 허용**에 **예**를 선택합니다.
6. **회신 URL**에는 Azure AD B2C에서 애플리케이션이 요청한 토큰을 반환하는 엔드포인트를 입력합니다. 예를 들어, `https://localhost:44316`에서 로컬로 수신하도록 설정할 수 있습니다. 포트 번호를 아직 모르는 경우 자리 표시자 값을 입력한 후 나중에 변경할 수 있습니다. 테스트 목적을 위해 조사할 토큰 내용을 표시하도록 `https://jwt.ms`로 설정할 수 있습니다. 이 자습서에서는 `https://jwt.ms`로 설정합니다. 

    회신 URL은 스키마 `https`로 시작해야 하고 모든 회신 URL 값은 단일 DNS 도메인을 공유해야 합니다. 예를 들어 애플리케이션의 회신 URL이 `https://login.contoso.com`이면 이 URL `https://login.contoso.com/new`와 같이 추가할 수 있습니다. 또는 `https://new.login.contoso.com`과 같이 `login.contoso.com`의 DNS 하위 도메인을 참조할 수 있습니다. `login-east.contoso.com` 및 `login-west.contoso.com`을 회신 URL로 사용하는 애플리케이션이 필요하면 회신 URL을 다음 순서로 추가해야 합니다. `https://contoso.com`, `https://login-east.contoso.com`, `https://login-west.contoso.com` 뒤의 두 개는 첫 번째 회신 URL `contoso.com`의 하위 도메인이므로 추가할 수 있습니다.

7. **만들기**를 클릭합니다.

    ![애플리케이션 속성 설정](./media/tutorial-register-applications/application-properties.png)

### <a name="create-a-client-secret"></a>클라이언트 비밀 만들기

애플리케이션이 코드를 토큰으로 교환하는 경우 애플리케이션 비밀을 만들어야 합니다.

1. **키**를 선택한 다음, **키 생성**을 클릭합니다.

    ![키 생성](./media/tutorial-register-applications/generate-keys.png)

2. **저장**을 선택하여 키를 확인합니다. **앱 키** 값을 기록해 둡니다. 이 값을 애플리케이션의 코드에서 애플리케이션 비밀로 사용합니다.

    ![키 저장](./media/tutorial-register-applications/save-key.png)
    
3. **API 액세스**를 선택하고 **추가**를 클릭한 다음, 웹 API와 범위(사용 권한)를 선택합니다.

    ![API 액세스 구성](./media/tutorial-register-applications/api-access.png)

## <a name="register-a-web-api"></a>웹 API 등록

1. **응용 프로그램**을 선택하고 **추가**를 선택합니다.
3. 애플리케이션의 이름을 입력합니다. 예를 들어 *webapi1*과 같습니다.
4. **웹앱/웹 API 포함** 및 **암시적 흐름 허용**에 **예**를 선택합니다.
5. **회신 URL**에는 Azure AD B2C에서 애플리케이션이 요청한 토큰을 반환하는 엔드포인트를 입력합니다. 예를 들어, `https://localhost:44316`에서 로컬로 수신 대기하도록 설정할 수 있습니다. 포트 번호를 아직 모르는 경우 자리 표시자 값을 입력한 후 나중에 변경할 수 있습니다.
6. **앱 ID URI**에 웹 API에 사용하는 식별자를 입력합니다. 도메인을 포함하는 전체 식별자 URI가 생성됩니다. 예: `https://contosotenant.onmicrosoft.com/api`
7. **만들기**를 클릭합니다.
8. 만든 *webapi1* 애플리케이션을 선택하고 **게시된 범위**를 선택하여 필요에 따라 더 많은 범위를 추가합니다. 기본적으로 `user_impersonation` 범위가 정의됩니다. `user_impersonation` 범위는 로그인한 사용자를 대신하여 다른 애플리케이션에게 이 API에 액세스할 수 있는 기능을 부여합니다. 필요하면 `user_impersonation` 범위를 제거할 수 있습니다.

    ![게시된 범위 설정](./media/tutorial-register-applications/published-scopes.png)


## <a name="register-a-mobile-or-native-application"></a>모바일 또는 원시 앱 등록

1. **응용 프로그램**을 선택하고 **추가**를 선택합니다.
2. 애플리케이션의 이름을 입력합니다. 예를 들어 *nativeapp1*과 같습니다.
3. **웹앱/웹 API 포함**에 **아니요**를 선택합니다.
4. **원시 클라이언트 포함**에 **예**를 선택합니다.
5. **리디렉션 URI**에 사용자 지정 체계를 사용하는 유효한 리디렉션 URI를 입력합니다. 리디렉션 URI를 선택하는 경우 다음과 같은 두 가지 중요한 고려 사항이 있습니다.

    - **고유** - 리디렉션 URI의 체계는 모든 애플리케이션에 대해 고유해야 합니다. 보기 `com.onmicrosoft.contoso.appname://redirect/path`에서 `com.onmicrosoft.contoso.appname`은 구성표입니다. 이 패턴을 따라야 합니다. 두 애플리케이션이 동일한 체계를 공유하는 경우 애플리케이션을 선택하기 위한 옵션이 제공됩니다. 사용자가 잘못된 항목을 선택하면 로그인이 실패합니다.
    - **전체** - 리디렉션 URI에는 체계 및 경로가 있어야 합니다. 경로는 도메인 다음에 하나 이상의 포워드 슬래시를 포함해야 합니다. 예를 들어 `//contoso/`는 실행되고 `//contoso`는 실행되지 않습니다. 리디렉션 URI가 밑줄 등의 특수 문자를 포함하지 않는지 확인합니다.

6. **만들기**를 클릭합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 웹 애플리케이션 등록
> * 웹 API 등록
> * 모바일 또는 원시 앱 등록

> [!div class="nextstepaction"]
> [Azure Active Directory B2C에서 사용자 흐름 만들기](tutorial-create-user-flows.md)