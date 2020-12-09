---
title: 컨테이너에 대 한 Azure Monitor의 보고서
description: Azure Monitor 컨테이너에 대해 수집 된 데이터를 분석 하는 데 사용할 수 있는 보고서에 대해 설명 합니다.
ms.topic: conceptual
ms.date: 12/07/2020
ms.openlocfilehash: 3cc2f8fb9bfaa278ce06b4a8cd6d379397b7129a
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/09/2020
ms.locfileid: "96907535"
---
# <a name="reports-in-azure-monitor-for-containers"></a>컨테이너에 대 한 Azure Monitor의 보고서
컨테이너에 대 한 Azure Monitor의 보고서에는 기본 [Azure 통합 문서가](../platform/workbooks-overview.md)권장 됩니다. 이 문서에서는 사용할 수 있는 다양 한 보고서와 이러한 보고서에 액세스 하는 방법을 설명 합니다.

## <a name="viewing-reports"></a>보고서 보기
Azure Portal **Azure Monitor** 메뉴에서 **컨테이너** 를 선택 합니다. **모니터링** 섹션에서 **Insights** 를 선택 하 고 특정 클러스터를 선택한 다음 **보고서 (미리 보기)** 페이지를 선택 합니다. 

[![보고서 페이지](media/container-insights-reports/reports-page.png)](media/container-insights-reports/reports-page.png#lightbox)

## <a name="create-a-custom-workbook"></a>사용자 지정 통합 문서 만들기
이러한 통합 문서 중 하나를 기반으로 사용자 지정 통합 문서를 만들려면 **통합 문서 보기** 드롭다운을 선택 하 고 드롭다운 아래에 있는 **AKS 갤러리로 이동** 합니다. 통합 문서 및 통합 문서 템플릿 사용에 대 한 자세한 내용은 [Azure Monitor 통합 문서](../platform/workbooks-overview.md) 를 참조 하세요.

[![AKS 갤러리](media/container-insights-reports/aks-gallery.png)](media/container-insights-reports/aks-gallery.png#lightbox)

## <a name="node-workbooks"></a>노드 통합 문서

- **디스크 용량**: 다음과 같은 관점에서 컨테이너 내의 노드에 표시 되는 각 디스크에 대 한 대화형 디스크 사용량 차트입니다.

    - 모든 디스크의 디스크 사용 백분율입니다.
    - 모든 디스크의 사용 가능한 디스크 공간
    - 각 노드의 디스크, 사용 된 공간의 백분율, 사용 가능한 디스크 공간 (GiB) 및 사용 가능한 디스크 공간 (GiB)의 추세를 보여 주는 표 테이블에서 행을 선택 하면 사용 된 공간 및 사용 가능한 디스크 공간 (GiB)의 백분율이 행 아래에 표시 됩니다.

- **디스크 IO**: 컨테이너 내의 노드에 표시 되는 각 디스크에 대 한 대화형 디스크 사용률 차트는 다음과 같은 관점에서 설명 합니다.

    - 모든 디스크에서 read bytes/sec, write bytes/sec, read 및 write bytes/sec 추세를 기준으로 전체 디스크 i/o를 요약 합니다.
    - 8 개의 성능 차트는 디스크 i/o 병목 상태를 측정 하 고 식별 하는 데 도움이 되는 핵심 성과 지표를 표시 합니다.

- **Gpu**: 각 Gpu 인식 Kubernetes 클러스터 노드에 대 한 대화형 gpu 사용량 차트입니다.

## <a name="resource-monitoring-workbooks"></a>리소스 모니터링 통합 문서

- **배포**: 사용자 지정 HPA를 포함 하 여 HPA (수평 Pod Autoscaler)의 & 배포 상태입니다. 
  
- **작업 세부 정보**: 네임 스페이스에 대 한 작업의 성능 통계를 보여 주는 대화형 차트입니다. 여러 탭이 포함 되어 있습니다.

  - POD의 CPU 및 메모리 사용량에 대 한 개요입니다.
  - Pod 다시 시작 추세, 컨테이너 다시 시작 추세 및 Pod의 컨테이너 상태를 보여 주는 POD/Container Status입니다.
  - Kubernetes 이벤트는 컨트롤러에 대 한 이벤트의 요약을 보여 줍니다.

- **Kubelet**: 키 노드 운영 통계를 표시 하는 두 개의 그리드를 포함 합니다.

    - 노드당 개요 그리드는 각 노드에 대 한 백분율 및 추세 별로 총 작업, 총 오류 및 성공한 작업을 요약 합니다.
    - 작업 유형별 개요 유형별 합계 작업, 총 오류, 성공 작업 (백분율 및 추세)을 요약 합니다.
## <a name="billing-workbooks"></a>청구 통합 문서

- **데이터 사용**: 설명서에서 공유 하는 내용에서 고유한 쿼리 라이브러리를 빌드하지 않고도 데이터 원본을 시각화할 수 있습니다. 이 통합 문서에는 다음과 같은 관점에서 청구 가능한 데이터를 볼 수 있는 차트가 있습니다.

  - 수집 총 청구 가능 데이터 (GB)
  - 컨테이너 로그 (응용 프로그램 로그)로 청구 가능한 데이터 수집
  - 청구 가능 컨테이너 로그 데이터 수집 per Kubernetes 네임 스페이스
  - 클러스터 이름으로 분리 된 청구 가능 컨테이너 로그 데이터 수집
  - Logsource 항목 수집 청구 가능 컨테이너 로그 데이터
  - 진단 마스터 노드 로그에 의해 수집 청구 가능한 진단 데이터

## <a name="networking-workbooks"></a>네트워킹 통합 문서

- **NPM 구성**: 네트워크 정책 관리자 (NPM)를 통해 구성 된 네트워크 구성에 대 한 모니터링입니다.

  - 전반적인 구성 복잡성에 대 한 요약 정보입니다.
  - 시간에 따른 정책, 규칙 및 집합 수를 통해 세 가지 간의 관계를 파악 하 고 구성을 디버그할 시간 차원을 추가할 수 있습니다.
  - 모든 IPSets 및 각 Ipsets의 항목 수입니다.
  - 네트워크 구성에 구성 요소를 추가 하기 위한 노드당 최악의 및 평균 사례 성능입니다.

- **네트워크**: 각 노드의 네트워크 어댑터에 대 한 대화형 네트워크 사용률 차트와 그리드는 네트워크 어댑터의 성능을 측정 하는 데 도움이 되는 핵심 성과 지표를 제공 합니다.



## <a name="next-steps"></a>다음 단계

- Azure Monitor의 통합 문서에 대 한 자세한 내용은 [Azure Monitor 통합 문서](../platform/workbooks-overview.md) 를 참조 하세요.
