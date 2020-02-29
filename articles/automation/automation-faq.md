---
title: Azure Automation FAQ | Microsoft Docs
description: Azure Automation에 대 한 자주 묻는 질문에 대 한 대답입니다.
services: automation
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 02/25/2020
ms.openlocfilehash: 129a5316c2e7be329b479c79706791e993d20b74
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/28/2020
ms.locfileid: "77925814"
---
# <a name="azure-automation-frequently-asked-questions"></a>Azure Automation 질문과 대답

이 Microsoft FAQ는 Azure Automation에 대 한 자주 묻는 질문의 목록입니다. 해당 기능에 대 한 추가 질문이 있는 경우 토론 포럼으로 이동 하 여 질문을 게시 하세요. 자주 묻는 질문일 경우 빠르고 쉽게 찾을 수 있도록 이 문서에 추가하겠습니다.

## <a name="update-management-solution"></a>업데이트 관리 솔루션

### <a name="can-i-prevent-unexpected-os-level-upgrades"></a>예기치 않은 OS 수준 업그레이드를 방지할 수 있나요?

Red Hat Enterprise Linux와 같은 일부 Linux 변형에서 패키지를 통해 OS 수준 업그레이드가 발생할 수 있습니다. 이로 인해 OS 버전 번호가 변경되는 업데이트 관리가 실행될 수 있습니다. 업데이트 관리는 동일한 방법을 사용 하 여 관리자가 Linux 컴퓨터에서 로컬로 사용 하는 패키지를 업데이트 하기 때문에이 동작은 의도적인 것입니다.

업데이트 관리 배포를 통해 OS 버전을 업데이트 하지 않으려면 **제외** 기능을 사용 합니다.

Red Hat Enterprise Linux에서 제외할 패키지 이름은 redhat-release-server.x86_64입니다.

### <a name="why-arent-criticalsecurity-updates-applied"></a>중요/보안 업데이트가 적용 되지 않는 이유는 무엇 인가요?

Linux 컴퓨터에 업데이트를 배포할 때 업데이트 분류를 선택할 수 있습니다. 이 옵션은 지정 된 조건을 충족 하는 컴퓨터에 적용 되는 업데이트를 필터링 합니다. 이 필터는 업데이트가 배포될 때 컴퓨터에 로컬로 적용됩니다.

업데이트 관리는 클라우드에서 업데이트 보강를 수행 하기 때문에 로컬 컴퓨터에 해당 정보가 없더라도 일부 업데이트는 업데이트 관리 보안에 영향을 주는 것으로 플래그가 지정 될 수 있습니다. 따라서 Linux 컴퓨터에 중요 업데이트를 적용 하는 경우 해당 컴퓨터에 보안 영향을 미치는 것으로 표시 되지 않은 업데이트가 있을 수 있으므로 업데이트가 적용 되지 않습니다. 그러나 업데이트 관리는 관련 업데이트에 대 한 추가 정보가 있으므로 해당 컴퓨터를 비규격으로 보고할 수 있습니다.

업데이트 분류를 통한 업데이트 배포는 RTM 버전의 CentOS에서 작동 하지 않습니다. CentOS에 대 한 업데이트를 제대로 배포 하려면 모든 분류를 선택 하 여 업데이트가 적용 되도록 합니다. SUSE의 경우에는 **다른 업데이트** 를 분류로 *만* 선택 하면 zypper (패키지 관리자)와 관련 된 보안 업데이트 또는 해당 종속성이 먼저 필요한 경우 일부 보안 업데이트도 설치 될 수 있습니다. 이 동작은 zypper의 제한 사항입니다. 업데이트 배포를 다시 실행 해야 하는 경우도 있습니다. 확인하려면 업데이트 로그를 확인합니다.

### <a name="can-i-deploy-updates-across-azure-tenants"></a>Azure 테 넌 트 간에 업데이트를 배포할 수 있나요?

다른 Azure 테 넌 트 보고에 컴퓨터를 사용 하 여 패치를 수행 해야 하는 업데이트 관리 경우 다음 해결 방법을 사용 하 여 예약을 수행 해야 합니다. `-ForUpdate` 스위치와 함께 [AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) cmdlet을 사용 하 여 일정을 만들고 [AzureRmAutomationSoftwareUpdateConfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) cmdlet을 사용 하 여 다른 테 넌 트의 컴퓨터를 `-NonAzureComputer` 매개 변수에 전달할 수 있습니다. 다음 예제에 이 작업을 수행하는 방법이 나와 있습니다.

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="next-steps"></a>다음 단계

질문에 대 한 답변이 없는 경우 추가 질문 및 답변을 확인 하려면 다음 포럼을 참조할 수 있습니다.

- [Azure Automation](https://social.msdn.microsoft.com/Forums/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc)

업데이트 관리 솔루션에 대 한 일반적인 피드백은 [사용자 의견 포럼](https://feedback.azure.com/forums/905242-update-management)을 참조 하세요.