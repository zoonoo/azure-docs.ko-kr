온-프레미스 네트워크에 대한 사이트 간 연결에는 VPN 장치가 필요합니다. Azure에서 작동할 수 있는 많은 다른 VPN 장치가 있습니다. VPN 장치 및 구성 설정에 대한 정보는 [VPN 장치](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md)를 참조하세요. VPN 장치를 구성하기 전에 사용하려는 VPN 장치에 대한 [알려진 장치 호환성 문제](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#known)를 확인합니다. 특정 VPN 장치 구성 정보는 장치 제조업체에 문의하세요.

VPN 장치를 구성하려면 다음 항목이 필요합니다.

- 가상 네트워크 게이트웨이의 **공용 IP 주소**입니다.

    -  Azure Portal을 사용하여 공용 IP 주소를 찾으려면 **가상 네트워크 게이트웨이**로 이동한 다음 게이트웨이의 이름을 클릭합니다. 

    - PowerShell을 사용하여 가상 네트워크 게이트웨이의 공용 IP 주소를 찾으려면 값을 고유한 값으로 대체하는 다음 샘플을 사용합니다.

            Get-AzureRmPublicIpAddress -Name GW1PublicIP -ResourceGroupName TestRG
- **공유 키**입니다. 사이트 간 VPN 연결을 만들 때 지정하는 동일한 공유 키입니다. 이 예제에서는 매우 기본적인 공유 키를 사용합니다. 사용할 복잡한 키를 생성해야 합니다.




