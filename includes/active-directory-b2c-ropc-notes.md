---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/27/2020
ms.author: mimart
ms.openlocfilehash: f4944881e6eff3e9a56a74bc997c280f9fff6be8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "78187004"
---
## <a name="ropc-flow-notes"></a>ROPC 흐름 노트
Azure AD B2C(Azure Active Directory B2C)에서 지원되는 옵션은 다음과 같습니다.

- **네이티브 클라이언트**: 코드가 사용자 쪽 디바이스에서 실행될 때 인증 중에 사용자 상호 작용이 발생합니다. 이 디바이스는 Android 및 iOS와 같은 네이티브 운영 체제에서 실행되는 모바일 애플리케이션일 수 있습니다.
- **공용 클라이언트 흐름**: 애플리케이션에서 수집한 사용자 자격 증명만 API 호출에서 전송됩니다. 애플리케이션의 자격 증명은 전송되지 않습니다.
- **새 클레임 추가**: ID 토큰 콘텐츠를 변경하여 새 클레임을 추가할 수 있습니다.

지원되지 않는 흐름은 다음과 같습니다.

- **서버 간**: ID 보호 시스템은 상호 작용의 일부인 호출자(네이티브 클라이언트)에서 수집한 신뢰할 수 있는 IP 주소가 필요합니다. 서버 쪽 API 호출에서는 서버의 IP 주소만 사용됩니다. 실패한 인증의 동적 임계값을 초과하면 ID 보호 시스템에서 반복되는 IP 주소를 공격자로 식별할 수 있습니다.
- **기밀 클라이언트 흐름**: 애플리케이션 클라이언트 ID의 유효성은 검사하지만 애플리케이션 비밀의 유효성은 검사하지 않습니다.

ROPC 흐름을 사용하는 경우 다음 사항을 고려하세요.

- 사용자 조작이 필요한 인증 흐름이 중단되면 ROPC가 작동하지 않습니다. 예를 들어, 암호가 만료되었거나 암호를 변경해야 하는 경우, 다단계 인증이 필요한 경우 또는 로그인 중에 추가 정보(예: 사용자 동의)를 수집해야 하는 경우가 있습니다.
- ROPC는 로컬 계정만 지원합니다. 사용자는 Microsoft, Google+, Twitter, AD-FS 또는 Facebook과 같은 페더레이션된 ID 공급자를 사용하여 로그인할 수 없습니다.
- KMSI(로그인 유지)를 비롯한 세션 관리는 적용되지 않습니다.
