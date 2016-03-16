#### 가상 장치를 중지하고 시작하려면
가상 장치를 중지하려면 해당 이름을 클릭하고 **종료**를 클릭합니다. 가상 장치를 종료하는 동안 상태는 **중지**입니다. 가상 장치가 중지된 후 상태는 **중지됨**입니다.

다음 cmdlet을 사용하여 가상 장치를 중지하고 시작합니다.

`Stop-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`


`Start-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`
    
#### 가상 장치를 다시 시작하려면

가상 장치를 실행하고 다시 시작하려면 해당 이름을 클릭하고 **다시 시작**을 클릭합니다. 가상 장치를 다시 시작하는 동안 상태는 **다시 시작 중**입니다. 가상 장치가 사용할 준비가 되면 상태는 **실행 중**입니다.

가상 장치를 다시 시작하려면 다음 cmdlet을 사용합니다.

`Restart-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

<!---HONumber=AcomDC_1217_2015-->