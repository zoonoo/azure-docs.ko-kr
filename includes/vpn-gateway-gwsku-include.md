가상 네트워크 게이트웨이를 만들 때 사용하려는 게이트웨이 SKU를 지정해야 합니다. 작업 부하, 처리량, 기능 및 SLA의 종류를 기반으로 하는 요구 사항을 충족하는 SKU를 선택합니다.

[!INCLUDE [classic SKU](./vpn-gateway-classic-sku-support-include.md)]

[!INCLUDE [Aggregated throughput by SKU](./vpn-gateway-table-gwtype-aggtput-include.md)]

###  <a name="workloads"></a>프로덕션 *vs.* 개발=테스트 워크로드

SLA 및 기능 집합의 차이로 인해 프로덕션 *vs.* 개발-테스트에 다음과 같은 SKU를 사용하는 것이 좋습니다.

| **워크로드**                       | **SKU**               |
| ---                                | ---                    |
| **프로덕션, 중요한 워크로드** | VpnGw1, VpnGw2, VpnGw3 |
| **개발-테스트 또는 개념 증명**   | Basic                  |
|                                    |                        |

이전 SKU를 사용하는 경우 프로덕션 SKU 권장 사항은 표준 및 HighPerformance SKU입니다. 이전 SKU에 대한 정보는 [게이트웨이 SKU(이전 SKU)](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md)를 참조하세요.

###  <a name="feature"></a>게이트웨이 SKU 기능 집합

새 게이트웨이 SKU는 게이트웨이에서 제공한 기능 집합을 간소화합니다.

| **SKU**| **기능**|
| ---    | ---         |
|**Basic**   | **경로 기반 VPN**: P2S 장착 10터널, P2S에 대한 RADIUS 인증 없음, P2S에 대한 IKEv2 없음<br>**정책 기반 VPN**: (IKEv1): 1개의 터널. P2S 없음|
| **VpnGw1, VpnGw2 및 VpnGw3** | **경로 기반 VPN**: 최대 30개의 터널( * ),P2S, BGP, 활성-활성, 사용자 지정 IPsec/IKE 정책, ExpressRoute/VPN 공존 |
|        |             |

( * ) 경로 기반 VPN 게이트웨이(VpnGw1, VpnGw2, VpnGw3)를 여러 온-프레미스 정책 기반 방화벽 장치에 연결하도록 "PolicyBasedTrafficSelectors"를 구성할 수 있습니다. 자세한 내용은 [PowerShell을 사용하여 VPN Gateway를 여러 온-프레미스 정책 기반 VPN 장치에 연결](../articles/vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md)을 참조하세요.

###  <a name="resize"></a>게이트웨이 SKU 크기 조정

1. VpnGw1, VpnGw2와 VpnGw3 SKU 간에 크기를 조정할 수 있습니다.
2. 이전 게이트웨이 SKU로 작동하는 경우 Basic, Standard 및 HighPerformance SKU 간에 크기를 조정할 수 있습니다.
2. Basic/Standard/HighPerformance SKU에서 새 VpnGw1/VpnGw2/VpnGw3 SKU까지 크기를 조정할 수 **없습니다**. 대신 새 SKU으로 [마이그레이션](#migrate)해야 합니다.

###  <a name="migrate"></a>이전 SKU에서 새 SKU로 마이그레이션

[!INCLUDE [Migrate SKU](./vpn-gateway-migrate-legacy-sku-include.md)]
