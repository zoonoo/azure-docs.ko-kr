---
title: "Azure Service Fabric 컨테이너 서비스에 대한 네트워킹 모드 구성 | Microsoft Docs"
description: "Azure Service Fabric에서 지원하는 다른 네트워킹 모드를 설정하는 방법을 알아봅니다."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: d552c8cd-67d1-45e8-91dc-871853f44fc6
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: subramar
ms.openlocfilehash: 1ecded3af6396f50e67dc5d2a9ef8337699046ea
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="service-fabric-container-networking-modes"></a>Service Fabric 컨테이너 네트워킹 모드

컨테이너 서비스에 대해 Service Fabric 클러스터에서 제공되는 기본 네트워킹 모드는 `nat` 네트워킹 모드입니다. `nat` 네트워킹 모드에서 동일한 포트에 수신 대기하는 둘 이상의 컨테이너 서비스가 있는 경우 배포 오류가 발생합니다. 동일한 포트에서 여러 서비스가 수신 대기하도록 하기 위해 Service Fabric은 `open` 네트워킹 모드(버전 5.7 이상)을 지원합니다. `open` 네트워킹 모드에서 각 컨테이너 서비스는 내부적으로 여러 서비스를 동일한 포트에서 수신 대기할 수 있는 동적으로 할당된 IP 주소를 가져옵니다.   

따라서 서비스 매니페스트에 정의된 고정 끝점을 갖는 단일 서비스 형식으로 `open` 네트워킹 모드를 사용하여 배포 오류 없이 새로운 서비스를 만들고 삭제할 수 있습니다. 마찬가지로 여러 서비스를 만들기 위해 고정 포트 매핑을 포함한 동일한 `docker-compose.yml` 파일을 사용할 수 있습니다.

서비스를 다시 시작하거나 다른 노드로 이동하는 경우 IP 주소를 변경하기 때문에 서비스를 검색하는 데 동적으로 할당된 IP를 사용하는 것은 좋지 않습니다. 서비스 검색을 위해 **Service Fabric 명명 서비스** 또는 **DNS 서비스**만을 사용합니다. 


> [!WARNING]
> 총 4096개의 IP가 Azure의 vNET에서 허용됩니다. 따라서 노드 수 및 컨테이너 서비스 인스턴스 수의 합계(`open` 네트워킹을 포함)는 vNET 내에서 4096개를 초과할 수 없습니다. 이러한 고밀도 시나리오에는 `nat` 네트워킹 모드를 사용하는 것이 좋습니다.
>

## <a name="setting-up-open-networking-mode"></a>오픈 네트워킹 모드 설정

1. `fabricSettings`에서 DNS 서비스와 IP 공급자를 사용하여 Azure Resource Manager 템플릿을 설정합니다. 

    ```json
    "fabricSettings": [
                {
                    "name": "DnsService",
                    "parameters": [
                       {
                            "name": "IsEnabled",
                            "value": "true"
                      }
                    ]
                },
                {
                    "name": "Hosting",
                    "parameters": [
                      { 
                            "name": "IPProviderEnabled",
                            "value": "true"
                      }
                    ]
                },
                {
                    "name":  "Trace/Etw", 
                    "parameters": [
                    {
                            "name": "Level",
                            "value": "5"
                    }
                    ]
                },
                {
                    "name": "Setup",
                    "parameters": [
                    {
                            "name": "ContainerNetworkSetup",
                            "value": "true"
                    }
                    ]
                }
            ],
    ```

2. 클러스터의 각 노드에서 구성되어야 하는 여러 IP 주소를 허용하도록 네트워크 프로필 섹션을 설정합니다. 다음 예제에서는 Windows/Linux Service Fabric 클러스터에 노드당 5개의 IP 주소를 설정합니다(따라서 각 노드에서 포트에 수신 대기하는 5개의 서비스 인스턴스가 있을 수 있음).

    ```json
    "variables": {
        "nicName": "NIC",
        "vmName": "vm",
        "virtualNetworkName": "VNet",
        "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
        "vmNodeType0Name": "[toLower(concat('NT1', variables('vmName')))]",
        "subnet0Name": "Subnet-0",
        "subnet0Prefix": "10.0.0.0/24",
        "subnet0Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet0Name'))]",
        "lbID0": "[resourceId('Microsoft.Network/loadBalancers',concat('LB','-', parameters('clusterName'),'-',variables('vmNodeType0Name')))]",
        "lbIPConfig0": "[concat(variables('lbID0'),'/frontendIPConfigurations/LoadBalancerIPConfig')]",
        "lbPoolID0": "[concat(variables('lbID0'),'/backendAddressPools/LoadBalancerBEAddressPool')]",
        "lbProbeID0": "[concat(variables('lbID0'),'/probes/FabricGatewayProbe')]",
        "lbHttpProbeID0": "[concat(variables('lbID0'),'/probes/FabricHttpGatewayProbe')]",
        "lbNatPoolID0": "[concat(variables('lbID0'),'/inboundNatPools/LoadBalancerBEAddressNatPool')]"
    }
    "networkProfile": {
                "networkInterfaceConfigurations": [
                  {
                    "name": "[concat(parameters('nicName'), '-0')]",
                    "properties": {
                      "ipConfigurations": [
                        {
                          "name": "[concat(parameters('nicName'),'-',0)]",
                          "properties": {
                            "primary": "true",
                            "loadBalancerBackendAddressPools": [
                              {
                                "id": "[variables('lbPoolID0')]"
                              }
                            ],
                            "loadBalancerInboundNatPools": [
                              {
                                "id": "[variables('lbNatPoolID0')]"
                              }
                            ],
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 1)]",
                          "properties": {
                            "primary": "false",
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 2)]",
                          "properties": {
                            "primary": "false",
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 3)]",
                          "properties": {
                            "primary": "false",
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 4)]",
                          "properties": {
                            "primary": "false",
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        },
                        {
                          "name": "[concat(parameters('nicName'),'-', 5)]",
                          "properties": {
                            "primary": "false",
                            "subnet": {
                              "id": "[variables('subnet0Ref')]"
                            }
                          }
                        }
                      ],
                      "primary": true
                    }
                  }
                ]
              }
   ```
 

3. Windows 클러스터의 경우에만 다음 값을 갖는 vNET에 UDP/53 포트를 여는 NSG 규칙을 설정합니다.

   | 우선 순위 |    이름    |    원본      |  대상   |   부여    | 동작 |
   |:--------:|:----------:|:--------------:|:--------------:|:------------:|:------:|
   |     2000 | Custom_Dns | VirtualNetwork | VirtualNetwork | DNS(UDP/53) | 허용  |


4. 각 `<NetworkConfig NetworkType="open">` 서비스에 대해 응용 프로그램 매니페스트에서 네트워킹 모드를 지정합니다.  `open` 모드에서는 서비스가 전용 IP 주소를 가져오도록 합니다. 모드를 지정하지 않으면 기본 `nat` 모드를 기본값으로 합니다. 따라서 다음 매니페스트 예제에서 `NodeContainerServicePackage1` 및 `NodeContainerServicePackage2`는 동일한 포트를 각각 수신할 수 있습니다(두 서비스는 모두 `Endpoint1`에서 수신 중).

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <ApplicationManifest ApplicationTypeName="NodeJsApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
      <Description>Calculator Application</Description>
      <Parameters>
        <Parameter Name="ServiceInstanceCount" DefaultValue="3"></Parameter>
        <Parameter Name="MyCpuShares" DefaultValue="3"></Parameter>
      </Parameters>
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="NodeContainerServicePackage1" ServiceManifestVersion="1.0"/>
        <Policies>
          <ContainerHostPolicies CodePackageRef="NodeContainerService1.Code" Isolation="hyperv">
           <NetworkConfig NetworkType="open"/>
           <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
          </ContainerHostPolicies>
        </Policies>
      </ServiceManifestImport>
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="NodeContainerServicePackage2" ServiceManifestVersion="1.0"/>
        <Policies>
          <ContainerHostPolicies CodePackageRef="NodeContainerService2.Code" Isolation="default">
            <NetworkConfig NetworkType="open"/>
            <PortBinding ContainerPort="8910" EndpointRef="Endpoint1"/>
          </ContainerHostPolicies>
        </Policies>
      </ServiceManifestImport>
    </ApplicationManifest>
    ```
Windows 클러스터용 응용 프로그램 내의 서비스에서 다른 네트워킹 모드를 혼합하고 일치시킬 수 있습니다. 따라서 `open` 모드에 일부 서비스가 있고 `nat` 네트워킹 모드에 일부 서비스가 있을 수 있습니다. `nat`로 서비스를 구성한 경우 수신하는 포트는 고유해야 합니다. 다른 서비스에 네트워킹 모드를 혼합하는 작업은 Linux 클러스터에서 지원되지 않습니다. 


## <a name="next-steps"></a>다음 단계
이 문서에서는 Service Fabric에서 제공하는 네트워킹 모드에 대해 알아보았습니다.  

* [서비스 패브릭 응용 프로그램 모델](service-fabric-application-model.md)
* [Service Fabric 서비스 매니페스트 리소스](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-service-manifest-resources)
* [Windows Server 2016에서 Windows 컨테이너를 Service Fabric에 배포](service-fabric-get-started-containers.md)
* [Linux에서 Docker 컨테이너를 Service Fabric에 배포](service-fabric-get-started-containers-linux.md)
