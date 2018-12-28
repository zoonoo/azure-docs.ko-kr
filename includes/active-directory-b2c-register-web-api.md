---
author: PatAltimore
ms.service: active-directory-b2c
ms.topic: include
ms.date: 11/03/2016
ms.author: patricka
ms.openlocfilehash: fcd71f74e0b00934958828024094773e42496b66
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2018
ms.locfileid: "51017273"
---
[!INCLUDE [active-directory-b2c-portal-add-application](active-directory-b2c-portal-add-application.md)]

웹 API를 등록하려면 표에 지정된 설정을 사용합니다.

![새 웹 API에 대한 등록 설정 예](./media/active-directory-b2c-register-web-api/b2c-new-web-api-settings.png)

| 설정      | 샘플 값  | 설명                                        |
| ------------ | ------- | -------------------------------------------------- |
| **Name** | Contoso B2C API | 소비자에게 API를 설명하는 애플리케이션 **이름**을 입력합니다. | 
| **웹앱/웹 API 포함** | yes | 웹 API에 **예**를 선택합니다. |
| **암시적 흐름 허용** | yes | 애플리케이션이 [OpenID Connect 로그인](../articles/active-directory-b2c/active-directory-b2c-reference-oidc.md)을 사용할 경우 **예** 선택 |
| **회신 URL** | `https://localhost:44316/` | 회신 URL은 Azure AD B2C에서 애플리케이션이 요청한 토큰을 반환하는 엔드포인트입니다. 이 예에서는 앱 API가 로컬이고 포트 44316에서 수신하고 있습니다. |
| **앱 ID URI** | api | 앱 ID URI는 웹 API에 사용하는 식별자입니다. 도메인을 포함하는 전체 식별자 URI가 생성됩니다. |

**만들기**를 클릭하여 응용 프로그램을 등록합니다.

새로 등록된 애플리케이션은 B2C 테넌트에 대한 애플리케이션 목록에 표시됩니다. 목록에서 웹 API를 선택합니다. API의 속성 창이 표시됩니다.

![웹 API 속성](./media/active-directory-b2c-register-web-api/b2c-web-api-properties.png)

전역적으로 고유한 **애플리케이션 클라이언트 ID**를 기록합니다. 애플리케이션 코드의 ID를 사용합니다.