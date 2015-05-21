<properties 
	pageTitle="고가용성 SharePoint 팜 리소스 관리자 템플릿" 
	description="고가용성 SharePoint 팜용 Azure 리소스 관리자 템플릿 파일의 구조를 단계별로 안내합니다." 
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

# 고가용성 SharePoint 팜 리소스 관리자 템플릿

이 항목에서는 고가용성 SharePoint 팜용 azuredeploy.json 템플릿 파일의 구조를 단계별로 안내합니다.

## "parameters" 섹션

"parameters"는 이 템플릿에 데이터를 입력하는 데 사용되는 매개 변수를 지정합니다. 최대 50개의 매개 변수를 정의할 수 있습니다. Azure 위치에 대한 매개 변수의 예는 다음과 같습니다.

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

"variables" 섹션은 이 템플릿 전체에서 사용할 수 있는 변수를 지정합니다. 최대 100개의 변수를 정의할 수 있습니다. 다음은 몇 가지 예입니다.

	"RDPNAT":"RDP", 
	"spWebLB":"spWeb", 
	"SQLAOListener":"SQLAlwaysOnEndPointListener", 
	"SQLAOProbe":"SQLAlwaysOnEndPointProbe", 
	"staticSubnetName": "staticSubnet", 
	"sqlSubnetName": "sqlSubnet", 
	"spwebSubnetName": "spwebSubnet", 
	"spappSubnetName": "spappSubnet", 

## "resources" 섹션

**"resources"** 섹션은 리소스 그룹에 SharePoint 팜용 리소스를 배포하는 데 필요한 정보를 지정합니다. 최대 250개의 리소스를 정의할 수 있으며, 5개 수준까지 리소스 종속성을 정의할 수 있습니다.

이 섹션은 다음 하위 섹션을 포함합니다.

### Microsoft.Storage/storageAccounts  

이 섹션은 팜의 모든 VHD 및 디스크 리소스에 대한 새 저장소 계정을 만듭니다. 저장소 계정에 대한 JSON 코드는 다음과 같습니다.

	{
	  "type": "Microsoft.Storage/storageAccounts",
	  "name": "[parameters('newStorageAccountName')]",
	  "apiVersion": "2014-12-01-preview",
	  "location": "[parameters('deploymentLocation')]",
	  "properties": {
		"accountType": "[parameters('storageAccountType')]"
	  }
	},

### Microsoft.Network/publicIPAddresses

이러한 섹션은 각 가상 컴퓨터에서 인터넷을 통해 연결할 수 있는 공용 IP 주소 집합을 만듭니다. 다음은 예제입니다.

	{
		"apiVersion": "2014-12-01-preview",
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

이러한 섹션은 배포의 각 계층에 대해 하나씩 4개의 가용성 집합을 만듭니다.

- Active Directory 도메인 컨트롤러
- SQL Server 클러스터
- 응용 프로그램 계층 서버
- 웹 계층 서버

다음은 예제입니다.

	{
		"type": "Microsoft.Compute/availabilitySets",
		"name": "[variables('spAvailabilitySetName')]",
		"apiVersion": "2014-12-01-preview",
		"location": "[parameters('deploymentLocation')]"
	},

### Microsoft.Network/virtualNetworks

이 섹션은 가상 컴퓨터가 배치되는 4개의 서브넷\(배포의 각 계층에 대해 하나씩\)이 있는 클라우드 전용 가상 네트워크를 만듭니다. JSON 코드는 다음과 같습니다.

	{
		"name": "[parameters('virtualNetworkName')]",
		"type": "Microsoft.Network/virtualNetworks",
		"location": "[parameters('deploymentLocation')]",
		"apiVersion": "2014-12-01-preview",
		"properties": {
			"addressSpace": {
			"addressPrefixes": [
				"[parameters('virtualNetworkPrefix')]"
			]
			},
			"subnets": "[variables('subnets')]"
		}
	},



### Microsoft.Network/loadBalancers

이러한 섹션은 각 가상 컴퓨터에 대해 인터넷을 통해 전달되는 인바운드 트래픽에 대한 NAT 및 트래픽 필터링을 제공하는 부하 분산 장치 인스턴스를 만듭니다. 각 부하 분산 장치에 대한 설정은 프런트 엔드, 백 엔드 및 인바운드 NAT 규칙을 구성합니다. 예를 들어 각 가상 컴퓨터에 대한 원격 데스크톱 트래픽 규칙과 웹 계층 서버에서 인터넷을 통해 전달되는 인바운드 웹 트래픽\(TCP 포트 80\)을 허용하는 규칙이 있습니다. 웹 계층 서버에 대한 예는 다음과 같습니다.

        {
            "apiVersion": "2014-12-01-preview",
            "name": "[variables('splbName')]",
            "type": "Microsoft.Network/loadBalancers",
            "location": "[parameters('deploymentLocation')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/publicIPAddresses',variables('spIPAddressName'))]"
            ],
            "properties": {
                "frontendIPConfigurations": [
                    {
                        "name": "[variables('spLBFE')]",
                        "properties": {
                            "publicIPAddress": {
                                "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('spIPAddressName'))]"
                            },
                        }
                    }
                ],
                "backendAddressPools": [
                    {
                        "name": "[variables('spWebLBBE')]"
                    }
                ],
                "loadBalancingRules": [
                    {
                        "name": "[variables('spWebLB')]",
                        "properties": {
                        "frontendIPConfiguration": {
                            "id": "[variables('splbFEConfigID')]"
                        },
                        "probe": {
                            "id": "[variables('spwebProbeID')]"
                        },
                        "protocol": "tcp",
                        "frontendPort": 80,
                        "backendPort": 80,
                        "enableFloatingIP": false
                        }
                    }
                ],
                "probes": [
                    {
                        "name": "[variables('spWebProbe')]",
                        "properties": {
                            "protocol": "http",
                            "port": "[variables('spWebProbePort')]",
                            "intervalInSeconds": "15",
                            "numberOfProbes": "5",
                            "requestPath":"/"
                        }
                    }
                ]
            }
        },

### Microsoft.Network/networkInterfaces

이러한 섹션은 각 가상 컴퓨터에 대한 하나의 네트워크 인터페이스를 만들고 도메인 컨트롤러에 대한 고정 IP 주소를 구성합니다. 주 도메인 컨트롤러에 대한 네트워크 인터페이스의 예는 다음과 같습니다.

	{
		"name": "[variables('adPDCNicName')]",
		"type": "Microsoft.Network/networkInterfaces",
		"location": "[parameters('deploymentLocation')]",
		"dependsOn": [
			"[parameters('virtualNetworkName')]",
			"[concat('Microsoft.Network/loadBalancers/',variables('rdpLBName'))]"
		],
		"apiVersion": "2014-12-01-preview",
		"properties": {
			"ipConfigurations": [
				{
					"name": "ipconfig1",
					"properties": {
						"privateIPAllocationMethod": "Static",
						"privateIPAddress" :"[parameters('adPDCNicIPAddress')]",
						"subnet": {
							"id": "[variables('staticSubnetRef')]"
						},
						"loadBalancerBackendAddressPools": [
							{
								"id":"[variables('adBEAddressPoolID')]"
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

이러한 섹션은 배포에서 9개의 가상 컴퓨터를 만들고 구성합니다.

처음 두 개의 주 섹션은 배포에서 도메인 컨트롤러를 만들고 구성합니다. 각 섹션은 다음을 수행합니다.

- 각 도메인 컨트롤러 가상 컴퓨터에 대한 저장소 계정, 가용성 집합, 네트워크 인터페이스 및 부하 분산 장치 인스턴스를 지정합니다.
- 각 도메인 컨트롤러 가상 컴퓨터에 추가 디스크를 추가합니다.
- PowerShell 스크립트를 실행하여 가상 컴퓨터를 도메인 컨트롤로 구성합니다.

주 도메인 컨트롤러의 예는 다음과 같습니다.

        {
            "apiVersion": "2014-12-01-preview",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[variables('adPDCVMName')]",
            "location": "[parameters('deploymentLocation')]",
            "dependsOn": [
                "[resourceId('Microsoft.Storage/storageAccounts',parameters('newStorageAccountName'))]",
                "[resourceId('Microsoft.Network/networkInterfaces',variables('adPDCNicName'))]",
                "[resourceId('Microsoft.Compute/availabilitySets', variables('adAvailabilitySetName'))]",
                "[resourceId('Microsoft.Network/loadBalancers',variables('rdpLBName'))]"
            ],
            "properties": {
                "hardwareProfile": {
                    "vmSize": "[parameters('adVMSize')]"
                },
                "availabilitySet": {
                    "id": "[resourceId('Microsoft.Compute/availabilitySets', variables('adAvailabilitySetName'))]"
                },
                "osProfile": {
                    "computername": "[variables('adPDCVMName')]",
                    "adminUsername": "[parameters('adminUsername')]",
                    "adminPassword": "[parameters('adminPassword')]",
                    "windowsProfile": {
                        "provisionVMAgent": "true"
                    }
                },
                "storageProfile": {
                    "sourceImage": {
                        "id": "[variables('adSourceImageName')]"
                    },
                    "destinationVhdsContainer": "[concat('http://',parameters('newStorageAccountName'),'.blob.core.windows.net/',parameters('vmContainerName'),'/')]",
                    "dataDisks": [
                        {
                            "vhd": {
                                "uri":"[concat('http://',parameters('newStorageAccountName'),'.blob.core.windows.net/',parameters('vmContainerName'),'/', variables('adPDCVMName'),'data-1.vhd')]"
                                },
                            "name":"[concat(variables('adPDCVMName'),'-data-disk1')]",
                            "caching" : "None",
                            "diskSizeGB": "[variables('adDataDiskSize')]",
                            "lun": 0
                        }
                    ]
                },
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('adPDCNicName'))]"
                        }
                    ]
                }
            },
            "resources" :[
                {
                    "type": "Microsoft.Compute/virtualMachines/extensions",
                    "name": "[concat(variables('adPDCVMName'),'/InstallDomainController')]",
                    "apiVersion": "2014-12-01-preview",
                    "location": "[parameters('deploymentLocation')]",
                    "dependsOn":[
                        "[resourceId('Microsoft.Compute/virtualMachines', variables('adPDCVMName'))]"
                    ],
                    "properties": {
                        "publisher": "Microsoft.Powershell",
                        "type": "DSC",
                        "typeHandlerVersion": "1.7",
                        "settings": {
                            "ModulesUrl": "[variables('adPDCModulesURL')]",
                            "ConfigurationFunction": "[variables('adPDCConfigurationFunction')]",
                            "Properties": {
                                "DomainName": "[parameters('domainName')]",
                                "AdminCreds":{
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


**"name": "UpdateVNetDNS"**로 시작하는 각 도메인 컨트롤러 뒤의 추가 섹션은 두 도메인 컨트롤의 고정 IP 주소를 사용하도록 가상 네트워크의 DNS 서버를 구성합니다.

다음 3개의 **"type": "Microsoft.Compute/virtualMachines"** 섹션은 배포에서 SQL Server 클러스터 가상 컴퓨터를 만듭니다. 각 섹션은 다음을 수행합니다.

- 각 가상 컴퓨터에 대한 저장소 계정, 가용성 집합, 부하 분산 장치, 가상 네트워크 및 네트워크 인터페이스를 지정합니다.
- 각 SQL Server에 추가 디스크 두 개를 추가합니다.

추가 **"Microsoft.Compute/virtualMachines/extensions"** 섹션은 PowerShell 스크립트를 호출하여 SQL Server 클러스터 가상 컴퓨터 및 SQL Server 클러스터를 구성하고 AlwaysOn 가용성 그룹을 사용하도록 설정합니다.

다음 4개의 **"type": "Microsoft.Compute/virtualMachines"** 섹션은 배포에서 SharePoint 팜 가상 컴퓨터를 만듭니다. 각 섹션은 각 가상 컴퓨터에 대한 저장소 계정, 가용성 집합, 부하 분산 장치, 가상 네트워크 및 네트워크 인터페이스를 지정합니다.

추가 **"Microsoft.Compute/virtualMachines/extensions"** 섹션은 PowerShell 스크립트를 호출하여 SharePoint Server를 SharePoint 팜으로 구성합니다.

JSON 파일의 **"resources"** 섹션에 구성된 전체 하위 섹션은 다음을 수행합니다.

1.	여러 가상 컴퓨터를 지원하는 데 필요한 Azure 인프라의 요소\(저장소 계정, 공용 IP 주소, 가용성 집합, 가상 네트워크, 네트워크 인터페이스, 부하 분산 장치 인스턴스\)를 만듭니다.
2.	이전에 만든 일반 및 특정 Azure 인프라 요소를 사용하는 도메인 컨트롤러 가상 컴퓨터를 만들고, 데이터 디스크를 추가하고, PowerShell 스크립트를 실행합니다. 또한 도메인 컨트롤러의 고정 IP 주소를 사용하도록 가상 네트워크를 업데이트합니다.
3.	도메인 컨트롤러에 대해 이전에 만든 일반 및 특정 Azure 인프라 요소를 사용하는 SQL Server 클러스터 가상 컴퓨터를 만들고, 데이터 디스크를 추가하고, PowerShell 스크립트를 실행하여 클러스터 및 SQL Server AlwaysOn 가용성 그룹을 구성합니다.
4.	이전에 만든 일반 및 특정 Azure 인프라 요소를 사용하는 SharePoint Server 가상 컴퓨터를 만들고, 데이터 디스크를 추가하고, PowerShell 스크립트를 실행하여 SharePoint 팜을 구성합니다.

Azure에서 다중 계층 인프라를 구축하는 사용자 고유의 JSON 템플릿은 동일한 단계를 따라야 합니다.

1.	배포를 지원하는 데 필요한 Azure 인프라의 일반 요소\(저장소 계정, 가상 네트워크\), 계층 관련 요소\(가용성 집합\) 및 가상 컴퓨터 관련 요소\(공용 IP 주소, 가용성 집합, 네트워크 인터페이스 및 부하 분산 장치 인스턴스\)를 만듭니다.
2.	응용 프로그램의 각 계층\(예: 인증, 데이터베이스, 웹\)에 대해 일반 요소\(저장소 계정, 가상 네트워크\), 계층 관련 요소\(가용성 집합\) 및 가상 컴퓨터 관련 요소\(공용 IP 주소, 가용성 집합, 네트워크 인터페이스 및 부하 분산 장치 인스턴스\)를 사용하여 해당 계층에서 서버를 만들고 구성합니다.

자세한 내용은 [Azure 리소스 관리자 템플릿 언어](https://msdn.microsoft.com/library/azure/dn835138.aspx)를 참조하세요.


## 추가 리소스

[Azure 리소스 관리자의 Azure 계산, 네트워크 및 저장소 공급자](virtual-machines-azurerm-versus-azuresm.md)

[Azure 리소스 관리자 개요](resource-group-overview.md)

[Azure 리소스 관리자 템플릿 작성](resource-group-authoring-templates.md)

[가상 컴퓨터 설명서](http://azure.microsoft.com/documentation/services/virtual-machines/)


<!--HONumber=52-->
