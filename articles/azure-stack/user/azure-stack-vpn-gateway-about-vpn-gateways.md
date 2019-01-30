---
title: Azure Stack에 대 한 VPN gateway 정보 | Microsoft Docs
description: 에 대해 알아보고 Azure Stack을 사용 하는 VPN gateway를 구성 합니다.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 0e30522f-20d6-4da7-87d3-28ca3567a890
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/04/2019
ms.author: sethm
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 62a0e763913ea1ca27edf88d7560c9400043bf27
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55251370"
---
# <a name="about-vpn-gateway-for-azure-stack"></a>Azure Stack에 대 한 VPN gateway 정보

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

Azure virtual network와 온-프레미스 사이트 간에 네트워크 트래픽을 전송할 수 있습니다, 전에 가상 네트워크에 대 한 가상 네트워크 게이트웨이 만들어야 합니다.

VPN Gateway는 공용 연결을 통해 암호화된 트래픽을 전송하는 가상 네트워크 게이트웨이의 유형입니다. Azure에서 안전 하 게 Azure Stack에서 virtual network 및 가상 네트워크 간에 트래픽을 보낼 VPN gateway를 사용할 수 있습니다. 가상 네트워크 및 VPN 장치에 연결 된 다른 네트워크 간에 안전 하 게 트래픽을 보낼 수도 있습니다.

가상 네트워크 게이트웨이를 만들 때 만들려는 게이트웨이 유형을 지정합니다. Azure Stack 지원 한 가지 유형의 가상 네트워크 게이트웨이: 합니다 **Vpn** 형식입니다.

가상 네트워크마다 두 개의 가상 네트워크 게이트웨이를 포함할 수 있으며 각 유형은 하나씩만 포함할 수 있습니다. 선택한 설정에 따라 단일 VPN Gateway에 대한 여러 연결을 만들 수 있습니다. 예로 다중 사이트 연결 구성은입니다.

만들고 Azure Stack에 대 한 VPN Gateway를 구성 하기 전에 검토 합니다 [Azure Stack 네트워킹에 대 한 고려 사항](azure-stack-network-differences.md) Azure Stack에 대 한 구성을 Azure에서 어떻게 다른 지에 대해 알아보려면 합니다.

>[!NOTE]
>Azure에서 선택한 SKU VPN gateway에 대 한 대역폭 처리량을 게이트웨이에 연결 된 모든 연결을 통해 나눌 수 있어야 합니다. 그러나 Azure Stack에 VPN 게이트웨이 SKU에 대 한 대역폭 값을 게이트웨이에 연결 된 각 연결 리소스에 적용 됩니다.
>
> 예: 
> * Azure에서 기본 VPN gateway SKU는 약 100mbps 집계 처리량을 수용할 수 있습니다. 두 개의 연결이 해당 VPN gateway 만들고 연결 50mbps 대역폭을 사용 하는 다음 50mbps는 다른 연결에 사용할 수 있습니다.
> * Azure stack에서 *각* 100mbps 처리량의 할당 된 기본 VPN gateway SKU에 연결 합니다.

## <a name="configuring-a-vpn-gateway"></a>VPN gateway 구성

VPN gateway 연결을 특정 설정을 사용 하 여 구성 된 여러 리소스에 의존 합니다. 대부분의 이러한 리소스를 개별적으로 구성할 수 있지만 일부 경우에는 특정 순서로 구성 되어야 합니다.

### <a name="settings"></a>설정

설정은 성공적인 연결을 만들기 위한 중요 한 각 리소스에 대 한 선택입니다.

개별 리소스 및 VPN gateway에 대 한 설정에 대 한 자세한 내용은 [for Azure Stack에 대 한 VPN gateway 설정](azure-stack-vpn-gateway-settings.md)합니다. 이 문서를 이해 하는 데 도움이 됩니다.

* 게이트웨이 유형, VPN, 형식과 연결 합니다.
* 게이트웨이 서브넷, 로컬 네트워크 게이트웨이 및 고려해 야 할 수 있는 다른 리소스 설정 합니다.

### <a name="deployment-tools"></a>배포 도구

수 만들고 Azure portal과 같은 하나의 구성 도구를 사용 하 여 리소스를 구성 합니다. 나중에 추가 리소스를 구성 하거나 해당 하는 경우 기존 리소스를 수정 하는 PowerShell과 같은 다른 도구로 전환할 수 있습니다. 현재 Azure portal에서 모든 리소스 및 리소스 설정을 구성할 수 없습니다. 있습니다. 각 연결 토폴로지에 대한 문서의 지침은 특정 구성 도구가 필요한지 여부를 지정합니다.

## <a name="connection-topology-diagrams"></a>연결 토폴로지 다이어그램

VPN gateway 연결에 사용할 수 있는 다양 한 구성 됩니다. 사용자의 요구에 맞는 최상의 구성을 결정 합니다. 다음 섹션에서는 다음 VPN gateway 연결에 대 한 정보 및 토폴로지 다이어그램을 볼 수 있습니다.

* 사용 가능한 배포 모델
* 사용 가능한 구성 도구
* 사용할 수 있는 경우 문서로 직접 이동하는 링크

다음 단원의 설명과 다이어그램 요구 사항에 맞게 연결 토폴로지를 선택할 수 있습니다. 다이어그램은 기본 초기 토폴로지를 보여 줍니다. 이지만 더 복잡 한 구성 가이드로 다이어그램을 사용 하 여 빌드할 수 있습니다.

## <a name="site-to-site-and-multi-site-ipsecike-vpn-tunnel"></a>사이트 간 및 다중 사이트 (IPsec/IKE VPN 터널)

### <a name="site-to-site"></a>사이트 간

A *사이트 간* (S2S) VPN gateway 연결 IPsec/IKE (IKEv1 또는 IKEv2) VPN 터널을 통해 연결 됩니다. 이 연결 유형은 VPN 장치는 온-프레미스 및 공용 IP가 할당 되는 필요 합니다. 이 장치는 NAT 뒤에 배치할 수 없습니다. S2S 연결은 프레미스 간 및 하이브리드 구성에 사용될 수 있습니다.

![사이트 간 VPN 연결 구성 예제](media/azure-stack-vpn-gateway-about-vpn-gateways/vpngateway-site-to-site-connection-diagram.png)

### <a name="multi-site"></a>다중 사이트

A *다중 사이트* 연결 사이트 간 연결의 변형입니다. 가상 네트워크 게이트웨이에서 일반적으로 여러 온-프레미스 사이트에 연결하는 둘 이상의 VPN 연결을 만듭니다. 여러 연결을 사용 하는 경우 경로 기반 VPN 유형 (클래식 Vnet과 함께 작업 하는 경우 동적 게이트웨이로 알려짐)를 사용 해야 합니다. 각 가상 네트워크가 하나의 VPN Gateway만 사용할 수 있으므로 게이트웨이를 통한 모든 연결은 사용 가능한 대역폭을 공유합니다.

![Azure VPN Gateway 다중 사이트 연결 예제](media/azure-stack-vpn-gateway-about-vpn-gateways/vpngateway-multisite-connection-diagram.png)

## <a name="gateway-skus"></a>게이트웨이 SKU

Azure Stack에 대 한 가상 네트워크 게이트웨이 만들 때 게이트웨이 사용 하려는 SKU를 지정 합니다. 다음 VPN gateway Sku가 지원 됩니다.

* Basic
* Standard
* 고성능

Standard 또는 Basic을 통해 더 높은 게이트웨이 SKU를 통해 Basic, Standard 또는 높은 성능 등을 선택 하면 더 많은 Cpu 및 네트워크 대역폭을 게이트웨이에 할당 됩니다. 결과적으로 게이트웨이 가상 네트워크에 더 높은 네트워크 처리량을 지원할 수 있습니다.

Azure Stack Ultra Performance 게이트웨이 Express Route를 사용 하 여 단독으로 사용 되는 SKU를 지원 하지 않습니다.

SKU를 선택 하는 경우 다음을 고려 합니다.

* Azure Stack에서 정책 기반 게이트웨이 지원 하지 않습니다.
* 프로토콜 BGP (경계 게이트웨이) 기본 SKU에서 지원 되지 않습니다.
* EXPRESSROUTE-VPN gateway 공존 구성은 Azure Stack에서 지원 되지 않습니다.
* 고성능 SKU만에서 활성-활성 S2S VPN gateway 연결을 구성할 수 있습니다.

## <a name="estimated-aggregate-throughput-by-sku"></a>SKU 기준으로 예상된 총 처리량

다음 표에서 게이트웨이 SKU 게이트웨이 형식과 예상된 총 처리량을 보여 줍니다.

|   | VPN Gateway 처리량 *(1)* | VPN Gateway 최대 IPsec 터널 *(2)* |
|-------|-------|-------|
|**기본 SKU** ***(3)***    | 100Mbps  | 20    |
|**표준 SKU**       | 100Mbps  | 20    |
|**고성능 SKU** | 200Mbps    | 10    |

**테이블 정보:**

*참고 (1)* -VPN 처리량 보장 된 처리량이 되지 크로스-프레미스 연결에 대 한 인터넷을 통해. 가능한 최대 처리량 측정값입니다.  
*참고 (2)* -최대 터널은 Azure Stack 배포의 모든 구독 당 총 합니다.  
*참고 (3)* -BGP 라우팅 기본 SKU에 지원 되지 않습니다.

>[!NOTE]
>두 Azure Stack 배포 간에 사이트 간 VPN 연결을 하나만 만들 수 있습니다. 동일한 IP 주소를 단일 VPN 연결을 허용 하는 플랫폼의 제한 사항 때문입니다. Azure Stack을 사용 하는 단일 공용 IP를 모든 VPN gateway에 대해 Azure Stack 시스템에서 다중 테 넌 트 게이트웨이 활용 하기 때문에 두 개의 Azure Stack 시스템 간에 VPN 연결을 하나만 있을 수 있습니다. 이 제한은 둘 이상의 사이트 간 VPN 연결에 단일 IP 주소를 사용 하는 VPN 게이트웨이 연결에 적용 됩니다. Azure Stack에서 동일한 IP 주소를 사용 하 여 만들어야 둘 이상의 로컬 네트워크 게이트웨이 리소스를 허용 하지 않습니다.

## <a name="next-steps"></a>다음 단계

[Azure Stack에 대 한 VPN gateway 구성 설정](azure-stack-vpn-gateway-settings.md)
