---
title: "Azure Service Fabric의 네트워킹 패턴 | Microsoft Docs"
description: "Service Fabric에 대한 일반적인 네트워킹 패턴과 Azure 네트워킹 기능을 사용하여 클러스터를 만드는 방법을 설명합니다."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/27/2017
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: 030114fa1ea9b76c0ed48baeffb8859260fc8e52
ms.openlocfilehash: 78012ae1552c01690b0ad5b1285173ef9faf12bc
ms.lasthandoff: 03/01/2017


---
# <a name="service-fabric-networking-patterns"></a>Service Fabric 네트워킹 패턴
Service Fabric 클러스터를 만들 때 확인된 일반적인 질문 중 하나는 다양한 Azure 네트워킹 기능에 클러스터를 통합하는 방법에 대한 것입니다.  이 문서에서는 다음과 같은 기능을 사용하여 클러스터를 만드는 방법을 보여 줍니다.

- [기존 가상 네트워크/서브넷](#existingvnet)
- [고정 공용 IP 주소](#staticpublicip)
- [내부 전용 부하 분산 장치](#internallb)
- [내부 + 외부 부하 분산 장치](#internalexternallb)

염두에 두어야 할 주요 개념은 Service Fabric이 표준 가상 컴퓨터 크기 집합에서 실행되므로 가상 컴퓨터 크기 집합에서 사용할 수 있는 모든 기능을 Service Fabric 클러스터에서도 사용할 수 있습니다. Resource Manager 템플릿의 네트워킹 부분은 동일합니다.  또한 기존 Vnet에 배포한 경우 ExpressRoute, VPN 게이트웨이, NSG(네트워크 보안 그룹) 및 VNet 피어링 등의 다른 네트워킹 기능을 통합하기가 쉽습니다.

유일한 Service Fabric 관련 부분은 [Azure Portal](https://portal.azure.com)이 내부적으로 SFRP(Service Fabric 리소스 공급자)를 사용하여 노드 및 응용 프로그램에 대한 정보를 얻기 위해 클러스터에 호출합니다.  SFRP는 관리 끝점에서 [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)가 클러스터를 관리하는 데 사용하는 HTTP 게이트웨이 포트(기본적으로&19080;)에 대해 공개적으로 액세스 가능한 인바운드 액세스 권한이 필요합니다. 또한 이 포트는 Service Fabric 리소스 공급자가 Azure Portal에 표시하기 위해 클러스터에 대한 정보를 쿼리하는 데도 사용합니다.  SFRP에서 이 포트에 액세스할 수 없는 경우 관리 포털에 '노드를 찾을 수 없음'과 같은 메시지가 나타나고 노드 및 응용 프로그램 목록이 빈 상태로 나타납니다.  Azure Portal을 통해 클러스터를 표시하려는 경우 부하 분산 장치는 공용 IP 주소를 노출해야 하고 NSG는 들어오는 19080 트래픽을 허용해야 합니다.  이러한 요구 사항을 충족하지 않는 경우 Azure Portal에 클러스터의 현재 상태가 표시되지 않습니다.  그렇지 않으면 클러스터는 영향을 받지 않으며 [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)를 사용하여 네트워킹 요구 사항에 따라 허용 가능한 제한이 될 수 있는 현재 상태를 가져올 수 있습니다.  이것은 추후 업데이트에서 해결될 일시적인 제한 요인으로, 공개적으로 클러스터에 액세스할 때는 항상 관리 포털 기능이 손실되는 상황을 나타냅니다.

## <a name="templates"></a>템플릿

모든 템플릿은 [여기](https://msdnshared.blob.core.windows.net/media/2016/10/SF_Networking_Templates.zip)에서 찾을 수 있습니다. 다음 powershell 명령을 사용하여 템플릿을 있는 그대로 배포할 수 있습니다.  기존 VNet 템플릿 또는 정적 공용 IP 템플릿을 배포하는 경우 먼저 [초기 설치](#initialsetup) 섹션을 수행해야 합니다.

<a id="initialsetup"></a>
## <a name="initial-setup"></a>초기 설치

### <a name="existing-virtual-network"></a>기존 가상 네트워크

서브넷 이름이 'default'인 리소스 그룹 *ExistingRG*에 'ExistingRG-vnet'이라는 기존 가상 네트워크로 시작합니다.  이러한 리소스는 Azure Portal을 사용하여 표준 가상 컴퓨터를 만들 때 만들어진 기본 리소스입니다.  또한 가상 컴퓨터를 만들지 않고 VNet 및 서브넷을 만들 수도 있습니다. 그렇지만 기존 VNet에 클러스터를 추가하는 주요 목적은 다른 VM에 대한 네트워크 연결을 제공하는 것이므로 VM을 만드는 작업은 VNet이 일반적으로 사용되는 방법을 구체적으로 보여 주는 예제가 됩니다.  Service Fabric 클러스터가 공용 IP 주소 없이 내부 부하 분산 장치만 사용하는 경우 해당 공용 IP를 갖는 VM은 점프 서버로도 사용할 수 있습니다.

### <a name="static-public-ip-address"></a>고정 공용 IP 주소

고정 공용 IP 주소는 할당된 VM에서 별도로 관리되는 전용 리소스이므로 전용 네트워킹 리소스 그룹에서 프로비전됩니다(Service Fabric 클러스터 리소스 그룹 자체 내부가 아님).  Azure Portal 또는 Powershell을 통해 동일한 *ExistingRG* 리소스 그룹에 이름이 'staticIP1'인 고정 공용 IP 주소를 만듭니다.

```powershell
PS C:\Users\user> New-AzureRmPublicIpAddress -Name staticIP1 -ResourceGroupName ExistingRG -Location westus -AllocationMethod Static -DomainNameLabel sfnetworking

Name                     : staticIP1
ResourceGroupName        : ExistingRG
Location                 : westus
Id                       : /subscriptions/1237f4d2-3dce-1236-ad95-123f764e7123/resourceGroups/ExistingRG/providers/Microsoft.Network/publicIPAddresses/staticIP1
Etag                     : W/"fc8b0c77-1f84-455d-9930-0404ebba1b64"
ResourceGuid             : 77c26c06-c0ae-496c-9231-b1a114e08824
ProvisioningState        : Succeeded
Tags                     :
PublicIpAllocationMethod : Static
IpAddress                : 40.83.182.110
PublicIpAddressVersion   : IPv4
IdleTimeoutInMinutes     : 4
IpConfiguration          : null
DnsSettings              : {
                             "DomainNameLabel": "sfnetworking",
                             "Fqdn": "sfnetworking.westus.cloudapp.azure.com"
                           }
```

### <a name="service-fabric-template"></a>Service Fabric 템플릿

표준 포털 마법사를 사용하여 클러스터를 만들기 전에 포털에서 다운로드할 수 있는 Service Fabric 템플릿 .json을 사용합니다. [템플릿 갤러리](https://azure.microsoft.com/en-us/documentation/templates/?term=service+fabric)에서 [Five node Service Fabric cluster](https://azure.microsoft.com/en-us/documentation/templates/service-fabric-unsecure-cluster-5-node-1-nodetype/)와 같은 템플릿 중 하나를 사용할 수도 있습니다.

<a id="existingvnet"></a>
## <a name="existing-virtual-networksubnet"></a>기존 가상 네트워크/서브넷

[2016년 1월 24일: [https://github.com/gbowerman/azure-myriad/tree/master/existing-vnet](https://github.com/gbowerman/azure-myriad/tree/master/existing-vnet)에는 Service Fabric 범위에 속하지 않는 또 다른 이 작업 샘플이 제공됩니다.]

1. 서브넷 매개 변수를 기존 서브넷의 이름으로 변경하고 기존 VNet을 참조하는 두 개의 새 매개 변수를 추가합니다.

    ```
        "subnet0Name": {
                "type": "string",
                "defaultValue": "default"
            },
            "existingVNetRGName": {
                "type": "string",
                "defaultValue": "ExistingRG"
            },

            "existingVNetName": {
                "type": "string",
                "defaultValue": "ExistingRG-vnet"
            },
            /*
            "subnet0Name": {
                "type": "string",
                "defaultValue": "Subnet-0"
            },
            "subnet0Prefix": {
                "type": "string",
                "defaultValue": "10.0.0.0/24"
            },*/
    ```


2. *vnetID* 변수를 기존 VNet을 가리키도록 변경합니다.

    ```
            /*old "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",*/
            "vnetID": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', parameters('existingVNetRGName'), '/providers/Microsoft.Network/virtualNetworks/', parameters('existingVNetName'))]",
    ```

3. Azure에 새 VNet을 만들지 않도록 Resources에서 *Microsoft.Network/virtualNetworks*를 제거합니다.

    ```
    /*{
    "apiVersion": "[variables('vNetApiVersion')]",
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[parameters('virtualNetworkName')]",
    "location": "[parameters('computeLocation')]",
    "properities": {
        "addressSpace": {
            "addressPrefixes": [
                "[parameters('addressPrefix')]"
            ]
        },
        "subnets": [
            {
                "name": "[parameters('subnet0Name')]",
                "properties": {
                    "addressPrefix": "[parameters('subnet0Prefix')]"
                }
            }
        ]
    },
    "tags": {
        "resourceType": "Service Fabric",
        "clusterName": "[parameters('clusterName')]"
    }
    },*/
    ```

4. 새 VNet을 만드는 데만 의존하지 않도록 *Microsoft.Compute/virtualMachineScaleSets*의 *dependsOn* 특성에서 VNet을 주석 처리합니다.

    ```
    "apiVersion": "[variables('vmssApiVersion')]",
    "type": "Microsoft.Computer/virtualMachineScaleSets",
    "name": "[parameters('vmNodeType0Name')]",
    "location": "[parameters('computeLocation')]",
    "dependsOn": [
        /*"[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]",
        */
        "[Concat('Microsoft.Storage/storageAccounts/', variables('uniqueStringArray0')[0])]",

    ```

5. 템플릿 배포

    ```powershell
    New-AzureRmResourceGroup -Name sfnetworkingexistingvnet -Location westus
    New-AzureRmResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkingexistingvnet -TemplateFile C:\SFSamples\Final\template\_existingvnet.json
    ```

    배포 후에 가상 네트워크는 새 크기 집합 VM을 포함하고, 가상 컴퓨터 크기 집합 노드 형식은 기존 VNet 및 서브넷을 나타내야 합니다.  VNet에 이미 있던 기존 VM으로 RDP를 수행하고 새 크기 집합 VM을 Ping할 수도 있습니다.

    ```
    C:>\Users\users>ping 10.0.0.5 -n 1
    C:>\Users\users>ping NOde1000000 -n 1
    ```

<a id="staticpublicip"></a>
## <a name="static-public-ip-address"></a>고정 공용 IP 주소

1. 기존 고정 IP 리소스 그룹의 이름 및 FQDN에 대한 매개 변수를 추가합니다.

    ```
    "existingStaticIPResourceGroup": {
                "type": "string"
            },
            "existingStaticIPName": {
                "type": "string"
            },
            "existingStaticIPDnsFQDN": {
                "type": "string"
    }
    ```

2. 고정 IP에 이미 있으므로 *dnsName* 매개 변수를 제거합니다.

    ```
    /*
    "dnsName": {
        "type": "string"
    }, 
    */
    ```

3. 기존 정적 IP를 참조하는 변수를 추가합니다.

    ```
    "existingStaticIP": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', parameters('existingStaticIPResourceGroup'), '/providers/Microsoft.Network/publicIPAddresses/', parameters('existingStaticIPName'))]",
    ```

4. Azure에 새 IP 주소를 만들지 않도록 *Resources*에서 *Microsoft.Network/publicIPAddresses*를 제거합니다.

    ```
    /*
    {
        "apiVersion": "[variables('publicIPApiVersion')]",
        "type": "Microsoft.Network/publicIPAddresses",
        "name": "[concat(parameters('lbIPName'),)'-', '0')]",
        "location": "[parameters('computeLocation')]",
        "properties": {
            "dnsSettings": {
                "domainNameLabel": "[parameters('dnsName')]"
            },
            "publicIPAllocationMethod": "Dynamic"        
        },
        "tags": {
            "resourceType": "Service Fabric",
            "clusterName": "[parameters('clusterName')]"
        }
    }, */
    ```

5. 새 IP 주소를 만드는 데만 의존하지 않도록 *Microsoft.Network/loadBalancers*의 *dependsOn* 특성에서 IP 주소를 주석 처리합니다.

    ```
    "apiVersion": "[variables('lbIPApiVersion')]",
    "type": "Microsoft.Network/loadBalancers",
    "name": "[concat('LB', '-', parameters('clusterName'), '-', parameters('vmNodeType0Name'))]",
    "location": "[parameters('computeLocation')]",
    /*
    "dependsOn": [
        "[concat('Microsoft.Network/publicIPAddresses/', concat(parameters('lbIPName'), '-', '0'))]"
    ], */
    "properties": {
    ```

6. *Microsoft.Network/loadBalancers* 리소스에서 *frontendIPConfigurations*의 *publicIPAddress* 요소를 새로 만든 IP 대신 기존의 고정 IP를 참조하도록 변경합니다.

    ```
                "frontendIPConfigurations": [
                        {
                            "name": "LoadBalancerIPConfig",
                            "properties": {
                                "publicIPAddress": {
                                    /*"id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(parameters('lbIPName'),'-','0'))]"*/
                                    "id": "[variables('existingStaticIP')]"
                                }
                            }
                        }
                    ],
    ```

7. *Microsoft.ServiceFabric/clusters* 리소스의 *managementEndpoint*를 고정 IP의 DNS FQDN으로 변경합니다.  **보안 클러스터를 사용하는 경우 'http://'를 'https://'로 변경해야 합니다.** (참고: 이 지침은 Service Fabric 클러스터에만 해당됩니다.  가상 컴퓨터 크기 집합을 사용하는 경우에는 이 단계를 건너뜁니다.)

    ```
                    "fabricSettings": [],
                    /*"managementEndpoint": "[concat('http://',reference(concat(parameters('lbIPName'),'-','0')).dnsSettings.fqdn,':',parameters('nt0fabricHttpGatewayPort'))]",*/
                    "managementEndpoint": "[concat('http://',parameters('existingStaticIPDnsFQDN'),':',parameters('nt0fabricHttpGatewayPort'))]",
    ```

8. 템플릿 배포

    ```powershell
    New-AzureRmResourceGroup -Name sfnetworkingstaticip -Location westus

    $staticip = Get-AzureRmPublicIpAddress -Name staticIP1 -ResourceGroupName ExistingRG

    $staticip

    New-AzureRmResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkingstaticip -TemplateFile C:\SFSamples\Final\template\_staticip.json -existingStaticIPResourceGroup $staticip.ResourceGroupName -existingStaticIPName $staticip.Name -existingStaticIPDnsFQDN $staticip.DnsSettings.Fqdn
    ```

배포 후에는 부하 분산 장치가 다른 리소스 그룹의 공용 고정 IP 주소에 바인딩된 것을 볼 수 있습니다. Service Fabric 클라이언트 연결 끝점 및 [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) 끝점은 고정 IP 주소의 DNS FQDN을 가리킵니다.

<a id="internallb"></a>
## <a name="internal-only-load-balancer"></a>내부 전용 부하 분산 장치

이 시나리오에서는 기본 Service Fabric 템플릿의 외부 부하 분산 장치를 내부 전용 부하 분산 장치로 바꿉니다.  Azure Portal 및 SFRP 의미에 대해서는 이전 내용을 참조하세요.

1. *dnsName* 매개 변수는 필요하지 않으므로 제거합니다.

    ```
    /*
    "dnsName": {
        "type": "string"
    },
    */
    ```

2. 경우에 따라 고정 할당 방법을 사용하는 경우에는 고정 IP 주소 매개 변수를 추가합니다. 동적 할당 방법을 사용하는 경우에는 이 매개 변수가 필요하지 않습니다.

    ```
            "internalLBAddress": {
                "type": "string",
                "defaultValue": "10.0.0.250"
            }
    ```

3. Azure에 새 IP 주소를 만들지 않도록 Resources에서 *Microsoft.Network/publicIPAddresses*를 제거합니다.

    ```
    /*
    {
        "apiVersion": "[variables('publicIPApiVersion')]",
        "type": "Microsoft.Network/publicIPAddresses",
        "name": "[concat(parameters('lbIPName'),)'-', '0')]",
        "location": "[parameters('computeLocation')]",
        "properties": {
            "dnsSettings": {
                "domainNameLabel": "[parameters('dnsName')]"
            },
            "publicIPAllocationMethod": "Dynamic"        
        },
        "tags": {
            "resourceType": "Service Fabric",
            "clusterName": "[parameters('clusterName')]"
        }
    }, */
    ```

4. 새 IP 주소를 만드는 데만 의존하지 않도록 *Microsoft.Network/loadBalancers*의 *dependsOn* 특성에서 IP 주소를 제거합니다.  부하 분산 장치는 이제 VNet의 서브넷에 의존하므로 VNet *dependsOn* 특성을 추가합니다.

    ```
                "apiVersion": "[variables('lbApiVersion')]",
                "type": "Microsoft.Network/loadBalancers",
                "name": "[concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType0Name'))]",
                "location": "[parameters('computeLocation')]",
                "dependsOn": [
                    /*"[concat('Microsoft.Network/publicIPAddresses/',concat(parameters('lbIPName'),'-','0'))]"*/
                    "[concat('Microsoft.Network/virtualNetworks/',parameters('virtualNetworkName'))]"
                ],
    ```

5. 부하 분산 장치의 *frontendIPConfigurations*을 *publicIPAddress* 사용 방식에서 서브넷 및 *privateIPAddress* 사용 방식으로 변경합니다. 이 경우 미리 정의된 고정 내부 IP 주소를 사용합니다.  *privateIPAddress* 요소를 제거하고 *privateIPAllocationMethod*를 "Dynamic"으로 변경하여 동적 IP 주소를 사용할 수 있습니다.

    ```
                "frontendIPConfigurations": [
                        {
                            "name": "LoadBalancerIPConfig",
                            "properties": {
                                /*
                                "publicIPAddress": {
                                    "id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(parameters('lbIPName'),'-','0'))]"
                                } */
                                "subnet" :{
                                    "id": "[variables('subnet0Ref')]"
                                },
                                "privateIPAddress": "[parameters('internalLBAddress')]",
                                "privateIPAllocationMethod": "Static"
                            }
                        }
                    ],
    ```

6. *Microsoft.ServiceFabric/clusters* 리소스에서 내부 부하 분산 장치 주소를 가리키도록 *managementEndpoint*를 변경합니다.  **보안 클러스터를 사용하는 경우 'http://'를 'https://'로 변경해야 합니다.** (참고: 이 지침은 Service Fabric 클러스터에만 해당됩니다.  가상 컴퓨터 크기 집합을 사용하는 경우에는 이 단계를 건너뜁니다.)

    ```
                    "fabricSettings": [],
                    /*"managementEndpoint": "[concat('http://',reference(concat(parameters('lbIPName'),'-','0')).dnsSettings.fqdn,':',parameters('nt0fabricHttpGatewayPort'))]",*/
                    "managementEndpoint": "[concat('http://',reference(variables('lbID0')).frontEndIPConfigurations[0].properties.privateIPAddress,':',parameters('nt0fabricHttpGatewayPort'))]",
    ```

7. 템플릿 배포

    ```powershell
    New-AzureRmResourceGroup -Name sfnetworkinginternallb -Location westus

    New-AzureRmResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkinginternallb -TemplateFile C:\SFSamples\Final\template\_internalonlyLB.json
    ```

배포 후에 부하 분산 장치는 개인 정적 10.0.0.250 IP 주소를 사용합니다. 다른 컴퓨터가 동일한 VNet에 있는 경우 내부 [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) 끝점으로 이동한 후 부하 분산 장치 뒤의 노드 중 하나에 연결되는지 확인할 수 있습니다.

<a id="internalexternallb"></a>
## <a name="internal-and-external-load-balancer"></a>내부 및 외부 부하 분산 장치

이 시나리오는 기존 단일 노드 형식 외부 부하 분산 장치를 사용하고 동일한 노드 형식에 대한 추가 내부 부하 분산 장치를 추가합니다.  백 엔드 주소 풀에 연결된 백 엔드 포트는 단일 부하 분산 장치에만 할당될 수 있으므로 응용 프로그램 포트가 있어야 하는 부하 분산 장치와 관리 끝점(포트 19000/19080)이 있어야 하는 부하 분산 장치를 결정해야 합니다.  관리 끝점을 내부 부하 분산 장치에 배치하기로 결정한 경우 위의 SFRP 제한에 유의하세요.  이 샘플은 외부 부하 분산 장치에 관리 끝점을 유지하고 포트 80 응용 프로그램 포트를 추가한 후 내부 부하 분산 장치에 배치합니다.

한 노드 형식은 외부 부하 분산 장치에 있고 다른 노드 형식은 내부 부하 분산 장치에 있는 두 노드 형식의 클러스터를 원할 경우 포털에서 만들어진 두 노드 형식 템플릿(2개의 부하 분산 장치와 함께 제공)을 사용하고, ‘내부 전용 부하 분산 장치' 섹션에 따라 두 번째 부하 분산 장치를 내부 부하 분산 장치로 전환합니다.

1. 정적 내부 LB IP 주소 매개 변수를 추가합니다(동적 IP 주소가 필요한 경우 위의 참고 사항 참조).

    ```
            "internalLBAddress": {
                "type": "string",
                "defaultValue": "10.0.0.250"
            }
    ```

2. 응용 프로그램 포트 80 매개 변수를 추가합니다.

3. 기존 네트워킹 변수의 내부 버전을 복사/붙여넣기로 추가하고 이름에 "-Int"를 추가합니다.

    ```
    /* Add internal LB networking variables */
            "lbID0-Int": "[resourceId('Microsoft.Network/loadBalancers', concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType0Name'), '-Internal'))]",
            "lbIPConfig0-Int": "[concat(variables('lbID0-Int'),'/frontendIPConfigurations/LoadBalancerIPConfig')]",
            "lbPoolID0-Int": "[concat(variables('lbID0-Int'),'/backendAddressPools/LoadBalancerBEAddressPool')]",
            "lbProbeID0-Int": "[concat(variables('lbID0-Int'),'/probes/FabricGatewayProbe')]",
            "lbHttpProbeID0-Int": "[concat(variables('lbID0-Int'),'/probes/FabricHttpGatewayProbe')]",
            "lbNatPoolID0-Int": "[concat(variables('lbID0-Int'),'/inboundNatPools/LoadBalancerBEAddressNatPool')]",
            /* internal LB networking variables end */
    ```

4. 응용 프로그램 포트 80을 사용하고 포털에서 생성된 템플릿으로 시작하는 경우 기본 포털 템플릿은 외부 부하 분산 장치에 *AppPort1*(포트 80)을 추가합니다.  이 경우 내부 부하 분산 장치에 추가할 수 있도록 외부 부하 분산 장치 *loadBalancingRules* 및 프로브에서 이 포트를 제거합니다.

    ```
    "loadBalancingRules": [
        {
            "name": "LBHttpRule",
            "properties":{
                "backendAddressPool": {
                    "id": "[variables('lbPoolID0')]"
                },
                "backendPort": "[parameters('nt0fabricHttpGatewayPort')]",
                "enableFloatingIP": "false",
                "frontendIPConfiguration": {
                    "id": "[variables('lbIPConfig0')]"            
                },
                "frontendPort": "[parameters('nt0fabricHttpGatewayPort')]",
                "idleTimeoutInMinutes": "5",
                "probe": {
                    "id": "[variables('lbHttpProbeID0')]"
                },
                "protocol": "tcp"
            }
        } /* remove the AppPort1 from the external LB,
        {
            "name": "AppPortLBRule1",
            "properties": {
                "backendAddressPool": {
                    "id": "[variables('lbPoolID0')]"
                },
                "backendPort": "[parameters('loadBalancedAppPort1')]",
                "enableFloatingIP": "false",
                "frontendIPConfiguration": {
                    "id": "[variables('lbIPConfig0')]"            
                },
                "frontendPort": "[parameters('loadBalancedAppPort1')]",
                "idleTimeoutInMinutes": "5",
                "probe": {
                    "id": "[concate(variables('lbID0'), '/probes/AppPortProbe1')]"
                },
                "protocol": "tcp"
            }
        }*/

    ],
    "probes": [
        {
            "name": "FabricGatewayProbe", 
            "properties": {
                "intervalInSeconds": 5,
                "numberOfProbes": 2,
                "port": "[parameters('nt0fabricTcpGatewayPort')]",
                "protocol": "tcp"
            }
        },
        {
            "name": "FabricHttpGatewayProbe",
            "properties": {
                "intervalInSeconds": 5,
                "numberOfProbes": 2,
                "port": "[parameters('nt0fabricHttpGatewayPort')]",
                "protocol": "tcp"
            }
        } /* remove the AppPort1 from the external LB,,
        {
            "name": "AppPortProbe1",
            "properties": {
                "intervalInSeconds": 5,
                "numberOfProbes": 2,
                "port": "[parameters('loadBalancedAppPort1')]",
                "protocol": "tcp"
            }
        } */

    ], 
    "inboundNatPools": [
    ```

5. 이전 [내부 전용 부하 분산 장치](#internallb) 섹션에서 만든 내부 부하 분산 장치와 매우 유사해 보이는 두 번째 *Microsoft.Network/loadBalancers*를 추가하지만 이번에는 '-Int' 부하 분산 장치 변수를 사용하고 응용 프로그램 포트 80만 구현합니다.  또한 이렇게 하면 공용 부하 분산 장치에 RDP 끝점을 유지하기 위해 *inboundNatPools*가 제거됩니다. 내부 부하 분산 장치에서 RDP를 사용하려고 하는 경우 외부 부하 분산 장치의 *inboundNatPools*를 외부 부하 분산 장치로 이동합니다.

    ```
            /* Add a second load balancer, configured with a static privateIPAddress and the "-Int" LB variables */
            {
                "apiVersion": "[variables('lbApiVersion')]",
                "type": "Microsoft.Network/loadBalancers",
                /* Add '-Internal' to name */
                "name": "[concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType0Name'), '-Internal')]",
                "location": "[parameters('computeLocation')]",
                "dependsOn": [
                    /* Remove public IP dependsOn, add vnet dependsOn
                    "[concat('Microsoft.Network/publicIPAddresses/',concat(parameters('lbIPName'),'-','0'))]"
                    */
                    "[concat('Microsoft.Network/virtualNetworks/',parameters('virtualNetworkName'))]"
                ],
                "properties": {
                    "frontendIPConfigurations": [
                        {
                            "name": "LoadBalancerIPConfig",
                            "properties": {
                                /* Switch from Public to Private IP address
                                */
                                "publicIPAddress": {
                                    "id": "[resourceId('Microsoft.Network/publicIPAddresses',concat(parameters('lbIPName'),'-','0'))]"
                                }
                                */
                                "subnet" :{
                                    "id": "[variables('subnet0Ref')]"
                                },
                                "privateIPAddress": "[parameters('internalLBAddress')]",
                                "privateIPAllocationMethod": "Static"
                            }
                        }
                    ],
                    "backendAddressPools": [
                        {
                            "name": "LoadBalancerBEAddressPool",
                            "properties": {}
                        }
                    ],
                    "loadBalancingRules": [
                        /* Add the AppPort rule, making sure to reference the "-Int" versions of the backendAddressPool, frontendIPConfiguration, and probe variables */
                        {
                            "name": "AppPortLBRule1",
                            "properties": {
                                "backendAddressPool": {
                                    "id": "[variables('lbPoolID0-Int')]"
                                },
                                "backendPort": "[parameters('loadBalancedAppPort1')]",
                                "enableFloatingIP": "false",
                                "frontendIPConfiguration": {
                                    "id": "[variables('lbIPConfig0-Int')]"
                                },
                                "frontendPort": "[parameters('loadBalancedAppPort1')]",
                                "idleTimeoutInMinutes": "5",
                                "probe": {
                                    "id": "[concat(variables('lbID0-Int'),'/probes/AppPortProbe1')]"
                                },
                                "protocol": "tcp"
                            }
                        }
                    ],
                    "probes": [
                    /* Add the probe for the app port */
                    {
                            "name": "AppPortProbe1",
                            "properties": {
                                "intervalInSeconds": 5,
                                "numberOfProbes": 2,
                                "port": "[parameters('loadBalancedAppPort1')]",
                                "protocol": "tcp"
                            }
                        }
                    ],
                    "inboundNatPools": [
                    ]
                },
                "tags": {
                    "resourceType": "Service Fabric",
                    "clusterName": "[parameters('clusterName')]"
                }
            },
    ```

6. *Microsoft.Compute/virtualMachineScaleSets* 리소스에 대한 *networkProfile*에서 내부 백 엔드 주소 풀을 추가합니다.

    ```
    "loadBalancerBackendAddressPools": [
                                                        {
                                                            "id": "[variables('lbPoolID0')]"
                                                        },
                                                        {
                                                            /* Add internal BE pool */
                                                            "id": "[variables('lbPoolID0-Int')]"
                                                        }
    ],
    ```

7. 템플릿 배포

    ```powershell
    New-AzureRmResourceGroup -Name sfnetworkinginternalexternallb -Location westus

    New-AzureRmResourceGroupDeployment -Name deployment -ResourceGroupName sfnetworkinginternalexternallb -TemplateFile C:\SFSamples\Final\template\_internalexternalLB.json
    ```

배포 후에 리소스 그룹에는 두 개의 부하 분산 장치가 표시되고 부하 분산 장치를 따라 이동하면 공용 IP 주소 및 공용 IP 주소에 할당된 관리 끝점(포트 19000/19080), 고정 내부 IP 주소 및 내부 부하 분산 장치에 할당된 응용 프로그램 끝점(포트 80), 동일한 가상 컴퓨터 크기 집합 백 엔드 풀을 사용하는 두 부하 분산 장치를 볼 수 있습니다.

## <a name="next-steps"></a>다음 단계
지금까지 Azure 네트워킹 기능에 Service Fabric 클러스터를 통합하는 방법을 살펴보았으므로 계속해서 [클러스터 만들기](service-fabric-cluster-creation-via-arm.md) 과정을 진행합니다. 

