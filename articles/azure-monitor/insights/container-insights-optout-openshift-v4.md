---
title: Azure 및 Red Hat OpenShift v4 클러스터의 모니터링을 중지 하는 방법 | Microsoft Docs
description: 이 문서는 컨테이너에 대 한 Azure Monitor를 사용 하 여 Azure Red Hat OpenShift 및 Red Hat OpenShift 버전 4 클러스터의 모니터링을 중지 하는 방법을 설명 합니다.
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: bf61457b9c8cff40eb3fee2c93c7184fbaae6db5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87091150"
---
# <a name="how-to-stop-monitoring-your-azure-and-red-hat-openshift-v4-cluster"></a>Azure 및 Red Hat OpenShift v4 클러스터의 모니터링을 중지 하는 방법

Azure Red Hat OpenShift 및 Red Hat OpenShift 버전 4.x 클러스터의 모니터링을 사용 하도록 설정한 후에는 더 이상 모니터링 하지 않기로 결정 한 경우 컨테이너에 대해 Azure Monitor를 사용 하 여 클러스터의 모니터링을 중지할 수 있습니다. 이 문서에서는이를 수행 하는 방법을 보여 줍니다.  

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

    그러면 클러스터에서 릴리스가 제거 됩니다. 다음 명령을 실행 하 여 확인할 수 있습니다 `helm list` .

    ```
    NAME                            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                           APP VERSION
    ```

구성 변경을 완료하려면 몇 분 정도 걸릴 수 있습니다. 투구는 삭제 한 후에도 릴리스를 추적 하므로, 클러스터의 기록을 감사 하 고를 사용 하 여 릴리스를 삭제 취소할 수 있습니다 `helm rollback` .

## <a name="next-steps"></a>다음 단계

클러스터 모니터링을 지원 하기 위해 Log Analytics 작업 영역을 만들었지만 더 이상 필요 하지 않은 경우 수동으로 삭제 해야 합니다. 작업 영역을 삭제 하는 방법에 익숙하지 않은 경우 [Azure Log Analytics 작업 영역 삭제](../platform/delete-workspace.md)를 참조 하세요.
