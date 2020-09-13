---
title: 컨테이너에 대 한 Azure Monitor를 사용 하 여 배포 & HPA 메트릭 | Microsoft Docs
description: 이 문서에서는 컨테이너에 대해 Azure Monitor를 사용 하 여 수집 되는 & HPA (수평 pod autoscaler) 메트릭을 설명 합니다.
ms.topic: conceptual
ms.date: 08/09/2020
ms.openlocfilehash: ee1f0d4849a8382a898aaca84956ff78166e138f
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89570529"
---
# <a name="deployment--hpa-metrics-with-azure-monitor-for-containers"></a>컨테이너에 대 한 Azure Monitor를 사용 하 & HPA 메트릭 배포

에이전트 버전 *ciprod08072020*부터 컨테이너에 대 한 Azure monitor-통합 에이전트가 이제 Hpa & 배포에 대 한 메트릭을 수집 합니다.

## <a name="deployment-metrics"></a>배포 메트릭

컨테이너 Azure Monitor는 다음 메트릭을 60 초 간격으로 수집 하 고 **InsightMetrics** 테이블에 저장 하 여 배포 모니터링을 자동으로 시작 합니다.

|메트릭 이름 |메트릭 차원 (태그) |Description |
|------------|------------------------|------------|
|kube_deployment_status_replicas_ready |container.azm.ms/clusterId, container.azm.ms/clusterName, creationTime, deployment, deploymentStrategy, k8sNamespace, spec_replicas, status_replicas_available, status_replicas_updated (updatedReplicas) | 이 배포의 대상으로 지정 된 준비 된 pod의 총 수입니다 (readyReplicas). 다음은이 메트릭의 차원입니다. <ul> <li> 배포-배포의 이름 </li> <li> k8sNamespace-배포의 Kubernetes 네임 스페이스 </li> <li> deploymentStrategy-pod을 새 항목으로 대체 하는 데 사용할 배포 전략 (사양. 형식)</li><li> creationTime-배포 만들기 타임 스탬프 </li> <li> spec_replicas-원하는 pod (spec)의 수 </li> <li>status_replicas_available-이 배포의 대상으로 지정 된 사용 가능한 총 pod 수 (최소 minReadySeconds) (availableReplicas)</li><li>status_replicas_updated-원하는 템플릿 사양 (updatedReplicas)이 있는이 배포의 대상으로 지정 된 종료 되지 않은 pod의 총 수입니다. </li></ul>|

## <a name="hpa-metrics"></a>HPA 메트릭

컨테이너 Azure Monitor는 다음 메트릭을 60 초 간격으로 수집 하 고 **InsightMetrics** 테이블에 저장 하 여 hpa 모니터링을 자동으로 시작 합니다.

|메트릭 이름 |메트릭 차원 (태그) |Description |
|------------|------------------------|------------|
|kube_hpa_status_current_replicas |container.azm.ms/clusterId, container.azm.ms/clusterName, creationTime, hpa, k8sNamespace, lastScaleTime, spec_max_replicas, spec_min_replicas, status_desired_replicas, targetKind, targetName | 이 autoscaler에서 관리 하는 pod의 현재 복제본 수 ()입니다. 다음은이 메트릭의 차원입니다. <ul> <li> hpa-HPA의 이름 </li> <li> HPA에 대 한 k8sNamespace-Kubernetes 네임 스페이스 </li> <li> lastScaleTime HPA pod의 수를 마지막으로 조정한 시간 (lastScaleTime)</li><li> creationTime-HPA 만들기 타임 스탬프 </li> <li> spec_max_replicas-autoscaler (spec 복제본)에서 설정할 수 있는 pod 수에 대 한 상한 </li> <li> autoscaler 확장 될 수 있는 복제본 수의 하한값 ()입니다. spec_min_replicas </li><li>이 autoscaler에서 관리 하는 pod 복제본 수 status_desired_replicas (desiredReplicas)</li><li>targetKind-HPA의 대상 종류 (scaleTargetRef) </li><li>targetName-HPA 대상의 이름 (spec.scaleTargetRef.name) </li></ul>|

## <a name="deployment--hpa-charts"></a>HPA 차트 & 배포 

컨테이너에 대 한 Azure Monitor에는 테이블 앞부분에 나열 된 메트릭에 대 한 미리 구성 된 차트가 모든 클러스터에 대 한 통합 문서로 포함 됩니다. 왼쪽 창에서 **통합** 문서를 선택 하 고 & HPA 통합 문서 **보기** 드롭다운 목록에서 **HPA** 를 AKS 클러스터에서 직접 배포 & 배포를 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Kubernetes의 Kube 메트릭을](https://github.com/kubernetes/kube-state-metrics/tree/master/docs) 검토 하 여 Kube 상태 메트릭에 대해 자세히 알아보세요.