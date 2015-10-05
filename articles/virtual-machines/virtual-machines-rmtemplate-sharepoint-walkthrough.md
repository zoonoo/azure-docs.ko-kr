<properties
	pageTitle="세 개의 서버 SharePoint 팜 ARM 템플릿 | Microsoft Azure"
	description="세 개의 서버 SharePoint 팜용 Azure 리소스 관리자 템플릿 파일의 구조를 단계별로 안내합니다."
	services="virtual-machines"
	documentationCenter=""
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"                                                                             ms.tgt_pltfrm="vm-windows-sharepoint"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/28/2015"
	ms.author="davidmu"/>

# 세 개의 서버 SharePoint 팜 리소스 관리자 템플릿

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]이 문서에서는 리소스 관리자 배포 모델을 사용하여 리소스를 만드는 방법을 설명합니다. 클래식 배포 모델을 사용하여 이 리소스를 만들 수 없습니다.

이 항목에서는 세 개의 서버 SharePoint 팜용 azuredeploy.json 템플릿 파일의 구조를 단계별로 안내합니다. [여기](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sharepoint-three-vm/azuredeploy.json)에서 브라우저를 통해 이 템플릿의 내용을 확인할 수도 있습니다.

또는, azuredeploy.json 파일의 로컬 복사본을 검사하고 파일 위치로 로컬 폴더를 지정한 다음 해당 폴더를 만듭니다(예를 들어, C:\\Azure\\Templates\\SharePointFarm). 폴더 이름을 입력하고 Azure PowerShell 명령 프롬프트에서 다음 명령을 실행합니다.

	$folderName="<folder name, such as C:\Azure\Templates\SharePointFarm>"
	$webclient = New-Object System.Net.WebClient
	$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/sharepoint-three-vm/azuredeploy.json"
	$filePath = $folderName + "\azuredeploy.json"
	$webclient.DownloadFile($url,$filePath)

텍스트 편집기 또는 선택한 도구에서 azuredeploy.json 템플릿을 엽니다. 아래에 템플릿 파일의 구조와 각 섹션의 용도에 대한 설명이 나와 있습니다.

## "parameters" 섹션

**"parameters"** 섹션은 이 템플릿에 데이터를 입력하는 데 사용되는 매개 변수를 지정합니다. 템플릿을 실행할 때는 데이터를 제공해야 합니다. 최대 50개의 매개 변수를 정의할 수 있습니다. Azure 위치에 대한 매개 변수의 예는 다음과 같습니다.

	"deploymentLocation": {
		"type": "string",
		"allowedValues": [
			"West US",
			"East US",
			"West Europe",
			"East Asia",
			"Southeast Asia"
		],
		"metadata": {
			"Description": "The region to deploy the resources into"
		},
		"defaultValue":"West Europe"
	},

## "variables" 섹션

**"variables"** 섹션은 템플릿에서 사용하는 변수와 그 값을 지정합니다. 변수 값은 명시적으로 설정되거나 매개 변수 값에서 파생될 수 있습니다. 매개 변수와는 반대로, 템플릿을 실행할 때 변수 값을 제공하지 않습니다. 최대 100개의 변수를 정의할 수 있습니다. 다음은 몇 가지 예입니다.

	"LBFE": "LBFE",
	"LBBE": "LBBE",
	"RDPNAT": "RDP",
	"spWebNAT": "spWeb",
	"adSubnetName": "adSubnet",
	"sqlSubnetName": "sqlSubnet",
	"spSubnetName": "spSubnet",
	"adNicName": "adNic",
	"sqlNicName": "sqlNic",
	"spNicName": "spNic",

## "resources" 섹션

**"resources"** 섹션은 리소스 그룹에 SharePoint 팜용 리소스를 배포하는 데 필요한 정보를 지정합니다. 최대 250개의 리소스를 정의할 수 있으며, 5개 수준까지 리소스 종속성을 정의할 수 있습니다.

이 섹션은 다음 하위 섹션을 포함합니다.

### Microsoft.Storage/storageAccounts  

이 섹션은 팜의 모든 VHD 및 디스크 리소스에 대한 새 저장소 계정을 만듭니다. 저장소 계정에 대한 JSON 코드는 다음과 같습니다.

	{
	  "type": "Microsoft.Storage/storageAccounts",
	  "name": "[parameters('newStorageAccountName')]",
	  "apiVersion": "2015-05-01-preview",
	  "location": "[parameters('deploymentLocation')]",
	  "properties": {
		"accountType": "[parameters('storageAccountType')]"
	  }
	},

### Microsoft.Network/publicIPAddresses

이러한 섹션은 각 가상 컴퓨터에서 인터넷을 통해 연결할 수 있는 공용 IP 주소 집합을 만듭니다. 다음은 예제입니다.

	{
		"apiVersion": "2015-05-01-preview",
		"type": "Microsoft.Network/publicIPAddresses",
		"name": "[variables('adpublicIPAddressName')]",
		"location": "[parameters('deploymentLocation')]",
		"properties": {
			"publicIPAllocationMethod": "[parameters('publicIPAddressType')]",
			"dnsSettings": {
				"domainNameLabel": "[parameters('adDNSPrefix')]"
			}
		}
	},

### Microsoft.Compute/availabilitySets

이러한 섹션은 배포의 각 계층에 대해 하나씩 세 개의 가용성 집합을 만듭니다.

- Active Directory 도메인 컨트롤러
- SQL Server 클러스터
- SharePoint 서버

다음은 예제입니다.

	{
		"type": "Microsoft.Compute/availabilitySets",
		"name": "[variables('spAvailabilitySetName')]",
		"apiVersion": "2015-05-01-preview",
		"location": "[parameters('deploymentLocation')]"
	},

### Microsoft.Network/virtualNetworks

이 섹션은 가상 컴퓨터가 배치되는 세 개의 서브넷(배포의 각 계층에 대해 하나씩)이 있는 클라우드 전용 가상 네트워크를 만듭니다. JSON 코드는 다음과 같습니다.

	{
		"name": "[parameters('virtualNetworkName')]",
		"type": "Microsoft.Network/virtualNetworks",
		"location": "[parameters('deploymentLocation')]",
		"apiVersion": "2015-05-01-preview",
		"properties": {
			"addressSpace": {
			"addressPrefixes": [
				"[parameters('virtualNetworkAddressRange')]"
			]
			},
			"subnets": "[variables('subnets')]"
		}
	},


### Microsoft.Network/loadBalancers

이러한 섹션은 각 가상 컴퓨터에 대해 인터넷을 통해 전달되는 인바운드 트래픽에 대한 네트워크 주소 변환(NAT) 및 트래픽 필터링을 제공하는 부하 분산 장치 인스턴스를 만듭니다. 각 부하 분산 장치에 대한 설정은 프런트 엔드, 백 엔드 및 인바운드 NAT 규칙을 구성합니다. 예를 들어 각 가상 컴퓨터에 대한 원격 데스크톱 트래픽 규칙과 SharePoint 서버에서 인터넷을 통해 전달되는 인바운드 웹 트래픽(TCP 포트 80)을 허용하는 규칙이 있습니다. SharePoint 서버에 대한 예는 다음과 같습니다.


	{
		"apiVersion": "2015-05-01-preview",
		"name": "[variables('spLBName')]",
		"type": "Microsoft.Network/loadBalancers",
		"location": "[parameters('deploymentLocation')]",
		"dependsOn": [
			"[resourceId('Microsoft.Network/publicIPAddresses',variables('sppublicIPAddressName'))]"
		],
		"properties": {
			"frontendIPConfigurations": [
				{
					"name": "[variables('LBFE')]",
					"properties": {
						"publicIPAddress": {
							"id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('sppublicIPAddressName'))]"
						}
					}
				}
			],
			"backendAddressPools": [
				{
					"name": "[variables('LBBE')]"
				}
			],
			"inboundNatRules": [
				{
					"name": "[variables('RDPNAT')]",
					"properties": {
						"frontendIPConfiguration": {
							"id": "[variables('splbFEConfigID')]"
						},
						"protocol": "tcp",
						"frontendPort": "[parameters('RDPPort')]",
						"backendPort": 3389,
						"enableFloatingIP": false
					}
				},
				{
					"name": "[variables('spWebNAT')]",
					"properties": {
						"frontendIPConfiguration": {
							"id": "[variables('splbFEConfigID')]"
						},
						"protocol": "tcp",
						"frontendPort": 80,
						"backendPort": 80,
						"enableFloatingIP": false
					}
				}
			]
		}
	},

### Microsoft.Network/networkInterfaces

이러한 섹션은 각 가상 컴퓨터에 대한 하나의 네트워크 인터페이스를 만들고 도메인 컨트롤러에 대한 고정 IP 주소를 구성합니다. 도메인 컨트롤러의 네트워크 인터페이스의 예는 다음과 같습니다.

	{
		"name": "[variables('adNicName')]",
		"type": "Microsoft.Network/networkInterfaces",
		"location": "[parameters('deploymentLocation')]",
		"dependsOn": [
			"[parameters('virtualNetworkName')]",
			"[concat('Microsoft.Network/loadBalancers/',variables('adlbName'))]"
		],
		"apiVersion": "2015-05-01-preview",
		"properties": {
			"ipConfigurations": [
				{
					"name": "ipconfig1",
					"properties": {
						"privateIPAllocationMethod": "Static",
						"privateIPAddress": "[parameters('adNicIPAddress')]",
						"subnet": {
							"id": "[variables('adSubnetRef')]"
						},
						"loadBalancerBackendAddressPools": [
							{
								"id": "[variables('adBEAddressPoolID')]"
							}
						],
						"loadBalancerInboundNatRules": [
							{
								"id": "[variables('adRDPNATRuleID')]"
							}
						]
					}
				}
			]
		}
	},


### Microsoft.Compute/virtualMachines

이러한 섹션은 배포에서 세 개의 가상 컴퓨터를 만들고 구성합니다.

첫 번째 섹션을 만들고 도메인 컨트롤러를 구성합니다. 다음과 같습니다.

- 저장소 계정, 가용성 집합, 네트워크 인터페이스 및 부하 분산 장치 인스턴스 지정
- 추가 디스크 추가
- PowerShell 스크립트를 실행하여 도메인 컨트롤러 구성

JSON 코드는 다음과 같습니다.

		{
			"apiVersion": "2015-05-01-preview",
			"type": "Microsoft.Compute/virtualMachines",
			"name": "[parameters('adVMName')]",
			"location": "[parameters('deploymentLocation')]",
			"dependsOn": [
				"[resourceId('Microsoft.Storage/storageAccounts',parameters('newStorageAccountName'))]",
				"[resourceId('Microsoft.Network/networkInterfaces',variables('adNicName'))]",
				"[resourceId('Microsoft.Compute/availabilitySets', variables('adAvailabilitySetName'))]",
				"[resourceId('Microsoft.Network/loadBalancers',variables('adlbName'))]"
			],
			"properties": {
				"hardwareProfile": {
					"vmSize": "[parameters('adVMSize')]"
				},
				"availabilitySet": {
					"id": "[resourceId('Microsoft.Compute/availabilitySets', variables('adAvailabilitySetName'))]"
				},
				"osProfile": {
					"computername": "[parameters('adVMName')]",
					"adminUsername": "[parameters('adminUsername')]",
					"adminPassword": "[parameters('adminPassword')]"
				},
				"storageProfile": {
					"imageReference": {
						"publisher": "[parameters('adImagePublisher')]",
						"offer": "[parameters('adImageOffer')]",
						"sku": "[parameters('adImageSKU')]",
						"version": "latest"
					},
					"osDisk": {
						"name": "osdisk",
						"vhd": {
							"uri": "[concat('http://',parameters('newStorageAccountName'),'.blob.core.windows.net/',parameters('vmContainerName'),'/',parameters('adVMName'),'-osdisk.vhd')]"
						},
						"caching": "ReadWrite",
						"createOption": "FromImage"
					},
					"dataDisks": [
						{
							"vhd": {
								"uri": "[concat('http://',parameters('newStorageAccountName'),'.blob.core.windows.net/',parameters('vmContainerName'),'/', variables('adDataDisk'),'-1.vhd')]"
							},
							"name": "[concat(parameters('adVMName'),'-data-disk1')]",
							"caching": "None",
							"createOption": "empty",
							"diskSizeGB": "[variables('adDataDiskSize')]",
							"lun": 0
						}
					]
				},
				"networkProfile": {
					"networkInterfaces": [
						{
							"id": "[resourceId('Microsoft.Network/networkInterfaces',variables('adNicName'))]"
						}
					]
				}
			},
			"resources": [
				{
					"type": "Microsoft.Compute/virtualMachines/extensions",
					"name": "[concat(parameters('adVMName'),'/InstallDomainController')]",
					"apiVersion": "2015-05-01-preview",
					"location": "[parameters('deploymentLocation')]",
					"dependsOn": [
						"[resourceId('Microsoft.Compute/virtualMachines', parameters('adVMName'))]"
					],
					"properties": {
						"publisher": "Microsoft.Powershell",
						"type": "DSC",
						"typeHandlerVersion": "1.7",
						"settings": {
							"ModulesUrl": "[variables('adModulesURL')]",
							"ConfigurationFunction": "[variables('adConfigurationFunction')]",
							"Properties": {
								"DomainName": "[parameters('domainName')]",
								"AdminCreds": {
									"UserName": "[parameters('adminUserName')]",
									"Password": "PrivateSettingsRef:AdminPassword"
								}
							}
						},
						"protectedSettings": {
							"Items": {
								"AdminPassword": "[parameters('adminPassword')]"
							}
						}
					}
				}
			]
		},

**"name": "UpdateVNetDNS"**로 시작하는 도메인 컨트롤러의 추가 섹션은 도메인 컨트롤의 고정 IP 주소를 사용하도록 가상 네트워크의 DNS 서버를 구성합니다.

다음 **"type": "Microsoft.Compute/virtualMachines"** 섹션은 배포에서 SQL Server 가상 컴퓨터를 만듭니다.

- 저장소 계정, 가용성 집합, 부하 분산 장치, 가상 네트워크 및 네트워크 인터페이스 지정
- 추가 디스크 추가

추가 **"Microsoft.Compute/virtualMachines/extensions"** 섹션은 PowerShell 스크립트를 호출하여 SQL Server를 구성합니다.

다음 **"type": "Microsoft.Compute/virtualMachines"** 섹션에서는 배포에서 SharePoint 가상 컴퓨터를 만들어, 저장소 계정, 가용성 집합, 부하 분산 장치, 가상 네트워크 및 네트워크 인터페이스를 지정합니다. 추가 **"Microsoft.Compute/virtualMachines/extensions"** 섹션은 PowerShell 스크립트를 호출하여 SharePoint 팜을 구성합니다.

JSON 파일의 **"resources"** 섹션에 구성된 전체 하위 섹션은 다음을 수행합니다.

1.	여러 가상 컴퓨터를 지원하는 데 필요한 Azure 인프라의 요소(저장소 계정, 공용 IP 주소, 가용성 집합, 가상 네트워크, 네트워크 인터페이스, 부하 분산 장치 인스턴스)를 만듭니다.
2.	이전에 만든 일반 및 특정 Azure 인프라 요소를 사용하는 도메인 컨트롤러 가상 컴퓨터를 만들고, 데이터 디스크를 추가하고, PowerShell 스크립트를 실행합니다. 또한 도메인 컨트롤러의 고정 IP 주소를 사용하도록 가상 네트워크를 업데이트합니다.
3.	도메인 컨트롤러에 대해 이전에 만든 일반 및 특정 Azure 인프라 요소를 사용하는 SQL Server 가상 컴퓨터를 만들고, 데이터 디스크를 추가하고, PowerShell 스크립트를 실행하여 SQL Server를 구성합니다.
4.	이전에 만든 일반 및 특정 Azure 인프라 요소를 사용하는 SharePoint Server 가상 컴퓨터를 만들고, PowerShell 스크립트를 실행하여 SharePoint 팜을 구성합니다.

Azure에서 다중 계층 인프라를 구축하는 사용자 고유의 JSON 템플릿은 동일한 단계를 따라야 합니다.

1.	배포를 지원하는 데 필요한 Azure 인프라의 일반 요소(저장소 계정, 가상 네트워크), 계층 관련 요소(가용성 집합) 및 가상 컴퓨터 관련 요소(공용 IP 주소, 가용성 집합, 네트워크 인터페이스 및 부하 분산 장치 인스턴스)를 만듭니다.
2.	응용 프로그램의 각 계층(예: 인증, 데이터베이스, 웹)에 대해 일반 요소(저장소 계정, 가상 네트워크), 계층 관련 요소(가용성 집합) 및 가상 컴퓨터 관련 요소(공용 IP 주소, 가용성 집합, 네트워크 인터페이스 및 부하 분산 장치 인스턴스)를 사용하여 해당 계층에서 서버를 만들고 구성합니다.

자세한 내용은 [Azure 리소스 관리자 템플릿 언어](../resource-group-authoring-templates.md)를 참조하세요.

## 추가 리소스

[Azure 리소스 관리자의 Azure 계산, 네트워크 및 저장소 공급자](virtual-machines-azurerm-versus-azuresm.md) [Azure 리소스 관리자 개요](../resource-group-overview.md)

[Azure 리소스 관리자 템플릿 작성](../resource-group-authoring-templates.md)

[가상 컴퓨터 설명서](http://azure.microsoft.com/documentation/services/virtual-machines/)

<!---HONumber=Sept15_HO4-->