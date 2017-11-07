## <a name="nic"></a>NIC
네트워크 인터페이스 카드(NIC) 리소스는 VNet 리소스에 있는 기존 서브넷에 대한 네트워크 연결을 제공합니다. 독립 실행형 개체로 NIC를 만들 수 있지만 실제로 연결을 제공하려면 다른 개체에 연결해야 합니다. VM을 서브넷, 공용 IP 주소 또는 부하 분산 장치를 연결하는 데 NIC를 사용할 수 있습니다.  

| 속성 | 설명 | 샘플 값 |
| --- | --- | --- |
| **virtualMachine** |NIC와 연결된 VM. |/subscriptions/{guid}/../Microsoft.Compute/virtualMachines/vm1 |
| **macAddress** |NIC에 대한 MAC 주소 |4와 30 사이의 임의 값 |
| **networkSecurityGroup** |NIC에 연결된 NSG |/subscriptions/{guid}/../Microsoft.Network/networkSecurityGroups/myNSG1 |
| **dnsSettings** |NIC에 대한 DNS 설정 |[PIP](#Public-IP-address) |

NIC(네트워크 인터페이스 카드)는 VM(가상 컴퓨터)에 연결 될 수 있는 네트워크 인터페이스를 나타냅니다. VM은 NIC를 하나 이상 포함할 수 있습니다.

![단일 VM의 NIC](./media/resource-groups-networking/Figure3.png)

### <a name="ip-configurations"></a>IP 구성
NIC는 다음 속성을 포함하는 **ipConfigurations** 라는 자식 개체를 포함합니다.

| 속성 | 설명 | 샘플 값 |
| --- | --- | --- |
| **subnet** |NIC가 연결되는 서브넷. |/subscriptions/{guid}/../Microsoft.Network/virtualNetworks/myvnet1/subnets/mysub1 |
| **privateIPAddress** |서브넷에서 NIC에 대한 IP 주소 |10.0.0.8 |
| **privateIPAllocationMethod** |IP 할당 방법 |동적 또는 정적 |
| **enableIPForwarding** |라우팅에 NIC를 사용할 수 있는지 여부 |true 또는 false |
| **primary** |NIC가 VM에 대한 기본 NIC인지 여부 |true 또는 false |
| **publicIPAddress** |NIC와 연결된 PIP |[DNS 설정](#DNS-settings) |
| **loadBalancerBackendAddressPools** |NIC와 연결된 백 엔드 주소 풀 | |
| **loadBalancerInboundNatRules** |NIC와 연결된 인바운드 부하 분산 장치 NAT 규칙 | |

JSON 형식의 샘플 공용 IP 주소:

    {
        "name": "lb-nic1-be",
        "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/nrprg/providers/Microsoft.Network/networkInterfaces/lb-nic1-be",
        "etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
        "type": "Microsoft.Network/networkInterfaces",
        "location": "eastus",
        "properties": {
            "provisioningState": "Succeeded",
            "resourceGuid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
            "ipConfigurations": [
                {
                    "name": "NIC-config",
                    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/nrprg/providers/Microsoft.Network/networkInterfaces/lb-nic1-be/ipConfigurations/NIC-config",
                    "etag": "W/\"0027f1a2-3ac8-49de-b5d5-fd46550500b1\"",
                    "properties": {
                        "provisioningState": "Succeeded",
                        "privateIPAddress": "10.0.0.4",
                        "privateIPAllocationMethod": "Dynamic",
                        "subnet": {
                            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/NRPRG/providers/Microsoft.Network/virtualNetworks/NRPVnet/subnets/NRPVnetSubnet"
                        },
                        "loadBalancerBackendAddressPools": [
                            {
                                "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool"
                            }
                        ],
                        "loadBalancerInboundNatRules": [
                            {
                                "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1"
                            }
                        ]
                    }
                }
            ],
            "dnsSettings": { ... },
            "macAddress": "00-0D-3A-10-F1-29",
            "enableIPForwarding": false,
            "primary": true,
            "virtualMachine": {
                "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/nrprg/providers/Microsoft.Compute/virtualMachines/web1"
            }
        }
    }

### <a name="additional-resources"></a>추가 리소스
* NIC에 대한 [REST API 참조 설명서](https://msdn.microsoft.com/library/azure/mt163579.aspx) 를 읽어보세요.

