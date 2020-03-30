---
title: 클라우드에 의한 Azure VMware 솔루션간단 - 온-프레미스에서 CloudSimple VPN 게이트웨이로 고가용성 구성
description: 온-프레미스 환경에서 고가용성을 지원하는 CloudSimple VPN 게이트웨이로 고가용성 연결을 구성하는 방법에 대해 설명합니다.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6e3118814eacc6cc63b5db59bd7f1877c1d347dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025268"
---
# <a name="configure-a-high-availability-connection-from-on-premises-to-cloudsimple-vpn-gateway"></a>온-프레미스에서 CloudSimple VPN 게이트웨이로 고가용성 연결 구성

네트워크 관리자는 온-프레미스 환경에서 CloudSimple VPN 게이트웨이로 고가용성 IPsec 사이트 간 VPN 연결을 구성할 수 있습니다.

이 가이드에서는 IPsec 사이트 간 VPN 고가용성 연결을 위해 온-프레미스 방화벽을 구성하는 단계를 제공합니다. 자세한 단계는 온-프레미스 방화벽의 유형에 따라 다릅니다. 예를 들어 이 가이드에서는 Cisco ASA와 팔로알토 네트웍스의 두 가지 유형의 방화벽에 대한 단계를 제공합니다.

## <a name="before-you-begin"></a>시작하기 전에

온-프레미스 방화벽을 구성하기 전에 다음 작업을 완료합니다.

1. 조직에서 필요한 노드를 [프로비전하고](create-nodes.md) 하나 이상의 CloudSimple 프라이빗 클라우드를 만들수 있는지 확인합니다.
2. 온-프레미스 네트워크와 CloudSimple 프라이빗 클라우드 간에 [사이트 간 VPN 게이트웨이를 구성합니다.](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)

지원되는 1단계 및 2단계 제안에 대한 [VPN 게이트웨이 개요를](cloudsimple-vpn-gateways.md) 참조하십시오.

## <a name="configure-on-premises-cisco-asa-firewall"></a>온-프레미스 Cisco ASA 방화벽 구성

이 섹션의 지침은 Cisco ASA 버전 8.4 이상에 적용됩니다. 구성 예제에서 Cisco 적응형 보안 어플라이언스 소프트웨어 버전 9.10은 IKEv1 모드로 배포및 구성됩니다.

사이트 간 VPN이 작동하려면 온-프레미스 Cisco ASA VPN 게이트웨이의 외부 인터페이스에서 CloudSimple 기본 및 보조 공용 IP(피어 IP)에서 UDP 500/4500 및 ESP(IP 프로토콜 50)를 허용해야 합니다.

### <a name="1-configure-phase-1-ikev1"></a>1. 1단계 구성(IKEv1)

외부 인터페이스에서 1단계(IKEv1)를 사용하려면 Cisco ASA 방화벽에 다음 CLI 명령을 입력합니다.

```crypto ikev1 enable outside```

### <a name="2-create-an-ikev1-policy"></a>2. IKEv1 정책 만들기

해시, 인증, Diffie-Hellman 그룹, 수명 및 암호화에 사용할 알고리즘과 메서드를 정의하는 IKEv1 정책을 만듭니다.

```
crypto ikev1 policy 1
authentication pre-share
encryption aes-256
hash sha
group 2
lifetime 28800
```

### <a name="3-create-a-tunnel-group"></a>3. 터널 그룹 만들기

IPsec 특성 아래에 터널 그룹을 만듭니다. 사이트 간 VPN 게이트웨이를 구성할 때 설정한 피어 IP 주소와 터널 사전 공유 [키를 구성합니다.](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)

```
tunnel-group <primary peer ip> type ipsec-l2l
tunnel-group <primary peer ip> ipsec-attributes
ikev1 pre-shared-key *****

tunnel-group <secondary peer ip> type ipsec-l2l
tunnel-group <secondary peer ip> ipsec-attributes
ikev1 pre-shared-key *****
```

### <a name="4-configure-phase-2-ipsec"></a>4. 구성 단계 2(IPsec)

IPsec(2단계)을 구성하려면 암호화및 터널링할 트래픽을 정의하는 ACL(액세스 제어 목록)을 만듭니다. 다음 예에서 관심 있는 트래픽은 온-프레미스 로컬 서브넷(10.16.1.0/24)에서 사설 클라우드 원격 서브넷(192.168.0.0/24)으로 공급되는 터널에서 발생합니다. 사이트 간에 여러 서브넷이 있는 경우 ACL에 여러 항목을 포함할 수 있습니다.

Cisco ASA 버전 8.4 이상에서는 네트워크, 서브넷, 호스트 IP 주소 또는 여러 개체에 대한 컨테이너 역할을 하는 개체 또는 개체 그룹을 만들 수 있습니다. 로컬 및 원격 서브넷에 대한 개체에 대한 개체를 만들고 암호화 ACL 및 NAT 문에 사용합니다.

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

#### <a name="configure-an-access-list-for-the-traffic-of-interest"></a>관심 트래픽에 대한 액세스 목록 구성

```
access-list ipsec-acl extended permit ip object AZ_inside object CS_inside
```

### <a name="5-configure-the-transform-set"></a>5. 변환 세트 구성

키워드를 ```ikev1```포함해야 하는 변환 집합(TS)을 구성합니다. TS에 지정된 암호화 및 해시 특성은 [CloudSimple VPN 게이트웨이의 기본 구성에](cloudsimple-vpn-gateways.md)나열된 매개 변수와 일치해야 합니다.

```
crypto ipsec ikev1 transform-set devtest39 esp-aes-256 esp-sha-hmac 
```

### <a name="6-configure-the-crypto-map"></a>6. 암호화 맵 구성

다음 구성 요소를 포함하는 암호화 맵을 구성합니다.

* 피어 IP 주소
* 관심 트래픽이 포함된 정의된 ACL
* 변환 세트

```
crypto map mymap 1 set peer <primary peer ip> <secondary peer ip>
crypto map mymap 1 match address ipsec-acl
crypto map mymap 1 set ikev1 transform-set devtest39
```

### <a name="7-apply-the-crypto-map"></a>7. 암호화 맵 적용

외부 인터페이스에 암호화 맵적용:

```crypto map mymap interface outside```

### <a name="8-confirm-applicable-nat-rules"></a>8. 해당 NAT 규칙 확인

다음은 사용되는 NAT 규칙입니다. VPN 트래픽이 다른 NAT 규칙의 적용을 받지 않도록 합니다.

```nat (inside,outside) source static AZ_inside AZ_inside destination static CS_inside CS_inside```

### <a name="sample-ipsec-site-to-site-vpn-established-output-from-cisco-asa"></a>시스코 ASA에서 IPsec 사이트 간 VPN 설정 출력 샘플

1단계 출력:

![시스코 ASA 방화벽을 위한 1상 출력](media/ha-vpn-connection-cisco-phase1.png)

2단계 출력:

![시스코 ASA 방화벽을 위한 2단계 출력](media/ha-vpn-connection-cisco-phase2.png)

## <a name="configure-on-premises-palo-alto-networks-firewall"></a>온-프레미스 팔로 알토 네트웍스 방화벽 구성

이 섹션의 지침은 팔로알토 네트웍스 버전 7.1 이상에 적용됩니다. 이 구성 예제에서는 팔로알토 네트웍스 VM-시리즈 소프트웨어 버전 8.1.0이 IKEv1 모드로 배포및 구성됩니다.

사이트 간 VPN이 작동하려면 온-프레미스 팔로 알토 네트웍스 게이트웨이의 외부 인터페이스에서 CloudSimple 기본 및 보조 공용 IP(피어 IP)에서 UDP 500/4500 및 ESP(IP 프로토콜 50)를 허용해야 합니다.

### <a name="1-create-primary-and-secondary-tunnel-interfaces"></a>1. 기본 및 보조 터널 인터페이스 만들기

팔로 알토 방화벽에 로그인하고 **네트워크** > **인터페이스** > **터널** > **추가를**선택하고 다음 필드를 구성하고 **확인을**클릭합니다.

* 인터페이스 이름입니다. 첫 번째 필드는 '터널'이라는 키워드로 자동 채워집니다. 인접 필드에 1에서 9999 사이의 숫자를 입력합니다. 이 인터페이스는 온-프레미스 데이터 센터와 프라이빗 클라우드 간에 사이트 간 트래픽을 전달하는 기본 터널 인터페이스로 사용됩니다.
* 코멘트. 터널의 목적을 쉽게 식별할 수 있는 주석 입력
* 넷플로우 프로필. 기본값을 유지합니다.
* 구성. 인터페이스 할당: 가상 라우터: **기본값**선택. 
        보안 영역: 신뢰할 수 있는 LAN 트래픽의 영역을 선택합니다. 이 예제에서 LAN 트래픽에 대한 영역 의 이름은 '신뢰'입니다.
* I p v 4입니다. 추가를 **Add** 클릭하고 기본 터널 인터페이스에 할당되고 터널을 모니터링하는 데 사용되는 환경에서 겹치지 않는 /32 ip 주소를 추가합니다(나중에 설명).

이 구성은 고가용성 VPN용이므로 두 개의 터널 인터페이스(기본 인터페이스와 보조 인터페이스)가 필요합니다. 이전 단계를 반복하여 보조 터널 인터페이스를 만듭니다. 다른 터널 ID와 사용되지 않는 다른 /32 ip 주소를 선택합니다.

### <a name="2-set-up-static-routes-for-private-cloud-subnets-to-be-reached-over-the-site-to-site-vpn"></a>2. 사이트 간 VPN을 통해 사설 클라우드 서브넷에 도달할 정적 경로 설정

온-프레미스 서브넷이 CloudSimple 프라이빗 클라우드 서브넷에 도달하려면 경로가 필요합니다.

**네트워크** > **가상 라우터** > *기본* > **정적 경로** > **추가,** 다음 필드를 구성 및 **확인을**클릭합니다.

* 이름. 경로의 목적을 쉽게 식별할 수 있는 이름을 입력합니다.
* 대상. 온-프레미스에서 S2S 터널 인터페이스를 통해 도달할 CloudSimple 프라이빗 클라우드 서브넷 지정
* 표시합니다. 드롭다운에서 1단계(섹션-2)에서 만든 기본 터널 인터페이스를 선택합니다. 이 예제에서는 tunnel.20입니다.
* 다음 홉. **없음**을 선택합니다.
* 관리자 거리. 기본값을 유지합니다.
* 메트릭. 값을 1에서 65535로 입력합니다. 키는 이전 경로를 선호하는 경로 대응 보조 터널 인터페이스에 비해 기본 터널 인터페이스에 대응하는 경로에 대한 낮은 메트릭을 입력하는 것입니다. tunnel.20의 미터법 값이 20인 경우 터널.30의 미터법 값인 30이 아닌 터널.20이 바람직합니다.
* 경로 테이블입니다. 기본값을 유지합니다.
* BFD 프로필. 기본값을 유지합니다.
* 경로 모니터링. 확인되지 않은 상태로 둡니다.

이전 단계를 반복하여 보조 터널 인터페이스를 통해 보조/백업 경로로 사용할 Private Cloud 서브넷에 대한 다른 경로를 만듭니다. 이번에는 기본 경로보다 다른 터널 ID와 더 높은 메트릭을 선택합니다.

### <a name="3-define-the-cryptographic-profile"></a>3. 암호화 프로필 정의

IKEv1 1단계에서 VPN 터널을 설정하는 데 사용할 식별, 인증 및 암호화를 위한 프로토콜 및 알고리즘을 지정하는 암호화 프로필을 정의합니다.

**네트워크** > **확장 네트워크 프로필** > **IKE 암호화** > **추가를**선택하고 다음 필드를 구성하고 **확인을**클릭합니다.

* 이름. IKE 암호화 프로필의 이름을 입력합니다.
* DH 그룹. **추가를** 클릭하고 적절한 DH 그룹을 선택합니다.
* 암호화. **추가를** 클릭하고 적절한 암호화 방법을 선택합니다.
* 인증. **추가를** 클릭하고 적절한 인증 방법을 선택합니다.
* 키 수명. 기본값을 유지합니다.
* IKEv2 인증 배수. 기본값을 유지합니다.

### <a name="4-define-ike-gateways"></a>4. IKE 게이트웨이 정의

IKE 게이트웨이를 정의하여 VPN 터널의 각 끝에서 피어 간의 통신을 설정합니다.

**네트워크** > **확장 네트워크 프로필** > **IKE 게이트웨이** > **추가,** 다음 필드 구성 및 **확인을**클릭합니다.

일반 탭:

* 이름. 기본 CloudSimple VPN 피어로 피어로 피어볼 IKE 게이트웨이의 이름을 입력합니다.
* 버전. **IKEv1 전용 모드를 선택합니다.**
* 주소 유형입니다. **IPv4**를 선택합니다.
* 표시합니다. 공용 인터페이스 또는 외부 인터페이스를 선택합니다.
* 로컬 IP 주소입니다. 기본값을 유지합니다.
* 피어 IP 주소 유형입니다. **IP를**선택합니다.
* 피어 주소입니다. 기본 CloudSimple VPN 피어 IP 주소를 입력합니다.
* 인증. **사전 공유 키를**선택합니다.
* 미리 공유 된 키 / 미리 공유 된 키를 확인합니다. CloudSimple VPN 게이트웨이 키와 일치하도록 사전 공유 키를 입력합니다.
* 현지 신분증. 온-프레미스 팔로 알토 방화벽의 공용 IP 주소를 입력합니다.
* 피어 식별. 기본 CloudSimple VPN 피어 IP 주소를 입력합니다.

고급 옵션 탭:

* 수동 모드를 활성화합니다. 확인되지 않은 상태로 둡니다.
* NAT 통과를 활성화합니다. 온-프레미스 팔로 알토 방화벽이 NAT 장치 뒤에 있지 않으면 선택하지 않은 상태로 둡니다. 그렇지 않으면 확인란을 선택합니다.

IKEv1:

* 교환 모드. **기본**을 선택합니다.
* IKE 암호화 프로필. 이전에 만든 IKE 암호화 프로필을 선택합니다. 조각화 활성화 상자를 선택하지 않은 상태로 둡니다.
* 데드 피어 감지. 확인란을 선택하지 않은 상태로 둡니다.

이전 단계를 반복하여 보조 IKE 게이트웨이를 만듭니다.

### <a name="5-define-ipsec-crypto-profiles"></a>5. IPSEC 암호화 프로필 정의

**네트워크** > **확장 네트워크 프로필** > **IPSEC 암호화** > **추가,** 다음 필드를 구성하고 **확인을**클릭합니다.

* 이름. IPsec 암호화 프로필의 이름을 입력합니다.
* IPsec 프로토콜. **ESP를**선택합니다.
* 암호화. **추가를** 클릭하고 적절한 암호화 방법을 선택합니다.
* 인증. **추가를** 클릭하고 적절한 인증 방법을 선택합니다.
* DH 그룹. **아니요-pfs를 선택합니다.**
* 수명. 30분으로 설정합니다.
* 사용. 확인란을 선택하지 않은 상태로 둡니다.

이전 단계를 반복하여 보조 CloudSimple VPN 피어로 사용되는 다른 IPsec 암호화 프로필을 만듭니다. 동일한 IPSEC 암호화 프로필을 기본 및 보조 IPsec 터널 모두에 사용할 수도 있습니다(다음 절차 참조).

### <a name="6-define-monitor-profiles-for-tunnel-monitoring"></a>6. 터널 모니터링을 위한 모니터 프로파일 정의

**네트워크** > **확장 네트워크 프로필** > **모니터** > **추가,** 다음 필드 구성 및 **확인을**클릭합니다.

* 이름. 오류에 대한 사전 대응에 대한 터널 모니터링에 사용할 모니터 프로필의 이름을 입력합니다.
* 액션. **장애 조치(failover)를 선택합니다.**
* 간격. 값 **3을**입력합니다.
* 임계값. 값 **7을**입력합니다.

### <a name="7-set-up-primary-and-secondary-ipsec-tunnels"></a>7. 기본 및 보조 IPsec 터널을 설정합니다.

**네트워크** > **IPsec 터널** > **추가,** 다음 필드를 구성하고 **확인을**클릭합니다.

일반 탭:

* 이름. 기본 CloudSimple VPN 피어로 피어로 피어질 기본 IPSEC 터널의 이름을 입력합니다.
* 터널 인터페이스. 기본 터널 인터페이스를 선택합니다.
* 을 입력한 다음 기본값을 유지합니다.
* 주소 유형입니다. **IPv4**를 선택합니다.
* IKE 게이트웨이. 기본 IKE 게이트웨이를 선택합니다.
* IPsec 암호화 프로필. 기본 IPsec 프로파일을 선택합니다. **고급 옵션 표시를**선택합니다.
* 재생 보호를 활성화합니다. 기본값을 유지합니다.
* TOS 헤더를 복사합니다. 확인란을 선택하지 않은 상태로 둡니다.
* 터널 모니터. 확인란을 선택합니다.
* 대상 IP. 사이트 간 연결을 통해 허용되는 CloudSimple 프라이빗 클라우드 서브넷에 속하는 IP 주소를 입력합니다. 팔로알토의 터널 인터페이스(예: tunnel.20 - 10.64.5.2/32 및 tunnel.30 - 10.64.6.2/32)는 사이트 간 VPN을 통해 CloudSimple 프라이빗 클라우드 IP 주소에 도달할 수 있도록 합니다. 프록시 아이디에 대한 다음 구성을 참조하십시오.
* 프로필. 모니터 프로파일을 선택합니다.

프록시 아이디 탭: **IPv4** > **추가를** 클릭하고 다음을 구성합니다.

* 프록시 ID. 흥미로운 트래픽에 대한 이름을 입력합니다. 하나의 IPsec 터널 내부에 여러 개의 프록시 아이디가 운반될 수 있습니다.
* 로컬. 사이트 간 VPN을 통해 사설 클라우드 서브넷과 통신할 수 있는 온-프레미스 로컬 서브넷을 지정합니다.
* 원격. 로컬 서브넷과 통신할 수 있는 프라이빗 클라우드 원격 서브넷을 지정합니다.
* 프로토콜. **다음을 선택합니다.**

이전 단계를 반복하여 보조 CloudSimple VPN 피어에 사용할 다른 IPsec 터널을 만듭니다.

## <a name="references"></a>참조

시스코 ASA에서 NAT 구성:

<a href="https://www.cisco.com/c/en/us/td/docs/security/asa/asa84/configuration/guide/asa_84_cli_config/nat_objects.html" target="_blank">시스코 ASA 5500 시리즈 구성 가이드</a>

시스코 ASA에서 지원되는 IKEv1 및 IKEv2 속성:

<a href="https://www.cisco.com/c/en/us/td/docs/security/asa/asa90/configuration/guide/asa_90_cli_config/vpn_ike.html#21661" target="_blank">시스코 ASA 시리즈 CLI 구성 가이드</a>

버전 8.4 이상으로 시스코 ASA에서 IPsec 사이트 간 VPN 구성:

<a href="https://www.cisco.com/c/en/us/support/docs/security/asa-5500-x-series-next-generation-firewalls/119141-configure-asa-00.html#anc8" target="_blank">ASA의 ASDM 또는 CLI로 IKEv1 IPsec 사이트 간 터널 구성</a>

Azure에서 Cisco 적응형 보안 어플라이언스 가상(ASAv) 구성:

<a href="https://www.cisco.com/c/en/us/td/docs/security/asa/asa96/asav/quick-start-book/asav-96-qsg/asav-azure.html" target="_blank">시스코 어댑티브 보안 가상 어플라이언스(ASAv) 퀵스타트 가이드</a>

팔로 알토에서 프록시 아이디를 사용하여 사이트 간 VPN 구성:

[사이트 간 VPN 설정](https://docs.paloaltonetworks.com/pan-os/9-0/pan-os-admin/vpns/set-up-site-to-site-vpn#)

터널 모니터 설정:

[터널 모니터링 설정](https://docs.paloaltonetworks.com/pan-os/7-1/pan-os-admin/vpns/set-up-tunnel-monitoring.html)

IKE 게이트웨이 또는 IPsec 터널 작업:

<a href="https://docs.paloaltonetworks.com/pan-os/9-0/pan-os-admin/vpns/set-up-site-to-site-vpn/enabledisable-refresh-or-restart-an-ike-gateway-or-ipsec-tunnel#" target="_blank">IKE 게이트웨이 또는 IPsec 터널을 활성화/비활성화, 새로 고침 또는 다시 시작</a>
