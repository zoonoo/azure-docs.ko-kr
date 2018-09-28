---
title: Azure Virtual WAN의 Office 365 제어판
description: Virtual WAN의 Office 365 제어판에 대해 알아봅니다.
author: cherylmc
ms.service: virtual-wan
services: virtual-wan
ms.topic: article
ms.date: 9/24/2018
ms.author: cherylmc
ms.openlocfilehash: 815b91295540e93f8f0ffbc002fcf02ce1c13365
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46992680"
---
# <a name="office-365-control-plane-in-virtual-wan"></a>Virtual WAN의 Office 365 제어판

선택된 SDWAN 장치를 사용하는 Virtual WAN 고객은 Azure Portal에서 신뢰할 수 있는 트래픽을 위한 O365 인터넷 혁신 정책을 구성할 수 있습니다. 이는 다음을 수행할 수 있습니다.
- O365 트래픽이 사용자와 가까운 Microsoft 네트워크를 입력해 최적의 사용자 환경을 제공합니다.
- 트래픽 역행과 헤어핀을 피하므로 WAN 비용이 절감됩니다.
- O365 연결 원칙을 제공합니다.

## <a name="faqs"></a>FAQ
### <a name="what-is-the-customer-benefit"></a>고객 혜택
이 기능을 Virtual WAN에 사용하면 이제 직접 인터넷 혁신에 대해 고객이 신뢰하는 Office 365 트래픽 범주를 특정할 수 있습니다. 이 신뢰할 수 있는 O365 트래픽은 프록시를 우회하여 사용자 위치에서 가장 가까운 Microsoft POP로 직접 라우팅합니다. 이것은 트래픽 역행과 헤어핀을 피하므로 최적화된 사용자 환경을 제공하며 WAN 비용이 절감됩니다. 

### <a name="what-are-the-office-365-traffic-categories"></a>Office 365 트래픽 범주
Office 365 엔드포인트는 네트워크 주소 및 서브넷을 나타냅니다. 엔드포인트는 URL이나 IP 주소, 또는 IP 범위일 수 있습니다. URL은 *account.office.net*과 같은 FQDN이나 **.office365.com*과 같은 와일드 카드 URL일 수 있습니다. 엔드포인트는 각 중요도에 따라 **최적화**, **허용** 및 **기본**의 세 가지 범주로 나뉩니다. 엔드포인트 범주에 대한 자세한 내용은 [여기](https://docs.microsoft.com/office365/enterprise/office-365-network-connectivity-principles#BKMK_Categories)서 확인하세요.

### <a name="which-office-365-traffic-category-is-recommended-by-microsoft-for-direct-internet-breakout"></a>직접 인터넷 혁신에 대해 Microsoft에서 권장하는 Office 365 트래픽 범주
**최적화** 범주는 가장 중요한 네트워크 엔드포인트로, SSL 중단 및 검사와 기타 네트워크 보안 장치를 우회해야 합니다. 사용자와 가까운 직접 인터넷 송신이 있어야 합니다. 이 엔드포인트는 네트워크 성능, 대기 시간 및 가용성에 가장 민감한 Office 365 시나리오를 나타냅니다. 이 범주는 작은(~10의 순서로) 키 URL 집합과 Exchange Online, SharePoint Online, 비즈니스용 Skype 및 Microsoft Teams와 같은 핵심 Office 365 워크로드 전용 IP 서브넷의 정의된 집합을 포함합니다. 

**허용** 범주 또한 직접 인터넷 송신에 권장됩니다. 하지만 네트워크 트래픽 허용은 일부 네트워크 대기 시간을 허용할 수 있습니다. 최적화 및 허용 범주에서의 엔드포인트는 모든 Microsoft 데이터 센터에서 호스팅되며 Office 365의 일부로서 관리됩니다. 기본 범주는 기본 인터넷 송신 위치로 보내지며 직접 인터넷 송신 또는 SSL 중단의 우회 및 장치 검사가 필요하지 않습니다.

### <a name="how-do-i-set-my-o365-policies-via-virtual-wan"></a>Virtual WAN으로 O365 정책을 설정하는 방법
**Virtual WAN** -> **설정** -> **구성** 탭에서 정책을 설정할 수 있습니다. 여기서, 직접 인터넷 혁신을 위한 O365 트래픽의 선호 범주를 지정할 수 있습니다.

![Virtual WAN에서의 Office 365 제어판 구성](media/virtual-wan-office365-overview/configure-office365-control-plane.png)

### <a name="how-does-this-work"></a>작동 방식

1.  O365 트래픽이 사용자와 가까운 Microsoft 네트워크를 입력해 최적의 사용자 환경을 제공합니다.
2.  라우팅 정책이 SDWAN에서 사용됩니다. 그런 다음, 신뢰할 수 있는 범주의 보안 프록시를 우회하고 해당 범주에 로컬 직접 혁신을 수행합니다.
3.  역행과 헤어핀을 피해 WAN 비용을 절감할 수 있습니다.

### <a name="which-partner-devices-support-this-via-virtual-wan"></a>Virtual WAN에서 지원되는 파트너 장치
현재 Citrix가 Virtual WAN에서 이 정책을 지원합니다.

### <a name="what-happens-to-the-remaining-categories-of-untrusted-o365-traffic"></a>신뢰할 수 없는 O365 트래픽의 나머지 범주의 경우
나머지 O365 트래픽은 고객 기본 인터넷 트래픽 경로를 따릅니다.

### <a name="what-if-i-have-already-specified-my-o365-policies-via-my-sdwan-provider"></a>SDWAN 공급자에서 이미 O365 정책을 지정한 경우
SDWAN UX와 Azure Virtual WAN 모두에서 정책을 지정하는 경우, Virtual WAN에서 설정한 정책이 우선 적용됩니다.

## <a name="next-steps"></a>다음 단계
- [Virtual WAN](virtual-wan-about.md)에 대한 자세한 정보.