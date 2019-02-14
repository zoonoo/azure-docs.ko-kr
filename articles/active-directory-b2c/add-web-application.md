---
title: 웹 애플리케이션 추가 - Azure Active Directory B2C | Microsoft Docs
description: Active Directory B2C 테넌트에 웹 애플리케이션을 추가하는 방법을 알아봅니다.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.author: davidmu
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: conceptual
ms.service: active-directory
ms.component: B2C
ms.openlocfilehash: c20f455a0a325dadd3eeeb77dea7026de4834c56
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/06/2019
ms.locfileid: "55757357"
---
# <a name="add-a-web-api-application-to-your-azure-active-directory-b2c-tenant"></a>Azure Active Directory B2C 테넌트에 웹 API 애플리케이션 추가

액세스 토큰을 제공하는 클라이언트 애플리케이션을 통해 보호된 리소스 요청을 수락하고 응답하려면 먼저 웹 API 리소스를 테넌트에 등록해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. Azure AD B2C 테넌트를 포함하는 디렉터리를 사용하려면 위쪽 메뉴에서 **디렉터리 및 구독 필터**를 클릭하고 테넌트가 포함된 디렉터리를 선택합니다.
3. Azure Portal의 왼쪽 위에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
4. **응용 프로그램**을 선택하고 **추가**를 선택합니다.
5. 애플리케이션의 이름을 입력합니다. 예를 들어 *webapi1*과 같습니다.
6. **웹앱/웹 API 포함** 및 **암시적 흐름 허용**에 **예**를 선택합니다.
7. **회신 URL**에는 Azure AD B2C에서 애플리케이션이 요청한 토큰을 반환하는 엔드포인트를 입력합니다. 이 자습서의 샘플은 로컬에서 실행되고 `https://localhost:44332`에서 수신 대기합니다.
8. **앱 ID URI**에 웹 API에 사용하는 식별자를 입력합니다. 도메인을 포함하는 전체 식별자 URI가 생성됩니다. 예: `https://contosotenant.onmicrosoft.com/api`
9. **만들기**를 클릭합니다.
10. 속성 페이지에서 웹 애플리케이션을 구성할 때 사용할 애플리케이션 ID를 기록합니다.

## <a name="configure-scopes"></a>범위 구성

범위는 보호된 리소스에 대한 액세스를 제어하는 방법을 제공합니다. 범위는 웹 API에서 범위 기반 액세스 제어를 구현하는 데 사용됩니다. 예를 들어 웹 API 사용자가 읽기 및 쓰기 액세스 권한을 모두 갖고 있을 수도 있고, 읽기 권한만 갖고 있을 수도 있습니다. 이 자습서에서는 범위를 사용하여 웹 API에 대한 읽기 및 쓰기 권한을 정의합니다.

1. **애플리케이션**을 선택한 다음, *webapi1*을 선택합니다.
2. **게시된 범위**를 선택합니다.
3. **범위**에 `Hello.Read`를 입력하고, 설명에 `Read access to hello`를 입력합니다.
4. **범위**에 `Hello.Write`를 입력하고, 설명에 `Write access to hello`를 입력합니다.
5. **저장**을 클릭합니다.

게시된 범위는 클라이언트 애플리케이션 사용 권한을 웹 API에 부여하는 데 사용할 수 있습니다.

## <a name="grant-permissions"></a>권한 부여

애플리케이션에서 보호된 웹 API를 호출하려면 애플리케이션 사용 권한을 API에 부여해야 합니다. 필수 조건 자습서에서 *webapp1*이라는 웹 애플리케이션을 Azure AD B2C에 만들었습니다. 이 애플리케이션은 웹 API를 호출하는 데 사용됩니다.

1. **애플리케이션**을 선택한 다음, 웹 애플리케이션을 선택합니다.
2. **API 액세스**를 선택한 다음, **추가**를 선택합니다.
3. **API 선택** 드롭다운에서 *webapi1*을 선택합니다.
4. **범위 선택** 드롭다운에서 이전에 정의한 **Hello.Read** 및 **Hello.Write**를 선택합니다.
5. **확인**을 클릭합니다.

애플리케이션이 보호된 웹 API를 호출하도록 등록됩니다. 사용자가 Azure AD B2C로 인증하여 애플리케이션을 사용합니다. 애플리케이션은 Azure AD B2C에서 권한을 부여받아 보호된 웹 API에 액세스합니다.