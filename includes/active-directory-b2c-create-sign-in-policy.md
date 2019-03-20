---
author: PatAltimore
ms.service: active-directory-b2c
ms.topic: include
ms.date: 11/30/2018
ms.author: patricka
ms.openlocfilehash: 0ab34d6234db9c13ffe82ccd0e8580217085f631
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/16/2019
ms.locfileid: "58114350"
---
애플리케이션에서 로그인만 사용하려면 **로그인** 사용자 흐름을 사용합니다. 이 사용자 흐름은 로그인 시 고객이 경험하는 환경 및 로그인 성공 시 애플리케이션이 수신하는 토큰의 콘텐츠에 대해 설명합니다.

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]
**관리**에서 **사용자 흐름**을 선택합니다.

블레이드의 맨 위에서 + **새 사용자 흐름**을 클릭합니다.

**사용자 흐름 유형 선택**에서 **모두**를 선택한 다음, 사용할 **로그인** 버전을 선택합니다.

**이름**은 애플리케이션에서 사용하는 로그인 사용자 흐름 이름을 결정합니다. 예를 들어 **SiIn**을 입력합니다.

**ID 공급자**에서 옵션을 선택합니다. 이미 구성되어 있는 경우 소셜 ID 공급자를 선택할 수도 있습니다. **확인**을 클릭합니다.

**애플리케이션 클레임**에서 **자세히 표시**를 클릭합니다.

**클레임 반환** 열에서 로그인에 성공한 후 애플리케이션으로 다시 전송되는 토큰에 반환하려는 클레임을 선택합니다. 예를 들어 **표시 이름**, **ID 공급자**, **우편 번호** 및 **사용자의 개체 ID**를 선택합니다. **확인**을 클릭합니다.

**만들기**를 클릭합니다. 방금 만든 사용자 흐름은 **B2C_1_SiIn**으로 표시됩니다(**B2C\_1\_** 조각이 자동으로 추가됨).

**사용자 흐름 실행**을 클릭합니다.

**애플리케이션** 드롭다운에서 **Contoso B2C 앱**을 선택하고, **회신 URL** 드롭다운에서 `https://localhost:44321/`을 선택합니다.

**사용자 흐름 실행**을 클릭합니다. 새 브라우저 탭이 열리고 애플리케이션에 로그인한 사용자 환경을 실행할 수 있습니다.

> [!NOTE]
> 사용자 흐름 만들기 및 업데이트가 적용되려면 최대 1분이 걸립니다.
>