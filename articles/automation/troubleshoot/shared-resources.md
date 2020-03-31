---
title: Azure Automation 공유 리소스 오류 해결
description: Runbook을 지원하는 Azure Automation 공유 리소스를 사용하여 문제를 해결하고 해결하는 방법을 알아봅니다.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/12/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 4cea558b11d7ee7bbe838cecbd061cd487b536d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278325"
---
# <a name="troubleshoot-errors-with-shared-resources"></a>공유 리소스 오류 해결

이 문서에서는 Azure Automation에서 공유 리소스를 사용할 때 발생할 수 있는 문제 해결 방법을 알아봅니다.

## <a name="modules"></a>모듈

### <a name="scenario-a-module-is-stuck-importing"></a><a name="module-stuck-importing"></a>시나리오: 모듈이 가져오기 상태에서 중단

#### <a name="issue"></a>문제

Azure Automation에서 모듈을 가져오거나 업데이트할 때 모듈이 **가져오기** 상태에서 중단됩니다.

#### <a name="cause"></a>원인

PowerShell 모듈 가져오기는 복잡한 다단계 프로세스입니다. 이 프로세스는 모듈 가져오기가 제대로 수행되지 않을 가능성이 있습니다. 이 문제가 발생하는 경우 가져오는 모듈이 일시적인 상태에서 중단될 수 있습니다. 이 프로세스에 대한 자세한 내용은 [PowerShell 모듈 가져오기](/powershell/scripting/developer/module/importing-a-powershell-module#the-importing-process)를 참조하세요.

#### <a name="resolution"></a>해결 방법

이 문제를 해결하려면 [Remove-AzureRmAutomationModule](/powershell/module/azurerm.automation/remove-azurermautomationmodule) cmdlet을 사용하여 **가져오기** 상태에서 중단된 모듈을 제거해야 합니다. 그 후 모듈 가져오기를 다시 시도할 수 있습니다.

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name ModuleName -ResourceGroupName ExampleResourceGroup -AutomationAccountName ExampleAutomationAccount -Force
```

### <a name="scenario-azurerm-modules-are-stuck-importing-after-trying-to-update-them"></a><a name="update-azure-modules-importing"></a>시나리오: AzureRM 모듈을 업데이트하려고 시도한 후 가져오는 데 방해가 됩니다.

#### <a name="issue"></a>문제

AzureRM 모듈을 업데이트하려고 시도한 후 다음 메시지가 있는 배너가 계정에 남아 있습니다.

```error
Azure modules are being updated
```

#### <a name="cause"></a>원인

0으로 시작하는 숫자 이름을 가진 리소스 그룹에 있는 자동화 계정에서 AzureRM 모듈을 업데이트하는 데 알려진 문제가 있습니다.

#### <a name="resolution"></a>해결 방법

자동화 계정에서 Azure 모듈을 업데이트하려면 해당 모듈은 사용어이름이 있는 리소스 그룹에 있어야 합니다. 숫자 이름이 0으로 시작하는 리소스 그룹은 현재 AzureRM 모듈을 업데이트할 수 없습니다.

### <a name="scenario-module-fails-to-import-or-cmdlets-cant-be-executed-after-importing"></a><a name="module-fails-to-import"></a>시나리오: 모듈이 가져오기를 실패하거나 가져오기를 실행한 후 cmdlet을 실행할 수 없음

#### <a name="issue"></a>문제

모듈이 가져오기를 실패하거나 성공하더라도 cmdlet이 추출되지 않습니다.

#### <a name="cause"></a>원인

모듈이 Azure Automation을 가져오지 못하는 몇 가지 일반적인 이유는 다음과 같습니다.

* 구조가 Automation에서 요구하는 구조와 일치하지 않습니다.
* 모듈이 Automation 계정에 배포되지 않은 다른 모듈에 종속되어 있습니다.
* 모듈 폴더에 종속성이 없습니다.
* `New-AzureRmAutomationModule` cmdlet을 사용하여 모듈을 업로드하고 있는데 전체 스토리지 경로를 지정하지 않았거나 공개적으로 액세스 가능한 URL을 사용하여 모듈을 로드하지 않았습니다.

#### <a name="resolution"></a>해결 방법

다음 해결 방법 중 하나를 사용하여 문제를 해결합니다.

* 모듈이 다음과 같은 형식을 따르는지 확인하십시오: ModuleName.Zip **->** 모듈 이름 또는 버전 번호(ModuleName.psm1, **->** ModuleName.psd1)
* .Psd1 파일을 열고 모듈에 종속성이 있는지 확인합니다. 종속성이 있으면 이러한 모듈을 Automation 계정에 업로드합니다.
* 모듈 폴더에 참조되는 .dll이 있는지 확인합니다.

### <a name="scenario-update-azuremoduleps1-suspends-when-updating-modules"></a><a name="all-modules-suspended"></a>시나리오: 모듈을 업데이트할 때 업데이트-AzureModule.ps1 일시 중단

#### <a name="issue"></a>문제

[Update-AzureModule.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) Runbook을 사용하여 Azure 모듈을 업데이트할 때 업데이트 프로세스가 일시 중단됩니다.

#### <a name="cause"></a>원인

`Update-AzureModule.ps1` 스크립트를 사용하면 동시에 업데이트되는 모듈 수를 결정하는 기본 설정이 10입니다. 동시에 너무 많은 모듈을 업데이트하면 업데이트 프로세스에서 오류가 발생할 가능성이 높습니다.

#### <a name="resolution"></a>해결 방법

모든 AzureRM 모듈이 반드시 동일한 Automation 계정에 있어야 하는 경우는 흔치 않습니다. 필요한 AzureRM 모듈만 가져오는 것이 좋습니다.

> [!NOTE]
> **AzureRM** 모듈을 가져오지 마세요. **AzureRM** 모듈을 가져오면 모든 **AzureRM.\*** 모듈을 가져오게 되므로 권장하지 않습니다.

업데이트 프로세스가 일시 중단되면 `SimultaneousModuleImportJobCount` 매개 변수를 `Update-AzureModules.ps1` 스크립트에 추가하고 기본값인 10보다 낮은 값을 입력해야 합니다. 이 로직을 구현할 때 3 또는 5 값으로 시작하는 것이 좋습니다. `SimultaneousModuleImportJobCount`는 Azure 모듈을 업데이트하는 데 사용되는 `Update-AutomationAzureModulesForAccount` 시스템 Runbook입니다. 이 변경으로 인해 프로세스가 더 오래 실행되지만, 완료 가능성이 높아집니다. 다음 예제에서는 매개 변수 및 Runbook에서 매개 변수를 삽입할 위치를 보여줍니다.

 ```powershell
         $Body = @"
            {
               "properties":{
               "runbook":{
                   "name":"Update-AutomationAzureModulesForAccount"
               },
               "parameters":{
                    ...
                    "SimultaneousModuleImportJobCount":"3",
                    ... 
               }
              }
           }
"@
```

## <a name="run-as-accounts"></a>실행 계정

### <a name="scenario-youre-unable-to-create-or-update-a-run-as-account"></a><a name="unable-create-update"></a>시나리오: Run As 계정을 만들거나 업데이트할 수 없습니다.

#### <a name="issue"></a>문제

실행 계정을 만들거나 업데이트하려고 할 때 다음 오류 메시지와 비슷한 오류가 표시됩니다.

```error
You do not have permissions to create…
```

#### <a name="cause"></a>원인

실행 계정을 만들거나 업데이트해야 하는 권한이 없거나 리소스 그룹 수준에서 리소스가 잠겨 있습니다.

#### <a name="resolution"></a>해결 방법

실행 계정을 만들거나 업데이트하려면 실행 계정에서 사용된 다양한 리소스에 대해 적절한 권한이 있어야 합니다. 실행 계정을 만들거나 업데이트하는 데 필요한 권한에 대해 알아보려면 [실행 계정 권한](../manage-runas-account.md#permissions)을 참조하세요.

문제의 원인이 잠금인 경우 잠금을 제거해도 되는지 확인합니다. 그런 다음, 잠긴 리소스로 이동하여 잠금을 마우스 오른쪽 단추로 클릭하고, **삭제**를 선택하여 잠금을 제거합니다.

### <a name="scenario-you-receive-the-error-unable-to-find-an-entry-point-named-getperadapterinfo-in-dll-iplpapidll-when-executing-a-runbook"></a><a name="iphelper"></a>시나리오: Runbook을 실행할 때 DLL 'iplpapi.dll'에서 'GetPerAdapterInfo'라는 진입점을 찾을 수 없음"이라는 오류가 나타납니다.

#### <a name="issue"></a>문제

Runbook을 실행할 때 다음과 같은 예외가 발생합니다.

```error
Unable to find an entry point named 'GetPerAdapterInfo' in DLL 'iplpapi.dll'
```

#### <a name="cause"></a>원인

이 오류는 잘못 구성된 [계정으로 실행](../manage-runas-account.md)으로 인해 발생할 가능성이 높습니다.

#### <a name="resolution"></a>해결 방법

[계정으로 실행이](../manage-runas-account.md) 제대로 구성되어 있는지 확인합니다. 올바르게 구성되면 Runbook에 Azure를 인증할 적절한 코드가 있는지 확인합니다. 다음 예제에서는 Run as Account를 사용하여 Runbook에서 Azure를 인증하는 코드 조각을 보여 줍니다.

```powershell
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
```

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* [Azure 포럼](https://azure.microsoft.com/support/forums/)을 통해 Azure 전문가로부터 답변을 얻습니다.
* 연결 [@AzureSupport](https://twitter.com/azuresupport) - Azure 커뮤니티를 올바른 리소스(답변, 지원 및 전문가)에 연결하여 고객 경험을 개선하기 위한 공식 Microsoft Azure 계정과 연결합니다.
* 추가 지원이 필요한 경우, Azure 기술 지원 인시던트를 제출할 수 있습니다. [Azure 지원 사이트로](https://azure.microsoft.com/support/options/) 이동하여 **지원 받기를**선택합니다.
