---
title: Azure 트래픽 분석 스키마 | 마이크로 소프트 문서
description: 트래픽 분석 스키마를 이해하여 Azure 네트워크 보안 그룹 흐름 로그를 분석합니다.
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
ms.openlocfilehash: ccfbb92c27e4508595f19c2ea6900730cde609b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74666378"
---
# <a name="schema-and-data-aggregation-in-traffic-analytics"></a>트래픽 분석의 스키마 및 데이터 집계

트래픽 분석은 클라우드 네트워크의 사용자 및 애플리케이션 작업에 대한 가시성을 제공하는 클라우드 기반 솔루션입니다. 트래픽 분석은 Network Watcher NSG(네트워크 보안 그룹) 흐름 로그를 분석하여 Azure 클라우드 내 트래픽 흐름에 대한 정보를 제공합니다. 트래픽 분석을 사용하면 다음과 같은 작업을 수행할 수 있습니다.

- Azure 구독에서 이루어지는 네트워크 작업을 시각화하고 핫스폿을 식별합니다.
- 열린 포트, 인터넷 액세스를 시도하는 애플리케이션, 불량 네트워크에 연결하는 VM(가상 머신) 같은 정보를 통해 네트워크에 대한 위협을 식별하고 네트워크를 보호합니다.
- Azure 지역 및 인터넷의 트래픽 흐름 패턴을 파악하여 네트워크 성능 및 용량에 맞게 네트워크 배포를 최적화합니다.
- 네트워크에서 연결 실패의 원인이 되는 네트워크 구성 오류를 정확히 파악합니다.
- 바이트, 패킷 또는 흐름으로 네트워크 사용량을 알 수 있습니다.

### <a name="data-aggregation"></a>데이터 집계

1. "FlowIntervalStartTime_t"과 "FlowIntervalEndTime_t" 사이의 NSG의 모든 흐름 로그는 트래픽 분석에서 처리하기 전에 저장소 계정에서 1분 간격으로 Blob으로 캡처됩니다.
2. 트래픽 분석의 기본 처리 간격은 60분입니다. 즉, 60분마다 트래픽 분석에서 집계를 위해 저장소에서 Blob을 선택합니다. 선택한 처리 간격이 10분인 경우 트래픽 분석에서는 10분마다 저장소 계정에서 Blob을 선택합니다.
3. 소스 IP, 대상 IP, 대상 포트, NSG 이름, NSG 규칙, 흐름 방향 및 전송 계층 프로토콜(TCP 또는 UDP)이 동일한 흐름(참고: 소스 포트가 집계에 제외)은 트래픽 분석에 의해 단일 흐름으로 클럽베드됩니다.
4. 이 단일 레코드는 장식되어 있으며(아래 섹션의 세부 정보) 트래픽 분석으로 로그 애널리틱스에서 수집됩니다.이 프로세스는 최대 1시간까지 걸릴 수 있습니다.
5. FlowStartTime_t 필드는 "FlowIntervalStartTime_t"과 "FlowIntervalEndTime_t" 사이의 흐름 로그 처리 간격에서 이러한 집계된 흐름(동일한 4튜플)의 첫 번째 발생을 나타냅니다.
6. TA의 모든 리소스에 대해 UI에 표시된 흐름은 NSG에서 볼 수 있는 총 흐름이지만 Log Analytics 사용자는 감소된 단일 레코드만 볼 수 있습니다. 모든 흐름을 보려면 저장소에서 참조할 수 있는 blob_id 필드를 사용합니다. 해당 레코드의 총 흐름 수는 Blob에 보이는 개별 흐름과 일치합니다.

아래 쿼리는 지난 30일 동안 온-프레미스의 모든 흐름 로그를 보는 데 도움이 됩니다.
```
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FlowStartTime_t >= ago(30d) and FlowType_s == "ExternalPublic"
| project Subnet_s  
```
위에서 언급한 쿼리에서 흐름에 대한 Blob 경로를 보려면 아래 쿼리를 사용하십시오.

```
let TableWithBlobId =
(AzureNetworkAnalytics_CL
   | where SubType_s == "Topology" and ResourceType == "NetworkSecurityGroup" and DiscoveryRegion_s == Region_s and IsFlowEnabled_b
   | extend binTime = bin(TimeProcessed_t, 6h),
            nsgId = strcat(Subscription_g, "/", Name_s),
            saNameSplit = split(FlowLogStorageAccount_s, "/")
   | extend saName = iif(arraylength(saNameSplit) == 3, saNameSplit[2], '')
   | distinct nsgId, saName, binTime)
| join kind = rightouter (
   AzureNetworkAnalytics_CL
   | where SubType_s == "FlowLog"  
   | extend binTime = bin(FlowEndTime_t, 6h)
) on binTime, $left.nsgId == $right.NSGList_s  
| extend blobTime = format_datetime(todatetime(FlowIntervalStartTime_t), "yyyy MM dd hh")
| extend nsgComponents = split(toupper(NSGList_s), "/"), dateTimeComponents = split(blobTime, " ")
| extend BlobPath = strcat("https://", saName,
                        "@insights-logs-networksecuritygroupflowevent/resoureId=/SUBSCRIPTIONS/", nsgComponents[0],
                        "/RESOURCEGROUPS/", nsgComponents[1],
                        "/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/", nsgComponents[2],
                        "/y=", dateTimeComponents[0], "/m=", dateTimeComponents[1], "/d=", dateTimeComponents[2], "/h=", dateTimeComponents[3],
                        "/m=00/macAddress=", replace(@"-", "", MACAddress_s),
                        "/PT1H.json")
| project-away nsgId, saName, binTime, blobTime, nsgComponents, dateTimeComponents;

TableWithBlobId
| where SubType_s == "FlowLog" and FlowStartTime_t >= ago(30d) and FlowType_s == "ExternalPublic"
| project Subnet_s , BlobPath
```

위의 쿼리는 Blob에 직접 액세스하는 URL을 생성합니다. 장소 표시자가 있는 URL은 다음과 같습니다.

```
https://{saName}@insights-logs-networksecuritygroupflowevent/resoureId=/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroup}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json

```

### <a name="fields-used-in-traffic-analytics-schema"></a>트래픽 분석 스키마에 사용되는 필드
  > [!IMPORTANT]
  > 트래픽 분석 스키마가 2019년 8월 22일에 업데이트되었습니다. 새 스키마는 소스 및 대상 IP를 별도로 제거하여 FlowDirection 필드를 구문 분석하여 쿼리를 더 간단하게 만듭니다. </br>
  > FASchemaVersion_s 1에서 2로 업데이트되었습니다. </br>
  > 사용되지 필드: VMIP_s, Subscription_s, Region_s, NSGRules_s, Subnet_s, VM_s, NIC_s, PublicIPs_s, FlowCount_d </br>
  > 새 필드: SrcPublicIPs_s, DestPublicIPs_s, NSGRule_s </br>
  > 더 이상 사용되지 않을 필드는 2019년 11월 22일까지 사용할 수 있습니다.

트래픽 분석은 로그 애널리틱스를 기반으로 구축되므로 트래픽 분석에서 장식한 데이터에 대한 사용자 지정 쿼리를 실행하고 동일한 경고를 설정할 수 있습니다.

다음은 스키마의 필드와 스키마의 의미입니다.

| 필드 | 형식 | 주석 |
|:---   |:---    |:---  |
| TableName | AzureNetworkAnalytics_CL | 트래픽 분석 데이터에 대한 표
| SubType_s | 플로우로그 | 흐름 로그의 하위 유형입니다. "FlowLog"만 사용, SubType_s 다른 값은 제품의 내부 작업에 대한 것입니다 |
| FASchemaVersion_s |   2   | 스키마 버전입니다. NSG 흐름 로그 버전을 반영하지 않음 |
| TimeProcessed_t   | UTC의 날짜 및 시간  | 트래픽 분석이 저장소 계정에서 원시 흐름 로그를 처리한 시간 |
| FlowIntervalStartTime_t | UTC의 날짜 및 시간 |  흐름 로그 처리 간격의 시작 시간입니다. 흐름 간격이 측정되는 시간입니다. |
| FlowIntervalEndTime_t | UTC의 날짜 및 시간 | 흐름 로그 처리 간격의 종료 시간 |
| FlowStartTime_t | UTC의 날짜 및 시간 |  "FlowIntervalStartTime_t"과 "FlowIntervalEndTime_t" 사이의 흐름 로그 처리 간격에서 흐름(집계)이 처음 발생합니다. 이 흐름은 집계 논리를 기반으로 집계됩니다. |
| FlowEndTime_t | UTC의 날짜 및 시간 | "FlowIntervalStartTime_t"과 "FlowIntervalEndTime_t" 사이의 흐름 로그 처리 간격에서 흐름(집계되는 흐름)의 마지막 발생입니다. 흐름 로그 v2의 관점에서 이 필드에는 동일한 4튜플이 있는 마지막 흐름이 시작된 시간(원시 흐름 레코드에서 "B"로 표시됨)이 포함됩니다. |
| FlowType_s |  * 인트라VNet <br> * 인터V넷 <br> * S2S <br> * P2S <br> * AzurePublic <br> * 외부 공개 <br> * 악의적 인 흐름 <br> * 알 수없는 개인 <br> * 알 수 없음 | 표 아래의 노트에 있는 정의 |
| SrcIP_s | 원본 IP 주소 | AzurePublic 및 외부 공용 흐름의 경우 비어 있습니다. |
| DestIP_s | 대상 IP 주소 | AzurePublic 및 외부 공용 흐름의 경우 비어 있습니다. |
| VMIP_s | VM의 IP | AzurePublic 및 외부 공용 흐름에 사용 |
| PublicIP_s | 공용 IP 주소 | AzurePublic 및 외부 공용 흐름에 사용 |
| DestPort_d | 대상 포트 | 트래픽이 들어오는 포트 |
| L4Protocol_s  | * T <br> * U  | 전송 프로토콜. T = TCP <br> U = UDP |
| L7Protocol_s  | 프로토콜 이름 | 대상 포트에서 파생 |
| FlowDirection_s | * I = 인바운드<br> * O = 아웃바운드 | 흐름 로그에 따라 NSG의 유입/내외 흐름 방향 |
| FlowStatus_s  | * A = NSG 규칙에 의해 허용됨 <br> * D = NSG 규칙에 의해 거부됨  | 흐름 로그에 따라 NSG에서 허용/nblocked된 흐름의 상태 |
| NSGList_s | \<구독ID>\/<\/ NSG_NAME>>RESOURCEGROUP_NAME<> | 흐름과 연결된 NSG(네트워크 보안 그룹) |
| NSGRules_s | \<인덱스 값 \| \<0)>\| \<>\| \<흐름 방향 \| \<>흐름 상태에>처리byRule> NSG_RULENAME |  이 흐름을 허용하거나 거부한 NSG 규칙 |
| NSGRule_s | NSG_RULENAME |  이 흐름을 허용하거나 거부한 NSG 규칙 |
| NSGRuleType_s | * 사용자 정의 * 기본값 |   흐름에서 사용되는 NSG 규칙의 유형 |
| MACAddress_s | MAC 주소 | 흐름이 캡처된 NIC의 MAC 주소 |
| Subscription_s | Azure 가상 네트워크/네트워크 인터페이스/가상 시스템의 구독이 이 필드에 채워집니다. | FlowType = S2S, P2S, AzurePublic, 외부 공개, 악의적인 흐름 및 알 수 없는 개인 흐름 유형(한쪽만 Azure인 흐름 유형)에만 적용 가능 |
| Subscription1_s | 구독 ID | 흐름의 소스 IP가 속한 가상 네트워크/네트워크 인터페이스/가상 시스템의 구독 ID |
| Subscription2_s | 구독 ID | 흐름의 대상 IP가 속한 가상 네트워크/네트워크 인터페이스/가상 시스템의 구독 ID |
| Region_s | 흐름의 IP가 속한 가상 네트워크/네트워크 인터페이스/가상 시스템의 Azure 영역 | FlowType = S2S, P2S, AzurePublic, 외부 공개, 악의적인 흐름 및 알 수 없는 개인 흐름 유형(한쪽만 Azure인 흐름 유형)에만 적용 가능 |
| Region1_s | Azure 지역 | 흐름의 소스 IP가 속한 가상 네트워크/네트워크 인터페이스/가상 시스템의 Azure 영역 |
| Region2_s | Azure 지역 | 흐름의 대상 IP가 속한 가상 네트워크의 Azure 영역 |
| NIC_s | \<resourcegroup_Name \/ \<>네트워크인터페이스네임> |  트래픽을 보내거나 받는 VM과 관련된 NIC |
| NIC1_s | <resourcegroup_Name\<>/ 네트워크인터페이스네임> | 흐름의 소스 IP와 연결된 NIC |
| NIC2_s | <resourcegroup_Name\<>/ 네트워크인터페이스네임> | 흐름의 대상 IP와 연결된 NIC |
| VM_s | <resourcegroup_Name \/ \<>네트워크인터페이스네임> | 네트워크 인터페이스와 연결된 가상 NIC_s |
| VM1_s | <resourcegroup_Name\<>/ 가상 머신네임> | 흐름의 소스 IP와 연결된 가상 시스템 |
| VM2_s | <resourcegroup_Name\<>/ 가상 머신네임> | 흐름의 대상 IP와 연결된 가상 시스템 |
| Subnet_s | <ResourceGroup_Name>/<\<VNET_Name>/ 서브넷네임> | NIC_s 연관된 서브넷 |
| Subnet1_s | <ResourceGroup_Name>/<\<VNET_Name>/ 서브넷네임> | 흐름의 소스 IP와 연결된 서브넷 |
| Subnet2_s | <ResourceGroup_Name>/<\<VNET_Name>/ 서브넷네임>    | 흐름의 대상 IP와 연결된 서브넷 |
| ApplicationGateway1_s | \<멤버쉽ID\<>/ 리소스\<그룹 이름>/ 응용 프로그램 게이트웨이 이름> | 흐름의 소스 IP와 연결된 응용 프로그램 게이트웨이 |
| ApplicationGateway2_s | \<멤버쉽ID\<>/ 리소스\<그룹 이름>/ 응용 프로그램 게이트웨이 이름> | 흐름의 대상 IP와 연결된 응용 프로그램 게이트웨이 |
| LoadBalancer1_s | \<SubscriptionID>/\<리소스 그룹\<이름>/ 로드밸슬러 이름> | 흐름의 소스 IP와 연결된 로드 밸러블러 |
| LoadBalancer2_s | \<SubscriptionID>/\<리소스 그룹\<이름>/ 로드밸슬러 이름> | 흐름의 대상 IP와 연결된 로드 밸러블러 |
| LocalNetworkGateway1_s | \<SubscriptionID>/\<리소스 그룹\<이름>/ 로컬네트워크게이트웨이네임> | 흐름의 소스 IP와 연결된 로컬 네트워크 게이트웨이 |
| LocalNetworkGateway2_s | \<SubscriptionID>/\<리소스 그룹\<이름>/ 로컬네트워크게이트웨이네임> | 흐름의 대상 IP와 연결된 로컬 네트워크 게이트웨이 |
| ConnectionType_s | 가능한 값은 VNetPeering, VpnGateway 및 익스프레스루트입니다. |    연결 유형 |
| ConnectionName_s | \<SubscriptionID>/\<리소스 그룹\<이름>/ 연결 이름> | 연결 이름입니다. 흐름 형 P2S의 경우 이 <gateway name>형식은 _로 지정됩니다.<VPN Client IP> |
| ConnectingVNets_s | 가상 네트워크 이름의 공간 분리 목록 | 허브 및 스포크 토폴로지의 경우 허브 가상 네트워크가 여기에 채워집니다. |
| Country_s | 두 글자 국가 코드(ISO 3166-1 알파-2) | 흐름 유형 외부 Public에 대해 채워집니다. PublicIPs_s 필드의 모든 IP 주소는 동일한 국가 코드를 공유합니다. |
| AzureRegion_s | Azure 지역 위치 | 흐름 유형 AzurePublic에 대해 채워집니다. PublicIPs_s 필드의 모든 IP 주소는 Azure 지역을 공유합니다. |
| AllowedInFlows_d | | 허용된 인바운드 흐름수입니다. 이는 흐름이 캡처된 네트워크 인터페이스에 동일한 4튜플 인바운드를 공유하는 흐름 수를 나타냅니다. |
| DeniedInFlows_d |  | 거부된 인바운드 흐름수입니다. (흐름이 캡처된 네트워크 인터페이스에 인바운드) |
| AllowedOutFlows_d | | 허용된 아웃바운드 흐름 수(흐름이 캡처된 네트워크 인터페이스로 아웃바운드) |
| DeniedOutFlows_d  | | 거부된 아웃바운드 흐름 수(흐름이 캡처된 네트워크 인터페이스로 아웃바운드) |
| FlowCount_d | 사용되지 않습니다. 동일한 4튜플과 일치하는 총 흐름입니다. 흐름 형식의 경우 ExternalPublic 및 AzurePublic은 다양한 PublicIP 주소의 흐름도 포함합니다.
| InboundPackets_d | NSG 규칙이 적용된 네트워크 인터페이스에서 캡처된 패킷 | NSG 흐름 로그 스키마의 버전 2에 대해서만 채워집니다. |
| OutboundPackets_d  | NSG 규칙이 적용된 네트워크 인터페이스에서 캡처된 패킷으로 전송된 패킷 | NSG 흐름 로그 스키마의 버전 2에 대해서만 채워집니다. |
| InboundBytes_d |  NSG 규칙이 적용된 네트워크 인터페이스에서 캡처된 것으로 수신된 바이트 | NSG 흐름 로그 스키마의 버전 2에 대해서만 채워집니다. |
| OutboundBytes_d | NSG 규칙이 적용된 네트워크 인터페이스에서 캡처된 것으로 전송된 바이트 | NSG 흐름 로그 스키마의 버전 2에 대해서만 채워집니다. |
| CompletedFlows_d  |  | NSG 흐름 로그 스키마의 버전 2에 대해서만 0이 아닌 값으로 채워집니다. |
| PublicIPs_s | >INBOUND_BYTES \| \<>\| \< \| \< \| \< \<>\|INBOUND_PACKETS>OUTBOUND_PACKETS>FLOW_STARTED_COUNT PUBLIC_IP><>OUTBOUND_BYTES OUTBOUND_BYTES OUTBOUND_BYTES OUTBOUND_BYTES OUTBOUND_BYTES>>>FLOW_ENDED_COUNT>>>>>. \| \< | 막대로 구분된 항목 |
| SrcPublicIPs_s | <SOURCE_PUBLIC_IP \| \<OUTBOUND_BYTES \| \<OUTBOUND_BYTES \| \<OUTBOUND_BYTES \| \<OUTBOUND_BYTES \| \<OUTBOUND_BYTES \| \<OUTBOUND_BYTES OUTBOUND_BYTES>>FLOW_ENDED_COUNT FLOW_ENDED_COUNT FLOW_ENDED_COUNT>> INBOUND_BYTES>INBOUND_PACKETS OUTBOUND_PACKETS>FLOW_STARTED_COUNT>>. | 막대로 구분된 항목 |
| DestPublicIPs_s | <INBOUND_PACKETS \| \<>\| \<>\| \<>\| \<>\| \<INBOUND_PACKETS \|INBOUND_PACKETS INBOUND_PACKETS INBOUND_PACKETS INBOUND_PACKETS INBOUND_PACKETS INBOUND_PACKETS INBOUND_PACKETS INBOUND_PACKETS> \<INBOUND_BYTES OUTBOUND_BYTES>OUTBOUND_PACKETS>FLOW_ENDED_COUNT INBOUND_PACKETS INBOUND_PACKETS INBOUND_PACKETS INBOUND_PACKETS>INBOUND_PACKETS INBOUND_PACKETS FLOW_STARTED_COUNT INBOUND_PACKETS FLOW_STARTED_COUNT DESTINATION_PUBLIC_IP>. | 막대로 구분된 항목 |

### <a name="notes"></a>메모

1. AzurePublic 및 ExternalPublic 흐름의 경우 고객이 소유한 Azure VM IP는 VMIP_s 필드에 채워지고 공용 IP 주소는 PublicIPs_s 필드에 채워집니다. 이러한 두 흐름 형식의 경우 SrcIP_s 및 DestIP_s 필드 대신 VMIP_s PublicIPs_s 사용해야 합니다. AzurePublic 및 ExternalPublicIP 주소의 경우 고객 로그 분석 작업 영역에 수집된 레코드 수가 최소화되도록 추가집계합니다. (이 필드는 곧 더 이상 사용되지 않으며 azure VM이 흐름의 원본인지 대상인지에 따라 SrcIP_ 사용하고 DestIP_s 합니다.)
1. 흐름 유형에 대한 세부 정보: 흐름과 관련된 IP 주소를 기반으로 흐름을 다음 흐름 유형으로 분류합니다.
1. IntraVNet - 흐름의 두 IP 주소는 모두 동일한 Azure 가상 네트워크에 있습니다.
1. InterVNet - 흐름의 IP 주소는 서로 다른 두 개의 서로 다른 Azure 가상 네트워크에 있습니다.
1. S2S – (사이트 사이트 연결) 다른 IP 주소는 VPN 게이트웨이 또는 익스프레스 경로를 통해 Azure 가상 네트워크에 연결된 고객 네트워크(Site)에 속하는 반면 IP 주소 는 Azure 가상 네트워크에 속합니다.
1. P2S - (사이트 로 지점) 다른 IP 주소는 VPN 게이트웨이를 통해 Azure 가상 네트워크에 연결된 고객 네트워크(Site)에 속하는 반면 IP 주소 는 Azure 가상 네트워크에 속합니다.
1. AzurePublic - IP 주소 중 하나는 Azure 가상 네트워크에 속하지만 다른 IP 주소는 Microsoft가 소유한 Azure 내부 공용 IP 주소에 속합니다. 고객이 소유한 공용 IP 주소는 이 흐름 형식의 일부가 아닙니다. 예를 들어 Azure 서비스(저장소 끝점)로 트래픽을 보내는 고객이 소유한 모든 VM은 이 흐름 유형에서 분류됩니다.
1. ExternalPublic - IP 주소 중 하나가 Azure 가상 네트워크에 속하지만 다른 IP 주소는 Azure에 없는 공용 IP이며 트래픽 분석이 사이의 처리 간격동안 사용하는 ASC 피드에서 악의적인 것으로 보고되지 않습니다. FlowIntervalStartTime_t"와 "FlowIntervalEndTime_t".
1. [복사제= IP 주소 중 하나는 Azure 가상 네트워크에 속하지만 다른 IP 주소는 Azure에 없는 공용 IP이며 트래픽 분석이 사이의 처리 간격동안 사용하는 ASC 피드에서 악의적인 것으로 보고됩니다. FlowIntervalStartTime_t"와 "FlowIntervalEndTime_t".
1. UnknownPrivate - 다른 IP 주소는 RFC 1918에 정의된 대로 개인 IP 범위에 속하며 트래픽 분석에서 고객 소유 사이트 또는 Azure 가상 네트워크에 매핑할 수 없는 동안 IP 주소 중 하나가 Azure 가상 네트워크에 속합니다.
1. 알 수 없음 - 흐름의 IP 주소 중 하나를 Azure의 고객 토폴로지및 온-프레미스(사이트)와 매핑할 수 없습니다.
1. 일부 필드 이름은 s \_또는 \_d와 함께 추가됩니다. 이러한 원본및 대상을 나타내지 않지만 데이터 형식 문자열과 소수를 각각 나타냅니다.

### <a name="next-steps"></a>다음 단계
자주 묻는 질문에 대한 답변을 얻으려면 [트래픽 분석 FAQ를](traffic-analytics-faq.md) 참조하여 기능에 대한 세부 정보를 보려면 [트래픽 분석 설명서를](traffic-analytics.md) 참조하십시오.
