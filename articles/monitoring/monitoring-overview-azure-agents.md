---
title: Azure 모니터링 에이전트 개요 | Microsoft Docs
description: 이 아티클에서는 Azure VM을 모니터링하도록 지원하는 Azure 에이전트의 자세한 개요를 제공합니다.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2018
ms.author: magoedte
ms.openlocfilehash: 81db6720422de111cc5b390c58e9020d7c19f90a
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2018
ms.locfileid: "51282036"
---
# <a name="overview-of-the-azure-agents-to-monitor-azure-virtual-machines"></a>Azure Virtual Machines를 모니터링하는 Azure 에이전트 개요
Microsoft Azure에서는 Microsoft Windows 및 Linux를 실행하는 Azure 또는 다른 클라우드 공급자에서 호스팅되는 Virtual Machines에서 다양한 형식의 데이터를 수집하는 여러 방법을 제공합니다.  이 아티클에서는 서비스 관리 또는 일반 모니터링 요구 사항을 지원할 에이전트를 결정하기 위해 각 에이전트의 차이점 및 지원되는 기능을 설명합니다.  

## <a name="comparing-azure-diagnostic-and-log-analytics-agent"></a>Azure 진단 및 Log Analytics 에이전트 비교
현재 Azure에는 Azure VM을 모니터링할 수 있는 Linux 및 Windows용 Azure 진단 확장 및 Log Analytics 에이전트라는 두 가지 형식의 에이전트가 있습니다.  근본적으로 이러한 에이전트는 메트릭 및 로그를 수집하고 리포지토리에 전달하도록 설계되었습니다. 그러나 이를 제외하면 두 서비스는 유사하지 않습니다.  

2010년에 지원된 이후로 Azure Cloud Services에 대해 제공되는 [Azure 진단 확장](../monitoring-and-diagnostics/azure-diagnostics.md)은 VM과 같은 Azure IaaS 리소스에서 진단 데이터의 간단한 컬렉션을 제공하고 Azure Storage에 저장하는 에이전트입니다.  저장소에 저장되면 [Visual Studio의 서버 탐색기](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage) 및 [Azure Storage 탐색기](../vs-azure-tools-storage-manage-with-storage-explorer.md)와 같이 지원되는 여러 도구 중 하나를 사용하여 볼 수 있습니다.

다음을 수집하도록 선택할 수 있습니다.

* 운영 체제 성능 카운터 및 이벤트 로그의 미리 정의된 집합 또는 수집할 항목을 지정할 수 있습니다. 
* 모든 요청 및/또는 IIS 웹 서버에 대해 실패한 요청
* 출력 로그를 추적하는 .NET 앱
* Windows(ETW) 이벤트에 대해 추적하는 이벤트 
* syslog에서 로그 이벤트 수집  
* 크래시 덤프 

또는 [Application Insights](../application-insights/app-insights-cloudservices.md)를 [Log Analytics](../log-analytics/log-analytics-queries.md) 또는 [Event Hub](../event-hubs/event-hubs-about.md)를 사용하는 비 Azure 서비스에 데이터를 전달할 수 있습니다. 

메트릭 및 로그의 하위 집합을 수집하는 것 이외의 기능이 필요한 고급 모니터링의 경우 Windows 및 Linux용 Log Analytics 에이전트가 필요합니다.  이 에이전트에서는 제공되는 전체 기능을 비롯해 Automation 및 Log Analytics와 같은 Azure 서비스를 사용하여 수명 주기 동안 Azure VM의 포괄적인 관리를 제공할 수 있습니다. 다음 내용이 포함됩니다.

* 운영 체제 업데이트의 [Azure Automation 업데이트 관리](../automation/automation-update-management.md)
* 일관된 구성 상태를 유지 관리하는 [Azure Automation 필요한 상태 구성](../automation/automation-dsc-overview.md)
* [Azure Automation 변경 내용 추적 및 인벤토리](../automation/automation-change-tracking.md)를 사용하여 구성 변경 내용 추적
* Log Analytics를 사용하는 [FluentD](../log-analytics/log-analytics-data-sources-json.md), [사용자 지정 로그](../log-analytics/log-analytics-data-sources-custom-logs.md), [MySQL 및 Apache](../log-analytics/log-analytics-data-sources-linux-applications.md)와 같은 OS의 사용자 지정 로그 및 호스트 응용 프로그램 수집
* [Application Insights](https://docs.microsoft.com/azure/application-insights/) 및 [Azure Security Center](https://docs.microsoft.com/azure/security-center/)와 같은 Azure 서비스는 기본적으로 Log Analytics에 직접 데이터를 저장합니다.  

## <a name="next-steps"></a>다음 단계

- 데이터 센터 또는 다른 클라우드 환경에 있는 컴퓨터에 에이전트를 배포하기 위해 요구 사항 및 지원되는 메서드를 보려면 [Log Analytics를 사용하여 환경의 컴퓨터에서 데이터 수집](../log-analytics/log-analytics-concept-hybrid.md)을 참조하세요.
- Azure VM에서 데이터 수집을 구성하려면 [Azure Virtual Machines에 대한 데이터 수집](../log-analytics/log-analytics-quick-collect-azurevm.md)을 참조하세요. 
