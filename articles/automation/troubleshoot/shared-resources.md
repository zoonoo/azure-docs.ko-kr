---
title: Azure Automation의 공유 리소스 문제 해결
description: Azure Automation 공유 리소스 문제를 해결 하 고 문제를 해결 하는 방법을 알아봅니다.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/12/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: c59e8ec67777a9cfebc12508b197e1237a61df4a
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82864201"
---
# <a name="troubleshoot-shared-resources-in-azure-automation"></a>Azure Automation의 공유 리소스 문제 해결

이 문서에서는 Azure Automation에서 [공유 리소스](../automation-intro.md#shared-resources) 를 사용 하는 경우 발생할 수 있는 문제에 대 한 해결 방법을 설명 합니다.

>[!NOTE]
>이 문서는 새 Azure PowerShell Az 모듈을 사용하도록 업데이트되었습니다. 현재 AzureRM 모듈을 계속 사용할 수 있습니다. 새 Az 모듈 및 AzureRM 호환성에 대한 자세한 내용은 [새 Azure PowerShell Az 모듈 소개](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)를 참조하세요. Hybrid Runbook Worker에 대한 Az 모듈 설치 지침은 [Azure PowerShell 모듈 설치](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)를 참조하세요. Automation 계정의 경우 [Azure Automation에서 Azure PowerShell 모듈을 업데이트 하는 방법을](../automation-update-azure-modules.md)사용 하 여 모듈을 최신 버전으로 업데이트할 수 있습니다.

## <a name="modules"></a>모듈

### <a name="scenario-a-module-is-stuck-during-import"></a><a name="module-stuck-importing"></a>시나리오: 가져오는 동안 모듈이 정지 됨

#### <a name="issue"></a>문제

모듈은 Azure Automation 모듈을 가져오거나 업데이트 하는 동안 *가져오기* 상태에서 중단 됩니다.

#### <a name="cause"></a>원인

PowerShell 모듈 가져오기는 복잡 하 고 다단계 프로세스 이기 때문에 모듈이 올바르게 가져오지 않을 수 있으며 일시적인 상태에서 중단 될 수 있습니다. 가져오기 프로세스에 대해 자세히 알아보려면 [PowerShell 모듈 가져오기](/powershell/scripting/developer/module/importing-a-powershell-module#the-importing-process)를 참조 하세요.

#### <a name="resolution"></a>해결 방법

이 문제를 해결 하려면 [AzAutomationModule](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationModule?view=azps-3.7.0) cmdlet을 사용 하 여 중단 된 모듈을 제거 해야 합니다. 그 후 모듈 가져오기를 다시 시도할 수 있습니다.

```azurepowershell-interactive
Remove-AzAutomationModule -Name ModuleName -ResourceGroupName ExampleResourceGroup -AutomationAccountName ExampleAutomationAccount -Force
```

### <a name="scenario-azurerm-modules-are-stuck-during-import-after-an-update-attempt"></a><a name="update-azure-modules-importing"></a>시나리오: 업데이트를 시도한 후 가져오기 작업을 수행 하는 동안 AzureRM 모듈이 정지 됨

#### <a name="issue"></a>문제

AzureRM 모듈을 업데이트 하려고 시도한 후 다음 메시지를 포함 하는 배너가 계정에 남아 있습니다.

```error
Azure modules are being updated
```

#### <a name="cause"></a>원인

Automation 계정에서 AzureRM 모듈을 업데이트 하는 것과 관련 된 알려진 문제가 있습니다. 특히, 모듈이 0으로 시작 하는 리소스 그룹에 있는 경우 문제가 발생 합니다.

#### <a name="resolution"></a>해결 방법

Automation 계정에서 AzureRM 모듈을 업데이트 하려면 계정이 영숫자 이름으로 리소스 그룹에 있어야 합니다. 숫자 이름이 0부터 시작 하는 리소스 그룹은 현재 AzureRM 모듈을 업데이트할 수 없습니다.

### <a name="scenario-module-fails-to-import-or-cmdlets-cant-be-executed-after-importing"></a><a name="module-fails-to-import"></a>시나리오: 모듈이 가져오기를 실패하거나 가져오기를 실행한 후 cmdlet을 실행할 수 없음

#### <a name="issue"></a>문제

모듈이 가져오기에 실패 하거나 성공적으로 가져오므로 cmdlet이 추출 되지 않습니다.

#### <a name="cause"></a>원인

모듈이 Azure Automation을 가져오지 못하는 몇 가지 일반적인 이유는 다음과 같습니다.

* 구조는 자동화에 필요한 구조와 일치 하지 않습니다.
* 모듈이 Automation 계정에 배포되지 않은 다른 모듈에 종속되어 있습니다.
* 모듈 폴더에 종속성이 없습니다.
* [AzAutomationModule](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationModule?view=azps-3.7.0) cmdlet이 모듈을 업로드 하는 데 사용 되 고 있으며, 전체 저장소 경로를 제공 하지 않았거나 공개적으로 액세스할 수 있는 URL을 사용 하 여 모듈을 로드 하지 않았습니다.

#### <a name="resolution"></a>해결 방법

이러한 솔루션 중 하나를 사용 하 여 문제를 해결 합니다.

* 모듈이 ModuleName-> ModuleName 또는 Version Number-> (ModuleName. .psm1, ModuleName. psd1) 형식을 따르는지 확인 합니다.
* **Psd1** 파일을 열고 모듈에 종속성이 있는지 확인 합니다. 종속성이 있으면 이러한 모듈을 Automation 계정에 업로드합니다.
* 참조 된 **.dll** 파일이 module 폴더에 있는지 확인 합니다.

### <a name="scenario-update-azuremoduleps1-suspends-when-updating-modules"></a><a name="all-modules-suspended"></a>시나리오: 모듈을 업데이트할 때 Update-AzureModule 일시 중단

#### <a name="issue"></a>문제

[Update-AzureModule](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AzureModule.ps1) runbook을 사용 하 여 Azure 모듈을 업데이트 하는 경우 모듈 업데이트 프로세스가 일시 중단 됩니다.

#### <a name="cause"></a>원인

이 runbook에서 동시에 업데이트 되는 모듈 수를 결정 하는 기본 설정은 10입니다. 동시에 너무 많은 모듈을 업데이트하면 업데이트 프로세스에서 오류가 발생할 가능성이 높습니다.

#### <a name="resolution"></a>해결 방법

모든 AzureRM 또는 Az 모듈이 동일한 Automation 계정에 필요한 것은 일반적이 지 않습니다. 필요한 특정 모듈만 가져와야 합니다.

> [!NOTE]
> 포함 된 모든 모듈 `Az.Automation` 을 `AzureRM.Automation` 가져오는 전체 또는 모듈을 가져오지 않도록 합니다.

업데이트 프로세스가 일시 중단 되 면 `SimultaneousModuleImportJobCount` 매개 변수를 **Update-AzureModules** 스크립트에 추가 하 고 기본값 10 보다 낮은 값을 입력 합니다. 이 논리를 구현 하는 경우 3 또는 5 값으로 시작 해 보세요. `SimultaneousModuleImportJobCount`는 Azure 모듈을 업데이트 하는 데 사용 되는 **AutomationAzureModulesForAccount** 시스템 runbook의 매개 변수입니다. 이러한 조정을 수행 하는 경우 업데이트 프로세스가 더 이상 실행 되지만 완료 될 가능성이 더 높아집니다. 다음 예제에서는 매개 변수 및 Runbook에서 매개 변수를 삽입할 위치를 보여줍니다.

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

### <a name="scenario-youre-unable-to-create-or-update-a-run-as-account"></a><a name="unable-create-update"></a>시나리오: 실행 계정을 만들거나 업데이트할 수 없습니다.

#### <a name="issue"></a>문제

실행 계정을 만들거나 업데이트 하려고 하면 다음과 같은 오류가 표시 됩니다.

```error
You do not have permissions to create…
```

#### <a name="cause"></a>원인

실행 계정을 만들거나 업데이트 하는 데 필요한 권한이 없거나 리소스가 리소스 그룹 수준에서 잠겨 있습니다.

#### <a name="resolution"></a>해결 방법

실행 계정을 만들거나 업데이트 하려면 실행 계정에서 사용 하는 다양 한 리소스에 대 한 적절 한 [권한이](../manage-runas-account.md#permissions) 있어야 합니다. 

잠금으로 인해 문제가 발생 하는 경우 잠금을 제거할 수 있는지 확인 합니다. 그런 다음 Azure Portal 잠긴 리소스로 이동 하 여 잠금을 마우스 오른쪽 단추로 클릭 하 고 **삭제**를 선택 합니다.

### <a name="scenario-you-receive-the-error-unable-to-find-an-entry-point-named-getperadapterinfo-in-dll-iplpapidll-when-executing-a-runbook"></a><a name="iphelper"></a>시나리오: runbook을 실행할 때 "' iplpapi ' DLL에서 ' GetPerAdapterInfo ' 라는 진입점을 찾을 수 없습니다." 오류가 표시 됩니다.

#### <a name="issue"></a>문제

Runbook을 실행 하는 경우 다음과 같은 예외가 표시 됩니다.

```error
Unable to find an entry point named 'GetPerAdapterInfo' in DLL 'iplpapi.dll'
```

#### <a name="cause"></a>원인

이 오류는 잘못 구성 된 [실행 계정](../manage-runas-account.md)으로 인해 발생할 수 있습니다.

#### <a name="resolution"></a>해결 방법

실행 계정이 제대로 구성 되어 있는지 확인 합니다. 그런 다음 Azure를 사용 하 여 인증 하는 runbook에 적절 한 코드가 있는지 확인 합니다. 다음 예제에서는 실행 계정을 사용 하 여 runbook에서 Azure에 인증 하는 코드 조각을 보여 줍니다.

```powershell
$connection = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $connection.TenantID `
-ApplicationID $connection.ApplicationID -CertificateThumbprint $connection.CertificateThumbprint
```

## <a name="next-steps"></a>다음 단계

이 문서에서 문제를 해결 하지 못하는 경우 추가 지원을 위해 다음 채널 중 하나를 시도해 보세요.

* Azure [포럼](https://azure.microsoft.com/support/forums/)을 통해 azure 전문가 로부터 답변을 받으세요.
* 을 사용 [@AzureSupport](https://twitter.com/azuresupport)하 여 연결 합니다. Azure 커뮤니티를 적절 한 리소스 (답변, 지원 및 전문가)에 연결 하는 공식 Microsoft Azure 계정입니다.
* Azure 지원 인시던트 제출 [Azure 지원 사이트](https://azure.microsoft.com/support/options/)로 이동 하 여 **지원 받기**를 선택 합니다.

