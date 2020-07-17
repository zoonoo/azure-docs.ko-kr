---
title: '시나리오: Azure 방화벽-Interhub 라우팅'
titleSuffix: Azure Virtual WAN
description: 라우팅 시나리오-Azure 방화벽이 있는 여러 허브 간의 라우팅
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: 1057dc28682c90374b2f8e8e0297f3d769c08bd6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85568053"
---
# <a name="scenario-azure-firewall---interhub-preview"></a>시나리오: Azure 방화벽-Interhub (미리 보기)

가상 WAN 가상 허브 라우팅으로 작업할 때 몇 가지 시나리오를 사용할 수 있습니다. 이 시나리오에서 목표는 Azure 방화벽을 포함 하는 여러 허브 간에 라우팅하는 것입니다. 가상 허브 라우팅에 대 한 자세한 내용은 [가상 허브 라우팅 정보](about-virtual-hub-routing.md)를 참조 하세요.

## <a name="scenario-architecture"></a><a name="architecture"></a>시나리오 아키텍처

이 시나리오에서는 다음과 같은 구성이 있다고 가정 합니다.

* 각 허브에 Azure 방화벽을 사용 하는 허브가 여러 개 있습니다.
* 보안 가상 허브를 사용 하 고 있습니다.
* VNet (개인 트래픽), 인터넷 및 분기 트래픽에 대 한 적절 한 정책이 설정 되었습니다.
* V2I (VNet 간), V2B (VNet 간), B2V (Branch to Branch), 모든 허브의 Azure 방화벽을 통해 이동 합니다.

추가 고려 사항:

* Azure 방화벽을 사용 하는 허브 간 시나리오의 경우 스포크 Vnet 별도의 경로 테이블에 연결 되지 않는다고 가정 합니다. (예: **경로 테이블 A**에 연결 된 **Vnet 1** 및 **경로 테이블 B**에 연결 된 **vnet 2** ). 모든 Vnet는 Azure 방화벽에 대 한 경로가 상주 하는 동일한 경로 테이블에 연결 됩니다.
* Azure 방화벽을 통한 보안 유지는 현재 **분기 < > VNet** 및 **인터넷** 트래픽만 제한 됩니다. Azure 방화벽을 통한 분기 간 흐름은 현재 지원 되지 않습니다.

**그림 1**

:::image type="content" source="./media/routing-scenarios/between-hubs-firewall/architecture.png" alt-text="architecture":::

## <a name="scenario-workflow"></a><a name="workflow"></a>시나리오 워크플로

이 시나리오를 구성 하려면 다음 단계를 고려해 야 합니다.

### <a name="step-1"></a><a name="step-1"></a>1단계

Azure 방화벽 관리자를 통해 연결을 이미 보호 하 고 있는 경우 첫 번째 단계는 모든 분기 및 VNet 연결이 **None**을 전파 하는지 확인 하는 것입니다. 이는 트래픽이 Azure 방화벽을 통해 설정 되는지 확인 하는 데 필요 합니다.

1. 허브를 선택 하 고 **없음** 경로 테이블을 편집 합니다.
1. 모든 분기 및 모든 Vnet을 선택 하려면 **전파** 를 업데이트 합니다.

### <a name="step-2"></a><a name="step-2"></a>2 단계

허브 당 사용자 지정 경로 테이블을 설정 합니다.

1. **허브 1**의 경우 **RT_Hub1**경로 테이블을 만듭니다.

    * Azure 방화벽 관리자를 사용 하는 경우 허브의 기본 경로 테이블에 다음 홉 **AZFW1** 을 사용 하 여 0.0.0.0/0에 대 한 경로를 자동으로 만듭니다. 3 단계에서이 설정을 수정 합니다. **RT_Hub1**의 경우 다음 홉 **AZFW1**을 사용 하 여 명시적으로 0.0.0.0/0에 대 한 항목을 만듭니다. 이 설정은 AZFW1를 통해 V2V, B2V 및 V2I를 활성화 합니다.
    * 허브 **2** 분기 및 vnet는 허브 **1** 의 **AZFW2** (AZFW1를 통해가 아닌)를 통해 도달 하려면 허브 **2** 분기 (10.5.0.0/16->AZFW2) 및 vnet (10.2.0.0/16->AZFW2)에 대해 다른 2 개의 집계 경로를 추가 해야 합니다.

1. **허브 2**의 경우 **RT_Hub2**경로 테이블을 만듭니다.

    * Azure 방화벽 관리자를 사용 하는 경우 허브의 기본 경로 테이블에 다음 홉 **AZFW2** 을 사용 하 여 0.0.0.0/0에 대 한 경로를 자동으로 만듭니다. 3 단계에서이 설정을 수정 합니다. **RT_Hub2**의 경우 다음 홉 **AZFW2**을 사용 하 여 명시적으로 0.0.0.0/0에 대 한 항목을 만듭니다. 이 설정은 **AZFW2**를 통해 V2V, B2V 및 V2I를 활성화 합니다.
    * 허브 간 트래픽이 허브 2의 **AZFW2**보호 되도록 하기 때문에 이전 허브 1 단계에서 두 번째 글머리 기호와 비슷한 단계를 명시적으로 추가할 필요가 없습니다.

### <a name="step-3"></a><a name="step-3"></a>3 단계

각 허브에서 **기본 경로 테이블** 을 수정 하 여 Azure 방화벽을 통해 VNet (B2V)에 **분기** 에 대 한 정적 경로를 추가 하 고 B2I ( **branch to Internet** ) 흐름에 분기에 대 한 정적 경로를 추가 합니다. 분기에 분기는 현재 Azure 방화벽을 통해 지원 되지 않습니다.

1. **허브 1 기본 경로 테이블**:

    * AZFW2:10.5.0.0/16->AZFW2 **를 통해 허브 2 분기로 분기**합니다. 이 구성은 허브 2 방화벽에 대 한 경로를 설정 합니다.
    * AZFW2:10.2.0.0/16 >AZFW2 **를 통해 허브 2로 분기**합니다. 이 구성은 허브 2 방화벽에 대 한 경로를 설정 합니다.
    * 분기 **(로컬)/분기로 VNet (local)/** Branch to Internet: 0.0.0.0/0 >AZFW1입니다.

2. **허브 2 기본 경로 테이블**:

    * 분기 (로컬 및 원격)/분기에서 VNet (로컬 및 원격)/분기로 인터넷: 0.0.0.0/0 >AZFW2으로 분기 합니다.

이렇게 하면 아래 그림에 표시 된 것 처럼 라우팅 구성이 변경 됩니다.

**그림 2**

:::image type="content" source="./media/routing-scenarios/between-hubs-firewall/workflow.png" alt-text="워크플로":::

## <a name="next-steps"></a>다음 단계

* Virtual WAN에 대한 자세한 내용은 [FAQ](virtual-wan-faq.md)를 참조하세요.
* 가상 허브 라우팅에 대 한 자세한 내용은 [가상 허브 라우팅 정보](about-virtual-hub-routing.md)를 참조 하세요.
* 가상 허브 라우팅을 구성 하는 방법에 대 한 자세한 내용은 [가상 허브 라우팅을 구성 하는 방법](how-to-virtual-hub-routing.md)을 참조 하세요.
