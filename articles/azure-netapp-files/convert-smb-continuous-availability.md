---
title: 기존 Azure NetApp Files SMB 볼륨을 SMB 지속적인 가용성을 사용하도록 변환 | Microsoft Docs
description: 기존 Azure NetApp Files SMB 볼륨을 변환하여 SMB CA를 사용하도록 설정하는 방법을 설명합니다.
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
ms.date: 05/10/2021
ms.author: b-juche
ms.openlocfilehash: b55b7e5632ad852b199121179772fa078129cdb5
ms.sourcegitcommit: 1b19b8d303b3abe4d4d08bfde0fee441159771e1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2021
ms.locfileid: "109755105"
---
# <a name="convert-existing-smb-volumes-to-use-continuous-availability"></a>지속적인 가용성을 사용하도록 기존 SMB 볼륨 변환 

[새 SMB 볼륨을 만들](azure-netapp-files-create-volumes-smb.md#add-an-smb-volume) 때 SMB CA(지속적인 가용성) 기능을 사용하도록 설정할 수 있습니다. SMB CA 기능을 사용하도록 기존 SMB 볼륨을 변환할 수도 있습니다.  이 문서에서는 기존 볼륨을 변환하여 SMB CA를 사용하도록 설정하는 방법을 보여줍니다.  

> [!IMPORTANT]
> 이 절차에는 원래 볼륨에서 CA 공유에 대해 사용하도록 설정된 새 볼륨으로의 전환이 포함됩니다. 따라서 이 프로세스에 대한 유지 관리 기간을 계획해야 합니다. 

## <a name="steps"></a>단계

1. [SMB 지속적인 가용성 공유](https://aka.ms/anfsmbcasharespreviewsignup) 기능을 등록했는지 확인합니다.  
2. SMB 볼륨을 사용하는 애플리케이션을 중지합니다. 
3. 기존 볼륨의 [주문형 스냅샷을 만듭니다](azure-netapp-files-manage-snapshots.md#create-an-on-demand-snapshot-for-a-volume). 
4. 기존 볼륨에서 **스냅샷** 을 선택하여 스냅샷 목록을 표시합니다.
5. 복원할 스냅샷을 마우스 오른쪽 단추로 클릭하고 메뉴 옵션에서 **새 볼륨으로 복원** 을 선택합니다.
    
    ![Restore to New Volume(새 볼륨으로 복원) 옵션을 보여주는 스냅샷.](../media/azure-netapp-files/azure-netapp-files-snapshot-restore-to-new-volume.png)

6. 표시되는 볼륨 만들기 창에서 새 볼륨에 대한 정보를 제공합니다.  

    * **볼륨 이름**    
    만들고 있는 볼륨의 이름을 지정합니다.   
    이름은 각 리소스 그룹 내에서 고유해야 합니다. 3자 이상이어야 합니다. 영숫자 문자를 사용할 수 있습니다.

    * **할당량**   
    볼륨에 할당하려는 논리 스토리지의 크기를 지정합니다.

    ![볼륨 만들기 창을 보여주는 스냅샷.](../media/azure-netapp-files/snapshot-restore-new-volume.png) 

7. 볼륨 만들기 창의 프로토콜 섹션에서 **지속적인 가용성 사용** 옵션을 선택했는지 확인합니다.

    ![지속적인 가용성 사용 옵션을 보여주는 스냅샷.](../media/azure-netapp-files/enable-continuous-availability-option.png) 

8. **검토 + 만들기** 를 클릭합니다. **만들기** 를 클릭합니다.   
    새 볼륨은 스냅샷에서 사용하는 것과 동일한 프로토콜을 사용합니다.
   스냅샷이 복원된 새 볼륨은 볼륨 보기에 표시됩니다.

9. 새 볼륨을 만든 후 선택한 볼륨 블레이드에서 **탑재 지침** 을 클릭합니다. 그런 다음, 지침에 따라 새 볼륨을 탑재합니다.    

10. 새 볼륨 탑재 지점을 사용하도록 애플리케이션을 다시 구성합니다.   

11. 2단계 중에 중지한 애플리케이션을 다시 시작합니다. 

12. 애플리케이션이 새 볼륨을 활용하기 시작하고 새 볼륨으로 복원하는 프로세스가 완료되면 필요에 따라 원래 볼륨을 삭제할 수 있습니다.  

## <a name="next-steps"></a>다음 단계  

* [Azure NetApp Files에 대한 SMB 볼륨 만들기](azure-netapp-files-create-volumes-smb.md)
* [Windows 또는 Linux 가상 머신에 대한 볼륨 탑재 또는 탑재 해제](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)