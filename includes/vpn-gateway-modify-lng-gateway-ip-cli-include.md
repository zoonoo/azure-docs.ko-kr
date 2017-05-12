### <a name="to-modify-the-local-network-gateway-gatewayipaddress"></a>로컬 네트워크 게이트웨이의 'gatewayIpAddress'를 수정하려면

연결하려는 VPN 장치의 공용 IP 주소가 변경된 경우 해당 변경 내용을 반영하도록 로컬 네트워크 게이트웨이를 수정해야 합니다. 기존 VPN Gateway 연결을 제거하지 않고도(있는 경우) 게이트웨이 IP 주소를 변경할 수 있습니다. 게이트웨이 IP 주소를 수정하려면 [az network local-gateway update](https://docs.microsoft.com/cli/azure/network/local-gateway#update) 명령을 사용하여 'Site2' 및 'TestRG1' 값을 사용자 고유의 값으로 바꿉니다.

```azurecli
az network local-gateway update --gateway-ip-address 23.99.222.170 --name Site2 --resource-group TestRG1
```

출력에서 IP 주소가 올바른지 확인합니다.

```
"gatewayIpAddress": "23.99.222.170",
```