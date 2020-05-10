---
title: SD-WAN 연결 아키텍처
titleSuffix: Azure Virtual WAN
description: Azure 가상 WAN으로 interconnecting a 개인 SD-WAN에 대해 알아봅니다.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 05/08/2020
ms.author: cherylmc
ms.openlocfilehash: 15e44b9c048f167935fe8660228581e5bac0f43d
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2020
ms.locfileid: "83006262"
---
# <a name="sd-wan-connectivity-architecture-with-azure-virtual-wan"></a>Azure 가상 WAN을 사용한 SD-WAN 연결 아키텍처

Azure 가상 WAN은 단일 운영 인터페이스를 사용 하 여 여러 클라우드 연결과 보안 서비스를 함께 제공 하는 네트워킹 서비스입니다. 이러한 서비스에는 분기 (사이트 간 VPN을 통해), 원격 사용자 (지점 및 사이트 간 VPN), 개인 (Express 경로) 연결 뿐만 아니라 Vnet, VPN 및 Express 경로 상호 연결과, 라우팅, Azure 방화벽 및 개인 연결에 대 한 암호화를 위한 클라우드 내부 전이적 연결이 포함 됩니다.

Azure 가상 WAN 자체는 SD WAN (Software Defined wan) 이지만 프레미스 기반 SD WAN 기술 및 서비스와 원활 하 게 상호 연결할 수 있도록 설계 되었습니다. 이러한 많은 서비스는 [가상 WAN](virtual-wan-locations-partners.md) 에코 시스템 및 Azure 네트워킹 [Msps (](../networking/networking-partners-msp.md)관리 서비스 파트너)에서 제공 합니다. 개인 WAN을 SD로 변환 하는 기업에는 Azure 가상 WAN을 사용 하 여 개인 SD-WAN을 interconnecting 때 옵션이 있습니다. 기업에서는 다음 옵션 중에서 선택할 수 있습니다.

* 직접 상호 연결 모델
* 간접 상호 연결 모델
* 즐겨 사용 하는 관리 되는 서비스 공급자 [MSP](../networking/networking-partners-msp.md) 를 사용 하 여 관리 되는 하이브리드 WAN 모델

이러한 모든 경우에서 가상 WAN을 SD와 상호 연결 하는 것은 연결 측면에서 유사 하지만 오케스트레이션과 작업 측면에서 다를 수 있습니다.

## <a name="direct-interconnect-model"></a><a name="direct"></a>직접 상호 연결 모델

![직접 상호 연결 모델](./media/sd-wan-connectivity-architecture/direct.png)

이 아키텍처 모델에서 SD WAN branch CPE (고객-프레미스 장비)는 IPsec 연결을 통해 가상 WAN 허브에 직접 연결 됩니다. 또한 분기 CPE는 개인 SD를 통해 다른 분기에 연결 하거나 분기 연결에 대 한 가상 WAN을 활용할 수 있습니다. Azure에서 워크 로드에 액세스 해야 하는 분기는 가상 WAN 허브에서 종료 되는 IPsec 터널을 통해 Azure에 직접 및 안전 하 게 액세스할 수 있습니다.

SD-WAN CPE 파트너는 각 CPE 장치에서 일반적으로 발생 하 고 오류가 발생 하기 쉬운 IPsec 연결을 자동화 하기 위해 자동화를 사용 하도록 설정할 수 있습니다. Automation을 통해 SD WAN 컨트롤러는 가상 wan API를 통해 Azure와 통신 하 여 가상 WAN 사이트를 구성 하 고, 필요한 IPsec 터널 구성을 해당 지점에 푸시할 수 있습니다. 다양 한 SD WAN 파트너의 가상 WAN 상호 관계 자동화에 대 한 설명은 [automation 지침](virtual-wan-configure-automation-providers.md) 을 참조 하세요.

SD WAN CPE는 트래픽 최적화 및 경로 선택이 구현 되 고 적용 되는 위치를 계속 유지 합니다. 

이 모델에서는 가상 WAN에 대 한 연결이 IPsec을 통해 발생 하 고 IPsec VPN이 가상 WAN VPN gateway에서 종료 되기 때문에 실시간 트래픽 특성을 기반으로 하는 일부 공급 업체 독점 트래픽 최적화가 지원 되지 않을 수 있습니다. 예를 들어 분기 장치에서 다양 한 네트워크 패킷 정보를 다른 SD WAN 노드와 교환 하 여 분기에서 동적 경로를 선택할 수 있으므로 분기에서 동적으로 우선 순위가 지정 된 트래픽에 사용할 최적 링크를 식별 합니다. 이 기능은 지난 마일 최적화 (가장 가까운 Microsoft POP로 분기)가 필요한 영역에서 유용할 수 있습니다.

가상 WAN을 사용 하면 사용자가 Azure 경로 선택 항목을 가져올 수 있습니다 .이는 여러 ISP 링크의 정책 기반 경로 선택으로, 분기에서 가상 WAN VPN 게이트웨이로 연결 됩니다. 가상 WAN을 사용 하면 여러 링크 (경로)를 동일한 SD WAN branch CPE로 설정할 수 있습니다. 각 링크는 SD WAN CPE의 서로 다른 공용 IP 인터페이스에서 종료 됩니다. SD WAN 공급 업체는이 기능을 활용 하 여 이러한 경로와 관련 된 트래픽 정책을 기반으로 가장 최적의 Azure 경로를 선택할 수 있습니다.

## <a name="indirect-interconnect-model"></a><a name="indirect"></a>간접 상호 연결 모델

![간접 상호 연결 모델](./media/sd-wan-connectivity-architecture/indirect.png)

이 아키텍처 모델에서 SD WAN 분기 CPEs는 가상 WAN 허브에 간접적으로 연결 됩니다. 그림에 표시 된 것 처럼 SD WAN 가상 CPE는 엔터프라이즈 VNet에 배포 됩니다. 이 가상 CPE는 IPsec을 사용 하 여 가상 WAN 허브에 연결 됩니다. 가상 CPE는 Azure에 대 한 SD WAN 게이트웨이의 역할을 합니다. Azure에서 워크 로드에 액세스 해야 하는 분기는 v-table 게이트웨이를 통해 액세스할 수 있습니다.

Azure에 대 한 연결은 NVA (vcpe gateway)를 통해 수행 되므로 Azure 워크 로드 Vnet에서 다른 SD로 들어오고 나가는 모든 트래픽이 NVA를 통해 이동 합니다. 이 모델에서 사용자는 고가용성, 확장성 및 라우팅을 포함 하 여 SD WAN NVA를 관리 하 고 운영 하는 일을 담당 합니다.
  
## <a name="managed-hybrid-wan-model"></a><a name="hybrid"></a>관리 하이브리드 WAN 모델

![관리 하이브리드 WAN 모델](./media/sd-wan-connectivity-architecture/hybrid.png)

이 아키텍처 모델에서 기업은 MSP (관리 서비스 공급자) 파트너에서 제공 하는 관리 되는 SD WAN 서비스를 활용할 수 있습니다. 이 모델은 위에서 설명한 직접 또는 간접 모델과 유사 합니다. 그러나이 모델에서는 sd wan 디자인, 오케스트레이션 및 작업이 SD WAN 공급자에 의해 제공 됩니다.

[Azure 네트워킹 MSP 파트너](../networking/networking-partners-msp.md) 는 [azure Lighthouse](https://azure.microsoft.com/services/azure-lighthouse/) 를 사용 하 여 엔터프라이즈 고객의 AZURE 구독에서 SD wan 및 가상 wan 서비스를 구현 하 고 고객 대신 종단 간 하이브리드 WAN을 운영할 수 있습니다. 이러한 MSPs는 가상 WAN에 Azure Express 경로를 구현 하 고 종단 간 관리 서비스로 운영할 수도 있습니다.

## <a name="additional-information"></a>추가 정보

* [FAQ 포함](virtual-wan-faq.md)
* [원격 연결 해결](work-remotely-support.md)