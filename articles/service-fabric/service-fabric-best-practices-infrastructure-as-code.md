---
title: Azure Service Fabric 코드 제공 인프라에 대한 모범 사례 | Microsoft Docs
description: Service Fabric을 코드 제공 인프라로 관리하기 위한 모범 사례입니다.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: 2dfe1493c6611fb69a417895aaa1028ad5881b9c
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66237425"
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
az group deployment create --name $ResourceGroupName  --template-file azuredeploy.json --parameters @azuredeploy.parameters.json
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

## <a name="azure-virtual-machine-operating-system-automatic-upgrade-configuration"></a>Azure 가상 머신 운영 체제 자동 업그레이드 구성 
가상 컴퓨터를 업그레이드 하는 것은 사용자가 시작한 작업이 및 사용 하는 것이 좋습니다 [가상 머신 크기 조정 설정 된 자동 운영 체제 업그레이드](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade) Azure Service Fabric 클러스터 호스트 패치 관리 패치 오케스트레이션 응용 프로그램을 위한 Azure 외부에서 호스팅되는 경우 POA POA 중인 가상 컴퓨터 운영 체제 자동 업그레이드를 선호 하는 일반적인 이유는 Azure에서 호스트 하는 오버 헤드를 사용 하 여 Azure에서 사용 될 수 있지만 대안 솔루션은 POA를 통해. 다음은 자동 OS 업그레이드를 사용 하도록 설정 하려면 Virtual Machine Scale 리소스 설정 관리자 계산 템플릿 속성입니다.

```json
"upgradePolicy": {
   "mode": "Automatic",
   "automaticOSUpgradePolicy": {
        "enableAutomaticOSUpgrade": true,
        "disableAutomaticRollback": false
    }
},
```
자동 OS 업그레이드에서 Service Fabric을 사용 하는 경우 Service Fabric에서 실행 되는 서비스의 고가용성을 유지 하기 위해 한 번에 하나의 업데이트 도메인을 새 OS 이미지가 롤백됩니다. Service Fabric에서 자동 OS 업그레이드를 활용하려면 실버 내구성 계층 이상을 사용하도록 클러스터가 구성되어야 합니다.

다음 레지스트리 키를 windows 호스트 컴퓨터 조정 되지 않은 업데이트를 시작 하지 않도록 설정 하려면 false로 설정 되었는지 확인 합니다. HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU.

다음은 WindowsUpdate 레지스트리 키를 false로 설정할 가상 컴퓨터 크기 조정 리소스 설정 관리자 계산 템플릿 속성입니다.
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

## <a name="azure-service-fabric-cluster-upgrade-configuration"></a>Azure Service Fabric 클러스터 업그레이드 구성
다음은 Service Fabric 클러스터 Resource Manager template 속성이 자동 업그레이드를 사용 하도록 설정 하려면:
```json
"upgradeMode": "Automatic",
```
클러스터를 수동으로 업그레이드 하려면 클러스터 가상 머신에 cab/deb 배포를 다운로드 하 고 다음 PowerShell을 호출 합니다.
```powershell
Copy-ServiceFabricClusterPackage -Code -CodePackagePath <"local_VM_path_to_msi"> -CodePackagePathInImageStore ServiceFabric.msi -ImageStoreConnectionString "fabric:ImageStore"
Register-ServiceFabricClusterPackage -Code -CodePackagePath "ServiceFabric.msi"
Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <"msi_code_version">
```

## <a name="next-steps"></a>다음 단계

* Windows Server를 실행하는 VM 또는 컴퓨터에서 클러스터 만들기: [Windows Server용 Service Fabric 클러스터 만들기](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* Linux를 실행하는 VM 또는 컴퓨터에서 클러스터 만들기: [Linux 클러스터 만들기](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
* [Service Fabric 지원 옵션](service-fabric-support.md) 알아보기
