---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: 80e5166775b0cf5acbfce32e61d91c0889e3b086
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78186365"
---
Azure AD B2C 테넌트에 애플리케이션을 등록하기 위해 현재 **애플리케이션** 환경 또는 새로운 통합 **앱 등록(미리 보기)** 환경을 사용할 수 있습니다. [새 환경에 대해 자세히 알아보세요](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[애플리케이션](#tab/applications/)

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택한 다음, Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.
1. 왼쪽 메뉴에서 **Azure AD B2C**를 선택합니다. 또는 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
1. **애플리케이션**을 선택하고 **추가**를 선택합니다.
1. 애플리케이션의 이름을 입력합니다. 예를 들면 *testapp1-development*입니다.
1. **Web App / Web API**에 대해 **예**를 선택합니다.
1. **회신 URL**에 `https://jwt.ms`을 입력 합니다.
1. **만들기**를 선택합니다.

#### <a name="app-registrations-preview"></a>[앱 등록(미리 보기)](#tab/app-reg-preview/)

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택한 다음, Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.
1. 왼쪽 메뉴에서 **Azure AD B2C**를 선택합니다. 또는 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
1. **앱 등록(미리 보기)** 을 선택한 다음, **새 등록**을 선택합니다.
1. 애플리케이션의 **이름**을 입력합니다. 예를 들면 *testapp1-development*입니다.
1. **모든 조직 디렉터리 또는 모든 id 공급자에서 계정을**선택 합니다.
1. **URI 리디렉션**에서 **웹**을 선택한 다음 URL 텍스트 상자에 `https://jwt.ms`을 입력 합니다.
1. **사용 권한** 아래에서 *openid 및 offline_access 권한에 대한 관리자 동의 허용* 확인란을 선택합니다.
1. **등록**을 선택합니다.

응용 프로그램 등록이 완료 되 면 암시적 권한 부여 흐름을 사용 하도록 설정 합니다.

1. **관리**에서 **인증**을 선택합니다.
1. **새 환경을 체험해 보세요.** (표시되는 경우)를 선택합니다.
1. **암시적 권한 부여**에서 **액세스 토큰** 및 **ID 토큰** 확인란을 둘 다 선택합니다.
1. **저장**을 선택합니다.