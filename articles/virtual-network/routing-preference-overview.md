---
title: Azure의 라우팅 기본 설정
description: 라우팅 기본 설정을 사용하여 Azure와 인터넷 간의 트래픽 라우팅 방법을 선택하는 방법에 대해 알아봅니다.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
Customer intent: As an Azure customer, I want to learn more about routing choices for my internet egress traffic.
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/18/2020
ms.author: mnayak
ms.openlocfilehash: 171ae04a70f3639981e74d20d260a03359a0f2c8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86187441"
---
# <a name="what-is-routing-preference-preview"></a>라우팅 기본 설정이란(미리 보기)?

Azure 라우팅 기본 설정을 사용하면 Azure와 인터넷 간의 트래픽 라우팅 방법을 선택할 수 있습니다. Microsoft 네트워크를 통해 또는 ISP 네트워크(공용 인터넷)를 통해 트래픽을 라우팅하도록 선택할 수 있습니다. 이러한 옵션을 각각 *콜드 포테이토 라우팅* 및 *핫 포테이토 라우팅*이라고도 합니다. 송신 데이터 전송 가격은 라우팅 선택에 따라 달라집니다. 공용 IP 주소를 만드는 동안 라우팅 옵션을 선택할 수 있습니다. 공용 IP 주소는 가상 머신, 가상 머신 확장 집합, 인터넷 연결 부하 분산 장치 등의 리소스와 연결될 수 있습니다. 또한 Blob, 파일, 웹, Azure DataLake와 같은 Azure 스토리지 리소스에 대한 라우팅 기본 설정도 설정할 수 있습니다. 기본적으로 트래픽은 모든 Azure 서비스에 대해 Microsoft 글로벌 네트워크를 통해 라우팅됩니다.

> [!IMPORTANT]
> 라우팅 기본 설정은 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="routing-via-microsoft-global-network"></a>Microsoft 글로벌 네트워크를 통한 라우팅

*Microsoft 글로벌 네트워크*를 통해 트래픽을 라우팅하면 165 에지 POP(Point of Presence) 이상으로 160,000마일이 넘는 파이버 범위까지 확장된 지구상에서 가장 큰 네트워크 중 하나를 통해 트래픽이 전달됩니다. 이 네트워크는 매우 높은 안정성과 가용성을 보장하기 위해 여러 중복 파이버 경로를 사용하여 적절히 프로비저닝되어 있습니다. 트래픽 엔지니어링은 트래픽에 대해 짧은 대기 시간 경로를 선택하고 프리미엄 네트워크 성능을 제공하는 소프트웨어 정의 WAN 컨트롤러를 통해 관리됩니다.

![Microsoft 글로벌 네트워크를 통한 라우팅](media/routing-preference-overview/route-via-microsoft-global-network.png)

**수신 트래픽:** 글로벌 BGP 애니캐스트 알림은 수신 트래픽이 사용자에게 가장 가까운 Microsoft 네트워크에 진입되도록 합니다. 예를 들어 싱가포르의 사용자가 미국 시카고에서 호스트되는 Azure 리소스에 액세스하는 경우, 싱가포르 Edge POP의 Microsoft 글로벌 네트워크로 트래픽은 들어간 다음, Microsoft 네트워크에서 시카고에 호스트되는 서비스로 이동합니다.

**송신 트래픽:** 송신 트래픽도 동일한 원칙을 따릅니다. 트래픽은 Microsoft 글로벌 네트워크에서 대부분 이동하고 사용자에게 가장 가까운 곳에서 끝납니다. 예를 들어 Azure 시카고의 트래픽의 목적지가 싱가포르의 사용자인 경우, 트래픽은 시카고의 Microsoft 네트워크에서 싱가포르로 이동하고 싱가포르 Edge POP의 Microsoft 네트워크에서 종료됩니다.

수신 및 송신 트래픽은 모두 Microsoft 글로벌 네트워크에서 대부분 이동합니다. 이를 *콜드 포테이토 라우팅*라고도 합니다.


## <a name="routing-over-public-internet-isp-network"></a>공용 인터넷(ISP 네트워크)을 통한 라우팅

새 라우팅 옵션으로 *인터넷 라우팅*을 선택하면 Microsoft 글로벌 네트워크에서의 이동을 최소화하고 전송 ISP 네트워크를 사용하여 트래픽을 라우팅합니다. 이 비용 최적화된 라우팅 옵션은 다른 클라우드 공급자와 비슷한 네트워크 성능을 제공합니다.

![공용 인터넷을 통한 라우팅](media/routing-preference-overview/route-via-isp-network.png)

**수신 트래픽:** 수신 경로는 트래픽이 호스트된 서비스 지역에 가장 가까운 Microsoft 네트워크로 진입하는 *핫 포테이토 라우팅*을 사용합니다. 예를 들어 싱가포르의 사용자가 시카고에서 호스트되는 Azure 리소스에 액세스하는 경우, 트래픽은 공용 인터넷을 통해 이동하고 시카고의 Microsoft 글로벌 네트워크에 진입합니다.

**송신 트래픽:** 송신 트래픽도 동일한 원칙을 따릅니다. 트래픽은 서비스가 호스트되는 동일한 지역에 있는 Microsoft 네트워크에서 종료됩니다. 예를 들어 Azure 시카고의 서비스 트래픽의 목적지가 싱가포르의 사용자인 경우, 트래픽은 시카고의 Microsoft 네트워크를 나가서 싱가포르의 공용 인터넷을 통해 이동합니다.

## <a name="supported-services"></a>지원되는 서비스

라우팅 기본 설정을 "Microsoft 글로벌 네트워크"로 선택한 공용 IP는 모든 Azure 서비스와 연결할 수 있습니다. 그러나 라우팅 기본 설정을 **인터넷**으로 선택한 공용 IP는 다음 Azure 리소스와 연결할 수 있습니다.

* 가상 머신
* 가상 머신 크기 집합
* AKS(Azure Kubernetes Service)
* 인터넷 연결 부하 분산 장치
* Application Gateway
* Azure Firewall

스토리지의 경우 기본 엔드포인트는 항상 **Microsoft 글로벌 네트워크**를 사용합니다. 트래픽 라우팅에 대한 선택으로 **인터넷**을 사용하여 보조 엔드포인트를 사용하도록 설정할 수 있습니다. 지원되는 스토리지 서비스는 다음과 같습니다.

* Blob
* 파일
* 웹
* Azure DataLake

## <a name="pricing"></a>가격 책정
두 옵션 간의 가격 차이는 인터넷 송신 데이터 전송 가격 책정에 반영됩니다. **Microsoft 글로벌 네트워크**를 통한 라우팅의 데이터 전송 가격은 현재 인터넷 송신 가격과 같습니다. 최신 가격 책정 정보는 [Azure 대역폭 가격 책정 페이지](https://azure.microsoft.com/pricing/details/bandwidth/)를 참조하세요. **공용 인터넷**를 통한 라우팅은 아래 표에 표시된 대로 가격이 낮아집니다.

| 송신 원본 지역 | 0-5GB/월 | 5GB-10TB/월 | 10-50TB/월 | 50-150TB/월 | 150-500TB/월 |
| --- | --- | --- | --- | --- | --- |
| 영역 1 | $0/GB | $0.085/GB | $0.065/GB | $0.06/GB | $0.04/GB |
| 영역 2 | $0/GB | $0.11/GB | $0.075/GB | $0.07/GB | $0.06/GB  |

월간 500TB를 초과하는 볼륨에 대해서는 [저희에게 문의](https://azure.microsoft.com/overview/sales-number/)하세요.
* 영역 1 - 오스트레일리아 중부, 오스트레일리아 중부 2, 캐나다 중부, 캐나다 동부, 북유럽, 서유럽, 프랑스 중부, 프랑스 남부, 독일 북부(공용), 독일 중서부(공용), 노르웨이 동부, 노르웨이 서부, 스위스 북부, 스위스 서부, 영국 남부, 영국 서부, 미국 중부, 미국 동부, 미국 동부 2, 미국 중북부, 미국 중남부, 미국 서부, 미국 서부 2, 미국 중서부

* 영역 2 - 동아시아, 동남 아시아, 오스트레일리아 동부, 오스트레일리아 남동부, 인도 중부, 인도 남부, 인도 서부, 일본 동부, 일본 서부, 한국 중부, 한국 남부

* 영역 3 - 브라질 남부, 남아프리카 공화국 북부, 남아프리카 공화국 서부, 아랍에미리트 중부, 아랍에미리트 북부

## <a name="availability"></a>가용성

라우팅 기본 설정 지원은 인터넷 송신에 공용 IP를 사용하는 가상 머신 및 인터넷 연결 부하 분산 장치 등의 서비스에 대해 북유럽, 서유럽, 프랑스 남부, 영국 남부, 미국 동부, 미국 중북부, 미국 중남부, 미국 서부, 미국 중서부, 동남 아시아, 독일 중서부, 스위스 서부, 일본 동부 및 일본 서부 지역에서 사용할 수 있습니다.

저장소 계정에 대 한 라우팅 기본 설정 지원은 Azure 지역-미국 중 북부, 미국 서 부, 미국 중 북부, 미국 동부, 미국 서 부, 유럽 서 부, 프랑스 남부, 독일 중서부, 스위스 서부, 남부 동아시아, 일본 동부 및 일본 서 부에서 사용할 수 있습니다.
## <a name="limitations"></a>제한 사항

* 라우팅 기본 설정은 공용 IP 주소의 표준 SKU와만 호환됩니다. 공용 IP 주소의 기본 SKU는 지원되지 않습니다.
* 현재 라우팅 기본 설정은 IPv4 공용 IP 주소만 지원합니다. IPv6 공용 IP 주소는 지원되지 않습니다.
* 여러 NIC를 사용하는 가상 머신은 라우팅 기본 설정 유형을 하나만 가질 수 있습니다.


## <a name="next-steps"></a>다음 단계

* [Azure PowerShell을 사용하여 VM에 대한 라우팅 기본 설정 구성](configure-routing-preference-virtual-machine-powershell.md)
* [Azure CLI를 사용하여 VM에 대한 라우팅 기본 설정 구성](configure-routing-preference-virtual-machine-cli.md)
