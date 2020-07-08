---
title: 'Express 경로 암호화 구성: Azure 가상 WAN에 대 한 Express 경로를 통한 IPsec'
description: 이 자습서에서는 Azure 가상 WAN을 사용 하 여 Express 경로 개인 피어 링을 통해 사이트 간 VPN 연결을 만드는 방법에 대해 알아봅니다.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 03/19/2020
ms.author: cherylmc
ms.openlocfilehash: d3a2c85540a4efa2f934605c4c056b458e2879ca
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84749593"
---
# <a name="expressroute-encryption-ipsec-over-expressroute-for-virtual-wan"></a>Express 경로 암호화: 가상 WAN을 위한 Express 경로를 통한 IPsec

이 문서에서는 azure 가상 WAN을 사용 하 여 Azure Express 경로 회로의 개인 피어 링을 통해 온-프레미스 네트워크에서 Azure로 IPsec/IKE VPN 연결을 설정 하는 방법을 보여 줍니다. 이 기술은 공용 인터넷을 사용 하거나 공용 IP 주소를 사용 하지 않고도 Express 경로를 통해 온-프레미스 네트워크와 Azure 가상 네트워크 간에 암호화 된 전송을 제공할 수 있습니다.

## <a name="topology-and-routing"></a>토폴로지 및 라우팅

다음 다이어그램에서는 Express 경로 개인 피어 링을 통한 VPN 연결의 예를 보여 줍니다.

![Express 경로를 통한 VPN](./media/vpn-over-expressroute/vwan-vpn-over-er.png)

이 다이어그램에서는 Express 경로 개인 피어 링을 통해 Azure 허브 VPN gateway에 연결 된 온-프레미스 네트워크 내의 네트워크를 보여 줍니다. 연결 설정은 다음과 같이 간단 합니다.

1. Express 경로 회로와 개인 피어 링을 사용 하 여 Express 경로 연결을 설정 합니다.
2. 이 문서에 설명 된 대로 VPN 연결을 설정 합니다.

이 구성의 중요 한 측면은 Express 경로와 VPN 경로를 통해 온-프레미스 네트워크와 Azure 간의 라우팅입니다.

### <a name="traffic-from-on-premises-networks-to-azure"></a>온-프레미스 네트워크에서 Azure로의 트래픽

온-프레미스 네트워크에서 Azure로의 트래픽에 대해 Azure 접두사 (허브에 연결 된 가상 허브 및 모든 스포크 가상 네트워크 포함)는 Express 경로 개인 피어 링 BGP와 VPN BGP를 통해 보급 됩니다. 그러면 온-프레미스 네트워크에서 Azure에 대 한 두 개의 네트워크 경로 (경로)가 생성 됩니다.

- IPsec으로 보호 된 경로에 대 한 하나
- IPsec 보호를 사용 *하지 않는* express 경로를 통해 직접 

통신에 암호화를 적용 하려면 다이어그램에서 VPN 연결 네트워크에 대해 온-프레미스 VPN gateway를 통한 Azure 경로가 직접 Express 경로 보다 우선적으로 사용 되도록 해야 합니다.

### <a name="traffic-from-azure-to-on-premises-networks"></a>Azure에서 온-프레미스 네트워크로의 트래픽

Azure에서 온-프레미스 네트워크로의 트래픽에는 동일한 요구 사항이 적용 됩니다. Ipsec 경로가 직접 Express 경로 경로 (IPsec 없음) 보다 선호 되도록 하려면 다음 두 가지 옵션을 사용할 수 있습니다.

- VPN에 연결 된 네트워크에 대 한 VPN BGP 세션에서 더 많은 특정 접두사를 보급 합니다. Express 경로 개인 피어 링을 통해 VPN에 연결 된 네트워크를 포함 하는 더 큰 범위를 보급 하 고 VPN BGP 세션에서 더 구체적인 범위를 확인할 수 있습니다. 예를 들어 Express 경로에 대해 10.0.0.0/16을, VPN을 통해 10.0.1.0/24를 보급 합니다.

- VPN 및 Express 경로에 대 한 비연속 접두사를 보급 합니다. VPN에 연결 된 네트워크 범위가 다른 Express 경로 연결 네트워크와 분리 된 경우 VPN 및 Express 경로 BGP 세션에서 각각 접두사를 보급할 수 있습니다. 예를 들어 Express 경로를 통해 10.0.0.0/24를, VPN을 통해 10.0.1.0/24를 보급 합니다.

이러한 두 예제에서 Azure는 vpn 보호 없이 Express 경로를 사용 하는 대신 VPN 연결을 통해 10.0.1.0/24로 트래픽을 전송 합니다.

> [!WARNING]
> Express 경로와 VPN 연결 모두에 *동일한* 접두사를 보급 하는 경우 AZURE는 vpn 보호 없이 직접 express 경로 경로를 사용 합니다.
>

## <a name="before-you-begin"></a>시작하기 전에

[!INCLUDE [Before you begin](../../includes/virtual-wan-tutorial-vwan-before-include.md)]

## <a name="1-create-a-virtual-wan-and-hub-with-gateways"></a><a name="openvwan"></a>1. 게이트웨이를 사용 하 여 가상 WAN 및 허브 만들기

진행 하기 전에 다음 Azure 리소스 및 해당 온-프레미스 구성이 준비 되어 있어야 합니다.

- Azure 가상 WAN
- [Express 경로 게이트웨이](virtual-wan-expressroute-portal.md) 및 [VPN gateway](virtual-wan-site-to-site-portal.md) 를 사용 하는 가상 WAN 허브

Azure 가상 WAN 및 Express 경로 연결을 사용 하는 허브를 만드는 단계는 [Azure 가상 wan을 사용 하 여 express 경로 연결 만들기](virtual-wan-expressroute-portal.md)를 참조 하세요. 가상 WAN에서 VPN gateway를 만드는 단계는 [Azure 가상 wan을 사용 하 여 사이트 간 연결 만들기](virtual-wan-site-to-site-portal.md)를 참조 하세요.

## <a name="2-create-a-site-for-the-on-premises-network"></a><a name="site"></a>2. 온-프레미스 네트워크에 대 한 사이트 만들기

사이트 리소스는 가상 WAN에 대 한 비 비트 VPN 사이트와 동일 합니다. 온-프레미스 VPN 장치의 IP 주소는 이제 1 단계에서 만든 Express 경로 개인 피어 링을 통해 연결할 수 있는 온-프레미스 네트워크의 공용 IP 주소 또는 개인 IP 주소일 수 있습니다.

> [!NOTE]
> 온-프레미스 VPN 장치의 IP 주소는 Azure Express 경로 개인 피어 링을 통해 가상 WAN 허브에 보급 된 주소 접두사의 일부 *여야 합니다* .
>

1. 브라우저에서 Azure Portal로 이동 합니다. 
1. 만든 WAN을 선택 합니다. WAN 페이지의 **연결**에서 **VPN 사이트**를 선택 합니다.
1. **VPN 사이트** 페이지에서 **+ 사이트 만들기**를 선택 합니다.
1. **사이트 만들기** 페이지에서 다음 필드를 채웁니다.
   * **구독**: 구독을 확인 합니다.
   * **리소스 그룹**: 사용 하려는 리소스 그룹을 선택 하거나 만듭니다.
   * **지역**: VPN 사이트 리소스에 대 한 Azure 지역을 입력 합니다.
   * **이름**: 온-프레미스 사이트를 참조 하는 데 사용할 이름을 입력 합니다.
   * **장치 공급 업체**: 온-프레미스 VPN 장치의 공급 업체를 입력 합니다.
   * **Border Gateway Protocol**: 온-프레미스 네트워크에서 BGP를 사용 하는 경우 "사용"을 선택 합니다.
   * **개인 주소 공간**: 온-프레미스 사이트에 있는 IP 주소 공간을 입력 합니다. 이 주소 공간으로 향하는 트래픽은 VPN gateway를 통해 온-프레미스 네트워크로 라우팅됩니다.
   * **허브**:이 VPN 사이트를 연결 하려면 허브를 하나 이상 선택 하세요. 선택한 허브에는 이미 만들어져 있는 VPN 게이트웨이가 있어야 합니다.
1. 다음: VPN 링크 설정에 대 한 **>링크** 를 선택 합니다.
   * **링크 이름**:이 연결을 참조 하는 데 사용할 이름입니다.
   * **공급자 이름**:이 사이트에 대 한 인터넷 서비스 공급자의 이름입니다. Express 경로 온-프레미스 네트워크의 경우 Express 경로 서비스 공급자의 이름입니다.
   * **속도**: 인터넷 서비스 링크 또는 express 경로 회로의 속도입니다.
   * **IP 주소**: 온-프레미스 사이트에 있는 VPN 장치의 공용 IP 주소입니다. 또는 온-프레미스 Express 경로의 경우 Express 경로를 통한 VPN 장치의 개인 IP 주소입니다.

   BGP를 사용 하는 경우 Azure에서이 사이트에 대해 만들어진 모든 연결에 적용 됩니다. 가상 WAN에서 BGP를 구성 하는 것은 Azure VPN gateway에서 BGP를 구성 하는 것과 같습니다. 
   
   온-프레미스 BGP 피어 주소는 장치에 대 한 VPN의 IP 주소 또는 VPN 사이트의 가상 네트워크 주소 공간과 같지 *않아야 합니다.* VPN 디바이스에서 BGP 피어 IP에 다른 IP 주소를 사용하세요. 디바이스에서 루프백 인터페이스에 할당한 주소가 될 수 있습니다. 그러나 APIPA (169.254) 일 수 *없습니다* .* x*. *x*) 주소입니다. 위치를 나타내는 해당 로컬 네트워크 게이트웨이에서이 주소를 지정 합니다. BGP 필수 조건은 [Azure VPN Gateway의 BGP 정보](../vpn-gateway/vpn-gateway-bgp-overview.md)를 참조하세요.

1. **다음: 검토 + >만들기** 를 선택 하 여 설정 값을 확인 하 고 VPN 사이트를 만듭니다. **허브** 를 선택 하 여 연결 하는 경우 온-프레미스 네트워크와 허브 VPN gateway 간에 연결이 설정 됩니다.

## <a name="3-update-the-vpn-connection-setting-to-use-expressroute"></a><a name="hub"></a>3. Express 경로를 사용 하도록 VPN 연결 설정 업데이트

VPN 사이트를 만들고 허브에 연결한 후에는 다음 단계를 사용 하 여 Express 경로 개인 피어 링을 사용 하도록 연결을 구성 합니다.

1. 가상 WAN 리소스 페이지로 돌아가서 허브 리소스를 선택 합니다. 또는 VPN 사이트에서 연결 된 허브로 이동 합니다.
1. **연결**에서 **VPN (사이트 간)** 을 선택 합니다.
1. Express 경로를 통해 VPN 사이트에서 줄임표 (**...**)를 선택 하 고 **이 허브에 대 한 vpn 연결 편집**을 선택 합니다.
1. **Azure 개인 IP 주소 사용**에 대해 **예**를 선택 합니다. 설정은 공용 IP 주소가 아닌이 연결에 대해 게이트웨이의 허브 주소 범위 내에서 개인 IP 주소를 사용 하도록 허브 VPN gateway를 구성 합니다. 이렇게 하면 온-프레미스 네트워크의 트래픽이이 VPN 연결에 공용 인터넷을 사용 하는 대신 Express 경로 개인 피어 링 경로를 통과 하 게 됩니다. 다음 스크린샷에서는 설정을 보여 줍니다.

   ![VPN 연결에 개인 IP 주소를 사용 하도록 설정](./media/vpn-over-expressroute/vpn-link-configuration.png)
   
1. **저장**을 선택합니다.

변경 내용을 저장 한 후에는 허브 VPN gateway에서 VPN 게이트웨이의 개인 IP 주소를 사용 하 여 Express 경로를 통해 온-프레미스 VPN 장치와의 IPsec/IKE 연결을 설정 합니다.

## <a name="4-get-the-private-ip-addresses-for-the-hub-vpn-gateway"></a><a name="associate"></a>4. 허브 VPN gateway에 대 한 개인 IP 주소 가져오기

VPN 장치 구성을 다운로드 하 여 허브 VPN 게이트웨이의 개인 IP 주소를 가져옵니다. 온-프레미스 VPN 장치를 구성 하려면 이러한 주소가 필요 합니다.

1. 허브에 대 한 페이지의 **연결**에서 **VPN (사이트 간)** 을 선택 합니다.
1. **개요** 페이지의 맨 위에서 **VPN 구성 다운로드**를 선택 합니다. 

   Azure는 리소스 그룹 "microsoft-네트워크-[location]"에 저장소 계정을 만듭니다. 여기서 *location* 은 WAN의 위치입니다. VPN 장치에 구성을 적용 한 후에는이 저장소 계정을 삭제할 수 있습니다.
1. 파일이 만들어진 후 링크를 선택 하 여 다운로드 합니다.
1. VPN 디바이스에 구성을 적용합니다.

### <a name="vpn-device-configuration-file"></a>VPN 장치 구성 파일

장치 구성 파일에는 온-프레미스 VPN 장치를 구성할 때 사용할 설정이 포함 되어 있습니다. 이 파일을 볼 때 다음 정보를 확인합니다.

* **vpnSiteConfiguration**:이 섹션에서는 가상 WAN에 연결 하는 사이트로 설정 된 장치 세부 정보를 나타냅니다. 여기에는 분기 장치의 이름 및 공용 IP 주소가 포함 됩니다.
* **vpnSiteConnections**:이 섹션에서는 다음 설정에 대 한 정보를 제공 합니다.

    * 가상 허브의 가상 네트워크에 대 한 주소 공간입니다.<br/>예:
           ```
           "AddressSpace":"10.51.230.0/24"
           ```
    * 허브에 연결 된 가상 네트워크의 주소 공간입니다.<br>예:
           ```
           "ConnectedSubnets":["10.51.231.0/24"]
            ```
    * 가상 허브의 VPN 게이트웨이의 IP 주소입니다. VPN 게이트웨이의 각 연결은 활성-활성 구성에서 두 개의 터널로 구성 되므로이 파일에 나열 된 두 IP 주소가 모두 표시 됩니다. 이 예제에서는 `Instance0` `Instance1` 각 사이트에 대해 및를 확인 하 고 공용 ip 주소 대신 개인 ip 주소입니다.<br>예:
           ``` 
           "Instance0":"10.51.230.4"
           "Instance1":"10.51.230.5"
           ```
    * BGP 및 미리 공유한 키와 같은 VPN gateway 연결에 대 한 구성 정보입니다. 미리 공유한 키가 자동으로 생성 됩니다. 언제 든 지 사용자 지정 미리 공유한 키에 대 한 **개요** 페이지에서 연결을 편집할 수 있습니다.
  
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

* VPN 장치 페이지의 지침은 가상 WAN에 대해 작성 되지 않습니다. 그러나 구성 파일의 가상 WAN 값을 사용 하 여 VPN 장치를 수동으로 구성할 수 있습니다. 
* VPN gateway에 대 한 다운로드 가능한 장치 구성 스크립트가 가상 WAN에 대해 작동 하지 않습니다. 구성이 다릅니다.
* 새 가상 WAN은 IKEv1와 IKEv2를 모두 지원할 수 있습니다.
* 가상 WAN은 경로 기반 VPN 장치 및 장치 명령만 사용할 수 있습니다.

## <a name="5-view-your-virtual-wan"></a><a name="viewwan"></a>5. 가상 WAN 보기

1. 가상 WAN으로 이동 합니다.
1. **개요** 페이지에서 지도의 각 점은 허브를 나타냅니다.
1. 허브 **및 연결** 섹션에서 허브, 사이트, 지역 및 VPN 연결 상태를 볼 수 있습니다. 바이트 및 출력을 볼 수도 있습니다.

## <a name="7-monitor-a-connection"></a><a name="connectmon"></a>7. 연결 모니터링

Azure VM (가상 머신)과 원격 사이트 간의 통신을 모니터링 하는 연결을 만듭니다. 연결 모니터를 설정하는 방법에 대한 자세한 내용은 [네트워크 통신 모니터링](~/articles/network-watcher/connection-monitor.md)을 참조하세요. 원본 필드는 Azure의 VM IP 이며 대상 IP는 사이트 IP입니다.

## <a name="8-clean-up-resources"></a><a name="cleanup"></a>8. 리소스 정리

이러한 리소스가 더 이상 필요 하지 않은 경우 [AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 를 사용 하 여 리소스 그룹 및 포함 된 모든 리소스를 제거할 수 있습니다. 다음 PowerShell 명령을 실행 하 고을 `myResourceGroup` 리소스 그룹의 이름으로 바꿉니다.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>다음 단계

이 문서는 가상 WAN을 사용 하 여 Express 경로 개인 피어 링을 통해 VPN 연결을 만드는 데 도움이 됩니다. 가상 WAN 및 관련 기능에 대해 자세히 알아보려면 [가상 wan 개요](virtual-wan-about.md)를 참조 하세요.
