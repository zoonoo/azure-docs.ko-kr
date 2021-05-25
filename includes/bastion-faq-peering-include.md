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
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94356655"
---
### <a name="can-i-still-deploy-multiple-bastion-hosts-across-peered-virtual-networks"></a>피어링된 가상 네트워크 간에 여러 Bastion 호스트를 배포할 수 있나요?

예. 기본적으로 사용자는 VM이 상주하는 동일한 가상 네트워크에 배포된 Bastion 호스트를 볼 수 있습니다. 그러나 **연결** 메뉴에서 사용자는 피어링된 네트워크에서 검색된 여러 Bastion 호스트를 볼 수 있습니다. 가상 네트워크에 배포된 VM에 연결하는 데 사용하고자 하는 Bastion 호스트를 선택할 수 있습니다.

### <a name="if-my-peered-vnets-are-deployed-in-different-subscriptions-will-connectivity-via-bastion-work"></a>피어링된 VNet이 다른 구독에 배포되는 경우 Bastion을 통한 연결이 작동하나요?

예, Bastion을 통한 연결은 단일 테넌트에 대한 다른 구독에서 피어링된 VNet에 대해 계속 작동합니다. 서로 다른 두 테넌트 간의 구독은 지원되지 않습니다. **연결** 드롭다운 메뉴에서 Bastion을 보려면 사용자는 **구독 > 전역 구독** 에서 액세스할 수 있는 하위 항목을 선택해야 합니다.

:::image type="content" source="./media/bastion-faq-peering-include/global-subscriptions.png" alt-text="전역 구독 필터" lightbox="./media/bastion-faq-peering-include/global-subscriptions.png":::

### <a name="i-have-access-to-the-peered-vnet-but-i-cant-see-the-vm-deployed-there"></a>피어링된 VNet에 액세스할 수 있지만 여기에 배포된 VM은 볼 수 없습니다.

사용자에게 VM 및 피어링된 VNet 모두에 대한 **읽기** 액세스 권한이 있는지 확인합니다. 또한 사용자에게 다음 리소스에 대한 **읽기** 액세스 권한이 있는지 IAM에서 확인합니다.

* 가상 머신에 대한 읽기 권한자 역할
* 가상 머신의 개인 IP를 사용하는 NIC에 대한 읽기 권한자 역할
* Azure Bastion 리소스에 대한 읽기 권한자 역할
* Virtual Network에 대한 리더 역할(피어링된 Virtual Network가 없는 경우 필요하지 않음).

|사용 권한|설명|사용 권한 유형|
|---|---| ---|
|Microsoft.Network/bastionHosts/read |요새 호스트 가져오기|작업|
|Microsoft.Network/virtualNetworks/BastionHosts/action |Virtual Network의 요새 호스트 참조를 가져옵니다.|작업|
|Microsoft.Network/virtualNetworks/bastionHosts/default/action|Virtual Network의 요새 호스트 참조를 가져옵니다.|작업|
|Microsoft.Network/networkInterfaces/read|네트워크 인터페이스 정의를 가져옵니다.|작업|
|Microsoft.Network/networkInterfaces/ipconfigurations/read|네트워크 인터페이스 IP 구성 정의를 가져옵니다.|작업|
|Microsoft.Network/virtualNetworks/read|가상 네트워크 정의를 가져옵니다.|작업|
|Microsoft.Network/virtualNetworks/subnets/virtualMachines/read|가상 네트워크 서브넷의 모든 가상 머신에 대한 참조를 가져옵니다.|작업|
|Microsoft.Network/virtualNetworks/virtualMachines/read|가상 네트워크의 모든 가상 머신에 대한 참조를 가져옵니다.|작업|