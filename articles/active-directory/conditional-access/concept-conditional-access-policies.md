---
title: 조건부 액세스 정책 빌드 - Azure Active 디렉터리
description: 조건부 액세스 정책을 빌드하는 데 사용할 수 있는 모든 옵션은 무엇이며 그 의미는 무엇입니까?
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 907ad8705742e4b2e38b13c3c675ebd333bd27d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295317"
---
# <a name="building-a-conditional-access-policy"></a>조건부 액세스 정책 빌드

문서에서 설명한 대로 [조건부 액세스는](overview.md), 조건부 액세스 정책은 **if-then** 문, 할당 및 **액세스 컨트롤입니다.** 조건부 액세스 정책은 신호를 함께 가져와 의사 결정을 내리고 조직 정책을 적용합니다.

조직에서 이러한 정책을 만드는 방법은 무엇입니까? 무엇이 필요합니까?

![조건부 액세스(신호 + 결정 + 적용 = 정책)](./media/concept-conditional-access-policies/conditional-access-signal-decision-enforcement.png)

## <a name="assignments"></a>할당

할당 부분은 조건부 액세스 정책의 누가, 무엇을, 어디에 있는지를 제어합니다.

### <a name="users-and-groups"></a>개요

[사용자 및 그룹은](concept-conditional-access-users-groups.md) 정책에 포함하거나 제외할 사용자를 할당합니다. 이 할당에는 모든 사용자, 특정 사용자 그룹, 디렉터리 역할 또는 외부 게스트 사용자가 포함될 수 있습니다. 

### <a name="cloud-apps-or-actions"></a>클라우드 앱 또는 작업

[클라우드 앱 또는 작업에는](concept-conditional-access-cloud-apps.md) 정책의 적용을 받는 클라우드 응용 프로그램 또는 사용자 작업이 포함되거나 제외될 수 있습니다.

### <a name="conditions"></a>조건

정책에는 여러 [조건이](concept-conditional-access-conditions.md)포함될 수 있습니다.

#### <a name="sign-in-risk"></a>로그인 위험

[Azure AD ID 보호](../identity-protection/overview.md)기능이 있는 조직의 경우 생성된 위험 검색이 조건부 액세스 정책에 영향을 미칠 수 있습니다.

#### <a name="device-platforms"></a>디바이스 플랫폼

여러 장치 운영 체제 플랫폼을 보유한 조직은 서로 다른 플랫폼에서 특정 정책을 적용할 수 있습니다. 

장치 플랫폼을 계산하는 데 사용되는 정보는 변경할 수 있는 사용자 에이전트 문자열과 같은 확인되지 않은 소스에서 가져옵니다.

#### <a name="locations"></a>위치

위치 데이터는 IP 지리적 위치 데이터에 의해 제공됩니다. 관리자는 위치를 정의하고 조직의 네트워크 위치와 같이 신뢰할 수 있는 위치로 표시하도록 선택할 수 있습니다.

#### <a name="client-apps"></a>클라이언트 앱

기본적으로 조건부 액세스 정책은 최신 인증을 지원하는 브라우저 앱, 모바일 앱 및 데스크톱 클라이언트에 적용됩니다. 

이 할당 조건을 사용하면 조건부 Access 정책이 최신 인증을 사용하지 않는 특정 클라이언트 응용 프로그램을 대상으로 지정할 수 있습니다. 이러한 응용 프로그램에는 Exchange ActiveSync 클라이언트, 최신 인증을 사용하지 않는 이전 Office 응용 프로그램 및 IMAP, MAPI, POP 및 SMTP와 같은 메일 프로토콜이 포함됩니다.

#### <a name="device-state"></a>디바이스 상태

이 컨트롤은 하이브리드 Azure AD조인 또는 Intune에서 호환되도록 표시된 장치를 제외하는 데 사용됩니다. 이 제외는 관리되지 않는 장치를 차단하기 위해 수행할 수 있습니다. 

## <a name="access-controls"></a>액세스 제어

조건부 액세스 정책의 액세스 제어 부분은 정책이 적용되는 방법을 제어합니다.

### <a name="grant"></a>허용

[Grant는](concept-conditional-access-grant.md) 관리자에게 액세스 권한을 차단하거나 부여할 수 있는 정책 적용 수단을 제공합니다.

#### <a name="block-access"></a>액세스 차단

블록 액세스는 지정된 할당에 따라 액세스를 차단합니다. 블록 컨트롤은 강력하며 적절한 지식을 가지고 사용해야합니다.

#### <a name="grant-access"></a>액세스 권한 부여

권한 부여 컨트롤은 하나 이상의 컨트롤의 적용을 트리거할 수 있습니다. 

- 다단계 인증 필요(Azure 다단계 인증)
- 장치를 규격으로 표시해야 합니다(Intune)
- 하이브리드 Azure AD 조인된 디바이스 필요
- 승인된 클라이언트 앱 필요
- 앱 보호 정책 필요

관리자는 다음 옵션을 사용하여 이전 컨트롤 중 하나 또는 선택한 모든 컨트롤을 요구하도록 선택할 수 있습니다. 여러 컨트롤의 기본값은 모두 요구하는 것입니다.

- 선택한 모든 컨트롤 필요(제어 및 제어)
- 선택한 컨트롤 중 하나(컨트롤 또는 컨트롤) 요구

### <a name="session"></a>세션

[세션 컨트롤은](concept-conditional-access-session.md) 환경을 제한할 수 있습니다. 

- 앱에서 적용된 제한 사항 사용
   - 현재 Exchange 온라인 및 쉐어포인트 온라인에서만 작동합니다.
      - 장치 정보를 전달하여 전체 또는 제한된 액세스 권한을 부여하는 환경을 제어할 수 있습니다.
- 조건부 액세스 앱 제어 사용
   - Microsoft 클라우드 앱 보안의 신호를 사용하여 다음과 같은 작업을 수행합니다. 
      - 중요한 문서의 다운로드, 잘라내기, 복사 및 인쇄를 차단합니다.
      - 위험한 세션 동작을 모니터링합니다.
      - 중요한 파일의 레이블이 필요합니다.
- 로그인 빈도
   - 최신 인증의 기본 로그인 빈도를 변경할 수 있습니다.
- 영구 브라우저 세션
   - 사용자가 브라우저 창을 닫고 다시 연 후에도 로그인 된 상태로 유지할 수 있습니다.

## <a name="simple-policies"></a>간단한 정책

조건부 액세스 정책은 최소한 다음을 포함해야 적용됩니다.

- 정책 **이름입니다.**
- **할당**
   - 정책을 적용할 **사용자 및/또는 그룹.**
   - 정책을 적용할 **클라우드 앱 또는 작업입니다.**
- **액세스 제어**
   - **컨트롤 부여** 또는 **차단**

![빈 조건부 액세스 정책](./media/concept-conditional-access-policies/conditional-access-blank-policy.png)

일반적인 [조건부 액세스 정책에는](concept-conditional-access-policy-common.md) 대부분의 조직에 유용할 것으로 생각되는 몇 가지 정책이 포함되어 있습니다.

## <a name="next-steps"></a>다음 단계

[조건부 액세스 What If 도구를 사용하여 로그인 동작 시뮬레이션](troubleshoot-conditional-access-what-if.md)

[클라우드 기반 Azure Multi-Factor Authentication 배포 계획](../authentication/howto-mfa-getstarted.md)

[Intune을 통해 장치 규정 준수 관리](/intune/device-compliance-get-started)

[마이크로소프트 클라우드 앱 보안 및 조건부 액세스](/cloud-app-security/proxy-intro-aad)
