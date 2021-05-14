---
title: Azure Firewall 중앙 관리
description: Azure Firewall Manager 중앙 관리에 대해 알아봅니다.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: victorh
ms.openlocfilehash: 23a7682d8a64de57db4ff9ae785ada90d4a06944
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87084673"
---
# <a name="azure-firewall-central-management"></a>Azure Firewall 중앙 관리

여러 방화벽을 관리하는 경우 방화벽 규칙을 지속적으로 변경하면 방화벽을 동기화된 상태로 유지하기가 어렵습니다. 중앙 IT 팀은 기준 방화벽 정책을 정의하고 여러 사업부에서 이 정책을 적용하도록 할 방법이 있어야 합니다. 그와 동시에 DevOps 팀은 민첩성 향상을 위해 자체 로컬 파생 방화벽 정책을 만들려고 합니다.

Azure Firewall Manager는 이러한 문제를 해결하는 데 도움이 될 수 있습니다.


## <a name="azure-firewall-manager"></a>Azure Firewall Manager

Azure Firewall Manager는 클라우드 기반 보안 경계에 대한 중앙 보안 정책 및 경로 관리를 제공하는 네트워크 보안 관리 서비스입니다. 이 서비스를 통해 엔터프라이즈 IT 팀은 여러 Azure Firewall 인스턴스 간에 트래픽을 필터링하는 데 사용되는 네트워크 및 애플리케이션 수준 규칙을 중앙에서 쉽게 정의할 수 있습니다. 트래픽 거버넌스 및 보호를 위해 허브 및 스포크 아키텍처의 다양한 Azure 지역 및 구독으로 범위를 확장할 수 있습니다. 또한 조직 전체에서 구현되는 파생된 로컬 방화벽 보안 정책을 통해 DevOps의 민첩성이 향상되도록 지원합니다.

### <a name="firewall-policy"></a>방화벽 정책

방화벽 정책은 NAT, 네트워크, 애플리케이션 규칙 컬렉션 및 위협 인텔리전스 설정이 포함된 Azure 리소스입니다. *보안 가상 허브* 및 *허브 가상 네트워크* 의 여러 Azure Firewall 인스턴스에서 사용할 수 있는 글로벌 리소스입니다. 새 정책은 처음부터 새로 만들거나 기존 정책에서 상속할 수 있습니다. DevOps는 상속을 사용하여 조직에서 규정한 기본 정책에 따라 로컬 방화벽 정책을 만들 수 있습니다. 정책은 여러 지역 및 구독에서 작동합니다.
 
방화벽 정책을 만들어 Azure Firewall Manager와 연결할 수 있습니다. 하지만 REST API, 템플릿, Azure PowerShell, CLI를 사용하여 정책을 만들고 관리할 수도 있습니다. 정책을 만든 후에는 가상 WAN 허브의 방화벽과 연결하여 ‘보안 가상 허브’로 설정하거나 가상 네트워크의 방화벽과 연결하여 ‘허브 가상 네트워크’로 설정할 수 있습니다. 

### <a name="pricing"></a>가격 책정

정책에 대한 요금은 방화벽 연결을 기준으로 청구됩니다. 방화벽 연결이 없거나 하나인 정책은 무료입니다. 방화벽 연결이 여러 개인 정책은 고정 요율로 청구됩니다. 자세한 내용은 [Azure Firewall Manager 가격](https://azure.microsoft.com/pricing/details/firewall-manager/)을 참조하세요.

## <a name="azure-firewall-management-partners"></a>Azure Firewall 관리 파트너

다음의 주요 타사 솔루션은 표준 Azure REST API를 사용하여 Azure Firewall 중앙 관리를 지원합니다. 해당 솔루션은 각각 고유한 특성과 기능을 갖추고 있습니다.

- [AlgoSec CloudFlow](https://www.algosec.com/azure/) 
- [Barracuda Cloud Security Guardian](https://www.barracuda.com/products/cloudsecurityguardian/for_azure)
- [Tufin Orca](https://www.tufin.com/products/tufin-orca)


## <a name="next-steps"></a>다음 단계

Azure Firewall Manager에 대한 자세한 내용은 [Azure Firewall Manager란?](../firewall-manager/overview.md)을 참조하세요.