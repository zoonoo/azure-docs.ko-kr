---
title: Azure Monitor의 정보에 대 한 개요 | Microsoft Docs
description: Insights는 특정 응용 프로그램 및 서비스에 대 한 Azure Monitor의 사용자 지정 모니터링 환경을 제공 합니다. 이 문서에서는 현재 사용할 수 있는 각 정보에 대 한 간략 한 설명을 제공 합니다.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/22/2019
ms.openlocfilehash: 3abf3d562b9ca4d6fc9907c065dbaf9a52d58c5f
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555349"
---
# <a name="overview-of-insights-in-azure-monitor"></a>Azure Monitor의 정보 개요
Insights는 특정 응용 프로그램 및 서비스에 대 한 사용자 지정 모니터링 환경을 제공 합니다. [Azure Monitor 데이터 플랫폼](../platform/data-platform.md) 에 데이터를 저장 하 고 분석 및 경고를 위해 다른 Azure Monitor 기능을 활용 하지만, 추가 데이터를 수집 하 고 Azure Portal에서 고유한 사용자 환경을 제공할 수 있습니다. Azure Portal Azure Monitor 메뉴의 **insights** 섹션에서 정보에 액세스 합니다.

다음 섹션에는 현재 Azure Monitor에서 사용할 수 있는 정보에 대 한 간략 한 설명이 나와 있습니다. 각 항목에 대 한 자세한 내용은 자세한 설명서를 참조 하세요.

## <a name="application-insights"></a>Application Insights
Application Insights는 여러 플랫폼의 웹 개발자를 위한 확장 가능한 APM(애플리케이션 성능 관리) 서비스입니다. 이를 사용하여 라이브 웹 애플리케이션을 모니터링합니다. 이 기능은 .NET, node.js 및 Java EE, 온-프레미스, 하이브리드 또는 공용 클라우드를 비롯 한 다양 한 플랫폼에서 응용 프로그램에 사용할 수 있습니다. 또한 DevOps 프로세스와 통합 되며 다양 한 개발 도구에 대 한 연결 지점이 있습니다.

[Application Insights 란?을](../app/app-insights-overview.md)참조 하세요.

![Application Insights](media/insights-overview/app-insights.png)

## <a name="azure-monitor-for-containers"></a>컨테이너 Azure Monitor
컨테이너용 Azure Monitor는 AKS(Azure Kubernetes Service)에 호스트된 Azure Container Instances 또는 관리되는 Kubernetes 클러스터에 배포된 컨테이너 워크로드의 성능을 모니터링합니다. 컨테이너 모니터링은 특히 여러 애플리케이션을 사용하여 대규모의 프로덕션 클러스터를 실행하는 경우에 중요합니다.

[컨테이너에 대 한 Azure Monitor 개요를](../insights/container-insights-overview.md)참조 하세요.

![컨테이너 Azure Monitor](media/insights-overview/container-insights.png)

## <a name="azure-monitor-for-resource-groups-preview"></a>리소스 그룹에 대 한 Azure Monitor (미리 보기)
리소스 그룹에 대 한 Azure Monitor는 전체 리소스 그룹의 상태 및 성능에 대 한 컨텍스트를 제공 하는 동시에 개별 리소스에 발생 하는 문제를 심사 및 진단 하는 데 도움이 됩니다.

[Azure Monitor를 사용 하 여 리소스 그룹 모니터링 (미리 보기)](../insights/resource-group-insights.md)을 참조 하세요.

![리소스 그룹에 대 한 Azure Monitor](media/insights-overview/resource-group-insights.png)

## <a name="azure-monitor-for-vms-preview"></a>VM용 Azure Monitor (미리 보기)
VM용 Azure Monitor는 Azure VM(Virtual Machines) 및 가상 머신 확장 집합을 대규모로 모니터링합니다. 또한 Windows 및 Linux VM의 성능과 상태를 분석하고, 프로세스와 다른 리소스 및 외부 프로세스에 대한 종속성을 모니터링합니다.

[VM용 Azure Monitor 정의를 참조 하세요](vminsights-overview.md) .

![VM용 Azure Monitor](media/insights-overview/vm-insights.png)

## <a name="next-steps"></a>다음 단계
* Insights에서 활용 하는 [Azure Monitor 데이터 플랫폼](../platform/data-platform.md) 에 대해 자세히 알아보세요.
* [Azure Monitor에서 사용](../platform/data-sources.md) 하는 다양 한 데이터 원본 및 각 정보에 의해 수집 된 다양 한 종류의 데이터에 대해 알아봅니다.
