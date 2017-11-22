---
title: "Azure의 Kubernetes 자습서 - 클러스터 업데이트 | Microsoft Docs"
description: "Azure의 Kubernetes 자습서 - 클러스터 업데이트"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: aks, azure-container-service
keywords: "Docker, 컨테이너, 마이크로 서비스, Kubernetes, DC/OS, Azure"
ms.assetid: 
ms.service: container-service
ms.devlang: aurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: d0193e891c6c41687f1aaa3a8033bc71e85b10c3
ms.sourcegitcommit: c25cf136aab5f082caaf93d598df78dc23e327b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2017
---
# <a name="upgrade-kubernetes-in-azure-container-service-aks"></a>AKS(Azure Container Service)에서 Kubernetes 업그레이드

Azure CLI를 사용하여 AKS(Azure Container Service) 클러스터를 업그레이드할 수 있습니다. 업그레이드 프로세스 중에 Kubernetes 노드는 신중하게 [코드화되고 드레이닝되어](https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/) 실행 중인 응용 프로그램의 중단을 최소화합니다.

총 8부 중 8부인 이 자습서에서는 Kubernetes 클러스터가 업그레이드됩니다. 완료하는 작업은 다음과 같습니다.

> [!div class="checklist"]
> * 현재 및 사용 가능한 Kubernetes 버전 확인
> * Kubernetes 노드 업그레이드
> * 성공적인 업그레이드의 유효성 검사

## <a name="before-you-begin"></a>시작하기 전에

이전 자습서에서는 응용 프로그램을 컨테이너 이미지에 패키지하고, 이 이미지를 Azure Container Registry에 업로드하고, Kubernetes 클러스터를 만들었습니다. 그런 다음 Kubernetes 클러스터에서 응용 프로그램을 실행했습니다.

이러한 단계를 수행하지 않은 경우 수행하려면 [자습서 1 - 컨테이너 이미지 만들기](./tutorial-kubernetes-prepare-app.md)로 돌아갑니다.


## <a name="get-cluster-versions"></a>클러스터 버전 가져오기

클러스터를 업그레이드하기 전에 `az aks get-versions` 명령을 사용하여 업그레이드할 수 있는 Kubernetes 릴리스를 확인합니다.

```azurecli-interactive
az aks get-versions --name myK8sCluster --resource-group myResourceGroup --output table
```

여기서 현재 노드 버전이 `1.7.7`이고, `1.7.9`, `1.8.1` 및 `1.8.2` 버전을 사용할 수 있다는 것을 알 수 있습니다.

```
Name     ResourceGroup    MasterVersion    MasterUpgrades       NodePoolVersion     NodePoolUpgrades
-------  ---------------  ---------------  -------------------  ------------------  -------------------
default  myAKSCluster     1.7.7            1.8.2, 1.7.9, 1.8.1  1.7.7               1.8.2, 1.7.9, 1.8.1
```

## <a name="upgrade-cluster"></a>클러스터 업그레이드

`az aks upgrade` 명령을 사용하여 클러스터 노드를 업그레이드합니다. 다음 예에서는 클러스터를 버전 `1.8.2`로 업데이트합니다.

```azurecli-interactive
az aks upgrade --name myK8sCluster --resource-group myResourceGroup --kubernetes-version 1.8.2
```

출력:

```json
{
  "id": "/subscriptions/4f48eeae-9347-40c5-897b-46af1b8811ec/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myK8sCluster",
  "location": "eastus",
  "name": "myK8sCluster",
  "properties": {
    "accessProfiles": {
      "clusterAdmin": {
        "kubeConfig": "..."
      },
      "clusterUser": {
        "kubeConfig": "..."
      }
    },
    "agentPoolProfiles": [
      {
        "count": 1,
        "dnsPrefix": null,
        "fqdn": null,
        "name": "myK8sCluster",
        "osDiskSizeGb": null,
        "osType": "Linux",
        "ports": null,
        "storageProfile": "ManagedDisks",
        "vmSize": "Standard_D2_v2",
        "vnetSubnetId": null
      }
    ],
    "dnsPrefix": "myK8sClust-myResourceGroup-4f48ee",
    "fqdn": "myk8sclust-myresourcegroup-4f48ee-406cc140.hcp.eastus.azmk8s.io",
    "kubernetesVersion": "1.8.2",
    "linuxProfile": {
      "adminUsername": "azureuser",
      "ssh": {
        "publicKeys": [
          {
            "keyData": "..."
          }
        ]
      }
    },
    "provisioningState": "Succeeded",
    "servicePrincipalProfile": {
      "clientId": "e70c1c1c-0ca4-4e0a-be5e-aea5225af017",
      "keyVaultSecretRef": null,
      "secret": null
    }
  },
  "resourceGroup": "myResourceGroup",
  "tags": null,
  "type": "Microsoft.ContainerService/ManagedClusters"
}
```

## <a name="validate-upgrade"></a>업그레이드 유효성 검사

이제 `az aks show` 명령을 사용하여 업그레이드가 성공적이었는지 확인할 수 있습니다.

```azurecli-interactive
az aks show --name myK8sCluster --resource-group myResourceGroup --output table
```

출력:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------------------------
myK8sCluster  eastus     myResourceGroup  1.8.2                Succeeded            myk8sclust-myresourcegroup-3762d8-2f6ca801.hcp.eastus.azmk8s.io
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 AKS 클러스터에서 Kubernetes를 업그레이드했습니다. 다음 작업을 완료했습니다.

> [!div class="checklist"]
> * 현재 및 사용 가능한 Kubernetes 버전 확인
> * Kubernetes 노드 업그레이드
> * 성공적인 업그레이드의 유효성 검사

AKS에 대해 자세히 알아보려면 다음 링크를 참조하세요.

> [!div class="nextstepaction"]
> [AKS 개요](./intro-kubernetes.md)