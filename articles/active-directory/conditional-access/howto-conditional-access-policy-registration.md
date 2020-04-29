---
title: 조건부 액세스-결합 된 보안 정보-Azure Active Directory
description: 보안 정보 등록을 위한 사용자 지정 조건부 액세스 정책 만들기
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
ms.openlocfilehash: 9c8081bb8145a6654c168fb2d664e1666b32dc18
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81457912"
---
# <a name="conditional-access-securing-security-info-registration"></a>조건부 액세스: 보안 정보 등록 보안

이제 조건부 액세스 정책의 사용자 작업을 통해 사용자가 Azure Multi-Factor Authentication 및 셀프 서비스 암호 재설정에 등록 하는 시기 및 방법에 대 한 보안을 설정할 수 있습니다. 이 미리 보기 기능은 [결합 된 등록 미리 보기](../authentication/concept-registration-mfa-sspr-combined.md)를 사용 하도록 설정한 조직에서 사용할 수 있습니다. 이 기능은 신뢰할 수 있는 네트워크 위치와 같은 조건을 사용 하 여 Azure Multi-Factor Authentication 및 셀프 서비스 암호 재설정 (SSPR)에 등록 하는 데 필요한 액세스를 제한 하는 조직에서 사용할 수 있습니다. 사용 가능한 조건에 대 한 자세한 내용은 [조건부 액세스: 조건](concept-conditional-access-conditions.md)문서를 참조 하세요.

## <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>신뢰할 수 있는 위치에서 등록을 요구 하는 정책 만들기

다음 정책은 모든 선택한 사용자에 게 적용 되며, 결합 된 등록 환경을 사용 하 여 등록을 시도 하 고, 신뢰할 수 있는 네트워크로 표시 된 위치에서 연결 하지 않는 한 액세스를 차단 합니다.

1. **Azure Portal**에서 **Azure Active Directory** > **보안** > **조건부 액세스**로 이동 합니다.
1. **새 정책**을 선택합니다.
1. 이름에이 정책의 이름을 입력 합니다. 예를 들어 **신뢰할 수 있는 네트워크에 대 한 보안 정보 등록이 결합**되어 있습니다.
1. **할당**아래에서 **사용자 및 그룹**을 선택 하 고이 정책을 적용할 사용자 및 그룹을 선택 합니다.

   > [!WARNING]
   > 사용자는 [결합 된 등록](../authentication/howto-registration-mfa-sspr-combined.md)을 사용 하도록 설정 해야 합니다.

1. **클라우드 앱 또는 작업**에서 **사용자 작업**을 선택 하 고 **보안 정보 등록**을 선택 합니다.
1. **조건** > **위치**에 있습니다.
   1. **예**를 구성 합니다.
   1. **모든 위치**를 포함 합니다.
   1. **모든 신뢰할 수 있는 위치**를 제외 합니다.
   1. 위치 블레이드에서 **완료** 를 선택 합니다.
   1. 조건 블레이드에서 **완료** 를 선택 합니다.
1. **조건** > **클라이언트 앱 (미리 보기)** 에서 **구성** 을 **예**로 설정 하 고 **완료**를 선택 합니다.
1. **액세스 제어** > **권한 부여**에서.
   1. **액세스 차단**을 선택합니다.
   1. 그런 다음 **선택**을 클릭합니다.
1. **정책 사용**을 **켜기**로 설정합니다.
1. 그런 다음 **저장**을 선택합니다.

이 정책의 6 단계에서 조직에서 선택할 수 있습니다. 위의 정책에는 신뢰할 수 있는 네트워크 위치의 등록이 필요 합니다. 조직에서는 **위치**대신 사용 가능한 조건을 활용 하도록 선택할 수 있습니다. 이 정책은 포함 된 항목이 차단 되 고 include와 일치 하지 않는 모든 항목이 허용 되도록 차단 정책입니다. 

일부는 위의 6 단계에서 위치 대신 장치 상태를 사용 하도록 선택할 수 있습니다.

6. **조건** > **장치 상태 (미리 보기)**
   1. **예**를 구성 합니다.
   1. **모든 장치 상태**를 포함 합니다.
   1. **규격으로 표시** 된 **장치 하이브리드 Azure AD 가입** 및/또는 장치 제외
   1. 위치 블레이드에서 **완료** 를 선택 합니다.
   1. 조건 블레이드에서 **완료** 를 선택 합니다.

> [!WARNING]
> 정책에서 장치 상태를 조건으로 사용 하는 경우 디렉터리의 게스트 사용자에 게 영향을 줄 수 있습니다. [보고서 전용 모드](concept-conditional-access-report-only.md) 는 정책 결정의 영향을 확인 하는 데 도움이 됩니다.

## <a name="next-steps"></a>다음 단계

[조건부 액세스 공통 정책](concept-conditional-access-policy-common.md)

[조건부 액세스 보고서 전용 모드를 사용 하 여 영향 확인](howto-conditional-access-report-only.md)

[조건부 액세스 What If 도구를 사용 하 여 로그인 동작 시뮬레이션](troubleshoot-conditional-access-what-if.md)
