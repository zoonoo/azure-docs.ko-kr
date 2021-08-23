---
title: 컨테이너 인사이트 배포 보기(미리 보기) | Microsoft Docs
description: 이 문서에서는 컨테이너 인사이트에서 kubectl을 사용하지 않고 Kubernetes 배포에 대한 실시간 보기를 설명합니다.
ms.topic: conceptual
ms.date: 10/15/2019
ms.custom: references_regions
ms.openlocfilehash: 6b3ae515f2addf21f8183aed83ab26922e77f981
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122566670"
---
# <a name="how-to-view-deployments-preview-in-real-time"></a>실시간으로 배포(미리 보기)를 보는 방법

컨테이너 인사이트를 통해 배포 보기(미리 보기) 기능을 사용하면 `kubeclt get deployments` 및 `kubectl describe deployment {your deployment}` 명령을 노출하여 Kubernetes 배포 개체에 대한 직접 액세스를 실시간으로 에뮬레이트합니다.

>[!NOTE]
>[프라이빗 클러스터](https://azure.microsoft.com/updates/aks-private-cluster/)로 설정된 AKS 클러스터는 이 기능에서 지원되지 않습니다. 이 기능은 브라우저에서 프록시 서버를 통해 Kubernetes API에 직접 액세스할 수 밖에 없습니다. 이 프록시의 Kubernetes API를 차단하도록 네트워킹 보안을 사용하도록 설정하면 이 트래픽이 차단됩니다.

[배포](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)에 대한 자세한 내용은 Kubernetes 설명서를 검토하세요.

## <a name="how-it-works"></a>작동 방법

라이브 데이터(미리 보기) 기능은 Kubernetes API에 직접 액세스할 수 있으며 인증 모델에 대한 추가 정보는 [여기](https://kubernetes.io/docs/concepts/overview/kubernetes-api/)에서 찾을 수 있습니다.

배포(미리 보기) 기능은 배포 엔드포인트 `/apis/apps/v1/deployments`에 대해 한 번(새로 고칠 수 있는) 로드를 수행합니다. 이를 통해 지정된 배포를 선택하고 배포 엔드포인트 `/apis/apps/v1/namespaces/${nameSpace}/deployments/${deploymentName}`에 대해 특정 배포에 대한 설명 세부 정보를 로드할 수 있습니다.

페이지의 왼쪽 위에 있는 **새로 고침** 을 선택하면 배포 목록을 새로 고침합니다. 이렇게 하면 `kubectl` 명령의 재실행 작업을 시뮬레이션합니다.

>[!IMPORTANT]
>이 기능을 수행하는 동안에는 데이터가 영구적으로 저장되지 않습니다. 세션 중에 캡처된 모든 정보는 브라우저를 닫거나 다른 곳으로 이동하면 삭제됩니다.

>[!NOTE]
>콘솔에서 Azure 대시보드에 라이브 데이터(미리 보기) 데이터를 고정할 수 없습니다.

## <a name="deployments-describe"></a>배포 설명

`kubectl describe deployment`에 해당하는 배포에 대한 세부 정보를 보려면 다음 단계를 수행합니다.

1. Azure Portal에서 AKS 클러스터 리소스 그룹으로 이동하여 AKS 리소스를 선택합니다.

2. AKS 클러스터 대시보드의 왼쪽에 있는 **모니터링** 에서 **인사이트** 를 선택합니다.

3. **배포(미리 보기)** 탭을 선택합니다.

    ![Azure Portal의 배포 보기](./media/container-insights-livedata-deployments/deployment-view.png)

보기에는 `kubectl get deployments –all-namespaces` 명령 실행을 에뮬레이트하여 실행 중인 모든 배포 목록과 네임스페이스 및 기타 세부 정보가 표시됩니다. 열 중 하나를 선택하여 결과를 정렬할 수 있습니다.

![배포 속성 창 세부 정보](./media/container-insights-livedata-deployments/deployment-properties-pane-details.png)

목록에서 배포를 선택하면 속성 창이 페이지 오른쪽에 자동으로 표시됩니다. `kubectl describe deployment {deploymentName}` 명령을 실행하면 표시되는 선택된 배포와 관련된 정보를 표시합니다. 설명 정보에 몇 가지 세부 정보가 누락된 것을 알 수 있습니다. 특히 **템플릿** 이 누락되었습니다. **원시** 탭을 선택하면 구문 분석되지 않은 설명 세부 정보로 이동할 수 있습니다.

![배포 속성 창 원시 세부 정보](./media/container-insights-livedata-deployments/deployment-properties-pane-raw.png)

배포 세부 정보를 검토하는 동안 컨테이너 로그 및 이벤트를 실시간으로 볼 수 있습니다. **라이브 콘솔 표시** 를 선택하면 라이브 데이터 표시(미리 보기) 콘솔 창이 배포 데이터 그리드 아래에 표시되며, 여기에서 연속 스트림으로 라이브 로그 데이터를 볼 수 있습니다. 페치 상태 표시기에 창의 맨 오른쪽에 있는 녹색 확인 표시가 나타나면 데이터를 검색할 수 있으며 콘솔에 대한 스트리밍을 시작합니다.

네임스페이스 또는 클러스터 수준 이벤트를 기준으로 필터링할 수도 있습니다. 콘솔에서 실시간으로 데이터를 보는 방법에 대한 자세한 내용은 [컨테이너 인사이트를 사용하여 라이브 데이터 보기(미리 보기)](container-insights-livedata-overview.md)를 참조하세요.

![콘솔의 배포 보기 라이브 데이터](./media/container-insights-livedata-deployments/deployments-console-view-events.png)

## <a name="next-steps"></a>다음 단계

- Azure Monitor를 사용하고 AKS 클러스터의 다른 측면을 모니터링하는 방법을 계속 알아보려면 [Azure Kubernetes 서비스 상태](container-insights-analyze.md)를 참조하세요.

- [로그 쿼리 예](container-insights-log-query.md)를 보면서 미리 정의된 쿼리 및 예제를 확인하고 경고, 시각화를 만들거나 클러스터에 대한 추가 분석을 수행합니다.
