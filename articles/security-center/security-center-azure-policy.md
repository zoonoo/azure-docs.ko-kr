---
title: Security Center에서 보고 Azure Policy의 일부분으로 설정할 수 있는 Azure Security Center 보안 정책 | Microsoft Docs
description: 이 문서에서는 Azure Policy에서 정책을 설정하거나 Azure Security Center에서 정책을 보는 방법을 설명합니다.
services: security-center
documentationcenter: na
author: rkarlin
manager: mbaldwin
editor: ''
ms.assetid: cd906856-f4f9-4ddc-9249-c998386f4085
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/25/2018
ms.author: rkarlin
ms.openlocfilehash: 330b66e64484417e50f39c35cf90a6fd62b1e888
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/27/2018
ms.locfileid: "52334672"
---
# <a name="view-security-policies"></a>보안 정책 보기

이 문서에서는 보안 정책을 구성하는 방법 및 Security Center에서 보안 정책을 보는 방법을 설명합니다. Azure Security Center에서는 등록되는 각 구독에 자동으로 해당 [기본 제공 보안 정책](security-center-policy-definitions.md)을 할당합니다. 관리 그룹 및 여러 구독에서 정책을 설정할 수도 있는 [Azure Policy](../azure-policy/azure-policy-introduction.md)에서 보안 정책을 구성할 수 있습니다.

PowerShell을 사용하여 정책을 설정하는 방법에 대한 지침을 알아보려면 [빠른 시작: Azure RM PowerShell 모듈을 사용하여 비준수 리소스를 식별하는 정책 할당 만들기](../azure-policy/assign-policy-definition-ps.md)를 참조하세요.



## <a name="what-are-security-policies"></a>보안 정책이란?
보안 정책은 워크로드에서 원하는 구성을 정의하고 회사 또는 규정 보안 요구 사항을 준수하는 데 도움이 됩니다. Azure Policy에서 Azure 구독에 대한 정책을 정의하고 워크로드의 유형 또는 데이터의 민감도에 맞게 정책을 조정할 수 있습니다. 예를 들어 개인 식별 정보와 같이 규제된 데이터를 사용하는 응용 프로그램은 다른 작업보다 높은 수준의 보안이 필요할 수 있습니다. 여러 구독이나 관리 그룹에 걸쳐 정책을 설정하려면 [Azure Policy](../azure-policy/azure-policy-introduction.md)에서 정책을 설정합니다.



보안 정책에 따라 Azure Security Center에서 얻는 보안 권장 사항이 결정됩니다. 잠재적인 취약성을 식별하고 위협을 완화하는 데 도움이 되려면 보안 권장 사항 규정 준수를 모니터링할 수 있습니다. 적절한 옵션을 결정하는 방법에 대한 자세한 내용은 [기본 제공 보안 정책](security-center-policy-definitions.md) 목록을 참조하세요.

### <a name="management-groups"></a>관리 그룹
조직에 구독이 많으면 구독에 대한 액세스, 정책 및 준수를 효율적으로 관리하는 방법이 필요할 수 있습니다. Azure 관리 그룹은 구독 상위 수준의 범위를 제공합니다. "관리 그룹"이라는 컨테이너에 구독을 구성하고 거버넌스 정책을 관리 그룹에 적용합니다. 관리 그룹에 속하는 모든 구독은 관리 그룹에 적용되는 정책을 자동으로 상속합니다. 각 디렉터리에는 "루트" 관리 그룹이라고 하는 단일 최상위 관리 그룹이 부여됩니다. 이 루트 관리 그룹은 모든 관리 그룹과 구독이 루트 관리 그룹까지 접히도록 만들어집니다. 이 루트 관리 그룹은 전역 정책 및 RBAC 할당을 디렉터리 수준에서 적용하는 것을 허용합니다. Azure Security Center에서 사용하기 위해 관리 그룹을 설정하려면 [Azure Security Center에 대한 테넌트 수준 표시 유형 얻기](security-center-management-groups.md)의 지침을 따릅니다.

> [!NOTE]
> 관리 그룹 및 구독의 계층 구조를 이해하는 것은 중요합니다. 관리 그룹, 루트 관리 및 관리 그룹 액세스에 대해 자세히 알아보려면 [Azure 관리 그룹으로 리소스 구성](../governance/management-groups/index.md#root-management-group-for-each-directory)을 참조하세요.
>

## <a name="how-security-policies-work"></a>보안 정책 작동 방법
Security Center는 각 Azure 구독에 대한 기본 보안 정책을 자동으로 만듭니다. Azure Policy에서 정책을 편집하여 다음을 수행할 수 있습니다.
- 새 정책 정의를 만듭니다.
- 전체 조직 또는 조직 내 비즈니스 단위를 나타낼 수 있는 관리 그룹 및 구독에 정책을 할당합니다.
- 정책 준수를 모니터링합니다.

Azure Policy에 대한 자세한 내용은 [규정 준수를 적용하는 정책 만들기 및 관리](../azure-policy/create-manage-policy.md)를 참조하세요.

Azure 정책은 다음 구성 요소로 구성됩니다.

- **정책**은 규칙입니다.
- **이니셔티브**는 정책 컬렉션입니다.
- **할당**은 특정 범위(관리 그룹, 구독 또는 리소스 그룹)에 이니셔티브 또는 정책을 적용하는 것입니다.

리소스는 할당되는 정책으로 계산되고 해당 리소스가 준수하는 정책 수에 따라 규정 준수 비율을 받습니다.

## <a name="who-can-edit-security-policies"></a>보안 정책은 누가 편집할 수 있나요?
Security Center는 Azure에서 사용자, 그룹 및 서비스에 할당할 수 있는 기본 제공 역할을 제공하는 RBAC(역할 기반 액세스 제어)를 사용합니다. 사용자가 Security Center를 열면 액세스 권한이 있는 리소스와 관련된 정보만 표시됩니다. 즉, 리소스가 속한 리소스 그룹이나 구독에 대한 소유자, 참가자 또는 읽기 권한자 역할이 사용자에게 할당됩니다. 이러한 역할 외에도 두 개의 특정한 Security Center 역할이 있습니다.

- 보안 읽기 권한자: 권장 사항, 경고, 정책 및 상태를 비롯한 Security Center에 대한 권한을 볼 수 있지만 변경할 수는 없습니다.
- 보안 관리자: 보안 읽기 권한자와 동일한 보기 권한을 갖지만 보안 정책을 업데이트하고 권장 사항 및 경고를 해제할 수 있습니다.

## <a name="edit-security-policies"></a>보안 정책 편집
[Azure Policy](../governance/policy/tutorials/create-and-manage.md)에서 각 Azure 구독 및 관리 그룹에 대한 기본 보안 정책을 편집할 수 있습니다. 보안 정책을 수정하려면 구독 또는 포함된 관리 그룹의 소유자, 참가자 또는 보안 관리자여야 합니다.

Azure Policy에서 보안 정책을 편집하는 방법에 대한 지침은 [규정 준수를 적용하는 정책 만들기 및 관리](../governance/policy/tutorials/create-and-manage.md)를 참조하세요.

## <a name="view-security-policies"></a>보안 정책 보기

Security Center에서 보안 정책을 보려면

1. **Security Center** 대시보드에서 **보안 정책**을 선택합니다.

    ![정책 관리 창](./media/security-center-policies/security-center-policy-mgt.png)

  **정책 관리** 화면에서 관리 그룹 구조뿐만 아니라 관리 그룹, 구독 및 작업 영역 수를 확인할 수 있습니다.

  > [!NOTE]
  > - Security Center 대시보드는 **정책 관리** 아래에 표시된 구독 수보다 **구독 적용 범위** 아래에 더 많은 구독 수를 표시할 수 있습니다. 구독 적용 범위는 표준, 무료 및 "포함 안 된" 구독 수를 표시합니다. "포함 안 된" 구독은 Security Center를 사용하도록 설정하지 않고 **정책 관리** 아래에 표시되지도 않습니다.
  >

  테이블의 열은 다음과 같이 표시됩니다.

 - **정책 이니셔티브 할당** – 구독 또는 관리 그룹에 할당되는 Security Center [기본 제공 정책](security-center-policy-definitions.md) 및 이니셔티브입니다.
 - **규정 준수** - 관리 그룹, 구독 또는 작업 영역에 대한 전반적인 규정 준수 점수입니다. 점수는 할당의 가중치 평균입니다. 단일 할당의 정책 수 및 할당이 적용되는 리소스 수에서 가중치 평균 비율입니다.

 예를 들어 사용자 구독에 할당된 5개의 정책이 포함된 두 개의 VM 및 한 개의 이니셔티브가 있으면 구독에 10개의 평가가 있습니다. VM 중 하나가 두 개의 정책을 준수하지 않는 경우 구독 할당의 전반적인 준수 점수는 80%입니다.

 - **적용 범위** – 관리 그룹, 구독 또는 작업 영역이 실행되고 있는 무료 또는 평균의 가격 책정 계층을 식별합니다.  Security Center의 가격 책정 계층에 대해 자세히 알아보려면 [가격 책정](security-center-pricing.md)을 참조하세요.
 - **설정** - 구독에는 **설정 편집** 링크가 있습니다. **설정 편집**을 선택하면 각 구독 또는 관리 그룹에 대한 [Security Center 설정](security-center-policies-overview.md)을 업데이트할 수 있습니다.

2. 확인하려는 구독 또는 관리 그룹의 정책을 선택합니다.

  - **보안 정책** 화면에는 선택한 구독 또는 관리 그룹에 할당된 정책에서 수행된 동작이 표시됩니다.
  - 맨 위에서 구독 또는 관리 그룹에 적용된 각 정책 **할당**을 열기 위해 제공된 링크를 사용합니다. 해당 링크를 사용하여 할당에 액세스하고 정책을 편집하거나 사용하지 않도록 설정할 수 있습니다. 예를 들어 특정 정책 할당이 엔드포인트 보호를 효과적으로 거부하는지 확인하는 경우 해당 링크를 사용하여 정책에 액세스하여 편집하거나 사용하지 않도록 설정할 수 있습니다.
  - 정책 목록에서 구독 또는 관리 그룹에 정책을 효과적으로 적용하는 것을 확인할 수 있습니다. 즉, 범위에 적용되는 각 정책의 설정이 고려되고 정책에서 수행되는 작업의 누적 결과가 제공됩니다. 예를 들어 하나의 할당에서 정책이 사용하지 않도록 설정되지만 다른 할당에서 정책이 AuditIfNotExist로 설정되는 경우 누적 효과는 AuditIfNotExist로 적용됩니다. 더 활성화된 효과가 항상 우선합니다.
  - 정책의 효과는 Append, Audit, AuditIfNotExists, Deny, DeployIfNotExists, Disabled일 수 있습니다. 효과를 적용하는 방법에 대한 자세한 내용은 [Policy 효과 이해](../governance/policy/concepts/effects.md)를 참조하세요.

   ![정책 화면](./media/security-center-policies/policy-screen.png)

> [!NOTE]
> - 할당된 정책을 보는 경우 여러 할당을 확인할 수 있으며 각 할당이 자체적으로 구성되는 방법도 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 Security Center에서 보안 정책을 구성하는 방법을 알아보았습니다. Security Center에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure Security Center 계획 및 작업 가이드](security-center-planning-and-operations-guide.md): Azure Security Center에 대한 디자인 고려 사항을 계획하고 이해하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 상태 모니터링](security-center-monitoring.md): Azure 리소스의 상태를 모니터링하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 경고 관리 및 대응](security-center-managing-and-responding-alerts.md): 보안 경고를 관리하고 대응하는 방법을 알아봅니다.
* [Azure Security Center에서 파트너 솔루션 모니터링](security-center-partner-solutions.md): 파트너 솔루션의 상태를 모니터링하는 방법을 알아봅니다.
* [Azure Security Center에 대한 테넌트 수준 표시 유형 얻기](security-center-management-groups.md): Azure Security Center에 대한 관리 그룹을 설정하는 방법을 알아봅니다.
* [Azure Security Center FAQ](security-center-faq.md): 서비스 사용에 관한 질문과 대답을 가져옵니다.
* [Azure 보안 블로그](https://blogs.msdn.com/b/azuresecurity/) - Azure 보안 및 규정 준수에 관한 블로그 게시물을 찾습니다.

Azure Policy에 대해 자세히 알아보려면 [Azure Policy란?](../azure-policy/azure-policy-introduction.md)을 참조하세요.
