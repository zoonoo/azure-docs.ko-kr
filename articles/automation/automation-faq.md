---
title: Azure 자동화 자주 묻는 질문 | 마이크로 소프트 문서
description: Azure 자동화에 대해 자주 묻는 질문에 대한 답변입니다.
services: automation
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 02/25/2020
ms.openlocfilehash: 3fa29f3df5f0434c4c61e8d12adbb3f55156a29f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81405955"
---
# <a name="azure-automation-frequently-asked-questions"></a>Azure 자동화 자주 묻는 질문

이 Microsoft FAQ는 Azure 자동화에 대해 자주 묻는 질문 목록입니다. 기능에 대한 추가 질문이 있는 경우 토론 포럼으로 이동하여 질문을 게시합니다. 자주 묻는 질문일 경우 빠르고 쉽게 찾을 수 있도록 이 문서에 추가하겠습니다.

>[!NOTE]
>이 문서는 새 Azure PowerShell Az 모듈을 사용하도록 업데이트되었습니다. AzureRM 모듈은 적어도 2020년 12월까지 버그 수정을 수신할 예정이므로 계속 사용하셔도 됩니다. 새 Az 모듈 및 AzureRM 호환성에 대한 자세한 내용은 [새 Azure PowerShell Az 모듈 소개](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)를 참조하세요. 하이브리드 Runbook 작업자의 Az 모듈 설치 지침은 [Azure PowerShell 모듈 설치를](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)참조하십시오. 자동화 계정의 경우 Azure 자동화 에서 [Azure PowerShell 모듈을 업데이트하는 방법을](automation-update-azure-modules.md)사용하여 모듈을 최신 버전으로 업데이트할 수 있습니다.

## <a name="update-management-solution"></a>업데이트 관리 솔루션

### <a name="can-i-prevent-unexpected-os-level-upgrades"></a>예기치 않은 OS 수준 업그레이드를 방지할 수 있습니까?

Red Hat 엔터프라이즈 Linux와 같은 일부 Linux 변형에서는 패키지를 통해 OS 수준 업그레이드가 발생할 수 있습니다. 이로 인해 OS 버전 번호가 변경되는 업데이트 관리가 실행될 수 있습니다. Update Management는 관리자가 Linux 컴퓨터에서 로컬로 사용하는 패키지를 업데이트하기 위해 동일한 메서드를 사용하므로 이 동작은 의도적입니다.

업데이트 관리 배포를 통해 OS 버전을 업데이트하지 않으려면 **제외** 기능을 사용합니다.

Red Hat 엔터프라이즈 Linux에서 제외할 `redhat-release-server.x86_64`패키지 이름은 .

### <a name="why-arent-criticalsecurity-updates-applied"></a>중요/보안 업데이트가 적용되지 않는 이유는 무엇입니까?

Linux 컴퓨터에 업데이트를 배포할 때 업데이트 분류를 선택할 수 있습니다. 이 옵션은 지정된 조건을 충족하는 업데이트를 필터링합니다. 이 필터는 업데이트가 배포될 때 컴퓨터에 로컬로 적용됩니다.

업데이트 관리는 클라우드에서 업데이트 보강을 수행하므로 로컬 컴퓨터에 해당 정보가 없더라도 업데이트 관리에서 일부 업데이트를 보안에 영향을 주는 것으로 플래그를 표시할 수 있습니다. Linux 컴퓨터에 중요한 업데이트를 적용하는 경우 해당 컴퓨터에 보안 영향이 있는 것으로 표시되지 않으므로 적용되지 않는 업데이트가 있을 수 있습니다. 그러나 업데이트 관리는 관련 업데이트에 대한 추가 정보가 있기 때문에 해당 컴퓨터를 비규격으로 보고할 수 있습니다.

업데이트 분류에 의한 업데이트를 배포하는 것은 CentOS의 RTM 버전에서 작동하지 않습니다. CentOS에 대한 업데이트를 올바르게 배포하려면 모든 분류를 선택하여 업데이트가 적용되었는지 확인합니다. SUSE의 경우 분류로 **다른 업데이트만** 선택하면 zypper(패키지 관리자) 또는 해당 종속성과 관련된 보안 업데이트가 먼저 필요한 경우 몇 가지 추가 보안 업데이트가 설치될 수 있습니다. 이 동작은 zypper의 제한 사항입니다. 경우에 따라 업데이트 배포를 다시 실행한 다음 업데이트 로그를 통해 배포를 확인해야 할 수 있습니다.

### <a name="can-i-deploy-updates-across-azure-tenants"></a>Azure 테넌트를 통해 업데이트를 배포할 수 있습니까?

업데이트 관리에 대한 다른 Azure 테넌트 보고에 패치가 필요한 컴퓨터가 있는 경우 다음 해결 방법을 사용하여 예약해야 합니다. [새-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSchedule?view=azps-3.7.0) cmdlet 을 사용하여 `ForUpdateConfiguration` 일정을 작성하도록 지정된 매개 변수를 지정할 수 있습니다. [New-AzAutomationSoftwareConfiguration](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSoftwareUpdateConfiguration?view=azps-3.7.0) cmdlet을 사용하고 다른 테넌트의 컴퓨터를 `NonAzureComputer` 매개 변수에 전달할 수 있습니다. 다음 예제에 이 작업을 수행하는 방법이 나와 있습니다.

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdateConfiguration

New-AzAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="next-steps"></a>다음 단계

여기서 질문에 대한 답변이 없는 경우 다음 소스를 참조하여 추가 질문과 답변을 확인할 수 있습니다.

- [Azure Automation](https://social.msdn.microsoft.com/Forums/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc)
- [피드백 포럼](https://feedback.azure.com/forums/905242-update-management)
