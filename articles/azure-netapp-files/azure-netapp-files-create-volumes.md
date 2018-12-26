---
title: Azure NetApp Files에 대한 볼륨 만들기 | Microsoft Docs
description: Azure NetApp Files에 대한 볼륨을 만드는 방법을 설명합니다.
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
ms.topic: get-started-article
ms.date: 11/13/2018
ms.author: b-juche
ms.openlocfilehash: 8fc76c33055f7131444a073d2f8560e136c0701d
ms.sourcegitcommit: b254db346732b64678419db428fd9eb200f3c3c5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/14/2018
ms.locfileid: "53413254"
---
# <a name="create-a-volume-for-azure-netapp-files"></a>Azure NetApp Files에 대한 볼륨 만들기

볼륨의 용량 소비는 해당 풀의 프로비전된 용량에 대해 계산됩니다.  용량 풀에서 여러 볼륨을 만들 수 있지만 볼륨의 총 용량 사용량은 풀 크기를 초과해서는 안 됩니다. 

## <a name="before-you-begin"></a>시작하기 전에 
용량 풀을 설정해야 합니다.   
[용량 풀 설정](azure-netapp-files-set-up-capacity-pool.md)   
Azure NetApp Files에 서브넷을 위임해야 합니다.  
[Azure NetApp Files에 서브넷 위임](azure-netapp-files-delegate-subnet.md)


## <a name="steps"></a>단계 
1.  용량 풀 관리 블레이드에서 **볼륨** 블레이드를 클릭합니다. 
2.  **+ 볼륨 추가**를 클릭하여 볼륨을 만듭니다.  
    새 볼륨 창이 나타납니다.

3.  새 볼륨 창에서 **만들기**를 클릭하고 다음 필드에 대한 정보를 제공합니다.   
    * **이름**      
        만들고 있는 볼륨의 이름을 지정합니다.   

        이름은 각 리소스 그룹 내에서 고유해야 합니다. 3자 이상이어야 합니다.  영숫자 문자를 사용할 수 있습니다.

    * **파일 경로**  
        새 볼륨에 대한 내보내기 경로를 만드는 데 사용할 파일 경로를 지정합니다. 내보내기 경로는 볼륨을 탑재하고 액세스하는 데 사용됩니다.   
     
        파일 경로 이름에는 문자, 숫자 및 하이픈("-")만 포함할 수 있습니다. 이름은 16자~40자여야 합니다.  

    * **할당량**  
        볼륨에 할당되는 논리 저장소의 크기를 지정합니다.  

        **사용 가능한 할당량** 필드는 새 볼륨을 만들 때 사용할 수 있는 선택한 용량 풀에서 사용되지 않은 공간의 양을 보여줍니다. 새 볼륨의 크기는 사용 가능한 할당량을 초과해서는 안 됩니다.  

    * **가상 네트워크**  
        볼륨에 액세스하려는 Azure Vnet(가상 네트워크)을 지정합니다.  

        지정하는 VNet에는 Azure NetApp Files에 위임된 서브넷이 있어야 합니다. Azure NetApp Files 서비스는 동일한 VNet 또는 VNet 피어링을 통해 볼륨과 동일한 영역에 있는 VNet에서만 액세스할 수 있습니다. Express 경로를 통해 온-프레미스 네트워크에서 볼륨에 액세스할 수도 있습니다.   

    * **서브넷**  
        볼륨에 사용할 서브넷을 지정합니다.  
        지정하는 서브넷은 Azure NetApp Files에 위임되어야 합니다. 
        
        서브넷을 위임하지 않은 경우에는 볼륨 만들기 페이지에서 **새로 만들기**를 클릭할 수 있습니다. 그런 다음, 서브넷 만들기 페이지에서 서브넷 정보를 지정하고 **Microsoft.NetApp/volumes**를 선택하여 Azure NetApp Files의 서브넷을 위임합니다.    
 
        ![새 볼륨](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![서브넷 만들기](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)


4.  **확인**을 클릭합니다. 
 
볼륨은 해당 용량 풀에서 구독, 리소스 그룹, 위치 특성을 상속합니다. 볼륨 배포 상태를 모니터링하려면 알림 탭을 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계  
* [볼륨에 대한 내보내기 정책 구성(선택 사항)](azure-netapp-files-configure-export-policy.md)
* [Azure 서비스에 대한 가상 네트워크 통합에 대해 알아보기](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)

