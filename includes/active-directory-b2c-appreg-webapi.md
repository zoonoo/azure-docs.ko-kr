---
author: mmacy
ms.service: active-directory-b2c
ms.topic: include
ms.date: 09/30/2019
ms.author: marsma
ms.openlocfilehash: 4ac69b51e69031a1a4ce83a2c09dc6cc010ccd62
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71720269"
---
액세스 토큰을 제공하는 클라이언트 애플리케이션을 통해 보호된 리소스 요청을 수락하고 응답하려면 먼저 웹 API 리소스를 테넌트에 등록해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택한 다음, Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.
1. 왼쪽 메뉴에서 **Azure AD B2C**를 선택합니다. 또는 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
1. **애플리케이션**을 선택하고 **추가**를 선택합니다.
1. 애플리케이션의 이름을 입력합니다. 예를 들어 *webapi1*과 같습니다.
1. **Web App / Web API**에 대해 **예**를 선택합니다.
1. **암시적 흐름 허용**에 대해 **예**를 선택합니다.
1. **회신 URL**에는 Azure AD B2C에서 애플리케이션이 요청한 토큰을 반환하는 엔드포인트를 입력합니다. 이 자습서의 샘플은 로컬에서 실행되고 `https://localhost:5000`에서 수신 대기합니다.
1. **앱 ID URI**의 경우 표시된 URI에 API 엔드포인트 식별자를 추가합니다. 이 자습서의 경우 전체 URI가 `https://contosob2c.onmicrosoft.com/api`와 비슷하도록 `api`를 입력합니다.
1. **만들기**를 선택합니다.
1. 이후 단계에서 사용할 **애플리케이션 ID**를 기록합니다.
