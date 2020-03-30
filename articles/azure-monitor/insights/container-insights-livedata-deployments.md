---
title: 컨테이너 배포에 대한 Azure 모니터 보기(미리 보기) | 마이크로 소프트 문서
description: 이 문서에서는 컨테이너에 대 한 Azure 모니터에서 kubectl을 사용 하지 않고 Kubernetes 배포의 실시간 보기를 설명 합니다.
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 7d0344851e1db8c014a1bb16b228a0c2f76444d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75404766"
---
# <a name="how-to-view-deployments-preview-in-real-time"></a>실시간으로 배포(미리 보기)를 보는 방법

컨테이너용 Azure Monitor를 사용하면 배포 보기(미리 보기) 기능은 `kubeclt get deployments` 및 `kubectl describe deployment {your deployment}` 명령을 노출하여 Kubernetes 배포 개체에 대한 직접 액세스를 실시간으로 에뮬레이트합니다. 

>[!NOTE]
>개인 클러스터로 활성화된 AKS [클러스터는](https://azure.microsoft.com/updates/aks-private-cluster/) 이 기능에서 지원되지 않습니다. 이 기능은 브라우저에서 프록시 서버를 통해 Kubernetes API에 직접 액세스하는 데 의존합니다. 네트워킹 보안을 사용하여 이 프록시에서 Kubernetes API를 차단하면 이 트래픽이 차단됩니다. 

>[!NOTE]
>이 기능은 Azure China를 포함한 모든 Azure 지역에서 사용할 수 있습니다. 현재 Azure 미국 정부에서는 사용할 수 없습니다.

자세한 내용은 배포에 대한 Kubernetes [설명서를 검토합니다.](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/) 

## <a name="how-it-works"></a>작동 방법

라이브 데이터(미리 보기) 기능은 Kubernetes API에 직접 액세스하며 인증 모델에 대한 추가 정보는 [여기에서](https://kubernetes.io/docs/concepts/overview/kubernetes-api/)확인할 수 있습니다. 

배포(미리 보기) 기능은 배포 끝점에 `/apis/apps/v1/deployments`대해 한 번(새로 고칠 수 있는) 부하를 수행합니다. 이를 통해 지정된 배포를 선택하고 배포 끝점에 `/apis/apps/v1/namespaces/${nameSpace}/deployments/${deploymentName}`대해 해당 특정 배포에 대한 설명 세부 정보를 로드할 수 있습니다. 

페이지 왼쪽 상단에서 **새로 고침을** 선택하면 배포 목록이 새로 고쳐집니다. 이렇게 하면 명령을 다시 `kubectl` 실행해야 합니다. 

>[!IMPORTANT]
>이 기능을 작동하는 동안 데이터가 영구적으로 저장되지 않습니다. 세션 중에 캡처된 모든 정보는 브라우저를 닫거나 브라우저에서 멀리 이동할 때 삭제됩니다.  

>[!NOTE]
>콘솔에서 Azure 대시보드에 라이브 데이터(미리 보기) 데이터를 고정할 수 없습니다.

## <a name="deployments-describe"></a>배포에 설명

`kubectl describe deployment`에 해당하는 배포에 대한 세부 정보 설명(을 참조하십시오)을 보려면 다음 단계를 수행합니다.

1. Azure 포털에서 AKS 클러스터 리소스 그룹을 찾아보고 AKS 리소스를 선택합니다.

2. AKS 클러스터 대시보드에서 왼쪽의 **모니터링** 아래에서 **Insights를**선택합니다. 

3. **배포(미리 보기) 탭을 선택합니다.**

    ![Azure 포털에서 배포 보기](./media/container-insights-livedata-deployments/deployment-view.png)

이 보기에는 네임스페이스 및 기타 자세한 정보와 함께 실행 중인 모든 배포 목록이 `kubectl get deployments –all-namespaces`표시되어 명령의 실행을 에뮬레이션합니다. 열 중 하나를 선택하여 결과를 정렬할 수 있습니다. 

![배포 속성 창 세부 정보](./media/container-insights-livedata-deployments/deployment-properties-pane-details.png)

목록에서 배포를 선택하면 속성 창이 페이지 오른쪽에 자동으로 표시됩니다. 명령을 `kubectl describe deployment {deploymentName}`실행한 경우 볼 수 있는 선택한 배포와 관련된 정보가 표시됩니다. 설명 정보에 몇 가지 세부 정보가 누락된 것으로 나타났습니다. 특히 **템플릿이** 없습니다. **원시** 탭을 선택하면 구문 분석되지 않은 설명 세부 정보로 이동할 수 있습니다.  

![배포 속성 창 원시 세부 정보](./media/container-insights-livedata-deployments/deployment-properties-pane-raw.png)

배포 세부 정보를 검토하는 동안 컨테이너 로그 및 이벤트를 실시간으로 볼 수 있습니다. 라이브 **콘솔 표시를** 선택하면 라이브 데이터(미리 보기) 콘솔 창이 배포 데이터 그리드 아래에 표시되며 여기서 연속 스트림에서 라이브 로그 데이터를 볼 수 있습니다. 가져오기 상태 표시기에서 창의 맨 오른쪽에 있는 녹색 확인 표시가 표시되면 데이터를 검색할 수 있으며 본체로 스트리밍을 시작합니다.

네임스페이스 또는 클러스터 수준 이벤트별로 필터링할 수도 있습니다. 콘솔에서 실시간으로 데이터 보기에 대한 자세한 내용은 [컨테이너에 대한 Azure 모니터를 사용하여 라이브 데이터 보기(미리 보기)를](container-insights-livedata-overview.md)참조하십시오. 

![배포는 콘솔에서 라이브 데이터를 봅니다.](./media/container-insights-livedata-deployments/deployments-console-view-events.png)

## <a name="next-steps"></a>다음 단계

- Azure 모니터를 사용하고 AKS 클러스터의 다른 측면을 모니터링하는 방법을 계속 알아보려면 [Azure Kubernetes 서비스 상태 보기를](container-insights-analyze.md)참조하십시오.

- [로그 쿼리 예제를](container-insights-log-search.md#search-logs-to-analyze-data) 보고 미리 정의된 쿼리 및 예제를 확인하여 경고, 시각화를 만들거나 클러스터에 대한 추가 분석을 수행합니다.
