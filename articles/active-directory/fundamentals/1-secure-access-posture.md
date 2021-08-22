---
title: Azure Active Directory 외부 협업을 위한 보안 태세 확인
description: 외부 액세스 보안 계획을 실행하려면 먼저 달성 목표를 결정해야 합니다.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4fb5d74f0c912db098b5e309793963d5f07fbd58
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/14/2021
ms.locfileid: "113730355"
---
# <a name="determine-your-security-posture-for-external-access"></a>외부 액세스를 위한 보안 상태 결정 

외부 액세스 제어를 고려할 때 조직 전체 및 각 시나리오 내에서 보안 및 협업 요구 사항을 평가해야 합니다. 조직 수준에서 IT 팀이 일상적인 협업을 통해 수행해야 하는 제어의 양을 고려합니다. 규제 산업의 조직에는 더 많은 IT 제어가 필요할 수 있습니다. 예를 들어 방어 계약자는 각 외부 사용자, 액세스 권한 및 액세스 제거를 적극적으로 식별하고 문서화해야 할 수 있습니다. 이 요구 사항은 모든 액세스 또는 특정 시나리오 또는 워크로드에 있을 수 있습니다. 스펙트럼의 다른 쪽 끝에서 컨설팅 회사는 일반적으로 최종 사용자가 특정 IT 가드레일 내에서 공동 작업해야 하는 외부 사용자를 결정하도록 허용할 수 있습니다. 

![IT 및 협업의 최종 사용자 제어](media/secure-external-access/1-overall-control.png)

> [!NOTE]
> 협업을 과도하게 엄격하게 제어하면 IT 예산이 증가하고 생산성이 저하되며 비즈니스 결과가 지연될 수 있습니다. 공식 협업 채널이 너무 번거롭다고 인식되면 최종 사용자는 IT 제공 시스템을 탐색하여 작업을 완료하는 경향이 있습니다(예: 보안되지 않은 문서 메일 보내기).

## <a name="think-in-terms-of-scenarios"></a>시나리오의 관점에서 생각해 보세요.

대부분의 경우 IT는 보안을 위해 가드레일을 제공하면서 적어도 일부 시나리오에서 파트너 액세스를 위임할 수 있습니다. IT 가드 레일은 지적 재산권이 안전하게 유지되도록 하는 동시에 직원들이 파트너와 협업하여 작업을 수행할 수 있도록 하는 데 도움이 될 수 있습니다.

조직 내의 시나리오를 고려할 때 리소스에 대한 직원 및 비즈니스 파트너 액세스의 필요성을 평가합니다. 은행에서는 사용자 계정 정보와 같은 특정 리소스에 대한 액세스를 소규모 내부 직원 그룹으로 제한하는 규정 준수 요구 사항이 있을 수 있습니다. 반대로, 동일한 은행에서 마케팅 캠페인을 진행하는 파트너에게 위임된 액세스를 사용하도록 설정할 수 있습니다.

![시나리오별 거버넌스 연속성](media\secure-external-access\1-scenarios.png)

각 시나리오에서 다음을 고려하십시오. 

* 위험에 대한 정보 민감도

* 파트너가 다른 사용자에 대해 볼 수 있는 항목을 제한해야 하는지 여부

* 위반 비용은 중앙 제어 및 최종 사용자 마찰의 무게와 비교됩니다.

 또한 중앙에서 관리되는 제어를 시작하여 규정 준수 목표를 충족하고 시간에 따라 최종 사용자에게 제어를 위임할 수 있습니다. 모든 액세스 관리 모델은 조직 내에서 동시에 공존할 수 있습니다. 

[파트너 관리 자격 증명](../external-identities/what-is-b2b.md) 을 사용하면 외부 사용자가 자신의 회사 리소스에 대한 액세스 권한을 상실한 후 리소스에 대한 액세스를 종료 하는 필수 신호를 조직에 제공합니다.

## <a name="goals-of-securing-external-access"></a>외부 액세스 보안 목표

IT 관리와 위임된 액세스의 목표가 다릅니다.

**IT 제어 액세스의 주요 목표는 다음과 같습니다.**

* GRC(거버넌스, 규정 및 규정 준수) 목표를 충족합니다. 

* 파트너 액세스와 구성원 사용자, 그룹 및 기타 파트너에 대해 볼 수 있는 파트너를 긴밀하게 제어합니다.

**액세스를 위임하는 주요 목표는 다음과 같습니다.**

* IT 제약 조건 내에서 비즈니스 소유자가 공동 작업을 관리할 수 있도록 합니다.

* 비즈니스 소유자가 정의 하는 규칙에 따라 액세스를 요청 하도록 비즈니스 파트너를 설정합니다.

조직 및 시나리오에 대해 다음을 수행해야 합니다. 

* **응용 프로그램, 데이터 및 콘텐츠에 대한 액세스를 제어** 합니다. [Azure AD](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing) 의 버전 및 [Microsoft 365](https://www.microsoft.com/microsoft-365/compare-microsoft-365-enterprise-plans)의 버전에 따라 다양한 방법을 통해 이를 수행할 수 있습니다. 

* **공격 노출 영역 축소**. [Privileged identity management](../privileged-identity-management/pim-configure.md), [DLP(데이터 손실 방지)](/exchange/security-and-compliance/data-loss-prevention/data-loss-prevention) 및 [암호화 기능은](/exchange/security-and-compliance/data-loss-prevention/data-loss-prevention) 공격 노출 영역을 줄입니다.

* **정기적으로 활동 및 감사 로그를 검토하여 규정 준수를 확인합니다**. IT는 권한 관리를 통해 비즈니스 소유자에게 액세스 결정을 위임할 수 있지만 액세스 검토는 지속적인 액세스를 주기적으로 확인하는 방법을 제공합니다. 민감도 레이블이 있는 자동화된 데이터 분류는 직원 최종 사용자가 쉽게 준수할 수 있도록 중요한 콘텐츠의 암호화를 자동화하는 데 도움이 됩니다.

## <a name="next-steps"></a>다음 단계 

리소스에 대한 외부 액세스 보안에 관해 다음 문서를 참조하세요. 작업은 나열된 순서대로 수행하는 것이 좋습니다.

1. [외부 액세스에 대한 보안 태세를 결정합니다](1-secure-access-posture.md)(사용자는 여기 있습니다.)

2. [현재 상태 파악](2-secure-access-current-state.md)

3. [거버넌스 계획 만들기](3-secure-access-plan.md)

4. [보안을 위해 그룹 사용](4-secure-access-groups.md)

5. [Azure AD B2B로 전환](5-secure-access-b2b.md)

6. [권한 관리를 사용한 보안 액세스](6-secure-access-entitlement-managment.md)

7. [조건부 액세스 정책을 사용한 보안 액세스](7-secure-access-conditional-access.md)

8. [민감도 레이블을 사용한 보안 액세스](8-secure-access-sensitivity-labels.md)

9. [Microsoft Teams, OneDrive 및 SharePoint에 대한 보안 액세스](9-secure-access-teams-sharepoint.md)
 

