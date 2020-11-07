---
title: VNet 피어 링 및 Azure 방호 아키텍처
description: 이 문서에서는 VNet 피어 링 및 Azure 방호를 함께 사용 하 여 Vm에 연결 하는 방법을 알아봅니다.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 11/05/2020
ms.author: cherylmc
ms.openlocfilehash: ad3cf33dacffc8bcda9376857206784afedf7139
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2020
ms.locfileid: "94361990"
---
# <a name="vnet-peering-and-azure-bastion-preview"></a>VNet 피어 링 및 Azure 방호 (미리 보기)

Azure 방호 및 VNet 피어 링은 함께 사용할 수 있습니다. VNet 피어 링이 구성 되 면 각 피어 링 VNet에 Azure 방호를 배포할 필요가 없습니다. 즉, 하나의 VNet (가상 네트워크)에서 구성 된 Azure 방호 호스트가 있는 경우 추가 요새 호스트를 배포 하지 않고 피어 링 VNet에 배포 된 Vm에 연결 하는 데 사용할 수 있습니다. VNet 피어 링에 대 한 자세한 내용은 [가상 네트워크 피어 링](../virtual-network/virtual-network-peering-overview.md)정보를 참조 하세요.

Azure 방호는 다음과 같은 피어 링 유형을 사용 합니다.

* **가상 네트워크 피어 링:** 동일한 Azure 지역 내에서 가상 네트워크를 연결 합니다.
* **글로벌 가상 네트워크 피어 링:** Azure 지역에서 가상 네트워크 연결

## <a name="architecture"></a>Architecture

VNet 피어 링이 구성 되 면 Azure 방호를 허브 및 스포크 또는 전체 메시 토폴로지에 배포할 수 있습니다. Azure Bastion 배포는 구독/계정 또는 가상 머신이 아닌 가상 네트워크별로 수행됩니다.

가상 네트워크에서 Azure 방호 서비스를 프로 비전 한 후에는 피어 링 Vnet 뿐만 아니라 동일한 VNet의 모든 Vm에서 RDP/SSH 환경을 사용할 수 있습니다. 즉, 요새 배포를 단일 VNet에 통합할 수 있으며, 전체 배포를 중앙 집중화 하 여 피어 링 VNet에 배포 된 Vm에 계속 연결할 수 있습니다.

:::image type="content" source="./media/vnet-peering/design.png" alt-text="디자인 및 아키텍처 다이어그램":::

이 그림은 허브 및 스포크 모델에서 Azure 요새 배포의 아키텍처를 보여 줍니다. 이 다이어그램에서는 다음과 같은 구성을 볼 수 있습니다.

* 요새 호스트는 중앙 집중식 허브 가상 네트워크에 배포 됩니다.
* 중앙 NSG (네트워크 보안 그룹)가 배포 됩니다.
* Azure VM에는 공용 IP가 필요 하지 않습니다.

**위한**

1. HTML5 브라우저를 사용 하 여 Azure Portal에 연결 합니다.
1. 연결할 가상 컴퓨터를 선택 합니다.
1. Azure 방호은 피어 링 VNet에서 원활 하 게 검색 됩니다.
1. 한 번의 클릭으로 RDP/SSH 세션이 브라우저에서 열립니다. RDP 및 SSH 동시 세션 제한의 경우 [rdp 및 ssh 세션](bastion-faq.md#limits)을 참조 하세요.

   :::image type="content" source="../../includes/media/bastion-vm-rdp/connect-vm.png" alt-text="연결":::

   Azure 방호를 통해 VM에 연결 하는 방법에 대 한 자세한 내용은 다음을 참조 하세요.

   * [VM RDP에 연결](bastion-connect-vm-rdp.md)합니다.
   * [VM에 연결-SSH](bastion-connect-vm-ssh.md)

## <a name="faq"></a>FAQ

[!INCLUDE [FAQ for VNet peering](../../includes/bastion-faq-peering-include.md)]

## <a name="next-steps"></a>다음 단계

[Bastion FAQ](bastion-faq.md)를 참조하세요.