## 네트워크 보안 그룹
NSG 리소스를 사용하면 허용 및 거부 규칙을 구현하여 워크로드에 대한 보안 경계를 만들 수 있습니다. 이러한 규칙은 VM, NIC 또는 서브넷에 적용할 수 있습니다.

|속성|설명|샘플 값|
|---|---|---|
|**서브넷**|NSG가 적용되는 서브넷 ID 목록입니다.|/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd|
|**securityRules**|NSG를 구성하는 보안 규칙의 목록|아래 [보안 규칙](#Security-rule) 참조|
|**defaultSecurityRules**|모든 NSG에 있는 기본 보안 규칙 목록|아래 [기본 보안 규칙](#Default-security-rules) 참조|

- **보안 규칙** - NSG에 대해 여러 보안 규칙을 정의할 수 있습니다. 각 규칙은 서로 다른 유형의 트래픽을 허용하거나 거부할 수 있습니다.

### 보안 규칙
보안 규칙은 아래 속성을 포함하는 NSG의 자식 리소스입니다.

|속성|설명|샘플 값|
|---|---|---|
|**description**|규칙에 대한 설명|서브넷 X에서 모든 VM에 인바운드 트래픽 허용|
|**protocol**|규칙과 일치하는 프로토콜|TCP, UDP 또는 *| 
|**sourcePortRange**|규칙과 일치하는 원본 포트 범위|80, 100-200, *| 
|**destinationPortRange**|규칙과 일치하는 대상 포트 범위|80, 100-200, *| 
|**sourceAddressPrefix**|규칙과 일치하는 원본 주소 접두사|10\.10.10.1, 10.10.10.0/24, VirtualNetwork|
|**destinationAddressPrefix**|규칙과 일치하는 대상 주소 접두사|10\.10.10.1, 10.10.10.0/24, VirtualNetwork|
|**direction**|규칙과 일치하는 트래픽의 방향|인바운드 또는 아웃바운드|
|**우선 순위**|규칙에 대한 우선순위입니다. 규칙은 우선 순위대로 검사되고, 규칙이 적용된 후에는 일치 여부를 알기 위해 규칙이 더 이상 테스트되지 않습니다.|10, 100, 65000|
|**access**|규칙이 일치하는 경우 적용할 액세스 유형|허용 또는 거부|

JSON 형식의 샘플 NSG:

	{
	    "name": "NSG-BackEnd",
	    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-BackEnd",
	    "etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
	    "type": "Microsoft.Network/networkSecurityGroups",
	    "location": "westus",
	    "tags": {
	        "displayName": "NSG - Front End"
	    },
	    "properties": {
	        "provisioningState": "Succeeded",
	        "resourceGuid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
	        "securityRules": [
	            {
	                "name": "rdp-rule",
	                "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-BackEnd/securityRules/rdp-rule",
	                "etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
	                "properties": {
	                    "provisioningState": "Succeeded",
	                    "description": "Allow RDP",
	                    "protocol": "Tcp",
	                    "sourcePortRange": "*",
	                    "destinationPortRange": "3389",
	                    "sourceAddressPrefix": "Internet",
	                    "destinationAddressPrefix": "*",
	                    "access": "Allow",
	                    "priority": 100,
	                    "direction": "Inbound"
	                }
	            }
	        ],
	        "defaultSecurityRules": [
	            { [...],
	        "subnets": [
	            {
	                "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd"
	            }
	        ]
	    }
	}

### 기본 보안 규칙
기본 보안 규칙에는 보안 규칙에서 사용할 수 있는 동일한 속성이 있습니다. 기본 보안 규칙은 NSG가 적용된 리소스 간의 기본 연결을 제공하기 위해 존재합니다. 어떤 [기본 보안 규칙](../articles/virtual-network/virtual-networks-nsg.md#Default-Rules)이 있는지 알아야 합니다.

### 추가 리소스

- [NSG](../articles/virtual-network/virtual-networks-nsg.md)에 대한 자세한 정보를 참조하세요.
- NSG에 대한 [REST API 참조 설명서](https://msdn.microsoft.com/library/azure/mt163615.aspx)를 읽어보세요.
- 보안 규칙에 대한 [REST API 참조 설명서](https://msdn.microsoft.com/library/azure/mt163580.aspx)를 읽어보세요.

<!---HONumber=AcomDC_0323_2016-->