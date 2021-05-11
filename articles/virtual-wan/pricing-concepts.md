---
title: Virtual WAN 가격 책정 정보
titleSuffix: Azure Virtual WAN
description: 이 문서에서는 일반적인 Virtual WAN 가격 책정 관련 질문을 설명합니다.
services: virtual-wan
author: reyandap
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 12/08/2020
ms.author: reyandap
ms.custom: references_pricing
ms.openlocfilehash: 13451291544f704000ab61d41ed5014fa69298e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100517691"
---
# <a name="about-virtual-wan-pricing"></a>Virtual WAN 가격 책정 정보

Azure Virtual WAN은 통합 프레임워크에서 여러 네트워크 및 보안 서비스를 함께 제공합니다. 허브 및 스포크 아키텍처를 기반으로 하며, 여기에서 허브는 허브 내에서 제공되는 다양한 서비스(예: VPN, ExpressRoute, 사용자 VPN(지점 및 사이트 간), 방화벽, 라우팅 등)를 사용하여 Microsoft에서 관리합니다.

Virtual WAN의 각 서비스에는 가격이 책정되어 있습니다. 따라서 단일 가격 제안은 Virtual WAN에 적용되지 않습니다. [Azure 가격 계산기](https://azure.microsoft.com/pricing/calculator/)는 Virtual WAN에서 프로비전된 서비스를 기반으로 비용을 계산하는 메커니즘을 제공합니다. 이 문서에서는 Virtual WAN 가격 책정에 대해 자주 묻는 질문을 설명합니다.

>[!NOTE]
>최신 가격 책정 정보는 [Virtual WAN 가격 책정](https://azure.microsoft.com/pricing/details/virtual-wan/)을 참조하세요. 지역 간(지역 내/대륙 간) 요금이 [Azure 데이터 전송 요금](https://azure.microsoft.com/pricing/details/bandwidth/) 적용 대상이므로 허브 간(허브에서 허브로) 요금은 Virtual WAN 가격 책정 페이지에 표시되지 않습니다.

## <a name="common-pricing-questions"></a><a name="questions"></a>가격 책정 질문

### <a name="what-is-a-scale-unit"></a><a name="scale-unit"></a>배율 단위는 무엇입니까?

**배율 단위** 는 가상 허브의 S2S(사이트 간), P2S(지점 및 사이트 간), ER(ExpressRoute) 집계 용량에 대한 단위를 제공합니다. 예를 들면 다음과 같습니다.

* **1 S2S VPN 배율 단위** 는 가상 허브 비용 $0.361/시간에 총 용량 500Mbps VPN 게이트웨이를 의미합니다(복원력을 위해 이중 인스턴스가 배포됨).
* **1 ER 배율 단위** 는 가상 허브 비용 $0.42/시간에서 총 2Gbps ER 게이트웨이를 의미합니다.
* **5 ER 배율 단위** 는 $0.42*5/시간으로 가격이 책정되는 가상 허브 VNet 내부에 있는 총 10Gbps ER 게이트웨이를 의미합니다. ER은 6~10 배율 단위에서 $0.25/시간만큼 증분됩니다.

### <a name="what-is-a-connection-unit"></a><a name="connection-unit">연결 단위는 무엇입니까?</a>

**연결 단위** 는 Azure 게이트웨이에 연결하는 모든 온-프레미스/타사 엔드포인트에 적용됩니다. 사이트 간 VPN의 경우 이 값은 분기를 의미합니다. 사용자 VPN(지점 및 사이트 간)의 경우 이 값은 원격 사용자를 의미합니다. ExpressRoute의 경우 이 값은 ExpressRoute 회로 연결을 의미합니다.<br>예를 들면 다음과 같습니다.

* 가상 허브에서 Azure VPN에 연결하는 단일 분기 연결 비용은 $0.05/시간입니다. 따라서 Azure 가상 허브에 연결하는 분기 연결 100개의 비용은 $0.05*100/시간입니다.

* 가상 허브에 연결 하는 2개의 ExpressRoute 회로 연결의 비용은 $0.05*2/시간입니다.

* Azure 가상 허브 P2S 게이트웨이에 연결하는 3개의 원격 사용자 연결의 비용은 $0.03*3/시간입니다.

### <a name="how-are-data-transfer-charges-calculated"></a><a name="data-transfer"></a>데이터 전송 요금은 어떻게 계산됩니까?

* Azure에 진입하는 트래픽에는 요금이 청구되지 않습니다. (VPN, ExpressRoute 또는 지점 및 사이트 간 사용자 VPN 연결을 통해) Azure에서 나가는 트래픽에는 표준 [Azure 데이터 전송 요금](https://azure.microsoft.com/pricing/details/bandwidth/)이 적용됩니다.

* Virtual WAN 허브와 원본 허브가 아닌 다른 지역에 있는 원격 Virtual WAN 허브 또는 VNet 간의 데이터 전송 요금에 대해서는 허브에서 나가는 트래픽에 대한 데이터 전송 요금이 적용됩니다. 예: 미국 동부 허브에서 나가는 트래픽은 미국 서부 허브로 이동하는 데 $0.02/GB의 요금이 청구됩니다. 미국 서부 허브로 들어가는 트래픽에 대해서는 요금이 청구되지 않습니다. 모든 허브 간 트래픽은 지역 간(지역 내/대륙 간) 요금인 [Azure 데이터 전송 요금](https://azure.microsoft.com/pricing/details/bandwidth/) 적용 대상입니다. 

### <a name="what-is-the-difference-between-a-standard-hub-fee-and-a-standard-hub-processing-fee"></a><a name="fee"></a>표준 허브 요금과 표준 허브 처리 요금의 차이는 무엇입니까?

Virtual WAN은 다음 두 가지 버전으로 제공됩니다.

* **기본 가상 WAN** 에서 사용자는 여러 허브를 배포하고 VPN 사이트 간 연결을 이용할 수 있습니다. 기본 가상 WAN에는 완전 메시형 허브, ExpressRoute 연결, 사용자 VPN/지점 및 사이트 간 VPN 연결, VNet 간 전이적 연결, VPN 및 ExpressRoute 전송 연결, Azure Firewall 등의 고급 기능이 없습니다. 기본 가상 WAN에는 허브에 대한 기본 요금 또는 데이터 처리 요금이 없습니다.

* **표준 가상 WAN** 은 완전 메시형 허브, ExpressRoute 연결, 사용자 VPN/지점 및 사이트 간 VPN 연결, VNet 간 전이적 연결, VPN 및 ExpressRoute 전송 연결, Azure Firewall 등의 고급 기능을 제공합니다. 가상 허브에서 여러 서비스를 사용할 수 있는 라우터가 모든 가상 허브 라우팅에 제공됩니다. 허브에 대한 기본 요금은 $0.25/시간으로 책정되어 있습니다. 또한 VNet 간 전송 연결에 대한 가상 허브 라우터의 데이터 처리 요금이 청구됩니다. 다음 그림을 참조하세요.

 아래 **예제** 그림에는 VNET 1과 VNET 2라는 2개의 VNet이 있습니다. VNET 1의 VM에서 VNET 2의 다른 VM으로 1Gbps 데이터를 전송하는 경우를 가정해 보겠습니다. 다음 요금이 적용됩니다.

* 가상 허브 기본 요금 $0.25/시간

* 1Gbps에 대한 가상 허브 데이터 처리 요금 $0.02/GB

**예제**

   :::image type="content" source="./media/pricing-concepts/figure-1.png" alt-text="예제":::

## <a name="next-steps"></a>다음 단계

* Virtual WAN에 대한 자세한 내용은 [FAQ](virtual-wan-faq.md)를 참조하세요.

* 최신 가격 책정 [Virtual WAN 가격 책정](https://azure.microsoft.com/pricing/details/virtual-wan/)을 참조하세요.
