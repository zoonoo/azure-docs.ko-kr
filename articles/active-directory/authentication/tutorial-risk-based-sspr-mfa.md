---
title: Azure Active Directory의 위험 기반 사용자 로그인 보호
description: 이 자습서에서는 계정에 대한 위험한 로그인 동작이 탐지되면 Azure ID 보호를 사용하여 사용자를 보호하도록 설정하는 방법을 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 05/11/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 718a38f4744b6a1f9b4ebd0112be07b2556f1c39
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83116067"
---
# <a name="tutorial-use-risk-detections-for-user-sign-ins-to-trigger-azure-multi-factor-authentication-or-password-changes"></a>자습서: 사용자 로그인에 대한 위험 탐지를 사용하여 Azure Multi-Factor Authentication 또는 암호 변경 트리거

사용자를 보호하기 위해 Azure AD(Active Directory)에서 위험한 동작에 자동으로 응답하는 위험 기반 정책을 구성할 수 있습니다. Azure AD ID 보호 정책은 로그인 시도를 자동으로 차단하거나 암호 변경 필요 또는 Azure Multi-Factor Authentication에 대한 프롬프트와 같은 추가 작업을 요구할 수 있습니다. 이러한 정책은 기존 Azure AD 조건부 액세스 정책에서 조직 구성에 대한 추가 계층으로 작동합니다. 사용자는 이러한 정책 중 하나에서 위험한 동작을 트리거하지 않을 수 있지만 보안을 손상시키려고 하면 조직을 보호합니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * Azure AD ID 보호에 사용할 수 있는 정책 이해
> * Azure Multi-Factor Authentication 등록 사용
> * 위험 기반 암호 변경 사용
> * 위험 기반 Multi-Factor Authentication 사용
> * 사용자 로그인 시도에 대한 위험 기반 정책 테스트

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하는 데 필요한 리소스와 권한은 다음과 같습니다.

* Azure AD Premium P2 평가판 이상의 라이선스를 사용하도록 설정되어 작동하는 Azure AD 테넌트
    * 필요한 경우, [체험 계정을 만드세요](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* *글로벌 관리자* 권한이 있는 계정
* 셀프 서비스 암호 재설정 및 Azure Multi-Factor Authentication을 사용하도록 구성된 Azure AD
    * 필요한 경우 [자습서를 완료하여 Azure AD SSPR을 사용하도록 설정](tutorial-enable-sspr.md)합니다.
    * 필요한 경우 [자습서를 완료하여 Azure Multi-Factor Authentication을 사용하도록 설정](tutorial-enable-azure-mfa.md)합니다.

## <a name="overview-of-azure-ad-identity-protection"></a>Azure AD ID 보호 개요

Microsoft는 매일 사용자 로그인 시도의 일환으로 익명화된 수조 개의 신호를 수집하고 분석합니다. 이러한 신호는 양호한 사용자 로그인 동작 패턴을 구축하고 잠재적으로 위험한 로그인 시도를 식별하는 데 도움이 됩니다. Azure AD ID 보호는 사용자 로그인 시도를 검토하고 의심스러운 동작이 있으면 추가 작업을 수행할 수 있습니다.

다음 작업 중 일부는 Azure AD ID 보호 위험 탐지를 트리거할 수 있습니다.

* 자격 증명이 유출된 사용자
* 익명 IP 주소에서의 로그인
* 비정상적 위치 간 이동 불가능
* 감염된 디바이스에서의 로그인
* 의심스러운 활동을 포함하는 IP 주소의 로그인
* 일반적이지 않은 위치에서의 로그인

사용자를 보호하고 의심스러운 활동에 대응하기 위해 Azure AD ID 보호에서 다음 세 가지 정책을 사용할 수 있습니다. 정책 적용을 설정 또는 해제하고, 정책을 적용할 사용자 또는 그룹을 선택하고, 로그인 시 액세스를 차단할지 아니면 추가 작업을 요구하는 메시지를 표시할지를 결정하도록 선택할 수 있습니다.

* 사용자 위험 정책
    * 자격 증명이 손상되었을 수 있는 사용자 계정을 식별하고 이에 응답합니다. 사용자에게 새 암호를 만들도록 요구하는 메시지를 표시할 수 있습니다.
* 로그인 위험 정책
    * 의심스러운 로그인 시도를 식별하고 이에 응답합니다. 사용자에게 Azure Multi-Factor Authentication을 사용하여 추가 확인 양식을 제공하도록 요구하는 메시지를 표시할 수 있습니다.
* MFA 등록 정책
    * 사용자가 Azure Multi-Factor Authentication에 등록되어 있어야 합니다. 로그인 위험 정책에서 MFA를 요구하는 메시지가 표시되는 경우 사용자는 Azure Multi-Factor Authentication에 이미 등록되어 있어야 합니다.

정책 사용자 또는 로그인 위험 정책을 사용하도록 설정하는 경우 *낮음 이상*, *중간 이상* 또는 *높음* 위험 수준 임계값을 선택할 수도 있습니다. 이러한 유연성을 통해 의심스러운 로그인 이벤트에 적극적으로 적용하려는 제어 수준을 결정할 수 있습니다.

Azure AD ID 보호에 대한 자세한 내용은 [Azure AD ID 보호란?](../identity-protection/overview-identity-protection.md)을 참조하세요.

## <a name="enable-mfa-registration-policy"></a>MFA 등록 정책 사용

Azure AD ID 보호에는 사용자가 Azure Multi-Factor Authentication에 등록하는 데 도움이 되는 기본 정책이 포함되어 있습니다. 추가 정책을 사용하여 로그인 이벤트를 보호하는 경우 사용자는 MFA에 이미 등록되어 있어야 합니다. 이 정책을 사용하도록 설정하면 사용자가 각 로그인 이벤트에서 MFA를 수행할 필요가 없습니다. 정책은 사용자의 등록 상태만 확인하고 필요한 경우 미리 등록하도록 요청합니다.

추가 Azure AD ID 보호 정책을 사용하도록 설정할 사용자에 대해 MFA 등록 정책을 사용하도록 설정하는 것이 좋습니다. 이 정책을 사용하도록 설정하려면 다음 단계를 수행합니다.

1. 전역 관리자 계정을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Azure Active Directory**를 검색하여 선택하고, **보안**을 선택한 다음, *보호* 메뉴 제목 아래에서 **ID 보호**를 선택합니다.
1. 왼쪽의 메뉴에서 **MFA 등록 정책**을 선택합니다.
1. 정책은 기본적으로 *모든 사용자*에게 적용됩니다. 원하는 경우 **할당**을 선택한 다음, 정책을 적용할 사용자 또는 그룹을 선택합니다.
1. *컨트롤* 아래에서 **액세스**를 선택합니다. *Azure MFA 등록 필요* 옵션이 선택되어 있는지 확인한 다음, **선택**을 선택합니다.
1. **정책 적용**을 *켜기*로 설정한 다음, **저장**을 선택합니다.

    ![사용자가 Azure Portal에서 MFA에 등록하도록 요구하는 방법의 스크린샷](./media/tutorial-risk-based-sspr-mfa/enable-mfa-registration.png)

## <a name="enable-user-risk-policy-for-password-change"></a>암호 변경에 대한 사용자 위험 정책 사용

Microsoft는 연구원, 법 집행 기관, Microsoft의 다양한 보안 팀, 신뢰할 수 있는 기타 소스와 협력하여 사용자 이름 및 암호 쌍을 찾습니다. 이러한 쌍 중 하나가 사용자 환경의 계정과 일치하면 위험 기반 암호 변경을 요청할 수 있습니다. 이 정책과 작업을 수행하려면 이전에 노출된 자격 증명이 더 이상 작동하지 않도록 로그인하기 전에 암호를 업데이트해야 합니다.

이 정책을 사용하도록 설정하려면 다음 단계를 수행합니다.

1. 왼쪽의 메뉴에서 **사용자 위험 정책**을 선택합니다.
1. 정책은 기본적으로 *모든 사용자*에게 적용됩니다. 원하는 경우 **할당**을 선택한 다음, 정책을 적용할 사용자 또는 그룹을 선택합니다.
1. *조건* 아래에서 **조건 선택 > 위험 수준 선택**을 차례로 선택한 다음, *중간 이상*을 선택합니다.
1. **선택**, **완료**를 차례로 선택합니다.
1. *액세스* 아래에서 **액세스**를 선택합니다. **액세스 허용** 및 *암호 변경 필요* 옵션이 선택되어 있는지 확인한 다음, **선택**을 선택합니다.
1. **정책 적용**을 *켜기*로 설정한 다음, **저장**을 선택합니다.

    ![Azure Portal에서 사용자 위험 정책을 사용하도록 설정하는 방법의 스크린샷](./media/tutorial-risk-based-sspr-mfa/enable-user-risk-policy.png)

## <a name="enable-sign-in-risk-policy-for-mfa"></a>MFA에 대한 로그인 위험 정책 사용

대부분의 사용자에게는 추적할 수 있는 정상적인 동작이 있습니다. 이 정상적인 동작에서 벗어나는 사용자인 경우 성공적인 로그인을 허용하는 것은 위험할 수 있습니다. 대신 해당 사용자를 차단하거나 다단계 인증을 수행하도록 요청하는 것이 좋습니다. 사용자가 MFA 챌린지를 성공적으로 완료하면 유효한 로그인 시도로 간주하고 애플리케이션 또는 서비스에 대한 액세스 권한을 부여할 수 있습니다.

이 정책을 사용하도록 설정하려면 다음 단계를 수행합니다.

1. 왼쪽의 메뉴에서 **로그인 위험 정책**을 선택합니다.
1. 정책은 기본적으로 *모든 사용자*에게 적용됩니다. 원하는 경우 **할당**을 선택한 다음, 정책을 적용할 사용자 또는 그룹을 선택합니다.
1. *조건* 아래에서 **조건 선택 > 위험 수준 선택**을 차례로 선택한 다음, *중간 이상*을 선택합니다.
1. **선택**, **완료**를 차례로 선택합니다.
1. *액세스* 아래에서 **컨트롤 선택**을 선택합니다. **액세스 허용** 및 *다단계 인증 필요* 옵션이 선택되어 있는지 확인한 다음, **선택**을 선택합니다.
1. **정책 적용**을 *켜기*로 설정한 다음, **저장**을 선택합니다.

    ![Azure Portal에서 로그인 위험 정책을 사용하도록 설정하는 방법의 스크린샷](./media/tutorial-risk-based-sspr-mfa/enable-sign-in-risk-policy.png)

## <a name="test-risky-sign-events"></a>위험한 서명 이벤트 테스트

대부분의 사용자 로그인 이벤트는 이전 단계에서 구성된 위험 기반 정책을 트리거하지 않습니다. 사용자에게 추가 MFA를 요구하거나 암호를 다시 설정하도록 요구하는 메시지가 표시되지 않을 수 있습니다. 자격 증명이 안전하게 유지되고 동작이 일관되면 로그인 이벤트가 성공적으로 수행됩니다.

이전 단계에서 만든 Azure AD ID 보호 정책을 테스트하려면 위험한 동작 또는 잠재적인 공격을 시뮬레이션할 수 있는 방법이 필요합니다. 이러한 테스트를 수행하는 단계는 유효성을 검사하려는 Azure AD ID 보호 정책에 따라 달라집니다. 시나리오 및 단계에 대한 자세한 내용은 [Azure AD ID 보호에서 위험 탐지 시뮬레이션](../identity-protection/howto-identity-protection-simulate-risk.md)을 참조하세요.

## <a name="clean-up-resources"></a>리소스 정리

테스트가 완료되었고 위험 기반 정책을 더 이상 사용하지 않도록 설정하려면 사용하지 않도록 설정하려는 각 정책으로 돌아가서 **정책 적용**을 *끄기*로 설정합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure AD ID 보호에 대한 위험 기반 사용자 정책을 사용하도록 설정했습니다. 구체적으로 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * Azure AD ID 보호에 사용할 수 있는 정책 이해
> * Azure Multi-Factor Authentication 등록 사용
> * 위험 기반 암호 변경 사용
> * 위험 기반 Multi-Factor Authentication 사용
> * 사용자 로그인 시도에 대한 위험 기반 정책 테스트

> [!div class="nextstepaction"]
> [Azure AD ID 보호에 대해 자세히 알아보기](../identity-protection/overview-identity-protection.md)
