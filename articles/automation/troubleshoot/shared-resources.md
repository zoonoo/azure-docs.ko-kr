---
title: Azure 자동화에서 공유 리소스 문제 해결
description: Azure Automation 공유 리소스를 사용하여 문제를 해결하고 해결하는 방법을 알아봅니다.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/12/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: e83c7074d252083329537e205666374705a31873
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733581"
---
# <a name="troubleshoot-shared-resources-in-azure-automation"></a>Azure 자동화에서 공유 리소스 문제 해결

이 문서에서는 Azure Automation에서 [공유 리소스를](../automation-intro.md#shared-resources) 사용할 때 실행할 수 있는 문제에 대한 해결 에 대해 설명합니다.

>[!NOTE]
>이 문서는 새 Azure PowerShell Az 모듈을 사용하도록 업데이트되었습니다. AzureRM 모듈은 적어도 2020년 12월까지 버그 수정을 수신할 예정이므로 계속 사용하셔도 됩니다. 새 Az 모듈 및 AzureRM 호환성에 대한 자세한 내용은 [새 Azure PowerShell Az 모듈 소개](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)를 참조하세요. 하이브리드 Runbook 작업자의 Az 모듈 설치 지침은 [Azure PowerShell 모듈 설치를](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)참조하십시오. 자동화 계정의 경우 Azure 자동화 에서 [Azure PowerShell 모듈을 업데이트하는 방법을](../automation-update-azure-modules.md)사용하여 모듈을 최신 버전으로 업데이트할 수 있습니다.

## <a name="modules"></a>모듈

### <a name="scenario-a-module-is-stuck-during-import"></a><a name="module-stuck-importing"></a>시나리오: 가져오기 중에 모듈이 멈췄습니다.

#### <a name="issue"></a>문제

Azure 자동화 모듈을 가져오거나 업데이트할 때 모듈이 가져오기 상태에 갇혀 있습니다.

#### <a name="cause"></a>원인

PowerShell 모듈 가져오기는 복잡한 다단계 프로세스이므로 모듈을 올바르게 가져오지 못하고 일시적인 상태에 갇히면 됩니다. 가져오기 프로세스에 대한 자세한 내용은 [PowerShell 모듈 가져오기를](/powershell/scripting/developer/module/importing-a-powershell-module#the-importing-process)참조하십시오.

#### <a name="resolution"></a>해결 방법

이 문제를 해결하려면 [제거-AzAutomationModule](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationModule?view=azps-3.7.0) cmdlet을 사용하여 가져오기 상태에 있는 모듈을 제거해야 합니다. 그 후 모듈 가져오기를 다시 시도할 수 있습니다.

```azurepowershell-interactive
Remove-AzAutomationModule -Name ModuleName -ResourceGroupName ExampleResourceGroup -AutomationAccountName ExampleAutomationAccount -Force
```

### <a name="scenario-azurerm-modules-are-stuck-during-import-after-an-update-attempt"></a><a name="update-azure-modules-importing"></a>시나리오: 업데이트 시도 후 가져오는 동안 AzureRM 모듈이 멈췄습니다.

#### <a name="issue"></a>문제

AzureRM 모듈을 업데이트하려고 시도한 후 다음 메시지가 있는 배너가 계정에 남아 있습니다.

```error
Azure modules are being updated
```

#### <a name="cause"></a>원인

0으로 시작하는 숫자 이름을 가진 리소스 그룹에 있는 자동화 계정에서 AzureRM 모듈을 업데이트하는 데 알려진 문제가 있습니다.

#### <a name="resolution"></a>해결 방법

자동화 계정에서 AzureRM 모듈을 업데이트하려면 계정이 동숫자 이름을 가진 리소스 그룹에 있어야 합니다. 숫자 이름이 0으로 시작하는 리소스 그룹은 현재 AzureRM 모듈을 업데이트할 수 없습니다.

### <a name="scenario-module-fails-to-import-or-cmdlets-cant-be-executed-after-importing"></a><a name="module-fails-to-import"></a>시나리오: 모듈이 가져오기를 실패하거나 가져오기를 실행한 후 cmdlet을 실행할 수 없음

#### <a name="issue"></a>문제

모듈을 가져오지 못하거나 성공적으로 가져오지만 cmdlet은 추출되지 않습니다.

#### <a name="cause"></a>원인

모듈이 Azure Automation을 가져오지 못하는 몇 가지 일반적인 이유는 다음과 같습니다.

* 구조가 자동화에 필요한 구조와 일치하지 않습니다.
* 모듈이 Automation 계정에 배포되지 않은 다른 모듈에 종속되어 있습니다.
* 모듈 폴더에 종속성이 없습니다.
* [New-AzAutomationModule](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationModule?view=azps-3.7.0) cmdlet 모듈을 업로드 하는 데 사용 되 고, 전체 저장소 경로 부여 하지 않은 또는 공개적으로 액세스할 수 있는 URL을 사용 하 여 모듈을 로드 하지 않은.

#### <a name="resolution"></a>해결 방법

이러한 해결 방법을 사용하여 문제를 해결합니다.

* 모듈이 형식을 따르는지 확인하십시오: ModuleName.zip-> 모듈이름 또는 버전 번호 ->(ModuleName.psm1, ModuleName.psd1).
* **.psd1** 파일을 열고 모듈에 종속성이 있는지 확인합니다. 종속성이 있으면 이러한 모듈을 Automation 계정에 업로드합니다.
* 참조된 **.dll** 파일이 모듈 폴더에 있는지 확인합니다.

### <a name="scenario-update-azuremoduleps1-suspends-when-updating-modules"></a><a name="all-modules-suspended"></a>시나리오: 모듈을 업데이트할 때 업데이트-AzureModule.ps1 일시 중단

#### <a name="issue"></a>문제

[업데이트-AzureModule.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) Runbook을 사용하여 Azure 모듈을 업데이트하면 모듈 업데이트 프로세스가 일시 중단됩니다.

#### <a name="cause"></a>원인

**Update-AzureModule.ps1**을 사용할 때 동시에 업데이트되는 모듈 수를 결정하는 기본 설정은 10입니다. 동시에 너무 많은 모듈을 업데이트하면 업데이트 프로세스에서 오류가 발생할 가능성이 높습니다.

#### <a name="resolution"></a>해결 방법

모든 AzureRM 또는 Az 모듈이 동일한 자동화 계정에 필요한 것은 일반적이지 않습니다. 필요한 특정 모듈만 가져오는 것이 좋습니다.

> [!NOTE]
> 포함된 모든 모듈을 가져오는 전체 `Az.Automation` 또는 `AzureRM.Automation` 모듈을 가져오지 마십시오.

업데이트 프로세스가 일시 중단되면 `SimultaneousModuleImportJobCount` **Update-AzureModules.ps1** 스크립트에 매개 변수를 추가하고 기본값인 10보다 낮은 값을 제공합니다. 이 논리를 구현하는 경우 값 3 또는 5로 시작하는 것이 좋습니다. `SimultaneousModuleImportJobCount`는 Azure 모듈을 업데이트하는 데 사용되는 **업데이트 자동화AzureModulesForAccount** 시스템 런북의 매개 변수입니다. 이 조정을 하면 업데이트 프로세스가 더 오래 실행되지만 완료될 확률이 높아지어집니다. 다음 예제에서는 매개 변수 및 Runbook에서 매개 변수를 삽입할 위치를 보여줍니다.

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

Run As 계정을 만들거나 업데이트하려면 Run As 계정에서 사용하는 다양한 리소스에 대한 적절한 [사용 권한이](../manage-runas-account.md#permissions) 있어야 합니다. 

잠금으로 인해 문제가 발생하면 잠금을 제거할 수 있는지 확인합니다. 그런 다음 Azure 포털에서 잠긴 리소스로 이동하여 잠금을 마우스 오른쪽 단추로 클릭한 다음 **삭제를**클릭합니다.

### <a name="scenario-you-receive-the-error-unable-to-find-an-entry-point-named-getperadapterinfo-in-dll-iplpapidll-when-executing-a-runbook"></a><a name="iphelper"></a>시나리오: Runbook을 실행할 때 DLL 'iplpapi.dll'에서 'GetPerAdapterInfo'라는 진입점을 찾을 수 없음 오류가 발생합니다.

#### <a name="issue"></a>문제

Runbook을 실행할 때 다음과 같은 예외가 발생합니다.

```error
Unable to find an entry point named 'GetPerAdapterInfo' in DLL 'iplpapi.dll'
```

#### <a name="cause"></a>원인

이 오류는 잘못 구성된 [계정으로 실행](../manage-runas-account.md)으로 인해 발생할 가능성이 높습니다.

#### <a name="resolution"></a>해결 방법

실행 As 계정이 제대로 구성되어 있는지 확인합니다. 그런 다음 Runbook에 Azure를 인증할 적절한 코드가 있는지 확인합니다. 다음 예제에서는 Run as Account를 사용하여 Runbook에서 Azure를 인증하는 코드 조각을 보여 줍니다.

```powershell
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
```

## <a name="next-steps"></a>다음 단계

위에 문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 통해 추가 지원을 시도해 보십시오.

* Azure 포럼을 통해 Azure 전문가의 답변을 얻을 [수 있습니다.](https://azure.microsoft.com/support/forums/)
* [@AzureSupport](https://twitter.com/azuresupport)Azure 커뮤니티를 올바른 리소스(답변, 지원 및 전문가)에 연결하여 고객 경험을 개선하기 위한 공식 Microsoft Azure 계정과 연결합니다.
* Azure 지원 인시던트 제출 [Azure 지원 사이트로](https://azure.microsoft.com/support/options/) 이동하여 **지원 받기를**선택합니다.
