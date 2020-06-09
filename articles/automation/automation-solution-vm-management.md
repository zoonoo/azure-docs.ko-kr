---
title: Azure Automation 작업 시간 외 VM 시작/중지 개요
description: 이 문서에서는 일정에 따라 VM을 시작 또는 중시하고 Azure Monitor 로그에서 선제적으로 모니터링하는 작업 시간 외 VM 시작/중지 기능에 대해 설명합니다.
services: automation
ms.subservice: process-automation
ms.date: 04/28/2020
ms.topic: conceptual
ms.openlocfilehash: e2f23f4045f0326ffea14ddeb4d588261872188f
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83743699"
---
# <a name="startstop-vms-during-off-hours-overview"></a>작업 시간 외 VM 시작/중지 개요

작업 시간 외 VM 시작/중지 기능은 사용하도록 설정된 Azure VM을 시작하거나 중지합니다. 사용자 정의 일정에 따라 머신을 시작 또는 중지하고, Azure Monitor 로그를 통해 인사이트를 제공하고, [작업 그룹](../azure-monitor/platform/action-groups.md)을 사용하여 선택적 메일을 전송합니다. 이 기능은 대부분의 시나리오에서 Azure Resource Manager 및 클래식 VM에서 사용하도록 설정할 수 있습니다. 

이 기능은 [Start-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/start-azurermvm?view=azurermps-6.13.0) cmdlet을 사용하여 VM을 시작하고 [Stop-AzureRmVM](https://docs.microsoft.com/powershell/module/AzureRM.Compute/Stop-AzureRmVM?view=azurermps-6.13.0)을 사용하여 VM을 중지합니다.

> [!NOTE]
> 작업 시간 외 VM 시작/중지는 사용 가능한 최신 버전의 Azure 모듈을 지원하도록 업데이트되었습니다. Microsoft는 AzureRM 모듈을 Az 모듈로 마이그레이션했기 때문에 Marketplace에서 받을 수 있는 이 기능의 업데이트된 버전에서는 AzureRM 모듈을 지원하지 않습니다.

이 기능은 VM 비용을 최적화하려는 사용자들에게 분산된 저비용 자동화 옵션을 제공합니다. 이 기능을 사용하여 다음과 같은 작업을 수행할 수 있습니다.

- [VM 시작/중지 예약](automation-solution-vm-management-config.md#schedule)
- [Azure Tags를 사용하여](automation-solution-vm-management-config.md#tags) VM이 오름차순으로 시작/중지되도록 예약 이 작업은 클래식 VM에서는 지원되지 않습니다.
- [낮은 CPU 사용량](automation-solution-vm-management-config.md#cpuutil)을 기준으로 VM 자동 중지

현재 기능의 제한 사항은 다음과 같습니다.

- 모든 지역의 VM을 관리하지만 Azure Automation 계정과 동일한 구독에서만 사용할 수 있습니다.
- Log Analytics 작업 영역, Azure Automation 계정 및 경고를 지원하는 Azure 및 Azure Government의 모든 지역에서 사용할 수 있습니다. Azure Government 지역에서는 현재 메일 기능을 지원하지 않습니다.

## <a name="prerequisites"></a>사전 요구 사항

작업 시간 외 VM 시작/중지 기능의 Runbook은 [Azure 실행 계정](automation-create-runas-account.md)을 통해 작동합니다. 실행 계정은 자주 만료되거나 변경될 수 있는 암호 대신 인증서 인증을 사용하기 때문에 선호되는 인증 방법입니다.

작업 시간 외 VM 시작/중지 기능이 사용하도록 설정된 VM을 사용할 때는 별도의 Automation 자동화 계정을 사용하는 것이 좋습니다. Azure 모듈 버전은 자주 업그레이드되고 매개 변수가 변경될 수 있는데, 이 기능은 다른 주기로 업그레이드되므로 이 기능에서 사용하는 cmdlet의 최신 버전에서 작동하지 않을 수 있습니다. 모듈 업데이트는 프로덕션 Automation 계정으로 가져오기 전에 테스트 Automation 계정에서 테스트해 보는 것이 좋습니다.

## <a name="permissions"></a>사용 권한

VM에서 작업 시간 외 VM 시작/중지 기능을 사용하도록 설정하려면 특정 권한이 있어야 합니다. 필요한 권한은 이 기능에서 미리 생성된 Automation 계정과 Log Analytics 작업 영역을 사용하는지 아니면 새 계정과 작업 영역을 사용하는지에 따라 달라집니다. 

구독의 기여자이고 Azure AD(Active Directory) 테넌트의 전역 관리자인 경우에는 권한을 구성할 필요가 없습니다. 이러한 권한이 없거나 사용자 지정 역할을 구성해야 하는 경우에는 다음과 같은 권한이 필요합니다.

### <a name="permissions-for-pre-existing-automation-account-and-log-analytics-workspace"></a>기존 Automation 계정 및 Log Analytics 작업 영역에 대한 권한

VM에서 기존 Automation 계정 및 Log Analytics 작업 영역을 사용하여 작업 시간 외 VM 시작/중지 기능을 사용하도록 설정하려면 리소스 그룹 범위에서 다음과 같은 권한이 필요합니다. 역할에 대한 자세한 내용은 [Azure 리소스에 대한 사용자 지정 역할](../role-based-access-control/custom-roles.md)을 참조하세요.

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
| Microsoft.automation/automationAccounts/jobSchedules/write | 리소스 그룹 |
| Microsoft.Automation/automationAccounts/jobs/write | 리소스 그룹 |
| Microsoft.Automation/automationAccounts/jobs/read | 리소스 그룹 |
| Microsoft.OperationsManagement/solutions/write | 리소스 그룹 |
| Microsoft.OperationalInsights/workspaces/* | 리소스 그룹 |
| Microsoft.Insights/diagnosticSettings/write | 리소스 그룹 |
| Microsoft.Insights/ActionGroups/Write | 리소스 그룹 |
| Microsoft.Insights/ActionGroups/read | 리소스 그룹 |
| Microsoft.Resources/subscriptions/resourceGroups/read | 리소스 그룹 |
| Microsoft.Resources/deployments/* | 리소스 그룹 |

### <a name="permissions-for-new-automation-account-and-new-log-analytics-workspace"></a>새 Automation 계정 및 새 Log Analytics 작업 영역에 대한 권한

VM에서 새 Automation 계정 및 Log Analytics 작업 영역을 사용하여 작업 시간 외 VM 시작/중지를 사용하도록 설정할 수 있습니다. 이 경우에는 앞 섹션에서 정의된 권한과 이 섹션에서 정의하는 권한이 모두 필요합니다. 다음과 같은 역할도 필요합니다.

- 구독의 공동 관리자. 이 역할은 클래식 VM을 관리해야 하는 경우 클래식 실행 계정을 만드는 데 필요합니다. [클래식 실행 계정](automation-create-standalone-account.md#create-a-classic-run-as-account)은 더 이상 기본적으로 생성되지 않습니다.
- [Azure AD](../active-directory/users-groups-roles/directory-assign-admin-roles.md) 애플리케이션 개발자 역할의 멤버 자격. 실행 계정을 구성하는 방법에 대한 자세한 내용은 [실행 계정 구성 권한](manage-runas-account.md#permissions)을 참조하세요.
- 구독에 대한 기여자 또는 다음과 같은 권한.

| 사용 권한 |범위|
| --- | --- |
| Microsoft.Authorization/Operations/read | Subscription|
| Microsoft.Authorization/permissions/read |Subscription|
| Microsoft.Authorization/roleAssignments/read | Subscription |
| Microsoft.Authorization/roleAssignments/write | Subscription |
| Microsoft.Authorization/roleAssignments/delete | Subscription || Microsoft.Automation/automationAccounts/connections/read | 리소스 그룹 |
| Microsoft.Automation/automationAccounts/certificates/read | 리소스 그룹 |
| Microsoft.Automation/automationAccounts/write | 리소스 그룹 |
| Microsoft.OperationalInsights/workspaces/write | 리소스 그룹 |

## <a name="components"></a>구성 요소

작업 시간 외 VM 시작/중지 기능에는 미리 구성된 Runbook, 일정, Azure Monitor 로그와의 통합이 포함됩니다. 이러한 요소를 사용하여 비즈니스 요구 사항에 맞게 VM의 시작 및 중지를 맞춤 설정할 수 있습니다.

### <a name="runbooks"></a>Runbook

다음 표에는 Automation 계정에 대한 배포를 포함하는 Runbook이 나와 있습니다. Runbook 코드를 변경하지 마세요. 대신, 새 기능에 대한 고유한 Runbook을 작성할 수는 있습니다.

> [!IMPORTANT]
> 이름 뒤에 **child**가 붙은 Runbook은 직접 실행하지 마세요.

모든 부모 Runbook에는 `WhatIf` 매개 변수가 포함됩니다. 이 매개 변수가 True로 설정되면 이 매개 변수 없이 실행될 때 Runbook이 수행하는 정확한 동작에 대한 자세한 설명을 지원하며, 올바른 VM이 대상으로 지정되었는지 확인합니다. Runbook은 `WhatIf` 매개 변수가 False로 설정된 경우에만 정의된 작업을 수행합니다.

|Runbook | 매개 변수 | Description|
| --- | --- | ---|
|AutoStop_CreateAlert_Child | VMObject <br> AlertAction <br> WebHookURI | 부모 Runbook에서 호출됩니다. 이 Runbook은 AutoStop 시나리오에서 리소스 기준으로 경고를 만듭니다.|
|AutoStop_CreateAlert_Parent | VMList<br> WhatIf: True 또는 False  | 대상 구독 또는 리소스 그룹에서 VM에 대해 Azure 경고 규칙을 만들거나 업데이트합니다. <br> `VMList`는 쉼표로 구분된 VM 목록입니다. `vm1, vm2, vm3`)을 입력합니다.<br> `WhatIf`는 Runbook 논리를 실행하지 않고 유효성을 검사합니다.|
|AutoStop_Disable | None | AutoStop 경고 및 기본 일정을 사용하지 않도록 설정합니다.|
|AutoStop_VM_Child | WebHookData | 부모 Runbook에서 호출됩니다. 경고 규칙은 이 Runbook을 호출하여 클래식 VM을 중지합니다.|
|AutoStop_VM_Child_ARM | WebHookData |부모 Runbook에서 호출됩니다. 경고 규칙은 이 Runbook을 호출하여 VM을 중지합니다.  |
|ScheduledStartStop_Base_Classic | CloudServiceName<br> 작업: 시작 또는 중지<br> VMList  | 클래식 VM 그룹에서 Cloud Services에 의해 작업 시작 또는 중지를 수행합니다. |
|ScheduledStartStop_Child | VMName <br> 작업: 시작 또는 중지 <br> ResourceGroupName | 부모 Runbook에서 호출됩니다. 예약된 중지에서 시작 또는 중지 작업을 실행합니다.|
|ScheduledStartStop_Child_Classic | VMName<br> 작업: 시작 또는 중지<br> ResourceGroupName | 부모 Runbook에서 호출됩니다. 클래식 VM의 예약된 중지에서 시작 또는 중지 작업을 실행합니다. |
|ScheduledStartStop_Parent | 작업: 시작 또는 중지 <br>VMList <br> WhatIf: True 또는 False | 구독의 모든 VM을 시작 또는 중지합니다. 이러한 대상이 지정된 리소스 그룹에서만 실행되도록 하려면 변수 `External_Start_ResourceGroupNames` 및 `External_Stop_ResourceGroupNames`를 편집합니다. `External_ExcludeVMNames` 변수를 업데이트하여 특정 VM을 제외할 수도 있습니다.|
|SequencedStartStop_Parent | 작업: 시작 또는 중지 <br> WhatIf: True 또는 False<br>VMList| 시작/중지 작업을 시퀀스하려는 각 VM에서 **sequencestart** 및 **sequencestop**이라는 태그를 만듭니다. 이 태그 이름은 대/소문자를 구분합니다. 태그 값은 시작하거나 중지하려는 순서에 해당하는 양의 정수(1, 2, 3)여야 합니다. <br>**참고**: VM은 `External_Start_ResourceGroupNames`, `External_Stop_ResourceGroupNames`, `External_ExcludeVMNames` 변수에 정의된 리소스 그룹에 있어야 합니다. 작업이 적용되려면 적절한 태그가 있어야 합니다.|

### <a name="variables"></a>variables

다음 표에는 Automation 계정에서 만든 변수가 나열되어 있습니다. `External` 접두사가 붙은 변수만 수정하세요. `Internal` 접두사가 붙은 변수를 수정하면 원치 않는 결과가 발생합니다.

> [!NOTE]
> VM 이름 및 리소스 그룹에 적용되는 제한 사항은 주로 변수 크기로 인한 것입니다. [Azure Automation에서 변수 관리](https://docs.microsoft.com/azure/automation/shared-resources/variables)를 참조하세요.

|변수 | Description|
|---------|------------|
|External_AutoStop_Condition | 경고를 트리거하기 전에 조건을 구성하는 데 필요한 조건부 연산자입니다. 허용되는 값은 `GreaterThan`, `GreaterThanOrEqual`, `LessThan`, `LessThanOrEqual`입니다.|
|External_AutoStop_Description | CPU %가 임계값을 초과하는 경우 VM을 중지하도록 경고합니다.|
|External_AutoStop_Frequency | 규칙의 평가 빈도입니다. 이 매개 변수는 시간 간격 형식의 입력을 허용합니다. 가능한 값은 5분에서 6시간 사이입니다. |
|External_AutoStop_MetricName | Azure 경고 규칙을 구성해야 하는 성능 메트릭의 이름입니다.|
|External_AutoStop_Severity | 메트릭 경고의 심각도로, 0~4 범위입니다. |
|External_AutoStop_Threshold | `External_AutoStop_MetricName` 변수에 지정된 Azure 경고 규칙에 대한 임계값입니다. 백분율 값의 범위는 1에서 100까지입니다.|
|External_AutoStop_TimeAggregationOperator | 조건 평가를 위해 선택된 기간 범위에 적용되는 시간 집계 연산자입니다. 허용되는 값은 `Average`, `Minimum`, `Maximum`, `Total`, `Last`입니다.|
|External_AutoStop_TimeWindow | Azure에서 경고 트리거를 위해 선택된 메트릭을 분석하는 기간의 범위입니다. 이 매개 변수는 시간 간격 형식의 입력을 허용합니다. 가능한 값은 5분에서 6시간 사이입니다.|
|External_EnableClassicVMs| 이 기능에서 클래식 VM이 대상으로 지정되었는지 여부를 지정하는 값입니다. 기본값은 True입니다. Azure CSP(클라우드 솔루션 공급자) 구독에서는 이 변수를 False로 설정하세요. 클래식 VM에는 [클래식 실행 계정](automation-create-standalone-account.md#create-a-classic-run-as-account)이 필요합니다.|
|External_ExcludeVMNames | 제외할 VM 이름의 쉼표로 구분된 목록으로, 140개 VM으로 제한됩니다. 목록에 140개가 넘는 VM을 추가하면 제외하도록 설정된 VM이 우발적으로 시작되거나 중지될 수 있습니다.|
|External_Start_ResourceGroupNames | 시작 작업의 대상으로 지정된 하나 이상의 리소스 그룹의 쉼표로 구분된 목록입니다.|
|External_Stop_ResourceGroupNames | 중지 작업의 대상으로 지정된 하나 이상의 리소스 그룹의 쉼표로 구분된 목록입니다.|
|External_WaitTimeForVMRetrySeconds |**SequencedStartStop_Parent** Runbook에 대해 VM에서 수행될 작업의 대기 시간(초)입니다. 이 변수를 사용하면 Runbook이 다음 작업을 진행하기 전에 지정된 시간(초) 동안 자녀 작업을 기다리도록 할 수 있습니다. 최대 대기 시간은 10800초(3시간)입니다. 기본값은 2100초입니다.|
|Internal_AutomationAccountName | Automation 계정의 이름을 지정합니다.|
|Internal_AutoSnooze_ARM_WebhookURI | AutoStop 시나리오에서 VM에 대해 호출되는 웹후크 URI입니다.|
|Internal_AutoSnooze_WebhookUri | AutoStop 시나리오에서 클래식 VM에 대해 호출되는 웹후크 URI입니다.|
|Internal_AzureSubscriptionId | Azure 구독 ID입니다.|
|Internal_ResourceGroupName | Automation 계정 리소스 그룹 이름입니다.|

>[!NOTE]
>변수 `External_WaitTimeForVMRetryInSeconds`의 기본값이 600에서 2100으로 업데이트되었습니다. 

**AutoStop_CreateAlert_Parent**, **SequencedStartStop_Parent** 및 **ScheduledStartStop_Parent** Runbook의 쉼표로 구분된 VM 목록을 제외하고, 모든 시나리오에서 VM을 대상으로 지정하려면 `External_Start_ResourceGroupNames`, `External_Stop_ResourceGroupNames`, `External_ExcludeVMNames` 변수가 필요합니다. 즉, 시작 및 중지 작업이 진행되려면 VM이 대상 리소스 그룹에 있어야 합니다. 이 논리는 구독 또는 리소스 그룹을 대상으로 지정할 수 있고 새로 만든 VM에서 작업이 상속된다는 측면에서 Azure Policy와 유사하게 작동합니다. 이 방법을 사용하면 모든 VM에 대해 별도 일정을 유지하고 시작 및 중지를 전체적으로 관리할 필요가 없습니다.

### <a name="schedules"></a>일정

다음 표에서는 Automation 계정에서 만든 각 기본 일정을 나열합니다. 일정을 수정하거나 고유한 사용자 지정 일정을 만들 수 있습니다. 기본적으로 모든 일정은 **Scheduled_StartVM** 및 **Scheduled_StopVM** 일정을 제외하고 사용되지 않도록 설정됩니다.

일정 작업이 겹칠 수 있으므로 모든 일정을 사용하도록 설정해서는 안 됩니다. 어느 최적화를 수행할지를 정하고 그에 맞게 수정하는 것이 좋습니다. 추가 설명을 보려면 개요 섹션에서 예제 시나리오를 참조하세요.

|일정 이름 | 빈도 | Description|
|--- | --- | ---|
|Schedule_AutoStop_CreateAlert_Parent | 8시간마다 | **AutoStop_CreateAlert_Parent** Runbook을 8시간 간격으로 실행합니다. 그러면 `External_Start_ResourceGroupNames`, `External_Stop_ResourceGroupNames`, `External_ExcludeVMNames` 변수에서 VM 기반 값이 중지됩니다. 또는 `VMList` 매개 변수를 사용하여 쉼표로 구분된 VM 목록을 지정할 수도 있습니다.|
|Scheduled_StopVM | 사용자 정의, 매일 | 매일 지정된 시간에 `Stop` 매개 변수를 사용하여 **ScheduledStopStart_Parent** Runbook을 실행합니다. 변수 자산에 의해 정의된 규칙을 충족하는 모든 VM을 자동으로 중지합니다. 관련된 일정 **Scheduled-StartVM**을 사용하도록 설정합니다.|
|Scheduled_StartVM | 사용자 정의, 매일 | 매일 지정된 시간에 `Start` 매개 변수 값을 사용하여 **ScheduledStopStart_Parent** Runbook을 실행합니다. 변수 자산에 의해 정의된 규칙을 충족하는 모든 VM을 자동으로 시작합니다. 관련된 일정 **Scheduled-StopVM**을 사용하도록 설정합니다.|
|Sequenced-StopVM | 오전 1시(UTC), 매주 금요일 | 매주 금요일 지정된 시간에 `Stop` 매개 변수 값을 사용하여 **Sequenced_StopStop_Parent** Runbook을 실행합니다. 적절한 변수로 **SequenceStop** 태그가 정의되어 있는 모든 VM이 순차적으로(오름차순으로) 중지됩니다. 태그 값과 자산 변수에 대한 자세한 내용은 [Runbook](#runbooks)을 참조하세요. 관련된 일정, **Sequenced-StartVM**을 사용하도록 설정합니다.|
|Sequenced-StartVM | 오후 1시(UTC), 매주 월요일 | 매주 월요일 지정된 시간에 `Start` 매개 변수 값을 사용하여 **SequencedStopStart_Parent** Runbook을 실행합니다. 적절한 변수로 **SequenceStart** 태그가 정의되어 있는 모든 VM이 순차적으로(내림차순으로) 시작됩니다. 태그 값과 변수 자산에 대한 자세한 내용은 [Runbook](#runbooks)을 참조하세요. 관련된 일정, **Sequenced-StopVM**을 사용하도록 설정합니다.

## <a name="use-the-feature-with-classic-vms"></a>클래식 VM에서 작업 시간 외 VM 시작/중지 기능 사용

클래식 VM에서 작업 시간 외 VM 시작/중지 기능을 사용하는 경우, Automation이 클라우드 서비스별로 모든 VM을 순차적으로 처리합니다. 서로 다른 클라우드 서비스에서는 VM이 여전히 병렬로 처리됩니다. 

클래식 VM에서 작업 시간 외 VM 시작/중지 기능을 사용하려면 클래식 실행 계정이 필요합니다. 클래식 실행 계정은 기본적으로 생성되지 않습니다. 클래식 실행 계정을 만드는 방법은 [클래식 실행 계정 만들기](automation-create-standalone-account.md#create-a-classic-run-as-account)를 참조하세요.

클라우드 서비스당 VM이 20개 이상인 경우에는 다음과 같은 권장 사항을 참조하세요.

* 부모 Runbook **ScheduledStartStop_Parent**를 사용하여 여러 일정을 만들고 일정당 20개의 VM을 지정합니다. 
* 일정 속성에서 `VMList` 매개 변수를 사용하여 VM 이름을 쉼표로 구분된 목록으로 지정합니다. 

이렇게 하지 않으면 이 기능에 대한 Automation 작업이 3시간 이상 실행될 경우 [공평 분배](automation-runbook-execution.md#fair-share) 제한에 따라 일시적으로 언로드되거나 중지됩니다.

Azure CSP 구독은 Azure Resource Manager 모델만 지원합니다. Azure Resource Manager 기반이 아닌 서비스는 프로그램에서 사용할 수 없습니다. 작업 시간 외 VM 시작/중지 기능이 실행되면 이 기능에 클래식 리소스를 관리하기 위한 cmdlet이 있으므로 오류가 발생할 수 있습니다. CSP에 대해 자세히 알아보려면 [CSP 구독에서 사용 가능한 서비스](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services)를 참조하세요. CSP 구독을 사용하는 경우 배포 후에 [External_EnableClassicVMs](#variables) 변수를 False로 설정해야 합니다.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="enable-the-feature"></a>기능 사용

이 기능의 사용을 시작하려면 [작업 시간 외 VM 시작/중지 사용](automation-solution-vm-management-enable.md)의 단계를 따릅니다.

## <a name="view-the-feature"></a>기능 보기

다음 메커니즘 중 하나를 사용하여 사용하도록 설정된 기능에 액세스합니다.

* Automation 계정의 **관련 리소스** 아래에서 **VM 시작/중지**를 선택합니다. VM 시작/중지 페이지의 **VM 시작/중지 솔루션 관리** 아래에서 **솔루션 관리**를 선택합니다.

* Automation 계정에 연결된 Log Analytics 작업 영역으로 이동합니다. 작업 영역을 선택한 다음 왼쪽 창에서 **솔루션**을 선택합니다. 솔루션 페이지의 목록에서 **Start-Stop-VM[workspace]** 를 선택합니다.  

기능을 선택하면 Start-Stop-VM[workspace] 페이지가 표시됩니다. 여기에서 **StartStopVM** 타일에 있는 정보와 같은 중요한 세부 정보를 검토할 수 있습니다. Log Analytics 작업 영역에서와 마찬가지로 이 타일에는 성공적으로 시작되고 완료된 기능에 대한 Runbook 작업의 수와 그래픽 표현이 나타납니다.

![Automation 업데이트 관리 페이지](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)

도넛형 타일을 클릭하면 작업 레코드의 추가 분석을 수행할 수 있습니다. 대시보드에는 작업 기록 및 미리 정의된 로그 검색 쿼리가 표시됩니다. 검색 쿼리를 기반으로 검색하려면 Log Analytics 고급 포털로 전환합니다.

## <a name="update-the-feature"></a>기능 업데이트

작업 시간 외 VM 시작/중지의 이전 버전을 배포한 경우, 업데이트된 릴리스를 배포하기 전에 먼저 계정에서 삭제해야 합니다. [기능 제거](#remove-the-feature) 단계를 따른 다음 [사용 설정](automation-solution-vm-management-enable.md) 단계를 따릅니다.

## <a name="remove-the-feature"></a>기능 제거

이 기능을 더 이상 사용할 필요가 없으면 Automation 계정에서 삭제할 수 있습니다. 기능을 삭제하면 연결된 Runbook만 제거됩니다. 기능을 추가할 때 생성된 일정 또는 변수는 삭제되지 않습니다. 

작업 시간 외 VM 시작/중지를 삭제하려면:

1. Automation 계정의 **관련 리소스** 아래에서 **연결된 작업 영역**을 선택합니다.

2. **작업 영역으로 이동**을 선택합니다.

3. **일반** 아래에서 **솔루션**을 클릭합니다. 

4. 솔루션 페이지에서 **Start-Stop-VM[workspace]** 를 선택합니다. 

5. VMManagementSolution[Workspace] 페이지의 메뉴에서 **삭제**를 선택합니다.<br><br> ![VM 관리 기능 삭제](media/automation-solution-vm-management/vm-management-solution-delete.png)

6. 솔루션 삭제 창에서 기능을 삭제할 것임을 확인합니다.

7. 정보가 확인되고 기능이 삭제되는 동안 메뉴에서 **알림**을 선택하여 진행 상황을 추적할 수 있습니다. 제거 프로세스가 끝나면 솔루션 페이지가 표시됩니다.

8. Automation 계정 및 Log Analytics 작업 영역은 이 프로세스의 일부로 삭제되지 않습니다. Log Analytics 작업 영역을 유지하지 않으려는 경우 Azure Portal에서 수동으로 삭제해야 합니다.

    1. **Log Analytics 작업 영역**을 검색하여 선택합니다.

    2. Log Analytics 작업 영역 페이지에서 작업 영역을 선택합니다.

    3. 메뉴에서 **삭제**를 선택합니다.

    4. Azure Automation 계정 [기능 구성 요소](#components)를 유지하지 않으려면 각각을 수동으로 삭제할 수 있습니다.

## <a name="next-steps"></a>다음 단계

사용자 환경에 있는 VM에서 이 기능을 사용하도록 설정하려면 [작업 시간 외 VM 시작/중지 사용](automation-solution-vm-management-enable.md)을 참조하세요.
