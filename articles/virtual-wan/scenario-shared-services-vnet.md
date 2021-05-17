---
title: '시나리오: 공유 서비스 VNet으로 경로 지정'
titleSuffix: Azure Virtual WAN
description: Virtual WAN 라우팅 시나리오에 대해 알아봅니다. 이 시나리오에서 모든 VNet 및 분기에서 액세스하는 워크로드를 통해 공유 서비스 VNet에 액세스하도록 경로를 설정합니다.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 04/27/2021
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: 4a6d0a662930deec29cdf6b09c349f33dbd17a0a
ms.sourcegitcommit: 62e800ec1306c45e2d8310c40da5873f7945c657
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108165788"
---
# <a name="scenario-route-to-shared-services-vnets"></a>시나리오: 공유 서비스 VNet으로 경로 지정

Virtual WAN 가상 허브 라우팅 작업 시 사용할 수 있는 시나리오가 상당히 많습니다. 이 시나리오에서는 목표는 모든 VNet 및 분기(VPN/ER/P2S)에서 액세스하려는 워크로드를 사용하여 **공유 서비스** VNet에 액세스하도록 경로를 설정하는 것입니다. 이러한 공유 워크로드의 예로는 도메인 컨트롤러 또는 파일 공유와 같은 서비스가 있는 Virtual Machines 또는 [Azure 프라이빗 엔드포인트](../private-link/private-endpoint-overview.md)를 통해 내부적으로 노출되는 Azure 서비스를 들 수 있습니다.

가상 허브 라우팅에 대한 자세한 내용은 [가상 허브 라우팅 정보](about-virtual-hub-routing.md)를 참조하세요.

## <a name="design"></a><a name="design"></a>디자인

연결 매트릭스를 사용하여 이 시나리오의 요구 사항을 요약할 수 있습니다.

**연결 매트릭스**

| 시작             | 아래와 같이 변경합니다.   |‘격리된 VNet’|‘공유 VNet’|*분기*|
|---|---|---|---|---|
|**격리된 VNet**| ->|        | 직접 | 직접 |
|**공유 VNet**  |->| 직접 | 직접 | 직접 |
|**분기**      |->| 직접 | 직접 | 직접 |

위의 표에 나와 있는 각 셀은 Virtual WAN 연결(흐름의 ‘시작’ 쪽, 즉 행 머리글)이 대상(흐름의 ‘대상’ 쪽, 즉 이탤릭체 열 머리글)과 통신하는지를 설명합니다. 이 시나리오에서는 방화벽이나 네트워크 가상 어플라이언스를 사용할 수 없으므로 Virtual WAN에서 직접 통신 흐름이 전달됩니다(따라서 표에 '직접'으로 표시됨).

[격리된 VNet 시나리오](scenario-isolate-vnets.md)와 마찬가지로 이 연결 매트릭스는 두 개의 경로 테이블(공유 서비스 VNet 및 분기의 연결 요구 사항이 동일함)로 변환되는 두 개의 서로 다른 행 패턴을 제공합니다. Virtual WAN에는 기본 경로 테이블이 이미 있으므로 이 예제의 **RT_SHARED** 를 호출하는 다른 사용자 지정 경로 테이블이 필요합니다.

VNet은 **RT_SHARED** 경로 테이블에 연결됩니다. 분기 및 공유 서비스 VNet에 대한 연결이 필요하므로 공유 서비스 VNet 및 분기는 **RT_SHARED** 로 전파해야 합니다. 그렇지 않으면 VNet은 분기 및 공유 VNet 접두사를 알 수 없습니다. 분기는 항상 기본 경로 테이블에 연결되어 있고 연결 요구 사항이 공유 서비스 VNet의 경우에도 동일하므로 공유 서비스 VNet을 기본 경로 테이블에도 연결합니다.

따라서 최종 디자인은 다음과 같습니다.

* 격리된 가상 네트워크:
  * 연결된 경로 테이블: **RT_SHARED**
  * 경로 테이블로 전파: **기본**
* 공유 서비스 가상 네트워크:
  * 연결된 경로 테이블: **기본**
  * 경로 테이블로 전파: **RT_SHARED** 및 **Default**
* 분기:
  * 연결된 경로 테이블: **기본**
  * 경로 테이블로 전파: **RT_SHARED** 및 **Default**

> [!NOTE]
> Virtual WAN을 여러 지역에 배포하는 경우에는 모든 허브에 **RT_SHARED** 경로 테이블을 만들어야 하고 각 공유 서비스 VNet 및 분기 연결의 경로는 전파 레이블을 사용하여 모든 가상 허브의 경로 테이블로 전파해야 합니다.

가상 허브 라우팅에 대한 자세한 내용은 [가상 허브 라우팅 정보](about-virtual-hub-routing.md)를 참조하세요.

## <a name="workflow"></a><a name="workflow"></a>워크플로

시나리오를 구성하려면 다음 단계를 고려하세요.

1. **공유 서비스** VNet을 식별합니다.
2. 사용자 지정 경로 테이블을 만듭니다. 이 예에서는 경로 테이블을 **RT_SHARED** 라고 합니다. 경로 테이블을 만드는 단계에 대해서는 [가상 허브 라우팅을 구성하는 방법](how-to-virtual-hub-routing.md)을 참조하세요. 다음 값을 지침으로 사용합니다.

   * **연결**
     * **공유 서비스 VNet *을 제외한* VNet** 의 경우 격리할 VNet을 선택합니다. 즉, 모든 VNet(공유 서비스 VNet 제외)이 RT_SHARED 경로 테이블의 경로를 기반으로 대상에 도달할 수 있습니다.

   * **전파**
      * **분기** 의 경우 이미 선택했을 수 있는 다른 경로 테이블 외에도 경로를 이 경로 테이블에 전파합니다. 이 단계 때문에 RT_SHARED 경로 테이블은 모든 분기 연결(VPN/ER/사용자 VPN)에서 경로를 학습합니다.
      * **Vnet** 에 대해 **공유 서비스 VNet** 을 선택합니다. 이 단계 때문에 RT_SHARED 경로 테이블은 공유 서비스 VNet 연결에서 경로를 학습합니다.

그러면 다음 그림에 표시된 경로 구성이 생성됩니다.

   :::image type="content" source="./media/routing-scenarios/shared-service-vnet/shared-services.png" alt-text="공유 서비스 VNet에 대한 다이어그램." lightbox="./media/routing-scenarios/shared-service-vnet/shared-services.png":::

## <a name="next-steps"></a>다음 단계

* ARM 템플릿을 사용하여 구성하려면 [빠른 시작: ARM 템플릿을 사용하여 공유 서비스 VNet으로 경로 지정](quickstart-route-shared-services-vnet-template.md)을 참조하세요.
* 가상 허브 라우팅에 대한 자세한 내용은 [가상 허브 라우팅 정보](about-virtual-hub-routing.md)를 참조하세요.
