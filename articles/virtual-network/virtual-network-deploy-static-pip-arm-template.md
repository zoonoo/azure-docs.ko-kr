<properties
   pageTitle="리소스 관리자에서 템플릿을 사용하여 고정 공용 IP를 사용하는 VM 배포 | Microsoft Azure"
   description="리소스 관리자에서 템플릿을 사용하여 고정 공용 IP를 사용하는 VM을 배포하는 방법을 알아봅니다."
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
   ms.date="04/27/2016"
   ms.author="telmos" />

# 템플릿을 사용하여 고정 공용 IP를 사용하는 VM 배포

[AZURE.INCLUDE [virtual-network-deploy-static-pip-arm-selectors-include.md](../../includes/virtual-network-deploy-static-pip-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]클래식 배포 모델.

[AZURE.INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

## 템플릿 파일의 공용 IP 리소스

[샘플 템플릿](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.json)을 보고 다운로드할 수 있습니다.

아래 섹션에서는 위의 시나리오를 기반으로 공용 IP 리소스의 정의를 보여 줍니다.

      {
        "apiVersion": "2015-06-15",
        "type": "Microsoft.Network/publicIPAddresses",
        "name": "[variables('webVMSetting').pipName]",
        "location": "[variables('location')]",
        "properties": {
          "publicIPAllocationMethod": "Static"
        },
        "tags": {
          "displayName": "PublicIPAddress - Web"
        }
      },

*고정*으로 설정된 **publicIPAllocationMethod** 속성을 확인합니다. 이 속성은 *동적*(기본값) 또는 *고정*입니다. 고정으로 설정할 경우 할당된 공용 IP 주소가 절대 변경되지 않습니다.

아래 섹션에서는 공용 IP 주소와 네트워크 인터페이스의 연결을 보여 줍니다.

      {
        "apiVersion": "2015-06-15",
        "type": "Microsoft.Network/networkInterfaces",
        "name": "[variables('webVMSetting').nicName]",
        "location": "[variables('location')]",
        "tags": {
          "displayName": "NetworkInterface - Web"
        },
        "dependsOn": [
          "[concat('Microsoft.Network/publicIPAddresses/', variables('webVMSetting').pipName)]",
          "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]"
        ],
        "properties": {
          "ipConfigurations": [
            {
              "name": "ipconfig1",
              "properties": {
                "privateIPAllocationMethod": "Static",
                "privateIPAddress": "[variables('webVMSetting').ipAddress]",
                "publicIPAddress": {
                  "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('webVMSetting').pipName)]"
                },
                "subnet": {
                  "id": "[variables('frontEndSubnetRef')]"
                }
              }
            }
          ]
        }
      },

**variables('webVMSetting').pipName**이라는 리소스의 **ID**를 가리키는 **publicIPAddress** 속성을 확인합니다. 이는 위에 표시된 공용 IP 리소스의 이름입니다.

마지막으로 위의 네트워크 인터페이스는 생성하는 VM의 **networkProfile** 속성에 나열됩니다.

      "networkProfile": {
        "networkInterfaces": [
          {
            "id": "[resourceId('Microsoft.Network/networkInterfaces', variables('webVMSetting').nicName)]"
          }
        ]
      }

## 클릭하여 배포하는 방식으로 템플릿 배포

공용 저장소에서 사용할 수 있는 샘플 템플릿은 위에 설명된 시나리오를 생성하는 데 사용된 기본값을 포함하는 매개 변수 파일을 사용합니다. 클릭-배포를 사용하여 이 템플릿을 배포하려면 [고정 PIP 템플릿이 포함된 VM](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/03-Static-public-IP)의 Readme.md 파일에서 **Azure에 배포**를 클릭합니다. 원할 경우 기본 매개 변수 값을 바꾸고 빈 매개 변수의 값을 입력합니다. 포털의 지침을 따라 고정 공용 IP 주소를 사용하여 가상 컴퓨터를 만듭니다.

## PowerShell을 사용하여 템플릿 배포

PowerShell을 사용하여 다운로드한 템플릿을 배포하려면 다음 단계를 수행합니다.

1. Azure PowerShell을 처음 사용하는 경우 [Azure PowerShell 설치 및 구성 방법](../powershell-install-configure.md)을 참조하고 1-3단계의 지침을 따릅니다.

2. 필요한 경우 PowerShell 콘솔에서 **New-AzureRmResourceGroup** cmdlet을 실행하여 새 리소스 그룹을 만듭니다. 리소스 그룹을 이미 만든 경우 3단계로 이동합니다.

		New-AzureRmResourceGroup -Name PIPTEST -Location westus

	예상된 출력:

		ResourceGroupName : PIPTEST
		Location          : westus
		ProvisioningState : Succeeded
		Tags              :
		ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/StaticPublicIP

3. PowerShell 콘솔에서 **New-AzureRmResourceGroupDeployment** cmdlet을 실행하여 템플릿을 배포합니다.

		New-AzureRmResourceGroupDeployment -Name DeployVM -ResourceGroupName PIPTEST `
		    -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.json `
		    -TemplateParameterUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.parameters.json

	예상된 출력:

		DeploymentName    : DeployVM
		ResourceGroupName : PIPTEST
		ProvisioningState : Succeeded
		Timestamp         : <Deployment date> <Deployment time>
		Mode              : Incremental
		TemplateLink      :
		                    Uri            : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/mas
		                    ter/IaaS-Story/03-Static-public-IP/azuredeploy.json
		                    ContentVersion : 1.0.0.0

		Parameters        :
		                    Name                      Type                       Value     
		                    ========================  =========================  ==========
		                    vnetName                  String                     WTestVNet
		                    vnetPrefix                String                     192.168.0.0/16
		                    frontEndSubnetName        String                     FrontEnd  
		                    frontEndSubnetPrefix      String                     192.168.1.0/24
		                    storageAccountNamePrefix  String                     iaasestd  
		                    stdStorageType            String                     Standard_LRS
		                    osType                    String                     Windows   
		                    adminUsername             String                     adminUser
		                    adminPassword             SecureString                         

		Outputs           :

## Azure CLI를 사용하여 템플릿 배포

Azure CLI를 사용하여 템플릿을 배포하려면 아래 단계를 따르세요.

1. 이전에 Azure CLI를 사용한 적이 없는 경우 [Azure CLI 설치 및 구성](../xplat-cli-install.md) 문서의 단계를 따른 다음 [Azure CLI(Azure 명령줄 인터페이스)에서 Azure 구독에 연결](../xplat-cli-connect.md) 문서의 "azure 로그인을 사용하여 대화식으로 인증" 섹션에 나와 있는 구독에 CLI를 연결하는 단계를 따릅니다.
2. 아래와 같이 **azure config mode** 명령을 실행하여 리소스 관리자 모드로 전환합니다.

		azure config mode arm

	다음은 위의 명령에 대해 예상된 출력입니다.

		info:    New mode is arm

3. [매개 변수 파일](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.parameters.json)을 열고 해당 내용을 선택한 다음 컴퓨터에 파일로 저장합니다. 이 예제에서는 *parameters.json*이라는 파일로 저장되었습니다. 원할 경우 파일 내 매배 변수 값을 변경합니다. 단, 최소한 adminPassword 매개 변수 값을 고유하고 복잡한 암호로 변경하는 것이 좋습니다.

4. **azure group deployment create** cmdlet을 실행하고 위에서 다운로드한 후 수정한 템플릿 및 매개 변수를 사용하여 새 VNet을 배포합니다. 아래 명령에서 <path>를 파일을 저장한 경로로 바꿉니다.

		azure group create -n PIPTEST2 -l westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.json -e <path>\parameters.json

	예상 출력(사용한 매개 변수 값 나열):

		info:    Executing command group create
		+ Getting resource group PIPTEST2
		+ Creating resource group PIPTEST2
		info:    Created resource group PIPTEST2
		+ Initializing template configurations and parameters
		+ Creating a deployment
		info:    Created template deployment "azuredeploy"
		data:    Id:                  /subscriptions/<Subscription ID>/resourceGroups/PIPTEST2
		data:    Name:                PIPTEST2
		data:    Location:            westus
		data:    Provisioning State:  Succeeded
		data:    Tags: null
		data:
		info:    group create command OK

<!---HONumber=AcomDC_0504_2016-->