<properties
   pageTitle="Express 경로 고객 라우터 구성 샘플 | Microsoft Azure"
   description="이 페이지는 Cisco 및 Juniper 라우터에 대한 라우터 구성 샘플을 제공합니다."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carolz"
   editor="" />
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/12/2015"
   ms.author="cherylmc"/>

# NAT 설정 및 관리를 위한 라우터 구성 샘플

이 페이지는 Cisco ASA 및 Juniper MX 시리즈 라우터에 NAT 구성 샘플을 제공합니다. 이러한 샘플은 단지 지침용이므로 그대로 사용해서는 안 됩니다. 사용 중인 네트워크에 적절하게 구성하려면 공급업체와 작업하면 됩니다.

>[AZURE.IMPORTANT]이 페이지에 있는 샘플은 순수하게 지침을 위한 것입니다. 공급업체의 영업/기술 팀 및 네트워킹 팀과 함께 작업하면서 필요에 맞게 적절히 구성해야 합니다. Microsoft는 이 페이지에 나열된 구성과 관련된 문제를 지원하지 않습니다. 지원 문제는 장치 공급업체에 문의해야 합니다.

아래의 라우터 구성 샘플은 Azure 공용 및 Microsoft 피어링에 적용됩니다. Azure 개인 피어링의 경우 NAT를 구성하면 안 됩니다. 자세한 내용은 [Express 경로 피어링](expressroute-circuit-peerings.md) 및 [Express 경로 NAT 요구 사항](expressroute-nat.md)을 검토하세요.

**참고:** 인터넷 및 Express 경로에 대한 연결은 별도의 NAT IP 풀을 사용해야 합니다. 인터넷 및 Express 경로에서 동일한 NAT IP 풀을 사용하면 비대칭 라우팅 및 연결 손실이 발생합니다.

## Cisco ASA 방화벽

### 고객 네트워크에서 Microsoft로의 트래픽을 위한 PAT 구성

    object network MSFT-PAT
      range <SNAT-START-IP> <SNAT-END-IP>
    
    
    object-group network MSFT-Range
      network-object <IP> <Subnet_Mask>
    
    object-group network on-prem-range-1
      network-object <IP> <Subnet_Mask>
    
    object-group network on-prem-range-2
      network-object <IP> <Subnet_Mask>
    
    object-group network on-prem
      network-object object on-prem-range-1
      network-object object on-prem-range-2
    
    nat (outside,inside) source dynamic on-prem pat-pool MSFT-PAT destination static MSFT-Range MSFT-Range



## Juniper MX 시리즈 라우터 

### 1\. 클러스터에 대한 중복 이더넷 인터페이스 만들기

	interfaces {
	    reth0 {
	        description "To Internal Network";
	        vlan-tagging;
	        redundant-ether-options {
	            redundancy-group 1;
	        }
	        unit 100 {
	            vlan-id 100;
	            family inet {
	                address <IP_Address/Subnet_mask>;
	            }
	        }
	    }
	    reth1 {
	        description "To Microsoft via Edge Router";
	        vlan-tagging;
	        redundant-ether-options {
	            redundancy-group 2;
	        }
	        unit 100 {
	            description "To Microsoft via Edge Router";
	            vlan-id 100;
	            family inet {
	                address <IP_Address/Subnet_mask>;
	            }
	        }
	    }
	}


### 2\. 두 보안 영역 만들기

 - 내부 네트워크에 대한 신뢰 영역 및 에지 라우터 방향의 외부 네트워크에 대한 신뢰할 수 없는 영역
 - 영역에 적절한 인터페이스 할당
 - 인터페이스에서 서비스 허용


	security { zones { security-zone Trust { host-inbound-traffic { system-services { ping; } protocols { bgp; } } interfaces { reth0.100; } } security-zone Untrust { host-inbound-traffic { system-services { ping; } protocols { bgp; } } interfaces { reth1.100; } } } }


### 3\. 영역 간에 보안 정책 만들기
 
	security {
	    policies {
	        from-zone Trust to-zone Untrust {
	            policy allow-any {
	                match {
	                    source-address any;
	                    destination-address any;
	                    application any;
	                }
	                then {
	                    permit;
	                }
	            }
	        }
	        from-zone Untrust to-zone Trust {
	            policy allow-any {
	                match {
	                    source-address any;
	                    destination-address any;
	                    application any;
	                }
	                then {
	                    permit;
	                }
	            }
	        }
	    }
	}


### 4\. NAT 정책 구성
 - 두 NAT 풀을 만듭니다. 하나는 Microsoft로의 NAT 트래픽 아웃바운드에 사용되며 다른 하나는 Microsoft에서 고객에게로의 NAT 트래픽 아웃바운드에 사용됩니다.
 - 각 트래픽에 대해 NAT를 수행하기 위한 규칙 만들기

		security {
		    nat {
		        source {
		            pool SNAT_To_ExpressRoute {
		                routing-instance {
		                    External_ExpressRoute;
		                }
		                address {
		                    <NAT_IP_address/Subnet_mask>;
		                }
		            }
		            pool SNAT_From_ExpressRoute {
		                routing-instance {
		                    Internal;
		                }
		                address {
		                    <NAT_IP_address/Subnet_mask>;
		                }
		            }
		            rule-set Outbound_NAT {
		                from routing-instance Internal;
		                to routing-instance External_ExpressRoute;
		                rule SNAT_Out {
		                    match {
		                        source-address 0.0.0.0/0;
		                    }
		                    then {
		                        source-nat {
		                            pool {
		                                SNAT_To_ExpressRoute;
		                            }
		                        }
		                    }
		                }
		            }
		            rule-set Inbound_NAT {
		                from routing-instance External_ExpressRoute;
		                to routing-instance Internal;
		                rule SNAT_In {
		                    match {
		                        source-address 0.0.0.0/0;
		                    }
		                    then {
		                        source-nat {
		                            pool {
		                                SNAT_From_ExpressRoute;
		                            }
		                        }
		                    }
		                }
		            }
		        }
		    }
		}


### 5\. 각 방향으로 선택적 접두사를 보급하도록 BGP 구성

[라우팅 구성 예제](expressroute-config-samples-routing.md) 페이지에 있는 샘플을 참조하세요.

### 6\. 정책 만들기

	routing-options {
	    	      autonomous-system <Customer_ASN>;
	}
	policy-options {
	    prefix-list Microsoft_Prefixes {
	        <IP_Address/Subnet_Mask;
	        <IP_Address/Subnet_Mask;
	    }
	    prefix-list private-ranges {
	        10.0.0.0/8;
	        172.16.0.0/12;
	        192.168.0.0/16;
	        100.64.0.0/10;
	    }
	    policy-statement Advertise_NAT_Pools {
	        from {
	            protocol static;
	            route-filter <NAT_Pool_Address/Subnet_mask> prefix-length-range /32-/32;
	        }
	        then accept;
	    }
	    policy-statement Accept_from_Microsoft {
	        term 1 {
	            from {
	                instance External_ExpressRoute;
	                prefix-list-filter Microsoft_Prefixes orlonger;
	            }
	            then accept;
	        }
	        term deny {
	            then reject;
	        }
	    }
	    policy-statement Accept_from_Internal {
	        term no-private {
	            from {
	                instance Internal;
	                prefix-list-filter private-ranges orlonger;
	            }
	            then reject;
	        }
	        term bgp {
	            from {
	                instance Internal;
	                protocol bgp;
	            }
	            then accept;
	        }
	        term deny {
	            then reject;
	        }
	    }
	}
	routing-instances {
	    Internal {
	        instance-type virtual-router;
	        interface reth0.100;
	        routing-options {
	            static {
	                route <NAT_Pool_IP_Address/Subnet_mask> discard;
	            }
	            instance-import Accept_from_Microsoft;
	        }
	        protocols {
	            bgp {
	                group customer {
	                    export <Advertise_NAT_Pools>;
	                    peer-as <Customer_ASN_1>;
	                    neighbor <BGP_Neighbor_IP_Address>;
	                }
	            }
	        }
	    }
	    External_ExpressRoute {
	        instance-type virtual-router;
	        interface reth1.100;
	        routing-options {
	            static {
	                route <NAT_Pool_IP_Address/Subnet_mask> discard;
	            }
	            instance-import Accept_from_Internal;
	        }
	        protocols {
	            bgp {
	                group edge_router {
	                    export <Advertise_NAT_Pools>;
	                    peer-as <Customer_Public_ASN>;
	                    neighbor <BGP_Neighbor_IP_Address>;
	                }
	            }
	        }
	    }
	}

## 다음 단계

자세한 내용은 [Express 경로 FAQ](expressroute-faqs.md)를 참조하세요.

<!---HONumber=Oct15_HO3-->