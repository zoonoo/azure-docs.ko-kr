---
title: Azure Monitor에서 Log Analytics 작업 영역 관리 | Microsoft Docs
description: 리소스, 작업 영역 또는 테이블 수준 사용 권한을 사용하여 Azure Monitor의 Log Analytics 작업 영역에 저장된 데이터에 대한 액세스를 관리할 수 있습니다. 이 문서에서 그 세부적인 방법을 설명합니다.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/10/2019
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 38d0b9db730d82642fafda4a42751c35d6cac0f8
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122537698"
---
# <a name="manage-access-to-log-data-and-workspaces-in-azure-monitor"></a>Azure Monitor의 로그 데이터 및 작업 영역에 대한 액세스 관리

Azure Monitor는 [로그](../logs/data-platform-logs.md) 데이터를 Log Analytics 작업 영역에 저장합니다. 작업 영역은 데이터 및 구성 정보를 포함하는 컨테이너입니다. 로그 데이터에 대한 액세스를 관리하려면 작업 영역에 관련된 다양한 관리 태스크를 수행합니다.

이 문서는 다음에 액세스 권한을 부여하는 방법을 포함하여, 로그 액세스를 관리하고 그것들이 담긴 작업 영역을 관리하는 방법을 설명합니다. 

* 작업 영역 사용 권한을 사용하는 작업 영역입니다.
* Azure RBAC(역할 기반 액세스 제어)를 사용하여 특정 리소스의 로그 데이터에 액세스해야 하는 사용자([리소스 컨텍스트](../logs/design-logs-deployment.md#access-mode)라고도 함)
* Azure RBAC를 사용하여 작업 영역의 특정 테이블에 있는 로그 데이터에 액세스해야 하는 사용자입니다.

Azure RBAC 및 액세스 전략에 대한 로그 개념을 이해하려면 [Azure Monitor Logs 배포 디자인](../logs/design-logs-deployment.md)을 참조 하세요.

## <a name="configure-access-control-mode"></a>액세스 제어 모드 구성

Azure Portal에서 또는 Azure PowerShell로 작업 영역에 구성된 [액세스 제어 모드](../logs/design-logs-deployment.md)를 볼 수 있습니다.  다음 방법 중 하나를 사용하여 이 설정을 변경할 수 있습니다.

* Azure Portal

* Azure PowerShell

* Azure Resource Manager 템플릿

### <a name="from-the-azure-portal"></a>Azure Portal에서

**Log Analytics 작업 영역** 메뉴의 작업 영역에 대한 **개요** 페이지에서 현재 작업 영역 액세스 제어 모드를 볼 수 있습니다.

![작업 영역 액세스 제어 모드 보기](media/manage-access/view-access-control-mode.png)

1. [https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.
1. Azure Portal에서 Log Analytics 작업 영역 > 작업 영역을 선택합니다.

작업 영역의 **속성** 페이지에서 이 설정을 변경할 수 있습니다. 작업 영역을 구성할 수 있는 권한이 없는 경우 이 설정을 변경할 수 없습니다.

![작업 영역 액세스 모드 변경](media/manage-access/change-access-control-mode.png)

### <a name="using-powershell"></a>PowerShell 사용

다음 명령을 사용하여 구독의 모든 작업 영역에 대한 액세스 제어 모드를 검사합니다.

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {$_.Name + ": " + $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions}
```

출력은 다음과 유사합니다.

```
DefaultWorkspace38917: True
DefaultWorkspace21532: False
```

`False` 값은 해당 작업 영역이 작업 영역 컨텍스트 액세스 모드로 구성되어 있음을 의미합니다.  `True` 값은 해당 작업 영역이 리소스 컨텍스트 액세스 모드로 구성되어 있음을 의미합니다.

> [!NOTE]
> 작업 영역이 부울 값 없이 반환되고 비어 있는 경우, 이는 `False` 값의 결과와도 일치합니다.
>

다음 스크립트를 사용하여 특정 작업 영역에 대한 액세스 제어 모드를 리소스 컨텍스트 권한으로 설정합니다.

```powershell
$WSName = "my-workspace"
$Workspace = Get-AzResource -Name $WSName -ExpandProperties
if ($Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null)
    { $Workspace.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else
    { $Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $Workspace.ResourceId -Properties $Workspace.Properties -Force
```

다음 스크립트를 사용하여 구독의 모든 작업 영역에 대한 액세스 제어 모드를 리소스 컨텍스트 권한으로 설정합니다.

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {
if ($_.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null)
    { $_.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else
    { $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $_.ResourceId -Properties $_.Properties -Force
}
```

### <a name="using-a-resource-manager-template"></a>Resource Manager 템플릿 사용

Azure Resource Manager 템플릿에서 액세스 모드를 구성하려면 작업 영역의 **enableLogAccessUsingOnlyResourcePermissions** 기능 플래그를 다음 값 중 하나로 설정합니다.

* **false**: 작업 영역을 작업 영역 컨텍스트 권한으로 설정합니다. 플래그가 설정되지 않은 경우 이 설정이 기본 설정입니다.
* **true**: 작업 영역을 리소스 컨텍스트 권한으로 설정합니다.

## <a name="manage-access-using-workspace-permissions"></a>작업 영역 권한을 사용하여 액세스 관리

각 작업 영역에는 여러 계정이 연결될 수 있으며, 각 계정이 여러 개의 작업 영역에 액세스할 수 있습니다. Azure [RBAC(역할 기반 액세스 제어)](../../role-based-access-control/role-assignments-portal.md)를 사용하여 액세스를 관리합니다.

다음 활동에도 Azure 권한이 필요합니다.

|작업 |필요한 Azure 권한 |메모 |
|-------|-------------------------|------|
| 모니터링 솔루션 추가 및 제거 | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/*` <br> `Microsoft.OperationsManagement/*` <br> `Microsoft.Automation/*` <br> `Microsoft.Resources/deployments/*/write` | 이러한 사용 권한은 리소스 그룹 또는 구독 수준에서 권한을 부여 받아야 합니다. |
| 가격 책정 계층 변경 | `Microsoft.OperationalInsights/workspaces/*/write` | |
| *Backup* 및 *Site Recovery* 솔루션 타일에서 데이터 보기 | 관리자 / 공동 관리자 | 클래식 배포 모델을 사용하여 배포된 리소스 액세스 |
| Azure Portal에서 작업 영역 만들기 | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/workspaces/*` ||
| 작업 영역 기본 속성 보기 및 포털에서 작업 영역 블레이드 입력 | `Microsoft.OperationalInsights/workspaces/read` ||
| 인터페이스를 사용하여 로그 쿼리 | `Microsoft.OperationalInsights/workspaces/query/read` ||
| 쿼리를 사용하여 모든 로그 형식 액세스 | `Microsoft.OperationalInsights/workspaces/query/*/read` ||
| 특정 로그 테이블 액세스 | `Microsoft.OperationalInsights/workspaces/query/<table_name>/read` ||
| 이 작업 영역으로 로그를 보낼 수 있도록 작업 영역 키를 읽습니다. | `Microsoft.OperationalInsights/workspaces/sharedKeys/action` ||

## <a name="manage-access-using-azure-permissions"></a>Azure 사용 권한을 사용하여 액세스 관리

Azure 권한을 사용하여 Log Analytics 작업 영역에 대한 액세스를 허용하려면 [Azure 역할을 할당하여 Azure 구독 리소스에 대한 액세스 관리](../../role-based-access-control/role-assignments-portal.md)에 있는 단계를 따릅니다. 예를 들어 사용자 지정 역할은 [예제 사용자 지정 역할](#custom-role-examples)을 참조하세요.

Azure의 Log Analytics 작업 영역에는 기본 제공되는 2개의 사용자 역할이 있습니다.

* Log Analytics 독자
* Log Analytics 참가자

*Log Analytics 독자* 역할의 멤버는 다음을 수행할 수 있습니다.

* 모든 모니터링 데이터 검색 및 보기
* 모든 Azure 리소스에 대한 Azure 진단 구성 보기를 포함해 모니터링 설정을 봅니다.

Log Analytics 독자 역할에는 다음 Azure 작업이 포함됩니다.

| 형식    | 사용 권한 | 설명 |
| ------- | ---------- | ----------- |
| 작업 | `*/read`   | 모든 Azure 리소스 및 리소스 구성 보는 기능. 볼 수 있습니다. <br> 가상 머신 확장 상태 <br> 리소스에 대한 Azure 진단 구성 <br> 모든 리소스의 모든 속성 및 설정 <br> 작업 영역의 경우 작업 영역 설정을 읽고 데이터에 대한 쿼리를 수행하는 데 무제한의 모든 권한을 허용합니다. 위의 세부적인 옵션을 참조하세요. |
| 작업 | `Microsoft.OperationalInsights/workspaces/analytics/query/action` | 더 이상 사용되지 않으며 사용자에게 할당할 필요가 없습니다. |
| 작업 | `Microsoft.OperationalInsights/workspaces/search/action` | 더 이상 사용되지 않으며 사용자에게 할당할 필요가 없습니다. |
| 작업 | `Microsoft.Support/*` | 지원 사례를 열 수 있습니다. |
|동작 없음 | `Microsoft.OperationalInsights/workspaces/sharedKeys/read` | 데이터 컬렉션 API를 사용하고 에이전트를 설치하는 데 필요한 작업 영역 키 읽는 것 방지. 사용자가 작업 영역에 새 리소스 추가 방지 |

*Log Analytics 참가자* 역할의 멤버는 다음을 수행할 수 있습니다.

* 사용자가 모든 모니터링 데이터를 읽을 수 있도록 하는 *Log Analytics Reader 역할* 의 모든 권한을 포함합니다.
* 자동화 계정 만들고 구성하기
* 관리 솔루션 추가 및 제거

    > [!NOTE]
    > 마지막 두 작업을 성공적으로 수행하려면 이 사용 권한은 리소스 그룹 또는 구독 수준에서 권한을 부여 받아야 합니다.

* 스토리지 계정 키 읽기
* Azure Storage에서 로그 수집 구성
* 다음을 포함한 Azure 리소스의 모니터링 설정 편집
  * VM에 VM 확장 추가
  * 모든 Azure 리소스에 대한 Azure 진단 구성

> [!NOTE]
> 가상 머신을 완전히 제어하기 위해 가상 머신으로 가상 머신 확장을 추가할 수 있는 기능을 사용할 수 있습니다.

Log Analytics 기여자 역할에는 다음 Azure 작업이 포함됩니다.

| 사용 권한 | Description |
| ---------- | ----------- |
| `*/read`     | 다음을 포함해 모든 리소스 및 리소스 구성을 볼 수 있습니다. <br> 가상 머신 확장 상태 <br> 리소스에 대한 Azure 진단 구성 <br> 모든 리소스의 모든 속성 및 설정 <br> 작업 영역의 경우 작업 영역 설정을 읽고 데이터에 대해 쿼리를 수행할 수 있는 완전 무제한 권한을 허용합니다. 위의 세부적인 옵션을 참조하세요. |
| `Microsoft.Automation/automationAccounts/*` | Runbook 추가 및 편집을 포함해 Azure Automation 계정을 만들고 구성할 수 있습니다. |
| `Microsoft.ClassicCompute/virtualMachines/extensions/*` <br> `Microsoft.Compute/virtualMachines/extensions/*` | Microsoft Monitoring Agent 확장 및 Linux 확장용 OMS Agent를 포함해 가상 머신 확장 추가, 업데이트 및 제거 |
| `Microsoft.ClassicStorage/storageAccounts/listKeys/action` <br> `Microsoft.Storage/storageAccounts/listKeys/action` | 스토리지 계정 키를 봅니다. Azure Storage 계정에서 로그를 읽을 Log Analytics 구성 필요 |
| `Microsoft.Insights/alertRules/*` | 규칙 추가, 업데이트 및 제거 |
| `Microsoft.Insights/diagnosticSettings/*` | Azure 리소스에 대한 진단 설정 추가, 업데이트 및 제거 |
| `Microsoft.OperationalInsights/*` | Log Analytics 작업 영역에 대한 구성을 추가, 업데이트 및 제거합니다. 사용자가 작업 영역 고급 설정을 편집하려면 `Microsoft.OperationalInsights/workspaces/write`가 필요합니다. |
| `Microsoft.OperationsManagement/*` | 관리 솔루션 추가 및 제거 |
| `Microsoft.Resources/deployments/*` | 디렉터리를 만들고 삭제합니다. 솔루션, 작업 공간 및 자동화 계정 추가 및 제거에 필요 |
| `Microsoft.Resources/subscriptions/resourcegroups/deployments/*` | 디렉터리를 만들고 삭제합니다. 솔루션, 작업 공간 및 자동화 계정 추가 및 제거에 필요 |

사용자 역할에 사용자를 추가 및 제거하려면 `Microsoft.Authorization/*/Delete` 및 `Microsoft.Authorization/*/Write` 권한이 있어야 합니다.

이러한 역할을 사용하여 사용자에게 다양한 범주에서 액세스를 제공합니다.

* 구독 - 구독에서 모든 작업 영역에 대한 액세스
* 리소스 그룹 - 리소스 그룹에서 모든 작업 영역에 대한 액세스
* 리소스 - 지정된 작업 영역에만 액세스

정확한 액세스 제어를 보장하려면 리소스 수준(작업 영역)에서 할당을 수행하는 것이 좋습니다. [사용자 지정 역할](../../role-based-access-control/custom-roles.md)을 사용하여 필요한 특정 권한이 있는 역할을 만듭니다.

### <a name="resource-permissions"></a>리소스 권한

사용자가 리소스 컨텍스트 액세스를 사용하여 작업 영역에서 로그를 쿼리하면 해당 리소스에 대해 다음과 같은 사용 권한이 부여됩니다.

| 사용 권한 | Description |
| ---------- | ----------- |
| `Microsoft.Insights/logs/<tableName>/read`<br><br>예:<br>`Microsoft.Insights/logs/*/read`<br>`Microsoft.Insights/logs/Heartbeat/read` | 리소스에 대한 모든 로그 데이터를 볼 수 있습니다.  |
| `Microsoft.Insights/diagnosticSettings/write` | 이 리소스에 대한 로그를 설정할 수 있도록 진단 설정을 구성할 수 있습니다. |

`/read`사용 권한은 일반적으로 기본 제공 _[Reader](../../role-based-access-control/built-in-roles.md#reader), [Contributor](../../role-based-access-control/built-in-roles.md#contributor) 역할과 같은 \*/read or_ _\*_ 사용 권한을 포함하는 역할에서 부여됩니다. 특정 작업 또는 전용 기본 제공 역할을 포함하는 사용자 지정 역할에는 이 권한이 포함되지 않을 수 있습니다.

다른 테이블에 대한 다른 액세스 제어를 만들려면 아래의 [테이블당 액세스 제어 정의](#table-level-azure-rbac)를 참조하세요.

## <a name="custom-role-examples"></a>사용자 지정 역할 예제

1. 사용자에게 리소스에서 로그 데이터 액세스를 허용하려면 다음을 수행합니다.

    * **작업 영역 또는 리소스 권한을 사용** 하도록 작업 영역 액세스 제어 모드 구성

    * 사용자에게 리소스에 대한 `*/read` 또는 `Microsoft.Insights/logs/*/read` 사용 권한을 부여합니다. 작업 영역에서 [Log Analytics Reader](../../role-based-access-control/built-in-roles.md#reader) 역할이 이미 할당되었다면 충분합니다.

2. 사용자에게 리소스의 로그 데이터에 대한 액세스를 허용하고 작업 영역에 로그를 보내도록 리소스를 구성하려면 다음을 수행합니다.

    * **작업 영역 또는 리소스 권한을 사용** 하도록 작업 영역 액세스 제어 모드 구성

    * 작업 영역에 대하여 사용자에게 `Microsoft.OperationalInsights/workspaces/read`과 `Microsoft.OperationalInsights/workspaces/sharedKeys/action` 권한을 부여합니다. 이러한 권한이 있는 사용자는 작업 영역 수준 쿼리를 수행할 수 없습니다. 작업 영역을 열거하고 이를 진단 설정 또는 에이전트 구성의 대상으로만 사용할 수 있습니다.

    * 리소스에 대하여 사용자에게 `Microsoft.Insights/logs/*/read`과 `Microsoft.Insights/diagnosticSettings/write` 권한을 부여합니다. 이미 해당 리소스에 대하여 [Log Analytics Contributor](../../role-based-access-control/built-in-roles.md#contributor) 역할을 할당 받거나 Reader 역할을 할당 받거나 `*/read` 권한이 부여되었다면 충분합니다.

3. 보안 이벤트를 읽고 데이터를 보낼 수 없는 경우 사용자에게 리소스의 로그 데이터에 대한 액세스를 허용하려면 다음을 수행합니다.

    * **작업 영역 또는 리소스 권한을 사용** 하도록 작업 영역 액세스 제어 모드 구성

    * 사용자에게 리소스에 대한 다음 권한을 부여합니다. `Microsoft.Insights/logs/*/read`

    * 사용자가 SecurityEvent 형식을 읽을 수 없도록 차단하는 다음과 같은 NonAction을 추가합니다. `Microsoft.Insights/logs/SecurityEvent/read` NonAction은 읽기 권한(`Microsoft.Insights/logs/*/read`)을 제공하는 동작과 동일한 사용자 지정 역할에 있어야 합니다. 사용자가 이 리소스 또는 구독 또는 리소스 그룹에 할당된 다른 역할의 읽기 작업을 수행하는 경우 모든 로그 유형을 읽을 수 있습니다. 이는 Reader 또는 Contributor 등의 역할과 함께 존재하는 `*/read`를 상속하는 경우에도 마찬가지입니다.

4. 사용자에게 리소스의 로그 데이터에 대한 액세스를 허용하고 모든 Azure AD 로그인을 읽고 작업 영역에서 업데이트 관리 솔루션 로그 데이터를 읽을 수 있도록 하려면 다음을 수행합니다.

    * **작업 영역 또는 리소스 권한을 사용** 하도록 작업 영역 액세스 제어 모드 구성

    * 사용자에게 작업 영역에 대한 다음 권한을 부여합니다. 

        * `Microsoft.OperationalInsights/workspaces/read` – 사용자가 작업 영역을 열거하고 Azure Portal 작업 영역 블레이드를 열 때 필요
        * `Microsoft.OperationalInsights/workspaces/query/read` – 쿼리를 실행할 수 있는 모든 사용자에게 필요
        * `Microsoft.OperationalInsights/workspaces/query/SigninLogs/read` – Azure AD 로그인 로그를 읽을 수 있음
        * `Microsoft.OperationalInsights/workspaces/query/Update/read` – 업데이트 관리 솔루션 로그를 읽을 수 있음
        * `Microsoft.OperationalInsights/workspaces/query/UpdateRunProgress/read` – 업데이트 관리 솔루션 로그를 읽을 수 있음
        * `Microsoft.OperationalInsights/workspaces/query/UpdateSummary/read` – 업데이트 관리 로그를 읽을 수 있음
        * `Microsoft.OperationalInsights/workspaces/query/Heartbeat/read` – 업데이트 관리 솔루션을 사용하는 데 필요
        * `Microsoft.OperationalInsights/workspaces/query/ComputerGroup/read` – 업데이트 관리 솔루션을 사용하는 데 필요

    * 사용자에게 리소스에 대한 다음 권한을 부여합니다. `*/read`, Reader 역할에 할당된 또는 `Microsoft.Insights/logs/*/read` 

## <a name="table-level-azure-rbac"></a>테이블 수준 Azure RBAC

**테이블 수준 Azure RBAC** 를 사용하면 다른 권한 외에도 Log Analytics 작업 영역에서 데이터에 대한 보다 세분화된 컨트롤을 정의할 수 있습니다. 이를 통해 관리자는 특정 사용자 집합만 액세스할 수 있는 특정 데이터 형식을 정의할 수 있습니다.

[Azure 사용자 지정 역할](../../role-based-access-control/custom-roles.md)을 사용하여 테이블 액세스 제어를 구현하여 작업 영역의 특정 [테이블](../logs/data-platform-logs.md)에 대한 액세스를 허용합니다. 이러한 역할은 사용자의 [액세스 모드](../logs/design-logs-deployment.md#access-mode)에 관계 없이 작업 영역 컨텍스트 또는 리소스 컨텍스트 [액세스 제어 모드](../logs/design-logs-deployment.md#access-control-mode)를 사용하여 작업 영역에 적용됩니다.

다음 작업을 사용하여 [사용자 지정 역할](../../role-based-access-control/custom-roles.md)을 만들어 테이블 액세스 제어에 대한 액세스를 정의합니다.

* 테이블에 대한 액세스를 허용하려면 역할 정의의 **Actions** 섹션에 해당 테이블을 포함합니다. 허용되는 **Actions** 에서 액세스를 빼려는 경우 해당 동작을 **NotActions** 섹션에 포함합니다.
* 모든 테이블을 지정하려면 Microsoft.OperationalInsights/workspaces/query/*를 사용합니다.

예를 들어 _Heartbeat_ 와 _AzureActivity_ 테이블에 액세스할 수 있는 역할을 만들려면 다음 작업을 사용하여 사용자 지정 역할을 만듭니다.

```
"Actions":  [
    "Microsoft.OperationalInsights/workspaces/read",
    "Microsoft.OperationalInsights/workspaces/query/read",
    "Microsoft.OperationalInsights/workspaces/query/Heartbeat/read",
    "Microsoft.OperationalInsights/workspaces/query/AzureActivity/read"
  ],
```

_SecurityBaseline_ 테이블에만 액세스할 수 있는 역할을 만들려면 다음 작업을 사용하여 사용자 지정 역할을 만듭니다.

```
"Actions":  [
    "Microsoft.OperationalInsights/workspaces/read",
    "Microsoft.OperationalInsights/workspaces/query/read",
    "Microsoft.OperationalInsights/workspaces/query/SecurityBaseline/read"
],
```
위의 예제는 허용되는 테이블 목록을 정의합니다. 이 예에서는 사용자가 모든 테이블에 액세스할 수 있지만 _SecurityAlert_ 테이블에 액세스할 수 없는 경우 차단되는 목록의 정의를 보여 줍니다.

```
"Actions":  [
    "Microsoft.OperationalInsights/workspaces/read",
    "Microsoft.OperationalInsights/workspaces/query/read",
    "Microsoft.OperationalInsights/workspaces/query/*/read"
],
"notActions":  [
    "Microsoft.OperationalInsights/workspaces/query/SecurityAlert/read"
],
```

### <a name="custom-logs"></a>사용자 지정 로그

 사용자 지정 로그는 사용자 지정 로그 및 HTTP 데이터 수집기 API와 같은 데이터 원본에서 생성됩니다. 로그 유형을 식별하는 가장 쉬운 방법은 [로그 스키마의 사용자 지정 로그](./log-analytics-tutorial.md#view-table-information)에 나열된 테이블을 확인하는 것입니다.

 개별 사용자 지정 로그에는 액세스를 허용할 수 없지만 모든 사용자 지정 로그에 대한 액세스 권한을 부여할 수는 있습니다. 모든 사용자 지정 로그에 대한 액세스 권한이 있는 역할을 만들려면 다음 작업을 사용하여 사용자 지정 역할을 만듭니다.

```
"Actions":  [
    "Microsoft.OperationalInsights/workspaces/read",
    "Microsoft.OperationalInsights/workspaces/query/read",
    "Microsoft.OperationalInsights/workspaces/query/Tables.Custom/read"
],
```
사용자 지정 로그에 대한 액세스를 관리하는 다른 방법은 Azure 리소스에 할당하고 리소스 컨텍스트 패러다임을 사용하여 액세스를 관리하는 것입니다. 이 메서드를 사용하려면 [x-ms-AzureResourceId](../logs/data-collector-api.md#request-headers) 헤더를 지정하여 [HTTP 데이터 수집기 API](../logs/data-collector-api.md)를 통해 데이터를 Log Analytics로 수집할 때 리소스 ID를 포함해야 합니다. 리소스 ID는 유효해야 하며 적용되는 액세스 규칙이 있어야 합니다. 로그를 수집한 후에는 여기에 설명된 대로 리소스에 대한 읽기 권한이 있는 로그에 액세스할 수 있습니다.

경우에 따라 특정 리소스에 직접 연결되지 않은 원본에서 사용자 지정 로그를 가져올 수 있습니다. 이 경우 이러한 로그에 대한 액세스를 관리하는 리소스 그룹을 만듭니다. 리소스 그룹에는 비용이 발생하지 않지만 사용자 지정 로그에 대한 액세스를 제어하는 유효한 리소스 ID를 제공합니다. 예를 들어 특정 방화벽에서 사용자 지정 로그를 보내는 경우 "MyFireWallLogs"라는 리소스 그룹을 만들고 API 요청에 "MyFireWallLogs"의 리소스 ID가 포함되어 있는지 확인합니다. 그러면 MyFireWallLogs 또는 전체 작업 영역 액세스 권한이 있는 사용자만 방화벽 로그 레코드에 액세스할 수 있습니다.          

### <a name="considerations"></a>고려 사항

* 사용자에게 _\*/read_ 동작을 포함하는 표준 Reader 또는 Contributor 역할을 사용하여 전역 읽기 권한이 부여된 경우에는 테이블당 액세스 제어를 재정의하고 모든 로그 데이터에 대한 액세스를 허용합니다.
* 사용자에게 테이블당 액세스를 허용하고 다른 권한은 부여하지 않으면 API에서 로그 데이터에 액세스할 수 있지만 Azure Portal에서는 액세스할 수 없습니다. Azure Portal에서 액세스할 수 있도록 하려면 Log Analytics Reader를 기본 역할로 사용합니다.
* 구독의 관리자와 소유자는 다른 권한 설정에 관계 없이 모든 데이터 형식에 액세스할 수 있습니다.
* 작업 영역 소유자는 테이블당 액세스 제어를 위해 다른 사용자와 같은 방식으로 처리됩니다.
* 할당 수를 줄이기 위해 개별 사용자 대신 보안 그룹에 역할을 할당하는 것이 좋습니다. 이는 기존 그룹 관리 도구를 사용하여 액세스를 구성하고 확인하는 데도 도움이 됩니다.

## <a name="next-steps"></a>다음 단계

* 데이터 센터 또는 다른 클라우드 환경의 컴퓨터에서 데이터를 수집하려면 [Log Analytics 에이전트 개요](../agents/log-analytics-agent.md)를 참조하세요.

* Azure VM에서 데이터 수집을 구성하려면 [Azure 가상 머신에 대한 데이터 수집](../vm/monitor-virtual-machine.md)을 참조하세요.