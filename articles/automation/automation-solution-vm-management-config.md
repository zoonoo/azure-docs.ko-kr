---
title: 작업 시간 외 Vm 시작/중지 솔루션 구성 Azure Automation
description: 이 문서에서는 다양 한 사용 사례 또는 시나리오를 지원 하도록 작업 시간 외 VM 시작/중지 솔루션을 구성 하는 방법을 설명 합니다.
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 9842a736cf922e0490f2b0c8acb1d2e5833f3d6c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81604771"
---
# <a name="how-to-configure-startstop-vms-during-off-hours-solution"></a>업무 시간 외 Vm 시작/중지 솔루션을 구성 하는 방법

**작업 시간 외 Vm 시작/중지** 솔루션을 사용 하 여 다음을 수행할 수 있습니다.

- [Vm을 시작 및 중지 하도록 예약](#schedule)합니다.
- [Azure 태그](#tags) (클래식 vm에서는 지원 되지 않음)를 사용 하 여 vm을 시작 하 고 오름차순으로 중지 하도록 예약 합니다.
- [낮은 CPU 사용량](#cpuutil)에 따라 vm을 자동으로 중지 합니다.

이 문서에서는 이러한 시나리오를 지원 하도록 솔루션을 구성 하는 방법을 설명 합니다. 또한 다음과 같은 솔루션에 대 한 다른 일반적인 구성 설정을 수행 하는 방법을 배울 수 있습니다.

* [전자 메일 알림 구성](#configure-email-notifications)

* [VM 추가](#add-a-vm)

* [VM 제외](#exclude-a-vm)

* [시작 및 종료 일정 수정](#modify-the-startup-and-shutdown-schedules)

## <a name="scenario-1-startstop-vms-on-a-schedule"></a><a name="schedule"></a>시나리오 1: 일정에 따라 VM 시작/중지

이 시나리오는 솔루션을 처음 배포할 때 기본 구성입니다. 예를 들어 저녁에 사무실을 나갈 때 구독의 모든 VM을 중지하고, 아침에 사무실로 복귀할 때 시작하도록 구성할 수 있습니다. 배포 중에 일정 **Scheduled-StartVM** 및 **Scheduled-StopVM**을 구성할 경우 대상 VM을 시작 및 중지합니다. VM을 중지하도록 이 솔루션을 구성하려면 [시작 및 종료 일정 수정](#modify-the-startup-and-shutdown-schedules)을 참조하여 사용자 지정 일정을 구성하는 방법을 알아봅니다.

> [!NOTE]
> 표준 시간대는 예약 시간 매개 변수를 구성할 당시의 현재 표준 시간대입니다. 하지만 Azure Automation에서 UTC 형식으로 저장됩니다. 따라서 배포 중에 처리될 때 표준 시간대 변환 작업을 수행할 필요가 없습니다.

다음 변수 **External_Start_ResourceGroupNames**, **External_Stop_ResourceGroupNames** 및 **External_ExcludeVMNames**를 구성하여 범위 안에 포함되는 VM을 제어합니다.

구독 및 리소스 그룹 또는 특정 VM 목록 중 하나로만 작업 대상을 지정할 수 있습니다.

### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>구독 및 리소스 그룹에 대한 시작 및 중지 작업의 대상 지정

1. `External_Stop_ResourceGroupNames` 및 `External_ExcludeVMNames` 변수를 구성 하 여 대상 vm을 지정 합니다.

2. **Scheduled-StartVM** 및 **Scheduled-StopVM** 일정을 설정하고 업데이트합니다.

3. **작업** 매개 변수 필드를 **start** 로 설정 하 고 **WHATIF** 매개 변수 필드를 True로 설정 하 여 **ScheduledStartStop_Parent** runbook을 실행 하 여 변경 내용을 미리 봅니다.

### <a name="target-the-start-and-stop-action-by-vm-list"></a>VM 목록별로 시작 및 중지 작업의 대상 지정

1. **시작**으로 설정 된 **작업** 으로 runbook **ScheduledStartStop_Parent** 를 실행 하 고, **vmlist** 매개 변수 필드에 쉼표로 구분 된 vm 목록을 추가한 다음, **WHATIF** 매개 변수 필드를 True로 설정 합니다. 변경 사항을 미리 봅니다.

2. 쉼표로 구분 `External_ExcludeVMNames` 된 vm 목록 (VM1, V M 2, v m 3)을 사용 하 여 변수를 구성 합니다.

3. 이 시나리오에서는 및 `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupnames` 변수를 인식 하지 않습니다. 이 시나리오의 경우 고유한 Automation 일정을 만들어야 합니다. 자세한 내용은 [Azure Automation에서 Runbook 예약](../automation/automation-schedules.md)을 참조하세요.

    > [!NOTE]
    > **대상 ResourceGroup 이름** 에 대 한 값은 및 `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupNames`모두에 대 한 값으로 저장 됩니다. 추가 세분성을 위해 각기 다른 리소스 그룹을 대상으로 하도록 이러한 각 변수를 수정할 수 있습니다. 시작 작업의 경우를 `External_Start_ResourceGroupNames`사용 하 고 `External_Stop_ResourceGroupNames` 중지 동작에 사용 합니다. VM이 시작 및 중지 일정에 자동으로 추가됩니다.

## <a name="scenario-2-startstop-vms-in-sequence-by-using-tags"></a><a name="tags"></a>시나리오 2: 태그를 사용하여 시퀀스의 VM 시작/중지

분산 워크로드를 지원하는 여러 VM에 둘 이상의 구성 요소를 포함하는 환경에서는 순서대로 시작 및 중지되는 구성 요소 시퀀스를 지원하는 것이 중요합니다. 다음 단계를 수행하여 이 시나리오를 수행할 수 있습니다.

### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>구독 및 리소스 그룹에 대한 시작 및 중지 작업의 대상 지정

1. 및 `External_Stop_ResourceGroupNames` 변수 `sequencestart` 를 대상 `sequencestop` `External_Start_ResourceGroupNames` 으로 하는 vm에 양의 정수 값이 있는 및 태그를 추가 합니다. 시작 및 중지 작업은 오름차순으로 수행됩니다. VM에 태그를 지정하는 방법을 알아보려면 [Azure에서 Windows 가상 머신에 태그 지정](../virtual-machines/windows/tag.md) 및 [Azure에서 Linux 가상 머신에 태그 지정](../virtual-machines/linux/tag.md)을 참조하세요.

2. 요구 사항을 충족하는 날짜 및 시간으로 일정 **Sequenced-StartVM** 및 **Sequenced StopVM**을 수정하고 일정을 사용하도록 설정합니다.

3. **작업** 을 **시작** 으로 설정 하 고 **WHATIF** 를 True로 설정 하 여 **SequencedStartStop_Parent** runbook을 실행 하 여 변경 내용을 미리 봅니다.

4. 작업을 미리 보고 프로덕션 VM에 대해 구현하기 전에 필요한 변경을 수행합니다. 준비가 되 면 매개 변수를 **False**로 설정 하 여 runbook을 수동으로 실행 하거나 지정 된 일정 `Sequenced-StartVM` 에 `Sequenced-StopVM` 따라 자동화를 예약 하 고 자동으로 실행 하도록 합니다.

### <a name="target-the-start-and-stop-action-by-vm-list"></a>VM 목록별로 시작 및 중지 작업의 대상 지정

1. 매개 변수에 `sequencestart` 추가 하려는 `sequencestop` vm에 양의 정수 값이 있는 및 태그를 추가 합니다. `VMList`

2. **시작**으로 설정 된 **작업** 으로 runbook **SequencedStartStop_Parent** 를 실행 하 고 **vmlist** 매개 변수 필드에 쉼표로 구분 된 vm 목록을 추가한 다음 **WHATIF** 를 True로 설정 합니다. 변경 사항을 미리 봅니다.

3. 쉼표로 구분 `External_ExcludeVMNames` 된 vm 목록 (VM1, V M 2, v m 3)을 사용 하 여 변수를 구성 합니다.

4. 이 시나리오에서는 및 `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupnames` 변수를 인식 하지 않습니다. 이 시나리오의 경우 고유한 Automation 일정을 만들어야 합니다. 자세한 내용은 [Azure Automation에서 Runbook 예약](../automation/automation-schedules.md)을 참조하세요.

5. 작업을 미리 보고 프로덕션 VM에 대해 구현하기 전에 필요한 변경을 수행합니다. 준비가 되 면 매개 변수를 **False**로 설정 하 여 **모니터링 및 진단/모니터링-작업-grunbook** 을 수동으로 실행 합니다. 또는 Automation 일정 `Sequenced-StartVM` 을 사용 하도록 지정 `Sequenced-StopVM` 하 고 정해진 일정에 따라 자동으로 실행 합니다.

## <a name="scenario-3-startstop-automatically-based-on-cpu-utilization"></a><a name="cpuutil"></a>시나리오 3: CPU 사용률에 따라 자동으로 시작/중지

이 솔루션은 사용량이 적은 기간 동안 사용 되지 않는 Vm을 평가 하 여 구독에서 Azure Resource Manager 및 클래식 가상 머신을 실행 하는 비용을 관리 하 고, 프로세서 사용률이 지정 된 비율 미만인 경우 자동으로 종료 하는 데 도움이 됩니다.

기본적으로 솔루션은 CPU 백분율 메트릭을 평가하고 평균 사용률이 5% 이하인지 파악하도록 미리 구성됩니다. 이 시나리오는 다음 변수를 통해 제어 되며, 기본값이 요구 사항을 충족 하지 않는 경우 수정할 수 있습니다.

* `External_AutoStop_MetricName`
* `External_AutoStop_Threshold`
* `External_AutoStop_TimeAggregationOperator`
* `External_AutoStop_TimeWindow`
* `External_AutoStop_Frequency`
* `External_AutoStop_Severity`

구독 및 리소스 그룹에 대해 작업을 사용 하도록 설정 하 고 대상으로 지정 하거나 특정 Vm 목록을 대상으로 지정할 수 있습니다.

Runbook **AutoStop_CreateAlert_Parent** 를 실행 하면 대상 구독, 리소스 그룹 및 vm이 있는지 확인 합니다. Vm이 있는 경우 runbook은 부모 runbook에 의해 확인 된 각 VM에 대해 **AutoStop_CreateAlert_Child** runbook을 호출 합니다. 이 자식 runbook은 다음을 수행 합니다.

* 확인 된 각 VM에 대 한 메트릭 경고 규칙을 만듭니다.

* CPU가 지정 된 시간 간격 동안 구성 된 임계값 아래로 떨어지면 특정 VM에 대 한 **AutoStop_VM_Child** runbook을 트리거합니다. 이 runbook은 VM을 중지 하려고 시도 합니다.

### <a name="to-target-the-auto-stop-action-against-all-vms-in-a-subscription"></a>구독의 모든 Vm에 대해 자동 중지 작업을 대상으로 하려면

1. `External_Stop_ResourceGroupNames` 변수가 비어 있거나 * (와일드 카드)로 설정 되어 있는지 확인 하십시오.

2. [선택적 단계] 자동 종료에서 일부 Vm을 제외 하려는 경우 쉼표로 구분 된 VM 이름 목록을 `External_ExcludeVMNames` 변수에 추가할 수 있습니다.

3. 실행 `Schedule_AutoStop_CreateAlert_Parent` 일정을 사용 하도록 설정 하 여 구독에 있는 모든 vm에 필요한 vm 중지 메트릭 경고 규칙을 만듭니다. 이 일정 유형을 실행 하면 새 Vm이 구독에 추가 될 때 새 메트릭 경고 규칙을 만들 수 있습니다.

### <a name="to-target-the-auto-stop-action-against-all-vms-in-a-resource-group-or-multiple-resource-groups"></a>리소스 그룹 또는 여러 리소스 그룹의 모든 Vm에 대해 자동 중지 작업을 대상으로 하려면

1. 쉼표로 구분 된 리소스 그룹 이름 목록을 `External_Stop_ResourceGroupNames` 변수에 추가 합니다.

2. 자동 종료에서 일부 Vm을 제외 하려는 경우 쉼표로 구분 된 VM 이름 목록을 `External_ExcludeVMNames` 변수에 추가할 수 있습니다.

3. 을 실행 하 여 리소스 그룹의 모든 Vm에 대 한 필수 중지 VM 메트릭 경고 규칙을 만들도록 **Schedule_AutoStop_CreateAlert_Parent** 일정을 사용 하도록 설정 합니다. 일정에 따라이 작업을 실행 하면 새 Vm이 리소스 그룹에 추가 될 때 새 메트릭 경고 규칙을 만들 수 있습니다.

### <a name="to-target-the-autostop-action-to-a-list-of-vms"></a>Vm 목록에 대해 autostop 작업을 대상으로 하려면

1. 새 [일정](shared-resources/schedules.md#creating-a-schedule) 을 만들고 **AutoStop_CreateAlert_Parent** runbook에 연결 하 여 `VMList` 매개 변수에 쉼표로 구분 된 VM 이름 목록을 추가 합니다.

2. 필요에 따라 자동 종료에서 일부 Vm을 제외 하려는 경우 쉼표로 구분 된 VM 이름 목록을 `External_ExcludeVMNames` 변수에 추가할 수 있습니다.

## <a name="configure-email-notifications"></a>전자 메일 알림 구성

솔루션이 배포 된 후 전자 메일 알림을 변경 하려면 배포 중에 만든 작업 그룹을 수정 합니다.  

> [!NOTE]
> Azure Government 클라우드의 구독은이 솔루션의 메일 기능을 지원 하지 않습니다.

1. Azure Portal에서 **모니터**, **작업 그룹**으로 차례로 이동 합니다. **StartStop_VM_Notication**이라는 작업 그룹을 선택 합니다.

    ![Automation 업데이트 관리 솔루션 페이지](media/automation-solution-vm-management/azure-monitor.png)

2. **StartStop_VM_Notification** 페이지의 **세부 정보** 아래에서 **세부 정보 편집**을 클릭합니다. 그러면 **메일/SMS/푸시/음성** 페이지가 열립니다. 메일 주소를 업데이트하고 **확인**을 클릭하여 변경 내용을 저장합니다.

    ![Automation 업데이트 관리 솔루션 페이지](media/automation-solution-vm-management/change-email.png)

    또는 작업 그룹에 추가 작업을 추가할 수 있습니다. 작업 그룹에 대한 자세한 내용은 [작업 그룹](../azure-monitor/platform/action-groups.md)을 참조하세요.

솔루션이 가상 머신을 종료할 때 전송되는 예제 메일은 다음과 같습니다.

![Automation 업데이트 관리 솔루션 페이지](media/automation-solution-vm-management/email.png)

## <a name="addexclude-vms"></a><a name="add-exclude-vms"></a>VM 추가/제외

이 솔루션은 솔루션의 대상이 되는 VM을 추가하거나 특별히 솔루션에서 머신을 제외할 수 있는 기능을 제공합니다.

### <a name="add-a-vm"></a>VM 추가

VM이 실행 될 때 시작/중지 솔루션에 포함 되도록 하기 위해 사용할 수 있는 두 가지 옵션이 있습니다.

* 솔루션의 각 부모 [runbook](automation-solution-vm-management.md#runbooks) 에는 `VMList` 매개 변수가 있습니다. 상황에 맞는 부모 runbook을 예약할 때 쉼표로 구분 된 VM 이름 목록을이 매개 변수에 전달할 수 있으며, 이러한 Vm은 솔루션이 실행 될 때 포함 됩니다.

* 여러 Vm을 선택 하려면을 `External_Start_ResourceGroupNames` 시작 `External_Stop_ResourceGroupNames` 하거나 중지 하려는 vm이 포함 된 리소스 그룹 이름으로 설정 합니다. 구독에서 모든 리소스 그룹에 대해 솔루션을 실행 `*` 하도록 변수를 값으로 설정할 수도 있습니다.

### <a name="exclude-a-vm"></a>VM 제외

솔루션에서 VM을 제외 하려면 `External_ExcludeVMNames` 변수에 추가할 수 있습니다. 이 변수는 시작/중지 솔루션에서 제외할 특정 Vm의 쉼표로 구분 된 목록입니다. 이 목록은 140개의 VM으로 제한됩니다. 이 쉼표로 구분 된 목록에 140 개 이상의 Vm을 추가 하는 경우 제외 되도록 설정 된 Vm이 실수로 시작 되거나 중지 될 수 있습니다.

## <a name="modify-the-startup-and-shutdown-schedules"></a>시작 및 종료 일정 수정

이 솔루션에서 시작 및 종료 일정을 관리하는 작업은 [Azure Automation에서 runbook 예약](automation-schedules.md)에 설명된 대로 동일한 단계를 따릅니다. VM을 시작하고 중지하려면 별도의 일정이 있어야 합니다.

특정 시간에 VM을 중지하도록 솔루션을 구성하는 작업이 지원됩니다. 이 시나리오에서는 **중지** 일정을 만들고 해당 하는 **시작** 일정은 만들지 않습니다. 이렇게 하려면 다음을 수행해야 합니다.

1. `External_Stop_ResourceGroupNames` 변수에서 종료할 vm에 대 한 리소스 그룹을 추가 했는지 확인 합니다.

2. VM을 종료하려는 시간에 대한 고유한 일정을 만듭니다.

3. **ScheduledStartStop_Parent** Runbook으로 이동하고 **일정**을 클릭합니다. 이 옵션을 사용하면 이전 단계에서 만든 일정을 선택할 수 있습니다.

4. **매개 변수 및 실행 설정** 을 선택 하 고 **작업** 필드를 **중지**로 설정 합니다.

5. **확인** 을 선택하여 변경 내용을 저장합니다.

## <a name="next-steps"></a>다음 단계

* 작업 시간 외 VM 시작/중지 문제를 해결 하는 방법에 대 한 자세한 내용은 [Vm 시작/중지 문제 해결](troubleshoot/start-stop-vm.md)을 참조 하세요.

* Azure Monitor 로그에 작성 된 자동화 레코드와 예제 로그 검색 쿼리를 [검토](automation-solution-vm-management-logs.md) 하 여 vm 시작/중지에서 automation runbook 작업의 상태를 분석 합니다.
