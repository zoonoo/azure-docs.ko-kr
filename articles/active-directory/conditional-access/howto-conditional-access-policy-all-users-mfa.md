---
title: 조건부 액세스 - 모든 사용자에 대해 MFA 필요 - Azure Active Directory
description: 모든 사용자가 다단계 인증을 수행하도록 요구하는 사용자 지정 조건부 액세스 정책 만들기
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
ms.openlocfilehash: c3e7b1a656c92e37a709b57dae463f6644003e42
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80755197"
---
# <a name="conditional-access-require-mfa-for-all-users"></a>조건부 액세스: 모든 사용자에 대해 MFA 필요

알렉스 Weinert로, 마이크로 소프트의 ID 보안 디렉토리, 그의 블로그 게시물에 언급 [당신의 Pa $ $word 중요하지 않습니다](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984):

> 암호는 중요하지 않지만 MFA는 암호가 중요합니다! 당사의 연구에 따르면 MFA를 사용하는 경우 계정이 99.9% 이상 손상될 가능성이 낮습니다.

이 문서의 지침은 조직에서 사용자 환경에 대한 균형 잡힌 MFA 정책을 만드는 데 도움이 됩니다.

## <a name="user-exclusions"></a>사용자 제외

조건부 액세스 정책은 강력한 도구이며 정책에서 다음 계정을 제외하는 것이 좋습니다.

* **긴급 액세스** 또는 **브레이크 글래스** 계정으로 테넌트 전체 계정 잠금 방지 드문 경우지만 모든 관리자가 테넌트에서 잠겨 있는 경우 긴급 액세스 관리 계정을 사용하여 테넌트에 로그인하여 액세스를 복구하는 단계를 수행할 수 있습니다.
   * 자세한 내용은 Azure [AD의 긴급 액세스 계정 관리](../users-groups-roles/directory-emergency-access.md)문서에서 찾을 수 있습니다.
* Azure AD 연결 동기화 계정과 같은 **서비스 계정** 및 **서비스 주체입니다.** 서비스 계정은 특정 사용자에게 연결되지 않은 대화형 계정이 아닙니다. 일반적으로 응용 프로그램에 프로그래밍 방식으로 액세스할 수 있도록 백 엔드 서비스에서 사용되지만 관리 목적으로 시스템에 로그인하는 데도 사용됩니다. MFA를 프로그래밍 방식으로 완료할 수 없으므로 이러한 서비스 계정은 제외해야 합니다. 서비스 주체가 만든 통화는 조건부 액세스에 의해 차단되지 않습니다.
   * 조직에서 스크립트 또는 코드에 이러한 계정을 사용 중인 경우 이를 [관리 ID](../managed-identities-azure-resources/overview.md)로 바꾸는 것이 좋습니다. 임시 해결 으로 이러한 특정 계정을 기준 정책에서 제외할 수 있습니다.

## <a name="application-exclusions"></a>응용 프로그램 제외

조직에는 많은 클라우드 응용 프로그램이 사용 중일 수 있습니다. 이러한 응용 프로그램 중 일부가 동일한 보안을 요구하는 것은 아닙니다. 예를 들어 급여 및 출석 신청서에는 MFA가 필요할 수 있지만 카페테리아는 그렇지 않을 수 있습니다. 관리자는 정책에서 특정 응용 프로그램을 제외하도록 선택할 수 있습니다.

## <a name="create-a-conditional-access-policy"></a>조건부 액세스 정책 만들기

다음 단계는 다단계 인증을 수행하기 위해 할당된 관리 역할을 요구하는 조건부 액세스 정책을 만드는 데 도움이 됩니다.

1. **Azure 포털에** 글로벌 관리자, 보안 관리자 또는 조건부 액세스 관리자로 로그인합니다.
1. Azure **Active 디렉터리** > **보안** > **조건부 액세스로**이동합니다.
1. **새 정책**을 선택합니다.
1. 정책 이름을 지정합니다. 조직에서 정책 이름에 대해 의미 있는 표준을 만드는 것이 좋습니다.
1. 할당에서 사용자 **및 그룹을** **선택합니다.**
   1. **포함**에서 **모든 사용자를** 선택합니다.
   1. **제외에서** **사용자 및 그룹을** 선택하고 조직의 비상 액세스 또는 브레이크 글래스 계정을 선택합니다. 
   1. **완료를 선택합니다.**
1. **클라우드 앱 또는 작업** > **에서 포함**, 모든 **클라우드 앱을**선택합니다.
   1. **제외에서**다단계 인증이 필요하지 않은 응용 프로그램을 선택합니다.
1. **조건 클라이언트** > **앱(미리 보기)에서** **구성을** **예로**설정하고 **완료를**선택합니다.
1. **액세스 제어** > **권한 부여,** 권한 부여 **액세스,** **다단계 인증 필요**및 선택 **을**선택합니다.
1. 설정을 확인하고 **정책 활성화를** **설정 합니다.**
1. 정책을 활성화하려면 **만들기를** 선택합니다.

### <a name="named-locations"></a>명명된 위치

조직은 **명명된** 위치로 알려진 알려진 네트워크 위치를 조건부 액세스 정책에 통합하도록 선택할 수 있습니다. 이러한 명명된 위치에는 본사 위치와 같은 신뢰할 수 있는 IPv4 네트워크가 포함될 수 있습니다. 명명된 위치 구성에 대한 자세한 내용은 [Azure Active Directory 조건부 액세스의 위치 조건은 무엇입니까?](location-condition.md)

위의 예제 정책에서 조직은 회사 네트워크에서 클라우드 앱에 액세스하는 경우 다단계 인증을 요구하지 않도록 선택할 수 있습니다. 이 경우 정책에 다음 구성을 추가할 수 있습니다.

1. 과제에서 **조건** > **위치를** **선택합니다.**
   1. **구성 예**.
   1. **모든 위치를**포함합니다.
   1. **신뢰할 수 있는 모든 위치를 제외합니다.**
   1. **완료를 선택합니다.**
1. **완료를 선택합니다.**
1. 정책 변경 내용을 **저장합니다.**

## <a name="next-steps"></a>다음 단계

[조건부 액세스 공통 정책](concept-conditional-access-policy-common.md)

[조건부 액세스 보고서 전용 모드를 사용하여 영향 확인](howto-conditional-access-report-only.md)

[조건부 액세스 What If 도구를 사용하여 로그인 동작 시뮬레이션](troubleshoot-conditional-access-what-if.md)
