---
title: Azure Automation FAQ | Microsoft Docs
description: Azure Automation에 대 한 자주 묻는 질문에 대 한 대답입니다.
services: automation
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 02/25/2020
ms.openlocfilehash: 3fa29f3df5f0434c4c61e8d12adbb3f55156a29f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81405955"
---
# <a name="azure-automation-frequently-asked-questions"></a>Azure Automation 질문과 대답

이 Microsoft FAQ는 Azure Automation에 대 한 자주 묻는 질문의 목록입니다. 해당 기능에 대 한 추가 질문이 있는 경우 토론 포럼으로 이동 하 여 질문을 게시 하세요. 자주 묻는 질문일 경우 빠르고 쉽게 찾을 수 있도록 이 문서에 추가하겠습니다.

>[!NOTE]
>이 문서는 새 Azure PowerShell Az 모듈을 사용하도록 업데이트되었습니다. AzureRM 모듈은 적어도 2020년 12월까지 버그 수정을 수신할 예정이므로 계속 사용하셔도 됩니다. 새 Az 모듈 및 AzureRM 호환성에 대한 자세한 내용은 [새 Azure PowerShell Az 모듈 소개](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)를 참조하세요. Hybrid Runbook Worker에 대한 Az 모듈 설치 지침은 [Azure PowerShell 모듈 설치](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)를 참조하세요. Automation 계정의 경우 [Azure Automation에서 Azure PowerShell 모듈을 업데이트하는 방법](automation-update-azure-modules.md)을 사용하여 모듈을 최신 버전으로 업데이트할 수 있습니다.

## <a name="update-management-solution"></a>업데이트 관리 솔루션

### <a name="can-i-prevent-unexpected-os-level-upgrades"></a>예기치 않은 OS 수준 업그레이드를 방지할 수 있나요?

Red Hat Enterprise Linux와 같은 일부 Linux 변형에서 패키지를 통해 OS 수준 업그레이드가 발생할 수 있습니다. 이로 인해 OS 버전 번호가 변경 되는 업데이트 관리 실행 될 수 있습니다. 업데이트 관리는 동일한 방법을 사용 하 여 관리자가 Linux 컴퓨터에서 로컬로 사용 하는 패키지를 업데이트 하기 때문에이 동작은 의도적인 것입니다.

업데이트 관리 배포를 통해 OS 버전을 업데이트 하지 않으려면 **제외** 기능을 사용 합니다.

Red Hat Enterprise Linux에서 제외할 패키지 이름은 `redhat-release-server.x86_64`입니다.

### <a name="why-arent-criticalsecurity-updates-applied"></a>중요/보안 업데이트가 적용 되지 않는 이유는 무엇 인가요?

Linux 컴퓨터에 업데이트를 배포할 때 업데이트 분류를 선택할 수 있습니다. 이 옵션은 지정 된 조건을 충족 하는 업데이트를 필터링 합니다. 이 필터는 업데이트가 배포될 때 컴퓨터에 로컬로 적용됩니다.

업데이트 관리는 클라우드에서 업데이트 보강를 수행 하기 때문에 로컬 컴퓨터에 해당 정보가 없더라도 보안에 영향을 주는 업데이트 관리 일부 업데이트에 플래그를 지정할 수 있습니다. Linux 컴퓨터에 중요 업데이트를 적용 하는 경우 해당 컴퓨터에 보안 영향을 미치는 것으로 표시 되지 않은 업데이트가 있을 수 있으므로 적용 되지 않습니다. 그러나 업데이트 관리는 관련 업데이트에 대 한 추가 정보가 있으므로 해당 컴퓨터를 비규격으로 보고할 수 있습니다.

업데이트 분류를 통한 업데이트 배포는 RTM 버전의 CentOS에서 작동 하지 않습니다. CentOS에 대 한 업데이트를 제대로 배포 하려면 모든 분류를 선택 하 여 업데이트가 적용 되도록 합니다. SUSE의 경우에는 **다른 업데이트** 를 분류로만 선택 하면 zypper (패키지 관리자)와 관련 된 보안 업데이트 또는 해당 종속성이 먼저 필요한 경우 추가 보안 업데이트가 설치 될 수 있습니다. 이 동작은 zypper의 제한 사항입니다. 업데이트 배포를 다시 실행 한 다음 업데이트 로그를 통해 배포를 확인 해야 하는 경우도 있습니다.

### <a name="can-i-deploy-updates-across-azure-tenants"></a>Azure 테 넌 트 간에 업데이트를 배포할 수 있나요?

업데이트 관리에 다른 Azure 테 넌 트 보고에 패치를 적용 해야 하는 컴퓨터가 있는 경우 다음 해결 방법을 사용 하 여 예약을 시작 해야 합니다. 지정 된 `ForUpdateConfiguration` 매개 변수와 함께 [AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSchedule?view=azps-3.7.0) cmdlet을 사용 하 여 일정을 만들 수 있습니다. [AzAutomationSoftwareUpdateConfiguration](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSoftwareUpdateConfiguration?view=azps-3.7.0) cmdlet을 사용 하 여 다른 테 넌 트의 컴퓨터를 `NonAzureComputer` 매개 변수에 전달할 수 있습니다. 다음 예제에 이 작업을 수행하는 방법이 나와 있습니다.

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdateConfiguration

New-AzAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="next-steps"></a>다음 단계

여기에서 질문에 대답할 수 없는 경우 추가 질문 및 답변에 대 한 다음 소스를 참조할 수 있습니다.

- [Azure Automation](https://social.msdn.microsoft.com/Forums/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc)
- [피드백 포럼](https://feedback.azure.com/forums/905242-update-management)
