---
title: Azure 네트워크 아키텍처
description: 이 문서에서는 Microsoft Azure 인프라 네트워크에 대한 일반적인 설명을 제공합니다.
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 67781f196b445c9330e0dcb1fc7d8b0a1a53cbc0
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37101688"
---
# <a name="azure-network-architecture"></a>Azure 네트워크 아키텍처
Azure 네트워크 아키텍처는 별도의 하드웨어 계층이 있는 업계 표준 코어/배포/액세스 모델의 수정된 버전을 따릅니다. 계층은 다음과 같습니다.

- 코어(데이터 센터 라우터)
- 배포(액세스 라우터 및 L2 집계) - 배포 계층은 L2 전환에서 L3 라우팅을 분리합니다.
- 액세스(L2 호스트 스위치)

네트워크 아키텍처에는 두 가지 수준의 계층 2 스위치가 있습니다. 한 계층에서 다른 계층의 트래픽을 집계하고, 계층 2는 중복을 통합하기 위해 반복됩니다. 이렇게 하면 더 유연한 VLAN 공간과 같은 이점을 제공하고 포트 크기 조정을 향상시킵니다. 아키텍처는 L2 및 L3을 구분하여 네트워크의 각기 다른 계층에서 하드웨어를 사용할 수 있게 하고, 한 계층의 결함을 최소화하여 다른 계층에 영향을 주지 않도록 합니다. 트렁크를 사용하면 L3 인프라에 대한 연결과 같은 리소스 공유가 가능합니다.

## <a name="network-configuration"></a>네트워크 구성
데이터 센터 내에서 Microsoft Azure 클러스터의 네트워크 아키텍처를 구성하는 장치는 다음과 같습니다.

- 라우터(데이터 센터, 액세스 라우터 및 경계 리프 라우터)
- 스위치(집계 및 Top of Rack 스위치)
- Digi CM
- PDU 또는 Nucleon

아래 그림에서는 클러스터 내의 Azure 네트워크 아키텍처에 대한 개요를 제공하고 있습니다. Azure에는 두 개의 개별 아키텍처가 있습니다. 일부 기존 Azure 고객 및 공유 서비스는 DLA(기본 LAN 아키텍처)에 상주하는 반면, 새로운 지역 및 가상 고객은 Q10(Quantum 10) 아키텍처를 통해 액세스됩니다. DLA 아키텍처는 활성 수동 액세스 라우터와 액세스 라우터에 적용되는 보안 ACL을 사용하는 기존의 트리 디자인입니다. Quantum 10 아키텍처는 라우터에서 ACL이 적용되지 않지만 SLB(소프트웨어 부하 분산) 또는 소프트웨어 정의 VLAN을 통한 라우팅 아래에 있는 라우터의 폐쇄형/메시 디자인입니다.

![Azure 네트워크][1]

### <a name="quantum-10-devices"></a>Quantum 10 장치
Quantum 10 디자인은 폐쇄형/메시 디자인에서 여러 장치에 걸쳐 3계층 전환 분산을 수행합니다. Q10 디자인의 이점은 기능과 기존 네트워크 인프라의 크기를 조정할 수 있는 능력이 더 많이 향상된다는 것입니다. 이 디자인은 경계 리프 라우터, 스파인 스위치 및 Top of Rack 라우터를 사용하여 여러 경로를 통해 트래픽을 클러스터에 전달함으로써 내결함성을 허용합니다. 네트워크 주소 변환과 같은 보안 서비스는 하드웨어 장치 대신 소프트웨어 부하 분산을 통해 처리됩니다.

### <a name="access-routers"></a>액세스 라우터
배포/액세스 L3 라우터(AR)는 배포 및 액세스 계층에 대한 기본 라우팅 기능을 수행합니다. 이러한 장치는 쌍으로 배포되며 서브넷에 대한 기본 게이트웨이입니다. 각 AR 쌍은 용량에 따라 여러 개의 L2 집계 스위치 쌍을 지원할 수 있습니다. 최대 개수는 장치의 용량 및 장애 도메인에 따라 달라집니다. 필요한 용량을 기준으로 한 일반적인 개수는 AR 쌍당 3개의 L2 집계 스위치 쌍입니다.

### <a name="l2-aggregation-switches"></a>L2 집계 스위치  
이러한 장치는 L2 트래픽에 대한 집계 지점 역할을 합니다. L2 패브릭에 대한 배포 계층이며, 많은 양의 트래픽을 처리할 수 있습니다. 이러한 장치는 트래픽을 집계하기 때문에 802.1q 기능이 필요하며 포트 집계 및 10GE와 같은 고대역폭 기술이 사용됩니다.

### <a name="l2-host-switches"></a>L2 호스트 스위치
호스트는 이러한 스위치에 직접 연결됩니다. 랙 탑재 스위치 또는 섀시 배포일 수 있습니다. 802.1q 표준은 하나의 VLAN을 원시 VLAN으로 지정하여 해당 VLAN을 표준(태그 없음) 이더넷 프레이밍으로 처리할 수 있게 합니다. 정상적인 상황에서 원시 VLAN의 프레임은 802.1q 트렁크 포트에서 태그가 지정되지 않은 상태로 전송 및 수신됩니다. 이 기능은 802.1q로의 마이그레이션 및 802.1q를 지원하지 않는 장치와의 호환성을 위해 설계되었습니다. 이 아키텍처에서는 네트워크 인프라만 원시 VLAN을 사용합니다.

이 아키텍처는 가능한 경우 모든 트렁크 및 L2Aggregation-L2Aggregation 트렁크에 대해 고유한 원시 VLAN이 AR 장치에 있도록 보장하는 원시 VLAN을 선택하기 위한 표준을 지정합니다. L2Aggregation-L2Host 스위치 트렁크에는 기본이 아닌 원시 VLAN이 있습니다.

### <a name="link-aggregation-8023ad"></a>링크 집계(802.3ad)
LAG(링크 집계)를 사용하면 여러 개의 개별 링크를 번들로 묶어 단일 논리 링크로 처리할 수 있습니다. 포트 채널 인터페이스를 지정하는 데 사용되는 번호는 작업 디버깅을 쉽게 하기 위해 표준화되어야 하며, 나머지 네트워크는 포트 채널의 양쪽 끝에 동일한 번호를 사용합니다. 이렇게 하려면 사용 가능한 다음 번호를 정의하는 데 사용할 포트 채널의 끝을 결정하는 표준이 필요합니다.

L2Agg-L2Host 스위치에 지정된 번호는 L2Agg 쪽에서 사용되는 포트 채널 번호입니다. 번호의 범위가 L2Host 쪽에서 더 제한적이므로 표준에서는 L2Host 쪽에서 1과 2 숫자를 사용하여 각각 "a" 쪽 및 "b" 쪽으로 가는 포트 채널을 나타냅니다.

### <a name="vlans"></a>VLAN
네트워크 아키텍처는 VLAN을 사용하여 서버를 모두 단일 브로드캐스트 도메인으로 그룹화합니다. VLAN 번호는 1-4094 번호로 지정된 VLAN을 지원하는 802.1q 표준을 따릅니다.

### <a name="customer-vlans"></a>고객 VLAN
고객은 솔루션의 분리 및 아키텍처 요구 사항을 충족하기 위해 Azure Portal을 통해 배포할 수 있는 다양한 VLAN 구현 옵션을 보유하고 있습니다. 이러한 솔루션은 가상 머신을 통해 배포됩니다. 고객 참조 아키텍처 예제는 [Azure 참조 아키텍처](https://docs.microsoft.com/azure/architecture/reference-architectures/)를 참조하세요.

### <a name="edge-architecture"></a>에지 아키텍처
Azure 데이터 센터는 고도로 중복되고 효율적으로 프로비전된 네트워크 인프라를 기반으로 합니다. Azure 데이터 센터 내의 네트워크는 "N+1"(필요한 개수+1)개의 중복 아키텍처 이상으로 구현됩니다. 데이터 센터 내 장애 조치 및 데이터 센터 간 장애 조치 기능 모두는 네트워크 및 서비스 가용성을 보장합니다. 외부적으로 데이터 센터는 여러 피어링 포인트에서 전 세계적으로 1,200개가 넘는 인터넷 서비스 공급자와 속성을 중복 연결하여 네트워크 전체에서 2,000Gbps를 초과하는 잠재적인 에지 용량을 제공하는 전용 고대역폭 네트워크 회로를 통해 서비스를 제공합니다.

Microsoft Azure 네트워크의 에지 및 액세스 계층에 있는 필터링 라우터는 패킷 수준에서 잘 설정된 보안을 제공하여 Azure에 대한 무단 연결 시도를 방지합니다. 이러한 라우터는 패킷의 실제 내용에 예상된 형식의 데이터가 포함되고 예상되는 클라이언트/서버 통신 체계를 준수하도록 하는 데 도움이 됩니다. Azure는 다음과 같은 네트워크 분리 및 액세스 제어 구성 요소로 계층적으로 구성된 아키텍처를 구현합니다.

- 에지 라우터 - 응용 프로그램 환경을 인터넷과 분리합니다. 에지 라우터는 스푸핑 방지 보호를 제공하고 ACL(액세스 제어 목록)을 사용하여 액세스를 제한하도록 설계되었습니다.
- 배포(액세스) 라우터 - 액세스 라우터는 Microsoft에서 승인한 IP 주소만 허용하고, 스푸핑 방지 및 ACL을 사용하여 설정된 연결을 제공하도록 설계되었습니다.

### <a name="a10-ddos-mitigation-architecture"></a>A10 DDOS 완화 아키텍처
서비스 거부 공격은 Microsoft 온라인 서비스의 안정성에 대한 실질적인 위협으로 계속 나타나고 있습니다. 공격의 대상이 점점 많아지고 더 정교해지고 Microsoft의 서비스가 지리적으로 다양해짐에 따라 이러한 공격의 영향을 식별하고 최소화할 수 있는 효과적인 메커니즘을 제공하는 것이 최우선 과제입니다.

다음 세부 정보는 A10 DDOS 완화 시스템이 네트워크 아키텍처 관점에서 구현되는 방식에 대해 설명하고 있습니다.  
Microsoft Azure는 자동화된 감지 및 완화를 제공하는 DCR(데이터 센터 라우터)에서 A10 네트워크 장치를 사용합니다. A10 솔루션은 Azure 네트워크 모니터링을 활용하여 네트워크 흐름 패킷을 샘플링하고 공격이 있는지 확인합니다. 공격이 감지되면 A10 장치는 공격을 완화하기 위한 스크러버로 사용됩니다. 완화된 후에는 더 깨끗해진 트래픽이 DCR에서 Azure 데이터 센터로 직접 허용됩니다. A10 솔루션은 Azure 네트워크 인프라를 보호하는 데 사용됩니다.

A10 솔루션의 DDoS 보호는 다음과 같습니다.

- UDP IPv4 및 IPv6 도배 방지
- ICMP IPv4 및 IPv6 도배 방지
- TCP IPv4 및 IPv6 도배 방지
- IPv4 및 IPv6에 대한 TCP SYN 공격 보호
- 조각화 공격

> [!NOTE]
> DDoS 보호는 기본적으로 모든 Azure 고객에게 제공됩니다.
>
>

## <a name="network-connection-rules"></a>네트워크 연결 규칙
Azure는 패킷 수준에서 보안을 제공하는 네트워크에 에지 라우터를 배포하여 Microsoft Azure에 대한 무단 연결 시도를 방지합니다. 이러한 라우터는 패킷의 실제 내용에 예상된 형식의 데이터가 포함되고 예상되는 클라이언트/서버 통신 체계를 준수하도록 보장합니다.

에지 라우터는 응용 프로그램 환경을 인터넷과 분리합니다. 에지 라우터는 스푸핑 방지 보호를 제공하고 ACL(액세스 제어 목록)을 사용하여 액세스를 제한하도록 설계되었습니다. 이러한 에지 라우터는 계층화된 ACL 접근 방식을 사용하여 에지 라우터 및 액세스 라우터를 전송할 수 있는 네트워크 프로토콜을 제한하도록 구성됩니다.

네트워크 장치는 액세스 및 에지 위치에 배치되며, 수신 및/또는 송신 필터가 적용되는 경계 지점 역할을 수행합니다.

## <a name="next-steps"></a>다음 단계
Azure 인프라를 보호하기 위해 Microsoft에서 수행하는 작업에 대해 자세히 알아보려면 다음을 참조하세요.

- [Azure 시설, 구역 및 물리적 보안](azure-physical-security.md)
- [Azure 인프라 가용성](azure-infrastructure-availability.md)
- [Azure 정보 시스템 구성 요소 및 경계](azure-infrastructure-components.md)
- [Azure 프로덕션 네트워크](azure-production-network.md)
- [Microsoft Azure SQL Database 보안 기능](azure-infrastructure-sql.md)
- [Azure 프로덕션 운영 및 관리](azure-infrastructure-operations.md)
- [Azure 인프라 모니터링](azure-infrastructure-monitoring.md)
- [Azure 인프라 무결성](azure-infrastructure-integrity.md)
- [Azure에서 고객 데이터 보호](azure-protection-of-customer-data.md)

<!--Image references-->
[1]: ./media/azure-infrastructure-network/network-arch.png
