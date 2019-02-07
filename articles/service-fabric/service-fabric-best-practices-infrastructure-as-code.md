---
title: Azure Service Fabric 코드 제공 인프라에 대한 모범 사례 | Microsoft Docs
description: Service Fabric을 코드 제공 인프라로 관리하기 위한 모범 사례입니다.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: jeanpaul.connock
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: 23c851008988af06927d387daaa5a6df980dab96
ms.sourcegitcommit: 97d0dfb25ac23d07179b804719a454f25d1f0d46
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/25/2019
ms.locfileid: "54914530"
---
# <a name="infrastructure-as-code"></a>코드 제공 인프라(Infrastructure as code)

프로덕션 시나리오에서 Resource Manager 템플릿을 사용하여 Azure Service Fabric 클러스터를 만듭니다. Resource Manager 템플릿은 리소스 속성을 더 효율적으로 제어하고 일관된 리소스 모델을 갖출 수 있도록 합니다.

Windows 및 Linux용 Resource Manager 템플릿 샘플은 [GitHub의 Azure 샘플](https://github.com/Azure-Samples/service-fabric-cluster-templates)에서 사용할 수 있습니다. 이러한 템플릿은 클러스터 템플릿의 시작점으로 사용할 수 있습니다. `azuredeploy.json` 및 `azuredeploy.parameters.json`을 다운로드하여 사용자 지정 요구 사항에 맞게 편집합니다.

위에서 다운로드한 `azuredeploy.json` 및 `azuredeploy.parameters.json` 템플릿을 배포하려면 다음 Azure CLI 명령을 사용합니다.

```azurecli
ResourceGroupName="sfclustergroup"
Location="westus"

az group create --name $ResourceGroupName --location $Location 
az group deployment create --name $ResourceGroupName  --template-file azuredeploy.json --parameters @azuredeploy.parameters.json
```

PowerShell을 사용하여 리소스 만들기

```powershell
$ResourceGroupName="sfclustergroup"
$Location="westus"
$Template="azuredeploy.json"
$Parameters="azuredeploy.parameters.json"

New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location
New-AzureRmResourceGroupDeployment -Name $ResourceGroupName -TemplateFile $Template -TemplateParameterFile $Parameters
```

## <a name="azure-service-fabric-resources"></a>Azure Service Fabric 리소스

Azure Resource Manager를 통해 Service Fabric 클러스터에 애플리케이션 및 서비스를 배포할 수 있습니다. 자세한 내용은 [애플리케이션 및 서비스를 Azure Resource Manager 리소스로 관리](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-arm-resource)를 참조하세요. Resource Manager 템플릿 리소스에 포함할 Service Fabric 애플리케이션 특정 리소스에 대한 추천 사례는 다음과 같습니다.

```json
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applications",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applications/services",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
    "location": "[variables('clusterLocation')]"
}
```

Azure Resource Manager를 사용하여 애플리케이션을 배포하려면 먼저 [Sfpkg](https://docs.microsoft.com/azure/service-fabric/service-fabric-package-apps#create-an-sfpkg) Service Fabric 애플리케이션 패키지를 만들어야 합니다. sfpkg를 만드는 방법을 보여 주는 Python 스크립트의 예제는 다음과 같습니다.

```python
# Create SFPKG that needs to be uploaded to Azure Storage Blob Container
microservices_sfpkg = zipfile.ZipFile(self.microservices_app_package_name, 'w', zipfile.ZIP_DEFLATED)
package_length = len(self.microservices_app_package_path)

for root, dirs, files in os.walk(self.microservices_app_package_path):
    root_folder = root[package_length:]
    for file in files:
        microservices_sfpkg.write(os.path.join(root, file), os.path.join(root_folder, file))

microservices_sfpkg.close()
```

## <a name="next-steps"></a>다음 단계

* Windows Server를 실행하는 VM 또는 컴퓨터에서 클러스터 만들기: [Windows Server용 Service Fabric 클러스터 만들기](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* Linux를 실행하는 VM 또는 컴퓨터에서 클러스터 만들기: [Linux 클러스터 만들기](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
* [Service Fabric 지원 옵션](service-fabric-support.md) 알아보기