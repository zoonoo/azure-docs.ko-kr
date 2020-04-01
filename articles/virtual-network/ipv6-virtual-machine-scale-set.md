---
title: Azure에서 IPv6를 통해 가상 시스템 스케일 세트 배포
titlesuffix: Azure Virtual Network
description: 이 문서에서는 Azure 가상 네트워크에서 IPv6를 사용하여 가상 시스템 크기 집합을 배포하는 방법을 보여 주며 있습니다.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: 6a751fa193c8dd530707f790af0292d536a6f47d
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420454"
---
# <a name="deploy-virtual-machine-scale-sets-with-ipv6-in-azure"></a>Azure에서 IPv6를 통해 가상 시스템 스케일 세트 배포

이 문서에서는 Azure 가상 네트워크에서 이중 스택 외부 로드 밸런서가 있는 이중 스택(IPv4 + IPv6) 가상 시스템 규모 집합을 배포하는 방법을 보여 주며 있습니다. IPv6 지원 가상 시스템 스케일 집합을 만드는 프로세스는 [여기에](ipv6-configure-standard-load-balancer-template-json.md)설명된 개별 VM을 만드는 프로세스와 거의 동일합니다. 개별 VM에 대해 설명된 단계와 유사한 단계로 시작합니다.
1.    IPv4 및 IPv6 공용 IP를 만듭니다.
2.    이중 스택 로드 밸러커를 만듭니다.  
3.    NSG(네트워크 보안 그룹) 규칙을 만듭니다.  

개별 VM과 다른 유일한 단계는 가상 시스템 규모 집합 리소스인 NETWORKProfile/networkInterfaceConfigurations를 사용하는 NIC(네트워크 인터페이스) 구성을 만드는 것입니다. JSON 구조는 NIC 및 IPv4 IpConfiguration를 **"기본"을** 사용하여 기본 인터페이스로 설정하는 추가와 함께 개별 VM에 사용되는 Microsoft.Network/networkInterface 개체와 유사합니다.

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


## <a name="sample-virtual-machine-scale-set-template-json"></a>샘플 가상 머신 스케일 세트 템플릿 JSON

듀얼 스택 (IPv4 + IPv6) 가상 머신 스케일 세트를 듀얼 스택 외부 로드 밸러커 및 가상 네트워크 보기 샘플 [템플릿을 여기에](https://azure.microsoft.com/resources/templates/ipv6-in-vnet-vmss/)배포하려면 .
## <a name="next-steps"></a>다음 단계

Azure 가상 네트워크에서 IPv6 지원에 대해 자세히 알아보려면 [Azure 가상 네트워크에 대한 IPv6이란 무엇입니까?](ipv6-overview.md)
