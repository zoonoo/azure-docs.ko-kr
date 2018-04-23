---
title: 네트워크 보안 그룹 만들기 - Azure Resource Manager 템플릿| Microsoft Docs
description: Azure Resource Manager 템플릿을 사용하여 네트워크 보안 그룹을 만들고 배포하는 방법을 알아봅니다.
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: f3e7385d-717c-44ff-be20-f9aa450aa99b
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1fc210ac5e28efe10be27f8b5a752788d3d5a5a3
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="create-network-security-groups-using-an-azure-resource-manager-template"></a>Azure Resource Manager 템플릿을 사용하여 네트워크 보안 그룹 만들기

[!INCLUDE [virtual-networks-create-nsg-selectors-arm-include](../../includes/virtual-networks-create-nsg-selectors-arm-include.md)]

[!INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

이 문서에서는 리소스 관리자 배포 모델에 대해 설명합니다. [클래식 배포 모델에서 NSG를 만들](virtual-networks-create-nsg-classic-ps.md)수도 있습니다.

[!INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

## <a name="nsg-resources-in-a-template-file"></a>템플릿 파일의 NSG 리소스
[샘플 템플릿](https://raw.githubusercontent.com/telmosampaio/azure-templates/master/201-IaaS-WebFrontEnd-SQLBackEnd/NSGs.json)을 보고 다운로드할 수 있습니다.

다음 섹션에서는 시나리오를 기반으로 프런트 엔드 NSG의 정의를 보여줍니다.

```json
"apiVersion": "2017-03-01",
"type": "Microsoft.Network/networkSecurityGroups",
"name": "[parameters('frontEndNSGName')]",
"location": "[resourceGroup().location]",
"tags": {
  "displayName": "NSG - Front End"
},
"properties": {
  "securityRules": [
    {
      "name": "rdp-rule",
      "properties": {
        "description": "Allow RDP",
        "protocol": "Tcp",
        "sourcePortRange": "*",
        "destinationPortRange": "3389",
        "sourceAddressPrefix": "Internet",
        "destinationAddressPrefix": "*",
        "access": "Allow",
        "priority": 100,
        "direction": "Inbound"
      }
    },
    {
      "name": "web-rule",
      "properties": {
        "description": "Allow WEB",
        "protocol": "Tcp",
        "sourcePortRange": "*",
        "destinationPortRange": "80",
        "sourceAddressPrefix": "Internet",
        "destinationAddressPrefix": "*",
        "access": "Allow",
        "priority": 101,
        "direction": "Inbound"
      }
    }
  ]
}
```
프런트 엔드 서브넷에 NSG를 연결하려면 템플릿에서 서브넷 정의를 변경하고 NSG에 대한 참조 ID를 사용해야 합니다.

```json
"subnets": [
  {
    "name": "[parameters('frontEndSubnetName')]",
    "properties": {
      "addressPrefix": "[parameters('frontEndSubnetPrefix')]",
      "networkSecurityGroup": {
      "id": "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('frontEndNSGName'))]"
      }
    }
  }, 
```

템플릿의 백 엔드 NSG 및 백 엔드 서브넷에 대해 동일한 작업이 수행됩니다.

## <a name="deploy-the-arm-template-by-using-click-to-deploy"></a>클릭하여 배포하는 방식으로 ARM 템플릿 배포
공용 저장소에서 사용할 수 있는 샘플 템플릿은 위에 설명된 시나리오를 생성하는 데 사용된 기본값을 포함하는 매개 변수 파일을 사용합니다. 클릭하여 배포하는 방식으로 이 템플릿을 배포하려면 [이 링크](http://github.com/telmosampaio/azure-templates/tree/master/201-IaaS-WebFrontEnd-SQLBackEnd-NSG)에 따라 **Azure에 배포**를 클릭하고 필요한 경우 기본 매개 변수 값을 대체하고 포털의 지침을 따릅니다.

## <a name="deploy-the-arm-template-by-using-powershell"></a>PowerShell을 사용하여 ARM 템플릿 배포
PowerShell을 사용하여 다운로드한 ARM 템플릿을 배포하려면 다음 단계를 수행합니다.

1. Azure PowerShell을 처음 사용하는 경우 [Azure PowerShell 설치 및 구성 방법](/powershell/azure/overview)의 지침을 따라 설치 및 구성을 합니다.
2. **`New-AzureRmResourceGroup`** cmdlet을 실행하고 템플릿을 사용하여 리소스 그룹을 만듭니다.

    ```powershell
    New-AzureRmResourceGroup -Name TestRG -Location uswest `
    -TemplateFile 'https://raw.githubusercontent.com/telmosampaio/azure-templates/master/201-IaaS-WebFrontEnd-SQLBackEnd/azuredeploy.json' `
    -TemplateParameterFile 'https://raw.githubusercontent.com/telmosampaio/azure-templates/master/201-IaaS-WebFrontEnd-SQLBackEnd/azuredeploy.parameters.json'
    ```

    예상 출력:

        ResourceGroupName : TestRG
        Location          : westus
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
                            Actions  NotActions
                            =======  ==========
                            *                  
   
        Resources         :
                            Name                Type                                     Location
                            ==================  =======================================  ========
                            sqlAvSet            Microsoft.Compute/availabilitySets       westus  
                            webAvSet            Microsoft.Compute/availabilitySets       westus  
                            SQL1                Microsoft.Compute/virtualMachines        westus  
                            SQL2                Microsoft.Compute/virtualMachines        westus  
                            Web1                Microsoft.Compute/virtualMachines        westus  
                            Web2                Microsoft.Compute/virtualMachines        westus  
                            TestNICSQL1         Microsoft.Network/networkInterfaces      westus  
                            TestNICSQL2         Microsoft.Network/networkInterfaces      westus  
                            TestNICWeb1         Microsoft.Network/networkInterfaces      westus  
                            TestNICWeb2         Microsoft.Network/networkInterfaces      westus  
                            NSG-BackEnd         Microsoft.Network/networkSecurityGroups  westus  
                            NSG-FrontEnd        Microsoft.Network/networkSecurityGroups  westus  
                            TestPIPSQL1         Microsoft.Network/publicIPAddresses      westus  
                            TestPIPSQL2         Microsoft.Network/publicIPAddresses      westus  
                            TestPIPWeb1         Microsoft.Network/publicIPAddresses      westus  
                            TestPIPWeb2         Microsoft.Network/publicIPAddresses      westus  
                            TestVNet            Microsoft.Network/virtualNetworks        westus  
                            testvnetstorageprm  Microsoft.Storage/storageAccounts        westus  
                            testvnetstoragestd  Microsoft.Storage/storageAccounts        westus  
   
        ResourceId        : /subscriptions/[Subscription Id]/resourceGroups/TestRG

## <a name="deploy-the-arm-template-by-using-the-azure-cli"></a>Azure CLI를 사용하여 ARM 템플릿 배포
Azure CLI를 사용하여 ARM 템플릿을 배포하려면 아래 단계를 따르세요.

1. Azure CLI를 처음 사용하는 경우 [Azure CLI 설치 및 구성](../cli-install-nodejs.md)을 참조하고 Azure 계정 및 구독을 선택하는 부분까지 관련 지침을 따릅니다.
2. 아래와 같이 **`azure config mode`** 명령을 실행하여 리소스 관리자 모드로 전환합니다.

    ```azurecli
    azure config mode arm
    ```

    다음은 위의 명령에 대해 예상된 출력입니다.

        info:    New mode is arm

3. **`azure group deployment create`** cmdlet을 실행하여 위에서 다운로드하고 수정한 템플릿 및 매개 변수 파일을 사용해 새 VNet을 배포합니다. 출력 다음에 표시되는 목록은 사용되는 매개 변수를 설명합니다.

    ```azurecli
    azure group create -n TestRG -l westus -f 'https://raw.githubusercontent.com/telmosampaio/azure-templates/master/201-IaaS-WebFrontEnd-SQLBackEnd/azuredeploy.json' -e 'https://raw.githubusercontent.com/telmosampaio/azure-templates/master/201-IaaS-WebFrontEnd-SQLBackEnd/azuredeploy.parameters.json'
    ```

    예상 출력:
   
        info:    Executing command group create
        info:    Getting resource group TestRG
        info:    Creating resource group TestRG
        info:    Created resource group TestRG
        info:    Initializing template configurations and parameters
        info:    Creating a deployment
        info:    Created template deployment "azuredeploy"
        data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG
        data:    Name:                TestRG
        data:    Location:            westus
        data:    Provisioning State:  Succeeded
        data:    Tags: null
        data:    
        info:    group create command OK
   
   * **-n (or --name)**. 만들 리소스 그룹의 이름입니다.
   * **-l (or --location)**. 리소스 그룹이 생성되는 Azure 지역입니다.
   * **-f (or --template-file)**. ARM 템플릿 파일에 대한 경로입니다.
   * **-e(또는 --parameters-file)**. ARM 매개 변수 파일에 대한 경로입니다.

