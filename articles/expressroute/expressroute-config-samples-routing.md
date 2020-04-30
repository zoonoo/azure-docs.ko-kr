---
title: 'Azure Express 경로: 라우터 구성 샘플'
description: 이 페이지는 Cisco 및 Juniper 라우터에 대한 라우터 구성 샘플을 제공합니다.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 03/26/2020
ms.author: osamaz
ms.openlocfilehash: 3603bc45b920dc62eb8bf6f2eb8557f98e21638e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82024815"
---
# <a name="router-configuration-samples-to-set-up-and-manage-routing"></a>라우팅 설정 및 관리를 위한 라우터 구성 샘플
이 페이지에서는 Azure Express 경로를 사용 하 여 작업할 때 Cisco IOS-XE 및 곱 향나무 MX 시리즈 라우터에 대 한 인터페이스 및 라우팅 구성 샘플을 제공 합니다.

> [!IMPORTANT]
> 이 페이지에 대 한 샘플은 전적으로 지침을 제공 합니다. 사용자의 요구 사항에 맞는 적절 한 구성을 찾으려면 공급 업체의 영업/기술 팀과 네트워킹 팀과 함께 작업 해야 합니다. Microsoft는이 페이지에 나열 된 구성과 관련 된 문제를 지원 하지 않습니다. 지원 문제는 장치 공급 업체에 문의 하세요.
> 
> 

## <a name="mtu-and-tcp-mss-settings-on-router-interfaces"></a>라우터 인터페이스의 MTU 및 TCP MSS 설정
Express 경로 인터페이스의 MTU (최대 전송 단위)는 라우터의 이더넷 인터페이스에 대 한 일반적인 기본 MTU 인 1500입니다. 라우터에 기본적으로 다른 MTU가 있지 않는 한, 라우터 인터페이스에서 값을 지정할 필요가 없습니다.

Azure VPN 게이트웨이와 달리 Express 경로 회로에 대 한 TCP MSS (최대 세그먼트 크기)를 지정할 필요가 없습니다.

이 문서의 라우터 구성 샘플은 모든 피어 링에 적용 됩니다. 라우팅에 대한 자세한 내용은 [ExpressRoute 피어링](expressroute-circuit-peerings.md) 및 [ExpressRoute 라우팅 요구 사항](expressroute-routing.md)을 검토하세요.


## <a name="cisco-ios-xe-based-routers"></a>Cisco IOS-XE 기반 라우터
이 섹션의 샘플은 IOS-XE OS 제품군을 실행 하는 모든 라우터에 적용 됩니다.

### <a name="configure-interfaces-and-subinterfaces"></a>인터페이스 및 인터페이스 구성
Microsoft에 연결 하는 모든 라우터에서 피어 링 당 하나의 하위 인터페이스가 필요 합니다. 하위 인터페이스는 VLAN ID 또는 스택 쌍의 VLAN id와 IP 주소를 사용 하 여 식별할 수 있습니다.

**Dot1Q 인터페이스 정의**

이 샘플에서는 단일 VLAN ID를 사용 하는 하위 인터페이스에 대 한 하위 인터페이스 정의를 제공 합니다. VLAN ID는 피어링별로 고유합니다. IPv4 주소의 마지막 옥텟은 항상 홀수입니다.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <VLAN_ID>
     ip address <IPv4_Address><Subnet_Mask>

**QinQ 인터페이스 정의**

이 샘플에서는 두 개의 VLAN Id가 있는 하위 인터페이스에 대 한 하위 인터페이스 정의를 제공 합니다. 외부 VLAN ID (s-태그)는 사용 되는 경우 모든 피어 링에서 동일 하 게 유지 됩니다. 안쪽의 VLAN ID(c-tag)는 피어링별로 고유합니다. IPv4 주소의 마지막 옥텟은 항상 홀수입니다.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <s-tag> seconddot1Q <c-tag>
     ip address <IPv4_Address><Subnet_Mask>

### <a name="set-up-ebgp-sessions"></a>EBGP 세션 설정
모든 피어 링에 대해 Microsoft와 BGP 세션을 설정 해야 합니다. 다음 샘플을 사용 하 여 BGP 세션을 설정 합니다. 하위 인터페이스에 사용 된 IPv4 주소가 a. b. c. d 인 경우 BGP 환경 (Microsoft)의 IP 주소는 .b.d + 1이 됩니다. BGP 인접 라우터에 대한 IPv4 주소의 마지막 옥텟은 항상 짝수입니다.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
     neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="set-up-prefixes-to-be-advertised-over-the-bgp-session"></a>BGP 세션을 통해 보급 될 접두사 설정
다음 샘플을 사용 하 여 Microsoft에 선택 접두사를 알리도록 라우터를 구성 합니다.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
      network <Prefix_to_be_advertised> mask <Subnet_mask>
      neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="route-maps"></a>경로 맵
경로 맵 및 접두사 목록을 사용 하 여 네트워크에 전파 되는 접두사를 필터링 합니다. 다음 샘플을 참조 하 고 적절 한 접두사 목록이 설정 되었는지 확인 합니다.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
      network <Prefix_to_be_advertised> mask <Subnet_mask>
      neighbor <IP#2_used_by_Azure> activate
      neighbor <IP#2_used_by_Azure> route-map <MS_Prefixes_Inbound> in
     exit-address-family
    !
    route-map <MS_Prefixes_Inbound> permit 10
     match ip address prefix-list <MS_Prefixes>
    !

### <a name="configure-bfd"></a>BFD 구성

두 위치 (인터페이스 수준에서, 다른 하나는 BGP 수준)에서 BFD를 구성 합니다. 여기의 예제는 QinQ 인터페이스에 대 한 것입니다. 

    interface GigabitEthernet<Interface_Number>.<Number>
     bfd interval 300 min_rx 300 multiplier 3
     encapsulation dot1Q <s-tag> seconddot1Q <c-tag>
     ip address <IPv4_Address><Subnet_Mask>
    
    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
      neighbor <IP#2_used_by_Azure> activate
      neighbor <IP#2_used_by_Azure> fall-over bfd
     exit-address-family
    !


## <a name="juniper-mx-series-routers"></a>Juniper MX 시리즈 라우터
이 단원의 샘플은 모든 곱 향나무 MX 시리즈 라우터에 적용 됩니다.

### <a name="configure-interfaces-and-subinterfaces"></a>인터페이스 및 인터페이스 구성

**Dot1Q 인터페이스 정의**

이 샘플에서는 단일 VLAN ID를 사용 하는 하위 인터페이스에 대 한 하위 인터페이스 정의를 제공 합니다. VLAN ID는 피어링별로 고유합니다. IPv4 주소의 마지막 옥텟은 항상 홀수입니다.

    interfaces {
        vlan-tagging;
        <Interface_Number> {
            unit <Number> {
                vlan-id <VLAN_ID>;
                family inet {
                    address <IPv4_Address/Subnet_Mask>;
                }
            }
        }
    }


**QinQ 인터페이스 정의**

이 샘플에서는 두 개의 VLAN Id가 있는 하위 인터페이스에 대 한 하위 인터페이스 정의를 제공 합니다. 외부 VLAN ID (s-태그)는 사용 되는 경우 모든 피어 링에서 동일 하 게 유지 됩니다. 안쪽의 VLAN ID(c-tag)는 피어링별로 고유합니다. IPv4 주소의 마지막 옥텟은 항상 홀수입니다.

    interfaces {
        <Interface_Number> {
            flexible-vlan-tagging;
            unit <Number> {
                vlan-tags outer <S-tag> inner <C-tag>;
                family inet {
                    address <IPv4_Address/Subnet_Mask>;
                }                           
            }                               
        }                                   
    }                           

### <a name="set-up-ebgp-sessions"></a>EBGP 세션 설정
모든 피어 링에 대해 Microsoft와 BGP 세션을 설정 해야 합니다. 다음 샘플을 사용 하 여 BGP 세션을 설정 합니다. 하위 인터페이스에 사용 된 IPv4 주소가 a. b. c. d 인 경우 BGP 환경 (Microsoft)의 IP 주소는 .b.d + 1이 됩니다. BGP 인접 라우터에 대한 IPv4 주소의 마지막 옥텟은 항상 짝수입니다.

    routing-options {
        autonomous-system <Customer_ASN>;
    }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }

### <a name="set-up-prefixes-to-be-advertised-over-the-bgp-session"></a>BGP 세션을 통해 보급 될 접두사 설정
다음 샘플을 사용 하 여 Microsoft에 선택 접두사를 알리도록 라우터를 구성 합니다.

    policy-options {
        policy-statement <Policy_Name> {
            term 1 {
                from protocol OSPF;
        route-filter 
    <Prefix_to_be_advertised/Subnet_Mask> exact;
                then {
                    accept;
                }
            }
        }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                export <Policy_Name>
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }


### <a name="route-policies"></a>경로 정책
경로 맵 및 접두사 목록을 사용 하 여 네트워크에 전파 되는 접두사를 필터링 할 수 있습니다. 다음 샘플을 참조 하 고 적절 한 접두사 목록이 설정 되었는지 확인 합니다.

    policy-options {
        prefix-list MS_Prefixes {
            <IP_Prefix_1/Subnet_Mask>;
            <IP_Prefix_2/Subnet_Mask>;
        }
        policy-statement <MS_Prefixes_Inbound> {
            term 1 {
                from {
                prefix-list MS_Prefixes;
                }
                then {
                    accept;
                }
            }
        }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                export <Policy_Name>
                import <MS_Prefixes_Inbound>
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }

### <a name="configure-bfd"></a>BFD 구성
프로토콜 BGP 섹션 에서만 BFD를 구성 합니다.

    protocols {
        bgp { 
            group <Group_Name> { 
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
                bfd-liveness-detection {
                       minimum-interval 3000;
                       multiplier 3;
                }
            }                               
        }                                   
    }


## <a name="next-steps"></a>다음 단계
자세한 내용은 [ExpressRoute FAQ](expressroute-faqs.md) 를 참조하세요.



