---
title: Azure 가상 WAN 전역 전송 네트워크 아키텍처 | Microsoft Docs
description: 가상 WAN에 대 한 전역 전송 네트워크 아키텍처에 알아봅니다
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 05/06/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to understand global transit network architecture as it relates to Virtual WAN.
ms.openlocfilehash: 8cda617ca60a17fceaaa818480ff9bbaef46c3fd
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65414062"
---
# <a name="global-transit-network-architecture-and-virtual-wan"></a>전역 전송 네트워크 아키텍처 및 가상 WAN

전역 전송 네트워크 아키텍처 통합, 연결 및 클라우드 중심 최신 엔터프라이즈 IT 사용 공간을 제어 하는 기업에서 채택 합니다. 최신 클라우드 중심 엔터프라이즈에서 네트워크 트래픽을 backhauled HQ 사용할 필요가 없습니다. 전역 전송 네트워크 아키텍처는 친숙 한 네트워킹 개념 및 클라우드 및 클라우드 기반 아키텍처에 고유한 새로운 개념을 기반으로 합니다.

![아키텍처](./media/virtual-wan-global-transit-network-architecture/architecture2.png)

**그림 1: 가상 WAN을 사용 하 여 전역 전송 네트워크**

오늘날의 기업 클라우드 및 온-프레미스 하이퍼 분산 된 응용 프로그램, 데이터 및 사용자 간의 보편적 연결이 필요합니다. Azure 가상 WAN Vnet, 사이트, 응용 프로그램 및 사용자가 전역으로 분산 된 집합 간의 유비쿼터스, any-임의의 연결을 사용 하 여 전역 전송 네트워크 아키텍처를 허용 합니다. Azure 가상 WAN는 Microsoft 관리 서비스입니다. 이 서비스는 구성 된 모든 네트워킹 구성 호스트 되며 Microsoft에서 관리 됩니다. 가상 WAN에 대 한 자세한 내용은 참조는 [가상 WAN 개요](virtual-wan-about.md) 문서.

Azure 지역 Azure 가상 WAN 아키텍처에서 분기를 연결 하도록 선택할 수는 허브 역할을 합니다. 분기 연결 되 면 분기-VNet을 설정 하 고 Azure 백본을 활용할 수 있습니다 및 필요에 따라 분기-분기 연결 합니다.

가상 WAN 스포크 (지점, Vnet, 사용자)로의 최대 수 있는 지역에 단일 가상 WAN 허브를 만들고 허브에 다른 지역에 있는 스포크 연결 하 여 설정할 수 있습니다. 또는 스포크는 지리적으로 분산 하는 경우 또한 지역별 허브 인스턴스화할를 허브를 상호 연결 합니다. 허브에는 동일한 가상 WAN의 일부인 모든 하지만 다른 지역 정책과 연결할 수 있습니다.

## <a name="hub"></a>허브-스포크 전송

전역 전송 네트워크 아키텍처는 클라우드 호스팅 네트워크 '허브' 다양 한 유형의 '스포크' 분산 될 수 있는 끝점 간의 전이적 연결 기능을 통해 여기서 클래식 허브 및 스포크 연결 모델을 기반으로 합니다.
  
이 모델에서는 스포크 수 있습니다.

* 가상 네트워크 (Vnet)
* 실제 분기 사이트
* 원격 사용자
* 인터넷

![허브 및 스포크 전역 전송 다이어그램](./media/virtual-wan-global-transit-network-architecture/architecture.png)

**그림 2: Hub-and-spoke**

그림 2 논리 뷰 지리적으로 분산된 된 사용자, 실제 사이트 및 Vnet 클라우드에서 호스트 네트워킹 허브를 통해 상호 연결 되어 있는 글로벌 네트워크를 보여 줍니다. 이 아키텍처는 네트워킹 끝점 간에 논리 1 홉 전송 연결입니다. 스포크는 다양 한 Azure 네트워킹 서비스에서 ExpressRoute 또는 사이트 간 사이트 VPN과 같은 실제 분기, Vnet 및 원격 사용자에 대 한 지점-사이트 간 VPN에 대 한 피어 링 된 VNet에 대 한 허브에 연결 됩니다.

## <a name="crossregion"></a>지역 간 연결

기업에서는 클라우드 공간은 일반적으로 실제 설치 공간을 따릅니다. 대부분의 기업은 해당 물리적 사이트 및 사용자에 게 가장 가까운 지역에서 클라우드를 액세스 합니다. 네트워크 엔터티와 끝점 간의 지역 간 연결을 사용 하도록 설정 방법은 전역 네트워크 아키텍처의 주요 보안 주체 중 하나입니다. 클라우드 사용 공간을 여러 지역에 걸쳐 있을 수 있습니다. 이 트래픽이 한 지역에서 클라우드로 연결 된 분기에서 다른 분기 또는 다른 지역의 VNet에 연결할 수 있는지를 의미 합니다.

## <a name="any"></a>Any-임의의 연결

전역 전송 네트워크 아키텍처를 사용 하면 *any-임의의 연결* 중앙 네트워크 허브를 통해. 이 아키텍처는 제거 또는 전체 메시 또는 빌드 및 유지 관리 보다 복잡 한 부분 메시 연결 모델에 대 한 필요성이 줄어듭니다. 또한 허브 및 스포크 메시 네트워크 및 라우팅 컨트롤 구성 및 유지 관리 하기가 쉽습니다.

Any-임의의 연결 전역 아키텍처 측면에서 전역적으로 분산된 된 사용자, 분기, 데이터 센터, Vnet 및 전송 hub를 통해 서로 연결 하도록 응용 프로그램을 사용 하 여 엔터프라이즈를 허용 합니다. 전송 허브 전역 전송 시스템으로 작동합니다.

![트래픽 경로](./media/virtual-wan-global-transit-network-architecture/trafficpath.png)

**그림 3: 가상 WAN 트래픽 경로**

Azure 가상 WAN 다음 전역 전송 연결 경로 지원합니다. 괄호 안에 있는 문자는 그림 3에 매핑됩니다.

* 분기-VNet (a)  
* 분기-분기 (b)
* 원격 사용자-VNet (c)
* 원격 사용자를-분기 (d)
* VNet 대 VNet VNet 피어 링 (e)를 사용 하 여
* ExpressRoute에 대 한 글로벌 정보 

### <a name="branchvnet"></a>Branch-to-VNet

분기-VNet에 Azure 가상 WAN에서 지원 되는 기본 경로입니다. 이 경로 사용 하면 Azure Vnet에 배포 되는 엔터프라이즈 워크 로드를 Azure IAAS에 분기를 연결할 수 있습니다. 분기는 ExpressRoute 또는 사이트 간 VPN을 통해 가상 WAN에 연결할 수 있습니다. VNet 연결을 통해 가상 WAN 허브에 연결 된 Vnet에 대 한 트래픽 전송은 합니다.

### <a name="branchbranch"></a>Branch-to-branch

분기는 ExpressRoute 회로 및/또는 사이트 간 VPN 연결을 사용 하 여 Azure 가상 WAN 허브에 연결할 수 있습니다. 분기를 분기에 가장 가까운 지역에 있는 가상 WAN 허브에 연결할 수 있습니다.

이 옵션을 사용 하면 분기를 연결 하 고 Azure 백본을 활용 하는 기업입니다. 그러나이 기능을 사용할 수 있지만 Azure 가상 WAN을 통해 개인 WAN을 사용 하 여 비교 분기를 연결 하는 이점을 평가 해야 합니다.

### <a name="usertovnet"></a>원격 사용자-VNet

가상 WAN에 원격 사용자 클라이언트에서 지점-사이트 간 연결을 사용 하 여 Azure에 직접이 고 안전한 원격 액세스를 사용할 수 있습니다. 엔터프라이즈 원격 사용자를 사용 하 여 회사 VPN 클라우드에 hairpin 더 필요가 없습니다.

### <a name="usertobranch"></a>원격 사용자-분기

사용자-분기를 원격 경로 사용 하면 클라우드를 통해 전송 하 여 Azure 액세스 온-프레미스 워크 로드 및 응용 프로그램에는 지점-사이트 간 연결을 사용 하는 원격 사용자가 있습니다. 이 경로 원격 사용자 유연성에 배포 된 Azure 및 온-프레미스 모두 않는 액세스 워크 로드를 제공 합니다. 기업에서 Azure 가상 WAN 중앙 클라우드 기반 보안 원격 액세스 서비스를 설정할 수 있습니다.

### <a name="vnetvnet"></a>VNet 피어 링을 사용 하 여 VNet 대 VNet 전송

여러 Vnet에서 구현 되는 다중 계층 응용 프로그램을 지원 하기 위해 서로에 Vnet를 연결, VNet 피어 링을 사용 합니다. Azure 가상 WAN을 통해 VNet 대 VNet 전송 시나리오는 현재 지원 되지 않는 이지만 Azure 로드맵 상에 있습니다. VNet 피어 링을 통해 Vnet을 연결 하는 것은 서로 연결 해야 하는 Vnet에 대 한 권장 솔루션입니다. VNet 피어 링 하는 방법에 대 한 자세한 내용은 참조 하세요. [VNet 피어 링 개요](../virtual-network/virtual-network-peering-overview.md)합니다.

### <a name="globalreach"></a>ExpressRoute Global Reach

ExpressRoute는 Microsoft 클라우드로 온-프레미스 네트워크를 연결 하는 개인 및 복원 력 있는 방법입니다. ExpressRoute 글로벌 환경 ExpressRoute에 대 한 추가 기능입니다. 글로벌 환경으로 표시 하기 위해 온-프레미스 네트워크 간의 개인 네트워크를 ExpressRoute 회로 연결할 수 있습니다. Azure 가상 WAN에 ExpressRoute를 사용 하 여 연결 된 분기는 ExpressRoute 글로벌 환경 상호 통신에 필요 합니다.

이 모델에서 ExpressRoute를 사용 하 여 가상 WAN 허브에 연결 되어 있는 각 분기는 분기와 VNet 간 경로 사용 하 여 Vnet에 연결할 수 있습니다. 분기-분기 트래픽을 Azure WAN을 통해 좀 더 최적의 경로 사용 하면 ExpressRoute 글로벌 환경 때문에 허브를 전송 되지 않습니다.

## <a name="security"></a>보안 및 정책 제어

가상 네트워크 허브 상호 연결 및 잠재적으로 모든 전송 트래픽을 확인 합니다. 호스트 중앙 네트워킹 기능 및 이러한는 클라우드 라우팅, 네트워크 정책 및 보안 및 인터넷 액세스 제어와 같은 서비스 위치 수 있습니다.

## <a name="next-steps"></a>다음 단계

가상 WAN을 사용 하 여 연결을 만듭니다.

* [가상 WAN을 사용 하 여 사이트 간 연결](virtual-wan-site-to-site-portal.md)
* [가상 WAN을 사용 하 여 지점 및 사이트 연결](virtual-wan-point-to-site-portal.md)
* [가상 WAN을 사용 하 여 ExpressRoute 연결](virtual-wan-expressroute-portal.md)
