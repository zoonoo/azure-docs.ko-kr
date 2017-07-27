---
title: "Azure Container Service 자습서 - 응용 프로그램 크기 조정 | Microsoft Docs"
description: "Azure Container Service 자습서 - 응용 프로그램 크기 조정"
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, 컨테이너, 마이크로 서비스, Kubernetes, Azure"
ms.assetid: 
ms.service: container-service
ms.devlang: aurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/26/2017
ms.author: danlep
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: e72e33e4c8511ab3daa0b9ccbefb5fde887bc0f9
ms.contentlocale: ko-kr
ms.lasthandoff: 06/28/2017

---

# <a name="scale-kubernetes-pods-and-kubernetes-infrastructure"></a>Kubernetes Pod 및 Kubernetes 인프라 크기 조정

자습서를 수행해 오고 있었다면 Azure Container Service에 작동하는 Kubernetes 클러스터가 있으며 Azure 투표 앱을 배포한 상태입니다. 

이 자습서에서는 앱의 Pod 규모를 확장하고 Pod 자동 크기 조정을 시도합니다. 에이전트 노드 수를 조정하여 워크로드 호스팅을 위한 클러스터 용량을 변경하는 방법도 알아봅니다. 완료된 작업은 다음과 같습니다.

> [!div class="checklist"]
> * 수동으로 Kubernetes Pod 크기 조정
> * 앱 프런트 엔드를 실행하는 Pod 자동 크기 조정 구성
> * Kubernetes Azure 에이전트 노드 크기 조정

## <a name="before-you-begin"></a>시작하기 전에

이전 자습서에서는 응용 프로그램을 컨테이너 이미지에 패키지하고, Azure Container Registry에 이러한 이미지를 업로드하고, Kubernetes 클러스터를 만들었습니다. 그런 다음 Kubernetes 클러스터에서 응용 프로그램을 실행했습니다. 이러한 단계를 수행하지 않은 경우 수행하려면 [자습서 1 - 컨테이너 이미지 만들기](./container-service-tutorial-kubernetes-prepare-app.md)로 돌아갑니다. 

최소한 이 자습서에는 실행 중인 응용 프로그램이 있는 Kubernetes 클러스터가 필요합니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하여 사용하도록 선택한 경우 이 자습서에서 Azure CLI 버전 2.0.4 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치]( /cli/azure/install-azure-cli)를 참조하세요. 

## <a name="manually-scale-pods"></a>수동으로 Pod 크기 조정

이전 자습서에서는 Azure 투표 프런트 엔드 및 백 엔드 각각을 단일 Pod에 배포했습니다. 확인하려면 [kubectl get](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get) 명령을 실행합니다.

```bash
kubectl get pods
```

다음과 유사하게 출력됩니다.

```bash
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-2549686872-4d2r5   1/1       Running   0          31m
azure-vote-front-848767080-tf34m   1/1       Running   0          31m
```

[kubectl scale](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#scale) 명령을 사용하여 수동으로 `azure-vote-front` 배포의 Pod 수를 변경합니다. 이 예제에서는 수를 4로 늘립니다.

```bash
kubectl scale --replicas=5 deployment/azure-vote-front
```

[kubectl get pods](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get)를 실행하여 Kubernetes에서 Pod를 만들고 있는지 확인합니다. 1분 정도 지나면 추가 Pod가 실행되고 있습니다.

```bash
kubectl get pods
```

출력:

```bash
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-2549686872-4d2r5   1/1       Running   0          33m
azure-vote-front-848767080-1kt72   1/1       Running   0          1m
azure-vote-front-848767080-2b62d   1/1       Running   0          1m
azure-vote-front-848767080-78rf0   1/1       Running   0          1m
azure-vote-front-848767080-tf34m   1/1       Running   0          33m
```

## <a name="autoscale-pods"></a>Pod 자동 크기 조정

Kubernetes는 [수평 Pod 자동 크기 조정](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/)을 지원하여 CPU 사용률 또는 다른 메트릭에 따라 배포에서 Pod 수를 조정할 수 있게 해줍니다. 

자동 크기 조정기를 사용하려면 Pod에 CPU 요청 및 제한이 정의되어 있어야 합니다. `azure-vote-front` 배포에서 각 컨테이너는 0.25 CPU를 요청하며 제한은 0.5 CPU입니다. 설정은 다음과 같습니다.

```YAML
resources:
  requests:
     cpu: 250m
  limits:
     cpu: 500m
```

다음 예제에서는 [kubectl autoscale](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#autoscale) 명령을 사용하여 `azure-vote-front` 배포의 Pod 수를 자동으로 조정합니다. 여기서는 CPU 사용률이 50%를 초과하면 자동 크기 조정기가 Pod를 최대 10개로 늘립니다.


```bash
kubectl autoscale deployment azure-vote-front --cpu-percent=50 --min=3 --max=10
```

자동 조정기의 상태를 확인하려면 다음 명령을 실행합니다.

```bash
kubectl get hpa
```

출력:

```bash
NAME               REFERENCE                     TARGETS    MINPODS   MAXPODS   REPLICAS   AGE
azure-vote-front   Deployment/azure-vote-front   0% / 50%   3         10        3          2m
```

Azure 투표 앱에 최소 부하를 적용한 상태로 몇 분이 지나면 Pod 복제본 수가 자동으로 3개로 줄어듭니다.

## <a name="scale-the-agents"></a>에이전트 크기 조정

이전 자습서에서 기본 명령을 사용하여 Kubernetes 클러스터를 만든 경우 이 클러스터에는 세 개의 에이전트 노드가 있습니다. 클러스터에 대한 컨테이너 워크로드를 더 늘리거나 줄일 계획인 경우 수동으로 에이전트 수를 조정할 수 있습니다. [az acs scale](/cli/azure/acs#scale) 명령을 사용하고 `--new-agent-count` 매개 변수로 에이전트 수를 지정합니다.

다음 예제에서는 *myK8sCluster*라는 Kubernetes 클러스터의 에이전트 노드 수를 4개로 늘립니다. 이 명령은 완료되는 데 2~3분이 걸립니다.

```azurecli-interactive
az acs scale --resource-group=myResourceGroup --name=myK8SCluster --new-agent-count 4
```

명령 출력에는 에이전트 노드 수가 `agentPoolProfiles:count` 값으로 표시됩니다.

```azurecli
{
  "agentPoolProfiles": [
    {
      "count": 4,
      "dnsPrefix": "myK8SCluster-myK8SCluster-e44f25-k8s-agents",
      "fqdn": "",
      "name": "agentpools",
      "vmSize": "Standard_D2_v2"
    }
  ],
...

```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Kubernetes 클러스터의 다양한 크기 조정 기능을 사용했습니다. 설명한 작업은 다음과 같습니다.

> [!div class="checklist"]
> * 수동으로 Kubernetes Pod 크기 조정
> * 앱 프런트 엔드를 실행하는 Pod 자동 크기 조정 구성
> * Kubernetes Azure 에이전트 노드 크기 조정

다음 자습서로 이동하여 Kubernetes에서 응용 프로그램 업데이트에 대해 알아봅니다.

> [!div class="nextstepaction"]
> [Kubernetes에서 응용 프로그램 업데이트](./container-service-tutorial-kubernetes-app-update.md)


