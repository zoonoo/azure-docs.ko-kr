<!--author=SharS last changed: 12/01/15-->

### <a name="step-1-authorize-a-device-to-change-the-service-data-encryption-key-in-the-azure-classic-portal"></a>1단계: Azure 클래식 포털에서 장치에 서비스 데이터 암호화 키를 변경할 권한을 부여합니다.
일반적으로 장치 관리자는 서비스 관리자가 장치에 서비스 데이터 암호화 키를 변경할 수 있는 권한을 부여하도록 요청합니다. 그러면 서비스 관리자는 장치에 키를 변경할 수 있는 권한을 부여합니다.

이 단계는 Azure 클래식 포털에서 수행됩니다. 서비스 관리자는 권한을 부여할 수 있는 표시된 장치 목록에서 장치를 선택할 수 있습니다. 그러면 장치에 서비스 데이터 암호화 키 변경 프로세스를 시작할 수 있는 권한이 부여됩니다.

#### <a name="which-devices-can-be-authorized-to-change-service-data-encryption-keys"></a>서비스 데이터 암호화 키를 변경할 권한이 부여될 수 있는 장치
서비스 데이터 암호화 키 변경을 시작할 권한을 부여받으려면 장치가 다음 조건을 충족해야 합니다.

* 장치는 온라인 상태여야 서비스 데이터 암호화 키 변경 권한 부여 대상이 됩니다.
* 키 변경이 시작되지 않은 경우 30분 후에 동일한 장치에 권한을 다시 부여할 수 있습니다.
* 이전에 권한을 부여받은 장치에서 키 변경을 시작하지 않은 경우 다른 장치에 권한을 부여할 수 있습니다. 새 장치에 권한을 부여하면 이전 장치는 변경을 시작할 수 없습니다.
* 서비스 데이터 암호화 키 롤오버가 진행 중인 동안에는 장치에 권한을 부여할 수 없습니다.
* 서비스에 등록된 장치 중 일부만 롤오버한 경우에는 장치에 권한을 부여할 수 있습니다. 이 경우는 적합한 장치는 서비스 데이터 암호화 키 변경을 완료한 장치입니다.

> [!NOTE]
> StorSimple 가상 장치는 Azure 클래식 포털에서 키 변경을 시작할 권한을 부여받을 수 있는 장치 목록에 표시되지 않습니다.
> 
> 

다음 단계에 따라 장치를 선택하고 서비스 데이터 암호화 키 변경을 시작할 권한을 부여합니다.

#### <a name="to-authorize-a-device-to-change-the-key"></a>장치에 키 변경 권한을 부여하려면
1. 서비스 대시보드 페이지에서 **서비스 데이터 암호화 키 변경**을 클릭합니다.
   
    ![서비스 암호화 키 변경](./media/storsimple-change-data-encryption-key/HCS_ChangeServiceDataEncryptionKey-include.png)
2. **서비스 데이터 암호화 키 변경** 대화 상자에서 장치를 선택하고 서비스 데이터 암호화 키 변경을 시작할 권한을 부여합니다. 드롭다운 목록에 권한을 부여할 수 있는 모든 장치가 나와 있습니다.
3. 확인 아이콘 ![확인 아이콘](./media/storsimple-change-data-encryption-key/HCS_CheckIcon-include.png)을 클릭합니다.

### <a name="step-2-use-windows-powershell-for-storsimple-to-initiate-the-service-data-encryption-key-change"></a>2단계: StorSimple용 Windows PowerShell을 사용하여 서비스 데이터 암호화 키 변경 시작
이 단계는 권한이 부여된 StorSimple 장치의 StorSimple용 Windows PowerShell 인터페이스에서 수행됩니다.

> [!NOTE]
> 키 롤오버가 완료될 때까지 StorSimple 관리자 서비스의 Azure 클래식 포털에서 수행할 수 있는 작업은 없습니다.
> 
> 

장치 직렬 콘솔을 사용하여 Windows PowerShell 인터페이스에 연결하는 경우 다음 단계를 수행합니다.

#### <a name="to-initiate-the-service-data-encryption-key-change"></a>서비스 데이터 암호화 키 변경을 시작하려면
1. 옵션 1을 선택하여 모든 권한으로 로그온합니다.
2. 명령 프롬프트에 다음을 입력합니다.
   
     `Invoke-HcsmServiceDataEncryptionKeyChange`
3. cmdlet이 성공적으로 완료되면 새 서비스 데이터 암호화 키를 얻을 수 있습니다. 이 키를 복사하고 저장해 두었다가 이 프로세스의 3단계에서 사용합니다. 이 키는 StorSimple 관리자 서비스에 등록된 나머지 모든 장치를 업데이트하는 데 사용됩니다.
   
   > [!NOTE]
   > 이 프로세스는 StorSimple 장치에 권한을 부여한 후 4시간 이내에 시작되어야 합니다.
   > 
   > 
   
   이 새 키는 서비스로 전송되어 서비스에 등록된 모든 장치에 푸시됩니다. 서비스 대시보드에 경고가 표시됩니다. 서비스는 등록된 장치에서 모든 작업을 사용할 수 없도록 설정합니다. 그러면 장치 관리자는 다른 장치에서 서비스 데이터 암호화 키를 업데이트해야 합니다. 그러나 I/O(클라우드로 데이터를 보내는 호스트)는 중단되지 않습니다.
   
   단일 장치를 서비스에 등록한 경우 이제 롤오버 프로세스가 완료되었으므로 다음 단계를 건너뛸 수 있습니다. 여러 장치를 서비스에 등록한 경우 3단계를 진행합니다.

### <a name="step-3-update-the-service-data-encryption-key-on-other-storsimple-devices"></a>3단계: 다른 StorSimple 장치에서 서비스 데이터 암호화 키 업데이트
여러 장치를 StorSimple 관리자 서비스에 등록한 경우 StorSimple 장치의 Windows PowerShell 인터페이스에서 다음 단계를 수행해야 합니다. 2단계: StorSimple용 Windows PowerShell을 사용하여 서비스 데이터 암호화 키 변경 시작에서 얻은 키를 사용하여 StorSimple 관리자 장치에 등록된 나머지 모든 StorSimple 장치를 업데이트해야 합니다.

다음 단계를 수행하여 장치에서 서비스 데이터 암호화를 업데이트합니다.

#### <a name="to-update-the-service-data-encryption-key"></a>서비스 데이터 암호화 키를 업데이트하려면
1. StorSimple용 Windows PowerShell을 사용하여 콘솔에 연결합니다. 옵션 1을 선택하여 모든 권한으로 로그온합니다.
2. 명령 프롬프트에 다음을 입력합니다.
   
    `Invoke-HcsmServiceDataEncryptionKeyChange – ServiceDataEncryptionKey`
3. [2단계: StorSimple용 Windows PowerShell을 사용하여 서비스 데이터 암호화 키 변경 시작](#to-initiate-the-service-data-encryption-key-change)에서 얻은 서비스 데이터 암호화 키를 제공합니다.

