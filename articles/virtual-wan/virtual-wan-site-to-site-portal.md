---
title: Azure Virtual WAN을 사용하여 Azure에 사이트 간 연결 만들기 | Microsoft Docs
description: 이 자습서에서는 Azure Virtual WAN을 사용하여 Azure에 사이트 간 VPN 연결을 만드는 방법을 알아봅니다.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 07/13/2018
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect my local site to my VNets using Virtual WAN and I don't want to go through a Virtual WAN partner.
ms.openlocfilehash: ea36a3d4a2471cee6a18d70275aaf2e83ffc6f39
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/19/2018
ms.locfileid: "39159654"
---
# <a name="tutorial-create-a-site-to-site-connection-using-azure-virtual-wan-preview"></a>자습서: Azure Virtual WAN을 사용하여 사이트 간 연결 만들기(미리 보기)

이 자습서에서는 가상 WAN을 사용하여 IPsec/IKE(IKEv2) VPN 연결을 통해 Azure에서 리소스를 연결하는 방법을 보여줍니다. 이 연결 유형은 할당된 외부 연결 공용 IP 주소를 갖고 있는 온-프레미스에 있는 VPN 장치를 필요로 합니다. 가상 WAN에 대한 자세한 내용은 [가상 WAN 개요](virtual-wan-about.md)를 참조하세요.

> [!NOTE]
> 사이트가 많은 경우 일반적으로 [가상 WAN 파트너](https://aka.ms/virtualwan)를 사용하여 이러한 구성을 만듭니다. 하지만 네트워킹에 익숙하고 자신의 VPN 장치를 구성하는 데 능숙한 경우에는 구성을 직접 만들 수 있습니다.
>

![Virtual WAN 다이어그램](./media/virtual-wan-about/virtualwan.png)

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * WAN 만들기
> * 사이트 만들기
> * 허브 만들기
> * 사이트에 허브 연결
> * 허브에 VNet 연결
> * VPN 장치 구성 다운로드 및 적용
> * 가상 WAN 보기
> * 리소스 상태 보기
> * 연결 모니터링

> [!IMPORTANT]
> Azure Virtual WAN은 현재 관리되는 공개 미리 보기입니다. 가상 WAN을 사용하려면 [미리 보기에 등록](#enroll)해야 합니다.
>
> 이 공개 미리 보기는 Service Level Agreement(서비스 수준 약정)없이 제공되므로 프로덕션 워크로드에 사용하지 말아야 합니다. 특정 기능은 지원되지 않을 수 있거나, 기능이 제한될 수 있거나 모든 Azure 위치에서 사용하지는 못할 수 있습니다. 자세한 내용은 [Microsoft Azure 미리 보기에 대한 보충 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="before-you-begin"></a>시작하기 전에

구성을 시작하기 전에 다음 기준을 충족하는지 확인합니다.

* IKEv2를 사용할 수 있는 호환 가능한 경로 기반 VPN 장치가 있고 이 장치를 구성할 수 있는 사람이 있어야 합니다. 가상 WAN 파트너 작업하는 경우 구성 설정이 자동으로 만들어지므로, 장치를 수동으로 구성하는 방법을 알아보느라 신경을 쓸 필요가 없습니다.
* VPN 장치에 대한 외부 연결 공용 IPv4 주소가 있는지 확인합니다. 이 IP 주소는 NAT 뒤에 배치할 수 없습니다.
* 연결하려는 가상 네트워크가 이미 있는 경우에는 온-프레미스 네트워크의 서브넷 중에 연결하려는 가상 네트워크와 겹치는 서브넷이 없는지 확인하십시오. 가상 네트워크에는 게이트웨이 서브넷이 필요하지 않으며 가상 네트워크 게이트웨이를 사용할 수 없습니다. 가상 네트워크가 없는 경우 이 문서에 있는 단계를 사용하여 만들 수 있습니다.
* 허브 지역의 IP 주소 범위를 확보합니다. 허브는 가상 네트워크이며 허브 영역에 지정하는 주소 범위는 사용자가 연결하는 기존 가상 네트워크와 겹칠 수 없습니다. 온-프레미스에 연결하는 주소 범위와도 겹칠 수 없습니다. 온-프레미스 네트워크에 있는 IP 주소 범위에 익숙하지 않은 경우 세부 정보를 제공할 수 있는 다른 사람의 도움을 받아야 합니다.
* Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="enroll"></a>1. 미리 보기에 등록

Virtual WAN을 구성하려면 그 전에 먼저 미리 보기에서 구독을 등록해야 합니다. 그렇지 않으면 포털에서 가상 WAN을 사용할 수 없습니다. 등록하려면 구독 ID로 **azurevirtualwan@microsoft.com**에 이메일을 전송합니다. 구독이 등록되면 이메일 회신을 받게 됩니다.

## <a name="vnet"></a>2. 가상 네트워크 만들기

VNet이 없으면 PowerShell을 사용하여 신속하게 만들 수 있습니다. Azure Portal을 사용하여 가상 네트워크를 만들 수도 있습니다.

* 사용자가 만든 VNet에 대한 주소 공간이 연결하려는 다른 VNet의 주소 범위 또는 온-프레미스 네트워크 주소 공간과 겹치지 않도록 해야 합니다. 
* VNet이 이미 있는 경우에는 필요한 조건을 충족하는지 가상 네트워크 게이트웨이가 없는지 확인합니다.

이 문서에서 "사용해 보세요."를 클릭하고 PowerShell 콘솔을 열어서 VNet을 손쉽게 만들 수 있습니다. 값을 조정한 다음, 명령을 복사하여 콘솔 창에 붙여 넣습니다.

### <a name="create-a-resource-group"></a>리소스 그룹 만들기

PowerShell 명령을 조정한 다음, 리소스 그룹을 만듭니다.

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName WANTestRG -Location WestUS
```

### <a name="create-a-vnet"></a>VNet 만들기

PowerShell 명령을 조정하여 사용자 환경에 호환되는 VNet을 만듭니다.

```azurepowershell-interactive
$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name FrontEnd -AddressPrefix "10.1.0.0/24"
$vnet   = New-AzureRmVirtualNetwork `
            -Name WANVNet1 `
            -ResourceGroupName WANTestRG `
            -Location WestUS `
            -AddressPrefix "10.1.0.0/16" `
            -Subnet $fesub1
```

## <a name="wan"></a>3. 가상 WAN 만들기

1. 브라우저에서 [Azure 포털](https://portal.azure.com) 로 이동하고 Azure 계정으로 로그인합니다.
2. 이 때는 **모든 서비스**로 이동하여 가상 WAN을 검색하면 가상 WAN을 찾을 수 있습니다. 또는 Azure Portal 맨 위에 있는 검색 상자에서 가상 WAN을 검색할 수 있습니다. **가상 WAN**을 클릭하여 페이지를 엽니다.
3. **만들기**를 클릭하여 **WAN 만들기** 페이지를 엽니다. 미리 보기에 대한 승인을 받지 않은 경우에는 페이지를 사용할 수 없습니다.

  ![WAN 만들기](./media/virtual-wan-site-to-site-portal/createwan.png)
4. WAN 만들기 페이지에서 다음 필드를 채웁니다.

  * **이름** - WAN을 호출할 이름을 선택합니다.
  * **구독** - 사용할 Azure 구독을 선택합니다.
  * **리소스 그룹** - 새로 만들거나 기존 항목을 사용합니다.
  * **리소스 위치** - 드롭다운에서 리소스 위치를 선택합니다. WAN은 전역 리소스이며 특정 지역에 상주하지 않습니다. 하지만 만든 WAN 리소스를 보다 쉽게 관리하고 찾으려면 지역을 선택해야 합니다.
5. **만들기**를 클릭하여 WAN을 만듭니다.

## <a name="site"></a>4. 사이트 만들기

물리적 위치에 해당하는 사이트를 필요한 만큼 만듭니다. 예를 들어 NY에 지사가 있고, 런던에 지사가 있고, LA에 지사가 있는 경우 3개의 사이트를 별도로 만들 수 있습니다. 이 사이트에는 온-프레미스 VPN 장치 엔드포인트가 포함됩니다. 이 때, 사이트에 대한 개인 주소 공간은 하나만 지정할 수 있습니다.

1. **모든 리소스**로 이동합니다.
2. 만든 가상 WAN을 클릭합니다.
3. 페이지 맨 위에 있는 **+사이트 만들기**를 클릭하여 **사이트 만들기** 페이지를 엽니다.

  ![새 사이트](media/virtual-wan-site-to-site-portal/createsite.png)
4. **사이트 만들기** 페이지에서 다음 필드를 채웁니다.

  *  **이름** - 온 - 프레미스 사이트를 참조할 때 사용하려는 이름입니다.
  *  **공용 IP 주소** - 온-프레미스 사이트에 상주하는 VPN 장치의 공용 IP 주소입니다.
  *  **개인 주소 공간** - 온-프레미스 사이트에 있는 IP 주소 공간입니다. 이 주소 공간으로 향하는 트래픽은 로컬 사이트로 라우팅됩니다.
  *  **구독** - 구독을 확인합니다.
  *  **리소스 그룹** - 사용하려는 리소스 그룹입니다.
5. **고급 표시**를 클릭하여 고급 설정을 표시합니다. **BGP를 사용하도록 설정**하면(선택적 필드), Azure에서 이 사이트에 대해 만들어진 모든 연결에서 이 기능을 사용하도록 설정됩니다. **장치 정보**(선택적 필드)를 입력할 수도 있습니다. 이렇게 하면 Azure 팀이 환경을 더 잘 이해하고 향후 최적화 가능성을 높이거나 문제를 해결하는 데 도움이 됩니다.

  ![BGP](media/virtual-wan-site-to-site-portal/sitebgp.png)
6. **확인**을 클릭하여 사이트를 만듭니다.
7. 만들려는 각 사이트에 대해 이 단계를 반복합니다.

## <a name="hub"></a>5. 허브 만들기 및 사이트 연결

1. 가상 WAN에 대한 페이지에서 **사이트**를 클릭합니다.
2. **연결되지 않은 사이트** 아래에 아직 허브에 연결하지 않은 사이트 목록이 표시됩니다.
3. 연결하려는 사이트를 선택합니다.
4. 드롭다운 목록에서 허브가 연결될 지역을 선택합니다. 연결하려는 VNet이 상주하는 지역에 허브를 연결해야 합니다.
5. **확인**을 클릭합니다. 이 지역에 허브가 아직 없으면 가상 허브 VNet이 자동으로 만들어집니다. 이런 경우 **지역 허브 만들기** 페이지가 나타납니다.
6. **지역 허브 만들기** 페이지에서 허브 VNet의 주소 범위를 입력합니다. 이 VNet은 허브 서비스를 포함할 VNet입니다. 여기에 입력하는 범위는 개인 IP 주소 범위여야 하며 온-프레미스 주소 공간이나 VNet 주소 공간과 겹치지 말아야 합니다. 후속 VPN 엔드포인트가 허브 VNet에 만들어집니다. (자동 허브 및 게이트웨이 만들기는 포털에서만 가능합니다.)
7. **만들기**를 클릭합니다.

## <a name="vnet"></a>6. 허브에 VNet 연결

이 단계에서는 허브와 VNet 간에 피어링 연결을 만듭니다. 연결하려는 각 VNet에 대해 이 단계를 반복합니다.

1. 가상 WAN에 대한 페이지에서 **가상 네트워크 연결**을 클릭합니다.
2. 가상 네트워크 연결 페이지에서 **+연결 추가**를 클릭합니다.
3. **연결 추가** 페이지에서 다음 필드를 채웁니다.

    * **연결 이름** - 연결의 이름을 지정합니다.
    * **허브** - 이 연결과 연결할 허브를 선택합니다.
    * **구독** - 구독을 확인합니다.
    * **가상 네트워크** - 이 허브에 연결할 가상 네트워크를 선택합니다. 가상 네트워크에는 기존의 가상 네트워크 게이트웨이를 사용할 수 없습니다.

## <a name="device"></a>7. VPN 구성 다운로드

VPN 장치 구성을 사용하여 온-프레미스 VPN 장치를 구성합니다.

1. 가상 WAN에 대한 페이지에서 **개요**를 클릭합니다.
2. 개요 페이지의 맨 위에서 **VPN 구성 다운로드**를 클릭합니다. Azure는 'microsoft-network-[location]' 리소스 그룹에 저장소 계정을 만듭니다. 여기서 위치는 WAN의 위치입니다. VPN 장치에 구성을 적용한 후에는 이 저장소 계정을 삭제할 수 있습니다.
3. 파일 만들기가 끝나면 링크를 클릭하여 다운로드할 수 있습니다.
4. VPN 장치에 구성을 적용합니다.

### <a name="understanding-the-vpn-device-configuration-file"></a>VPN 장치 구성 파일 이해

장치 구성 파일은 온-프레미스 VPN 장치를 구성할 때 사용할 설정을 포함합니다. 이 파일을 볼 때 다음 정보를 확인합니다.

* **vpnSiteConfiguration -** 이 섹션은 Virtual WAN에 연결된 사이트로 설정된 장치 정보를 나타냅니다. 여기에는 분기 장치의 이름 및 공용 IP 주소가 포함됩니다.
* **vpnSiteConnections -** 이 섹션은 다음 항목에 대한 정보를 제공합니다.

    * 가상 허브 VNet의 **주소 공간**<br>예제:
 
        ```
        "AddressSpace":"10.1.0.0/24"
        ```
    * 허브에 연결된 VNet의 **주소 공간**<br>예제:

         ```
        "ConnectedSubnets":["10.2.0.0/16","10.30.0.0/16"]
         ```
    * 가상 허브 vpngateway의 **IP 주소**. vpngateway에는 활성-활성 구성의 2개 터널로 구성된 각 연결이 있기 때문에 이 파일에 두 IP 주소가 모두 나열됩니다. 이 예제에서는 각 사이트에 대한 “Instance0” 및 “Instance1”이 표시됩니다.<br>예제:

        ``` 
        "Instance0":"104.45.18.186"
        "Instance1":"104.45.13.195"
        ```
    * BGP, 미리 공유한 키 등의 **Vpngateway 연결 구성 세부 정보**. PSK는 자동으로 생성되는 미리 공유한 키입니다. 사용자 지정 PSK의 개요 페이지에서 연결을 언제든지 편집할 수 있습니다.
  
### <a name="example-device-configuration-file"></a>예제 장치 구성 파일

  ```
  { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"r403583d-9c82-4cb8-8570-1cbbcd9983b5"
      },
      "vpnSiteConfiguration":{ 
         "Name":"testsite1",
         "IPAddress":"73.239.3.208"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe",
               "ConnectedSubnets":[ 
                  "10.2.0.0/16",
                  "10.30.0.0/16"
               ]
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.186",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"bkOWe5dPPqkx0DfFE3tyuP7y3oYqAEbI",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   },
   { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"1f33f891-e1ab-42b8-8d8c-c024d337bcac"
      },
      "vpnSiteConfiguration":{ 
         "Name":" testsite2",
         "IPAddress":"66.193.205.122"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe"
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.187",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"XzODPyAYQqFs4ai9WzrJour0qLzeg7Qg",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   },
   { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"cd1e4a23-96bd-43a9-93b5-b51c2a945c7"
      },
      "vpnSiteConfiguration":{ 
         "Name":" testsite3",
         "IPAddress":"182.71.123.228"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe"
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.187",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"YLkSdSYd4wjjEThR3aIxaXaqNdxUwSo9",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   }
  ```

### <a name="configuring-your-vpn-device"></a>VPN 장치 구성

>[!NOTE]
> 가상 WAN 파트너 솔루션을 사용하는 경우, 장치 컨트롤러가 Azure에서 구성 파일을 가져와서 장치에 적용하여 Azure에 대한 연결을 설정하는 위치에서 VPN 장치 구성이 자동으로 수행됩니다. 따라서 VPN 장치를 수동으로 구성하는 방법을 알 필요가 없습니다.
>

장치를 구성하는 지침이 필요한 경우에는 [VPN 장치 구성 스크립트 페이지](~/articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#configscripts)의 지침을 다음 주의 사항과 함께 사용하면 됩니다.

* VPN 장치 페이지의 지침은 가상 WAN용으로 작성되지 않았지만 구성 파일의 가상 WAN 값을 사용하여 VPN 장치를 수동으로 구성할 수 있습니다. 
* VPN Gateway용 다운로드 가능한 장치 구성 스크립트는 구성이 다르기 때문에 가상 WAN에서는 작동하지 않습니다.
* 가상 WAN은 IKEv2만 사용할 수 있고 IKEv1은 사용할 수 없습니다.
* 가상 WAN은 경로 기반 VPN 장치와 장치 지침만 사용할 수 있습니다.

## <a name="viewwan"></a>8. 가상 WAN 보기

1. 가상 WAN 탭으로 이동합니다.
2. 개요 페이지의 맵에 있는 각 점은 허브를 나타냅니다. 점 위로 마우스를 가져가면 허브 상태 요약을 볼 수 있습니다.
3. 허브 및 연결 섹션에서 허브 상태, 사이트, 지역, VPN 연결 상태 및 입/출력 바이트를 볼 수 있습니다.

## <a name="viewhealth"></a>9. 리소스 상태 보기

1. WAN으로 이동합니다.
2. WAN 페이지의 **지원 및 문제 해결** 섹션에서 **상태**를 클릭하고 리소스를 봅니다.

## <a name="connectmon"></a>10. 연결 모니터링

Azure VM과 원격 사이트 간의 통신을 모니터링하는 연결을 만듭니다. 연결 모니터를 설정하는 방법에 대한 자세한 내용은 [네트워크 통신 모니터링](~/articles/network-watcher/connection-monitor.md)을 참조하세요. 원본 필드는 Azure의 VM IP이고 대상 IP는 사이트 IP입니다.

## <a name="cleanup"></a>11. 리소스 정리

리소스가 더 이상 필요하지 않은 경우 [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup)을 사용하여 리소스 그룹 및 여기에 포함된 모든 리소스를 제거할 수 있습니다. "myResourceGroup"을 리소스 그룹의 이름으로 바꾸고 다음 PowerShell 명령을 실행합니다.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="feedback"></a>미리 보기 피드백

여러분의 의견에 감사드립니다. 문제를 보고하거나 Virtual WAN에 대한 피드백(긍정적이거나 부정적인)을 제공하려면 <azurevirtualwan@microsoft.com>에 메일을 보내세요. 제목 줄에서 “[]”에 회사 이름을 적어주세요. 문제를 보고하려는 경우 구독 ID도 보내주세요.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * WAN 만들기
> * 사이트 만들기
> * 허브 만들기
> * 사이트에 허브 연결
> * 허브에 VNet 연결
> * VPN 장치 구성 다운로드 및 적용
> * 가상 WAN 보기
> * 리소스 상태 보기
> * 연결 모니터링

가상 WAN에 대해 자세히 알아보려면 [가상 WAN 개요](virtual-wan-about.md) 페이지를 참조하세요.
