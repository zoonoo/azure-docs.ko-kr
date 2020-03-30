---
title: 조건부 액세스 - 레거시 인증 차단 - Azure Active Directory
description: 레거시 인증 프로토콜을 차단하는 사용자 지정 조건부 액세스 정책 만들기
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6a868c8199ac34a498a280e2522d6b1e4c7ec370
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295225"
---
# <a name="conditional-access-block-legacy-authentication"></a>조건부 액세스: 레거시 인증 차단

레거시 인증 프로토콜과 관련된 위험이 증가하기 때문에 조직에서 이러한 프로토콜을 사용하여 인증 요청을 차단하고 최신 인증을 요구하는 것이 좋습니다.

## <a name="create-a-conditional-access-policy"></a>조건부 액세스 정책 만들기

다음 단계는 레거시 인증 요청을 차단하는 조건부 액세스 정책을 만드는 데 도움이 됩니다. 이 정책은 보고서 [전용 모드로](howto-conditional-access-report-only.md) 되어 있으므로 관리자는 기존 사용자에게 미치는 영향을 확인할 수 있습니다. 관리자가 정책이 의도한 대로 적용되는 데 익숙해지면 특정 그룹을 추가하고 다른 그룹을 제외하여 **On으로** 전환하거나 배포를 진행할 수 있습니다.

1. **Azure 포털에** 글로벌 관리자, 보안 관리자 또는 조건부 액세스 관리자로 로그인합니다.
1. Azure **Active 디렉터리** > **보안** > **조건부 액세스로**이동합니다.
1. **새 정책**을 선택합니다.
1. 정책 이름을 지정합니다. 조직에서 정책 이름에 대해 의미 있는 표준을 만드는 것이 좋습니다.
1. 할당에서 사용자 **및 그룹을** **선택합니다.**
   1. **포함**에서 **모든 사용자를**선택합니다.
   1. **제외에서** **사용자 및 그룹을** 선택하고 레거시 인증을 사용하는 기능을 유지해야 하는 계정을 선택합니다. 계정이 잠기지 않도록 계정을 하나 이상 제외합니다. 계정을 제외하지 않으면 이 정책을 만들 수 없습니다.
   1. **완료를 선택합니다.**
1. **클라우드 앱 또는 작업에서**모든 클라우드 **앱을**선택합니다.
   1. **완료를 선택합니다.**
1. **조건** > **클라이언트 앱(미리 보기)에서** **구성을** **예로**설정합니다.
   1. 확인란 모바일 **앱 및 데스크톱 클라이언트** > **다른 클라이언트만**선택합니다.
   1. **완료를 선택합니다.**
1. **액세스 제어** > **권한 부여에서** **액세스 차단을**선택합니다.
   1. **선택**을 선택합니다.
1. 설정을 확인하고 **정책 사용(보고서** **전용)을**설정합니다.
1. 정책을 활성화하려면 **만들기를** 선택합니다.

## <a name="next-steps"></a>다음 단계

[조건부 액세스 공통 정책](concept-conditional-access-policy-common.md)

[조건부 액세스 보고서 전용 모드를 사용하여 영향 확인](howto-conditional-access-report-only.md)

[조건부 액세스 What If 도구를 사용하여 로그인 동작 시뮬레이션](troubleshoot-conditional-access-what-if.md)
