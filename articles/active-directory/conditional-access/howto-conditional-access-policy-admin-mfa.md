---
title: 조건부 액세스 - 관리자를 위한 MFA 필요 - Azure Active Directory
description: 관리자가 다단계 인증을 수행하도록 요구하는 사용자 지정 조건부 액세스 정책 만들기
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
ms.openlocfilehash: c08c8d5d4203ae90cedd826bb5dcb01011d07afa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295272"
---
# <a name="conditional-access-require-mfa-for-administrators"></a>조건부 액세스: 관리자를 위한 MFA 필요

관리 권한이 할당된 계정은 공격자의 대상이 됩니다. 이러한 계정에 MFA(다단계 인증)를 요구하는 것은 해당 계정이 손상될 위험을 쉽게 줄일 수 있는 방법입니다.

최소한 다음 역할에 MFA가 필요한 것이 좋습니다.

* 대금 청구 관리자
* 조건부 액세스 관리자
* Exchange 관리자
* 전역 관리자
* 헬프 데스크(암호) 관리자
* 암호 관리자
* 보안 관리자
* SharePoint 관리자
* 사용자 관리자

조직은 적합한 역할을 포함하거나 제외하도록 선택할 수 있습니다.

## <a name="user-exclusions"></a>사용자 제외

조건부 액세스 정책은 강력한 도구이며 정책에서 다음 계정을 제외하는 것이 좋습니다.

* **긴급 액세스** 또는 **브레이크 글래스** 계정으로 테넌트 전체 계정 잠금 방지 드문 경우지만 모든 관리자가 테넌트에서 잠겨 있는 경우 긴급 액세스 관리 계정을 사용하여 테넌트에 로그인하여 액세스를 복구하는 단계를 수행할 수 있습니다.
   * 자세한 내용은 Azure [AD의 긴급 액세스 계정 관리](../users-groups-roles/directory-emergency-access.md)문서에서 찾을 수 있습니다.
* Azure AD 연결 동기화 계정과 같은 **서비스 계정** 및 **서비스 주체입니다.** 서비스 계정은 특정 사용자에게 연결되지 않은 대화형 계정이 아닙니다. 일반적으로 백 엔드 서비스에서 사용되며 응용 프로그램에 프로그래밍 방식으로 액세스할 수 있습니다. MFA를 프로그래밍 방식으로 완료할 수 없으므로 서비스 계정은 제외해야 합니다.
   * 조직에서 스크립트 또는 코드에 이러한 계정을 사용 중인 경우 이를 [관리 ID](../managed-identities-azure-resources/overview.md)로 바꾸는 것이 좋습니다. 임시 해결 으로 이러한 특정 계정을 기준 정책에서 제외할 수 있습니다.

## <a name="create-a-conditional-access-policy"></a>조건부 액세스 정책 만들기

다음 단계는 다단계 인증을 수행하기 위해 할당된 관리 역할을 요구하는 조건부 액세스 정책을 만드는 데 도움이 됩니다.

1. **Azure 포털에** 글로벌 관리자, 보안 관리자 또는 조건부 액세스 관리자로 로그인합니다.
1. Azure **Active 디렉터리** > **보안** > **조건부 액세스로**이동합니다.
1. **새 정책**을 선택합니다.
1. 정책 이름을 지정합니다. 조직에서 정책 이름에 대해 의미 있는 표준을 만드는 것이 좋습니다.
1. 할당에서 사용자 **및 그룹을** **선택합니다.**
   1. **포함**에서 **디렉터리 역할(미리 보기)을** 선택하고 최소한 다음 역할을 선택합니다.
      * 인증 관리자
      * 대금 청구 관리자
      * 조건부 액세스 관리자
      * Exchange 관리자
      * 전역 관리자
      * 기술 지원팀 관리자
      * 암호 관리자
      * 보안 관리자
      * SharePoint 관리자
      * 사용자 관리자
   1. **제외에서** **사용자 및 그룹을** 선택하고 조직의 비상 액세스 또는 브레이크 글래스 계정을 선택합니다. 
   1. **완료를 선택합니다.**
1. **클라우드 앱 또는 작업** > **에서 포함**, 모든 클라우드 **앱을**선택하고 **완료**를 선택합니다.
1. **조건 클라이언트** > **앱(미리 보기)에서** **구성을** **예로**설정하고 **완료를**선택합니다.
1. **액세스 제어** > **권한 부여,** 권한 부여 **액세스,** **다단계 인증 필요**및 선택 **을**선택합니다.
1. 설정을 확인하고 **정책 활성화를** **설정 합니다.**
1. 정책을 활성화하려면 **만들기를** 선택합니다.

## <a name="next-steps"></a>다음 단계

[조건부 액세스 공통 정책](concept-conditional-access-policy-common.md)

[조건부 액세스 보고서 전용 모드를 사용하여 영향 확인](howto-conditional-access-report-only.md)

[조건부 액세스 What If 도구를 사용하여 로그인 동작 시뮬레이션](troubleshoot-conditional-access-what-if.md)
