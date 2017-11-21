---
title: "Microsoft Azure Multi-Factor Authentication 사용자 상태"
description: "Azure MFA의 사용자 상태에 대해 알아보세요."
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.assetid: 0b9fde23-2d36-45b3-950d-f88624a68fbd
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/26/2017
ms.author: joflore
ms.reviewer: richagi
ms.custom: it-pro
ms.openlocfilehash: 6a0f8cb76684a6efcc5e2d4be05493f18d5d4c76
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2017
---
# <a name="how-to-require-two-step-verification-for-a-user-or-group"></a>사용자 또는 그룹에 대해 2단계 인증을 요구하는 방법

2단계 인증을 요구하는 두 가지 방법이 있습니다. 첫 번째 옵션은 각 개별 사용자가 Azure MFA(Multi-Factor Authentication)를 사용하도록 설정하는 것입니다. 사용자가 개별적으로 설정되면 신뢰할 수 있는 IP 주소에서 로그인하거나 기억된 장치 기능이 설정되어 경우와 같이 몇 가지 예외를 제외하고는 항상 2단계 인증을 수행합니다. 두 번째 옵션은 특정 조건에서 2단계 인증을 요구하는 조건부 액세스 정책을 설정하는 것입니다.

>[!TIP] 
>두 가지 방법 중 하나를 선택하여 2단계 인증을 요구합니다. 사용자가 Azure MFA를 사용하도록 설정하면 모든 조건부 액세스 정책이 무시됩니다.

## <a name="which-option-is-right-for-you"></a>어떤 옵션이 적합한가요?

**사용자 상태를 변경하여 Azure MFA를 사용하도록 설정**하는 것이 2단계 인증을 요구하는 일반적인 방법입니다. 이 방법은 클라우드의 Azure MFA와 Azure MFA Server 모두에서 작동합니다. 사용하도록 설정한 모든 사용자는 동일한 환경에서 로그인할 때마다 2단계 인증을 수행합니다. 사용자가 사용하도록 설정되면 해당 사용자에게 영향을 줄 수 있는 모든 조건부 액세스 정책이 무시됩니다. 

**조건부 액세스 정책을 사용하여 Azure MFA를 사용하도록 설정**하는 것은 2단계 인증을 요구하는 것보다 더 유연한 방법입니다. Azure MFA는 클라우드에서만 작동하며 조건부 액세스는 [Azure Active Directory의 유료 기능](https://www.microsoft.com/cloud-platform/azure-active-directory-features)입니다. 개별 사용자뿐만 아니라 그룹에도 적용되는 조건부 액세스 정책을 만들 수 있습니다. 위험 수준이 높은 그룹에는 위험 수준이 낮은 그룹보다 더 많은 제한이 제공되거나 위험 수준이 높은 클라우드 앱에만 2단계 인증이 요구되고 위험 수준이 낮은 그룹에는 건너뛸 수 있습니다. 

두 옵션은 모두 인증 요구가 설정된 후에 사용자가 처음 로그인하면 Azure Multi-Factor Authentication에 등록하라는 메시지를 표시합니다. 또한 두 옵션은 모두 구성 가능한 [Azure Multi-Factor Authentication 설정](multi-factor-authentication-whats-next.md)에서도 작동합니다.

## <a name="enable-azure-mfa-by-changing-user-status"></a>사용자 상태를 변경하여 Azure MFA를 사용하도록 설정

Azure Multi-Factor Authentication의 사용자 계정은 다음과 같은 3가지 상태를 갖습니다.

| 가동 상태 | 설명 | 영향 받는 비브라우저 앱 |
|:---:|:---:|:---:|
| 사용 안 함 |Azure MFA(Multi-Factor Authentication)에 등록되지 않은 새 사용자에 대한 기본 상태입니다. |아니요 |
| 사용 |사용자가 Azure MFA에 등록되었지만 등록하지 않았습니다. 이 경우 다음에 로그인할 때 등록하라는 메시지가 표시됩니다. |아니요.  등록 프로세스가 완료될 때까지 계속 작업합니다. |
| 적용 |사용자가 등록되었으며 Azure MFA를 위한 등록 프로세스를 완료했습니다. |예.  앱에 앱 암호가 필요합니다. |

사용자의 상태는 관리자가 사용자를 Azure MFA에 등록했는지 그리고 사용자가 등록 프로세스를 완료했는지 여부를 반영합니다.

모든 사용자는 *사용 안 함*으로 시작합니다. Azure MFA에 사용자를 등록하면 상태는 *사용*으로 변경됩니다. 사용된 사용자가 로그인하고 등록 프로세스를 완료하면 상태는 변경 *적용*으로 변경됩니다.  

### <a name="view-the-status-for-a-user"></a>사용자 상태 보기

다음 단계를 통해 사용자 상태를 보고 관리할 수 있는 페이지에 액세스합니다.

1. 관리자로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **Azure Active Directory** > **사용자 및 그룹** > **모든 사용자**로 차례로 이동합니다.
3. **Multi-Factor Authentication**을 선택합니다.
   ![Multi-Factor Authentication 선택](./media/multi-factor-authentication-get-started-user-states/selectmfa.png)
4. 사용자 상태를 표시하는 새 페이지가 열립니다.
   ![다단계 인증 사용자 상태 - 스크린샷](./media/multi-factor-authentication-get-started-user-states/userstate1.png)

### <a name="change-the-status-for-a-user"></a>사용자 상태 변경

1. 이전 단계를 통해 다단계 인증 사용자 페이지로 이동합니다.
2. Azure MFA에 사용하려는 사용자를 찾습니다. 위쪽에서 보기를 변경해야 할 수 있습니다. 
   ![사용자 찾기 - 스크린샷](./media/multi-factor-authentication-get-started-cloud/enable1.png)
3. 이름 옆의 확인란을 선택합니다.
4. 오른쪽의 빠른 단계에서 **사용** 또는 **사용 안 함**을 선택합니다.
   ![선택한 사용자 사용 - 스크린샷](./media/multi-factor-authentication-get-started-cloud/user1.png)

   >[!TIP]
   >Azure MFA에 등록하면 *사용* 사용자는 *적용*으로 자동으로 전환됩니다. 사용자 상태를 수동으로 '적용'으로 변경하면 안됩니다. 

5. 열리는 팝업 창에서 선택한 내용을 확인합니다. 

사용자를 사용으로 설정한 후 전자 메일을 통해 알려야 합니다. 다음에 로그인할 때 등록하도록 요구된다고 알려줍니다. 또한 조직에서 최신 인증을 지원하지 않는 비브라우저 앱을 사용하는 경우 앱 암호를 만들어야 합니다. 또한 사용자가 시작할 수 있도록 [Azure MFA 최종 사용자 가이드](./end-user/multi-factor-authentication-end-user.md)에 연결되는 링크를 포함할 수도 있습니다.

### <a name="use-powershell"></a>PowerShell 사용
[Azure AD PowerShell](/powershell/azure/overview)을 사용하여 사용자 상태를 변경하려면 `$st.State`를 변경합니다. 여기에는 세 가지 상태가 있습니다.

* 사용
* 적용
* 사용 안 함  

사용자를 *적용* 상태로 직접 전환하지 마세요. 사용자가 MFA 등록을 마치고 [앱 암호](multi-factor-authentication-whats-next.md#app-passwords)를 가져오지 못했기 때문에 비 브라우저 기반 앱은 작동을 중단합니다. 

PowerShell을 사용하는 방법은 사용자를 대량으로 사용하도록 설정해야 할 때 적합한 옵션입니다. 사용자 목록을 통해 반복하여 이 사용자들을 사용하도록 설정하는 PowerShell 스크립트를 만듭니다.

        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "*"
        $st.State = “Enabled”
        $sta = @($st)
        Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta

다음은 예제입니다.

    $users = "bsimon@contoso.com","jsmith@contoso.com","ljacobson@contoso.com"
    foreach ($user in $users)
    {
        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "*"
        $st.State = “Enabled”
        $sta = @($st)
        Set-MsolUser -UserPrincipalName $user -StrongAuthenticationRequirements $sta
    }

## <a name="enable-azure-mfa-with-a-conditional-access-policy"></a>조건부 액세스 정책으로 Azure MFA 사용

조건부 액세스는 여러 가지 구성 옵션이 가능한 Azure Active Directory의 유료 기능입니다. 이러한 단계는 정책을 만드는 한 가지 방법을 안내합니다. 자세한 내용은 [Azure Active Directory 조건부 액세스](../active-directory/active-directory-conditional-access-azure-portal.md)를 참조하세요.

1. 관리자로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **Azure Active Directory** > **조건부 액세스**로 차례로 이동합니다.
3. **새 정책**을 선택합니다.
4. **할당** 아래에서 **사용자 및 그룹**을 선택합니다. **포함** 및 **제외** 탭을 사용하여 정책별로 관리될 사용자와 그룹을 지정합니다.
5. **할당** 아래에서 **클라우드 앱**을 선택합니다. **모든 클라우드 앱**을 포함하도록 선택합니다.
6. **액세스 제어** 아래에서 **허용**을 선택합니다. **다단계 인증 필요**를 선택합니다.
7. **정책 사용**을 **설정**으로 선택한 다음 **저장**을 선택합니다.

조건부 액세스 정책의 다른 옵션을 사용하면 2단계 인증이 요구되는 경우를 정확하게 지정할 수 있습니다. 예를 들어 계약자가 도메인에 가입되지 않은 장치의 신뢰할 수 없는 네트워크에서 조달 앱에 액세스하려고 할 때 2단계 인증을 요구하는 정책을 만들 수 있습니다. 

## <a name="next-steps"></a>다음 단계

- [조건부 액세스 모범 사례](../active-directory/active-directory-conditional-access-best-practices.md)에 대한 팁을 가져옵니다.

- [사용자 및 장치](multi-factor-authentication-manage-users-and-devices.md)에 대한 Multi-Factor Authentication 설정을 관리합니다.