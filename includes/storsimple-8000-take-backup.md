---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 48a3326dbe0e9eed4a5490e720248555586d189c
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66118209"
---
### <a name="to-take-a-backup"></a>백업을 수행하려면

1. StorSimple 디바이스 관리자 서비스로 이동합니다. 테이블 형식 디바이스 목록에서 디바이스를 선택하고 클릭한 후 **모든 설정**을 클릭합니다. **설정** 블레이드에서 **설정 > 관리 > Backup 정책**으로 이동합니다.

    ![Add-backup-policy](./media/storsimple-8000-take-backup/step8takebu1.png)

2. **Backup 정책** 블레이드에서 **+ 정책 추가**를 클릭합니다.

    ![Add-backup-policy](./media/storsimple-8000-take-backup/step8takebu2.png)

3. **백업 정책 만들기** 블레이드에서 백업 정책 이름을 3~150자 사이로 입력합니다.

4. 백업할 볼륨을 선택합니다. 볼륨을 두 개 이상 선택하면 충돌에 일관성이 있는 백업을 만들기 위해 볼륨이 그룹화됩니다.

    ![Add-backup-policy](./media/storsimple-8000-take-backup/step8takebu4.png)

5. **첫 번째 일정 추가** 블레이드에서:

    1. 백업 유형을 선택합니다. 빠른 복원을 수행하려면 **로컬** 스냅샷을 선택합니다. 데이터를 복원하려면 **클라우드** 스냅샷을 선택합니다.
    2. 백업 빈도를 분, 시간, 일 또는 주 단위로 지정합니다.
    3. 보존 시간을 선택합니다. 보존 선택 항목은 백업 빈도에 따라 달라집니다. 예를 들어 일별 정책의 경우 보존을 주 단위로 지정하고 월별 정책의 경우 월 단위로 지정할 수 있습니다.
    4. 백업 정책의 시작 시간과 날짜를 선택합니다.
    5. **확인**을 클릭하여 백업 정책을 만듭니다.

        ![Add-backup-policy](./media/storsimple-8000-take-backup/step8takebu5.png) 

6. **만들기**를 클릭하면 백업 정책 만들기가 시작됩니다. 백업 정책이 성공적으로 만들어지면 알림이 표시됩니다. 백업 정책 목록도 업데이트 됩니다.
      
      ![Add-backup-policy](./media/storsimple-8000-take-backup/step8takebu9.png)
      
      이제 볼륨 데이터의 예약된 백업을 만드는 백업 정책이 생성되었습니다.




