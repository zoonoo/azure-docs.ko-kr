---
title: Azure NetApp Files에 대한 용량 풀 설정 | Microsoft Docs
description: 볼륨을 만들 수 있도록 용량 풀을 설정하는 방법을 설명합니다.
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
ms.topic: how-to
ms.date: 09/22/2020
ms.author: b-juche
ms.openlocfilehash: 2b52ad50854092cddd7b9e79cbeebd4a83017081
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91325416"
---
# <a name="set-up-a-capacity-pool"></a>용량 풀 설정

용량 풀 설정을 통해 볼륨을 만들 수 있습니다.  

## <a name="before-you-begin"></a>시작하기 전에 

NetApp 계정을 만들어야 합니다.   

[NetApp 계정 만들기](azure-netapp-files-create-netapp-account.md)

## <a name="steps"></a>단계 

1. NetApp 계정의 관리 블레이드로 이동한 다음, 탐색 창에서 **용량 풀**을 클릭합니다.  
    
    ![용량 풀로 이동](../media/azure-netapp-files/azure-netapp-files-navigate-to-capacity-pool.png)

2. **+ 풀 추가**를 클릭하여 새 용량 풀을 만듭니다.   
    새 용량 풀 창이 나타납니다.

3. 새 용량 풀에 대한 다음과 같은 정보를 제공합니다.  
   * **Name**  
     용량 풀의 이름을 지정합니다.  
     용량 풀 이름은 각 NetApp 계정에 대해 고유해야 합니다.

   * **서비스 수준**   
     이 필드는 용량 풀에 대한 대상 성능을 보여줍니다.  
     용량 풀의 서비스 수준 ( [**Ultra**](azure-netapp-files-service-levels.md#Ultra), [**Premium**](azure-netapp-files-service-levels.md#Premium)또는 [**Standard**](azure-netapp-files-service-levels.md#Standard))을 지정 합니다.

    * **크기가**     
     구입하려는 용량 풀의 크기를 지정합니다.        
     최소 용량 풀 크기는 4TiB입니다. 4TiB의 배수인 크기로 풀을 만들 수 있습니다.   

   * **QoS**   
     용량 풀에서 **수동** 또는 **자동** QoS 유형을 사용 해야 하는지 여부를 지정 합니다.  

     QoS 유형을 이해 하려면 [저장소 계층](azure-netapp-files-understand-storage-hierarchy.md) 및 [성능 고려 사항](azure-netapp-files-performance-considerations.md) 을 참조 하세요.  

     > [!IMPORTANT] 
     > **QoS 유형을** **Manual** 로 설정 하는 것은 영구적입니다. 자동 QoS를 사용 하도록 수동 QoS 용량 풀을 변환할 수 없습니다. 그러나 수동 QoS를 사용 하도록 자동 QoS 용량 풀을 변환할 수 있습니다. [수동 QoS를 사용 하려면 용량 풀 변경을](manage-manual-qos-capacity-pool.md#change-to-qos)참조 하세요.   
     > 용량 풀에 수동 QoS 유형을 사용 하려면 등록이 필요 합니다. [수동 QoS 용량 풀 관리를](manage-manual-qos-capacity-pool.md#register-the-feature)참조 하세요. 

    ![새 용량 풀](../media/azure-netapp-files/azure-netapp-files-new-capacity-pool.png)

4. **만들기**를 클릭합니다.

## <a name="next-steps"></a>다음 단계 

- [저장소 계층 구조](azure-netapp-files-understand-storage-hierarchy.md) 
- [Azure NetApp Files에 대한 서비스 수준](azure-netapp-files-service-levels.md)
- [가격 책정 페이지 Azure NetApp Files](https://azure.microsoft.com/pricing/details/storage/netapp/)
- [수동 QoS 용량 풀 관리](manage-manual-qos-capacity-pool.md)
- [Azure NetApp Files에 서브넷 위임](azure-netapp-files-delegate-subnet.md)
