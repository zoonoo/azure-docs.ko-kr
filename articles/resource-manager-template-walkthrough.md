<properties
   pageTitle="리소스 관리자 템플릿 연습 | Microsoft Azure"
   description="기본 Azure IaaS 아키텍처를 프로비전하는 리소스 관리자 템플릿의 단계별 연습 과정입니다."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="navalev"
   manager=""
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/29/2016"
   ms.author="navale;tomfitz"/>
   
# 리소스 관리자 템플릿 연습

이 토픽에서는 리소스 관리자 템플릿을 만드는 단계를 안내합니다. [빠른 시작 갤러리](https://github.com/Azure/azure-quickstart-templates)에서 [부하 분산 장치 및 부하 분산 장치 템플릿을 포함하는 2개의 VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-2-vms-loadbalancer-lbrules)을 기반으로 템플릿을 만듭니다. 학습한 기법은 만들어야 하는 모든 템플릿에 적용할 수 있습니다.

일반적인 아키텍처를 살펴보겠습니다.

* 동일한 저장소 계정을 사용하는 두 개의 가상 컴퓨터는 동일한 가용성 집합에 있으며 가상 네트워크의 동일한 서브넷에 있습니다.
* 각 가상 컴퓨터에 대한 단일 NIC 및 VM IP 주소입니다.
* 포트 80에서 부하 분산 규칙이 있는 부하 분산 장치

![아키텍처](./media/resource-group-overview/arm_arch.png)

이 아키텍처를 Azure에 배포하고 리소스 관리자 템플릿을 사용하기로 결정했으면 다른 시간에 아키텍처를 쉽게 다시 배포할 수 있지만 해당 템플릿을 만드는 방법은 모릅니다. 이 토픽은 템플릿에 어떤 것을 넣을지 이해하는 데 도움이 됩니다.

템플릿을 만들 때 모든 유형의 편집기를 사용할 수 있습니다. Visual Studio는 템플릿 개발을 간소화하는 도구를 제공하지만 이 자습서를 완료하는 데 Visual Studio가 필요하지 않습니다. Visual Studio를 사용하여 웹 앱 및 SQL 데이터베이스 배포를 만드는 자습서는 [Visual Studio를 통해 Azure 리소스 그룹 만들기 및 배포](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)를 참조하세요.

## 리소스 관리자 템플릿 만들기

템플릿은 배포할 리소스를 모두 정의하는 JSON 파일입니다. 또한 템플릿을 통해 배포 중에 지정된 매개 변수, 다른 값 및 식에서 생성된 변수 및 배포의 출력을 정의할 수 있습니다.

가장 간단한 템플릿으로 시작해 보겠습니다.

    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {  },
      "variables": {  },
      "resources": [  ],
      "outputs": {  }
    }

이 파일을 **azuredeploy.json**으로 저장합니다.

먼저, **resources** 섹션을 주로 살펴보고 이 토픽의 뒷부분에서 **parameters** 및 **variables**를 살펴봅니다.

## 저장소 계정
가상 컴퓨터에서 사용할 저장소 계정을 정의합니다. **resources** 섹션에서 다음과 같이 저장소 계정을 정의하는 개체를 추가합니다.

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[parameters('storageAccountName')]",
    "apiVersion": "2015-06-15",
    "location": "[resourceGroup().location]",
    "properties": {
      "accountType": "Standard_LRS"
    }
  }
]
```

이러한 속성 및 값은 어디에서 가져올지 궁금할 수 있습니다. **type**, **name**, **apiVersion** 및 **location** 속성은 모든 리소스 유형에서 사용 가능한 표준 요소입니다. [리소스](../resource-group-authoring-templates/#resources)에서 공통 요소에 대해 학습할 수 있습니다. **name**은 배포 중에 전달하는 매개 변수 값으로 설정합니다. **location**은 리소스 그룹에서 사용하는 위치로 설정합니다. **type** 및 **apiVersion**을 결정하는 방법은 아래 섹션에서 살펴보겠습니다.

**properties** 섹션에는 특정 리소스 유형에 고유한 모든 속성을 포함합니다. 이 섹션에서 지정한 값은 해당 리소스 유형을 만들기 위한 REST API의 PUT 작업과 정확히 일치합니다. 저장소 계정을 만들 때 **accountType**을 제공해야 합니다. [저장소 계정 만들기를 위한 REST API](https://msdn.microsoft.com/library/azure/mt163564.aspx)를 보면 REST 작업의 properties 섹션에도 **accountType** 속성이 포함되며 허용되는 값이 문서화되어 있습니다. 이 예에서는 계정 유형이 **Standard\_LRS**로 설정되지만 다른 값을 지정하거나 사용자가 매개 변수로 계정 유형을 전달하도록 지정할 수 있습니다.

## 가용성 집합
저장소 계정을 정의한 후 가상 컴퓨터에 대한 가용성 집합을 추가합니다. 이 경우 추가 속성이 필요하지 않으므로 정의가 비교적 간단합니다. 업데이트 도메인 수 및 장애 도메인 수 값을 정의하려는 경우 전체 속성 섹션은 [가용성 집합 만들기를 위한 REST API](https://msdn.microsoft.com/library/azure/mt163607.aspx)를 참조하세요.

```json
{
   "type": "Microsoft.Compute/availabilitySets",
   "name": "[variables('availabilitySetName')]",
   "apiVersion": "2015-06-15",
   "location": "[resourceGroup().location]",
   "properties": {}
}
```

**name**은 변수의 값으로 설정됨에 유의합니다. 이 템플릿의 경우 일부 서로 다른 위치에 가용성 집합 이름이 필요합니다. 해당 값을 한 번 정의하여 여러 위치에서 사용하여 템플릿을 보다 쉽게 유지 관리할 수 있습니다.

**type**에 대해 지정한 값은 리소스 공급자와 리소스 유형을 모두 포함합니다. 가용성 집합의 경우 리소스 공급자는 **Microsoft.Compute**이고 리소스 유형은 **availabilitySets**입니다. 다음 PowerShell 명령을 실행하여 사용 가능한 리소스 공급자의 목록을 얻을 수 있습니다.

    PS C:\> Get-AzureRmResourceProvider -ListAvailable

또는 Azure CLI를 사용하는 경우 다음 명령을 실행할 수 있습니다.

    azure provider list

이 토픽에서는 저장소 계정, 가상 컴퓨터 및 가상 네트워킹으로 만들고 있으므로 다음으로 작업하게 됩니다.

- Microsoft.Storage
- Microsoft.Compute
- Microsoft.Network

특정 공급자에 대한 리소스 유형을 보려면 다음 PowerShell 명령을 실행합니다.

    PS C:\> (Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute).ResourceTypes

또는 Azure CLI의 경우 다음 명령은 JSON 형식으로 사용 가능한 유형을 반환하며 이를 파일로 저장합니다.

    azure provider show Microsoft.Compute --json > c:\temp.json

**Microsoft.Compute** 내에 있는 유형 중 하나로 **availabilitySets**가 표시됩니다. 이 유형의 전체 이름은 **Microsoft.Compute/availabilitySets**입니다. 템플릿에서 모든 리소스에 대한 리소스 유형 이름을 확인할 수 있습니다.

## 공용 IP
공용 IP 주소를 정의합니다. 다시, 설정할 속성에 대해 [공용 IP 주소에 대한 REST API](https://msdn.microsoft.com/library/azure/mt163590.aspx)를 살펴보세요.

```json
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/publicIPAddresses",
  "name": "[parameters('publicIPAddressName')]",
  "location": "[resourceGroup().location]",
  "properties": {
    "publicIPAllocationMethod": "Dynamic",
    "dnsSettings": {
      "domainNameLabel": "[parameters('dnsNameforLBIP')]"
    }
  }
}
```

할당 메소드는 **Dynamic**으로 설정되지만 필요한 값으로 설정하거나 매개 변수 값을 허용하도록 설정할 수 있습니다. 도메인 이름 레이블에 대한 값에 전달하도록 템플릿의 사용자를 설정했습니다.

이제 **apiVersion**을 결정하는 방법을 살펴보겠습니다. 사용자가 지정하는 값은 리소스를 만들 때 사용할 REST API의 버전과 일치합니다. 따라서 해당 리소스 유형에 대한 REST API 설명서를 살펴볼 수 있습니다. 또는 특정 유형에 대해 다음 PowerShell 명령을 실행할 수 있습니다.

    PS C:\> ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Network).ResourceTypes | Where-Object ResourceTypeName -eq publicIPAddresses).ApiVersions

그러면 다음 값이 반환됩니다.

    2015-06-15
    2015-05-01-preview
    2014-12-01-preview

Azure CLI 및 API 버전을 보려면 이전에 본 것처럼 동일한 **azure provider show** 명령을 실행합니다.

새 템플릿을 만들 때 최신 API 버전을 선택합니다.

## 가상 네트워크 및 서브넷
하나의 서브넷이 있는 가상 네트워크를 만듭니다. 설정할 모든 속성은 [가상 네트워크에 대한 REST API](https://msdn.microsoft.com/library/azure/mt163661.aspx)를 확인합니다.

```json
{
   "apiVersion": "2015-06-15",
   "type": "Microsoft.Network/virtualNetworks",
   "name": "[parameters('vnetName')]",
   "location": "[resourceGroup().location]",
   "properties": {
     "addressSpace": {
       "addressPrefixes": [
         "10.0.0.0/16"
       ]
     },
     "subnets": [
       {
         "name": "[variables('subnetName')]",
         "properties": {
           "addressPrefix": "10.0.0.0/24"
         }
       }
     ]
   }
}
```

## 부하 분산 장치
이제 외부 연결 부하 분산 장치를 만듭니다. 이 부하 분산 장치는 공용 IP 주소를 사용하므로 **dependsOn** 섹션에서 공용 IP 주소에 대한 종속성을 선언해야 합니다. 즉, 부하 분산 장치는 공용 IP 주소가 배포를 완료할 때까지 배포되지 않습니다. 이 종속성을 정의하지 않으면 리소스 관리자가 병렬로 리소스를 배포하려고 하므로 오류가 수신되고 부하 분산 장치를 아직 존재하지 않는 공용 IP 주소로 설정하려고 합니다.

이 리소스 정의에 포트 80에서 tcp 프로브를 포함하는 부하 분산 규칙, RDP에서 VM에 대한 인바운드 NAT 규칙 몇 가지, 백 엔드 주소 풀을 만듭니다. 모든 속성은 [부하 분산 장치에 대한 REST API](https://msdn.microsoft.com/library/azure/mt163574.aspx)를 확인하세요.

```json
{
   "apiVersion": "2015-06-15",
   "name": "[parameters('lbName')]",
   "type": "Microsoft.Network/loadBalancers",
   "location": "[resourceGroup().location]",
   "dependsOn": [
     "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]"
   ],
   "properties": {
     "frontendIPConfigurations": [
       {
         "name": "LoadBalancerFrontEnd",
         "properties": {
           "publicIPAddress": {
             "id": "[variables('publicIPAddressID')]"
           }
         }
       }
     ],
     "backendAddressPools": [
       {
         "name": "BackendPool1"
       }
     ],
     "inboundNatRules": [
       {
         "name": "RDP-VM0",
         "properties": {
           "frontendIPConfiguration": {
             "id": "[variables('frontEndIPConfigID')]"
           },
           "protocol": "tcp",
           "frontendPort": 50001,
           "backendPort": 3389,
           "enableFloatingIP": false
         }
       },
       {
         "name": "RDP-VM1",
         "properties": {
           "frontendIPConfiguration": {
             "id": "[variables('frontEndIPConfigID')]"
           },
           "protocol": "tcp",
           "frontendPort": 50002,
           "backendPort": 3389,
           "enableFloatingIP": false
         }
       }
     ],
     "loadBalancingRules": [
       {
         "name": "LBRule",
         "properties": {
           "frontendIPConfiguration": {
             "id": "[variables('frontEndIPConfigID')]"
           },
           "backendAddressPool": {
             "id": "[variables('lbPoolID')]"
           },
           "protocol": "tcp",
           "frontendPort": 80,
           "backendPort": 80,
           "enableFloatingIP": false,
           "idleTimeoutInMinutes": 5,
           "probe": {
             "id": "[variables('lbProbeID')]"
           }
         }
       }
     ],
     "probes": [
       {
         "name": "tcpProbe",
         "properties": {
           "protocol": "tcp",
           "port": 80,
           "intervalInSeconds": 5,
           "numberOfProbes": 2
         }
       }
     ]
   }
}
```

## 네트워크 인터페이스
각 VM에 대해 하나씩 2개의 네트워크 인터페이스를 만듭니다. 네트워크 인터페이스에 대해 중복된 항목을 포함하는 대신, [copyIndex() 함수](resource-group-create-multiple.md)를 사용하여 복사 루프(nicLoop라고도 함)를 반복하고 `numberOfInstances` 변수에 정의된 대로 여러 네트워크 인터페이스를 만들 수 있습니다. 네트워크 인터페이스는 가상 네트워크 및 부하 분산 장치 생성에 따라 달라집니다. 가상 네트워크 만들기에 정의된 서브넷과 부하 분산 장치 ID를 사용하여 부하 분산 장치 주소 풀 및 인바운드 NAT 규칙을 구성합니다. 모든 속성은 [네트워크 인터페이스에 대한 REST API](https://msdn.microsoft.com/library/azure/mt163668.aspx)를 확인하세요.

```json
{
   "apiVersion": "2015-06-15",
   "type": "Microsoft.Network/networkInterfaces",
   "name": "[concat(parameters('nicNamePrefix'), copyindex())]",
   "location": "[resourceGroup().location]",
   "copy": {
     "name": "nicLoop",
     "count": "[variables('numberOfInstances')]"
   },
   "dependsOn": [
     "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]",
     "[concat('Microsoft.Network/loadBalancers/', parameters('lbName'))]"
   ],
   "properties": {
     "ipConfigurations": [
       {
         "name": "ipconfig1",
         "properties": {
           "privateIPAllocationMethod": "Dynamic",
           "subnet": {
             "id": "[variables('subnetRef')]"
           },
           "loadBalancerBackendAddressPools": [
             {
               "id": "[concat(variables('lbID'), '/backendAddressPools/BackendPool1')]"
             }
           ],
           "loadBalancerInboundNatRules": [
             {
               "id": "[concat(variables('lbID'),'/inboundNatRules/RDP-VM', copyindex())]"
             }
           ]
         }
       }
     ]
   }
}
```

## 가상 컴퓨터
[네트워크 인터페이스](#network-interface) 생성 시 수행한 것처럼 copyIndex() 함수를 사용하여 2개의 가상 컴퓨터를 만듭니다. VM 생성은 저장소 계정, 네트워크 인터페이스 및 가용성 집합에 따라 달라집니다. 이 VM은 `storageProfile` 속성에 정의된 대로 마켓플레이스 이미지에서 생성되며 `imageReference`은 이미지 게시자, 제품, SKU 및 버전을 정의하는 데 사용됩니다. 마지막으로, VM에 대해 진단을 사용하도록 진단 프로필이 구성됩니다.

마켓플레이스 이미지에 대한 관련 속성을 찾으려면 [Linux 가상 컴퓨터 이미지 선택](./virtual-machines/virtual-machines-linux-cli-ps-findimage.md) 또는 [Windows 가상 컴퓨터 이미지 선택](./virtual-machines/virtual-machines-windows-cli-ps-findimage.md) 문서를 따릅니다. 타사 공급업체에서 게시한 이미지의 경우 `plan`이라는 이름의 다른 속성을 지정해야 합니다. 예제는 빠른 시작 갤러리의 [이 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/checkpoint-single-nic)에서 확인할 수 있습니다.


```json
{
   "apiVersion": "2015-06-15",
   "type": "Microsoft.Compute/virtualMachines",
   "name": "[concat(parameters('vmNamePrefix'), copyindex())]",
   "copy": {
     "name": "virtualMachineLoop",
     "count": "[variables('numberOfInstances')]"
   },
   "location": "[resourceGroup().location]",
   "dependsOn": [
     "[concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName'))]",
     "[concat('Microsoft.Network/networkInterfaces/', parameters('nicNamePrefix'), copyindex())]",
     "[concat('Microsoft.Compute/availabilitySets/', variables('availabilitySetName'))]"
   ],
   "properties": {
     "availabilitySet": {
       "id": "[resourceId('Microsoft.Compute/availabilitySets',variables('availabilitySetName'))]"
     },
     "hardwareProfile": {
       "vmSize": "[parameters('vmSize')]"
     },
     "osProfile": {
       "computerName": "[concat(parameters('vmNamePrefix'), copyIndex())]",
       "adminUsername": "[parameters('adminUsername')]",
       "adminPassword": "[parameters('adminPassword')]"
     },
     "storageProfile": {
       "imageReference": {
         "publisher": "[parameters('imagePublisher')]",
         "offer": "[parameters('imageOffer')]",
         "sku": "[parameters('imageSKU')]",
         "version": "latest"
       },
       "osDisk": {
         "name": "osdisk",
         "vhd": {
           "uri": "[concat('http://',parameters('storageAccountName'),'.blob.core.windows.net/vhds/','osdisk', copyindex(), '.vhd')]"
         },
         "caching": "ReadWrite",
         "createOption": "FromImage"
       }
     },
     "networkProfile": {
       "networkInterfaces": [
         {
           "id": "[resourceId('Microsoft.Network/networkInterfaces',concat(parameters('nicNamePrefix'),copyindex()))]"
         }
       ]
     },
     "diagnosticsProfile": {
       "bootDiagnostics": {
          "enabled": "true",
          "storageUri": "[concat('http://',parameters('storageAccountName'),'.blob.core.windows.net')]"
       }
     }
}
```

템플릿에 대한 리소스 정의를 완료했습니다.

## 매개 변수

매개 변수 섹션에서 템플릿을 배포할 때 지정할 수 있는 값을 정의합니다. 배포 중에 변동된다고 생각되는 값만 매개 변수를 정의합니다. 배포 중에 값이 제공되지 않는 경우 사용된 매개 변수의 기본 값을 제공할 수 있습니다. **imageSKU** 매개 변수에 대해 표시된 것처럼 허용되는 값도 정의할 수 있습니다.

```
"parameters": {
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of storage account"
      }
    },
    "adminUsername": {
      "type": "string",
      "metadata": {
        "description": "Admin username"
      }
    },
    "adminPassword": {
      "type": "securestring",
      "metadata": {
        "description": "Admin password"
      }
    },
    "dnsNameforLBIP": {
      "type": "string",
      "metadata": {
        "description": "DNS for Load Balancer IP"
      }
    },
    "vmNamePrefix": {
      "type": "string",
      "defaultValue": "myVM",
      "metadata": {
        "description": "Prefix to use for VM names"
      }
    },
    "imagePublisher": {
      "type": "string",
      "defaultValue": "MicrosoftWindowsServer",
      "metadata": {
        "description": "Image Publisher"
      }
    },
    "imageOffer": {
      "type": "string",
      "defaultValue": "WindowsServer",
      "metadata": {
        "description": "Image Offer"
      }
    },
    "imageSKU": {
      "type": "string",
      "defaultValue": "2012-R2-Datacenter",
      "allowedValues": [
        "2008-R2-SP1",
        "2012-Datacenter",
        "2012-R2-Datacenter"
      ],
      "metadata": {
        "description": "Image SKU"
      }
    },
    "lbName": {
      "type": "string",
      "defaultValue": "myLB",
      "metadata": {
        "description": "Load Balancer name"
      }
    },
    "nicNamePrefix": {
      "type": "string",
      "defaultValue": "nic",
      "metadata": {
        "description": "Network Interface name prefix"
      }
    },
    "publicIPAddressName": {
      "type": "string",
      "defaultValue": "myPublicIP",
      "metadata": {
        "description": "Public IP Name"
      }
    },
    "vnetName": {
      "type": "string",
      "defaultValue": "myVNET",
      "metadata": {
        "description": "VNET name"
      }
    },
    "vmSize": {
      "type": "string",
      "defaultValue": "Standard_D1",
      "metadata": {
        "description": "Size of the VM"
      }
    }
  }
```

## 변수

변수 섹션에는 템플릿에서 두 곳 이상 사용되는 값 또는 다른 식 이나 변수에서 생성되는 값을 정의할 수 있습니다. 변수는 템플릿의 구문을 간소화하는 데 자주 사용됩니다.

```
"variables": {
    "availabilitySetName": "myAvSet",
    "subnetName": "Subnet-1",
    "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('vnetName'))]",
    "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables ('subnetName'))]",
    "publicIPAddressID": "[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]",
    "numberOfInstances": 2,
    "lbID": "[resourceId('Microsoft.Network/loadBalancers',parameters('lbName'))]",
    "frontEndIPConfigID": "[concat(variables('lbID'),'/frontendIPConfigurations/LoadBalancerFrontEnd')]",
    "lbPoolID": "[concat(variables('lbID'),'/backendAddressPools/BackendPool1')]",
    "lbProbeID": "[concat(variables('lbID'),'/probes/tcpProbe')]"
  }
```



## 다음 단계

템플릿을 만들었고 이제 배포할 준비가 되었습니다.

- 템플릿 구조에 대해 자세히 알아보려면 [Azure Resource Manager 템플릿 작성하기](resource-group-authoring-templates.md)를 참조하세요.
- 템플릿 배포에 대해 자세히 알아보려면 [Azure Resource Manager 템플릿을 사용하여 리소스 그룹 배포](resource-group-template-deploy.md)를 참조하세요.

<!---HONumber=AcomDC_0330_2016-->