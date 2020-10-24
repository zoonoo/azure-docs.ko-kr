---
title: 컨테이너에 대 한 Azure Monitor를 사용 하 여 PV 모니터링 구성 | Microsoft Docs
description: 이 문서에서는 Azure Monitor 컨테이너에 대해 영구적 볼륨으로 모니터링 Kubernetes 클러스터를 구성 하는 방법을 설명 합니다.
ms.topic: conceptual
ms.date: 10/20/2020
ms.openlocfilehash: 30e99c2abbc66de257f9623dedc901fca51976c1
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/23/2020
ms.locfileid: "92492175"
---
# <a name="configure-pv-monitoring-with-azure-monitor-for-containers"></a>컨테이너에 대 한 Azure Monitor를 사용 하 여 PV 모니터링 구성

에이전트 버전 *ciprod10052020*부터 컨테이너에 대 한 Azure monitor 통합 에이전트가 이제 PV (영구적 볼륨) 사용량 모니터링을 지원 합니다.

## <a name="pv-metrics"></a>PV 메트릭

컨테이너 Azure Monitor는 60 초 간격으로 다음 메트릭을 수집 하 고 **InsightMetrics** 테이블에 저장 하 여 모니터링 PV를 자동으로 시작 합니다.

|메트릭 이름 |메트릭 차원 (태그) |설명 |
|------------|------------------------|------------|
| `pvUsedBytes`|`container.azm.ms/pv`|특정 pod에서 사용 하는 클레임을 가진 특정 영구적 볼륨에 대해 사용 된 공간 (바이트)입니다. `pvCapacityBytes` 는 데이터 수집 비용을 줄이고 쿼리를 간소화 하기 위해 태그 필드에 차원으로 중첩 됩니다.|

## <a name="monitor-persistent-volumes"></a>영구 볼륨 모니터링

컨테이너에 대 한 Azure Monitor에는이 메트릭에 대 한 미리 구성 된 차트가 모든 클러스터에 대 한 통합 문서에 포함 됩니다. 왼쪽 창에서 통합 문서를 선택 하 고, **정보** 에 있는 **통합 문서 보기** 드롭다운 목록에서 AKS 클러스터의 영구적 볼륨 탭에서 차트를 직접 찾을 수 있습니다. 또한 PV 사용에 대해 권장 되는 경고를 사용 하도록 설정 하 고 Log Analytics에서 이러한 메트릭을 쿼리할 수 있습니다.  

![Azure Monitor PV 워크 로드 통합 문서 예제](./media/container-insights-persistent-volumes/pv-workload-example.PNG)

## <a name="next-steps"></a>다음 단계

- 수집 된 PV 메트릭에 대 한 자세한 내용은 [여기](https://aka.ms/ci/pvconfig)를 참조 하세요.
