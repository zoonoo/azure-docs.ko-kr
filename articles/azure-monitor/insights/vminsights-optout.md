---
title: Vm (미리 보기)에 대 한 Azure Monitor의 모니터링 사용 안 함 | Microsoft Docs
description: 이 문서에서는 Azure Monitor에서 가상 컴퓨터를 Vm에 대 한 모니터링을 중지 하는 방법을 설명 합니다.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/05/2018
ms.author: magoedte
ms.openlocfilehash: eb667486a6e3279cb78fefe02723f14d9f7c9b4f
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2019
ms.locfileid: "67155695"
---
# <a name="disable-monitoring-of-your-vms-in-azure-monitor-for-vms-preview"></a>Azure Monitor에서 Vm의 Vm (미리 보기)에 대 한 모니터링 사용 안 함

가상 컴퓨터 (Vm)의 모니터링을 활성화 한 후에 Vm에 대 한 Azure Monitor의 모니터링을 사용 하지 않도록 설정 하려면 나중에 선택할 수 있습니다. 이 문서에는 하나 이상의 Vm에 대 한 모니터링을 사용 하지 않도록 설정 하는 방법을 보여 줍니다.  

현재 Vm에 대 한 Azure Monitor는 선택적 비활성화 VM 모니터링을 지원 하지 않습니다. Log Analytics 작업 영역 Vm 및 다른 솔루션에 대 한 Azure Monitor를 지원할 수 있습니다. 기타 모니터링 데이터를 수집할 수도 있습니다. Log Analytics 작업 영역에서 이러한 서비스를 제공 하는 경우 적용 및 모니터링을 시작 하기 전에 사용 하지 않도록 설정 하는 메서드를 이해 해야 합니다.

VM용 Azure Monitor는 해당 환경을 제공하기 위해 다음 구성 요소를 사용합니다.

* Log Analytics 작업 영역을 Vm 및 기타 원본에서 모니터링 데이터를 저장 합니다.
* 컬렉션 작업 영역에서 구성 하는 성능 카운터입니다. 컬렉션 작업 영역에 연결 된 모든 Vm에 모니터링 구성을 업데이트 합니다.
* `InfrastructureInsights` 및 `ServiceMap`, 작업 영역에서 구성 된 솔루션을 모니터링 하는입니다. 이러한 솔루션에는 작업 영역에 연결 된 모든 Vm에 모니터링 구성을 업데이트 합니다.
* `MicrosoftMonitoringAgent` 및 `DependencyAgent`에 Azure VM 확장 합니다. 이러한 확장 수집 하 고 작업 영역에 데이터를 보냅니다.

Vm의 모니터링을 사용 하지 않도록 설정할 준비가 이러한 사항을 고려 하십시오.

* 단일 VM을 사용 하 여 확인 하 고 미리 선택 된 기본 Log Analytics 작업 영역을 사용 하는 경우 VM에서 종속성 에이전트를 제거 하 고이 작업 영역에서 Log Analytics 에이전트를 연결을 해제 하 여 모니터링을 비활성화할 수 있습니다. 이 방법은 VM을 다른 용도로 사용 하 고 나중에 다른 작업 영역에 다시 연결 하려는 경우에 적합 합니다.
* 다른 원본에서 데이터 수집 및 기타 모니터링 솔루션을 지 원하는 기존 Log Analytics 작업 영역을 선택한 경우에 중단 또는 작업 영역에 영향을 주지 않고 작업 영역에서 솔루션 구성 요소를 제거할 수 있습니다.  

>[!NOTE]
> 작업 영역에서 솔루션 구성 요소를 제거한 후에 Azure Vm의 상태를 확인 하려면 계속 될 수 있습니다. 특히 성능이 하 고 포털의 보기 중 하나를 수행할 때 데이터를 매핑합니다. 데이터는 결국 나타나는 중지 합니다 **성능** 하 고 **지도** 뷰. 하지만 **상태** 보기 계속 Vm에 대 한 상태 표시 됩니다. 합니다 **지금** 모니터링 나중에 다시 사용할 수 있도록 옵션 선택된 된 Azure VM에서 사용할 수 있습니다.  

## <a name="remove-azure-monitor-for-vms-completely"></a>Vm에 대 한 Azure Monitor를 완전히 제거

Log Analytics 작업 영역을 계속 해야 하는 경우에 Vm에 대 한 Azure Monitor를 완전히 제거 하려면 다음이 단계를 수행 합니다. 제거 된 `InfrastructureInsights` 및 `ServiceMap` 작업 영역에서 솔루션입니다.  

>[!NOTE]
>Vm에 대 한 Azure Monitor를 사용 하도록 설정 하 고에 여전히 의존 하기 전에 솔루션을 모니터링 하는 서비스 맵을 사용한 경우 다음 절차의 마지막 단계에 설명 된 대로 해당 솔루션을 제거 하지 마세요.  
>

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. Azure Portal에서 **모든 서비스**를 선택합니다. 리소스 목록에서 **Log Analytics**를 입력합니다. 입력을 시작 하면 입력에 따라 제안 목록을 필터링 합니다. **Log Analytics**를 선택합니다.
3. Log Analytics 작업 영역 목록에 작업 영역을 선택 했다면, Vm에 대 한 Azure Monitor를 사용 하면 됩니다.
4. 왼쪽에서 선택 **솔루션**합니다.  
5. 솔루션의 목록에서 선택 **InfrastructureInsights (작업 영역 이름)** 합니다. 에 **개요** 솔루션을 선택에 대 한 페이지 **삭제**합니다. 선택 확인 대화 상자가 나타나면 **예**합니다.  
6. 솔루션의 목록에서 선택 **(작업 영역 이름)은 ServiceMap**합니다. 에 **개요** 솔루션을 선택에 대 한 페이지 **삭제**합니다. 선택 확인 대화 상자가 나타나면 **예**합니다.  

그렇지 않은 경우 Vm에 대 한 Azure Monitor를 사용 하기 전에 [성능 카운터를 수집](vminsights-enable-overview.md#performance-counters-enabled) 작업 영역에서 Windows 기반 또는 Linux 기반 Vm에 대 한 [규칙을 사용 하지 않도록 설정](../platform/data-sources-performance-counters.md#configuring-performance-counters) Linux 및 Windows에 대 한 합니다.

## <a name="disable-monitoring-and-keep-the-workspace"></a>작업 영역을 두고 모니터링 사용 안 함  

Log Analytics 작업 영역에서 계속 하는 경우 Vm에 대 한 Azure Monitor를 평가 하는 데 사용 하는 VM에 모니터링을 사용 하지 않도록 설정 하려면 다음이 단계를 따라 다른 원본에서 모니터링을 지원 해야 합니다. Azure Vm에 대 한 VM에서 직접 종속성 에이전트 VM 확장 및 Log Analytics 에이전트 VM 확장 Windows 또는 Linux에 제거할 수 있습니다. 

>[!NOTE]
>경우에 Log Analytics 에이전트를 제거 하지 않습니다. 
>
> * Azure Automation 구성 또는 업데이트 관리 또는 프로세스를 오케스트레이션 하는 VM을 관리 합니다. 
> * Azure Security Center는 보안 및 위협 검색에 대 한 VM을 관리 합니다. 
>
> Log Analytics 에이전트를 제거한 경우 해당 서비스 및 솔루션을 수 없게 됩니다에서 사전에 VM을 관리 합니다. 

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 
2. Azure Portal에서 **Virtual Machines**를 선택합니다. 
3. 목록에서 VM을 선택합니다. 
4. 왼쪽에서 선택 **확장**합니다. 에 **Extensions** 페이지에서 **DependencyAgent**합니다.
5. 확장 속성 페이지에서 선택 **제거**합니다.
6. 에 **Extensions** 페이지에서 **MicrosoftMonitoringAgent**합니다. 확장 속성 페이지에서 선택 **제거**합니다.  
