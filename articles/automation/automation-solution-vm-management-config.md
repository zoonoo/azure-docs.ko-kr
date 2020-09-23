---
title: Azure Automation 작업 시간 외 VM 시작/중지 구성
description: 이 문서에서는 작업 시간 외 VM 시작/중지 기능을 구성하여 다양한 사용 사례 또는 시나리오를 지원하는 방법을 설명합니다.
services: automation
ms.subservice: process-automation
ms.date: 06/01/2020
ms.topic: conceptual
ms.openlocfilehash: b0bc23d515bebdd0d943bbad33c5ebba35a35605
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90987204"
---
# <a name="configure-startstop-vms-during-off-hours"></a>작업 시간 외 VM 시작/중지 구성

이 문서에서는 [작업 시간 외 VM 시작/중지](automation-solution-vm-management.md) 기능을 구성하여 설명한 시나리오를 지원하는 방법을 설명합니다. 또한 다음 방법을 알아볼 수 있습니다.

* [이메일 알림 구성](#configure-email-notifications)
* [VM 추가](#add-a-vm)
* [VM 제외](#exclude-a-vm)
* [시작 및 종료 일정 수정](#modify-the-startup-and-shutdown-schedules)

## <a name="scenario-1-startstop-vms-on-a-schedule"></a><a name="schedule"></a>시나리오 1: 일정에 따라 VM 시작/중지

이 시나리오는 작업 시간 외 VM 시작/중지를 처음 배포할 때의 기본 구성입니다. 예를 들어 저녁에 사무실을 나갈 때 구독의 모든 VM을 중지하고, 아침에 사무실로 복귀할 때 시작하는 기능을 구성할 수 있습니다. 배포 중에 일정 **Scheduled-StartVM** 및 **Scheduled-StopVM**을 구성할 경우 대상 VM을 시작 및 중지합니다. 

VM을 중지하는 기능 구성만 지원됩니다. 사용자 지정 일정을 구성하는 방법을 알아보려면 [시작 및 종료 일정 수정](#modify-the-startup-and-shutdown-schedules)을 참조하세요.

> [!NOTE]
> 기능으로 사용되는 표준 시간대는 예약 시간 매개 변수를 구성할 당시의 현재 표준 시간대입니다. 그러나 Azure Automation은 Azure Automation에서 UTC 형식으로 저장합니다. 따라서 머신 배포 중에 처리될 때 표준 시간대 변환 작업을 수행할 필요가 없습니다.

범위에 있는 VM을 제어하려면 `External_Start_ResourceGroupNames`, `External_Stop_ResourceGroupNames` 및 `External_ExcludeVMNames` 변수를 구성합니다.

구독 및 리소스 그룹 또는 특정 VM 목록 중 하나로만 작업 대상을 지정할 수 있습니다.

### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>구독 및 리소스 그룹에 대한 시작 및 중지 작업의 대상 지정

1. `External_Stop_ResourceGroupNames` 및 `External_ExcludeVMNames` 변수를 구성하여 대상 VM을 지정합니다.

2. **Scheduled-StartVM** 및 **Scheduled-StopVM** 일정을 설정하고 업데이트합니다.

3. **ACTION** 매개 변수 필드를 **start**로 설정하고 **WHATIF** 매개 변수 필드를 True로 설정하여 **ScheduledStartStop_Parent** Runbook을 실행하면 변경 내용을 미리 볼 수 있습니다.

### <a name="target-the-start-and-stop-action-by-vm-list"></a>VM 목록별로 시작 및 중지 작업의 대상 지정

1. **작업** 을 **시작**으로 설정 하 여 **ScheduledStartStop_Parent** runbook을 실행 합니다.

2. **Vmlist** 매개 변수 필드에 쉼표로 구분 된 vm 목록 (공백 없음)을 추가 합니다. 예제 목록은 `vm1,vm2,vm3` 입니다.

3. **WHATIF** 매개 변수 필드를 True로 설정 합니다.

4. 쉼표로 `External_ExcludeVMNames` 구분 된 값 사이에 공백이 없는 쉼표로 구분 된 vm 목록 (VM1, v m 2, v m 3)으로 변수를 구성 합니다.

5. 이 시나리오에서는 `External_Start_ResourceGroupNames` 및 `External_Stop_ResourceGroupnames` 변수를 따르지 않습니다. 이 시나리오의 경우 고유한 Automation 일정을 만들어야 합니다. 자세한 내용은 [Azure Automation에서 Runbook 예약](shared-resources/schedules.md)을 참조하세요.

    > [!NOTE]
    > **Target ResourceGroup Names**의 값은 `External_Start_ResourceGroupNames` 및 `External_Stop_ResourceGroupNames` 모두에 대한 값으로 저장됩니다. 추가 세분성을 위해 각기 다른 리소스 그룹을 대상으로 하도록 이러한 각 변수를 수정할 수 있습니다. 시작 작업의 경우 `External_Start_ResourceGroupNames`를 사용하고, 중지 작업의 경우 `External_Stop_ResourceGroupNames`를 사용합니다. VM이 시작 및 중지 일정에 자동으로 추가됩니다.

## <a name="scenario-2-startstop-vms-in-sequence-by-using-tags"></a><a name="tags"></a>시나리오 2: 태그를 사용하여 시퀀스의 VM 시작/중지

분산 워크로드를 지원하는 여러 VM에 둘 이상의 구성 요소를 포함하는 환경에서는 순서대로 시작 및 중지되는 구성 요소 시퀀스를 지원하는 것이 중요합니다. 

### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>구독 및 리소스 그룹에 대한 시작 및 중지 작업의 대상 지정

1. 양의 정수 값을 가진 `sequencestart` 및 `sequencestop` 태그를 `External_Start_ResourceGroupNames` 및 `External_Stop_ResourceGroupNames` 변수를 대상으로 하는 VM에 추가합니다. 시작 및 중지 작업은 오름차순으로 수행됩니다. VM에 태그를 지정하는 방법을 알아보려면 [Azure에서 Windows 가상 머신에 태그 지정](../virtual-machines/windows/tag.md) 및 [Azure에서 Linux 가상 머신에 태그 지정](../virtual-machines/linux/tag.md)을 참조하세요.

2. 요구 사항을 충족하는 날짜 및 시간으로 일정 **Sequenced-StartVM** 및 **Sequenced StopVM**을 수정하고 일정을 사용하도록 설정합니다.

3. **ACTION**을 **start**로, **WHATIF**를 True로 설정하여 **SequencedStartStop_Parent** Runbook을 실행하면 변경 내용을 미리 볼 수 있습니다.

4. 작업을 미리 보고 프로덕션 VM에 대해 구현하기 전에 필요한 변경을 수행합니다. 준비가 되면 매개 변수를 **False**로 설정하여 Runbook을 수동으로 실행하거나 Automation 일정 **Sequenced-StartVM** 및 **Sequenced-StopVM**이 지정된 일정에 따라 자동으로 실행되도록 할 수 있습니다.

### <a name="target-the-start-and-stop-actions-by-vm-list"></a>VM 목록별로 시작 및 중지 작업의 대상 지정

1. 양의 정수 값을 가진 `sequencestart` 및 `sequencestop` 태그를 `VMList` 매개 변수에 추가할 VM에 추가합니다.

2. **작업** 을 **시작**으로 설정 하 여 **SequencedStartStop_Parent** runbook을 실행 합니다.

3. **Vmlist** 매개 변수 필드에 쉼표로 구분 된 vm 목록 (공백 없음)을 추가 합니다. 예제 목록은 `vm1,vm2,vm3` 입니다.

4. **WHATIF** 를 True로 설정 합니다. 

5. 쉼표로 `External_ExcludeVMNames` 구분 된 값 사이에 공백이 없는 쉼표로 구분 된 vm 목록으로 변수를 구성 합니다.

6. 이 시나리오에서는 `External_Start_ResourceGroupNames` 및 `External_Stop_ResourceGroupnames` 변수를 따르지 않습니다. 이 시나리오의 경우 고유한 Automation 일정을 만들어야 합니다. 자세한 내용은 [Azure Automation에서 Runbook 예약](shared-resources/schedules.md)을 참조하세요.

7. 작업을 미리 보고 프로덕션 VM에 대해 구현하기 전에 필요한 변경을 수행합니다. 준비가 되면 매개 변수를 **False**로 설정하여 **monitoring-and-diagnostics/monitoring-action-groupsrunbook**을 수동으로 실행합니다. 또는 지정된 일정에 따라 Automation이 **Sequenced-StartVM** 및 **Sequenced-StopVM** 일정을 자동으로 실행하도록 합니다.

## <a name="scenario-3-start-or-stop-automatically-based-on-cpu-utilization"></a><a name="cpuutil"></a>시나리오 3: CPU 사용률에 따라 자동으로 시작 또는 중지

작업 시간 외 VM 시작/중지는 사용량이 많지 않은 기간(예 : 몇 시간 후) 동안에 사용되지 않는 머신을 평가하여 Azure Resource Manager 및 구독의 클래식 VM 실행에 필요한 비용을 관리하는 데 도움을 줄 수 있습니다. 그리고 프로세서 사용률이 지정된 백분율보다 작은 경우 자동으로 종료하도록 합니다.

기본적으로 기능은 CPU 백분율 메트릭을 평가하고 평균 사용률이 5% 이하인지 파악하도록 미리 구성됩니다. 이 시나리오는 다음 변수를 통해 제어되며, 기본값이 요구 사항을 충족하지 않는 경우 수정할 수 있습니다.

* `External_AutoStop_MetricName`
* `External_AutoStop_Threshold`
* `External_AutoStop_TimeAggregationOperator`
* `External_AutoStop_TimeWindow`
* `External_AutoStop_Frequency`
* `External_AutoStop_Severity`

구독 및 리소스 그룹 또는 특정 VM 목록 중 하나로 작업 대상을 지정할 수 있습니다.

**AutoStop_CreateAlert_Parent** Runbook을 실행하면 대상으로 지정된 구독, 리소스 그룹 및 VM이 있는지 확인합니다. VM이 있으면 Runbook은 부모 Runbook에서 확인한 각 VM에 대해 **AutoStop_CreateAlert_Child** Runbook을 호출합니다. 해당 자식 Runbook은 다음과 같습니다.

* 확인된 각 VM에 대한 메트릭 경고 규칙을 만듭니다.
* 지정된 시간 간격 동안 CPU가 구성된 임계값 아래로 떨어지면 특정 VM에 대한 **AutoStop_VM_Child** Runbook을 트리거합니다. 
* VM 중지를 시도합니다.

### <a name="target-the-autostop-action-against-all-vms-in-a-subscription"></a>구독의 모든 VM에 대해 자동 중지 작업을 대상으로 지정

1. `External_Stop_ResourceGroupNames` 변수가 비어 있거나 *(와일드 카드)로 설정되어 있는지 확인합니다.

2. [선택 사항] 자동 중지 작업에서 일부 VM을 제외하려는 경우 쉼표로 구분된 VM 이름 목록을 `External_ExcludeVMNames` 변수에 추가할 수 있습니다.

3. **Schedule_AutoStop_CreateAlert_Parent** 일정을 사용하여 구독의 모든 VM에 필요한 VM 중지 메트릭 경고 규칙을 만듭니다. 이 유형의 일정을 실행하면 새 VM이 구독에 추가될 때 새 메트릭 경고 규칙을 만들 수 있습니다.

### <a name="target-the-autostop-action-against-all-vms-in-a-resource-group-or-multiple-resource-groups"></a>리소스 그룹 또는 여러 리소스 그룹의 모든 VM에 대해 자동 중지 작업을 대상으로 지정합니다.

1. 쉼표로 구분된 리소스 그룹 이름 목록을 `External_Stop_ResourceGroupNames` 변수에 추가합니다.

2. 자동 중지에서 일부 VM을 제외하려는 경우 쉼표로 구분된 VM 이름 목록을 `External_ExcludeVMNames` 변수에 추가할 수 있습니다.

3. **Schedule_AutoStop_CreateAlert_Parent** 일정을 사용하여 리소스 그룹의 모든 VM에 필요한 VM 중지 메트릭 경고 규칙을 만듭니다. 일정에 따라 이 작업을 실행하면 새 VM이 리소스 그룹에 추가될 때 새 메트릭 경고 규칙을 만들 수 있습니다.

### <a name="target-the-autostop-action-to-a-list-of-vms"></a>VM 목록에 대해 자동 중지 작업을 대상으로 지정합니다.

1. 새 [일정](shared-resources/schedules.md#create-a-schedule)을 만들어 **AutoStop_CreateAlert_Parent** Runbook에 연결하여 쉼표로 구분된 VM 이름 목록을 `VMList` 매개 변수에 추가합니다.

2. 필요에 따라 autostop 작업에서 일부 Vm을 제외 하려는 경우 쉼표로 구분 된 VM 이름 목록 (공백 없음)을 변수에 추가할 수 있습니다 `External_ExcludeVMNames` .

## <a name="configure-email-notifications"></a>전자 메일 알림 구성

작업 시간 외 VM 시작/중지가 배포된 후 메일 알림을 변경하려면 배포 중에 생성된 작업 그룹을 수정하면 됩니다.  

> [!NOTE]
> Azure Government 클라우드의 구독은 이 기능의 메일 기능을 지원하지 않습니다.

1. Azure Portal에서 **모니터**, **작업 그룹**으로 차례로 이동합니다. **StartStop_VM_Notication**이라는 작업 그룹을 선택합니다.

    :::image type="content" source="media/automation-solution-vm-management/azure-monitor.png" alt-text="모니터-작업 그룹 페이지의 스크린샷":::

2. StartStop_VM_Notification 페이지의 **세부 정보** 아래에서 **세부 정보 편집**을 클릭합니다. 그러면 메일/SMS/푸시/음성 페이지가 열립니다. 메일 주소를 업데이트하고 **확인**을 클릭하여 변경 내용을 저장합니다.

    :::image type="content" source="media/automation-solution-vm-management/change-email.png" alt-text="업데이트 된 예제 전자 메일 주소를 보여 주는 전자 메일/SMS/푸시/음성 페이지의 스크린샷":::

    또는 작업 그룹에 추가 작업을 추가할 수 있습니다. 작업 그룹에 대한 자세한 내용은 [작업 그룹](../azure-monitor/platform/action-groups.md)을 참조하세요.

기능이 가상 머신을 종료할 때 전송되는 예제 메일은 다음과 같습니다.

:::image type="content" source="media/automation-solution-vm-management/email.png" alt-text="기능이 가상 컴퓨터를 종료할 때 전송 되는 예제 전자 메일의 스크린샷":::

## <a name="add-or-exclude-vms"></a><a name="add-exclude-vms"></a>VM 추가 또는 제외

기능을 사용하면 대상으로 지정하거나 제외할 VM을 추가할 수 있습니다. 

### <a name="add-a-vm"></a>VM 추가

기능이 실행될 때 VM이 포함되도록 하는 방법에는 두 가지가 있습니다.

* 기능의 각 부모 [Runbook](automation-solution-vm-management.md#runbooks)에는 `VMList` 매개 변수가 있습니다. 상황에 맞는 부모 runbook을 예약 하는 경우 쉼표로 구분 된 VM 이름 목록을이 매개 변수에 전달할 수 있으며, 이러한 Vm은 기능이 실행 될 때 포함 됩니다.

* 여러 VM을 선택하려면 시작하거나 중지하려는 VM이 포함된 리소스 그룹 이름으로 `External_Start_ResourceGroupNames` 및 `External_Stop_ResourceGroupNames`를 설정합니다. 또한 기능이 구독의 모든 리소스 그룹에 대해 실행되도록 하려면 변수를 `*` 값으로 설정할 수도 있습니다.

### <a name="exclude-a-vm"></a>VM 제외

작업 시간 외 VM 시작/중지에서 VM을 제외하려면 해당 이름을 `External_ExcludeVMNames` 변수에 추가하면 됩니다. 이 변수는 기능에서 제외할 특정 Vm (공백 없음)의 쉼표로 구분 된 목록입니다. 이 목록은 140개의 VM으로 제한됩니다. 목록에 140개가 넘는 VM을 추가하면 제외하도록 설정된 VM이 우발적으로 시작되거나 중지될 수 있습니다.

## <a name="modify-the-startup-and-shutdown-schedules"></a>시작 및 종료 일정 수정

이 기능에서 시작 및 종료 일정을 관리하는 작업은 [Azure Automation에서 runbook 예약](shared-resources/schedules.md)에 설명된 대로 동일한 단계를 따릅니다. VM을 시작 및 중지하려면 별도의 일정이 필요합니다.

특정 시간에 VM을 중지하도록 기능을 구성하는 작업이 지원됩니다. 이 시나리오에서는 중지 일정만 만들고 해당하는 시작 일정은 만들지 않습니다. 

1. `External_Stop_ResourceGroupNames` 변수에서 종료할 VM에 대한 리소스 그룹을 추가했는지 확인합니다.

2. VM을 종료하려는 경우 시간에 대한 고유한 일정을 만듭니다.

3. **ScheduledStartStop_Parent** Runbook으로 이동하고 **일정**을 클릭합니다. 이 옵션을 사용하면 이전 단계에서 만든 일정을 선택할 수 있습니다.

4. **매개 변수 및 실행 설정**을 선택하고 **ACTION** 필드를 **Stop**으로 설정합니다.

5. **확인** 을 선택하여 변경 내용을 저장합니다.

## <a name="next-steps"></a>다음 단계

* 작업 중에 기능을 모니터링하려면 [작업 시간 외 VM 시작/중지에서 로그 쿼리](automation-solution-vm-management-logs.md)를 참조하세요.
* VM을 관리하는 동안 문제를 처리하려면 [작업 시간 외 VM 시작/중지 문제 해결](troubleshoot/start-stop-vm.md)을 참조하세요.
