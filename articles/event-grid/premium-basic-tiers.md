---
title: Azure Event Grid 프리미엄 및 기본 계층
description: 이 문서에서는 Azure Event Grid 프리미엄 및 기본 계층의 차이점과 각 계층을 사용하는 경우를 설명합니다.
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: overview
ms.date: 03/11/2020
ms.author: babanisa
ms.openlocfilehash: 7ae73337ccccc2b7d2402cfa2a54ae93552d2465
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2020
ms.locfileid: "79300337"
---
# <a name="azure-event-grid-premium-and-basic-tiers"></a>Azure Event Grid 프리미엄 및 기본 계층
Azure Event Grid에는 다음의 두 가지 계층이 있습니다. **프리미엄** 및 **기본** 기본 계층은 소비 또는 종량제 가격 책정을 사용합니다. 이벤트 구동 프로그래밍 모델을 위해 Event Grid를 사용하는 데 필요한 모든 기본 게시/구독 도구를 제공합니다. 프리미엄 계층은 엔터프라이즈를 대상으로 하는 보안 기능을 사용하여 이 한 단계를 추가로 수행합니다. 프리미엄 계층은 개발 중인 많은 기능을 포함하는 초기 **미리 보기**에 있습니다.

## <a name="overview"></a>개요
Event Grid의 모든 사용자 지정 토픽 및 도메인은 기본 계층 또는 프리미엄 계층에 속합니다. `2020-04-01-preview` API 버전부터 토픽 또는 도메인 만들기의 일부로 원하는 계층을 선택할 수 있습니다. 기본값은 기본 계층입니다. 이전 API 버전을 사용하여 만든 토픽 및 도메인은 기본 계층으로 기본 설정됩니다. 토픽 및 도메인에 대한 가격 책정 계층을 변경하려면 [토픽 및 도메인에 대한 계층을 업데이트하는 방법](update-tier.md)을 참조하세요.

## <a name="capabilities-and-features"></a>기능 및 특징

다음 표에서는 계층 간의 차이점을 설명합니다.

|       &nbsp;                                           | Basic           | Premium        |
| ------------------------------------------------------ | --------------- | -------------- |
| 수신에 대한 IP 방화벽 규칙                          | 미리 보기  | 미리 보기 |
| 송신에 대한 서비스 태그                                | 미리 보기  | 미리 보기 |
| 수신 시 프라이빗 엔드포인트 VNet 통합          | 사용할 수 없음   | 미리 보기 |

## <a name="availability"></a>가용성
초기 미리 보기 중에 프라이빗 엔드포인트 통합이 포함된 프리미엄 계층 토픽 및 도메인은 다음 지역에서 제공됩니다.

- 미국 동부
- 미국 서부 2
- 미국 중남부

## <a name="pricing-and-quotas"></a>가격 책정 및 할당량
기본 계층 사용에 대한 자세한 가격 정보는 [Event Grid 가격 책정](https://azure.microsoft.com/pricing/details/event-grid/)을 참조하세요. 프리미엄 계층 가격 책정은 아직 발표되지 않았으며 가격 책정을 사용할 수 있을 때까지 무료입니다.

토픽 및 도메인 수에 대한 기존 할당량 및 처리량은 프리미엄 계층 가격이 발표될 때까지 프리미엄 및 기본 계층 리소스에 모두 적용됩니다.

## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요.

- 기본 계층에서 프리미엄 계층으로 업그레이드하려면 [가격 책정 계층 업데이트](update-tier.md) 문서를 참조하세요. 
- Event Grid 리소스에 대한 IP 방화벽을 구성하여 IP 주소 또는 IP 주소 범위의 선택 세트에서만 공용 인터넷을 통해 액세스를 제한할 수 있습니다. 단계별 지침에 대해서는 [방화벽 구성](configure-firewall.md)을 참조하세요.
- 선택한 가상 네트워크에서만 액세스를 제한하도록 프라이빗 엔드포인트를 구성할 수 있습니다. 단계별 지침에 대해서는 [프라이빗 엔드포인트 구성](configure-private-endpoints.md)을 참조하세요.