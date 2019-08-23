---
title: CloudSimple의 Azure VMware 솔루션-온-프레미스에서 CloudSimple VPN gateway로 고가용성 구성
description: 온-프레미스 환경에서 고가용성을 위해 사용 하도록 설정 된 CloudSimple VPN gateway로 고가용성 연결을 구성 하는 방법을 설명 합니다.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ea8ad80d8d12ae9ecac88bd6a3d661ca895d67f4
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69972770"
---
# <a name="configure-a-high-availability-connection-from-on-premises-to-cloudsimple-vpn-gateway"></a>온-프레미스에서 CloudSimple VPN gateway로 고가용성 연결 구성

네트워크 관리자는 온-프레미스 환경에서 CloudSimple VPN gateway로 고가용성 IPsec 사이트 간 VPN 연결을 구성할 수 있습니다.

이 가이드는 IPsec 사이트 간 VPN 고가용성 연결에 대 한 온-프레미스 방화벽을 구성 하는 단계를 제공 합니다. 자세한 단계는 온-프레미스 방화벽의 유형에 따라 다릅니다. 예를 들어이 가이드는 두 가지 유형의 방화벽에 대 한 단계를 제공 합니다. Cisco GLOBAL.ASA 및 Palo Alto Networks.

## <a name="default-configuration-for-cloudsimple-vpn-gateways"></a>CloudSimple VPN gateway에 대 한 기본 구성

기본적으로 CloudSimple VPN 게이트웨이는 다음 1 단계 및 2 단계 특성과 함께 IKEv1 모드에서 구성 됩니다. 다른 VPN 특성을 사용 하거나 IKEV1 대신 IKEv2를 사용 하려는 경우 <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">지원 요청을 엽니다</a>.

### <a name="phase-1"></a>1단계

| 매개 변수 | 값 |
|-----------|-------|
| IKE 버전 | IKEv1 |
| 암호화 | AES 256 |
| 해시 알고리즘| SHA 256 |
| Diffie-hellman 그룹 (DH 그룹) | 1 |
| 수명 시간 | 86400 초 |
| 데이터 크기 | 4GB |

### <a name="phase-2"></a>2단계

| 매개 변수 | 값 |
|-----------|-------|
| 암호화 | AES 256 |
| 해시 알고리즘| SHA 256 |
| 완벽 한 전달 보안 그룹 (PFS 그룹) | 없음 |
| 수명 시간 | 28,800초 |
| 데이터 크기 | 4GB |

## <a name="before-you-begin"></a>시작하기 전 주의 사항

온-프레미스 방화벽을 구성 하기 전에 다음 작업을 완료 합니다.

1. 조직에서 필요한 노드를 [구매](create-nodes.md) 하 고 하나 이상의 Cloudsimple 사설 클라우드를 만들었는지 확인 합니다.
2. 온-프레미스 네트워크와 CloudSimple 사설 클라우드 간에 [사이트 간 VPN gateway를 구성](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway) 합니다.

## <a name="configure-on-premises-cisco-asa-firewall"></a>온-프레미스 Cisco GLOBAL.ASA 방화벽 구성

이 섹션의 지침은 Cisco GLOBAL.ASA 버전 8.4 이상에 적용 됩니다. 구성 예제에서 Cisco 적응 보안 어플라이언스 소프트웨어 버전 9.10은 IKEv1 모드에서 배포 되 고 구성 됩니다.

사이트 간 VPN이 작동 하려면 온-프레미스 Cisco GLOBAL.ASA VPN 게이트웨이의 외부 인터페이스에서 CloudSimple 기본 및 보조 공용 IP (피어 IP)에서 UDP 500/4500 및 ESP (IP 프로토콜 50)를 허용 해야 합니다.

### <a name="1-configure-phase-1-ikev1"></a>1. 1 단계 (IKEv1) 구성

외부 인터페이스에서 1 단계 (IKEv1)를 사용 하도록 설정 하려면 Cisco GLOBAL.ASA 방화벽에서 다음 CLI 명령을 입력 합니다.

```crypto ikev1 enable outside```

### <a name="2-create-an-ikev1-policy"></a>2. IKEv1 정책 만들기

해시, 인증, Diffie-hellman 그룹, 수명 및 암호화에 사용할 알고리즘 및 메서드를 정의 하는 IKEv1 정책을 만듭니다.

```
crypto ikev1 policy 1
authentication pre-share
encryption aes-256
hash sha
group 2
lifetime 28800
```

### <a name="3-create-a-tunnel-group"></a>3. 터널 그룹 만들기

IPsec 특성 아래에 터널 그룹을 만듭니다. [사이트 간 VPN gateway를 구성할](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)때 설정한 피어 IP 주소와 터널 미리 공유한 키를 구성 합니다.

```
tunnel-group <primary peer ip> type ipsec-l2l
tunnel-group <primary peer ip> ipsec-attributes
ikev1 pre-shared-key *****

tunnel-group <secondary peer ip> type ipsec-l2l
tunnel-group <secondary peer ip> ipsec-attributes
ikev1 pre-shared-key *****
```

### <a name="4-configure-phase-2-ipsec"></a>4. 2 단계 (IPsec) 구성

2 단계 (IPsec)를 구성 하려면 암호화 및 터널링 되는 트래픽을 정의 하는 ACL (액세스 제어 목록)을 만듭니다. 다음 예제에서 관심 있는 트래픽은 온-프레미스 로컬 서브넷 (10.16.1.0/24)에서 사설 클라우드 원격 서브넷 (192.168.0.0/24)으로 시작 하는 터널에서 가져온 것입니다. 사이트 사이에 여러 서브넷이 있는 경우 ACL에 여러 항목이 포함 될 수 있습니다.

Cisco 이상 버전 8.4 이상에서는 네트워크, 서브넷, 호스트 IP 주소 또는 여러 개체에 대 한 컨테이너 역할을 하는 개체 또는 개체 그룹을 만들 수 있습니다. 원격 서브넷에 대 한 개체 및 로컬 개체를 만들고이 개체를 crypto ACL 및 NAT 문에 사용 합니다.

#### <a name="define-an-on-premises-local-subnet-as-an-object"></a>온-프레미스 로컬 서브넷을 개체로 정의

```
object network AZ_inside
subnet 10.16.1.0 255.255.255.0
```

#### <a name="define-the-cloudsimple-remote-subnet-as-an-object"></a>CloudSimple 원격 서브넷을 개체로 정의

```
object network CS_inside
subnet 192.168.0.0 255.255.255.0
```

#### <a name="configure-an-access-list-for-the-traffic-of-interest"></a>관심 있는 트래픽에 대 한 액세스 목록 구성

```
access-list ipsec-acl extended permit ip object AZ_inside object CS_inside
```

### <a name="5-configure-the-transform-set"></a>5. 변환 집합 구성

키워드 ```ikev1```와 관련 된 변환 집합 (TS)을 구성 합니다. TS에 지정 된 암호화 및 해시 특성은 [CloudSimple VPN gateway에 대 한 기본 구성](#default-configuration-for-cloudsimple-vpn-gateways)에 나열 된 매개 변수와 일치 해야 합니다.

```
crypto ipsec ikev1 transform-set devtest39 esp-aes-256 esp-sha-hmac 
```

### <a name="6-configure-the-crypto-map"></a>6. 암호화 맵 구성

다음 구성 요소가 포함 된 crypto 맵을 구성 합니다.

* 피어 IP 주소
* 관심이 있는 트래픽을 포함 하는 정의 된 ACL
* 변환 집합

```
crypto map mymap 1 set peer <primary peer ip> <secondary peer ip>
crypto map mymap 1 match address ipsec-acl
crypto map mymap 1 set ikev1 transform-set devtest39
```

### <a name="7-apply-the-crypto-map"></a>7. 암호화 맵 적용

외부 인터페이스에 crypto map을 적용 합니다.

```crypto map mymap interface outside```

### <a name="8-confirm-applicable-nat-rules"></a>8. 적용 가능한 NAT 규칙 확인

다음은 사용 되는 NAT 규칙입니다. VPN 트래픽이 다른 NAT 규칙에 적용 되지 않는지 확인 합니다.

```nat (inside,outside) source static AZ_inside AZ_inside destination static CS_inside CS_inside```

### <a name="sample-ipsec-site-to-site-vpn-established-output-from-cisco-asa"></a>Cisco GLOBAL.ASA에서 IPsec 사이트 간 VPN 설정 출력

1 단계 출력:

![Cisco GLOBAL.ASA 방화벽에 대 한 1 단계 출력](media/ha-vpn-connection-cisco-phase1.png)

2 단계 출력:

![Cisco GLOBAL.ASA 방화벽에 대 한 2 단계 출력](media/ha-vpn-connection-cisco-phase2.png)

## <a name="configure-on-premises-palo-alto-networks-firewall"></a>온-프레미스 Palo Alto Networks 방화벽 구성

이 섹션의 지침은 Palo Alto Networks 버전 7.1 이상에 적용 됩니다. 이 구성 예제에서 Palo Alto Networks VM 시리즈 Software Version 8.1.0는 IKEv1 모드에서 배포 되 고 구성 됩니다.

사이트 간 VPN이 작동 하려면 온-프레미스 Palo Alto Networks 게이트웨이의 외부 인터페이스에서 CloudSimple 기본 및 보조 공용 IP (피어 IP)에서 UDP 500/4500 및 ESP (IP 프로토콜 50)를 허용 해야 합니다.

### <a name="1-create-primary-and-secondary-tunnel-interfaces"></a>1. 기본 및 보조 터널 인터페이스 만들기

Palo Alto 방화벽에 로그인 하 고 **네트워크** > **인터페이스** > **터널** > **추가**를 선택 하 고 다음 필드를 구성 하 고 **확인**을 클릭 합니다.

* 인터페이스 이름입니다. 첫 번째 필드는 ' 터널 ' 키워드를 사용 하 여 채워집니다 합니다. 인접 필드에 1에서 9999 사이의 숫자를 입력 합니다. 이 인터페이스는 기본 터널 인터페이스로 사용 되어 온-프레미스 데이터 센터와 사설 클라우드 사이에서 사이트 간 트래픽을 전달 합니다.
* 주석의. 터널의 용도를 쉽게 식별할 수 있도록 설명 입력
* Netflow 프로필. 기본값을 그대로 둡니다.
* Config. 인터페이스 할당 대상: 가상 라우터: **기본값**을 선택 합니다. 
        보안 영역: 트러스트 된 LAN 트래픽에 대 한 영역을 선택 합니다. 이 예제에서 LAN 트래픽에 대 한 영역의 이름은 ' 신뢰 '입니다.
* IPv4. **추가** 를 클릭 하 고 사용자 환경에서 사용 되지 않은/32 ip 주소를 추가 합니다 .이 주소는 기본 터널 인터페이스에 할당 되며, 터널 모니터링에 사용 됩니다 (뒷부분에서 설명).

이 구성은 고가용성 VPN을 위한 것 이므로 다음과 같은 두 개의 터널 인터페이스가 필요 합니다. 주 데이터베이스와 보조 복제본이 하나씩 있습니다. 이전 단계를 반복 하 여 보조 터널 인터페이스를 만듭니다. 다른 터널 ID와 사용 되지 않는/32 ip 주소를 선택 합니다.

### <a name="2-set-up-static-routes-for-private-cloud-subnets-to-be-reached-over-the-site-to-site-vpn"></a>2. 사이트 간 VPN을 통해 연결할 사설 클라우드 서브넷의 고정 경로 설정

온-프레미스 서브넷이 CloudSimple 사설 클라우드 서브넷에 도달 하려면 경로가 필요 합니다.

**네트워크**가상라우터 > 기본 정적 경로추가를선택하고다음필드를구성하고확인을클릭합니다 > . >  > 

* 이름입니다. 경로의 용도를 쉽게 식별할 수 있도록 이름을 입력 합니다.
* 대상이. 온-프레미스에서 S2S 터널 인터페이스를 통해 연결할 CloudSimple 사설 클라우드 서브넷을 지정 합니다.
* 감열재. 드롭다운 목록에서-1 단계에서 만든 기본 터널 인터페이스 (섹션 2)를 선택 합니다. 이 예제에서는 터널. 20입니다.
* 다음 홉. **없음**을 선택합니다.
* 관리자 거리입니다. 기본값을 그대로 둡니다.
* 수치. 1에서 65535 사이의 값을 입력 합니다. 키는 이전 경로를 선호 하는 경로에 해당 하는 보조 터널 인터페이스와 비교할 때 기본 터널 인터페이스에 해당 하는 경로에 대 한 하위 메트릭을 입력 하는 것입니다. 터널. 20의 메트릭 값이 값이 30 인 터널에 대해 30을 설정 하는 것과 달리 20 인 경우에는 터널. 20을 선호 합니다.
* 경로 테이블입니다. 기본값을 그대로 둡니다.
* BFD 프로필. 기본값을 그대로 둡니다.
* 경로 모니터링. 선택 하지 않은 상태로 둡니다.

이전 단계를 반복 하 여 사설 클라우드 서브넷이 보조 터널 인터페이스를 통해 보조/백업 경로로 사용할 다른 경로를 만듭니다. 이번에는 기본 경로 보다 다른 터널 ID와 더 높은 메트릭을 선택 합니다.

### <a name="3-define-the-cryptographic-profile"></a>3. 암호화 프로필 정의

IKEv1 단계 1에서 VPN 터널을 설정 하는 데 사용 되는 식별, 인증 및 암호화에 대 한 프로토콜 및 알고리즘을 지정 하는 암호화 프로필을 정의 합니다.

 >  **네트워크** > 를 선택 하 고 네트워크 프로필 IKE 암호화 추가를 선택 하 고 다음 필드를 구성 하 고 확인을 클릭 합니다. > 

* 이름입니다. IKE 암호화 프로필의 이름을 입력 합니다.
* DH 그룹. **추가** 를 클릭 하 고 적절 한 DH 그룹을 선택 합니다.
* 암호화. **추가** 를 클릭 하 고 적절 한 암호화 방법을 선택 합니다.
* 인증. **추가** 를 클릭 하 고 적절 한 인증 방법을 선택 합니다.
* 키 수명. 기본값을 그대로 둡니다.
* IKEv2 인증 다중. 기본값을 그대로 둡니다.

### <a name="4-define-ike-gateways"></a>4. IKE 게이트웨이 정의

VPN 터널의 각 끝에서 피어 간의 통신을 설정 하는 IKE 게이트웨이를 정의 합니다.

**네트워크**확장네트워크 > 프로필**IKE 게이트웨이**추가를 선택 하 고 다음 필드를 구성 하 고 확인을 클릭 합니다. >  > 

일반 탭:

* 이름입니다. 기본 CloudSimple VPN 피어와 피어 링 될 IKE 게이트웨이의 이름을 입력 합니다.
* 버전. **IKEv1 only 모드**를 선택 합니다.
* 주소 유형입니다. **IPv4**를 선택 합니다.
* 감열재. 공용 연결 또는 외부 인터페이스를 선택 합니다.
* 로컬 IP 주소입니다. 기본값을 그대로 둡니다.
* 피어 IP 주소 유형입니다. **IP**를 선택 합니다.
* 피어 주소입니다. 기본 CloudSimple VPN 피어 IP 주소를 입력 합니다.
* 인증. **미리 공유한 키**를 선택 합니다.
* 미리 공유한 키/미리 공유한 키를 확인 합니다. CloudSimple VPN gateway 키와 일치 하는 사전 공유 키를 입력 합니다.
* 로컬 Id입니다. 온-프레미스 Palo Alto 방화벽의 공용 IP 주소를 입력 합니다.
* 피어 Id입니다. 기본 CloudSimple VPN 피어 IP 주소를 입력 합니다.

고급 옵션 탭:

* Passive 모드를 사용 합니다. 선택 하지 않은 상태로 둡니다.
* NAT 통과를 사용 합니다. 온-프레미스 Palo Alto 방화벽이 NAT 장치 뒤에 있지 않은 경우에는 선택 하지 않은 상태로 둡니다. 그렇지 않은 경우 확인란을 선택 합니다.

IKEv1

* Exchange 모드입니다. **주**를 선택 합니다.
* IKE 암호화 프로필. 이전에 만든 IKE 암호화 프로필을 선택 합니다. 조각화 사용 확인란을 선택 취소 된 상태로 둡니다.
* 배달 되지 않은 피어 검색. 확인란을 선택 하지 않은 상태로 둡니다.

이전 단계를 반복 하 여 보조 IKE 게이트웨이를 만듭니다.

### <a name="5-define-ipsec-crypto-profiles"></a>5. IPSEC 암호화 프로필 정의

 >  **네트워크** > 를 선택 하 고 네트워크 프로필 IPSEC 암호화 추가를 선택 하 고 다음 필드를 구성 하 고 확인을 클릭 합니다. > 

* 이름입니다. IPsec 암호화 프로필의 이름을 입력 합니다.
* IPsec 프로토콜입니다. **ESP**를 선택 합니다.
* 암호화. **추가** 를 클릭 하 고 적절 한 암호화 방법을 선택 합니다.
* 인증. **추가** 를 클릭 하 고 적절 한 인증 방법을 선택 합니다.
* DH 그룹. **Pfs 없음**을 선택 합니다.
* 수명. 30 분으로 설정 합니다.
* 활성화. 확인란을 선택 하지 않은 상태로 둡니다.

이전 단계를 반복 하 여 다른 IPsec 암호화 프로필을 만듭니다 .이 프로필은 보조 CloudSimple VPN 피어로 사용 됩니다. 기본 및 보조 IPsec 터널 모두에 동일한 IPSEC 암호화 프로필을 사용할 수도 있습니다 (다음 절차 참조).

### <a name="6-define-monitor-profiles-for-tunnel-monitoring"></a>6. 터널 모니터링에 대 한 모니터 프로필 정의

**네트워크**확장네트워크 > 프로필**모니터**추가를 선택 하 고 다음 필드를 구성 하 고 확인을 클릭 합니다. >  > 

* 이름입니다. 실패에 대 한 사전 대응 반응에 대해 터널 모니터링에 사용할 모니터 프로필의 이름을 입력 합니다.
* 조치. 장애 조치 ( **failover**)를 선택 합니다.
* 간격은. 값 **3**을 입력 합니다.
* 고대비. 값 **7**을 입력 합니다.

### <a name="7-set-up-primary-and-secondary-ipsec-tunnels"></a>7. 기본 및 보조 IPsec 터널을 설정 합니다.

**네트워크**IPsec 터널추가 > 를 선택 하 고 다음 필드를 구성 하 고 확인 > 을 클릭 합니다.

일반 탭:

* 이름입니다. 기본 CloudSimple VPN 피어와 피어 링 될 기본 IPSEC 터널의 이름을 입력 합니다.
* 터널 인터페이스입니다. 기본 터널 인터페이스를 선택 합니다.
* 입력할. 기본값을 그대로 둡니다.
* 주소 유형입니다. **IPv4**를 선택 합니다.
* IKE 게이트웨이. 기본 IKE 게이트웨이를 선택 합니다.
* IPsec 암호화 프로필. 기본 IPsec 프로필을 선택 합니다. **고급 옵션 표시**를 선택 합니다.
* 재생 보호를 사용 하도록 설정 합니다. 기본값을 그대로 둡니다.
* TOS 헤더를 복사 합니다. 확인란을 선택 하지 않은 상태로 둡니다.
* 터널 모니터. 확인란을 선택 합니다.
* 대상 IP. 사이트 간 연결을 통해 허용 되는 CloudSimple 사설 클라우드 서브넷에 속하는 IP 주소를 입력 합니다. Palo Alto의 터널 인터페이스 (예: 10.64.5.2/32 및 10.64.6.2/32)가 사이트 간 VPN을 통해 CloudSimple 사설 클라우드 IP 주소에 도달할 수 있는지 확인 합니다. 프록시 Id에 대 한 다음 구성을 참조 하십시오.
* Profile. 모니터 프로필을 선택 합니다.

프록시 Id 탭: **IPv4** > **추가** 를 클릭 하 고 다음을 구성 합니다.

* 프록시 ID입니다. 관심 있는 트래픽에 대 한 이름을 입력 합니다. 하나의 IPsec 터널 내에 여러 개의 프록시 Id가 전달 될 수 있습니다.
* 로컬. 사이트 간 VPN을 통해 사설 클라우드 서브넷과 통신할 수 있는 온-프레미스 로컬 서브넷을 지정 합니다.
* 리모콘. 로컬 서브넷과 통신할 수 있는 사설 클라우드 원격 서브넷을 지정 합니다.
* 프로토콜만. **Any**를 선택 합니다.

이전 단계를 반복 하 여 보조 CloudSimple VPN 피어에 사용할 다른 IPsec 터널을 만듭니다.

## <a name="references"></a>참조 항목

Cisco에서 NAT 구성:

<a href="https://www.cisco.com/c/en/us/td/docs/security/asa/asa84/configuration/guide/asa_84_cli_config/nat_objects.html" target="_blank">Cisco GLOBAL.ASA 5500 시리즈 구성 가이드</a>

Cisco의 지원 되는 IKEv1 및 IKEv2 특성:

<a href="https://www.cisco.com/c/en/us/td/docs/security/asa/asa90/configuration/guide/asa_90_cli_config/vpn_ike.html#21661" target="_blank">Cisco GLOBAL.ASA 시리즈 CLI 구성 가이드</a>

8\.4 이상 버전을 사용 하 여 Cisco smi-s에서 IPsec 사이트 간 VPN 구성:

<a href="https://www.cisco.com/c/en/us/support/docs/security/asa-5500-x-series-next-generation-firewalls/119141-configure-asa-00.html#anc8" target="_blank">GLOBAL.ASA에서 ASDM 또는 CLI를 사용 하 여 IKEv1 IPsec 사이트 간 터널 구성</a>

Azure에서 Cisco 적응 보안 어플라이언스 가상 (ASAv) 구성:

<a href="https://www.cisco.com/c/en/us/td/docs/security/asa/asa96/asav/quick-start-book/asav-96-qsg/asav-azure.html" target="_blank">Cisco 적응 ASAv (보안 가상 어플라이언스) 빠른 시작 가이드</a>

Palo Alto에서 프록시 Id를 사용 하 여 사이트 간 VPN 구성:

[사이트 간 VPN 설정](https://docs.paloaltonetworks.com/pan-os/9-0/pan-os-admin/vpns/set-up-site-to-site-vpn#)

터널 모니터 설정:

[터널 모니터링 설정](https://docs.paloaltonetworks.com/pan-os/7-1/pan-os-admin/vpns/set-up-tunnel-monitoring.html)

IKE 게이트웨이 또는 IPsec 터널 작업:

<a href="https://docs.paloaltonetworks.com/pan-os/9-0/pan-os-admin/vpns/set-up-site-to-site-vpn/enabledisable-refresh-or-restart-an-ike-gateway-or-ipsec-tunnel#" target="_blank">IKE 게이트웨이 또는 IPsec 터널을 사용/사용 안 함, 새로 고침 또는 다시 시작</a>
