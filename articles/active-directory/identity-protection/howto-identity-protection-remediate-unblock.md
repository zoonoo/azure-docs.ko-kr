---
title: Azure AD ID 보호에서 위험 수정 및 사용자 차단 해제
description: 활성 위험 검색을 닫는 옵션에 대해 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 01/25/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: e322f78468c89a549955a01f73952e8cde7a13c8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98872827"
---
# <a name="remediate-risks-and-unblock-users"></a>위험 수정 및 사용자 차단 해제

[조사](howto-identity-protection-investigate-risk.md)를 완료한 후 위험을 수정하거나 사용자를 차단 해제하기 위한 조치를 취하려고 합니다. 조직은 [위험 정책](howto-identity-protection-configure-risk-policies.md)을 사용하여 자동화된 수정을 사용하도록 설정할 수도 있습니다. 조직은 제공되는 모든 위험 검색을 편하게 생각되는 기간 내에 닫으려고 시도해야 합니다. 위험 관련 작업을 할 때는 시간이 중요하므로 최대한 빨리 이벤트를 닫는 것이 좋습니다.

## <a name="remediation"></a>수정

모든 활성 위험 검색은 ‘사용자 위험 수준’이라고 하는 값의 계산에 영향을 줍니다. 사용자 위험 수준은 계정이 손상된 가능성에 대한 지표(낮음, 보통, 높음)입니다. 관리자는 모든 위험 검색을 닫아서 영향을 받는 사용자가 더 이상 위험에 처하지 않도록 하고 싶습니다.

이벤트가 더 이상 위험한 것으로 확인되지 않았기 때문에 일부 위험 검색이 Identity Protection을 통해 “닫힘(시스템)”으로 표시될 수 있습니다.

관리자는 다음 수정 옵션을 사용할 수 있습니다.

- 위험 정책을 통한 직접 수정
- 수동 암호 다시 설정
- 사용자 위험 해제
- 수동으로 개별 위험 검색 닫기

### <a name="self-remediation-with-risk-policy"></a>위험 정책을 통한 직접 수정

사용자가 위험 정책에서 Azure AD MFA(Multi-Factor Authentication) 및 SSPR(셀프 서비스 암호 재설정)을 사용하여 직접 수정할 수 있게 하면 위험이 검색될 때 사용자가 직접 차단을 해제할 수 있습니다. 해당 검색은 닫힌 것으로 간주합니다. 위험이 검색될 때 사용하려면 사용자가 이전에 Azure AD MFA 및 SSPR에 등록되었어야 합니다.

일부 검색에서는 사용자가 직접 수정해야 하는 수준까지 위험이 증가하지 않을 수도 있지만 관리자는 관련 검색을 계속 평가해야 합니다. 관리자는 [여러 위치에서 액세스를 차단](../conditional-access/howto-conditional-access-policy-location.md)하거나 정책에서 허용 가능한 위험을 줄이는 것과 같은 추가 조치가 필요하다고 결정할 수 있습니다.

### <a name="manual-password-reset"></a>수동 암호 다시 설정

사용자 위험 정책을 사용하여 암호를 다시 설정하도록 요구할 수 없는 경우 관리자는 수동 암호 다시 설정을 통해 사용자의 모든 위험 검색을 닫을 수 있습니다.

관리자는 사용자의 암호를 다시 설정할 때 다음 두 가지 옵션을 사용할 수 있습니다.

- **임시 암호 생성** - 임시 암호를 생성하여 ID를 안전한 상태로 즉시 전환할 수 있습니다. 이 방법을 사용하려면 임시 암호가 무엇인지 알아야 하므로 영향을 받는 사용자에게 문의해야 합니다. 암호는 임시 암호이므로 사용자에게 다음 로그인 시 암호를 새 암호로 변경하라는 메시지가 표시됩니다.

- **사용자가 암호를 재설정해야 함** - 사용자가 암호를 재설정하도록 요구하면 지원 센터 또는 관리자에게 문의하지 않고도 셀프 복구가 가능합니다. 이 방법은 Azure AD MFA 및 SSPR에 등록된 사용자에게만 적용됩니다. 아직 등록되지 않은 사용자의 경우 이 옵션을 사용할 수 없습니다.

### <a name="dismiss-user-risk"></a>사용자 위험 해제

예를 들어, 사용자가 삭제되어 암호를 다시 설정할 수 없는 경우에는 사용자 위험 검색을 해제하도록 선택할 수 있습니다.

**사용자 위험 해제** 를 클릭하면 모든 이벤트가 닫히고 영향을 받는 사용자가 더 이상 위험에 노출되지 않습니다. 그러나 이 방법은 기존 암호에 영향을 주지 않으므로 관련된 ID를 안전한 상태로 다시 전환하지 않습니다. 

### <a name="close-individual-risk-detections-manually"></a>수동으로 개별 위험 검색 닫기

수동으로 개별 위험 검색을 닫을 수 있습니다. 위험 검색을 수동으로 닫으면 사용자 위험 수준을 낮출 수 있습니다. 일반적으로 위험 검색은 관련된 조사에 대한 응답에서 수동으로 닫힙니다. 예를 들어, 사용자와 대화하면 활성 위험 검색이 더 이상 필요하지 않는 것으로 나타납니다. 
 
위험 검색을 수동으로 닫는 경우 다음 작업 중 하나를 수행하여 위험 검색 상태를 변경하도록 선택할 수 있습니다.

- 사용자 손상됨 확인
- 사용자 위험 해제
- 로그인 보안 확인
- 로그인 손상됨 확인

## <a name="unblocking-users"></a>사용자 차단 해제

관리자는 위험 정책 또는 조사에 따라 로그인을 차단하도록 선택할 수 있습니다. 차단은 로그인 또는 사용자 위험에 따라 발생할 수 있습니다.

### <a name="unblocking-based-on-user-risk"></a>사용자 위험에 따라 차단 해제

사용자 위험으로 인해 차단된 계정의 차단을 해제하기 위해 관리자는 다음 옵션을 사용할 수 있습니다.

1. **암호 재설정** - 사용자의 암호를 다시 설정할 수 있습니다.
1. **사용자 위험 해제** - 액세스 차단을 위해 구성된 사용자 위험 수준에 도달하면 사용자 위험 정책이 사용자를 차단합니다. 사용자 위험을 해제하거나 보고된 위험 검색을 수동으로 닫아서 사용자의 위험 수준을 줄일 수 있습니다.
1. **정책에서 사용자 제외** - 로그인 정책의 현재 구성으로 인해 특정 사용자에 관련된 문제가 발생하고 있다고 생각되면 로그인 정책에서 해당 사용자를 제외할 수 있습니다. 자세한 내용은 [방법: 위험 정책 구성 및 사용](howto-identity-protection-configure-risk-policies.md#exclusions) 문서에서 제외 섹션을 참조하세요.
1. **정책 비활성화** - 정책 구성이 모든 사용자에 대해 문제를 일으킨다고 생각하는 경우 정책을 비활성화할 수 있습니다. 자세한 내용은 [방법: 위험 정책 구성 및 사용](howto-identity-protection-configure-risk-policies.md) 문서를 참조하세요.

### <a name="unblocking-based-on-sign-in-risk"></a>로그인 위험에 따라 차단 해제

로그인 위험에 따라 계정의 차단을 해제하기 위해 관리자는 다음 옵션을 사용할 수 있습니다.

1. **친숙한 위치 또는 디바이스에서 로그인** - 차단된 의심스러운 로그인에 대한 일반적인 이유는 알 수 없는 위치 또는 디바이스에서의 로그인 시도입니다. 사용자사용자는 친숙한 위치 또는 디바이스에서 로그인을 시도하여 차단 이유인지 여부를 빠르게 확인할 수 있습니다.
1. **정책에서 사용자 제외** - 로그인 정책의 현재 구성으로 인해 특정 사용자에 관련된 문제가 발생하고 있다고 생각되면 로그인 정책에서 해당 사용자를 제외할 수 있습니다. 자세한 내용은 [방법: 위험 정책 구성 및 사용](howto-identity-protection-configure-risk-policies.md#exclusions) 문서에서 제외 섹션을 참조하세요.
1. **정책 비활성화** - 정책 구성이 모든 사용자에 대해 문제를 일으킨다고 생각하는 경우 정책을 비활성화할 수 있습니다. 자세한 내용은 [방법: 위험 정책 구성 및 사용](howto-identity-protection-configure-risk-policies.md) 문서를 참조하세요.

## <a name="powershell-preview"></a>PowerShell 미리 보기

Microsoft Graph PowerShell SDK 미리 보기 모듈을 사용하면 조직은 PowerShell을 통해 위험을 관리할 수 있습니다. 미리 보기 모듈 및 샘플 코드는 [Azure AD GitHub 리포지토리](https://github.com/AzureAD/IdentityProtectionTools)에서 찾을 수 있습니다. 

리포지토리에 포함된 `Invoke-AzureADIPDismissRiskyUser.ps1` 스크립트를 사용하면 조직에서 해당 디렉터리에 있는 모든 위험한 사용자를 해제할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure AD ID 보호의 개요는 [Azure AD ID 보호 개요](overview-identity-protection.md)를 참조하세요.
