---
title: "StorSimple 백업 카탈로그 관리 | Microsoft Docs"
description: "StorSimple 관리자 서비스 백업 카탈로그 페이지를 사용하여 볼륨에 대한 백업 세트를 나열, 선택 및 삭제하는 방법을 설명합니다."
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: 
ms.assetid: ad81bee9-fe43-40b3-a384-b15fb274ecd9
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 04/28/2016
ms.author: v-sharos
ms.openlocfilehash: 5ee9855e1428c7a2d871d9c215d302c5c3b7101a
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2017
---
# <a name="use-the-storsimple-manager-service-to-manage-your-backup-catalog"></a>StorSimple 관리자 서비스를 사용하여 백업 카탈로그 관리
## <a name="overview"></a>개요
StorSimple 관리자 서비스 **백업 카탈로그** 페이지는 수동 또는 예약된 백업을 수행할 때 생성되는 모든 백업 세트를 표시합니다. 이 페이지를 사용하여 백업 정책 또는 볼륨에 대한 모든 백업을 나열하거나, 백업을 선택 또는 삭제하거나 백업을 사용하여 볼륨을 복원 또는 복제할 수 있습니다.

이 자습서에서는 백업 세트를 표시하고 선택하고 삭제하는 방법을 설명합니다. 백업에서 장치를 복원하는 방법을 알아보려면 [백업 세트에서 장치 복원](storsimple-restore-from-backup-set.md)으로 이동합니다. 볼륨 복제 방법을 알아보려면 [StorSimple 볼륨 복제](storsimple-clone-volume.md)로 이동합니다.

![백업 카탈로그](./media/storsimple-manage-backup-catalog/backupcatalog.png) 

**백업 카탈로그** 페이지는 백업 세트 선택 범위를 좁힐 수 있는 쿼리를 제공합니다. 다음 매개 변수를 기반으로 검색되는 백업 세트를 필터링 할 수 있습니다.

* **장치** – 백업 세트를 만든 장치입니다.
* **백업 정책 또는 볼륨** – 백업 정책 또는 이 백업 세트와 연결된 볼륨입니다.
* **시작 및 종료** – 백업 세트를 만든 날짜 및 시간 범위입니다.

그런 다음 필터링된 백업 세트는 다음 특성을 기반으로 표로 정리됩니다.

* **이름** – 백업 정책 또는 이 백업 세트와 연결된 볼륨입니다.
* **크기** – 백업 세트의 실제 크기입니다.
* **만든 날짜** – 백업이 만들어진 날짜 및 시간입니다. 
* **유형** – 백업 세트는 로컬 스냅숏 또는 클라우드 스냅숏일 수입니다. 로컬 스냅숏은 장치에 로컬로 저장된 모든 볼륨 데이터의 백업인 반면 클라우드 스냅숏은 클라우드에 상주하는 볼륨 데이터의 백업을 참조합니다. 로컬 스냅숏은 데이터 복구 기능으로 클라우드 스냅숏을 선택하는 반면 빠른 액세스를 제공합니다.
* **시작 기준** – 일정에 따라 자동으로 또는 사용자가 수동으로 백업을 시작할 수 있습니다. 백업을 예약하기 위해 백업 정책을 사용할 수 있습니다. 또는 **백업 수행** 옵션을 사용하여 수동 백업을 수행할 수 있습니다.

## <a name="list-backup-sets-for-a-volume"></a>볼륨에 대한 목록 백업 세트
볼륨에 대한 모든 백업을 나열하려면 다음 단계를 완료 합니다.

#### <a name="to-list-backup-sets"></a>백업 세트를 나열하려면
1. StorSimple 관리자 서비스 페이지에서 **백업 카탈로그** 탭을 클릭합니다.
2. 다음과 같이 선택 항목을 필터링합니다.
   
   1. 해당 장치를 선택합니다.
   2. 드롭다운 목록에서 해당 볼륨을 선택하여 백업을 봅니다.
   3. 시간 범위를 지정합니다.
   4. 확인 아이콘 ![확인 아이콘](./media/storsimple-manage-backup-catalog/HCS_CheckIcon.png) 을 클릭하여 이 쿼리를 실행 합니다.
      
      선택한 볼륨과 연결된 백업이 백업 세트의 목록에 나타나야 합니다.

## <a name="select-a-backup-set"></a>백업 세트를 선택합니다.
볼륨 또는 백업 정책에 백업 세트를 선택 하려면 다음 단계를 완료 합니다.

#### <a name="to-select-a-backup-set"></a>백업 세트를 선택하려면
1. StorSimple 관리자 서비스 페이지에서 **백업 카탈로그** 탭을 클릭합니다.
2. 다음과 같이 선택 항목을 필터링합니다.
   
   1. 해당 장치를 선택합니다.
   2. 드롭다운 목록에서 선택하려는 백업에 대 한 볼륨 또는 백업 정책을 선택합니다.
   3. 시간 범위를 지정합니다.
   4. 확인 아이콘 ![확인 아이콘](./media/storsimple-manage-backup-catalog/HCS_CheckIcon.png) 을 클릭하여 이 쿼리를 실행 합니다.
      
      선택한 볼륨와 연결된 백업을 또는 백업 정책이 백업 세트의 목록에 나타나야 합니다.
3. 백업 세트를 선택하고 확장합니다. **복원** 및 **삭제** 옵션이 페이지의 맨 아래에 표시됩니다. 선택한 백업 세트에 이러한 동작 중 하나를 수행할 수 있습니다.

## <a name="delete-a-backup-set"></a>백업 세트 삭제
백업에 연결된 데이터를 더이상 보존하고 싶지 않은 경우 백업을 삭제 합니다. 백업 세트를 삭제하려면 다음 단계를 수행합니다.

#### <a name="to-delete-a-backup-set"></a>백업 세트를 삭제 하려면
1. StorSimple 관리자 서비스 페이지에서 **백업 카탈로그**탭을 클릭합니다.
2. 다음과 같이 선택 항목을 필터링합니다.
   
   1. 해당 장치를 선택합니다.
   2. 드롭다운 목록에서 선택하려는 백업에 대 한 볼륨 또는 백업 정책을 선택합니다.
   3. 시간 범위를 지정합니다.
   4. 확인 아이콘 ![확인 아이콘](./media/storsimple-manage-backup-catalog/HCS_CheckIcon.png) 을 클릭하여 이 쿼리를 실행 합니다.
      
      선택한 볼륨와 연결된 백업을 또는 백업 정책이 백업 세트의 목록에 나타나야 합니다.
3. 백업 세트를 선택하고 확장합니다. **복원** 및 **삭제** 옵션이 페이지의 맨 아래에 표시됩니다. **삭제**를 클릭합니다.
4. 삭제가 진행 중일 때와 삭제가 완료되었을 때 알림이 표시됩니다. 삭제를 완료 한 후 이 페이지의 쿼리를 새로 고칩니다. 삭제 된 백업 세트는 백업 세트의 목록에 더이상 나타나지 않습니다.

## <a name="next-steps"></a>다음 단계
* 백업 카탈로그를 사용하여 [백업 세트에서 장치를 복원](storsimple-restore-from-backup-set.md)하는 방법을 알아봅니다.
* [StorSimple Manager 서비스를 사용하여 StorSimple 장치를 관리](storsimple-manager-service-administration.md)하는 방법을 알아봅니다.

