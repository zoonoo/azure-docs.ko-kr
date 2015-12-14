<properties
	pageTitle="가상 컴퓨터 규모 집합의 크기를 자동으로 조정 | Microsoft Azure"
	description="첫 번째 Azure 가상 컴퓨터 규모 집합 생성 및 관리 시작하기"
	services="virtual-machines"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/19/2015"
	ms.author="davidmu"/>

# 가상 컴퓨터 규모 집합에서 자동으로 컴퓨터 규모 조정

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-machines-create-windows-powershell-service-manager.md).

가상 컴퓨터 규모 집합은 동일한 가상 컴퓨터를 집합으로 쉽게 배포하고 관리할 수 있습니다. 규모 집합은 대규모 응용 프로그램에 대한 높은 확장성과 사용자 지정 가능한 계산 계층을 제공하고 Windows 플랫폼 이미지, Linux 플랫폼 이미지, 사용자 지정 이미지 및 확장을 지원합니다. 규모 집합에 대한 자세한 내용은 [가상 컴퓨터 규모 집합](virtual-machines-vmss-overview.md)을 참조하세요.

이 자습서에서는 Windows 가상 컴퓨터의 가상 컴퓨터 규모 집합을 만들고 집합에서 컴퓨터를 자동으로 규모 조정하는 방법을 보여 줍니다. Azure 리소스 관리자 템플릿을 작성하고 Azure PowerShell을 사용하여 배포하여 이 작업을 수행합니다. 템플릿에 대한 더 자세한 내용은 [Azure 리소스 관리자 템플릿 작성하기](../resource-group-authoring-templates.md)를 참조하세요.

이 자습서에서 작성하는 템플릿은 템플릿 갤러리에서 찾을 수 있는 템플릿과 유사합니다. 자세한 내용은 [Windows VM 및 Jumpbox로 간단한 VM 규모 집합 배포](https://azure.microsoft.com/blog/azure-vm-scale-sets-public-preview)를 참조하세요.

[AZURE.INCLUDE [powershell-preview-inline-include](../../includes/powershell-preview-inline-include.md)]

[AZURE.INCLUDE [virtual-machines-vmss-preview](../../includes/virtual-machines-vmss-preview-include.md)]

## 1단계: 리소스 그룹 및 저장소 계정 만들기

1.	**Microsoft Azure에 로그인합니다**. Microsoft Azure PowerShell 창을 열고 **로그인-AzureRmAccount**를 실행합니다.

2.	**리소스 그룹 만들기** – 모든 리소스는 리소스 그룹에 배포되어야 합니다. 이 자습서의 경우 리소스 그룹의 이름을 **vmss-test1**로 지정합니다. [New-AzureRmResourceGroup](https://msdn.microsoft.com/library/mt603739.aspx)을 참조하세요.

3.	**새 리소스 그룹에 저장소 계정 배포** – 이 자습서에서는 여러 저장소 계정을 사용하여 가상 컴퓨터 규모 집합을 용이하게 합니다. [New-AzureRmStorageAccount](https://msdn.microsoft.com/library/mt607148.aspx)를 사용하여 **vmssstore1**이라는 저장소 계정을 만듭니다. 이 자습서의 뒷부분 단계를 위해 Azure PowerShell 창을 열어 둡니다.

## 2단계: 템플릿 만들기
Azure 리소스 관리자 템플릿을 사용하면 리소스와 관련 배포 매개 변수에 대한 JSON 설명을 사용하여 Azure 리소스를 함께 배포하고 관리할 수 있습니다.

1.	원하는 텍스트 편집기에서 C:\\VMSSTemplate.json 파일을 만들고 템플릿을 지원하도록 초기 JSON 구조를 추가합니다.

	```
{
		"$schema":"http://schema.management.azure.com/schemas/2014-04-01-preview/VM.json",
   "contentVersion": "1.0.0.0",
 "parameters": {
		}
		"variables": {
		}
		"resources": [
		]
	}
	```

2.	매개 변수는 항상 필요한 것은 아니지만 템플릿 관리를 용이하게 합니다. 템플릿 값을 지정하는 방법을 제공하고 값 형식, 기본값(필요한 경우) 및 매개 변수의 허용되는 값을 설명합니다.

	템플릿에 추가한 매개 변수 부모 요소 아래에 이러한 매개 변수를 추가합니다.

	- 규모 집합의 컴퓨터에 액세스하는 데 사용되는 jumpbox 가상 컴퓨터에 대한 이름입니다.
	- 템플릿이 저장된 저장소 계정의 이름입니다.
	- 규모 집합에서 처음으로 만들기 위한 가상 컴퓨터의 인스턴스 수입니다.
	- 가상 컴퓨터의 관리자 계정 이름 및 암호입니다.
	- 규모 집합의 가상 컴퓨터에서 사용되는 저장소 계정의 이름에 대한 접두사입니다.


	```
	"vmName": {
		"type": "string"
      },
	"vmSSName": {
		"type": "string"
    },
    "instanceCount": {
		"type": "string"
      },
    "adminUsername": {
		"type": "string"
    },
    "adminPassword": {
		"type": "securestring"
	},
	"storageAccountName": {
		"type": "string"
	},
	"vmssStoragePrefix": {
		"type": "string"
      }
	```


3.	템플릿에서 변수를 사용하여 자주 변경되는 값 또는 매개 변수 값의 조합에서 만들어야 하는 값을 지정할 수 있습니다.

	템플릿에 추가한 변수 부모 요소 아래에 이러한 변수를 추가합니다.

	- 네트워크 인터페이스에서 사용되는 DNS 이름입니다.
	- 규모 집합에 사용되는 가상 컴퓨터의 크기입니다. 가상 컴퓨터 크기에 대한 자세한 내용은 [가상 컴퓨터 크기](virtual-machines-size-specs.md)를 참조하세요.
	- 규모 집합의 가상 컴퓨터에서 실행할 운영 체제를 정의하기 위한 플랫폼 이미지 정보입니다. 이미지 선택에 대한 자세한 내용은 [Windows PowerShell 및 Azure CLI를 사용하여 Azure 가상 컴퓨터 탐색 및 선택](resource-groups-vm-searching.md)을 참조하세요.
	- 가상 네트워크 및 서브넷에 대한 IP 주소 이름 및 접두사입니다.
	- 가상 네트워크, 부하 분산 장치 및 네트워크 인터페이스의 이름 및 식별자입니다.
	- 규모 집합의 컴퓨터와 연결된 계정에 대한 저장소 계정 이름입니다.
	- 가상 컴퓨터에 설치되어 있는 진단 확장에 대한 설정입니다. 진단 확장에 대한 자세한 내용은 [Azure 리소스 관리자 템플릿을 사용한 모니터링 및 진단으로 Windows 가상 컴퓨터 만들기(영문)](virtual-machines-extensions-diagnostics-windows-template.md)를 참조하세요.

	```
	"dnsName1": "[concat(parameters('resourcePrefix'),'dn1')] ",
	"dnsName2": "[concat(parameters('resourcePrefix'),'dn2')] ",
	"vmSize": "Standard_A0",
	"imagePublisher": "MicrosoftWindowsServer",
	"imageOffer": "WindowsServer",
	"imageVersion": "2012-R2-Datacenter",
    "addressPrefix": "10.0.0.0/16",
	"subnetName": "Subnet",
    "subnetPrefix": "10.0.0.0/24",
	"publicIP1": "[concat(parameters('resourcePrefix'),'ip1')]",
	"publicIP2": "[concat(parameters('resourcePrefix'),'ip2')]",
	"loadBalancerName": "[concat(parameters('resourcePrefix'),'lb1')]",
	"virtualNetworkName": "[concat(parameters('resourcePrefix'),'vn1')]",
	"nicName1": "[concat(parameters('resourcePrefix'),'nc1')]",
	"nicName2": "[concat(parameters('resourcePrefix'),'nc2')]",
	"vnetID": "[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
	"publicIPAddressID1": "[resourceId('Microsoft.Network/publicIPAddresses',variables('publicIP1'))]",
	"publicIPAddressID2": "[resourceId('Microsoft.Network/publicIPAddresses',variables('publicIP2'))]",
	"lbID": "[resourceId('Microsoft.Network/loadBalancers',variables('loadBalancerName'))]",
	"nicId": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName2'))]",
	"frontEndIPConfigID": "[concat(variables('lbID'),'/frontendIPConfigurations/loadBalancerFrontEnd')]",
	"storageAccountType": "Standard_LRS",
	"storageAccountPrefix": [ "a", "g", "m", "s", "y" ],
	"diagnosticsStorageAccountName": "[concat('a', parameters('vmssStorageSuffix'))]",
	"accountid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/', resourceGroup().name,'/providers/','Microsoft.Storage/storageAccounts/', variables('diagnosticsStorageAccountName'))]",
	"wadlogs": "<WadCfg> <DiagnosticMonitorConfiguration overallQuotaInMB="4096" xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error"/> <WindowsEventLog scheduledTransferPeriod="PT1M" > <DataSource name="Application!*[System[(Level = 1 or Level = 2)]]" /> <DataSource name="Security!*[System[(Level = 1 or Level = 2)]]" /> <DataSource name="System!*[System[(Level = 1 or Level = 2)]]" /></WindowsEventLog>",
	"wadperfcounter": "<PerformanceCounters scheduledTransferPeriod="PT1M"><PerformanceCounterConfiguration counterSpecifier="\\Processor(_Total)\\% Processor Time" sampleRate="PT15S" unit="Percent"><annotation displayName="CPU utilization" locale="ko-KR"/></PerformanceCounterConfiguration>",
	"wadcfgxstart": "[concat(variables('wadlogs'),variables('wadperfcounter'),'<Metrics resourceId="')]",
	"wadmetricsresourceid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name ,'/providers/','Microsoft.Compute/virtualMachineScaleSets/',parameters('vmssName'))]",
	"wadcfgxend": "[concat('"><MetricAggregation scheduledTransferPeriod="PT1H"/><MetricAggregation scheduledTransferPeriod="PT1M"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>')]"
	```

4.	이 자습서에서는 다음 리소스 및 확장을 배포합니다.

 - Microsoft.Storage/storageAccounts
 - Microsoft.Network/virtualNetworks
 - Microsoft.Network/publicIPAddresses
 - Microsoft.Network/loadBalancers
 - Microsoft.Network/networkInterfaces
 - Microsoft.Compute/virtualMachines
 - Microsoft.Compute/virtualMachineScaleSets
 - Microsoft.Insights.VMDiagnosticsSettings
 - Microsoft.Insights/autoscaleSettings

	리소스 관리자 리소스에 대한 자세한 내용은 [Azure 리소스 관리자의 Azure 계산, 네트워크 및 저장소 공급자](virtual-machines-azurerm-versus-azuresm.md)를 참조하세요.

	템플릿에 추가된 리소스 부모 요소 아래에 저장소 계정 리소스를 추가합니다. 이 템플릿은 루프를 사용하여 운영 체제 디스크 및 진단 데이터가 저장되는 권장되는 5개의 저장소 계정을 만듭니다. 이 계정 집합은 현재 최대인 규모 집합에서 최대 100개의 가상 컴퓨터를 지원할 수 있습니다. 템플릿에 대한 매개 변수에서 제공하는 접미사와 결합된 변수에서 정의된 문자 지정자와 함께 각 저장소 계정 이름이 지정됩니다.

	```
    {
      "type": "Microsoft.Storage/storageAccounts",
		"name": "[concat(variables('storagePrefix')[copyIndex()], parameters('vmssStorageSuffix'))]",
		"apiVersion": "2015-05-01-preview",
      "copy": {
        "name": "storageLoop",
        "count": 5
      },
		"location": "[resourceGroup().location]",
      "properties": {
        "accountType": "[variables('storageAccountType')]"
      }
    },
	```

5.	가상 네트워크 리소스를 추가합니다. 자세한 내용은 [네트워크 리소스 공급자](../virtual-network/resource-groups-networking.md)를 참조하세요.

	```
    {
		"apiVersion": "2015-06-15",
      "type": "Microsoft.Network/virtualNetworks",
      "name": "[variables('virtualNetworkName')]",
		"location": "[resourceGroup().location]",
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
	```

6.	부하 분산 장치 및 네트워크 인터페이스에서 사용되는 공용 IP 주소 리소스를 추가합니다.

	```
	{
		"apiVersion": "2015-06-15",
		"type": "Microsoft.Network/publicIPAddresses",
		"name": "[variables('publicIP1')]",
		"location": "[resourceGroup().location]",
		"properties": {
			"publicIPAllocationMethod": "Dynamic",
			"dnsSettings": {
				"domainNameLabel": "[variables('dnsName1')]"
			}
		}
	},
	{
		"apiVersion": "2015-06-15",
		"type": "Microsoft.Network/publicIPAddresses",
		"name": "[variables('publicIP2')]",
		"location": "[resourceGroup().location]",
		"properties": {
			"publicIPAllocationMethod": "Dynamic",
			"dnsSettings": {
				"domainNameLabel": "[variables('dnsName2')]"
			}
		}
	},
	```

7.	규모 집합에서 사용되는 부하 분산 장치 리소스를 추가합니다. 자세한 내용은 [부하 분산 장치에 대한 Azure 리소스 관리자 지원](../load-balancer/oad-balancer-arm.md)을 참조하세요.

	```
    {
		"apiVersion": "2015-06-15",
		"name": "[variables('loadBalancerName')]",
		"type": "Microsoft.Network/loadBalancers",
		"location": "[resourceGroup().location]",
		"dependsOn": [
			"[concat('Microsoft.Network/publicIPAddresses/', variables('publicIP1'))]"
		],
		"properties": {
			"frontendIPConfigurations": [
				{
					"name": "loadBalancerFrontEnd",
					"properties": {
						"publicIPAddress": {
							"id": "[variables('publicIPAddressID1')]"
						}
					}
				}
			],
			"backendAddressPools": [
				{
					"name": "bepool1"
				}
			],
			"inboundNatPools": [
				{
					"name": "natpool1",
					"properties": {
						"frontendIPConfiguration": {
							"id": "[variables('frontEndIPConfigID')]"
						},
						"protocol": "tcp",
						"frontendPortRangeStart": 50000,
						"frontendPortRangeEnd": 50500,
						"backendPort": 3389
					}
				}
			]
		}
	},
	```

8.	Jumpbox 가상 컴퓨터에서 사용되는 네트워크 인터페이스 리소스를 추가합니다.


	```
	{
		"apiVersion": "2015-05-01-preview",
		"type": "Microsoft.Network/networkInterfaces",
		"name": "[variables('nicName1')]",
		"location": "[resourceGroup().location]",
		"dependsOn": [
			"[concat('Microsoft.Network/publicIPAddresses/', variables('publicIP2'))]",
			"[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
		],
		"properties": {
			"ipConfigurations": [
				{
					"name": "ipconfig1",
					"properties": {
						"privateIPAllocationMethod": "Dynamic",
						"publicIPAddress": {
							"id": "[resourceId('Microsoft.Network/publicIPAddresses', variables('publicIP2'))]"
						},
						"subnet": {
							"id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/virtualNetworks/',variables('virtualNetworkName'),'/subnets/',variables('subnetName'))]"
						}
					}
				}
			]
		}
	},
	```


9.	규모 집합과 동일한 네트워크에 가상 컴퓨터 리소스를 추가합니다. 가상 컴퓨터 규모 집합의 컴퓨터는 공용 IP 주소를 사용하여 직접 액세스할 수 없으므로 jumpbox 가상 컴퓨터는 규모 집합으로 동일한 가상 네트워크에 만들어지고 집합의 컴퓨터에 원격으로 액세스하는 데 사용됩니다.

	```
	{
		"apiVersion": "2015-05-01-preview",
		"type": "Microsoft.Compute/virtualMachines",
		"name": "[parameters('vmName')]",
		"location": "[resourceGroup().location]",
      "dependsOn": [
			"[concat('Microsoft.Network/networkInterfaces/', variables('nicName1'))]"
		],
		"properties": {
			"hardwareProfile": {
				"vmSize": "[variables('vmSize')]"
			},
			"osProfile": {
				"computername": "[parameters('vmName')]",
				"adminUsername": "[parameters('adminUsername')]",
				"adminPassword": "[parameters('adminPassword')]"
			},
			"storageProfile": {
				"imageReference": {
					"publisher": "[variables('imagePublisher')]",
					"offer": "[variables('imageOffer')]",
					"sku": "[variables('imageVersion')]",
					"version": "latest"
				},
				"osDisk": {
					"name": "davidmuos1",
					"vhd": {
						"uri":  "[concat('https://',parameters('storageAccountName'),'.blob.core.windows.net/vhds/',parameters('resourcePrefix'),'os1.vhd')]"
					},
					"caching": "ReadWrite",
					"createOption": "FromImage"        
				}
			},
			"networkProfile": {
				"networkInterfaces": [
					{
						"id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName1'))]"
					}
				]
			}
		}
	},
	```

10.	가상 컴퓨터 규모 집합 리소스를 추가하고 규모 집합의 모든 가상 컴퓨터에 설치되어 있는 진단 확장을 지정합니다. 이 리소스에 대한 설정 중 대부분은 가상 컴퓨터 리소스와 유사합니다. 주요 차이점은 다음과 같습니다.

	- **용량** - 규모 집합에 초기화해야 할 가상 컴퓨터의 수를 지정합니다. instanceCount 매개 변수의 값을 지정하여 이 값을 설정합니다.

	- **upgradePolicy** – 규모 집합의 가상 컴퓨터에 업데이트하는 방법을 지정합니다. 수동은 다시 배포하는 경우 새 가상 컴퓨터만 템플릿의 변경 내용에 영향을 받도록 지정합니다. 자동은 규모 집합의 모든 컴퓨터가 업데이트되고 다시 시작하도록 지정합니다.

	가상 컴퓨터 규모 집합은 모든 저장소 계정이 dependsOn 요소로 지정된 대로 만들어질 때까지 만들어지지 않습니다.

	```
	{
		"type": "Microsoft.Compute/virtualMachineScaleSets",
		"apiVersion": "2015-06-15",
		"name": "[parameters('vmSSName')]",
		"location": "[resourceGroup().location]",
		"dependsOn": [
			"[concat('Microsoft.Storage/storageAccounts/a', parameters('vmssStorageSuffix'))]",
			"[concat('Microsoft.Storage/storageAccounts/g', parameters('vmssStorageSuffix'))]",
			"[concat('Microsoft.Storage/storageAccounts/m', parameters('vmssStorageSuffix'))]",
			"[concat('Microsoft.Storage/storageAccounts/s', parameters('vmssStorageSuffix'))]",
			"[concat('Microsoft.Storage/storageAccounts/y', parameters('vmssStorageSuffix'))]",
			"[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]",
			"[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]"
      ],
      "sku": {
        "name": "[variables('vmSize')]",
        "tier": "Standard",
        "capacity": "[parameters('instanceCount')]"
      },
      "properties": {
         "upgradePolicy": {
         "mode": "Manual"
        },
        "virtualMachineProfile": {
          "storageProfile": {
            "osDisk": {
              "vhdContainers": [
							"[concat('https://a', parameters('vmssStorageSuffix'), '.blob.core.windows.net/vmss')]",
							"[concat('https://g', parameters('vmssStorageSuffix'), '.blob.core.windows.net/vmss')]",
							"[concat('https://m', parameters('vmssStorageSuffix'), '.blob.core.windows.net/vmss')]",
							"[concat('https://s', parameters('vmssStorageSuffix'), '.blob.core.windows.net/vmss')]",
							"[concat('https://y', parameters('vmssStorageSuffix'), '.blob.core.windows.net/vmss')]"
              ],
						"name": "vmssosdisk",
              "caching": "ReadOnly",
              "createOption": "FromImage"
            },
					"imageReference": {
						"publisher": "[variables('imagePublisher')]",
						"offer": "[variables('imageOffer')]",
						"sku": "[variables('imageVersion')]",
						"version": "latest"
					}
          },
          "osProfile": {
            "computerNamePrefix": "[parameters('vmSSName')]",
            "adminUsername": "[parameters('adminUsername')]",
            "adminPassword": "[parameters('adminPassword')]"
          },
          "networkProfile": {
            "networkInterfaceConfigurations": [
              {
							"name": "[variables('nicName2')]",
                "properties": {
                  "primary": "true",
                  "ipConfigurations": [
                    {
										"name": "ip1",
                      "properties": {
                        "subnet": {
												"id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/virtualNetworks/',variables('virtualNetworkName'),'/subnets/',variables('subnetName'))]"
											},
											"loadBalancerBackendAddressPools": [
												{
													"id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/loadBalancers/',variables('loadBalancerName'),'/backendAddressPools/bepool1')]"
												}
											],
											"loadBalancerInboundNatPools": [
												{
													"id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/loadBalancers/',variables('loadBalancerName'),'/inboundNatPools/natpool1')]"
												}
											]
										}
                        }
								]
                      }
                    }
                  ]
				},
				"extensionProfile": {
					"extensions": [
						{
							"name": "Microsoft.Insights.VMDiagnosticsSettings",
							"properties": {
								"publisher": "Microsoft.Azure.Diagnostics",
								"type": "IaaSDiagnostics",
								"typeHandlerVersion": "1.5",
								"autoUpgradeMinorVersion": true,
								"settings": {
									"xmlCfg": "[base64(concat(variables('wadcfgxstart'),variables('wadmetricsresourceid'),variables('wadcfgxend')))]",
									"storageAccount": "[variables('diagnosticsStorageAccountName')]"
								},
								"protectedSettings": {
									"storageAccountName": "[variables('diagnosticsStorageAccountName')]",
									"storageAccountKey": "[listkeys(variables('accountid'), '2015-05-01-preview').key1]",
									"storageAccountEndPoint": "https://core.windows.net"
								}
                }
              }
            ]
				}
			}
          }
	},
	```

11.	규모 집합이 집합의 컴퓨터에서 프로세서 사용량에 따라 조정하는 방법을 정의하는 autoscaleSettings 리소스를 추가합니다. 이 자습서의 경우 다음은 중요한 값입니다.

 - **metricName** - wadperfcounter 변수에 정의한 성능 카운터와 동일합니다. 해당 변수를 사용하여 진단 확장은 **Processor(\_Total)\\% Processor Time** 카운터를 수집합니다.
 - **metricResourceUri** - 가상 컴퓨터 규모 집합의 리소스 식별자입니다.
 - **timeGrain** – 수집되는 메트릭의 세분성입니다. 이 템플릿에서 1분으로 설정됩니다.
 - **statistic** – 자동 규모 조정 작업을 수용하기 위해 메트릭을 결합하는 방법을 결정합니다. 가능한 값은 평균, 최소, 최대입니다. 이 템플릿에서 규모 집합의 가상 컴퓨터 간의 총 평균 CPU 사용량을 살펴봅니다.
 - **timeWindow** – 인스턴스 데이터가 수집되는 시간 범위입니다. 5분에서 12시간 사이여야 합니다.
 - **timeAggregation** – 시간이 지남에 따라 수집된 데이터가 결합되어야 하는 방법을 결정합니다. 기본값은 평균입니다. 가능한 값은 평균, 최소, 최대, 마지막, 합계, 개수입니다.
 - **operator** – 메트릭 데이터와 임계값을 비교하는 데 사용되는 연산자입니다. 가능한 값은 Equals, NotEquals, GreaterThan, GreaterThanOrEqual, LessThan, LessThanOrEqual입니다.
 - **threshold** - 규모 조정 작업을 트리거하는 값입니다. 이 템플릿에서 집합의 컴퓨터 간 평균 CPU 사용량이 50%가 넘는 경우 컴퓨터가 규모 집합에 추가됩니다.
 - **direction** – 임계값이 달성되었을 때 수행되는 동작을 결정합니다. 가능한 값은 증가 또는 감소입니다. 이 템플릿에서 규모 집합의 가상 컴퓨터 수는 임계값이 정의된 시간 창에서 50%가 넘는 경우 증가합니다.
 - **type** – 발생되어야 하는 동작의 유형이며 ChangeCount로 설정되어 있어야 합니다.
 - **value** – 규모 집합에서 추가되거나 제거된 가상 컴퓨터의 수입니다. 이 값은 1 이상이어야 합니다. 기본값은 1입니다. 이 템플릿에서 규모 집합의 컴퓨터 수는 임계값에 도달한 경우 1씩 증가합니다.
 - **cooldown** – 다음 작업이 발생하기 전에 마지막 규모 조정 작업 이후에 대기 시간입니다. 1분에서 1주 사이여야 합니다.

	```
	{
		"type": "Microsoft.Insights/autoscaleSettings",
		"apiVersion": "2015-04-01",
		"name": "[concat(parameters('resourcePrefix'),'as1')]",
		"location": "[resourceGroup().location]",
		"dependsOn": [
			"[concat('Microsoft.Compute/virtualMachineScaleSets/',parameters('vmSSName'))]"
		],
		"properties": {
			"enabled": true,
			"name": "[concat(parameters('resourcePrefix'),'as1')]",
			"profiles": [
				{
					"name": "Profile1",
					"capacity": {
						"minimum": "1",
						"maximum": "10",
						"default": "1"
					},
					"rules": [
						{
							"metricTrigger": {
								"metricName": "\\Processor(_Total)\\% Processor Time",
								"metricNamespace": "",
								"metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Compute/virtualMachineScaleSets/',parameters('vmSSName'))]",
								"timeGrain": "PT1M",
								"statistic": "Average",
								"timeWindow": "PT5M",
								"timeAggregation": "Average",
								"operator": "GreaterThan",
								"threshold": 50.0
							},
							"scaleAction": {
								"direction": "Increase",
								"type": "ChangeCount",
								"value": "1",
								"cooldown": "PT5M"
							}
						}
					]
				}
			],
			"targetResourceUri": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/', resourceGroup().name,'/providers/Microsoft.Compute/virtualMachineScaleSets/',parameters('vmSSName'))]"
		}
	}
	```

12.	템플릿 파일을 저장합니다.    

## 3단계: 템플릿을 저장소에 업로드

1단계에서 만든 저장소 계정의 계정 이름 및 기본 키를 알고 있는 한 Microsoft Azure PowerShell 창에서 템플릿을 업로드할 수 있습니다.

1.	Microsoft Azure PowerShell 창에서 1단계에서 배포한 저장소 계정의 이름을 지정하는 변수를 설정합니다.

		$StorageAccountName = "vmssstore1"

2.	저장소 계정의 기본 키를 지정하는 변수를 설정합니다.

		$StorageAccountKey = "<primary-account-key>"

	Azure 포털에서 저장소 계정 리소스를 볼 때 열쇠 아이콘을 클릭하여 이 키를 가져올 수 있습니다.

3.	저장소 계정으로 작업의 유효성을 검사하는 데 사용되는 저장소 계정 컨텍스트 개체를 만듭니다.

		$ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

4.	사용자가 만든 템플릿을 저장할 수 있는 새 템플릿 컨테이너를 만듭니다.

		$ContainerName = "templates"
		New-AzureStorageContainer -Name $ContainerName -Context $ctx  -Permission Blob

5.	새 컨테이너에 템플릿 파일을 업로드합니다.

		$BlobName = "VMSSTemplate.json"
		$fileName = "C:" + $BlobName
		Set-AzureStorageBlobContent -File $fileName -Container $ContainerName -Blob  $BlobName -Context $ctx

## 4단계: 템플릿 배포

템플릿을 만들었으므로 리소스 배포를 시작할 수 있습니다. 이 명령을 사용하여 프로세스를 시작합니다.

		New-AzureRmResourceGroupDeployment -Name "vmss-testdeployment1" -ResourceGroupName "vmss-test1" -TemplateUri "https://vmssstore1.blob.core.windows.net/templates/VMSSTemplate.json"

Enter 키를 누르면 지정한 변수에 대한 값을 제공하라는 메시지가 표시됩니다. 다음 값을 제공합니다.

	vmName: vmssvm1
	vmSSName: vmsstest1
	instanceCount: 5
	adminUserName: vmadmin1
	adminPassword: vmadminpass1
	storageAccountName: vmssstore1
	resourcePrefix: vmsstest

모든 리소스가 성공적으로 배포되는 데 15분 정도가 소요됩니다.

>[AZURE.NOTE]포털의 기능을 사용하여 리소스를 배포할 수도 있습니다. 이렇게 하려면 이 링크를 사용합니다. https://portal.azure.com/#create/Microsoft.Template/uri/<link to VM Scale Set JSON template>

## 4단계: 리소스 모니터링

이러한 메서드를 사용하여 가상 컴퓨터 규모 집합에 대한 정보를 얻을 수 있습니다.

 - Azure 포털 - 포털을 사용하여 현재 제한된 양의 정보를 얻을 수 있습니다.
 - [Azure 리소스 탐색기](https://resources.azure.com/) - 규모 집합의 현재 상태를 탐색할 수 있는 최상의 도구입니다. 이 경로를 따르고 사용자가 만든 규모 집합의 인스턴스 보기가 표시되어야 합니다.

		subscriptions > {your subscription} > resourceGroups > vmss-test1 > providers > Microsoft.Compute > virtualMachineScaleSets > vmsstest1 > virtualMachines

 - Azure PowerShell - 이 명령을 사용하여 몇 가지 정보를 가져옵니다.

		Get-AzureRmResource -name vmsstest1 -ResourceGroupName vmss-test1 -ResourceType Microsoft.Compute/virtualMachineScaleSets -ApiVersion 2015-06-15

 - 다른 컴퓨터와 마찬가지로 jumpbox 가상 컴퓨터에 연결한 다음 개별 프로세스를 모니터링하도록 규모 집합의 가상 컴퓨터에 원격으로 액세스할 수 있습니다.

>[AZURE.NOTE]규모 집합에 대한 정보를 얻기 위해 전체 REST API를 [가상 컴퓨터 크기 규모 집합](https://msdn.microsoft.com/library/mt589023.aspx)에서 찾을 수 있습니다.

## 5단계: 리소스 제거

Azure에서 사용되는 리소스에 대한 요금이 부과되기 때문에, 더 이상 필요하지 않은 리소스를 항상 삭제하는 것이 좋습니다. 리소스 그룹에서 각 리소스를 개별적으로 삭제할 필요가 없습니다. 리소스 그룹을 삭제하면 모든 해당 리소스가 자동으로 삭제됩니다.

	Remove-AzureRmResourceGroup -Name vmss-test1

리소스 그룹을 유지하려는 경우 규모 집합만을 삭제할 수 있습니다.

	Remove-AzureRmResource -Name vmsstest1 -ResourceGroupName vmss-test1 -ApiVersion 2015-06-15 -ResourceType Microsoft.Compute/virtualMachineScaleSets

<!---HONumber=AcomDC_1203_2015-->