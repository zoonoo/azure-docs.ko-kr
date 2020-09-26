---
title: 가상 WAN 가격 정보
titleSuffix: Azure Virtual WAN
description: 이 문서에서는 일반적인 가상 WAN 가격 관련 질문을 설명 합니다.
services: virtual-wan
author: reyandap
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: reyandap
ms.custom: references_pricing
ms.openlocfilehash: b4025990a1a62351d3971d788558dea8ecb390ba
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91327960"
---
# <a name="about-virtual-wan-pricing"></a>가상 WAN 가격 정보

Azure 가상 WAN은 통합 프레임 워크에서 여러 네트워크 및 보안 서비스를 함께 제공 합니다. 허브 및 스포크 아키텍처를 기반으로 합니다. 여기에서 허브는 허브 내에서 제공 되는 다양 한 서비스 (예: VPN, Express 경로, 사용자 VPN (지점 및 사이트 간), 방화벽, 라우팅 등)를 사용 하 여 Microsoft에서 관리 됩니다.

가상 WAN의 각 서비스는 가격이 책정 됩니다. 따라서 단일 가격을 제안 하는 것은 가상 WAN에는 적용 되지 않습니다. [Azure 가격 계산기](https://azure.microsoft.com/pricing/calculator/) 는 가상 WAN에서 프로 비전 된 서비스를 기반으로 비용을 도출 하는 메커니즘을 제공 합니다. 이 문서에서는 가상 WAN 가격에 대 한 자주 묻는 질문을 설명 합니다.

>[!NOTE]
>최신 가격 책정 정보는 [가상 WAN 가격](https://azure.microsoft.com/pricing/details/virtual-wan/)을 참조 하세요.
>

## <a name="common-pricing-questions"></a><a name="questions"></a>일반적인 가격 책정 질문

### <a name="what-is-a-scale-unit"></a><a name="scale-unit"></a>배율 단위 란 무엇 인가요?

**배율 단위** 는 가상 허브에서 S2S (사이트 간), 지점 및 사이트 간 (P2S) 및 express 경로 (ER)의 용량을 집계 하는 단위를 제공 합니다. 예를 들면 다음과 같습니다.

* **1 S2S vpn 배율 단위** 는 가상 허브 비용 $0.361/시간에 500 Mbps vpn 게이트웨이의 총 용량을 의미 합니다 (이중 인스턴스는 복원 력을 위해 배포 됨).
* **1 ER 배율 단위** 는 가상 허브 비용 $0.42/hr에서 총 2 Gbps ER 게이트웨이를 의미 합니다.
* **5 ER 배율 단위** 는 $0.42 * 5/시간으로 가격이 책정 되는 가상 허브 VNet 내부에 총 10gbps의 게이트웨이를 의미 합니다. ER는 6 ~ 10 번째 배율 단위에서 $0.25/hr을 증가 시킵니다.

### <a name="what-is-a-connection-unit"></a><a name="connection-unit"></a>연결 단위 란 무엇 인가요?

**연결 단위** 는 Azure 게이트웨이에 연결 하는 모든 온-프레미스/비 Microsoft 끝점에 적용 됩니다. 사이트 간 VPN의 경우 분기를 의미 합니다. 사용자 VPN (지점 및 사이트 간)의 경우 원격 사용자를 의미 합니다. Express 경로의 경우이는 Express 경로 회로 연결을 의미 합니다.<br>예를 들면 다음과 같습니다.

* 가상 허브에서 Azure VPN에 연결 하는 단일 분기 연결 비용은 $0.05/hr입니다. 따라서 100 Azure 가상 허브에 연결 하는 분기 연결의 비용은 $0.05 * 100/시간입니다.

* 가상 허브에 연결 하는 두 개의 Express 경로 회로 연결의 비용은 $0.05 * 2/시간입니다.

* Azure virtual hubs P2S gateway에 연결 하는 세 가지 원격 사용자 연결은 $0.03 * 3/hr 비용을 부과 합니다.

### <a name="how-are-data-transfer-charges-calculated"></a><a name="data-transfer"></a>데이터 전송 요금은 어떻게 계산 되나요?

* Azure를 시작 하는 트래픽은 요금이 청구 되지 않습니다. Azure에서 나가는 트래픽 (VPN, Express 경로 또는 지점 및 사이트 간 사용자 VPN 연결을 통해)은 표준 [Azure 데이터 전송 요금이](https://azure.microsoft.com/pricing/details/bandwidth/)적용 됩니다.

* 가상 WAN 허브와 원본 허브와 다른 지역에 있는 원격 가상 WAN 허브 또는 VNet 간의 데이터 전송 요금에 대해서는 허브에서 나가는 트래픽에 대해 데이터 전송 요금이 적용 됩니다. 예: 미국 동부 허브로 나가는 트래픽은 $0.02/GB가 미국 서 부 허브로 이동 하는 것으로 청구 됩니다. 미국 서 부 허브로 들어가는 트래픽에 대해서는 요금이 부과 되지 않습니다. 다음 표에서는 요금을 보여 줍니다.

아래 표에서는 {베트남: 북아메리카}, {EU: 유럽}, {MEA: 중동 아프리카}, {OC: 오세아니아 (오스트레일리아 중부 및 오스트레일리아 중부 2)}, {LATAM: 라틴 아메리카} 약어를 사용 합니다. 

**대륙 내 가격 (*)**

| 대륙 내| 가격 ($/GB)|
|---|---|
| 베트남에서 베트남로|$0.02 |
| EU에서 EU로 |$0.02 |
| 아시아-아시아 (중국 제외)|$0.10 |
| MEA MEA|$0.16 |
| LATAM-LATAM |$0.16 |
| OC-OC|$0.12 |

**본토 가격 책정 (*)**

| 상호 본토| 가격 ($/GB)|
|---|---|
| 베트남에서 EU 또는 EU에서 베트남으로 |$0.07 |
| LATAM에서 모든 위치로 |$0.17 |
| MEA에서 모든 위치로 |$0.17 |
| 오세아니아에서 모든 위치로 |$0.12 |
| 아시아 (중국 제외)에서 모든 위치로 |$0.12 |

(*) 일부 요금은 2020 년 8 월 1 일부 터 적용 될 수 있습니다.

### <a name="what-is-the-difference-between-a-standard-hub-fee-and-a-standard-hub-processing-fee"></a><a name="fee"></a>표준 허브 요금 및 표준 허브 처리 요금 간의 차이점은 무엇 인가요?

가상 WAN은 다음과 같은 두 가지 특성으로 제공 됩니다.

* 사용자가 여러 허브를 배포 하 고 VPN 사이트 간 연결을 즐길 수 있는 **기본 가상 WAN** 기본 가상 WAN에는 완전히 연결 된 허브, Express 경로 연결, 사용자 VPN/지점 및 사이트 간 VPN 연결, VNet 간 전이적 연결, VPN 및 Express 경로 전송 연결, Azure 방화벽 등의 고급 기능이 없습니다. 기본 가상 WAN에서 허브에 대 한 기본 요금 또는 데이터 처리 요금은 없습니다.

* **표준 가상 WAN** 은 완전히 연결 된 허브, express 경로 연결, 사용자 Vpn/지점 및 사이트 간 vpn 연결, vnet 간 전이적 연결, VPN 및 express 경로 전송 연결, Azure 방화벽 등의 고급 기능을 제공 합니다. 모든 가상 허브 라우팅은 가상 허브에서 여러 서비스를 사용 하도록 설정 하는 라우터에 의해 제공 됩니다. 허브에 대 한 기본 요금은 $0.25/hr으로 가격이 책정 됩니다. 또한 VNet 간 전송 연결에 대 한 가상 허브 라우터의 데이터 처리 요금이 청구 됩니다. 다음 그림을 참조하세요.

 아래 **예제** 그림에는 vnet 1과 vnet 2의 두 가지 vnet 있습니다. Vnet 1의 VM에서 VNET 2의 다른 VM으로 1Gbps 데이터를 전송 하는 경우를 가정해 보겠습니다. 다음 요금이 적용 됩니다.

* 가상 허브 기본 요금 $0.25/시간

* 1Gbps의 $0.02/GB에 대 한 가상 허브 데이터 처리 요금

**예제**

   :::image type="content" source="./media/pricing-concepts/figure-1.png" alt-text="예제":::

## <a name="next-steps"></a>다음 단계

* Virtual WAN에 대한 자세한 내용은 [FAQ](virtual-wan-faq.md)를 참조하세요.

* 최신 가격은 [가상 WAN 가격](https://azure.microsoft.com/pricing/details/virtual-wan/)을 참조 하세요.
