---
title: Azure 방화벽 관리자 신뢰할 수 있는 보안 파트너란 무엇입니까(미리 보기)
description: Azure 방화벽 관리자 신뢰할 수 있는 보안 파트너에 대해 알아보기
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: victorh
ms.openlocfilehash: b92242ce9086579d0397f78853402cfc08453f68
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75436783"
---
# <a name="what-are-trusted-security-partners-preview"></a>신뢰할 수 있는 보안 파트너(미리 보기)란?

> [!IMPORTANT]
> 이 공개 미리 보기는 Service Level Agreement(서비스 수준 약정)없이 제공되므로 프로덕션 워크로드에 사용하지 말아야 합니다. 특정 기능은 지원되지 않을 수 있거나, 기능이 제한될 수 있거나 모든 Azure 위치에서 사용하지는 못할 수 있습니다. 자세한 내용은 [Microsoft Azure 미리 보기에 대한 보충 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Azure 방화벽 관리자의 *신뢰할 수 있는 보안 파트너(미리 보기)를* 사용하면 친숙한 동종 최고의 타사 보안을 서비스(SECaaS) 오퍼링으로 사용하여 사용자의 인터넷 액세스를 보호할 수 있습니다.

빠른 구성을 통해 지원되는 보안 파트너와 함께 허브를 보호하고 가상 네트워크(VNet) 또는 리전 내의 지점 위치에서 인터넷 트래픽을 라우팅 및 필터링할 수 있습니다. 이 작업은 사용자 정의 경로(DR)를 설정하고 관리하지 않고 자동화된 경로 관리를 사용하여 수행됩니다.

여러 Azure 리전에서 원하는 보안 파트너로 구성된 보안 허브를 배포하여 해당 지역의 전 세계 어디서나 사용자에 대한 연결 및 보안을 얻을 수 있습니다. 보안 허브의 개인 트래픽에 대한 인터넷/SaaS 응용 프로그램 트래픽에 대한 보안 파트너의 오퍼링을 사용하고 보안 허브의 개인 트래픽에 대한 Azure 방화벽을 사용할 수 있으므로 이제 전 세계에 분산된 사용자 및 응용 프로그램에 가까운 Azure에서 보안 에지를 구축할 수 있습니다.

이 미리 보기에서 지원되는 보안 파트너는 **ZScaler** 및 **iboss**입니다. 지원되는 지역은 서중부, 북중부, 서부, 웨스트US2 및 이스트US입니다.

![신뢰할 수 있는 보안 파트너](media/trusted-security-partners/trusted-security-partners.png)

## <a name="key-scenarios"></a>주요 시나리오

다음 시나리오에서 보안 파트너를 사용하여 인터넷 트래픽을 필터링할 수 있습니다.

- 가상 네트워크(VNet) - 인터넷

   Azure에서 실행되는 클라우드 워크로드에 고급 사용자 인식 인터넷 보호를 활용합니다.

- 지점에서 인터넷으로

   Azure 연결 및 전역 배포를 활용하여 분기에 대한 타사 NSaaS 필터링을 인터넷 시나리오에 쉽게 추가할 수 있습니다. Azure Virtual WAN을 사용하여 글로벌 전송 네트워크 및 보안 에지를 구축할 수 있습니다.

다음과 같은 시나리오가 지원됩니다.
-   타사 파트너 오퍼링을 통해 인터넷에 VNet을 제공합니다.
-   타사 파트너 오퍼링을 통해 인터넷으로 분기합니다.
-   타사 파트너 오퍼링을 통해 인터넷에 분기하는 나머지 개인 트래픽(스포크-투-스포크, 지점 간, 지점 간)은 Azure 방화벽을 통해 수행됩니다.

다음 시나리오는 지원되지 않습니다.

- 파트너 오퍼링을 통해 VNet에서 인터넷으로 개인 트래픽에 대한 Azure 방화벽과 결합할 수 없습니다. 다음 제한 사항들을 참조하십시오.

## <a name="current-limitations"></a>현재 제한 사항

- VNet에서 인터넷으로 의한 개인 트래픽에 대한 Azure 방화벽 추가와 인터넷 트래픽에 대한 파트너 제공을 혼합할 수 없습니다. 인터넷 트래픽을 Azure 방화벽으로 보내거나 보안 가상 허브에서 제공하는 타사 보안 파트너로 보낼 수 있지만 둘 다 전송할 수는 없습니다. 
- 가상 허브당 최소 하나의 보안 파트너를 배포할 수 있습니다. 공급자를 변경해야 하는 경우 기존 파트너를 제거하고 새 파트너를 추가해야 합니다.

## <a name="best-practices-for-internet-traffic-filtering-in-secured-virtual-hubs"></a>보안 가상 허브에서 인터넷 트래픽 필터링을 위한 모범 사례

인터넷 트래픽에는 일반적으로 웹 트래픽이 포함됩니다. 그러나 Office 365(O365) 및 Azure 저장소, Azure Sql 과 같은 Azure 공용 PaaS 서비스와 같은 SaaS 응용 프로그램으로 향하는 트래픽도 포함됩니다. 다음은 이러한 서비스에 대한 트래픽을 처리하는 모범 사례 권장 사항입니다.

### <a name="handling-azure-paas-traffic"></a>Azure PaaS 트래픽 처리
 
- 트래픽이 대부분 Azure PaaS로 구성되고 IP 주소, FQDNs, 서비스 태그 또는 FQDN 태그를 사용하여 응용 프로그램의 리소스 액세스를 필터링할 수 있는 경우 보호를 위해 Azure 방화벽을 사용합니다.

- 트래픽이 SaaS 응용 프로그램 액세스로 구성중이거나 사용자 인식 필터링(예: 가상 데스크톱 인프라(VDI) 워크로드)이 필요하거나 고급 인터넷 필터링 기능이 필요한 경우 허브에서 타사 파트너 솔루션을 사용합니다.

![Azure 방화벽 관리자에 대한 모든 시나리오](media/trusted-security-partners/all-scenarios.png)

## <a name="handling-office-365-o365-traffic"></a>Office 365(O365) 트래픽 처리

전역적으로 분산된 분기 위치 시나리오에서는 나머지 인터넷 트래픽을 Azure 보안 허브로 보내기 전에 지점에서 직접 Office 365 트래픽을 리디렉션해야 합니다.

Office 365의 경우 성공적인 사용자 환경을 위해 네트워크 대기 시간 및 성능이 중요합니다. 최적의 성능 및 사용자 환경과 관련된 이러한 목표를 달성하려면 고객은 Azure를 통해 나머지 인터넷 트래픽을 라우팅하기 전에 Office 365 직접 및 로컬 이스케이프를 구현해야 합니다.

[Office 365 네트워크 연결 원칙은](https://docs.microsoft.com/office365/enterprise/office-365-network-connectivity-principles) 주요 Office 365 네트워크 연결을 사용자 분기 또는 모바일 장치에서 로컬로 라우팅하고 인터넷을 통해 가장 가까운 Microsoft 네트워크 지점으로 직접 라우팅하도록 요구합니다.

또한 Office 365 연결은 개인 정보 보호를 위해 강력하게 암호화되며 성능상의 이유로 효율적이고 독점적인 프로토콜을 사용합니다. 따라서 이러한 연결이 기존 네트워크 수준 보안 솔루션에 적용되는 것은 비실용적이고 영향력 있는 솔루션입니다. 이러한 이유로 고객은 Azure를 통해 나머지 트래픽을 보내기 전에 지점에서 직접 Office 365 트래픽을 보내는 것이 좋습니다. Microsoft는 Azure 및 Office 365와 통합하고 고객이 Office 365 직접 및 로컬 인터넷 브레이크아웃을 쉽게 사용할 수 있도록 하는 여러 SD-WAN 솔루션 공급자와 파트너관계를 맺고 있습니다. 자세한 내용은 [가상 WAN을 통해 O365 정책을 설정하려면 어떻게](https://docs.microsoft.com/azure/virtual-wan/virtual-wan-office365-overview) 해야 합니까?


## <a name="next-steps"></a>다음 단계

[Azure 방화벽 관리자를 사용하여 보안 허브에 신뢰할 수 있는 보안 제품을 배포합니다.](deploy-trusted-security-partner.md)
