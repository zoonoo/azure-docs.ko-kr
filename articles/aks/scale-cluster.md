---
title: "Azure Container Service(AKS) 클러스터 크기 조정"
description: "Azure Container Service(AKS) 클러스터의 크기를 조정합니다."
services: container-service
author: gabrtv
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 11/15/2017
ms.author: gamonroy
ms.custom: mvc
ms.openlocfilehash: 299eb74686f00dc6d5eb9a1c6127aa134dcd9b77
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/06/2017
---
# <a name="scale-an-azure-container-service-aks-cluster"></a>Azure Container Service(AKS) 클러스터 크기 조정

서로 다른 노드 수로 AKS 클러스터의 크기를 조정하는 것은 쉽습니다.  원하는 노드 수를 선택하고 `az aks scale` 명령을 실행합니다.  축소하는 경우 노드는 신중하게 [코드화되고 드레이닝되어](https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/) 실행 중인 응용 프로그램의 중단을 최소화합니다.  확장하는 경우 `az` 명령은 노드가 `Ready` Kubernetes 클러스터에 의해 표시될 때까지 대기합니다.

## <a name="scale-the-cluster-nodes"></a>클러스터 노드 크기 조정

`az aks scale` 명령을 사용하여 클러스터 노드의 크기를 조정합니다. 다음 예제에서는 *myK8sCluster*라는 클러스터를 단일 노드로 크기 조정합니다.

```azurecli-interactive
az aks scale --name myK8sCluster --resource-group myResourceGroup --node-count 1
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
    "kubernetesVersion": "1.7.7",
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

## <a name="next-steps"></a>다음 단계

AKS 자습서를 통한 AKS 배포 및 관리에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [AKS 자습서](./tutorial-kubernetes-prepare-app.md)
