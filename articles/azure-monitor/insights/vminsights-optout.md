---
title: VM에 대한 Azure 모니터에서 모니터링 을 사용하지 않도록 설정
description: 이 문서에서는 VM용 Azure 모니터에서 가상 컴퓨터 모니터링을 중지하는 방법을 설명합니다.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: 80473aa494b8fbcea5e43870b7717cd3472dd7d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480524"
---
# <a name="disable-monitoring-of-your-vms-in-azure-monitor-for-vms"></a>VM용 Azure 모니터에서 VM 모니터링 을 비활성화

가상 시스템(VM)의 모니터링을 사용하도록 설정한 후 나중에 VM용 Azure 모니터에서 모니터링을 사용하지 않도록 선택할 수 있습니다. 이 문서에서는 하나 이상의 VM에 대한 모니터링을 사용하지 않도록 설정하는 방법을 보여 주며 있습니다.  

현재 VM용 Azure 모니터는 VM 모니터링의 선택적 비활성화를 지원하지 않습니다. 로그 분석 작업 영역에서 VM 및 기타 솔루션에 대한 Azure 모니터를 지원할 수 있습니다. 다른 모니터링 데이터도 수집할 수 있습니다. Log Analytics 작업 영역에서 이러한 서비스를 제공하는 경우 시작하기 전에 모니터링을 비활성화하는 효과와 방법을 이해해야 합니다.

VM용 Azure Monitor는 해당 환경을 제공하기 위해 다음 구성 요소를 사용합니다.

* VM 및 기타 소스의 모니터링 데이터를 저장하는 로그 분석 작업 영역입니다.
* 작업 영역에서 구성된 성능 카운터의 컬렉션입니다. 컬렉션은 작업 영역에 연결된 모든 VM에서 모니터링 구성을 업데이트합니다.
* `VMInsights`은 작업 영역에서 구성된 모니터링 솔루션입니다. 이 솔루션은 작업 영역에 연결된 모든 VM에서 모니터링 구성을 업데이트합니다.
* `MicrosoftMonitoringAgent`및 `DependencyAgent`Azure VM 확장입니다. 이러한 확장은 데이터를 수집하고 작업 영역으로 보냅니다.

VM 모니터링을 비활성화할 준비를 할 때 다음 사항을 염두에 두어야 합니다.

* 단일 VM으로 평가하고 미리 선택된 기본 Log Analytics 작업 영역을 사용한 경우 VM에서 종속성 에이전트를 제거하고 이 작업 영역에서 Log Analytics 에이전트를 연결 해제하여 모니터링을 비활성화할 수 있습니다. 이 방법은 VM을 다른 용도로 사용하고 나중에 다른 작업 영역에 다시 연결하기로 결정한 경우에 적합합니다.
* 다른 소스에서 다른 모니터링 솔루션 및 데이터 수집을 지원하는 기존 Log Analytics 작업 영역을 선택한 경우 작업 영역을 중단하거나 영향을 주지 않고 작업 영역에서 솔루션 구성 요소를 제거할 수 있습니다.  

>[!NOTE]
> 작업 영역에서 솔루션 구성 요소를 제거한 후에도 Azure VM에 대한 성능 및 매핑 데이터를 계속 볼 수 있습니다. 결국 데이터는 **성능** 및 **맵** 보기에 나타나지 않습니다. 나중에 모니터링을 다시 활성화할 수 있도록 선택한 Azure VM에서 **사용** 옵션을 사용할 수 있습니다.  

## <a name="remove-azure-monitor-for-vms-completely"></a>VM용 Azure 모니터 를 완전히 제거합니다.

여전히 로그 분석 작업 영역이 필요한 경우 다음 단계에 따라 VM용 Azure 모니터를 완전히 제거합니다. 작업 영역에서 `VMInsights` 솔루션을 제거합니다.  

1. [Azure 포털에](https://portal.azure.com)로그인합니다.
2. Azure 포털에서 **모든 서비스를**선택합니다. 리소스 목록에서 **로그 분석을**입력합니다. 입력을 시작하면 목록에서 입력에 따라 제안 사항이 필터링됩니다. **로그 분석을 선택합니다.**
3. 로그 분석 작업 영역 목록에서 VM에 대한 Azure 모니터를 사용하도록 설정할 때 선택한 작업 영역을 선택합니다.
4. 왼쪽에서 **솔루션**을 선택합니다.  
5. 솔루션 목록에서 **VMInsights(작업 영역 이름)를**선택합니다. 솔루션의 **개요** 페이지에서 **삭제를**선택합니다. 확인 하라는 메시지가 표시되면 **예**.

## <a name="disable-monitoring-and-keep-the-workspace"></a>모니터링 을 비활성화하고 작업 영역 유지  

Log Analytics 작업 영역에서 여전히 다른 소스의 모니터링을 지원해야 하는 경우 다음 단계에 따라 VM에 대한 Azure 모니터를 평가하는 데 사용한 VM에 대한 모니터링을 사용하지 않도록 설정합니다. Azure VM의 경우 종속성 에이전트 VM 확장 및 Windows 또는 Linux용 로그 분석 에이전트 VM 확장을 VM에서 직접 제거합니다. 

>[!NOTE]
>다음과 같은 경우에는 로그 분석 에이전트를 제거하지 마십시오. 
>
> * Azure Automation은 VM을 관리하여 프로세스를 오케스트레이션하거나 구성 또는 업데이트를 관리합니다. 
> * Azure 보안 센터는 보안 및 위협 검색을 위해 VM을 관리합니다. 
>
> Log Analytics 에이전트를 제거하면 해당 서비스 및 솔루션이 VM을 사전에 관리하지 못하도록 방지할 수 있습니다. 

1. [Azure 포털에](https://portal.azure.com)로그인합니다. 
2. Azure Portal에서 **Virtual Machines**를 선택합니다. 
3. 목록에서 VM을 선택합니다. 
4. 왼쪽에서 확장을 **선택합니다.** 확장 페이지에서 **종속성 에이전트를** **선택합니다.**
5. 확장 속성 페이지에서 **제거를**선택합니다.
6. 확장 페이지에서 **Microsoft MonitoringAgent를** **선택합니다.** 확장 속성 페이지에서 **제거를**선택합니다.  
