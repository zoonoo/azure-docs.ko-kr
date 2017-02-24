## <a name="route-tables"></a>경로 테이블
경로 테이블 리소스는 Azure 인프라 내에서 트래픽의 흐름 방식을 정의하는 데 사용되는 경로를 포함합니다. UDR(사용자 정의 경로)을 사용하여 제공된 서브넷의 모든 트래픽을 방화벽 또는 IDS(침입 검색 시스템)와 같은 가상 어플라이언스로 보낼 수 있습니다. 경로 테이블은 서브넷에 연결할 수 있습니다. 

경로 테이블에는 다음 속성이 포함될 수 있습니다.

| 속성 | 설명 | 샘플 값 |
| --- | --- | --- |
| **routes** |경로 테이블의 사용자 정의 경로 컬렉션 | [사용자 정의 경로](#User-defined-routes) |
| **서브넷** |경로 테이블이 적용되는 서브넷의 컬렉션 | [서브넷](#Subnets) |

### <a name="user-defined-routes"></a>사용자 정의 경로
UDR을 만들어 대상 주소에 따라 트래픽을 보낼 위치를 지정할 수 있습니다. 경로를 네트워크 패킷의 대상 주소에 기반한 기본 게이트웨이 정의로 간주해도 됩니다.

UDR에는 다음 속성이 포함될 수 있습니다. 

| 속성 | 설명 | 샘플 값 |
| --- | --- | --- |
| **addressPrefix** |대상의 주소 접두사 또는 전체 IP 주소 |192.168.1.0/24, 192.168.1.101 |
| **nextHopType** |트래픽을 보낼 장치의 유형 |VirtualAppliance, VPN 게이트웨이, 인터넷 |
| **nextHopIpAddress** |다음 홉에 대한 IP 주소 |192.168.1.4 |

JSON 형식의 샘플 경로 테이블:

    {
        "name": "UDR-BackEnd",
        "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/routeTables/UDR-BackEnd",
        "etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
        "type": "Microsoft.Network/routeTables",
        "location": "westus",
        "properties": {
            "provisioningState": "Succeeded",
            "routes": [
                {
                    "name": "RouteToFrontEnd",
                    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/routeTables/UDR-BackEnd/routes/RouteToFrontEnd",
                    "etag": "W/\"v\"",
                    "properties": {
                        "provisioningState": "Succeeded",
                        "addressPrefix": "192.168.1.0/24",
                        "nextHopType": "VirtualAppliance",
                        "nextHopIpAddress": "192.168.0.4"
                    }
                }
            ],
            "subnets": [
                {
                    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/BackEnd"
                }
            ]
        }
    }

### <a name="additional-resources"></a>추가 리소스
* [UDR](../articles/virtual-network/virtual-networks-udr-overview.md)에 대해 자세히 알아보세요.
* 경로 테이블에 대한 [REST API 참조 설명서](https://msdn.microsoft.com/library/azure/mt502549.aspx) 를 읽어보세요.
* UDR(사용자 정의 경로)에 대한 [REST API 참조 설명서](https://msdn.microsoft.com/library/azure/mt502539.aspx) 를 읽어보세요.



<!--HONumber=Nov16_HO3-->


