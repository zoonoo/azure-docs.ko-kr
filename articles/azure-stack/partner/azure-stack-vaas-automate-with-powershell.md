---
title: PowerShell 사용 하 여 Azure Stack 유효성 검사를 자동화 | Microsoft Docs
description: PowerShell 사용 하 여 Azure Stack 유효성 검사를 자동화할 수 있습니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 1cb4b1a7cfd72ea302676244a53af58e77215aa9
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2018
ms.locfileid: "40235348"
---
# <a name="automate-azure-stack-validation-with-powershell"></a>PowerShell 사용 하 여 Azure Stack 유효성 검사 자동화 

유효성 검사 (VaaS) 서비스로 사용 하 여 테스트 실행을 자동화 하는 기능을 제공 합니다 **LaunchVaaSTests.ps1** 스크립트입니다.

다음 워크플로에 대 한 PowerShell을 사용할 수 있습니다.

- 테스트 통과 워크플로

이 스크립트는 워크플로의 네 개의 요소를 포함:

- 필수 구성 요소를 설치 합니다.
- 설치 하 고 로컬 에이전트를 시작 합니다.
- 테스트, 통합, 기능, 안정성 등의 범주를 시작합니다.
- 모니터링에 대 한 결과 전달 하 고 보고 하는 각 테스트 보고서 생성을 파일입니다.

## <a name="launch-the-test-pass-workflow"></a>테스트 통과 워크플로 시작 합니다.

1. 관리자 권한 PowerShell 프롬프트를 엽니다.

2. Automation 스크립트를 다운로드 하려면 다음 스크립트를 실행 합니다.

    ````PowerShell  
    New-Item -ItemType Directory -Path <VaaSLaunchDirectory>
    Set-Location <VaaSLaunchDirectory>
    Invoke-WebRequest -Uri https://vaastestpacksprodeastus.blob.core.windows.net/packages/Microsoft.VaaS.Scripts.3.0.0.nupkg -OutFile "LaunchVaaS.zip"
    Expand-Archive -Path ".\LaunchVaaS.zip" -DestinationPath .\ -Force
    ````

3. 값을 사용 하 여 다음 스크립트를 실행 합니다.

    ````PowerShell  
    $VaaSAccountCreds = New-Object System.Management.Automation.PSCredential "<VaaSUserId>", (ConvertTo-SecureString "<VaaSUserPassword>"  -AsPlainText -Force)
    $ServiceAdminCreds = New-Object System.Management.Automation.PSCredential "<ServiceAdminUser>", (ConvertTo-SecureString "<ServiceAdminPassword>" -AsPlainText -Force)
    $TenantAdminCreds = New-Object System.Management.Automation.PSCredential "<TenantAdminUser>", (ConvertTo-SecureString "<TenantAdminPassword>" -AsPlainText -Force)
    .\LaunchVaaSTests.ps1 -VaaSAccountCreds $VaaSAccountCreds `
        -VaaSAccountTenantId <VaaSAccountTenantId> `
        -VaaSSolutionName <VaaSSolutionName> `
        -VaaSTestPassName <VaaSTestPassName> `
        -VaaSTestCategories Integration,Functional `
        -MaxScriptWaitTimeInHours 12 `
        -ServiceAdminCreds $ServiceAdminCreds `
    ````

    **매개 변수**

    | 매개 변수 | 설명 |
    | --- | --- |
    | VaaSUserld | VaaS 사용자 id입니다. | 
    | VaaSUserPassword | VaaS 암호입니다. |
    | ServiceAdminUser | Azure Stack 서비스 관리자 계정입니다.  |
    | ServiceAdminPassword | Azure Stack 서비스 문의 하십시오입니다.  |
    | TenantAdminUser | 기본 테 넌 트 관리자입니다.  |
    | TenantAdminPassword | 기본 테 넌 트에 대 한 암호입니다.  |
    | FunctionalCategory| 통합 기능, 또는 합니다. 안정성입니다. 여러 값을 사용 하면 각 값을 쉼표로 구분 합니다.  |

4. 테스트의 결과 검토 합니다. 테스트 결과 읽기에 대 한 내용은 [테스트 모니터링](azure-stack-vaas-monitor-test.md)합니다.

## <a name="next-steps"></a>다음 단계

 - 에 대해 자세히 알아보려면 [서비스로 Azure Stack 유효성 검사](https://docs.microsoft.com/azure/azure-stack/partner)합니다.
 - Azure Stack에서 PowerShell에 대 한 자세한 내용은 참조는 [Azure Stack 모듈](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.3.0) 참조 합니다.