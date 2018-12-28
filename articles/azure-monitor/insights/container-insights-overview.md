---
title: 컨테이너용 Azure Monitor 개요 | Microsoft Docs
description: 이 문서에서는 AKS Container Insights 솔루션을 모니터링하는 컨테이너용 Azure Monitor 및 Azure Monitor가 Azure의 AKS 클러스터 및 Container Instances 상태를 모니터링하여 제공하는 가치에 대해 설명합니다.
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
ms.date: 12/06/2018
ms.author: magoedte
ms.openlocfilehash: 2de3e76714c6e22f16f9aac1e1c9994a835b16e5
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2018
ms.locfileid: "53190223"
---
# <a name="azure-monitor-for-containers-overview"></a>컨테이너용 Azure Monitor 개요

컨테이너용 Azure Monitor는 AKS(Azure Kubernetes Service)에 호스트된 Azure Container Instances 또는 관리되는 Kubernetes 클러스터에 배포된 컨테이너 워크로드의 성능을 모니터링하도록 설계된 기능입니다. 컨테이너 모니터링은 특히 여러 응용 프로그램을 사용하여 대규모의 프로덕션 클러스터를 실행하는 경우에 중요합니다.

컨테이너용 Azure Monitor는 Metrics API를 통해 Kubernetes에서 사용할 수 있는 컨트롤러, 노드 및 컨테이너의 메모리 및 프로세서 메트릭을 수집하여 성능을 시각적으로 표시합니다. 컨테이너 로그도 수집됩니다.  Kubernetes 클러스터에서 모니터링을 사용하도록 설정하면, 이러한 메트릭 및 로그가 Linux용 Log Analytics 에이전트의 컨테이너화된 버전을 통해 자동으로 수집되어 [Log Analytics](../../azure-monitor/log-query/log-query-overview.md) 작업 영역에 저장됩니다. 
 
## <a name="what-does-azure-monitor-for-containers-provide"></a>컨테이너용 Azure Monitor에서 제공하는 기능은 무엇인가요?

컨테이너용 Azure Monitor에는 상주하는 컨테이너 워크로드와 모니터링되는 Kubernetes 클러스터의 성능 상태에 영향을 미치는 요소를 보여 주는 미리 정의된 여러 뷰가 포함되어 있으므로 다음 작업을 수행할 수 있습니다.  

* 노드에서 실행 중인 AKS 컨테이너와 평균 프로세서 및 메모리 사용률을 확인합니다. 이 정보를 통해 리소스 병목 상태를 파악할 수 있습니다.
* Azure Container Instances에 호스트된 컨테이너 그룹 및 해당 컨테이너의 프로세서 및 메모리 사용률을 식별합니다.  * 컨트롤러 또는 Pod에서 컨테이너가 상주하는 위치를 식별합니다. 이 정보를 통해 컨트롤러 또는 Pod의 전반적인 성능을 볼 수 있습니다.
* Pod를 지원하는 표준 프로세스와 관련이 없는 호스트에서 실행되는 워크로드의 리소스 사용률을 검토합니다.
* 평균 부하 및 최고 부하 상태에서.클러스터의 동작을 이해합니다. 이 정보를 통해 용량 요구 사항을 파악하고 클러스터를 유지할 수 있는 최대 부하를 확인할 수 있습니다. 

## <a name="how-do-i-access-this-feature"></a>이 기능에 액세스하려면 어떻게 하나요?
컨테이너용 Azure Monitor는 Azure Monitor에서 액세스하거나 선택한 AKS 클러스터에서 직접 액세스할 수 있습니다. Azure Monitor에서 배포된 모든 컨테이너, 모니터링되는 컨테이너 및 모니터링되지 않는 컨테이너에 대한 전역 큐브 뷰를 사용하면 구독 및 리소스 그룹 전체를 검색하고 필터링한 다음, 선택한 컨테이너의 컨테이너용 Azure Monitor를 상세히 검색할 수 있습니다.  또는 AKS 페이지에서 선택한 AKS 컨테이너의 기능에 바로 액세스할 수 있습니다.  

![컨테이너용 Azure Monitor에 액세스하는 방법에 대한 개요](./media/container-insights-overview/azmon-containers-views-1812.png)

Docker 및 Windows 컨테이너 호스트를 모니터링하고 관리하여 구성, 감사 및 리소스 사용률을 확인하려는 경우 [컨테이너 모니터링 솔루션](../../azure-monitor/insights/containers.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계
AKS 클러스터 모니터링을 시작하려면 [컨테이너용 Azure Monitor를 등록하는 방법](container-insights-onboard.md)을 검토하여 모니터링 사용을 위한 요구 사항과 사용 가능한 방법을 파악하세요.  
