---
title: Azure 모니터의 인사이트 개요 | 마이크로 소프트 문서
description: Insights는 특정 응용 프로그램 및 서비스에 대한 Azure Monitor에서 사용자 지정된 모니터링 환경을 제공합니다. 이 문서에서는 현재 사용 가능한 각 인사이트에 대한 간략한 설명을 제공합니다.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/22/2019
ms.openlocfilehash: 15ea7698c9e90fa8b0dfa20f71b552a2b0e9c7d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77657251"
---
# <a name="overview-of-insights-in-azure-monitor"></a>Azure 모니터의 인사이트 개요
Insights는 특정 응용 프로그램 및 서비스에 대한 맞춤형 모니터링 환경을 제공합니다. [Azure Monitor 데이터 플랫폼에](../platform/data-platform.md) 데이터를 저장하고 다른 Azure Monitor 기능을 활용하여 분석 및 경고를 제공하지만 추가 데이터를 수집하고 Azure Portal에서 고유한 사용자 환경을 제공할 수 있습니다. Azure 포털의 Azure 모니터 메뉴의 **인사이트** 섹션에서 정보에 액세스합니다.

다음 섹션에서는 Azure Monitor에서 현재 사용할 수 있는 인사이트에 대한 간략한 설명을 제공합니다. 각각에 대한 자세한 설명서를 참조하십시오.

## <a name="application-insights"></a>애플리케이션 정보
Application Insights는 여러 플랫폼의 웹 개발자를 위한 확장 가능한 APM(애플리케이션 성능 관리) 서비스입니다. 이를 사용하여 라이브 웹 애플리케이션을 모니터링합니다. .NET, Node.js 및 Java EE, 호스팅 온-프레미스, 하이브리드 또는 퍼블릭 클라우드를 비롯한 다양한 플랫폼에서 애플리케이션에 사용할 수 있습니다. 또한 DevOps 프로세스와 통합되며 다양한 개발 도구에 대한 연결 지점이 있습니다.

[응용 프로그램 인사이트는 무엇입니까?](../app/app-insights-overview.md).

![애플리케이션 정보](media/insights-overview/app-insights.png)

## <a name="azure-monitor-for-containers"></a>컨테이너용 Azure Monitor
컨테이너용 Azure Monitor는 AKS(Azure Kubernetes Service)에 호스트된 Azure Container Instances 또는 관리되는 Kubernetes 클러스터에 배포된 컨테이너 워크로드의 성능을 모니터링합니다. 컨테이너 모니터링은 특히 여러 애플리케이션을 사용하여 대규모의 프로덕션 클러스터를 실행하는 경우에 중요합니다.

[컨테이너 개요는 Azure 모니터를](../insights/container-insights-overview.md)참조하십시오.

![컨테이너용 Azure Monitor](media/insights-overview/container-insights.png)

## <a name="azure-monitor-for-resource-groups-preview"></a>리소스 그룹에 대한 Azure 모니터(미리 보기)
리소스 그룹에 대한 Azure Monitor는 리소스 그룹의 상태 및 성능에 대한 컨텍스트를 제공하면서 개별 리소스가 발생하는 문제를 분류하고 진단하는 데 도움이 됩니다.

[Azure 모니터(미리 보기)를 사용 하 여 리소스 그룹 모니터링을](../insights/resource-group-insights.md)참조 합니다.

![리소스 그룹에 대한 Azure 모니터](media/insights-overview/resource-group-insights.png)

## <a name="azure-monitor-for-vms-preview"></a>VM용 Azure 모니터(미리 보기)
VM용 Azure Monitor는 Azure VM(Virtual Machines) 및 가상 머신 확장 집합을 대규모로 모니터링합니다. 또한 Windows 및 Linux VM의 성능과 상태를 분석하고, 프로세스와 다른 리소스 및 외부 프로세스에 대한 종속성을 모니터링합니다.

[VM용 Azure 모니터란 무엇입니까?](vminsights-overview.md)

![VM용 Azure Monitor](media/insights-overview/vm-insights.png)

## <a name="azure-monitor-for-networks-preview"></a>네트워크를 위한 Azure 모니터(미리 보기)
[네트워크에 대한 Azure 모니터는](network-insights-overview.md) 모든 네트워크 리소스에 대한 상태 및 메트릭에 대한 포괄적인 보기를 제공합니다. 고급 검색 기능을 사용하면 리소스 종속성을 식별할 수 있으므로 웹 사이트 이름을 검색하기만 하면 웹 사이트를 호스팅하는 리소스를 식별하는 등의 시나리오가 가능합니다.

![Networks용 Azure Monitor](media/insights-overview/network-insights.png)

## <a name="next-steps"></a>다음 단계
* 인사이트를 활용한 [Azure Monitor 데이터 플랫폼에](../platform/data-platform.md) 대해 자세히 알아봅니다.
* [Azure Monitor에서 사용하는](../platform/data-sources.md) 다양한 데이터 원본과 각 인사이트에 의해 수집된 다양한 종류의 데이터에 대해 알아봅니다.
