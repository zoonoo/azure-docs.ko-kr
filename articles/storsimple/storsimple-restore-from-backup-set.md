---
title: 백업에서 StorSimple 볼륨 복원 | Microsoft Docs
description: StorSimple 관리자 서비스 백업 카탈로그 페이지를 사용하여 백업 세트에서 StorSimple 볼륨을 복원하는 방법을 설명합니다.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''

ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 08/17/2016
ms.author: alkohli

---
# 백업 세트에서 StorSimple 볼륨 복원
[!INCLUDE [storsimple-version-selector-restore-from-backup](../../includes/storsimple-version-selector-restore-from-backup.md)]

## 개요
**백업 카탈로그** 페이지는 수동 또는 자동화된 백업을 수행할 때 생성되는 모든 백업 세트를 표시합니다. 이 페이지를 사용하여 백업 정책 또는 볼륨에 대한 모든 백업을 나열하거나, 백업을 선택 또는 삭제하거나 백업을 사용하여 볼륨을 복원 또는 복제할 수 있습니다.

 ![백업 카탈로그 페이지](./media/storsimple-restore-from-backup-set/HCS_BackupCatalog.png)

이 자습서에서는 **백업 카탈로그** 페이지를 사용하여 백업 세트에서 장치의 볼륨을 복원하는 방법을 설명합니다.

## 백업 카탈로그를 사용하는 방법
**백업 카탈로그** 페이지는 백업 세트 선택 범위를 좁히는 데 도움이 되는 쿼리를 제공합니다. 다음 매개 변수를 기반으로 검색되는 백업 세트를 필터링할 수 있습니다.

* **장치** – 백업 세트를 만든 장치입니다.
* **백업 정책** 또는 **볼륨** – 백업 정책 또는 이 백업 세트와 연결된 볼륨입니다.
* **에서** 및 **까지** – 백업 세트를 만든 날짜 및 시간 범위입니다.

그런 다음 필터링된 백업 세트는 다음 특성을 기반으로 표로 정리됩니다.

* **이름** – 백업 정책 또는 이 백업 세트와 연결된 볼륨입니다.
* **크기** – 백업 세트의 실제 크기입니다.
* **만든 날짜** – 백업이 만들어진 날짜 및 시간입니다.
* **유형** – 백업 세트는 로컬 스냅숏 또는 클라우드 스냅숏일 수입니다. 로컬 스냅숏은 장치에 로컬로 저장된 모든 볼륨 데이터의 백업인 반면 클라우드 스냅숏은 클라우드에 상주하는 볼륨 데이터의 백업을 참조합니다. 로컬 스냅숏은 데이터 복구 기능으로 클라우드 스냅숏을 선택하는 반면 빠른 액세스를 제공합니다.
* **시작 기준** – 일정에 따라 자동으로 또는 사용자가 수동으로 백업을 시작할 수 있습니다. (백업을 예약하기 위해 백업 정책을 사용할 수 있습니다. 또는, **백업 수행** 옵션을 사용하여 대화형 백업을 수행할 수 있습니다.)

## 백업에서 StorSimple 볼륨을 복원하는 방법
**백업 카탈로그** 페이지를 사용하여 특정 백업에서 StorSimple 볼륨을 복원할 수 있습니다.

> [!WARNING]
> 백업에서 복원되면 백업에서 기존 볼륨을 대체합니다. 백업이 수행된 후 작성된 모든 데이터가 손실될 수 있습니다.
> 
> 

볼륨의 복원을 시작하기 전에 해당 볼륨이 오프라인 상태인지 확인합니다. 먼저 호스트에서도 볼륨을 오프라인으로 전환한 다음 장치를 오프라인으로 전환해야 합니다. [볼륨을 오프라인으로 전환](storsimple-manage-volumes.md#take-a-volume-offline) 단계를 따릅니다. 다음 단계를 수행하여 백업 세트에서 볼륨을 복원합니다.

### 백업 세트에서 복원하려면
1. StorSimple 관리자 서비스 페이지에서 **백업 카탈로그** 탭을 클릭합니다.
   
    ![백업 카탈로그](./media/storsimple-restore-from-backup-set/HCS_Restore.png)
2. 백업 세트를 다음과 같이 선택합니다.
   
   1. 해당 장치를 선택합니다.
   2. 드롭다운 목록에서 선택하려는 백업에 대 한 볼륨 또는 백업 정책을 선택합니다.
   3. 시간 범위를 지정합니다.
   4. 확인 아이콘![확인 아이콘](./media/storsimple-restore-from-backup-set/HCS_CheckIcon.png)을 클릭하여 이 쿼리를 실행 합니다.
      
      선택한 볼륨와 연결된 백업을 또는 백업 정책이 백업 세트의 목록에 나타나야 합니다.
3. 백업 세트를 확장하여 연결된 볼륨을 봅니다. 이 볼륨은 복원하려면 호스트와 장치에서 오프라인 상태여야 합니다. [볼륨을 오프라인으로 전환](storsimple-manage-volumes.md#take-a-volume-offline) 단계를 따릅니다.
   
   > [!IMPORTANT]
   > 해당 볼륨을 오프 라인으로 전환하기 전에, 먼저 호스트에서 해당 볼륨을 오프라인으로 전환했는지 확인합니다. 호스트에서 볼륨을 오프라인으로 전환하지 않은 경우 잠재적으로 데이터가 손상될 수입니다.
   > 
   > 
4. 백업 세트를 선택합니다. 페이지의 맨 아래에서 **복원**을 클릭합니다.
5. 확인하라는 메시지가 표시됩니다.
   
    ![확인 페이지](./media/storsimple-restore-from-backup-set/HCS_ConfirmRestore.png)
6. 복원 정보를 검토하고 확인 아이콘![확인 아이콘](./media/storsimple-restore-from-backup-set/HCS_CheckIcon.png)을 클릭합니다. 그러면 **작업** 페이지에 액세스하여 볼 수 있는 복원 작업이 시작됩니다.
7. 복원이 완료되면 볼륨의 내용을 백업의 볼륨으로 바뀌는 것을 확인할 수 있습니다.

![동영상 사용 가능](./media/storsimple-restore-from-backup-set/Video_icon.png) **동영상 사용 가능**

StorSimple에서 복제 및 복원 기능을 사용하여 삭제된 파일을 복구하는 방법을 보여 주는 동영상을 시청하려면 [여기](https://azure.microsoft.com/documentation/videos/storsimple-recover-deleted-files-with-storsimple/)를 클릭하세요.

## 다음 단계
* [StorSimple 볼륨을 관리](storsimple-manage-volumes.md)하는 방법을 알아봅니다.
* [StorSimple Manager 서비스를 사용하여 StorSimple 장치를 관리](storsimple-manager-service-administration.md)하는 방법을 알아봅니다.

<!---HONumber=AcomDC_0824_2016-->