게이트웨이 IP 주소를 수정하려면 'New-AzureRmVirtualNetworkGatewayConnection' cmdlet을 사용합니다. 현재 'Set' cmdlet은 게이트웨이 IP 주소 수정을 지원하지 않습니다.

### <a name="gwipnoconnection"></a>게이트웨이 IP 주소 수정 - 게이트웨이 연결 없음
아직 연결되지 않은 로컬 네트워크 게이트웨이의 게이트웨이 IP 주소를 수정하려면 아래 예제를 사용합니다. 이와 동시에 주소 접두사를 수정할 수도 있습니다. 로컬 네트워크 게이트웨이의 기존 이름으로 현재 설정을 덮어써야 합니다. 이렇게 하지 않으면 기존 게이트웨이를 덮어쓰지 않고 새 로컬 네트워크 게이트웨이를 만들게 됩니다.

다음 예제를 사용하여 값을 사용자의 값으로 바꿉니다.

```powershell
New-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName `
-Location "West US" -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24') `
-GatewayIpAddress "5.4.3.2" -ResourceGroupName MyRGName
```

### <a name="gwipwithconnection"></a>게이트웨이 IP 주소 수정 - 기존 게이트웨이 연결
게이트웨이 연결이 이미 있는 경우 먼저 연결을 제거해야 합니다. 연결을 제거한 후 게이트웨이 IP 주소를 수정하고 새 연결을 다시 만들 수 있습니다. 이와 동시에 주소 접두사를 수정할 수도 있습니다. 이로 인해 VPN 연결에 약간의 가동 중지 시간이 발생합니다.

> [!IMPORTANT]
> VPN 게이트웨이는 삭제하지 않습니다. 삭제할 경우 뒤로 돌아가서 단계를 다시 반복해야 합니다. 뿐만 아니라 새 VPN Gateway IP 주소로 온-프레미스 VPN 장치를 업데이트해야 합니다.
> 
> 

1. 연결을 제거합니다. 'Get-AzureRmVirtualNetworkGatewayConnection' cmdlet을 사용하여 연결 이름을 찾을 수 있습니다.

  ```powershell
  Remove-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName `
  -ResourceGroupName MyRGName
  ```
2. 'GatewayIpAddress' 값을 수정합니다. 이와 동시에 주소 접두사를 수정할 수도 있습니다. 로컬 네트워크 게이트웨이의 기존 이름으로 현재 설정을 덮어써야 합니다. 이렇게 하지 않으면 기존 게이트웨이를 덮어쓰지 않고 새 로컬 네트워크 게이트웨이를 만들게 됩니다.

  ```powershell
  New-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName `
  -Location "West US" -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24') `
  -GatewayIpAddress "104.40.81.124" -ResourceGroupName MyRGName
  ```
3. 연결을 만듭니다. 이 예제에서는 IPsec 연결 형식을 구성합니다. 연결을 다시 만들 때 구성에 대해 지정된 연결 유형을 사용합니다. 추가 연결 형식은 [PowerShell cmdlet](https://msdn.microsoft.com/library/mt603611.aspx) 페이지를 참조하세요.  VirtualNetworkGateway 이름을 가져오려면 'Get-AzureRmVirtualNetworkGateway' cmdlet을 실행합니다.
   
    변수를 설정합니다.

  ```powershell
  $local = Get-AzureRMLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName `
  $vnetgw = Get-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName MyRGName
  ```
   
    연결을 만듭니다.

  ```powershell 
  New-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName -ResourceGroupName MyRGName `
  -Location "West US" `
  -VirtualNetworkGateway1 $vnetgw `
  -LocalNetworkGateway2 $local `
  -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
  ```