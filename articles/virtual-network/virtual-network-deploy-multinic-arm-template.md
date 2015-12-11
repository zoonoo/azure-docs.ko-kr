<properties 
   pageTitle="리소스 관리자에서 템플릿을 사용하여 여러 NIC VM 배포 | Microsoft Azure"
   description="리소스 관리자에서 템플릿을 사용하여 여러 NIC VM을 배포하는 방법에 대해 알아봅니다."
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
   ms.date="11/20/2015"
   ms.author="telmos" />

# 템플릿을 사용하여 다중 NIC VM 배포

[AZURE.INCLUDE [virtual-network-deploy-multinic-arm-selectors-include.md](../../includes/virtual-network-deploy-multinic-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-multinic-intro-include.md](../../includes/virtual-network-deploy-multinic-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-network-deploy-multinic-classic-ps.md).

[AZURE.INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

현재는 단일 NIC가 사용되는 VM과 여러 NIC가 사용되는 VM을 동일 리소스 그룹에서 사용할 수 없기 때문에 백 엔드 서버는 리소스 그룹에서 구현하고 다른 모든 구성 요소는 다른 보안 그룹에서 구현하게 됩니다. 다음 단계에서는 기본 리소스 그룹에 *IaaSStory* 라는 리소스 그룹을, 백 엔드 서버에는 *IaaSStory-BackEnd* 를 사용합니다.

## 필수 조건

백 엔드 서버를 배포하려면 먼저 이 시나리오에 필요한 모든 리소스로 기본 리소스 그룹을 배포해야 합니다. 이러한 리소스를 배포하려면 다음 단계를 수행하세요.

1. [템플릿 페이지](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/11-MultiNIC)로 이동합니다.
2. 서식 파일 페이지에서 **Parent resource group(부모 리소스 그룹)** 오른쪽에 있는 **Azure에 배포**를 클릭합니다.
3. 필요한 경우 매개 변수 값을 변경한 다음 Azure Preview 포털의 단계에 따라 리소스 그룹을 배포합니다.

> [AZURE.IMPORTANT]저장소 계정 이름이 고유한지 확인합니다. Azure에서 중복된 저장소 계정 이름을 사용할 수 없습니다.

## 배포 템플릿 이해

이 설명서와 함께 제공된 템플릿을 배포하기 전에 먼저 그 기능을 이해해야 합니다. 다음 단계를 통해 이 템플릿에 대해 개략적으로 이해할 수 있습니다.

1. [템플릿 페이지](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC)로 이동합니다.
2. **azuredeploy.json**을 클릭하여 템플릿 파일을 엽니다.
3. 아래에 *osType* 매개 변수가 나열됩니다. 이 매개 변수는 여러 운영 체제 관련 설정과 함께 데이터베이스 서버에 사용할 VM 이미지를 선택하는 데 사용됩니다.

	    "osType": {
	      "type": "string",
	      "defaultValue": "Windows",
	      "allowedValues": [
	        "Windows",
	        "Ubuntu"
	      ],
	      "metadata": {
	        "description": "Type of OS to use for VMs: Windows or Ubuntu."
	      }
	    },

4. 아래로 스크롤하여 변수 목록으로 이동한 후 아래에 나온 것처럼 *dbVMSetting* 변수의 정의를 확인합니다. 이는 *dbVMSettings* 변수에 포함된 배열 요소 중 하나를 받습니다. 소프트웨어 개발 용어에 익숙한 작업자라면 *dbVMSettings* 변수를 해시 테이블 또는 사전으로 간주해도 됩니다.

		"dbVMSetting": "[variables('dbVMSettings')[parameters('osType')]]"

5. 백 엔드에서 SQL을 실행하는 Windows VM을 배포하기로 결정하는 경우, *osType* 의 값은 *Windows* 이고, *dbVMSetting* 변수에는 다음과 같은 요소가 포함됩니다. 이 요소는 *dbVMSettings* 변수의 첫 번째 값을 나타냅니다.

	      "Windows": {
	        "vmSize": "Standard_DS3",
	        "publisher": "MicrosoftSQLServer",
	        "offer": "SQL2014SP1-WS2012R2",
	        "sku": "Standard",
	        "version": "latest",
	        "vmName": "DB",
	        "osdisk": "osdiskdb",
	        "datadisk": "datadiskdb",
	        "nicName": "NICDB",
	        "ipAddress": "192.168.2.",
	        "extensionDeployment": "",
	        "avsetName": "ASDB",
	        "remotePort": 3389,
	        "dbPort": 1433
	      },

6. *vmSize* 에는 값 *Standard\_DS3* 이 포함되어 있습니다. 특정 VM 크기만 여러 NIC를 사용할 수 있습니다. 여러 NIC를 사용할 수 있는 VM 크기를 확인하려면 [다중 NIC 개요](virtual-networks-multiple-nics.md)를 참조하세요.
7. 아래로 스크롤하여 **resources**로 이동한 후 첫 번째 요소를 확인합니다. 여기에는 저장소 계정이 설명되어 있습니다. 이 저장소 계정은 각 데이터베이스 VM에서 사용하는 데이터 디스크를 유지 관리하는 데 사용됩니다. 이 시나리오의 각 데이터베이스 VM에는 일반 저장소에 OS 디스크가 있고 SSD(프리미엄) 저장소에 두 개의 데이터 디스크가 저장되어 있습니다.

	    {
	      "apiVersion": "2015-05-01-preview",
	      "type": "Microsoft.Storage/storageAccounts",
	      "name": "[parameters('prmStorageName')]",
	      "location": "[variables('location')]",
	      "tags": {
	        "displayName": "Storage Account - Premium"
	      },
	      "properties": {
	        "accountType": "[parameters('prmStorageType')]"
	      }
	    },

8. 아래에 나열된 다음 리소스로 스크롤합니다. 이 리소스는 각 데이터베이스 VM에서 데이터베이스 액세스에 사용되는 NIC를 나타냅니다. 이 리소스에서는 **copy** 함수가 사용되었습니다. 템플릿을 사용하면 *dbCount* 매개 변수를 기반으로 원하는 수만큼 VM을 배포할 수 있습니다. 따라서 데이터베이스 액세스를 위해 VM당 하나씩, 같은 수량의 NIC를 만들어야 합니다.

	    {
	      "apiVersion": "2015-06-15",
	      "type": "Microsoft.Network/networkInterfaces",
	      "name": "[concat(variables('dbVMSetting').nicName,'-DA-', copyindex(1))]",
	      "location": "[variables('location')]",
	      "tags": {
	        "displayName": "NetworkInterfaces - DB DA"
	      },
	      "copy": {
	        "name": "dbniccount",
	        "count": "[parameters('dbCount')]"
	      },
	      "properties": {
	        "ipConfigurations": [
	          {
	            "name": "ipconfig1",
	            "properties": {
	              "privateIPAllocationMethod": "Static",
	              "privateIPAddress": "[concat(variables('dbVMSetting').ipAddress,copyindex(4))]",
	              "subnet": {
	                "id": "[variables('backEndSubnetRef')]"
	              }
	            }
	          }
	        ]
	      }
	    },

9. 아래에 나열된 다음 리소스로 스크롤합니다. 이 리소스는 각 데이터베이스 VM에서 관리에 사용되는 NIC를 나타냅니다. 각 데이터베이스 VM에 대해 이 NIC 중 하나가 필요합니다. 아래의 *networkSecurityGroup* 요소는 RDP/SSH 액세스 권한을 이 NIC에만 허용하는 NSG를 연결합니다.

	    {
	      "apiVersion": "2015-06-15",
	      "type": "Microsoft.Network/networkInterfaces",
	      "name": "[concat(variables('dbVMSetting').nicName, '-RA-',copyindex(1))]",
	      "location": "[variables('location')]",
	      "tags": {
	        "displayName": "NetworkInterfaces - DB RA"
	      },
	      "copy": {
	        "name": "dbniccount",
	        "count": "[parameters('dbCount')]"
	      },
	      "properties": {
	        "ipConfigurations": [
	          {
	            "name": "ipconfig1",
	            "properties": {
	              "networkSecurityGroup": {
	                "id": "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('remoteAccessNSGName'))]"
	              },
	              "privateIPAllocationMethod": "Static",
	              "privateIPAddress": "[concat(variables('dbVMSetting').ipAddress,copyindex(54))]",
	              "subnet": {
	                "id": "[variables('backEndSubnetRef')]"
	              }
	            }
	          }
	        ]
	      }
	    },

10. 아래에 나열된 다음 리소스로 스크롤합니다. 이 리소스는 모든 데이터베이스 VM이 공유할 가용성 집합을 나타냅니다. 이런 방식을 통해, 유지 관리 도중에 집합에서 하나 이상의 VM이 항상 실행되도록 보장할 수 있습니다.

	    {
	      "apiVersion": "2015-06-15",
	      "type": "Microsoft.Compute/availabilitySets",
	      "name": "[variables('dbVMSetting').avsetName]",
	      "location": "[variables('location')]",
	      "tags": {
	        "displayName": "AvailabilitySet - DB"
	      }
	    },

11. 아래로 스크롤하여 다음 리소스로 이동합니다. 이 리소스는 아래에 나열된 처음 몇 줄에서 볼 수 있듯이 데이터베이스 VM을 나타냅니다. 여기서도 **copy** 함수가 사용되어 *dbCount* 매개 변수를 기반으로 여러 VM이 만들어집니다. *dependsOn* 컬렉션도 확인됩니다. 여기에는 VM을 배포하기 전에 먼저 만들어야 하는 두 개의 NIC가 가용성 집합 및 저장소 계정과 함께 나열됩니다.

		  "apiVersion": "2015-06-15",
		  "type": "Microsoft.Compute/virtualMachines",
		  "name": "[concat(variables('dbVMSetting').vmName,copyindex(1))]",
		  "location": "[variables('location')]",
		  "dependsOn": [
		    "[concat('Microsoft.Network/networkInterfaces/', variables('dbVMSetting').nicName,'-DA-', copyindex(1))]",
		    "[concat('Microsoft.Network/networkInterfaces/', variables('dbVMSetting').nicName,'-RA-', copyindex(1))]",
		    "[concat('Microsoft.Compute/availabilitySets/', variables('dbVMSetting').avsetName)]",
		    "[concat('Microsoft.Storage/storageAccounts/', parameters('prmStorageName'))]"
		  ],
		  "tags": {
		    "displayName": "VMs - DB"
		  },
		  "copy": {
		    "name": "dbvmcount",
		    "count": "[parameters('dbCount')]"
		  },

12. VM 리소스에서 아래로 스크롤하여 다음과 같은 **networkProfile** 요소로 이동합니다. 각 VM의 참조가 되는 두 개의 NIC가 있습니다. 단일 VM에 대해 여러 개의 NIC를 만들 때는 NIC 중 하나의 *primary* 속성을 *true* 로, 나머지의 속성을 *false* 로 설정해야 합니다.

        "networkProfile": {
          "networkInterfaces": [
            {
              "id": "[resourceId('Microsoft.Network/networkInterfaces', concat(variables('dbVMSetting').nicName,'-DA-',copyindex(1)))]",
              "properties": { "primary": true }
            },
            {
              "id": "[resourceId('Microsoft.Network/networkInterfaces', concat(variables('dbVMSetting').nicName,'-RA-',copyindex(1)))]",
              "properties": { "primary": false }
            }
          ]
        }
      }

## 클릭하여 배포하는 방식으로 ARM 템플릿 배포

> [AZURE.IMPORTANT]아래 지침을 따르기 전에 먼저 [필수 조건](#Pre-requisites) 단계를 따라야 합니다.

공용 저장소에서 사용할 수 있는 샘플 템플릿은 위에 설명된 시나리오를 생성하는 데 사용된 기본값을 포함하는 매개 변수 파일을 사용합니다. 클릭하여 배포하는 방식으로 이 템플릿을 배포하려면 **백 엔드 리소스 그룹(설명서 참조)** 오른쪽의 [이 링크](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC)에 따라 **Azure에 배포**를 클릭하고 필요한 경우 기본 매개 변수 값을 대체하고 포털의 지침을 따릅니다.

아래 그림은 배포 후 새 리소스 그룹의 내용을 보여 줍니다.

![백 엔드 리소스 그룹](./media/virtual-network-deploy-multinic-arm-template/Figure2.png)

## PowerShell을 사용하여 템플릿 배포

PowerShell을 사용하여 다운로드한 템플릿을 배포하려면 다음 단계를 수행합니다.

[AZURE.INCLUDE [powershell-preview-include.md](../../includes/powershell-preview-include.md)]

3. **New-AzureRMResourceGroup** cmdlet을 실행하고 템플릿을 사용하여 리소스 그룹을 만듭니다.

		New-AzureRmResourceGroup -Name IaaSStory-Backend -Location uswest `
		    -TemplateFile 'https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.json' `
		    -TemplateParameterFile 'https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.parameters.json'	

	예상된 출력:

		ResourceGroupName : IaaSStory-Backend
		Location          : westus
		ProvisioningState : Succeeded
		Tags              : 
		Permissions       : 
		                    Actions  NotActions
		                    =======  ==========
		                    *                  
		                    
		Resources         : 
		                    Name                 Type                                 Location
		                    ===================  ===================================  ========
		                    ASDB                 Microsoft.Compute/availabilitySets   westus  
		                    DB1                  Microsoft.Compute/virtualMachines    westus  
		                    DB2                  Microsoft.Compute/virtualMachines    westus  
		                    NICDB-DA-1           Microsoft.Network/networkInterfaces  westus  
		                    NICDB-DA-2           Microsoft.Network/networkInterfaces  westus  
		                    NICDB-RA-1           Microsoft.Network/networkInterfaces  westus  
		                    NICDB-RA-2           Microsoft.Network/networkInterfaces  westus  
		                    wtestvnetstorageprm  Microsoft.Storage/storageAccounts    westus  
		                    
		ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend

## Azure CLI를 사용하여 템플릿 배포

Azure CLI를 사용하여 템플릿을 배포하려면 아래 단계를 따르세요.

1. Azure CLI를 처음 사용하는 경우 [Azure CLI 설치 및 구성](xplat-cli.md)을 참조하고 Azure 계정 및 구독을 선택하는 부분까지의 관련 지침을 따릅니다.
2. 아래와 같이 **azure config mode** 명령을 실행하여 리소스 관리자 모드로 전환합니다.

		azure config mode arm

	다음은 위의 명령에 대해 예상된 출력입니다.

		info:    New mode is arm

3. [매개 변수 파일](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.parameters.json)을 열고 해당 내용을 선택한 후 컴퓨터의 파일에 저장합니다. 이 예에서는 매개 변수 파일을 *parameters.json* 에 저장했습니다.

4. **azure group deployment create** cmdlet을 실행하고 위에서 다운로드한 후 수정한 템플릿 및 매개 변수 파일을 사용하여 새 VNet을 배포합니다. 출력 다음에 표시되는 목록은 사용되는 매개 변수를 설명합니다.

		azure group create -n IaaSStory-Backend -l westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/azuredeploy.json -e parameters.json

	예상된 출력:

		info:    Executing command group create
		+ Getting resource group IaaSStory-Backend
		+ Creating resource group IaaSStory-Backend
		info:    Created resource group IaaSStory-Backend
		+ Initializing template configurations and parameters
		+ Creating a deployment
		info:    Created template deployment "azuredeploy"
		data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend
		data:    Name:                IaaSStory-Backend
		data:    Location:            westus
		data:    Provisioning State:  Succeeded
		data:    Tags: null
		data:
		info:    group create command OK

<!----HONumber=AcomDC_1125_2015-->