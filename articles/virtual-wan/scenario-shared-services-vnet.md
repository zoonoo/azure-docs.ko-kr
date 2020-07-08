---
title: '시나리오: 공유 서비스로 라우팅 Vnet'
titleSuffix: Azure Virtual WAN
description: 라우팅 시나리오-모든 VNet 및 분기에서 액세스 하려는 작업을 사용 하 여 공유 서비스 VNet에 액세스 하기 위한 경로를 설정 합니다.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: 64bb4c85399f811c0ab7ff84b297b64734efc491
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85568294"
---
# <a name="scenario-route-to-shared-services-vnets"></a>시나리오: 공유 서비스로 라우팅 Vnet

가상 WAN 가상 허브 라우팅으로 작업할 때 몇 가지 시나리오를 사용할 수 있습니다. 이 시나리오에서는 모든 VNet 및 분기 (VPN/ER/P2S)에서 액세스 하려는 작업을 사용 하 여 **공유 서비스** VNet에 액세스할 수 있는 경로를 설정 하는 것이 목표입니다. 가상 허브 라우팅에 대 한 자세한 내용은 [가상 허브 라우팅 정보](about-virtual-hub-routing.md)를 참조 하세요.

## <a name="scenario-workflow"></a><a name="workflow"></a>시나리오 워크플로

이 시나리오를 구성 하려면 다음 단계를 고려해 야 합니다.

1. **공유 서비스** VNet을 식별 합니다.
2. 사용자 지정 경로 테이블을 만듭니다. 이 예에서는 **RT_SHARED**으로이 경로 테이블을 참조 합니다. 경로 테이블을 만드는 단계 [는 가상 허브 라우팅을 구성 하는 방법](how-to-virtual-hub-routing.md)을 참조 하세요. 다음 값을 지침으로 사용 합니다.

   * **연결**
     * **공유 서비스 VNet을 *제외한* vnet**의 경우 격리할 vnet를 선택 합니다. 이는 모든 Vnet (공유 서비스 VNet 제외)가 경로 테이블 RT_SHARED 경로를 기반으로 대상에 도달할 수 있음을 의미 합니다.

   * **전파**
      * **분기**의 경우 이미 선택 했을 수 있는 다른 경로 테이블 외에도 경로를이 경로 테이블에 전파 합니다. 이 단계 때문에 경로 테이블 RT_SHARED 모든 분기 연결 (VPN/ER/User VPN)에서 경로를 학습 합니다.
      * **Vnet**에 대해 **공유 서비스 VNet**을 선택 합니다. 이 단계로 인해 RT_SHARED 경로 테이블은 공유 서비스 VNet 연결에서 경로를 학습 합니다.

     이렇게 하면 아래 그림에 표시 된 것 처럼 라우팅 구성이 변경 됩니다.

   :::image type="content" source="./media/routing-scenarios/shared-service-vnet/shared-services.png" alt-text="공유 서비스 VNet":::

## <a name="next-steps"></a>다음 단계

* Virtual WAN에 대한 자세한 내용은 [FAQ](virtual-wan-faq.md)를 참조하세요.
* 가상 허브 라우팅에 대 한 자세한 내용은 [가상 허브 라우팅 정보](about-virtual-hub-routing.md)를 참조 하세요.
