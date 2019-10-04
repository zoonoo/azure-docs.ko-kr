---
title: Azure 가상 WAN 글로벌 전송 네트워크 아키텍처 | Microsoft Docs
description: 가상 WAN의 글로벌 전송 네트워크 아키텍처에 대해 알아보기
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 07/23/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to understand global transit network architecture as it relates to Virtual WAN.
ms.openlocfilehash: 0a5059382c26afd6120dc14a1ab2c7e5d281e7a1
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695263"
---
# <a name="global-transit-network-architecture-and-virtual-wan"></a>글로벌 전송 네트워크 아키텍처 및 가상 WAN

기업에서 글로벌 전송 네트워크 아키텍처를 채택 하 여 클라우드 중심 현대 엔터프라이즈 IT의 공간을 통합 하 고, 연결 하 고, 제어할 수 있습니다. 최신 클라우드 중심 기업의 경우 네트워크 트래픽이 backhauled 필요가 없습니다. 글로벌 전송 네트워크 아키텍처는 친숙 한 네트워킹 개념과 클라우드 기반 아키텍처에 고유한 새로운 개념을 기반으로 합니다.

![architecture](./media/virtual-wan-global-transit-network-architecture/architecture2.png)

**그림 1: 가상 WAN을 사용 하는 글로벌 전송 네트워크 @ no__t-0

최신 기업은 클라우드 및 온-프레미스에서 하이퍼 분산 응용 프로그램, 데이터 및 사용자 간에 다양 한 연결이 필요 합니다. Azure 가상 WAN은 Vnet, 사이트, 응용 프로그램 및 사용자의 전 세계에 분산 된 집합 간에 다양 한 연결을 가능 하 게 하 여 글로벌 전송 네트워크 아키텍처를 허용 합니다. Azure 가상 WAN은 Microsoft에서 관리 하는 서비스입니다. 이 서비스가 구성 된 모든 네트워킹 구성 요소는 Microsoft에서 호스트 되 고 관리 됩니다. 가상 WAN에 대 한 자세한 내용은 [가상 Wan 개요](virtual-wan-about.md) 문서를 참조 하세요.

Azure 가상 WAN 아키텍처에서 Azure 지역은 분기를 연결 하기 위해 선택할 수 있는 허브 역할을 합니다. 분기가 연결 되 면 Azure 백본을 활용 하 여 분기 간 연결을 설정 하 고 필요에 따라 분기 간 연결을 설정할 수 있습니다.

스포크 (분기, Vnet, 사용자) 수가 가장 많은 지역에서 단일 가상 WAN 허브를 만든 다음 다른 지역의 스포크를 허브에 연결 하 여 가상 WAN을 설정할 수 있습니다. 또는 스포크가 지리적으로 분산 된 경우 지역 허브를 인스턴스화하고 허브를 상호 연결할 수도 있습니다. 허브는 모두 동일한 가상 WAN의 일부 이지만 서로 다른 지역 정책에 연결 될 수 있습니다.

## <a name="hub"></a>허브 및 스포크 전송

글로벌 전송 네트워크 아키텍처는 클라우드 호스 티 드 네트워크 ' 허브 '를 사용 하 여 여러 종류의 ' 스포크 '에 분산 될 수 있는 끝점 간의 전이적 연결을 지 원하는 클래식 허브 및 스포크 연결 모델을 기반으로 합니다.
  
이 모델에서 스포크는 다음과 같을 수 있습니다.

* 가상 네트워크 (Vnet)
* 실제 분기 사이트
* 원격 사용자
* 인터넷

![허브 및 스포크 글로벌 전송 다이어그램](./media/virtual-wan-global-transit-network-architecture/architecture.png)

**그림 2: 허브 및 스포크 @ no__t-0

그림 2에서는 지리적으로 분산 된 사용자, 물리적 사이트 및 Vnet 클라우드에서 호스트 되는 네트워킹 허브를 통해 상호 연결 된 글로벌 네트워크의 논리적 보기를 보여 줍니다. 이 아키텍처는 네트워킹 끝점 간에 논리적 1 홉 전송 연결을 사용 하도록 설정 합니다. 스포크는 Express 경로 또는 사이트 간 VPN과 같은 다양 한 Azure 네트워킹 서비스에 의해 허브에 연결 됩니다. 예를 들어 Vnet에 대 한 VNet 연결, 원격 사용자를 위한 지점 및 사이트 간 VPN을 사용할 수 있습니다.

## <a name="crossregion"></a>지역 간 연결

기업의 경우 클라우드 공간은 일반적으로 물리적 공간을 따릅니다. 대부분의 기업은 물리적 사이트 및 사용자에 게 가장 가까운 지역에서 클라우드에 액세스 합니다. 글로벌 네트워크 아키텍처의 핵심 주체 중 하나는 네트워크 엔터티 및 끝점 간에 지역 간 연결을 사용 하도록 설정 하는 것입니다. 클라우드 공간은 여러 지역에 걸쳐 있을 수 있습니다. 즉, 한 지역의 클라우드에 연결 된 분기의 트래픽은 허브 간 연결을 사용 하 여 다른 지역에 있는 다른 분기 또는 VNet에 연결할 수 있습니다 .이는 현재 로드맵에 있습니다.

## <a name="any"></a>임의 연결

글로벌 전송 네트워크 아키텍처는 중앙 네트워크 허브를 통해 모든 *연결을 가능 하 게* 합니다. 이 아키텍처는 빌드 및 유지 관리가 더 복잡 한 전체 메시 또는 부분 메시 연결 모델에 대 한 필요성을 제거 하거나 감소 시킵니다. 또한 허브 및 스포크 네트워크의 라우팅 제어를 구성 하 고 유지 관리 하는 것이 더 쉽습니다.

모든 연결 (전역 아키텍처의 컨텍스트에서)은 전 세계적으로 분산 된 사용자, 분기, 데이터 센터, Vnet 및 응용 프로그램을 통해 전송 허브를 통해 서로 연결할 수 있습니다. 전송 허브는 글로벌 전송 시스템 역할을 합니다.

![트래픽 경로](./media/virtual-wan-global-transit-network-architecture/trafficpath.png)

**그림 3: 가상 WAN 트래픽 경로 @ no__t-0

Azure 가상 WAN은 다음과 같은 글로벌 전송 연결 경로를 지원 합니다. 괄호 안의 문자는 그림 3에 매핑됩니다.

* VNet (a)에 분기  
* 분기 분기 (b)
* 원격 사용자-VNet (c)
* 원격 사용자-분기 (d)
* Vnet 피어 링 (e)을 사용 하는 vnet 간
* ExpressRoute Global Reach 

### <a name="branchvnet"></a>VNet에 분기

VNet은 Azure 가상 WAN에서 지 원하는 기본 경로입니다. 이 경로를 사용 하 여 Azure Vnet에 배포 된 Azure IAAS enterprise 워크 로드에 분기를 연결할 수 있습니다. 분기는 Express 경로 또는 사이트 간 VPN을 통해 가상 WAN에 연결 될 수 있습니다. 전송은 트래픽은 VNet 연결을 통해 가상 WAN 허브에 연결 되는 Vnet에 연결 됩니다. 가상 WAN은 게이트웨이를 분기 사이트로 자동으로 전송할 수 있으므로 가상 WAN에 대 한 [게이트웨이 전송은](../virtual-network/virtual-network-peering-overview.md#gateways-and-on-premises-connectivity) 필요 하지 않습니다.

### <a name="branchbranch"></a>분기 분기

분기는 Express 경로 회로 및/또는 사이트 간 VPN 연결을 사용 하 여 Azure 가상 WAN 허브에 연결할 수 있습니다. 분기와 가장 가까운 지역에 있는 가상 WAN 허브에 분기를 연결할 수 있습니다.

이 옵션을 통해 기업은 Azure 백본을 활용 하 여 분기를 연결할 수 있습니다. 그러나이 기능을 사용할 수 있는 경우에도 Azure 가상 WAN을 통해 분기를 연결 하는 이점 및 사설 WAN 사용의 이점을 비교 해야 합니다.

### <a name="usertovnet"></a>원격 사용자-VNet

원격 사용자 클라이언트에서 가상 WAN으로 지점 및 사이트 간 연결을 사용 하 여 Azure에 대 한 직접 보안 원격 액세스를 사용 하도록 설정할 수 있습니다. 엔터프라이즈 원격 사용자는 더 이상 회사 VPN을 사용 하 여 클라우드로 hairpin 필요가 없습니다.

### <a name="usertobranch"></a>원격 사용자-분기

원격 사용자-분기 경로를 사용 하면 Azure에 대 한 지점 및 사이트 간 연결을 사용 하는 원격 사용자가 클라우드를 통해 전송을 하 여 온-프레미스 워크 로드 및 응용 프로그램에 액세스할 수 있습니다. 이 경로를 통해 원격 사용자는 Azure 및 온-프레미스에 배포 된 워크 로드에 유연 하 게 액세스할 수 있습니다. 기업은 Azure Virtual WAN에서 중앙 클라우드 기반 보안 원격 액세스 서비스를 사용 하도록 설정할 수 있습니다.

### <a name="vnetvnet"></a>Vnet 피어 링을 사용 하 여 vnet 간 전송

여러 Vnet에서 구현 되는 다중 계층 응용 프로그램을 지원 하기 위해 Vnet을 서로 연결 하려면 VNet 피어 링을 사용 합니다. Azure 가상 WAN을 통해 VNet 간 전송 시나리오는 현재 지원 되지 않지만 Azure 로드맵에 있습니다. VNet 피어 링을 통해 Vnet를 연결 하는 것은 서로 연결 해야 하는 Vnet에 대 한 권장 솔루션입니다. 

### <a name="globalreach"></a>Express 경로 Global Reach

Express 경로는 온-프레미스 네트워크를 Microsoft 클라우드에 연결 하는 개인적이 고 복원 력 있는 방법입니다. Express 경로 Global Reach는 Express 경로에 대 한 추가 기능입니다. Global Reach를 통해 Express 경로 회로를 함께 연결 하 여 온-프레미스 네트워크 간에 개인 네트워크를 만들 수 있습니다. Express 경로를 사용 하 여 Azure 가상 WAN에 연결 된 분기에는 Express 경로 Global Reach 서로 통신 해야 합니다.

이 모델에서 Express 경로를 사용 하 여 가상 WAN 허브에 연결 된 각 분기에서 VNet 간 경로를 사용 하 여 Vnet에 연결할 수 있습니다. Express 경로 Global Reach는 Azure WAN을 통해 더 최적의 경로를 사용할 수 있으므로 분기 간 트래픽은 허브를 전송 하지 않습니다.

## <a name="security"></a>보안 및 정책 제어

가상 네트워크 허브 상호 간의 통신 및 잠재적으로 모든 전송 트래픽을 볼 수 있습니다. 이러한 클라우드 라우팅, 네트워크 정책, 보안 및 인터넷 액세스 제어와 같은 중앙 네트워킹 기능 및 서비스를 호스트 하는 장소가 될 수 있습니다.

## <a name="next-steps"></a>다음 단계

가상 WAN을 사용 하 여 연결을 만듭니다.

* [가상 WAN을 사용 하 여 사이트 간 연결](virtual-wan-site-to-site-portal.md)
* [가상 WAN을 사용한 Express 경로 연결](virtual-wan-expressroute-portal.md)
