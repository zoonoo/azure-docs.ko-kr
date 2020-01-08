---
title: 조건부 액세스-결합 된 보안 정보-Azure Active Directory
description: 보안 정보 등록을 위해 신뢰할 수 있는 위치를 요구 하는 사용자 지정 조건부 액세스 정책 만들기
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c9b01cc06b3d0ef8f47b34e9ef86bec9adac03f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75424843"
---
# <a name="conditional-access-require-trusted-location-for-mfa-registration"></a>조건부 액세스: MFA 등록을 위해 신뢰할 수 있는 위치 필요

이제 조건부 액세스 정책의 사용자 작업을 통해 사용자가 Azure Multi-Factor Authentication 및 셀프 서비스 암호 재설정에 등록 하는 시기 및 방법에 대 한 보안을 설정할 수 있습니다. 이 미리 보기 기능은 [결합 된 등록 미리 보기](../authentication/concept-registration-mfa-sspr-combined.md)를 사용 하도록 설정한 조직에서 사용할 수 있습니다. 신뢰할 수 있는 네트워크 위치와 같은 조건을 사용 하 여 Azure Multi-Factor Authentication 및 SSPR 등록에 대 한 액세스를 제한 하려는 조직에서이 기능을 사용 하도록 설정할 수 있습니다. 조건부 액세스에서 신뢰할 수 있는 위치를 만드는 방법에 대 한 자세한 내용은 [Azure Active Directory 조건부 액세스의 위치 조건 이란?](../conditional-access/location-condition.md#named-locations) 문서를 참조 하세요.

## <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>신뢰할 수 있는 위치에서 등록을 요구 하는 정책 만들기

다음 정책은 모든 선택한 사용자에 게 적용 되며, 결합 된 등록 환경을 사용 하 여 등록을 시도 하 고, 신뢰할 수 있는 네트워크로 표시 된 위치에서 연결 하지 않는 한 액세스를 차단 합니다.

1. **Azure Portal**에서 **Azure Active Directory** > **보안** > **조건부 액세스**로 이동 합니다.
1. **새 정책**을 선택합니다.
1. 이름에이 정책의 이름을 입력 합니다. 예를 들어 **신뢰할 수 있는 네트워크에 대 한 보안 정보 등록이 결합**되어 있습니다.
1. **할당**에서 **사용자 및 그룹**을 클릭 하 고이 정책을 적용할 사용자 및 그룹을 선택 합니다.

   > [!WARNING]
   > 사용자는 [결합 된 등록 미리 보기](../authentication/howto-registration-mfa-sspr-combined.md)를 사용 하도록 설정 해야 합니다.

1. **클라우드 앱 또는 작업**에서 **사용자 작업**을 선택 하 고 **보안 정보 등록 (미리 보기)** 을 선택 합니다.
1. **조건** > **위치**에 있습니다.
   1. **예**를 구성 합니다.
   1. **모든 위치**를 포함 합니다.
   1. **모든 신뢰할 수 있는 위치**를 제외 합니다.
   1. 위치 블레이드에서 **완료** 를 클릭 합니다.
   1. 조건 블레이드에서 **완료** 를 클릭 합니다.
1. **액세스 제어** 에서 **Grant** > 합니다.
   1. **액세스 차단**을 클릭 합니다.
   1. 그런 다음 **선택**을 클릭합니다.
1. **정책 사용**을 **켜기**로 설정합니다.
1. 그런 다음 **Save**를 클릭합니다.

## <a name="next-steps"></a>다음 단계

[조건부 액세스 공통 정책](concept-conditional-access-policy-common.md)

[조건부 액세스 보고서 전용 모드를 사용 하 여 영향 확인](howto-conditional-access-report-only.md)

[조건부 액세스 What If 도구를 사용 하 여 로그인 동작 시뮬레이션](troubleshoot-conditional-access-what-if.md)
