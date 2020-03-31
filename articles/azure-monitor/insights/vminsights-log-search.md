---
title: VM에 대 한 Azure 모니터에서 로그를 쿼리 하는 방법
description: VM용 Azure Monitor 솔루션은 메트릭 및 로그 데이터를 수집하고 이 문서에서는 레코드를 설명하고 샘플 쿼리를 포함합니다.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: 71258b04bad9a7aec4e86564d51d1d6f3f8cac76
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80283806"
---
# <a name="how-to-query-logs-from-azure-monitor-for-vms"></a>VM용 Azure Monitor에서 로그를 쿼리하는 방법

VM용 Azure Monitor는 성능 및 연결 메트릭, 컴퓨터 및 프로세스 인벤토리 데이터, 상태 정보를 수집하고 Azure 모니터의 로그 분석 작업 영역으로 전달합니다.  이 데이터는 Azure 모니터에서 [쿼리에](../../azure-monitor/log-query/log-query-overview.md) 사용할 수 있습니다. 마이그레이션 계획, 용량 분석, 검색 및 주문형 성능 문제 해결을 포함하는 시나리오에 이 데이터를 적용할 수 있습니다.

## <a name="map-records"></a>레코드 매핑

프로세스 또는 컴퓨터가 시작되거나 VM용 Azure Monitor 맵 기능에 등록된 경우 생성되는 레코드 외에도 고유한 각 컴퓨터 및 프로세스에 대해 시간당 하나의 레코드가 생성됩니다. 이러한 레코드는 다음 표의 속성을 가집니다. ServiceMapComputer_CL 이벤트의 필드 및 값은 ServiceMap Azure Resource Manager API의 컴퓨터 리소스 필드에 매핑됩니다. ServiceMapProcess_CL 이벤트의 필드 및 값은 ServiceMap Azure Resource Manager API의 프로세스 리소스 필드에 매핑됩니다. ResourceName_s 필드는 해당하는 Resource Manager 리소스의 이름 필드와 일치합니다. 

고유한 프로세스 및 컴퓨터를 식별하는 데 사용할 수 있는 내부적으로 생성된 속성이 있습니다.

- 컴퓨터: *ResourceId* 또는 *ResourceName_s*를 사용하여 Log Analytics 작업 영역 내에서 컴퓨터를 고유하게 식별합니다.
- 프로세스: *ResourceId*를 사용하여 Log Analytics 작업 영역 내에서 프로세스를 고유하게 식별합니다. *ResourceName_s*는 프로세스가 실행 중인 머신(MachineResourceName_s)의 컨텍스트 내에서 고유합니다. 

지정된 시간 범위 내에서 지정된 프로세스 및 컴퓨터에 대해 여러 레코드가 존재할 수 있으므로 쿼리는 동일한 컴퓨터 또는 프로세스에 대해 둘 이상의 레코드를 반환할 수 있습니다. 가장 최근 레코드만 포함하려면 `| summarize arg_max(TimeGenerated, *) by ResourceId` 쿼리에 추가합니다.

### <a name="connections-and-ports"></a>연결 및 포트

연결 메트릭 기능은 Azure 모니터 로그(VMConnection 및 VMBoundPort)에 두 개의 새 테이블을 소개합니다. 이 표는 컴퓨터(인바운드 및 아웃바운드)의 연결과 해당 컴퓨터에서 열려/활성화된 서버 포트에 대한 정보를 제공합니다. 연결메트릭은 시간 기간 동안 특정 메트릭을 가져오는 수단을 제공하는 API를 통해서도 노출됩니다. 수신 대기 소켓에서 *수락하여* 발생하는 TCP 연결은 인바운드이며 지정된 IP 및 포트에 *연결하여* 만든 연결은 아웃바운드입니다. 연결의 방향은 **인바운드** 또는 **아웃바운드** 중 하나로 설정할 수 있는 Direction 속성으로 표현됩니다. 

이러한 테이블의 레코드는 종속성 에이전트에서 보고한 데이터에서 생성됩니다. 모든 레코드는 1분 간격에 대한 관찰을 나타냅니다. TimeGenerated 속성은 시간 간격의 시작을 나타냅니다. 각 레코드에는 각 엔터티를 식별하는 정보가 있습니다. 즉, 해당 엔터티에 연결된 메트릭과 함께 연결 또는 포트를 포함합니다. 현재 IPv4를 통해 TCP를 사용하여 발생하는 네트워크 활동만 보고됩니다. 

#### <a name="common-fields-and-conventions"></a>공통 필드 및 규칙 

다음 필드와 규칙은 VMConnection 및 VMBoundPort 모두에 적용됩니다. 

- 컴퓨터: 보고 컴퓨터의 정규화된 도메인 이름 
- AgentId: 로그 분석 에이전트가 있는 컴퓨터의 고유 식별자  
- 컴퓨터: ServiceMap에서 노출된 컴퓨터에 대한 Azure 리소스 관리자 리소스의 이름입니다. *GUID가* AgentId와 동일한 GUID인 *m-{GUID}* 형식입니다.  
- 프로세스: ServiceMap에서 노출되는 프로세스에 대한 Azure 리소스 관리자 리소스의 이름입니다. *p-{hex 문자열}* 형식입니다. 프로세스는 기계 범위 내에서 고유하며 기계 간에 고유한 프로세스 ID를 생성하고 기계 및 프로세스 필드를 결합합니다. 
- 프로세스 이름: 보고 프로세스의 실행 이름입니다.
- 모든 IP 주소는 IPv4 표준 형식의 문자열입니다(예: *13.107.3.160)* 

비용 및 복잡성을 관리하기 위해 연결 레코드는 개별 물리적 네트워크 연결을 나타내지 않습니다. 여러 물리적 네트워크 연결은 논리적 연결로 그룹화됩니다. 그런 다음, 각 테이블에 반영됩니다.  즉, *VMConnection* 테이블의 레코드는 관찰되는 개별 물리적 연결이 아닌 논리적 그룹화를 나타냅니다. 지정된 1분 간격 동안 다음 특성에 대해 동일한 값을 공유하는 물리적 네트워크 연결이 *VMConnection*의 단일 논리적 레코드에 집계됩니다. 

| 속성 | 설명 |
|:--|:--|
|Direction |연결 방향으로 값은 *인바운드* 또는 *아웃바운드*입니다. |
|컴퓨터 |컴퓨터 FQDN |
|Process |연결을 시작/수락하는 프로세스 또는 프로세스 그룹의 ID입니다. |
|SourceIp |원본의 IP 주소 |
|DestinationIp |대상의 IP 주소 |
|DestinationPort |대상의 포트 번호 |
|프로토콜 |연결에 사용되는 프로토콜입니다.  값은 *tcp*입니다. |

그룹화의 영향을 고려하기 위해 그룹화된 물리적 연결 수에 대한 정보가 다음과 같은 레코드 속성에서 제공됩니다.

| 속성 | 설명 |
|:--|:--|
|LinksEstablished |보고 기간 동안 설정된 물리적 네트워크 연결의 수 |
|LinksTerminated |보고 기간 동안 종료된 물리적 네트워크 연결의 수 |
|LinksFailed |보고 기간 동안 실패한 물리적 네트워크 연결의 수. 이 정보는 현재 아웃바운드 연결에 대해서만 사용할 수 있습니다. |
|LinksLive |보고 기간의 마지막에 시작된 물리적 네트워크 연결의 수|

#### <a name="metrics"></a>메트릭

연결 수 메트릭 외에도 지정된 논리적 연결 또는 네트워크 포트에 전송 및 수신된 데이터의 볼륨에 대한 정보도 다음과 같은 레코드 속성에 포함됩니다.

| 속성 | 설명 |
|:--|:--|
|BytesSent |보고 기간 동안 전송된 총 바이트 수 |
|BytesReceived |보고 기간 동안 수신된 총 바이트 수 |
|응답 |보고 기간 동안 관찰된 응답의 수 
|ResponseTimeMax |보고 기간 동안 관찰된 최대 응답 시간(밀리초). 값이 없는 경우 속성은 비어 있습니다.|
|ResponseTimeMin |보고 기간 동안 관찰된 최소 응답 시간(밀리초). 값이 없는 경우 속성은 비어 있습니다.|
|ResponseTimeSum |보고 기간 동안 관찰된 모든 응답 시간의 합계(밀리초). 값이 없는 경우 속성은 비어 있습니다.|

보고할 데이터의 세 번째 형식은 호출자가 원격 엔드포인트에서 처리 및 응답할 연결을 통해 전송된 요청을 대기하는 데 걸리는 시간인 응답 시간입니다. 보고된 응답 시간은 기본 애플리케이션 프로토콜의 true 응답 시간에 대한 추정치입니다. 물리적 네트워크 연결의 원본 및 대상 끝 사이의 데이터 흐름을 관찰하여 그에 따른 추론 방식을 사용하여 계산됩니다. 개념적으로는 발신자가 요청의 마지막 바이트를 보낸 시간과 응답의 마지막 바이트가 다시 도착할 때의 시간 사이에는 차이가 있습니다. 이러한 두 타임스탬프는 지정된 물리적 연결에서 요청 및 응답 이벤트를 설명하는 데 사용됩니다. 그 차이는 단일 요청의 응답 시간을 나타냅니다. 

이 기능의 이 첫 번째 릴리스에서 해당 알고리즘은 지정된 네트워크 연결에 사용된 실제 애플리케이션 프로토콜에 따라 각기 다른 성공도로 작동할 수도 있는 근사값입니다. 예를 들어, 현재 접근 방식은 HTTP(S)와 같은 프로토콜 기반 요청-응답에는 적합하지만, 단방향 또는 메시지 큐 기반 프로토콜에는 작동하지 않습니다.

고려할 몇 가지 중요한 사항은 다음과 같습니다.

1. 프로세스가 동일한 IP 주소에 있지만 여러 네트워크 인터페이스를 통한 연결을 허용하는 경우 각 인터페이스에 대한 별도 레코드가 보고됩니다. 
2. 와일드카드 IP를 사용한 레코드에는 활동이 포함되지 않습니다. 해당 레코드는 머신에 있는 포트가 인바운드 트래픽에 대해 열려있음을 나타내기 위해 포함됩니다.
3. 자세한 정도 및 데이터 볼륨을 줄이기 위해 와일드카드 IP를 사용한 레코드는 특정 IP 주소와 일치하는 레코드(동일한 프로세스, 포트 및 프로토콜에 대해)가 있는 경우 생략됩니다. 와일드카드 IP 레코드가 생략되면 포트가 보고한 머신의 모든 인터페이스에서 노출됨을 나타내기 위해 특정 IP 주소를 가진 IsWildcardBind 레코드 속성이 “True”로 설정됩니다.
4. 특정 인터페이스에서만 바인딩된 포트에는 IsWildcardBind가 *False로*설정되어 있습니다.

#### <a name="naming-and-classification"></a>이름 지정 및 분류

편의를 위해 연결의 원격 끝 IP 주소는 RemoteIp 속성에 포함됩니다. 인바운드 연결의 경우 RemoteIp는 SourceIp와 동일하고, 아웃바운드 연결의 경우 DestinationIp와 동일합니다. RemoteDnsCanonicalNames 속성은 RemoteIp에 대한 머신에서 보고된 DNS 정식 이름을 나타냅니다. 나중에 사용하기 위해 RemoteDnsQuestions 및 RemoteClassification 속성이 예약되어 있습니다. 

#### <a name="geolocation"></a>지리적 위치

또한 *VMConnection*은 다음과 같은 레코드 속성에서 각 연결 레코드의 원격 끝에 대한 지리적 위치 정보를 포함합니다. 

| 속성 | 설명 |
|:--|:--|
|RemoteCountry |RemoteIp를 호스팅하는 국가/지역의 이름입니다.  예를 들어, *미국* |
|RemoteLatitude |지리적 위치 위도입니다. 예: *47.68* |
|RemoteLongitude |지리적 위치 경도입니다. 예: *-122.12* |

#### <a name="malicious-ip"></a>악성 IP

*VMConnection* 테이블의 모든 RemoteIp 속성을 알려진 악의적인 활동의 IP 집합에 대해 검사합니다. RemoteIp가 악성으로 식별되면 다음과 같은 속성이 다음과 같은 레코드 속성에서 채워집니다(IP가 악성으로 간주되지 않으면 비어 있음).

| 속성 | 설명 |
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

### <a name="ports"></a>포트 

들어오는 트래픽을 적극적으로 수락하거나 트래픽을 허용할 수 있지만 보고 시간 동안 유휴 상태인 컴퓨터의 포트는 VMBoundPort 테이블에 기록됩니다.  

VMBoundPort의 모든 레코드는 다음 필드로 식별됩니다. 

| 속성 | 설명 |
|:--|:--|
|Process | 포트가 연결된 프로세스(또는 프로세스 그룹)의 ID입니다.|
|Ip | 포트 IP 주소(와일드카드 IP, *0.0.0.0)* |
|포트 |포트 번호 |
|프로토콜 | 프로토콜입니다.  예를 들어 *tcp* 또는 *udp(tcp만* 현재 지원됩니다). *udp*|
 
ID 포트는 위의 다섯 개 필드에서 파생되며 PortId 속성에 저장됩니다. 이 속성을 사용하여 시간에 걸쳐 특정 포트에 대한 레코드를 빠르게 찾을 수 있습니다. 

#### <a name="metrics"></a>메트릭 

포트 레코드에는 연결된 연결을 나타내는 메트릭이 포함됩니다. 현재 다음 메트릭이 보고됩니다(각 측정항목에 대한 세부 정보는 이전 섹션에 설명되어 있음). 

- 바이트센트 및 바이트 수신 
- 링크설립, 링크종료, 링크라이브 
- 레포즈타임, 응답시간민, 응답시간맥스, 응답타임섬 

고려할 몇 가지 중요한 사항은 다음과 같습니다.

- 프로세스가 동일한 IP 주소에 있지만 여러 네트워크 인터페이스를 통한 연결을 허용하는 경우 각 인터페이스에 대한 별도 레코드가 보고됩니다.  
- 와일드카드 IP를 사용한 레코드에는 활동이 포함되지 않습니다. 해당 레코드는 머신에 있는 포트가 인바운드 트래픽에 대해 열려있음을 나타내기 위해 포함됩니다. 
- 자세한 정도 및 데이터 볼륨을 줄이기 위해 와일드카드 IP를 사용한 레코드는 특정 IP 주소와 일치하는 레코드(동일한 프로세스, 포트 및 프로토콜에 대해)가 있는 경우 생략됩니다. 와일드카드 IP 레코드를 생략하면 특정 IP 주소가 있는 레코드에 대한 *IsWildcardBind* 속성이 *True로*설정됩니다.  이는 포트가 보고 시스템의 모든 인터페이스에 노출됨을 나타냅니다. 
- 특정 인터페이스에서만 바인딩된 포트에는 IsWildcardBind가 *False로*설정되어 있습니다. 

### <a name="vmcomputer-records"></a>VM컴퓨터 레코드

*VMComputer* 유형이 있는 레코드에는 종속성 에이전트가 있는 서버에 대한 인벤토리 데이터가 있습니다. 이러한 레코드는 다음 표의 속성을 가집니다.

| 속성 | 설명 |
|:--|:--|
|TenantId | 작업 영역의 고유 식별자 |
|SourceSystem | *Insights* | 
|TimeGenerated | 레코드의 타임스탬프(UTC) |
|Computer | 컴퓨터 FQDN | 
|AgentId | 로그 분석 에이전트의 고유 ID |
|컴퓨터 | ServiceMap에서 노출된 컴퓨터에 대한 Azure 리소스 관리자 리소스의 이름입니다. *GUID가* AgentId와 동일한 GUID인 *m-{GUID}의*형식입니다. | 
|DisplayName | 표시 이름 | 
|전체 표시 이름 | 전체 표시 이름 | 
|HostName | 도메인 이름이 없는 컴퓨터이름 |
|부팅 시간 | 컴퓨터 부팅 시간(UTC) |
|TimeZone | 정규화된 표준 시간대 |
|가상화 상태 | *가상,* *하이퍼바이저,* *물리적* |
|Ipv4 주소 | IPv4 주소 배열 | 
|Ipv4서브넷 마스크 | IPv4 서브넷 마스크의 배열(Ipv4주소와 동일한 순서). |
|Ipv4Default게이트웨이 | IPv4 게이트웨이 배열 | 
|Ipv6 주소 | IPv6 주소 배열 | 
|맥 주소 | MAC 주소 배열 | 
|Dns 네임스 | 컴퓨터와 연결된 DNS 이름 배열입니다. |
|종속성 에이전트버전 | 컴퓨터에서 실행 중인 종속성 에이전트의 버전입니다. | 
|운영 체제가족 | *리눅스*, *윈도우* |
|운영 체제전체이름 | 운영 체제의 전체 이름 | 
|물리적 메모리MB | 메가바이트의 실제 메모리 | 
|Cpu | 프로세서 수 | 
|CPU 속도 | CPU 속도(MHz) | 
|가상 머신 타입 | *하이퍼 ,* *vmware,* *젠* |
|가상머신네이티브 | 해당 하이퍼바이저에 의해 할당된 VM ID | 
|가상 머신네이티브 이름 | VM 이름 |
|가상머신하이퍼바이저리드 | VM을 호스팅하는 하이퍼바이저의 고유 식별자 |
|하이퍼바이저 타입 | *하이퍼프* |
|하이퍼바이소리드 | 하이퍼바이저의 고유 ID | 
|호스팅 제공자 | *Azure* |
|_ResourceId | Azure 리소스의 고유 식별자 |
|AzureSubscriptionId | 구독을 식별하는 전역 고유 식별자 | 
|AzureResourceGroup | 컴퓨터가 구성원인 Azure 리소스 그룹의 이름입니다. |
|Azure리소스 이름 | Azure 리소스의 이름 |
|Azure위치 | Azure 리소스의 위치 |
|AzureUpdate도메인 | Azure 업데이트 도메인의 이름 |
|AzureFault도메인 | Azure 오류 도메인의 이름 |
|AzureVmId | Azure 가상 시스템의 고유 식별자 |
|Azure 크기 | Azure VM의 크기 |
|Azure이미지 게시자 | Azure VM 게시자의 이름 |
|Azure이미지 제공 | Azure VM 제공 유형의 이름 | 
|AzureImageSku | Azure VM 이미지의 SKU | 
|Azure이미지버전 | Azure VM 이미지의 버전 | 
|Azure클라우드서비스이름 | Azure 클라우드 서비스의 이름 |
|Azure클라우드서비스배포 | 클라우드 서비스를 위한 배포 ID |
|Azure클라우드서비스롤네임 | 클라우드 서비스 역할 이름 |
|Azure클라우드서비스롤타입 | 클라우드 서비스 역할 유형: *작업자* 또는 *웹* |
|Azure클라우드서비스인스턴스Id | 클라우드 서비스 역할 인스턴스 ID |
|AzureVmScaleSetName | 가상 시스템 규모 집합의 이름 |
|AzureVmScaleSet 배포 | 가상 머신 스케일 세트 배포 ID |
|AzureVmScaleSetResourceId | 가상 시스템 규모 집합 리소스의 고유 식별자입니다.|
|AzureVmScaleSet인스턴스ID | 가상 시스템 규모 집합의 고유 식별자 |
|AzureService패브릭클러스터Id | Azure 서비스 패브릭 클러스터의 고유한 식별자 | 
|AzureService 패브릭 클러스터 이름 | Azure 서비스 패브릭 클러스터의 이름 |

### <a name="vmprocess-records"></a>VMProcess 레코드

*VMProcess* 유형이 있는 레코드에는 종속성 에이전트가 있는 서버에서 TCP에 연결된 프로세스에 대한 인벤토리 데이터가 있습니다. 이러한 레코드는 다음 표의 속성을 가집니다.

| 속성 | 설명 |
|:--|:--|
|TenantId | 작업 영역의 고유 식별자 |
|SourceSystem | *Insights* | 
|TimeGenerated | 레코드의 타임스탬프(UTC) |
|Computer | 컴퓨터 FQDN | 
|AgentId | 로그 분석 에이전트의 고유 ID |
|컴퓨터 | ServiceMap에서 노출된 컴퓨터에 대한 Azure 리소스 관리자 리소스의 이름입니다. *GUID가* AgentId와 동일한 GUID인 *m-{GUID}의*형식입니다. | 
|Process | 서비스 맵 프로세스의 고유 식별자입니다. *p-{GUID}의*형태입니다. 
|실행 하기 이름 | 프로세스 실행 파일의 이름 | 
|DisplayName | 프로세스 표시 이름 |
|역할 | 프로세스 역할: *웹 서버,* *앱서버,* *데이터베이스 서버,* *ldapServer,* *smbServer* |
|그룹 | 프로세스 그룹 이름입니다. 동일한 그룹의 프로세스는 논리적으로 관련되어 있습니다(예: 동일한 제품 또는 시스템 구성 요소의 일부). |
|StartTime | 프로세스 풀 시작 시간 |
|퍼스트 피드 | 프로세스 풀의 첫 번째 PID |
|설명 | 프로세스 설명 |
|CompanyName | 회사의 이름 |
|InternalName | 내부 이름 |
|ProductName | 제품 이름 |
|ProductVersion | 제품의 버전 |
|FileVersion | 파일의 버전 |
|실행 경로 |실행 물의 경로 |
|명령줄 | 명령줄 |
|WorkingDirectory | 작업 디렉터리 |
|Services | 프로세스가 실행되는 일련의 서비스 |
|UserName | 프로세스를 실행 중인 계정 |
|UserDomain | 프로세스를 실행 중인 도메인 |
|_ResourceId | 작업 영역 내 프로세스에 대한 고유 식별자 |


## <a name="sample-map-queries"></a>맵 쿼리 샘플

### <a name="list-all-known-machines"></a>알려진 모든 컴퓨터 나열

```kusto
VMComputer | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="when-was-the-vm-last-rebooted"></a>VM이 마지막으로 재부팅된 시기

```kusto
let Today = now(); VMComputer | extend DaysSinceBoot = Today - BootTime | summarize by Computer, DaysSinceBoot, BootTime | sort by BootTime asc
```

### <a name="summary-of-azure-vms-by-image-location-and-sku"></a>이미지, 위치 및 SKU별 Azure VM 요약

```kusto
VMComputer | where AzureLocation != "" | summarize by ComputerName, AzureImageOffering, AzureLocation, AzureImageSku
```

### <a name="list-the-physical-memory-capacity-of-all-managed-computers"></a>관리되는 모든 컴퓨터의 실제 메모리 용량 나열

```kusto
VMComputer | summarize arg_max(TimeGenerated, *) by _ResourceId | project PhysicalMemoryMB, Computer
```

### <a name="list-computer-name-dns-ip-and-os"></a>컴퓨터 이름, DNS, IP 및 OS 목록

```kusto
VMComputer | summarize arg_max(TimeGenerated, *) by _ResourceId | project Computer, OperatingSystemFullName, DnsNames, Ipv4Addresses
```

### <a name="find-all-processes-with-sql-in-the-command-line"></a>명령줄에서 "sql"로 모든 프로세스 찾기

```kusto
VMComputer | where CommandLine contains_cs "sql" | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="find-a-machine-most-recent-record-by-resource-name"></a>리소스 이름으로 컴퓨터(가장 최근 레코드) 찾기

```kusto
search in (VMComputer) "m-4b9c93f9-bc37-46df-b43c-899ba829e07b" | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="find-a-machine-most-recent-record-by-ip-address"></a>IP 주소로 컴퓨터(가장 최근 레코드) 찾기

```kusto
search in (VMComputer) "10.229.243.232" | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="list-all-known-processes-on-a-specified-machine"></a>특정 컴퓨터의 알려진 프로세스 모두 나열

```kusto
VMProcess | where Machine == "m-559dbcd8-3130-454d-8d1d-f624e57961bc" | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="list-all-computers-running-sql-server"></a>SQL Server를 실행하는 모든 컴퓨터 나열

```kusto
VMComputer | where AzureResourceName in ((search in (VMProcess) "\*sql\*" | distinct Machine)) | distinct Computer
```

### <a name="list-all-unique-product-versions-of-curl-in-my-datacenter"></a>내 데이터 센터에서 curl의 고유한 제품 버전 모두 나열

```kusto
VMProcess | where ExecutableName == "curl" | distinct ProductVersion
```

### <a name="create-a-computer-group-of-all-computers-running-centos"></a>CentOS를 실행하는 모든 컴퓨터의 컴퓨터 그룹 만들기

```kusto
VMComputer | where OperatingSystemFullName contains_cs "CentOS" | distinct ComputerName
```

### <a name="bytes-sent-and-received-trends"></a>보내고 받은 바이트 수 추세

```kusto
VMConnection | summarize sum(BytesSent), sum(BytesReceived) by bin(TimeGenerated,1hr), Computer | order by Computer desc | render timechart
```

### <a name="which-azure-vms-are-transmitting-the-most-bytes"></a>가장 많은 바이트를 전송하는 Azure VM

```kusto
VMConnection | join kind=fullouter(VMComputer) on $left.Computer == $right.Computer | summarize count(BytesSent) by Computer, AzureVMSize | sort by count_BytesSent desc
```

### <a name="link-status-trends"></a>연결 상태 추세

```kusto
VMConnection | where TimeGenerated >= ago(24hr) | where Computer == "acme-demo" | summarize dcount(LinksEstablished), dcount(LinksLive), dcount(LinksFailed), dcount(LinksTerminated) by bin(TimeGenerated, 1h) | render timechart
```

### <a name="connection-failures-trend"></a>연결 오류 추세

```kusto
VMConnection | where Computer == "acme-demo" | extend bythehour = datetime_part("hour", TimeGenerated) | project bythehour, LinksFailed | summarize failCount = count() by bythehour | sort by bythehour asc | render timechart
```

### <a name="bound-ports"></a>바운드 포트

```kusto
VMBoundPort
| where TimeGenerated >= ago(24hr)
| where Computer == 'admdemo-appsvr'
| distinct Port, ProcessName
```

### <a name="number-of-open-ports-across-machines"></a>컴퓨터 간 열린 포트 수

```kusto
VMBoundPort
| where Ip != "127.0.0.1"
| summarize by Computer, Machine, Port, Protocol
| summarize OpenPorts=count() by Computer, Machine
| order by OpenPorts desc
```

### <a name="score-processes-in-your-workspace-by-the-number-of-ports-they-have-open"></a>열려 있는 포트 수에 따라 작업 공간의 프로세스 점수 매기기

```kusto
VMBoundPort
| where Ip != "127.0.0.1"
| summarize by ProcessName, Port, Protocol
| summarize OpenPorts=count() by ProcessName
| order by OpenPorts desc
```

### <a name="aggregate-behavior-for-each-port"></a>각 포트에 대한 집계 동작

그런 다음 이 쿼리를 사용하여 활동별 포트 점수 매기기(예: 인바운드/아웃바운드 트래픽이 가장 많은 포트, 대부분의 연결이 있는 포트)를 사용할 수 있습니다.
```kusto
// 
VMBoundPort
| where Ip != "127.0.0.1"
| summarize BytesSent=sum(BytesSent), BytesReceived=sum(BytesReceived), LinksEstablished=sum(LinksEstablished), LinksTerminated=sum(LinksTerminated), arg_max(TimeGenerated, LinksLive) by Machine, Computer, ProcessName, Ip, Port, IsWildcardBind
| project-away TimeGenerated
| order by Machine, Computer, Port, Ip, ProcessName
```

### <a name="summarize-the-outbound-connections-from-a-group-of-machines"></a>머신 그룹에서 아웃바운드 연결 요약

```kusto
// the machines of interest
let machines = datatable(m: string) ["m-82412a7a-6a32-45a9-a8d6-538354224a25"];
// map of ip to monitored machine in the environment
let ips=materialize(VMComputer
| summarize ips=makeset(todynamic(Ipv4Addresses)) by MonitoredMachine=AzureResourceName
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

## <a name="performance-records"></a>성능 레코드
*InsightsMetrics* 유형이 있는 레코드에는 가상 시스템의 게스트 운영 체제의 성능 데이터가 있습니다. 이러한 레코드는 다음 표의 속성을 가집니다.


| 속성 | 설명 |
|:--|:--|
|TenantId | 작업 영역에 대한 고유 식별자 |
|SourceSystem | *Insights* | 
|TimeGenerated | 값이 수집된 시간(UTC) |
|Computer | 컴퓨터 FQDN | 
|원본 | *vm.azm.ms* |
|네임스페이스 | 성능 카운터의 범주 | 
|이름 | 성능 카운터의 이름입니다. |
|Val | 수집된 값 | 
|태그들 | 레코드에 대한 관련 세부 정보입니다. 다른 레코드 유형과 함께 사용되는 태그는 아래 표를 참조하십시오.  |
|AgentId | 각 컴퓨터 에이전트에 대한 고유 식별자 |
|Type | *인사이트메트릭* |
|_Resourceid_ | 가상 시스템의 리소스 ID |

*InsightsMetrics* 테이블에 현재 수집된 성능 카운터는 다음 표에 나열됩니다.

| 네임스페이스 | 이름 | 설명 | 단위 | 태그들 |
|:---|:---|:---|:---|:---|
| Computer    | Heartbeat             | 컴퓨터 하트비트                        | | |
| 메모리      | 사용 가능한 MB           | 사용 가능한 메모리 바이트                    | 바이트          | 메모리크기MB - 총 메모리 크기|
| 네트워크     | WriteBytesPerSecond   | 초당 네트워크 쓰기 바이트            | 초당 바이트 수 | 네트워크장치Id - 장치의 ID<br>바이트 - 총 전송 바이트 |
| 네트워크     | ReadBytesPerSecond    | 초당 네트워크 읽기 바이트             | 초당 바이트 수 | 네트워크장치Id - 장치의 ID<br>바이트 - 총 수신 바이트 |
| 프로세서   | 사용률백분율 | 프로세서 사용률 백분율          | 백분율        | 총 CPU - 총 CPU |
| LogicalDisk | WritesPerSecond       | 논리 디스크 초당 쓰기            | 초당 개수 | mountId - 장치의 마운트 ID |
| LogicalDisk | 쓰기 지연 성        | 논리 디스크 쓰기 대기 시간 밀리초    | 밀리초   | mountId - 장치의 마운트 ID |
| LogicalDisk | WriteBytesPerSecond   | 초당 논리 디스크 쓰기 바이트       | 초당 바이트 수 | mountId - 장치의 마운트 ID |
| LogicalDisk | TransfersPerSecond    | 초당 논리 디스크 전송         | 초당 개수 | mountId - 장치의 마운트 ID |
| LogicalDisk | 전송지연     | 논리 디스크 전송 대기 시간 밀리초 | 밀리초   | mountId - 장치의 마운트 ID |
| LogicalDisk | ReadsPerSecond        | 초당 논리 디스크 읽기             | 초당 개수 | mountId - 장치의 마운트 ID |
| LogicalDisk | 판독시간         | 논리 디스크 읽기 대기 시간 밀리초     | 밀리초   | mountId - 장치의 마운트 ID |
| LogicalDisk | ReadBytesPerSecond    | 초당 논리 디스크 읽기 바이트        | 초당 바이트 수 | mountId - 장치의 마운트 ID |
| LogicalDisk | 프리스페이스백분율   | 논리 디스크 여유 공간 백분율        | 백분율        | mountId - 장치의 마운트 ID |
| LogicalDisk | 프리스페이스MB           | 논리 디스크 여유 공간 바이트             | 바이트          | mountId - 장치의 마운트 ID<br>diskSizeMB - 총 디스크 크기 |
| LogicalDisk | 초당 바이트 수        | 초당 논리 디스크 바이트             | 초당 바이트 수 | mountId - 장치의 마운트 ID |


## <a name="next-steps"></a>다음 단계

* Azure Monitor에서 로그 쿼리를 새로 작성하는 경우 Azure 포털에서 [로그 분석을 사용하여](../../azure-monitor/log-query/get-started-portal.md) 로그 쿼리를 작성하는 방법을 검토합니다.

* [검색 쿼리 작성](../../azure-monitor/log-query/search-queries.md)에 대해 알아봅니다.
