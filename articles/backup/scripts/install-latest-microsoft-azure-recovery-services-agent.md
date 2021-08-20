---
title: 스크립트 샘플 - 온-프레미스 Windows 서버에 최신 MARS 에이전트 설치
description: 스크립트를 사용하여 스토리지 계정의 온-프레미스 Windows 서버에 최신 MARS 에이전트를 설치하는 방법을 알아봅니다.
ms.topic: sample
ms.date: 06/23/2021
ms.openlocfilehash: 1080149d33eebed160449865d58f422e6eba36b1
ms.sourcegitcommit: f0168d80eb396ce27032aa02fe9da5a0c10b5af3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2021
ms.locfileid: "112560869"
---
# <a name="powershell-script-to-install-the-latest-mars-agent-on-an-on-premises-windows-server"></a>온-프레미스 Windows 서버에 최신 MARS 에이전트를 설치하기 위한 PowerShell 스크립트

이 스크립트를 사용하면 온-프레미스 Windows 서버에 최신 MARS 에이전트를 설치할 수 있습니다.

## <a name="sample-script"></a>샘플 스크립트

```azurepowershell
<#

.SYNOPSIS
Sets MARS agent

.DESCRIPTION
Sets MARS agent

.ROLE
Administrators

#>
Set-StrictMode -Version 5.0
$ErrorActionPreference = "Stop"
Try {
    $agentPath = $env:TEMP + '\MARSAgentInstaller.exe'
    Invoke-WebRequest -Uri 'https://aka.ms/azurebackup_agent' -OutFile $agentPath
    & $agentPath /q | out-null

    $env:PSModulePath = (Get-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Session Manager\Environment' -Name PSModulePath).PSModulePath
    $azureBackupModuleName = 'MSOnlineBackup'
    $azureBackupModule = Get-Module -ListAvailable -Name $azureBackupModuleName
    if ($azureBackupModule) {
        $true
    }
    else {
        $false
    }
}
Catch {
    if ($error[0].ErrorDetails) {
        throw $error[0].ErrorDetails
    }
    throw $error[0]
}

```

## <a name="next-steps"></a>다음 단계

PowerShell을 사용하여 MARS 에이전트로 온-프레미스 백업을 배포하고 관리하는 방법을 [자세히 알아봅니다.](../backup-client-automation.md)