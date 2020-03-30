---
title: Azure 방화벽 관리자 아키텍처 옵션은 무엇입니까?
description: 허브 가상 네트워크 또는 보안 가상 허브 아키텍처를 Azure 방화벽 관리자와 비교하고 대조합니다.
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: article
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: b946a360ced05500a4ef89cda7c623d8ae16658e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444577"
---
# <a name="what-are-the-azure-firewall-manager-architecture-options"></a>Azure 방화벽 관리자 아키텍처 옵션은 무엇입니까?

Azure 방화벽 관리자는 두 가지 네트워크 아키텍처 유형에 대한 보안 관리를 제공할 수 있습니다.

- **보안 가상 허브**

   [Azure 가상 WAN 허브는](../virtual-wan/virtual-wan-about.md#resources) 허브 및 스포크 아키텍처를 쉽게 만들 수 있는 Microsoft 관리 리소스입니다. 보안 및 라우팅 정책이 이러한 허브와 연결된 경우 *[보안 가상 허브라고](secured-virtual-hub.md)* 합니다. 
- **허브 가상 네트워크**

   직접 만들고 관리하는 표준 Azure 가상 네트워크입니다. 보안 정책이 이러한 허브와 연결되면 허브 가상 *네트워크라고*합니다. 현재 Azure 방화벽 정책만 지원됩니다. 워크로드 서버 및 서비스를 포함하는 스포크 가상 네트워크를 피어로 만들 수 있습니다. 스포크로 피어를 참조하지 않는 독립 실행형 가상 네트워크에서 방화벽을 관리할 수도 있습니다.

## <a name="comparison"></a>비교

다음 표에서는 이러한 두 아키텍처 옵션을 비교하고 조직의 보안 요구 사항에 적합한 아키텍처 옵션을 결정하는 데 도움이 될 수 있습니다.


|  |**허브 가상 네트워크**|**보안 가상 허브**  |
|---------|---------|---------|
|**기본 리소스**     |가상 네트워크|가상 WAN 허브|
|**허브 & 스포크**     |가상 네트워크 피어링 사용|허브 가상 네트워크 연결을 사용하여 자동화|
|**온프레미 연결**     |최대 10Gbps 및 30개의 S2S 연결로 VPN 게이트웨이; 익스프레스루트|확장성이 더 큰 VPN 게이트웨이최대 20Gbps 및 1000 S2S 연결; 익스프레스 루트|
|**SDWAN을 사용한 자동 지점 연결**      |지원되지 않음|지원됨|
|**지역별 허브**     |리전당 여러 가상 네트워크|리전당 단일 가상 허브. 여러 가상 WAN으로 가능한 여러 허브|
|**Azure 방화벽 – 여러 공용 IP 주소**      |고객이 제공|자동 생성. GA에서 사용할 수 있습니다.|
|**Azure 방화벽 가용성 영역**     |지원됨|미리 보기에서 사용할 수 없습니다. GA에서 사용할 수 있도록|
|**서비스 파트너로서 타사 보안을 갖춘 고급 인터넷 보안**     |고객이 선택한 파트너 서비스에 대한 VPN 연결 설정 및 관리|신뢰할 수 있는 보안 파트너 흐름 및 파트너 관리 환경을 통한 자동화|
|**허브로 트래픽을 라우팅하는 중앙 집중식 경로 관리**     |고객 관리 사용자 정의 경로|BGP를 사용하여 지원|
|**Application Gateway의 Web Application Firewall** |가상 네트워크에서 지원|현재 스포크 네트워크에서 지원|
|**네트워크 가상 어플라이언스**|가상 네트워크에서 지원|현재 스포크 네트워크에서 지원|

## <a name="next-steps"></a>다음 단계

- [Azure Firewall Manager Preview 배포 개요](deployment-overview.md) 검토
- 보안 [가상 허브에](secured-virtual-hub.md)대해 자세히 알아봅니다.