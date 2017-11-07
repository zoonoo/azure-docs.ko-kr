---
title: "Azure Site Recovery에서 두 Azure 지역 간 네트워크 매핑 | Microsoft Docs"
description: "Azure Site Recovery는 가상 컴퓨터 및 실제 서버의 복제, 장애 조치 및 복구를 조정합니다. Azure로 또는 보조 데이터 센터로 장애 조치에 대해 알아봅니다."
services: site-recovery
documentationcenter: 
author: prateek9us
manager: gauravd
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 08/11/2017
ms.author: pratshar
ms.openlocfilehash: 9d6a806ec533259797080fbfee2c38f918ebd8a2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="network-mapping-between-two-azure-regions"></a>두 Azure 지역 간 네트워크 매핑


이 문서에는 두 Azure 지역의 Azure 가상 네트워크를 서로 매핑하는 방법이 설명되어 있습니다. 네트워크를 매핑하면 복제된 가상 컴퓨터가 대상 Azure 지역에 만들어지는 경우 원본 가상 컴퓨터의 가상 네트워크에 매핑되는 가상 네트워크에 만들어지도록 합니다.  

## <a name="prerequisites"></a>필수 조건
네트워크를 매핑하기 전에 원본 및 대상 Azure 지역 모두에 [Azure 가상 네트워크](../virtual-network/virtual-networks-overview.md)를 만들었는지 확인합니다.

## <a name="map-networks"></a>네트워크 매핑

하나의 Azure 지역에서 다른 지역의 다른 가상 네트워크에 Azure 가상 네트워크를 매핑하려면 Site Recovery 인프라 -> 네트워크 매핑(Azure 가상 컴퓨터의 경우)으로 이동하여 네트워크 매핑을 만듭니다.

![네트워크 매핑](./media/site-recovery-network-mapping-azure-to-azure/network-mapping1.png)


아래 예제에서는 내 가상 컴퓨터가 동아시아 지역에서 실행되고 있고 동남 아시아로 복제됩니다.

원본 및 대상 네트워크를 선택하고 [확인]을 클릭하여 동아시아에서 동남 아시아로 네트워크 매핑을 만듭니다.

![네트워크 매핑](./media/site-recovery-network-mapping-azure-to-azure/network-mapping2.png)


동일한 작업을 수행하여 동남 아시아에서 동아시아로 네트워크 매핑을 만듭니다.  
![네트워크 매핑](./media/site-recovery-network-mapping-azure-to-azure/network-mapping3.png)


## <a name="mapping-network-when-enabling-replication"></a>복제를 사용하도록 설정할 때 네트워크 매핑

하나의 Azure 지역에서 다른 지역으로 처음 가상 컴퓨터를 복제할 때 네트워크 매핑이 수행되지 않으면 동일한 프로세스의 일부로 대상 네트워크를 선택할 수 있습니다. Site Recovery는 선택 내용에 따라 원본 지역에서 대상 지역으로 그리고 대상 지역에서 원본 지역으로 네트워크 매핑을 만듭니다.   

![네트워크 매핑](./media/site-recovery-network-mapping-azure-to-azure/network-mapping4.png)

기본적으로 Site Recovery는 원본 네트워크와 동일한 대상 지역에, 원본 네트워크 이름에 접미사로 '-asr'을 추가하여 네트워크를 만듭니다. [사용자 지정]을 클릭하여 이미 만들어진 네트워크를 선택할 수 있습니다.

![네트워크 매핑](./media/site-recovery-network-mapping-azure-to-azure/network-mapping5.png)


네트워크 매핑이 이미 수행된 경우 복제를 사용하도록 설정하는 동안 대상 가상 네트워크를 변경할 수 없습니다. 변경하려면 기존 네트워크 매핑을 수정합니다.  

![네트워크 매핑](./media/site-recovery-network-mapping-azure-to-azure/network-mapping6.png)

![네트워크 매핑](./media/site-recovery-network-mapping-azure-to-azure/modify-network-mapping.png)

> [!IMPORTANT]
> 지역-1에서 지역-2로 네트워크 매핑을 수정하는 경우 지역-2에서 지역-1로도 네트워크 매핑을 수정해야 합니다.
>
>


## <a name="subnet-selection"></a>서브넷 선택
원본 가상 컴퓨터의 서브넷의 이름에 따라 대상 가상 컴퓨터의 서브넷이 선택됩니다. 대상 네트워크에서 사용할 수 있는 원본 가상 컴퓨터의 이름과 동일한 이름의 서브넷이 있는 경우 대상 가상 컴퓨터에 대해 선택된 서브넷입니다. 대상 네트워크에 동일한 이름의 서브넷이 없는 경우 사전순으로 첫 번째 서브넷이 대상 서브넷으로 선택됩니다. 가상 컴퓨터의 계산 및 네트워크 설정으로 이동하여 이 서브넷을 수정할 수 있습니다.

![서브넷 수정](./media/site-recovery-network-mapping-azure-to-azure/modify-subnet.png)


## <a name="ip-address"></a>IP 주소

대상 가상 컴퓨터의 각 네트워크 인터페이스에 대한 IP 주소는 다음과 같이 선택됩니다.

### <a name="dhcp"></a>DHCP
원본 가상 컴퓨터의 네트워크 인터페이스에서 DHCP를 사용하는 경우 대상 가상 컴퓨터의 네트워크 인터페이스도 DHCP로 설정됩니다.

### <a name="static-ip"></a>고정 IP
원본 가상 컴퓨터의 네트워크 인터페이스에서 고정 IP를 사용하는 경우 대상 가상 컴퓨터의 네트워크 인터페이스도 고정 IP를 사용하도록 설정됩니다. 고정 IP는 다음과 같이 선택됩니다.

#### <a name="same-address-space"></a>동일한 주소 공간

원본 서브넷과 대상 서브넷에 동일한 주소 공간이 있으면 대상 IP가 원본 가상 컴퓨터의 네트워크 인터페이스 IP와 동일하게 설정됩니다. 동일한 IP를 사용할 수 없는 경우 몇 가지 다른 사용 가능한 IP가 대상 IP로 설정됩니다.

#### <a name="different-address-space"></a>다른 주소 공간

원본 서브넷과 대상 서브넷에 다른 주소 공간이 있으면 대상 IP가 대상 서브넷의 사용 가능한 IP로 설정됩니다.

가상 컴퓨터의 계산 및 네트워크 설정으로 이동하여 각 네트워크 인터페이스의 대상 IP를 수정할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure VM 복제를 위한 네트워킹 지침](site-recovery-azure-to-azure-networking-guidance.md)에 대해 자세히 알아봅니다.
