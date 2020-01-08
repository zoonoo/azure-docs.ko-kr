---
title: VM용 Azure Monitor에서 모니터링 사용 안 함 (미리 보기) | Microsoft Docs
description: 이 문서에서는 VM용 Azure Monitor에서 가상 컴퓨터의 모니터링을 중지 하는 방법을 설명 합니다.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/05/2018
ms.openlocfilehash: b2d58b501429a47c3011fc727f756edeeb7775e6
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75399297"
---
# <a name="disable-monitoring-of-your-vms-in-azure-monitor-for-vms-preview"></a>VM용 Azure Monitor에서 Vm 모니터링 사용 안 함 (미리 보기)

Vm (가상 컴퓨터)에 대 한 모니터링을 사용 하도록 설정한 후 나중에 VM용 Azure Monitor 모니터링을 사용 하지 않도록 선택할 수 있습니다. 이 문서에서는 하나 이상의 Vm에 대 한 모니터링을 사용 하지 않도록 설정 하는 방법을 보여 줍니다.  

현재 VM용 Azure Monitor는 VM 모니터링의 선택적 비활성화를 지원 하지 않습니다. Log Analytics 작업 영역에서 VM용 Azure Monitor 및 기타 솔루션을 지원할 수 있습니다. 다른 모니터링 데이터를 수집할 수도 있습니다. Log Analytics 작업 영역에서 이러한 서비스를 제공 하는 경우 시작 하기 전에 모니터링을 사용 하지 않도록 설정 하는 효과와 방법을 이해 해야 합니다.

VM용 Azure Monitor는 해당 환경을 제공하기 위해 다음 구성 요소를 사용합니다.

* Vm 및 기타 원본의 모니터링 데이터를 저장 하는 Log Analytics 작업 영역입니다.
* 작업 영역에 구성 된 성능 카운터의 컬렉션입니다. 컬렉션은 작업 영역에 연결 된 모든 Vm의 모니터링 구성을 업데이트 합니다.
* 작업 영역에 구성 된 모니터링 솔루션 인 `InfrastructureInsights` 및 `ServiceMap`. 이러한 솔루션은 작업 영역에 연결 된 모든 Vm의 모니터링 구성을 업데이트 합니다.
* `MicrosoftMonitoringAgent` 및 `DependencyAgent`(Azure VM 확장). 이러한 확장은 데이터를 수집 하 고 작업 영역으로 보냅니다.

Vm 모니터링을 사용 하지 않도록 준비할 때 다음 사항을 염두에 두어야 합니다.

* 단일 VM을 사용 하 여 평가 하 고 미리 선택 된 기본 Log Analytics 작업 영역을 사용한 경우 VM에서 종속성 에이전트를 제거 하 고이 작업 영역에서 Log Analytics 에이전트의 연결을 해제 하 여 모니터링을 사용 하지 않도록 설정할 수 있습니다. 이 방법은 다른 용도로 VM을 사용 하 고 나중에 다른 작업 영역에 다시 연결 하도록 결정 하는 경우에 적합 합니다.
* 다른 원본에서 다른 모니터링 솔루션 및 데이터 컬렉션을 지 원하는 기존 Log Analytics 작업 영역을 선택한 경우 작업 영역을 중단 하거나 영향을 주지 않고 작업 영역에서 솔루션 구성 요소를 제거할 수 있습니다.  

>[!NOTE]
> 작업 영역에서 솔루션 구성 요소를 제거한 후 Azure Vm의 상태를 계속 확인할 수 있습니다. 특히 포털에서 보기 중 하나로 이동 하면 성능 및 지도 데이터가 표시 됩니다. 데이터는 궁극적으로 **성능** 및 **지도** 보기에 나타나지 않습니다. 하지만 **상태** 보기에는 vm의 상태 상태가 계속 표시 됩니다. **지금 체험** 옵션은 선택한 Azure VM에서 사용할 수 있으므로 나중에 모니터링을 다시 사용 하도록 설정할 수 있습니다.  

## <a name="remove-azure-monitor-for-vms-completely"></a>VM용 Azure Monitor 완전히 제거

Log Analytics 작업 영역이 필요한 경우 다음 단계에 따라 VM용 Azure Monitor를 완전히 제거 합니다. 작업 영역에서 `InfrastructureInsights` 및 `ServiceMap` 솔루션을 제거 합니다.  

>[!NOTE]
>VM용 Azure Monitor을 사용 하도록 설정 하기 전에 서비스 맵 모니터링 솔루션을 사용 하 고 있지만 계속 사용 하는 경우 다음 절차의 마지막 단계에 설명 된 대로 해당 솔루션을 제거 하지 마십시오.  
>

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. Azure Portal에서 **모든 서비스**를 선택합니다. 리소스 목록에서 **Log Analytics**를 입력합니다. 입력을 시작 하면 목록에서 입력을 기준으로 제안을 필터링 합니다. **Log Analytics**를 선택합니다.
3. Log Analytics 작업 영역 목록에서 VM용 Azure Monitor을 사용 하도록 설정할 때 선택한 작업 영역을 선택 합니다.
4. 왼쪽에서 **솔루션**을 선택 합니다.  
5. 솔루션 목록에서 **InfrastructureInsights (작업 영역 이름)** 을 선택 합니다. 솔루션에 대 한 **개요** 페이지에서 **삭제**를 선택 합니다. 확인 하 라는 메시지가 표시 되 면 **예**를 선택 합니다.  
6. 솔루션 목록에서 **Servicemap (작업 영역 이름)** 을 선택 합니다. 솔루션에 대 한 **개요** 페이지에서 **삭제**를 선택 합니다. 확인 하 라는 메시지가 표시 되 면 **예**를 선택 합니다.  

VM용 Azure Monitor를 사용 하도록 설정 하기 전에 작업 영역에서 Windows 기반 또는 Linux 기반 Vm에 대 한 [성능 카운터를 수집](vminsights-enable-overview.md#performance-counters-enabled) 하지 않은 경우 Windows 및 linux에 대해 [해당 규칙을 사용 하지 않도록 설정](../platform/data-sources-performance-counters.md#configuring-performance-counters) 합니다.

## <a name="disable-monitoring-and-keep-the-workspace"></a>모니터링 사용 안 함 및 작업 영역 유지  

Log Analytics 작업 영역에서 여전히 다른 원본의 모니터링을 지원 해야 하는 경우 다음 단계에 따라 VM용 Azure Monitor를 평가 하는 데 사용한 VM에 대 한 모니터링을 사용 하지 않도록 설정 합니다. Azure Vm의 경우 VM에서 직접 Windows 또는 Linux 용 종속성 에이전트 VM 확장 및 Log Analytics 에이전트 VM 확장을 제거 합니다. 

>[!NOTE]
>다음 경우 Log Analytics 에이전트를 제거 하지 마세요. 
>
> * Azure Automation는 VM을 관리 하 여 프로세스를 오케스트레이션 하거나 구성 또는 업데이트를 관리 합니다. 
> * Azure Security Center는 보안 및 위협 검색을 위해 VM을 관리 합니다. 
>
> Log Analytics 에이전트를 제거 하면 해당 서비스 및 솔루션이 VM을 사전에 관리 하지 못하게 됩니다. 

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 
2. Azure Portal에서 **Virtual Machines**를 선택합니다. 
3. 목록에서 VM을 선택합니다. 
4. 왼쪽에서 **확장**을 선택 합니다. **확장** 페이지에서 **DependencyAgent**를 선택 합니다.
5. 확장 속성 페이지에서 **제거**를 선택 합니다.
6. **확장** 페이지에서 **MicrosoftMonitoringAgent**를 선택 합니다. 확장 속성 페이지에서 **제거**를 선택 합니다.  
