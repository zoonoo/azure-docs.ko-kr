<properties 
   pageTitle="템플릿을 사용하여 리소스 관리자에서 라우팅 제어 및 가상 어플라이언스 사용 | Microsoft Azure"
   description="템플릿을 사용하여 Azure 리소스 관리자에서 라우팅을 제어하고 가상 어플라이언스를 사용하는 방법을 알아봅니다."
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/23/2016"
   ms.author="telmos" />

#템플릿을 사용하여 리소스 관리자에서 UDR(사용자 정의 경로) 만들기

[AZURE.INCLUDE [virtual-network-create-udr-arm-selectors-include.md](../../includes/virtual-network-create-udr-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]이 문서에서는 리소스 관리자 배포 모델에 대해 설명합니다.

[AZURE.INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

## 템플릿 파일의 UDR 리소스

[샘플 템플릿](https://github.com/telmosampaio/azure-templates/tree/master/IaaS-NSG-UDR)을 보고 다운로드할 수 있습니다.

아래 섹션에서는 위의 시나리오를 기반으로 **azuredeploy-vnet-nsg-udr.json** 파일에 있는 프런트 엔드 UDR의 정의를 보여줍니다.

	"apiVersion": "2015-06-15",
	"type": "Microsoft.Network/routeTables",
	"name": "[parameters('frontEndRouteTableName')]",
	"location": "[resourceGroup().location]",
	"tags": {
	  "displayName": "UDR - FrontEnd"
	},
	"properties": {
	  "routes": [
	    {
	      "name": "RouteToBackEnd",
	      "properties": {
	        "addressPrefix": "[parameters('backEndSubnetPrefix')]",
	        "nextHopType": "VirtualAppliance",
	        "nextHopIpAddress": "[parameters('vmaIpAddress')]"
	      }
	    }
	  ]


프런트 엔드 서브넷에 UDR을 연결하려면 템플릿에서 서브넷 정의를 변경하고 UDR에 대한 참조 ID를 사용해야 합니다.

	"subnets": [
		"name": "[parameters('frontEndSubnetName')]",
		"properties": {
		  "addressPrefix": "[parameters('frontEndSubnetPrefix')]",
		  "networkSecurityGroup": {
		    "id": "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('frontEndNSGName'))]"
		  },
		  "routeTable": {
		      "id": "[resourceId('Microsoft.Network/routeTables', parameters('frontEndRouteTableName'))]"
		  }
		},
		...]

템플릿의 백 엔드 NSG 및 백 엔드 서브넷에 대해 동일한 작업이 수행됩니다.

**FW1** VM에 NIC에 대해 설정된 패킷 수신 및 전달에 사용할 IP 전달 속성이 있는지도 확인해야 합니다. 아래 섹션에서는 위의 시나리오를 기반으로 azuredeploy-nsg-udr.json 파일에 있는 FW1에 대한 NIC의 정의를 보여줍니다.

	"apiVersion": "2015-06-15",
	"type": "Microsoft.Network/networkInterfaces",
	"location": "[variables('location')]",
	"tags": {
	  "displayName": "NetworkInterfaces - DMZ"
	},
	"name": "[concat(variables('fwVMSettings').nicName, copyindex(1))]",
	"dependsOn": [
	  "[concat('Microsoft.Network/publicIPAddresses/', variables('fwVMSettings').pipName, copyindex(1))]",
	  "[concat('Microsoft.Resources/deployments/', 'vnetTemplate')]"
	],
	"properties": {
	  "ipConfigurations": [
	    {
	      "name": "ipconfig1",
	      "properties": {
	        "enableIPForwarding": true,
	        "privateIPAllocationMethod": "Static",
	        "privateIPAddress": "[concat('192.168.0.',copyindex(4))]",
	        "publicIPAddress": {
	          "id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(variables('fwVMSettings').pipName, copyindex(1)))]"
	        },
	        "subnet": {
	          "id": "[variables('dmzSubnetRef')]"
	        }
	      }
	    }
	  ]
	},
	"copy": {
	  "name": "fwniccount",
	  "count": "[parameters('fwCount')]"
	}

## 클릭하여 배포하는 방식으로 ARM 템플릿 배포

공용 저장소에서 사용할 수 있는 샘플 템플릿은 위에 설명된 시나리오를 생성하는 데 사용된 기본값을 포함하는 매개 변수 파일을 사용합니다. 클릭하여 배포하는 방식으로 이 템플릿을 배포하려면 [이 링크](https://github.com/telmosampaio/azure-templates/tree/master/IaaS-NSG-UDR)에 따라 **Azure에 배포**를 클릭하고 필요한 경우 기본 매개 변수 값을 대체하고 포털의 지침을 따릅니다.

## PowerShell을 사용하여 ARM 템플릿 배포

PowerShell을 사용하여 다운로드한 ARM 템플릿을 배포하려면 다음 단계를 수행합니다.

[AZURE.INCLUDE [powershell-preview-include.md](../../includes/powershell-preview-include.md)]

1. Azure PowerShell을 처음 사용하는 경우 [Azure PowerShell을 설치 및 구성하는 방법](../powershell-install-configure.md)을 참조하고 지침을 끝까지 따르면서 Azure에 로그인하고 구독을 선택합니다.

2. `New-AzureRmResourceGroup` cmdlet을 실행하여 리소스 그룹을 만듭니다.

		New-AzureRmResourceGroup -Name TestRG -Location westus

3. `New-AzureRmResourceGroupDeployment` cmdlet을 실행하여 템플릿을 배포합니다.

		New-AzureRmResourceGroupDeployment -Name DeployUDR -ResourceGroupName TestRG `
		    -TemplateUri https://raw.githubusercontent.com/telmosampaio/azure-templates/master/IaaS-NSG-UDR/azuredeploy.json `
		    -TemplateParameterUri https://raw.githubusercontent.com/telmosampaio/azure-templates/master/IaaS-NSG-UDR/azuredeploy.parameters.json	    	

	예상된 출력:

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
		                    ASFW                Microsoft.Compute/availabilitySets       westus  
		                    ASSQL               Microsoft.Compute/availabilitySets       westus  
		                    ASWEB               Microsoft.Compute/availabilitySets       westus  
		                    FW1                 Microsoft.Compute/virtualMachines        westus  
		                    SQL1                Microsoft.Compute/virtualMachines        westus  
		                    SQL2                Microsoft.Compute/virtualMachines        westus  
		                    WEB1                Microsoft.Compute/virtualMachines        westus  
		                    WEB2                Microsoft.Compute/virtualMachines        westus  
		                    NICFW1              Microsoft.Network/networkInterfaces      westus  
		                    NICSQL1             Microsoft.Network/networkInterfaces      westus  
		                    NICSQL2             Microsoft.Network/networkInterfaces      westus  
		                    NICWEB1             Microsoft.Network/networkInterfaces      westus  
		                    NICWEB2             Microsoft.Network/networkInterfaces      westus  
		                    NSG-BackEnd         Microsoft.Network/networkSecurityGroups  westus  
		                    NSG-FrontEnd        Microsoft.Network/networkSecurityGroups  westus  
		                    PIPFW1              Microsoft.Network/publicIPAddresses      westus  
		                    PIPSQL1             Microsoft.Network/publicIPAddresses      westus  
		                    PIPSQL2             Microsoft.Network/publicIPAddresses      westus  
		                    PIPWEB1             Microsoft.Network/publicIPAddresses      westus  
		                    PIPWEB2             Microsoft.Network/publicIPAddresses      westus  
		                    UDR-BackEnd         Microsoft.Network/routeTables            westus  
		                    UDR-FrontEnd        Microsoft.Network/routeTables            westus  
		                    TestVNet            Microsoft.Network/virtualNetworks        westus  
		                    testvnetstorageprm  Microsoft.Storage/storageAccounts        westus  
		                    testvnetstoragestd  Microsoft.Storage/storageAccounts        westus  
		                    
		ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG

## Azure CLI를 사용하여 ARM 템플릿 배포

Azure CLI를 사용하여 ARM 템플릿을 배포하려면 아래 단계를 따르세요.

1. Azure CLI를 처음 사용하는 경우 [Azure CLI 설치 및 구성](../xplat-cli-install.md)을 참조하고 Azure 계정 및 구독을 선택하는 부분까지 관련 지침을 따릅니다.
2. 아래와 같이 `azure config mode` 명령을 실행하여 리소스 관리자 모드로 전환합니다.

		azure config mode arm

	다음은 위의 명령에 대해 예상된 출력입니다.

		info:    New mode is arm

3. 브라우저에서 ****https://raw.githubusercontent.com/telmosampaio/azure-templates/master/IaaS-NSG-UDR/azuredeploy.parameters.json**으로 이동하고 json 파일의 내용을 복사하여 컴퓨터에 새 파일로 붙여넣습니다. 이 시나리오의 경우 아래 값을 **c:\\udr\\azuredeploy.parameters.json**이라는 파일로 복사합니다.

		{
		  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
		  "contentVersion": "1.0.0.0",
		  "parameters": {
		    "fwCount": {
		      "value": 1
		    },
		    "webCount": {
		      "value": 2
		    },
		    "sqlCount": {
		      "value": 2
		    }
		  }
		}

4. **azure group create** cmdlet을 실행하고 위에서 다운로드한 후 수정한 템플릿 및 매개 변수를 사용하여 새 VNet을 배포합니다. 출력 다음에 표시되는 목록은 사용되는 매개 변수를 설명합니다.

		azure group create -n TestRG -l westus --template-uri 'https://raw.githubusercontent.com/telmosampaio/azure-templates/master/IaaS-NSG-UDR/azuredeploy.json' -e 'c:\udr\azuredeploy.parameters.json'

	예상된 출력:

		info:    Executing command group create
		info:    Getting resource group TestRG
		info:    Updating resource group TestRG
		info:    Updated resource group TestRG
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

5. **azure group show** 명령을 실행하여 새 리소스 그룹에 생성된 리소스를 확인합니다.

		azure group show TestRG

	예상된 결과
		
		info:    Executing command group show
		info:    Listing resource groups
		info:    Listing resources for the group
		data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG
		data:    Name:                TestRG
		data:    Location:            westus
		data:    Provisioning State:  Succeeded
		data:    Tags: null
		data:    Resources:
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Compute/availabilitySets/ASFW
		data:      Name    : ASFW
		data:      Type    : availabilitySets
		data:      Location: westus
		data:      Tags    : displayName=AvailabilitySet - DMZ
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Compute/availabilitySets/ASSQL
		data:      Name    : ASSQL
		data:      Type    : availabilitySets
		data:      Location: westus
		data:      Tags    : displayName=AvailabilitySet - SQL
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Compute/availabilitySets/ASWEB
		data:      Name    : ASWEB
		data:      Type    : availabilitySets
		data:      Location: westus
		data:      Tags    : displayName=AvailabilitySet - Web
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/FW1
		data:      Name    : FW1
		data:      Type    : virtualMachines
		data:      Location: westus
		data:      Tags    : displayName=VMs - DMZ
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/SQL1
		data:      Name    : SQL1
		data:      Type    : virtualMachines
		data:      Location: westus
		data:      Tags    : displayName=VMs - SQL
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/SQL2
		data:      Name    : SQL2
		data:      Type    : virtualMachines
		data:      Location: westus
		data:      Tags    : displayName=VMs - SQL
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/WEB1
		data:      Name    : WEB1
		data:      Type    : virtualMachines
		data:      Location: westus
		data:      Tags    : displayName=VMs - Web
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/WEB2
		data:      Name    : WEB2
		data:      Type    : virtualMachines
		data:      Location: westus
		data:      Tags    : displayName=VMs - Web
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICFW1
		data:      Name    : NICFW1
		data:      Type    : networkInterfaces
		data:      Location: westus
		data:      Tags    : displayName=NetworkInterfaces - DMZ
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICSQL1
		data:      Name    : NICSQL1
		data:      Type    : networkInterfaces
		data:      Location: westus
		data:      Tags    : displayName=NetworkInterfaces - SQL
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICSQL2
		data:      Name    : NICSQL2
		data:      Type    : networkInterfaces
		data:      Location: westus
		data:      Tags    : displayName=NetworkInterfaces - SQL
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICWEB1
		data:      Name    : NICWEB1
		data:      Type    : networkInterfaces
		data:      Location: westus
		data:      Tags    : displayName=NetworkInterfaces - Web
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICWEB2
		data:      Name    : NICWEB2
		data:      Type    : networkInterfaces
		data:      Location: westus
		data:      Tags    : displayName=NetworkInterfaces - Web
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-BackEnd
		data:      Name    : NSG-BackEnd
		data:      Type    : networkSecurityGroups
		data:      Location: westus
		data:      Tags    : displayName=NSG - Front End
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd
		data:      Name    : NSG-FrontEnd
		data:      Type    : networkSecurityGroups
		data:      Location: westus
		data:      Tags    : displayName=NSG - Remote Access
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/PIPFW1
		data:      Name    : PIPFW1
		data:      Type    : publicIPAddresses
		data:      Location: westus
		data:      Tags    : displayName=PublicIPAddresses - DMZ
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/PIPSQL1
		data:      Name    : PIPSQL1
		data:      Type    : publicIPAddresses
		data:      Location: westus
		data:      Tags    : displayName=PublicIPAddresses - SQL
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/PIPSQL2
		data:      Name    : PIPSQL2
		data:      Type    : publicIPAddresses
		data:      Location: westus
		data:      Tags    : displayName=PublicIPAddresses - SQL
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/PIPWEB1
		data:      Name    : PIPWEB1
		data:      Type    : publicIPAddresses
		data:      Location: westus
		data:      Tags    : displayName=PublicIPAddresses - Web
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/PIPWEB2
		data:      Name    : PIPWEB2
		data:      Type    : publicIPAddresses
		data:      Location: westus
		data:      Tags    : displayName=PublicIPAddresses - Web
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/routeTables/UDR-BackEnd
		data:      Name    : UDR-BackEnd
		data:      Type    : routeTables
		data:      Location: westus
		data:      Tags    : displayName=Route Table - Back End
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/routeTables/UDR-FrontEnd
		data:      Name    : UDR-FrontEnd
		data:      Type    : routeTables
		data:      Location: westus
		data:      Tags    : displayName=UDR - FrontEnd
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
		data:      Name    : TestVNet
		data:      Type    : virtualNetworks
		data:      Location: westus
		data:      Tags    : displayName=VNet
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Storage/storageAccounts/testvnetstorageprm
		data:      Name    : testvnetstorageprm
		data:      Type    : storageAccounts
		data:      Location: westus
		data:      Tags    : displayName=Storage Account - Premium
		data:    
		data:      Id      : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Storage/storageAccounts/testvnetstoragestd
		data:      Name    : testvnetstoragestd
		data:      Type    : storageAccounts
		data:      Location: westus
		data:      Tags    : displayName=Storage Account - Simple
		data:    
		data:    Permissions:
		data:      Actions: *
		data:      NotActions: 
		data:    
		info:    group show command OK

>[AZURE.TIP] 일부 리소스가 표시되지 않으면 `azure group deployment show` 명령을 실행하여 배포의 프로비전 상태가 *성공*인지 확인합니다.

<!---HONumber=AcomDC_0330_2016-->