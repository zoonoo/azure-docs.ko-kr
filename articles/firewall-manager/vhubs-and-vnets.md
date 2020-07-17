---
title: Azure 방화벽 관리자 아키텍처 옵션은 무엇 인가요?
description: Azure 방화벽 관리자를 사용 하 여 허브 가상 네트워크 또는 보안 가상 허브 아키텍처를 사용 하는 비교 및 대조
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: article
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: 7a06111fbe38f167ddf3512fdb312d7de754a738
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85563573"
---
# <a name="what-are-the-azure-firewall-manager-architecture-options"></a>Azure 방화벽 관리자 아키텍처 옵션은 무엇 인가요?

Azure 방화벽 관리자는 다음 두 가지 네트워크 아키텍처 유형에 대 한 보안 관리를 제공할 수 있습니다.

- **보안 가상 허브**

   [Azure Virtual WAN Hub](../virtual-wan/virtual-wan-about.md#resources)는 허브 및 스포크 아키텍처를 쉽게 만들 수 있는 Microsoft 관리 리소스입니다. 보안 및 라우팅 정책이 이러한 허브와 연결 된 경우 보안 *[가상 허브](secured-virtual-hub.md)* 라고 합니다. 
- **허브 가상 네트워크**

   이는 사용자가 직접 만들고 관리하는 표준 Azure 가상 네트워크입니다. 이러한 허브와 연결된 보안 정책을 *보안 가상 허브*라고도 합니다. 지금은 Azure Firewall Policy만 지원됩니다. 워크로드 서버 및 서비스가 포함된 스포크 가상 네트워크를 피어링할 수 있습니다. 또한 모든 스포크에 피어링되지 않는 독립 실행형 가상 네트워크에서 방화벽을 관리할 수 있습니다.

## <a name="comparison"></a>비교

다음 표에서는 이러한 두 가지 아키텍처 옵션을 비교 하 고 조직의 보안 요구 사항에 적합 한 것을 결정 하는 데 도움이 될 수 있습니다.


|  |**Hub 가상 네트워크**|**보안 가상 허브**  |
|---------|---------|---------|
|**기본 리소스**     |가상 네트워크|가상 WAN 허브|
|**허브 & 스포크**     |가상 네트워크 피어 링 사용|허브 가상 네트워크 연결을 사용 하 여 자동화|
|**온-프레미스 연결**     |최대 10gbps 및 30 개의 S2S 연결을 VPN Gateway 합니다. ExpressRoute|더 확장 가능한 VPN Gateway 20gbps 및 1000 S2S 연결 Express 경로|
|**SDWAN을 사용 하 여 자동화 된 분기 연결**      |지원되지 않음|지원됨|
|**지역별 허브**     |지역별 여러 가상 네트워크|지역별 단일 가상 허브. 여러 가상 Wan으로 가능한 여러 허브|
|**Azure 방화벽-여러 공용 IP 주소**      |고객이 제공|자동 생성 됨|
|**Azure 방화벽 가용성 영역**     |지원됨|아직 사용할 수 없음|
|**타사 보안을 서비스 파트너로 사용한 고급 인터넷 보안**     |선택한 파트너 서비스에 대 한 사용자 설정 및 관리 되는 VPN 연결|보안 파트너 공급자 흐름 및 파트너 관리 환경을 통해 자동화|
|**트래픽을 허브로 라우팅하는 중앙 경로 관리**     |고객이 관리 하는 사용자 정의 경로|BGP를 사용 하 여 지원|
|**여러 보안 공급자 지원**|타사 방화벽에 대 한 강제 터널링을 수동으로 구성 하 여 지원|두 보안 공급자에 대 한 자동화 된 지원: 개인 트래픽 필터링을 위한 Azure 방화벽 및 인터넷 필터링을 위한 타사|
|**Application Gateway의 Web Application Firewall** |Virtual Network에서 지원|현재 스포크 네트워크에서 지원 됨|
|**네트워크 가상 어플라이언스**|Virtual Network에서 지원|현재 스포크 네트워크에서 지원 됨|

## <a name="next-steps"></a>다음 단계

- [Azure 방화벽 관리자 배포 개요](deployment-overview.md) 를 검토 합니다.
- [보안 가상 허브](secured-virtual-hub.md)에 대해 알아보기