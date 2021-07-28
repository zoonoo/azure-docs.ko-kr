---
title: Azure 네트워킹 아키텍처 설명서
description: Azure 네트워킹 서비스에 사용할 수 있는 참조 아키텍처 설명서에 대해 알아봅니다.
services: networking
author: KumudD
ms.service: virtual-network
ms.topic: article
ms.date: 03/30/2021
ms.author: kumud
ms.openlocfilehash: 8ed448f9f4f8dc688271f2ce671b2d89da0f342f
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108146815"
---
# <a name="azure-networking-architecture-documentation"></a>Azure 네트워킹 아키텍처 설명서

이 문서에서는 애플리케이션 빌드에 사용할 수 있는 Azure의 다양한 네트워킹 서비스를 살펴보는 데 도움이 되는 아키텍처 가이드에 대한 정보를 제공합니다.

## <a name="networking-overview"></a>네트워킹 개요

다음 표에는 Azure에서 가상 데이터 센터와 허브 및 스포크 토폴로지의 네트워킹 개요를 제공하는 문서가 포함되어 있습니다.

|제목 |Description  |
|---------|---------|
|[가상 데이터 센터](/azure/architecture/vdc/networking-virtual-datacenter)   | Azure의 가상 데이터 센터에 대한 네트워킹 큐브 뷰를 제공합니다.       |
|[허브-스포크 토폴로지](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)  |구독 제한과 여러 허브에 대한 정보와 함께 Azure의 허브 및 스포크 네트워크 토폴로지에 대한 개요를 제공합니다.          |

## <a name="connect-to-azure-resources"></a>Azure 리소스에 연결

다음 표에는 Azure 리소스 간 연결, 온-프레미스 네트워크에서 Azure 리소스로의 연결, Azure에서 분기 간 연결을 제공하는 Azure 네트워킹 서비스에 대한 문서가 포함되어 있습니다.

|제목 |Description  |
|---------|---------|
|[피어링된 가상 네트워크에 IP 주소 공간 추가](/azure/architecture/networking/prefixes/add-ip-space-peered-vnet)     | 피어링 가상 네트워크에 IP 주소 공간을 추가하는 데 도움이 되는 스크립트를 제공합니다.        |
|[Azure 네트워크 어댑터를 사용하여 독립 실행형 서버 연결](/azure/architecture/hybrid/azure-network-adapter)   | Windows Admin Center를 통해 배포하는 Azure 네트워크 어댑터를 사용하여 온-프레미스 독립 실행형 서버를 Microsoft Azure 가상 네트워크에 연결하는 방법을 보여 줍니다.        |
|[가상 네트워크 피어링 및 VPN 게이트웨이 중에서 선택](/azure/architecture/reference-architectures/hybrid-networking/vnet-peering)   | Azure에서 가상 네트워크를 연결하는 두 가지 방법, 즉 가상 네트워크 피어링과 VPN 게이트웨이를 비교합니다.        |
|[Azure에 온-프레미스 네트워크 연결](/azure/architecture/reference-architectures/hybrid-networking/)  | 온-프레미스 네트워크를 Azure VNet(Virtual Network)에 연결하는 옵션을 비교합니다. 각 옵션에서 자세한 참조 아키텍처를 사용할 수 있습니다.        |
|[Azure Virtual WAN을 사용한 SD-WAN 연결 아키텍처](../../virtual-wan/sd-wan-connectivity-architecture.md)|Azure Virtual WAN을 사용하여 프라이빗 SD-WAN(Software Defined WAN)을 상호 연결하는 다양한 연결 옵션을 설명합니다.|

## <a name="deploy-highly-available-applications"></a>고가용성 애플리케이션 배포

다음 표에는 Azure 네트워킹 서비스의 조합을 사용하여 고가용성을 위해 애플리케이션을 배포하는 방법을 설명하는 문서가 포함되어 있습니다.

|제목 |Description  |
|---------|---------|
|[다중 지역 N 계층 애플리케이션](/azure/architecture/reference-architectures/n-tier/multi-region-sql-server)  | Traffic Manager를 사용하여 들어오는 요청을 주 지역으로 라우팅하고, 해당 지역을 사용할 수 없게 되면 Traffic Manager이 보조 지역으로 장애 조치(failover)하는 다중 지역 n 계층 애플리케이션을 설명합니다.      |
| [Azure의 다중 테넌트 SaaS](/azure/architecture/example-scenario/multi-saas/multitenant-saas)       |   Front Door와 Application Gateway의 조합을 포함하는 다중 테넌트 솔루션을 사용합니다.  Front Door를 사용하면 지역 간 트래픽 부하를 분산하고, Application Gateway는 애플리케이션에서 트래픽 부하를 내부적으로 분산하여 클라이언트 비즈니스 요구를 충족하는 다양한 서비스에 라우팅합니다.  |
| [고가용성 및 재해 복구를 위한 다중 계층 웹 애플리케이션](/azure/architecture/example-scenario/infrastructure/multi-tier-app-disaster-recovery)        |      고가용성 및 재해 복구를 위한 다중 계층 애플리케이션을 배포합니다. 주 지역을 사용할 수 없게 되면 Traffic Manager가 보조 지역으로 장애 조치합니다.  |
|[IaaS: 관계형 데이터베이스를 사용하는 웹 애플리케이션](/azure/architecture/high-availability/ref-arch-iaas-web-and-db)    |   여러 영역에 걸쳐 있는 리소스를 사용하여 IaaS(Infrastructure as a Service) 웹 애플리케이션 및 SQL Server 데이터베이스를 호스트하기 위한 고가용성 아키텍처 제공 방법을 설명합니다.     |
|[저렴한 서버리스 Azure 서비스를 사용한 실시간 위치 공유](/azure/architecture/example-scenario/signalr/#azure-front-door)       |   Azure Front Door를 사용하여 단일 지역에 배포하는 것보다 더 높은 가용성을 애플리케이션에 제공합니다. 지역 중단이 주 지역에 영향을 미치는 경우 Front Door를 사용하여 보조 지역으로 장애 조치(failover)할 수 있습니다.      |
|[고가용성 네트워크 가상 어플라이언스](/azure/architecture/reference-architectures/dmz/nva-ha)     | Azure에서 고가용성을 위한 일련의 NVA(네트워크 가상 어플라이언스)를 배포하는 방법을 보여 줍니다.        |
|[Traffic Manager 및 Application Gateway를 통해 다중 지역 부하 분산](/azure/architecture/high-availability/reference-architecture-traffic-manager-application-gateway)     | 가용성 및 강력한 재해 복구 인프라를 달성하기 위해 여러 Azure 지역에 복원력이 뛰어난 다중 계층 애플리케이션을 배포하는 방법을 설명합니다.        |

## <a name="secure-your-network-resources"></a>네트워크 리소스 보안 유지

다음 표에는 Azure 네트워킹 서비스를 사용하여 네트워크 리소스를 보호하는 방법을 설명하는 문서가 포함되어 있습니다.

|제목 |Description  |
|---------|---------|
|[네트워크 보안 모범 사례](../../security/fundamentals/network-best-practices.md) |네트워크 보안을 향상하기 위한 Azure 모범 사례 컬렉션을 설명합니다.         |
[Azure Firewall 아키텍처 가이드](/azure/architecture/example-scenario/firewalls/) | 타사 가상 어플라이언스를 사용하여 Azure에서 고가용성 방화벽을 디자인하기 위해 구조화된 접근 방식을 제공합니다.        |
|[보안 하이브리드 네트워크 구현](/azure/architecture/reference-architectures/dmz/secure-vnet-dmz)     | 온-프레미스 네트워크와 Azure 가상 네트워크 간의 경계 네트워크라고도 하는 DMZ를 구현하는 아키텍처를 설명합니다. 모든 인바운드 트래픽 및 아웃바운드 트래픽은 Azure Firewall을 통해 전달됩니다.        |
|[네트워크 수준 세분화를 통해 워크로드 보호 및 제어](/azure/architecture/reference-architectures/hybrid-networking/network-level-segmentation) | 네트워킹 큐브 뷰에서 Azure의 워크로드를 구성하는 데 사용되는 세 가지 일반적인 패턴을 설명합니다.   각 패턴은 서로 다른 형식의 격리 및 연결을 제공합니다.      |
|[가상 네트워크에 대한 방화벽 및 Application Gateway](/azure/architecture/example-scenario/gateway/firewall-application-gateway) | Azure Firewall 및 Azure Application Gateway와 같은 Azure Virtual Network 보안 서비스에 대해 각 서비스를 사용하는 경우와 두 서비스를 조합하는 네트워크 디자인 옵션을 설명합니다.      |

## <a name="next-steps"></a>다음 단계

[Azure Virtual Network](../../virtual-network/virtual-networks-overview.md)에 관한 자세한 정보.