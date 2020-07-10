---
title: Azure Automation FAQ | Microsoft Docs
description: 이 문서에서는 Azure Automation에 대한 질문과 대답을 제공합니다.
services: automation
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 02/25/2020
ms.openlocfilehash: 76c8d09ef2ef0130ddac856a1f37f8b68d977494
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86186234"
---
# <a name="azure-automation-frequently-asked-questions"></a>Azure Automation 질문과 대답

이 Microsoft FAQ는 Azure Automation에 대한 일반적인 질문과 대답 목록입니다. 기능에 대한 추가 질문이 있으면 토론 포럼으로 이동하여 질문을 게시하세요. 자주 묻는 질문일 경우 빠르고 쉽게 찾을 수 있도록 이 문서에 추가하겠습니다.

## <a name="update-management"></a>업데이트 관리

### <a name="can-i-prevent-unexpected-os-level-upgrades"></a>예기치 않은 OS 수준 업그레이드를 방지할 수 있나요?

Red Hat Enterprise Linux와 같은 일부 Linux 변형에서는 OS 수준 업그레이드가 패키지를 통해 발생할 수 있습니다. 이로 인해 OS 버전 번호가 변경되는 업데이트 관리가 실행될 수 있습니다. 업데이트 관리는 관리자가 Linux 머신에서 로컬로 사용하는 것과 동일한 방법으로 패키지를 업데이트하므로 이 동작은 의도적인 것입니다.

업데이트 관리 배포를 통해 OS 버전을 업데이트하지 않으려면 **제외** 기능을 사용합니다.

Red Hat Enterprise Linux에서 제외할 패키지 이름은 `redhat-release-server.x86_64`입니다.

### <a name="why-arent-criticalsecurity-updates-applied"></a>중요/보안 업데이트가 적용되지 않는 이유는 무엇인가요?

Linux 컴퓨터에 업데이트를 배포할 때 업데이트 분류를 선택할 수 있습니다. 이 옵션은 지정된 조건에 맞는 업데이트를 필터링합니다. 이 필터는 업데이트가 배포될 때 컴퓨터에 로컬로 적용됩니다.

업데이트 관리는 클라우드에서 업데이트 적용을 수행하므로, 로컬 머신에 해당 정보가 없더라도 업데이트 관리의 일부 업데이트에 보안에 영향을 주는 것으로 플래그를 지정할 수 있습니다. Linux 시스템에 중요 업데이트를 적용하면 해당 머신의 보안에 영향을 주는 것으로 표시되지 않은 일부 업데이트가 있으므로 업데이트가 적용되지 않습니다. 그러나 업데이트 관리는 관련 업데이트에 대한 추가 정보가 있으므로 해당 머신을 호환되지 않는 것으로 보고할 수 있습니다.

업데이트 분류에 따라 업데이트를 배포하는 것은 RTM 버전의 CentOS에서 작동하지 않습니다. CentOS에 대한 업데이트를 제대로 배포하려면 업데이트를 적용할 수 있도록 모든 분류를 선택합니다. SUSE의 경우 분류로 **기타 업데이트**만 선택하면 zypper(패키지 관리자)와 관련된 보안 업데이트나 해당 종속성이 먼저 필요한 경우에도 일부 추가 보안 업데이트가 설치될 수 있습니다. 이 동작은 zypper의 제한 사항입니다. 경우에 따라 업데이트 배포를 다시 실행한 후 업데이트 로그를 통해 배포를 확인해야 할 수도 있습니다.

### <a name="can-i-deploy-updates-across-azure-tenants"></a>Azure 테넌트에 업데이트를 배포할 수 있나요?

업데이트 관리에 보고하는 다른 Azure 테넌트에서 패치가 필요한 머신이 있는 경우 다음 해결 방법을 사용하여 예약해야 합니다. `ForUpdateConfiguration` 매개 변수가 지정된 [New-AzAutomationSchedule](/powershell/module/Az.Automation/New-AzAutomationSchedule?view=azps-3.7.0) cmdlet을 사용하여 일정을 만들 수 있습니다. [New-AzAutomationSoftwareUpdateConfiguration](/powershell/module/Az.Automation/New-AzAutomationSoftwareUpdateConfiguration?view=azps-3.7.0) cmdlet을 사용하여 다른 테넌트의 머신을 `NonAzureComputer` 매개 변수에 전달할 수 있습니다. 다음 예제에 이 작업을 수행하는 방법이 나와 있습니다.

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdateConfiguration

New-AzAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="next-steps"></a>다음 단계

질문에 대한 대답이 여기에 없으면 다음 출처에서 추가 질문 및 대답을 참조할 수 있습니다.

- [Azure Automation](/answers/topics/azure-automation.html)
- [피드백 포럼](https://feedback.azure.com/forums/905242-update-management)
