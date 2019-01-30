---
author: PatAltimore
ms.service: active-directory-b2c
ms.topic: include
ms.date: 11/30/2018
ms.author: patricka
ms.openlocfilehash: 0d9f0a24d84bd18bdf1fac84c744cc34a7d89ab3
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2018
ms.locfileid: "52742343"
---
애플리케이션에서 프로필 편집을 사용하도록 설정하려면 **프로필 편집** 사용자 흐름을 사용합니다. 이 사용자 흐름은 프로필을 편집하는 동안 고객이 경험하게 될 환경 및 성공적으로 완료 시 애플리케이션이 수신하게 될 토큰의 콘텐츠에 대해 설명합니다.

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]

**관리**에서 **사용자 흐름**을 선택하고 +**새 사용자 흐름**을 클릭합니다.

![새 사용자 흐름 선택](media/active-directory-b2c-create-profile-editing-policy/add-b2c-new-user-flow.png)

**추천** 탭에서 **프로필 편집**을 선택합니다.

참조할 애플리케이션에 대한 사용자 흐름 **이름**을 입력합니다. 예를 들어 `SiPe`을 입력합니다.

**ID 공급자** 아래에서 **로컬 계정 로그인**을 선택합니다. 또한 필요에 따라 이미 구성되어 있는 소셜 ID 공급자를 선택할 수 있습니다.

![ID 공급자로 로컬 계정 등록을 선택하고 확인 단추를 클릭합니다.](media/active-directory-b2c-create-profile-editing-policy/add-b2c-profile-editing-identity-providers.png)

**사용자 특성**에서 **자세히 표시**를 클릭합니다. **특성 수집** 열에서 소비자가 프로필을 통해 보고 편집할 수 있는 특성을 선택합니다. 예를 들어 **국가/지역**, **표시 이름** 및 **우편 번호**를 선택합니다.

**클레임 반환** 열에서 프로필 편집에 성공한 후 애플리케이션으로 다시 전송되는 인증 토큰에 반환하려는 클레임을 선택합니다. 예를 들어 **표시 이름**, **우편 번호**를 선택합니다.

**확인**을 클릭합니다.

![일부 애플리케이션 클레임을 선택하고 확인 단추를 클릭합니다.](media/active-directory-b2c-create-profile-editing-policy/add-b2c-user-attributes.png)

**만들기**를 클릭하여 사용자 흐름을 추가합니다. 사용자 흐름은 **B2C_1_SiPe**로 표시됩니다. **B2C_1_** 접두사가 이름에 추가됩니다.

**사용자 흐름 실행**을 선택합니다. 표에 지정된 설정을 확인하고 **사용자 흐름 실행**을 클릭합니다.

![사용자 흐름을 선택하고 실행합니다.](media/active-directory-b2c-create-profile-editing-policy/add-b2c-profile-editing-run-user-flow.png)

| 설정      | 값  |
| ------------ | ------ |
| **응용 프로그램** | Contoso B2C 앱 |
| **회신 URL** | `https://localhost:44316/` |

새 브라우저 탭이 열리고 구성된 프로필 편집 소비자 환경을 확인할 수 있습니다.

> [!NOTE]
> 사용자 흐름 만들기 및 업데이트가 적용되려면 최대 1분이 걸립니다.
>