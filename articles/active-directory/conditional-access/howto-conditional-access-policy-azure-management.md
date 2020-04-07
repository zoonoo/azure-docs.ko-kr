---
title: 조건부 액세스 - Azure 관리를 위한 MFA 필요 - Azure Active Directory
description: Azure 관리 작업에 다단계 인증을 요구하는 사용자 지정 조건부 액세스 정책 만들기
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 04/02/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: b92d833e6f32821ad907ff966771bbba8bbb77ce
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80755174"
---
# <a name="conditional-access-require-mfa-for-azure-management"></a>조건부 액세스: Azure 관리를 위한 MFA 필요

조직은 다음과 같은 다양한 Azure 서비스를 사용하고 Azure Resource Manager 기반 도구에서 관리합니다.

* Azure portal
* Azure PowerShell
* Azure CLI

이러한 도구는 구독 전체 구성, 서비스 설정 및 구독 청구를 변경할 수 있는 리소스에 대한 높은 권한 있는 액세스를 제공할 수 있습니다. 이러한 권한 있는 리소스를 보호 하려면 이러한 리소스에 액세스 하는 모든 사용자에 대 한 다단계 인증을 요구 하는 것이 좋습니다.

## <a name="user-exclusions"></a>사용자 제외

조건부 액세스 정책은 강력한 도구이며 정책에서 다음 계정을 제외하는 것이 좋습니다.

* **긴급 액세스** 또는 **브레이크 글래스** 계정으로 테넌트 전체 계정 잠금 방지 드문 경우지만 모든 관리자가 테넌트에서 잠겨 있는 경우 긴급 액세스 관리 계정을 사용하여 테넌트에 로그인하여 액세스를 복구하는 단계를 수행할 수 있습니다.
   * 자세한 내용은 Azure [AD의 긴급 액세스 계정 관리](../users-groups-roles/directory-emergency-access.md)문서에서 찾을 수 있습니다.
* Azure AD 연결 동기화 계정과 같은 **서비스 계정** 및 **서비스 주체입니다.** 서비스 계정은 특정 사용자에게 연결되지 않은 대화형 계정이 아닙니다. 일반적으로 응용 프로그램에 프로그래밍 방식으로 액세스할 수 있도록 백 엔드 서비스에서 사용되지만 관리 목적으로 시스템에 로그인하는 데도 사용됩니다. MFA를 프로그래밍 방식으로 완료할 수 없으므로 이러한 서비스 계정은 제외해야 합니다. 서비스 주체가 만든 통화는 조건부 액세스에 의해 차단되지 않습니다.
   * 조직에서 스크립트 또는 코드에 이러한 계정을 사용 중인 경우 이를 [관리 ID](../managed-identities-azure-resources/overview.md)로 바꾸는 것이 좋습니다. 임시 해결 으로 이러한 특정 계정을 기준 정책에서 제외할 수 있습니다.

## <a name="create-a-conditional-access-policy"></a>조건부 액세스 정책 만들기

다음 단계는 다단계 인증을 수행하기 위해 할당된 관리 역할을 요구하는 조건부 액세스 정책을 만드는 데 도움이 됩니다.

1. **Azure 포털에** 글로벌 관리자, 보안 관리자 또는 조건부 액세스 관리자로 로그인합니다.
1. Azure **Active 디렉터리** > **보안** > **조건부 액세스로**이동합니다.
1. **새 정책**을 선택합니다.
1. 정책 이름을 지정합니다. 조직에서 정책 이름에 대해 의미 있는 표준을 만드는 것이 좋습니다.
1. 할당에서 사용자 **및 그룹을** **선택합니다.**
   1. **포함**에서 **모든 사용자를**선택합니다.
   1. **제외에서** **사용자 및 그룹을** 선택하고 조직의 비상 액세스 또는 브레이크 글래스 계정을 선택합니다. 
   1. **완료를 선택합니다.**
1. **클라우드 앱 또는 작업** > **포함**, 앱 **선택**선택, Microsoft **Azure 관리**선택 및 **수행**후 **선택 선택**
1. **조건 클라이언트** > **앱(미리 보기)에서** **구성을** **예로**설정하고 **완료를**선택합니다.
1. **액세스 제어** > **권한 부여,** 권한 부여 **액세스,** **다단계 인증 필요**및 선택 **을**선택합니다.
1. 설정을 확인하고 **정책 활성화를** **설정 합니다.**
1. 정책을 활성화하려면 **만들기를** 선택합니다.

## <a name="next-steps"></a>다음 단계

[조건부 액세스 공통 정책](concept-conditional-access-policy-common.md)

[조건부 액세스 보고서 전용 모드를 사용하여 영향 확인](howto-conditional-access-report-only.md)

[조건부 액세스 What If 도구를 사용하여 로그인 동작 시뮬레이션](troubleshoot-conditional-access-what-if.md)
