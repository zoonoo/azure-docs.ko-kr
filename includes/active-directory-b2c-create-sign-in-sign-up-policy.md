---
author: PatAltimore
ms.service: active-directory-b2c
ms.topic: include
ms.date: 11/30/2018
ms.author: patricka
ms.openlocfilehash: f23d2b02bc2a23c5333a48a50532c03f3aa6a031
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2018
ms.locfileid: "52742402"
---
[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]

**관리**에서 **사용자 흐름**을 선택하고 +**새 사용자 흐름**을 클릭합니다.

![새 사용자 흐름 선택](media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-user-flow.png)

**추천** 탭에서 **가입 및 로그인**을 선택합니다.

![가입 및 로그인 사용자 흐름 선택](media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-user-flow-type.png)

참조할 애플리케이션에 대한 사용자 흐름 **이름**을 입력합니다. 예를 들어 `SiUpIn`을 입력합니다.

**ID 공급자**에서 **메일 가입**을 선택합니다. 또한 필요에 따라 이미 구성되어 있는 소셜 ID 공급자를 선택할 수 있습니다.

**다단계 인증**에서 **사용** 또는 **사용 안 함**을 선택합니다.

![이름 입력 및 ID 공급자로 메일 가입 선택](media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-name-identity-providers.png)

**사용자 특성 및 클레임**에서 **자세히 표시**를 선택하여 선택할 수 있는 특성 및 클레임의 전체 목록을 표시합니다.

**특성 수집** 열에서 가입 중에 고객에게서 수집하려는 특성을 선택합니다. 예를 들어 **국가/지역**, **표시 이름** 및 **우편 번호**를 선택합니다.

**클레임 반환** 열에서 성공적인 가입 또는 로그인 환경 이후에 애플리케이션으로 다시 전송된 권한 부여 토큰에서 반환하려는 클레임을 선택합니다. 예를 들어 **표시 이름**, **ID 공급자**, **우편 번호**, **새 사용자** 및 **사용자의 개체 ID**를 선택합니다.

**확인**을 클릭합니다.

![일부 사용자 특성 및 클레임을 선택하고 확인 단추를 클릭합니다.](media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-sign-up-all-attributes.png)

**만들기**를 클릭하여 사용자 흐름을 추가합니다. 사용자 흐름은 **B2C_1_SiUpIn**으로 표시됩니다. **B2C_1_** 접두사가 이름에 추가됩니다.

**사용자 흐름 실행**을 선택합니다. 표에 지정된 설정을 확인하고 **사용자 흐름 실행**을 클릭합니다.

![사용자 흐름 실행 선택](media/active-directory-b2c-create-sign-in-sign-up-policy/run-user-flow-b2c-signup-signin.png)

| 설정      | 값  |
| ------------ | ------ |
| **응용 프로그램** | Contoso B2C 앱 |
| **회신 URL** | `https://localhost:44316/` |

새 브라우저 탭이 열리고 구성된 대로 등록 또는 로그인 소비자 환경을 실행할 수 있습니다.

> [!NOTE]
> 사용자 흐름 만들기 및 업데이트가 적용되려면 최대 1분이 걸립니다.
>