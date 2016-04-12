<properties 
   pageTitle="IPv6 개요 | Microsoft Azure"
   description="Azure의 IPv6 주소 지정에 대해 알아보기"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/04/2016"
   ms.author="telmos" />

# Azure의 IPv6 지원

IPv6 프로토콜은 공용 인터넷에서의 32비트 IPv4 주소 제한을 해결하기 위해 1998년 IETF(Internet Engineering Task Force)에 의해 처음 도입되었습니다. 인터넷에 연결하는 모바일 장치의 수가 늘어나고 IoT(사물 인터넷)가 등장함에 따라 공용 인터넷에서 IPv6 주소가 사용되는 일이 점점 빈번해지고 있습니다.

>[AZURE.IMPORTANT] Azure의 IPv6은 현재 비공개 미리 보기이므로 모든 고객이 사용할 수는 없습니다. 모든 고객이 기능을 사용할 수 있게 되면 이 페이지가 업데이트될 예정입니다.

Resource Manager 배포의 Azure IaaS VM에서 공용 인터넷에 대한 아웃바운드 및 인바운드 연결에 대해 IPv6 주소를 사용할 수 있습니다. 다음 목록에서는 Azure의 IPv6 기능을 설명합니다.

- **Resource Manager의 IaaS VM**
	- VM에는 IPv4 및 IPv6 끝점이 모두 있습니다.
	- VM은 인바운드(부하 분산 장치 및 NAT) 연결 및 아웃바운드(SNAT) 연결 시 부하 분산 장치 공용 IP를 통해 인터넷에 연결할 수 있습니다.
	- VM은 IPv4 끝점을 사용하여 모든 Azure 서비스에 연결합니다.
	- VM은 동-서 통신에 대해 IPv4 끝점만 사용합니다.
- **Azure DNS**
	- Azure DNS는 IPv6을 지원하며 IPv4 A 레코드와 함께 AAAA 레코드를 제공합니다.
	- Azure DNS는 AAAA 및 A 레코드가 모두 존재할 경우 이에 대한 조회 쿼리에 응답합니다. 
- **Office365**
	- Office365 서비스는 IPv6을 지원합니다.
	- Express 경로 회로를 사용하여 온-프레미스 네트워크에서 Office365의 IPv6 리소스에 연결할 수 있습니다.

## Resource Manager의 IaaS VM

리소스 관리 배포 모델을 사용해야만 IPv6 연결을 활용할 수 있습니다. 아래 표시된 것처럼 Resource Manager 배포의 모든 VM에 IPv4 및 IPv6 끝점이 있지만 IPv6 끝점은 부하 분산 장치를 통한 공용 인터넷 연결에만 사용할 수 있습니다.

![IPv6 연결](./media/virtual-network-ipv6-overview/figure1.png)

## Azure DNS

Azure DNS에서 IPv6 AAAA 레코드를 호스트할 수 있습니다. Azure DNS에서 리소스에 대한 쿼리를 받으면 해당 호스트 이름에 대해 사용할 수 있는 모든 레코드로 응답합니다. 예를 들어 특정 호스트 이름(예: www.contoso.com)에 대해 A 레코드 및 AAAA 레코드가 있는 경우 Azure DNS는 쿼리에 대한 응답으로 두 주소를 모두 보냅니다. Azure DNS 또는 AAAA 레코드를 지원하는 다른 DNS 서버로부터 응답을 받은 경우 연결 시 IPv4 또는 IPv6 끝점을 사용할지는 DNS 클라이언트가 결정합니다.

>[AZURE.NOTE] Windows 컴퓨터는 항상 IPv4 연결보다 먼저 IPv6을 사용하여 연결하려고 시도합니다.

## Office 365

Express 경로 회로 및 Microsoft 피어링을 사용하여 온-프레미스 네트워크와 Office365 간에 IPv6 연결을 사용할 수 있습니다. Microsoft 피어링을 사용하여 연결하려면 [Express 경로의 작동 원리](../expressroute/expressroute-introduction.md)를 이해해야 합니다.

## 다음 단계

- [Azure DNS](../dns/dns-overview.md)에 대해 알아보기
- [Express 경로](../expressroute/expressroute-introduction.md)에 대해 알아보기

<!---HONumber=AcomDC_0309_2016-->