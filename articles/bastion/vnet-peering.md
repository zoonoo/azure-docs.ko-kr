---
title: VNet 피어 링 및 Azure 방호 아키텍처
description: 이 문서에서는 VNet 피어 링 및 Azure 방호를 함께 사용 하 여 Vm에 연결 하는 방법을 알아봅니다.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 12/09/2020
ms.author: cherylmc
ms.openlocfilehash: f72a3739fac1e7d6afdafd2676ea6fcefe847b2a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101710586"
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
2. 대상 VM과 피어 링 VNet 모두에 대 한 **읽기** 권한이 있는지 확인 합니다. 또한 다음 리소스에 대 한 읽기 권한이 있는 IAM 아래를 확인 합니다.
   * 가상 머신에 대한 읽기 권한자 역할
   * 가상 머신의 개인 IP를 사용하는 NIC에 대한 읽기 권한자 역할
   * Azure Bastion 리소스에 대한 읽기 권한자 역할
   * Virtual Network에 대 한 판독기 역할 (피어 링 Virtual Network가 없는 경우에는 필요 하지 않음)
3. **연결** 드롭다운 메뉴에서 방호를 보려면 **구독 > 전역 구독** 에서 액세스할 수 있는 사용자를 선택 해야 합니다.
4. 연결할 가상 컴퓨터를 선택 합니다.
5. Azure 방호은 피어 링 VNet에서 원활 하 게 검색 됩니다.
6. 한 번의 클릭으로 RDP/SSH 세션이 브라우저에서 열립니다. RDP 및 SSH 동시 세션 제한의 경우 [rdp 및 ssh 세션](bastion-faq.md#limits)을 참조 하세요.

  :::image type="content" source="../../includes/media/bastion-vm-rdp/connect-vm.png" alt-text="연결":::

   Azure 방호를 통해 VM에 연결 하는 방법에 대 한 자세한 내용은 다음을 참조 하세요.

   * [VM RDP에 연결](bastion-connect-vm-rdp.md)합니다.
   * [VM에 연결-SSH](bastion-connect-vm-ssh.md)

## <a name="faq"></a>FAQ

[!INCLUDE [FAQ for VNet peering](../../includes/bastion-faq-peering-include.md)]

## <a name="next-steps"></a>다음 단계

[Bastion FAQ](bastion-faq.md)를 참조하세요.
