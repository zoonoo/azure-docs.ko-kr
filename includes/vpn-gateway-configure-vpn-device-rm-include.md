
VPN 장치를 구성하기 위해 온-프레미스 VPN 장치를 구성하는 데 대한 가상 네트워크 게이트웨이의 공용 IP 주소가 필요합니다. 특정 구성 정보는 장치 제조업체에 문의하고 장치를 구성합니다. Azure와 잘 작동하는 VPN 장치에 대한 자세한 내용은 [VPN 장치](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md)를 참조합니다.

PowerShell을 사용하여 가상 네트워크 게이트웨이의 공용 IP 주소를 찾으려면 다음 샘플을 사용합니다.

	Get-AzureRmPublicIpAddress -Name GW1PublicIP -ResourceGroupName TestRG

Azure 포털을 사용하여 가상 네트워크 게이트웨이에 대한 공용 IP 주소를 볼 수 있습니다. **가상 네트워크 게이트웨이**로 이동한 다음 게이트웨이의 이름을 클릭합니다.

<!---HONumber=AcomDC_0406_2016-->