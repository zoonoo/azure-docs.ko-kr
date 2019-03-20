---
author: PatAltimore
ms.service: active-directory-b2c
ms.topic: include
ms.date: 11/30/2018
ms.author: patricka
ms.openlocfilehash: 78abb190dccd27c5bf70dfe12f978e1118601815
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/16/2019
ms.locfileid: "58114871"
---
애플리케이션에서 세분화된 암호 재설정을 사용하려면 **암호 재설정** 사용자 흐름을 사용합니다. [여기](../articles/active-directory-b2c/active-directory-b2c-reference-sspr.md)에서는 테넌트 범위의 암호 재설정 옵션이 지정되어 있습니다. 이 사용자 흐름은 암호를 재설정하는 동안 고객이 경험하게 될 환경 및 성공적으로 완료 시 애플리케이션이 수신하게 될 토큰의 콘텐츠에 대해 설명합니다.

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]

**관리**에서 **사용자 흐름**을 선택하고 +**새 사용자 흐름**을 클릭합니다.

![새 사용자 흐름 선택](media/active-directory-b2c-create-password-reset-policy/add-b2c-new-user-flow.png)

**추천** 탭에서 **암호 재설정**을 선택합니다.

참조할 애플리케이션에 대한 사용자 흐름 **이름**을 입력합니다. 예를 들어 `SSPR`을 입력합니다.

**ID 공급자**에서 **메일 주소를 사용하여 암호 재설정**을 선택합니다.

![이름을 입력하고 메일 주소를 사용하여 암호 재설정을 ID 공급자로 선택합니다.](media/active-directory-b2c-create-password-reset-policy/add-b2c-password-reset-identity-providers.png)

**애플리케이션 클레임**에서 **자세히 표시**를 클릭하고, 암호 재설정에 성공하면 애플리케이션으로 다시 전송되는 인증 토큰에 반환하려는 클레임을 선택합니다. 예를 들어 **사용자의 개체 ID**를 선택합니다.

**확인**을 클릭합니다.

![일부 애플리케이션 클레임을 선택하고 확인 단추를 클릭합니다.](media/active-directory-b2c-create-password-reset-policy/add-b2c-password-reset-application-claims.png)

**만들기**를 클릭하여 사용자 흐름을 추가합니다. 사용자 흐름은 **B2C_1_SSPR**로 표시됩니다. **B2C_1_** 접두사가 이름에 추가됩니다.

**사용자 흐름 실행**을 클릭합니다. 표에 지정된 설정을 확인하고 **사용자 흐름 실행**을 클릭합니다.

![사용자 흐름을 선택하고 실행합니다.](media/active-directory-b2c-create-password-reset-policy/add-b2c-sspr-run-user-flow.png)

| 설정      | 값  |
| ------------ | ------ |
| **애플리케이션** | Contoso B2C 앱 |
| **회신 URL 선택** | `https://localhost:44316/` |

새 브라우저 탭이 열리고 애플리케이션에서 암호 재설정 사용자 환경을 확인할 수 있습니다.

> [!NOTE]
> 정책 만들기 및 업데이트가 적용되려면 최대 1분이 걸립니다.
>
