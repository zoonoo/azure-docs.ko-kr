<properties
	pageTitle="Azure Site Recovery를 통해 장애 조치(Failover) 후 워크로드에 연결 | Microsoft Azure"
	description="이 문서에서는 Azure Site Recovery를 통해 장애 조치(Failover) 후 워크로드에 연결하는 옵션을 설명합니다."
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery"
	ms.date="02/15/2016"
	ms.author="raynew"/>

#  Azure Site Recovery를 통해 장애 조치(Failover) 후 워크로드에 연결

Azure Site Recovery 서비스는 가상 컴퓨터와 물리적 서버의 복제, 장애 조치(Failover) 및 복구를 오케스트레이션하여 BCDR(비즈니스 연속성 및 재해 복구) 전략에 기여합니다. 컴퓨터는 Azure 또는 보조 온-프레미스 데이터 센터로 복제할 수 있습니다. 빠른 개요를 알아보려면 [Azure Site Recovery란?](site-recovery-overview.md)을 확인하세요.


## 개요

BCDR 전략의 일환으로 장애 조치(Failover) 후 워크로드에 연결할 수 있는지 확인해야 합니다. 이 문서에서는 이 작업을 수행하기 위한 전략을 설명합니다.

최신 응용 프로그램은 거의 항상 어느 정도 네트워킹에 종속되어 있으므로 한 사이트에서 다른 사이트로 물리적으로 서비스를 이동하면 장애 조치(failover) 이후 응용 프로그램 및 작업에 접근할 수 있는지에 대한 네트워킹 문제가 발생합니다. 이 문제에 대한 두 가지 주요 솔루션이 있습니다.

- **고정 IP 주소**: 첫 번째 방법은 고정 IP 주소를 유지 관리하는 것입니다. 비록 서로 다른 물리적 위치에서 서비스가 이동하고 호스팅 서버가 존재하기는 하지만 응용 프로그램이 IP 주소 구성을 새 위치로 함께 가지고 갑니다.
- **수정된 IP 주소**: 두 번째 방법을 사용하려면 복구된 사이트로 전환하는 동안 IP 주소를 변경해야 합니다.

## 옵션 1: 고정 IP 주소

재해 복구 관점에서는 고정 IP 주소를 사용하는 것이 가장 쉽게 구현할 수 있는 방법으로 보입니다. 그러나 많은 잠재적인 문제점 때문에 실제로는 가장 인기 없는 방법입니다. Azure Site Recovery는 모든 시나리오에서 IP 주소를 유지하는 기능을 제공합니다. 이 방법을 사용하기 전에 주요 장애 조치(Failover) 시나리오(늘어난 서브넷 및 서브넷 장애 조치(Failover))를 위한 고려 사항을 먼저 살펴보겠습니다.

- **늘어난 서브넷 장애 조치(Failover)**: 늘어난 서브넷에서 서브넷을 주 위치와 대상 위치 두 곳에서 동시에 사용할 수 있습니다. 간단히 말해 서버와 해당 IP(계층 3) 구성을 보조 사이트로 이동할 수 있고 네트워크가 트래픽을 자동으로 새 위치에 라우팅한다는 것입니다. 서버 관점에서는 이것을 간단히 처리할 수 있지만 실제로는 많은 문제점이 있습니다.
	- 계층 2(데이터 링크 계층) 관점에서 이를 수행하려면, 현재 널리 사용되고 있기는 하지만 늘어난 VLAN을 관리할 수 있는 네트워킹 장비가 필요합니다.
	- 두 번째로 더 어려운 문제는 VLAN을 늘림으로써 잠재적 장애 도메인이 이 두 사이트에 확장되어 본질적으로 단일 오류 지점이 된다는 것입니다. 가능성이 별로 없기는 하지만 브로드캐스트 스톰이 시작해서 격리되지 못할 수도 있습니다. 이 문제의 경우 성공적인 구현부터 “절대 불가”까지 의견이 분분합니다.
	- DR 사이트인 Azure에 장애 조치(failover)하는 경우 늘어난 서브넷이 가능하지 않습니다.
- **서브넷 장애 조치(Failover)**: 실제로 서브넷을 늘리지 않고 늘어난 서브넷의 이점을 얻기 위해 서브넷 장애 조치(failover)를 구현할 수 있습니다. 이 구성에서, 지정된 서브넷은 사이트 1 또는 사이트 2에 있으며 두 사이트에 동시에 존재하지는 않습니다. 장애 조치(failover) 시 IP 주소 공간을 유지 관리하기 위해 라우터 인프라에 대해 프로그래밍 방식으로 정렬하여 한 사이트에서 다른 사이트로 서브넷을 이동할 수 있습니다. 장애 조치(failover) 시나리오에서 서브넷은 연결되어 있는 보호된 VM과 함께 이동합니다. 이 방법의 주요 단점은 오류 발생 시 전체 서브넷을 이동해야 하는 것으로, 이는 합리적인 솔루션일 수 있지만 장애 조치(failover)의 세부적인 고려 사항에 영향을 미칠 수 있습니다.


### 예 - 기업에서 서브넷 장애 조치(failover)

가상의 기업(Contoso)이 전체 서브넷을 장애 조치(failover)하면서 해당 VM을 복구 위치에 복제할 수 있는 방법을 살펴보겠습니다. 두 개의 온-프레미스 위치 사이에 VM을 복제하는 동안 Contoso가 해당 서브넷을 관리할 수 있는 방법을 살펴보고 Azure가 재해 복구 사이트로 사용될 때 서브넷 장애 조치(failover)가 작동하는 방식을 설명하겠습니다.

- 주 사이트에 서브넷 192.168.1.0/24에서 실행되는 응용 프로그램이 있습니다.
- 전체 서브넷이 장애 조치(failover)되고, 서브넷의 모든 가상 컴퓨터가 복구 사이트로 장애 조치(failover)되어 해당 IP 주소를 유지합니다.
- 아래 다이어그램에 나와 있는 것처럼 주 사이트와 복구 사이트 간, 세 번째 사이트와 주 사이트 간, 세 번째 사이트와 복구 사이트 간의 라우팅이 서브넷 192.168.1.0/24에 속한 모든 가상 컴퓨터가 복구 사이트로 이동되었다는 사실을 반영하도록 적절히 수정되어야 합니다.
- 이 다이어그램은 다음을 가정합니다.
	-  VMM의 자체 인스턴스에서 각 데이터 센터를 서비스합니다. 데이터 센터 간에는 System Center VMM 데이터베이스의 복제가 없습니다.
	-  각 데이터 센터는 가상 컴퓨터의 고정 IP 주소를 사용합니다.
	-  데이터 센터 간의 연결은 인터넷을 통한 VPN 연결이 아닌 전용 회로를 통해 이루어집니다.

	**장애 조치(failover) 전**

	![장애 조치(failover) 전](./media/site-recovery-connect-after-failover/network-design2.png)

	**장애 조치(failover) 후**

	![장애 조치(failover) 후](./media/site-recovery-connect-after-failover/network-design3.png)


특정 가상 컴퓨터에 대한 보호를 사용하도록 설정하는 경우 Site Recovery가 다음과 같이 네트워킹 리소스를 할당합니다.

1. Site Recovery가 각 VMM 인스턴스에 대한 관련 네트워크에 정의된 고정 IP 주소 풀에서 가상 컴퓨터의 각 네트워크 인터페이스에 대한 IP 주소를 할당합니다.
2. 관리자가 복구 사이트의 네트워크에 대해 주 사이트에서 사용한 것과 동일한 IP 주소 풀을 정의하면 Site Recovery가 주 가상 컴퓨터에 할당된 주소와 동일한 IP 주소를 복제 가상 컴퓨터에 할당합니다. IP 주소가 VMM에서 예약되기는 하지만 장애 조치(failover) IP 주소로 설정되지는 않습니다. 장애 조치(failover) IP 주소는 장애 조치(failover)하기 직전에 설정됩니다. 이 스크린샷은 Hyper-V 콘솔에서 복제 가상 컴퓨터에 대한 장애 조치(failover) TCP/IP 설정을 보여 줍니다. 이러한 설정은 가상 컴퓨터에 대한 장애 조치(failover)가 시작되기 직전에 복제됩니다.

	![IP 주소 설정](./media/site-recovery-connect-after-failover/network-design4.png)

3. 동일한 IP 주소를 사용할 수 없는 경우 Site Recovery가 풀에서 다른 주소를 할당합니다.
4. 보호를 위해 가상 컴퓨터를 사용하도록 설정한 후 다음 샘플 스크립트를 사용하여 가상 컴퓨터에 할당된 IP 주소를 확인할 수 있습니다. 동일한 IP 주소가 장애 조치(failover) IP 주소로 설정되고 장애 조치(failover) 시 VM에 할당됩니다.

    $vm = Get-SCVirtualMachine -Name $na = $vm[0].VirtualNetworkAdapters $ip = Get-SCIPAddress -GrantToObjectID $na[0].id $ip.address

가상 컴퓨터가 DHCP를 사용하는 경우 Site Recovery에서 IP 주소 관리를 처리하지 않습니다. 복구 사이트에서 IP 주소를 할당하는 DHCP 서버가 주 사이트의 범위와 동일한 범위에서 주소를 할당할 수 있도록 해야 합니다.

### 예 - Azure로 서브넷 장애 조치(failover)

Azure에 장애 조치(failover)하는 경우 몇 가지 제약 조건이 있습니다. 온-프레미스 인프라가 해당 사업 분야 응용 프로그램을 호스트하고 Azure가 해당 모바일 응용 프로그램을 호스트하는 가상의 회사(Woodgrove Bank)를 살펴보겠습니다.


- Azure와 온-프레미스 서버에서 Woodgrove Bank VM 간의 연결은 Azure의 가상 네트워크와 Woodgrove Bank의 온-프레미스 네트워크의 확장을 보여 주는 사이트 간 VPN을 통해 이루어집니다.
- Woodgrove는 Site Recovery를 사용하여 해당 온-프레미스 작업을 Azure에 복제하려고 합니다.
- Woodgrove는 하드 코드된 IP 주소에 종속된 응용 프로그램 및 구성을 다루어야 하므로 Azure로 장애 조치(failover)한 이후 해당 응용 프로그램의 IP 주소를 유지해야 합니다.
- Woodgrove의 온-프레미스 인프라는 VMM 2012 R2 서버에서 관리됩니다.
- VMM 서버에 만들어진 VLAN 기반 논리 네트워크(응용 프로그램 네트워크)가 있습니다.![논리 네트워크](./media/site-recovery-connect-after-failover/network-design5.png)
- 논리 네트워크를 사용하여 VM 네트워크(응용 프로그램 VM 네트워크)가 만들어졌습니다. ![VM 네트워크](./media/site-recovery-connect-after-failover/network-design6.png)
- 응용 프로그램의 모든 가상 컴퓨터는 고정 IP 주소를 사용하므로 고정 IP 풀도 논리 네트워크에 대해 정의되었습니다.
- Woodgrove는 IP 주소 범위(172.16.1.0/24, 172.16.2.0/24)의 IP 주소를 Azure에서 실행 중인 해당 리소스에 할당합니다.


Woodgrove가 복제를 배포하고 IP 주소를 유지 관리하려면 다음이 필요합니다.

- 응용 프로그램이 원활하게 장애 조치(failover)할 수 있도록 온-프레미스 네트워크의 확장인 Azure 가상 컴퓨터
- Azure에서 사이트 간 연결을 설정하는 경우 Azure가 늘어난 서브넷을 지원하지 않기 때문에 IP 주소의 범위가 온-프레미스 IP 주소 범위와 다른 경우에만 Azure 네트워크를 통해 온-프레미스 위치(Azure에서는 로컬 네트워크라고 함)에 트래픽을 라우팅할 수 있습니다. 이는 온-프레미스에 서브넷 192.168.1.0/24가 있는 경우 Azure 네트워크에 로컬 네트워크 192.168.1.0/24를 추가할 수 없다는 것입니다. 이렇게 예측하는 이유는 Azure가 서브넷에 활성 VM이 없고 해당 서브넷이 DR 용도로만 만들어진다는 것을 모르기 때문입니다. Azure 네트워크 외부에서 제대로 네트워크 트래픽을 라우팅할 수 있으려면 네트워크 및 로컬 네트워크의 서브넷이 충돌하지 않아야 합니다.
- 장애 조치(failover)된 VM이 만들어지는 Azure(복구 네트워크)에 추가 네트워크를 만들어야 합니다.

	![Azure 네트워크](./media/site-recovery-connect-after-failover/network-design7.png)

- VM에 대한 IP 주소가 유지되도록 하기 위해 Site Recovery의 VM 속성에서 동일한 IP 주소가 사용되도록 지정합니다. 그러면 장애 조치(failover) 후 Site Recovery가 지정된 IP 주소를 VM에 할당합니다. ![Azure 네트워크](./media/site-recovery-connect-after-failover/network-design8.png)


- 장애 조치(failover)가 트리거되고 VM이 필요한 IP 주소로 복구 네트워크에 만들어지면 이를 사용하여 VM에 대한 연결을 설정할 수 있습니다. 이 작업은 스크립팅할 수 있습니다. 이전 섹션에서 서브넷 장애 조치(failover)에 대해 설명한 것처럼 Azure로 장애 조치(failover)의 경우에도 라우팅이 해당 192.168.1.0/24가 현재 Azure로 이동했다는 것을 반영하도록 적절하게 수정되어야 합니다. ![Azure 네트워크](./media/site-recovery-connect-after-failover/network-design9.png)

## 옵션 2: 수정된 IP 주소

이 방법이 가장 일반적으로 보이며 이는 장애 조치(failover)되는 모든 VM의 IP 주소가 변경된다는 것입니다. 이 방법의 주요 단점은 네트워크 인프라가 IP 주소가 변경되었다는 점을 알아야 하고 DNS 항목이 네트워크 전체에서 변경되고 플러시되어야 하고 네트워크 테이블의 캐시된 항목도 변경되고 플러시되어야 한다는 점입니다. 이로 인해 DNS 인프라가 설정된 방법에 따라 가동 중지 시간이 발생할 수 있습니다. 이러한 문제는 인트라넷 응용 프로그램의 경우 낮은 TTL 값을 사용하고, 인터넷 기반 응용 프로그램의 경우 [Site Recovery와 함께 Azure 트래픽 관리자](https://azure.microsoft.com/blog/2015/03/03/reduce-rto-by-using-azure-traffic-manager-with-azure-site-recovery/)를 사용하면 완화할 수 있습니다.

### 예 - 수정된 IP 주소

주 또는 복구 사이트에 호스트된 응용 프로그램이 액세스할 수 있는 세 번째 사이트가 있는 예를 사용하여 이 시나리오를 살펴보겠습니다.

![Azure 네트워크](./media/site-recovery-connect-after-failover/network-design10.png)

- 일부 응용 프로그램이 주 사이트의 192.168.1.0/24 서브넷에 호스트되고 장애 조치(failover) 후 서브넷 172.16.1.0/24의 복구 사이트에서 실행되도록 구성되었습니다.
- 세 사이트 모두 서로 액세스할 수 있도록 VPN 연결/네트워크 라우팅이 적절하게 구성되었습니다.
- 하나 이상의 응용 프로그램을 장애 조치(failover)한 후 해당 응용 프로그램이 복구 서브넷에서 복원됩니다. 이 경우 동시에 전체 서브넷을 장애 조치(failover)할 필요가 없으며 VPN 또는 네트워크 라우팅을 재구성하기 위해 변경할 사항이 없습니다.
- 장애 조치(failover) 및 일부 DNS 업데이트를 실행해도 응용 프로그램에 계속 액세스할 수 있습니다. DNS 서버가 동적 업데이트를 허용하도록 구성된 경우 장애 조치(failover) 후 가상 컴퓨터가 시작되면 새 IP 주소를 사용하여 자체적으로 등록합니다.

	![Azure 네트워크](./media/site-recovery-connect-after-failover/network-design11.png)

- 장애 조치(failover) 후 복제 가상 컴퓨터는 주 가상 컴퓨터의 IP 주소와 동일하지 않은 IP 주소를 가질 수 있습니다.
- 가상 컴퓨터가 시작되면 사용 중인 DNS 서버가 업데이트됩니다. DNS 항목은 일반적으로 네트워크 전체에서 변경되거나 플러시되어야 하고, 네트워크 테이블의 캐시된 항목도 업데이트되고 플러시되어야 하므로 이러한 상태가 변경되는 동안 가동 중지 시간이 발생하는 것은 흔한 일입니다. 이는 다음 방법으로 완화할 수 있습니다.

	- 인트라넷 응용 프로그램의 경우 낮은 TTL 값 사용
	- 인터넷 기반 응용 프로그램에 [Site Recovery와 함께 Azure 트래픽 관리자](https://azure.microsoft.com/blog/2015/03/03/reduce-rto-by-using-azure-traffic-manager-with-azure-site-recovery/) 사용
	- DNS 서버를 업데이트할 때 제 시간에 업데이트되도록 하기 위해 복구 계획 내에서 다음 스크립트 사용(동적 DNS 등록이 구성되지 않은 경우 스크립트가 필요하지 않음)

    [string]$Zone, [string]$name, [string]$IP ) $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name $newrecord = $record.clone() $newrecord.RecordData[0].IPv4Address = $IP Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord


## 다음 단계

[이 블로그 게시물](https://azure.microsoft.com/blog/2014/09/04/networking-infrastructure-setup-for-microsoft-azure-as-a-disaster-recovery-site/)은 IP 주소를 반드시 유지할 필요가 없는 경우에 필수 Azure 네트워킹 인프라를 설정하는 연습을 제공합니다. 네트워킹 온-프레미스 및 Azure에서 설정하는 방법을 보여줍니다. 마지막으로 테스트 장애 조치(failover) 및 계획된 장애 조치(failover)를 실행하기 위한 지침이 나옵니다.

<!---HONumber=AcomDC_0218_2016-->