---
title: Azure 방화벽 중앙 관리
description: Azure 방화벽 관리자 중앙 관리에 대 한 자세한 정보
author: vhorne
ms.service: firewall
services: firewall
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: 58f670f3f55a63f0c1823adc13c98f5863d4d650
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/18/2020
ms.locfileid: "77444551"
---
# <a name="azure-firewall-central-management"></a>Azure 방화벽 중앙 관리

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

여러 방화벽을 관리 하는 경우 방화벽 규칙을 지속적으로 변경 하 여 동기화 된 상태로 유지 하는 것을 알 수 있습니다. 중앙 IT 팀은 기본 방화벽 정책을 정의 하 고 여러 사업부에서 적용 하는 방법이 필요 합니다. 동시에 DevOps 팀은 더 나은 민첩성을 위해 자체 로컬 파생 방화벽 정책을 만들려고 합니다.

Azure 방화벽 관리자 미리 보기를 통해 이러한 문제를 해결할 수 있습니다.


## <a name="azure-firewall-manager-preview"></a>Azure 방화벽 관리자 미리 보기

Azure 방화벽 관리자 미리 보기는 클라우드 기반 보안 경계에 대 한 중앙 보안 정책 및 경로 관리를 제공 하는 네트워크 보안 관리 서비스입니다. 이를 통해 엔터프라이즈 IT 팀은 여러 Azure 방화벽 인스턴스 간의 트래픽 필터링을 위한 네트워크 및 응용 프로그램 수준 규칙을 쉽게 정의할 수 있습니다. 트래픽 거 버 넌 스 및 보호를 위한 허브 및 스포크 아키텍처에서 다양 한 Azure 지역 및 구독을 확장할 수 있습니다. 또한 조직 전체에서 구현 된 파생 된 로컬 방화벽 보안 정책을 사용 하 여 DevOps의 민첩성을 향상 시킬 수 있습니다.

### <a name="firewall-policy"></a>방화벽 정책

방화벽 정책은 NAT, 네트워크 및 응용 프로그램 규칙 컬렉션 및 위협 인텔리전스 설정을 포함 하는 Azure 리소스입니다. *보안 가상 허브* 및 *허브 가상 네트워크*의 여러 Azure 방화벽 인스턴스에서 사용할 수 있는 전역 리소스입니다. 새 정책은 처음부터 새로 만들거나 기존 정책에서 상속할 수 있습니다. 상속을 통해 DevOps는 조직의 기본 정책에 따라 로컬 방화벽 정책을 만들 수 있습니다. 정책은 지역 및 구독에서 작동 합니다.
 
Azure 방화벽 관리자를 사용 하 여 방화벽 정책 및 연결을 만들 수 있습니다. 그러나 REST API, 템플릿, Azure PowerShell 및 CLI를 사용 하 여 정책을 만들고 관리할 수도 있습니다. 정책을 만든 후에는 가상 WAN 허브의 방화벽과 연결 하 여 가상 네트워크에서 *보호 된 가상 허브* 및/또는 방화벽으로 it *허브를 Virtual Network*수 있습니다.

### <a name="pricing"></a>가격

정책은 방화벽 연결을 기준으로 요금이 청구 됩니다. 방화벽 연결이 0 개 또는 1 인 정책은 무료로 제공 됩니다. 여러 방화벽 연결을 사용 하는 정책은 고정 요금으로 청구 됩니다. 자세한 내용은 [Azure 방화벽 관리자 가격 책정](https://azure.microsoft.com/pricing/details/firewall-manager/)을 참조 하세요.

## <a name="azure-firewall-management-partners"></a>Azure 방화벽 관리 파트너

다음의 주요 타사 솔루션은 표준 Azure REST Api를 사용 하 여 Azure 방화벽 중앙 관리를 지원 합니다. 이러한 각 솔루션에는 고유한 특성 및 기능이 있습니다.

- [AlgoSec CloudFlow](https://www.algosec.com/azure/) 
- [Barracuda 클라우드 보안 보호자](https://www.barracuda.com/products/cloudsecurityguardian/for_azure)
- [Orca.exe](https://www.tufin.com/products/tufin-orca)


## <a name="next-steps"></a>다음 단계

Azure 방화벽 관리자 미리 보기에 대 한 자세한 내용은 [Azure 방화벽 관리자 미리 보기용 이란?](../firewall-manager/overview.md) 을 참조 하세요.