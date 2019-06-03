---
title: Azure Monitor의 Insights 개요 | Microsoft Docs
description: Insights에는 특정 응용 프로그램 및 서비스에 대 한 Azure Monitor에서 사용자 지정된 모니터링 환경을 제공 합니다. 이 문서에서는 각 현재 사용할 수 있는 통찰력의 한 간단한 설명을 제공 합니다.
services: azure-monitor
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: bwren
ms.openlocfilehash: 8cb39a174c570b7019e872d731f49252a9505406
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66247231"
---
# <a name="overview-of-insights-in-azure-monitor"></a>Azure Monitor의 Insights 개요
Insights 특정 응용 프로그램 및 서비스에 대 한 사용자 지정된 모니터링 환경을 제공합니다. 데이터를 저장 합니다 [Azure Monitor 데이터 플랫폼](../platform/data-platform.md) 분석 및 경고에 대 한 다른 Azure Monitor 기능을 활용 하지만 수 추가 데이터를 수집 및 Azure portal에서 고유한 사용자 환경을 제공 합니다. 인 사이트에 액세스 합니다 **Insights** Azure portal에서 Azure Monitor 메뉴의 섹션입니다.

다음 섹션에서는 Azure Monitor에서 현재 사용할 수 있는 정보에 대 한 간략 한 설명을 제공 합니다. 각 세부 정보에 대 한 자세한 설명서를 참조 하세요.

## <a name="application-insights"></a>Application Insights
Application Insights는 여러 플랫폼의 웹 개발자를 위한 확장 가능한 APM(애플리케이션 성능 관리) 서비스입니다. 이를 사용하여 라이브 웹 애플리케이션을 모니터링합니다. 다양 한.NET, Node.js 및 Java EE를 비롯 하 여 플랫폼에서 응용 프로그램에 대 한 작동 온-프레미스, 하이브리드 또는 공용 클라우드에 호스트 합니다. 또한 DevOps 프로세스와 통합 하 고 다양 한 개발 도구를 가리키도록 연결 합니다.

참조 [Application Insights 란?](../app/app-insights-overview.md)합니다.

![Application Insights](media/insights-overview/app-insights.png)

## <a name="azure-monitor-for-containers"></a>컨테이너에 대 한 azure Monitor
컨테이너용 Azure Monitor는 AKS(Azure Kubernetes Service)에 호스트된 Azure Container Instances 또는 관리되는 Kubernetes 클러스터에 배포된 컨테이너 워크로드의 성능을 모니터링합니다. 컨테이너 모니터링은 특히 여러 애플리케이션을 사용하여 대규모의 프로덕션 클러스터를 실행하는 경우에 중요합니다.

참조 [컨테이너 개요에 대 한 Azure Monitor](../insights/container-insights-overview.md)합니다.

![컨테이너에 대 한 azure Monitor](media/insights-overview/container-insights.png)

## <a name="azure-monitor-for-resource-groups-preview"></a>Azure 리소스 그룹 (미리 보기) 모니터링
리소스 그룹에 대 한 azure Monitor 심사 상태 및 리소스 그룹의 전체 성능에 대 한 컨텍스트를 제공 하는 동안 개별 리소스에 발생 하는 모든 문제를 진단 하는 데 도움이 됩니다.

참조 [Azure Monitor (미리 보기)를 사용 하 여 리소스 그룹을 모니터링](../insights/resource-group-insights.md)합니다.

![리소스 그룹에 대 한 azure Monitor](media/insights-overview/resource-group-insights.png)

## <a name="azure-monitor-for-vms-preview"></a>Azure Vm (미리 보기) 모니터링
VM용 Azure Monitor는 Azure VM(Virtual Machines) 및 가상 머신 확장 집합을 대규모로 모니터링합니다. 또한 Windows 및 Linux VM의 성능과 상태를 분석하고, 프로세스와 다른 리소스 및 외부 프로세스에 대한 종속성을 모니터링합니다.

참조 [Vm에 대 한 Azure Monitor 란?](vminsights-overview.md)

![VM용 Azure Monitor](media/insights-overview/vm-insights.png)

## <a name="next-steps"></a>다음 단계
* 에 대 한 자세한 정보는 [Azure Monitor 데이터 플랫폼](../platform/data-platform.md) insights에서 활용 합니다.
* 다양 한 알아봅니다 [Azure Monitor에서 사용 되는 데이터 원본을](../platform/data-sources.md) 및 각 정보에 의해 수집 된 데이터의 다양 한 종류.
