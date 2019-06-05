---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 4fc92931979aa367bdead435c3d6fd758d66a397
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66118284"
---
#### <a name="to-create-a-manual-backup"></a>수동 백업을 만들려면

1. StorSimple Device Manager 서비스로 이동한 다음, **디바이스**를 클릭합니다. 테이블 형식 디바이스 목록에서 디바이스를 선택합니다. **설정 >관리 > Backup 정책**으로 이동합니다.

2. **Backup 정책** 블레이드에는 백업하려는 볼륨에 대한 정책을 비롯한 테이블 형식의 모든 백업 정책이 나열됩니다. 백업하려는 볼륨과 연결된 정책을 선택하고 마우스 오른쪽 단추를 클릭하여 상황에 맞는 메뉴를 호출합니다. 드롭다운 목록에서 **지금 백업**을 선택합니다.

    ![수동 백업 만들기](./media/storsimple-8000-create-manual-backup/createmanualbu1.png)

3. **지금 백업** 블레이드에서 다음 단계를 수행합니다.

    1. 드롭다운 목록에서 적절한 **스냅샷 유형**을 선택합니다. **로컬** 스냅숏 또는 **클라우드** 스냅숏을 선택합니다. 빠른 백업 또는 복원을 위해서는 로컬 스냅샷을, 데이터 복원력을 위해서는 클라우드 스냅샷을 선택합니다.

        ![수동 백업 만들기](./media/storsimple-8000-create-manual-backup/createmanualbu2.png)

    2. **확인**을 클릭하면 스냅숏을 만들기 위한 작업이 시작됩니다. 작업이 성공적으로 만들어지면 페이지 맨 아래에 알림이 표시됩니다.

        ![수동 백업 만들기](./media/storsimple-8000-create-manual-backup/createmanualbu4.png)

    3. 작업을 모니터링하려면 알림을 클릭합니다. 작업 진행 상태를 볼 수 있는 **작업** 블레이드로 이동합니다.


5. 백업 작업이 완료되면 **Backup 카탈로그** 탭으로 이동합니다.

6. 적절한 디바이스, 백업 정책 및 시간 범위로 필터 선택을 설정합니다. 카탈로그에 표시되는 백업 세트의 목록에 백업이 나타납니다.

