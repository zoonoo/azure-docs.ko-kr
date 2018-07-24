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
ms.date: 03/28/2018
ms.author: b-juche
ms.openlocfilehash: 42e475f4da2102bb8b010daec6e6451ba7f13848
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/13/2018
ms.locfileid: "39011095"
---
# <a name="create-a-volume-for-azure-netapp-files"></a>Azure NetApp Files에 대한 볼륨 만들기

볼륨의 용량 소비는 해당 풀의 프로비전된 용량에 대해 계산됩니다.  용량 풀에서 여러 볼륨을 만들 수 있지만 볼륨의 총 용량 사용량은 풀 크기를 초과해서는 안 됩니다. 

## <a name="before-you-begin"></a>시작하기 전에 
용량 풀을 설정해야 합니다.  

[용량 풀 설정](azure-netapp-files-set-up-capacity-pool.md)

## <a name="steps"></a>단계 
1.  용량 풀 관리 블레이드에서 **볼륨** 블레이드를 클릭합니다. 
2.  **+ 볼륨 추가**를 클릭하여 볼륨을 만듭니다.  
    새 볼륨 창이 나타납니다.

3.  새 볼륨 창에서 **만들기**를 클릭하고 다음 필드에 대한 정보를 제공합니다.   
    * **이름**      
        만드는 볼륨에 대한 이름을 지정합니다.   
        이름은 각 리소스 그룹 내에서 고유해야 합니다. 3자 이상이어야 합니다.  영숫자 문자를 사용할 수 있습니다.

    * **파일 경로**  
        새 볼륨에 대한 내보내기 경로를 만드는 데 사용할 파일 경로를 지정합니다. 내보내기 경로는 볼륨을 탑재하고 액세스하는 데 사용됩니다.   
        탑재 대상은 NFS 서비스 IP 주소의 엔드포인트입니다. 자동으로 생성됩니다.    
        파일 경로 이름에는 문자, 숫자 및 하이픈("-")만 포함할 수 있습니다. 16자~40자여야 합니다.  

    * **할당량**  
        볼륨에 할당되는 논리 저장소의 크기를 지정합니다.  
        **사용 가능한 할당량** 필드는 새 볼륨을 만들 때 사용할 수 있는 선택한 용량 풀에서 사용되지 않은 공간의 양을 보여줍니다. 새 볼륨의 크기는 사용 가능한 할당량을 초과해서는 안 됩니다.  

    * **가상 네트워크**  
        볼륨에 액세스하려는 Azure Vnet(가상 네트워크)을 지정합니다. 지정하는 Vnet은 구성된 Azure NetApp Files가 있어야 합니다. Azure NetApp Files 서비스는 볼륨과 같은 위치에 있는 Vnet에서만 액세스할 수 있습니다.   

    ![새 볼륨](../media/azure-netapp-files/azure-netapp-files-new-volume.png)

4.  **확인**을 클릭합니다. 
 
볼륨은 해당 용량 풀에서 구독, 리소스 그룹, 위치 특성을 상속합니다. 볼륨 배포 상태를 모니터링하려면 알림 탭을 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계  
[볼륨에 대한 내보내기 정책 구성(선택 사항)](azure-netapp-files-configure-export-policy.md)

