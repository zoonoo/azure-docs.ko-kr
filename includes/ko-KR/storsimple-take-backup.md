<!--author=alkohli last changed: 9/17/15-->

### <a name="to-take-a-backup"></a>백업을 수행하려면
1. 장치 **빠른 시작** 페이지에서 **백업 정책 추가**를 클릭합니다. 그러면 백업 정책 추가 마법사가 시작됩니다. 
2. **백업 정책 정의** 페이지에서 다음을 수행합니다.
   
   1. 백업 정책 이름을 3~150자 사이로 입력합니다.
   2. 백업할 볼륨을 선택합니다. 볼륨을 두 개 이상 선택하면 충돌에 일관성이 있는 백업을 만들기 위해 볼륨이 그룹화됩니다.
   3. 화살표 아이콘  ![arrow-icon](./media/storsimple-take-backup/HCS_ArrowIcon-include.png)를 클릭합니다. 
      
      ![Add-backup-policy](./media/storsimple-take-backup/HCS_AddBackupPolicyWizard1M-include.png)
3. **일정 정의** 페이지에서 다음을 수행합니다.
   
   1. 드롭다운 목록에서 백업 유형을 선택합니다. 빠른 복원을 수행하려면 **로컬 스냅숏**을 선택합니다. 데이터를 복원하려면 **클라우드 스냅숏**을 선택합니다.
   2. 백업 빈도를 분, 시간, 일 또는 주 단위로 지정합니다.
   3. 보존 시간을 선택합니다. 보존 선택 항목은 백업 빈도에 따라 달라집니다. 예를 들어 일별 정책의 경우 보존을 주 단위로 지정하고 월별 정책의 경우 월 단위로 지정할 수 있습니다.
   4. 백업 정책의 시작 시간과 날짜를 선택합니다.
   5. **사용** 확인란을 선택하여 백업 정책을 사용하도록 설정합니다. 
   6. 확인 아이콘  ![check-icon](./media/storsimple-take-backup/HCS_CheckIcon-include.png) 을 클릭하여 정책을 저장합니다.
      
      ![Add-backup-policy](./media/storsimple-take-backup/HCS_AddBackupPolicyWizard2M-include.png)
      
      이제 볼륨 데이터의 예약된 백업을 만드는 백업 정책이 생성되었습니다.

장치 구성을 완료했습니다. 

![동영상 사용 가능](./media/storsimple-take-backup/Video_icon.png) **동영상 사용 가능**

StorSimple 백업을 수행하는 방법을 보여 주는 동영상을 시청하려면 [여기](https://azure.microsoft.com/documentation/videos/take-a-storsimple-backup/)를 클릭하세요.

