---
title: Azure Virtual WAN 자동화 구성 - Virtual WAN 파트너용 | Microsoft Docs
description: 이 문서는 소프트웨어 정의 연결 솔루션 파트너가 Azure Virtual WAN 자동화를 설정하는 데 도움이 됩니다.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 08/23/2018
ms.author: cherylmc
Customer intent: As a Virtual WAN software-defined connectivity provider, I want to set up a provisioning environment.
ms.openlocfilehash: bac728f286c90550107b27da76a070623577ed82
ms.sourcegitcommit: ebb460ed4f1331feb56052ea84509c2d5e9bd65c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2018
ms.locfileid: "42918903"
---
# <a name="configure-virtual-wan-automation---for-virtual-wan-partners-preview"></a>Virtual WAN 자동화 구성 - Virtual WAN 파트너용(미리 보기)

이 문서는 Azure Virtual WAN의 분기 장치(고객 온-프레미스 VPN 장치 또는 SDWAN)를 연결 및 구성하기 위해 자동화 환경을 설정하는 방법을 이해하는 데 도움이 됩니다. 이 문서는 IPsec/IKEv2를 통해 VPN 연결을 수용할 수 있는 분기 장치를 제공하는 공급자를 대상으로 합니다.

일반적으로 소프트웨어 정의 연결 솔루션은 컨트롤러 또는 장치 프로비저닝 센터를 사용하여 해당 분기 장치를 관리합니다. 컨트롤러는 Azure API를 사용하여 Azure Virtual WAN에 대한 연결을 자동화할 수 있습니다. 이 연결 유형을 사용하려면 할당된 외부 연결 공용 IP 주소를 가진 온-프레미스에 있는 SDWAN 또는 VPN 장치가 필요합니다.

##  <a name="access"></a>액세스 제어

고객은 장치 UI에서 Virtual WAN에 적절한 액세스 제어를 설정할 수 있어야 합니다. 이 경우, Azure 서비스 주체를 사용하는 것이 좋습니다. 서비스 주체 기반 액세스는 분기 정보 업로드에 적합한 인증을 장치 컨트롤러에 제공합니다. 자세한 내용은 [서비스 주체 만들기](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application)를 참조하세요.

##  <a name="site"></a>분기 정보 업로드

Azure에 분기(온-프레미스 사이트) 정보를 업로드하도록 사용자 환경을 디자인합니다. **VPNSite**의[REST API](https://docs.microsoft.com/rest/api/virtualwan/vpnsites)를 사용하여 Virtual WAN에서 사이트 정보를 작성할 수 있습니다. 모든 분기 SDWAN/VPN 장치를 제공하거나 적절하게 장치 사용자 지정을 선택할 수 있습니다.

##  <a name="hub"></a>허브 및 서비스

분기 장치가 Azure에 업로드된 후 고객은 일반적으로 Azure Portal에서 허브 지역 및/또는 서비스를 선택합니다. 그러면 허브 가상 네트워크 및 허브 내의 VPN 끝점을 만드는 작업 집합이 호출됩니다. VPN 게이트웨이는 대역폭 및 연결 요구 사항에 따라 적절하게 크기가 조정되는 확장 가능한 게이트웨이입니다.

## <a name="device"></a>장치 구성

이 단계에서는 공급자를 사용하지 않는 고객은 수동으로 Azure 구성을 다운로드하고 온-프레미스 SDWAN/VPN 장치에 적용합니다. 공급자는 이 단계를 자동화해야 합니다. 컨트롤러는 **GetVpnConfiguration** REST API를 호출하여 일반적으로 다음 파일과 비슷하게 표시되는 Azure 구성을 다운로드할 수 있습니다.

**구성 정보**

  * 가상 허브에 연결된 Azure VNet은 ConnectedSubnet으로 표시됩니다.
  * VPN 연결에는 경로 기반 구성 및 IKEv2가 사용됩니다.

### <a name="understanding-the-device-configuration-file"></a>장치 구성 파일 이해

장치 구성 파일에는 온-프레미스 VPN 장치를 구성할 때 사용할 설정이 포함되어 있습니다. 이 파일을 볼 때 다음 정보를 확인합니다.

* **vpnSiteConfiguration -** 이 섹션은 Virtual WAN에 연결된 사이트로 설정된 장치 정보를 나타냅니다. 여기에는 분기 장치의 이름 및 공용 IP 주소가 포함됩니다.
* **vpnSiteConnections -** 이 섹션에서는 다음 정보를 제공합니다.

    * 가상 허브 VNet의 **주소 공간**.<br>예제:
 
        ```
        "AddressSpace":"10.1.0.0/24"
        ```
    * 허브에 연결된 VNet의 **주소 공간**.<br>예제:

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

## <a name="default"></a>기본 정책

### <a name="initiator"></a>시작자

다음 섹션에는 Azure가 터널의 시작자인 경우, 지원되는 정책 조합이 나와 있습니다.

**1단계**

* AES_256, SHA1, DH_GROUP_2
* AES_256, SHA_256, DH_GROUP_2
* AES_128, SHA1, DH_GROUP_2
* AES_128, SHA_256, DH_GROUP_2
* 3DES, SHA1, DH_GROUP_2
* 3DES, SHA_256, DH_GROUP_2

**2단계**

* GCM_AES_256, GCM_AES_256, PFS_NONE
* AES_256, SHA_1, PFS_NONE
* CBC_3DES, SHA_1, PFS_NONE
* AES_256, SHA_256, PFS_NONE
* AES_128, SHA_1, PFS_NONE
* CBC_3DES, SHA_256, PFS_NONE


### <a name="responder"></a>응답자

다음 섹션에는 Azure가 터널의 응답자인 경우, 지원되는 정책 조합이 나와 있습니다.

**1단계**

* AES_256, SHA1, DH_GROUP_2
* AES_256, SHA_256, DH_GROUP_2
* AES_128, SHA1, DH_GROUP_2
* AES_128, SHA_256, DH_GROUP_2
* 3DES, SHA1, DH_GROUP_2
* 3DES, SHA_256, DH_GROUP_2

**2단계**

* GCM_AES_256, GCM_AES_256, PFS_NONE
* AES_256, SHA_1, PFS_NONE
* CBC_3DES, SHA_1, PFS_NONE
* AES_256, SHA_256, PFS_NONE
* AES_128, SHA_1, PFS_NONE
* CBC_3DES, SHA_256, PFS_NONE
* CBC_DES, SHA_1, PFS_NONE 
* AES_256, SHA_1, PFS_1
* AES_256, SHA_1, PFS_2
* AES_256, SHA_1, PFS_14
* AES_128, SHA_1, PFS_1
* AES_128, SHA_1, PFS_2
* AES_128, SHA_1, PFS_14
* CBC_3DES, SHA_1, PFS_1
* CBC_3DES, SHA_1, PFS_2
* CBC_3DES, SHA_256, PFS_2
* AES_256, SHA_256, PFS_1
* AES_256, SHA_256, PFS_2
* AES_256, SHA_256, PFS_14
* AES_256, SHA_1, PFS_24
* AES_256, SHA_256, PFS_24
* AES_128, SHA_256, PFS_NONE
* AES_128, SHA_256, PFS_1
* AES_128, SHA_256, PFS_2
* AES_128, SHA_256, PFS_14
* CBC_3DES, SHA_1, PFS_14

### <a name="does-everything-need-to-match-between-the-virtual-hub-vpngateway-policy-and-my-on-premises-sdwanvpn-device-or-sd-wan-configuration"></a>가상 허브 vpngateway 정책과 온-프레미스 SDWAN/VPN 장치 또는 SD-WAN 구성 간에 모든 항목이 일치해야 하나요?

온-프레미스 SDWAN/VPN 장치 또는 SD-WAN 구성은 Azure IPsec/IKE 정책에서 지정한 다음과 같은 알고리즘 및 매개 변수와 일치하거나 해당 항목을 포함해야 합니다. SA 수명은 로컬 사양일 뿐이며 일치하지 않아도 됩니다.

* IKE 암호화 알고리즘
* IKE 무결성 알고리즘
* DH 그룹
* IPsec 암호화 알고리즘
* IPsec 무결성 알고리즘
* PFS 그룹

## <a name="feedback"></a>미리 보기 피드백

여러분의 의견에 감사드립니다. 문제를 보고하거나 Virtual WAN에 대한 피드백(긍정적이거나 부정적인)을 제공하려면 <azurevirtualwan@microsoft.com>에 메일을 보내세요. 제목 줄에서 “[]”에 회사 이름을 적어주세요. 문제를 보고하려는 경우 구독 ID도 보내주세요.

## <a name="next-steps"></a>다음 단계

Virtual WAN에 대한 자세한 내용은 [Azure Virtual WAN 정보](virtual-wan-about.md) 및 [Azure Virtual WAN FAQ](virtual-wan-faq.md)를 참조하세요.
