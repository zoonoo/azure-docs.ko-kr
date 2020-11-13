---
title: NetApp Azure Files를 사용하여 스냅샷 관리 | Microsoft Docs
description: Azure NetApp Files를 사용 하 여 스냅숏을 만들고 관리 하 고 사용 하는 방법을 설명 합니다.
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
ms.date: 11/12/2020
ms.author: b-juche
ms.openlocfilehash: c64bc8bf265a8e3cc3c490827bdbd79661e3528a
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/13/2020
ms.locfileid: "94591754"
---
# <a name="manage-snapshots-by-using-azure-netapp-files"></a>NetApp Azure Files를 사용하여 스냅샷 관리

Azure NetApp Files는 주문형 스냅숏 만들기 및 스냅숏 정책 사용을 지원 하 여 자동 스냅숏 생성을 예약 합니다. 스냅숏을 새 볼륨으로 복원 하거나, 클라이언트를 사용 하 여 단일 파일을 복원 하거나, 스냅숏을 사용 하 여 기존 볼륨을 되돌릴 수도 있습니다.

## <a name="create-an-on-demand-snapshot-for-a-volume"></a>볼륨에 대한 주문형 스냅샷 만들기

요청 시 볼륨 스냅숏을 만들 수 있습니다. 

1.  스냅숏을 만들 볼륨으로 이동 합니다. **스냅숏** 을 클릭 합니다.

    ![스냅샷으로 이동](../media/azure-netapp-files/azure-netapp-files-navigate-to-snapshots.png)

2.  **+ 스냅샷 추가** 를 클릭하여 볼륨에 대한 주문형 스냅샷을 만듭니다.

    ![스냅샷 추가](../media/azure-netapp-files/azure-netapp-files-add-snapshot.png)

3.  새 스냅샷 창에 만들고 있는 새 스냅샷의 이름을 제공합니다.   

    ![새 스냅샷](../media/azure-netapp-files/azure-netapp-files-new-snapshot.png)

4. **확인** 을 클릭합니다. 

## <a name="manage-snapshot-policies"></a>스냅숏 정책 관리

스냅숏 정책을 사용 하 여 볼륨 스냅숏이 자동으로 수행 되도록 예약할 수 있습니다. 필요에 따라 스냅숏 정책을 수정 하거나 더 이상 필요 하지 않은 스냅숏 정책을 삭제할 수도 있습니다.  

### <a name="register-the-feature"></a>기능 등록

**스냅숏 정책** 기능은 현재 미리 보기로 제공 됩니다. 이 기능을 처음 사용하는 경우 먼저 기능을 등록해야 합니다. 

1. 기능을 등록 합니다. 

    ```azurepowershell-interactive
    Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSnapshotPolicy
    ```

2. 기능 등록의 상태를 확인 합니다. 

    > [!NOTE]
    > **RegistrationState** `Registering` 로 변경 하기 전까지 최대 60 분 동안 registrationstate 상태가 될 수 있습니다 `Registered` . 계속 하기 전에 상태가 **등록** 될 때까지 기다립니다.

    ```azurepowershell-interactive
    Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSnapshotPolicy
    ```
[Azure CLI 명령을](/cli/azure/feature?preserve-view=true&view=azure-cli-latest) 사용 하 여 `az feature register` 기능을 `az feature show` 등록 하 고 등록 상태를 표시할 수도 있습니다. 

### <a name="create-a-snapshot-policy"></a>스냅숏 정책 만들기 

스냅숏 정책을 사용 하면 매시간, 매일, 매주 또는 매월 주기로 스냅숏 생성 빈도를 지정할 수 있습니다. 또한 볼륨에 대해 유지할 최대 스냅숏 수를 지정 해야 합니다.  

1.  NetApp 계정 보기에서 **스냅숏 정책** 을 클릭 합니다.

    ![스냅숏 정책 탐색](../media/azure-netapp-files/snapshot-policy-navigation.png)

2.  스냅숏 정책 창에서 정책 상태를 **사용** 으로 설정 합니다. 

3.  **매시간,** **매일** , **매주** 또는 **매월** 탭을 클릭 하 여 매시간, 매일, 매주 또는 매월 스냅숏 정책을 만듭니다. **유지할 스냅숏의 수** 를 지정 합니다.  

    볼륨에 허용 되는 최대 스냅숏 수에 대 한 [Azure NetApp Files 리소스 제한을](azure-netapp-files-resource-limits.md) 참조 하세요. 

    다음 예에서는 매시간 스냅숏 정책 구성을 보여 줍니다. 

    ![스냅숏 정책 매시간](../media/azure-netapp-files/snapshot-policy-hourly.png)

    다음 예제에서는 일별 스냅숏 정책 구성을 보여 줍니다.

    ![매일 스냅숏 정책](../media/azure-netapp-files/snapshot-policy-daily.png)

    다음 예에서는 주별 스냅숏 정책 구성을 보여 줍니다.

    ![매주 스냅숏 정책](../media/azure-netapp-files/snapshot-policy-weekly.png)

    다음 예에서는 월간 스냅숏 정책 구성을 보여 줍니다.

    ![스냅숏 정책 매월](../media/azure-netapp-files/snapshot-policy-monthly.png) 

4.  **저장** 을 클릭합니다.  

추가 스냅숏 정책을 만들어야 하는 경우 3 단계를 반복 합니다.
만든 정책이 스냅숏 정책 페이지에 표시 됩니다.

볼륨에서 스냅숏 정책을 사용 하려면 해당 [정책을 볼륨에 적용](azure-netapp-files-manage-snapshots.md#apply-a-snapshot-policy-to-a-volume)해야 합니다. 

### <a name="apply-a-snapshot-policy-to-a-volume"></a>볼륨에 스냅숏 정책 적용

만든 스냅숏 정책을 볼륨에 사용 하려면 해당 정책을 볼륨에 적용 해야 합니다. 

지역 간 복제에서 대상 볼륨에 스냅숏 정책을 적용할 수 없습니다.  

1.  **볼륨** 페이지로 이동 하 여 스냅숏 정책을 적용 하려는 볼륨을 마우스 오른쪽 단추로 클릭 하 고 **편집** 을 선택 합니다.

    ![볼륨 오른쪽 클릭 메뉴](../media/azure-netapp-files/volume-right-cick-menu.png) 

2.  편집 창의 **스냅숏 정책** 에서 볼륨에 사용할 정책을 선택 합니다.  **확인** 을 클릭 하 여 정책을 적용 합니다.  

    ![스냅숏 정책 편집](../media/azure-netapp-files/snapshot-policy-edit.png) 

### <a name="modify-a-snapshot-policy"></a>스냅숏 정책 수정 

기존 스냅숏 정책을 수정 하 여 정책 상태, 스냅숏 빈도 (매시간, 매일, 매주 또는 매월) 또는 유지할 스냅숏 수를 변경할 수 있습니다.  
 
1.  NetApp 계정 보기에서 **스냅숏 정책** 을 클릭 합니다.

2.  수정 하려는 스냅숏 정책을 마우스 오른쪽 단추로 클릭 한 다음 **편집** 을 선택 합니다.

    ![스냅숏 정책 마우스 오른쪽 단추 클릭 메뉴](../media/azure-netapp-files/snapshot-policy-right-click-menu.png) 

3.  표시 되는 스냅숏 정책 창에서 변경한 다음 **저장** 을 클릭 합니다. 

### <a name="delete-a-snapshot-policy"></a>스냅숏 정책 삭제 

더 이상 유지 하지 않을 스냅숏 정책을 삭제할 수 있습니다.   

1.  NetApp 계정 보기에서 **스냅숏 정책** 을 클릭 합니다.

2.  수정 하려는 스냅숏 정책을 마우스 오른쪽 단추로 클릭 한 다음 **삭제** 를 선택 합니다.

    ![스냅숏 정책 마우스 오른쪽 단추 클릭 메뉴](../media/azure-netapp-files/snapshot-policy-right-click-menu.png) 

3.  **예** 를 클릭 하 여 스냅숏 정책의 삭제를 확인 합니다.   

    ![스냅숏 정책 삭제 확인](../media/azure-netapp-files/snapshot-policy-delete-confirm.png) 

## <a name="edit-the-hide-snapshot-path-option"></a>스냅숏 경로 숨기기 옵션을 편집 합니다.
스냅숏 경로 숨기기 옵션은 볼륨의 스냅숏 경로를 표시할지 여부를 제어 합니다. [NFS](azure-netapp-files-create-volumes.md#create-an-nfs-volume) 또는 [SMB](azure-netapp-files-create-volumes-smb.md#add-an-smb-volume) 볼륨을 만드는 동안 스냅숏 경로를 숨길지 여부를 지정 하는 옵션이 있습니다. 나중에 필요에 따라 스냅숏 경로 숨기기 옵션을 편집할 수 있습니다.  

> [!NOTE]
> 지역 간 복제의 [대상 볼륨](cross-region-replication-create-peering.md#create-the-data-replication-volume-the-destination-volume) 에서 스냅숏 경로 숨기기 옵션은 기본적으로 사용 하도록 설정 되어 있으며 설정은 수정할 수 없습니다. 

1. 볼륨의 스냅숏 경로 숨기기 옵션 설정을 보려면 볼륨을 선택 합니다. **스냅숏 경로 숨기기** 필드는 옵션의 설정 여부를 표시 합니다.   
    ![스냅숏 경로 숨기기 필드를 설명 하는 스크린샷](../media/azure-netapp-files/hide-snapshot-path-field.png) 
2. 스냅숏 경로 숨기기 옵션을 편집 하려면 볼륨 페이지에서 **편집** 을 클릭 하 고 필요에 따라 **스냅숏 경로 숨기기** 옵션을 수정 합니다.   
    ![볼륨 스냅숏 편집 옵션을 설명 하는 스크린샷](../media/azure-netapp-files/volume-edit-snapshot-options.png) 

## <a name="restore-a-snapshot-to-a-new-volume"></a>새 볼륨으로 스냅샷 복원

현재 새 볼륨으로만 스냅샷을 복원할 수 있습니다. 
1. 볼륨 **블레이드에서 스냅숏을 선택 하** 여 스냅숏 목록을 표시 합니다. 
2. 복원할 스냅숏을 마우스 오른쪽 단추로 클릭 하 고 메뉴 옵션에서 **새 볼륨으로 복원을** 선택 합니다.  

    ![새 볼륨으로 스냅샷 복원](../media/azure-netapp-files/azure-netapp-files-snapshot-restore-to-new-volume.png)

3. 볼륨 만들기 창에서 새 볼륨에 대 한 정보를 제공 합니다.  
    * **Name**   
        만들고 있는 볼륨의 이름을 지정합니다.  
        
        이름은 각 리소스 그룹 내에서 고유해야 합니다. 3자 이상이어야 합니다.  영숫자 문자를 사용할 수 있습니다.

    * **할당량**  
        볼륨에 할당 하려는 논리적 저장소 크기를 지정 합니다.  

    ![새 볼륨으로 복원](../media/azure-netapp-files/snapshot-restore-new-volume.png) 

4. **검토 + 만들기** 를 클릭 합니다.  **만들기** 를 클릭합니다.   
    새 볼륨은 스냅숏에 사용 되는 것과 동일한 프로토콜을 사용 합니다.   
    스냅샷이 복원된 새 볼륨은 볼륨 블레이드에 표시됩니다.

## <a name="restore-a-file-from-a-snapshot-using-a-client"></a>클라이언트를 사용 하 여 스냅숏에서 파일 복원

[전체 스냅숏을 볼륨으로 복원](#restore-a-snapshot-to-a-new-volume)하지 않으려는 경우 볼륨이 탑재 된 클라이언트를 사용 하 여 스냅숏에서 파일을 복원 하는 옵션이 있습니다.  

탑재 된 볼륨에는  `.snapshot` (NFS 클라이언트에서) 또는 `~snapshot` 클라이언트에서 액세스할 수 있는 (SMB 클라이언트) 라는 스냅숏 디렉터리가 포함 되어 있습니다. 스냅숏 디렉터리는 볼륨의 스냅숏에 해당 하는 하위 디렉터리를 포함 합니다. 각 하위 디렉터리에는 스냅숏의 파일이 포함 되어 있습니다. 실수로 파일을 삭제 하거나 덮어쓴 경우 snapshot 하위 디렉터리에서 읽기/쓰기 디렉터리로 파일을 복사 하 여 부모 읽기/쓰기 디렉터리에 파일을 복원할 수 있습니다. 

스냅숏 디렉터리가 표시 되지 않으면 스냅숏 경로 숨기기 옵션을 현재 사용할 수 있기 때문에이 디렉터리가 숨겨져 있을 수 있습니다. [스냅숏 경로 숨기기 옵션을 편집](#edit-the-hide-snapshot-path-option) 하 여 사용 하지 않도록 설정할 수 있습니다.  

### <a name="restore-a-file-by-using-a-linux-nfs-client"></a>Linux NFS 클라이언트를 사용 하 여 파일 복원 

1. `ls`Linux 명령을 사용 하 여 디렉터리에서 복원 하려는 파일을 나열 합니다 `.snapshot` . 

    예를 들면 다음과 같습니다.

    `$ ls my.txt`   
    `ls: my.txt: No such file or directory`   

    `$ ls .snapshot`   
    `daily.2020-05-14_0013/              hourly.2020-05-15_1106/`   
    `daily.2020-05-15_0012/              hourly.2020-05-15_1206/`   
    `hourly.2020-05-15_1006/             hourly.2020-05-15_1306/`   

    `$ ls .snapshot/hourly.2020-05-15_1306/my.txt`   
    `my.txt`

2. 명령을 사용 `cp` 하 여 부모 디렉터리에 파일을 복사 합니다.  

    예를 들면 다음과 같습니다. 

    `$ cp .snapshot/hourly.2020-05-15_1306/my.txt .`   

    `$ ls my.txt`   
    `my.txt`   

### <a name="restore-a-file-by-using-a-windows-client"></a>Windows 클라이언트를 사용 하 여 파일 복원 

1. `~snapshot`볼륨의 디렉터리가 숨겨진 경우 표시할 부모 디렉터리에 [숨겨진 항목을 표시](https://support.microsoft.com/help/4028316/windows-view-hidden-files-and-folders-in-windows-10) `~snapshot` 합니다.

    ![숨겨진 항목 표시](../media/azure-netapp-files/snapshot-show-hidden.png) 

2. 에서 하위 디렉터리로 이동 `~snapshot` 하 여 복원 하려는 파일을 찾습니다.  파일을 마우스 오른쪽 단추로 클릭 합니다. **복사** 를 선택합니다.  

    ![복원할 파일 복사](../media/azure-netapp-files/snapshot-copy-file-restore.png) 

3. 부모 디렉터리로 돌아갑니다. 부모 디렉터리를 마우스 오른쪽 단추로 클릭 하 고를 선택 `Paste` 하 여 파일을 디렉터리에 붙여넣습니다.

    ![복원할 파일 붙여넣기](../media/azure-netapp-files/snapshot-paste-file-restore.png) 

4. 부모 디렉터리를 마우스 오른쪽 단추로 클릭 하 고 **속성** 을 선택한 다음 **이전 버전** 탭을 클릭 하 여 스냅숏 목록을 확인 하 고 **복원** 을 선택 하 여 파일을 복원할 수도 있습니다.  

    ![속성 이전 버전](../media/azure-netapp-files/snapshot-properties-previous-version.png) 

## <a name="revert-a-volume-using-snapshot-revert"></a>Snapshot revert를 사용 하 여 볼륨 되돌리기

스냅숏 되돌리기 기능을 사용 하면 특정 스냅숏을 만들 때의 상태로 볼륨을 신속 하 게 되돌릴 수 있습니다. 대부분의 경우 볼륨을 되돌리면 개별 파일을 스냅숏에서 활성 파일 시스템으로 복원 하는 것 보다 훨씬 빠릅니다. 또한 스냅숏을 새 볼륨으로 복원 하는 것 보다 더 효율적입니다. 

볼륨의 스냅숏 메뉴에서 볼륨 되돌리기 옵션을 찾을 수 있습니다. 변경할지에 대 한 스냅숏을 선택한 후에는 선택한 스냅숏을 만들 때 포함 된 데이터 및 타임 스탬프로 볼륨을 되돌릴 Azure NetApp Files. 

> [!IMPORTANT]
> 선택한 스냅숏이 만들어진 후에 수행 된 활성 파일 시스템 데이터 및 스냅숏은 손실 됩니다. 스냅숏 되돌리기 작업은 대상 볼륨의 *모든* 데이터를 선택한 스냅숏의 데이터로 바꿉니다. 스냅숏을 선택 하는 경우 스냅숏 콘텐츠와 만든 날짜에 주의를 기울여야 합니다. 스냅숏 되돌리기 작업을 실행 취소할 수 없습니다.

1. 볼륨의 **스냅숏** 메뉴로 이동 합니다.  되돌리기 작업에 사용할 스냅숏을 마우스 오른쪽 단추로 클릭 합니다. **볼륨 되돌리기** 를 선택 합니다. 

    ![스냅숏의 오른쪽 클릭 메뉴를 설명 하는 스크린샷](../media/azure-netapp-files/snapshot-right-click-menu.png) 

2. 볼륨을 스냅숏으로 되돌리기 창에서 볼륨의 이름을 입력 하 고 **되돌리기** 를 클릭 합니다.   

    이제 볼륨이 선택한 스냅숏의 지정 시간으로 복원 됩니다.

    ![볼륨을 스냅숏으로 되돌리기 창 스크린샷](../media/azure-netapp-files/snapshot-revert-volume.png) 

## <a name="delete-snapshots"></a>스냅샷 삭제  

더 이상 유지 하지 않아도 되는 스냅숏을 삭제할 수 있습니다. 

1. 볼륨의 **스냅숏** 메뉴로 이동 합니다. 삭제할 스냅숏을 마우스 오른쪽 단추로 클릭 합니다. **삭제** 를 선택합니다.

    ![스냅숏의 오른쪽 클릭 메뉴를 설명 하는 스크린샷](../media/azure-netapp-files/snapshot-right-click-menu.png) 

2. 스냅숏 삭제 창에서 **예** 를 클릭 하 여 스냅숏을 삭제할 것인지 확인 합니다. 

    ![스냅숏 삭제를 확인 하는 스크린샷](../media/azure-netapp-files/snapshot-confirm-delete.png)  

## <a name="next-steps"></a>다음 단계

* [스냅샷 정책 문제 해결](troubleshoot-snapshot-policies.md)
* [Azure NetApp Files에 대한 리소스 제한](azure-netapp-files-resource-limits.md)
* [Azure NetApp Files 스냅숏 101 비디오](https://www.youtube.com/watch?v=uxbTXhtXCkw&feature=youtu.be)
