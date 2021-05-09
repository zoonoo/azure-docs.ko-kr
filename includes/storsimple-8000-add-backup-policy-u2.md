---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 02/09/2021
ms.author: alkohli
ms.openlocfilehash: 23ce17844a0113f63931c6ece7d36bfefedc2de5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100551302"
---
#### <a name="to-add-a-storsimple-backup-policy"></a>StorSimple 백업 정책을 추가하려면

1. StorSimple 디바이스로 이동하고 **백업 정책** 을 클릭합니다.

2. **Backup 정책** 블레이드의 명령 모음에서 **+ 정책 추가** 를 클릭합니다.
   
    ![백업 정책 추가](./media/storsimple-8000-add-backup-policy-u2/add-backup-policy-01.png)

3. **백업 정책 만들기** 블레이드에서 다음 단계를 수행합니다.
   
   1. 선택한 디바이스에 따라 **디바이스 선택** 이 자동으로 채워집니다.
   
   2. 3~150자의 백업 **정책 이름** 을 지정합니다. 정책을 만든 후에는 정책 이름을 바꿀 수 없습니다.
       
   3. 이 백업 정책에 볼륨을 할당하려면 선택 **볼륨 추가** 를 선택한 후 테이블 형식 볼륨 목록에서 해당 확인란을 클릭하여 이 백업 정책에 하나 이상의 볼륨을 할당합니다.

       ![백업 정책 2 추가](./media/storsimple-8000-add-backup-policy-u2/add-backup-policy-02.png)<!--Replacement screen source: create-backup-policy-addvolumes.png-->

   4. 이 백업 정책에 대한 일정을 정의하려면 **첫 번째 일정** 을 클릭한 후 다음 매개 변수를 수정합니다.<!--Do the substeps remain the same? Can they follow without a screenshot?-->

       ![백업 정책 3 추가](./media/storsimple-8000-add-backup-policy-u2/add-backup-policy-03.png)<!--Replacement screen source: create-backup-policy-first-schedule.png-->

       1. **스냅샷 유형** 으로 **클라우드** 또는 **로컬** 을 선택합니다.

       2. 백업 빈도를 표시합니다. 숫자를 지정한 다음 드롭다운 목록에서 **일** 또는 **주** 를 선택합니다.

       3. 보존 일정을 입력합니다.

       4. 백업 정책의 시작 시간과 날짜를 입력합니다.

       5. 일정을 정의하려면 **확인** 을 클릭합니다.

   5. 백업 정책을 만들려면 **만들기** 를 클릭합니다.
   
   6. 백업 정책이 만들어지면 알림이 표시됩니다. 새로 추가된 정책은 **Backup 정책** 블레이드에서 테이블 형식 뷰로 표시됩니다.

       ![백업 정책 5 추가](./media/storsimple-8000-add-backup-policy-u2/add-backup-policy-07.png)
