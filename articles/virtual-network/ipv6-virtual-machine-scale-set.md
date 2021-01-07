---
title: Azure에서 i p v 6를 사용 하 여 가상 머신 확장 집합 배포
titlesuffix: Azure Virtual Network
description: 이 문서에서는 Azure virtual network에 i p v 6를 사용 하 여 가상 머신 확장 집합을 배포 하는 방법을 보여 줍니다.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: f969d7edc22c9c36481ca42449193af5f8c7b0d9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "84710000"
---
# <a name="deploy-virtual-machine-scale-sets-with-ipv6-in-azure"></a>Azure에서 i p v 6를 사용 하 여 가상 머신 확장 집합 배포

이 문서에서는 Azure 가상 네트워크에서 이중 스택 외부 부하 분산 장치를 사용 하 여 이중 스택 (IPv4 + IPv6) 가상 머신 확장 집합을 배포 하는 방법을 보여 줍니다. IPv6 지원 가상 머신 확장 집합을 만드는 프로세스는 [여기](ipv6-configure-standard-load-balancer-template-json.md)에 설명 된 개별 vm을 만드는 프로세스와 거의 동일 합니다. 개별 Vm에 대해 설명한 것과 비슷한 단계를 시작 합니다.
1.    IPv4 및 IPv6 공용 Ip를 만듭니다.
2.    이중 스택 부하 분산 장치를 만듭니다.  
3.    NSG (네트워크 보안 그룹) 규칙을 만듭니다.  

개별 Vm과 다른 유일한 단계는 가상 머신 확장 집합 리소스를 사용 하는 NIC (네트워크 인터페이스) 구성 (networkProfile/networkInterfaceConfigurations)을 만드는 것입니다. JSON 구조는 다음 예제에 표시 된 대로 **"primary": true**  특성을 사용 하는 기본 인터페이스로 NIC 및 IPv4 IpConfiguration을 설정 하는 것 외에도 개별 vm에 사용 되는 Microsoft 네트워크/networkinterfaces 개체의 구조와 유사 합니다.

```json
          "networkProfile": {
            "networkInterfaceConfigurations": [
              {
                "name": "[variables('nicName')]",
                "properties": {
                  "primary": true,
          "networkSecurityGroup": {
            "id": "[resourceId('Microsoft.Network/networkSecurityGroups','VmssNsg')]"
          },                  
                  "ipConfigurations": [
                    {
                      "name": "[variables('ipConfigName')]",
                      "properties": {
                        "primary": true,
                        "subnet": {
                          "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets', 'MyvirtualNetwork','Mysubnet')]"
                        },
                        "privateIPAddressVersion":"IPv4",                       
                        "publicipaddressconfiguration": {
                          "name": "pub1",
                          "properties": {
                            "idleTimeoutInMinutes": 15
                          }
                        },
                        "loadBalancerBackendAddressPools": [
                          {
                            "id": "[resourceId('Microsoft.Network/loadBalancers/backendAddressPools', 'loadBalancer', 'bePool'))]"
                          }
                        ],
                        "loadBalancerInboundNatPools": [
                          {
                            "id": "[resourceId('Microsoft.Network/loadBalancers/inboundNatPools', 'loadBalancer', 'natPool')]"
                          }
                        ]
                      }
                    },
                    {
                      "name": "[variables('ipConfigNameV6')]",
                      "properties": {
                        "subnet": {
                          "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets','MyvirtualNetwork','Mysubnet')]"
                        },
                        "privateIPAddressVersion":"IPv6",
                        "loadBalancerBackendAddressPools": [
                          {
                            "id": "[resourceId('Microsoft.Network/loadBalancers/backendAddressPools', 'loadBalancer','bePoolv6')]"
                          }
                        ],                        
                      }
                    }
                  ]
                }
              }
            ]
          }

```


## <a name="sample-virtual-machine-scale-set-template-json"></a>샘플 가상 머신 확장 집합 템플릿 JSON

이중 스택 (IPv4 + IPv6) 가상 머신 확장 집합을 배포 하려면 [여기](https://azure.microsoft.com/resources/templates/ipv6-in-vnet-vmss/)에 이중 스택 외부 Load Balancer 및 가상 네트워크 뷰 샘플 템플릿이 있습니다.
## <a name="next-steps"></a>다음 단계

Azure virtual network의 IPv6 지원에 대 한 자세한 내용은 [azure Virtual Network에 대 한](ipv6-overview.md)i p v 6을 참조 하세요.
