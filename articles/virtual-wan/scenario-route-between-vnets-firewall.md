---
title: '시나리오: 가상 WAN에 대 한 Azure 방화벽 사용자 지정 라우팅'
titleSuffix: Azure Virtual WAN
description: Vnet 간 라우팅을 직접 라우팅하는 시나리오 이지만 VNet->인터넷/분기에 대 한 Azure 방화벽 및 VNet 트래픽 흐름에 대 한 분기를 사용 합니다.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: cdaa594a87d960688638591e6edd525aa3b048f4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85568324"
---
# <a name="scenario-azure-firewall---custom"></a>시나리오: Azure 방화벽-사용자 지정

가상 WAN 가상 허브 라우팅으로 작업할 때 몇 가지 시나리오를 사용할 수 있습니다. 이 시나리오에서는 Vnet 간에 트래픽을 직접 라우팅하는 것이 목표 이지만 VNet 간/분기 및 분기 간 트래픽 흐름에는 Azure 방화벽을 사용 합니다. 가상 허브 라우팅에 대 한 자세한 내용은 [가상 허브 라우팅 정보](about-virtual-hub-routing.md)를 참조 하세요.

## <a name="scenario-workflow"></a><a name="workflow"></a>시나리오 워크플로

이 시나리오에서는 VNet 간, VNet 간 또는 분기 간 트래픽에 대해 Azure 방화벽을 통해 트래픽을 라우트 하지만 vnet 간 트래픽에 대해 직접 이동 하려는 경우를 예로 들어 보겠습니다. Azure 방화벽 관리자를 사용 하는 경우 경로 설정이 **기본 경로 테이블**에 자동으로 채워집니다. 개인 트래픽은 VNet 및 분기에 적용 되 고 인터넷 트래픽은 0.0.0.0/0에 적용 됩니다.

VPN, Express 경로 및 사용자 VPN 연결을 통칭 하 여 분기 라고 하며 동일한 (기본) 경로 테이블에 연결 합니다. 모든 VPN, Express 경로 및 사용자 VPN 연결은 경로를 동일한 경로 테이블 집합으로 전파 합니다. 이 시나리오를 구성 하려면 다음 단계를 고려해 야 합니다.

1. **RT_VNET**사용자 지정 경로 테이블을 만듭니다.
1. Azure 방화벽을 가리키는 다음 홉을 사용 하 여 VNet 간 및 VNet 간: 0.0.0.0/0을 활성화 하는 경로를 만듭니다. 전파 섹션에서 더 구체적인 경로를 확인 하는 Vnet가 선택 되었는지 확인 하 여 VNet 간 직접 트래픽 흐름을 허용 합니다.

   * **연결:** vnet을 선택 하 여 vnet가이 경로 테이블의 경로에 따라 대상에 도달 함을 의미 합니다.
   * **전파 중:** vnet를 선택 합니다 .이 경로는 vnet가이 경로 테이블에 전파 됨을 의미 합니다. 즉, 더 구체적인 경로는이 경로 테이블에 전파 되므로 VNet 간에 직접 트래픽 흐름이 보장 됩니다.

1. Vnet에 대 한 집계 고정 경로를 **기본 경로 테이블** 에 추가 하 여 Azure 방화벽을 통해 분기 간 흐름을 활성화 합니다. 

   * 분기를 연결 하 고 기본 경로 테이블에 전파 하는 것을 명심 하십시오.
   * 분기는 RT_VNET 경로 테이블에 전파 되지 않습니다. 이렇게 하면 Azure 방화벽을 통해 VNet 간 트래픽 흐름이 보장 됩니다.

이로 인해 **그림 1**에 표시 된 대로 라우팅 구성이 변경 됩니다.

**그림 1**

:::image type="content" source="./media/routing-scenarios/between-vnets-firewall/routing.png" alt-text="그림 1":::

## <a name="next-steps"></a>다음 단계

* Virtual WAN에 대한 자세한 내용은 [FAQ](virtual-wan-faq.md)를 참조하세요.
* 가상 허브 라우팅에 대 한 자세한 내용은 [가상 허브 라우팅 정보](about-virtual-hub-routing.md)를 참조 하세요.
* 가상 허브 라우팅을 구성 하는 방법에 대 한 자세한 내용은 [가상 허브 라우팅을 구성 하는 방법](how-to-virtual-hub-routing.md)을 참조 하세요.
