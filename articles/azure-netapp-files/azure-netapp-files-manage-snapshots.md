---
title: NetApp Azure Files를 사용하여 스냅샷 관리 | Microsoft Docs
description: Azure NetApp Files를 사용하여 스냅샷을 만들고, 관리하고, 사용하는 방법을 설명합니다.
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
ms.date: 06/14/2021
ms.author: b-juche
ms.openlocfilehash: b0cc4deebac60fcf0ac4df20b112b8f69da05414
ms.sourcegitcommit: 8651d19fca8c5f709cbb22bfcbe2fd4a1c8e429f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/14/2021
ms.locfileid: "112072112"
---
# <a name="manage-snapshots-by-using-azure-netapp-files"></a>NetApp Azure Files를 사용하여 스냅샷 관리

Azure NetApp Files는 주문형 스냅샷 만들기 및 스냅샷 정책을 사용하여 자동 스냅샷 만들기 예약을 지원합니다. 스냅샷을 새 볼륨으로 복원하거나, 클라이언트를 사용하여 단일 파일을 복원하거나, 스냅샷을 사용하여 기존 볼륨을 되돌릴 수도 있습니다.

> [!NOTE] 
> 지역 간 복제의 스냅샷 관리에 대한 고려 사항은 [지역 간 복제 사용을 위한 요구 사항 및 고려 사항](cross-region-replication-requirements-considerations.md)을 참조하세요.

## <a name="create-an-on-demand-snapshot-for-a-volume"></a>볼륨에 대한 주문형 스냅샷 만들기

주문형 방식으로 볼륨 스냅샷을 만들 수 있습니다. 

1.  스냅샷을 만들 볼륨으로 이동합니다. **스냅샷** 을 클릭합니다.

    ![스냅샷으로 이동](../media/azure-netapp-files/azure-netapp-files-navigate-to-snapshots.png)

2.  **+ 스냅샷 추가** 를 클릭하여 볼륨에 대한 주문형 스냅샷을 만듭니다.

    ![스냅샷 추가](../media/azure-netapp-files/azure-netapp-files-add-snapshot.png)

3.  새 스냅샷 창에 만들고 있는 새 스냅샷의 이름을 제공합니다.   

    ![새 스냅샷](../media/azure-netapp-files/azure-netapp-files-new-snapshot.png)

4. **확인** 을 클릭합니다. 

## <a name="manage-snapshot-policies"></a>스냅샷 정책 관리

스냅샷 정책을 사용하여 볼륨 스냅샷을 자동으로 만들도록 예약할 수 있습니다. 필요에 따라 스냅샷 정책을 수정하거나 더 이상 필요하지 않은 스냅샷 정책을 삭제할 수도 있습니다.  

### <a name="create-a-snapshot-policy"></a>스냅샷 정책 만들기 

스냅샷 정책을 사용하면 스냅샷 만들기 빈도를 매시간, 매일, 매주 또는 매월 주기로 지정할 수 있습니다. 또한 볼륨에 대해 보관할 최대 스냅샷 수를 지정해야 합니다.  

1.  NetApp 계정 보기에서 **스냅샷 정책** 을 클릭합니다.

    ![스냅샷 정책 살펴보기](../media/azure-netapp-files/snapshot-policy-navigation.png)

2.  스냅샷 정책 창에서 정책 상태를 **사용** 으로 설정합니다. 

3.  **매시간,** **매일,** **매주** 또는 **매월** 탭을 클릭하여 매시간, 매일, 매주 또는 매월 스냅샷 정책을 만듭니다. **보관할 스냅샷 수** 를 지정합니다.  

    볼륨에 허용되는 최대 스냅샷 수에 대해서는 [Azure NetApp Files에 대한 리소스 제한](azure-netapp-files-resource-limits.md)을 참조하세요. 

    다음 예제에서는 매시간 스냅샷 정책 구성을 보여 줍니다. 

    ![매시간 스냅샷 정책](../media/azure-netapp-files/snapshot-policy-hourly.png)

    다음 예제에서는 매일 스냅샷 정책 구성을 보여 줍니다.

    ![매일 스냅샷 정책](../media/azure-netapp-files/snapshot-policy-daily.png)

    다음 예제에서는 매주 스냅샷 정책 구성을 보여 줍니다.

    ![매주 스냅샷 정책](../media/azure-netapp-files/snapshot-policy-weekly.png)

    다음 예제에서는 매월 스냅샷 정책 구성을 보여 줍니다.

    ![매월 스냅샷 정책](../media/azure-netapp-files/snapshot-policy-monthly.png) 

4.  **저장** 을 클릭합니다.  

추가 스냅샷 정책을 만들어야 하는 경우 3단계를 반복합니다.
만든 정책이 스냅샷 정책 페이지에 표시됩니다.

볼륨에서 스냅샷 정책을 사용하려면 [볼륨에 정책을 적용](azure-netapp-files-manage-snapshots.md#apply-a-snapshot-policy-to-a-volume)해야 합니다. 

### <a name="apply-a-snapshot-policy-to-a-volume"></a>볼륨에 스냅샷 정책 적용

볼륨에서 만든 스냅샷 정책을 사용하려면 볼륨에 정책을 적용해야 합니다. 

지역 간 복제에서는 대상 볼륨에 스냅샷 정책을 적용할 수 없습니다.  

1.  **볼륨** 페이지로 이동하여 스냅샷 정책을 적용할 볼륨을 마우스 오른쪽 단추로 클릭하고 **편집** 을 선택합니다.

    ![볼륨 오른쪽 클릭 메뉴](../media/azure-netapp-files/volume-right-cick-menu.png) 

2.  편집 창의 **스냅샷 정책** 에서 볼륨에 사용할 정책을 선택합니다.  **확인** 을 클릭하여 정책을 적용합니다.  

    ![스냅샷 정책 편집](../media/azure-netapp-files/snapshot-policy-edit.png) 

### <a name="modify-a-snapshot-policy"></a>스냅샷 정책 수정 

기존 스냅샷 정책을 수정하여 정책 상태, 스냅샷 빈도(매시간, 매일, 매주 또는 매월) 또는 보존할 스냅샷 수를 변경할 수 있습니다.  
 
1.  NetApp 계정 보기에서 **스냅샷 정책** 을 클릭합니다.

2.  수정하려는 스냅샷 정책을 마우스 오른쪽 단추로 클릭한 다음, **편집** 을 선택합니다.

    ![스냅샷 정책 오른쪽 클릭 메뉴](../media/azure-netapp-files/snapshot-policy-right-click-menu.png) 

3.  나타나는 스냅샷 정책 창에서 변경을 수행한 후 **저장** 을 클릭합니다. 

### <a name="delete-a-snapshot-policy"></a>스냅샷 정책 삭제 

더 이상 보존하지 않으려는 스냅샷 정책을 삭제할 수 있습니다.   

1.  NetApp 계정 보기에서 **스냅샷 정책** 을 클릭합니다.

2.  수정하려는 스냅샷 정책을 마우스 오른쪽 단추로 클릭한 다음, **삭제** 를 선택합니다.

    ![스냅샷 정책 오른쪽 클릭 메뉴](../media/azure-netapp-files/snapshot-policy-right-click-menu.png) 

3.  **예** 를 클릭하여 스냅샷 정책을 삭제할 것임을 확인합니다.   

    ![스냅샷 정책 삭제 확인](../media/azure-netapp-files/snapshot-policy-delete-confirm.png) 

## <a name="edit-the-hide-snapshot-path-option"></a>스냅샷 경로 숨기기 옵션 편집
스냅샷 경로 숨기기 옵션은 볼륨의 스냅샷 경로가 표시되는지 여부를 제어합니다. [NFS](azure-netapp-files-create-volumes.md#create-an-nfs-volume) 또는 [SMB](azure-netapp-files-create-volumes-smb.md#add-an-smb-volume) 볼륨을 만드는 동안 스냅샷 경로를 숨길지 여부를 지정하는 옵션이 제공됩니다. 이후에 필요에 따라 스냅샷 경로 숨기기 옵션을 편집할 수 있습니다.  

> [!NOTE]
> 지역 간 복제의 [대상 볼륨](cross-region-replication-create-peering.md#create-the-data-replication-volume-the-destination-volume)에서는 스냅샷 경로 숨기기 옵션이 기본적으로 사용하도록 설정되며 이 설정을 수정할 수 없습니다. 

1. 볼륨의 스냅샷 경로 숨기기 옵션 설정을 보려면 볼륨을 선택합니다. **스냅샷 경로 숨기기** 필드에는 옵션이 사용하도록 설정되어 있는지 여부가 표시됩니다.   
    ![스냅샷 경로 숨기기 필드를 설명하는 스크린샷](../media/azure-netapp-files/hide-snapshot-path-field.png) 
2. 스냅샷 경로 숨기기 옵션을 편집하려면 볼륨 페이지에서 **편집** 을 클릭하고 필요에 따라 **스냅샷 경로 숨기기** 옵션을 수정합니다.   
    ![볼륨 스냅샷 편집 옵션을 설명하는 스크린샷](../media/azure-netapp-files/volume-edit-snapshot-options.png) 

## <a name="restore-a-snapshot-to-a-new-volume"></a>새 볼륨으로 스냅샷 복원

현재 새 볼륨으로만 스냅샷을 복원할 수 있습니다. 
1. 볼륨 블레이드에서 **스냅샷** 을 선택하여 스냅샷 목록을 표시합니다. 
2. 복원할 스냅샷을 마우스 오른쪽 단추로 클릭하고 메뉴 옵션에서 **새 볼륨으로 복원** 을 선택합니다.  

    ![새 볼륨으로 스냅샷 복원](../media/azure-netapp-files/azure-netapp-files-snapshot-restore-to-new-volume.png)

3. 볼륨 만들기 창에서 다음과 같은 새 볼륨 정보를 입력합니다.  
    * **Name**   
        만들고 있는 볼륨의 이름을 지정합니다.  
        
        이름은 각 리소스 그룹 내에서 고유해야 합니다. 3자 이상이어야 합니다.  영숫자 문자를 사용할 수 있습니다.

    * **할당량**  
        볼륨에 할당하려는 논리 스토리지의 크기를 지정합니다.  

    ![새 볼륨으로 복원](../media/azure-netapp-files/snapshot-restore-new-volume.png) 

4. **검토+만들기** 를 클릭합니다.  **만들기** 를 클릭합니다.   
    새 볼륨은 스냅샷에서 사용하는 것과 동일한 프로토콜을 사용합니다.   
    스냅샷이 복원된 새 볼륨은 볼륨 블레이드에 표시됩니다.

## <a name="restore-a-file-from-a-snapshot-using-a-client"></a>클라이언트를 사용하여 스냅샷에서 파일 복원

[전체 스냅샷을 볼륨으로 복원](#restore-a-snapshot-to-a-new-volume)하지 않으려면 볼륨이 탑재된 클라이언트를 사용하여 스냅샷에서 파일을 복원할 수 있습니다.  

탑재된 볼륨에는 스냅샷 디렉터리 `.snapshot`(NFS 클라이언트) 또는 클라이언트에서 액세스할 수 있는 `~snapshot`(SMB 클라이언트)이 포함되어 있습니다. 스냅샷 디렉터리에는 볼륨의 스냅샷에 해당하는 하위 디렉터리가 포함되어 있습니다. 각 하위 디렉터리에는 스냅샷의 파일이 있습니다. 실수로 파일을 삭제하거나 덮어쓰는 경우 스냅샷 하위 디렉터리에서 읽기-쓰기 디렉터리로 파일을 복사하여 파일을 부모 읽기-쓰기 디렉터리로 복원할 수 있습니다. 

[스냅샷 경로 숨기기 옵션](#edit-the-hide-snapshot-path-option)을 사용하여 스냅샷 디렉터리에 대한 액세스를 제어할 수 있습니다. 이 옵션은 디렉터리를 클라이언트에서 숨길지 여부를 제어합니다. 따라서 이 옵션으로 스냅샷의 파일 및 폴더에 대한 액세스도 제어할 수 있습니다.  

NFSv4.1에는 `.snapshot` 디렉터리(`ls -la`)가 표시되지 않습니다. 그러나 스냅샷 경로 숨기기 옵션을 설정하지 않은 경우에도 클라이언트 명령줄에서 `.snapshot` 명령을 사용하여 NFSv4.1을 통해 `cd <snapshot-path>` 디렉터리에 계속 액세스할 수 있습니다. 

### <a name="restore-a-file-by-using-a-linux-nfs-client"></a>Linux NFS 클라이언트를 사용하여 파일 복원 

1. `ls` Linux 명령을 사용하여 `.snapshot` 디렉터리에서 복원하려는 파일을 나열합니다. 

    예를 들면 다음과 같습니다.

    `$ ls my.txt`   
    `ls: my.txt: No such file or directory`   

    `$ ls .snapshot`   
    `daily.2020-05-14_0013/              hourly.2020-05-15_1106/`   
    `daily.2020-05-15_0012/              hourly.2020-05-15_1206/`   
    `hourly.2020-05-15_1006/             hourly.2020-05-15_1306/`   

    `$ ls .snapshot/hourly.2020-05-15_1306/my.txt`   
    `my.txt`

2. `cp` 명령을 사용하여 부모 디렉터리에 파일을 복사합니다.  

    예를 들면 다음과 같습니다. 

    `$ cp .snapshot/hourly.2020-05-15_1306/my.txt .`   

    `$ ls my.txt`   
    `my.txt`   

### <a name="restore-a-file-by-using-a-windows-client"></a>Windows 클라이언트를 사용하여 파일 복원 

1. 볼륨의 `~snapshot`디렉터리가 숨겨진 경우 부모 디렉터리에서 [숨겨진 항목을 표시](https://support.microsoft.com/help/4028316/windows-view-hidden-files-and-folders-in-windows-10)하여 `~snapshot`을 표시합니다.

    ![숨겨진 항목 표시](../media/azure-netapp-files/snapshot-show-hidden.png) 

2. `~snapshot` 내의 하위 디렉터리로 이동하여 복원하려는 파일을 찾습니다.  파일을 마우스 오른쪽 단추로 클릭합니다. **복사** 를 선택합니다.  

    ![복원할 파일 복사](../media/azure-netapp-files/snapshot-copy-file-restore.png) 

3. 부모 디렉터리로 돌아갑니다. 부모 디렉터리를 마우스 오른쪽 단추로 클릭하고 `Paste`를 선택하여 파일을 디렉터리에 붙여넣습니다.

    ![복원할 파일 붙여넣기](../media/azure-netapp-files/snapshot-paste-file-restore.png) 

4. 부모 디렉터리를 마우스 오른쪽 단추로 클릭하고 **속성** 을 선택한 다음, **이전 버전** 탭을 클릭하여 스냅샷 목록을 확인하고 **복원** 을 선택하여 파일을 복원할 수도 있습니다.  

    ![속성 이전 버전](../media/azure-netapp-files/snapshot-properties-previous-version.png) 

## <a name="revert-a-volume-using-snapshot-revert"></a>스냅샷 되돌리기를 사용하여 볼륨 되돌리기

스냅샷 되돌리기 기능을 사용하면 볼륨을 특정 스냅샷을 만들 때의 상태로 신속하게 되돌릴 수 있습니다. 대부분의 경우 볼륨을 되돌리는 것이 스냅샷에서 활성 파일 시스템으로 개별 파일을 복원하는 것보다 훨씬 빠릅니다. 또한 스냅샷을 새 볼륨으로 복원하는 것에 비해 공간 효율성이 더 높습니다. 

볼륨의 스냅샷 메뉴에서 볼륨 되돌리기 옵션을 찾을 수 있습니다. 되돌릴 스냅샷을 선택하면 Azure NetApp Files는 선택한 스냅샷을 만들 때 포함했던 데이터 및 타임스탬프로 볼륨을 되돌립니다. 

> [!IMPORTANT]
> 선택한 스냅샷이 만들어진 후에 만들어진 활성 파일 시스템 데이터와 스냅샷은 손실됩니다. 스냅샷 되돌리기 작업은 대상 볼륨의 모든 데이터를 선택한 스냅샷의 데이터로 바꿉니다. 스냅샷을 선택하는 경우 스냅샷 콘텐츠와 만든 날짜에 주의를 기울여야 합니다. 스냅샷 되돌리기 작업은 실행 취소할 수 없습니다.

1. 볼륨의 **스냅샷** 메뉴로 이동합니다.  되돌리기 작업에 사용할 스냅샷을 마우스 오른쪽 단추로 클릭합니다. **볼륨 되돌리기** 를 선택합니다. 

    ![스냅샷의 오른쪽 클릭 메뉴를 설명하는 스크린샷](../media/azure-netapp-files/snapshot-right-click-menu.png) 

2. 볼륨을 스냅샷으로 되돌리기 창에서 볼륨의 이름을 입력하고 **되돌리기** 를 클릭합니다.   

    이제 볼륨이 선택한 스냅샷의 지정 시간으로 복원됩니다.

    ![볼륨을 스냅샷으로 되돌리기 창 스크린샷](../media/azure-netapp-files/snapshot-revert-volume.png) 

## <a name="delete-snapshots"></a>스냅샷 삭제  

더 이상 보존하지 않아도 되는 스냅샷은 삭제할 수 있습니다. 

> [!IMPORTANT]
> 스냅샷 삭제 작업은 실행 취소할 수 없습니다. 삭제한 스냅샷은 복구할 수 없습니다. 

1. 볼륨의 **스냅샷** 메뉴로 이동합니다. 삭제할 스냅샷을 마우스 오른쪽 단추로 클릭합니다. **삭제** 를 선택합니다.

    ![스냅샷의 오른쪽 클릭 메뉴를 설명하는 스크린샷](../media/azure-netapp-files/snapshot-right-click-menu.png) 

2. 스냅샷 삭제 창에서 **예** 를 클릭하여 스냅샷을 삭제할 것임을 확인합니다. 

    ![스냅샷 삭제를 확인하는 스크린샷](../media/azure-netapp-files/snapshot-confirm-delete.png)  

## <a name="next-steps"></a>다음 단계

* [스냅샷 정책 문제 해결](troubleshoot-snapshot-policies.md)
* [Azure NetApp Files에 대한 리소스 제한](azure-netapp-files-resource-limits.md)
* [Azure NetApp Files 스냅샷 101 동영상](https://www.youtube.com/watch?v=uxbTXhtXCkw&feature=youtu.be)
* [Azure Application Consistent Snapshot Tool이란?](azacsnap-introduction.md)
