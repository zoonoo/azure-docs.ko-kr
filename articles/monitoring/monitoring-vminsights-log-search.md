---
title: VM용 Azure Monitor(미리 보기)에서 로그를 쿼리하는 방법 | Microsoft Docs
description: VM용 Azure Monitor 솔루션은 메트릭 및 로그 데이터를 Log Analytics에 전달합니다. 이 문서에서는 레코드를 설명하며 샘플 쿼리가 포함되어 있습니다.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/25/2018
ms.author: magoedte
ms.openlocfilehash: 6558a888ded1c8657bef6aba886a6f7d14cb554a
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51254539"
---
# <a name="how-to-query-logs-from-azure-monitor-for-vms-preview"></a>VM용 Azure Monitor(미리 보기)에서 로그를 쿼리하는 방법
VM용 Azure Monitor는 성능 및 연결 메트릭, 컴퓨터 및 프로세스 인벤토리 데이터 및 상태 정보를 수집하여 Azure Monitor의 Log Analytics 데이터 저장소에 전달합니다.  이 데이터는 Log Analytics에서 [검색](../log-analytics/log-analytics-queries.md)에 사용할 수 있습니다. 마이그레이션 계획, 용량 분석, 검색 및 주문형 성능 문제 해결을 포함하는 시나리오에 이 데이터를 적용할 수 있습니다.

## <a name="map-records"></a>레코드 매핑
프로세스 또는 컴퓨터가 시작되거나 VM용 Azure Monitor 맵 기능에 등록된 경우 생성되는 레코드 외에도 고유한 각 컴퓨터 및 프로세스에 대해 시간당 하나의 레코드가 생성됩니다. 이러한 레코드는 다음 표의 속성을 가집니다. ServiceMapComputer_CL 이벤트의 필드 및 값은 ServiceMap Azure Resource Manager API의 컴퓨터 리소스 필드에 매핑됩니다. ServiceMapProcess_CL 이벤트의 필드 및 값은 ServiceMap Azure Resource Manager API의 프로세스 리소스 필드에 매핑됩니다. ResourceName_s 필드는 해당하는 Resource Manager 리소스의 이름 필드와 일치합니다. 

고유한 프로세스 및 컴퓨터를 식별하는 데 사용할 수 있는 내부적으로 생성된 속성이 있습니다.

- 컴퓨터: *ResourceId* 또는 *ResourceName_s*를 사용하여 Log Analytics 작업 영역 내에서 컴퓨터를 고유하게 식별합니다.
- 프로세스: *ResourceId*를 사용하여 Log Analytics 작업 영역 내에서 프로세스를 고유하게 식별합니다. *ResourceName_s*는 프로세스가 실행 중인 머신(MachineResourceName_s)의 컨텍스트 내에서 고유합니다. 

지정된 시간 범위 내에서 지정된 프로세스 및 컴퓨터에 대해 여러 레코드가 존재할 수 있으므로 쿼리는 동일한 컴퓨터 또는 프로세스에 대해 둘 이상의 레코드를 반환할 수 있습니다. 가장 최근 레코드만 포함하려면 쿼리에 "| dedup ResourceId"를 추가합니다.

### <a name="connections"></a>연결
연결 메트릭은 Log Analytics - VMConnection에서 새 테이블에 작성됩니다. 이 테이블은 머신에 대한 연결 관련 정보를 제공합니다(인바운드 및 아웃바운드). 또한 연결 메트릭은 해당 기간 동안 특정 메트릭을 가져올 수 있는 방법을 제공하는 API를 사용하여 노출됩니다.  수신 대기 소켓에서의 *수락*으로 인해 발생한 TCP 연결은 인바운드되는 반면, 지정된 IP 및 포트로 *연결*되면서 생성된 연결은 아웃바운드됩니다. 연결의 방향은 **인바운드** 또는 **아웃바운드** 중 하나로 설정할 수 있는 Direction 속성으로 표현됩니다. 

이러한 테이블의 레코드는 종속성 에이전트에서 보고된 데이터에서 생성됩니다. 모든 레코드는 1분 간격의 관찰을 나타냅니다. TimeGenerated 속성은 시간 간격의 시작을 나타냅니다. 각 레코드에는 각 엔터티를 식별하는 정보가 있습니다. 즉, 해당 엔터티에 연결된 메트릭과 함께 연결 또는 포트를 포함합니다. 현재 IPv4를 통해 TCP를 사용하여 발생하는 네트워크 활동만 보고됩니다.

비용 및 복잡성을 관리하기 위해 연결 레코드는 개별 물리적 네트워크 연결을 나타내지 않습니다. 여러 물리적 네트워크 연결은 논리적 연결로 그룹화됩니다. 그런 다음, 각 테이블에 반영됩니다.  즉, *VMConnection* 테이블의 레코드는 관찰되는 개별 물리적 연결이 아닌 논리적 그룹화를 나타냅니다. 지정된 1분 간격 동안 다음 특성에 대해 동일한 값을 공유하는 물리적 네트워크 연결이 *VMConnection*의 단일 논리적 레코드에 집계됩니다. 

| 자산 | 설명 |
|:--|:--|
|방향 |연결 방향으로 값은 *인바운드* 또는 *아웃바운드*입니다. |
|컴퓨터 |컴퓨터 FQDN |
|Process |연결을 시작/수락하는 프로세스 또는 프로세스 그룹의 ID입니다. |
|SourceIp |원본의 IP 주소 |
|DestinationIp |대상의 IP 주소 |
|DestinationPort |대상의 포트 번호 |
|프로토콜 |연결에 사용되는 프로토콜입니다.  값은 *tcp*입니다. |

그룹화의 영향을 고려하기 위해 그룹화된 물리적 연결 수에 대한 정보가 다음과 같은 레코드 속성에서 제공됩니다.

| 자산 | 설명 |
|:--|:--|
|LinksEstablished |보고 기간 동안 설정된 물리적 네트워크 연결의 수 |
|LinksTerminated |보고 기간 동안 종료된 물리적 네트워크 연결의 수 |
|LinksFailed |보고 기간 동안 실패한 물리적 네트워크 연결의 수. 이 정보는 현재 아웃바운드 연결에 대해서만 사용할 수 있습니다. |
|LinksLive |보고 기간의 마지막에 시작된 물리적 네트워크 연결의 수|

#### <a name="metrics"></a>메트릭

연결 수 메트릭 외에도 지정된 논리적 연결 또는 네트워크 포트에 전송 및 수신된 데이터의 볼륨에 대한 정보도 다음과 같은 레코드 속성에 포함됩니다.

| 자산 | 설명 |
|:--|:--|
|BytesSent |보고 기간 동안 전송된 총 바이트 수 |
|BytesReceived |보고 기간 동안 수신된 총 바이트 수 |
|응답 |보고 기간 동안 관찰된 응답의 수 
|ResponseTimeMax |보고 기간 동안 관찰된 최대 응답 시간(밀리초). 값이 없는 경우 속성은 비어 있습니다.|
|ResponseTimeMin |보고 기간 동안 관찰된 최소 응답 시간(밀리초). 값이 없는 경우 속성은 비어 있습니다.|
|ResponseTimeSum |보고 기간 동안 관찰된 모든 응답 시간의 합계(밀리초). 값이 없는 경우 속성은 비어 있습니다.|

보고할 데이터의 세 번째 형식은 호출자가 원격 엔드포인트에서 처리 및 응답할 연결을 통해 전송된 요청을 대기하는 데 걸리는 시간인 응답 시간입니다. 보고된 응답 시간은 기본 응용 프로그램 프로토콜의 true 응답 시간에 대한 추정치입니다. 물리적 네트워크 연결의 원본 및 대상 끝 사이의 데이터 흐름을 관찰하여 그에 따른 추론 방식을 사용하여 계산됩니다. 개념적으로는 발신자가 요청의 마지막 바이트를 보낸 시간과 응답의 마지막 바이트가 다시 도착할 때의 시간 사이에는 차이가 있습니다. 이러한 두 타임스탬프는 지정된 물리적 연결에서 요청 및 응답 이벤트를 설명하는 데 사용됩니다. 그 차이는 단일 요청의 응답 시간을 나타냅니다. 

이 기능의 이 첫 번째 릴리스에서 해당 알고리즘은 지정된 네트워크 연결에 사용된 실제 응용 프로그램 프로토콜에 따라 각기 다른 성공도로 작동할 수도 있는 근사값입니다. 예를 들어, 현재 접근 방식은 HTTP(S)와 같은 프로토콜 기반 요청-응답에는 적합하지만, 단방향 또는 메시지 큐 기반 프로토콜에는 작동하지 않습니다.

고려할 몇 가지 중요한 사항은 다음과 같습니다.

1. 프로세스가 동일한 IP 주소에 있지만 여러 네트워크 인터페이스를 통한 연결을 허용하는 경우 각 인터페이스에 대한 별도 레코드가 보고됩니다. 
2. 와일드카드 IP를 사용한 레코드에는 활동이 포함되지 않습니다. 해당 레코드는 머신에 있는 포트가 인바운드 트래픽에 대해 열려있음을 나타내기 위해 포함됩니다.
3. 자세한 정도 및 데이터 볼륨을 줄이기 위해 와일드카드 IP를 사용한 레코드는 특정 IP 주소와 일치하는 레코드(동일한 프로세스, 포트 및 프로토콜에 대해)가 있는 경우 생략됩니다. 와일드카드 IP 레코드가 생략되면 포트가 보고한 머신의 모든 인터페이스에서 노출됨을 나타내기 위해 특정 IP 주소를 가진 IsWildcardBind 레코드 속성이 “True”로 설정됩니다.
4. 특정 인터페이스에서만 바인딩된 포트는 IsWildcardBind가 “False”로 설정되어 있습니다.

#### <a name="naming-and-classification"></a>이름 지정 및 분류
편의를 위해 연결의 원격 끝 IP 주소는 RemoteIp 속성에 포함됩니다. 인바운드 연결의 경우 RemoteIp는 SourceIp와 동일하고, 아웃바운드 연결의 경우 DestinationIp와 동일합니다. RemoteDnsCanonicalNames 속성은 RemoteIp에 대한 머신에서 보고된 DNS 정식 이름을 나타냅니다. 나중에 사용하기 위해 RemoteDnsQuestions 및 RemoteClassification 속성이 예약되어 있습니다. 

#### <a name="geolocation"></a>지리적 위치
또한 *VMConnection*은 다음과 같은 레코드 속성에서 각 연결 레코드의 원격 끝에 대한 지리적 위치 정보를 포함합니다. 

| 자산 | 설명 |
|:--|:--|
|RemoteCountry |RemoteIp를 호스팅하는 국가 이름입니다.  예: *United States* |
|RemoteLatitude |지리적 위치 위도입니다. 예: *47.68* |
|RemoteLongitude |지리적 위치 경도입니다. 예: *-122.12* |

#### <a name="malicious-ip"></a>악성 IP
*VMConnection* 테이블의 모든 RemoteIp 속성을 알려진 악의적인 활동의 IP 집합에 대해 검사합니다. RemoteIp가 악성으로 식별되면 다음과 같은 속성이 다음과 같은 레코드 속성에서 채워집니다(IP가 악성으로 간주되지 않으면 비어 있음).

| 자산 | 설명 |
|:--|:--|
|MaliciousIp |RemoteIp 주소 |
|IndicatorThreadType |검색된 위협 표시기가 *Botnet*, *C2*, *CryptoMining*, *Darknet*, *DDos*, *MaliciousUrl*, *Malware*, *Phishing*, *Proxy*, *PUA*, *Watchlist* 값 중 하나입니다.   |
|설명 |관찰된 위협에 대한 설명입니다. |
|TLPLevel |TLP(Traffic Light Protocol) 수준은 정의된 *White*, *Green*, *Amber*, *Red* 값 중 하나입니다. |
|신뢰도 |값은 *0 - 100*입니다. |
|심각도 |값은 *0 - 5*입니다. 여기서 *5*는 가장 심각하고 *0*은 심각하지 않습니다. 기본값은 *3*입니다.  |
|FirstReportedDateTime |공급자가 표시기를 처음 보고한 시간입니다. |
|LastReportedDateTime |표시기가 Interflow에 의해 마지막으로 확인된 시간입니다. |
|IsActive |표시기가 *True* 또는 *False* 값으로 비활성화되었음을 나타냅니다. |
|ReportReferenceLink |지정된 관찰 가능 개체와 관련된 보고서의 링크입니다. |
|AdditionalInformation |관찰된 위협에 대한 추가 정보(해당되는 경우)를 제공합니다. |

### <a name="servicemapcomputercl-records"></a>ServiceMapComputer_CL 레코드
*ServiceMapComputer_CL* 형식의 레코드는 종속성 에이전트가 있는 서버에 대한 인벤토리 데이터를 포함합니다. 이러한 레코드는 다음 표의 속성을 가집니다.

| 자산 | 설명 |
|:--|:--|
| type | *ServiceMapComputer_CL* |
| SourceSystem | *OpsManager* |
| ResourceId | 작업 영역 내 컴퓨터에 대한 고유 식별자 |
| ResourceName_s | 작업 영역 내 컴퓨터에 대한 고유 식별자 |
| ComputerName_s | 컴퓨터 FQDN |
| Ipv4Addresses_s | 서버의 IPv4 주소 목록 |
| Ipv6Addresses_s | 서버의 IPv6 주소 목록 |
| DnsNames_s | DNS 이름 배열 |
| OperatingSystemFamily_s | Windows 또는 Linux |
| OperatingSystemFullName_s | 운영 체제의 전체 이름  |
| Bitness_s | 컴퓨터의 비트(32비트 또는 64비트)  |
| PhysicalMemory_d | 실제 메모리(MB) |
| Cpus_d | CPU 수 |
| CPUSpeed_d | CPU 속도(MHz)|
| VirtualizationState_s | *unknown*, *physical*, *virtual*, *hypervisor* |
| VirtualMachineType_s | *hyperv*, *vmware* 등 |
| VirtualMachineNativeMachineId_g | 해당 하이퍼바이저에 의해 할당된 VM ID |
| VirtualMachineName_s | VM 이름 |
| BootTime_t | 부팅 시간 |

### <a name="servicemapprocesscl-type-records"></a>ServiceMapProcess_CL 형식 레코드
*ServiceMapProcess_CL* 형식의 레코드는 종속성 에이전트가 있는 서버에서 TCP 연결 프로세스에 대한 인벤토리 데이터를 포함합니다. 이러한 레코드는 다음 표의 속성을 가집니다.

| 자산 | 설명 |
|:--|:--|
| type | *ServiceMapProcess_CL* |
| SourceSystem | *OpsManager* |
| ResourceId | 작업 영역 내 프로세스에 대한 고유 식별자 |
| ResourceName_s | 프로세스를 실행 중인 컴퓨터 내의 프로세스에 대한 고유 식별자|
| MachineResourceName_s | 컴퓨터의 리소스 이름 |
| ExecutableName_s | 프로세스 실행 파일의 이름 |
| StartTime_t | 프로세스 풀 시작 시간 |
| FirstPid_d | 프로세스 풀의 첫 번째 PID |
| Description_s | 프로세스 설명 |
| CompanyName_s | 회사의 이름 |
| InternalName_s | 내부 이름 |
| ProductName_s | 제품 이름 |
| ProductVersion_s | 제품 버전 |
| FileVersion_s | 파일 버전 |
| CommandLine_s | 명령줄 |
| ExecutablePath _s | 실행 파일 경로 |
| WorkingDirectory_s | 작업 디렉터리 |
| 사용자 이름 | 프로세스를 실행 중인 계정 |
| UserDomain | 프로세스를 실행 중인 도메인 |

## <a name="sample-log-searches"></a>샘플 로그 검색

### <a name="list-all-known-machines"></a>알려진 모든 컴퓨터 나열
`ServiceMapComputer_CL | summarize arg_max(TimeGenerated, *) by ResourceId`

### <a name="list-the-physical-memory-capacity-of-all-managed-computers"></a>모든 관리되는 컴퓨터의 실제 메모리 용량을 나열합니다.
`ServiceMapComputer_CL | summarize arg_max(TimeGenerated, *) by ResourceId | project PhysicalMemory_d, ComputerName_s`

### <a name="list-computer-name-dns-ip-and-os"></a>컴퓨터 이름, DNS, IP 및 OS를 나열합니다.
`ServiceMapComputer_CL | summarize arg_max(TimeGenerated, *) by ResourceId | project ComputerName_s, OperatingSystemFullName_s, DnsNames_s, Ipv4Addresses_s`

### <a name="find-all-processes-with-sql-in-the-command-line"></a>명령줄에서 "sql"로 모든 프로세스 찾기
`ServiceMapProcess_CL | where CommandLine_s contains_cs "sql" | summarize arg_max(TimeGenerated, *) by ResourceId`

### <a name="find-a-machine-most-recent-record-by-resource-name"></a>리소스 이름으로 컴퓨터(가장 최근 레코드) 찾기
`search in (ServiceMapComputer_CL) "m-4b9c93f9-bc37-46df-b43c-899ba829e07b" | summarize arg_max(TimeGenerated, *) by ResourceId`

### <a name="find-a-machine-most-recent-record-by-ip-address"></a>IP 주소로 컴퓨터(가장 최근 레코드) 찾기
`search in (ServiceMapComputer_CL) "10.229.243.232" | summarize arg_max(TimeGenerated, *) by ResourceId`

### <a name="list-all-known-processes-on-a-specified-machine"></a>특정 컴퓨터의 알려진 프로세스 모두 나열
`ServiceMapProcess_CL | where MachineResourceName_s == "m-559dbcd8-3130-454d-8d1d-f624e57961bc" | summarize arg_max(TimeGenerated, *) by ResourceId`

### <a name="list-all-computers-running-sql"></a>SQL을 실행하는 모든 컴퓨터를 나열합니다.
`ServiceMapComputer_CL | where ResourceName_s in ((search in (ServiceMapProcess_CL) "\*sql\*" | distinct MachineResourceName_s)) | distinct ComputerName_s`

### <a name="list-all-unique-product-versions-of-curl-in-my-datacenter"></a>내 데이터 센터에서 curl의 고유한 제품 버전 모두 나열
`ServiceMapProcess_CL | where ExecutableName_s == "curl" | distinct ProductVersion_s`

### <a name="create-a-computer-group-of-all-computers-running-centos"></a>CentOS를 실행하는 모든 컴퓨터의 컴퓨터 그룹 만들기
`ServiceMapComputer_CL | where OperatingSystemFullName_s contains_cs "CentOS" | distinct ComputerName_s`

### <a name="summarize-the-outbound-connections-from-a-group-of-machines"></a>머신 그룹에서 아웃바운드 연결 요약
```
// the machines of interest
let machines = datatable(m: string) ["m-82412a7a-6a32-45a9-a8d6-538354224a25"];
// map of ip to monitored machine in the environment
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
// all connections to/from the machines of interest
let out=materialize(VMConnection
| where Machine in (machines)
| summarize arg_max(TimeGenerated, *) by ConnectionId);
// connections to localhost augmented with RemoteMachine
let local=out
| where RemoteIp startswith "127."
| project ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine=Machine;
// connections not to localhost augmented with RemoteMachine
let remote=materialize(out
| where RemoteIp !startswith "127."
| join kind=leftouter (ips) on $left.RemoteIp == $right.ips
| summarize by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine=MonitoredMachine);
// the remote machines to/from which we have connections
let remoteMachines = remote | summarize by RemoteMachine;
// all augmented connections
(local)
| union (remote)
//Take all outbound records but only inbound records that come from either //unmonitored machines or monitored machines not in the set for which we are computing dependencies.
| where Direction == 'outbound' or (Direction == 'inbound' and RemoteMachine !in (machines))
| summarize by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine
// identify the remote port
| extend RemotePort=iff(Direction == 'outbound', DestinationPort, 0)
// construct the join key we'll use to find a matching port
| extend JoinKey=strcat_delim(':', RemoteMachine, RemoteIp, RemotePort, Protocol)
// find a matching port
| join kind=leftouter (VMBoundPort 
| where Machine in (remoteMachines) 
| summarize arg_max(TimeGenerated, *) by PortId 
| extend JoinKey=strcat_delim(':', Machine, Ip, Port, Protocol)) on JoinKey
// aggregate the remote information
| summarize Remote=makeset(iff(isempty(RemoteMachine), todynamic('{}'), pack('Machine', RemoteMachine, 'Process', Process1, 'ProcessName', ProcessName1))) by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol
```

## <a name="next-steps"></a>다음 단계
* Log Analytics에서 처음 쿼리를 작성하는 경우 Azure Portal에서 [Log Analytics 페이지를 사용하는 방법](../log-analytics/query-language/get-started-analytics-portal.md)을 검토하세요.
* [검색 쿼리 작성](../log-analytics/query-language/search-queries.md)에 대해 알아봅니다.
