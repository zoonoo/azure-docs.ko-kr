온-프레미스 네트워크에 대한 사이트 간 연결에는 VPN 장치가 필요합니다. 모든 VPN 장치에 대해 구성 단계를 제공하지는 않지만 다음 링크의 정보가 유용할 수 있습니다.

- 호환되는 VPN 장치에 대한 내용은 [VPN 장치](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md)를 참조하세요. 
- 장치 구성 설정에 대한 링크는 [확인된 VPN 장치](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#devicetable)를 참조하세요. 장치 구성에 대한 링크가 가장 효율적으로 제공됩니다. 최신 구성 정보에 대해서는 항상 장치 제조업체에 문의하는 것이 가장 좋습니다.
- 장치 구성 샘플을 편집하는 방법에 대한 정보는 [샘플 편집](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#editing)을 참조하세요.
- IPsec/IKE 매개 변수는 [매개 변수](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#ipsec)를 참조하세요.
- VPN 장치를 구성하기 전에 사용하려는 VPN 장치에 대한 [알려진 장치 호환성 문제](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#known)를 확인합니다.

VPN 장치를 구성할 때 다음이 필요합니다.

- 공유 키 - 사이트 간 VPN 연결을 만들 때 지정하는 것과 동일한 공유 키입니다. 이 예제에서는 기본적인 공유 키를 사용합니다. 실제로 사용할 키는 좀 더 복잡하게 생성하는 것이 좋습니다.

- 가상 네트워크 게이트웨이의 공용 IP 주소 Azure Portal, PowerShell 또는 CLI를 사용하여 공용 IP 주소를 볼 수 있습니다.