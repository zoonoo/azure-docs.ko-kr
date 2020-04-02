---
title: 작업 시간 외 VM 시작/중지 솔루션
description: 이 VM 관리 솔루션은 일정에 따라 Azure 가상 컴퓨터를 시작하고 중지하고 Azure Monitor 로그에서 사전에 모니터링합니다.
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: cef3176c99cd57ae229b602feb3c825081fcfe3e
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548364"
---
# <a name="startstop-vms-during-off-hours-solution-in-azure-automation"></a>Automation의 작업 시간 외 VM 시작/중지 솔루션

오프 시간 동안 VM 시작/중지 는 사용자 정의 일정에 Azure 가상 컴퓨터를 중지 하 고 Azure Monitor 로그를 통해 통찰력을 제공 하 고 [작업 그룹을](../azure-monitor/platform/action-groups.md)사용 하 여 선택적 전자 메일을 보냅니다. 대부분의 시나리오에서 Azure Resource Manager 및 클래식 VM이 둘 다 지원됩니다. 

클래식 VM에서 이 솔루션을 사용하려면 기본적으로 만들어지지 않은 클래식 RunAs 계정이 필요합니다. 클래식 RunAs 계정 만들기에 대한 지침은 [클래식 실행-로 계정](automation-create-standalone-account.md#classic-run-as-accounts)을 참조하십시오.

> [!NOTE]
> 사용 가능한 Azure 모듈의 최신 버전을 지원하도록 오프시간 솔루션 동안 VM 시작/중지가 업데이트되었습니다.

이 솔루션은 VM 비용을 최적화하려는 사용자에게 분산된 저비용 자동화 옵션을 제공합니다. 이 솔루션을 사용하면 다음과 같은 작업을 수행할 수 있습니다.

- [VM을 시작하고 중지하도록 예약합니다.](automation-solution-vm-management-config.md#schedule)
- [Azure 태그를 사용하여](automation-solution-vm-management-config.md#tags) VM을 오름차순으로 시작하고 중지하도록 예약합니다(클래식 VM에서는 지원되지 않음).
- [낮은 CPU 사용량을](automation-solution-vm-management-config.md#cpuutil)기반으로 하는 자동 정지 VM.

다음은 현재 솔루션의 제한 사항입니다.

- 이 솔루션은 모든 지역의 VM을 관리하지만 Azure Automation 계정과 동일한 구독에서만 사용할 수 있습니다.
- 이 솔루션은 로그 분석 작업 영역, Azure 자동화 계정 및 경고를 지원하는 모든 지역에서 Azure 및 Azure 정부에서 사용할 수 있습니다. Azure 정부 지역은 현재 전자 메일 기능을 지원하지 않습니다.

> [!NOTE]
> 클래식 VM용 솔루션을 사용하는 경우 모든 VM이 클라우드 서비스마다 순차적으로 처리됩니다. 가상 머신은 다른 클라우드 서비스에서 여전히 병렬로 처리됩니다. 클라우드 서비스당 VM이 20개 이상인 경우 부모 Runbook **ScheduledStartStop_Parent** 여러 일정을 만들고 일정당 20개의 VM을 지정하는 것이 좋습니다. 일정 속성에서 **VMList** 매개 변수에 쉼표 로 구분 된 목록, VM 이름으로 지정 합니다. 그렇지 않으면 이 솔루션의 자동화 작업이 3시간 이상 실행되면 공정 [공유](automation-runbook-execution.md#fair-share) 제한에 따라 일시적으로 언로드되거나 중지됩니다.
>
> Azure CSP(Cloud Solution Provider) 구독은 Azure Resource Manager 모델만 지원하므로 Azure Resource Manager 이외의 서비스는 프로그램에서 사용할 수 없습니다. 시작/중지 솔루션을 실행하면 클래식 리소스를 관리하기 위한 cmdlet에 발생하는 오류가 발생할 수 있습니다. CSP에 대해 자세히 알아보려면 [CSP 구독에서 사용 가능한 서비스](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services)를 참조하세요. CSP 구독을 사용하는 경우 배포 후에 [**External_EnableClassicVMs**](#variables) 변수를 **False**로 수정해야 합니다.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>사전 요구 사항

이 솔루션에 대한 Runbook은 [Azure 실행 계정](automation-create-runas-account.md)을 통해 작동합니다. 실행 계정은 자주 만료되거나 변경될 수 있는 암호 대신 인증서 인증을 사용하기 때문에 선호하는 인증 방법입니다.

VM 시작/중지 솔루션에 대해 별도의 자동화 계정을 사용하는 것이 좋습니다. Azure 모듈 버전이 자주 업그레이드되고 해당 매개 변수가 변경될 수 있기 때문입니다. VM 시작/중지 솔루션은 동일한 케이던스에서 업그레이드되지 않으므로 사용하는 cmdlet의 최신 버전에서는 작동하지 않을 수 있습니다. 또한 프로덕션 자동화 계정에서 모듈을 가져오기 전에 테스트 자동화 계정에서 모듈 업데이트를 테스트하는 것이 좋습니다.

### <a name="permissions"></a>사용 권한

사용자가 근무 외 솔루션 중에 시작/중지 VM을 배포해야 하는 특정 권한이 있습니다. 이러한 권한은 미리 만들어진 자동화 계정 및 Log Analytics 작업 영역을 사용하거나 배포 하는 동안 새 작업 영역을 만드는 경우 다릅니다. 구독의 기여자와 Azure Active Directory 테넌트의 전역 관리자인 경우 다음 사용 권한을 구성할 필요가 없습니다. 이러한 권한이 없거나 사용자 지정 역할을 구성해야 하는 경우 아래에 필요한 사용 권한을 참조하십시오.

#### <a name="pre-existing-automation-account-and-log-analytics-workspace"></a>기존 자동화 계정 및 로그 분석 작업 영역

기존 자동화 계정 및 Log Analytics 작업 영역에 근무 외 솔루션 중에 VM 시작/중지를 배포하려면 솔루션을 배포하는 사용자에게 **리소스 그룹에**대한 다음과 같은 권한이 필요합니다. 역할에 대해 자세히 알아보려면 [Azure 리소스에 대한 사용자 지정 역할을](../role-based-access-control/custom-roles.md)참조하세요.

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

#### <a name="new-automation-account-and-a-new-log-analytics-workspace"></a>새 자동화 계정 및 새 로그 분석 작업 영역

운영 외 작업 시간 동안 VM 시작/중지를 새 자동화 계정 및 Log Analytics 작업 영역에 배포하려면 솔루션을 배포하는 사용자에게 는 이전 섹션에 정의된 권한과 다음 권한이 필요합니다.

- 구독의 공동 관리자 - 클래식 VM을 관리하려는 경우에만 클래식 실행 계정으로 만들기만 하면 됩니다. [클래식 RunAs 계정은](automation-create-standalone-account.md#classic-run-as-accounts) 더 이상 기본적으로 생성되지 않습니다.
- Azure Active [디렉터리](../active-directory/users-groups-roles/directory-assign-admin-roles.md) **응용 프로그램 개발자** 역할의 구성원입니다. 계정으로 실행 구성에 대한 자세한 내용은 [계정으로 실행을 구성할 수 있는 권한을](manage-runas-account.md#permissions)참조하세요.
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

이 솔루션에는 미리 구성된 Runbook, 일정 및 Azure Monitor 로그와의 통합이 포함되어 있으므로 비즈니스 요구에 맞게 가상 시스템의 시작 및 종료를 조정할 수 있습니다.

### <a name="runbooks"></a>Runbook

다음 표에서는 이 솔루션이 Automation 계정에 배포한 Runbook을 나열합니다. Runbook 코드를 변경하지 마십시오. 대신, 새 기능에 대한 고유한 Runbook을 작성할 수는 있습니다.

> [!IMPORTANT]
> 해당 이름에 *하위가* 추가된 Runbook을 직접 실행하지 마십시오.

모든 부모 Runbook에는 _WhatIf_ 매개 변수가 포함됩니다. 이 매개 변수가 **True**로 설정되면 _WhatIf_는 _WhatIf_ 매개 변수 없이 실행될 때 Runbook이 수행하는 정확한 동작에 대한 자세한 설명을 지원하며, 올바른 VM이 대상으로 지정되었는지 확인합니다. Runbook은 _WhatIf_ 매개 변수가 **False**로 설정된 경우에만 정의된 작업을 수행합니다.

|Runbook | 매개 변수 | Description|
| --- | --- | ---|
|AutoStop_CreateAlert_Child | VMObject <br> AlertAction <br> WebHookURI | 부모 Runbook에서 호출됩니다. 이 Runbook은 AutoStop 시나리오에서 리소스 기준으로 경고를 만듭니다.|
|AutoStop_CreateAlert_Parent | VMList<br> WhatIf: True 또는 False  | 대상 구독 또는 리소스 그룹에서 VM에 대해 Azure 경고 규칙을 만들거나 업데이트합니다. <br> VMList: 쉼표로 구분된 VM 목록입니다. 예: _vm1, vm2, vm3_.<br> *WhatIf*는 Runbook 논리를 실행하지 않고 유효성을 검사합니다.|
|AutoStop_Disable | none | AutoStop 경고 및 기본 일정을 사용하지 않도록 설정합니다.|
|AutoStop_VM_Child | WebHookData | 부모 Runbook에서 호출됩니다. 경고 규칙은 이 Runbook을 호출하여 클래식 VM을 중지합니다.|
|AutoStop_VM_Child_ARM | WebHookData |부모 Runbook에서 호출됩니다. 경고 규칙은 이 Runbook을 호출하여 VM을 중지합니다.  |
|ScheduledStartStop_Base_Classic | CloudServiceName<br> 작업: 시작 또는 중지<br> VMList  | 이 Runbook은 클라우드 서비스에서 클래식 VM 그룹에서 작업 시작 또는 중지를 수행하는 데 사용됩니다.<br> VMList: 쉼표로 구분된 VM 목록입니다. 예: _vm1, vm2, vm3_. |
|ScheduledStartStop_Child | VMName <br> 작업: 시작 또는 중지 <br> ResourceGroupName | 부모 Runbook에서 호출됩니다. 예약된 중지에서 시작 또는 중지 작업을 실행합니다.|
|ScheduledStartStop_Child_Classic | VMName<br> 작업: 시작 또는 중지<br> ResourceGroupName | 부모 Runbook에서 호출됩니다. 클래식 VM에 대해 예약된 중지에 대해 시작 또는 중지 작업을 실행합니다. |
|ScheduledStartStop_Parent | 작업: 시작 또는 중지 <br>VMList <br> WhatIf: True 또는 False | 이 설정은 구독에 있는 모든 VM에 영향을 줍니다. 이러한 대상이 지정된 리소스 그룹에서만 실행되도록 **External_Start_ResourceGroupNames** 및 **External_Stop_ResourceGroupNames**를 편집합니다. **External_ExcludeVMNames** 변수를 업데이트하여 특정 VM을 제외할 수도 있습니다.<br> VMList: 쉼표로 구분된 VM 목록입니다. 예: _vm1, vm2, vm3_.<br> _WhatIf_는 Runbook 논리를 실행하지 않고 유효성을 검사합니다.|
|SequencedStartStop_Parent | 작업: 시작 또는 중지 <br> WhatIf: True 또는 False<br>VMList| 시작/중지 활동을 시퀀스화하려는 각 VM에서 **시퀀스 시작** 및 **시퀀스 중지라는** 태그를 만듭니다. 이 태그 이름은 대/소문자를 구분합니다. 태그 값은 시작하거나 중지하려는 순서에 해당하는 양의 정수(1, 2, 3)여야 합니다. <br> VMList: 쉼표로 구분된 VM 목록입니다. 예: _vm1, vm2, vm3_. <br> _WhatIf_는 Runbook 논리를 실행하지 않고 유효성을 검사합니다. <br> **참고**: VM은 Azure Automation 변수에 External_Start_ResourceGroupNames, External_Stop_ResourceGroupNames 및 External_ExcludeVMNames으로 정의된 리소스 그룹 내에 있어야 합니다. 작업이 적용되려면 적절한 태그가 있어야 합니다.|

### <a name="variables"></a>variables

다음 표에는 Automation 계정에서 만든 변수가 나열되어 있습니다. **External** 접두사가 붙은 변수만 수정합니다. 변수의 접두사를 **Internal**로 수정하면 원치 않는 결과로 이어집니다.

|변수 | Description|
|---------|------------|
|External_AutoStop_Condition | 경고를 트리거하기 전에 조건을 구성하는 데 필요한 조건부 연산자입니다. 사용 가능한 값은 **GreaterThan**, **GreaterThanOrEqual**, **LessThan** 및 **LessThanOrEqual**입니다.|
|External_AutoStop_Description | CPU %가 임계값을 초과하는 경우 VM을 중지하도록 경고합니다.|
|External_AutoStop_Frequency | 규칙에 대한 평가 빈도입니다. 이 매개 변수는 시간 간격 형식의 입력을 허용합니다. 가능한 값은 5분에서 6시간까지입니다. |
|External_AutoStop_MetricName | Azure 경고 규칙을 구성해야 하는 성능 메트릭의 이름입니다.|
|External_AutoStop_Severity | 0에서 4사이일 수 있는 메트릭 경고의 심각도입니다. |
|External_AutoStop_Threshold | _External_AutoStop_MetricName_ 변수에 지정된 Azure 경고 규칙에 대한 임계값입니다. 백분율 값의 범위는 1에서 100까지입니다.|
|External_AutoStop_TimeAggregationOperator | 조건을 평가하기 위해 선택한 기간 크기에 적용되는 시간 집계 연산자입니다. 사용 가능한 값은 **Average**, **Minimum**, **Maximum**, **Total** 및 **Last**입니다.|
|External_AutoStop_TimeWindow | Azure에서 경고를 트리거하기 위해 선택된 메트릭을 분석하는 기간입니다. 이 매개 변수는 시간 간격 형식의 입력을 허용합니다. 가능한 값은 5분에서 6시간 사이입니다.|
|External_EnableClassicVMs| 솔루션이 클래식 VM을 대상으로 하는지 여부를 지정합니다. 기본값은 True입니다. CSP 구독의 경우 False로 설정해야 합니다. 클래식 VM에는 [클래식 실행 계정이](automation-create-standalone-account.md#classic-run-as-accounts)필요합니다.|
|External_ExcludeVMNames | 공백 없이 쉼표로 이름을 구분하여 제외할 VM 이름을 입력합니다. VM은 140개로 제한됩니다. 이 쉼표로 구분된 목록에 140개 이상의 VM을 추가하는 경우 제외되도록 설정된 VM이 실수로 시작되거나 중지될 수 있습니다.|
|External_Start_ResourceGroupNames | 시작 작업의 대상이 될 하나 이상의 리소스 그룹을 쉼표로 구분해서 지정합니다.|
|External_Stop_ResourceGroupNames | 중지 작업의 대상이 될 하나 이상의 리소스 그룹을 쉼표로 구분해서 지정합니다.|
|External_WaitTimeForVMRetrySeconds |시퀀스 시작/중지 실행책에 대한 VM에서 수행될 작업에 대한 대기 시간(초)입니다.<br> 기본값은 2100초이며 최대 값 10800 또는 3시간으로 구성을 지원합니다.|
|Internal_AutomationAccountName | Automation 계정의 이름을 지정합니다.|
|Internal_AutoSnooze_ARM_WebhookURI | 클래식 VM에 대 한 자동 중지 시나리오에 대 한 호출 된 Webhook URI를 지정 합니다.|
|Internal_AutoSnooze_WebhookUri | AutoStop 시나리오에 대해 호출되는 Webhook URI를 지정합니다.|
|Internal_AzureSubscriptionId | Azure 구독 ID를 지정합니다.|
|Internal_ResourceGroupName | Automation 계정 리소스 그룹 이름을 지정합니다.|

>[!NOTE]
>변수 **External_WaitTimeForVMRetryInSeconds**경우 기본값이 600에서 2100으로 업데이트되었습니다. 이 변수를 사용하면 **Sequenced 시작/중지 시나리오** Runbook에서 다음 작업을 진행하기 전에 지정된 시간(초) 동안 자식 작업을 기다릴 수 있습니다.
>

모든 시나리오에서 **AutoStop_CreateAlert_Parent**, **SequencedStartStop_Parent** 및 **ScheduledStartStop_Parent** Runbook에 대해 쉼표로 구분된 VM 목록을 제공하는 경우를 제외하고 대상 VM을 지정하는 데 **External_Start_ResourceGroupNames**, **External_Stop_ResourceGroupNames** 및 **External_ExcludeVMNames** 변수가 필요합니다. 즉, 시작 및 중지 작업이 진행되려면 VM이 대상 리소스 그룹에 있어야 합니다. 이 논리는 구독 또는 리소스 그룹을 대상으로 지정하며 새로 만든 VM에서 작업이 상속된다는 측면에서 Azure 정책과 유사하게 작동합니다. 이 방법을 사용하면 모든 VM에 대해 별도 일정을 유지하고 시작 및 중지를 전체적으로 관리할 필요가 없습니다.

### <a name="schedules"></a>일정

다음 표에서는 Automation 계정에서 만든 각 기본 일정을 나열합니다.일정을 수정하거나 고유한 사용자 지정 일정을 만들 수 있습니다.기본적으로 모든 일정은 **Scheduled_StartVM** 및 **Scheduled_StopVM**을 제외하고 사용되지 않도록 설정됩니다.

일정 작업이 겹칠 수 있기 때문에 모든 일정을 사용하도록 설정해서는 안 됩니다. 그에 맞게 수행하고 수정할 최적화를 결정하는 것이 가장 좋습니다. 추가 설명을 보려면 개요 섹션에서 예제 시나리오를 참조하세요.

|일정 이름 | 빈도 | Description|
|--- | --- | ---|
|Schedule_AutoStop_CreateAlert_Parent | 8시간마다 | AutoStop_CreateAlert_Parent Runbook을 8시간 간격으로 실행합니다. 그러면 Azure Automation 변수에서 External_Start_ResourceGroupNames, External_Stop_ResourceGroupNames 및 External_ExcludeVMNames에서 VM 기반 값이 중지됩니다. 또는 VMList 매개 변수를 사용하여 쉼표로 구분된 VM 목록을 지정할 수 있습니다.|
|Scheduled_StopVM | 사용자 정의, 매일 | 매일 지정된 시간에 _Stop_ 매개 변수를 사용하여 Scheduled_Parent Runbook이 실행됩니다.자산 변수를 통해 정의된 규칙을 충족하는 모든 VM이 자동으로 중지됩니다.관련된 일정 **Scheduled-StartVM**을 사용하도록 설정합니다.|
|Scheduled_StartVM | 사용자 정의, 매일 | 매일 지정된 시간에 _Start_ 매개 변수를 사용하여 Scheduled_Parent Runbook이 실행됩니다. 적절한 변수로 정의된 규칙을 충족하는 모든 VM이 자동으로 시작됩니다.관련된 일정 **Scheduled-StopVM**을 사용하도록 설정합니다.|
|Sequenced-StopVM | 오전 1시(UTC), 매주 금요일 | 매주 금요일 지정된 시간에 _Stop_ 매개 변수를 사용하여 Sequenced_Parent Runbook이 실행됩니다.적절한 변수로 **SequenceStop** 태그가 정의되어 있는 모든 VM이 순차적으로(오름차순으로) 중지됩니다. 태그 값과 자산 변수에 대한 자세한 내용은 Runbook 섹션을 참조하세요.관련된 일정, **Sequenced-StartVM**을 사용하도록 설정합니다.|
|Sequenced-StartVM | 오후 1시(UTC), 매주 월요일 | 매주 월요일 지정된 시간에 _Start_ 매개 변수를 사용하여 Sequenced_Parent Runbook이 실행됩니다. 적절한 변수로 **SequenceStart** 태그가 정의되어 있는 모든 VM이 순차적으로(내림차순으로) 시작됩니다. 태그 값과 자산 변수에 대한 자세한 내용은 Runbook 섹션을 참조하세요. 관련된 일정, **Sequenced-StopVM**을 사용하도록 설정합니다.|

## <a name="enable-the-solution"></a>솔루션 활성화

솔루션 사용을 시작하려면 [VM 시작/중지 사용 솔루션의](automation-solution-vm-management-enable.md)단계를 수행합니다.

## <a name="viewing-the-solution"></a>솔루션 보기

다음 방법 중 하나에서 솔루션을 사용하도록 설정한 후 솔루션에 액세스할 수 있습니다.

* 자동화 계정에서 **관련 리소스에서** **VM 시작/중지를** 선택합니다. **VM 시작/중지** 페이지에서 **VM 솔루션 시작/중지**관리 섹션 아래 페이지의 오른쪽에서 **솔루션 관리를** 선택합니다.

* 자동화 계정에 연결된 Log Analytics 작업 영역으로 이동한 후 작업 영역을 선택한 후 왼쪽 창에서 **솔루션을** 선택합니다. **솔루션** 페이지에서 목록에서 **시작-중지-VM[작업 공간]** 솔루션을 선택합니다.  

솔루션을 선택하면 **Start-Stop-VM[workspace]** 솔루션 페이지가 표시됩니다. 여기에서 **StartStopVM** 타일과 같은 중요한 세부 정보를 검토할 수 있습니다. Log Analytics 작업 영역에서와 마찬가지로 이 타일에는 성공적으로 시작되고 완료된 솔루션에 대한 Runbook 작업의 수와 그래픽 표시가 나타납니다.

![Automation 업데이트 관리 솔루션 페이지](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)

여기에서 도넛형 타일을 클릭하면 작업 레코드의 추가 분석을 수행할 수 있습니다. 솔루션 대시보드에는 작업 기록 및 미리 정의된 로그 검색 쿼리가 표시됩니다. 로그 분석 고급 포털로 전환하여 검색 쿼리를 기반으로 검색합니다.

## <a name="update-the-solution"></a>솔루션 업데이트

이 솔루션의 이전 버전을 배포한 경우 업데이트된 릴리스를 배포하기 전에 먼저 계정에서 삭제해야 합니다. 단계에 따라 [솔루션을 제거한](#remove-the-solution) 다음 단계를 수행하여 [솔루션을 배포합니다.](automation-solution-vm-management-enable.md)

## <a name="remove-the-solution"></a>솔루션 제거

솔루션을 더 이상 사용할 필요가 없으면 Automation 계정에서 삭제할 수 있습니다. 솔루션을 삭제하면 Runbook만 제거됩니다. 솔루션을 추가할 때 생성된 일정 또는 변수는 삭제되지 않습니다. 다른 Runbook에서 사용하지 않는 경우 해당 자산을 수동으로 삭제해야 합니다.

솔루션을 삭제하려면 다음 단계를 수행합니다.

1. **관련 리소스에서**자동화 계정에서 **연결된 작업 영역을**선택합니다.

2. **작업 영역으로 이동을**선택합니다.

3. **일반**에서 **솔루션을**선택합니다. 

4. **솔루션** 페이지에서 솔루션 **Start-Stop-VM[Workspace]** 를 선택합니다. **VMManagementSolution[Workspace]** 페이지의 메뉴에서 **삭제**를 클릭합니다.<br><br> ![VM Mgmt 솔루션 삭제](media/automation-solution-vm-management/vm-management-solution-delete.png)

5. **솔루션 삭제** 창에서 솔루션을 삭제할 것임을 확인합니다.

6. 정보가 확인되고 솔루션이 삭제되는 동안 메뉴의 **알림**에서 진행 상황을 추적할 수 있습니다. 솔루션 제거 프로세스가 시작되면 **솔루션** 페이지로 돌아갑니다.

이 프로세스의 일부로 Automation 계정 및 Log Analytics 작업 영역이 삭제되지는 않습니다. Log Analytics 작업 영역을 유지하지 않으려는 경우 수동으로 삭제해야 합니다. Azure Portal에서도 이 작업을 수행할 수 있습니다.

1. Azure 포털에서 **로그 분석 작업 영역을**검색하고 선택합니다.

2. **로그 분석 작업 영역** 페이지에서 작업 영역을 선택합니다.

3. 작업 영역 설정 페이지에 있는 메뉴에서 **삭제**를 선택합니다.

Azure Automation 계정 구성 요소를 유지하지 않으려면 각각을 수동으로 삭제할 수 있습니다. 솔루션에서 만든 Runbook, 변수 및 일정 목록은 [솔루션 구성 요소](#solution-components)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Azure VM에 대한 오프시간 솔루션 중에 시작/중지를 [활성화합니다.](automation-solution-vm-management-enable.md)
