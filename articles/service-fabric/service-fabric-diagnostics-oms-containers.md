---
title: Log Analytics로 Azure Service Fabric에서 컨테이너 모니터링 | Microsoft Docs
description: Azure Service Fabric 클러스터에서 실행 중인 컨테이너를 모니터링하려면 Log Analytics를 사용하십시오.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/1/2017
ms.author: dekapur
ms.openlocfilehash: 7a775b6d23c144c81650bb3608ee6a117475a9ba
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="monitor-containers-with-log-analytics"></a>Log Analytics를 사용하여 컨테이너 모니터링
 
이 문서에서는 클러스터에 대해 컨테이너는 모니터링을 설정하는 데 필요한 단계를 설명합니다. 이에 대한 자세한 내용은 [Service Fabric에서 컨테이너 모니터링](service-fabric-diagnostics-event-analysis-oms.md#monitoring-containers)을 참조하세요. 이에 대한 단계별 자습서를 보려면 [Service Fabric에서 Windows 컨테이너 모니터링](service-fabric-tutorial-monitoring-wincontainers.md)을 참조하세요.

## <a name="set-up-the-container-monitoring-solution"></a>컨테이너 모니터링 솔루션 설정

> [!NOTE]
> 클러스터에 Log Analytics를 설정하고 노드에 OMS 에이전트를 배포해야 합니다. 그렇지 않으면 [Log Analytics 설정](service-fabric-diagnostics-oms-setup.md) 및 [OMS Agent를 클러스터에 추가](service-fabric-diagnostics-oms-agent.md)의 단계를 먼저 수행합니다.

1. 클러스터가 Log Analytics 및 OMS 에이전트로 설정되었으면 컨테이너를 배포합니다. 다음 단계로 이동하기 전에 컨테이너가 배포될 때까지 기다립니다.

2. Azure Marketplace에서 *컨테이너 모니터링 솔루션*을 검색하고 모니터링 + 관리 범주 아래에 나타나는 **컨테이너 모니터링 솔루션** 리소스를 클릭합니다.

    ![컨테이너 솔루션 추가](./media/service-fabric-diagnostics-event-analysis-oms/containers-solution.png)

3. 클러스터에 대해 이미 작성된 동일한 작업 영역 내부에 솔루션을 만듭니다. 이렇게 변경하면 컨테이너에서 docker 데이터 수집을 시작하는 에이전트가 자동으로 트리거됩니다. 약 15분 정도 동안 로그와 통계가 들어오는 것으로 솔루션에 불이 들어옵니다.

## <a name="next-steps"></a>다음 단계
* Service Fabric - [Service Fabric 및 컨테이너](service-fabric-containers-overview.md)에서 컨테이너 오케스트레이션에 대해 자세히 알아보세요.
* Log Analytics의 일부로 제공되는 [로그 검색 및 쿼리](../log-analytics/log-analytics-log-searches.md) 기능 알아보기
* 검색 및 진단에 도움이 되는 [자동 경고](../log-analytics/log-analytics-alerts.md) 규칙을 설정하도록 Log Analytics를 구성합니다.