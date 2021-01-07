---
title: 포함 파일
description: 포함 파일
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 11/05/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 57b0bb9ab8ceb34021a38db0d0abf2c9c919e808
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2020
ms.locfileid: "94356655"
---
### <a name="can-i-still-deploy-multiple-bastion-hosts-across-peered-virtual-networks"></a>피어 링 가상 네트워크 간에 여러 개의 요새 호스트를 배포할 수 있나요?

예. 기본적으로 사용자는 VM이 상주 하는 동일한 가상 네트워크에 배포 된 요새 호스트를 볼 수 있습니다. 그러나 **연결** 메뉴에서 사용자는 피어 링 네트워크에서 검색 된 여러 개의 방호 호스트를 볼 수 있습니다. 가상 네트워크에 배포 된 VM에 연결 하는 데 사용 하고자 하는 요새 호스트를 선택할 수 있습니다.

### <a name="if-my-peered-vnets-are-deployed-in-different-subscriptions-will-connectivity-via-bastion-work"></a>피어 링 Vnet가 다른 구독에 배포 되는 경우에는 요새를 통한 연결이 작동 하나요?

예, 다른 구독에서 단일 테 넌 트에 대해 피어 링 Vnet에 대 한 연결을 계속 해 서 사용할 수 있습니다. 서로 다른 두 테 넌 트 간의 구독은 지원 되지 않습니다. **연결** 드롭다운 메뉴에서 방호를 보려면 사용자는 **구독 > 전역 구독** 에서 액세스 권한이 있는 사용자를 선택 해야 합니다.

:::image type="content" source="./media/bastion-faq-peering-include/global-subscriptions.png" alt-text="전역 구독 필터" lightbox="./media/bastion-faq-peering-include/global-subscriptions.png":::

### <a name="i-have-access-to-the-peered-vnet-but-i-cant-see-the-vm-deployed-there"></a>피어 링 VNet에 액세스할 수 있지만 여기에 배포 된 VM은 볼 수 없습니다.

사용자에 게 VM 및 피어 링 VNet 모두에 대 한 **읽기** 권한이 있는지 확인 합니다. 또한 IAM 아래에서 사용자에 게 다음 리소스에 대 한 **읽기** 권한이 있는지 확인 합니다.

* 가상 머신에 대한 읽기 권한자 역할
* 가상 머신의 개인 IP를 사용하는 NIC에 대한 읽기 권한자 역할
* Azure Bastion 리소스에 대한 읽기 권한자 역할
* Virtual Network에 대 한 판독기 역할 (피어 링 Virtual Network가 없는 경우에는 필요 하지 않음)

|사용 권한|설명|사용 권한 유형|
|---|---| ---|
|Microsoft.Network/bastionHosts/read |요새 호스트 가져오기|작업|
|Microsoft.Network/virtualNetworks/BastionHosts/action |Virtual Network의 요새 호스트 참조를 가져옵니다.|작업|
|Microsoft. Network/virtualNetworks/bastionHosts/default/action|Virtual Network의 요새 호스트 참조를 가져옵니다.|작업|
|Microsoft.Network/networkInterfaces/read|네트워크 인터페이스 정의를 가져옵니다.|작업|
|Microsoft.Network/networkInterfaces/ipconfigurations/read|네트워크 인터페이스 IP 구성 정의를 가져옵니다.|작업|
|Microsoft.Network/virtualNetworks/read|가상 네트워크 정의를 가져옵니다.|작업|
|Microsoft.Network/virtualNetworks/subnets/virtualMachines/read|가상 네트워크 서브넷의 모든 가상 머신에 대한 참조를 가져옵니다.|작업|
|Microsoft.Network/virtualNetworks/virtualMachines/read|가상 네트워크의 모든 가상 머신에 대한 참조를 가져옵니다.|작업|