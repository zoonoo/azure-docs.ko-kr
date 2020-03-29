---
title: Azure 보안 센터의 테넌트 간 관리 | 마이크로 소프트 문서
description: Azure 위임리소스 관리를 사용하여 보안 센터에서 여러 테넌트의 보안 상태를 관리하기 위해 테넌트 간 관리를 설정하는 방법을 알아봅니다.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 7d51291a-4b00-4e68-b872-0808b60e6d9c
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/11/2019
ms.author: memildin
ms.openlocfilehash: 2aeb2ab4cfb4ed5e8652638aaced320cc7119d3e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919484"
---
# <a name="cross-tenant-management-in-security-center"></a>보안 센터의 테넌트 간 관리

테넌트 간 관리를 사용하면 [Azure 위임리소스 관리를](../lighthouse/concepts/azure-delegated-resource-management.md) 활용하여 보안 센터에서 여러 테넌트의 보안 상태를 보고 관리할 수 있습니다. 각 테넌트의 디렉터리에 로그인할 필요 없이 단일 보기에서 여러 테넌트를 효율적으로 관리할 수 있습니다.

- 서비스 공급자는 자신의 테넌트 내에서 여러 고객을 위해 리소스의 보안 상태를 관리할 수 있습니다.

- 여러 테넌트와 조직의 보안 팀은 단일 위치에서 보안 상태를 보고 관리할 수 있습니다.

## <a name="set-up-cross-tenant-management"></a>테넌트 간 관리 설정

[Azure 위임된](../lighthouse/concepts/azure-delegated-resource-management.md)리소스 관리를 사용하여 관리되는 테넌트의 리소스에 대한 액세스를 자신의 테넌트에 위임하여 테넌트 간 관리를 설정합니다.

> [!NOTE]
> Azure 위임 리소스 관리는 Azure Lighthouse의 주요 구성 요소 중 하나입니다.

## <a name="how-does-cross-tenant-management-work-in-security-center"></a>보안 센터에서 테넌트 간 관리가 어떻게 작동합니까?

단일 테넌트에서 여러 구독을 관리하는 것과 동일한 방식으로 여러 테넌트에서 구독을 검토하고 관리할 수 있습니다.

상단 메뉴 모음에서 필터 아이콘을 클릭하고 각 테넌트의 디렉터리에서 구독을 선택합니다.

  ![필터 테넌트](./media/security-center-cross-tenant-management/cross-tenant-filter.png)

뷰와 작업은 기본적으로 동일합니다. 예를 들어 다음과 같은 노래를 선택할 수 있다.

- **보안 정책 관리**: 한 보기에서 [정책을](tutorial-security-policy.md)사용하여 많은 리소스의 보안 상태를 관리하고, 보안 권장 사항을 사용하여 조치를 취하고, 보안 관련 데이터를 수집 및 관리합니다.
- **보안 점수 및 규정 준수 자세 개선**: 테넌트 간 가시성을 통해 모든 테넌트의 전반적인 보안 상태를 볼 수 있으며 각 테넌트에 대한 보안 [점수](security-center-secure-score.md) 및 규정 [준수 상태를](security-center-compliance-dashboard.md) 가장 잘 개선할 수 있는 위치와 방법을 확인할 수 있습니다.
- **권장 사항 수정**: 한 번에 다양한 테넌트의 여러 리소스에 대한 [권장 사항을](security-center-recommendations.md) 모니터링하고 수정합니다. 그런 다음 모든 테넌트에서 가장 높은 위험을 초래하는 취약점을 즉시 해결할 수 있습니다.
- 경고 관리 : 다른 테넌트 전체에서 [경고를 검색합니다.](security-center-alerts-overview.md) **Manage Alerts** 실행 가능한 [수정 단계를](security-center-managing-and-responding-alerts.md)준수하지 않는 리소스에 대해 조치를 취합니다.

- **고급 클라우드 방어 기능 관리**: [JIT(적시) VM 액세스,](security-center-just-in-time.md) [적응형 네트워크 강화,](security-center-adaptive-network-hardening.md) [적응형 애플리케이션 제어](security-center-adaptive-application.md)등과 같은 다양한 위협 보호 서비스를 관리합니다.
 
## <a name="next-steps"></a>다음 단계
이 문서에서는 보안 센터에서 테넌트 간 관리가 작동하는 방식을 설명합니다. 보안 센터에 대한 자세한 내용은 다음을 참조하세요.

* [Azure 보안 센터를 사용하여 보안 태세 강화](security-center-monitoring.md) - Azure 리소스의 상태를 모니터링하는 방법을 알아봅니다.
* [Azure Security Center FAQ](faq-general.md) - 서비스 사용에 관한 질문과 대답을 찾습니다.
* [엔터프라이즈 시나리오에서 Azure 등대에 대해 알아보기](https://docs.microsoft.com/azure/lighthouse/concepts/enterprise) - Azure 등대에서 여러 Azure AD 테넌트를 사용하는 엔터프라이즈 내에서 교차 테넌트 관리를 간소화하는 방법을 알아봅니다.