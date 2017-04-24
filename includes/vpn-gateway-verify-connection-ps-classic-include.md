**Get-AzureVNetConnection**을 사용하여 클래식 가상 네트워크 게이트웨이의 연결을 확인할 수 있습니다. 

1. 일치하는 값을 구성하는 데 다음 cmdlet 예제를 사용합니다. 공백이 포함된 가상 네트워크의 이름은 따옴표로 묶어야 합니다.

  ```powershell
  Get-AzureVNetConnection "Group ClassicRG ClassicVNet"
  ```
2. cmdlet이 완료되면 값을 봅니다. 아래 예제에서는 [연결 상태]가 '연결됨'으로 표시되고 송/수신 바이트를 볼 수 있습니다.

        ConnectivityState         : Connected
        EgressBytesTransferred    : 181664
        IngressBytesTransferred   : 182080
        LastConnectionEstablished : 1/7/2016 12:40:54 AM
        LastEventID               : 24401
        LastEventMessage          : The connectivity state for the local network site 'RMVNetLocal' changed from Connecting to
                                    Connected.
        LastEventTimeStamp        : 1/7/2016 12:40:54 AM
        LocalNetworkSiteName      : RMVNetLocal
