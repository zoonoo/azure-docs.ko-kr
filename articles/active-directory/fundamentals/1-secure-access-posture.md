---
title: Azure Active Directory에서 외부 공동 작업을 위한 보안 상태 결정
description: 외부 액세스 보안 계획을 실행 하기 전에 달성할 수 있는 사항을 결정 해야 합니다.
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
ms.openlocfilehash: 63d6c37f6cd32985e540164ef8b308652a5e7414
ms.sourcegitcommit: 44844a49afe8ed824a6812346f5bad8bc5455030
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/23/2020
ms.locfileid: "97744244"
---
# <a name="determine-your-security-posture-for-external-access"></a>외부 액세스를 위한 보안 상태 결정 

외부 액세스를 관리 하는 것을 고려 하 여 조직에 대 한 보안 및 공동 작업 요구를 각 시나리오 내에서 평가 해야 합니다. 조직 수준에서 일상적인 공동 작업을 수행 하는 데 필요한 IT 팀의 제어 용량을 고려 합니다. 규제 산업의 조직은 더 많은 IT 제어를 요구할 수 있습니다. 예를 들어, 방어 계약자는 각 외부 사용자, 액세스 및 액세스 제거를 명확 하 게 식별 하 고 문서화 해야 할 수 있습니다. 이 요구 사항은 모든 액세스 권한 또는 특정 시나리오 또는 워크 로드에 있을 수 있습니다. 스펙트럼의 다른 쪽 끝에서 컨설팅 회사는 일반적으로 특정 IT 가드 레일 내에서 최종 사용자가 공동 작업 해야 하는 외부 사용자를 결정할 수 있습니다. 

![공동 작업의 최종 사용자 제어와 비교](media/secure-external-access/1-overall-control.png)

> [!NOTE]
> 공동 작업을 지나치게 엄격 하 게 제어 하면 IT 예산이 높아지고 생산성을 줄이고 지연 된 비즈니스 결과를 초래할 수 있습니다. 공식 공동 작업 채널이 너무 번거로운으로 인식 되 면 최종 사용자는 보안 되지 않은 문서를 전자 메일로 보내는 예를 비롯 하 여 작업을 수행 하는 데 제공 된 시스템을 활용 하는 경향이 있습니다.

## <a name="think-in-terms-of-scenarios"></a>시나리오 측면에서 고려

대부분의 경우 보안을 위한 가드 레일을 제공 하는 동시에 일부 시나리오에서 파트너 액세스를 위임할 수 있습니다. IT 가드 레일은 직원이 파트너와 공동 작업을 수행 하 여 작업을 수행할 수 있도록 하는 동시에 지적 재산을 안전 하 게 유지 하는 데 도움이 될 수 있습니다.

조직 내에서 시나리오를 고려할 때 리소스에 대 한 직원 및 비즈니스 파트너의 액세스 필요성을 평가 합니다. 은행에는 사용자 계정 정보와 같은 특정 리소스에 대 한 액세스를 제한 하는 규정 준수 요구 사항이 있을 수 있습니다. 반대로, 동일한 은행이 마케팅 캠페인에서 작업 하는 파트너에 게 위임 된 액세스를 사용 하도록 설정할 수 있습니다.

![시나리오 당 continuum의 거 버 넌 스](media\secure-external-access\1-scenarios.png)

각 시나리오에서 다음을 고려 하십시오. 

* 위험에 대 한 정보 민감도

* 파트너가 다른 사용자에 대해 볼 수 있는 항목을 제한 해야 하는지 여부

* 위반 비용은 중앙 제어 및 최종 사용자 마찰의 무게와 비교 합니다.

 또한 중앙에서 관리 되는 제어를 시작 하 여 규정 준수 목표를 충족 하 고 시간에 따라 최종 사용자에 게 제어를 위임할 수 있습니다. 모든 액세스 관리 모델은 조직 내에서 동시에 공존할 수 있습니다. 

[파트너 관리 자격 증명](../external-identities/what-is-b2b.md) 을 사용 하면 외부 사용자가 자신의 회사 리소스에 대 한 액세스 권한을 상실 한 후 리소스에 대 한 액세스를 종료 하는 필수 신호를 조직에 제공 합니다.

## <a name="goals-of-securing-external-access"></a>외부 액세스 보안 목표

IT 관리 및 위임 된 액세스의 목표가 다릅니다.

**IT 제어 액세스의 주요 목표는 다음과 같습니다.**

* GRC (거 버 넌 스, 규정 및 규정 준수) 목표를 충족 합니다. 

* 파트너 액세스와 구성원 사용자, 그룹 및 기타 파트너에 대해 볼 수 있는 파트너를 긴밀 하 게 제어 합니다.

**액세스를 위임 하는 주요 목표는 다음과 같습니다.**

* IT 제약 조건 내에서 비즈니스 소유자가 공동 작업을 관리할 수 있도록 합니다.

* 비즈니스 소유자가 정의 하는 규칙에 따라 액세스를 요청 하도록 비즈니스 파트너를 설정 합니다.

조직 및 시나리오에 대해 다음을 수행 해야 합니다. 

* **응용 프로그램, 데이터 및 콘텐츠에 대 한 액세스를 제어** 합니다. [AZURE AD](https://azure.microsoft.com/pricing/details/active-directory/) 의 버전 및 [Microsoft 365](https://www.microsoft.com/microsoft-365/compare-microsoft-365-enterprise-plans)따라 다양 한 방법을 통해이를 수행할 수 있습니다. 

* **공격 노출 영역을 줄입니다**. [권한 있는 id 관리](../privileged-identity-management/pim-configure.md), [DLP (데이터 손실 방지)](https://docs.microsoft.com/exchange/security-and-compliance/data-loss-prevention/data-loss-prevention) 및 [암호화 기능은](https://docs.microsoft.com/exchange/security-and-compliance/data-loss-prevention/data-loss-prevention) 공격 노출 영역을 줄입니다.

* **정기적으로 작업 및 감사 로그를 검토 하 여 규정 준수를 확인** 합니다. 액세스 검토는 자격 관리를 통해 비즈니스 소유자에 게 액세스 결정을 위임할 수 있으며, 액세스 검토는 주기적으로 지속적으로 액세스를 확인 하는 방법을 제공 합니다. 민감도 레이블을 사용 하 여 자동화 된 데이터 분류를 사용 하면 중요 한 콘텐츠의 암호화를 자동화 하 여 직원의 최종 사용자가 쉽게 준수할 수 있습니다.

## <a name="next-steps"></a>다음 단계 

리소스에 대 한 외부 액세스 보안에 대 한 다음 문서를 참조 하세요. 이러한 작업은 나열 된 순서 대로 수행 하는 것이 좋습니다.

1. [외부 액세스를 위한 보안](1-secure-access-posture.md) 상태를 확인 합니다.

2. [현재 상태 검색](2-secure-access-current-state.md)

3. [거 버 넌 스 계획 만들기](3-secure-access-plan.md)

4. [보안을 위해 그룹 사용](4-secure-access-groups.md)

5. [Azure AD B2B로 전환](5-secure-access-b2b.md)

6. [권한 관리를 사용 하 여 안전 하 게 액세스](6-secure-access-entitlement-managment.md)

7. [조건부 액세스 정책을 사용 하 여 안전 하 게 액세스](7-secure-access-conditional-access.md)

8. [민감도 레이블을 사용 하 여 안전 하 게 액세스](8-secure-access-sensitivity-labels.md)

9. [Microsoft 팀, OneDrive 및 SharePoint에 대 한 액세스 보호](9-secure-access-teams-sharepoint.md)
 

 
