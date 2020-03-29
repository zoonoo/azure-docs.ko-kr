---
title: Azure 자동화 자주 묻는 질문 | 마이크로 소프트 문서
description: Azure 자동화에 대해 자주 묻는 질문에 대한 답변입니다.
services: automation
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 02/25/2020
ms.openlocfilehash: 129a5316c2e7be329b479c79706791e993d20b74
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77925814"
---
# <a name="azure-automation-frequently-asked-questions"></a>Azure 자동화 자주 묻는 질문

이 Microsoft FAQ는 Azure 자동화에 대해 자주 묻는 질문 목록입니다. 기능에 대한 추가 질문이 있는 경우 토론 포럼으로 이동하여 질문을 게시합니다. 자주 묻는 질문일 경우 빠르고 쉽게 찾을 수 있도록 이 문서에 추가하겠습니다.

## <a name="update-management-solution"></a>업데이트 관리 솔루션

### <a name="can-i-prevent-unexpected-os-level-upgrades"></a>예기치 않은 OS 수준 업그레이드를 방지할 수 있습니까?

Red Hat 엔터프라이즈 Linux와 같은 일부 Linux 변형에서는 패키지를 통해 OS 수준 업그레이드가 발생할 수 있습니다. 이로 인해 OS 버전 번호가 변경되는 업데이트 관리가 실행될 수 있습니다. Update Management는 동일한 메서드를 사용하여 관리자가 Linux 컴퓨터에서 로컬로 사용하는 패키지를 업데이트하므로 이 동작은 의도적입니다.

업데이트 관리 배포를 통해 OS 버전을 업데이트하지 않으려면 **제외** 기능을 사용합니다.

Red Hat Enterprise Linux에서 제외할 패키지 이름은 redhat-release-server.x86_64입니다.

### <a name="why-arent-criticalsecurity-updates-applied"></a>중요/보안 업데이트가 적용되지 않는 이유는 무엇입니까?

Linux 컴퓨터에 업데이트를 배포할 때 업데이트 분류를 선택할 수 있습니다. 이 옵션은 지정된 조건을 충족하는 컴퓨터에 적용되는 업데이트를 필터링합니다. 이 필터는 업데이트가 배포될 때 컴퓨터에 로컬로 적용됩니다.

업데이트 관리는 클라우드에서 업데이트 보강을 수행하므로 로컬 컴퓨터에 해당 정보가 없더라도 업데이트 관리에서 일부 업데이트가 보안에 영향을 미치는 것으로 플래그를 지정할 수 있습니다. 따라서 Linux 컴퓨터에 중요한 업데이트를 적용하는 경우 해당 컴퓨터에 보안 영향이 있는 것으로 표시되지 않는 업데이트가 있을 수 있으므로 업데이트가 적용되지 않습니다. 그러나 업데이트 관리는 관련 업데이트에 대한 추가 정보가 있기 때문에 해당 컴퓨터를 비규격으로 보고할 수 있습니다.

업데이트 분류에 의한 업데이트를 배포하는 것은 CentOS의 RTM 버전에서 작동하지 않습니다. CentOS에 대한 업데이트를 올바르게 배포하려면 모든 분류를 선택하여 업데이트가 적용되었는지 확인합니다. SUSE의 경우 분류로 다른 *업데이트만* **Other updates** 선택하면 zypper(패키지 관리자) 또는 해당 종속성과 관련된 보안 업데이트가 먼저 필요한 경우 일부 보안 업데이트도 설치될 수 있습니다. 이 동작은 zypper의 제한 사항입니다. 경우에 따라 업데이트 배포를 다시 실행해야 할 수도 있습니다. 확인하려면 업데이트 로그를 확인합니다.

### <a name="can-i-deploy-updates-across-azure-tenants"></a>Azure 테넌트를 통해 업데이트를 배포할 수 있습니까?

업데이트 관리에 패치해야 하는 다른 Azure 테넌트보고에 컴퓨터가 있는 경우 다음 해결 방법을 사용하여 예약해야 합니다. 스위치와 함께 [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) cmdlet을 사용하여 일정을 만들고 [New-AzureRmAutomationSoftwareUpdateConfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) cmdlet을 사용하고 다른 테넌트의 `-NonAzureComputer` 컴퓨터를 매개 변수로 전달할 수 있습니다. `-ForUpdate` 다음 예제에 이 작업을 수행하는 방법이 나와 있습니다.

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="next-steps"></a>다음 단계

여기서 질문에 대한 답변이 없는 경우 다음 포럼을 참조하여 추가 질문과 답변을 얻을 수 있습니다.

- [Azure 자동화](https://social.msdn.microsoft.com/Forums/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc)

업데이트 관리 솔루션에 대한 일반적인 피드백은 [피드백 포럼을](https://feedback.azure.com/forums/905242-update-management)방문하십시오.