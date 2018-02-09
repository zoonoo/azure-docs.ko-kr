---
title: "Azure 스택의 암호를 회전 | Microsoft Docs"
description: "Azure 스택에서 암호를 회전 하는 방법을 알아봅니다."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 49071044-6767-4041-9EDD-6132295FA551
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/08/2018
ms.author: mabrigg
ms.openlocfilehash: e2e9d93af3889714ade1d0364a6f747c184e6d75
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/17/2018
---
# <a name="rotate-secrets-in-azure-stack"></a>Azure 스택의 암호를 회전

*적용 대상: Azure 스택 시스템 통합*

Azure 스택 일반 흐름에 있는 구성 요소에 대 한 암호를 업데이트 합니다.

## <a name="updating-the-passwords-for-the-baseboard-management-controller-bmc"></a>베이스 보드 관리 컨트롤러 (BMC)에 대 한 암호를 업데이트합니다.

베이스 보드 관리 컨트롤러 BMC ()는 실제 서버의 상태를 모니터링합니다. 사양 및 BMC의 암호를 업데이트 하는 방법에 대 한 지침 (oem) 하드웨어 공급 업체에 따라 다릅니다.

1. Azure 스택 물리적 서버에서 BMC OEM 지침에 따라 업데이트 합니다. 사용자 환경에서 각 BMC에 대 한 암호는 같아야 합니다.
2. Azure 스택 세션에서 권한 있는 끝점을 엽니다. 명령에 대 한 참조 [권한 있는 끝점을 사용 하 여 Azure 스택의](azure-stack-privileged-endpoint.md)합니다.
3. 프롬프트를 변경 된 후에 PowerShell **[IP 주소 또는 ERCS VM name]: PS >** 또는 **[azs ercs01]: PS >**실행 하는 환경에 따라 `Set-BmcPassword` 를 실행 하 여 `invoke-command`합니다. 권한 있는 끝점 세션 변수를 매개 변수로 전달 합니다. 예: 

    ```powershell
    # Interactive Version
    $PEip = "<Privileged Endpoint IP or Name>" # You can also use the machine name instead of IP here.
    $PECred = Get-Credential "<Domain>\CloudAdmin" -Message "PE Credentials" 
    $NewBMCpwd = Read-Host -Prompt "Enter New BMC password" -AsSecureString 

    $PEPSession = New-PSSession -ComputerName $PEip -Credential $PECred -ConfigurationName "PrivilegedEndpoint" 

    Invoke-Command -Session $PEPSession -ScriptBlock {
        Set-Bmcpassword -bmcpassword $using:NewBMCpwd
    }
    ```
    
    또한 암호와 함께 코드 줄으로 정적 PowerShell 버전을 사용할 수 있습니다.
    
    ```powershell
    # Static Version
    $PEip = "<Privileged Endpoint IP or Name>" # You can also use the machine name instead of IP here.
    $PEUser = "<Privileged Endpoint user for exmaple Domain\CloudAdmin>"
    $PEpwd = ConvertTo-SecureString "<Privileged Endpoint Password>" -AsPlainText -Force
    $PECred = New-Object System.Management.Automation.PSCredential ($PEUser, $PEpwd) 
    $NewBMCpwd = ConvertTo-SecureString "<New BMC Password>" -AsPlainText -Force 

    $PEPSession = New-PSSession -ComputerName $PEip -Credential $PECred -ConfigurationName "PrivilegedEndpoint" 

    Invoke-Command -Session $PEPSession -ScriptBlock {
        Set-Bmcpassword -bmcpassword $using:NewBMCpwd
    }
    ```

## <a name="next-steps"></a>다음 단계

보안 및 Azure 스택 하는 방법에 대 한 자세한 참조 [Azure 스택 인프라 보안 상태](azure-stack-security-foundations.md)합니다.
