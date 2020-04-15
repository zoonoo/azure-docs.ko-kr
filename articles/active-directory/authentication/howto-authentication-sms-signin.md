---
title: Azure Active Directory에 대한 SMS 기반 사용자 로그인
description: SMS(미리 보기)를 사용하여 사용자가 Azure Active Directory에 로그인하도록 구성하고 활성화하는 방법에 대해 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rateller
ms.collection: M365-identity-device-management
ms.openlocfilehash: b199854867c48906061cff65bad21e514456c911
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81378705"
---
# <a name="configure-and-enable-users-for-sms-based-authentication-using-azure-active-directory-preview"></a>Azure Active Directory(미리 보기)를 사용하여 SMS 기반 인증을 위한 사용자 구성 및 사용 설정

사용자가 응용 프로그램 및 서비스에 로그인하는 복잡성 및 보안 위험을 줄이기 위해 Azure Active Directory(Azure AD)는 여러 인증 옵션을 제공합니다. 현재 미리 보기 중인 SMS 기반 인증을 통해 사용자는 사용자 이름과 암호를 제공하거나 알 필요 없이 로그인할 수 있습니다. ID 관리자가 계정을 만든 후 로그인 프롬프트에 전화 번호를 입력하고 문자 메시지를 통해 전송되는 인증 코드를 제공할 수 있습니다. 이 인증 방법은 특히 일선 작업자의 응용 프로그램 및 서비스에 대한 액세스를 단순화합니다.

이 문서에서는 Azure AD의 일부 사용자 또는 그룹에 대해 SMS 기반 인증을 사용하도록 설정하는 방법을 보여 주며 이 문서에서는

|     |
| --- |
| 사용자에 대한 SMS 기반 인증은 Azure Active Directory의 공개 미리 보기 기능입니다. 미리 보기에 대한 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관을](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 참조하십시오.|
|     |

## <a name="before-you-begin"></a>시작하기 전에

이 문서를 완료하려면 다음과 같은 리소스와 권한이 필요합니다.

* 활성화된 Azure 구독.
    * Azure 구독이 없는 경우 [계정을 만듭니다.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* 구독과 연결된 Azure Active Directory 테넌트입니다.
    * 필요한 경우 [Azure Active Directory 테넌트를 만들거나][create-azure-ad-tenant][Azure 구독을 계정에 연결합니다][associate-azure-ad-tenant].
* SMS 기반 인증을 사용하려면 Azure AD 테넌트에서 *글로벌 관리자* 권한이 필요합니다.
* 문자 메시지 인증 방법 정책에서 활성화된 각 사용자는 사용하지 않더라도 라이선스가 부여되어야 합니다. 활성화된 각 사용자는 다음 Azure AD 또는 Microsoft 365 라이선스 중 하나를 가져야 합니다.
    * [Azure AD Premium P1 또는 P2][azuread-licensing]
    * [마이크로 소프트 365 F1 또는 F3][m365-firstline-workers-licensing]

## <a name="limitations"></a>제한 사항

SMS 기반 인증의 공개 미리 보기 중에 다음과 같은 제한 사항이 적용됩니다.

* SMS 기반 인증은 현재 Azure 다단계 인증과 호환되지 않습니다.
* 팀을 제외한 SMS 기반 인증은 현재 네이티브 Office 응용 프로그램과 호환되지 않습니다.
* B2B 계정에는 SMS 기반 인증이 권장되지 않습니다.
* 페더레이션된 사용자는 홈 테넌트에서 인증되지 않습니다. 클라우드에서만 인증합니다.

## <a name="enable-the-sms-based-authentication-method"></a>SMS 기반 인증 방법 사용

조직에서 SMS 기반 인증을 활성화하고 사용하는 세 가지 주요 단계가 있습니다.

* 인증 방법 정책을 사용하도록 설정합니다.
* SMS 기반 인증 방법을 사용할 수 있는 사용자 또는 그룹을 선택합니다.
* 각 사용자 계정에 전화 번호를 할당합니다.
    * 이 전화 번호는 Azure 포털(이 문서에 표시됨)과 *내 직원* 또는 *내 프로필*에 할당할 수 있습니다.

먼저 Azure AD 테넌트에 대해 SMS 기반 인증을 사용하도록 설정해 보겠습니다.

1. [Azure 포털에][azure-portal] 글로벌 *관리자로*로그인합니다.
1. **Azure Active Directory**를 검색하고 선택합니다.
1. Azure Active Directory 창의 왼쪽에 있는 탐색 메뉴에서 **보안 > 인증 방법 > 인증 메서드 정책(미리 보기)을**선택합니다.

    [![](media/howto-authentication-sms-signin/authentication-method-policy-cropped.png "Browse to and select the Authentication method policy (preview) window in the Azure portal")](media/howto-authentication-sms-signin/authentication-method-policy.png#lightbox)

1. 사용 가능한 인증 방법 목록에서 **문자 메시지를**선택합니다.
1. **사용 설정을** *예로 설정합니다.*

    ![인증 방법 정책 창에서 텍스트 인증 사용](./media/howto-authentication-sms-signin/enable-text-authentication-method.png)

    *모든 사용자* 또는 사용자 및 그룹 *선택에* 대해 SMS 기반 인증을 사용하도록 선택할 수 있습니다. 다음 섹션에서는 테스트 사용자에 대 한 SMS 기반 인증을 사용 합니다.

## <a name="assign-the-authentication-method-to-users-and-groups"></a>사용자 및 그룹에 인증 방법 할당

Azure AD 테넌트에서 SMS 기반 인증을 사용하도록 설정하면 이제 이 인증 방법을 사용할 수 있도록 일부 사용자 또는 그룹을 선택합니다.

1. 문자 메시지 인증 정책 창에서 **대상을** *사용자 선택으로*설정합니다.
1. 사용자 **또는 그룹 추가를**선택한 다음 *Contoso* 사용자 또는 *Contoso SMS 사용자와*같은 테스트 사용자 또는 그룹을 선택합니다.

    [![](media/howto-authentication-sms-signin/add-users-or-groups-cropped.png "Choose users or groups to enable for SMS-based authentication in the Azure portal")](media/howto-authentication-sms-signin/add-users-or-groups.png#lightbox)

1. 사용자 또는 그룹을 선택한 경우 **선택을**선택한 다음 업데이트된 인증 방법 정책 **저장을** 선택합니다.

문자 메시지 인증 방법 정책에서 활성화된 각 사용자는 사용하지 않더라도 라이선스가 부여되어야 합니다. 인증 방법 정책에서 활성화한 사용자에 적합한 라이선스가 있는지 확인합니다( 특히 대규모 사용자 그룹에 대한 기능을 사용하도록 설정하는 경우).

## <a name="set-a-phone-number-for-user-accounts"></a>사용자 계정에 대한 전화 번호 설정

이제 사용자는 SMS 기반 인증을 사용할 수 있지만 로그인하려면 전화 번호가 Azure AD의 사용자 프로필과 연결되어야 합니다. 사용자는 *내 프로필에서* [이 전화 번호를 스스로 설정하거나](../user-help/sms-sign-in-explainer.md) Azure 포털을 사용하여 전화 번호를 할당할 수 있습니다. 전화 번호는 *글로벌 관리자,* *인증 관리자*또는 권한 있는 인증 관리자가 설정할 수 *있습니다.*

SMS-sign에 대해 전화 번호를 설정한 경우 [Azure 다단계 인증][tutorial-azure-mfa] 및 셀프 [서비스 암호 재설정과][tutorial-sspr]함께 사용할 수도 있습니다.

1. **Azure Active Directory**를 검색하고 선택합니다.
1. Azure Active Directory 창의 왼쪽에 있는 탐색 메뉴에서 **사용자를**선택합니다.
1. *Contoso User와*같은 이전 섹션에서 SMS 기반 인증에 대해 사용하도록 설정한 사용자를 선택한 다음 **인증 방법을**선택합니다.
1. 국가 코드를 포함 하 여 사용자의 전화 번호를 입력 (예: *+1 xxxxxxxxx .* Azure 포털은 전화 번호가 올바른 형식임을 확인합니다.

    ![Azure 포털의 사용자가 SMS 기반 인증에 사용할 전화 번호를 설정합니다.](./media/howto-authentication-sms-signin/set-user-phone-number.png)

    전화 번호는 테넌트에서 고유해야 합니다. 여러 사용자에 대해 동일한 전화 번호를 사용하려고 하면 오류 메시지가 표시됩니다.

1. 사용자의 계정에 전화 번호를 적용하려면 **저장을**선택합니다.

프로비전이 성공적으로 완료되면 *SMS 로그인 이 활성화된*에 대한 확인 표시가 나타납니다.

## <a name="test-sms-based-sign-in"></a>SMS 기반 로그인 테스트

이제 SMS 기반 로그인에 사용할 수 있는 사용자 계정을 테스트하려면 다음 단계를 완료하십시오.

1. 새 InPrivate 또는 시크릿 웹 브라우저 창을 엽니다.[https://www.office.com][office]
1. 오른쪽 상단 모서리에서 **로그인을**선택합니다.
1. 로그인 프롬프트에서 이전 섹션에서 사용자와 연결된 전화 번호를 입력한 다음 **다음을**선택합니다.

    ![테스트 사용자의 로그인 프롬프트에 전화 번호를 입력합니다.](./media/howto-authentication-sms-signin/sign-in-with-phone-number.png)

1. 제공된 전화 번호로 문자 메시지가 전송됩니다. 로그인 프로세스를 완료하려면 로그인 프롬프트에서 문자 메시지에 제공된 6자리 코드를 입력합니다.

    ![사용자의 전화 번호로 문자 메시지를 통해 전송된 확인 코드를 입력합니다.](./media/howto-authentication-sms-signin/sign-in-with-phone-number-confirmation-code.png)

1. 이제 사용자 이름이나 암호를 제공할 필요 없이 로그인됩니다.

## <a name="troubleshoot-sms-based-sign-in"></a>SMS 기반 로그인 문제 해결

SMS 기반 로그인을 활성화하고 사용하는 데 문제가 있는 경우 다음 시나리오 및 문제 해결 단계를 사용할 수 있습니다.

### <a name="phone-number-already-set-for-a-user-account"></a>사용자 계정에 대해 이미 설정된 전화 번호

사용자가 Azure 다단계 인증 및/또는 셀프 서비스 암호 재설정(SSPR)에 이미 등록한 경우 이미 계정과 연결된 전화 번호가 있습니다. 이 전화 번호는 SMS 기반 로그인과 함께 자동으로 사용할 수 없습니다.

계정에 대해 이미 설정된 전화 번호가 있는 사용자에게 **내 프로필** 페이지에서 *SMS 로그인 사용 버튼이* 표시됩니다. 이 단추를 선택하면 계정이 SMS 기반 로그인 및 이전 Azure 다단계 인증 또는 SSPR 등록과 함께 사용할 수 있도록 활성화됩니다.

최종 사용자 경험에 대한 자세한 내용은 [전화 번호에 대한 SMS 로그인 사용자 환경(미리 보기)을](../user-help/sms-sign-in-explainer.md)참조하십시오.

### <a name="error-when-trying-to-set-a-phone-number-on-a-users-account"></a>사용자 계정에 전화 번호를 설정하려고 할 때 오류가 발생했습니다.

Azure 포털에서 사용자 계정에 대한 전화 번호를 설정하려고 할 때 오류가 발생하면 다음 문제 해결 단계를 검토하십시오.

1. SMS 기반 로그인 미리 보기를 사용하도록 설정했는지 확인합니다.
1. *문자 메시지* 인증 방법 정책에서 사용자 계정이 활성화되어 있는지 확인합니다.
1. Azure 포털에서 유효성을 검사한 대로(예: *+1 4251234567)으로*전화 번호를 설정해야 합니다.
1. 테넌트의 다른 곳에서 전화 번호가 사용되지 않는지 확인합니다.
1. 계정에 음성 번호가 설정되어 있지 않은지 확인합니다. 음성 번호가 설정된 경우 삭제하고 전화 번호를 다시 시도합니다.

## <a name="next-steps"></a>다음 단계

Microsoft 인증자 앱 또는 FIDO2 보안 키와 같은 암호 없이 Azure AD에 로그인하는 추가 방법은 [Azure AD에 대한 암호 없는 인증 옵션을][concepts-passwordless]참조하십시오.

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../fundamentals/active-directory-how-subscriptions-associated-directory.md
[concepts-passwordless]: concept-authentication-passwordless.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[tutorial-sspr]: tutorial-enable-sspr.md

<!-- EXTERNAL LINKS -->
[azure-portal]: https://portal.azure.com
[office]: https://www.office.com
[m365-firstline-workers-licensing]: https://www.microsoft.com/licensing/news/m365-firstline-workers
[azuread-licensing]: https://azure.microsoft.com/pricing/details/active-directory/
