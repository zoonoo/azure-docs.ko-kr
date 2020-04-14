---
title: 근무 외 솔루션 중 VM 시작/중지
description: 이 VM 관리 솔루션은 일정에 따라 Azure 가상 컴퓨터를 시작하고 중지하고 Azure Monitor 로그에서 사전에 모니터링합니다.
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: fbcd4ea174d4b6a2a45495c32f178ed1bd01bbe0
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261349"
---
# <a name="startstop-vms-during-off-hours-solution-in-azure-automation"></a>Azure 자동화에서 근무 외 솔루션 중에 VM 시작/중지

**운영 외 솔루션 중에 VM 시작/중지가** Azure 가상 컴퓨터를 시작하거나 중지합니다. 사용자 정의 일정에 따라 컴퓨터를 시작하거나 중지하고, Azure Monitor 로그를 통해 통찰력을 제공하고, [작업 그룹을](../azure-monitor/platform/action-groups.md)사용하여 선택적 전자 메일을 보냅니다. 이 솔루션은 대부분의 시나리오에서 Azure 리소스 관리자와 클래식 VM을 모두 지원합니다. 

이 솔루션은 VM 비용을 최적화하려는 사용자에게 분산된 저비용 자동화 옵션을 제공합니다. 이 솔루션을 사용하면 다음과 같은 작업을 수행할 수 있습니다.

- [VM을 시작하고 중지하도록 예약합니다.](automation-solution-vm-management-config.md#schedule)
- [Azure 태그를 사용하여](automation-solution-vm-management-config.md#tags) VM을 오름차순으로 시작하고 중지하도록 예약합니다(클래식 VM에서는 지원되지 않음).
- [낮은 CPU 사용량을](automation-solution-vm-management-config.md#cpuutil)기반으로 하는 자동 정지 VM.

> [!NOTE]
> 사용 가능한 Azure 모듈의 최신 버전을 지원하도록 오프시간 솔루션 **동안 VM 시작/중지가** 업데이트되었습니다.

다음은 현재 솔루션의 제한 사항입니다.

- 모든 리전에서 VM을 관리하지만 Azure Automation 계정과 동일한 구독에서만 사용할 수 있습니다.
- 로그 분석 작업 영역, Azure 자동화 계정 및 경고를 지원하는 모든 지역에서 Azure 및 Azure 정부에서 사용할 수 있습니다. Azure 정부 지역은 현재 전자 메일 기능을 지원하지 않습니다.

## <a name="solution-prerequisites"></a>솔루션 전제 조건

이 솔루션에 대한 Runbook은 [Azure 실행 계정](automation-create-runas-account.md)을 통해 작동합니다. Run As 계정은 만료되거나 자주 변경될 수 있는 암호 대신 인증서 인증을 사용하기 때문에 기본 인증 방법입니다.

영업외 솔루션 **중에 VM 시작/중지에** 대해 별도의 자동화 계정을 사용하는 것이 좋습니다. Azure 모듈 버전은 자주 업그레이드되며 해당 매개 변수가 변경될 수 있습니다. 솔루션은 동일한 케이던스에서 업그레이드되지 않으며 사용하는 cmdlet의 최신 버전에서는 작동하지 않을 수 있습니다. 프로덕션 자동화 계정에서 모듈 업데이트를 가져오기 전에 테스트 자동화 계정에서 모듈 업데이트를 테스트하는 것이 좋습니다.

## <a name="solution-permissions"></a>솔루션 권한

근무 외 솔루션 **중에 시작/중지 VM을** 배포할 수 있는 특정 권한이 있어야 합니다. 솔루션이 배포 하는 동안 새 계정 및 작업 영역을 만드는 경우 솔루션에 미리 만들어진 된 자동화 계정 및 Log Analytics 작업 영역을 사용 하는 경우 사용 권한이 다릅니다. 

구독의 기여자와 Azure Active Directory 테넌트의 전역 관리자인 경우 사용 권한을 구성할 필요가 없습니다. 이러한 권한이 없거나 사용자 지정 역할을 구성해야 하는 경우 아래에 설명된 사용 권한이 있는지 확인합니다.

### <a name="permissions-for-pre-existing-automation-account-and-log-analytics-workspace"></a>기존 자동화 계정 및 로그 분석 작업 영역에 대한 사용 권한

기존 자동화 계정 및 Log Analytics 작업 영역에 근무 외 솔루션 **중에 VM 시작/중지를** 배포하려면 솔루션을 배포하는 사용자에게 리소스 그룹 범위에 대한 다음과 같은 권한이 필요합니다. 역할에 대해 자세히 알아보려면 [Azure 리소스에 대한 사용자 지정 역할을](../role-based-access-control/custom-roles.md)참조하세요.

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
| Microsoft.자동화/자동화계정/작업일정/쓰기 | 리소스 그룹 |
| Microsoft.Automation/automationAccounts/jobs/write | 리소스 그룹 |
| Microsoft.Automation/automationAccounts/jobs/read | 리소스 그룹 |
| Microsoft.OperationsManagement/solutions/write | 리소스 그룹 |
| 마이크로소프트.오퍼리인사이트/작업 영역/* | 리소스 그룹 |
| 마이크로소프트.인사이트/진단설정/쓰기 | 리소스 그룹 |
| Microsoft.Insights/ActionGroups/Write | 리소스 그룹 |
| 마이크로소프트.인사이트/작업 그룹/읽기 | 리소스 그룹 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹 |
| Microsoft.Resources/deployments/* | 리소스 그룹 |

### <a name="permissions-for-new-automation-account-and-new-log-analytics-workspace"></a>새 자동화 계정 및 새 로그 분석 작업 영역에 대한 사용 권한

근무 외 솔루션 **중에 VM 시작/중지를** 새 자동화 계정 및 Log Analytics 작업 영역에 배포할 수 있습니다. 이 경우 솔루션을 배포하는 사용자에게는 이 섹션에 정의된 권한과 이전 섹션에 정의된 사용 권한이 필요합니다. 

솔루션을 배포하는 사용자에게는 다음 역할이 필요합니다.

- 구독의 공동 관리자입니다. 이 역할은 클래식 VM을 관리하려는 경우 클래식 실행 계정을 만드는 데 필요합니다. [클래식 실행 계정은](automation-create-standalone-account.md#create-a-classic-run-as-account) 더 이상 기본적으로 만들어지지 않습니다.
- [Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md) 응용 프로그램 개발자 역할의 구성원 자격입니다. 계정으로 실행 구성에 대한 자세한 내용은 [계정으로 실행을 구성할 수 있는 권한을](manage-runas-account.md#permissions)참조하세요.
- 구독 또는 다음 권한에 대한 기고자.

| 사용 권한 |범위|
| --- | --- |
| 마이크로소프트.권한 부여/운영/읽기 | Subscription|
| Microsoft.Authorization/permissions/read |Subscription|
| Microsoft.Authorization/roleAssignments/read | Subscription |
| Microsoft.Authorization/roleAssignments/write | Subscription |
| Microsoft.Authorization/roleAssignments/delete | Subscription |
| Microsoft.Automation/automationAccounts/connections/read | 리소스 그룹 |
| Microsoft.Automation/automationAccounts/certificates/read | 리소스 그룹 |
| Microsoft.Automation/automationAccounts/write | 리소스 그룹 |
| Microsoft.OperationalInsights/workspaces/write | 리소스 그룹 |

## <a name="solution-components"></a>솔루션 구성 요소

**근무 외 솔루션 중 VM 시작/중지에는** 미리 구성된 런북, 일정 및 Azure Monitor 로그와의 통합이 포함됩니다. 이러한 요소를 사용하여 비즈니스 요구에 맞게 VM의 시작 및 종료를 조정할 수 있습니다.

### <a name="runbooks"></a>Runbook

다음 표에는 솔루션이 자동화 계정에 배포하는 Runbook이 나열되어 있습니다. Runbook 코드를 변경하지 마십시오. 대신, 새 기능에 대한 고유한 Runbook을 작성할 수는 있습니다.

> [!IMPORTANT]
> 해당 이름에 **하위가** 추가된 Runbook을 직접 실행하지 마십시오.

모든 상위 Runbook에는 매개 변수가 포함됩니다. `WhatIf` True로 설정하면 매개 변수없이 실행할 때 Runbook에서 걸리는 정확한 동작을 자세히 설명하고 올바른 VM이 대상으로 있는지 확인합니다. Runbook은 `WhatIf` 매개 변수가 False로 설정된 경우에만 정의된 작업을 수행합니다.

|Runbook | 매개 변수 | Description|
| --- | --- | ---|
|AutoStop_CreateAlert_Child | VMObject <br> AlertAction <br> WebHookURI | 부모 Runbook에서 호출됩니다. 이 Runbook은 자동 중지 시나리오에 대한 리소스별로 경고를 만듭니다.|
|AutoStop_CreateAlert_Parent | VMList<br> WhatIf: True 또는 False  | 대상 구독 또는 리소스 그룹에서 VM에 대해 Azure 경고 규칙을 만들거나 업데이트합니다. <br> `VMList`은 쉼표로 구분된 VM 목록입니다. `vm1, vm2, vm3`)을 입력합니다.<br> `WhatIf`실행하지 않고도 Runbook 논리의 유효성 검사를 수행할 수 있습니다.|
|AutoStop_Disable | 없음 | 자동 중지 경고 및 기본 일정을 비활성화합니다.|
|AutoStop_VM_Child | WebHookData | 부모 Runbook에서 호출됩니다. 경고 규칙은 이 Runbook을 호출하여 클래식 VM을 중지합니다.|
|AutoStop_VM_Child_ARM | WebHookData |부모 Runbook에서 호출됩니다. 경고 규칙은 이 Runbook을 호출하여 VM을 중지합니다.  |
|ScheduledStartStop_Base_Classic | CloudServiceName<br> 작업: 시작 또는 중지<br> VMList  | 클라우드 서비스에서 클래식 VM 그룹에서 작업 시작 또는 중지를 수행합니다. |
|ScheduledStartStop_Child | VMName <br> 작업: 시작 또는 중지 <br> ResourceGroupName | 부모 Runbook에서 호출됩니다. 예약된 중지에서 시작 또는 중지 작업을 실행합니다.|
|ScheduledStartStop_Child_Classic | VMName<br> 작업: 시작 또는 중지<br> ResourceGroupName | 부모 Runbook에서 호출됩니다. 클래식 VM에 대해 예약된 중지에 대해 시작 또는 중지 작업을 실행합니다. |
|ScheduledStartStop_Parent | 작업: 시작 또는 중지 <br>VMList <br> WhatIf: True 또는 False | 구독의 모든 VM을 시작하거나 중지합니다. 변수를 `External_Start_ResourceGroupNames` 편집하고 `External_Stop_ResourceGroupNames` 이러한 대상 리소스 그룹에서만 실행합니다. 변수를 업데이트하여 특정 VM을 `External_ExcludeVMNames` 제외할 수도 있습니다.|
|SequencedStartStop_Parent | 작업: 시작 또는 중지 <br> WhatIf: True 또는 False<br>VMList| 시작/중지 활동을 시퀀스할 각 VM에 **시퀀스 시작** 및 **시퀀스 중지라는** 태그를 만듭니다. 이 태그 이름은 대/소문자를 구분합니다. 태그 값은 시작하거나 중지하려는 순서에 해당하는 양의 정수(1, 2, 3)여야 합니다. <br>**참고**: VM은 `External_Start_ResourceGroupNames`에 `External_Stop_ResourceGroupNames` `External_ExcludeVMNames` 정의된 리소스 그룹 내에 있어야 합니다. 작업이 적용되려면 적절한 태그가 있어야 합니다.|

### <a name="variables"></a>variables

다음 표에는 Automation 계정에서 만든 변수가 나열되어 있습니다. `External`에 접두번으로 고정된 변수만 수정합니다. 접두사로 고정된 `Internal` 변수를 수정하면 바람직하지 않은 효과가 발생합니다.

> [!NOTE]
> VM 이름 및 리소스 그룹에 대한 제한은 대체로 가변 크기의 결과입니다.

|변수 | Description|
|---------|------------|
|External_AutoStop_Condition | 경고를 트리거하기 전에 조건을 구성하는 데 필요한 조건부 연산자입니다. 허용되는 `GreaterThan`값은 `LessThan`" `LessThanOrEqual` `GreaterThanOrEqual`및|
|External_AutoStop_Description | CPU %가 임계값을 초과하는 경우 VM을 중지하도록 경고합니다.|
|External_AutoStop_Frequency | 규칙에 대한 평가 빈도입니다. 이 매개 변수는 시간 간격 형식의 입력을 허용합니다. 가능한 값은 5분에서 6시간 사이입니다. |
|External_AutoStop_MetricName | Azure 경고 규칙을 구성해야 하는 성능 메트릭의 이름입니다.|
|External_AutoStop_Severity | 0에서 4사이일 수 있는 메트릭 경고의 심각도입니다. |
|External_AutoStop_Threshold | 변수에 `External_AutoStop_MetricName`지정된 Azure Alert 규칙에 대한 임계값입니다. 백분율 값범위는 1에서 100사이입니다.|
|External_AutoStop_TimeAggregationOperator | 조건을 평가하기 위해 선택한 창 크기에 적용된 시간 집계 연산자입니다. 허용되는 `Average`값은 `Maximum` `Total`" `Minimum` `Last`및|
|External_AutoStop_TimeWindow | Azure에서 경고를 트리거하기 위해 선택한 메트릭을 분석하는 기간의 크기입니다. 이 매개 변수는 시간 간격 형식의 입력을 허용합니다. 가능한 값은 5분에서 6시간 사이입니다.|
|External_EnableClassicVMs| 클래식 VM이 솔루션의 대상이 되는지 지정하는 값입니다. 기본값은 True입니다. 이 변수를 CSP(Azure 클라우드 솔루션 공급자) 구독에 대해 False로 설정합니다. 클래식 VM은 [계정으로 클래식 실행이](automation-create-standalone-account.md#create-a-classic-run-as-account)필요합니다.|
|External_ExcludeVMNames | 140개의 VM으로 제한되는 제외할 VM 이름의 쉼표 로 구분된 목록입니다. 목록에 140개 이상의 VM을 추가하는 경우 제외되도록 설정된 VM이 실수로 시작되거나 중지될 수 있습니다.|
|External_Start_ResourceGroupNames | 시작 작업의 대상이 되는 하나 이상의 리소스 그룹의 쉼표 로 구분된 목록입니다.|
|External_Stop_ResourceGroupNames | 중지 작업을 대상으로 하는 하나 이상의 리소스 그룹의 쉼표 구분 목록입니다.|
|External_WaitTimeForVMRetrySeconds |**SequencedStartStop_Parent** 실행책에 대한 VM에서 수행할 작업에 대한 대기 시간(초)입니다. 이 변수를 사용하면 Runbook에서 다음 작업을 진행하기 전에 지정된 시간 동안 자식 작업을 대기할 수 있습니다. 최대 대기 시간은 10800 또는 3시간입니다. 기본값은 2100초입니다.|
|Internal_AutomationAccountName | Automation 계정의 이름을 지정합니다.|
|Internal_AutoSnooze_ARM_WebhookURI | 웹후크 URI에서 VM에 대한 자동 중지 시나리오를 호출했습니다.|
|Internal_AutoSnooze_WebhookUri | 웹후크 URI에서 클래식 VM에 대한 자동 중지 시나리오를 호출했습니다.|
|Internal_AzureSubscriptionId | Azure 구독 ID입니다.|
|Internal_ResourceGroupName | 자동화 계정 리소스 그룹 이름입니다.|

>[!NOTE]
>변수의 `External_WaitTimeForVMRetryInSeconds`경우 기본값이 600에서 2100으로 업데이트되었습니다. 

모든 시나리오에서 변수는 `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupNames` `External_ExcludeVMNames` **AutoStop_CreateAlert_Parent,** **SequencedStartStop_Parent**및 **ScheduledStartStop_Parent** 런북에 대한 쉼표로 구분된 VM 목록을 제외하고 VM을 대상으로 하는 데 필요합니다. 즉, VM이 시작 및 중지 작업이 발생하려면 대상 리소스 그룹에 속해야 합니다. 이 논리는 구독 또는 리소스 그룹을 대상으로 지정하며 새로 만든 VM에서 작업이 상속된다는 측면에서 Azure 정책과 유사하게 작동합니다. 이 방법을 사용하면 모든 VM에 대해 별도 일정을 유지하고 시작 및 중지를 전체적으로 관리할 필요가 없습니다.

### <a name="schedules"></a>일정

다음 표에서는 Automation 계정에서 만든 각 기본 일정을 나열합니다.일정을 수정하거나 고유한 사용자 지정 일정을 만들 수 있습니다.기본적으로 **Scheduled_StartVM** 및 **Scheduled_StopVM** 일정을 제외한 모든 일정은 비활성화됩니다.

이렇게 하면 겹치는 일정 작업이 발생할 수 있으므로 모든 일정을 사용하도록 설정하지 마십시오. 수행할 최적화를 결정하고 그에 따라 수정하는 것이 가장 좋습니다. 추가 설명을 보려면 개요 섹션에서 예제 시나리오를 참조하세요.

|일정 이름 | 빈도 | Description|
|--- | --- | ---|
|Schedule_AutoStop_CreateAlert_Parent | 8시간마다 | **8시간마다 AutoStop_CreateAlert_Parent** 런북을 실행하여 VM 기반 값의 `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupNames`. `External_ExcludeVMNames` 및 변수를 중지합니다. 또는 매개 변수를 사용하여 쉼표로 구분된 VM `VMList` 목록을 지정할 수 있습니다.|
|Scheduled_StopVM | 사용자 정의, 매일 | 지정된 **ScheduledStopStart_Parent** 시간에 매일 매개 `Stop` 변수로 ScheduledStopStart_Parent Runbook을 실행합니다.가변 자산에 의해 정의된 규칙을 충족하는 모든 VM을 자동으로 중지합니다.관련 일정 **예약-StartVM을**사용하도록 설정합니다.|
|Scheduled_StartVM | 사용자 정의, 매일 | 지정된 **ScheduledStopStart_Parent** 시간에 매일 매개 `Start` 변수 값으로 ScheduledStopStart_Parent Runbook을 실행합니다. 가변 자산에 의해 정의된 규칙을 충족하는 모든 VM이 자동으로 시작됩니다.관련 일정 **예약-StopVM을**사용하도록 설정합니다.|
|Sequenced-StopVM | 오전 1시(UTC), 매주 금요일 | 지정된 시간에 매주 금요일의 `Stop` 매개 변수 값으로 Sequenced_Parent Runbook을 실행합니다.적절한 변수로 **SequenceStop** 태그가 정의되어 있는 모든 VM이 순차적으로(오름차순으로) 중지됩니다. 태그 값과 자산 변수에 대한 자세한 내용은 Runbook 섹션을 참조하세요.관련된 일정, **Sequenced-StartVM**을 사용하도록 설정합니다.|
|Sequenced-StartVM | 오후 1시(UTC), 매주 월요일 | 지정된 **SequencedStopStart_Parent** 시간에 매주 월요일의 `Start` 매개 변수 값으로 SequencedStopStart_Parent Runbook을 실행합니다. 적절한 변수로 **SequenceStart** 태그가 정의되어 있는 모든 VM이 순차적으로(내림차순으로) 시작됩니다. 태그 값 및 가변 자산에 대한 자세한 내용은 [Runbooks](#runbooks)를 참조하십시오. 관련된 일정, **Sequenced-StopVM**을 사용하도록 설정합니다.

## <a name="use-of-the-solution-with-classic-vms"></a>클래식 VM을 사용한 솔루션 사용

클래식 **VM의 오프시간 솔루션 중에 시작/중지 VM을** 사용하는 경우 자동화는 클라우드 서비스당 모든 VM을 순차적으로 처리합니다. VM은 여전히 서로 다른 클라우드 서비스에서 병렬로 처리됩니다. 

클래식 VM에서 솔루션을 사용하려면 기본적으로 만들어지지 않은 클래식 실행 계정이 필요합니다. 클래식 실행 계정을 만드는 방법에 대한 지침은 [[클래식 실행 계정으로 만들기]를](automation-create-standalone-account.md#create-a-classic-run-as-account)참조하십시오.

클라우드 서비스당 VM이 20개 이상인 경우 몇 가지 권장 사항은 다음과 같습니다.

* 상위 runbook **ScheduledStartStop_Parent** 일정에 따라 20개의 VM을 지정하여 여러 일정을 만듭니다. 
* 일정 속성에서 매개 `VMList` 변수를 사용하여 VM 이름을 쉼표로 구분된 목록으로 지정합니다. 

그렇지 않으면 이 솔루션의 자동화 작업이 3시간 이상 실행되면 [공정 한도에](automation-runbook-execution.md#fair-share) 따라 일시적으로 언로드되거나 중지됩니다.

Azure CSP 구독은 Azure 리소스 관리자 모델만 지원합니다. 비 Azure 리소스 관리자 서비스는 프로그램에서 사용할 수 없습니다. **영업외 솔루션 중에 VM 시작/중지가** 실행되면 cmdlet이 있어 클래식 리소스를 관리할 수 있으므로 오류가 발생할 수 있습니다. CSP에 대해 자세히 알아보려면 [CSP 구독에서 사용 가능한 서비스](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services)를 참조하세요. CSP 구독을 사용하는 경우 배포 후 [External_EnableClassicVMs](#variables) 변수를 False로 설정해야 합니다.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="enable-the-solution"></a>솔루션 활성화

솔루션 사용을 시작하려면 [VM 시작/중지 솔루션 사용](automation-solution-vm-management-enable.md)의 단계를 따릅니다.

## <a name="view-the-solution"></a>솔루션 보기

다음 메커니즘 중 하나를 사용하여 솔루션을 활성화한 후 솔루션에 액세스합니다.

* 자동화 계정에서 **관련 리소스에서** **VM 시작/중지를** 선택합니다. VM 시작/중지 페이지에서 **VM 솔루션 시작/중지 관리**에서 페이지 오른쪽에서 **솔루션 관리를** 선택합니다.

* 자동화 계정에 연결된 로그 분석 작업 영역으로 이동합니다. 작업 영역을 선택한 후 왼쪽 창에서 **솔루션을** 선택합니다. 솔루션 페이지에서 목록에서 **시작-중지-VM[작업 공간]** 솔루션을 선택합니다.  

솔루션을 선택하면 **Start-Stop-VM[workspace]** 솔루션 페이지가 표시됩니다. 여기에서 **StartStopVM** 타일의 정보와 같은 중요한 세부 정보를 검토할 수 있습니다. Log Analytics 작업 영역에서와 마찬가지로 이 타일에는 성공적으로 시작되고 완료된 솔루션에 대한 Runbook 작업의 수와 그래픽 표시가 나타납니다.

![Automation 업데이트 관리 솔루션 페이지](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)

도넛 타일을 클릭하여 작업 레코드에 대한 추가 분석을 수행할 수 있습니다. 솔루션 대시보드에는 작업 기록 및 미리 정의된 로그 검색 쿼리가 표시됩니다. 로그 분석 고급 포털로 전환하여 검색 쿼리를 기반으로 검색합니다.

## <a name="update-the-solution"></a>솔루션 업데이트

이 솔루션의 이전 버전을 배포한 경우 업데이트된 릴리스를 배포하기 전에 계정에서 삭제합니다. 단계에 따라 [솔루션을 제거한](#remove-the-solution) 다음 단계를 수행하여 [솔루션을 배포합니다.](automation-solution-vm-management-enable.md)

## <a name="remove-the-solution"></a>솔루션 제거

솔루션을 더 이상 사용할 필요가 없는 경우 자동화 계정에서 삭제할 수 있습니다. 솔루션을 삭제하면 Runbook만 제거됩니다. 솔루션을 추가할 때 생성된 일정 또는 변수는 삭제되지 않습니다. 다른 Runbook과 함께 사용하지 않는 경우 이러한 자산을 수동으로 제거합니다.

솔루션을 삭제하려면 다음을 수행하십시오.

1. 자동화 계정에서 **관련 리소스**아래에 연결된 **작업 영역을** 선택합니다.

2. **작업 영역으로 이동을**선택합니다.

3. **일반**에서 **솔루션을** 클릭합니다. 

4. 솔루션 페이지에서 솔루션 **Start-Stop-VM[Workspace]** 를 선택합니다. 

5. **VM관리솔루션[작업 공간]** 페이지에서 메뉴에서 **삭제를** 선택합니다.<br><br> ![VM Mgmt 솔루션 삭제](media/automation-solution-vm-management/vm-management-solution-delete.png)

6. 솔루션 **삭제** 창에서 솔루션을 삭제할지 확인합니다.

7. 정보가 확인되고 솔루션이 삭제되는 동안 메뉴에서 선택한 **알림**에서 진행 상황을 추적할 수 있습니다. 솔루션을 제거하는 프로세스가 시작된 후 솔루션 페이지로 돌아갑니다.

이 프로세스의 일부로 Automation 계정 및 Log Analytics 작업 영역이 삭제되지는 않습니다. 로그 분석 작업 영역을 유지하지 않으려면 Azure 포털에서 수동으로 삭제해야 합니다.

1. **로그 분석 작업 영역을**검색하고 선택합니다.

2. **로그 분석 작업 영역** 페이지에서 작업 영역을 선택합니다.

3. 작업 영역 설정 페이지에 있는 메뉴에서 **삭제**를 선택합니다.

4. Azure 자동화 계정 구성 요소를 유지하지 않으려면 각각을 수동으로 삭제할 수 있습니다. [솔루션 구성 요소를](#solution-components)참조하십시오.

## <a name="next-steps"></a>다음 단계

Azure **VM에 대한 오프시간 솔루션 중에 VM 시작/중지를** [활성화합니다.](automation-solution-vm-management-enable.md)
