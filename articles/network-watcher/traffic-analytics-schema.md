---
title: Azure 트래픽 분석 스키마 | Microsoft Docs
description: Azure 네트워크 보안 그룹 흐름 로그 분석 트래픽 분석의 스키마를 이해 합니다.
services: network-watcher
documentationcenter: na
author: vinynigam
manager: agummadi
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/26/2019
ms.author: vinigam
ms.openlocfilehash: 246c5256f56fd0b891d4e7d642c421b1e340fc6d
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59799337"
---
# <a name="schema-and-data-aggregation-in-traffic-analytics"></a>트래픽 분석의 스키마 및 데이터 집계

트래픽 분석은 클라우드 네트워크의 사용자 및 애플리케이션 작업에 대한 가시성을 제공하는 클라우드 기반 솔루션입니다. 트래픽 분석은 Network Watcher NSG(네트워크 보안 그룹) 흐름 로그를 분석하여 Azure 클라우드 내 트래픽 흐름에 대한 정보를 제공합니다. 트래픽 분석을 사용하면 다음과 같은 작업을 수행할 수 있습니다.

- Azure 구독에서 이루어지는 네트워크 작업을 시각화하고 핫스폿을 식별합니다.
- 열린 포트, 인터넷 액세스를 시도하는 애플리케이션, 불량 네트워크에 연결하는 VM(가상 머신) 같은 정보를 통해 네트워크에 대한 위협을 식별하고 네트워크를 보호합니다.
- Azure 지역 및 인터넷의 트래픽 흐름 패턴을 파악하여 네트워크 성능 및 용량에 맞게 네트워크 배포를 최적화합니다.
- 네트워크에서 연결 실패의 원인이 되는 네트워크 구성 오류를 정확히 파악합니다.
- 네트워크 사용량 (바이트), 패킷 또는 흐름을 알고 있습니다.

### <a name="data-aggregation"></a>데이터 집계

1. 모든 "FlowIntervalStartTime_t" 및 "FlowIntervalEndTime_t" 간에 NSG 흐름 로그는 트래픽 분석에서 처리 되기 전에 blob으로 1 분 간격으로 저장소 계정에 캡처됩니다. 
2. 트래픽 분석의 기본 처리 간격은 60 분입니다. 따라서 집계에 대 한 저장소에서 blob을 선택 하는 트래픽 분석 60 분 마다.
3. 동일한 원본 IP, 대상 IP, 대상 포트, NSG 이름, NSG 규칙, 흐름 방향 및 전송 있는 흐름 (TCP 또는 UDP) 프로토콜 계층 (참고: 원본 포트는 집계 제외)에서 트래픽 분석을 단일 흐름으로 clubbed 됩니다
4. 이 단일 레코드는 데코레이팅된 (아래 섹션에서 세부 정보) 및 트래픽 분석 하 여 Log Analytics에서 수집 합니다.
5. FlowStartTime_t 필드 흐름 로그 처리 간격 "FlowIntervalStartTime_t" 및 "FlowIntervalEndTime_t"에서 맨 처음 발견 되는 이러한 집계 된 흐름 (동일한 4-튜플)를 나타냅니다. 
6. 테이블에서 모든 리소스에 대 한 UI에 표시 된 흐름은 NSG를 표시 하는 총 흐름 하지만 로그 Anlaytics 사용자 단일 고 줄어든 레코드만 표시 됩니다. 모든 흐름을 보려면 저장소에서 참조할 수 있는 blob_id 필드를 사용 합니다. 레코드는 blob에 표시 하는 개별 흐름을 일치에 대 한 총 흐름 수입니다.


### <a name="fields-used-in-traffic-analytics-schema"></a>트래픽 분석 스키마에서 사용 되는 필드

트래픽 분석 트래픽 분석 하 여 데코 레이트 하 고 동일한 경고를 설정 하는 데이터에 사용자 지정 쿼리를 실행할 수 있도록 Log Analytics를 기반으로 빌드됩니다.

아래에 나열 된는 스키마와 나타내는 새로운 필드

| 필드 | 형식 | 설명 | 
|:---   |:---    |:---  |
| TableName | AzureNetworkAnalytics_CL | 트래픽 Anlaytics 데이터에 대 한 테이블
| SubType_s | FlowLog | 흐름 로그에 대 한 하위 유형 |
| FASchemaVersion_s |   1   | Scehma 버전입니다. NSG 흐름 로그 버전을 반영 하지 않습니다. |
| TimeProcessed_t   | 날짜 및 시간 (UTC)  | 트래픽 분석 처리 저장소 계정에서 원시 흐름 로그는 시간 |
| FlowIntervalStartTime_t | 날짜 및 시간 (UTC) |  흐름 로그 처리 간격의 시작 시간입니다. 이 시간 흐름 간격 측정 |
| FlowIntervalEndTime_t | 날짜 및 시간 (UTC) | 흐름 로그 처리 간격의 시간 끝 |
| FlowStartTime_t | 날짜 및 시간 (UTC) |  흐름 (집계 가져오기지 것입니다)의 첫 번째 발생의 흐름 로그 처리 간격 "FlowIntervalStartTime_t" 및 "FlowIntervalEndTime_t"입니다. 이 흐름을 집계 논리에 따라 집계를 가져옵니다. |
| FlowEndTime_t | 날짜 및 시간 (UTC) | 흐름 (집계 가져오기지 것입니다)의 마지막 발생 흐름 로그 처리 간격 "FlowIntervalStartTime_t" 및 "FlowIntervalEndTime_t"입니다. 흐름 로그 v2를 기준으로이 필드에 동일한 네 튜플을 사용 하 여 마지막 흐름 (에서 표시 "B" 원시 흐름 레코드) 시작 된 시간 |
| FlowType_s |  * IntraVNet <br> * InterVNet <br> * S2S <br> * P2S <br> * AzurePublic <br> * ExternalPublic <br> * MaliciousFlow <br> * 알 수 없는 개인 <br> * 알 수 없음 | 정보 테이블 아래에 정의 |
| SrcIP_s | 원본 IP 주소 | AzurePublic 시 비어 있게 됩니다 및 ExternalPublic 흐름 |
| DestIP_s | 대상 IP 주소  | AzurePublic 시 비어 있게 됩니다 및 ExternalPublic 흐름 |
| VMIP_s | VM의 IP | AzurePublic 및 ExternalPublic 흐름에 사용 되는 |
| PublicIP_S | 공용 IP 주소 | AzurePublic 및 ExternalPublic 흐름에 사용 되는 |
| DestPort_d | 대상 포트 | 트래픽을 수신 되는 포트 | 
| L4Protocol_s  | * T <br> * U  | 전송 프로토콜입니다. T = TCP <br> U = UDP | 
| L7Protocol_s  | 프로토콜 이름 | 대상 포트에서 파생 |
| FlowDirection_s | * I = 인바운드<br> * O = 아웃 바운드 | 흐름 로그에 따라/NSG에서 흐름의 방향 | 
| FlowStatus_s  | * NSG 규칙에서 허용 하는 A = <br> * D = NSG 규칙에 의해 거부 되었습니다.  | 흐름 허용/nblocked 하 여 NSG 흐름 로그에 따라 상태 |
| NSGList_s | \<SUBSCRIPTIONID>\/<RESOURCEGROUP_NAME>\/<NSG_NAME> | 흐름을 사용 하 여 연결 된 네트워크 보안 그룹 (NSG) |
| NSGRules_s | \<값이 0 인덱스) >< NSG_RULENAME >\<흐름 방향을 >\<흐름 상태 >\<FlowCount ProcessedByRule > |  허용 되거나이 흐름을 거부 하는 NSG 규칙 |
| NSGRuleType_s | * 사용자 정의 * 기본값 |   흐름에서 사용 하는 NSG 규칙의 형식 |
| MACAddress_s | MAC 주소 | 흐름을 캡처한 NIC의 MAC 주소 |
| Subscription_s | Azure 가상 네트워크의 구독 / 인터페이스에 네트워크 / 가상 컴퓨터가이 필드에 채워집니다. | FlowType에만 적용할 수 = S2S, P2S, AzurePublic, ExternalPublic, MaliciousFlow, 및 UnknownPrivate 흐름 형식 (흐름 하나만 쪽 인 azure) |
| Subscription1_s | 구독 ID | 가상 네트워크의 구독 ID / 인터페이스에 네트워크 / 가상 컴퓨터에 속해 있는 흐름에서 원본 IP |
| Subscription2_s | 구독 ID | 가상 네트워크의 구독 ID 네트워크 인터페이스 / / 대상 IP 흐름에 속한 가상 컴퓨터 |
| Region_s | Azure 지역 가상 네트워크의 네트워크 인터페이스 / / 흐름에 있는 IP에 속해 있는 가상 컴퓨터 | FlowType에만 적용할 수 = S2S, P2S, AzurePublic, ExternalPublic, MaliciousFlow, 및 UnknownPrivate 흐름 형식 (흐름 하나만 쪽 인 azure) |
| Region1_s | Azure 지역 | Azure 지역 가상 네트워크의 네트워크 인터페이스 / / 원본 IP 흐름에 속한 가상 컴퓨터 |
| Region2_s | Azure 지역 | 대상 IP 흐름에 속한 가상 네트워크의 azure 지역 |
| NIC_s | \<resourcegroup_Name>\/\<NetworkInterfaceName> |  트래픽을 보내거나 받는 VM과 연결 된 NIC |
| NIC1_s | <resourcegroup_Name>/\<NetworkInterfaceName> | 흐름의 원본 IP와 연결 된 NIC |
| NIC2_s | <resourcegroup_Name>/\<NetworkInterfaceName> | 흐름의 대상 IP와 사용 하 여 연결 된 NIC |
| VM_s | <resourcegroup_Name>\/\<NetworkInterfaceName> | NIC_s 네트워크 인터페이스에 연결 된 가상 컴퓨터 |
| VM1_s | <resourcegroup_Name>/\<VirtualMachineName> | 흐름의 원본 IP와 연결 된 가상 컴퓨터 |
| VM2_s | <resourcegroup_Name>/\<VirtualMachineName> | 흐름의 대상 IP와 연결 된 가상 컴퓨터 |
| Subnet_s | <ResourceGroup_Name>/<VNET_Name>/\<SubnetName> | NIC_s와 연결 된 서브넷 |
| Subnet1_s | <ResourceGroup_Name>/<VNET_Name>/\<SubnetName> | 흐름의 원본 IP와 연결 된 서브넷 |
| Subnet2_s | <ResourceGroup_Name>/<VNET_Name>/\<SubnetName>    | 흐름의 대상 IP와 연결 된 서브넷 |
| ApplicationGateway1_s | \<SubscriptionID>/\<ResourceGroupName>/\<ApplicationGatewayName> | 흐름의 원본 IP와 연결 된 응용 프로그램 게이트웨이 | 
| ApplicationGateway2_s | \<SubscriptionID>/\<ResourceGroupName>/\<ApplicationGatewayName> | 흐름의 대상 IP와 연결 된 응용 프로그램 게이트웨이 |
| LoadBalancer1_s | \<SubscriptionID>/\<ResourceGroupName>/\<LoadBalancerName> | 흐름의 원본 IP와 연결 된 부하 분산 장치 |
| LoadBalancer2_s | \<SubscriptionID>/\<ResourceGroupName>/\<LoadBalancerName> | 흐름의 대상 IP와 연결 된 부하 분산 장치 |
| LocalNetworkGateway1_s | \<SubscriptionID>/\<ResourceGroupName>/\<LocalNetworkGatewayName> | 흐름의 원본 IP와 연결 된 로컬 네트워크 게이트웨이 |
| LocalNetworkGateway2_s | \<SubscriptionID>/\<ResourceGroupName>/\<LocalNetworkGatewayName> | 흐름의 대상 IP와 연결 된 로컬 네트워크 게이트웨이 |
| ConnectionType_s | 가능한 값은 VNetPeering, VpnGateway를 및 ExpressRoute |    연결 형식 |
| ConnectionName_s | \<SubscriptionID>/\<ResourceGroupName>/\<ConnectionName> | 연결 이름 |
| ConnectingVNets_s | 가상 네트워크 이름의 공백으로 구분 된 목록 | 허브 및 스포크 토폴로지 경우 허브 가상 네트워크는 채워지지 않습니다 여기 |
| Country_s | 두 자로 된 국가 코드 (ISO 3166-1 알파-2) | 흐름 형식 ExternalPublic에 대해 채워집니다. PublicIPs_s 필드의 모든 IP 주소는 동일한 국가 코드를 공유 합니다. |
| AzureRegion_s | Azure 지역 위치 | 흐름 형식 AzurePublic에 대해 채워집니다. PublicIPs_s 필드의 모든 IP 주소는 공유 Azure 지역 |
| AllowedInFlows_d | | 허용 된 인바운드 흐름의 수입니다. 동일한 4-튜플을 공유 하는 흐름 수를 나타내는이 인바운드 흐름을 캡처한 netweork 인터페이스 | 
| DeniedInFlows_d |  | 거부 된 인바운드 흐름의 수입니다. (인바운드 흐름을 캡처된 네트워크 인터페이스) |
| AllowedOutFlows_d | | (아웃 바운드 흐름을 캡처된 네트워크 인터페이스)를 허용 된 아웃 바운드 흐름 수 |
| DeniedOutFlows_d  | | (아웃 바운드 흐름을 캡처된 네트워크 인터페이스)를 거부 된 아웃 바운드 흐름 수 |
| FlowCount_d | 사용되지 않습니다. 동일한 4-튜플 일치 하는 총 흐름입니다. 흐름 형식 ExternalPublic 및 AzurePublic 경우 수 뿐만 아니라 다양 한 PublicIP 주소에서 흐름 포함 됩니다.
| InboundPackets_d | 여기서 NSG 규칙이 적용 된 네트워크 인터페이스에서 캡처된 수신 패킷 | 버전 2의 NSG 흐름 로그 스키마에 대해서만 채워집니다. |
| OutboundPackets_d  | 여기서 NSG 규칙이 적용 된 네트워크 인터페이스에 확보 보낸 패킷 | 버전 2의 NSG 흐름 로그 스키마에 대해서만 채워집니다. |
| InboundBytes_d |  수신 위치 NSG 규칙이 적용 된 네트워크 인터페이스에 캡처된 바이트 | 버전 2의 NSG 흐름 로그 스키마에 대해서만 채워집니다. |
| OutboundBytes_d | 여기서 NSG 규칙이 적용 된 네트워크 인터페이스에서 캡처된으로 보낸 바이트 수 | 버전 2의 NSG 흐름 로그 스키마에 대해서만 채워집니다. |
| CompletedFlows_d  |  | 버전 2의 NSG 흐름 로그 스키마에 대해서만 0이 아닌 값으로 입력 됩니다. |
| PublicIPs_s | <PUBLIC_IP>\|\<FLOW_STARTED_COUNT>\|\<FLOW_ENDED_COUNT>\|\<OUTBOUND_PACKETS>\|\<INBOUND_PACKETS>\|\<OUTBOUND_BYTES>\|\<INBOUND_BYTES> | 막대로 구분 된 항목 |
    
### <a name="notes"></a>메모
    
1. AzurePublic 및 ExternalPublic 흐름의 경우 고객 PublicIPs_s 필드에서 공용 IP 주소를 채워지고 동안 VMIP_s 필드에 Azure VM IP 채워집니다 소유 합니다. 이러한 두 가지 흐름 유형에 VMIP_s 및 PublicIPs_s SrcIP_s 및 DestIP_s 필드 대신 사용 해야 합니다. AzurePublic 및 ExternalPublicIP 주소에 대 한 집계 또한 고객이 log analytics 작업 영역에 수집 된 레코드 수가 최소화 되도록 합니다. (이 필드는 곧 중단 될 예정 및에서는 사용 해야 SrcIP_ 및 DestIP_s azure VM의 원본 또는 대상 흐름에 되었는지 여부에 따라) 
1. 흐름 형식에 대 한 세부 정보: 다음 흐름 형식으로의 흐름 분류할 흐름에 관련 된 IP 주소에 따라: 
1. IntraVNet-흐름의 두 IP 주소는 동일한 Azure Virtual Network에 상주합니다. 
1. Vnet 간-흐름의 IP 주소 2 개 Azure 가상 네트워크에 상주합니다. 
1. S2S-IP 주소 중 하나를 (사이트 간) 속하는데 Azure Virtual Network VPN 게이트웨이 통해 Azure Virtual Network 또는 Expressroute에 연결 (사이트) 고객 네트워크에 속한 다른 IP 주소. 
1. P2S-(지점-사이트) VPN 게이트웨이 통해 Azure Virtual Network에 연결 (사이트) 고객 네트워크에 다른 IP 주소가 속한 하는 동안 Azure Virtual Network에 속한 IP 주소 중 하나입니다.
1. Microsoft가 소유 하는 Azure 내부 공용 IP 주소로 다른 IP 주소가 속한 하는 동안 Azure Virtual Network에 속한 IP 주소 중 하나 AzurePublic-합니다. 고객 소유 공용 IP 주소는이 흐름 형식은 포함 되지 않습니다. 예를 들어 모든 고객은 Azure 서비스 (저장소 끝점)에 트래픽을 전송 하는 VM이 흐름 형식에서 분류 되 소유 합니다. 
1. Azure Virtual Network에 속하는 IP 주소 중 하나는 ExternalPublic-다른 IP 주소는 Azure에 있지 않은 공용 IP, 트래픽 분석 처리 간격을 사용 하는 ASC 피드의 악의적인 주소로 보고 되지 않았습니다 " FlowIntervalStartTime_t"및"FlowIntervalEndTime_t"입니다. 
1. 다른 IP 주소는 Azure에서 되지 및 처리 간격에 대 한 트래픽 분석을 사용 하는 ASC 피드의 악성으로 보고 되는 공용 IP를 azure 가상 네트워크에 속한 IP 주소 중 하나로 MaliciousFlow " FlowIntervalStartTime_t"및"FlowIntervalEndTime_t"입니다. 
1. 다른 IP 주소로 RFC 1918에 정의 된 대로 개인 IP 범위에 속하는 고객 소유 사이트 또는 Azure Virtual Network 트래픽 분석 하 여 매핑할 수 없습니다 하는 동안 Azure Virtual Network에 속한 IP 주소 중 하나 UnknownPrivate-합니다.
1. 알 수 없음-IP 중 하나에 매핑할 수 없습니다 Azure에서 고객 토폴로지를 사용 하 여 흐름에서 주소 뿐만 아니라 온-프레미스 (사이트).

### <a name="next-steps"></a>다음 단계
자주 묻는 질문과 대답을 참조 하세요 [트래픽 분석 FAQ](traffic-analytics-faq.md) 기능에 대 한 세부 정보를 보려면 [트래픽 분석 설명서](traffic-analytics.md)
    


    


