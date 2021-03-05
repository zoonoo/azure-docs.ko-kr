---
title: Azure Automation 작업 시간 외 VM 시작/중지 개요 제거
description: 이 문서에서는 작업 시간 외 VM 시작/중지 기능을 제거 하 고 Log Analytics 작업 영역에서 Automation 계정의 연결을 해제 하는 방법을 설명 합니다.
services: automation
ms.subservice: process-automation
ms.date: 03/04/2021
ms.topic: conceptual
ms.openlocfilehash: 0bab5d8e82ce432e9b3834fe4c003316545eb338
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102122088"
---
# <a name="remove-startstop-vms-during-off-hours-from-automation-account"></a>Automation 계정에서 작업 시간 외 VM 시작/중지 제거

작업 시간 외 VM 시작/중지 기능을 사용 하도록 설정 하 여 Azure Vm의 실행 상태를 관리 한 후에는 사용을 중지 하도록 결정할 수 있습니다. 지원 되는 배포 모델을 기반으로 다음 방법 중 하나를 사용 하 여이 기능을 제거할 수 있습니다.

* Automation 계정 및 연결 된 Azure Monitor Log Analytics 작업 영역을 포함 하는 리소스 그룹을 삭제 합니다. 각 작업 영역에서이 기능을 지원 합니다.
* Automation 계정에서 Log Analytics 작업 영역의 연결을 해제 하 고이 기능 전용 Automation 계정을 삭제 합니다.
* 다른 관리 및 모니터링 목표를 지 원하는 Automation 계정 및 연결 된 작업 영역에서 기능을 삭제 합니다.

이 기능을 삭제 하면 연결 된 runbook만 제거 되 고, 배포 중에 생성 된 일정 또는 변수 또는 이후에 만든 사용자 지정 정의 된 runbook은 삭제 되지 않습니다.

## <a name="delete-the-dedicated-resource-group"></a>전용 리소스 그룹을 삭제 합니다.

리소스 그룹을 삭제 하려면 [리소스 그룹 및 리소스 삭제 Azure Resource Manager](../azure-resource-manager/management/delete-resource-group.md) 문서에 설명 된 단계를 따르세요.

## <a name="delete-the-automation-account"></a>Automation 계정 삭제

작업 시간 외 VM 시작/중지 전용 Automation 계정을 삭제 하려면 다음 단계를 수행 합니다.

1. 에서 Azure에 로그인 [https://portal.azure.com](https://portal.azure.com) 합니다.

2. Automation 계정으로 이동 하 고 **관련 리소스** 에서 **연결 된 작업 영역** 을 선택 합니다.

3. **작업 영역으로 이동** 을 선택합니다.

4. **일반** 아래에서 **솔루션** 을 클릭합니다.

5. 솔루션 페이지에서 **Start-Stop-VM[workspace]** 를 선택합니다.

6. **Vmmanagementsolution [Workspace]** 페이지의 메뉴에서 **삭제** 를 선택 합니다.

7. 정보가 확인되고 기능이 삭제되는 동안 메뉴에서 **알림** 을 선택하여 진행 상황을 추적할 수 있습니다. 제거 프로세스가 끝나면 솔루션 페이지가 표시됩니다.

### <a name="unlink-workspace-from-automation-account"></a>Automation 계정에서 작업 영역 연결 해제

Automation 계정에서 Log Analytics 작업 영역 연결을 해제 하는 두 가지 옵션이 있습니다. Automation 계정 또는 연결 된 작업 영역에서이 프로세스를 수행할 수 있습니다.

Automation 계정에서 연결을 끊으려면 다음 단계를 수행 합니다.

1. Azure Portal에서 **Automation 계정** 을 선택합니다.

2. Automation 계정을 열고 왼쪽의 **관련 리소스** 아래에서 **연결된 작업 영역** 을 선택합니다.

3. **작업 영역 연결 해제** 페이지에서 **작업 영역 연결 해제** 를 선택 하 고 프롬프트에 응답 합니다.

   ![작업 영역 연결 해제 페이지](media/automation-solution-vm-management-remove/automation-unlink-workspace-blade.png)

    Log Analytics 작업 영역의 연결을 해제 하려고 시도 하는 동안 메뉴의 **알림** 에서 진행률을 추적할 수 있습니다.

작업 영역에서 연결을 끊으려면 다음 단계를 수행 합니다.

1. Azure Portal에서 **Log Analytics 작업 영역** 을 선택 합니다.

2. 작업 영역에서 **관련 리소스** 아래에 있는 **Automation 계정** 을 선택 합니다.

3. Automation 계정 페이지에서 **계정 연결 해제** 를 선택 하 고 프롬프트에 응답 합니다.

Automation 계정의 연결을 해제 하려고 시도 하는 동안 메뉴의 **알림** 에서 진행률을 추적할 수 있습니다.

### <a name="delete-automation-account"></a>Automation 계정 삭제

1. Azure Portal에서 **Automation 계정** 을 선택합니다.

2. Automation 계정을 열고 메뉴에서 **삭제** 를 선택 합니다.

정보를 확인 하 고 계정을 삭제 하는 동안 메뉴에서 선택한 **알림** 에서 진행률을 추적할 수 있습니다.

## <a name="delete-the-feature"></a>기능 삭제

Automation 계정에서 작업 시간 외 VM 시작/중지을 삭제 하려면 다음 단계를 수행 합니다. Automation 계정 및 Log Analytics 작업 영역은 이 프로세스의 일부로 삭제되지 않습니다. Log Analytics 작업 영역을 유지 하지 않으려는 경우 수동으로 삭제 해야 합니다. 작업 영역을 삭제 하는 방법에 대 한 자세한 내용은 [Azure Log Analytics 작업 영역 삭제 및 복구](../azure-monitor/logs/delete-workspace.md)를 참조 하세요.

1. Automation 계정으로 이동 하 고 **관련 리소스** 에서 **연결 된 작업 영역** 을 선택 합니다.

2. **작업 영역으로 이동** 을 선택합니다.

3. **일반** 아래에서 **솔루션** 을 클릭합니다.

4. 솔루션 페이지에서 **Start-Stop-VM[workspace]** 를 선택합니다.

5. **Vmmanagementsolution [Workspace]** 페이지의 메뉴에서 **삭제** 를 선택 합니다.

    ![VM 관리 기능 삭제](media/automation-solution-vm-management/vm-management-solution-delete.png)

6. 솔루션 삭제 창에서 기능을 삭제할 것임을 확인합니다.

7. 정보가 확인되고 기능이 삭제되는 동안 메뉴에서 **알림** 을 선택하여 진행 상황을 추적할 수 있습니다. 제거 프로세스가 끝나면 솔루션 페이지가 표시됩니다.

8. 기능이 나 나중에 (예:, 변수, 일정 등)에서 만든 [리소스](automation-solution-vm-management.md#components) 를 유지 하지 않으려면 계정에서 수동으로 삭제 해야 합니다.

## <a name="next-steps"></a>다음 단계

이 기능을 다시 사용 하도록 설정 하려면 [업무 시간 외에 시작/중지 사용](automation-solution-vm-management-enable.md)을 참조 하세요.