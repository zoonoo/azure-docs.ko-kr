---
title: Azure Automation 작업 시간 외 VM 시작/중지 제거 개요
description: 이 문서에서는 작업 시간 외 VM 시작/중지 기능을 제거하고 Log Analytics 작업 영역에서 Automation 계정의 연결을 해제하는 방법을 설명합니다.
services: automation
ms.subservice: process-automation
ms.date: 04/15/2021
ms.topic: conceptual
ms.openlocfilehash: 9ec76197bfde6bb679f70c44ab01712f9f52bfd2
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533956"
---
# <a name="remove-startstop-vms-during-off-hours-from-automation-account"></a>Automation 계정에서 작업 시간 외 VM 시작/중지 제거

Azure VM의 실행 상태를 관리하기 위해 작업 시간 외 VM 시작/중지 기능을 사용하도록 설정한 후 기능 사용을 중지하도록 결정할 수 있습니다. 지원되는 배포 모델을 기반으로 다음 방법 중 하나를 사용하여 이 기능을 제거할 수 있습니다.

* 각각 이 기능을 지원하기 위한 전용 Automation 계정 및 연결된 Azure Monitor Log Analytics 작업 영역을 포함하는 리소스 그룹을 삭제합니다.
* Automation 계정에서 Log Analytics 작업 영역의 연결을 해제하고 이 기능을 위한 전용 Automation 계정을 삭제합니다.
* 다른 관리 및 모니터링 목표를 지원하는 Automation 계정 및 연결된 작업 영역에서 기능을 삭제합니다.

이 기능을 삭제하면 연결된 Runbook만 제거되고, 배포 중에 생성된 일정이나 변수 또는 이후에 만든 사용자 지정 정의된 항목은 삭제되지 않습니다.

> [!NOTE]
> 계속하기 전에 중요한 리소스를 실수로 삭제하거나 수정하지 못하도록 하는 [Resource Manager 잠금](../azure-resource-manager/management/lock-resources.md)이 구독, 리소스 그룹 또는 리소스에서 적용되지 않는지 확인합니다. 작업 시간 외 VM 시작/중지 솔루션을 배포할 때 Automation 계정의 여러 종속 리소스(특히 해당 Runbook 및 변수)에 대해 잠금 수준을 **CanNotDelete** 로 설정합니다. Automation 계정을 삭제하려면 먼저 잠금을 제거해야 합니다.

## <a name="delete-the-dedicated-resource-group"></a>전용 리소스 그룹 삭제

리소스 그룹을 삭제하려면 [Azure Resource Manager 리소스 그룹 및 리소스 삭제](../azure-resource-manager/management/delete-resource-group.md) 문서에 설명된 단계를 따릅니다.

## <a name="delete-the-automation-account"></a>Automation 계정 삭제

작업 시간 외 VM 시작/중지를 위한 전용 Automation 계정을 삭제하려면 다음 단계를 수행합니다.

1. [https://portal.azure.com](https://portal.azure.com)에서 Azure에 로그인합니다.

2. Automation 계정으로 이동하고 **관련 리소스** 에서 **연결된 작업 영역** 을 선택합니다.

3. **작업 영역으로 이동** 을 선택합니다.

4. **일반** 아래에서 **솔루션** 을 클릭합니다.

5. 솔루션 페이지에서 **Start-Stop-VM[workspace]** 를 선택합니다.

6. **VMManagementSolution[Workspace]** 페이지의 메뉴에서 **삭제** 를 선택합니다.

7. 정보가 확인되고 기능이 삭제되는 동안 메뉴에서 **알림** 을 선택하여 진행 상황을 추적할 수 있습니다. 제거 프로세스가 끝나면 솔루션 페이지가 표시됩니다.

### <a name="unlink-workspace-from-automation-account"></a>Automation 계정에서 작업 영역 연결 해제

Automation 계정에서 Log Analytics 작업 영역 연결을 해제하는 두 가지 옵션이 있습니다. Automation 계정 또는 연결된 작업 영역에서 이 프로세스를 수행할 수 있습니다.

Automation 계정에서 연결을 해제하려면 다음 단계를 수행합니다.

1. Azure Portal에서 **Automation 계정** 을 선택합니다.

2. Automation 계정을 열고 왼쪽의 **관련 리소스** 아래에서 **연결된 작업 영역** 을 선택합니다.

3. **작업 영역 연결 해제** 페이지에서 **작업 영역 연결 해제** 를 클릭하고 프롬프트에 응답합니다.

   ![작업 영역 연결 해제 페이지](media/automation-solution-vm-management-remove/automation-unlink-workspace-blade.png)

    Log Analytics 작업 영역의 연결을 해제하는 동안 메뉴의 **알림** 에서 진행 상황을 추적할 수 있습니다.

작업 영역에서 연결을 해제하려면 다음 단계를 수행합니다.

1. Azure Portal에서 **Log Analytics 작업 영역** 을 선택합니다.

2. 작업 영역의 **관련 리소스** 아래에서 **Automation 계정** 을 선택합니다.

3. Automation 계정 페이지에서 **계정 연결 해제** 를 선택하고 프롬프트에 응답합니다.

Automation 계정의 연결을 해제하는 동안 메뉴의 **알림** 에서 진행 상황을 추적할 수 있습니다.

### <a name="delete-automation-account"></a>Automation 계정 삭제

1. Azure Portal에서 **Automation 계정** 을 선택합니다.

2. Automation 계정을 열고 메뉴에서 **삭제** 를 선택합니다.

정보가 확인되고 기능이 삭제되는 동안 메뉴에서 **알림** 을 선택하여 진행 상황을 추적할 수 있습니다.

## <a name="delete-the-feature"></a>기능 삭제

Automation 계정에서 작업 시간 외 VM 시작/중지를 삭제하려면 다음 단계를 수행합니다. Automation 계정 및 Log Analytics 작업 영역은 이 프로세스의 일부로 삭제되지 않습니다. Log Analytics 작업 영역을 유지하지 않으려면 Azure Portal에서 수동으로 삭제해야 합니다. 작업 영역을 삭제하는 방법에 관한 자세한 내용은 [Azure Log Analytics 작업 영역 삭제 및 복구](../azure-monitor/logs/delete-workspace.md)를 참조하세요.

1. Automation 계정으로 이동하고 **관련 리소스** 에서 **연결된 작업 영역** 을 선택합니다.

2. **작업 영역으로 이동** 을 선택합니다.

3. **일반** 아래에서 **솔루션** 을 클릭합니다.

4. 솔루션 페이지에서 **Start-Stop-VM[workspace]** 를 선택합니다.

5. **VMManagementSolution[Workspace]** 페이지의 메뉴에서 **삭제** 를 선택합니다.

    ![VM 관리 기능 삭제](media/automation-solution-vm-management/vm-management-solution-delete.png)

6. 솔루션 삭제 창에서 기능을 삭제할 것임을 확인합니다.

7. 정보가 확인되고 기능이 삭제되는 동안 메뉴에서 **알림** 을 선택하여 진행 상황을 추적할 수 있습니다. 제거 프로세스가 끝나면 솔루션 페이지가 표시됩니다.

8. 기능을 통해 또는 나중에 직접 [리소스](automation-solution-vm-management.md#components)(예: 변수, 일정 등)를 유지하지 않으려면 계정에서 수동으로 삭제해야 합니다.

## <a name="next-steps"></a>다음 단계

이 기능을 다시 사용하려면 [작업 시간 외 시작/중지 사용](automation-solution-vm-management-enable.md)을 참조하세요.