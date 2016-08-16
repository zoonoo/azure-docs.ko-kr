VNet 피어링은 Azure 백본 네트워크를 통해 동일한 지역에 있는 두 개의 가상 네트워크를 연결하는 메커니즘입니다. 두 가상 네트워크가 피어링되면 모든 연결에 대해 단일 가상 네트워크처럼 표시됩니다. VNet 피어링에 익숙하지 않은 경우 [VNet 피어링 개요](../articles/virtual-network/virtual-network-peering-overview.md)를 참고하세요.

VNet 피어링은 공개 미리 보기 상태이며 아래 명령을 사용하여 등록해야 사용할 수 있습니다.

    Register-AzureRmProviderFeature -FeatureName AllowVnetPeering -ProviderNamespace Microsoft.Network

    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
 

<!---HONumber=AcomDC_0810_2016-->