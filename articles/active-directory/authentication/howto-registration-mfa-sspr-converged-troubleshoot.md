---
title: Azure AD SSPR 및 MFA에 융합 등록 사용 안 함(공개 미리 보기)
description: Azure AD Multi-Factor Authenticaiton 및 셀프 서비스 암호 재설정 등록 사용 안 함(공개 미리 보기)
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 08/02/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry, michmcla
ms.openlocfilehash: 6a51b1a3050b37fdcc822006f9e25d6662c65fb2
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54426355"
---
# <a name="disable-azure-ad-converged-registration-public-preview"></a>Azure AD 융합 등록 사용 안 함(공개 미리 보기)

사용자가 새로운 융합 환경에 전화 번호 및/또는 모바일 앱을 등록하면 서비스에서는 해당 사용자의 방법에 대한 플래그 집합(StrongAuthenticationMethods)을 스탬핑합니다. 이 기능을 통해 사용자는 MFA가 필요할 때마다 본인의 방법을 사용하여 MFA(Multi-Factor Authentication)를 수행할 수 있습니다.

사용자가 새 환경을 통해 등록하는 방법에 StrongAuthenticationMethods 속성이 설정됩니다. 공개 미리 보기를 사용할 수 있게 되면 이 동작도 발생합니다. 관리자가 미리 보기를 사용하도록 설정하면 사용자는 새 경험을 통해 등록하고, 관리자가 미리 보기를 사용하지 않도록 설정하면 사용자는 자신도 모르게 MFA에 등록될 수 있습니다.

융합 등록을 마친 사용자가 [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup)에서 현재 SSPR 등록 페이지로 이동하면 해당 페이지에 액세스하려면 MFA를 수행하라는 메시지가 표시됩니다. 이 단계는 기술적 관점에서 예상되는 동작이지만, 이전에 SSPR에만 등록한 사용자에게는 이 단계가 새로운 동작입니다. 이 추가 단계는 추가 보안 수준을 제공하여 사용자의 보안을 강화하지만, 관리자는 사용자가 더 이상 MFA를 수행할 수 없도록 사용자를 롤백할 수 있습니다.  

## <a name="how-to-roll-back-users"></a>사용자를 롤백하는 방법

관리자가 사용자의 MFA 설정을 재설정할 수 있도록, 사용자의 모바일 앱 및/또는 전화 번호에 대한 StrongAuthenticationMethods 속성을 지울 수 있는 PowerShell 스크립트를 만들어 두었습니다. 필요한 경우 사용자에 대해 이 스크립트를 실행하면 사용자가 MFA에 다시 등록해야 합니다. 영향을 받는 모든 사용자를 롤백하기 전에 사용자 한 명 또는 두 명으로 롤백을 테스트하는 것이 좋습니다.

이어지는 단계는 사용자 또는 사용자 그룹을 롤백하는 데 도움이 됩니다.

### <a name="pre-requisites"></a>필수 조건

1. 적절한 Azure AD PowerShell 모듈을 설치해야 합니다. PowerShell 창에서 다음 명령을 실행하여 모듈을 설치합니다.

   ```powershell
   Install-Module -Name MSOnline
   Import-Module MSOnline
   ```

1. 영향을 받는 사용자 개체 ID/ID 목록을 머신에 한 줄당 ID 하나씩 텍스트 파일로 저장합니다. 파일 위치를 적어 둡니다.
1. 머신에 다음 스크립트를 저장하고 스크립트 위치를 기록해 둡니다.

```powershell
<# 
//********************************************************
//*                                                      *
//*   Copyright (C) Microsoft. All rights reserved.      *
//*                                                      *
//********************************************************
#>

param($path)

# Define Remediation Fn
function RemediateUser {

    param  
    (
        $ObjectId
    )

    $user = Get-MsolUser -ObjectId $ObjectId

    Write-Host "Checking if user is eligible for rollback: UPN: "  $user.UserPrincipalName  " ObjectId: "  $user.ObjectId -ForegroundColor Yellow

    $hasMfaRelyingParty = $false
    foreach($p in $user.StrongAuthenticationRequirements)
    {
        if ($p.RelyingParty -eq "*")
        {
            $hasMfaRelyingParty = $true
            Write-Host "User was enabled for per-user MFA." -ForegroundColor Yellow
        }
    }

    if ($user.StrongAuthenticationMethods.Count -gt 0 -and -not $hasMfaRelyingParty)
    {
        Write-Host $user.UserPrincipalName " is eligible for rollback" -ForegroundColor Yellow
        Write-Host "Rolling back user ..." -ForegroundColor Yellow
        Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName $user.UserPrincipalName
        Write-Host "Successfully rolled back user " $user.UserPrincipalName -ForegroundColor Green
    }
    else
    {
        Write-Host $user.UserPrincipalName " is not eligible for rollback. No action required."
    }

    Write-Host ""
    Start-Sleep -Milliseconds 750
}

# Connect
Import-Module MSOnline
Connect-MsolService

foreach($line in Get-Content $path)
{
    RemediateUser -ObjectId $line
}
```

### <a name="rollback"></a>롤백

PowerShell 창에서 강조 표시된 위치를 업데이트한 후 다음 명령을 실행합니다. 메시지가 표시되면 글로벌 관리자 자격 증명을 입력합니다. 스크립트가 각 사용자 업데이트 작업의 결과를 출력합니다.

`<script location> -path <user file location>`

## <a name="disable-preview-experience"></a>미리 보기 환경을 사용하지 않도록 설정

사용자에게 미리 보기 환경을 사용하지 않으려면 다음 단계를 수행합니다.

1. 글로벌 관리자 또는 사용자 관리자로 Azure Portal에 로그인합니다.
2. **Azure Active Directory**, **사용자 설정**, **액세스 패널 미리 보기 기능의 설정 관리**로 이동합니다.
3. **사용자가 보안 정보를 등록하고 관리하기 위한 미리 보기 기능을 사용 가능**에서 선택기를 **없음**으로 설정하고 **저장**을 클릭합니다.

사용자에게 미리 보기 환경을 사용하여 등록하라는 메시지가 더 이상 표시되지 않습니다.

## <a name="next-steps"></a>다음 단계

[셀프 서비스 암호 재설정 및 Azure Multi-Factor Authentication를 위한 융합 등록 공개 미리 보기에 대해 자세히 알아보기](concept-registration-mfa-sspr-converged.md)