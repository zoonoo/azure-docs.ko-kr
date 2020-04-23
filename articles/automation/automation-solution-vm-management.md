---
title: 업무 시간 외 Vm 시작/중지 솔루션
description: 이 VM 관리 솔루션은 일정에 따라 Azure virtual machines를 시작 및 중지 하 고 Azure Monitor 로그를 사전에 모니터링 합니다.
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 0331678b50d2448013556ab0694d0ca87045c3a3
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82096919"
---
# <a name="startstop-vms-during-off-hours-solution-in-azure-automation"></a>Azure Automation에서 작업 시간 외 Vm 시작/중지 솔루션

**업무 시간 외 Vm 시작/중지** 솔루션은 Azure virtual machines를 시작 하거나 중지 합니다. 사용자 정의 일정에서 컴퓨터를 시작 하거나 중지 하 고, Azure Monitor 로그를 통해 정보를 제공 하 고, [작업 그룹](../azure-monitor/platform/action-groups.md)을 사용 하 여 선택적 전자 메일을 보냅니다. 솔루션은 대부분의 시나리오에서 Azure Resource Manager 및 클래식 Vm을 모두 지원 합니다. 

이 솔루션은 VM 비용을 최적화하려는 사용자에게 분산된 저비용 자동화 옵션을 제공합니다. 이 솔루션을 사용하면 다음과 같은 작업을 수행할 수 있습니다.

- [Vm을 시작 및 중지 하도록 예약](automation-solution-vm-management-config.md#schedule)합니다.
- [Azure 태그](automation-solution-vm-management-config.md#tags) (클래식 vm에서는 지원 되지 않음)를 사용 하 여 vm을 시작 하 고 오름차순으로 중지 하도록 예약 합니다.
- [낮은 CPU 사용량](automation-solution-vm-management-config.md#cpuutil)에 따라 vm을 자동으로 중지 합니다.

> [!NOTE]
> **작업 시간 외 Vm 시작/중지** 솔루션은 사용 가능한 최신 버전의 Azure 모듈을 지원 하도록 업데이트 되었습니다.

현재 솔루션에 대 한 제한 사항은 다음과 같습니다.

- 모든 지역에서 Vm을 관리 하지만 Azure Automation 계정과 동일한 구독 에서만 사용할 수 있습니다.
- Azure에서 사용할 수 있으며 Log Analytics 작업 영역, Azure Automation 계정 및 경고를 지 원하는 모든 지역에 Azure Government 있습니다. Azure Government 지역은 현재 전자 메일 기능을 지원 하지 않습니다.

## <a name="solution-prerequisites"></a>솔루션 필수 조건

이 솔루션에 대한 Runbook은 [Azure 실행 계정](automation-create-runas-account.md)을 통해 작동합니다. 실행 계정은 자주 만료 되거나 변경 될 수 있는 암호 대신 인증서 인증을 사용 하기 때문에 기본 인증 방법입니다.

**업무 시간 외 Vm 시작/중지** 솔루션에 별도의 Automation 계정을 사용 하는 것이 좋습니다. Azure 모듈 버전이 자주 업그레이드 되 고 해당 매개 변수가 변경 될 수 있습니다. 솔루션은 동일한 흐름에서 업그레이드 되지 않으며 사용 하는 최신 버전의 cmdlet에서 작동 하지 않을 수 있습니다. 프로덕션 Automation 계정으로 가져오기 전에 테스트 자동화 계정에서 모듈 업데이트를 테스트 하는 것이 좋습니다.

## <a name="solution-permissions"></a>솔루션 권한

**업무 시간 외 Vm 시작/중지** 솔루션을 배포 하려면 특정 권한이 있어야 합니다. 솔루션에서 배포 중에 새 계정 및 작업 영역을 만드는 경우 필요한 사용 권한 으로부터 미리 만든 Automation 계정을 사용 하 고 작업 영역 Log Analytics 작업 영역을 사용 하는 경우 사용 권한이 달라 집니다. 

구독 및 Azure Active Directory 테 넌 트의 전역 관리자에 대 한 참가자 인 경우 사용 권한을 구성할 필요가 없습니다. 이러한 권한이 없거나 사용자 지정 역할을 구성 해야 하는 경우 아래에 설명 된 사용 권한이 있는지 확인 합니다.

### <a name="permissions-for-pre-existing-automation-account-and-log-analytics-workspace"></a>기존 Automation 계정 및 Log Analytics 작업 영역에 대 한 사용 권한

**업무 시간 외 Vm 시작/중지** 솔루션을 기존 Automation 계정 및 Log Analytics 작업 영역에 배포 하려면 솔루션을 배포 하는 사용자에 게 리소스 그룹 범위에 대 한 다음 권한이 필요 합니다. 역할에 대 한 자세한 내용은 [Azure 리소스에 대 한 사용자 지정 역할](../role-based-access-control/custom-roles.md)을 참조 하세요.

| 사용 권한 | 범위|
| --- | --- |
| Microsoft.Automation/automationAccounts/read | 리소스 그룹 |
| Microsoft.Automation/automationAccounts/variables/write | 리소스 그룹 |
| Microsoft.Automation/automationAccounts/schedules/write | 리소스 그룹 |
| Microsoft.Automation/automationAccounts/runbooks/write | 리소스 그룹 |
| Microsoft.Automation/automationAccounts/connections/write | 리소스 그룹 |
| Microsoft.Automation/automationAccounts/certificates/write | 리소스 그룹 |
| Microsoft.Automation/automationAccounts/modules/write | 리소스 그룹 |
| Microsoft.Automation/automationAccounts/modules/read | 리소스 그룹 |
| Microsoft. automation/automationAccounts/jobSchedules/write | 리소스 그룹 |
| Microsoft.Automation/automationAccounts/jobs/write | 리소스 그룹 |
| Microsoft.Automation/automationAccounts/jobs/read | 리소스 그룹 |
| Microsoft.OperationsManagement/solutions/write | 리소스 그룹 |
| OperationalInsights/작업 영역/* | 리소스 그룹 |
| Microsoft diagnosticSettings/write/write | 리소스 그룹 |
| Microsoft.Insights/ActionGroups/Write | 리소스 그룹 |
| Microsoft Insights/ActionGroups/read | 리소스 그룹 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹 |
| Microsoft.Resources/deployments/* | 리소스 그룹 |

### <a name="permissions-for-new-automation-account-and-new-log-analytics-workspace"></a>새 Automation 계정 및 새 Log Analytics 작업 영역에 대 한 사용 권한

**업무 시간 외 Vm 시작/중지** 솔루션을 새 Automation 계정 및 Log Analytics 작업 영역에 배포할 수 있습니다. 이 경우 솔루션을 배포 하는 사용자에 게는이 섹션에 정의 된 권한 뿐만 아니라 이전 섹션에 정의 된 사용 권한이 필요 합니다. 

솔루션을 배포 하는 사용자에 게는 다음 역할이 필요 합니다.

- 구독에 대 한 공동 관리자입니다. 클래식 Vm을 관리 하려는 경우 클래식 실행 계정을 만들려면이 역할이 필요 합니다. [클래식 실행 계정은](automation-create-standalone-account.md#create-a-classic-run-as-account) 더 이상 기본적으로 생성 되지 않습니다.
- [Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md) 응용 프로그램 개발자 역할의 멤버 자격 실행 계정을 구성 하는 방법에 대 한 자세한 내용은 [실행 계정을 구성할 수 있는 권한](manage-runas-account.md#permissions)을 참조 하세요.
- 구독에 대 한 참가자 또는 다음 사용 권한.

| 사용 권한 |범위|
| --- | --- |
| Microsoft. 권한 부여/작업/읽기 | Subscription|
| Microsoft.Authorization/permissions/read |Subscription|
| Microsoft.Authorization/roleAssignments/read | Subscription |
| Microsoft.Authorization/roleAssignments/write | Subscription |
| Microsoft.Authorization/roleAssignments/delete | Subscription |
| Microsoft.Automation/automationAccounts/connections/read | 리소스 그룹 |
| Microsoft.Automation/automationAccounts/certificates/read | 리소스 그룹 |
| Microsoft.Automation/automationAccounts/write | 리소스 그룹 |
| Microsoft.OperationalInsights/workspaces/write | 리소스 그룹 |

## <a name="solution-components"></a>솔루션 구성 요소

**업무 시간 외 Vm 시작/중지 솔루션** 에는 미리 구성 된 runbook, 일정 및 Azure Monitor 로그와의 통합이 포함 됩니다. 이러한 요소를 사용 하 여 비즈니스 요구에 맞게 Vm의 시작 및 종료를 조정할 수 있습니다.

### <a name="runbooks"></a>Runbook

다음 표에서는 솔루션이 Automation 계정에 배포 하는 runbook을 나열 합니다. Runbook 코드를 변경 하지 마십시오. 대신, 새 기능에 대한 고유한 Runbook을 작성할 수는 있습니다.

> [!IMPORTANT]
> **자식을** 이름에 추가 하 여 runbook을 직접 실행 하지 마세요.

모든 부모 runbook에는 `WhatIf` 매개 변수가 포함 됩니다. True로 설정 되 면 매개 변수는 runbook이 매개 변수 없이 실행 될 때 runbook이 수행 하는 정확한 동작을 자세히 설명 하 고 올바른 Vm이 대상으로 지정 되었는지 확인 합니다. Runbook은 `WhatIf` 매개 변수가 False로 설정 된 경우에만 정의 된 작업을 수행 합니다.

|Runbook | 매개 변수 | Description|
| --- | --- | ---|
|AutoStop_CreateAlert_Child | VMObject <br> AlertAction <br> WebHookURI | 부모 Runbook에서 호출됩니다. 이 runbook은 자동 중지 시나리오에 대 한 리소스 별로 경고를 만듭니다.|
|AutoStop_CreateAlert_Parent | VMList<br> WhatIf: True 또는 False  | 대상 구독 또는 리소스 그룹에서 VM에 대해 Azure 경고 규칙을 만들거나 업데이트합니다. <br> `VMList`는 쉼표로 구분 된 Vm 목록입니다. `vm1, vm2, vm3`)을 입력합니다.<br> `WhatIf`runbook 논리의 유효성 검사를 실행 하지 않고 사용 하도록 설정 합니다.|
|AutoStop_Disable | 없음 | 자동 중지 경고 및 기본 일정을 사용 하지 않도록 설정 합니다.|
|AutoStop_VM_Child | WebHookData | 부모 Runbook에서 호출됩니다. 경고 규칙은이 runbook을 호출 하 여 클래식 VM을 중지 합니다.|
|AutoStop_VM_Child_ARM | WebHookData |부모 Runbook에서 호출됩니다. 경고 규칙은이 runbook을 호출 하 여 VM을 중지 합니다.  |
|ScheduledStartStop_Base_Classic | CloudServiceName<br> 작업: 시작 또는 중지<br> VMList  | Cloud Services 하 여 클래식 VM 그룹에서 작업을 시작 하거나 중지 합니다. |
|ScheduledStartStop_Child | VMName <br> 작업: 시작 또는 중지 <br> ResourceGroupName | 부모 Runbook에서 호출됩니다. 예약된 중지에서 시작 또는 중지 작업을 실행합니다.|
|ScheduledStartStop_Child_Classic | VMName<br> 작업: 시작 또는 중지<br> ResourceGroupName | 부모 Runbook에서 호출됩니다. 클래식 Vm에 대해 예약 된 중지에 대 한 시작 또는 중지 작업을 실행 합니다. |
|ScheduledStartStop_Parent | 작업: 시작 또는 중지 <br>VMList <br> WhatIf: True 또는 False | 구독의 모든 Vm을 시작 하거나 중지 합니다. 변수 `External_Start_ResourceGroupNames` 를 편집 `External_Stop_ResourceGroupNames` 하 여 이러한 대상 리소스 그룹에 대해서만 실행 합니다. 변수를 `External_ExcludeVMNames` 업데이트 하 여 특정 vm을 제외할 수도 있습니다.|
|SequencedStartStop_Parent | 작업: 시작 또는 중지 <br> WhatIf: True 또는 False<br>VMList| Start/stop 작업을 시퀀싱 하려는 각 VM에 **sequencestart** 및 **sequencestop** 라는 태그를 만듭니다. 이 태그 이름은 대/소문자를 구분합니다. 태그 값은 시작하거나 중지하려는 순서에 해당하는 양의 정수(1, 2, 3)여야 합니다. <br>**참고**: vm은 `External_Start_ResourceGroupNames`, `External_Stop_ResourceGroupNames`및 `External_ExcludeVMNames` 변수에 정의 된 리소스 그룹 내에 있어야 합니다. 작업이 적용되려면 적절한 태그가 있어야 합니다.|

### <a name="variables"></a>variables

다음 표에는 Automation 계정에서 만든 변수가 나열되어 있습니다. 접두사가 접두사로 붙은 변수만 수정 `External`합니다. 접두사가 접두사로 붙은 변수 `Internal` 를 수정 하면 원치 않는 효과가 발생 합니다.

> [!NOTE]
> VM 이름 및 리소스 그룹에 대 한 제한 사항은 주로 변수 크기의 결과입니다. [Azure Automation의 변수 자산을](https://docs.microsoft.com/azure/automation/shared-resources/variables)참조 하세요.

|변수 | Description|
|---------|------------|
|External_AutoStop_Condition | 경고를 트리거하기 전에 조건을 구성하는 데 필요한 조건부 연산자입니다. 허용 되는 `GreaterThan`값 `GreaterThanOrEqual`은 `LessThan`,, `LessThanOrEqual`및입니다.|
|External_AutoStop_Description | CPU %가 임계값을 초과하는 경우 VM을 중지하도록 경고합니다.|
|External_AutoStop_Frequency | 규칙에 대 한 평가 빈도입니다. 이 매개 변수는 시간 간격 형식의 입력을 허용합니다. 가능한 값은 5분에서 6시간 사이입니다. |
|External_AutoStop_MetricName | Azure 경고 규칙을 구성해야 하는 성능 메트릭의 이름입니다.|
|External_AutoStop_Severity | 0에서 4 사이의 범위를 지정할 수 있는 메트릭 경고의 심각도입니다. |
|External_AutoStop_Threshold | 변수에 `External_AutoStop_MetricName`지정 된 Azure 경고 규칙에 대 한 임계값입니다. 백분율 값의 범위는 1 ~ 100입니다.|
|External_AutoStop_TimeAggregationOperator | 조건을 평가 하기 위해 선택한 창 크기에 적용 되는 시간 집계 연산자입니다. 허용 되는 `Average`값 `Minimum`은 `Maximum`, `Total`,, `Last`및입니다.|
|External_AutoStop_TimeWindow | Azure에서 경고를 트리거하기 위해 선택 된 메트릭을 분석 하는 기간의 창 크기입니다. 이 매개 변수는 시간 간격 형식의 입력을 허용합니다. 가능한 값은 5분에서 6시간 사이입니다.|
|External_EnableClassicVMs| 솔루션의 대상으로 지정 된 클래식 Vm 인지 여부를 지정 하는 값입니다. 기본값은 True입니다. Azure CSP (클라우드 솔루션 공급자) 구독에 대해이 변수를 False로 설정 합니다. 클래식 Vm에는 [클래식 실행 계정이](automation-create-standalone-account.md#create-a-classic-run-as-account)필요 합니다.|
|External_ExcludeVMNames | 제외할 VM 이름의 쉼표로 구분 된 목록으로, 140 Vm으로 제한 됩니다. 140 개를 초과 하는 Vm을 목록에 추가 하는 경우 제외 되도록 설정 된 Vm이 실수로 시작 되거나 중지 될 수 있습니다.|
|External_Start_ResourceGroupNames | 시작 동작을 대상으로 하는 하나 이상의 리소스 그룹을 쉼표로 구분한 목록입니다.|
|External_Stop_ResourceGroupNames | 중지 동작을 대상으로 하는 하나 이상의 리소스 그룹을 쉼표로 구분한 목록입니다.|
|External_WaitTimeForVMRetrySeconds |**SequencedStartStop_Parent** runbook에 대 한 vm에서 작업이 수행 되는 대기 시간 (초)입니다. 이 변수를 사용 하면 runbook에서 다음 작업을 진행 하기 전에 지정 된 시간 (초) 동안 자식 작업을 기다릴 수 있습니다. 최대 대기 시간은 10800 또는 3 시간입니다. 기본값은 2100 초입니다.|
|Internal_AutomationAccountName | Automation 계정의 이름을 지정합니다.|
|Internal_AutoSnooze_ARM_WebhookURI | Vm의 AutoStop 시나리오에 대해 호출 되는 webhook URI입니다.|
|Internal_AutoSnooze_WebhookUri | 클래식 Vm의 AutoStop 시나리오에 대해 호출 되는 webhook URI입니다.|
|Internal_AzureSubscriptionId | Azure 구독 ID입니다.|
|Internal_ResourceGroupName | Automation 계정 리소스 그룹 이름입니다.|

>[!NOTE]
>변수의 `External_WaitTimeForVMRetryInSeconds`경우 기본값은 600에서 2100로 업데이트 되었습니다. 

모든 시나리오에서, `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupNames`및 `External_ExcludeVMNames` 변수는 **AutoStop_CreateAlert_Parent**, **SequencedStartStop_Parent**및 **ScheduledStartStop_Parent** runbook에 대 한 쉼표로 구분 된 vm 목록을 제외 하 고 vm을 대상으로 지정 하는 데 필요 합니다. 즉, 시작 및 중지 작업을 수행 하려면 Vm이 대상 리소스 그룹에 속해야 합니다. 이 논리는 구독 또는 리소스 그룹을 대상으로 지정하며 새로 만든 VM에서 작업이 상속된다는 측면에서 Azure 정책과 유사하게 작동합니다. 이 방법을 사용하면 모든 VM에 대해 별도 일정을 유지하고 시작 및 중지를 전체적으로 관리할 필요가 없습니다.

### <a name="schedules"></a>일정

다음 표에서는 Automation 계정에서 만든 각 기본 일정을 나열합니다.일정을 수정하거나 고유한 사용자 지정 일정을 만들 수 있습니다.기본적으로 **Scheduled_StartVM** 및 **Scheduled_StopVM** 일정을 제외 하 고 모든 일정은 사용 하지 않도록 설정 됩니다.

모든 일정을 사용 하도록 설정 하지 마세요. 이렇게 하면 겹치는 일정 동작이 발생할 수 있습니다. 수행 하려는 최적화를 결정 하 고 적절 하 게 수정 하는 것이 좋습니다. 추가 설명을 보려면 개요 섹션에서 예제 시나리오를 참조하세요.

|일정 이름 | 빈도 | Description|
|--- | --- | ---|
|Schedule_AutoStop_CreateAlert_Parent | 8시간마다 | 는 8 시간 마다 runbook **AutoStop_CreateAlert_Parent** 를 실행 합니다. 그러면, 및 `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupNames` `External_ExcludeVMNames` 변수에서 VM 기반 값이 중지 됩니다. 또는 매개 변수를 `VMList` 사용 하 여 쉼표로 구분 된 vm 목록을 지정할 수 있습니다.|
|Scheduled_StopVM | 사용자 정의, 매일 | 지정 된 **ScheduledStopStart_Parent** 시간에 매일 매개 변수 `Stop` 를 사용 하 여 ScheduledStopStart_Parent runbook을 실행 합니다.변수 자산에 정의 된 규칙을 충족 하는 모든 Vm이 자동으로 중지 됩니다.**예약 된 예약-StartVM**을 사용 하도록 설정 합니다.|
|Scheduled_StartVM | 사용자 정의, 매일 | 지정 된 **ScheduledStopStart_Parent** 시간에 매일 매개 변수 값 `Start` 을 사용 하 여 ScheduledStopStart_Parent runbook을 실행 합니다. 변수 자산에 정의 된 규칙을 충족 하는 모든 Vm이 자동으로 시작 됩니다.관련 일정 **예약-StopVM**을 사용 하도록 설정 합니다.|
|Sequenced-StopVM | 오전 1시(UTC), 매주 금요일 | 지정 된 **Sequenced_StopStop_Parent** 시간에 모든 금요일의 `Stop` 매개 변수 값을 사용 하 여 Sequenced_StopStop_Parent runbook을 실행 합니다.적절한 변수로 **SequenceStop** 태그가 정의되어 있는 모든 VM이 순차적으로(오름차순으로) 중지됩니다. 태그 값과 자산 변수에 대 한 자세한 내용은 [runbook](#runbooks)을 참조 하세요.관련된 일정, **Sequenced-StartVM**을 사용하도록 설정합니다.|
|Sequenced-StartVM | 오후 1시(UTC), 매주 월요일 | 지정 된 **SequencedStopStart_Parent** 시간에 모든 월요일의 `Start` 매개 변수 값을 사용 하 여 SequencedStopStart_Parent runbook을 실행 합니다. 적절한 변수로 **SequenceStart** 태그가 정의되어 있는 모든 VM이 순차적으로(내림차순으로) 시작됩니다. 태그 값 및 변수 자산에 대 한 자세한 내용은 [runbook](#runbooks)을 참조 하세요. 관련된 일정, **Sequenced-StopVM**을 사용하도록 설정합니다.

## <a name="use-of-the-solution-with-classic-vms"></a>클래식 Vm에서 솔루션 사용

클래식 Vm에 대 한 **작업 시간 외 Vm 시작/중지** 솔루션을 사용 하는 경우 자동화는 클라우드 서비스 별로 모든 vm을 순차적으로 처리 합니다. Vm은 서로 다른 클라우드 서비스에서 병렬로 계속 처리 됩니다. 

클래식 Vm을 사용 하는 솔루션의 경우 기본적으로 생성 되지 않는 클래식 실행 계정이 필요 합니다. 클래식 실행 계정을 만드는 방법에 대 한 지침은 [클래식 실행 계정 만들기](automation-create-standalone-account.md#create-a-classic-run-as-account)를 참조 하세요.

클라우드 서비스 당 Vm이 20 개를 초과 하는 경우 몇 가지 권장 사항은 다음과 같습니다.

* 부모 runbook **ScheduledStartStop_Parent** 를 사용 하 여 일정을 여러 개 만들고 일정 당 20 개의 vm을 지정 합니다. 
* 일정 속성에서 `VMList` 매개 변수를 사용 하 여 VM 이름을 쉼표로 구분 된 목록으로 지정 합니다. 

그렇지 않고이 솔루션에 대 한 자동화 작업이 3 시간 이상 실행 되 면 공평 하 게 [공유](automation-runbook-execution.md#fair-share) 한도에 따라 일시적으로 언로드되고 중지 됩니다.

Azure CSP 구독은 Azure Resource Manager 모델만 지원 합니다. Azure Resource Manager 없는 서비스는 프로그램에서 사용할 수 없습니다. **업무 시간 외 Vm 시작/중지 솔루션** 을 실행 하는 경우 클래식 리소스를 관리 하는 cmdlet이 있으므로 오류가 발생할 수 있습니다. CSP에 대해 자세히 알아보려면 [CSP 구독에서 사용 가능한 서비스](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services)를 참조하세요. CSP 구독을 사용 하는 경우 배포 후 [External_EnableClassicVMs](#variables) 변수를 False로 설정 해야 합니다.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="enable-the-solution"></a>솔루션 활성화

솔루션 사용을 시작 하려면 [Vm 시작/중지 솔루션 사용](automation-solution-vm-management-enable.md)의 단계를 따르세요.

## <a name="view-the-solution"></a>솔루션 보기

다음 메커니즘 중 하나를 사용 하 여 솔루션을 사용 하도록 설정한 후에 액세스 합니다.

* Automation 계정에서 **관련 리소스**아래에 있는 **VM 시작/중지** 를 선택 합니다. VM 시작/중지 페이지의 **관리 시작/중지 솔루션**에서 페이지의 오른쪽에 있는 **솔루션 관리** 를 선택 합니다.

* Automation 계정에 연결 된 Log Analytics 작업 영역으로 이동 합니다. 작업 영역을 선택한 후 왼쪽 창에서 **솔루션** 을 선택 합니다. 솔루션 페이지의 목록에서 **시작-중지-VM [작업 영역]** 솔루션을 선택 합니다.  

솔루션을 선택하면 **Start-Stop-VM[workspace]** 솔루션 페이지가 표시됩니다. 여기에서 **Startstopvm** 타일의 정보와 같은 중요 한 세부 정보를 검토할 수 있습니다. Log Analytics 작업 영역에서와 마찬가지로 이 타일에는 성공적으로 시작되고 완료된 솔루션에 대한 Runbook 작업의 수와 그래픽 표시가 나타납니다.

![Automation 업데이트 관리 솔루션 페이지](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)

도넛형 타일을 클릭 하 여 작업 레코드의 추가 분석을 수행할 수 있습니다. 솔루션 대시보드에는 작업 기록과 미리 정의 된 로그 검색 쿼리가 표시 됩니다. 검색 쿼리를 기준으로 검색 하려면 log analytics 고급 포털로 전환 합니다.

## <a name="update-the-solution"></a>솔루션 업데이트

이 솔루션의 이전 버전을 배포한 경우 업데이트 된 릴리스를 배포 하기 전에 계정에서 삭제 합니다. 단계에 따라 [솔루션을 제거한](#remove-the-solution) 후 [솔루션을 배포](automation-solution-vm-management-enable.md)하는 단계를 수행 합니다.

## <a name="remove-the-solution"></a>솔루션 제거

솔루션을 더 이상 사용할 필요가 없는 경우 Automation 계정에서 삭제할 수 있습니다. 솔루션을 삭제하면 Runbook만 제거됩니다. 솔루션을 추가할 때 생성된 일정 또는 변수는 삭제되지 않습니다. 이러한 자산은 다른 runbook에서 사용 하지 않는 경우 수동으로 제거 합니다.

솔루션을 삭제 하려면:

1. Automation 계정에서 **관련 리소스**아래에 있는 **연결 된 작업 영역** 을 선택 합니다.

2. **작업 영역으로 이동을**선택 합니다.

3. **일반**에서 **솔루션** 을 클릭 합니다. 

4. 솔루션 페이지에서 솔루션 **Start-Stop-VM[Workspace]** 를 선택합니다. 

5. **Vmmanagementsolution [Workspace]** 페이지의 메뉴에서 **삭제** 를 선택 합니다.<br><br> ![VM Mgmt 솔루션 삭제](media/automation-solution-vm-management/vm-management-solution-delete.png)

6. **솔루션 삭제** 창에서 솔루션을 삭제할 것인지 확인 합니다.

7. 정보를 확인 하 고 솔루션을 삭제 하는 동안 메뉴에서 선택한 **알림**에서 진행률을 추적할 수 있습니다. 솔루션 제거 프로세스가 시작 되 면 솔루션 페이지로 돌아갑니다.

이 프로세스의 일부로 Automation 계정 및 Log Analytics 작업 영역이 삭제되지는 않습니다. Log Analytics 작업 영역을 유지 하지 않으려면 Azure Portal에서 수동으로 삭제 해야 합니다.

1. **Log Analytics 작업 영역**을 검색 하 고 선택 합니다.

2. Log Analytics 작업 영역 페이지에서 작업 영역을 선택 합니다.

3. 작업 영역 설정 페이지에 있는 메뉴에서 **삭제**를 선택합니다.

4. Azure Automation 계정 [솔루션 구성 요소](#solution-components)를 유지 하지 않으려는 경우에는 각 구성 요소를 수동으로 삭제할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure Vm에 대해 **작업 시간 외 Vm 시작/중지** 솔루션을 [사용 하도록 설정](automation-solution-vm-management-enable.md) 합니다.
