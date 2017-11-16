---
title: "Azure Container Service(AKS) 클러스터에 연결 | Microsoft 문서"
description: "Azure Container Service(AKS) 클러스터 업그레이드"
services: container-service
documentationcenter: 
author: gabrtv
manager: timlt
editor: 
tags: aks, azure-container-service
keywords: "Kubernetes, Docker, 컨테이너, 마이크로 서비스, Azure"
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: gamonroy
ms.custom: mvc
ms.openlocfilehash: 15e3e96587962ef9cc531e1825f37b92d26928fd
ms.sourcegitcommit: c25cf136aab5f082caaf93d598df78dc23e327b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2017
---
# <a name="upgrade-an-azure-container-service-aks-cluster"></a>Azure Container Service(AKS) 클러스터 업그레이드

Azure Container Service(AKS)를 사용하면 Kubernetes 클러스터를 업그레이드하는 등 일반적인 관리 작업을 쉽게 수행할 수 있습니다.

## <a name="upgrade-an-aks-cluster"></a>AKS 클러스터 업그레이드

클러스터를 업그레이드하기 전에 `az aks get-versions` 명령을 사용하여 업그레이드할 수 있는 Kubernetes 릴리스를 확인합니다.

```azurecli-interactive
az aks get-versions --name myK8sCluster --resource-group myResourceGroup --output table
```

출력:

```console
Name     ResourceGroup    MasterVersion    MasterUpgrades       NodePoolVersion     NodePoolUpgrades
-------  ---------------  ---------------  -------------------  ------------------  -------------------
default  myResourceGroup  1.7.7            1.8.2, 1.7.9, 1.8.1  1.7.7               1.8.2, 1.7.9, 1.8.1
```

업그레이드할 수 있는 버전으로 세 가지 버전, 즉 1.7.9, 1.8.1 및 1.8.2가 있습니다. `az aks upgrade` 명령을 사용하여 사용 가능한 최신 버전으로 업그레이드할 수 있습니다.  업그레이드 프로세스 중에 노드는 신중하게 [코드화되고 드레이닝되어](https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/) 실행 중인 응용 프로그램의 중단을 최소화합니다.

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

AKS 자습서를 통한 AKS 배포 및 관리에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [AKS 자습서](./tutorial-kubernetes-prepare-app.md)