가상 네트워크 게이트웨이를 만들 때 사용하려는 게이트웨이 SKU를 지정해야 합니다. 더 높은 게이트웨이 SKU를 선택하고 더 많은 CPU 및 네트워크 대역폭을 게이트웨이에 할당하면 결과적으로 게이트웨이는 가상 네트워크에 더 높은 네트워크 처리량을 지원할 수 있습니다.

VPN Gateway는 다음 SKU를 사용할 수 있습니다.

- Basic
- Standard
- HighPerformance

SKU를 선택할 때 다음 제한 사항을 고려합니다.

- 정책 기반 VPN 유형을 사용하려는 경우 기본 게이트웨이 SKU를 사용해야 합니다. 정책 기반 VPN(이전에는 정적 라우팅이라고 함)은 다른 SKU에서 지원되지 않습니다.
- BGP는 기본 SKU에 지원되지 않습니다.
- ExpressRoute-VPN Gateway 공존 구성은 기본 SKU에서 지원되지 않습니다.


<!--HONumber=Oct16_HO2-->


