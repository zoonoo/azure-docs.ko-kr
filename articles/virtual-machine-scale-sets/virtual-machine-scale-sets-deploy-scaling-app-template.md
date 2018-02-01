---
title: "Azure 템플릿을 사용하여 가상 머신 확장 집합 만들기 | Microsoft Docs"
description: "Azure Resource Manager 템플릿을 사용하여 가상 머신 확장 집합을 빠르게 만드는 방법을 알아봅니다."
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/16/2017
ms.author: iainfou
ms.openlocfilehash: 201b752c2a79362f2e049d2e0f0b953d77aaedfe
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2018
---
# <a name="create-a-virtual-machine-scale-set-with-the-azure-cli-20"></a>Azure CLI 2.0을 사용하여 가상 머신 확장 집합 만들기
가상 머신 확장 집합을 사용하면 동일한 자동 크기 조정 가상 머신 집합을 배포하고 관리할 수 있습니다. 확장 집합의 VM 수를 수동으로 조정하거나 CPU와 같은 리소스 사용량, 메모리 요구량 또는 네트워크 트래픽을 기반으로 자동으로 크기를 조정하는 규칙을 정의할 수도 있습니다. 이 시작 문서에서는 Azure Resource Manager 템플릿을 사용하여 가상 머신 확장 집합을 만듭니다. 확장 집합은 [Azure CLI 2.0](virtual-machine-scale-sets-create-cli.md), [Azure PowerShell](virtual-machine-scale-sets-create-powershell.md) 또는 [Azure Portal](virtual-machine-scale-sets-create-portal.md)을 사용하여 만들 수도 있습니다.


## <a name="overview-of-templates"></a>템플릿 개요
Azure Resource Manager 템플릿을 사용하면 관련 리소스 그룹을 배포할 수 있습니다. 템플릿은 JSON(JavaScript Object Notation)으로 작성되며, 응용 프로그램에 대한 전체 Azure 인프라 환경을 정의합니다. 단일 템플릿에서 가상 머신 확장 집합을 만들고, 응용 프로그램을 설치하고, 자동 크기 조정 규칙을 구성할 수 있습니다. 변수와 매개 변수를 사용하면 이 템플릿을 다시 사용하여 기존의 확장 집합을 업데이트하거나 추가 확장 집합을 만들 수 있습니다. Azure Portal, Azure CLI 2.0 또는 Azure PowerShell을 통해 템플릿을 배포할 수 있을 뿐만 아니라 CI/CD(지속적인 통합/지속적인 업데이트) 파이프라인에서 템플릿을 호출할 수도 있습니다.

템플릿에 대한 자세한 내용은 [Azure Resource Manager 개요](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#template-deployment)를 참조하세요.


## <a name="define-a-scale-set"></a>확장 집합 정의
템플릿은 각 리소스 종류에 대한 구성을 정의합니다. 가상 머신 확장 집합 리소스 종류는 개별 VM과 비슷합니다. 가상 머신 확장 집합 리소스 종류의 주요 부분은 다음과 같습니다.

| 자산                     | 속성 설명                                  | 예제 템플릿 값                    |
|------------------------------|----------------------------------------------------------|-------------------------------------------|
| 형식                         | 만들 Azure 리소스 종류                            | Microsoft.Compute/virtualMachineScaleSets |
| 이름                         | 확장 집합 이름                                       | myScaleSet                                |
| location                     | 확장 집합을 만들 위치                     | 미국 동부                                   |
| sku.name                     | 각 확장 집합 인스턴스에 대한 VM 크기                  | Standard_A1                               |
| sku.capacity                 | 처음에 만들 VM 인스턴스의 수           | 2                                         |
| upgradePolicy.mode           | 변경 발생 시의 VM 인스턴스 업그레이드 모드              | 자동                                 |
| imageReference               | VM 인스턴스에 사용할 플랫폼 또는 사용자 지정 이미지 | Canonical Ubuntu Server 16.04-LTS         |
| osProfile.computerNamePrefix | 각 VM 인스턴스에 대한 이름 접두사                     | myvmss                                    |
| osProfile.adminUsername      | 각 VM 인스턴스에 대한 사용자 이름                        | azureuser                                 |
| osProfile.adminPassword      | 각 VM 인스턴스에 대한 암호                        | P@ssw0rd!                                 |

 다음 코드 조각에서는 코어 확장 집합이 템플릿에서 리소스 정의를 설정하는 것을 보여 줍니다. 샘플을 짧게 유지하기 위해 가상 NIC(네트워크 인터페이스 카드) 구성이 표시되지 않습니다. 확장 집합 템플릿을 사용자 지정하려면 VM 크기 또는 초기 용량을 변경하거나 다른 플랫폼 또는 사용자 지정 이미지를 사용할 수 있습니다.

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US",
  "apiVersion": "2016-04-30-preview",
  "sku": {
    "name": "Standard_A1",
    "capacity": "2"
  },
  "properties": {
    "upgradePolicy": {
      "mode": "Automatic"
    },
    "virtualMachineProfile": {
      "storageProfile": {
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage"
        },
        "imageReference":  {
          "publisher": "Canonical",
          "offer": "UbuntuServer",
          "sku": "16.04-LTS",
          "version": "latest"
        }
      },
      "osProfile": {
        "computerNamePrefix": "myvmss",
        "adminUsername": "azureuser",
        "adminPassword": "P@ssw0rd!"
      }
    }
  }
}
```


## <a name="install-an-application"></a>응용 프로그램 설치
확장 집합을 배포하는 경우 VM 확장에서 배포 후 구성 및 자동화 작업(예: 응용 프로그램 설치)을 제공할 수 있습니다. 스크립트는 Azure 저장소 또는 GitHub에서 다운로드하거나 확장 런타임에서 Azure Portal에 제공할 수 있습니다. 확장 집합에 확장을 적용하려면 앞의 리소스 예제에 *extensionProfile* 섹션을 추가합니다. 확장 프로필은 일반적으로 다음 속성을 정의합니다.

- 확장 형식
- 확장 게시자
- 확장 버전
- 구성 또는 설치 스크립트의 위치
- VM 인스턴스에서 실행할 명령

확장을 사용하여 응용 프로그램을 설치하는 두 가지 방법, 즉 사용자 지정 스크립트 확장을 사용하여 Linux에서 Python 앱을 설치하거나, PowerShell DSC 확장을 사용하여 Windows에서 ASP.NET 앱을 설치하는 방법을 살펴보겠습니다.

### <a name="python-http-server-on-linux"></a>Linux의 Python HTTP 서버
[Linux의 Python HTTP 서버](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale)는 사용자 지정 스크립트 확장을 사용하여 [Bottle](http://bottlepy.org/docs/dev/) Python 웹 프레임워크 및 간단한 HTTP 서버를 설치합니다. 

두 스크립트는 *fileUris* - *installserver.sh* 및 *workserver.py*에 정의되어 있습니다. 이러한 파일은 GitHub에서 다운로드한 다음 *commandToExecute*에서 설치하고 구성할 앱에 대한 `bash installserver.sh`를 정의합니다.

```json
"extensionProfile": {
  "extensions": [
    {
      "name": "AppInstall",
      "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "typeHandlerVersion": "2.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "fileUris": [
            "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/installserver.sh",
            "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/workserver.py"
          ],
          "commandToExecute": "bash installserver.sh"
        }
      }
    }
  ]
}
```

### <a name="aspnet-application-on-windows"></a>Windows의 ASP.NET 응용 프로그램
[Windows의 ASP.NET 응용 프로그램](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-webapp-dsc-autoscale) 샘플 템플릿은 PowerShell DSC 확장을 사용하여 IIS에서 실행되는 ASP.NET MVC 앱을 설치합니다. 

설치 스크립트는 *url*에서 정의한 대로 GitHub에서 다운로드됩니다. 그런 다음 확장이 *function* 및 *script*에서 정의한 대로 *IISInstall.ps1* 스크립트에서 *InstallIIS*를 실행합니다. ASP.NET 앱 자체는 *WebDeployPackagePath*에서 정의한 대로 GitHub에서 다운로드되는 웹 배포 패키지로 제공됩니다.

```json
"extensionProfile": {
  "extensions": [
    {
      "name": "Microsoft.Powershell.DSC",
      "properties": {
        "publisher": "Microsoft.Powershell",
        "type": "DSC",
        "typeHandlerVersion": "2.9",
        "autoUpgradeMinorVersion": true,
        "forceUpdateTag": "1.0",
        "settings": {
          "configuration": {
            "url": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-windows-webapp-dsc-autoscale/DSC/IISInstall.ps1.zip",
            "script": "IISInstall.ps1",
            "function": "InstallIIS"
          },
          "configurationArguments": {
            "nodeName": "localhost",
            "WebDeployPackagePath": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-windows-webapp-dsc-autoscale/WebDeploy/DefaultASPWebApp.v1.0.zip"
          }
        }
      }
    }
  ]
}
```

## <a name="deploy-the-template"></a>템플릿 배포
[Linux의 Python HTTP 서버](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale) 또는 [Windows의 ASP.NET MVC 응용 프로그램](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-webapp-dsc-autoscale) 템플릿을 배포하는 가장 간단한 방법은 GitHub의 추가 정보 파일에 있는 **Azure에 배포** 단추를 사용하는 것입니다.  또한 PowerShell 또는 Azure CLI를 사용하여 샘플 템플릿을 배포할 수도 있습니다.

### <a name="azure-cli-20"></a>Azure CLI 2.0
Azure CLI 2.0을 사용하여 다음과 같이 Linux에서 Python HTTP 서버를 설치할 수 있습니다.

```azurecli-interactive
# Create a resource group
az group create --name myResourceGroup --location EastUS

# Deploy template into resource group
az group deployment create \
    --resource-group myResourceGroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/azuredeploy.json
```

작업 중인 앱을 보려면 다음과 같이 [az network public-ip list](/cli/azure/network/public-ip#az_network_public_ip_show)를 사용하여 부하 분산 장치의 공용 IP 주소를 가져옵니다.

```azurecli-interactive
az network public-ip list \
    --resource-group myResourceGroup \
    --query [*].ipAddress -o tsv
```

웹 브라우저에 부하 분산 장치의 공용 IP 주소를 *http://<publicIpAddress>:9000/do_work* 형식으로 입력합니다. 부하 분산 장치는 다음 예제와 같이 VM 인스턴스 중 하나에 트래픽을 분산합니다.

![NGINX의 기본 웹 페이지](media/virtual-machine-scale-sets-create-template/running-python-app.png)


### <a name="azure-powershell"></a>Azure PowerShell
Azure PowerShell을 사용하여 다음과 같이 Windows에서 ASP.NET 응용 프로그램을 설치할 수 있습니다.

```azurepowershell-interactive
# Create a resource group
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS

# Deploy template into resource group
New-AzureRmResourceGroupDeployment `
    -ResourceGroupName myResourceGroup `
    -TemplateFile https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-windows-webapp-dsc-autoscale/azuredeploy.json
```

작업 중인 앱을 보려면 다음과 같이 [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress)를 사용하여 부하 분산 장치의 공용 IP 주소를 가져옵니다.

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup | Select IpAddress
```

웹 브라우저에 부하 분산 장치의 공용 IP 주소를 *http://<publicIpAddress>/MyApp* 형식으로 입력합니다. 부하 분산 장치는 다음 예제와 같이 VM 인스턴스 중 하나에 트래픽을 분산합니다.

![실행 중인 IIS 사이트](./media/virtual-machine-scale-sets-create-powershell/running-iis-site.png)


## <a name="clean-up-resources"></a>리소스 정리
더 이상 필요하지 않은 경우 [az group delete](/cli/azure/group#az_group_delete)를 사용하여 다음과 같이 리소스 그룹, 확장 집합 및 모든 관련 리소스를 제거할 수 있습니다.

```azurecli-interactive 
az group delete --name myResourceGroup
```


## <a name="next-steps"></a>다음 단계
