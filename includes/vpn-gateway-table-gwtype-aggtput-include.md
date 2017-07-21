Azure는 VPN Gateway SKU를 제공합니다.

|**SKU**   | **S2S/VNet 간<br>터널** | **P2S<br>연결** | **집계<br>처리량** |
|---       | ---                             | ---                    | ---                         |
|**VpnGw1**| 최대 30                         | 최대 128               | 500Mbps                    |
|**VpnGw2**| 최대 30                         | 최대 128               | 1Gbps                      |
|**VpnGw3**| 최대 30                         | 최대 128               | 1.25Gbps                   |
|**Basic** | 최대 10                         | 최대 128               | 100Mbps                    | 
|          |                                 |                        |                             | 

- 처리량은 단일 게이트웨이를 통해 집계된 여러 터널의 측정값을 기반으로 합니다. 인터넷 트래픽 조건 및 응용 프로그램 동작으로 인해 처리량이 보장되지 않습니다.

- 가격 책정 정보는 [가격 책정](https://azure.microsoft.com/pricing/details/vpn-gateway) 페이지에 있습니다.

- [SLA](https://azure.microsoft.com/en-us/support/legal/sla/vpn-gateway/) 페이지에서 Service Level Agreement(서비스 수준 계약) 정보를 확인할 수 있습니다.