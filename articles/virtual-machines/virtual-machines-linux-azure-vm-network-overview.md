---
title: "Azure 및 Linux VM 네트워크 개요 | Microsoft Docs"
description: "Azure 및 Linux VM 네트워킹 개요입니다."
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: vlivech
manager: timlt
editor: 
ms.assetid: b5420e35-0463-4456-9803-6142db150f2e
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/25/2016
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: 63cf1a5476a205da2f804fb2f408f4d35860835f
ms.openlocfilehash: 9cc4fcb68148ef64829fff6135449a3c0efb75d0


---
# <a name="azure-and-linux-vm-network-overview"></a>Azure 및 Linux VM 네트워크 개요
## <a name="virtual-networks"></a>가상 네트워크
Azure 가상 네트워크(VNet)는 클라우드의 사용자 네트워크를 나타내는 표현입니다. 구독 전용 Azure 클라우드를 논리적으로 격리한 것이 가상 네트워크입니다. 사용자는 이 네트워크 내부의 IP 주소 블록, DNS 설정, 보안 정책 및 경로 테이블을 완벽하게 제어할 수 있습니다. 또한 VNet을 여러 서브넷으로 분할하고 Azure IaaS VM(가상 컴퓨터) 및/또는 Cloud Services(PaaS 역할 인스턴스)를 실행할 수 있습니다. 뿐만 아니라 Azure에서 제공하는 연결 옵션 중 하나를 사용하여 가상 네트워크를 온-프레미스 네트워크에 연결할 수 있습니다. 기본적으로 네트워크를 Azure로 확장하여 IP 주소 블록을 완벽하게 제어하고, Azure가 제공하는 엔터프라이즈급 솔루션의 혜택을 누릴 수 있습니다.

* [가상 네트워크 개요](../virtual-network/virtual-networks-overview.md)

Azure CLI를 사용하여 VNet을 만들려면 다음 연습을 수행합니다.

* [Azure CLI를 사용하여 VNet을 만드는 방법](../virtual-network/virtual-networks-create-vnet-arm-cli.md)

## <a name="network-security-groups"></a>네트워크 보안 그룹
NSG(네트워크 보안 그룹)은 ACL(액세스 제어 목록)의 가상 네트워크에 VM 인스턴스에 대한 허용 또는 거부 네트워크 트래픽 규칙의 목록을 포함합니다. NSG는 서브넷 또는 서브넷 내의 개별 VM 인스턴스 중 하나와 연결될 수 있습니다. NSG를 서브넷과 연결한 경우 ACL 규칙은 해당 서브넷에 있는 모든 VM 인스턴스에 적용됩니다. 또한 개별 VM에 대한 트래픽은 해당 VM에 직접 NSG를 연결하여 추가로 제한할 수 있습니다.

* [NSG(네트워크 보안 그룹)란?](../virtual-network/virtual-networks-nsg.md)
* [Azure CLI에서 NSG를 만드는 방법](../virtual-network/virtual-networks-create-nsg-arm-cli.md)

## <a name="user-defined-routes"></a>사용자 정의 경로
Azure에서 VNet(가상 네트워크)에 VM(가상 컴퓨터)을 추가하면 네트워크를 통해 다른 VM과 통신할 수 있는지 자동으로 확인할 수 있습니다. 서로 다른 서브넷에 있는 VM 간에도 게이트웨이를 지정할 필요가 없습니다. VM에서 공용 인터넷에 통신하는 경우는 물론, Azure와 사용자 고유의 데이터 센터 간 하이브리드 연결이 있는 경우 사용자의 온-프레미스 네트워크에 통신하는 경우에도 마찬가지입니다 .

* [사용자 정의 경로 및 IP 전달이란?](../virtual-network/virtual-networks-udr-overview.md)
* [Azure CLI에서 UDR 만들기](../virtual-network/virtual-network-create-udr-arm-cli.md)

## <a name="associating-a-fqdn-to-your-linux-vm"></a>Linux VM에 FQDN 연결
Resource Manager 배포 모델을 사용하여 Azure Portal에서 VM(가상 컴퓨터)을 만들 때, 가상 컴퓨터의 공용 IP 리소스가 자동으로 만들어집니다. 이 IP 주소를 사용하여 VM에 원격으로 액세스합니다. 기본적으로 포털에서 정규화된 도메인 이름 또는 FQDN을 만들지는 않지만 VM을 만들면 이름을 추가할 수 있습니다.

* [Azure Portal에서 정규화된 도메인 이름 만들기](virtual-machines-linux-portal-create-fqdn.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="virtual-nics"></a>가상 NIC
네트워크 인터페이스(NIC)는 VM(가상 컴퓨터)과 기본 소프트웨어 네트워크 간 상호 연결입니다. 이 문서에서는 네트워크 인터페이스란 무엇이며 Azure Resource Manager 배포 모델에서 어떻게 사용되는지에 대해 설명합니다.

* [가상 네트워크 인터페이스 개요](../virtual-network/virtual-network-network-interface-overview.md)

## <a name="virtual-nics-and-dns-labeling"></a>가상 NIC 및 DNS 레이블 지정
영구적으로 유지해야 하는 서버가 있지만 서버가 가축으로 처리되고 해체되며 자주 배포되는 경우 NIC에 DNS 레이블 지정을 사용하여 VNET의 이름을 유지하려고 합니다.  다음 연습을 통해 고정 IP를 가진 NIC를 영구적으로 설정합니다.

* [Azure CLI를 사용하여 전체 Linux 환경 만들기](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="virtual-network-gateways"></a>가상 네트워크 게이트웨이
가상 네트워크 게이트웨이는 Azure 가상 네트워크와 온-프레미스 위치 및 Azure 내의 가상 네트워크 간(VNet 간)에 네트워크 트래픽을 보내는 데 사용됩니다. VPN Gateway를 구성할 때 가상 네트워크 게이트웨이 및 가상 네트워크 게이트웨이 연결을 만들고 구성해야 합니다.

* [VPN Gateway 정보](../vpn-gateway/vpn-gateway-about-vpngateways.md)

## <a name="internal-load-balancing"></a>내부 부하 분산
Azure 부하 분산 장치는 계층 4(TCP, UDP) 부하 분산 장치입니다. 부하 분산 장치는 부하 분산 장치 집합에 있는 클라우드 서비스 또는 가상 컴퓨터의 정상 서비스 인스턴스 간에 들어오는 트래픽을 배포하여 고가용성을 제공합니다. Azure Load Balancer는 여러 포트, 여러 IP 주소 또는 둘 다에서 이러한 서비스를 제공할 수도 있습니다.

* [Azure CLI를 사용하여 내부 부하 분산 장치 만들기](../load-balancer/load-balancer-get-started-internet-arm-cli.md)




<!--HONumber=Nov16_HO3-->


