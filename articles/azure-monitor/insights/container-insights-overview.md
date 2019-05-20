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
ms.date: 04/25/2019
ms.author: magoedte
ms.openlocfilehash: d6321564672097fbf901d1d33afac9f606fcb63a
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2019
ms.locfileid: "65521826"
---
# <a name="azure-monitor-for-containers-overview"></a>컨테이너용 Azure Monitor 개요

컨테이너용 Azure Monitor는 AKS(Azure Kubernetes Service)에 호스트된 Azure Container Instances 또는 관리되는 Kubernetes 클러스터에 배포된 컨테이너 워크로드의 성능을 모니터링하도록 설계된 기능입니다. 컨테이너 모니터링은 특히 여러 애플리케이션을 사용하여 대규모의 프로덕션 클러스터를 실행하는 경우에 중요합니다.

컨테이너용 Azure Monitor는 Metrics API를 통해 Kubernetes에서 사용할 수 있는 컨트롤러, 노드 및 컨테이너의 메모리 및 프로세서 메트릭을 수집하여 성능을 시각적으로 표시합니다. 컨테이너 로그도 수집됩니다.  Kubernetes 클러스터 모니터링을 활성화 한 후 메트릭 및 로그는 자동으로 수집 Linux 용 Log analytics의 컨테이너 화 된 버전을 통해. 메트릭 메트릭 저장소에 기록 되 고 로그 데이터와 관련 된 로그 저장소에 기록 됩니다 하 [Log Analytics](../log-query/log-query-overview.md) 작업 영역입니다. 

![Azure Monitor 컨테이너 아키텍처](./media/container-insights-overview/azmon-containers-architecture-01.png)
 
## <a name="what-does-azure-monitor-for-containers-provide"></a>컨테이너용 Azure Monitor에서 제공하는 기능은 무엇인가요?

Azure Monitor 컨테이너에 대 한 성능 및 Kubernetes 클러스터 및 컨테이너 워크 로드의 상태를 이해 하는 데 사용할 수 Azure Monitor의 다양 한 기능을 사용 하 여 포괄적인 모니터링 환경을 제공 합니다. 컨테이너에 대 한 Azure Monitor를 사용 하 여 다음을 수행할 수 있습니다.

* 노드에서 실행 중인 AKS 컨테이너와 평균 프로세서 및 메모리 사용률을 확인합니다. 이 정보를 통해 리소스 병목 상태를 파악할 수 있습니다.
* Azure Container Instances에 호스트된 컨테이너 그룹 및 해당 컨테이너의 프로세서 및 메모리 사용률을 식별합니다.  
* 컨트롤러 또는 Pod에서 컨테이너가 상주하는 위치를 확인합니다. 이 정보를 통해 컨트롤러 또는 Pod의 전반적인 성능을 볼 수 있습니다.
* Pod를 지원하는 표준 프로세스와 관련이 없는 호스트에서 실행되는 워크로드의 리소스 사용률을 검토합니다.
* 평균 부하 및 최고 부하 상태에서.클러스터의 동작을 이해합니다. 이 정보를 통해 용량 요구 사항을 파악하고 클러스터를 유지할 수 있는 최대 부하를 확인할 수 있습니다. 
* 사전에 알리거나 노드 또는 컨테이너의 CPU 및 메모리 사용률에 임계값을 초과 하는 경우 기록에 대 한 경고를 구성 합니다.  

## <a name="how-do-i-access-this-feature"></a>이 기능에 액세스하려면 어떻게 하나요?
컨테이너용 Azure Monitor는 Azure Monitor에서 액세스하거나 선택한 AKS 클러스터에서 직접 액세스할 수 있습니다. Azure Monitor에서 모든 컨테이너 배포를 모니터링 하는 장치와 아닌 장치를 사용할 수 있도록 검색 및 필터에는 구독 및 리소스 그룹에서의 컨테이너에 대 한 Azure Monitor를 살펴볼 글로벌 큐브 뷰에 해야 합니다 선택한 컨테이너입니다.  그렇지 않으면 기능 AKS 페이지에서 선택된 된 AKS 컨테이너에서 직접 액세스할 수 있습니다.  

![컨테이너용 Azure Monitor에 액세스하는 방법에 대한 개요](./media/container-insights-overview/azmon-containers-experience.png)

Docker 및 Windows 컨테이너 호스트를 모니터링하고 관리하여 구성, 감사 및 리소스 사용률을 확인하려는 경우 [컨테이너 모니터링 솔루션](../../azure-monitor/insights/containers.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계
AKS 클러스터를 모니터링 하려면 검토 [컨테이너에 대 한 Azure Monitor를 사용 하도록 설정 하는 방법을](container-insights-onboard.md) 모니터링을 사용 하도록 요구 사항 및 사용할 수 있는 메서드를 알아야 합니다.  
