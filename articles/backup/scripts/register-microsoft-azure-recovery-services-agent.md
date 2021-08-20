---
title: 스크립트 샘플 - Recovery Services 자격 증명 모음에 온-프레미스 Windows 서버 또는 클라이언트 머신 등록
description: 스크립트를 사용하여 Recovery Services 자격 증명 모음에 온-프레미스 Windows Server 또는 클라이언트 머신을 등록하는 방법을 알아봅니다.
ms.topic: sample
ms.date: 06/23/2021
ms.openlocfilehash: a35d241ba86868c666916e284fb0700b59034eee
ms.sourcegitcommit: f0168d80eb396ce27032aa02fe9da5a0c10b5af3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2021
ms.locfileid: "112560851"
---
# <a name="powershell-script-to-register-an-on-premises-windows-server-or-a-client-machine-with-recovery-services-vault"></a>Recovery Services 자격 증명 모음에 온-프레미스 Windows 서버 또는 클라이언트 머신을 등록하기 위한 PowerShell 스크립트

이 스크립트를 사용하면 Recovery Services 자격 증명 모음에 온-프레미스 Windows 서버 또는 클라이언트 머신을 등록할 수 있습니다. 

## <a name="sample-script"></a>샘플 스크립트

```azurepowershell
<#

.SYNOPSIS
Registers MARS agent

.DESCRIPTION
Registers MARS agent

.ROLE
Administrators

#>
param (
    [Parameter(Mandatory = $true)]
    [String]
    $vaultcredPath,
    [Parameter(Mandatory = $true)]
    [String]
    $passphrase
)
Set-StrictMode -Version 5.0
$env:PSModulePath = (Get-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Session Manager\Environment' -Name PSModulePath).PSModulePath
Import-Module MSOnlineBackup
$ErrorActionPreference = "Stop"
Try {
    $date = Get-Date
    Start-OBRegistration -VaultCredentials $vaultcredPath -Confirm:$false
    $securePassphrase = ConvertTo-SecureString -String $passphrase -AsPlainText -Force
    Set-OBMachineSetting -EncryptionPassphrase $securePassphrase -SecurityPIN " "
}
Catch {
    if ($error[0].ErrorDetails) {
        throw $error[0].ErrorDetails
    }
    throw $error[0]
}

```

## <a name="how-to-execute-the-script"></a>스크립트를 실행하는 방법

1. 위의 스크립트를 선택한 이름과 .ps1 확장명으로 머신에 저장합니다.
1. 다음 매개 변수를 제공하여 스크립트를 실행합니다.
   - – vaultcredPath - 다운로드한 자격 증명 모음 자격 증명 파일의 전체 경로
   - – passphrase - [ConvertTo-SecureString](/powershell/module/microsoft.powershell.security/convertto-securestring?view=powershell-7.1&preserve-view=true) cmdlet을 사용하여 보안 문자열로 변환된 일반 텍스트 문자열

>[!Note]
>또한 Azure Portal에서 생성된 보안 PIN을 제공해야 합니다. PIN을 생성하려면 Recovery Services 자격 증명 모음 블레이드에서 **설정** -> **속성** -> **보안 PIN** 으로 이동한 다음, **생성** 을 선택합니다.

## <a name="next-steps"></a>다음 단계

PowerShell을 사용하여 MARS 에이전트로 온-프레미스 백업을 배포하고 관리하는 방법을 [자세히 알아봅니다.](../backup-client-automation.md)

