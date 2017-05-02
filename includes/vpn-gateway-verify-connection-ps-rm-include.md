'-디버그'를 사용하거나 사용하지 않고 'Get-AzureRmVirtualNetworkGatewayConnection' cmdlet을 사용하여 연결이 성공했는지 확인할 수 있습니다. 

1. 일치하는 값을 구성하는 데 다음 cmdlet 예제를 사용합니다. 메시지가 표시되면 '모두' 실행하기 위해 A를 선택합니다. 예제에서 '-이름'은 만들고 테스트한 연결의 이름을 의미합니다.

  ```powershell
  Get-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnection -ResourceGroupName MyRG
  ```
2. cmdlet이 완료되면 값을 봅니다. 아래 예제에서는 연결 상태가 '연결됨'으로 표시되고 송/수신 바이트를 볼 수 있습니다.
   
  ```
  "connectionStatus": "Connected",
  "ingressBytesTransferred": 33509044,
  "egressBytesTransferred": 4142431
  ```