---
title: Azure 방화벽 관리자 보안 파트너 공급자 란?
description: Azure 방화벽 관리자 보안 파트너 공급자에 대해 알아보기
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: 3c09e75a98e5342ab54710e05466755d86705b2a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91399434"
---
# <a name="what-are-security-partner-providers"></a>보안 파트너 공급자란?

Azure 방화벽 관리자의 *보안 파트너 공급자* 를 사용 하면 사용자에 대 한 인터넷 액세스를 보호 하기 위해 친숙 하 고, 최고 수준의 타사의 secaas (security as a service) 제품을 사용할 수 있습니다.

빠른 구성에서는 지원 되는 보안 파트너를 사용 하 여 허브를 보호 하 고, 가상 네트워크 (Vnet) 또는 지역 내의 분기 위치에서 인터넷 트래픽을 라우팅하고 필터링 할 수 있습니다. UDRs (사용자 정의 경로)를 설정 및 관리 하지 않고도 자동 경로 관리를 사용 하 여이 작업을 수행할 수 있습니다.

여러 Azure 지역에서 선택한 보안 파트너를 사용 하 여 구성 된 보안 허브를 배포 하 여 해당 지역의 전 세계 어디에서 나 사용자에 대 한 연결 및 보안을 얻을 수 있습니다. 인터넷/SaaS 응용 프로그램 트래픽에 대 한 보안 파트너의 제품 및 보안 허브의 개인 트래픽에 대 한 Azure 방화벽을 사용 하는 기능을 사용 하 여 전역으로 분산 된 사용자 및 응용 프로그램에 가까운 Azure에서 보안에 지를 구축할 수 있습니다.

지원 되는 보안 파트너는 **ZScaler**, **Check Point** (preview) 및 **iboss** (미리 보기)입니다.

![보안 파트너 공급자](media/trusted-security-partners/trusted-security-partners.png)

## <a name="key-scenarios"></a>주요 시나리오

보안 파트너를 사용 하 여 다음과 같은 시나리오에서 인터넷 트래픽을 필터링 할 수 있습니다.

- 인터넷에 대 한 Virtual Network (VNet)

   Azure에서 실행되는 클라우드 워크로드에 고급 사용자 인식 인터넷 보호를 활용합니다.

- 인터넷으로 분기

   Azure 연결 및 글로벌 배포를 활용 하 여 인터넷 시나리오에 분기에 대 한 타사 NSaaS 필터링을 쉽게 추가할 수 있습니다. Azure 가상 WAN을 사용 하 여 글로벌 전송 네트워크 및 보안에 지를 빌드할 수 있습니다.

다음과 같은 시나리오가 지원됩니다.
- Azure 방화벽을 통해 보안 파트너 공급자와 기타 트래픽 (스포크 to 스포크, 스포크 to branch, branch로 분기)을 통해 인터넷에 대 한 VNet/분기.
- 보안 파트너 공급자를 통해 인터넷으로의 VNet/지사

## <a name="best-practices-for-internet-traffic-filtering-in-secured-virtual-hubs"></a>보안 가상 허브에서 인터넷 트래픽 필터링 모범 사례

인터넷 트래픽은 일반적으로 웹 트래픽을 포함 합니다. 하지만 Microsoft 365와 같은 SaaS 응용 프로그램으로 향하는 트래픽과 Azure Storage, Azure Sql 등의 Azure 공용 PaaS 서비스를 포함 합니다. 다음은 이러한 서비스에 대 한 트래픽을 처리 하기 위한 최선의 권장 방법입니다.

### <a name="handling-azure-paas-traffic"></a>Azure PaaS 트래픽 처리
 
- 트래픽이 대부분의 Azure PaaS로 구성 된 경우 보호를 위해 Azure 방화벽을 사용 하 고, IP 주소, Fqdn, 서비스 태그 또는 FQDN 태그를 사용 하 여 응용 프로그램에 대 한 리소스 액세스를 필터링 할 수 있습니다.

- 트래픽이 SaaS 응용 프로그램 액세스로 구성 되어 있거나 사용자에 게 인식 되는 필터링 (예: VDI (가상 데스크톱 인프라) 워크 로드의 경우) 또는 고급 인터넷 필터링 기능이 필요한 경우 허브에서 타사 파트너 솔루션을 사용 합니다.

![Azure 방화벽 관리자에 대 한 모든 시나리오](media/trusted-security-partners/all-scenarios.png)

## <a name="handling-microsoft-365-traffic"></a>Microsoft 365 트래픽 처리

전역적으로 분산 된 분기 위치 시나리오에서는 Azure 보안 허브에 남은 인터넷 트래픽을 보내기 전에 분기에서 직접 Microsoft 365 트래픽을 리디렉션해야 합니다.

성공적인 사용자 환경에서는 네트워크 대기 시간 및 성능이 매우 중요 Microsoft 365 있습니다. 최적의 성능 및 사용자 환경에 대해 이러한 목표를 달성 하기 위해 고객은 Azure를 통해 나머지 인터넷 트래픽 라우팅을 고려 하기 전에 Microsoft 365 직접 및 로컬 이스케이프를 구현 해야 합니다.

[네트워크 연결 원리 Microsoft 365](/microsoft-365/enterprise/microsoft-365-network-connectivity-principles) 사용자 분기 또는 모바일 장치에서 로컬로 라우팅 하 고 인터넷을 통해 직접 라우팅할 수 있는 Microsoft 네트워크 상태에 네트워크 연결을 Microsoft 365 호출 합니다.

또한 Microsoft 365 연결은 개인 정보를 위해 암호화 되며 성능상의 이유로 효율적이 고 독점적인 프로토콜을 사용 합니다. 이렇게 하면 기존 네트워크 수준 보안 솔루션에 대 한 이러한 연결을 적용 하기가 어렵습니다. 이러한 이유로 Azure를 통해 나머지 트래픽을 전송 하기 전에 고객이 분기에서 직접 Microsoft 365 트래픽을 전송 하는 것이 좋습니다. Microsoft는 Azure 및 Microsoft 365와 통합 하는 여러 SD WAN 솔루션 공급자와 파트너 관계를 맺고 있으며, 고객이 직접 및 로컬 인터넷 분리를 쉽게 사용할 Microsoft 365 수 있도록 합니다. 자세한 내용은 [Azure 가상 WAN 이란?](../virtual-wan/virtual-wan-about.md) 을 참조 하세요.

## <a name="next-steps"></a>다음 단계

[Azure 방화벽 관리자를 사용 하 여 보안 허브에 보안 파트너 제품을 배포](deploy-trusted-security-partner.md)합니다.
