---
title: Service Fabric 관리형 클러스터의 노드 유형 추가 및 제거(미리 보기)
description: 이 자습서에서는 Service Fabric 관리형 클러스터의 노드 유형을 추가 및 제거하는 방법에 대해 알아봅니다.
ms.topic: tutorial
ms.date: 09/28/2020
ms.openlocfilehash: 860345f5b297edaeea9d099ac392243176dfaca7
ms.sourcegitcommit: b48e8a62a63a6ea99812e0a2279b83102e082b61
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2020
ms.locfileid: "91410251"
---
# <a name="tutorial-add-and-remove-node-types-from-a-service-fabric-managed-cluster-preview"></a>자습서: Service Fabric 관리형 클러스터에서 노드 유형 추가 및 제거(미리 보기)

이 자습서 시리즈에서는 다음을 설명합니다.

> [!div class="checklist"]
> * [Service Fabric 관리형 클러스터 배포 방법](tutorial-managed-cluster-deploy.md)
> * [Service Fabric 관리형 클러스터 확장 방법](tutorial-managed-cluster-scale.md)
> * Service Fabric 관리형 클러스터에서 노드를 추가 및 제거하는 방법
> * [Service Fabric 관리형 클러스터에 애플리케이션을 배포하는 방법](tutorial-managed-cluster-deploy-app.md)

시리즈의 이번 부분에서는 다음 작업을 수행하는 방법에 대해 설명합니다.

> [!div class="checklist"]
> * Service Fabric 관리형 클러스터에 노드 유형 추가
> * Service Fabric 관리형 클러스터에서 노드 유형 삭제

## <a name="prerequisites"></a>사전 요구 사항

* Service Fabric 관리형 클러스터([*관리형 클러스터 배포*](tutorial-managed-cluster-deploy.md) 참조)
* [Azure PowerShell 4.7.0](https://docs.microsoft.com/powershell/azure/release-notes-azureps?view=azps-4.7.0&preserve-view=true#azservicefabric) 이상([*Azure PowerShell 설치*](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-4.7.0&preserve-view=true) 참조)

## <a name="add-a-node-type-to-a-service-fabric-managed-cluster"></a>Service Fabric 관리형 클러스터에 노드 유형 추가

Azure Resource Manager 템플릿, PowerShell 또는 CLI를 통해 노드 유형을 Service Fabric 관리형 클러스터에 추가할 수 있습니다. 이 자습서에서는 Azure PowerShell을 사용하여 노드 유형을 추가합니다.

새 노드 유형을 만들려면 다음 세 가지 속성을 정의해야 합니다.

* **노드 유형 이름**: 클러스터의 기존 노드 유형에서 고유한 이름입니다.
* **인스턴트 개수**: 새 노드 유형의 초기 노드 수입니다.
* **VM 크기**: 노드에 대한 VM SKU입니다. 지정하지 않으면 기본값 *Standard_D2*가 사용됩니다.

> [!NOTE]
> 추가하는 노드 유형이 클러스터에서 첫 번째 또는 유일한 노드 유형인 경우 Primary 속성을 사용해야 합니다.

```powershell
$resourceGroup = "myResourceGroup"
$clusterName = "mysfcluster"
$nodeTypeName = "nt2"
$vmSize = "Standard_D2_v2"

New-AzServiceFabricManagedNodeType -ResourceGroupName $resourceGroup -ClusterName $clusterName -Name $nodeTypeName -InstanceCount 3 -vmSize $vmSize
```

## <a name="remove-a-node-type-from-a-service-fabric-managed-cluster"></a>Service Fabric 관리형 클러스터에서 노드 유형 제거

Service Fabric 관리형 클러스터에서 노드 유형을 제거하려면 PowerShell 또는 CLI를 사용해야 합니다. 이 자습서에서는 Azure PowerShell을 사용하여 노드 유형을 제거합니다.

> [!NOTE]
> 클러스터의 유일한 주 노드 유형인 경우 주 노드 유형을 제거할 수 없습니다.  

노드 유형을 제거하려면 다음을 수행합니다.

```powershell
$resourceGroup = "myResourceGroup"
$clusterName = "myCluster"
$nodeTypeName = "nt2"

Remove-AzServiceFabricManagedNodeType -ResourceGroupName $resourceGroup -ClusterName $clusterName  -Name $nodeTypeName
```

## <a name="next-steps"></a>다음 단계

 이 섹션에서는 노드 유형을 추가하고 삭제했습니다. Service Fabric 관리형 클러스터에 애플리케이션을 배포하는 방법에 대한 자세한 내용은 다음을 참조하세요.

> [!div class="nextstepaction"]
> [Service Fabric 관리형 클러스터에 앱 배포](tutorial-managed-cluster-deploy-app.md)
