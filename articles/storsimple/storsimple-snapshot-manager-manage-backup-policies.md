---
title: StorSimple Snapshot Manager 백업 정책 | Microsoft Docs
description: StorSimple 스냅샷 관리자 MMC 스냅인을 사용하여 예약된 백업을 제어하는 백업 정책을 만들고 관리하는 방법을 설명합니다.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 04415d0b-42f0-4737-8afa-257fb2dbe5d0
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: 745aeb9844f1abad075d34b34735563200ea618b
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2020
ms.locfileid: "90054945"
---
# <a name="use-storsimple-snapshot-manager-to-create-and-manage-backup-policies"></a>StorSimple 스냅샷 관리자를 사용하여 백업 정책 만들기 및 관리
## <a name="overview"></a>개요
백업 정책은 로컬로 또는 클라우드에 볼륨 데이터를 백업하기 위한 일정을 만듭니다. 백업 정책을 만들 때 보존 정책을 지정할 수도 있습니다. 최대 64 개의 스냅숏을 유지할 수 있습니다. 백업 정책에 대 한 자세한 내용은 StorSimple 8000 시리즈의 [백업 유형](storsimple-what-is-snapshot-manager.md#backup-types-and-backup-policies) [: 하이브리드 클라우드 솔루션](storsimple-overview.md)을 참조 하세요.

이 자습서에서는 다음을 수행하는 방법을 설명합니다.

* 백업 정책 만들기
* 백업 정책 편집
* 백업 정책 삭제

## <a name="create-a-backup-policy"></a>백업 정책 만들기
다음 절차에 따라 새 백업 정책을 만듭니다.

#### <a name="to-create-a-backup-policy"></a>백업 정책을 만들려면
1. 바탕 화면 아이콘을 클릭하여 StorSimple 스냅샷 관리자를 시작합니다.
2. **범위** 창에서 **백업 정책**을 마우스 오른쪽 단추로 클릭하고 **백업 정책 만들기**를 클릭합니다.

    ![백업 정책 만들기](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_BU_policy.png)

    **정책 만들기** 대화 상자가 나타납니다.

    ![정책 만들기 - 일반 탭](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_policy_general.png)
3. **일반** 탭에서 다음 정보를 완료합니다.

   1. **이름** 텍스트 상자에 정책의 이름을 입력합니다.
   2. **볼륨 그룹** 텍스트 상자에 정책과 연관된 볼륨 그룹 이름을 입력합니다.
   3. **로컬 스냅숏**이나 **클라우드 스냅숏**을 선택합니다.
   4. 보존할 스냅샷 수를 선택합니다. **모두**를 선택하면 64개의 스냅숏이 보유됩니다(최대).
4. **일정** 탭을 클릭합니다.

    ![정책 만들기 - 일정 탭](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_policy_schedule.png)
5. **일정** 탭에서 다음 정보를 완료합니다.

   1. **사용** 확인란을 클릭해 다음 백업 일정을 설정합니다.
   2. **설정** 아래에서 **한 번**, **일간**, **주간** 또는 **월간**을 선택합니다.
   3. **시작** 텍스트 상자에 달력 아이콘을 클릭하고 시작 날짜를 선택합니다.
   4. **고급 설정** 아래에서 선택 사항으로 반복 일정과 종료 날짜를 설정할 수 있습니다.
   5. **확인**을 클릭합니다.

백업 정책을 만들면 다음 정보가 **결과** 창에 나타납니다.

* **이름** - 백업 정책 이름입니다.
* **유형** - 로컬 스냅숏 또는 클라우드 스냅숏입니다.
* **볼륨 그룹** - 정책과 연결된 볼륨 그룹입니다.
* **보유** - 보유 스냅숏 개수이며 최대값은 64입니다.
* **만든 날짜** - 이 정책을 만든 날짜입니다.
* **사용** - 정책이 현재 적용되는지 여부. **True**는 적용되고 있음을 나타냅니다. **False**는 적용되지 않음을 나타냅니다.

## <a name="edit-a-backup-policy"></a>백업 정책 편집
다음 절차에 따라 기존 백업 정책을 편집합니다.

#### <a name="to-edit-a-backup-policy"></a>백업 정책을 편집하려면
1. 바탕 화면 아이콘을 클릭하여 StorSimple 스냅샷 관리자를 시작합니다.
2. **범위** 창에서 **백업 정책** 노드를 클릭합니다. 모든 백업 정책이 **결과** 창에 나타납니다.
3. 편집할 정책을 마우스 오른쪽 단추로 클릭하고 **편집**을 클릭합니다.

    ![백업 정책 편집](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Edit_BU_policy.png)
4. **정책 만들기** 창이 나타나면 변경 내용을 입력하고 **확인**을 클릭합니다.

## <a name="delete-a-backup-policy"></a>백업 정책 삭제
다음 절차에 따라 백업 정책을 삭제합니다.

#### <a name="to-delete-a-backup-policy"></a>백업 정책을 삭제하려면
1. 바탕 화면 아이콘을 클릭하여 StorSimple 스냅샷 관리자를 시작합니다.
2. **범위** 창에서 **백업 정책** 노드를 클릭합니다. 모든 백업 정책이 **결과** 창에 나타납니다.
3. 삭제할 백업 정책을 마우스 오른쪽 단추로 클릭하고 **삭제**를 클릭합니다.
4. 확인 메시지가 나타나면 **예**를 클릭합니다.

    ![백업 정책 삭제 확인](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Delete_BU_policy.png)

## <a name="next-steps"></a>다음 단계
* [StorSimple 스냅샷 관리자를 사용하여 StorSimple 솔루션을 관리](storsimple-snapshot-manager-admin.md)하는 방법을 알아봅니다.
* [StorSimple 스냅샷 관리자를 사용하여 백업 작업을 보고 관리](storsimple-snapshot-manager-manage-backup-jobs.md)하는 방법을 알아봅니다.
