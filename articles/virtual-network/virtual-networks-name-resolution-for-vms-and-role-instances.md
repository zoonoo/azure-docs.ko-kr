<properties 
   pageTitle="VM 및 역할 인스턴스 이름 확인"
   description="Azure IaaS, 하이브리드 솔루션, 서로 다른 클라우드 서비스, Active Directory, 자체 DNS 서버 사용 시의 이름 확인 시나리오"
   services="virtual-network"
   documentationCenter="na"
   authors="GarethBradshawMSFT"
   manager="jdial"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/02/2015"
   ms.author="joaoma" />

# VM 및 역할 인스턴스에 대한 이름 확인

IaaS, PaaS, 하이브리드 솔루션 호스팅에 Azure를 어떻게 사용할지에 따라 귀하가 만든 VM 및 역할 인스턴스가 서로 통신하도록 설정해야 할 수도 있습니다. IP 주소를 이용해 통신할 수도 있지만, 기억하기 쉽고 변경되지 않는 이름을 사용하면 보다 간편합니다.

역할 인스턴스와 VM을 Azure에 호스트할 때 도메인 이름으로 내부 IP 주소를 확인하는 방법은 다음의 두 가지가 있습니다.

- [Azure에서 제공하는 이름 확인](#azure-provided-name-resolution)

- [자체 DNS 서버를 사용한 이름 확인](#name-resolution-using-your-own-dns-server)

어떤 방법으로 이름을 확인할지는 사용하는 VM 및 역할 인스턴스가 서로 어떻게 통신해야 하는지에 따라 다릅니다.

**다음 표에 각 시나리오 별로 해당하는 이름 확인 방법이 나와 있습니다.**

| **시나리오** | **제공된 이름 확인:** | **자세한 내용은 다음을 참조하세요.** |
|--------------|----------------------------------|-------------------------------|
| 역할 인스턴스 또는 VM이 동일한 클라우드 서비스에 위치할 경우 | Azure에서 제공하는 이름 확인 | [Azure에서 제공하는 이름 확인](#azure-provided-name-resolution)|
| VM과 역할 인스턴스가 동일한 가상 네트워크에 위치할 경우 | Azure에서 제공하는 이름 확인(ARM 기반 배포만 해당) 또는 자체 DNS 서버를 이용한 이름 확인 | [Azure에서 제공하는 이름 확인](#azure-provided-name-resolution), [자체 DNS 서버를 이용한 이름 확인](#name-resolution-using-your-own-dns-server) 및 [DNS 서버 요구 사항](#dns-server-requirements) |
| VM와 역할 인스턴스가 동일한 가상 네트워크에 위치하지 않을 경우 | 자체 DNS 서버를 이용한 이름 확인| [자체 DNS 서버를 이용한 이름 확인](#name-resolution-using-your-own-dns-server) 및 [DNS 서버 요구 사항](#dns-server-requirements)|
| 프레미스 간 이름 확인: Azure와 온-프레미스 컴퓨터 간에 역할 인스턴스나 VM을 연결할 경우| 자체 DNS 서버를 이용한 이름 확인| [자체 DNS 서버를 이용한 이름 확인](#name-resolution-using-your-own-dns-server) 및 [DNS 서버 요구 사항](#dns-server-requirements)|
| 내부 IP 역방향 조회| 자체 DNS 서버를 이용한 이름 확인| [자체 DNS 서버를 이용한 이름 확인](#name-resolution-using-your-own-dns-server) 및 [DNS 서버 요구 사항](#dns-server-requirements)|
| 비-공용 도메인에 대한 이름 확인(예: Active Directory 도메인)| 자체 DNS 서버를 이용한 이름 확인| [자체 DNS 서버를 이용한 이름 확인](#name-resolution-using-your-own-dns-server) 및 [DNS 서버 요구 사항](#dns-server-requirements)|
| 역할 인스턴스가 서로 다른 클라우드 서비스에 위치하며 가상 네트워크에 존재하지 않을 경우| 사용할 수 없습니다. 가상 네트워크 외부에 있는 VM과 역할 인스턴스가 서로 다른 클라우드 서비스에 위치한 경우에는 연결을 지원하지 않습니다.| 사용할 수 없습니다.|


## Azure에서 제공하는 이름 확인

Azure에서는 공용 DNS 이름 확인과 함께, 동일한 가상 네트워크 또는 클라우드 서비스 내에 있는 VM 및 역할 인스턴스에 대한 내부 이름 확인을 제공합니다. 클라우드 서비스에 있는 VM/인스턴스는 동일한 DNS 접미사를 공유하므로 호스트 이름만으로도 충분합니다. 클래식 가상 네트워크에서는 클라우드 서비스마다 DNS 접미사가 다르므로 FQDN이 필요합니다. ARM 기반 가상 네트워크에서는 DNS 접미사가 가상 네트워크에 공통적으로 사용되므로 FQDN이 필요하지 않으며 NIC 또는 가상 컴퓨터에 DNS 이름을 할당할 수 있습니다. Azure에서 제공한 이름 확인은 별도로 구성할 필요가 없으나, 위의 표에 나온 바와 같이 모든 배포 서비스에서 선택할 수 있는 것은 아닙니다.

> [AZURE.NOTE]웹 역할 및 작업자 역할의 경우, Azure 서비스 관리 REST API를 사용하면 역할 이름과 인스턴스 번호를 통해 역할 인스턴스의 내부 IP 주소를 접속할 수도 있습니다. 자세한 내용은 [서비스 관리 REST API 참조](https://msdn.microsoft.com/library/azure/ee460799.aspx)를 참조하세요.

### 기능 및 고려 사항

**기능:**

- 사용 편의성: Azure에서 제공하는 이름 확인을 사용하면 별도로 구성할 필요가 없습니다.

- Azure에서 제공하는 이름 확인 서비스는 항상 사용 가능하며 사용자 고유 DNS 서버의 클러스터를 만들고 관리해야 하는 필요성을 줄여줍니다.

- 역할 인스턴스 또는 VM이 동일한 클라우드 서비스에 위치할 경우에 이름 확인이 제공되며 FQDN이 필요하지 않습니다.

- ARM 기반 가상 네트워크에서는 VM 간에 이름 확인이 제공되므로 FQDN이 필요하지 않으며 클래식 가상 네트워크에서는 서로 다른 클라우드 서비스에서 이름을 확인할 때 FQDN이 필요합니다.

- 자동 생성되는 이름 대신에 배포를 가장 잘 설명해주는 호트스 이름을 만들 수 있습니다.

**고려 사항:**

- 가상 네트워크 간, Azure 및 온-프레미스 컴퓨터 간에 이름 확인을 사용할 수 없습니다.

- Azure에서 만든 DNS 접미사는 수정할 수 없습니다.

- 사용자 고유의 레코드를 수동으로 등록할 수 없습니다.

- WINS 및 NetBIOS는 지원되지 않습니다. (Windows 탐색기에서 네트워크 브라우저에 있는 가상 컴퓨터를 나열할 수 없습니다.)

- 호스트 이름이 DNS와 호환될 수 있어야 합니다. (0-9, a-z 및 '-'만 사용이 가능하며, '-'로 시작하거나 끝날 수 없습니다. RFC 3696 섹션을 2를 참조하세요.)

- DNS 쿼리 트래픽은 VM별로 제한됩니다. 이 대부분의 응용 프로그램에 영향을 주지 않아야 합니다. 요청 제한이 확인되는 경우 클라이언트쪽 캐싱이 사용하도록 설정되었는지 확인합니다. 자세한 내용은 참조 [Azure에서 제공하는 이름 확인 활용](#Getting-the-most-from-Azure-provided-name-resolution)을 참조하세요.

- 최초 180개의 클라우드 서비스에 있는 VM만 각 클래식 가상 네트워크에 대해 등록됩니다. ARM 기반 가상 네트워크에는 적용되지 않습니다.


### Azure에서 제공하는 이름 확인 활용
**클라이언트쪽 캐싱:**

모든 DNS 쿼리를 네트워크를 통해 전송해야 하는 것은 아닙니다. 클라이언트쪽 캐싱을 사용하면 대기 시간을 줄이고 로컬 캐시에서 되풀이되는 DNS 쿼리를 확인하여 네트워크 블립에 대한 복원력을 개선하는 데 도움이 됩니다. DNS 레코드는 레코드 새로 고침에 영향을 주지 않으면서 캐시가 가능한 오랫동안 레코드를 저장할 수 있도록 하는 TTL(Time-To-Live)을 포함하므로 클라이언트쪽 캐싱은 대부분의 상황에 적합합니다.

기본 Windows DNS 클라이언트에는 기본 제공되는 DNS 캐시가 있습니다. 일부 Linux 배포판은 기본적으로 캐싱을 포함하지 않으므로 각 Linux VM에 추가된 캐싱이 권장됩니다. dnsmasq와 같은 다양한 여러 DNS 캐싱이 제공되며 여기서는 가장 일반적인 배포판에 dnsmasq를 설치하는 단계를 설명합니다.

- **Ubuntu(resolvconf 사용)**:
	- dnsmasq 패키지를 설치합니다("sudo apt-get install dnsmasq").
- **SUSE(netconf 사용)**:
	- dnsmasq 패키지를 설치합니다("sudo zypper install dnsmasq"). 
	- dnsmasq 서비스를 사용하도록 설정합니다("systemctl enable dnsmasq.service"). 
	- dnsmasq 서비스를 시작합니다("systemctl start dnsmasq.service"). 
	- "/etc/sysconfig/network/config"를 편집하고 NETCONFIG\_DNS\_FORWARDER=""를 "dnsmasq"로 변경합니다.
	- 캐시를 로컬 DNS 확인자로 설정하기 위해 resolv.conf("netconfig update")를 업데이트합니다.
- **OpenLogic(NetworkManager 사용)**:
	- dnsmasq 패키지를 설치합니다("sudo yum install dnsmasq").
	- dnsmasq 서비스를 사용하도록 설정합니다("systemctl enable dnsmasq.service").
	- dnsmasq 서비스를 시작합니다("systemctl start dnsmasq.service").
	- "prepend domain-name-servers 127.0.0.1;"을 "/etc/dhclient-eth0.conf"에 추가합니다.
	- 캐시를 로컬 DNS 확인자로 설정하기 위해 네트워크 서비스("service network restart")를 다시 시작합니다.

[AZURE.NOTE]\: 'dnsmasq' 패키지는 여러 DNS 캐시 중에 Linux에 사용할 수 있는 유일한 캐시입니다. 사용하기 전에 특정 요구 사항에 대한 적합성을 확인하고 다른 캐시가 설치되어 있지 않은지 확인합니다.

**클라이언트쪽 재시도:**

DNS는 주로 UDP 프로토콜입니다. UDP 프로토콜은 메시지 배달을 보장하지 않으므로 DNS 프로토콜 자체에서 재시도 논리가 처리됩니다. 각 DNS 클라이언트(운영 체제)는 작성자의 기본 설정에 따라 서로 다른 재시도 논리를 나타낼 수 있습니다.

 - Windows 운영 체제는 1초 후 재시도한 후 2초, 4초 후 다시 재시도하고 또 다시 4초 후 재시도합니다. 
 - 기본 Linux 설정에서는 5초 후 재시도합니다. 1초 간격으로 5번 재시도하도록 설정을 변경하는 것이 좋습니다.  

Linux VM에서 현재 설정을 확인하려면 'cat /etc/resolv.conf'에서 'options' 줄을 확인합니다. 예를 들면 다음과 같습니다.

	options timeout:1 attempts:5

resolv.conf 파일은 일반적으로 자동으로 생성되며 편집할 수 없습니다. 'options' 줄을 추가하는 구체적인 단계는 배포판마다 다릅니다.

- **Ubuntu**(resolvconf 사용):
	- options 줄을 '/etc/resolveconf/resolv.conf.d/head'에 추가합니다. 
	- 'resolvconf -u'를 실행하여 업데이트합니다.
- **SUSE**(netconf 사용):
	- 'timeout:1 attempts:5'를 '/etc/sysconfig/network/config'의 NETCONFIG\_DNS\_RESOLVER\_OPTIONS="" 매개 변수에 추가합니다. 
	- 'netconfig update'를 실행하여 업데이트합니다.
- **OpenLogic**(NetworkManager 사용):
	- 'echo "options timeout:1 attempts:5"'를 '/etc/NetworkManager/dispatcher.d/11-dhclient'에 추가합니다. 
	- 'service network restart'를 실행하여 업데이트합니다.

## 자체 DNS 서버를 이용한 이름 확인

이름 확인 요구 사항이 Azure에서 제공하는 기능을 벗어나는 경우, 자체 DNS 서버를 사용할 수 있습니다. 자체 DNS 서버를 사용하면 DNS 레코드 관리를 직접 담당해야 합니다.

> [AZURE.NOTE]배포 시나리오에서 필요한 경우가 아니면 외부 DNS 서버를 사용하지 않는 것이 좋습니다.

## DNS 서버 요구 사항

Azure에서 제공하지 않는 이름 확인을 사용할 경우, 지정하는 DNS 서버에서 다음을 지원해야 합니다.

- DNS 서버가 DDNS(동적 DNS) 프로토콜을 통해 동적 DNS 등록을 허용하거나 사용자가 필요한 레코드를 만들어야 합니다.

- 동적 DNS에 의존하는 경우 DNS 서버의 레코드 청소 기능을 꺼야 합니다. Azure에서 IP 주소는 장기 DHCP 임대를 하므로 청소하는 동안 DNS 서버에서 레코드가 제거될 수 있습니다.

- DNS 서버에서 재귀 기능을 사용해 외부 도메인 이름 확인을 허용해야 합니다.

- 클라이언트 이름 확인 요청과 이름이 등록될 서비스 및 가상 컴퓨터에 의해 DNS 서버에 엑세스할 수 있어야 합니다( TCP/UDP port 53 이용).

- 또한 많은 보트들이 열려 있는 재귀 DNS 해결자를 검색하므로 인터넷에서 엑세스할 수 없더록 DNS 서버를 보호하는 것이 좋습니다.

- 최상의 성능을 위해 DNS 서버로 Azure VM을 사용할 때는 IPv6을 사용하지 않도록 설정하고 [인스턴스 수준 공용 IP](virtual-networks-instance-level-public-ip.mp)를 각 DNS 서버 VM에 할당해야 합니다.

## DNS 서버 지정

역할 인스턴스와 VM에서 사용할 DNS 서버를 여러 개 지정할 수 있습니다. 각 DNS 쿼리에 대해 클라이언트는 기본 DNS 서버를 가장 먼저 시도하며, 기본 DNS 서버가 응답하지 않을 경우에만 다른 서버를 시도합니다. 즉, DNS 쿼리가 서로 다른 DNS 서버에 부하 분산되지 않습니다. 그러므로 사용자 환경에 맞게 DNS 서버가 올바른 순서로 등록되어 있는지 확인하세요.

> [AZURE.NOTE]이미 배포된 가상 네트워크의 네트워크 구성 파일에서 DNS 설정을 변경할 경우, 각 VM을 다시 시작해야 변경 내용이 적용됩니다.

### 관리 포털에서 DNS 서버 지정

관리 포털에서 가상 네트워크를 만들 경우, IP 주소 및 DNS 서버(다수의 서버 가능) 이름을 원하는 대로 지정할 수 있습니다. 가상 네트워크가 일단 만들어지면, 가상 네트워크에 배포한 가상 컴퓨터와 역할 인스턴스는 지정한 DNS 설정에 따라 자동으로 구성됩니다. 특정 클라우드 서비스(Azure 클래식)에 대해 지정된 DNS 서버 또는 네트워크 인터페이스 카드(ARM 기반 배포)는 가상 네트워크에 대해 지정된 것보다 우선합니다. [관리 포털에서 가상 네트워크 구성 정보](virtual-networks-settings.md)를 참조하세요.

### 구성 파일을 이용한 DNS 서버 지정(Azure 클래식)

클래식 가상 네트워크에 대해 DNS 설정을 지정하려면 두 개의 구성 파일을 사용합니다. *네트워크 구성* 파일과 *서비스 구성* 파일입니다.

네트워크 구성 파일은 구독에서 가상 네트워크를 설명합니다. 가상 네트워크에 있는 클라우드 서비스에 역할 인스턴스나 VM을 추가할 경우, 네트워크 구성 파일에 따른 DNS 설정이 각 역할 인스턴스나 VM에 적용됩니다(단, 클라우드 서비스 특정 DNS 서버가 지정되지 않은 경우).

서비스 구성 파일은 Azure에 클라우드 서비스 파일을 추가할 때마다 생성됩니다. 역할 인스턴스나 VM을 클라우드 서비스에 추가할 경우, 서비스 구성 파일의 DNS 설정이 각 역할 인스턴스나 VM에 적용됩니다.

> [AZURE.NOTE]서비스 구성 파일의 DNS 서버는 네트워크 구성 파일의 설정을 재정의합니다.


## 다음 단계

[Azure 서비스 구성 스키마](https://msdn.microsoft.com/library/azure/ee758710)

[가상 네트워크 구성 스키마](https://msdn.microsoft.com/library/azure/jj157100)

[관리 포털에서 가상 네트워크 설정 구성 정보](virtual-networks-settings.md)

[네트워크 구성 파일을 사용하여 가상 네트워크 구성](virtual-networks-using-network-configuration-file.md)

<!---HONumber=Sept15_HO2-->