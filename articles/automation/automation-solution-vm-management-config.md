---
title: VM 솔루션 시작/중지 구성
description: 이 문서에서는 다른 사용 사례 또는 시나리오를 지원 하도록 오프 시간 솔루션 동안 시작/중지 VM을 구성 하는 방법을 설명 합니다.
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: d3ca8d17d6637f0ab2b5a5d3d7a99ac0beaafd2e
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80550384"
---
# <a name="how-to-configure-startstop-vms-during-off-hours-solution"></a>오프 시간 솔루션 동안 VM 시작/중지를 구성하는 방법

영업외 솔루션 중 VM 시작/중지를 사용하면 다음을 수행할 수 있습니다.

- [VM을 시작하고 중지하도록 예약합니다.](#schedule)
- [Azure 태그를 사용하여](#tags) VM을 오름차순으로 시작하고 중지하도록 예약합니다(클래식 VM에서는 지원되지 않음).
- [낮은 CPU 사용량을](#cpuutil)기반으로 VM을 자동 중지합니다.

이 문서에서는 이러한 시나리오를 지원 하도록 솔루션을 성공적으로 구성 하는 방법을 설명 합니다. 다음과 같이 솔루션에 대한 다른 일반적인 구성 설정을 수행하는 방법도 알아볼 수 있습니다.

* [이메일 알림 구성](#configure-email-notifications)

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

1. 대상 VM을 지정하도록 **External_Stop_ResourceGroupNames** 및 **External_ExcludeVMNames** 변수를 구성합니다.

2. **Scheduled-StartVM** 및 **Scheduled-StopVM** 일정을 설정하고 업데이트합니다.

3. ACTION 매개 변수를 **start**로 설정하고 WHATIF 매개 변수를 **True**로 설정하여 **ScheduledStartStop_Parent** Runbook을 실행하면 변경 내용을 미리 볼 수 있습니다.

### <a name="target-the-start-and-stop-action-by-vm-list"></a>VM 목록별로 시작 및 중지 작업의 대상 지정

1. ACTION 매개 변수를 **start**로 설정하고, *VMList* 매개 변수에 쉼표로 구분된 VM 목록을 추가한 다음, WHATIF 매개 변수를 **True**로 설정하여 **ScheduledStartStop_Parent** Runbook을 실행합니다. 변경 사항을 미리 봅니다.

2. 쉼표로 구분된 VM 목록(VM1, VM2, VM3)을 사용하여 **External_ExcludeVMNames** 매개 변수를 구성합니다.

3. 이 시나리오는 **External_Start_ResourceGroupNames** 및 **External_Stop_ResourceGroupnames** 변수를 고려하지 않습니다. 이 시나리오의 경우 고유한 Automation 일정을 만들어야 합니다. 자세한 내용은 [Azure Automation에서 Runbook 예약](../automation/automation-schedules.md)을 참조하세요.

    > [!NOTE]
    > **Target ResourceGroup Names**의 값은 두 **External_Start_ResourceGroupNames** 및 **External_Stop_ResourceGroupNames**의 값으로 저장됩니다. 추가 세분성을 위해 각기 다른 리소스 그룹을 대상으로 하도록 이러한 각 변수를 수정할 수 있습니다. 시작 작업의 경우 **External_Start_ResourceGroupNames**를 사용하고, 중지 작업의 경우 **External_Stop_ResourceGroupNames**를 사용합니다. VM이 시작 및 중지 일정에 자동으로 추가됩니다.

## <a name="scenario-2-startstop-vms-in-sequence-by-using-tags"></a><a name="tags"></a>시나리오 2: 태그를 사용하여 시퀀스의 VM 시작/중지

분산 워크로드를 지원하는 여러 VM에 둘 이상의 구성 요소를 포함하는 환경에서는 순서대로 시작 및 중지되는 구성 요소 시퀀스를 지원하는 것이 중요합니다. 다음 단계를 수행하여 이 시나리오를 수행할 수 있습니다.

### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>구독 및 리소스 그룹에 대한 시작 및 중지 작업의 대상 지정

1. **External_Start_ResourceGroupNames** 및 **External_Stop_ResourceGroupNames** 변수를 대상으로 하는 VM에 양수 정수 값을 가진 **시퀀스 시작** 및 **시퀀스 중지** 태그를 추가합니다. 시작 및 중지 작업은 오름차순으로 수행됩니다. VM에 태그를 지정하는 방법을 알아보려면 [Azure에서 Windows 가상 머신에 태그 지정](../virtual-machines/windows/tag.md) 및 [Azure에서 Linux 가상 머신에 태그 지정](../virtual-machines/linux/tag.md)을 참조하세요.

2. 요구 사항을 충족하는 날짜 및 시간으로 일정 **Sequenced-StartVM** 및 **Sequenced StopVM**을 수정하고 일정을 사용하도록 설정합니다.

3. ACTION 매개 변수를 **start**로 설정하고 WHATIF 매개 변수를 **True**로 설정하여 **SequencedStartStop_Parent** Runbook을 실행하면 변경 내용을 미리 볼 수 있습니다.

4. 작업을 미리 보고 프로덕션 VM에 대해 구현하기 전에 필요한 변경을 수행합니다. 준비가 되면 매개 변수를 **False**로 설정하여 Runbook을 수동으로 실행하거나 Automation 일정 **Sequenced-StartVM** 및 **Sequenced-StopVM**이 지정된 일정에 따라 자동으로 실행되도록 할 수 있습니다.

### <a name="target-the-start-and-stop-action-by-vm-list"></a>VM 목록별로 시작 및 중지 작업의 대상 지정

1. 양의 정수 값이 포함된 **sequencestart** 및 **sequencestop** 태그를 **VMList** 매개 변수에 추가하려는 VM에 추가합니다.

2. ACTION 매개 변수를 **start**로 설정하고, *VMList* 매개 변수에 쉼표로 구분된 VM 목록을 추가한 다음, WHATIF 매개 변수를 **True**로 설정하여 **SequencedStartStop_Parent** Runbook을 실행합니다. 변경 사항을 미리 봅니다.

3. 쉼표로 구분된 VM 목록(VM1, VM2, VM3)을 사용하여 **External_ExcludeVMNames** 매개 변수를 구성합니다.

4. 이 시나리오는 **External_Start_ResourceGroupNames** 및 **External_Stop_ResourceGroupnames** 변수를 고려하지 않습니다. 이 시나리오의 경우 고유한 Automation 일정을 만들어야 합니다. 자세한 내용은 [Azure Automation에서 Runbook 예약](../automation/automation-schedules.md)을 참조하세요.

5. 작업을 미리 보고 프로덕션 VM에 대해 구현하기 전에 필요한 변경을 수행합니다. 준비가 되면 매개 변수를 **False**로 설정하여 monitoring-and-diagnostics/monitoring-action-groupsrunbook을 수동으로 실행하거나 Automation 일정 **Sequenced-StartVM** 및 **Sequenced-StopVM**이 지정된 일정에 따라 자동으로 실행되도록 할 수 있습니다.

## <a name="scenario-3-startstop-automatically-based-on-cpu-utilization"></a><a name="cpuutil"></a>시나리오 3: CPU 사용률에 따라 자동으로 시작/중지

이 솔루션은 시간 후와 같이 사용량이 많지 않은 기간 동안 사용되지 않는 VM을 평가하고 프로세서 사용률이 지정된 백분율미만인 경우 자동으로 종료하여 구독에서 Azure Resource Manager 및 Classic 가상 컴퓨터를 실행하는 비용을 관리하는 데 도움이 될 수 있습니다.

기본적으로 솔루션은 CPU 백분율 메트릭을 평가하고 평균 사용률이 5% 이하인지 파악하도록 미리 구성됩니다. 이 시나리오는 다음 변수를 통해 제어되며, 기본값이 요구 사항을 충족하지 않는 경우 수정할 수 있습니다.

* **External_AutoStop_MetricName**

* **External_AutoStop_Threshold**

* **External_AutoStop_TimeAggregationOperator**

* **External_AutoStop_TimeWindow**

* **External_AutoStop_Frequency**

* **External_AutoStop_Severity**

구독 및 리소스 그룹에 대해 작업을 활성화 및 대상으로 지정하거나 특정 VM 목록을 대상으로 지정할 수 있습니다.

**AutoStop_CreateAlert_Parent** Runbook을 실행하면 대상 구독, 리소스 그룹 및 VM이 있는지 확인합니다. VM이 있는 경우 부모 Runbook에서 확인된 각 VM에 대해 **AutoStop_CreateAlert_Child** Runbook을 호출합니다. 이 자식 실행책은 다음을 수행합니다.

* 확인된 각 VM에 대한 메트릭 경고 규칙을 만듭니다.

* 지정된 시간 간격에 대해 CPU가 구성된 임계값 아래로 떨어지면 특정 VM에 대한 **AutoStop_VM_Child** 런북을 트리거합니다. 그런 다음 이 Runbook은 VM을 중지하려고 시도합니다.

### <a name="to-target-the-auto-stop-action-against-all-vms-in-a-subscription"></a>구독의 모든 VM에 대해 자동 중지 작업을 대상으로 지정하려면

1. **External_Stop_ResourceGroupNames** 변수가 비어 있는지 확인하거나 * (와일드카드)로 설정합니다.

2. 【옵션 단계】 자동 종료에서 일부 VM을 제외하려면 **External_ExcludeVMNames** 변수에 VM 이름의 쉼표 분리 된 목록을 추가할 수 있습니다.

3. **Schedule_AutoStop_CreateAlert_Parent** 일정을 실행하여 구독의 모든 VM에 필요한 중지 VM 메트릭 경고 규칙을 만듭니다. 일정에 따라 이 작업을 실행하면 새 VM이 구독에 추가될 때 새 메트릭 경고 규칙을 만들 수 있습니다.

### <a name="to-target-the-auto-stop-action-against-all-vms-in-a-resource-groupmultiple-resource-groups"></a>리소스 그룹/여러 리소스 그룹의 모든 VM에 대해 자동 중지 작업을 대상으로 지정하려면

1. **External_Stop_ResourceGroupNames** 변수에 리소스 그룹 이름의 공통 분리 된 목록을 추가합니다.

2. 선택적으로 일부 VM을 자동 종료에서 제외하려는 경우 **External_ExcludeVMNames** 변수에 VM 이름의 쉼표 분리 된 목록을 추가할 수 있습니다.

3. **Schedule_AutoStop_CreateAlert_Parent** 일정을 실행하여 리소스 그룹의 모든 VM에 필요한 **VM 중지 메트릭** 경고 규칙을 만듭니다. 일정에서 이 작업을 실행하면 리소스 그룹에 새 VM이 추가될 때 새 메트릭 경고 규칙을 만들 수 있습니다.

### <a name="to-target-the-auto-stop-action-to-a-list-of-vms"></a>자동 중지 작업을 VM 목록으로 타겟팅하려면

1. 새 [Schedule을](shared-resources/schedules.md#creating-a-schedule) 만들고 **AutoStop_CreateAlert_Parent** Runbook에 연결하여 VM 이름의 쉼표 분리 목록을 **VMList** 매개 변수에 추가합니다.

2. 선택적으로 일부 VM을 자동 종료에서 제외하려는 경우 **External_ExcludeVMNames** 변수에 VM 이름의 쉼표 분리 목록을 추가할 수 있습니다.

## <a name="configure-email-notifications"></a>전자 메일 알림 구성

솔루션이 배포된 후 메일 알림을 변경하려면 배포 중에 만들어진 작업 그룹을 수정합니다.  

> [!NOTE]
> Azure Government 클라우드의 구독은 이 솔루션의 이메일 기능을 지원하지 않습니다.

1. Azure Portal에서 [모니터] -> [작업 그룹]으로 이동합니다. **StartStop_VM_Notication**이라는 작업 그룹을 선택합니다.

    ![Automation 업데이트 관리 솔루션 페이지](media/automation-solution-vm-management/azure-monitor.png)

2. **StartStop_VM_Notification** 페이지의 **세부 정보** 아래에서 **세부 정보 편집**을 클릭합니다. 그러면 **메일/SMS/푸시/음성** 페이지가 열립니다. 메일 주소를 업데이트하고 **확인**을 클릭하여 변경 내용을 저장합니다.

    ![Automation 업데이트 관리 솔루션 페이지](media/automation-solution-vm-management/change-email.png)

    또는 작업 그룹에 추가 작업을 추가할 수 있습니다. 작업 그룹에 대한 자세한 내용은 [작업 그룹](../azure-monitor/platform/action-groups.md)을 참조하세요.

솔루션이 가상 머신을 종료할 때 전송되는 예제 메일은 다음과 같습니다.

![Automation 업데이트 관리 솔루션 페이지](media/automation-solution-vm-management/email.png)

## <a name="addexclude-vms"></a><a name="add-exclude-vms"></a>VM 추가/제외

이 솔루션은 솔루션의 대상이 되는 VM을 추가하거나 특별히 솔루션에서 머신을 제외할 수 있는 기능을 제공합니다.

### <a name="add-a-vm"></a>VM 추가

VM이 실행될 때 시작/중지 솔루션에 VM이 포함되어 있는지 확인하는 데 사용할 수 있는 두 가지 옵션이 있습니다.

* 솔루션의 각 상위 [Runbook에는](automation-solution-vm-management.md#runbooks) **VMList** 매개 변수가 있습니다. 상황에 맞는 상위 Runbook을 예약할 때 이 매개 변수에 대 한 쉼표 구분 된 VM 이름 목록을 전달할 수 있으며 솔루션실행 시 이러한 VM이 포함 됩니다.

* 여러 VM을 선택하려면 시작하거나 중지하려는 VM이 포함된 리소스 그룹 이름을 사용하여 **External_Start_ResourceGroupNames** 및 **External_Stop_ResourceGroupNames**를 설정합니다. 솔루션이 구독의 모든 리소스 그룹에 대해 실행되도록 하려면 이 값을 `*`로 설정할 수도 있습니다.

### <a name="exclude-a-vm"></a>VM 제외

솔루션에서 VM을 제외하려면 해당 VM을 **External_ExcludeVMNames** 변수에 추가할 수 있습니다. 이 변수는 시작/중지 솔루션에서 제외할 특정 VM의 쉼표로 구분된 목록입니다. 이 목록은 140개의 VM으로 제한됩니다. 이 쉼표로 구분된 목록에 140개 이상의 VM을 추가하는 경우 제외되도록 설정된 VM이 실수로 시작되거나 중지될 수 있습니다.

## <a name="modify-the-startup-and-shutdown-schedules"></a>시작 및 종료 일정 수정

이 솔루션에서 시작 및 종료 일정을 관리하는 작업은 [Azure Automation에서 runbook 예약](automation-schedules.md)에 설명된 대로 동일한 단계를 따릅니다. VM을 시작하고 중지하려면 별도의 일정이 있어야 합니다.

특정 시간에 VM을 중지하도록 솔루션을 구성하는 작업이 지원됩니다. 이 시나리오에서는 **중지** 일정만 만들고 해당하는 **시작** 일정은 만들지 않습니다. 이렇게 하려면 다음을 수행해야 합니다.

1. **External_Stop_ResourceGroupNames** 변수에서 종료할 VM에 대한 리소스 그룹을 추가했는지 확인합니다.

2. VM을 종료하려는 시간에 대한 고유한 일정을 만듭니다.

3. **ScheduledStartStop_Parent** Runbook으로 이동하고 **일정**을 클릭합니다. 이 옵션을 사용하면 이전 단계에서 만든 일정을 선택할 수 있습니다.

4. **매개 변수를 선택하고 설정을 실행하고** **ACTION** 매개 변수를 **중지로**설정합니다.

5. **확인** 을 선택하여 변경 내용을 저장합니다.

## <a name="next-steps"></a>다음 단계

* 근무 외 시간에 VM 시작/중지 문제를 해결하는 방법을 알아보려면 [VM 시작/중지 를](troubleshoot/start-stop-vm.md)참조하세요.

* Azure Monitor Logs에 기록된 자동화 레코드와 예제 로그 검색 쿼리를 [검토하여](automation-solution-vm-management-logs.md) VM 시작/중지에서 자동화 runbook 작업의 상태를 분석합니다.
