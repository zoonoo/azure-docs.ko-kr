---
title: '시나리오: 공유 서비스로 라우팅 Vnet'
titleSuffix: Azure Virtual WAN
description: 라우팅 시나리오-모든 VNet 및 분기에서 액세스 하려는 작업을 사용 하 여 공유 서비스 VNet에 액세스 하기 위한 경로를 설정 합니다.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 08/07/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: c72ff14246f3f96980fe1de2fdef16794bf23e4e
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89399993"
---
# <a name="scenario-route-to-shared-services-vnets"></a>시나리오: 공유 서비스로 라우팅 Vnet

가상 WAN 가상 허브 라우팅으로 작업할 때 몇 가지 시나리오를 사용할 수 있습니다. 이 시나리오에서는 모든 VNet 및 분기 (VPN/ER/P2S)에서 액세스 하려는 작업을 사용 하 여 **공유 서비스** VNet에 액세스할 수 있는 경로를 설정 하는 것이 목표입니다. 이러한 공유 워크 로드의 예로는 도메인 컨트롤러 또는 파일 공유와 같은 서비스 또는 [Azure 개인 끝점](../private-link/private-endpoint-overview.md)을 통해 내부적으로 노출 되는 azure 서비스를 Virtual Machines 수 있습니다.

가상 허브 라우팅에 대 한 자세한 내용은 [가상 허브 라우팅 정보](about-virtual-hub-routing.md)를 참조 하세요.

## <a name="design"></a><a name="design"></a>디자인

연결 매트릭스를 사용 하 여이 시나리오의 요구 사항을 요약할 수 있습니다. 행렬에서 각 셀은 가상 WAN 연결 (흐름의 "From" 쪽, 테이블의 행 머리글)이 특정 트래픽 흐름에 대 한 대상 접두사 (흐름의 "대상" 쪽, 테이블의 기울임꼴 열 머리글)를 학습 하는지 여부를 설명 합니다. "X"는 가상 WAN에서 연결이 제공 됨을 의미 합니다.

**연결 매트릭스**

| 시작             | 대상:   |*격리 된 Vnet*|*공유 VNet*|*분기*|
|---|---|---|---|---|
|**격리 된 Vnet**|&#8594;|                |        X        |       X      |
|**공유 Vnet**  |&#8594;|       X        |        X        |       X      |
|**분기**      |&#8594;|       X        |        X        |       X      |

[Isolated VNet 시나리오](scenario-isolate-vnets.md)와 마찬가지로이 연결 행렬에서는 두 개의 경로 테이블로 변환 하는 두 개의 서로 다른 행 패턴을 제공 합니다. 즉, 공유 서비스 vnet와 분기에 동일한 연결 요구 사항이 있습니다. 가상 WAN에는 이미 기본 경로 테이블이 있으므로이 예제에서 **RT_SHARED** 를 호출 하는 다른 사용자 지정 경로 테이블이 필요 합니다.

Vnet은 **RT_SHARED** 경로 테이블에 연결 됩니다. 분기 및 공유 서비스 Vnet에 대 한 연결이 필요 하기 때문에 공유 서비스 VNet 및 분기는 **RT_SHARED** 에 전파 해야 합니다. 그렇지 않으면 vnet는 분기 및 공유 VNet 접두사를 알 수 없습니다. 분기는 항상 기본 경로 테이블에 연결 되어 있고 공유 서비스 Vnet 연결 요구 사항이 동일 하기 때문에 shared service Vnet를 기본 경로 테이블에도 연결 합니다.

결과적으로 최종 설계는 다음과 같습니다.

* 격리 된 가상 네트워크:
  * 연결 된 경로 테이블: **RT_SHARED**
  * 경로 테이블에 전파: **기본값**
* 공유 서비스 가상 네트워크:
  * 연결 된 경로 테이블: **기본값**
  * 경로 테이블에 전파: **RT_SHARED** 및 **기본값**
* 분기
  * 연결 된 경로 테이블: **기본값**
  * 경로 테이블에 전파: **RT_SHARED** 및 **기본값**

> [!NOTE]
> 가상 WAN을 여러 지역에 배포 하는 경우 모든 허브에 **RT_SHARED** 경로 테이블을 만들어야 합니다. 각 공유 서비스 VNet 및 분기 연결의 경로는 전파 레이블을 사용 하 여 모든 가상 허브의 경로 테이블에 전파 해야 합니다.

가상 허브 라우팅에 대 한 자세한 내용은 [가상 허브 라우팅 정보](about-virtual-hub-routing.md)를 참조 하세요.

## <a name="workflow"></a><a name="workflow"></a>워크플로

시나리오를 구성 하려면 다음 단계를 고려 하십시오.

1. **공유 서비스** VNet을 식별 합니다.
2. 사용자 지정 경로 테이블을 만듭니다. 이 예에서는 경로 테이블을 **RT_SHARED**으로 참조 합니다. 경로 테이블을 만드는 단계 [는 가상 허브 라우팅을 구성 하는 방법](how-to-virtual-hub-routing.md)을 참조 하세요. 다음 값을 지침으로 사용 합니다.

   * **연결**
     * **공유 서비스 VNet을 *제외한* vnet**의 경우 격리할 vnet를 선택 합니다. 이는 모든 Vnet (공유 서비스 VNet 제외)가 경로 테이블 RT_SHARED 경로를 기반으로 대상에 도달할 수 있음을 의미 합니다.

   * **전파**
      * **분기**의 경우 이미 선택 했을 수 있는 다른 경로 테이블 외에도 경로를이 경로 테이블에 전파 합니다. 이 단계 때문에 RT_SHARED 경로 테이블은 모든 분기 연결 (VPN/ER/User VPN)에서 경로를 학습 합니다.
      * **Vnet**에 대해 **공유 서비스 VNet**을 선택 합니다. 이 단계 때문에 RT_SHARED 경로 테이블은 공유 서비스 VNet 연결에서 경로를 학습 합니다.

그러면 다음 그림에 표시 된 라우팅 구성이 생성 됩니다.

   :::image type="content" source="./media/routing-scenarios/shared-service-vnet/shared-services.png" alt-text="공유 서비스 VNet" lightbox="./media/routing-scenarios/shared-service-vnet/shared-services.png":::

## <a name="next-steps"></a>다음 단계

* Virtual WAN에 대한 자세한 내용은 [FAQ](virtual-wan-faq.md)를 참조하세요.
* 가상 허브 라우팅에 대 한 자세한 내용은 [가상 허브 라우팅 정보](about-virtual-hub-routing.md)를 참조 하세요.
