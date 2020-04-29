---
title: Azure 방화벽 관리자 아키텍처 옵션은 무엇 인가요?
description: Azure 방화벽 관리자를 사용 하 여 허브 가상 네트워크 또는 보안 가상 허브 아키텍처를 사용 하는 비교 및 대조
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: article
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: b946a360ced05500a4ef89cda7c623d8ae16658e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77444577"
---
# <a name="what-are-the-azure-firewall-manager-architecture-options"></a>Azure 방화벽 관리자 아키텍처 옵션은 무엇 인가요?

Azure 방화벽 관리자는 다음 두 가지 네트워크 아키텍처 유형에 대 한 보안 관리를 제공할 수 있습니다.

- **보안 가상 허브**

   [Azure 가상 WAN 허브](../virtual-wan/virtual-wan-about.md#resources) 는 허브 및 스포크 아키텍처를 쉽게 만들 수 있는 Microsoft에서 관리 하는 리소스입니다. 보안 및 라우팅 정책이 이러한 허브와 연결 된 경우 보안 *[가상 허브](secured-virtual-hub.md)* 라고 합니다. 
- **허브 가상 네트워크**

   사용자가 만들고 관리 하는 표준 Azure virtual network입니다. 이러한 허브와 연결 된 보안 정책에는 *허브 가상 네트워크*라고 합니다. 지금은 Azure 방화벽 정책만 지원 됩니다. 작업 서버 및 서비스가 포함 된 스포크 가상 네트워크를 피어 링 할 수 있습니다. 또한 모든 spoke에 피어 링 되지 않는 독립 실행형 가상 네트워크에서 방화벽을 관리할 수 있습니다.

## <a name="comparison"></a>비교

다음 표에서는 이러한 두 가지 아키텍처 옵션을 비교 하 고 조직의 보안 요구 사항에 적합 한 것을 결정 하는 데 도움이 될 수 있습니다.


|  |**허브 가상 네트워크**|**보안 가상 허브**  |
|---------|---------|---------|
|**기본 리소스**     |가상 네트워크|가상 WAN 허브|
|**허브 & 스포크**     |가상 네트워크 피어 링 사용|허브 가상 네트워크 연결을 사용 하 여 자동화|
|**온-프레미스 연결**     |최대 10gbps 및 30 개의 S2S 연결을 VPN Gateway 합니다. ExpressRoute|더 확장 가능한 VPN Gateway 20gbps 및 1000 S2S 연결 Express 경로|
|**SDWAN을 사용 하 여 자동화 된 분기 연결**      |지원되지 않음|지원됨|
|**지역별 허브**     |지역별 여러 가상 네트워크|지역별 단일 가상 허브. 여러 가상 Wan으로 가능한 여러 허브|
|**Azure 방화벽-여러 공용 IP 주소**      |고객이 제공|자동 생성 되었습니다. GA에서 사용할 수 있습니다.|
|**Azure 방화벽 가용성 영역**     |지원됨|미리 보기에서는 사용할 수 없습니다. GA에서 사용 가능|
|**타사 보안을 서비스 파트너로 사용한 고급 인터넷 보안**     |선택한 파트너 서비스에 대 한 사용자 설정 및 관리 되는 VPN 연결|트러스트 된 보안 파트너 흐름 및 파트너 관리 환경을 통해 자동화|
|**트래픽을 허브로 라우팅하는 중앙 경로 관리**     |고객이 관리 하는 사용자 정의 경로|BGP를 사용 하 여 지원|
|**Application Gateway의 Web Application Firewall** |Virtual Network에서 지원|현재 스포크 네트워크에서 지원 됨|
|**네트워크 가상 어플라이언스**|Virtual Network에서 지원|현재 스포크 네트워크에서 지원 됨|

## <a name="next-steps"></a>다음 단계

- [Azure Firewall Manager Preview 배포 개요](deployment-overview.md) 검토
- [보안 가상 허브](secured-virtual-hub.md)에 대해 알아봅니다.