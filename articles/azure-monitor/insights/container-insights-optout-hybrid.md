---
title: 하이브리드 Kubernetes 클러스터 모니터링을 중지 하는 방법 | Microsoft Docs
description: 이 문서는 컨테이너에 대 한 Azure Monitor를 사용 하 여 hybrid Kubernetes 클러스터의 모니터링을 중지 하는 방법을 설명 합니다.
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: f2f3a8671c1f2baf60d399cc87f2f843dfee4f70
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82196219"
---
# <a name="how-to-stop-monitoring-your-hybrid-cluster"></a>하이브리드 클러스터 모니터링을 중지 하는 방법

Azure Stack 또는 온-프레미스에서 실행 되는 Kubernetes 클러스터의 모니터링을 사용 하도록 설정한 후에는 더 이상 모니터링 하지 않기로 결정 한 경우 컨테이너에 대해 Azure Monitor를 사용 하 여 클러스터의 모니터링을 중지할 수 있습니다. 이 문서에서는이를 수행 하는 방법을 보여 줍니다.  

## <a name="how-to-stop-monitoring-using-helm"></a>투구를 사용 하 여 모니터링을 중지 하는 방법

1. 먼저 클러스터에 설치 된 컨테이너에 대 한 Azure Monitor를 식별 하려면 다음 투구 명령을 실행 합니다.

    ```
    helm list
    ```

    출력은 다음과 유사합니다.

    ```
    NAME                            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                           APP VERSION
    azmon-containers-release-1      default         3               2020-04-21 15:27:24.1201959 -0700 PDT   deployed        azuremonitor-containers-2.7.0   7.0.0-1
    ```

    *azmon-1* 은 컨테이너의 Azure Monitor에 대 한 투구 차트 릴리스를 나타냅니다.

2. 차트 릴리스를 삭제 하려면 다음 투구 명령을 실행 합니다.

    `helm delete <releaseName>`

    예제:

    `helm delete azmon-containers-release-1`

    그러면 클러스터에서 릴리스가 제거 됩니다. 다음 `helm list` 명령을 실행 하 여 확인할 수 있습니다.

    ```
    NAME                            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                           APP VERSION
    ```

구성 변경을 완료하려면 몇 분 정도 걸릴 수 있습니다. 투구는 삭제 한 후에도 릴리스를 추적 하므로, 클러스터의 기록을 감사 하 고를 사용 하 여 `helm rollback`릴리스를 삭제 취소할 수 있습니다.

## <a name="next-steps"></a>다음 단계

클러스터 모니터링을 지원 하기 위해 Log Analytics 작업 영역을 만들었지만 더 이상 필요 하지 않은 경우 수동으로 삭제 해야 합니다. 작업 영역을 삭제 하는 방법에 익숙하지 않은 경우 [Azure Log Analytics 작업 영역 삭제](../../log-analytics/log-analytics-manage-del-workspace.md)를 참조 하세요.
