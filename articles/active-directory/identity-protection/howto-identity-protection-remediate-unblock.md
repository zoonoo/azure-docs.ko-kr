---
title: Azure AD ID 보호에서 위험 수정 및 사용자 차단 해제
description: 미해결 위험 검색을 종료 하는 옵션에 대해 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 10/06/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: c297e1a4f6443e584f04914712314d33df23b119
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91776105"
---
# <a name="remediate-risks-and-unblock-users"></a>위험 재구성 및 사용자 차단 해제

[조사](howto-identity-protection-investigate-risk.md)를 완료 한 후에는 작업을 수행 하 여 위험을 수정 하거나 사용자를 차단 해제 하는 것이 좋습니다. 또한 조직에는 [위험 정책을](howto-identity-protection-configure-risk-policies.md)사용 하 여 자동화 된 수정 기능을 사용 하도록 설정할 수 있는 옵션도 있습니다. 조직에서는 조직이 편안 하 게 느껴질 때 제공 되는 모든 위험 검색을 닫으려고 합니다. 위험을 다룰 때 시간이 중요 하므로 가능한 한 빨리 이벤트를 닫는 것이 좋습니다.

## <a name="remediation"></a>수정

모든 활성 위험 검색은 사용자 위험 수준 이라는 값을 계산 하는 데 영향을 주지 않습니다. 사용자 위험 수준은 계정이 손상된 가능성에 대한 표시기(낮음, 보통, 높음)입니다. 관리자는 영향을 받는 사용자가 더 이상 위험에 노출 되지 않도록 모든 위험 검색을 종결 하려고 합니다.

이벤트가 더 이상 위험한 것으로 확인 되지 않았기 때문에 일부 위험이 검색은 Id 보호로 "닫힘 (시스템)"으로 표시 될 수 있습니다.

관리자는 다음 옵션을 수정 하 여 수정할 수 있습니다.

- 위험 정책을 통한 자체 재구성
- 수동 암호 다시 설정
- 사용자 위험 해제
- 수동으로 개별 위험 검색 닫기

### <a name="self-remediation-with-risk-policy"></a>위험 정책을 통한 자체 재구성

사용자가 위험 정책에서 MFA (Azure Multi-Factor Authentication)와 SSPR (셀프 서비스 암호 재설정)를 사용 하 여 자체 재구성을 허용 하면 위험이 감지 되 면 스스로 차단을 해제할 수 있습니다. 이러한 검색은 닫힌 것으로 간주 됩니다. 위험이 검색 될 때를 사용 하려면 사용자가 이전에 Azure MFA 및 SSPR에 등록 되어 있어야 합니다.

일부 검색은 사용자가 직접 수정 해야 하는 수준에 대 한 위험을 발생 시 키 지 않지만 관리자는 이러한 검색을 평가 해야 할 수도 있습니다. 관리자는 [위치에서의 액세스를 차단](../conditional-access/howto-conditional-access-policy-location.md) 하거나 정책에서 허용 가능한 위험을 낮추는 등의 추가 측정값이 필요 하다 고 결정할 수 있습니다.

### <a name="manual-password-reset"></a>수동 암호 다시 설정

사용자 위험 정책을 사용 하 여 암호 재설정을 수행할 필요가 없는 경우 관리자는 암호를 수동으로 다시 설정 하 여 사용자에 대 한 모든 위험 검색을 종료할 수 있습니다.

관리자는 사용자에 대 한 암호를 다시 설정할 때 다음과 같은 두 가지 옵션을 제공 합니다.

- **임시 암호 생성** - 임시 암호를 생성하여 ID를 안전한 상태로 즉시 전환할 수 있습니다. 이 메서드는 임시 암호가 무엇 인지 알아야 하므로 영향을 받는 사용자에 게 연락 해야 합니다. 암호는 임시 암호 이므로 사용자에 게 다음 로그인 시 암호를 새로운 항목으로 변경 하 라는 메시지가 표시 됩니다.

- **사용자가 암호를 재설정해야 함** - 사용자가 암호를 재설정하도록 요구하면 지원 센터 또는 관리자에게 문의하지 않고도 셀프 복구가 가능합니다. 이 메서드는 Azure MFA 및 SSPR에 등록 된 사용자 에게만 적용 됩니다. 등록 되지 않은 사용자의 경우에는이 옵션을 사용할 수 없습니다.

### <a name="dismiss-user-risk"></a>사용자 위험 해제

암호 재설정이 옵션이 아닌 경우, 예를 들어 사용자가 삭제 되었으므로 사용자 위험 검색을 해제 하도록 선택할 수 있습니다.

**사용자 위험 해제**를 클릭 하면 모든 이벤트가 닫히고 영향을 받는 사용자는 더 이상 위험 하지 않습니다. 그러나 이 방법은 기존 암호에 영향을 주지 않으므로 관련된 ID를 안전한 상태로 다시 전환하지 않습니다. 

### <a name="close-individual-risk-detections-manually"></a>수동으로 개별 위험 검색 닫기

개별 위험 검색을 수동으로 닫을 수 있습니다. 수동으로 위험 검색을 닫아 사용자 위험 수준을 낮출 수 있습니다. 일반적으로 관련 조사에 대 한 응답으로 위험 검색이 수동으로 닫힙니다. 예를 들어 사용자에 게 통신 하는 경우 활성 위험 검색이 더 이상 필요 하지 않습니다. 
 
위험 검색을 수동으로 종료할 때 다음 작업 중 하나를 수행 하 여 위험 검색의 상태를 변경 하도록 선택할 수 있습니다.

- 사용자의 손상 확인
- 사용자 위험 해제
- 로그인 보안 확인
- 로그인이 손상 되었는지 확인

## <a name="unblocking-users"></a>사용자 차단 해제

관리자는 위험 정책 또는 조사에 따라 로그인을 차단 하도록 선택할 수 있습니다. 로그인 또는 사용자 위험에 따라 블록이 발생할 수 있습니다.

### <a name="unblocking-based-on-user-risk"></a>사용자 위험에 따라 차단 해제

사용자 위험으로 인해 차단 된 계정의 차단을 해제 하기 위해 관리자는 다음과 같은 옵션을 사용할 수 있습니다.

1. **암호 재설정** - 사용자의 암호를 다시 설정할 수 있습니다.
1. **사용자 위험 해제** -액세스 차단에 대 한 구성 된 사용자 위험 수준에 도달 하는 경우 사용자 위험 정책은 사용자를 차단 합니다. 사용자 위험을 해제 하거나 보고 된 위험 감지를 수동으로 닫으면 사용자의 위험 수준을 줄일 수 있습니다.
1. **정책에서 사용자를 제외** 합니다.-로그인 정책의 현재 구성이 특정 사용자에 대해 문제를 일으키는 것으로 생각 되는 경우 해당 사용자를 제외할 수 있습니다. 자세한 내용은 [방법: 위험 정책 구성 및 사용](howto-identity-protection-configure-risk-policies.md#exclusions)문서에서 제외 섹션을 참조 하세요.
1. **정책 비활성화** - 정책 구성이 모든 사용자에 대해 문제를 일으킨다고 생각하는 경우 정책을 비활성화할 수 있습니다. 자세한 내용은 [방법: 위험 정책 구성 및 사용](howto-identity-protection-configure-risk-policies.md)문서를 참조 하세요.

### <a name="unblocking-based-on-sign-in-risk"></a>로그인 위험에 따라 차단 해제

로그인 위험에 따라 계정을 차단 해제 하기 위해 관리자는 다음과 같은 옵션을 사용할 수 있습니다.

1. **친숙한 위치 또는 디바이스에서 로그인** - 차단된 의심스러운 로그인에 대한 일반적인 이유는 알 수 없는 위치 또는 디바이스에서의 로그인 시도입니다. 사용자는 친숙 한 위치나 장치에서 로그인을 시도 하 여이 이유가 차단 이유가 되는지 빠르게 확인할 수 있습니다.
1. **정책에서 사용자를 제외** 합니다.-로그인 정책의 현재 구성이 특정 사용자에 대해 문제를 일으키는 것으로 생각 되는 경우 해당 사용자를 제외할 수 있습니다. 자세한 내용은 [방법: 위험 정책 구성 및 사용](howto-identity-protection-configure-risk-policies.md#exclusions)문서에서 제외 섹션을 참조 하세요.
1. **정책 비활성화** - 정책 구성이 모든 사용자에 대해 문제를 일으킨다고 생각하는 경우 정책을 비활성화할 수 있습니다. 자세한 내용은 [방법: 위험 정책 구성 및 사용](howto-identity-protection-configure-risk-policies.md)문서를 참조 하세요.

## <a name="powershell-preview"></a>PowerShell 미리 보기

Microsoft Graph PowerShell SDK 미리 보기 모듈을 사용 하 여 조직은 PowerShell을 사용 하 여 위험을 관리할 수 있습니다. 미리 보기 모듈 및 샘플 코드는 [AZURE AD GitHub 리포지토리](https://github.com/AzureAD/IdentityProtectionTools)에서 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure AD ID 보호의 개요는 [Azure AD ID 보호 개요](overview-identity-protection.md)를 참조하세요.
