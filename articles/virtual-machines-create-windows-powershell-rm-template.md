<properties 
	pageTitle="리소스 관리자 템플릿을 사용하여 Windows 가상 컴퓨터 만들기" 
	description="PowerShell 또는 Azure CLI와 함께 리소스 관리자 템플릿을 사용하여 새 Windows 가상 컴퓨터를 쉽게 만들 수 있습니다." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/29/2015" 
	ms.author="josephd"/>

# 리소스 관리자 템플릿을 사용하여 Windows 가상 컴퓨터 만들기

Azure PowerShell 또는 Azure CLI와 함께 리소스 관리자 템플릿을 사용하여 새 Windows 기반 Azure VM\(가상 컴퓨터\)을 쉽게 만들 수 있습니다. 이 템플릿은 새 리소스 그룹에 단일 서브넷이 있는 새 가상 네트워크에서 Windows를 실행하는 단일 가상 컴퓨터를 만듭니다.

![](./media/virtual-machines-create-windows-powershell-rm-template/windowsvm.png)
 
시작하기 전에 Azure, PowerShell 및 Azure CLI가 구성 및 준비되어 있는지 확인합니다.

[AZURE.INCLUDE [arm-getting-setup-powershell](../includes/arm-getting-setup-powershell.md)]

[AZURE.INCLUDE [xplat-getting-set-up-arm](../includes/xplat-getting-set-up-arm.md)]

## Azure PowerShell을 사용하여 리소스 관리자 템플릿으로 Windows VM 만들기

Azure PowerShell과 Github 템플릿 리포지토리의 리소스 관리자 템플릿을 사용하여 Windows VM을 만들려면 다음 단계를 수행하세요.

### 1단계: JSON 파일에서 템플릿 검사

다음은 템플릿에 대한 JSON 파일의 내용입니다.

	{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "newStorageAccountName": {
            "type": "string",
            "metadata": {
                "Description": "Unique DNS Name for the Storage Account where the Virtual Machine's disks will be placed."
            }
        },
        "adminUsername": {
            "type": "string",
            "metadata": {
               "Description": "Username for the Virtual Machine."
            }
        },
        "adminPassword": {
            "type": "securestring",
            "metadata": {
                "Description": "Password for the Virtual Machine."
            }
        },
        "dnsNameForPublicIP": {
            "type": "string",
            "metadata": {
                  "Description": "Unique DNS Name for the Public IP used to access the Virtual Machine."
            }
        },
        "windowsOSVersion": {
            "type": "string",
            "defaultValue": "2012-R2-Datacenter",
            "allowedValues": [
                "2008-R2-SP1", 
                "2012-Datacenter", 
                "2012-R2-Datacenter", 
                "Windows-Server-Technical-Preview"
            ],
            "metadata": {
                "Description": "The Windows version for the VM. This will pick a fully patched image of this given Windows version. Allowed values: 2008-R2-SP1, 2012-Datacenter, 2012-R2-Datacenter, Windows-Server-Technical-Preview."
            }
        }
    },
    "variables": {
        "location": "West US",
        "imagePublisher": "MicrosoftWindowsServer", 
        "imageOffer": "WindowsServer", 
        "OSDiskName": "osdiskforwindowssimple",
        "nicName": "myVMNic",
        "addressPrefix": "10.0.0.0/16", 
        "subnetName": "Subnet",
        "subnetPrefix": "10.0.0.0/24",
        "storageAccountType": "Standard_LRS",
        "publicIPAddressName": "myPublicIP",
        "publicIPAddressType": "Dynamic",
        "vmStorageAccountContainerName": "vhds",
        "vmName": "MyWindowsVM",
        "vmSize": "Standard_D1",
        "virtualNetworkName": "MyVNET",        
        "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
        "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables('subnetName'))]"
    },    
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[parameters('newStorageAccountName')]",
            "apiVersion": "2015-05-01-preview",
            "location": "[variables('location')]",
            "properties": {
                "accountType": "[variables('storageAccountType')]"
            }
        },
        {
            "apiVersion": "2015-05-01-preview",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "[variables('publicIPAddressName')]",
            "location": "[variables('location')]",
            "properties": {
                "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
                "dnsSettings": {
                    "domainNameLabel": "[parameters('dnsNameForPublicIP')]"
                }
            }
        },
        {
            "apiVersion": "2015-05-01-preview",
            "type": "Microsoft.Network/virtualNetworks",
            "name": "[variables('virtualNetworkName')]",
            "location": "[variables('location')]",
            "properties": {
                "addressSpace": {
                    "addressPrefixes": [
                        "[variables('addressPrefix')]"
                    ]
                },
                "subnets": [
                    {
                        "name": "[variables('subnetName')]",
                        "properties": {
                            "addressPrefix": "[variables('subnetPrefix')]"
                        }
                    }
                ]
            }
        },
        {
            "apiVersion": "2015-05-01-preview",
            "type": "Microsoft.Network/networkInterfaces",
            "name": "[variables('nicName')]",
            "location": "[variables('location')]",
            "dependsOn": [
                "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'))]",
                "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
            ],
            "properties": {
                "ipConfigurations": [
                    {
                        "name": "ipconfig1",
                        "properties": {
                            "privateIPAllocationMethod": "Dynamic",
                            "publicIPAddress": {
                                "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('publicIPAddressName'))]"
                            },
                            "subnet": {
                                "id": "[variables('subnetRef')]"
                            }
                        }
                    }
                ]
            }
        },
        {
            "apiVersion": "2015-05-01-preview",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[variables('vmName')]",
            "location": "[variables('location')]",
            "dependsOn": [
                "[concat('Microsoft.Storage/storageAccounts/', parameters('newStorageAccountName'))]",
                "[concat('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
            ],
            "properties": {
                "hardwareProfile": {
                    "vmSize": "[variables('vmSize')]"
                },
                "osProfile": {
                    "computername": "[variables('vmName')]",
                    "adminUsername": "[parameters('adminUsername')]",
                    "adminPassword": "[parameters('adminPassword')]"
                },
                "storageProfile": {
                    "imageReference": {
                        "publisher": "[variables('imagePublisher')]",
                        "offer": "[variables('imageOffer')]",
                        "sku" : "[parameters('windowsOSVersion')]",
                        "version":"latest"
                    },
                   "osDisk" : {
                        "name": "osdisk",
                        "vhd": {
                            "uri": "[concat('http://',parameters('newStorageAccountName'),'.blob.core.windows.net/',variables('vmStorageAccountContainerName'),'/',variables('OSDiskName'),'.vhd')]"
                        },
                        "caching": "ReadWrite",
                        "createOption": "FromImage"
                    }
                },
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
                        }
                    ]
                }
            }
        }
    ]
	} 


### 2단계: 템플릿을 사용하여 가상 컴퓨터 만들기

Azure 배포 이름, 리소스 그룹 이름 및 Azure 데이터 센터 위치를 입력하고 다음 명령을 실행합니다.

	$deployName="<deployment name>"
	$RGName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$templateURI="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-windows-vm/azuredeploy.json"
	New-AzureResourceGroup –Name $RGName –Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI

**New-AzureResourceGroupDeployment** 명령을 실행하면 JSON 파일의 "parameters" 섹션에 매개 변수 값을 제공하라는 메시지가 표시됩니다. 매개 변수 값을 모두 지정하면 명령에서 리소스 그룹과 가상 컴퓨터를 만듭니다.

다음은 예제입니다.

	$deployName="TestDeployment"
	$RGName="TestRG"
	$locname="West US"
	$templateURI="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-windows-vm/azuredeploy.json"
	New-AzureResourceGroup –Name $RGName –Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI

다음과 같이 표시됩니다.

	cmdlet New-AzureResourceGroupDeployment at command pipeline position 1
	Supply values for the following parameters:
	(Type !? for Help.)
	newStorageAccountName: newsaacct
	adminUsername: WinAdmin1
	adminPassword: *********
	dnsNameForPublicIP: contoso
	VERBOSE: 10:56:59 AM - Template is valid.
	VERBOSE: 10:56:59 AM - Create template deployment 'TestDeployment'.
	VERBOSE: 10:57:08 AM - Resource Microsoft.Network/virtualNetworks 'MyVNET' provisioning status is succeeded
	VERBOSE: 10:57:11 AM - Resource Microsoft.Network/publicIPAddresses 'myPublicIP' provisioning status is running
	VERBOSE: 10:57:11 AM - Resource Microsoft.Storage/storageAccounts 'newsaacct' provisioning status is running
	VERBOSE: 10:57:38 AM - Resource Microsoft.Storage/storageAccounts 'newsaacct' provisioning status is succeeded
	VERBOSE: 10:57:40 AM - Resource Microsoft.Network/publicIPAddresses 'myPublicIP' provisioning status is succeeded
	VERBOSE: 10:57:45 AM - Resource Microsoft.Compute/virtualMachines 'MyWindowsVM' provisioning status is running
	VERBOSE: 10:57:45 AM - Resource Microsoft.Network/networkInterfaces 'myVMNic' provisioning status is succeeded
	VERBOSE: 11:01:59 AM - Resource Microsoft.Compute/virtualMachines 'MyWindowsVM' provisioning status is succeeded
	
	
	DeploymentName    : TestDeployment
	ResourceGroupName : TestRG
	ProvisioningState : Succeeded
	Timestamp         : 4/28/2015 6:02:13 PM
	Mode              : Incremental
	TemplateLink      :
	Parameters        :
                    	Name             Type                       Value
	                    ===============  =========================  ==========
	                    newStorageAccountName  String                     newsaacct
	                    adminUsername    String                     WinAdmin1
	                    adminPassword    SecureString
	                    dnsNameForPublicIP  String                     contoso9875
	                    windowsOSVersion  String                     2012-R2-Datacenter
	
	Outputs           :

이제 새 리소스 그룹에 MyWindowsVM이라는 새 Windows 가상 컴퓨터가 생성되었습니다.

## Azure CLI를 사용하여 리소스 관리자 템플릿으로 Windows VM 만들기

Azure CLI 명령과 Github 템플릿 리포지토리의 리소스 관리자 템플릿을 사용하여 Windows VM을 만들려면 다음 단계를 수행하세요.

리소스 그룹 이름 및 Azure 위치\(예: 미국 서부의 경우 westus\)를 입력하고 괄호를 제거한 후 다음 명령을 실행합니다.

	azure group create [resource group] [location]
	azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-windows-vm/azuredeploy.json [resource group] firstdeployment

다음은 템플릿에 대한 Azure CLI 명령 집합의 예제입니다.

	azure group create testrg westus
	azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-windows-vm/azuredeploy.json testrg firstdeployment

다음과 유사한 결과가 표시됩니다.

	azure group create testrg westus
	info:    Executing command group create
	+ Getting resource group testrg                                             
	+ Creating resource group testrg                                            
	info:    Created resource group testrg
	data:    Id:                  /subscriptions/2c73c582-4b11-4800-96f9-a9bd790a861c/resourceGroups/testrg
	data:    Name:                testrg
	data:    Location:            westus
	data:    Provisioning State:  Succeeded
	data:    Tags: 
	data:    
	info:    group create command OK

	azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-windows-vm/azuredeploy.json testrg firstdeployment
	info:    Executing command group deployment create
	info:    Supply values for the following parameters
	newStorageAccountName: newstorage
	adminUsername: ops
	adminPassword: Pa$$W0rd1
	dnsNameForPublicIP: contoso
	windowsOSVersion: 2012-R2-Datacenter
	+ Initializing template configurations and parameters                          
	+ Creating a deployment                                                        
	info:    Created template deployment "firstdeployment"
	+ Registering providers                                                        


## 추가 리소스

[Azure 리소스 관리자의 Azure 계산, 네트워크 및 저장소 공급자](virtual-machines-azurerm-versus-azuresm.md)

[Azure 리소스 관리자 개요](resource-group-overview.md)

[Azure 리소스 관리자 및 PowerShell을 사용하여 Windows 가상 컴퓨터 만들기](virtual-machines-create-windows-powershell-rm.md)

[PowerShell 및 Azure 서비스 관리자를 사용하여 Windows 가상 컴퓨터 만들기](virtual-machines-create-windows-powershell-sm.md)

[가상 컴퓨터 설명서](http://azure.microsoft.com/documentation/services/virtual-machines/)

[Azure PowerShell을 설치 및 구성하는 방법](install-configure-powershell.md)

<!--HONumber=52-->
