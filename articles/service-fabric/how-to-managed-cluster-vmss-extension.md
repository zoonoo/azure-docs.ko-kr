---
title: Service Fabric 관리형 클러스터 노드 형식에 가상 머신 확장 집합 확장 추가
description: Service Fabric 관리형 클러스터 노드 형식에 가상 머신 확장 집합 확장을 추가하는 방법은 다음과 같습니다.
ms.topic: article
ms.date: 5/10/2021
ms.openlocfilehash: 0c0aac8d7804caeb6c08657b1ed36c45958a0ea5
ms.sourcegitcommit: b044915306a6275c2211f143aa2daf9299d0c574
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/29/2021
ms.locfileid: "113033698"
---
# <a name="add-a-virtual-machine-scale-set-extension-to-a-service-fabric-managed-cluster-node-type"></a>Service Fabric 관리형 클러스터 노드 형식에 가상 머신 확장 집합 확장 추가

Service Fabric 관리형 클러스터의 각 노드 형식은 가상 머신 확장 집합을 통해 백업됩니다. 이를 통해 [가상 머신 확장 집합 확장](../virtual-machines/extensions/overview.md)을 Service Fabric 관리형 클러스터 노드 형식에 추가할 수 있습니다.

[Add-AzServiceFabricManagedNodeTypeVMExtension](/powershell/module/az.servicefabric/add-azservicefabricmanagednodetypevmextension) PowerShell 명령을 사용하여 노드 형식에 가상 머신 확장 집합 확장을 추가할 수 있습니다.

또는 Azure Resource Manager 템플릿의 Service Fabric 관리형 클러스터 노드 형식에서 가상 머신 확장 집합 확장을 사용할 수 있습니다. 예를 들면 다음과 같습니다.

```json
{
    "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
    "apiVersion": "[variables('sfApiVersion')]",
    "name": "[concat(parameters('clusterName'), '/', parameters('nodeTypeName'))]",
    "dependsOn": [
        "[concat('Microsoft.ServiceFabric/managedclusters/', parameters('clusterName'))]"
    ],
    "location": "[resourceGroup().location]",
    "properties": {
        "isPrimary": true,
        "vmInstanceCount": 3,
        "dataDiskSizeGB": 100,
        "vmSize": "Standard_D2",
        "vmImagePublisher": "MicrosoftWindowsServer",
        "vmImageOffer": "WindowsServer",
        "vmImageSku": "2019-Datacenter",
        "vmImageVersion": "latest",
        "vmExtensions": [{
            "name": "ExtensionA",
            "properties": {
                "publisher": "ExtensionA.Publisher",
                "type": "KeyVaultForWindows",
                "typeHandlerVersion": "1.0",
                "autoUpgradeMinorVersion": true,
                "settings": {
                }
            }
        }]
    }
}
```

Service Fabric 관리형 클러스터 노드 형식 구성에 대한 자세한 내용은 [관리형 클러스터 노드 형식](/azure/templates/microsoft.servicefabric/2020-01-01-preview/managedclusters/nodetypes)을 참조하세요.

## <a name="next-steps"></a>다음 단계

Service Fabric 관리형 클러스터에 대해 자세히 알아보려면 다음을 참고하세요.

> [!div class="nextstepaction"]
> [Service Fabric 관리형 클러스터 질문과 대답](./faq-managed-cluster.yml)
