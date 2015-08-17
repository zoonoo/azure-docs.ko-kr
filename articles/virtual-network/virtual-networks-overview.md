<properties
   pageTitle="Azure 가상 네트워크(VNet) 개요"
   description="Azure의 가상 네트워크(VNet)에 대해 자세히 알아봅니다."
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carolz"
   editor="tysonn" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/05/2015"
   ms.author="telmos" />

# 가상 네트워크 개요

Azure 가상 네트워크(VNet)는 클라우드의 사용자 네트워크를 나타내는 표현입니다. 사용자가 Azure 네트워크 설정을 제어하고 DHCP 주소 블록, DNS 설정, 보안 정책 및 라우팅을 정의할 수 있습니다. 또한 물리적 컴퓨터 및 가상 컴퓨터를 온-프레미스 데이터 센터에 배포하는 것과 동일한 방식으로 VNet을 서브넷으로 분할하고 Azure IaaS 가상 컴퓨터(VM) 및 PaaS 역할 인스턴스를 배포할 수 있습니다. 기본적으로 IP 주소 블록을 가져와서 Azure로 네트워크를 확장할 수 있습니다.

VNet을 더 잘 이해하기 위해 간소화된 온-프레미스 네트워크가 표시된 아래 그림을 살펴보겠습니다.

![온-프레미스 네트워크](./media/virtual-networks-overview/figure01.png)

위의 그림에서는 라우터를 통해 공용 인터넷에 연결된 온-프레미스 네트워크를 보여줍니다. 또한 DNS 서버 및 웹 서버 팜을 호스트하는 DMZ와 라우터 사이의 방화벽도 볼 수 있습니다. 웹 서버 팜은 인터넷에 노출되어 있는 하드웨어 부하 분산 장치를 사용하여 부하 분산되며 내부 서브넷의 리소스를 소모합니다. 내부 서브넷은 다른 방화벽에 의해 DMZ에서 분리되어 있으며 Active Directory 도메인 컨트롤러 서버, 데이터베이스 서버 및 응용 프로그램 서버를 호스트합니다.

아래 그림에 나온 것처럼 동일한 네트워크를 Azure에서 호스트할 수 있습니다.

![Azure 가상 네트워크](./media/virtual-networks-overview/figure02.png)

Azure 인프라가 라우터의 역할을 수행하여 어떠한 구성도 필요 없이 VNet에서 공용 인터넷에 액세스할 수 있는 방법을 알아봅니다. 방화벽을 대체하여 NSG(네트워크 보안 그룹)를 각 개별 서브넷에 적용할 수 있습니다. 또한 실제 부하 분산 장치는 Azure에서 인터넷에 연결된 내부 부하 분산로 대체됩니다.

## 가상 네트워크

VNet은 IaaS VM 및 이 VM에 배포된 역할 PaaS 역할 인스턴스에 다음 서비스를 제공합니다.

- **격리**. VNet은 서로 완전히 격리됩니다. 따라서 동일한 CIDR 주소 블록을 사용하는 개발, 테스트 및 프로덕션 환경에 대해 개별 VNet을 만들 수 있습니다.

- **제약**. VNet은 여러 Azure 지역에 걸쳐 확장할 수 없습니다.

    >[AZURE.NOTE]Azure에는 클래식(서비스 관리라고도 함) 및 ARM(Azure 리소스 관리자)의 두 가지 배포 모드가 있습니다. 클래식 VNet은 선호도 그룹에 추가하거나 지역 VNet으로 생성할 수 있습니다. VNet이 선호도 그룹에 있는 경우는 [지역 VNet으로 마이그레이션](./virtual-networks-migrate-to-regional-vnet.md)하는 것이 좋습니다.

- **공용 인터넷 액세스**. VNet의 모든 IaaS VM 및 PaaS 역할 인스턴스에서 기본적으로 공용 인터넷에 액세스할 수 있습니다. NSG(네트워크 보안 그룹)을 사용하면 사용자가 액세스를 제어할 수 있습니다.

- **VNet 내의 VM 액세스**. IaaS VM 및 PaaS 역할 인스턴스는 서로 다른 서브넷에 있는 경우에도 게이트웨이를 구성하거나 공용 IP 주소를 사용할 필요 없이 PaaS 및 IaaS 환경을 결합하여 동일한 VNet에서 서로 연결할 수 있습니다.

- **이름 확인**. Azure는 VNet에 배포된 IaaS VM 및 PaaS 역할 인스턴스에 대한 내부 이름 확인 기능을 제공합니다. 또한 사용자 고유의 DNS 서버를 배포하고 이 서버를 사용하도록 VNet을 구성할 수도 있습니다.

- **연결**. VNet은 사이트 간 VPN 연결 또는 Express 경로 연결을 사용하여 상호 연결할 수 있으며 온-프레미스 데이터 센터에도 연결할 수 있습니다. VPN 게이트웨이에 대한 자세한 내용은 [VPN 게이트웨이 정보](./vpn-gateway-about-vpngateways.md)를 참조하세요. Express 경로에 대한 자세한 내용은 [Express 경로 기술 개요](./expressroute-introduction.md)를 참조하세요.

    >[AZURE.NOTE]임의의 IaaS VM 또는 PaaS 역할 인스턴스를 Azure 환경에 배포하기 전에 VNet을 만들었는지 확인합니다. ARM 기반 VM은 VNet이 필요하며 기존 VNet을 지정하지 않으면 Azure에서 기본 VNet을 만드는데, 이 기본 VNet은 온-프레미스 네트워크와 CIDR 주소 블록 충돌이 발생할 수 있습니다. 이 문제가 발생하면 VNet을 온-프레미스 네트워크에 연결할 수 없습니다.

## 서브넷

조직 및 보안을 위해 여러 서브넷으로 VNet을 나눌 수 있습니다. VNet 내의 서브넷은 추가 구성 없이 서로 통신할 수 있습니다. 또한 서브넷 수준에서 라우팅 설정을 변경할 수 있으며 서브넷에 NSG를 적용할 수도 있습니다.

## IP 주소

Azure에서 구성 요소에 할당되는 IP 주소는 공용 및 개인의 두 종류가 있습니다. Azure 서브넷에 배포된 IaaS VM 및 PaaS 역할 인스턴스는 서브넷에 할당된 CIDR 주소 블록에 따라 각 NIC에 개인 IP 주소가 자동으로 할당됩니다. 또한 IaaS VM 및 PaaS 역할 인스턴스에 공용 IP 주소를 할당할 수도 있습니다.

이러한 IP 주소는 동적이므로 언제든 변경할 수 있습니다. 특정 서비스에 대한 IP 주소가 항상 동일하게 유지되어야 할 수 있습니다. 이를 수행하기 위해 해당 IP 주소를 정적으로 예약할 수 있습니다.

## Azure 부하 분산 장치

Azure에서는 두 가지 유형의 부하 분산 장치를 사용할 수 있습니다.

- **외부 부하 분산 장치**. 외부 부하 분산 장치를 사용하면 공용 인터넷에서 액세스하는 IaaS VM 및 PaaS 역할 인스턴스에 높은 가용성을 제공할 수 있습니다.

- **내부 부하 분산 장치**. 내부 부하 분산 장치를 사용하면 VNet의 다른 서비스에서 액세스하는 IaaS VM 및 PaaS 역할 인스턴스에 높은 가용성을 제공할 수 있습니다.

Azure에서 부하 분산에 대한 자세한 내용은 [부하 분산 장치 개요](../load-balancer-overview.md)를 참조하세요.

## 네트워크 보안 그룹(NSG)

네트워크 인터페이스(NIC), VM 및 서브넷에 대한 인바운드 및 아웃바운드 액세스를 제어하는 NSG를 만들 수 있습니다. 각 NSG에는 원본 IP 주소, 원본 포트, 대상 IP 주소 및 대상 포트에 기반하여 트래픽을 승인할지 또는 거부할지 지정하는 하나 이상의 규칙이 포함되어 있습니다. NSG에 대해 자세히 알아보려면 [네트워크 보안 그룹이란?](../virtual-networks-nsg.md)을 참조하세요.

## 가상 어플라이언스

가상 어플라이언스는 방화벽, WAN 최적화 또는 침입 감지 등과 같은 소프트웨어 기반 어플라이언스 기능을 실행하는 VNet의 또 다른 VM입니다. 가상 어플라이언스를 통해 VNet 트래픽을 라우팅하는 Azure 경로를 만들어 해당 기능을 사용할 수 있습니다.

예를 들어 NSG는 VNet에 대한 보안을 제공하는 데 사용할 수 있습니다. 그러나 NSG는 들어오고 나가는 패킷에 대해 계층 4 ACL(액세스 제어 목록)을 제공합니다. 계층 7 보안 모델을 사용하려면 방화벽 어플라이언스를 사용해야 합니다.

가상 어플라이언스는 [사용자 정의 경로 및 IP 전달](../virtual-networks-udr-overview.md)에 따라 달라집니다.

## 다음 단계

- [VNet 만들기](../virtual-networks-create-a-vnet.md) 및 서브넷 만들기.
- [VNet에서 VM 만들기](../virtual-machines-windows-tutorial.md).
- [NSG](../virtual-networks-nsg.md)에 대해 알아보기.
- [부하 분산 장치](../load-balancer-overview.md)에 대해 알아보기.
- [내부 IP 주소 예약](../virtual-networks-reserved-private-ip.md).
- [공용 IP 주소 예약](../virtual-networks-reserved-public-ip.md).
- [사용자 정의 경로 및 IP 전달](virtual-networks-udr-overview.md)에 대해 알아보기.

<!---HONumber=August15_HO6-->