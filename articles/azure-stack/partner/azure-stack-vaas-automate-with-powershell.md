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
ms.date: 03/04/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/26/2018
ROBOTS: NOINDEX
ms.openlocfilehash: 7d675eb55e7ecda1dcf79859e85821c4e86ccbdd
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/05/2019
ms.locfileid: "57338521"
---
# <a name="automate-azure-stack-validation-with-powershell"></a>PowerShell 사용 하 여 Azure Stack 유효성 검사 자동화

유효성 검사 (VaaS) 서비스로 사용 하 여 테스트 실행을 자동화 하는 기능을 제공 합니다 **LaunchVaaSTests.ps1** 스크립트입니다.

> [!NOTE]  
> Automation만 테스트 통과 워크플로에 대해 제공 됩니다. 패키지 유효성 검사 및 솔루션 유효성 검사 워크플로 VaaS 포털을 통해 에서만 지원 됩니다.

이 자습서에서는 스크립트를 작성 하는 방법을 알아봅니다입니다.

> [!div class="checklist"]
> * 필수 구성 요소 설치
> * 설치 하 고 로컬 에이전트를 시작 합니다.
> * 테스트, 통합, 기능, 안정성 등의 범주를 시작합니다.
> * 보고서 테스트 결과

## <a name="launch-the-test-pass-workflow"></a>테스트 통과 워크플로 시작

1. 관리자 권한 PowerShell 프롬프트를 엽니다.

2. Automation 스크립트를 다운로드 하려면 다음 스크립트를 실행 합니다.

    ```PowerShell
    New-Item -ItemType Directory -Path <VaaSLaunchDirectory>
    Set-Location <VaaSLaunchDirectory>
    Invoke-WebRequest -Uri https://storage.azurestackvalidation.com/packages/Microsoft.VaaS.Scripts.latest.nupkg -OutFile "LaunchVaaS.zip"
    Expand-Archive -Path ".\LaunchVaaS.zip" -DestinationPath .\ -Force
    ```

3. 적절 한 매개 변수 값을 사용 하 여 다음 스크립트를 실행 합니다.

    ```PowerShell
    $VaaSAccountCreds = New-Object System.Management.Automation.PSCredential "<VaaSUserId>", (ConvertTo-SecureString "<VaaSUserPassword>" -AsPlainText -Force)
    .\LaunchVaaSTests.ps1 -VaaSAccountCreds $VaaSAccountCreds `
                          -VaaSAccountTenantId <VaaSAccountTenantId> `
                          -VaaSSolutionName <VaaSSolutionName> `
                          -VaaSTestPassName <VaaSTestPassName> `
                          -VaaSTestCategories Integration,Functional `
                          -MaxScriptWaitTimeInHours 12 `
                          -ServiceAdminUserName <AzSServiceAdminUser> `
                          -ServiceAdminUserPassword <AzSServiceAdminPassword> `
                          -TenantAdminUserName <AzSTenantAdminUser> `
                          -TenantAdminUserPassword <AzSTenantAdminPassword> `
                          -CloudAdminUserName <AzSCloudAdminUser> `
                          -CloudAdminUserPassword <AzSCloudAdminPassword>
    ```

    **매개 변수**

    | 매개 변수 | 설명 |
    | --- | --- |
    | VaaSUserId | VaaS 사용자 id입니다. |
    | VaaSUserPassword | VaaS 암호입니다. |
    | VaaSAccountTenantId | 프로그램 VaaS 테 넌 트 GUID입니다. |
    | VaaSSolutionName | 테스트에 통과 중인 VaaS 솔루션의 이름을 실행 됩니다. |
    | VaaSTestPassName | 이름이 VaaS 테스트 워크플로 만들기를 전달 합니다. |
    | VaaSTestCategories | `Integration``Functional`, 또는 합니다. `Reliability`. 여러 값을 사용 하면 각 값을 쉼표로 구분 합니다.  |
    | ServiceAdminUserName | Azure Stack 서비스 관리자 계정입니다.  |
    | ServiceAdminPassword | Azure Stack 서비스 문의 하십시오입니다.  |
    | TenantAdminUserName | 기본 테 넌 트 관리자입니다.  |
    | TenantAdminPassword | 기본 테 넌 트에 대 한 암호입니다.  |
    | CloudAdminUserName | 클라우드 관리자 사용자 이름입니다.  |
    | CloudAdminPassword | 클라우드 관리자에 대 한 암호입니다.  |

4. 테스트의 결과 검토 합니다. 다른 옵션에 대해서 [모니터 VaaS 포털에서 테스트를 관리 하 고](azure-stack-vaas-monitor-test.md)입니다.

## <a name="next-steps"></a>다음 단계

Azure Stack에서 PowerShell에 대 한 자세한 내용은 최신 모듈을 검토 합니다.

> [!div class="nextstepaction"]
> [Azure Stack 모듈](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.6.0)
