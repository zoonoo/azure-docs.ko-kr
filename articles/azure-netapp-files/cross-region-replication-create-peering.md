---
title: Azure NetApp Files에 대 한 복제 피어 링 만들기 | Microsoft Docs
description: 지역 간 복제를 설정 하는 Azure NetApp Files에 대 한 볼륨 복제 피어 링을 만드는 방법을 설명 합니다.
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
ms.date: 09/16/2020
ms.author: b-juche
ms.openlocfilehash: df70f8a37a7223119068afd323583ea6126ca542
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/16/2020
ms.locfileid: "90708825"
---
# <a name="create-replication-peering-for-azure-netapp-files"></a>Azure NetApp Files에 대 한 복제 피어 링 만들기

> [!IMPORTANT]
> 지역 간 복제 기능은 현재 공개 미리 보기로 제공 됩니다. [Azure NetApp Files 지역 간 복제 waitlist 제출 페이지](https://aka.ms/anfcrrpreviewsignup)를 통해 기능에 액세스 하기 위한 waitlist 요청을 제출 해야 합니다. 지역 간 복제 기능을 사용 하기 전에 Azure NetApp Files 팀의 공식 확인 전자 메일을 기다리십시오.

이 문서에서는 복제 피어 링을 만들어 지역 간 복제를 설정 하는 방법을 보여 줍니다. 

복제 피어 링을 설정 하면 Azure NetApp Files 볼륨 (원본)에서 다른 Azure NetApp Files 볼륨 (대상)으로 데이터를 비동기식으로 복제할 수 있습니다. 원본 볼륨과 대상 볼륨은 별도의 지역에 배포 해야 합니다. 대상 용량 풀의 서비스 수준이 원본 용량 풀의 서비스 수준과 일치 하거나 다른 서비스 수준을 선택할 수 있습니다.   

Azure NetApp Files 복제는 현재 여러 구독을 지원 하지 않습니다. 모든 복제는 단일 구독에서 수행 해야 합니다.

시작 하기 전에 [지역 간 복제 사용에 대 한 요구 사항 및 고려](cross-region-replication-requirements-considerations.md)사항을 검토 했는지 확인 합니다.  

## <a name="locate-the-source-volume-resource-id"></a>원본 볼륨 리소스 ID 찾기  

복제 하려는 원본 볼륨의 리소스 ID를 가져와야 합니다. 

1. 원본 볼륨으로 이동 하 고 설정 아래에서 **속성** 을 선택 하 여 원본 볼륨 리소스 ID를 표시 합니다.   
    ![원본 볼륨 리소스 ID 찾기](../media/azure-netapp-files/cross-region-replication-source-volume-resource-id.png)
 
2. 리소스 ID를 클립보드에 복사 합니다.  이 시간은 나중에 필요합니다.

## <a name="create-the-data-replication-volume-the-destination-volume"></a>데이터 복제 볼륨 (대상 볼륨) 만들기

원본 볼륨의 데이터를 복제 하려는 대상 볼륨을 만들어야 합니다.  대상 볼륨을 만들려면 먼저 NetApp 계정 및 대상 지역에 용량 풀이 있어야 합니다. 

1. 대상 계정은 원본 볼륨 영역에서 다른 지역에 있어야 합니다. 필요한 경우 [netapp 계정 만들기](azure-netapp-files-create-netapp-account.md)의 단계를 수행 하 여 복제에 사용할 Netapp 계정을 Azure 지역에 만듭니다.   
다른 지역에서 기존 NetApp 계정을 선택할 수도 있습니다.  

2. 필요한 경우 [용량 풀 설정](azure-netapp-files-set-up-capacity-pool.md)의 단계에 따라 새로 만든 netapp 계정에 용량 풀을 만듭니다.   

    또한 복제 대상 볼륨을 호스트할 기존 용량 풀을 선택할 수 있습니다.  

    대상 용량 풀의 서비스 수준이 원본 용량 풀의 서비스 수준과 일치 하거나 다른 서비스 수준을 선택할 수 있습니다.

3. [Azure NetApp Files에 서브넷 위임](azure-netapp-files-delegate-subnet.md)의 단계를 수행 하 여 복제에 사용할 지역의 서브넷을 위임 합니다.

4. 대상 NetApp 계정의 저장소 서비스에서 **볼륨** 을 선택 하 여 데이터 복제 볼륨을 만듭니다. **+ 데이터 복제 추가** 단추를 클릭 합니다.  

    ![데이터 복제 추가](../media/azure-netapp-files/cross-region-replication-add-data-replication.png)
 
5. 표시 되는 볼륨 만들기 페이지의 **기본 사항** 탭에서 다음 필드를 완료 합니다.
    * 볼륨 이름
    * 용량 풀
    * 볼륨 할당량
        > [!NOTE] 
        > 원본 볼륨의 볼륨 할당량 크기를 미러 하는 것이 좋습니다.
    * 가상 네트워크 
    * 서브넷

    필드에 대 한 자세한 내용은 [NFS 볼륨 만들기](azure-netapp-files-create-volumes.md#create-an-nfs-volume)를 참조 하세요. 

6. **프로토콜** 탭에서 원본 볼륨과 동일한 프로토콜을 선택 합니다.  
NFS 프로토콜의 경우 내보내기 정책 규칙이 내보내기에 액세스할 원격 네트워크에 있는 모든 호스트의 요구 사항을 충족 하는지 확인 합니다.  

7. **태그** 탭에서 필요에 따라 키/값 쌍을 만듭니다.  

8. **복제** 탭에서 [원본 볼륨 리소스 id 찾기](#locate-the-source-volume-resource-id)에서 가져온 원본 볼륨 리소스 id를 붙여넣고 원하는 복제 일정을 선택 합니다. 복제 일정에 대 한 옵션에는 10 분 마다, 매시간, 매일, 매주, 매월 등이 있습니다.  

    ![볼륨 복제 만들기](../media/azure-netapp-files/cross-region-replication-create-volume-replication.png)

9. **검토 + 만들기**를 클릭 한 다음 **만들기** 를 클릭 하 여 데이터 복제 볼륨을 만듭니다.   

    ![복제 검토 및 만들기](../media/azure-netapp-files/cross-region-replication-review-create-replication.png)

## <a name="authorize-replication-from-the-source-volume"></a>원본 볼륨의 복제 권한 부여  

복제 권한을 부여 하려면 복제 대상 볼륨의 리소스 ID를 가져와서 복제 원본 볼륨의 권한 부여 필드에 붙여 넣어야 합니다. 

1. Azure Portal에서 Azure NetApp Files로 이동 합니다.

2. 대상 NetApp 계정 및 복제 대상 볼륨이 있는 대상 용량 풀로 이동 합니다.

3. 복제 대상 볼륨을 선택 하 고, 설정 아래의 **속성** 으로 이동 하 고, 대상 볼륨의 **리소스 ID** 를 찾습니다. 대상 볼륨 리소스 ID를 클립보드에 복사 합니다.

    ![속성 리소스 ID](../media/azure-netapp-files/cross-region-replication-properties-resource-id.png) 
 
4. Azure NetApp Files에서 복제 원본 계정 및 원본 용량 풀로 이동 합니다. 

5. 복제 원본 볼륨을 찾아 선택 합니다. 저장소 서비스의 **복제** 로 이동 하 고 **권한 부여**를 클릭 합니다.

    ![복제 권한 부여](../media/azure-netapp-files/cross-region-replication-authorize.png) 

6. 권한 부여 필드에 3 단계에서 가져온 대상 복제 볼륨 리소스 ID를 붙여넣은 다음 **확인**을 클릭 합니다.

## <a name="next-steps"></a>다음 단계  

* [지역 간 복제](cross-region-replication-introduction.md)
* [지역 간 복제 사용을 위한 요구 사항 및 고려 사항](cross-region-replication-requirements-considerations.md)
* [복제 관계의 상태를 표시 합니다.](cross-region-replication-display-health-status.md)
* [볼륨 복제 메트릭](azure-netapp-files-metrics.md#replication)
* [재해 복구 관리](cross-region-replication-manage-disaster-recovery.md)
* [지역 간 복제 문제 해결](troubleshoot-cross-region-replication.md)

