---
title: 컨테이너용 Azure Monitor 개요 | Microsoft Docs
description: 이 문서에서는 AKS Container Insights 솔루션을 모니터링하는 컨테이너용 Azure Monitor 및 Azure Monitor가 Azure의 AKS 클러스터 및 Container Instances 상태를 모니터링하여 제공하는 가치에 대해 설명합니다.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 3ff2c35ae9f5838447ce90e2a020649427920a43
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275231"
---
# <a name="azure-monitor-for-containers-overview"></a>컨테이너용 Azure Monitor 개요

컨테이너용 Azure Monitor는 다음에 배포된 컨테이너 워크로드의 성능을 모니터링하도록 설계된 기능입니다.

- [Azure Kubernetes 서비스(AKS)에서](../../aks/intro-kubernetes.md) 호스팅되는 관리형 Kubernetes 클러스터
- [AKS 엔진을](https://github.com/Azure/aks-engine) 사용하여 Azure에서 호스팅되는 자체 관리 Kubernetes 클러스터
- [Azure Container Instances](../../container-instances/container-instances-overview.md)
- [Azure 스택](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1910) 또는 온-프레미스에서 호스팅되는 자체 관리 Kubernetes 클러스터
- [Azure Red Hat OpenShift](../../openshift/intro-openshift.md)

컨테이너용 Azure Monitor는 Linux 및 Windows Server 2019 운영 체제를 실행하는 클러스터를 지원합니다. 

컨테이너 모니터링은 특히 여러 애플리케이션을 사용하여 대규모의 프로덕션 클러스터를 실행하는 경우에 중요합니다.

컨테이너용 Azure Monitor는 Metrics API를 통해 Kubernetes에서 사용할 수 있는 컨트롤러, 노드 및 컨테이너의 메모리 및 프로세서 메트릭을 수집하여 성능을 시각적으로 표시합니다. 컨테이너 로그도 수집됩니다.  Kubernetes 클러스터에서 모니터링을 활성화하면 Linux용 로그 분석 에이전트의 컨테이너화된 버전을 통해 메트릭 및 로그가 자동으로 수집됩니다. 메트릭은 메트릭 저장소에 기록되고 로그 데이터는 [Log Analytics](../log-query/log-query-overview.md) 작업 영역과 연결된 로그 저장소에 기록됩니다. 

![컨테이너 아키텍처에 대한 Azure 모니터](./media/container-insights-overview/azmon-containers-architecture-01.png)
 
## <a name="what-does-azure-monitor-for-containers-provide"></a>컨테이너용 Azure Monitor에서 제공하는 기능은 무엇인가요?

컨테이너용 Azure Monitor는 Azure Monitor의 다양한 기능을 사용하여 포괄적인 모니터링 환경을 제공합니다. 이러한 기능을 사용하면 Linux 및 Windows Server 2019 운영 체제를 실행하는 Kubernetes 클러스터의 성능과 상태와 컨테이너 워크로드를 이해할 수 있습니다. 컨테이너용 Azure 모니터를 사용하면 다음을 수행할 수 있습니다.

* 노드에서 실행 중인 AKS 컨테이너와 평균 프로세서 및 메모리 사용률을 확인합니다. 이 정보를 통해 리소스 병목 상태를 파악할 수 있습니다.
* Azure Container Instances에 호스트된 컨테이너 그룹 및 해당 컨테이너의 프로세서 및 메모리 사용률을 식별합니다.  
* 컨트롤러 또는 Pod에서 컨테이너가 상주하는 위치를 확인합니다. 이 정보를 통해 컨트롤러 또는 Pod의 전반적인 성능을 볼 수 있습니다. 
* Pod를 지원하는 표준 프로세스와 관련이 없는 호스트에서 실행되는 워크로드의 리소스 사용률을 검토합니다.
* 평균 부하 및 최고 부하 상태에서.클러스터의 동작을 이해합니다. 이 정보를 통해 용량 요구 사항을 파악하고 클러스터를 유지할 수 있는 최대 부하를 확인할 수 있습니다. 
* 노드 또는 컨테이너의 CPU 및 메모리 사용률이 임계값을 초과하거나 인프라 또는 노드 상태 롤업에서 클러스터에서 상태 변경이 발생하는 경우 사전에 사용자에게 알리거나 기록하도록 경고를 구성합니다.
* [Prometheus와](https://prometheus.io/docs/introduction/overview/) 통합하여 [쿼리를](container-insights-log-search.md) 사용하여 노드 및 Kubernetes에서 수집하는 응용 프로그램 및 워크로드 메트릭을 확인하여 사용자 지정 경고, 대시보드 및 자세한 수행 세부 분석을 생성합니다.
* Azure 스택에서 [AKS 엔진](https://github.com/Azure/aks-engine) 온-프레미스 및 [AKS 엔진에](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908)배포된 컨테이너 워크로드를 모니터링합니다.
* Azure Red [Hat OpenShift에 배포된](../../openshift/intro-openshift.md)컨테이너 워크로드를 모니터링합니다.

    >[!NOTE]
    >Azure Red Hat OpenShift에 대한 지원은 현재 공개 미리 보기의 기능입니다.
    >

Linux 클러스터와 비교하여 Windows Server 클러스터를 모니터링하는 주요 차이점은 다음과 같습니다.

- Windows 노드 및 컨테이너에 메모리 RSS 메트릭을 사용할 수 없습니다.
- Windows 노드에서 디스크 저장소 용량 정보를 사용할 수 없습니다.
- Windows 노드에서 실행 중인 컨테이너에는 컨테이너 로그를 사용할 수 없습니다.
- Windows 컨테이너 로그를 제외한 라이브 데이터(미리 보기) 기능 지원을 사용할 수 있습니다.
- 도커 환경이 아닌 포드 환경만 모니터링됩니다.
- 미리 보기 릴리스에서는 최대 30개의 Windows Server 컨테이너가 지원됩니다. 이 제한은 Linux 컨테이너에는 적용되지 않습니다. 

컨테이너에 대한 Azure Monitor를 사용하여 AKS 클러스터를 모니터링하는 방법에 대해 알아보는 데 도움이 되는 중간 수준의 심층 분석 결과를 제공하는 다음 비디오를 확인하십시오.

> [!VIDEO https://www.youtube.com/embed/RjsNmapggPU]

## <a name="how-do-i-access-this-feature"></a>이 기능에 액세스하려면 어떻게 하나요?

컨테이너용 Azure Monitor는 Azure Monitor에서 액세스하거나 선택한 AKS 클러스터에서 직접 액세스할 수 있습니다. Azure Monitor에서 배포된 모든 컨테이너에 대한 전역 관점이 있으며, 모니터링되고 모니터링되지 않는 컨테이너는 구독 및 리소스 그룹 간에 검색 및 필터링한 다음 Azure Monitor로 드릴다운하여 선택한 컨테이너.  그렇지 않으면 AKS 페이지에서 선택한 AKS 컨테이너에서 직접 기능에 액세스할 수 있습니다.  

![컨테이너용 Azure Monitor에 액세스하는 방법에 대한 개요](./media/container-insights-overview/azmon-containers-experience.png)

구성, 감사 및 리소스 사용률을 보기 위해 AKS 외부에서 실행되는 Docker 및 Windows 컨테이너 호스트를 모니터링하고 관리하는 데 관심이 있는 경우 [컨테이너 모니터링 솔루션을](../../azure-monitor/insights/containers.md)참조하십시오.

## <a name="next-steps"></a>다음 단계

Kubernetes 클러스터 모니터링을 시작하려면 [컨테이너에 대한 Azure Monitor를 사용하여 모니터링을 활성화하는](container-insights-onboard.md) 요구 사항 및 사용 가능한 방법을 이해하는 방법을 검토합니다. 
