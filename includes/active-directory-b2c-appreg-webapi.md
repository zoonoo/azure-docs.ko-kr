---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: 9367719b1a1050ce8abf36804b3d0d0d58c7484b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2020
ms.locfileid: "78183386"
---
액세스 토큰을 제공하는 클라이언트 애플리케이션을 통해 보호된 리소스 요청을 수락하고 응답하려면 먼저 웹 API 리소스를 테넌트에 등록해야 합니다.

Azure AD B2C 테넌트에 애플리케이션을 등록하기 위해 현재 **애플리케이션** 환경 또는 새로운 통합 **앱 등록(미리 보기)** 환경을 사용할 수 있습니다. [새 환경에 대해 자세히 알아보세요](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[애플리케이션](#tab/applications/)

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택한 다음, Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.
1. 왼쪽 메뉴에서 **Azure AD B2C**를 선택합니다. 또는 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
1. **애플리케이션**을 선택하고 **추가**를 선택합니다.
1. 애플리케이션의 이름을 입력합니다. 예를 들어 *webapi1*과 같습니다.
1. **Web App / Web API**에 대해 **예**를 선택합니다.
1. **암시적 흐름 허용**에 대해 **예**를 선택합니다.
1. **회신 URL**에는 Azure AD B2C에서 애플리케이션이 요청한 토큰을 반환하는 엔드포인트를 입력합니다. 이 자습서의 샘플은 로컬에서 실행되고 `https://localhost:5000`에서 수신 대기합니다.
1. **앱 ID URI**의 경우 표시된 URI에 API 엔드포인트 식별자를 추가합니다. 이 자습서의 경우 전체 URI가 `api`와 비슷하도록 `https://contosob2c.onmicrosoft.com/api`를 입력합니다.
1. **만들기**를 선택합니다.
1. 이후 단계에서 사용할 **애플리케이션 ID**를 기록합니다.

#### <a name="app-registrations-preview"></a>[앱 등록(미리 보기)](#tab/app-reg-preview/)

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택한 다음, Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.
1. 왼쪽 메뉴에서 **Azure AD B2C**를 선택합니다. 또는 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
1. **앱 등록(미리 보기)** 을 선택한 다음, **새 등록**을 선택합니다.
1. 애플리케이션의 **이름**을 입력합니다. 예를 들어 *webapi1*과 같습니다.
1. **리디렉션 URI**에서 **Web**을 선택한 다음, Azure AD B2C에서 애플리케이션이 요청한 토큰을 반환해야 하는 엔드포인트를 입력합니다. 이 자습서의 샘플은 로컬에서 실행되고 `http://localhost:5000`에서 수신 대기합니다.
1. **등록**을 선택합니다.
1. 이후 단계에서 사용할 수 있게 **애플리케이션(클라이언트) ID**를 기록합니다.

다음으로, 암시적 권한 부여 흐름을 사용하도록 설정합니다.

1. **관리**에서 **인증**을 선택합니다.
1. **새 환경을 체험해 보세요.** (표시되는 경우)를 선택합니다.
1. **암시적 권한 부여**에서 **액세스 토큰** 및 **ID 토큰** 확인란을 둘 다 선택합니다.
1. **저장**을 선택합니다.