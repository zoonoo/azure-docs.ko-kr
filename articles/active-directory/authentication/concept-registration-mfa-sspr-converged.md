---
title: Azure AD SSPR 및 MFA에 대한 등록 수렴(공개 미리 보기)
description: Azure AD Multi-Factor Authenticaiton 및 셀프 서비스 암호 재설정 등록(공개 미리 보기)
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry, michmcla
ms.openlocfilehash: af57faddcc1413747b4bb847e27287ba86562175
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2018
ms.locfileid: "42144340"
---
# <a name="converged-registration-for-self-service-password-reset-and-azure-multi-factor-authentication-public-preview"></a>셀프 서비스 암호 재설정 및 Azure Multi-Factor Authentication에 대한 등록 수렴(공개 미리 보기)

지금까지는 사용자가 두 개의 다른 포털에서 Azure MFA(Multi-Factor Authentication) 및 SSPR(셀프 서비스 암호 재설정)에 대한 인증 방법을 등록해야 했습니다. Azure MFA와 SSPR 모두에서 유사한 방법이 사용되기 때문에 많은 사용자가 혼란을 겪었고 두 포털 모두에 등록하지 않았습니다. 이러한 차이로 인해 일부 사용자는 Azure MFA 또는 SSPR을 필요할 때 사용할 수 없어서 기술 지원팀에 전화를 하기도 하면서 사용자에게 많은 불편을 초래했습니다. 이제는 사용자가 한 번 등록하면 Azure MFA와 SSPR의 이점을 모두 누릴 수 있으며, 이러한 기능을 위해 인증 방법을 두 번 등록할 필요가 없습니다.  

조직에서 새로운 환경을 활성화하기 전에 이 문서는 물론 [최종 사용자 설명서](https://aka.ms/securityinfoguide)를 검토하여 이 환경이 사용자에게 미치는 영향을 이해하는 것이 좋습니다. [최종 사용자 설명서](https://aka.ms/securityinfoguide)를 사용하여 새로운 환경에 대비하여 사용자를 교육하고 성공적인 출시를 보장할 수 있습니다.

|     |
| --- |
| 셀프 서비스 암호 재설정 및 Azure Multi-Factor Authentication에 대한 등록 수렴은 Azure Active Directory의 공개 미리 보기 기능입니다. 미리 보기에 대한 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.|
|     |

사용자가 단일 환경에서 Azure Multi-Factor Authentication과 셀프 서비스 암호 재설정에 대한 인증 방법을 등록할 수 있도록 하려면 다음 단계를 완료하세요.

1. 글로벌 관리자 또는 사용자 관리자로 Azure Portal에 로그인합니다.
2. **Azure Active Directory**, **사용자 설정**, **액세스 패널 미리 보기 기능의 설정 관리**로 이동합니다.
3. **사용자가 보안 정보를 등록하고 관리하기 위한 미리 보기 기능을 사용 가능**에서 **선택한** 사용자 그룹이나 **모든** 사용자가 사용할 수 있도록 선택할 수 있습니다.

이제 사용자는 [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo)로 이동하여 MFA 및 SSPR에 대한 인증 방법을 등록할 수 있습니다. 새로운 환경에서 사용자에게 표시되는 내용에 대해 자세히 알아보려면 [최종 사용자 설명서](https://aka.ms/securityinfoguide)를 참조하세요.  

> [!NOTE]
> 이 환경을 활성화하면 새로운 환경을 통해 전화 번호나 모바일 앱을 등록하거나 확인한 사용자는 MFA 및 SSPR 정책에 해당 방법을 사용하도록 설정된 경우 MFA 및 SSPR을 사용할 수 있습니다. 그런 다음, 이 환경을 비활성화하면 이전 SSPR 등록 페이지(aka.ms/ssprsetup)로 이동하는 사용자에게 페이지에 액세스하려면 MFA를 수행해야 한다는 메시지가 표시됩니다.  

## <a name="how-it-works"></a>작동 방법

이전에 별도의 MFA 및 SSPR 등록 환경을 통해 인증 방법을 등록한 사용자는 해당 정보를 다시 등록할 필요가 없습니다. 단, 사용자에게 MFA 또는 SSPR 등록을 요구하도록 설정한 경우에는 사용자가 로그인할 때 보안 정보를 검토하라는 프롬프트가 표시될 수 있습니다.

사용자가 MFA에 사용될 수 있는 방법을 등록한 경우에는 새 환경에 액세스하려면 MFA를 수행하라는 메시지가 표시됩니다.

MFA 또는 SSPR에 대한 등록을 적용한 후 아직 등록하지 않은 사용자에게는 로그인할 때 등록하라는 메시지가 표시됩니다.

로그인하는 동안 등록하라는 메시지가 표시되는 사용자에게는 다음과 같은 환경이 표시됩니다.

![등록 수렴 새 사용자로 방법 설정](./media/concept-registration-mfa-sspr-converged/concept-registration-add-methods.png)

> [!NOTE]
> 이 환경은 로그인하는 동안 등록하라는 메시지가 사용자에게 표시되는 경우에만 표시됩니다. 새 환경(aka.ms/setupsecurityinfo)으로 바로 이동한 사용자에게는 다른 버전의 환경이 표시되며 이 내용은 이 문서의 뒷부분에 설명되어 있습니다.

MFA 또는 SSPR 정책에 활성화된 방법을 기반으로 표시되는 인증 방법이 변경됩니다. MFA 정책, SSPR 정책 또는 두 가지 정책 모두를 준수하는 데 필요한 최소 인증 방법 수를 등록하라는 메시지가 표시됩니다. 사용자가 등록할 수 있는 인증 방법에 유연성이 있으면 **Choose security info**(보안 정보 선택)를 선택하여 다른 인증 방법을 선택할 수 있습니다.  

> [!NOTE]
> 모바일 앱 알림 및 모바일 앱 코드 둘 다 사용하도록 설정한 경우 알림을 사용하여 Microsoft Authenticator 앱을 등록하려는 사용자는 알림 및 코드 모두를 사용하여 해당 ID를 확인할 수 있습니다.

이전 MFA 등록 환경과 달리 새 등록 환경에서 새 앱 암호를 등록하라는 메시지가 사용자에게 표시되지 않습니다. 대신 앱 암호 자습서에 나와 있는 단계에 따라 새 환경에서 앱 암호를 등록해야 합니다.  

사용자가 등록을 완료하면 기본 MFA 방법이 자동으로 설정됩니다. 사용자가 인증자 앱을 등록하면 기본 방법이 앱으로 설정됩니다. 사용자가 인증자 앱을 등록하지 않고 전화 번호만 등록한 경우에는 기본 방법이 전화 통화로 설정됩니다. 사용자가 [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo)로 이동하여 **기본값 변경**를 선택하면 기본값을 변경할 수 있습니다.  

등록이 적용되지 않으면 [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo)에서 사용자가 자신의 인증 방법을 관리할 수 있습니다. 사용자가 MFA에 사용될 수 있는 방법을 이전에 등록한 경우 페이지에 액세스하려면 MFA를 수행하라는 메시지가 표시됩니다.  

![등록 수렴 등록된 사용자로 방법 편집](./media/concept-registration-mfa-sspr-converged/concept-registration-edit-methods.png)

이 페이지에는 이전에 등록한 인증 방법과 사무실 전화와 같이 등록된 인증 방법이 표시됩니다. 사용자는 인증 방법(사무실 전화 제외)을 추가, 편집 또는 삭제할 수 있습니다.  

새로운 환경에 대한 감사 로그는 감사 로그의 인증 방법 범주에 있습니다.  

## <a name="known-issues"></a>알려진 문제

**사용자가 문자 메시지를 사용하여 전화를 등록하면 기본 MFA 방식이 전화 통화로 설정됩니다.**

   * 일부 사용자는 문자 메시지를 사용하여 전화 번호를 등록한 후 기본 MFA 방법이 전화 통화로 설정된 것을 알 수 있습니다. 사용자는 [보안 정보 관리(미리 보기)](../user-help/security-info-manage-settings.md#change-your-info) 문서에 있는 지침에 따라 해당 기본 메서드를 변경하여 이 문제를 해결할 수 있습니다.

**관리자가 기본 방법을 사용하지 않도록 설정한 후 사용자가 새 등록 환경에 액세스할 수 없습니다.**

   * 사용자가 이전에 등록한 기본 MFA 방법을 관리자가 사용하지 않도록 설정한 경우 일부 사용자가 새 등록 환경에 액세스하지 못할 수 있습니다. 다음은 예제 시나리오입니다.
      1. 사용자가 이전에 전화 번호를 등록했고 전화 통화를 기본 방법으로 설정했습니다.
      2. 관리자가 테넌트에 대한 MFA 방법으로 전화 통화를 사용하지 않도록 설정했습니다.
      3. 사용자가 테넌트 SSPR 정책을 충족하기 위한 추가 방법을 등록해야 하기 때문에 로그인 중에 등록하라는 메시지가 표시됩니다.
      4. 사용자가 등록을 시도하지만 기본 방법이 설정되어 있지 않아서 페이지에 액세스할 수 없고 루프에 빠지게 됩니다.

## <a name="next-steps"></a>다음 단계

[Azure AD 셀프 서비스 암호 재설정을 배포하는 방법 알아보기](howto-sspr-deployment.md)

[로그인할 때 다단계 인증을 요구하는 방법](howto-mfa-getstarted.md)

[최종 사용자 인증 방법 구성 설명서](https://aka.ms/securityinfoguide)