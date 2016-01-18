<properties
	pageTitle="템플릿을 사용하여 Azure 리소스 배포 | Microsoft Azure"
	description="Azure 리소스 관리 라이브러리에서 사용 가능한 일부 클라이언트를 사용하여 가상 컴퓨터, 가상 네트워크 및 저장소 계정을 배포하는 방법을 알아봅니다."
	services="virtual-machines,virtual-networks,storage"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="multiple"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/06/2016"
	ms.author="davidmu"/>

# .NET 라이브러리 및 템플릿을 사용하여 Azure 리소스 배포

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]클래식 배포 모델.


리소스 그룹 및 템플릿을 사용하면 응용 프로그램을 함께 지원하는 모든 리소스를 관리할 수 있습니다. 이 자습서에는 Azure 리소스 관리 라이브러리에서 사용 가능한 일부 클라이언트를 사용하는 방법 및 가상 컴퓨터, 가상 네트워크 및 저장소 계정을 배포하기 위해 템플릿을 빌드하는 방법을 보여 줍니다.

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

이 자습서를 완료하려면 다음이 필요합니다.

- [Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)
- [Azure 저장소 계정](../storage-create-storage-account.md)
- [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) 또는 [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855)

>[AZURE.NOTE]이 시점에서 만든 저장소 계정은 템플릿을 저장하는 데 사용됩니다. 다른 저장소 계정은 가상 컴퓨터에 디스크를 저장하는 데 사용되는 템플릿을 배포할 때 만들어집니다. 이 저장소 계정을 명명한 템플릿에 컨테이너를 만듭니다.

[AZURE.INCLUDE [powershell-preview](../../includes/powershell-preview-inline-include.md)]

이러한 단계를 수행하려면 약 30분이 걸립니다.

## 1단계: Azure AD에 응용 프로그램 추가 및 사용 권한 설정

Azure AD를 사용하여 Azure 리소스 관리자에 요청을 인증하려면, 응용 프로그램이 기본 디렉터리에 추가되어야 합니다. 응용 프로그램을 추가하려면 다음을 수행 합니다.

1. Azure PowerShell 명령 프롬프트를 연 다음 이 명령을 실행하고 메시지가 표시되면 구독에 대한 자격 증명을 입력합니다.

			Login-AzureRmAccount

2. 다음 명령에서 {password}를 사용할 명령으로 바꾼 다음 실행하여 응용 프로그램을 만듭니다.

			New-AzureRmADApplication -DisplayName "My AD Application 1" -HomePage "https://myapp1.com" -IdentifierUris "https://myapp1.com"  -Password "{password}"

	>[AZURE.NOTE]다음 단계에 필요하므로 응용 프로그램이 만들어진 후 반환되는 응용 프로그램 식별자를 기록해 둡니다. 또한 Azure 포털의 Active Directory 섹션에 있는 응용 프로그램의 클라이언트 ID 필드에서 응용 프로그램 식별자를 찾을 수 있습니다.

3. {application-id}를 방금 기록된 식별자로 바꾼 다음 해당 응용 프로그램에 대한 서비스 주체를 만듭니다.

			New-AzureRmADServicePrincipal -ApplicationId {application-id}

4. 응용 프로그램을 사용하는 사용 권한을 설정합니다.

			New-AzureRmRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName "https://myapp1.com"

## 2단계: Visual Studio 프로젝트, 템플릿 파일 및 매개 변수 파일 만들기

### 템플릿 파일 만들기

Azure 리소스 관리자 템플릿을 사용하면 리소스와 관련 배포 매개 변수에 대한 JSON 설명을 사용하여 Azure 리소스를 함께 배포하고 관리할 수 있습니다. 이 자습서에서 작성하는 템플릿은 템플릿 갤러리에서 찾을 수 있는 템플릿과 유사합니다. 자세한 내용을 알아보려면 [미국 서부에서 간단한 Windows VM 배포](https://azure.microsoft.com/documentation/templates/101-simple-windows-vm/)를 참조하세요.

Visual Studio에서 다음을 수행합니다.

1. **파일** > **새로 만들기** > **프로젝트**를 클릭합니다.

2. **템플릿** > **Visual C#**에서 **콘솔 응용 프로그램**을 선택하고, 프로젝트의 이름과 위치를 입력한 다음 **확인**을 클릭합니다.

3. 솔루션 탐색기에서 프로젝트 이름을 마우스 오른쪽 단추로 클릭하고 **추가** > **새 항목**을 클릭합니다.

4.	새 항목 추가 창에서 **텍스트 파일**을 선택하고 *VirtualMachineTemplate.json* 을 이름으로 입력한 후 **추가**를 클릭합니다.

5.	VirtualMachineTemplate.json 파일을 열고 여는 괄호와 닫는 괄호, 필요한 스키마 요소 및 필요한 contentVersion 요소를 추가합니다.

	```
	{
		"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
		"contentVersion": "1.0.0.0",
	}
	```

6. [매개 변수](../resource-group-authoring-templates.md#parameters)는 항상 필요한 것은 아니지만 템플릿 관리를 용이하게 합니다. 값 형식, 기본값(필요한 경우) 및 매개 변수의 허용되는 값을 설명합니다. 이 자습서에서는 가상 컴퓨터, 저장소 계정 및 가상 네트워크를 만드는 데 사용되는 매개 변수가 템플릿에 추가됩니다.

    매개 변수 요소와 해당 자식 요소를 contentVersion 요소 뒤에 추가합니다.

	```
	{
		"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
		"contentVersion": "1.0.0.0",
		"parameters": {
			"newStorageAccountName": { "type": "string" },
			"adminUserName": { "type": "string" },
			"adminPassword": { "type": "securestring" },
			"dnsNameForPublicIP": { "type": "string" },
			"windowsOSVersion": {
				"type": "string",
				"defaultValue": "2012-R2-Datacenter",
				"allowedValues": [ "2008-R2-SP1", "2012-Datacenter", "2012-R2-Datacenter" ]
			}
		},
 	}
	```

7.	템플릿에서 [변수](../resource-group-authoring-templates.md#variables)를 사용하여 자주 변경되는 값 또는 매개 변수 값의 조합에서 만들어야 하는 값을 지정할 수 있습니다.

    변수 요소를 매개 변수 섹션 뒤에 추가합니다.

	```
	{
		"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
		"contentVersion": "1.0.0.0",
		"parameters": {
			"newStorageAccountName": { "type": "string" },
			"adminUsername": { "type": "string" },
			"adminPassword": { "type": "securestring" },
			"dnsNameForPublicIP": { "type": "string" },
			"windowsOSVersion": {
				"type": "string",
				"defaultValue": "2012-R2-Datacenter",
				"allowedValues": ["2008-R2-SP1", "2012-Datacenter", "2012-R2-Datacenter"]
			}
		},
		"variables": {
			"location": "West US",
			"imagePublisher": "MicrosoftWindowsServer",
			"imageOffer": "WindowsServer",
			"OSDiskName": "osdiskforwindowssimple",
			"nicName": "myVMnic1",
			"addressPrefix": "10.0.0.0/16",
			"subnetName": "Subnet",
			"subnetPrefix": "10.0.0.0/24",
			"storageAccountType": "Standard_LRS",
			"publicIPAddressName": "myPublicIP",
			"publicIPAddressType": "Dynamic",
			"vmStorageAccountContainerName": "vhds",
			"vmName": "MyWindowsVM",
			"vmSize": "Standard_A2",
			"virtualNetworkName": "MyVNET",
			"vnetID":"[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
			"subnet1Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]"
		},
	}
	```

8.	가상 컴퓨터, 가상 네트워크 및 저장소 계정과 같은 [리소스](../resource-group-authoring-templates.md#resources)는 다음에 템플릿에 정의됩니다.

    리소스 섹션을 변수 섹션 뒤에 추가합니다.

	```
	{
		"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
		"contentVersion": "1.0.0.0",
		"parameters": {
			"newStorageAccountName": { "type": "string" },
			"adminUsername": { "type": "string" },
			"adminPassword": { "type": "securestring" },
			"dnsNameForPublicIP": { "type": "string" },
			"windowsOSVersion": {
				"type": "string",
				"defaultValue": "2012-R2-Datacenter",
				"allowedValues": [ "2008-R2-SP1", "2012-Datacenter", "2012-R2-Datacenter"]
			}
		},
		"variables": {
			"location": "West US",
			"imagePublisher": "MicrosoftWindowsServer",
			"imageOffer": "WindowsServer",
			"OSDiskName": "osdiskforwindowssimple",
			"nicName": "myVMnic1",
			"addressPrefix": "10.0.0.0/16",
			"subnetName": "Subnet",
			"subnetPrefix": "10.0.0.0/24",
			"storageAccountType": "Standard_LRS",
			"publicIPAddressName": "myPublicIP",
			"publicIPAddressType": "Dynamic",
			"vmStorageAccountContainerName": "vhds",
			"vmName": "MyWindowsVM",
			"vmSize": "Standard_A2",
			"virtualNetworkName": "MyVNET",
			"vnetID":"[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
			"subnet1Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]"
		},
		"resources": [
			{
  			"apiVersion": "2015-05-01-preview",
				"type": "Microsoft.Storage/storageAccounts",
				"name": "[parameters('newStorageAccountName')]",
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
						"addressPrefixes": [ "[variables('addressPrefix')]" ]
					},
					"subnets": [ {
						"name": "[variables('subnetName')]",
						"properties": {
							"addressPrefix": "[variables('subnetPrefix')]"
						}
					} ]
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
					"ipConfigurations": [ {
						"name": "ipconfig1",
						"properties": {
							"privateIPAllocationMethod": "Dynamic",
							"publicIPAddress": {
								"id": "[resourceId('Microsoft.Network/publicIPAddresses', variables('publicIPAddressName'))]"
							},
							"subnet": {
								"id": "[variables('subnetRef')]"
							}
						}
					} ]
				}
			},
			{
				"apiVersion": "2015-06-15",
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
						"computerName": "[variables('vmName')]",
						"adminUsername": "[parameters('adminUsername')]",
						"adminPassword": "[parameters('adminPassword')]",
					},
					"storageProfile": {
						"imageReference": {
							"publisher": "[variables('imagePublisher')]",
							"offer": "[variables('imageOffer')]",
							"sku": "[parameters('windowsOSVersion')]",
							"version" : "latest"
						},
						"osDisk": {
							"name": "osdisk",
							"vhd": {
								"uri": "[concat('http://',parameters('newStorageAccountName'),'.blob.core.windows.net/',variables('vmStorageAccountContainerName'),'/',variables('OSDiskName'),'.vhd')]"
							},
							"caching": "ReadWrite",
							"createOption": "FromImage"
						}
					},
					"networkProfile": {
						"networkInterfaces" : [ {
							"id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
						} ]
					}
				}
			} ]
		}
		```

9.	만든 템플릿 파일을 저장합니다.

### 매개 변수 파일 만들기

템플릿에 정의된 리소스 매개 변수의 값을 지정하려면 값이 포함된 매개 변수 파일을 만들고 템플릿과 함께 리소스 관리자에 제출합니다. Visual Studio에서 다음을 수행합니다.

1.	솔루션 탐색기에서 프로젝트 이름을 마우스 오른쪽 단추로 클릭하고 **추가** > **새 항목**을 클릭합니다.

2.	새 항목 추가 창에서 **텍스트 파일**을 선택하고 *Parameters.json* 을 이름으로 입력한 후 **추가**를 클릭합니다.

3.	Parameters.json 파일을 열고 다음 JSON 내용을 추가합니다.

	```
	{
		"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
		"contentVersion": "1.0.0.0",
		"parameters": {
			"newStorageAccountName": { "value": "mytestsa1" },
			"adminUserName": { "value": "mytestacct1" },
			"adminPassword": { "value": "mytestpass1" },
			"dnsNameForPublicIP": { "value": "mytestdns1" }
		}
	}
	```

    >[AZURE.NOTE]이 자습서는 Windows Server 운영 체제의 버전을 실행하는 가상 컴퓨터를 만듭니다. 기타 이미지 선택에 대해 자세히 알아보려면 [Windows PowerShell 및 Azure CLI를 사용하여 Azure 가상 컴퓨터 탐색 및 선택](resource-groups-vm-searching.md)을 참조하세요.


4.	만든 매개 변수 파일을 저장합니다.

### 파일 업로드

템플릿 파일과 매개 변수 파일은 Azure 저장소 계정에서 Azure 리소스 관리자를 통해 액세스할 수 있습니다. 만든 첫 번째 저장소의 파일을 배치하려면 다음을 수행합니다.

1.	서버 탐색기를 열고 파일을 배치할 저장소 계정의 컨테이너로 이동합니다. 이 자습서에서는 템플릿이 있는 컨테이너의 이름을 templates로 지정합니다.

2.	templates 컨테이너 창의 오른쪽 위에서 Blob 업로드 아이콘을 클릭하고 사용자가 만든 VirtualMachineTemplate.json 파일을 찾아서 **열기**를 클릭합니다.

3. Blob 업로드 아이콘을 다시 클릭하고 사용자가 만든 Parameters.json 파일을 찾아서 **열기**를 클릭합니다.

## 3단계: 라이브러리 설치

NuGet 패키지는 이 자습서를 완료하는데 필요한 라이브러리를 설치하는 가장 쉬운 방법입니다. Azure 리소스 관리 라이브러리 및 Azure Active Directory 인증 라이브러리를 설치해야 합니다. Visual Studio에서 이 라이브러리를 가져오려면 다음을 수행합니다.

1.	솔루션 탐색기에서 프로젝트 이름을 마우스 오른쪽 단추로 클릭한 후 **NuGet 패키지 관리**를 클릭합니다.

2.	검색 상자에 *Active Directory* 를 입력하고 Active Directory 인증 라이브러리 패키지에 대해 **설치**를 클릭한 다음 지침에 따라 패키지를 설치합니다.

3. 	페이지의 위쪽에서 **시험판 포함**을 선택합니다. 검색 상자에 *Microsoft.Azure.Management.Resources*를 입력하고 Microsoft Azure 리소스 관리 라이브러리에 대해 **설치**를 클릭한 다음 지침에 따라 패키지를 설치합니다.

이제 라이브러리를 사용하기 시작하여 응용 프로그램을 만들 준비가 되었습니다.

##4단계: 요청을 인증하는데 사용되는 자격 증명 만들기

Azure Active Directory 응용 프로그램이 생성되고 인증 라이브러리가 설치되었으므로 Azure 리소스 관리자에 요청을 인증하는데 사용되는 자격 증명에 대한 응용 프로그램 정보의 서식을 지정합니다. 다음을 수행합니다.

1.	사용자가 만든 프로젝트에 대한 Program.cs 파일을 연 후, 다음 using 문을 파일의 위쪽에 추가합니다.

	```
	using Microsoft.Azure;
	using Microsoft.IdentityModel.Clients.ActiveDirectory;
	using Microsoft.Azure.Management.Resources;
	using Microsoft.Azure.Management.Resources.Models;
	using Microsoft.Rest;
	```

2.	자격 증명을 만드는 데 필요한 토큰을 가져올 프로그램 클래스에 다음 메서드를 추가합니다.

	```
	private static string GetAuthorizationHeader()
	{
		ClientCredential cc = new ClientCredential("{application-id}", "{password}");
		var context = new AuthenticationContext("https://login.windows.net/{tenant-id}");
		var result = context.AcquireTokenAsync("https://management.azure.com/", cc);
		if (result == null)
		{
			throw new InvalidOperationException("Failed to obtain the JWT token");
		}

		string token = result.AccessToken;

		return token;
	}
	```

	{application-id}를 이전에 기록한 응용 프로그램 ID로 바꾸고, {password}를 AD 응용 프로그램에 대해 선택한 암호로 바꾸고, {tenant-id}를 구독을 위한 테넌트 ID로 바꿉니다. Get-AzureSubscription을 실행하여 테넌트 ID를 찾을 수 있습니다.

3.	Program.cs 파일에서 Main 메서드에 다음 코드를 추가하여 자격 증명을 만듭니다.

	```
	var token = GetAuthorizationHeader();
	var credential = new TokenCredentials(token);
	```

4.	Program.cs 파일을 저장합니다.


##5단계: 템플릿을 배포하는 코드 추가

리소스는 항상 템플릿에서 리소스 그룹에 배포됩니다. [ResourceGroup](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.models.resourcegroup.aspx) 및 [ResourceManagementClient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.resourcemanagementclient.aspx) 클래스를 사용하여 리소스가 배포되는 리소스 그룹을 만듭니다.

1.	Program 클래스에 다음 메서드를 추가하여 리소스 그룹을 만듭니다.

	```
	public static void CreateResourceGroup(
		TokenCredentials credential,
		string groupName,
		string subscriptionId,
		string location)
	{
		Console.WriteLine("Creating the resource group...");
		var resourceManagementClient = new ResourceManagementClient(credential);
		resourceManagementClient.SubscriptionId = subscriptionId;
		var resourceGroup = new ResourceGroup {
			Location = location
		};
		var rgResult = resourceManagementClient.ResourceGroups.CreateOrUpdate(groupName, resourceGroup);
		Console.WriteLine(rgResult.StatusCode.Properties.ProvisioningState);
	}
	```

2.	방금 추가한 메서드를 호출하려면 Main 메서드에 다음 코드를 추가합니다.

	```
	CreateResourceGroup(
		credential,
		"{group-name}",
		"{subscription-id}",
		"{location}");
		Console.ReadLine();
	```

3.	Program 클래스에 다음 메서드를 추가하여 사용자가 정의한 템플릿으로 리소스 그룹에 리소스를 배포합니다.

	```
	public static void CreateTemplateDeployment(
		TokenCredentials credential,
		string groupName,
		string storageName,
		string deploymentName,
		string subscriptionId)
	{
		Console.WriteLine("Creating the template deployment...");
		var deployment = new Deployment();
		deployment.Properties = new DeploymentProperties
		{
			Mode = DeploymentMode.Incremental,
			TemplateLink = new TemplateLink
			{
				Uri = "https://{storage-name}.blob.core.windows.net/templates/VirtualMachineTemplate.json"
			},
			ParametersLink = new ParametersLink
			{
				Uri = "https://{storage-name}.blob.core.windows.net/templates/Parameters.json"
			}
		};
		var resourceManagementClient = new ResourceManagementClient(credential);
		resourceManagementClient.SubscriptionId = subscriptionId;
		var dpResult = resourceManagementClient.Deployments.CreateOrUpdate(
			groupName,
			deploymentName,
			deployment);
		Console.WriteLine(dpResult.Properties.ProvisioningState);
	}
	```

	{storage-name}을 이전에 파일을 저장한 계정의 이름으로 바꿉니다.

4.	방금 추가한 메서드를 호출하려면 Main 메서드에 다음 코드를 추가합니다.

	```
	CreateTemplateDeployment(
		credential,
		"{group-name}",
		"{storage-name}",
		"{deployment-name}",
		"{subscription-id}");
	Console.ReadLine();
	```

    {group-name}을 만든 리소스 그룹의 이름으로 바꿉니다. {storage-name}을 템플릿 파일을 둔 저장소 계정의 이름으로 바꿉니다. {deployment-name}을 리소스의 배포 집합을 식별하는 데 사용하려는 이름으로 바꿉니다. {subscription-id}를 구독 식별자로 바꿉니다. Get-AzureSubscription을 실행하여 이를 가져올 수 있습니다.

##6단계: 리소스를 삭제하는 코드 추가

Azure에서 사용되는 리소스에 대한 요금이 부과되기 때문에, 더 이상 필요하지 않은 리소스를 항상 삭제하는 것이 좋습니다. 리소스 그룹에서 각 리소스를 개별적으로 삭제할 필요가 없습니다. 리소스 그룹을 삭제하면 모든 해당 리소스가 자동으로 삭제됩니다.

1.	Program 클래스에 다음 메서드를 추가하여 리소스 그룹을 삭제합니다.

	```
	public static void DeleteResourceGroup(
		TokenCredentials credential,
		string groupName,
		string subscriptionId)
	{
		Console.WriteLine("Deleting resource group...");
		var resourceGroupClient = new ResourceManagementClient(credential);
		resourceGroupClient.ResourceGroups.DeleteAsync(groupName);
	}
	```

2.	방금 추가한 메서드를 호출하려면 Main 메서드에 다음 코드를 추가합니다.

	```
	DeleteResourceGroup(
		credential,
		"{group-name}",
		"{subscription-id}");
	Console.ReadLine();
	```
    {group-name}을 만든 리소스 그룹의 이름으로 바꿉니다. {subscription-id}를 구독 식별자로 바꿉니다. Get-AzureSubscription을 실행하여 이를 가져올 수 있습니다.

##7단계: 콘솔 응용 프로그램 실행

1.	콘솔 응용 프로그램을 실행하려면, Visual Studio에서 **시작**을 클릭한 다음 구독에 사용되는 동일한 사용자 이름 및 암호를 사용하여 Azure AD에 로그인합니다.

2.	수락됨 상태가 표시된 후에 **Enter** 키를 누릅니다.

	이 콘솔 응용 프로그램을 처음부터 끝까지 완전히 실행하려면 약 5분이 필요합니다. Enter를 눌러 리소스를 삭제하기 전에 Azure 포털에서 리소스 만들기를 확인하는 데에 몇 분이 걸릴 수 있습니다.

3. 리소스의 상태를 보려면 Azure 포털에서 감사 로그로 이동합니다.

	![AD 응용 프로그램 만들기](./media/arm-template-deployment/crpportal.png)

<!---HONumber=AcomDC_0107_2016-->