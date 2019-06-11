---
title: Azure 컨테이너 모니터링 개요 | Microsoft Docs
description: 이 문서에서는 클러스터의 상태와 가용성을 빠르게 파악하기 위해 Azure에서 컨테이너를 모니터링하는 데 사용할 수 있는 여러 가지 방법에 대해 간략히 설명합니다.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: magoedte
ms.openlocfilehash: d137576b4beb5cf36dce99ffb1869049f37b60b2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60494646"
---
# <a name="overview-of-monitoring-containers-in-azure"></a>Azure의 컨테이너 모니터링 개요
Azure를 사용하면 Kubernetes 또는 Docker를 실행하는 Azure 컨테이너에 배포된 작업을 효과적으로 모니터링하고 관리할 수 있습니다. 안정적인 서비스를 규모에 맞게 제공하고 모니터링 계획을 지원하기 위해 여러 마이크로 서비스 애플리케이션이 있는 컨테이너를 수행하는 방법을 이해해야 합니다. 이 문서에서는 요구 사항에 따라 적절한 기능을 이해할 수 있도록 Azure의 관리 및 모니터링 기능에 대해 간략히 설명합니다.

[컨테이너용 Azure Monitor](container-insights-overview.md)를 사용하면 Linux 컨테이너 인프라의 성능과 상태를 한 눈에 보고 문제를 빠르게 조사할 수 있습니다. 원격 분석은 Log Analytics 작업 영역에 저장되고 Azure Portal에 통합되어 있습니다. 여기서는 대시보드를 통해 집계된 데이터를 탐색, 필터링 및 분할하여 로드, 성능 및 상태를 계속 감시할 수 있습니다.  

호스팅된 Azure Kubernetes 서비스 외부에서 실행되는 컨테이너의 경우, Log Analytics [Windows 및 Docker 컨테이너 솔루션](../../azure-monitor/insights/containers.md)을 사용하여 Windows 및 Docker 컨테이너 호스트를 보고 관리할 수 있습니다. Log Analytics 작업 영역에서는 환경의 노드 및 컨테이너에서 인벤토리 세부 정보, 성능 및 이벤트를 볼 수 있습니다. 컨테이너와 함께 사용되는 명령을 보여주는 자세한 감사 정보를 볼 수 있으며, Docker 또는 Windows 호스트에 원격으로 액세스하지 않고도 중앙 집중식 로그를 보고 검색하여 컨테이너 문제를 해결할 수 있습니다.

애플리케이션에 대한 전체적 또는 엔드투엔드 모니터링을 수행하려면 Azure 또는 온-프레미스 리소스인지 여부에 관계없이 Azure Monitor 또는 Log Analytics를 사용하여 모니터링해야 합니다.  Application Insights를 사용하는 플랫폼 및 애플리케이션 수준 모두에서 추가 상태 인식 계층을 추가하려면 애플리케이션 계층이 포함되어야 합니다. 플랫폼 수준에서는 [Kubernetes]( https://github.com/Microsoft/ApplicationInsights-Kubernetes), [Docker](https://hub.docker.com/r/microsoft/applicationinsights/) 및 [Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-analysis-appinsights)에 대한 Application Insights SDK가 있습니다. 마이크로 서비스 응용 프로그램에 대 한 지원은 [Java](../../azure-monitor/app/java-get-started.md), [Node.js](../../azure-monitor/learn/nodejs-quick-start.md), [.NET](../../azure-monitor/app/asp-net.md)하십시오 [.NET Core](../../azure-monitor/app/asp-net-core.md), 및 다양 한 다른 [언어/프레임 워크](../../azure-monitor/app/platforms.md)합니다. 

그렇지 않으면 애플리케이션의 가용성에 영향을 줄 수 있는 문제가 확인되지 않고, 서비스 수준 목표가 충족되지 않습니다.  
