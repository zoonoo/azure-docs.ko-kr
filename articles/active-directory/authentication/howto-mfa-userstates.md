---
title: 사용자별 다단계 인증 사용 - Azure Active Directory
description: 사용자 상태를 변경하여 사용자별 Azure 다단계 인증을 사용하도록 설정하는 방법 알아보기
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 04/13/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e8ceaf13324864c7ec3df731c3e710815b0eba9
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81309774"
---
# <a name="enable-per-user-azure-multi-factor-authentication-to-secure-sign-in-events"></a>로그인 이벤트를 보호하기 위해 사용자별 Azure 다단계 인증 사용

Azure AD에서 다단계 인증을 요구하여 사용자 로그인 이벤트를 보호하는 방법에는 두 가지가 있습니다. 첫 번째 및 기본 설정 옵션은 특정 조건에서 다단계 인증을 필요로 하는 조건부 액세스 정책을 설정하는 것입니다. 두 번째 옵션은 Azure 다단계 인증에 대해 각 사용자를 사용하도록 설정하는 것입니다. 사용자가 개별적으로 사용하도록 설정되면 로그인할 때마다 다단계 인증을 수행합니다(예: 신뢰할 수 있는 IP 주소에서 로그인하거나 _기억된 장치_ 기능이 켜져 있는 경우와 같은 일부 예외).

> [!NOTE]
> 조건부 액세스 정책을 사용하여 Azure 다단계 인증을 사용하도록 설정하는 것이 권장되는 방법입니다. 사용자가 로그인할 때마다 MFA를 수행해야 하므로 라이선스에 조건부 액세스가 포함되지 않는 한 사용자 상태를 변경하는 것은 더 이상 권장되지 않습니다.
>
> 조건부 액세스 사용을 시작하려면 [자습서: Azure 다단계 인증을 사용한 사용자 로그인 이벤트 보안](tutorial-enable-azure-mfa.md)을 참조하십시오.

## <a name="azure-multi-factor-authentication-user-states"></a>Azure 다단계 인증 사용자 상태

Azure Multi-Factor Authentication의 사용자 계정은 다음과 같은 3가지 상태를 갖습니다.

> [!IMPORTANT]
> 조건부 액세스 정책을 통해 Azure 다단계 인증을 사용하도록 설정해도 사용자의 상태는 변경되지 않습니다. 사용자가 비활성화된 것처럼 보이면 놀라지 마세요. 조건부 액세스는 상태를 변경하지 않습니다.
>
> **조건부 액세스 정책을 사용하는 경우 사용자를 사용하도록 설정하거나 적용해서는 안 됩니다.**

| 상태 | Description | 영향 받는 비브라우저 앱 | 영향 받는 브라우저 앱 | 영향 받는 최신 인증 |
|:---:| --- |:---:|:--:|:--:|
| 사용 안 함 | Azure 다단계 인증에 등록되지 않은 새 사용자의 기본 상태입니다. | 예 | 예 | 예 |
| 사용 | 사용자가 Azure 다단계 인증에 등록되었지만 등록되지 않았습니다. 다음에 로그인할 때 등록하라는 메시지가 표시됩니다. | 아니요.  등록 프로세스가 완료될 때까지 계속 작업합니다. | 예. 세션이 만료된 후 Azure 다단계 인증 등록이 필요합니다.| 예. 액세스 토큰이 만료된 후 Azure 다단계 인증 등록이 필요합니다. |
| 적용 | 사용자가 등록되어 있으며 Azure 다단계 인증에 대한 등록 프로세스를 완료했습니다. | 예. 앱에 앱 암호가 필요합니다. | 예. 로그인 시 Azure 다단계 인증이 필요합니다. | 예. 로그인 시 Azure 다단계 인증이 필요합니다. |

사용자의 상태는 관리자가 Azure 다단계 인증에 등록했는지 여부와 등록 프로세스를 완료했는지 여부를 반영합니다.

모든 사용자는 *사용 안 함*으로 시작합니다. Azure 다단계 인증에 사용자를 등록하면 해당 상태가 *Enabled*으로 변경됩니다. 사용된 사용자가 로그인하고 등록 프로세스를 완료하면 상태는 변경 *적용*으로 변경됩니다.

> [!NOTE]
> 전화 나 전자 메일과 같은 등록 세부 정보가 이미 있는 사용자 개체에서 MFA를 다시 사용하도록 설정한 경우 관리자는 Azure 포털 또는 PowerShell을 통해 해당 사용자가 MFA를 다시 등록해야 합니다. 사용자가 다시 등록하지 않으면 MFA 상태가 *사용* 가능에서 MFA 관리 UI에서 *적용된* 상태로 전환되지 않습니다.

## <a name="view-the-status-for-a-user"></a>사용자 상태 보기

다음 단계를 사용하여 사용자 상태를 보고 관리할 수 있는 Azure 포털 페이지에 액세스합니다.

1. 관리자로 [Azure 포털에](https://portal.azure.com) 로그인합니다.
1. Azure Active *Directory를*검색하고 선택한 다음 **사용자** > **모든 사용자를**선택합니다.
1. **다단계 인증을**선택합니다. 이 메뉴 옵션을 보려면 오른쪽으로 스크롤해야 할 수 있습니다. 아래 예제 스크린샷을 선택하여 전체 Azure 포털 창 및 메뉴 위치를 확인합니다.[![](media/howto-mfa-userstates/selectmfa-cropped.png "Azure AD의 사용자 창에서 다단계 인증 선택")](media/howto-mfa-userstates/selectmfa.png#lightbox)
1. 다음 예제와 같이 사용자 상태를 표시하는 새 페이지가 열립니다.
   ![Azure 다단계 인증에 대한 예제 사용자 상태 정보를 보여 주는 스크린샷](./media/howto-mfa-userstates/userstate1.png)

## <a name="change-the-status-for-a-user"></a>사용자 상태 변경

사용자에 대한 Azure 다단계 인증 상태를 변경하려면 다음 단계를 완료합니다.

1. 이전 단계를 통해 Azure Multi-Factor Authentication **사용자** 페이지로 이동합니다.
1. Azure 다단계 인증에 대해 사용할 사용자를 찾습니다. 맨 위에 있는 보기를 **사용자로**변경해야 할 수 있습니다.
   ![사용자 탭에서 상태를 변경할 사용자 선택](./media/howto-mfa-userstates/enable1.png)
1. 사용자의 이름 옆에 있는 확인란을 선택하여 상태를 변경합니다.
1. 오른쪽에서 **빠른 단계**아래에서 에서 **사용 또는** 사용 **안 함**선택 다음 예제에서 사용자 *John Smith는* 이름 옆에 검사를 하고 사용할 수 ![있도록 활성화중입니다.](./media/howto-mfa-userstates/user1.png)

   > [!TIP]
   > *활성화된* 사용자는 Azure 다단계 인증에 등록할 때 *자동으로 적용으로* 전환됩니다. 사용자 상태를 적용된 으로 수동으로 변경하지 *마십시오.*

1. 열리는 팝업 창에서 선택한 내용을 확인합니다.

사용자를 사용으로 설정한 후 전자 메일을 통해 알립니다. 사용자에게 다음에 로그인할 때 등록을 요청하는 프롬프트가 표시되도록 사용자에게 알릴 수 있습니다. 또한 조직에서 최신 인증을 지원하지 않는 비브라우저 앱을 사용하는 경우 앱 암호를 만들어야 합니다. 자세한 내용은 Azure [다단계 인증 최종 사용자 가이드를](../user-help/multi-factor-authentication-end-user.md) 참조하여 시작하는 데 도움이 됩니다.

## <a name="change-state-using-powershell"></a>PowerShell을 사용하여 상태 변경

[Azure AD PowerShell을](/powershell/azure/overview)사용하여 사용자 상태를 변경하려면 사용자 계정에 대한 `$st.State` 매개 변수를 변경합니다. 사용자 계정에는 세 가지 상태가 있습니다.

* *사용*
* *적용*
* *사용 안 함*  

사용자를 *적용* 상태로 직접 전환하지 마세요. 이렇게 하면 사용자가 Azure 다단계 인증 등록을 거치지 않았고 [앱 암호를](howto-mfa-mfasettings.md#app-passwords)얻었기 때문에 브라우저기반이 아닌 앱이 작동하지 않습니다.

시작하려면 다음과 같이 [설치 모듈을](/powershell/module/powershellget/install-module) 사용하여 *MSOnline 모듈을* 설치하십시오.

```PowerShell
Install-Module MSOnline
```

다음으로, [연결-MsolService를](/powershell/module/msonline/connect-msolservice)사용 하 여 연결:

```PowerShell
Connect-MsolService
```

다음 예제 PowerShell 스크립트를 사용 하 *bsimon@contoso.com*고 MFA라는 개별 사용자에 대 한:

```PowerShell
$st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
$st.RelyingParty = "*"
$st.State = "Enabled"
$sta = @($st)

# Change the following UserPrincipalName to the user you wish to change state
Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta
```

PowerShell은 사용자를 대량 설정해야 할 때 적합한 옵션입니다. 다음 스크립트는 사용자 목록을 통해 반복되며 계정에서 MFA를 사용할 수 있습니다. 다음과 `$users` 같이 첫 번째 줄에 설정된 사용자 계정을 정의합니다.

   ```PowerShell
   # Define your list of users to update state in bulk
   $users = "bsimon@contoso.com","jsmith@contoso.com","ljacobson@contoso.com"

   foreach ($user in $users)
   {
       $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
       $st.RelyingParty = "*"
       $st.State = "Enabled"
       $sta = @($st)
       Set-MsolUser -UserPrincipalName $user -StrongAuthenticationRequirements $sta
   }
   ```

MFA를 사용하지 않도록 설정하려면 다음 예제에서는 [Get-MsolUser를](/powershell/module/msonline/get-msoluser)사용하는 사용자를 가져옵니다. *StrongAuthenticationRequirements* [Set-MsolUser](/powershell/module/msonline/set-msoluser)

```PowerShell
Get-MsolUser -UserPrincipalName bsimon@contoso.com | Set-MsolUser -StrongAuthenticationRequirements @()
```

다음과 같이 [Set-MsolUser를](/powershell/module/msonline/set-msoluser) 사용하는 사용자의 MFA를 직접 사용하지 않도록 설정할 수도 있습니다.

```PowerShell
Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements @()
```

## <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>사용자별 MFA에서 조건부 액세스 기반 MFA로 사용자 변환

다음 PowerShell은 조건부 액세스 기반 Azure 다단계 인증으로 변환하는 데 도움이 될 수 있습니다.

```PowerShell
# Sets the MFA requirement state
function Set-MfaState {

    [CmdletBinding()]
    param(
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $ObjectId,
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $UserPrincipalName,
        [ValidateSet("Disabled","Enabled","Enforced")]
        $State
    )

    Process {
        Write-Verbose ("Setting MFA state for user '{0}' to '{1}'." -f $ObjectId, $State)
        $Requirements = @()
        if ($State -ne "Disabled") {
            $Requirement =
                [Microsoft.Online.Administration.StrongAuthenticationRequirement]::new()
            $Requirement.RelyingParty = "*"
            $Requirement.State = $State
            $Requirements += $Requirement
        }

        Set-MsolUser -ObjectId $ObjectId -UserPrincipalName $UserPrincipalName `
                     -StrongAuthenticationRequirements $Requirements
    }
}

# Disable MFA for all users
Get-MsolUser -All | Set-MfaState -State Disabled
```

> [!NOTE]
> 최근에 동작 및 이 PowerShell 스크립트를 변경했습니다. 이전에는 스크립트가 MFA 메서드를 저장하고 MFA를 사용하지 않도록 설정했으며 메서드를 복원했습니다. 비활성화에 대한 기본 동작이 메서드를 지우지 않으므로 더 이상 필요하지 않습니다.
>
> 전화 나 전자 메일과 같은 등록 세부 정보가 이미 있는 사용자 개체에서 MFA를 다시 사용하도록 설정한 경우 관리자는 Azure 포털 또는 PowerShell을 통해 해당 사용자가 MFA를 다시 등록해야 합니다. 사용자가 다시 등록하지 않으면 MFA 상태가 *사용* 가능에서 MFA 관리 UI에서 *적용된* 상태로 전환되지 않습니다.

## <a name="next-steps"></a>다음 단계

신뢰할 수 있는 IP, 사용자 지정 음성 메시지 및 사기 경고와 같은 Azure 다단계 인증 설정을 구성하려면 [Azure 다단계 인증 설정 구성을](howto-mfa-mfasettings.md)참조하십시오. Azure 다단계 인증에 대한 사용자 설정을 관리하려면 [Azure 다단계 인증을 사용하여 사용자 설정 관리를](howto-mfa-userdevicesettings.md)참조하십시오.

사용자에게 MFA를 수행하라는 메시지가 표시되는지 또는 수행하지 않는 이유를 이해하려면 [Azure 다단계 인증 보고서를](howto-mfa-reporting.md#azure-ad-sign-ins-report)참조하십시오.
