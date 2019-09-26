---
title: Azure Log Analytics 작업 영역 삭제 및 복원 | Microsoft Docs
description: 개인 구독에서 작업 영역을 만들었거나 작업 영역 모델을 재구성한 경우 Log Analytics 작업 영역을 삭제하는 방법을 알아봅니다.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: magoedte
ms.openlocfilehash: f8dcab1a7a46d518b752e48f9886b60a37d8ec4c
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2019
ms.locfileid: "71299536"
---
# <a name="delete-and-restore-azure-log-analytics-workspace"></a>Azure Log Analytics 작업 영역 삭제 및 복원
이 문서에서는 Azure Log Analytics 작업 영역 일시 삭제의 개념과 삭제 된 작업 영역을 복구 하는 방법을 설명 합니다. 

## <a name="considerations-when-deleting-a-workspace"></a>작업 영역을 삭제할 때의 고려 사항
Log Analytics 작업 영역을 삭제 하면 해당 데이터 및 연결 된 에이전트를 포함 하는 작업 영역을 14 일 내에 복구 하도록 허용 하기 위해 일시 삭제 작업이 수행 됩니다. 일시 삭제 기간이 지나면 작업 영역 및 해당 데이터는 복구할 수 없으며 30 일 내에 영구적으로 삭제 될 때까지 대기 됩니다.

작업 영역을 삭제할 때 서비스 작업에 부정적인 영향을 줄 수 있는 중요 한 데이터 및 구성이 있을 수 있으므로 주의를 기울여야 합니다. 다음과 같이 Log Analytics에 데이터를 저장 하는 에이전트, 솔루션 및 기타 Azure 서비스와 소스를 검토 합니다.
* 관리 솔루션
* Azure Automation
* Windows 및 Linux 가상 머신에서 실행되는 에이전트
* 사용자 환경의 Windows 및 Linux 컴퓨터에서 실행되는 에이전트
* System Center Operations Manager

일시 삭제 작업은 작업 영역 리소스를 삭제 하 고 연결 된 사용자의 사용 권한은 중단 됩니다. 사용자가 다른 작업 영역과 연결 되어 있으면 다른 작업 영역과 함께 Log Analytics를 계속 사용할 수 있습니다.

## <a name="soft-delete-behavior"></a>일시 삭제 동작
작업 영역 삭제 작업은 작업 영역 리소스 관리자 리소스를 제거 하지만 해당 구성과 데이터는 14 일 동안 유지 되지만 작업 영역을 삭제 하는 모양을 제공 합니다. 작업 영역에 보고 하도록 구성 된 모든 에이전트와 System Center Operations Manager 관리 그룹은 일시 삭제 기간 동안 분리 된 상태로 유지 됩니다. 이 서비스는 데이터 및 연결 된 리소스를 포함 하 여 삭제 된 작업 영역을 복구 하는 메커니즘을 제공 하며, 기본적으로 삭제를 취소 합니다.

> [!NOTE] 
> 설치 된 솔루션 및 Automation 계정과 같은 연결 된 서비스는 삭제 시 작업 영역에서 영구적으로 제거 되며 복구할 수 없습니다. 작업 영역을 이전 기능으로 가져오기 위해 복구 작업 후 다시 구성 해야 합니다. 

[PowerShell](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/remove-azurermoperationalinsightsworkspace?view=azurermps-6.13.0), [API](https://docs.microsoft.com/rest/api/loganalytics/workspaces/delete)또는 [Azure Portal](https://portal.azure.com)를 사용 하 여 작업 영역을 삭제할 수 있습니다.

### <a name="delete-workspace-in-azure-portal"></a>Azure Portal에서 작업 영역 삭제
1. 로그인 하려면 [Azure Portal](https://portal.azure.com)으로 이동 합니다. 
2. Azure Portal에서 **모든 서비스**를 선택합니다. 리소스 목록에서 **Log Analytics**를 입력합니다. 입력을 시작하면 입력한 내용을 바탕으로 목록이 필터링됩니다. **Log Analytics 작업 영역**을 선택합니다.
3. Log Analytics 작업 영역 목록에서 작업 영역을 선택한 다음 가운데 창의 위쪽에서 **삭제** 를 클릭 합니다.
   ![작업 영역 속성 창에서 옵션 삭제](media/delete-workspace/log-analytics-delete-workspace.png)
4. 작업 영역 삭제를 확인할지 묻는 확인 메시지 창이 나타나면 **예**를 클릭합니다.
   ![작업 영역 삭제 확인](media/delete-workspace/log-analytics-delete-workspace-confirm.png)

## <a name="recover-workspace"></a>작업 영역 복구
일시 삭제 작업 이전에 작업 영역이 연결 된 구독 및 리소스 그룹에 대 한 참가자 권한이 있는 경우 해당 데이터, 구성 및 연결 된 에이전트를 포함 하 여 일시 삭제 기간 동안 복구할 수 있습니다. 일시 삭제 기간이 지나면 작업 영역은 복구 불가능 하 고 영구 삭제를 위해 할당 됩니다.

지원 되는 만들기 방법 중 하나를 사용 하 여 작업 영역을 다시 만들면 작업 영역을 복구할 수 있습니다. 다음을 포함 하 여 이러한 속성이 삭제 된 작업 영역의 세부 정보로 채워지는 경우 PowerShell, Azure CLI 또는 Azure Portal에서
1.  구독 ID
2.  리소스 그룹 이름
3.  작업 영역 이름
4.  Region

> [!NOTE]
> 삭제 된 작업 영역의 이름은 일시 삭제 기간 동안 유지 되며 새 작업 영역을 만들 때 사용할 수 없습니다. 작업 영역 이름은 *해제* 되어 일시 삭제 기간이 만료 된 후 새 작업 영역을 만들 때 사용할 수 있습니다.
