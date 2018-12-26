---
title: Azure Active Directory B2C에서 애플리케이션 등록 | Microsoft Docs
description: Azure Active Directory B2C에 애플리케이션을 등록하는 방법을 알아봅니다.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 8068c4a8f38cd33a1a0547f5db5079bc75c76ec1
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2018
ms.locfileid: "51013414"
---
# <a name="register-an-application-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 애플리케이션 등록

소비자 등록 및 로그인을 수락하는 [애플리케이션](active-directory-b2c-apps.md)을 만들려면 먼저 Azure AD B2C 테넌트를 사용하여 해당 애플리케이션을 등록해야 합니다. 이 문서를 통해 몇 분 이내에 Azure Active Directory(Azure AD) B2C 테넌트에서 애플리케이션을 등록할 수 있습니다. 완료되면 Azure AD B2C 테넌트에서 사용할 수 있도록 응용 프로그램이 등록됩니다.

## <a name="prerequisites"></a>필수 조건

[Azure Active Directory B2C 테넌트 만들기](tutorial-create-tenant.md)의 단계를 사용하여 자신의 테넌트를 가져옵니다.

애플리케이션 유형에 따라 다음 단계 선택

- [웹 응용 프로그램 등록](#register-a-web-application)
- [웹 API 등록](#register-a-web-api)
- [모바일 또는 네이티브 앱 등록](#register-a-mobile-or-native-application)

## <a name="register-a-web-application"></a>웹 응용 프로그램 등록

1. Azure AD B2C 테넌트를 포함하는 디렉터리를 사용하려면 위쪽 메뉴에서 **디렉터리 및 구독 필터**를 클릭하고 테넌트가 포함된 디렉터리를 선택합니다.
2. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
3. **응용 프로그램**을 선택하고 **추가**를 선택합니다.
4. 응용 프로그램의 이름을 입력합니다. 예: *testapp1*
5. **웹앱/웹 API 포함** 및 **암시적 흐름 허용**에 **예**를 선택합니다.
6. **회신 URL**에는 Azure AD B2C에서 앱이 요청한 토큰을 반환하는 엔드포인트를 입력합니다. 예를 들어, `https://localhost:44316`에서 로컬로 수신 대기하도록 설정할 수 있습니다. 포트 번호를 아직 모르는 경우 자리 표시자 값을 입력한 후 나중에 변경할 수 있습니다.
7. **만들기**를 클릭합니다.

### <a name="create-a-client-secret"></a>클라이언트 비밀 만들기

애플리케이션에서 Azure AD B2C로 보호되는 웹 API를 호출할 경우 애플리케이션 비밀을 만들어야 합니다.

1. **키**를 선택한 다음, **키 생성**을 클릭합니다. 
2. **저장**을 선택하여 키를 확인합니다. **앱 키** 값을 기록해 둡니다. 이 값을 응용 프로그램의 코드에서 응용 프로그램 비밀로 사용합니다.
3. **API 액세스**를 선택하고 **추가**를 클릭한 다음, 웹 API와 범위(사용 권한)를 선택합니다.

## <a name="register-a-web-api"></a>웹 API 등록

1. Azure AD B2C 테넌트를 포함하는 디렉터리를 사용하려면 위쪽 메뉴에서 **디렉터리 및 구독 필터**를 클릭하고 테넌트가 포함된 디렉터리를 선택합니다.
2. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
3. **응용 프로그램**을 선택하고 **추가**를 선택합니다.
4. 응용 프로그램의 이름을 입력합니다. 예: *testapp2*.
5. **웹앱/웹 API 포함** 및 **암시적 흐름 허용**에 **예**를 선택합니다.
6. **회신 URL**에는 Azure AD B2C에서 앱이 요청한 토큰을 반환하는 엔드포인트를 입력합니다. 예를 들어, `https://localhost:44316`에서 로컬로 수신 대기하도록 설정할 수 있습니다. 포트 번호를 아직 모르는 경우 자리 표시자 값을 입력한 후 나중에 변경할 수 있습니다.
7. **앱 ID URI**에 웹 API에 사용하는 식별자를 입력합니다. 도메인을 포함하는 전체 식별자 URI가 생성됩니다. 예: `https://contosotenant.onmicrosoft.com/api`
8. **만들기**를 클릭합니다.
9. **게시된 범위**를 선택하여 필요에 따라 범위를 더 추가합니다. 기본적으로 `user_impersonation` 범위가 정의됩니다. `user_impersonation` 범위는 로그인한 사용자를 대신하여 다른 애플리케이션에게 이 API에 액세스할 수 있는 기능을 부여합니다. 필요하면 `user_impersonation` 범위를 제거할 수 있습니다.

## <a name="register-a-mobile-or-native-application"></a>모바일 또는 원시 앱 등록

1. Azure AD B2C 테넌트를 포함하는 디렉터리를 사용하려면 위쪽 메뉴에서 **디렉터리 및 구독 필터**를 클릭하고 테넌트가 포함된 디렉터리를 선택합니다.
2. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
3. **응용 프로그램**을 선택하고 **추가**를 선택합니다.
4. 응용 프로그램의 이름을 입력합니다. 예: *testapp3*.
5. **웹앱/웹 API 포함**에 **아니요**를 선택합니다.
6. **원시 클라이언트 포함**에 **예**를 선택합니다.
7. **리디렉션 URI**에 [사용자 지정 체계로 리디렉션 URI](active-directory-b2c-apps.md)를 입력합니다. 적절한 리디렉션 URI를 선택하고 밑줄 등의 특수 문자를 포함하지 않습니다.
8. **만들기**를 클릭합니다.

### <a name="create-a-client-secret"></a>클라이언트 비밀 만들기

애플리케이션에서 Azure AD B2C로 보호되는 웹 API를 호출할 경우 애플리케이션 비밀을 만들어야 합니다.

1. **키**를 선택한 다음, **키 생성**을 클릭합니다. 
2. **저장**을 선택하여 키를 확인합니다. **앱 키** 값을 기록해 둡니다. 이 값을 응용 프로그램의 코드에서 응용 프로그램 비밀로 사용합니다.
3. **API 액세스**를 선택하고 **추가**를 클릭한 다음, 웹 API와 범위(사용 권한)를 선택합니다.

## <a name="next-steps"></a>다음 단계

Azure AD B2C에 등록된 응용 프로그램이 있으니 [빠른 시작 자습서](active-directory-b2c-overview.md) 중 하나를 완료하여 실행할 수 있습니다.

> [!div class="nextstepaction"]
> [등록, 로그인 및 암호 재설정으로 ASP.NET 웹앱 만들기](active-directory-b2c-devquickstarts-web-dotnet-susi.md)
