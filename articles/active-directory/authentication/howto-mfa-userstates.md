---
title: 사용자별 Multi-Factor Authentication 사용-Azure Active Directory
description: 사용자 상태를 변경 하 여 사용자별 Azure Multi-Factor Authentication를 사용 하도록 설정 하는 방법을 알아봅니다.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81309774"
---
# <a name="enable-per-user-azure-multi-factor-authentication-to-secure-sign-in-events"></a>사용자별 Azure Multi-Factor Authentication를 사용 하 여 로그인 이벤트 보호

Azure AD에서 multi-factor authentication을 요구 하 여 사용자 로그인 이벤트를 보호 하는 방법에는 두 가지가 있습니다. 첫 번째 및 기본 옵션은 특정 조건에서 multi-factor authentication을 요구 하는 조건부 액세스 정책을 설정 하는 것입니다. 두 번째 옵션은 Azure Multi-Factor Authentication에 대 한 각 사용자를 사용 하도록 설정 하는 것입니다. 사용자를 개별적으로 사용 하도록 설정 하면 사용자가 로그인 할 때마다 multi-factor authentication을 수행 합니다 (예: 신뢰할 수 있는 IP 주소에서 로그인 하거나 _기억 된 장치_ 기능이 설정 된 경우).

> [!NOTE]
> 조건부 액세스 정책을 사용 하 여 Azure Multi-Factor Authentication를 사용 하도록 설정 하는 것이 좋습니다. 사용자가 로그인 할 때마다 MFA를 수행 해야 하므로 라이선스에 조건부 액세스가 포함 되지 않는 한 사용자 상태를 변경 하는 것은 더 이상 권장 되지 않습니다.
>
> 조건부 액세스 사용을 시작 하려면 [자습서: Azure Multi-Factor Authentication를 사용 하 여 보안 사용자 로그인 이벤트](tutorial-enable-azure-mfa.md)를 참조 하세요.

## <a name="azure-multi-factor-authentication-user-states"></a>Azure Multi-Factor Authentication 사용자 상태

Azure Multi-Factor Authentication의 사용자 계정은 다음과 같은 3가지 상태를 갖습니다.

> [!IMPORTANT]
> 조건부 액세스 정책을 통해 Azure Multi-Factor Authentication를 사용 하도록 설정 해도 사용자의 상태는 변경 되지 않습니다. 사용자가 사용 하지 않도록 설정 된 경우에는 경보를 표시 하지 않습니다. 조건부 액세스는 상태를 변경 하지 않습니다.
>
> **조건부 액세스 정책을 사용 하는 경우 사용자를 사용 하도록 설정 하거나 적용 하면 안 됩니다.**

| 상태 | 설명 | 영향 받는 비브라우저 앱 | 영향 받는 브라우저 앱 | 영향 받는 최신 인증 |
|:---:| --- |:---:|:--:|:--:|
| 사용 안 함 | Azure Multi-Factor Authentication에 등록 되지 않은 새 사용자의 기본 상태입니다. | 아니요 | 아니요 | 아니요 |
| 사용 | 사용자가 Azure Multi-Factor Authentication에 등록 되었지만 등록 되지 않았습니다. 다음에 로그인할 때 등록하라는 메시지가 표시됩니다. | 아니요.  등록 프로세스가 완료될 때까지 계속 작업합니다. | 예. 세션이 만료 된 후에는 Azure Multi-Factor Authentication 등록이 필요 합니다.| 예. 액세스 토큰이 만료 되 면 Azure Multi-Factor Authentication 등록이 필요 합니다. |
| 적용 | 사용자가 등록 되었으며 Azure Multi-Factor Authentication에 대 한 등록 프로세스를 완료 했습니다. | 예. 앱에 앱 암호가 필요합니다. | 예. 로그인 할 때 Azure Multi-Factor Authentication 필요 합니다. | 예. 로그인 할 때 Azure Multi-Factor Authentication 필요 합니다. |

사용자의 상태는 관리자가 Azure Multi-Factor Authentication에 등록 했는지 여부와 등록 프로세스를 완료 했는지 여부를 반영 합니다.

모든 사용자는 *사용 안 함*으로 시작합니다. Azure Multi-Factor Authentication에 사용자를 등록 하면 해당 상태가 *사용*으로 변경 됩니다. 사용된 사용자가 로그인하고 등록 프로세스를 완료하면 상태는 변경 *적용*으로 변경됩니다.

> [!NOTE]
> 전화 또는 전자 메일과 같이 등록 정보가 이미 있는 사용자 개체에서 MFA를 다시 사용 하도록 설정 하는 경우 관리자가 Azure Portal 또는 PowerShell을 통해 MFA를 다시 등록 해야 합니다. 사용자가 다시 등록 되지 않은 경우 mfa 상태가 *사용* 에서 MFA 관리 UI에 *적용* 으로 전환 되지 않습니다.

## <a name="view-the-status-for-a-user"></a>사용자 상태 보기

사용자 상태를 보고 관리할 수 있는 Azure Portal 페이지에 액세스 하려면 다음 단계를 사용 합니다.

1. 관리자 권한으로 [Azure Portal](https://portal.azure.com) 에 로그인 합니다.
1. *Azure Active Directory*를 검색 하 고 선택한 다음 **사용자** > **모든 사용자**를 선택 합니다.
1. **Multi-Factor Authentication**를 선택 합니다. 이 메뉴 옵션을 보려면 오른쪽으로 스크롤해야 할 수 있습니다. 아래 예제 스크린샷을 선택 하 여 전체 Azure Portal 창 및 메뉴 위치를 확인 합니다.[![](media/howto-mfa-userstates/selectmfa-cropped.png "Azure AD의 사용자 창에서 Multi-Factor Authentication을 선택 합니다.")](media/howto-mfa-userstates/selectmfa.png#lightbox)
1. 다음 예제와 같이 사용자 상태를 표시 하는 새 페이지가 열립니다.
   ![Azure Multi-Factor Authentication에 대 한 예제 사용자 상태 정보를 보여 주는 스크린샷](./media/howto-mfa-userstates/userstate1.png)

## <a name="change-the-status-for-a-user"></a>사용자 상태 변경

사용자에 대 한 Azure Multi-Factor Authentication 상태를 변경 하려면 다음 단계를 완료 합니다.

1. 이전 단계를 통해 Azure Multi-Factor Authentication **사용자** 페이지로 이동합니다.
1. Azure Multi-Factor Authentication에 대해 사용 하도록 설정 하려는 사용자를 찾습니다. 위쪽의 보기를 **사용자**로 변경 해야 할 수 있습니다.
   ![사용자 탭에서 상태를 변경할 사용자를 선택 합니다.](./media/howto-mfa-userstates/enable1.png)
1. 상태를 변경할 사용자의 이름 옆에 있는 확인란을 선택 합니다.
1. 오른쪽의 **빠른 단계**에서 **사용** 또는 **사용 안 함**을 선택 합니다. 다음 예제에서 사용자 *John Smith* 는 이름 옆에 확인을 사용 하 고 사용 하도록 설정 됩니다. 빠른 단계 메뉴 ![에서 사용을 클릭 하 여 선택한 사용자를 사용 하도록 설정 합니다.](./media/howto-mfa-userstates/user1.png)

   > [!TIP]
   > *사용 하도록 설정* 된 사용자는 Azure Multi-Factor Authentication에 등록할 때 자동으로 *적용* 되도록 전환 됩니다. 수동으로 사용자 상태를 *변경 하지 마세요.*

1. 열리는 팝업 창에서 선택한 내용을 확인합니다.

사용자를 사용으로 설정한 후 전자 메일을 통해 알립니다. 사용자에 게 다음에 로그인 할 때 등록 하도록 요청 하 라는 메시지가 표시 됨을 알려 줍니다. 또한 조직에서 최신 인증을 지원하지 않는 비브라우저 앱을 사용하는 경우 앱 암호를 만들어야 합니다. 자세한 내용은 [Azure Multi-Factor Authentication 최종 사용자 가이드](../user-help/multi-factor-authentication-end-user.md) 를 참조 하 여 시작 하는 데 도움을 받으세요.

## <a name="change-state-using-powershell"></a>PowerShell을 사용 하 여 상태 변경

[AZURE AD PowerShell](/powershell/azure/overview)을 사용 하 여 사용자 상태를 변경 하려면 사용자 계정 `$st.State` 에 대 한 매개 변수를 변경 합니다. 사용자 계정에 대 한 세 가지 가능한 상태는 다음과 같습니다.

* *사용*
* *적용*
* *사용 안 함*  

사용자를 *적용* 상태로 직접 전환하지 마세요. 이렇게 하면 사용자가 Azure Multi-Factor Authentication 등록을 거치지 않았고 [앱 암호](howto-mfa-mfasettings.md#app-passwords)를 얻 었으 므로 비 브라우저 기반 앱이 작동을 중지 합니다.

시작 하려면 다음과 같이 [install-module](/powershell/module/powershellget/install-module) 을 사용 하 여 *msonline* 모듈을 설치 합니다.

```PowerShell
Install-Module MSOnline
```

다음으로 [connect-msolservice](/powershell/module/msonline/connect-msolservice)를 사용 하 여 연결 합니다.

```PowerShell
Connect-MsolService
```

다음 예제 PowerShell 스크립트는 이라는 *bsimon@contoso.com*개별 사용자에 대해 MFA를 사용 하도록 설정 합니다.

```PowerShell
$st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
$st.RelyingParty = "*"
$st.State = "Enabled"
$sta = @($st)

# Change the following UserPrincipalName to the user you wish to change state
Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta
```

PowerShell은 사용자를 대량 설정해야 할 때 적합한 옵션입니다. 다음 스크립트는 사용자 목록을 반복 하 여 해당 계정에서 MFA를 사용 하도록 설정 합니다. 다음과 같이의 `$users` 첫 번째 줄에 설정 하는 사용자 계정을 정의 합니다.

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

MFA를 사용 하지 않도록 설정 하려면 다음 예제에서는 [set-msoluser](/powershell/module/msonline/get-msoluser)를 사용 하는 사용자를 가져온 다음 [set-msoluser](/powershell/module/msonline/set-msoluser)를 사용 하 여 정의 된 사용자에 대 한 *StrongAuthenticationRequirements* 집합을 제거 합니다.

```PowerShell
Get-MsolUser -UserPrincipalName bsimon@contoso.com | Set-MsolUser -StrongAuthenticationRequirements @()
```

다음과 같이 [set-msoluser](/powershell/module/msonline/set-msoluser) 를 사용 하 여 사용자에 대해 MFA를 직접 사용 하지 않도록 설정할 수도 있습니다.

```PowerShell
Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements @()
```

## <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>사용자 단위 MFA의 사용자를 조건부 액세스 기반 MFA로 변환

다음 PowerShell은 Azure Multi-Factor Authentication 기반으로 조건부 액세스를 변환 하는 데 도움이 될 수 있습니다.

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
> 최근 동작 및이 PowerShell 스크립트를 변경 했습니다. 이전에는 스크립트에서 MFA 메서드를 사용 하지 않도록 설정 하 고 MFA를 사용 하지 않도록 설정 하 고 메서드를 복원 했습니다. 더 이상 사용 하지 않도록 설정 하는 기본 동작이 메서드를 지우지 않으므로 이제는이 작업이 필요 하지 않습니다.
>
> 전화 또는 전자 메일과 같이 등록 정보가 이미 있는 사용자 개체에서 MFA를 다시 사용 하도록 설정 하는 경우 관리자가 Azure Portal 또는 PowerShell을 통해 MFA를 다시 등록 해야 합니다. 사용자가 다시 등록 되지 않은 경우 mfa 상태가 *사용* 에서 MFA 관리 UI에 *적용* 으로 전환 되지 않습니다.

## <a name="next-steps"></a>다음 단계

신뢰할 수 있는 Ip, 사용자 지정 음성 메시지 및 사기 행위 경고와 같은 Azure Multi-Factor Authentication 설정을 구성 하려면 [azure Multi-Factor Authentication 설정 구성](howto-mfa-mfasettings.md)을 참조 하세요. Azure Multi-Factor Authentication에 대 한 사용자 설정을 관리 하려면 [azure Multi-Factor Authentication를 사용 하 여 사용자 설정 관리](howto-mfa-userdevicesettings.md)를 참조 하세요.

사용자에 게 메시지가 표시 되거나 MFA를 수행 하 라는 메시지가 표시 되지 않는 이유를 이해 하려면 [Azure Multi-Factor Authentication 보고서](howto-mfa-reporting.md#azure-ad-sign-ins-report)를 참조 하세요.
