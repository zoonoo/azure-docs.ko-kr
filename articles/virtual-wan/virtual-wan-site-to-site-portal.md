---
title: 'Azure 가상 WAN: 사이트 간 연결 만들기'
description: 이 자습서에서는 azure 가상 WAN을 사용 하 여 Azure에 대 한 사이트 간 VPN 연결을 만드는 방법에 대해 알아봅니다.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect my local site to my VNets using Virtual WAN and I don't want to go through a Virtual WAN partner.
ms.openlocfilehash: b4278cb2e8c5152f522258a37c37acda5efbacf8
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78391161"
---
# <a name="tutorial-create-a-site-to-site-connection-using-azure-virtual-wan"></a>자습서: Azure 가상 WAN을 사용 하 여 사이트 간 연결 만들기

이 자습서에서는 가상 WAN을 사용 하 여 IPsec/IKE (IKEv1 및 IKEv2) VPN 연결을 통해 Azure에서 리소스에 연결 하는 방법을 보여 줍니다. 이 유형의 연결에는 외부에 연결 된 공용 IP 주소를 포함 하는 온-프레미스에 있는 VPN 장치가 필요 합니다. 가상 WAN에 대 한 자세한 내용은 [가상 Wan 개요](virtual-wan-about.md)를 참조 하세요.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 가상 WAN 만들기
> * 허브 만들기
> * 사이트 만들기
> * 허브에 사이트 연결
> * 허브에 VPN 사이트 연결
> * 허브에 VNet 연결
> * 구성 파일 다운로드
> * 가상 WAN 보기

> [!NOTE]
> 사이트가 많은 경우 일반적으로 [가상 WAN 파트너](https://aka.ms/virtualwan) 를 사용 하 여이 구성을 만듭니다. 그러나 고유한 VPN 장치를 구성 하는 방법에 대해 잘 알고 있는 경우에는이 구성을 직접 만들 수 있습니다.
>

![가상 WAN 다이어그램](./media/virtual-wan-about/virtualwan.png)

## <a name="before-you-begin"></a>시작 하기 전에

구성을 시작 하기 전에 다음 조건을 충족 하는지 확인 합니다.

* 연결 하려는 가상 네트워크가 있습니다. 온-프레미스 네트워크의 서브넷이 연결 하려는 가상 네트워크와 겹치지 않는지 확인 합니다. Azure Portal에서 가상 네트워크를 만들려면 [빠른](../virtual-network/quick-create-portal.md)시작을 참조 하세요.

* 가상 네트워크에 가상 네트워크 게이트웨이가 없습니다. 가상 네트워크에 게이트웨이 (VPN 또는 Express 경로)가 있는 경우 모든 게이트웨이를 제거 해야 합니다. 이 구성을 사용 하려면 가상 네트워크를 가상 WAN 허브 게이트웨이에 대신 연결 해야 합니다.

* 허브 지역의 IP 주소 범위를 가져옵니다. 허브는 가상 WAN에서 만들고 사용 하는 가상 네트워크입니다. 허브에 대해 지정 하는 주소 범위는 연결 하는 기존 가상 네트워크와 겹칠 수 없습니다. 또한 온-프레미스에 연결 하는 주소 범위와 겹칠 수 없습니다. 온-프레미스 네트워크 구성에 있는 IP 주소 범위에 익숙하지 않은 경우 해당 세부 정보를 제공할 수 있는 사용자와 조정 합니다.

* Azure 구독이 없는 경우 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="openvwan"></a>가상 WAN 만들기

브라우저에서 Azure Portal로 이동 하 여 Azure 계정으로 로그인 합니다.

1. 가상 WAN 페이지로 이동 합니다. 포털에서 **+ 리소스 만들기**를 클릭 합니다. 검색 상자에 **가상 WAN** 을 입력 하 고 Enter 키를 선택 합니다.
2. 결과에서 **가상 WAN** 을 선택 합니다. 가상 WAN 페이지에서 **만들기** 를 클릭 하 여 WAN 만들기 페이지를 엽니다.
3. **WAN 만들기** 페이지의 **기본 사항** 탭에서 다음 필드를 입력 합니다.

   ![가상 WAN](./media/virtual-wan-site-to-site-portal/vwan.png)

   * **구독** -사용 하려는 구독을 선택 합니다.
   * **리소스 그룹** -새로 만들거나 기존를 사용 합니다.
   * **리소스 그룹 위치** -드롭다운에서 리소스 위치를 선택 합니다. WAN은 글로벌 리소스 이며 특정 지역에 거주 하지 않습니다. 그러나 만든 WAN 리소스를 보다 쉽게 관리 하 고 찾기 위해 지역을 선택 해야 합니다.
   * **이름** -WAN에 호출 하려는 이름을 입력 합니다.
   * **유형:** 기본 또는 표준입니다. 기본 WAN을 만드는 경우 기본 허브만 만들 수 있습니다. 기본 허브는 VPN 사이트 간 연결만 가능 합니다.
4. 필드 입력을 완료 한 후 **검토 + 만들기**를 선택 합니다.
5. 유효성 검사가 통과 되 면 **만들기** 를 선택 하 여 가상 WAN을 만듭니다.

## <a name="hub"></a>허브 만들기

허브는 사이트 간, Express 경로 또는 지점 및 사이트 간 기능을 위한 게이트웨이를 포함할 수 있는 가상 네트워크입니다. 허브를 만든 후에는 사이트를 연결 하지 않는 경우에도 허브에 대 한 요금이 청구 됩니다. 가상 허브에서 사이트 간 VPN 게이트웨이를 만드는 데 30 분이 소요 됩니다.

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-s2s-hub-include.md)]

## <a name="site"></a>사이트 만들기

이제 실제 위치에 해당 하는 사이트를 만들 준비가 되었습니다. 실제 위치에 해당 하는 사이트를 필요한 만큼 만듭니다. 예를 들어 지사에 지사, 런던, 지사 및 LA가 있는 경우 별도의 3 개 사이트를 만들 수 있습니다. 이러한 사이트에는 온-프레미스 VPN 장치 끝점이 포함 되어 있습니다. 가상 WAN에서 가상 허브 당 최대 1000 개의 사이트를 만들 수 있습니다. 허브가 여러 개 있는 경우 각 허브 당 1000을 만들 수 있습니다. 가상 WAN 파트너 (링크 삽입) CPE 장치가 있는 경우 Azure에 대 한 자동화에 대 한 자세한 내용을 확인 하세요. 일반적으로 automation은 대규모 분기 정보를 azure로 내보내고 CPE에서 Azure 가상 WAN VPN 게이트웨이로의 연결을 설정 하는 간단한 클릭 환경을 의미 합니다. 자세한 내용은 [Azure에서 CPE 파트너에 대 한 Automation 지침](virtual-wan-configure-automation-providers.md)을 참조 하세요.

[!INCLUDE [Create a site](../../includes/virtual-wan-tutorial-s2s-site-include.md)]

## <a name="connectsites"></a>허브에 VPN 사이트 연결

이 단계에서는 VPN 사이트를 허브에 연결 합니다.

[!INCLUDE [Connect VPN sites](../../includes/virtual-wan-tutorial-s2s-connect-vpn-site-include.md)]

## <a name="vnet"></a>VNet을 허브에 연결

이 단계에서는 허브와 VNet 간에 연결을 만듭니다. 연결 하려는 각 VNet에 대해 이러한 단계를 반복 합니다.

1. 가상 WAN에 대 한 페이지에서 **가상 네트워크 연결**을 클릭 합니다.
2. 가상 네트워크 연결 페이지에서 **+ 연결 추가**를 클릭 합니다.
3. **연결 추가** 페이지에서 다음 필드를 입력 합니다.

    * **연결 이름** -연결 이름을로 합니다.
    * **허브** -이 연결과 연관 시킬 허브를 선택 합니다.
    * **구독** -구독을 확인 합니다.
    * **가상 네트워크** -이 허브에 연결 하려는 가상 네트워크를 선택 합니다. 가상 네트워크에 기존 가상 네트워크 게이트웨이가 있을 수 없습니다.
4. **확인** 을 클릭 하 여 가상 네트워크 연결을 만듭니다.

## <a name="device"></a>VPN 구성 다운로드

VPN 장치 구성을 사용 하 여 온-프레미스 VPN 장치를 구성 합니다.

1. 가상 WAN에 대 한 페이지에서 **개요**를 클릭 합니다.
2. **허브 > VPNSite** 페이지의 맨 위에서 **VPN 구성 다운로드**를 클릭 합니다. Azure는 ' microsoft-network-[location] ' 리소스 그룹에 저장소 계정을 만듭니다. 여기서 location은 WAN의 위치입니다. VPN 장치에 구성을 적용 한 후에는이 저장소 계정을 삭제할 수 있습니다.
3. 파일 만들기가 완료 되 면 링크를 클릭 하 여 다운로드할 수 있습니다.
4. 온-프레미스 VPN 장치에 구성을 적용 합니다.

### <a name="understanding-the-vpn-device-configuration-file"></a>VPN 장치 구성 파일 이해

장치 구성 파일에는 온-프레미스 VPN 장치를 구성할 때 사용할 설정이 포함 되어 있습니다. 이 파일을 볼 때 다음 정보를 확인 합니다.

* **vpnSiteConfiguration-** 이 섹션에서는 가상 WAN에 연결 하는 사이트로 설정 된 장치 세부 정보를 나타냅니다. 여기에는 분기 장치의 이름 및 공용 ip 주소가 포함 됩니다.
* **vpnSiteConnections-** 이 섹션에서는 다음 설정에 대 한 정보를 제공 합니다.

    * 가상 허브 VNet의 **주소 공간**<br>예 들어
 
        ```
        "AddressSpace":"10.1.0.0/24"
        ```
    * 허브에 연결 된 Vnet의 **주소 공간**<br>예 들어

         ```
        "ConnectedSubnets":["10.2.0.0/16","10.3.0.0/16"]
         ```
    * 가상 허브 vpngateway의 **IP 주소** 입니다. Vpngateway의 각 연결은 활성-활성 구성에서 두 개의 터널로 구성 되므로이 파일에 나열 된 두 IP 주소가 모두 표시 됩니다. 이 예제에는 각 사이트에 대 한 "Instance0" 및 "Instance1"가 표시 됩니다.<br>예 들어

        ``` 
        "Instance0":"104.45.18.186"
        "Instance1":"104.45.13.195"
        ```
    * BGP, 미리 공유한 키 등의 **Vpngateway 연결 구성 세부 정보** PSK는 자동으로 생성 되는 미리 공유한 키입니다. 사용자 지정 PSK의 개요 페이지에서 언제 든 지 연결을 편집할 수 있습니다.
  
### <a name="example-device-configuration-file"></a>장치 구성 파일의 예

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
                  "10.3.0.0/16"
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
> 가상 WAN 파트너 솔루션을 사용 하는 경우 VPN 장치 구성이 자동으로 수행 됩니다. 장치 컨트롤러는 Azure에서 구성 파일을 가져와 Azure에 대 한 연결을 설정 하는 장치에 적용 됩니다. 즉, VPN 장치를 수동으로 구성 하는 방법을 알 필요가 없습니다.
>

장치를 구성 하는 지침이 필요한 경우 [VPN 장치 구성 스크립트 페이지](~/articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#configscripts) 의 지침을 사용 하 여 다음과 같은 주의 사항이 있습니다.

* VPN 장치 페이지의 지침은 가상 WAN 용으로 작성 되지 않지만 구성 파일의 가상 WAN 값을 사용 하 여 VPN 장치를 수동으로 구성할 수 있습니다. 
* VPN Gateway에 대 한 다운로드 가능한 장치 구성 스크립트는 구성이 다르므로 가상 WAN에 대해 작동 하지 않습니다.
* 새 가상 WAN은 IKEv1와 IKEv2를 모두 지원할 수 있습니다.
* 가상 WAN은 정책 기반 및 경로 기반 VPN 장치 및 장치 지침을 모두 사용할 수 있습니다.

## <a name="viewwan"></a>가상 WAN 보기

1. 가상 WAN으로 이동 합니다.
2. **개요** 페이지에서 지도의 각 점은 허브를 나타냅니다. 지점 위로 마우스를 가져가면 허브 상태 요약, 연결 상태 및 바이트 수를 볼 수 있습니다.
3. 허브 및 연결 섹션에서 허브 상태, VPN 사이트 등을 볼 수 있습니다. 특정 허브 이름을 클릭 하 고 VPN 사이트로 이동 하 여 자세한 내용을 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

가상 WAN에 대해 자세히 알아보려면 [가상 Wan 개요](virtual-wan-about.md) 페이지를 참조 하세요.
