---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/27/2020
ms.author: mimart
ms.openlocfilehash: f4944881e6eff3e9a56a74bc997c280f9fff6be8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78187004"
---
## <a name="ropc-flow-notes"></a>ROPC 흐름 노트
Azure Active Directory B2C(Azure AD B2C)에서는 다음 옵션이 지원됩니다.

- **네이티브 클라이언트**: 코드가 사용자 쪽 디바이스에서 실행될 때 인증 중에 사용자 상호 작용이 발생합니다. 이 장치는 Android 및 iOS와 같은 기본 운영 체제에서 실행되는 모바일 응용 프로그램일 수 있습니다.
- **공용 클라이언트 흐름**: 애플리케이션에서 수집한 사용자 자격 증명만 API 호출에서 전송됩니다. 애플리케이션의 자격 증명은 전송되지 않습니다.
- **새 클레임 추가**: ID 토큰 콘텐츠를 변경하여 새 클레임을 추가할 수 있습니다.

지원되지 않는 흐름은 다음과 같습니다.

- **서버 간**: ID 보호 시스템은 상호 작용의 일부인 호출자(네이티브 클라이언트)에서 수집한 신뢰할 수 있는 IP 주소가 필요합니다. 서버 쪽 API 호출에서는 서버의 IP 주소만 사용됩니다. 실패한 인증의 동적 임계값을 초과하면 ID 보호 시스템에서 반복되는 IP 주소를 공격자로 식별할 수 있습니다.
- **기밀 클라이언트 흐름**: 애플리케이션 클라이언트 ID의 유효성은 검사하지만 애플리케이션 비밀의 유효성은 검사하지 않습니다.

ROPC 흐름을 사용할 때는 다음을 고려하십시오.

- ROPC는 사용자 상호 작용이 필요한 인증 흐름에 중단이 있을 때 작동하지 않습니다. 예를 들어 암호가 만료되었거나 변경해야 하는 경우 다단계 인증이 필요하거나 로그인 중에 더 많은 정보를 수집해야 하는 경우(예: 사용자 동의).
- ROPC는 로컬 계정만 지원합니다. 사용자는 마이크로소프트, 구글+, 트위터, AD-FS, 페이스북과 같은 페더레이션된 아이덴티티 제공업체에 로그인할 수 없습니다.
- 로그인 유지(KMSI)를 포함한 세션 관리는 적용되지 않습니다.
