---
title: Azure 가상 WAN에서 Express 경로 개인 피어 링을 통해 사이트 간 VPN 연결 만들기 | Microsoft Docs
description: 이 자습서에서는 Azure 가상 WAN을 사용 하 여 Express 경로 개인 피어 링을 통해 사이트 간 VPN 연결을 만드는 방법에 대해 알아봅니다.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 10/11/2019
ms.author: cherylmc
Customer intent: I want to connect my on-premises networks to my VNets using S2S VPN connection over my ExpressRoute private peering using Azure Virtual WAN.
ms.openlocfilehash: 6272d6fe6f8c35c06a8121e10be2dd5a2e5512a8
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73515032"
---
# <a name="create-a-site-to-site-vpn-connection-over-expressroute-private-peering-using-azure-virtual-wan"></a>Azure 가상 WAN을 사용 하 여 Express 경로 개인 피어 링을 통해 사이트 간 VPN 연결 만들기

이 문서에서는 가상 WAN을 사용 하 여 Express 경로 회로의 개인 피어 링을 통해 온-프레미스 네트워크에서 Azure로 IPsec/IKE VPN 연결을 설정 하는 방법을 보여 줍니다. 이는 공용 인터넷을 사용 하거나 공용 IP 주소를 사용 하지 않고 Express 경로를 통해 온-프레미스 네트워크와 Azure 가상 네트워크 간에 암호화 된 전송을 제공할 수 있습니다.

## <a name="topology-and-routing"></a>토폴로지 및 라우팅

다음 다이어그램에서는 Express 경로 개인 피어 링 연결을 통한 VPN의 예를 보여 줍니다.

![Express 경로를 통한 VPN](./media/vpn-over-expressroute/vwan-vpn-over-er.png)

이 다이어그램에서는 Express 경로 개인 피어 링을 통해 Azure 허브 VPN gateway에 연결 된 온-프레미스 네트워크 내의 네트워크를 보여 줍니다. 연결 설정은 다음과 같이 간단 합니다.

1. Express 경로 회로와 개인 피어 링을 사용 하 여 Express 경로 연결 설정
2. 이 문서에 설명 된 대로 VPN 연결을 설정 합니다.

이 구성의 중요 한 측면은 Express 경로와 VPN 경로를 통해 온-프레미스 네트워크와 Azure 간의 라우팅입니다.

### <a name="traffic-from-on-premises-networks-to-azure"></a>온-프레미스 네트워크에서 Azure로의 트래픽

온-프레미스 네트워크에서 Azure로의 트래픽에 대해 Azure 접두사 (허브에 연결 된 가상 허브 및 모든 스포크 가상 네트워크 포함)는 Express 경로 개인 피어 링 BGP와 VPN BGP를 통해 보급 됩니다. 그러면 온-프레미스 네트워크에서 Azure에 대 한 두 개의 네트워크 경로 (경로)가 생성 됩니다. ipsec 보호 된 경로에 대 한 하나 이며 ipsec 보호를 사용 **하지 않는** express 경로에서 직접 사용 됩니다. 통신에 암호화가 적용 되도록 하려면 다이어그램에서 VPN에 연결 된 네트워크에 대해 온-프레미스 VPN gateway를 통한 Azure 경로가 직접 Express 경로 보다 우선적으로 사용 되도록 해야 합니다.

### <a name="traffic-from-azure-to-on-premises-networks"></a>Azure에서 온-프레미스 네트워크로의 트래픽

Azure에서 온-프레미스 네트워크로의 트래픽에는 동일한 요구 사항이 적용 됩니다. Ipsec 경로를 사용 하지 않고 직접 Express 경로 경로 보다 IPsec 경로가 선호 되도록 하려면 다음 두 가지 옵션을 사용할 수 있습니다.

- VPN에 연결 된 네트워크에 대 한 VPN BGP 세션에서 더 많은 특정 접두사를 보급 합니다. Express 경로 개인 피어 링을 통해 "VPN에 연결 된 네트워크"를 포괄 하는 더 큰 범위를 광고 하 고, VPN BGP 세션에서 더 구체적인 범위를 확인할 수 있습니다. 예를 들어 Express 경로에 대해 10.0.0.0/16을, VPN을 통해 10.0.1.0/24를 보급 합니다.

- VPN 및 Express 경로에 대 한 비연속 접두사를 보급 합니다. VPN에 연결 된 네트워크 범위가 다른 Express 경로 연결 네트워크와 분리 된 경우 VPN 및 Express 경로 BGP 세션에서 각각 접두사를 보급할 수 있습니다. 예를 들어 Express 경로를 통해 10.0.0.0/24를, VPN을 통해 10.0.1.0/24를 보급 합니다.

이러한 두 예제에서 Azure는 vpn 보호 없이 Express 경로를 사용 하는 대신 VPN 연결을 통해 10.0.1.0/24로 트래픽을 전송 합니다.

> [!WARNING]
> Express 경로와 VPN 연결 모두에 **동일한** 접두사를 보급 하는 경우 AZURE는 **vpn 보호 없이 직접 express 경로 경로를 사용**합니다.
>

## <a name="before-you-begin"></a>시작하기 전에

[!INCLUDE [Before you begin](../../includes/virtual-wan-tutorial-vwan-before-include.md)]

## <a name="openvwan"></a>1. 게이트웨이를 사용 하 여 가상 WAN 및 허브 만들기

계속 하기 전에 다음 Azure 리소스 및 해당 온-프레미스 구성이 준비 되어 있어야 합니다.

1. Azure 가상 WAN
2. [Express 경로 게이트웨이](virtual-wan-expressroute-portal.md) 및 [VPN gateway](virtual-wan-site-to-site-portal.md) 를 사용 하는 가상 WAN 허브

Azure 가상 wan 및 Express 경로 연결을 사용 하 여 허브를 만드는 단계는 azure 가상 wan을 [사용 하 여 express 경로 연결 만들기](virtual-wan-expressroute-portal.md) 를 참조 하 고, VPN을 만드는 단계를 위해 [azure 가상 wan을 사용 하 여 사이트 간 연결을 만듭니다](virtual-wan-site-to-site-portal.md) . 가상 WAN의 게이트웨이입니다.

## <a name="site"></a>2. 온-프레미스 네트워크에 대 한 사이트 만들기

사이트 리소스는 가상 WAN에 대 한 비 비트 VPN 사이트와 동일 합니다. 중요 한 점은 온-프레미스 VPN 장치의 IP 주소는 이제 개인 IP 주소 이거나 1 단계에서 만든 Express 경로 개인 피어 링을 통해 연결할 수 있는 온-프레미스 네트워크의 공용 IP 주소입니다.

> [!NOTE]
> 온-프레미스 VPN 장치 IP 주소는 Azure Express 경로 개인 피어 링을 통해 가상 WAN 허브에 보급 된 주소 접두사의 일부 여야 합니다.
>

1. 브라우저에서 Azure Portal로 이동 합니다. 만든 WAN을 클릭합니다. WAN 페이지의 **연결**에서 **vpn 사이트** 를 클릭 하 여 vpn 사이트 페이지를 엽니다.

2. **VPN 사이트** 페이지에서 **+사이트 만들기**를 클릭합니다.

3. **사이트 만들기** 페이지에서 다음 필드를 채웁니다.

   * **구독** - 구독을 확인합니다.
   * **리소스 그룹** -사용 하려는 리소스 그룹을 선택 하거나 만듭니다.
   * **지역** -VPN 사이트 리소스에 대 한 Azure 지역입니다.
   * **이름** - 온-프레미스 사이트를 참조할 때 사용하려는 이름입니다.
   * **장치 공급 업체** -온-프레미스 VPN 장치의 공급 업체입니다.
   * **Border Gateway Protocol** -온-프레미스 네트워크에서 BGP를 사용 하는 경우 "사용"을 선택 합니다.
   * **프라이빗 주소 공간** - 온-프레미스 사이트에 있는 IP 주소 공간입니다. 이 주소 공간으로 향하는 트래픽은 VPN gateway를 통해 온-프레미스 네트워크로 라우팅됩니다.
   * **허브** -이 VPN 사이트에 연결할 허브를 하나 이상 선택 합니다. 선택한 허브에는 이미 만들어져 있는 VPN 게이트웨이가 있어야 합니다.

4. 다음: VPN 링크 설정에 대 한 **> 링크** 를 클릭 합니다.

   * **링크 이름** -이 연결을 참조 하는 데 사용할 이름입니다.
   * **공급자 이름** -이 사이트에 대 한 인터넷 서비스 공급자의 이름입니다. Express 경로 온-프레미스 네트워크의 경우 Express 경로 서비스 공급자의 이름입니다.
   * **속도** -인터넷 서비스 링크 또는 express 경로 회로의 속도입니다.
   * **IP 주소** -온-프레미스 사이트에 있는 VPN 장치의 공용 IP 주소입니다. 또는 온-프레미스 Express 경로의 경우 Express 경로를 통한 VPN 장치의 개인 IP 주소입니다.

   BGP를 사용 하는 경우 Azure에서이 사이트에 대해 만들어진 모든 연결에 적용 됩니다. Virtual WAN에서 BGP를 구성하는 것은 Azure VPN 게이트웨이에서 BGP를 구성하는 것과 같습니다. 온-프레미스 BGP 피어 주소는 vpn 사이트의 VNet 주소 공간 또는 장치에 대 한 VPN의 IP 주소와 달라 *야* 합니다. VPN 디바이스에서 BGP 피어 IP에 다른 IP 주소를 사용하세요. 디바이스에서 루프백 인터페이스에 할당한 주소가 될 수 있습니다. 그러나 APIPA(169.254.*x*.*x*) 주소는 *안 됩니다*. 위치를 나타내는 해당 로컬 네트워크 게이트웨이에서 이 주소를 지정합니다. BGP 필수 조건은 [Azure VPN Gateway의 BGP 정보](../vpn-gateway/vpn-gateway-bgp-overview.md)를 참조하세요.

5. 다음을 클릭 합니다 **. 검토 + > 만들기** 를 클릭 하 여 설정 값을 확인 하 고 VPN 사이트를 만듭니다. **허브** 를 선택 하 여 연결 하는 경우 온-프레미스 네트워크와 허브 VPN gateway 간에 연결이 설정 됩니다.

## <a name="hub"></a>3. Express 경로를 사용 하도록 VPN 연결 설정 업데이트

VPN 사이트를 만들고 허브에 연결한 후에는 다음 단계를 사용 하 여 Express 경로 개인 피어 링을 사용 하도록 연결을 구성 합니다.

1. 가상 WAN 리소스 페이지로 돌아가서 허브 리소스를 클릭 합니다. 또는 VPN 사이트에서 연결 된 허브로 이동 합니다.

2. **연결**에서 **VPN (사이트 간)** 을 클릭 합니다.

3. "..."를 클릭 합니다. Express 경로를 통한 VPN 사이트에서 "**이 허브에 대 한 vpn 연결 편집**"을 선택 합니다.

4. "**Azure 개인 IP 주소 사용**"에서 "예"를 선택 합니다. 설정은 공용 IP 주소가 아닌이 연결에 대해 게이트웨이의 허브 주소 범위 내에서 개인 IP 주소를 사용 하도록 허브 VPN gateway를 구성 합니다. 이렇게 하면 온-프레미스 네트워크의 트래픽이이 VPN 연결에 공용 인터넷을 사용 하는 대신 Express 경로 개인 피어 링 경로를 통과 하 게 됩니다. 다음 스크린샷에서는 설정 창을 보여 줍니다.

   ![VPN 연결 설정](./media/vpn-over-expressroute/vpn-link-configuration.png)
   
5. **Save**를 클릭합니다.

저장 한 후에는 허브 VPN gateway에서 VPN 게이트웨이의 개인 IP 주소를 사용 하 여 Express 경로를 통해 온-프레미스 VPN 장치와의 IPsec/IKE 연결을 설정 합니다.

## <a name="associate"></a>4. 허브 VPN gateway 개인 IP 주소 가져오기

VPN 장치 구성을 다운로드 하 여 허브 VPN 게이트웨이의 개인 IP 주소를 가져옵니다. 온-프레미스 VPN 장치를 구성 하는 데 필요 합니다.

1. 허브에 대 한 페이지의 **연결** 에서 **VPN (사이트 간)** 을 클릭 합니다.

2. 개요 페이지의 맨 위에서 **VPN 구성 다운로드**를 클릭 합니다. Azure는 ' microsoft-network-[location] ' 리소스 그룹에 저장소 계정을 만듭니다. 여기서 location은 WAN의 위치입니다. VPN 디바이스에 구성을 적용한 후에는 이 스토리지 계정을 삭제할 수 있습니다.

3. 파일 만들기가 끝나면 링크를 클릭하여 다운로드할 수 있습니다.

4. VPN 디바이스에 구성을 적용합니다.

### <a name="understanding-the-vpn-device-configuration-file"></a>VPN 디바이스 구성 파일 이해

디바이스 구성 파일은 온-프레미스 VPN 디바이스를 구성할 때 사용할 설정을 포함합니다. 이 파일을 볼 때 다음 정보를 확인합니다.

* **vpnSiteConfiguration -** 이 섹션은 Virtual WAN에 연결된 사이트로 설정된 디바이스 정보를 나타냅니다. 여기에는 분기 디바이스의 이름 및 공용 IP 주소가 포함됩니다.
* **vpnSiteConnections -** 이 섹션에서는 다음 설정에 관한 정보를 제공합니다.

    * 가상 허브 VNet의 **주소 공간**<br/>예:
           ```
           "AddressSpace":"10.51.230.0/24"
           ```
    * 허브에 연결된 VNet의 **주소 공간**<br>예:
           ```
           "ConnectedSubnets":["10.51.231.0/24"]
            ```
    * 가상 허브 vpngateway의 **IP 주소**. vpngateway의 각 연결은 활성-활성 구성의 2개 터널로 구성되므로 이 파일에 두 IP 주소가 모두 나열됩니다. 이 예제에는 각 사이트에 대 한 "Instance0" 및 "Instance1"가 표시 되며, 공용 IP 주소 대신 개인 IP 주소입니다.<br>예:
           ``` 
           "Instance0":"10.51.230.4"
           "Instance1":"10.51.230.5"
           ```
    * BGP, 미리 공유한 키 등의 **Vpngateway 연결 구성 세부 정보** PSK는 자동으로 생성 되는 미리 공유한 키입니다. 사용자 지정 PSK의 개요 페이지에서 연결을 언제든지 편집할 수 있습니다.
  
### <a name="example-device-configuration-file"></a>예제 디바이스 구성 파일

```
[{
      "configurationVersion":{
        "LastUpdatedTime":"2019-10-11T05:57:35.1803187Z",
        "Version":"5b096293-edc3-42f1-8f73-68c14a7c4db3"
      },
      "vpnSiteConfiguration":{
        "Name":"VPN-over-ER-site",
        "IPAddress":"172.24.127.211",
        "LinkName":"VPN-over-ER"
      },
      "vpnSiteConnections":[{
        "hubConfiguration":{
          "AddressSpace":"10.51.230.0/24",
          "Region":"West US 2",
          "ConnectedSubnets":["10.51.231.0/24"]
        },
        "gatewayConfiguration":{
          "IpAddresses":{
            "Instance0":"10.51.230.4",
            "Instance1":"10.51.230.5"
          }
        },
        "connectionConfiguration":{
          "IsBgpEnabled":false,
          "PSK":"abc123",
          "IPsecParameters":{"SADataSizeInKilobytes":102400000,"SALifeTimeInSeconds":3600}
        }
      }]
    },
    {
      "configurationVersion":{
        "LastUpdatedTime":"2019-10-11T05:57:35.1803187Z",
        "Version":"fbdb34ea-45f8-425b-9bc2-4751c2c4fee0"
      },
      "vpnSiteConfiguration":{
        "Name":"VPN-over-INet-site",
        "IPAddress":"13.75.195.234",
        "LinkName":"VPN-over-INet"
      },
      "vpnSiteConnections":[{
        "hubConfiguration":{
          "AddressSpace":"10.51.230.0/24",
          "Region":"West US 2",
          "ConnectedSubnets":["10.51.231.0/24"]
        },
        "gatewayConfiguration":{
          "IpAddresses":{
            "Instance0":"51.143.63.104",
            "Instance1":"52.137.90.89"
          }
        },
        "connectionConfiguration":{
          "IsBgpEnabled":false,
          "PSK":"abc123",
          "IPsecParameters":{"SADataSizeInKilobytes":102400000,"SALifeTimeInSeconds":3600}
        }
      }]
}]
```

### <a name="configuring-your-vpn-device"></a>VPN 디바이스 구성

디바이스를 구성하는 지침이 필요한 경우에는 [VPN 디바이스 구성 스크립트 페이지](~/articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#configscripts)의 지침을 다음 주의 사항과 함께 사용하면 됩니다.

* VPN 디바이스 페이지의 지침은 Virtual WAN용으로 작성되지 않았지만 구성 파일의 Virtual WAN 값을 사용하여 VPN 디바이스를 수동으로 구성할 수 있습니다. 
* VPN Gateway용 다운로드 가능한 디바이스 구성 스크립트는 구성이 다르기 때문에 Virtual WAN에서는 작동하지 않습니다.
* 새 가상 WAN은 IKEv1 및 IKEv2를 모두 지원할 수 있습니다.
* 가상 WAN은 경로 기반 VPN 디바이스와 디바이스 지침만 사용할 수 있습니다.

## <a name="viewwan"></a>5. 가상 WAN 보기

1. 가상 WAN 탭으로 이동합니다.

2. 개요 페이지의 맵에 있는 각 점은 허브를 나타냅니다. 점 위로 마우스를 가져가면 허브 상태 요약을 볼 수 있습니다.

3. 허브 및 연결 섹션에서 허브 상태, 사이트, 지역, VPN 연결 상태 및 입/출력 바이트를 볼 수 있습니다.

## <a name="viewhealth"></a>6. 리소스 상태 보기

1. WAN으로 이동합니다.

2. WAN 페이지의 **지원 및 문제 해결** 섹션에서 **상태**를 클릭하고 리소스를 봅니다.

## <a name="connectmon"></a>7. 연결 모니터링

Azure VM과 원격 사이트 간의 통신을 모니터링하는 연결을 만듭니다. 연결 모니터를 설정하는 방법에 대한 자세한 내용은 [네트워크 통신 모니터링](~/articles/network-watcher/connection-monitor.md)을 참조하세요. 원본 필드는 Azure의 VM IP이고 대상 IP는 사이트 IP입니다.

## <a name="cleanup"></a>8. 리소스 정리

이러한 리소스가 더 이상 필요하지 않은 경우 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)을 사용하여 리소스 그룹과 여기에 포함된 모든 리소스를 제거할 수 있습니다. "myResourceGroup"을 리소스 그룹의 이름으로 바꾸고 다음 PowerShell 명령을 실행합니다.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>다음 단계

이 문서는 가상 WAN을 사용 하 여 Express 경로 개인 피어 링을 통해 VPN 연결을 만드는 데 도움이 됩니다. 가상 WAN 및 기타 관련 기능에 대해 자세히 알아보려면 [가상 Wan 개요](virtual-wan-about.md) 페이지를 참조 하세요.
