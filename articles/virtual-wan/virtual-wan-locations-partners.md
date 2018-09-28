---
title: Azure Virtual WAN 파트너 위치 | Microsoft Docs
description: 이 문서에는 Azure Virtual WAN 파트너 및 허브 위치 목록이 포함되어 있습니다.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect find a Virtual WAN partner
ms.openlocfilehash: 76542392522ff14642ed7ccc6de8ed543d66513f
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46984996"
---
# <a name="virtual-wan-partners-and-virtual-hub-locations"></a>가상 WAN 파트너 및 가상 허브 위치

이 문서에서는 가상 허브로 연결하는 데 선호되는 파트너와 가상 WAN 지원 영역에 관한 정보를 제공합니다.

Azure Virtual WAN은 Azure를 통해 최적화 및 자동화된 분기 간 연결을 제공하는 네트워킹 서비스입니다. Virtual WAN을 사용하여 Azure와 통신하도록 분기 장치를 연결 및 구성할 수 있습니다. 수동으로 또는 Virtual WAN 선호 파트너를 통해 선호하는 공급자 장치를 사용하여 수행할 수 있습니다. 선호하는 파트너 장치를 사용하여 사용 편의성, 연결 및 구성 관리의 간소화를 얻을 수 있습니다. 온-프레미스 장치에서 가상 허브로의 연결은 자동화된 방식으로 설정됩니다. 가상 허브는 Microsoft에서 관리하는 가상 네트워크입니다. 허브는 온-프레미스 네트워크(vpnsite)에서 연결을 활성화하는 다양한 서비스 엔드포인트를 포함합니다. 지역당 허브는 하나만 있을 수 있습니다.

## <a name="regions"></a>영역

지원되는 영역 및 사용 가능한 영역 목록은 다음과 같습니다.

|지역 | Azure 지역|
|---|---|
|북아메리카 | 미국 동부, 미국 서부, 미국 동부 2, 미국 서부 2, 미국 중부, 미국 중남부, 미국 중북부, 미국 중서부, 캐나다 중부, 캐나다 동부 |
|남미 |브라질 남부 |
| 유럽 | 프랑스 중부, 프랑스 남부, 북유럽, 유럽 서부, 영국 서부, 영국 남부 |
| 아시아 | 동아시아, 동남 아시아 |
| 일본  | 일본 서부, 일본 동부 |
| 오스트레일리아 | 오스트레일리아 남동부, 오스트레일리아 동부 | 
| 오스트레일리아 정부 | 오스트레일리아 중부, 오스트레일리아 중부 2 |
| 인도 | 인도 서부, 인도 중부, 인도 남부 |
| 대한민국 | 한국 중부, 한국 남부 | 

## <a name="automation-from-connectivity-partners"></a>연결 파트너로부터 자동화

이 섹션에서는 연결 제공자로부터의 자동화에 관한 상위 레벨 세부사항을 설명합니다.

Azure Virtual WAN에 연결하는 장치에는 연결 자동화가 기본으로 제공됩니다. 이 기능은 일반적으로 device-management UI(또는 이와 동등)에 설정되며, VPN 분기 장치와 Azure Virtual Hub VPN 엔드포인트(VPN 게이트웨이) 간의 연결 및 구성 관리를 설정합니다.

다음 상위 레벨 자동화가 장치 콘솔/관리 센터에 설정됩니다.

* 장치가 Azure Virtual WAN 리소스 그룹에 액세스하기 위한 적절한 권한
* Azure Virtual WAN에 분기 장치 업로드
* Azure 연결 정보 자동 다운로드 
* 온-프레미스 분기 장치 구성 

일부 연결 파트너는 Azure Virtual Hub VNet과 VPN 게이트웨이 만들기를 포함하도록 자동화를 확장할 수 있습니다. 자동화에 대해 자세히 알려면 [자동화 구성 - WAN 파트너](virtual-wan-configure-automation-providers.md)를 참조하세요.

## <a name="connectivity-through-preferred-partners"></a>선호 파트너를 통한 연결

아래 섹션에 분기 장치 파트너가 나열되어 있지 않으면 해당 분기 장치 공급자에게 연락하여 azurevirtualwan@microsoft.com으로 메일을 보내 Microsoft에 연락하도록 요청하세요.

선호 파트너가 제공하는 서비스에 관한 자세한 정보를 수집하려면 다음 링크를 확인할 수 있습니다. 

|선호 파트너|
|---|
|[Barracuda Networks](https://www.barracuda.com/AzurevWAN)|
| [Check Point](https://www.checkpoint.com/solutions/microsoft-azure-virtual-wan/) |
| [Citrix](https://www.citrix.com/global-partners/microsoft/sd-wan-for-azure-virtual-wan.html)|
|[Netfoundry](https://netfoundry.io/solutions/netfoundry-for-microsoft-azure-virtual-wan/)|
|[Palo Alto Networks](https://researchcenter.paloaltonetworks.com/2018/09/azure-vwan-integration/) |
|[Riverbed Technology](https://www.riverbed.com/go/steelconnect-azurewan.html)|
|[128 Technology](https://www.128technology.com/partners/azure) |

## <a name="next-steps"></a>다음 단계

가상 WAN에 대한 자세한 내용은 [가상 WAN FAQ](virtual-wan-faq.md)를 참조하세요.

Azure Virtual WAN에 대한 연결을 자동화하는 방법에 대한 자세한 내용은 [가상 WAN 파트너 - 자동화 방법](virtual-wan-configure-automation-providers.md)을 참조하세요.
