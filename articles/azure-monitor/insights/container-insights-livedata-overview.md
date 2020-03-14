---
title: 컨테이너의 Azure Monitor 사용 하 여 라이브 데이터 (미리 보기) 보기 | Microsoft Docs
description: 이 문서에서는 컨테이너에 Azure Monitor kubectl을 사용 하지 않고 Kubernetes 로그, 이벤트 및 pod 메트릭에 대 한 실시간 보기를 설명 합니다.
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 9e7c7a7b7bf276b3451cee1d289b8b07ac0f40ba
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79216562"
---
# <a name="how-to-view-kubernetes-logs-events-and-pod-metrics-in-real-time"></a>Kubernetes 로그, 이벤트 및 pod 메트릭을 실시간으로 보는 방법

컨테이너의 Azure Monitor에는 AKS (Azure Kubernetes Service) 컨테이너 로그 (stdout/stderror), 이벤트 및 pod 메트릭에 직접 액세스할 수 있게 해 주는 고급 진단 기능인 라이브 데이터 (미리 보기) 기능이 포함 되어 있습니다. `kubectl logs -c`, `kubectl get` 이벤트 및 `kubectl top pods`에 대 한 직접 액세스를 노출 합니다. 콘솔 창에는 실시간 문제 해결에 도움이 되는 컨테이너 엔진에서 생성 한 로그, 이벤트 및 메트릭이 표시 됩니다.

이 문서에서는 자세한 개요를 제공 하 고이 기능을 사용 하는 방법을 이해 하는 데 도움을 줍니다. 

>[!NOTE]
>[개인 클러스터](https://azure.microsoft.com/updates/aks-private-cluster/) 로 설정 된 AKS 클러스터는이 기능에서 지원 되지 않습니다. 이 기능은 브라우저에서 프록시 서버를 통해 Kubernetes API에 직접 액세스 하는 데 의존 합니다. 이 프록시의 Kubernetes API를 차단 하도록 네트워킹 보안을 사용 하도록 설정 하면이 트래픽이 차단 됩니다. 

>[!NOTE]
>이 기능은 Azure 중국을 비롯 한 모든 Azure 지역에서 사용할 수 있습니다. 현재는 Azure 미국 정부에서 사용할 수 없습니다.

라이브 데이터 (미리 보기) 기능을 설정 하거나 문제를 해결 하는 방법에 대 한 도움말은 [설치 가이드](container-insights-livedata-setup.md)를 참조 하세요. 이 기능은 Kubernetes API에 직접 액세스할 수 있으며 인증 모델에 대 한 추가 정보는 [여기](https://kubernetes.io/docs/concepts/overview/kubernetes-api/)에서 찾을 수 있습니다. 

## <a name="live-data-preview-functionality-overview"></a>라이브 데이터 (미리 보기) 기능 개요

### <a name="search"></a>검색

![라이브 데이터 콘솔 창 필터 예](./media/container-insights-livedata-overview/livedata-pane-filter-example.png)

라이브 데이터 (미리 보기) 기능에는 검색 기능이 포함 되어 있습니다. **검색** 필드에서 키 단어나 용어를 입력 하 여 결과를 필터링 할 수 있으며, 모든 일치 결과가 강조 표시 되어 빠르게 검토할 수 있습니다. 이벤트를 보는 동안 검색 표시줄의 오른쪽에 있는 **필터** 약을 사용 하 여 결과를 추가로 제한할 수 있습니다. 선택한 리소스에 따라 약에는 선택한 Pod, 네임 스페이스 또는 클러스터가 나열 됩니다.  

### <a name="scroll-lock-and-pause"></a>Scroll Lock 및 Pause 

자동 스크롤를 일시 중단 하 고 창의 동작을 제어 하 여 새 데이터 읽기를 수동으로 스크롤할 수 있도록 **스크롤** 옵션을 사용할 수 있습니다. 자동 스크롤를 다시 사용 하도록 설정 하려면 **스크롤** 옵션을 다시 선택 하면 됩니다. **일시 중지** 옵션을 선택 하 여 로그 나 이벤트 데이터의 검색을 일시 중지 하 고, 다시 시작할 준비가 되 면 **Play**를 선택 하면 됩니다.  

![라이브 데이터 콘솔 창 일시 중지 라이브 보기](./media/container-insights-livedata-overview/livedata-pane-scroll-pause-example.png)

>[!IMPORTANT]
>문제를 해결 하는 동안 짧은 시간 동안 자동 스크롤 일시 중단 하거나 일시 중지 하는 것이 좋습니다. 이러한 요청은 클러스터의 Kubernetes API에 대 한 가용성 및 제한에 영향을 줄 수 있습니다. 

>[!IMPORTANT]
>이 기능을 수행 하는 동안 데이터가 영구적으로 저장 되지 않습니다. 세션 중에 캡처된 모든 정보는 브라우저를 닫거나 다른 곳으로 이동 하면 삭제 됩니다. 데이터는 메트릭 기능의 5 분 기간 내에 시각화에 대해서만 표시 됩니다. 5 분 보다 오래 된 메트릭도 모두 삭제 됩니다. 적절 한 메모리 사용 제한 내에서 라이브 데이터 (미리 보기) 버퍼 쿼리 (구체적으로는 적절 한 것은 무엇 인가요?) 

## <a name="view-logs"></a>로그 보기

**노드**, **컨트롤러**및 **컨테이너** 뷰에서 컨테이너 엔진에 의해 생성 된 실시간 로그 데이터를 볼 수 있습니다. 로그 데이터를 보려면 다음 단계를 수행 합니다.

1. Azure Portal에서 AKS cluster 리소스 그룹으로 이동 하 여 AKS 리소스를 선택 합니다.

2. AKS 클러스터 대시보드의 왼쪽에 있는 **모니터링** 에서 **Insights**를 선택 합니다. 

3. **노드**, **컨트롤러**또는 **컨테이너** 탭을 선택 합니다.

4. 성능 표에서 개체를 선택 하 고 오른쪽에 있는 속성 창에서 **라이브 데이터 보기 (미리 보기)** 옵션을 선택 합니다. AKS 클러스터가 Azure AD를 사용 하 여 Single Sign-On 구성 된 경우 해당 브라우저 세션 중 처음 사용할 때 인증 하 라는 메시지가 표시 됩니다. 계정을 선택하고 Azure를 통해 인증을 수행합니다.  

    >[!NOTE]
    >속성 창에서 **분석에서 보기** 옵션을 선택 하 여 Log Analytics 작업 영역에서 데이터를 볼 때 로그 검색 결과에 **노드**, **데몬 집합**, **복제본 집합**, **작업**, **Cron 작업**, **pod**및 **컨테이너가** 더 이상 없을 수 있습니다. `kubectl`에서 사용할 수 없는 컨테이너에 대 한 로그 검색을 시도 해도 여기에서 오류가 발생 합니다. 기록 로그, 이벤트 및 메트릭을 보는 방법에 대 한 자세한 내용은 [analytics의 보기](container-insights-log-search.md#search-logs-to-analyze-data) 기능을 검토 하세요.  

성공적으로 인증 된 후에는 라이브 데이터 (미리 보기) 콘솔 창이 성능 데이터 표 아래에 표시 되며이 위치에서 연속 스트림에서 로그 데이터를 볼 수 있습니다. 인출 상태 표시기가 창의 맨 오른쪽에 있는 녹색 확인 표시를 표시 하는 경우 데이터를 검색 하 고 콘솔에 대 한 스트리밍을 시작 하는 것을 의미 합니다.  

![노드 속성 창 데이터 보기 옵션](./media/container-insights-livedata-overview/node-properties-pane.png)  

창 제목은 컨테이너를 그룹화 할 pod의 이름을 표시 합니다.

## <a name="view-events"></a>이벤트 보기

컨테이너, pod, node, ReplicaSet, DaemonSet, 작업, CronJob 또는 배포가 선택 된 경우 **노드**, **컨트롤러**, **컨테이너**및 **배포 (미리 보기)** 보기에서 컨테이너 엔진에 의해 생성 되는 실시간 이벤트 데이터를 볼 수 있습니다. 이벤트를 보려면 다음 단계를 수행 합니다.

1. Azure Portal에서 AKS cluster 리소스 그룹으로 이동 하 여 AKS 리소스를 선택 합니다.

2. AKS 클러스터 대시보드의 왼쪽에 있는 **모니터링** 에서 **Insights**를 선택 합니다. 

3. **노드**, **컨트롤러**, **컨테이너**또는 **배포 (미리 보기)** 탭을 선택 합니다.

4. 성능 표에서 개체를 선택 하 고 오른쪽에 있는 속성 창에서 **라이브 데이터 보기 (미리 보기)** 옵션을 선택 합니다. AKS 클러스터가 Azure AD를 사용 하 여 Single Sign-On 구성 된 경우 해당 브라우저 세션 중 처음 사용할 때 인증 하 라는 메시지가 표시 됩니다. 계정을 선택하고 Azure를 통해 인증을 수행합니다.  

    >[!NOTE]
    >속성 창에서 **분석에서 보기** 옵션을 선택 하 여 Log Analytics 작업 영역에서 데이터를 볼 때 로그 검색 결과에 **노드**, **데몬 집합**, **복제본 집합**, **작업**, **Cron 작업**, **pod**및 **컨테이너가** 더 이상 없을 수 있습니다. `kubectl`에서 사용할 수 없는 컨테이너에 대 한 로그 검색을 시도 해도 여기에서 오류가 발생 합니다. 기록 로그, 이벤트 및 메트릭을 보는 방법에 대 한 자세한 내용은 [analytics의 보기](container-insights-log-search.md#search-logs-to-analyze-data) 기능을 검토 하세요.  

성공적으로 인증 된 후에는 성능 데이터 표 아래에 라이브 데이터 (미리 보기) 콘솔 창이 나타납니다. 인출 상태 표시기가 창의 맨 오른쪽에 있는 녹색 확인 표시를 표시 하는 경우 데이터를 검색 하 고 콘솔에 대 한 스트리밍을 시작 하는 것을 의미 합니다. 
    
선택한 개체가 컨테이너 인 경우 창에서 **이벤트** 옵션을 선택 합니다. Node, Pod 또는 controller를 선택 하면 이벤트 보기가 자동으로 선택 됩니다. 

![컨트롤러 속성 창 보기 이벤트](./media/container-insights-livedata-overview/controller-properties-live-event.png)  

창 제목은 컨테이너를 그룹화 할 Pod의 이름을 표시 합니다.

### <a name="filter-events"></a>이벤트 필터링 

이벤트를 보는 동안 검색 표시줄의 오른쪽에 있는 **필터** 약을 사용 하 여 결과를 추가로 제한할 수 있습니다. 선택한 리소스에 따라 약에는 선택한 Pod, 네임 스페이스 또는 클러스터가 나열 됩니다.  

## <a name="view-metrics"></a>메트릭 보기 

**Pod** 가 선택 된 경우에만 **노드** 또는 **컨트롤러** 뷰에서 컨테이너 엔진에 의해 생성 되는 실시간 메트릭 데이터를 볼 수 있습니다. 메트릭을 보려면 다음 단계를 수행 합니다.

1. Azure Portal에서 AKS cluster 리소스 그룹으로 이동 하 여 AKS 리소스를 선택 합니다.

2. AKS 클러스터 대시보드의 왼쪽에 있는 **모니터링** 에서 **Insights**를 선택 합니다. 

3. **노드** 또는 **컨트롤러** 탭을 선택 합니다.

4. 성능 표에서 **Pod** 개체를 선택 하 고 오른쪽에 있는 속성 창에서 **라이브 데이터 보기 (미리 보기)** 옵션을 선택 합니다. AKS 클러스터가 Azure AD를 사용 하 여 Single Sign-On 구성 된 경우 해당 브라우저 세션 중 처음 사용할 때 인증 하 라는 메시지가 표시 됩니다. 계정을 선택하고 Azure를 통해 인증을 수행합니다.  

    >[!NOTE]
    >속성 창에서 **분석에서 보기** 옵션을 선택 하 여 Log Analytics 작업 영역에서 데이터를 볼 때 로그 검색 결과에 **노드**, **데몬 집합**, **복제본 집합**, **작업**, **Cron 작업**, **pod**및 **컨테이너가** 더 이상 없을 수 있습니다. `kubectl`에서 사용할 수 없는 컨테이너에 대 한 로그 검색을 시도 해도 여기에서 오류가 발생 합니다. 기록 로그, 이벤트 및 메트릭을 보는 방법에 대 한 자세한 내용은 [analytics의 보기](container-insights-log-search.md#search-logs-to-analyze-data) 기능을 검토 하세요.  

성공적으로 인증 된 후에는 성능 데이터 표 아래에 라이브 데이터 (미리 보기) 콘솔 창이 나타납니다. 메트릭 데이터가 검색 되 고 두 차트에 표시 하기 위해 콘솔에 대 한 스트리밍을 시작 합니다. 창 제목은 컨테이너를 그룹화 할 pod의 이름을 표시 합니다.

![Pod 메트릭 예제 보기](./media/container-insights-livedata-overview/pod-properties-live-metrics.png)  

## <a name="next-steps"></a>다음 단계

- Azure Monitor를 사용하고 AKS 클러스터의 다른 측면을 모니터링하는 방법을 계속 알아보려면 [Azure Kubernetes 서비스 상태](container-insights-analyze.md)를 참조하세요.

- [로그 쿼리 예](container-insights-log-search.md#search-logs-to-analyze-data) 를 확인 하 여 미리 정의 된 쿼리 및 예제를 확인 하 여 경고, 시각화를 만들거나 클러스터에 대 한 추가 분석을 수행 합니다.
