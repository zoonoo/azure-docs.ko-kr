---
title: 컨테이너 인사이트를 사용하는 배포 및 HPA 메트릭 | Microsoft Docs
description: 이 문서에서는 컨테이너 인사이트를 사용하여 수집되는 배포 및 HPA(Horizontal Pod Autoscaler) 메트릭에 대해 설명합니다.
ms.topic: conceptual
ms.date: 08/09/2020
ms.openlocfilehash: c8bb100b756ea92d73e1c3a698f119b4f8157930
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101717709"
---
# <a name="deployment--hpa-metrics-with-container-insights"></a>컨테이너 인사이트를 사용하는 배포 및 HPA 메트릭

에이전트 버전 *ciprod08072020* 부터 컨테이너 인사이트 통합 에이전트가 이제 배포 및 HPA에 대한 메트릭을 수집합니다.

## <a name="deployment-metrics"></a>배포 메트릭

컨테이너 인사이트는 배포 모니터링을 자동으로 시작하여 다음 메트릭을 60초 간격으로 수집하고 **InsightMetrics** 테이블에 저장합니다.

|메트릭 이름 |메트릭 차원(태그) |Description |
|------------|------------------------|------------|
|kube_deployment_status_replicas_ready |container.azm.ms/clusterId, container.azm.ms/clusterName, creationTime, deployment, deploymentStrategy, k8sNamespace, spec_replicas, status_replicas_available, status_replicas_updated(status.updatedReplicas) | 이 배포의 대상으로 지정된 지원 Pod의 총 수입니다(status.readyReplicas). 다음은 이 메트릭의 차원입니다. <ul> <li> 배포 - 배포의 이름 </li> <li> k8sNamespace - 배포의 Kubernetes 네임스페이스 </li> <li> deploymentStrategy - Pod를 새 것으로 대체하는 데 사용할 배포 전략(spec.strategy.type)</li><li> creationTime - 배포 생성 타임스탬프 </li> <li> spec_replicas - 원하는 Pod의 수(spec.replicas) </li> <li>status_replicas_available - 이 배포의 대상으로 지정된 사용 가능한 총 Pod 수(최소한 minReadySeconds 지원)(status.availableReplicas)</li><li>status_replicas_updated - 원하는 템플릿 사양이 있는 이 배포의 대상으로 지정된 종료되지 않은 Pod의 총 수(status.updatedReplicas) </li></ul>|

## <a name="hpa-metrics"></a>HPA 메트릭

컨테이너 인사이트는 HPA 모니터링을 자동으로 시작하여 다음 메트릭을 60초 간격으로 수집하고 **InsightMetrics** 테이블에 저장합니다.

|메트릭 이름 |메트릭 차원(태그) |Description |
|------------|------------------------|------------|
|kube_hpa_status_current_replicas |container.azm.ms/clusterId, container.azm.ms/clusterName, creationTime, hpa, k8sNamespace, lastScaleTime, spec_max_replicas, spec_min_replicas, status_desired_replicas, targetKind, targetName | 이 Autoscaler에서 관리하는 Pod의 현재 복제본 수(status.currentReplicas). 다음은 이 메트릭의 차원입니다. <ul> <li> hpa - HPA의 이름 </li> <li> k8sNamespace - HPA의 Kubernetes 네임스페이스 </li> <li> lastScaleTime - HPA Pod의 수를 마지막으로 스케일링한 시간(status.lastScaleTime)</li><li> creationTime - HPA 생성 타임스탬프 </li> <li> spec_max_replicas - Autoscaler에서 설정할 수 있는 Pod 수의 상한값(spec.maxReplicas) </li> <li> spec_min_replicas - Autoscaler에서 스케일 다운할 수 있는 복제본 수의 하한값(spec.minReplicas) </li><li>status_desired_replicas - 해당 Autoscaler에서 관리되는 Pod 복제본 수(status.desiredReplicas) 지정</li><li>targetKind - HPA 대상의 종류(spec.scaleTargetRef.kind) </li><li>targetName - HPA 대상의 이름(spec.scaleTargetRef.name) </li></ul>|

## <a name="deployment--hpa-charts"></a>배포 및 HPA 차트 

컨테이너 인사이트에는 테이블에 앞서 나열된 메트릭에 대해 미리 구성된 차트가 모든 클러스터에 대한 통합 문서로 포함되어 있습니다. 인사이트에서 왼쪽 창의 **통합 문서** 를 선택하고 드롭다운 목록에서 **통합 문서 보기** 를 선택하여 AKS 클러스터에서 바로 배포 및 HPA 통합 문서, **배포 및 HPA** 를 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Kubernetes에서 Kube 상태 메트릭](https://github.com/kubernetes/kube-state-metrics/tree/master/docs)을 검토하여 Kube 상태 메트릭에 대해 자세히 알아보세요.