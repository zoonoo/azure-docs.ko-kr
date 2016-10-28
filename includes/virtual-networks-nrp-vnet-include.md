## 가상 네트워크
VNET(가상 네트워크)과 서브넷 리소스를 사용하여 Azure에서 실행 중인 워크로드에 대한 보안 경계를 정의할 수 있습니다. VNet은 주소 공간의 모임(CIDR 블록으로 정의됨)으로 특징지을 수 있습니다.

>[AZURE.NOTE] 네트워크 관리자는 CIDR 표기법에 익숙합니다. CIDR을 잘 모르는 경우 [자세히 알아보세요](http://whatismyipaddress.com/cidr).

![다중 서브넷을 가진 VNet](./media/resource-groups-networking/Figure4.png)

VNet에는 다음 속성이 포함될 수 있습니다.

|속성|설명|샘플 값|
|---|---|---|
|**addressSpace**|CIDR 표기법에서 VNet을 구성하는 주소 접두사 컬렉션|192\.168.0.0/16|
|**서브넷**|VNet을 구성하는 서브넷 컬렉션|아래 [서브넷](#Subnets) 참조|
|**ipAddress**|개체에 할당된 IP 주소입니다. 읽기 전용 속성입니다.|104\.42.233.77|

### 서브넷
서브넷은 VNet의 자식 리소스이며, IP 주소 접두사를 사용하여 CIDR 블록 내의 주소 공간 세그먼트를 정의하는 데 도움이 됩니다. NIC는 서브넷에 추가하고 VM에 연결하여 다양한 워크로드에 대한 연결을 제공할 수 있습니다.

서브넷에는 다음 속성이 포함될 수 있습니다.

|속성|설명|샘플 값|
|---|---|---|
|**addressPrefix**|CIDR 표기법에서 서브넷을 구성하는 단일 주소 접두사|192\.168.1.0/24|
|**networkSecurityGroup**|서브넷에 적용된 NSG|[NSG](#Network-Security-Group) 참조|
|**routeTable**|서브넷에 적용된 경로 테이블|[UDR](#Route-table) 참조|
|**ipConfigurations**|서브넷에 연결된 NIC에 사용된 IP 구성 개체 컬렉션|[UDR](#Route-table) 참조|


JSON 형식의 샘플 VNet:

	{
	    "name": "TestVNet",
	    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet",
	    "etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
	    "type": "Microsoft.Network/virtualNetworks",
	    "location": "westus",
	    "tags": {
	        "displayName": "VNet"
	    },
	    "properties": {
	        "provisioningState": "Succeeded",
	        "resourceGuid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
	        "addressSpace": {
	            "addressPrefixes": [
	                "192.168.0.0/16"
	            ]
	        },
	        "subnets": [
	            {
	                "name": "FrontEnd",
	                "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
	                "etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
	                "properties": {
	                    "provisioningState": "Succeeded",
	                    "addressPrefix": "192.168.1.0/24",
	                    "networkSecurityGroup": {
	                        "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-BackEnd"
	                    },
	                    "routeTable": {
	                        "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/routeTables/UDR-FrontEnd"
	                    },
	                    "ipConfigurations": [
	                        {
	                            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICWEB1/ipConfigurations/ipconfig1"
	                        },
	                        ...]
	                }
	            },
	            ...]
	    }
	}

### 추가 리소스

- [VNet](../articles/virtual-network/virtual-networks-overview.md)에 대한 자세한 정보를 참조하세요.
- VNet에 대한 [REST API 참조 설명서](https://msdn.microsoft.com/library/azure/mt163650.aspx)를 읽어보세요.
- 서브넷에 대한 [REST API 참조 설명서](https://msdn.microsoft.com/library/azure/mt163618.aspx)를 읽어보세요.

<!---HONumber=AcomDC_0323_2016-->