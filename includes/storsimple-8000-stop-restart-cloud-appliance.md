#### <a name="to-stop-and-start-a-cloud-appliance"></a>클라우드 어플라이언스를 중지 및 시작하려면

1. 클라우드 어플라이언스를 중지하려면 클라우드 어플라이언스에 대한 VM으로 이동합니다.
    ![StorSimple Cloud Appliance 가상 컴퓨터](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart1.png)

2. 명령 모음에서 **중지**를 클릭합니다.

    ![StorSimple Cloud Appliance 가상 컴퓨터](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart2.png)

3. 확인하라는 메시지가 표시되면 **예**를 클릭합니다.

    ![StorSimple Cloud Appliance 가상 컴퓨터](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart3.png)

4. VM을 중지하면 할당 취소됩니다. 클라우드 어플라이언스를 중지하는 동안에는 상태가 **할당 취소 중**입니다. 클라우드 어플라이언스를 중지한 후에는 상태가 **중지됨(할당 취소됨)** 입니다.

    ![StorSimple Cloud Appliance 가상 컴퓨터](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart4.png)

5. VM이 중지되면 **시작**(단추를 사용할 수 있게 됨)을 클릭하여 VM을 시작합니다. 클라우드 어플라이언스가 시작되면 상태는 **시작됨**입니다.

    ![StorSimple Cloud Appliance 가상 컴퓨터](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart5.png)

다음 cmdlet을 사용하여 클라우드 어플라이언스를 중지했다가 시작합니다.

`Stop-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

`Start-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

#### <a name="to-restart-a-cloud-appliance"></a>클라우드 어플라이언스를 다시 시작하려면

클라우드 어플라이언스를 다시 시작하려면 클라우드 어플라이언스에 대한 VM으로 이동합니다. 명령 모음에서 **다시 시작**을 클릭합니다. 메시지가 표시되면 다시 시작을 확인합니다. 클라우드 어플라이언스를 사용할 준비가 되면 상태는 **실행 중**입니다.

![StorSimple Cloud Appliance 가상 컴퓨터](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart6.png)

다음 cmdlet을 사용하여 클라우드 어플라이언스를 다시 시작합니다.

`Restart-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

