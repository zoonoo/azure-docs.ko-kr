---
title: "빠른 시작 - Linux용 Azure Kubernetes 클러스터 | Microsoft Docs"
description: "AKS에서 Azure CLI를 사용하여 Linux 컨테이너용 Kubernetes 클러스터를 빠르게 만드는 방법에 대해 알아봅니다."
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: aks, azure-container-service, kubernetes
keywords: 
ms.assetid: 8da267e8-2aeb-4c24-9a7a-65bdca3a82d6
ms.service: container-service
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: nepeters
ms.custom: H1Hack27Feb2017, mvc, devcenter
ms.openlocfilehash: 84f542340f62194a31817a8e358d75c0d0f103ee
ms.sourcegitcommit: c25cf136aab5f082caaf93d598df78dc23e327b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2017
---
# <a name="deploy-an-azure-container-service-aks-cluster"></a>AKS(Azure Container Service) 클러스터 배포

이 빠른 시작에서는 Azure CLI를 사용하여 AKS 클러스터가 배포됩니다. 웹 프런트 엔드 및 Redis 인스턴스로 구성된 다중 컨테이너 응용 프로그램이 클러스터에서 실행됩니다. 완료되면 인터넷을 통해 응용 프로그램에 액세스할 수 있습니다.

![Azure Vote로 이동하는 이미지](media/container-service-kubernetes-walkthrough/azure-vote.png)

이 빠른 시작에서는 Kubernetes 개념에 대한 기본적인 이해가 있다고 가정하며 Kubernetes에 대한 자세한 정보는 [Kubernetes 설명서]( https://kubernetes.io/docs/home/)를 참조하세요.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하여 사용하도록 선택한 경우 이 빠른 시작에서 Azure CLI 버전 2.0.21 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치]( /cli/azure/install-azure-cli)를 참조하세요.

## <a name="enabling-aks-preview-for-your-azure-subscription"></a>Azure 구독에 AKS 미리 보기 사용
AKS가 미리 보기 상태인 경우 새 클러스터를 만들려면 구독에서 기능 플래그가 필요합니다. 사용하려는 구독의 수에 관계 없이 이 기능을 요청할 수 있습니다. `az provider register` 명령을 사용하여 AKS 공급자를 등록합니다.

```azurecli-interactive
az provider register -n Microsoft.ContainerService
```

등록하면 이제 AKS를 사용하여 Kubernetes 클러스터를 만들 준비가 되었습니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[az group create](/cli/azure/group#create) 명령을 사용하여 리소스 그룹을 만듭니다. Azure 리소스 그룹은 Azure 리소스가 배포되고 관리되는 논리 그룹입니다.

다음 예제에서는 *eastus* 위치에 *myResourceGroup*이라는 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

출력:

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup",
  "location": "eastus",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-aks-cluster"></a>AKS 클러스터 만들기

다음 예제에서는 1개의 노드가 있는 *myK8sCluster*라는 클러스터를 만듭니다.

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myK8sCluster --node-count 1 --generate-ssh-keys
```

몇 분 후 명령이 완료되고 클러스터에 대해 JSON 형식 정보가 반환됩니다.

## <a name="connect-to-the-cluster"></a>클러스터에 연결

Kubernetes 클러스터를 관리하려면 Kubernetes 명령줄 클라이언트인 [kubectl](https://kubernetes.io/docs/user-guide/kubectl/)을 사용하세요.

Azure Cloud Shell을 사용하는 경우 kubectl이 이미 설치되어 있습니다. 로컬로 설치하려면 다음 명령을 실행합니다.


```azurecli
az aks install-cli
```

Kubernetes 클러스터에 연결하도록 kubectl을 구성하려면 다음 명령을 실행합니다. 이 단계에서는 자격 증명을 다운로드하고 Kubernetes CLI가 자격 증명을 사용하도록 구성합니다.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myK8sCluster
```

클러스터에 대한 연결을 확인하려면 [kubectl get](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get) 명령을 사용하여 클러스터 노드 목록을 반환합니다.

```azurecli-interactive
kubectl get nodes
```

출력:

```
NAME                          STATUS    ROLES     AGE       VERSION
k8s-myk8scluster-36346190-0   Ready     agent     2m        v1.7.7
```

## <a name="run-the-application"></a>응용 프로그램 실행

Kubernetes 매니페스트 파일은 어떤 컨테이너 이미지가 실행되는지 등과 같은 클러스터에 대해 원하는 상태를 정의합니다. 이 예제에서는 Azure Vote 응용 프로그램을 실행하는 데 필요한 모든 개체를 만드는 데 매니페스트를 사용합니다.

`azure-vote.yml`이라는 파일을 만들고 다음 YAML 코드에 복사합니다. Azure Cloud Shell에서 작업하고 있는 경우 이 파일은 가상 또는 실제 시스템에서 작업하고 있는 것처럼 vi 또는 Nano를 사용하여 만들 수 있습니다.

```yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: azure-vote-back
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: azure-vote-back
    spec:
      containers:
      - name: azure-vote-back
        image: redis
        ports:
        - containerPort: 6379
          name: redis
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-back
spec:
  ports:
  - port: 6379
  selector:
    app: azure-vote-back
---
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: azure-vote-front
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: azure-vote-front
    spec:
      containers:
      - name: azure-vote-front
        image: microsoft/azure-vote-front:redis-v1
        ports:
        - containerPort: 80
        env:
        - name: REDIS
          value: "azure-vote-back"
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

[kubectl create](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#create) 명령을 사용하여 응용 프로그램을 실행합니다.

```azurecli-interactive
kubectl create -f azure-vote.yml
```

출력:

```
deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-the-application"></a>응용 프로그램 테스트

응용 프로그램을 실행하면 응용 프로그램 프런트 엔드를 인터넷에 공개하는 [Kubernetes 서비스](https://kubernetes.io/docs/concepts/services-networking/service/)가 만들어집니다. 이 프로세스를 완료하는 데 몇 분이 걸릴 수 있습니다.

진행 상태를 모니터링하려면 `--watch` 인수와 함께 [kubectl get service](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get) 명령을 사용합니다.

```azurecli-interactive
kubectl get service azure-vote-front --watch
```

처음에는 *azure-vote-front* 서비스에 대한 *EXTERNAL-IP*가 *보류 중*으로 표시됩니다.

```
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

*EXTERNAL-IP* 주소가 *보류 중*에서 *IP 주소*로 변경되면 `CTRL-C`를 사용하여 kubectl 조사식 프로세스를 중지합니다.

```
azure-vote-front   LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

이제 외부 IP 주소로 이동하여 Azure Vote 앱을 볼 수 있습니다.

![Azure Vote로 이동하는 이미지](media/container-service-kubernetes-walkthrough/azure-vote.png)

## <a name="delete-cluster"></a>클러스터 삭제
클러스터가 더 이상 필요하지 않으면 [az group delete](/cli/azure/group#delete) 명령을 사용하여 리소스 그룹, 컨테이너 서비스 및 모든 관련 리소스를 제거할 수 있습니다.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="get-the-code"></a>코드 가져오기

이 빠른 시작에서는 Kubernetes 배포를 만드는 데 미리 생성된 컨테이너 이미지를 사용했습니다. 관련된 응용 프로그램 코드, Dockerfile 및 Kubernetes 매니페스트 파일을 GitHub에서 사용할 수 있습니다.

[https://github.com/Azure-Samples/azure-voting-app-redis](https://github.com/Azure-Samples/azure-voting-app-redis.git)

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Kubernetes 클러스터를 배포하고, 이 클러스터에 다중 컨테이너 응용 프로그램을 배포했습니다.

AKS에 대해 자세히 알아보고 배포 예제에 대한 전체 코드를 연습해 보려면 Kubernetes 클러스터 자습서를 계속 진행합니다.

> [!div class="nextstepaction"]
> [AKS 클러스터 관리](./tutorial-kubernetes-prepare-app.md)
