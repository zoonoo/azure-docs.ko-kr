---
title: Azure의 Kubernetes 자습서 - 응용 프로그램 크기 조정
description: AKS 자습서 - 응용 프로그램 크기 조정
services: container-service
author: dlepow
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 02/22/2018
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 22f7f9aee791d315300ffdc4dc9f708a80a5baf7
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39127413"
---
# <a name="tutorial-scale-application-in-azure-kubernetes-service-aks"></a>자습서: AKS(Azure Kubernetes Service)에서 응용 프로그램 크기 조정

자습서를 수행하고 있다면 AKS에서 작동하는 Kubernetes 클러스터가 있으며 Azure Voting 앱을 배포한 상태입니다.

7개 중 5단계인 이 자습서에서는 앱의 Pod를 스케일 아웃하고 Pod 자동 크기 조정을 시도합니다. Azure VM 노드 수를 조정하여 워크로드 호스팅을 위한 클러스터 용량을 변경하는 방법도 알아봅니다. 완료된 작업은 다음과 같습니다.

> [!div class="checklist"]
> * Kubernetes Azure 노드 크기 조정
> * 수동으로 Kubernetes Pod 크기 조정
> * 앱 프런트 엔드를 실행하는 Pod 자동 크기 조정 구성

후속 자습서에서 Azure Vote 응용 프로그램은 새 버전으로 업데이트됩니다.

## <a name="before-you-begin"></a>시작하기 전에

이전 자습서에서는 응용 프로그램을 컨테이너 이미지에 패키지하고, 이 이미지를 Azure Container Registry에 업로드하고, Kubernetes 클러스터를 만들었습니다. 그런 다음 Kubernetes 클러스터에서 응용 프로그램을 실행했습니다.

이러한 단계를 아직 수행하지 않았으나 수행하려는 경우 [자습서 1 - 컨테이너 이미지 만들기][aks-tutorial-prepare-app]로 돌아갑니다.

## <a name="scale-aks-nodes"></a>AKS 노드 크기 조정

이전 자습서에서 명령을 사용하여 Kubernetes 클러스터를 만든 경우 한 개의 노드가 있습니다. 클러스터에 대한 컨테이너 워크로드를 더 늘리거나 줄일 계획인 경우 수동으로 노드 수를 조정할 수 있습니다.

다음 예제에서는 *myAKSCluster*라는 Kubernetes 클러스터의 노드 수를 3개로 늘립니다. 이 명령은 완료되는 데 2~3분이 걸립니다.

```azurecli
az aks scale --resource-group=myResourceGroup --name=myAKSCluster --node-count 3
```

다음과 유사하게 출력됩니다.

```
"agentPoolProfiles": [
  {
    "count": 3,
    "dnsPrefix": null,
    "fqdn": null,
    "name": "myAKSCluster",
    "osDiskSizeGb": null,
    "osType": "Linux",
    "ports": null,
    "storageProfile": "ManagedDisks",
    "vmSize": "Standard_D2_v2",
    "vnetSubnetId": null
  }
```

## <a name="manually-scale-pods"></a>수동으로 Pod 크기 조정

따라서 Azure Vote 프런트 엔드 및 Redis 인스턴스가 각각 단일 복제본으로 배포되었습니다. 확인하려면 [kubectl get][kubectl-get] 명령을 실행합니다.

```azurecli
kubectl get pods
```

출력:

```
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-2549686872-4d2r5   1/1       Running   0          31m
azure-vote-front-848767080-tf34m   1/1       Running   0          31m
```

[kubectl scale][kubectl-scale] 명령을 사용하여 수동으로 `azure-vote-front` 배포의 Pod 수를 변경합니다. 이 예제에서는 수를 5로 늘립니다.

```azurecli
kubectl scale --replicas=5 deployment/azure-vote-front
```

[kubectl get pods][kubectl-get]를 실행하여 Kubernetes에서 Pod를 만들고 있는지 확인합니다. 1분 정도 지나면 추가 Pod가 실행되고 있습니다.

```azurecli
kubectl get pods
```

출력:

```
NAME                                READY     STATUS    RESTARTS   AGE
azure-vote-back-2606967446-nmpcf    1/1       Running   0          15m
azure-vote-front-3309479140-2hfh0   1/1       Running   0          3m
azure-vote-front-3309479140-bzt05   1/1       Running   0          3m
azure-vote-front-3309479140-fvcvm   1/1       Running   0          3m
azure-vote-front-3309479140-hrbf2   1/1       Running   0          15m
azure-vote-front-3309479140-qphz8   1/1       Running   0          3m
```

## <a name="autoscale-pods"></a>Pod 자동 크기 조정

Kubernetes는 [수평 Pod 자동 크기 조정][kubernetes-hpa]을 지원하여 CPU 사용률 또는 다른 선택 메트릭에 따라 배포에서 Pod 수를 조정할 수 있게 해줍니다. [메트릭 서버][metrics-server]는 Kubernetes에 리소스 사용률을 제공하는 데 사용됩니다. 메트릭 서버를 설치하려면 `metrics-server` GitHub 리포지토리를 복제하고 예제 리소스 정의를 설치합니다. 이러한 YAML 정의의 콘텐츠를 참조하려면 [Kuberenetes 1.8+에 대한 메트릭 서버][metrics-server-github]를 참조하세요.

```console
git clone https://github.com/kubernetes-incubator/metrics-server.git
kubectl create -f metrics-server/deploy/1.8+/
```

자동 크기 조정기를 사용하려면 Pod에 CPU 요청 및 제한이 정의되어 있어야 합니다. `azure-vote-front` 배포에서 프런트 엔드 컨테이너는 0.25 CPU를 요청하며 제한은 0.5 CPU입니다. 설정은 다음과 같습니다.

```YAML
resources:
  requests:
     cpu: 250m
  limits:
     cpu: 500m
```

다음 예제에서는 [kubectl autoscale][kubectl-autoscale] 명령을 사용하여 `azure-vote-front` 배포의 Pod 수를 자동으로 조정합니다. 여기서는 CPU 사용률이 50%를 초과하면 자동 크기 조정기가 Pod를 최대 10개로 늘립니다.

```azurecli
kubectl autoscale deployment azure-vote-front --cpu-percent=50 --min=3 --max=10
```

자동 조정기의 상태를 확인하려면 다음 명령을 실행합니다.

```azurecli
kubectl get hpa
```

출력:

```
NAME               REFERENCE                     TARGETS    MINPODS   MAXPODS   REPLICAS   AGE
azure-vote-front   Deployment/azure-vote-front   0% / 50%   3         10        3          2m
```

Azure Vote 앱에 최소 부하를 적용한 상태로 몇 분이 지나면 Pod 복제본 수가 자동으로 3개로 줄어듭니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Kubernetes 클러스터의 다양한 크기 조정 기능을 사용했습니다. 설명한 작업은 다음과 같습니다.

> [!div class="checklist"]
> * 수동으로 Kubernetes Pod 크기 조정
> * 앱 프런트 엔드를 실행하는 Pod 자동 크기 조정 구성
> * Kubernetes Azure 노드 크기 조정

다음 자습서로 이동하여 Kubernetes에서 응용 프로그램 업데이트에 대해 알아봅니다.

> [!div class="nextstepaction"]
> [Kubernetes에서 응용 프로그램 업데이트][aks-tutorial-update-app]

<!-- LINKS - external -->
[kubectl-autoscale]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#autoscale
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-scale]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#scale
[kubernetes-hpa]: https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/
[metrics-server-github]: https://github.com/kubernetes-incubator/metrics-server/tree/master/deploy/1.8%2B
[metrics-server]: https://kubernetes.io/docs/tasks/debug-application-cluster/core-metrics-pipeline/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-update-app]: ./tutorial-kubernetes-app-update.md
