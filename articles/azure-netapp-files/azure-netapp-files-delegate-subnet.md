---
title: Azure NetApp Files에 서브넷 위임 | Microsoft Docs
description: 서브넷을 Azure NetApp Files에 위임하는 방법에 대해 설명합니다.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: b-juche
ms.openlocfilehash: b83f530549ffa43789963fd0c95b4982f5289356
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80054456"
---
# <a name="delegate-a-subnet-to-azure-netapp-files"></a>Azure NetApp Files에 서브넷 위임 

Azure NetApp Files에 서브넷을 위임해야 합니다.   볼륨을 만들 때는 위임된 서브넷을 지정해야 합니다.

## <a name="considerations"></a>고려 사항
* 새 서브넷 만들기용 마법사의 기본값은 /24 네트워크 마스크이며, 이는 251개의 IP 주소를 제공합니다. 16개의 사용 가능한 IP 주소를 제공하는 /28 네트워크 마스크를 서비스에 사용하면 충분합니다.
* 각 Azure Virtual Network(VNet)에서 하나의 서브넷만 Azure NetApp Files에 위임할 수 있습니다.   
   Azure를 통해 VNet에서 여러 개의 위임 된 서브넷을 만들 수 있습니다.  그러나 두 개 이상의 위임 된 서브넷을 사용 하는 경우 새 볼륨을 만들려는 모든 시도가 실패 합니다.
* 위임된 서브넷에서 네트워크 보안 그룹 또는 서비스 엔드포인트를 지정할 수 없습니다. 이렇게 하면 서브넷 위임이 실패합니다.
* 전역적으로 피어 링 가상 네트워크의 볼륨에 대 한 액세스는 현재 지원 되지 않습니다.
* 주소 접두사 (대상)가 Azure NetApp Files에 위임 된 서브넷에 대 한 [사용자 정의 사용자 지정 경로](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes) 를 만드는 것은 지원 되지 않습니다. 이렇게 하면 VM 연결에 영향을 줍니다.

## <a name="steps"></a>단계 
1.  Azure Portal에서 **가상 네트워크** 블레이드로 이동하여 Azure NetApp Files에 사용할 가상 네트워크를 선택합니다.    

1. 가상 네트워크 블레이드에서 **서브넷**을 선택하고 **+서브넷** 단추를 클릭합니다. 

1. 서브넷 추가 페이지에서 다음 필수 필드를 완료하여 Azure NetApp Files에 사용할 새 서브넷을 만듭니다.
    * **이름**: 서브넷 이름을 지정 합니다.
    * **주소 범위**: IP 주소 범위를 지정 합니다.
    * **서브넷 위임**: **Microsoft netapp/볼륨**을 선택 합니다. 

      ![서브넷 위임](../media/azure-netapp-files/azure-netapp-files-subnet-delegation.png)
    
[Azure NetApp Files용 볼륨을 만들](azure-netapp-files-create-volumes.md) 때 서브넷을 만들고 위임할 수도 있습니다. 

## <a name="next-steps"></a>다음 단계  
* [Azure NetApp Files에 대한 볼륨 만들기](azure-netapp-files-create-volumes.md)
* [Azure 서비스에 대 한 가상 네트워크 통합에 대해 알아보기](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)


