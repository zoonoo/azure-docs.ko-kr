---
title: Azure 방화벽 중앙 관리
description: Azure 방화벽 관리자 중앙 관리에 대해 알아보기
author: vhorne
ms.service: firewall
services: firewall
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: 58f670f3f55a63f0c1823adc13c98f5863d4d650
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444551"
---
# <a name="azure-firewall-central-management"></a>Azure 방화벽 중앙 관리

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

여러 방화벽을 관리하는 경우 방화벽 규칙을 지속적으로 변경하면 방화벽 규칙을 동기화하기가 어려워집니다. 중앙 IT 팀은 기본 방화벽 정책을 정의하고 여러 비즈니스 단위에서 이를 적용하는 방법이 필요합니다. 동시에 DevOps 팀은 민첩성을 높이기 위해 자체 로컬 파생 방화벽 정책을 만들려고 합니다.

Azure 방화벽 관리자 미리 보기는 이러한 문제를 해결하는 데 도움이 될 수 있습니다.


## <a name="azure-firewall-manager-preview"></a>Azure 방화벽 관리자 미리 보기

Azure 방화벽 관리자 미리 보기는 클라우드 기반 보안 경계에 대한 중앙 보안 정책 및 경로 관리를 제공하는 네트워크 보안 관리 서비스입니다. 엔터프라이즈 IT 팀은 여러 Azure 방화벽 인스턴스에서 트래픽 필터링을 위한 네트워크 및 응용 프로그램 수준 규칙을 중앙에서 쉽게 정의할 수 있습니다. 트래픽 거버넌스 및 보호를 위해 허브 및 스포크 아키텍처의 다양한 Azure 지역 및 구독을 포괄할 수 있습니다. 또한 DevOps는 조직 간에 구현되는 파생 로컬 방화벽 보안 정책을 통해 민첩성을 향상시고 있습니다.

### <a name="firewall-policy"></a>방화벽 정책

방화벽 정책은 NAT, 네트워크 및 응용 프로그램 규칙 컬렉션 및 위협 인텔리전스 설정을 포함하는 Azure 리소스입니다. 보안 가상 허브 및 *허브 가상 네트워크의*여러 Azure 방화벽 인스턴스에서 사용할 수 있는 글로벌 *리소스입니다.* 새 정책을 처음부터 만들거나 기존 정책에서 상속할 수 있습니다. 상속을 통해 DevOps는 조직에서 위임한 기본 정책 위에 로컬 방화벽 정책을 만들 수 있습니다. 정책은 지역 및 구독에서 작동합니다.
 
방화벽 정책 및 Azure 방화벽 관리자와의 연결을 만들 수 있습니다. 그러나 REST API, 템플릿, Azure PowerShell 및 CLI를 사용하여 정책을 만들고 관리할 수도 있습니다. 정책을 만든 후에는 가상 WAN 허브의 방화벽과 연결하여 *보안 가상 허브* 및/또는 가상 네트워크의 방화벽으로 가상 네트워크 허브 가상 *네트워크로*만들 수 있습니다.

### <a name="pricing"></a>가격 책정

정책은 방화벽 연결에 따라 청구됩니다. 방화벽 연결이 0이거나 하나인 정책은 무료입니다. 여러 방화벽 연결이 있는 정책은 고정 된 속도로 청구 됩니다. 자세한 내용은 [Azure 방화벽 관리자 가격 책정](https://azure.microsoft.com/pricing/details/firewall-manager/)을 참조하십시오.

## <a name="azure-firewall-management-partners"></a>Azure 방화벽 관리 파트너

다음 주요 타사 솔루션은 표준 Azure REST API를 사용하여 Azure 방화벽 중앙 관리를 지원합니다. 이러한 각 솔루션에는 고유한 특성과 기능이 있습니다.

- [알고섹 클라우드플로우](https://www.algosec.com/azure/) 
- [바라쿠다 클라우드 시큐리티 가디언](https://www.barracuda.com/products/cloudsecurityguardian/for_azure)
- [투핀 오르카](https://www.tufin.com/products/tufin-orca)


## <a name="next-steps"></a>다음 단계

Azure 방화벽 관리자 미리 보기에 대한 자세한 내용은 [Azure 방화벽 관리자 미리 보기란 무엇입니까?](../firewall-manager/overview.md)