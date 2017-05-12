### <a name="noconnection"></a>로컬 네트워크 게이트웨이 IP 주소 접두사를 수정하려면 - 게이트웨이 연결 없음

게이트웨이 연결이 없고 IP 주소 접두사를 추가 또는 제거하려는 경우 로컬 네트워크 게이트웨이를 만들 때 사용하는 [az network local-gateway create](https://docs.microsoft.com/cli/azure/network/local-gateway#create) 명령을 사용합니다. 이 명령을 사용하여 VPN 장치의 게이트웨이 IP 주소도 업데이트할 수 있습니다. 현재 설정을 덮어쓰려면 로컬 네트워크 게이트웨이의 기존 이름을 사용합니다. 다른 이름을 사용하면 기존 게이트웨이를 덮어쓰지 않고 새 로컬 네트워크 게이트웨이를 만들게 됩니다.

변경할 때마다 변경하려는 접두사뿐 아니라 전체 접두사 목록을 지정해야 합니다. 계속 유지할 접두사만 지정합니다. 이 예에서는 10.0.0.0/24 및 20.0.0.0/24입니다.

```azurecli
az network local-gateway create --gateway-ip-address 23.99.221.164 --name Site2 --connection-name TestRG1 --local-address-prefixes 10.0.0.0/24 20.0.0.0/24
```

### <a name="withconnection"></a>로컬 네트워크 게이트웨이 IP 주소 접두사를 수정하려면 - 기존 게이트웨이 연결

게이트웨이 연결이 있고 IP 주소 접두사를 추가 또는 제거하려는 경우 [az network local-gateway update](https://docs.microsoft.com/cli/azure/network/local-gateway#update) 명령을 사용하여 접두사를 업데이트할 수 있습니다. 이로 인해 VPN 연결에 약간의 가동 중지 시간이 발생합니다. IP 주소 접두사를 수정할 때 VPN Gateway를 삭제할 필요가 없습니다.

변경할 때마다 변경하려는 접두사뿐 아니라 전체 접두사 목록을 지정해야 합니다. 이 예제에서는 10.0.0.0/24 및 20.0.0.0/24가 이미 있습니다. 접두사 30.0.0.0/24 및 40.0.0.0/24를 추가하고 업데이트할 때 접두사 4개를 모두 지정합니다.

```azurecli
az network local-gateway update --local-address-prefixes 10.0.0.0/24 20.0.0.0/24 30.0.0.0/24 40.0.0.0/24 --name VNet1toSite2 --connection-name TestRG1
```