---
title: 'ExpressRoute 암호화 구성: Azure Virtual WAN용 ExpressRoute를 통한 IPsec'
description: Azure Virtual WAN을 사용하여 ExpressRoute 개인 피어링을 통해 사이트 간 VPN 연결을 만드는 방법을 알아봅니다.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c74468620b61c0bff01acc0012958be71d5f3786
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110704692"
---
# <a name="expressroute-encryption-ipsec-over-expressroute-for-virtual-wan"></a>ExpressRoute 암호화: Virtual WAN용 ExpressRoute를 통한 IPsec

이 문서에서는 Azure Virtual WAN을 사용하여 Azure ExpressRoute 회로의 개인 피어링을 통해 온-프레미스 네트워크에서 Azure로 IPsec/IKE VPN 연결을 설정하는 방법을 보여 줍니다. 이 기술은 공용 인터넷을 통하거나 공용 IP 주소를 사용하지 않고도 ExpressRoute를 통해 온-프레미스 네트워크와 Azure 가상 네트워크 간에 암호화된 전송을 제공합니다.

## <a name="topology-and-routing"></a>토폴로지 및 라우팅

다음 다이어그램은 ExpressRoute 개인 피어링을 통한 VPN 연결의 예를 보여 줍니다.

:::image type="content" source="./media/vpn-over-expressroute/vwan-vpn-over-er.png" alt-text="ExpressRoute를 통한 VPN":::

다이어그램은 ExpressRoute 개인 피어링을 통해 Azure 허브 VPN 게이트웨이에 연결된 온-프레미스 네트워크 내의 네트워크를 보여 줍니다. 연결 설정은 다음과 같이 간단합니다.

1. ExpressRoute 회로와 개인 피어링을 사용하여 ExpressRoute 연결을 설정합니다.
2. 이 문서에 설명된 대로 VPN 연결을 설정합니다.

이 구성에서 중요한 점은 ExpressRoute와 VPN 경로를 통한 온-프레미스 네트워크와 Azure 간의 라우팅입니다.

### <a name="traffic-from-on-premises-networks-to-azure"></a>온-프레미스 네트워크에서 Azure로의 트래픽

온-프레미스 네트워크에서 Azure로의 트래픽의 경우, Azure 접두사(가상 허브 및 허브에 연결된 모든 스포크 가상 네트워크 포함)는 ExpressRoute 개인 피어링 BGP와 VPN BGP를 통해 보급됩니다. 따라서 온-프레미스 네트워크에서 Azure로 향하는 두 개의 네트워크 라우팅(경로)이 생성됩니다.

- 하나는 IPsec으로 보호된 경로를 통해 생성
- 하나는 IPsec를 *보호하지 않고* ExpressRoute를 직접 통해 생성 

통신에 암호화를 적용하려면 다이어그램의 VPN 연결 네트워크에 대해 온-프레미스 VPN 게이트웨이를 통한 Azure 경로가 직접 ExpressRoute 경로보다 우선적으로 사용되도록 해야 합니다.

### <a name="traffic-from-azure-to-on-premises-networks"></a>Azure에서 온-프레미스 네트워크로의 트래픽

Azure에서 온-프레미스 네트워크로의 트래픽에는 동일한 요구 사항이 적용됩니다. IPsec 경로가 직접 ExpressRoute 경로(IPsec 없이)보다 우선적으로 사용되도록 하기 위해 다음 두 가지 옵션을 사용할 수 있습니다.

- VPN 연결 네트워크에 대한 VPN BGP 세션에서 보다 구체적인 접두사를 보급합니다. ExpressRoute 개인 피어링을 통해 VPN 연결 네트워크를 포함하는 더 큰 범위를 보급한 다음 VPN BGP 세션에서 더 구체적인 범위를 보급할 수 있습니다. 예를 들어, ExpressRoute를 통한 10.0.0.0/16 및 VPN을 통한 10.0.1.0/24를 보급합니다.

- VPN 및 ExpressRoute에 대한 분리형 접두사를 보급합니다. VPN 연결 네트워크 범위가 다른 ExpressRoute 연결 네트워크와 분리된 경우 VPN 및 ExpressRoute BGP 세션에서 각각 접두사를 보급할 수 있습니다. 예를 들어, ExpressRoute를 통한 10.0.0.0/24및 VPN을 통한 10.0.1.0/24를 보급합니다.

이러한 두 예제에서 Azure는 VPN 보호 없이 ExpressRoute를 직접 사용하는 대신 VPN 연결을 통해 10.0.1.0/24로 트래픽을 전송합니다.

> [!WARNING]
> ExpressRoute와 VPN 연결에 모두 *동일한* 접두사를 보급하는 경우 Azure는 VPN 보호 없이 직접 ExpressRoute 경로를 사용합니다.
>

## <a name="before-you-begin"></a>시작하기 전에

[!INCLUDE [Before you begin](../../includes/virtual-wan-tutorial-vwan-before-include.md)]

## <a name="1-create-a-virtual-wan-and-hub-with-gateways"></a><a name="openvwan"></a>1. 게이트웨이를 사용하여 가상 WAN 및 허브 만들기

진행하기 전에 다음 Azure 리소스 및 해당 온-프레미스 구성이 있어야 합니다.

- Azure Virtual WAN:
- [ExpressRoute 게이트웨이](virtual-wan-expressroute-portal.md) 및 [VPN 게이트웨이](virtual-wan-site-to-site-portal.md)를 사용하는 가상 WAN 허브

Azure Virtual WAN 및 ExpressRoute 연결을 사용하는 허브를 만드는 단계는 [Azure Virtual WAN을 사용하여 ExpressRoute 연결 만들기](virtual-wan-expressroute-portal.md)를 참조하세요. 가상 WAN에서 VPN 게이트웨이를 만드는 단계는 [Azure Virtual WAN을 사용하여 사이트 간 연결 만들기](virtual-wan-site-to-site-portal.md)를 참조하세요.

## <a name="2-create-a-site-for-the-on-premises-network"></a><a name="site"></a>2. 온-프레미스 네트워크에 대한 사이트 만들기

사이트 리소스는 가상 WAN에 대한 비 ExpressRoute VPN 사이트와 동일합니다. 이제 온-프레미스 VPN 디바이스의 IP 주소는 개인 IP 주소이거나, 1단계에서 만든 ExpressRoute 개인 피어링을 통해 연결할 수 있는 온-프레미스 네트워크의 공용 IP 주소일 수 있습니다.

> [!NOTE]
> 온-프레미스 VPN 디바이스의 IP 주소는 Azure ExpressRoute 개인 피어링을 통해 가상 WAN 허브에 보급된 주소 접두사에 *반드시* 포함되어야 합니다.
>

1. 브라우저에서 Azure Portal로 이동합니다. 
1. 앞에서 만든 허브를 선택합니다. 가상 WAN 허브 페이지의 **연결** 에서 **VPN 사이트** 를 선택합니다.
1. **VPN 사이트** 페이지에서 **+사이트 만들기** 를 선택합니다.
1. **사이트 만들기** 페이지에서 다음 필드를 채웁니다.
   * **구독**: 구독을 확인합니다.
   * **리소스 그룹**: 사용하려는 리소스 그룹을 선택하거나 만듭니다.
   * **지역**: VPN 사이트 리소스에 대한 Azure 지역을 입력합니다.
   * **이름**: 온-프레미스 사이트를 참조할 때 사용하려는 이름을 입력합니다.
   * **디바이스 공급업체**: 온-프레미스 VPN 디바이스의 공급업체를 입력합니다.
   * **Border Gateway Protocol**: 온-프레미스 네트워크에서 BGP를 사용하는 경우 "사용"을 선택합니다.
   * **프라이빗 주소 공간**: 온프레미스 사이트에 있는 IP 주소 공간을 입력합니다. 이 주소 공간으로 향하는 트래픽은 VPN 게이트웨이를 통해 온-프레미스 네트워크로 라우팅됩니다.
   * **허브**: 이 VPN 사이트를 연결하려면 허브를 하나 이상 선택합니다. 선택한 허브에는 VPN 게이트웨이가 이미 생성되어 있어야 합니다.
1. **다음: 링크 >** 를 선택하여 VPN 링크를 다음과 같이 설정합니다.
   * **링크 이름**: 이 연결을 참조할 이름입니다.
   * **공급자 이름**: 이 사이트에 대한 인터넷 서비스 공급자의 이름입니다. ExpressRoute 온-프레미스 네트워크의 경우 EExpressRoute 서비스 공급자의 이름입니다.
   * **속도**: 인터넷 서비스 링크 또는 ExpressRoute 회로의 속도입니다.
   * **공용 IP 주소**: 온-프레미스 사이트에 상주하는 VPN 디바이스의 공용 IP 주소입니다. 또는 온-프레미스 ExpressRoute의 경우 ExpressRoute를 통한 VPN 디바이스의 개인 IP 주소입니다.

   BGP를 사용하도록 설정한 경우 Azure에서 이 사이트에 대해 만들어진 모든 연결에 적용됩니다. 가상 WAN에서 BGP를 구성하는 것은 Azure VPN 게이트웨이에서 BGP를 구성하는 것과 같습니다. 
   
   온-프레미스 BGP 피어 주소는 디바이스에 대한 VPN의 IP 주소 또는 VPN 사이트의 가상 네트워크 주소 공간과 *동일하지 않아야 합니다*. VPN 디바이스에서 BGP 피어 IP에 다른 IP 주소를 사용하세요. 디바이스에서 루프백 인터페이스에 할당한 주소가 될 수 있습니다. 그러나 APIPA(169.254.*x*.*x*) 주소는 *안 됩니다*. 위치를 나타내는 해당 VPN 사이트에서 이 주소를 지정합니다. BGP 필수 조건은 [Azure VPN Gateway의 BGP 정보](../vpn-gateway/vpn-gateway-bgp-overview.md)를 참조하세요.

1. **다음: 검토 + 만들기 >** 를 선택하여 설정값을 확인하고 VPN 사이트를 만듭니다. 연결할 **허브** 를 선택한 경우 온 프레미스 네트워크와 허브 VPN 게이트웨이 간에 연결이 설정됩니다.

## <a name="3-update-the-vpn-connection-setting-to-use-expressroute"></a><a name="hub"></a>3. VPN 연결 설정을 업데이트하여 ExpressRoute 사용

VPN 사이트를 만들고 허브에 연결한 후에는 다음 단계를 따라 ExpressRoute 개인 피어링을 사용하도록 연결을 구성합니다.

1. 가상 WAN 리소스 페이지로 돌아가서 허브 리소스를 선택합니다. 또는 VPN 사이트에서 연결된 허브로 이동합니다.

   :::image type="content" source="./media/vpn-over-expressroute/hub-selection.png" alt-text="허브 선택":::
1. **연결** 에서 **VPN(사이트 간)** 을 선택합니다.

   :::image type="content" source="./media/vpn-over-expressroute/vpn-select.png" alt-text="VPN 선택(사이트 간)":::
1. ExpressRoute를 통해 VPN 사이트에서 줄임표 ( **...** )를 선택하고 **이 허브에 대한 VPN 연결 편집** 을 선택합니다.

   :::image type="content" source="./media/vpn-over-expressroute/config-menu.png" alt-text="구성 메뉴 입력":::
1. **Azure 개인 IP 주소 사용** 에 대해 **예** 를 선택합니다. 이 설정은 허브 VPN 게이트웨이가 공용 IP 주소 대신 이 연결에 대한 게이트웨이의 허브 주소 범위 내에 있는 개인 IP 주소를 사용하도록 구성합니다. 이렇게 하면 온-프레미스 네트워크의 트래픽이 이 VPN 연결에 공용 인터넷을 사용하는 대신 ExpressRoute 개인 피어링 경로를 통과하게 됩니다. 다음 스크린샷은 이 설정을 보여 줍니다.

   :::image type="content" source="./media/vpn-over-expressroute/vpn-link-configuration.png" alt-text="VPN 연결에 개인 IP 주소를 사용하도록 설정" border="false":::
1. **저장** 을 선택합니다.

변경 내용을 저장하면 허브 VPN 게이트웨이가 VPN 게이트웨이의 개인 IP 주소를 사용하여 ExpressRoute를 통해 온-프레미스 VPN 디바이스와의 IPsec/IKE 연결을 설정합니다.

## <a name="4-get-the-private-ip-addresses-for-the-hub-vpn-gateway"></a><a name="associate"></a>4. 허브 VPN 게이트웨이에 대한 개인 IP 주소 가져오기

VPN 디바이스 구성을 다운로드하여 허브 VPN 게이트웨이의 개인 IP 주소를 가져옵니다. 온-프레미스 VPN 디바이스를 구성하려면 이러한 주소가 필요합니다.

1. 허브에 대한 페이지의 **연결** 에서 **VPN(사이트 간)** 을 선택합니다.
1. **개요** 페이지의 맨 위에서 **VPN 구성 다운로드** 를 선택합니다. 

   Azure는 "microsoft-network-[location]," 리소스 그룹에 스토리지 계정을 만듭니다. 여기서 *location* 은 WAN의 위치입니다. VPN 디바이스에 구성을 적용한 후에는 이 스토리지 계정을 삭제할 수 있습니다.
1. 파일이 만들어지면 링크를 선택하여 다운로드합니다.
1. VPN 디바이스에 구성을 적용합니다.

### <a name="vpn-device-configuration-file"></a>VPN 디바이스 구성 파일

디바이스 구성 파일에는 온-프레미스 VPN 디바이스를 구성할 때 사용할 설정이 포함되어 있습니다. 이 파일을 볼 때 다음 정보를 확인합니다.

* **vpnSiteConfiguration**: 이 섹션은 가상 WAN에 연결되는 사이트로 설정된 디바이스 세부 정보를 나타냅니다. 여기에는 분기 디바이스의 이름 및 공용 IP 주소가 포함됩니다.
* **vpnSiteConnections**: 이 섹션에서는 다음 설정에 관한 정보를 제공합니다.

    * 가상 허브의 가상 네트워크 주소 공간입니다.<br/>예:
           ```
           "AddressSpace":"10.51.230.0/24"
           ```
    * 허브에 연결된 가상 네트워크의 주소 공간입니다.<br>예:
           ```
           "ConnectedSubnets":["10.51.231.0/24"]
            ```
    * 가상 허브의 VPN 게이트웨이 IP 주소입니다. VPN 게이트웨이의 각 연결은 활성-활성 구성에서 두 개 터널로 구성되므로 이 파일에 두 IP 주소가 모두 나열됩니다. 이 예제에서는 각 사이트에 대해 `Instance0` 및 `Instance1`이 표시되며 이들은 공용 IP 주소가 아닌 개인 IP 주소입니다.<br>예:
           ``` 
           "Instance0":"10.51.230.4"
           "Instance1":"10.51.230.5"
           ```
    * BGP 및 미리 공유한 키와 같은 VPN 게이트웨이 연결에 대한 구성 세부 정보입니다. 미리 공유한 키는 자동으로 생성됩니다. 사용자 지정 미리 공유한 키의 **개요** 페이지에서 연결을 언제든지 편집할 수 있습니다.
  
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

* VPN 디바이스 페이지의 지침은 가상 WAN에 대해 작성되지 않습니다. 그러나 구성 파일의 가상 WAN 값을 사용하여 VPN 디바이스를 수동으로 구성할 수 있습니다. 
* VPN 게이트웨이에 대해 다운로드 가능한 디바이스 구성 스크립트는 구성이 다르기 때문에 가상 WAN에서는 작동하지 않습니다.
* 새 가상 WAN은 IKEv1 및 IKEv2를 모두 지원할 수 있습니다.
* 가상 WAN은 경로 기반 VPN 디바이스와 디바이스 지침만 사용할 수 있습니다.

## <a name="5-view-your-virtual-wan"></a><a name="viewwan"></a>5. 가상 WAN 보기

1. 가상 WAN으로 이동합니다.
1. **개요** 페이지의 맵에 있는 각 점은 허브를 나타냅니다.
1. **허브 및 연결** 섹션에서 허브, 사이트, 지역 및 VPN 연결 상태를 볼 수 있습니다. 또한 바이트를 안팎으로 볼 수 있습니다.

## <a name="6-monitor-a-connection"></a><a name="connectmon"></a>6. 연결 모니터링

Azure VM(가상 머신)과 원격 사이트 간의 통신을 모니터링하는 연결을 만듭니다. 연결 모니터를 설정하는 방법에 대한 자세한 내용은 [네트워크 통신 모니터링](~/articles/network-watcher/connection-monitor.md)을 참조하세요. 원본 필드는 Azure의 VM IP이고 대상 IP는 사이트 IP입니다.

## <a name="7-clean-up-resources"></a><a name="cleanup"></a>7. 리소스 정리

이러한 리소스가 더 이상 필요하지 않은 경우 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)을 사용하여 리소스 그룹과 여기에 포함된 모든 리소스를 제거할 수 있습니다. 다음 PowerShell 명령을 실행하고 `myResourceGroup`을 리소스 그룹의 이름으로 바꿉니다.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>다음 단계

이 문서는 가상 WAN을 사용하여 ExpressRoute 개인 피어링을 통해 VPN 연결을 만드는 데 도움이 됩니다. 가상 WAN 및 관련 기능에 대해 자세히 알아보려면 [가상 WAN 개요](virtual-wan-about.md)를 참조하세요.
