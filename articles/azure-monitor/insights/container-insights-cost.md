---
title: 컨테이너의 Azure Monitor에 대 한 모니터링 비용 | Microsoft Docs
description: 이 문서에서는 고객이 사용 및 관련 비용을 관리 하는 데 도움이 되는 컨테이너에 대 한 Azure Monitor 수집 된 메트릭 & 인벤토리 데이터에 대 한 모니터링 비용을 설명 합니다.
ms.topic: conceptual
ms.date: 05/29/2020
ms.openlocfilehash: a03e94fa7650c56a4d3b3beda3c27283329aebbe
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84204653"
---
# <a name="understand-monitoring-costs-for-azure-monitor-for-containers"></a>컨테이너의 Azure Monitor에 대 한 모니터링 비용 이해

이 문서에서는 다음을 이해 하는 데 도움이 되는 컨테이너의 Azure Monitor에 대 한 가격 책정 지침을 제공 합니다.

* 이 통찰력을 사용 하도록 설정 하기 전에 비용을 계산 하는 방법

* 하나 이상의 컨테이너에서 컨테이너에 대 한 Azure Monitor를 사용 하도록 설정한 후 비용을 측정 하는 방법

* 데이터 컬렉션을 제어 하 고 비용을 절감 하는 방법

Azure Monitor 로그는 Kubernetes 클러스터에 의해 생성 된 데이터를 수집, 인덱스 및 저장 합니다. 

Azure Monitor 가격 책정 모델은 주로 Log Analytics 작업 영역에 대 한 일일 데이터 수집 (gb)을 기반으로 합니다. Log Analytics 작업 영역의 비용은 수집 된 데이터의 양을 기반으로 하지 않으며, 선택한 계획에 따라 달라 지 며, 클러스터에서 생성 된 데이터를 저장 하도록 선택한 계획에 따라 달라 집니다.

>[!NOTE]
>모든 크기와 가격은 샘플 예측에 대해서만 사용할 수 있습니다. Azure Monitor Log Analytics 가격 책정 모델 및 Azure 지역에 따라 [최신 가격 책정 페이지 Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/) 를 참조 하세요.

다음은 Kubernetes 클러스터에서 수집 되는 데이터의 형식에 대 한 요약으로, 비용에 영향을 주는 컨테이너에 대 한 Azure Monitor를 사용 하 여 사용자 지정할 수 있습니다.

- 클러스터의 모든 Kubernetes 네임 스페이스에 있는 모든 모니터링 되는 컨테이너의 Stdout, stderr 컨테이너 로그

- 클러스터에 있는 모든 모니터링 되는 컨테이너의 컨테이너 환경 변수

- 모니터링이 필요 하지 않은 클러스터에서 완료 된 Kubernetes 작업/pod

- 프로메테우스 메트릭의 활성 스크랩

- AKS 클러스터에 있는 Kubernetes 마스터 노드 로그의 [진단 로그 수집](../../aks/view-master-logs.md) - *kube* 와 같은 마스터 구성 요소에 의해 생성 된 로그 데이터를 분석 합니다 *.*

## <a name="what-is-collected-from-kubernetes-clusters"></a>Kubernetes 클러스터에서 수집 되는 내용

컨테이너에 대 한 Azure Monitor에는 Log Analytics 작업 영역에서 로그 데이터로 기록 되는 미리 정의 된 메트릭 및 인벤토리 항목 집합이 포함 되어 있습니다. 아래에 나열 된 모든 메트릭은 1 분 마다 기본적으로 수집 됩니다.

### <a name="node-metrics-collected"></a>수집 된 노드 메트릭

다음 목록은 수집 된 노드당 24 개의 메트릭입니다.

- cpuUsageNanoCores
- cpuCapacityNanoCores
- cpuAllocatableNanoCores
- memoryRssBytes
- memoryWorkingSetBytes
- memoryCapacityBytes
- memoryAllocatableBytes
- restartTimeEpoch
- 사용 됨 (디스크)
- 무료 (디스크)
- used_percent (디스크)
- io_time (diskio)
- 쓰기 (diskio)
- 읽기 (diskio)
- write_bytes (diskio)
- write_time (diskio)
- iops_in_progress (diskio)
- read_bytes (diskio)
- read_time (diskio)
- err_in (net)
- err_out (net)
- bytes_recv (net)
- bytes_sent (net)
- Kubelet_docker_operations (Kubelet)

### <a name="container-metrics"></a>컨테이너 메트릭

다음 목록은 수집 된 컨테이너 당 8 개의 메트릭입니다.

- cpuUsageNanoCores
- cpuRequestNanoCores
- cpuLimitNanoCores
- memoryRssBytes
- memoryWorkingSetBytes
- memoryRequestBytes
- Memorybytes 바이트
- restartTimeEpoch

### <a name="cluster-inventory"></a>클러스터 인벤토리

다음 목록은 기본적으로 수집 되는 클러스터 인벤토리 데이터입니다.

- KubePodInventory – 컨테이너 당 분당 1 개
- KubeNodeInventory – 노드당 1 분당 1
- KubeServices – 분당 서비스 1 개
- ContainerInventory – 분당 컨테이너 당 1

## <a name="estimating-costs-to-monitor-your-aks-cluster"></a>AKS 클러스터 모니터링 비용 예측

아래 예측은 다음 크기 조정 예제를 사용 하 여 Azure Kubernetes 서비스 (AKS) 클러스터를 기반으로 합니다. 또한 예상 값은 수집 된 메트릭과 인벤토리 데이터에만 적용 됩니다. 컨테이너 로그 (stdout, stderr 및 환경 변수)의 경우 작업에 의해 생성 된 로그 크기에 따라 달라 지 며 예측에서 제외 됩니다.

다음과 같이 구성 된 AKS 클러스터의 모니터링을 사용 하도록 설정한 경우

- 3 개 노드
- 노드당 두 개의 디스크
- 노드당 하나의 네트워크 인터페이스
- 20 pod (각 pod의 한 컨테이너 = 총 20 개의 컨테이너)
- 두 개의 Kubernetes 네임 스페이스
- 5 개의 Kubernetes 서비스 (kube 시스템 pod, 서비스 및 네임 스페이스 포함)
- 수집 빈도 = 60 초 (기본값)

할당 된 Log Analytics 작업 영역에서 시간당 생성 된 데이터의 테이블 및 볼륨을 볼 수 있습니다. 이러한 각 테이블에 대 한 자세한 내용은 [컨테이너 레코드](container-insights-log-search.md#container-records)를 참조 하십시오.

|테이블 | 예상 크기 (MB/시간) |
|------|---------------|
|Perf | 12.9 |
|InsightsMetrics | 11.3 |
|KubePodInventory | 1.5 |
|KubeNodeInventory | 0.75 |
|KubeServices | 0.13 |
|ContainerInventory | 3.6 |
|KubeHealth | 0.1 |
|KubeMonAgentEvents |0.005 |

합계 = 31 m b/시간 = 23.1 g b/월 (1 개월 = 31 일)

종 량 제 모델인 Log Analytics의 기본 [가격](https://azure.microsoft.com/pricing/details/monitor/) 을 사용 하 여 월별 Azure Monitor 비용을 예측할 수 있습니다. 용량 예약을 포함 한 후에는 선택한 예약에 따라 월별 가격이 더 높아집니다.

## <a name="controlling-ingestion-to-reduce-cost"></a>수집을 제어 하 여 비용 절감

조직의 다른 비즈니스 단위가 Kubernetes 인프라와 Log Analytics 작업 영역을 공유 하는 시나리오를 고려해 보세요. 각 비즈니스 단위가 Kubernetes 네임 스페이스로 구분 됩니다. 최근 릴리스된 통합 문서를 사용 하 여 각 작업 영역에서 얼마나 많은 데이터를 수집 시각화할 수 있습니다. [통합 문서 갤러리](../platform/workbooks-overview.md#getting-started)에 있는 **컨테이너 Insights 사용** 통합 문서를 사용 하면 설명서에서 공유 하는 내용에서 고유한 쿼리 라이브러리를 작성 하지 않고도 데이터 원본을 시각화할 수 있습니다. 이 통합 문서에는 다음과 같은 관점에서 청구 가능한 데이터를 볼 수 있는 차트가 있습니다.

- 수집 총 청구 가능 데이터 (GB)

- 컨테이너 로그 (응용 프로그램 로그)로 청구 가능한 데이터 수집

- 청구 가능 컨테이너 로그 데이터 수집 per Kubernetes 네임 스페이스

- 클러스터 이름으로 분리 된 청구 가능 컨테이너 로그 데이터 수집

- Logsource 항목 수집 청구 가능 컨테이너 로그 데이터

- 진단 마스터 노드 로그에 의해 수집 청구 가능한 진단 데이터

통합 문서에 대 한 권한 및 사용 권한을 관리 하는 방법에 대 한 자세한 내용은 [액세스 제어](../platform/workbooks-access-control.md)를 참조 하세요.

분석을 완료 하 여 요구 사항을 초과 하는 데이터를 가장 많이 생성 하는 원본 또는 원본을 확인 한 후 데이터 수집을 다시 구성할 수 있습니다. Stdout, stderr 및 환경 변수를 구성 하는 방법에 대 한 자세한 내용은 [에이전트 데이터 컬렉션 설정 구성](container-insights-agent-config.md) 문서에 설명 되어 있습니다.

다음은 작업 비용을 제어 하기 위해 ConfigMap 파일을 수정 하 여 클러스터에 적용할 수 있는 변경 내용에 대 한 예입니다.

1. ConfigMap 파일에서 다음을 수정 하 여 클러스터의 모든 네임 스페이스에서 stdout 로그를 사용 하지 않도록 설정 합니다.

    ```
    [log_collection_settings]       
       [log_collection_settings.stdout]          
          enabled = false
    ```

2. 개발 네임 스페이스 (예: 개발 **-테스트**)에서 stderr 로그 수집을 사용 하지 않도록 설정 하 고, configmap 파일에서 다음을 수정 하 여 다른 네임 스페이스 (예: **prod** 및 **기본값**)에서 stderr 로그를 계속 수집 합니다.

    >[!NOTE]
    >Kube-시스템 로그 수집은 기본적으로 사용 되지 않습니다. 기본 설정이 유지 되 고, 제외 네임 스페이스 목록에 **개발-테스트** 네임 스페이스를 추가 하면 stderr 로그 수집에 적용 됩니다.

    ```
    [log_collection_settings.stderr]          
       enabled = true          
          exclude_namespaces = ["kube-system", "dev-test"]
    ```

3. ConfigMap 파일에서 다음을 수정 하 여 클러스터에서 환경 변수 수집을 사용 하지 않도록 설정 합니다. 이는 모든 Kubernetes 네임 스페이스의 모든 컨테이너에 적용 됩니다. 

    ```
    [log_collection_settings.env_var]
        enabled = false
    ```

4. 완료 된 작업을 정리 하려면 ConfigMap 파일에서 다음을 수정 하 여 작업 정의에서 정리 정책을 지정 합니다.

    ```
    apiVersion: batch/v1
    kind: Job
    metadata:
      name: pi-with-ttl
    spec:
      ttlSecondsAfterFinished: 100
    ```

이러한 변경 내용을 ConfigMaps에 적용 한 후에는 [업데이트 된 Configmaps을 적용](container-insights-prometheus-integration.md#applying-updated-configmap) 하 여 클러스터에 적용을 참조 하세요.

### <a name="prometheus-metrics-scraping"></a>프로메테우스 메트릭 스크랩

[프로메테우스 메트릭 스크랩](container-insights-prometheus-integration.md)을 활용 하는 경우 클러스터에서 수집 하는 메트릭 수를 제한 하려면 다음 사항을 고려 하세요.

- 스크랩 frequency가 최적으로 설정 되어 있는지 확인 합니다 (기본값은 60 초). 빈도를 15 초로 늘릴 수 있지만 스크랩 메트릭이 해당 빈도로 게시 되는지 확인 해야 합니다. 그렇지 않은 경우에는 데이터 수집 및 보존 비용에 추가 되는 간격으로 많은 중복 메트릭이 스크랩 Log Analytics 작업 영역으로 전송 되 고 값은 줄어듭니다. 

- 컨테이너 Azure Monitor는 메트릭 이름별 제외 & 포함 목록을 지원 합니다. 예를 들어 클러스터에서 **kubedns** 메트릭을 스크랩 하는 경우, 기본적으로 스크랩를 가져오는 수백 개의가 있을 수 있지만, 하위 집합에만 관심이 있을 가능성이 높습니다. 스크랩에 대 한 메트릭 목록을 지정 했는지 확인 하거나, 몇 가지를 제외한 다른 항목을 제외 하 여 데이터 수집 볼륨에 저장 합니다. 스크랩를 사용 하도록 설정 하 고 대부분의 메트릭을 사용 하지 않고 Log Analytics 청구서에 추가 요금을 추가 하는 것이 쉽습니다.

- Pod 주석을 통해 스크랩 때 사용 하지 않는 네임 스페이스 (예: **개발-테스트** 네임 스페이스)의 스크랩 pod 메트릭을 제외 하도록 네임 스페이스로 필터링 해야 합니다.

## <a name="next-steps"></a>다음 단계

컨테이너의 Azure Monitor으로 수집 된 데이터에서 최근 사용 패턴을 기반으로 하는 비용을 이해 하는 방법에 대 한 자세한 내용은 [사용량 관리 및 비용 예측](../platform/manage-cost-storage.md)을 참조 하세요.