---
title: Azure Service Fabric 코드 제공 인프라에 대한 모범 사례
description: Azure Service Fabric을 코드 제공 인프라로 관리하기 위한 모범 사례 및 디자인 고려 사항입니다.
author: peterpogorski
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: ad357c53d64a9bd9fdb5822e7a7c6a94b60a3ee1
ms.sourcegitcommit: eda26a142f1d3b5a9253176e16b5cbaefe3e31b3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2021
ms.locfileid: "109732329"
---
# <a name="infrastructure-as-code"></a>코드 제공 인프라(Infrastructure as code)

프로덕션 시나리오에서 Resource Manager 템플릿을 사용하여 Azure Service Fabric 클러스터를 만듭니다. Resource Manager 템플릿은 리소스 속성을 더 효율적으로 제어하고 일관된 리소스 모델을 갖출 수 있도록 합니다.

Windows 및 Linux용 Resource Manager 템플릿 샘플은 [GitHub의 Azure 샘플](https://github.com/Azure-Samples/service-fabric-cluster-templates)에서 사용할 수 있습니다. 이러한 템플릿은 클러스터 템플릿의 시작점으로 사용할 수 있습니다. `azuredeploy.json` 및 `azuredeploy.parameters.json`을 다운로드하여 사용자 지정 요구 사항에 맞게 편집합니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

위에서 다운로드한 `azuredeploy.json` 및 `azuredeploy.parameters.json` 템플릿을 배포하려면 다음 Azure CLI 명령을 사용합니다.

```azurecli
ResourceGroupName="sfclustergroup"
Location="westus"

az group create --name $ResourceGroupName --location $Location 
az deployment group create --name $ResourceGroupName  --template-file azuredeploy.json --parameters @azuredeploy.parameters.json
```

PowerShell을 사용하여 리소스 만들기

```powershell
$ResourceGroupName="sfclustergroup"
$Location="westus"
$Template="azuredeploy.json"
$Parameters="azuredeploy.parameters.json"

New-AzResourceGroup -Name $ResourceGroupName -Location $Location
New-AzResourceGroupDeployment -Name $ResourceGroupName -TemplateFile $Template -TemplateParameterFile $Parameters
```

## <a name="service-fabric-resources"></a>Service Fabric 리소스

Azure Resource Manager를 통해 Service Fabric 클러스터에 애플리케이션 및 서비스를 배포할 수 있습니다. 자세한 내용은 [애플리케이션 및 서비스를 Azure Resource Manager 리소스로 관리](./service-fabric-application-arm-resource.md)를 참조하세요. Resource Manager 템플릿 리소스에 포함할 Service Fabric 애플리케이션 특정 리소스에 대한 추천 사례는 다음과 같습니다.

```json
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications/services",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
    "location": "[variables('clusterLocation')]"
}
```

Azure Resource Manager를 사용하여 애플리케이션을 배포하려면 먼저 [Sfpkg](./service-fabric-package-apps.md#create-an-sfpkg) Service Fabric 애플리케이션 패키지를 만들어야 합니다. sfpkg를 만드는 방법을 보여 주는 Python 스크립트의 예제는 다음과 같습니다.

```python
# Create SFPKG that needs to be uploaded to Azure Storage Blob Container
microservices_sfpkg = zipfile.ZipFile(
    self.microservices_app_package_name, 'w', zipfile.ZIP_DEFLATED)
package_length = len(self.microservices_app_package_path)

for root, dirs, files in os.walk(self.microservices_app_package_path):
    root_folder = root[package_length:]
    for file in files:
        microservices_sfpkg.write(os.path.join(
            root, file), os.path.join(root_folder, file))

microservices_sfpkg.close()
```

## <a name="virtual-machine-os-automatic-upgrade-configuration"></a>가상 머신 OS 자동 업그레이드 구성

가상 머신 업그레이드는 사용자가 시작한 작업이며, Service Fabric 클러스터 노드 패치 관리에 [가상 머신 확장 집합 자동 이미지 업그레이드를 사용하도록 설정하는](how-to-patch-cluster-nodes-windows.md) 것이 좋습니다. POA(패치 오케스트레이션 애플리케이션)는 비 Azure 호스팅 클러스터를 위한 대체 솔루션입니다. POA는 Azure에서 사용할 수 있지만 호스팅하려면 확장 집합 자동 OS 이미지 업그레이드를 사용하도록 설정하는 것보다 더 많은 관리가 필요합니다. 다음은 자동 OS 업그레이드를 사용하도록 설정하는 가상 머신 확장 집합 Resource Manager 템플릿 속성입니다.

```json
"upgradePolicy": {
   "mode": "Automatic",
   "automaticOSUpgradePolicy": {
        "enableAutomaticOSUpgrade": true,
        "disableAutomaticRollback": false
    }
},
```
Service Fabric과 함께 자동 OS 업그레이드를 사용할 때 새 OS 이미지는 Service Fabric에서 실행되는 서비스의 고가용성을 유지하는 시점에 업데이트 도메인에 롤아웃됩니다. Service Fabric에서 자동 OS 업그레이드를 활용하려면 실버 내구성 계층 이상을 사용하도록 클러스터가 구성되어야 합니다.

Windows 호스트 머신에서 조정되지 않은 업데이트를 시작하지 못하도록 하려면 다음 레지스트리 키가 false로 설정되어 있는지 확인합니다. HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU

다음 가상 머신 확장 집합 템플릿 속성을 설정하여 Windows 업데이트를 사용하지 않도록 설정합니다.
```json
"osProfile": {
        "computerNamePrefix": "{vmss-name}",
        "adminUsername": "{your-username}",
        "secrets": [],
        "windowsConfiguration": {
          "provisionVMAgent": true,
          "enableAutomaticUpdates": false
        }
      },
```

## <a name="service-fabric-cluster-upgrade-configuration"></a>Service Fabric 클러스터 업그레이드 구성

다음은 자동 업그레이드를 사용하도록 설정하는 Service Fabric 클러스터 템플릿 속성입니다.

```json
"upgradeMode": "Automatic",
```

클러스터를 수동으로 업그레이드하려면 cab/deb 배포를 클러스터 가상 머신에 다운로드한 다음, 다음 PowerShell을 호출합니다.

```powershell
Copy-ServiceFabricClusterPackage -Code -CodePackagePath <"local_VM_path_to_msi"> -CodePackagePathInImageStore ServiceFabric.msi -ImageStoreConnectionString "fabric:ImageStore"
Register-ServiceFabricClusterPackage -Code -CodePackagePath "ServiceFabric.msi"
Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <"msi_code_version">
```

## <a name="next-steps"></a>다음 단계

* Windows Server를 실행하는 VM 또는 컴퓨터에서 클러스터 만들기: [Windows Server용 서비스 패브릭 클러스터 만들기](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* Linux를 실행하는 VM 또는 컴퓨터에서 클러스터 만들기: [Linux 클러스터 만들기](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
* [Service Fabric 지원 옵션](service-fabric-support.md) 알아보기
