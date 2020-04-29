---
title: Azure Active Directory에 대 한 SMS 기반 사용자 로그인
description: 사용자가 SMS (미리 보기)를 사용 하 여 Azure Active Directory에 로그인 하도록 구성 하 고 사용 하도록 설정 하는 방법을 알아봅니다.
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
ms.openlocfilehash: 845948d9aec28ee79a11fb11aaef4cfbf1b263fa
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81770562"
---
# <a name="configure-and-enable-users-for-sms-based-authentication-using-azure-active-directory-preview"></a>Azure Active Directory를 사용 하 여 SMS 기반 인증용 사용자 구성 및 사용 (미리 보기)

사용자가 응용 프로그램 및 서비스에 로그인 할 때의 복잡성 및 보안 위험을 줄이기 위해 Azure Active Directory (Azure AD)는 여러 인증 옵션을 제공 합니다. 현재 미리 보기로 제공 되는 SMS 기반 인증을 사용 하면 사용자의 사용자 이름과 암호를 제공 하거나 몰라도 사용자가 로그인 할 수 있습니다. Id 관리자가 해당 계정을 만든 후 로그인 프롬프트에 전화 번호를 입력 하 고 문자 메시지를 통해 전송 되는 인증 코드를 제공할 수 있습니다. 이 인증 방법을 사용 하면 특히 프런트 라인 작업자의 응용 프로그램 및 서비스에 간편 하 게 액세스할 수 있습니다.

이 문서에서는 Azure AD에서 선택 사용자 또는 그룹에 대해 SMS 기반 인증을 사용 하도록 설정 하는 방법을 보여 줍니다.

|     |
| --- |
| 사용자를 위한 SMS 기반 인증은 Azure Active Directory의 공개 미리 보기 기능입니다. 미리 보기에 대 한 자세한 내용은 [Microsoft Azure 미리 보기의 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 을 참조 하세요.|
|     |

## <a name="before-you-begin"></a>시작하기 전에

이 문서를 완료 하려면 다음 리소스와 권한이 필요 합니다.

* 활성화된 Azure 구독.
    * Azure 구독이 없는 경우 [계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* 구독과 연결 된 Azure Active Directory 테 넌 트입니다.
    * 필요한 경우 [Azure Active Directory 테넌트를 만들거나][create-azure-ad-tenant][Azure 구독을 계정에 연결합니다][associate-azure-ad-tenant].
* SMS 기반 인증을 사용 하도록 설정 하려면 Azure AD 테 넌 트에서 *전역 관리자* 권한이 필요 합니다.
* 문자 메시지 인증 방법 정책에서 사용 하도록 설정 된 각 사용자는 사용 하지 않는 경우에도 사용이 허가 되어야 합니다. 사용 하도록 설정 된 각 사용자에 게는 다음 Azure AD 또는 Microsoft 365 라이선스 중 하나가 있어야 합니다.
    * [Azure AD Premium P1 또는 P2][azuread-licensing]
    * [Microsoft 365 (M365) F1 또는 F3][m365-firstline-workers-licensing]
    * [EMS (Enterprise Mobility + Security) e3 또는 e5][ems-licensing] 또는 [Microsoft 365 (M365) e3 또는 e5][m365-licensing]

## <a name="limitations"></a>제한 사항

SMS 기반 인증의 공개 미리 보기 중에는 다음과 같은 제한 사항이 적용 됩니다.

* SMS 기반 인증은 현재 Azure Multi-Factor Authentication와 호환 되지 않습니다.
* 팀을 제외 하 고, SMS 기반 인증은 현재 네이티브 Office 응용 프로그램과 호환 되지 않습니다.
* B2B 계정에는 SMS 기반 인증을 사용 하지 않는 것이 좋습니다.
* 페더레이션된 사용자는 홈 테 넌 트에서 인증 하지 않습니다. 클라우드에서 인증 합니다.

## <a name="enable-the-sms-based-authentication-method"></a>SMS 기반 인증 방법 사용

조직에서 SMS 기반 인증을 사용 하도록 설정 하 고 사용 하는 세 가지 주요 단계는 다음과 같습니다.

* 인증 방법 정책을 사용 하도록 설정 합니다.
* SMS 기반 인증 방법을 사용할 수 있는 사용자 또는 그룹을 선택 합니다.
* 각 사용자 계정에 대 한 전화 번호를 할당 합니다.
    * 이 전화 번호는이 문서에 나와 있는 Azure Portal 및 *내 직원* 또는 *내 프로필*에 할당할 수 있습니다.

먼저 Azure AD 테 넌 트에 대해 SMS 기반 인증을 사용 하도록 설정 합니다.

1. *전역 관리자 권한*으로 [Azure Portal][azure-portal] 에 로그인 합니다.
1. **Azure Active Directory**를 검색하고 선택합니다.
1. Azure Active Directory 창 왼쪽의 탐색 메뉴에서 **보안 > 인증 방법 > 인증 방법 정책 (미리 보기)** 을 선택 합니다.

    [![](media/howto-authentication-sms-signin/authentication-method-policy-cropped.png "Browse to and select the Authentication method policy (preview) window in the Azure portal")](media/howto-authentication-sms-signin/authentication-method-policy.png#lightbox)

1. 사용 가능한 인증 방법 목록에서 **문자 메시지**를 선택 합니다.
1. **사용** 을 *예*로 설정 합니다.

    ![인증 방법 정책 창에서 텍스트 인증 사용](./media/howto-authentication-sms-signin/enable-text-authentication-method.png)

    *모든 사용자* 에 대해 SMS 기반 인증을 사용 하도록 설정 하거나 사용자 및 그룹을 *선택할* 수 있습니다. 다음 섹션에서는 테스트 사용자에 대해 SMS 기반 인증을 사용 하도록 설정 합니다.

## <a name="assign-the-authentication-method-to-users-and-groups"></a>사용자 및 그룹에 인증 방법 할당

Azure AD 테 넌 트에서 SMS 기반 인증을 사용 하도록 설정한 상태에서이 인증 방법을 사용할 수 있는 사용자 또는 그룹을 선택 합니다.

1. 문자 메시지 인증 정책 창에서 **대상** 을 선택 하 여 *사용자를 선택*합니다.
1. **사용자 또는 그룹을 추가**하도록 선택한 다음 테스트 사용자 또는 그룹 (예: *Contoso USER* 또는 *contoso SMS 사용자*)을 선택 합니다.

    [![](media/howto-authentication-sms-signin/add-users-or-groups-cropped.png "Choose users or groups to enable for SMS-based authentication in the Azure portal")](media/howto-authentication-sms-signin/add-users-or-groups.png#lightbox)

1. 사용자 또는 그룹을 선택한 경우 **선택**을 선택 하 고 업데이트 된 인증 방법 정책을 **저장** 합니다.

문자 메시지 인증 방법 정책에서 사용 하도록 설정 된 각 사용자는 사용 하지 않는 경우에도 사용이 허가 되어야 합니다. 인증 방법 정책에서 사용 하도록 설정 하는 사용자에 대 한 적절 한 라이선스가 있는지 확인 합니다. 특히, 사용자 그룹에 대해이 기능을 사용 하도록 설정 하는 경우에 해당 합니다.

## <a name="set-a-phone-number-for-user-accounts"></a>사용자 계정에 대 한 전화 번호 설정

이제 사용자가 SMS 기반 인증을 사용할 수 있지만 로그인 하려면 Azure AD의 사용자 프로필과 연결 되어 있어야 합니다. 사용자는 *내 프로필*에서 [이 전화 번호를 직접 설정](../user-help/sms-sign-in-explainer.md) 하거나 Azure Portal를 사용 하 여 전화 번호를 할당할 수 있습니다. 전화 번호는 *전역 관리자*, *인증 관리자*또는 *권한 있는 인증 관리자*가 설정할 수 있습니다.

전화 번호가 SMS 서명에 대해 설정 된 경우 [Azure Multi-Factor Authentication][tutorial-azure-mfa] 및 [셀프 서비스 암호 재설정][tutorial-sspr]에도 사용할 수 있습니다.

1. **Azure Active Directory**를 검색하고 선택합니다.
1. Azure Active Directory 창의 왼쪽 탐색 메뉴에서 **사용자**를 선택 합니다.
1. 이전 섹션에서 (예: *Contoso user*) SMS 기반 인증을 사용 하도록 설정한 사용자를 선택한 다음 **인증 방법**을 선택 합니다.
1. 사용자의 전화 번호 (예: *+ 1 xxxxxxxxx)* 를 입력 합니다. Azure Portal는 전화 번호가 올바른 형식 인지 확인 합니다.

    ![SMS 기반 인증에 사용할 Azure Portal의 사용자에 대 한 전화 번호 설정](./media/howto-authentication-sms-signin/set-user-phone-number.png)

    전화 번호는 테 넌 트에서 고유 해야 합니다. 여러 사용자에 대해 동일한 전화 번호를 사용 하려고 하면 오류 메시지가 표시 됩니다.

1. 사용자의 계정에 전화 번호를 적용 하려면 **저장**을 선택 합니다.

성공적으로 프로 비전 되 면 *SMS 로그인을 사용 하도록 설정*된 확인 표시가 나타납니다.

## <a name="test-sms-based-sign-in"></a>SMS 기반 로그인 테스트

이제 SMS 기반 로그인에 사용할 수 있는 사용자 계정을 테스트 하려면 다음 단계를 완료 합니다.

1. 새 InPrivate 또는 Incognito 웹 브라우저 창을 엽니다.[https://www.office.com][office]
1. 오른쪽 위 모서리에서 **로그인**을 선택 합니다.
1. 로그인 프롬프트에서 이전 섹션의 사용자와 연결 된 전화 번호를 입력 하 고 **다음**을 선택 합니다.

    ![테스트 사용자에 대 한 로그인 프롬프트에 전화 번호를 입력 합니다.](./media/howto-authentication-sms-signin/sign-in-with-phone-number.png)

1. 제공 된 전화 번호로 문자 메시지를 보냅니다. 로그인 프로세스를 완료 하려면 로그인 프롬프트에서 문자 메시지에 제공 된 6 자리 코드를 입력 합니다.

    ![문자 메시지를 통해 사용자의 전화 번호로 보낸 확인 코드를 입력 합니다.](./media/howto-authentication-sms-signin/sign-in-with-phone-number-confirmation-code.png)

1. 사용자는 사용자 이름 또는 암호를 제공할 필요 없이 로그인 됩니다.

## <a name="troubleshoot-sms-based-sign-in"></a>SMS 기반 로그인 문제 해결

SMS 기반 로그인을 설정 하 고 사용 하는 데 문제가 있는 경우 다음 시나리오 및 문제 해결 단계를 사용할 수 있습니다.

### <a name="phone-number-already-set-for-a-user-account"></a>사용자 계정에 대해 이미 설정 된 전화 번호

사용자가 이미 Azure Multi-Factor Authentication 및/또는 SSPR (셀프 서비스 암호 재설정)에 등록 한 경우 해당 계정과 연결 된 전화 번호가 있습니다. 이 전화 번호는 SMS 기반 로그인에 자동으로 사용할 수 없습니다.

계정에 대해 이미 설정 된 전화 번호가 있는 사용자는 **내 프로필** 페이지에서 *SMS 로그인을 사용 하도록 설정* 하는 단추를 표시 합니다. 이 단추를 선택 하면 계정이 SMS 기반 로그인 및 이전 Azure Multi-Factor Authentication 또는 SSPR 등록에 사용할 수 있도록 설정 됩니다.

최종 사용자 환경에 대 한 자세한 내용은 [전화 번호에 대 한 SMS 로그인 사용자 환경 (미리 보기)](../user-help/sms-sign-in-explainer.md)을 참조 하세요.

### <a name="error-when-trying-to-set-a-phone-number-on-a-users-account"></a>사용자의 계정에 전화 번호를 설정 하는 동안 오류가 발생 했습니다.

Azure Portal에서 사용자 계정에 대 한 전화 번호를 설정 하려고 할 때 오류가 표시 되 면 다음 문제 해결 단계를 검토 합니다.

1. SMS 기반 로그인 미리 보기에 대해 사용 하도록 설정 했는지 확인 합니다.
1. *텍스트 메시지* 인증 방법 정책에서 사용자 계정이 사용 하도록 설정 되어 있는지 확인 합니다.
1. Azure Portal에서 유효성을 검사 하 여 적절 한 형식으로 전화 번호를 설정 해야 합니다 (예: *+ 1 4251234567*).
1. 전화 번호가 테 넌 트의 다른 곳에서 사용 되지 않는지 확인 합니다.
1. 계정에 설정 된 음성 번호가 없는지 확인 합니다. 음성 번호가 설정 된 경우을 삭제 하 고 전화 번호를 다시 시도 합니다.

## <a name="next-steps"></a>다음 단계

Microsoft Authenticator App 또는 FIDO2 보안 키와 같은 암호 없이 Azure AD에 로그인 하는 다른 방법은 [AZURE ad에 대 한 암호 없는 인증 옵션][concepts-passwordless]을 참조 하세요.

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
[ems-licensing]: https://www.microsoft.com/microsoft-365/enterprise-mobility-security/compare-plans-and-pricing
[m365-licensing]: https://www.microsoft.com/microsoft-365/compare-microsoft-365-enterprise-plans
