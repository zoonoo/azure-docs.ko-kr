---
title: Azure Monitor의 인사이트 개요 | Microsoft Docs
description: 인사이트는 특정 응용 프로그램 및 서비스에 대해 Azure Monitor에서 사용자 지정 모니터링 환경을 제공합니다. 이 문서에서는 현재 사용 가능한 각 인사이트에 대한 간략한 설명을 제공합니다.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/22/2019
ms.openlocfilehash: e3eefb97c85b3865abbf116b9912dc45f6db8ce8
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87326871"
---
# <a name="overview-of-insights-in-azure-monitor"></a>Azure Monitor의 인사이트 개요
인사이트는 특정 응용 프로그램 및 서비스에 대한 사용자 지정 모니터링 환경을 제공합니다. Insights는 [Azure Monitor 데이터 플랫폼](../platform/data-platform.md)에 데이터를 저장하고 분석 및 경고를 위해 다른 Azure Monitor 기능을 활용합니다. 그러나 추가 데이터를 수집하고 Azure Portal에서 고유한 사용자 경험을 제공할 수 있습니다. Azure Portal의 Azure Monitor 메뉴에 있는 **Insights** 섹션에서 insights에 액세스합니다.


다음 섹션에서는 현재 Azure Monitor에서 제공되는 인사이트에 대해 간단히 설명합니다. 각 항목에 대한 세부 정보는 자세한 설명서를 참조하세요.

## <a name="application-insights"></a>Application Insights
Application Insights는 여러 플랫폼의 웹 개발자를 위한 확장 가능한 APM(애플리케이션 성능 관리) 서비스입니다. 이를 사용하여 라이브 웹 애플리케이션을 모니터링합니다. .NET, Node.js 및 Java EE, 호스트된 온-프레미스, 하이브리드 또는 퍼블릭 클라우드를 포함하여 다양한 플랫폼에서 응용 프로그램과 함께 작동합니다. 또한 DevOps 프로세스와 통합되며, 다양한 개발 도구와의 연결 지점을 갖고 있습니다.

[Application Insights란?](../app/app-insights-overview.md)

![Application Insights](media/insights-overview/app-insights.png)

## <a name="azure-monitor-for-containers"></a>컨테이너용 Azure Monitor
컨테이너용 Azure Monitor는 AKS(Azure Kubernetes Service)에 호스트된 Azure Container Instances 또는 관리되는 Kubernetes 클러스터에 배포된 컨테이너 워크로드의 성능을 모니터링합니다. 컨테이너 모니터링은 특히 여러 애플리케이션을 사용하여 대규모의 프로덕션 클러스터를 실행하는 경우에 중요합니다.

[컨테이너용 Azure Monitor 개요](./container-insights-overview.md)를 참조하세요.

![컨테이너용 Azure Monitor](media/insights-overview/container-insights.png)

## <a name="azure-monitor-for-resource-groups-preview"></a>리소스 그룹용 Azure Monitor(미리 보기)
리소스 그룹용 Azure Monitor를 통해 리소스 그룹의 상태 및 성능과 관련된 컨텍스트를 전체적으로 제공하면서 개별 리소스에 발생하는 문제를 쉽게 심사하고 진단할 수 있습니다.

[Azure Monitor(미리 보기)를 사용하여 리소스 그룹 모니터링](./resource-group-insights.md)을 참조하세요.

![리소스 그룹용 Azure Monitor](media/insights-overview/resource-group-insights.png)

## <a name="azure-monitor-for-vms-preview"></a>VM용 Azure Monitor(미리 보기)
VM용 Azure Monitor는 Azure VM(Virtual Machines) 및 가상 머신 확장 집합을 대규모로 모니터링합니다. 또한 Windows 및 Linux VM의 성능과 상태를 분석하고, 프로세스와 다른 리소스 및 외부 프로세스에 대한 종속성을 모니터링합니다.

[VM용 Azure Monitor란?](vminsights-overview.md)을 참조하세요.

![VM용 Azure Monitor](media/insights-overview/vm-insights.png)

## <a name="azure-monitor-for-networks-preview"></a>네트워크용 Azure Monitor(미리 보기)
[네트워크용 Azure Monitor](network-insights-overview.md)는 모든 네트워크 리소스에 대한 상태 및 메트릭의 포괄적인 보기를 제공합니다. 고급 검색 기능은 리소스 종속성을 식별하는 데 유용하여 웹 사이트 이름을 검색하기만 하면 웹 사이트를 호스트하는 리소스를 식별하는 등의 시나리오를 사용할 수 있습니다.

![Networks용 Azure Monitor](media/insights-overview/network-insights.png)

## <a name="next-steps"></a>다음 단계
* 인사이트를 활용한 [Azure Monitor 데이터 플랫폼](../platform/data-platform.md)에 대해 자세히 알아봅니다.
* [Azure Monitor에서 사용하는 다양한 데이터 원본](../platform/data-sources.md)과 각 인사이트를 통해 수집된 여러 종류의 데이터에 대해 알아봅니다.

