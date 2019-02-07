---
title: Azure Log Analytics 및 OMS 포털에서 작업 영역 관리 | Microsoft Docs
description: 사용자, 계정, 작업 영역 및 Azure 계정에 대한 다양한 관리 작업을 사용하여 Azure Log Analytics 및 OMS 포털에서 작업 영역을 관리할 수 있습니다.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: d0e5162d-584b-428c-8e8b-4dcaa746e783
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: magoedte
ms.openlocfilehash: 32a31a87bacbb13cd3b2cb4561ac04e54d51ba46
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/02/2019
ms.locfileid: "55656756"
---
# <a name="manage-workspaces"></a>작업 영역 관리

Log Analytics에 대한 액세스를 관리하려면 작업 영역에 관련된 다양한 관리 태스크를 수행합니다. 이 문서에서는 작업 영역을 관리하기 위한 조언 및 절차를 제공합니다. 작업 영역은 기본적으로 계정에 대한 간단한 구성 정보와 계정 정보를 포함하는 컨테이너입니다. 사용자나 조직의 다른 구성원이 여러 개의 작업 영역을 사용하여 IT 인프라 전체 또는 일부에서 수집되는 각 데이터 집합을 관리할 수 있습니다.

작업 영역을 만들려면 다음 항목을 수행해야 합니다.

1. Azure 구독이 있어야 합니다.
2. 작업 영역 이름을 선택합니다.
3. 작업 영역을 구독 및 리소스 그룹 중 하나와 연결합니다.
4. 지리적 위치를 선택합니다.

## <a name="determine-the-number-of-workspaces-you-need"></a>필요한 작업 영역의 수 결정
작업 영역은 Azure 리소스이며 Azure Portal에서 데이터가 수집, 집계, 분석 및 표시되는 컨테이너입니다.

Azure 구독당 여러 작업 영역을 포함할 수 있으며 이들 간의 손쉬운 쿼리 기능을 사용하여 두 개 이상의 작업 영역에 액세스할 수 있습니다. 이 섹션에서는 둘 이상의 작업 영역을 만들 때 유용할 수 있는 시기를 설명합니다.

현재 작업 영역은 다음을 제공합니다.

* 데이터 저장소의 지리적 위치
* 다른 사용자 액세스 권한을 정의하기 위한 데이터 격리
* [가격 책정 계층](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#changing-pricing-tier), [보존](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period), [데이터 최대 가용량 사용](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#daily-cap) 등의 설정 구성 범위 

사용 관점에서 가능한 한 적은 작업 영역을 만드는 것이 좋습니다. 관리 및 쿼리 환경을 쉽고 빠르게 합니다. 그러나 위의 특성을 기반으로 다음과 같은 경우 여러 작업 영역을 만드는 것이 좋습니다.

* 글로벌 회사이며 데이터 주권 또는 규정 준수 때문에 특정 지역에 저장된 데이터가 필요합니다.
* Azure를 사용하고 있으며 작업 영역을 자신이 관리하는 Azure 리소스와 같은 지역에 두어서 아웃바운드 데이터 전송 요금을 피하려고 합니다.
* 자체 Azure 구독에서 각 부서 또는 비즈니스 그룹에 대한 작업 영역을 만들어 사용량에 따라 다른 부서 또는 비즈니스 그룹에 요금을 할당하려고 합니다.
* 관리되는 서비스 공급자이며 자신이 관리하는 각 고객에 대한 Log Analytics 데이터를 다른 고객의 데이터와 격리되게 유지해야 합니다.
* 여러 고객을 관리하며 각 고객/부서/비즈니스 그룹의 고유 데이터를 보지만 다른 고객/부서/비즈니스 그룹의 데이터를 보지 않기를 원합니다.

Windows 에이전트를 사용하여 데이터를 수집하는 경우 [각 에이전트를 구성하여 하나 이상의 작업 영역에 보고](../../azure-monitor/platform/agent-windows.md)할 수 있습니다.

System Center Operations Manager를 사용하는 경우 각 Operations Manager 관리 그룹을 한 작업 영역에만 연결할 수 있습니다. Operations Manager에서 관리하는 컴퓨터에 Microsoft 모니터링 에이전트를 설치하고 에이전트가 Operations Manager와 서로 다른 Log Analytics 작업 영역에 모두 보고하게 할 수 있습니다.

## <a name="workspace-information"></a>작업 영역 정보

Azure Portal에서 작업 영역에 대한 세부 정보를 볼 수 있습니다. 

1. 아직 로그인하지 않은 경우 [Azure 포털](https://portal.azure.com)에 로그인합니다.

2. Azure Portal에서 **모든 서비스**를 클릭합니다. 리소스 목록에서 **Log Analytics**를 입력합니다. 입력을 시작하면 입력한 내용을 바탕으로 목록이 필터링됩니다. **Log Analytics**를 선택합니다.  

    ![Azure portal](media/manage-access/azure-portal-01.png)  

3. Log Analytics 구독 창에서 작업 영역을 선택합니다.

4. 작업 영역 페이지에는 시작 및 구성에 대한 세부 정보와 추가 정보에 대한 링크가 표시됩니다.  

    ![작업 영역 정보](./media/manage-access/workspace-overview-page.png)  

## <a name="manage-accounts-and-users"></a>계정 및 사용자 관리
각 작업 영역에는 여러 계정이 연결될 수 있으며, 각 계정이 여러 개의 작업 영역에 액세스할 수 있습니다. 액세스는 [Azure 역할 기반 액세스](../../role-based-access-control/role-assignments-portal.md)를 통해 관리됩니다. 이러한 액세스 권한은 Azure Portal 및 API 액세스 모두에 적용됩니다.


다음 활동에도 Azure 권한이 필요합니다.

| 조치                                                          | 필요한 Azure 권한 | 메모 |
|-----------------------------------------------------------------|--------------------------|-------|
| 관리 솔루션 추가 및 제거                        | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/*` <br> `Microsoft.OperationsManagement/*` <br> `Microsoft.Automation/*` <br> `Microsoft.Resources/deployments/*/write` | 이러한 사용 권한은 리소스 그룹 또는 구독 수준에서 권한을 부여 받아야 합니다. |
| 가격 책정 계층 변경                                       | `Microsoft.OperationalInsights/workspaces/*/write` | |
| *Backup* 및 *Site Recovery* 솔루션 타일에서 데이터 보기 | 관리자 / 공동 관리자 | 클래식 배포 모델을 사용하여 배포된 리소스 액세스 |
| Azure Portal에서 작업 영역 만들기                        | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/workspaces/*` ||


### <a name="managing-access-to-log-analytics-using-azure-permissions"></a>Azure 권한을 사용하여 Log Analytics에 대한 액세스 관리
Azure 권한을 사용하여 Log Analytics 작업 영역에 대한 액세스 권한을 부여하려면 [역할 할당을 사용하여 Azure 구독 리소스에 대한 액세스 관리](../../role-based-access-control/role-assignments-portal.md)의 단계를 따릅니다.

Azure의 Log Analytics에는 기본 제공되는 2개의 사용자 역할이 있습니다.
- Log Analytics 독자
- Log Analytics 참가자

*Log Analytics 독자* 역할의 멤버는 다음을 수행할 수 있습니다.
- 모든 모니터링 데이터 검색 및 보기 
- 모든 Azure 리소스에 대한 Azure 진단 구성 보기를 포함해 모니터링 설정 보기

Log Analytics 독자 역할에는 다음 Azure 작업이 포함됩니다.

| Type    | 사용 권한 | 설명 |
| ------- | ---------- | ----------- |
| 조치 | `*/read`   | 모든 Azure 리소스 및 리소스 구성 보는 기능. 볼 수 있습니다. <br> 가상 머신 확장 상태 <br> 리소스에 대한 Azure 진단 구성 <br> 모든 리소스의 모든 속성 및 설정 |
| 조치 | `Microsoft.OperationalInsights/workspaces/analytics/query/action` | 로그 검색 v2 쿼리를 수행할 수 있습니다. |
| 조치 | `Microsoft.OperationalInsights/workspaces/search/action` | 로그 검색 v1 쿼리를 수행할 수 있습니다. |
| 조치 | `Microsoft.Support/*` | 지원 사례를 열 수 있습니다. |
|동작 없음 | `Microsoft.OperationalInsights/workspaces/sharedKeys/read` | 데이터 컬렉션 API를 사용하고 에이전트를 설치하는 데 필요한 작업 영역 키 읽는 것 방지. 사용자가 작업 영역에 새 리소스 추가 방지 |


*Log Analytics 참가자* 역할의 멤버는 다음을 수행할 수 있습니다.
- Log Analytics 독자로서 모든 모니터링 데이터 읽기  
- Automation 계정 만들기 및 구성  
- 관리 솔루션 추가 및 제거    
    > [!NOTE] 
    > 마지막 두 작업을 성공적으로 수행하려면 이 사용 권한은 리소스 그룹 또는 구독 수준에서 권한을 부여 받아야 합니다.  

- 저장소 계정 키 읽기   
- Azure Storage에서 로그 수집 구성  
- 다음을 포함한 Azure 리소스의 모니터링 설정 편집
  - VM에 VM 확장 추가
  - 모든 Azure 리소스에 대한 Azure 진단 구성

> [!NOTE] 
> 가상 머신을 완전히 제어하기 위해 가상 머신으로 가상 머신 확장을 추가할 수 있는 기능을 사용할 수 있습니다.

Log Analytics 기여자 역할에는 다음 Azure 작업이 포함됩니다.

| 사용 권한 | 설명 |
| ---------- | ----------- |
| `*/read`     | 다음을 포함해 모든 리소스 및 리소스 구성을 볼 수 있습니다. <br> 가상 머신 확장 상태 <br> 리소스에 대한 Azure 진단 구성 <br> 모든 리소스의 모든 속성 및 설정 |
| `Microsoft.Automation/automationAccounts/*` | Runbook 추가 및 편집을 포함해 Azure Automation 계정을 만들고 구성할 수 있습니다. |
| `Microsoft.ClassicCompute/virtualMachines/extensions/*` <br> `Microsoft.Compute/virtualMachines/extensions/*` | Microsoft Monitoring Agent 확장 및 Linux 확장용 OMS Agent를 포함해 가상 머신 확장 추가, 업데이트 및 제거 |
| `Microsoft.ClassicStorage/storageAccounts/listKeys/action` <br> `Microsoft.Storage/storageAccounts/listKeys/action` | 저장소 계정 키를 봅니다. Azure Storage 계정에서 로그를 읽을 Log Analytics 구성 필요 |
| `Microsoft.Insights/alertRules/*` | 규칙 추가, 업데이트 및 제거 |
| `Microsoft.Insights/diagnosticSettings/*` | Azure 리소스에 대한 진단 설정 추가, 업데이트 및 제거 |
| `Microsoft.OperationalInsights/*` | Log Analytics 작업 영역에 대한 구성 추가, 업데이트 및 제거 |
| `Microsoft.OperationsManagement/*` | 관리 솔루션 추가 및 제거 |
| `Microsoft.Resources/deployments/*` | 디렉터리를 만들고 삭제합니다. 솔루션, 작업 공간 및 자동화 계정 추가 및 제거에 필요 |
| `Microsoft.Resources/subscriptions/resourcegroups/deployments/*` | 디렉터리를 만들고 삭제합니다. 솔루션, 작업 공간 및 자동화 계정 추가 및 제거에 필요 |

사용자 역할에 사용자를 추가 및 제거하려면 `Microsoft.Authorization/*/Delete` 및 `Microsoft.Authorization/*/Write` 권한이 있어야 합니다.

이러한 역할을 사용하여 사용자에게 다양한 범주에서 액세스를 제공합니다.
- 구독 - 구독에서 모든 작업 영역에 대한 액세스
- 리소스 그룹 - 리소스 그룹에서 모든 작업 영역에 대한 액세스
- 리소스 - 지정된 작업 영역에만 액세스

정확한 액세스 제어를 보장하기 위해 리소스 수준(작업 영역)에서 할당을 수행하는 것이 좋습니다.  [사용자 지정 역할](../../role-based-access-control/custom-roles.md)을 사용하여 필요한 특정 권한이 있는 역할을 만듭니다.

## <a name="next-steps"></a>다음 단계
* 데이터 센터 또는 다른 클라우드 환경의 컴퓨터에서 데이터를 수집하려면 [Log Analytics 에이전트 개요](../../azure-monitor/platform/log-analytics-agent.md)를 참조하세요.
* Azure VM에서 데이터 수집을 구성하려면 [Azure Virtual Machines에 대한 데이터 수집](../../azure-monitor/learn/quick-collect-azurevm.md)을 참조하세요.  
* [솔루션 갤러리에서 Log Analytics 솔루션을 추가](../../azure-monitor/insights/solutions.md) 하여 기능을 추가하고 데이터를 수집합니다.

