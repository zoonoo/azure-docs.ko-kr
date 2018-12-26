---
title: Azure NetApp Files 볼륨에 대한 내보내기 정책 구성 | Microsoft Docs
description: 내보내기 정책을 구성하여 Azure NetApp Files 볼륨에 대한 액세스를 제어하는 방법을 설명합니다.
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
ms.openlocfilehash: 08de7e2ca8cd993a0931f5b16cb9d6c9a04e53dc
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/13/2018
ms.locfileid: "39010959"
---
# <a name="configure-export-policy-for-a-volume-optional"></a>볼륨에 대한 내보내기 정책 구성(선택 사항)

필요에 따라 내보내기 정책을 구성하여 Azure NetApp Files 볼륨에 대한 액세스를 제어할 수 있습니다. 

## <a name="steps"></a>단계 

1.  볼륨 관리 블레이드에서 **내보내기 정책 만들기** 블레이드를 클릭합니다. 

2.  내보내기 정책 규칙을 만들도록 다음 필드에 대한 정보를 지정합니다.   
    *  **인덱스**   
        규칙에 대한 인덱스 번호를 지정합니다.  
        내보내기 정책은 최대 5개의 규칙으로 구성할 수 있습니다. 규칙은 인덱스 번호 목록의 해당 순서에 따라 평가됩니다. 더 낮은 인덱스 번호의 규칙이 먼저 평가됩니다. 예를 들어 인덱스 번호가 1인 규칙은 인덱스 번호가 2인 규칙보다 먼저 계산됩니다. 

    * **허용된 클라이언트**   
        다음 형식 중 하나에 값을 지정합니다.  
        * IPv4 주소(예: `10.1.12.24`) 
        * 비트 수로 표현된 서브넷 마스크가 있는 IPv4 주소(예: `10.1.12.10/4`)

    * **Access**  
        다음 액세스 유형 중 하나를 선택합니다.  
        * 액세스 권한 없음 
        * 읽기 및 쓰기
        * 읽기 전용

    * **프로토콜**   
        내보내기 정책에 사용할 프로토콜을 지정합니다.   
        현재 Azure NetApp Files는 NFSv3만 지원합니다.

    ![내보내기 정책](../media/azure-netapp-files/azure-netapp-files-export-policy.png) 


## <a name="next-steps"></a>다음 단계 
* [볼륨 관리](azure-netapp-files-manage-volumes.md)
* [가상 머신에 대한 볼륨 탑재 또는 분리](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [스냅숏 관리](azure-netapp-files-manage-snapshots.md)
