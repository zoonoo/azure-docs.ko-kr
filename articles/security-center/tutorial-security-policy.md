---
title: 보안 정책 작업 | Microsoft Docs
description: 이 문서에서는 Azure 보안 센터에서 보안 정책을 사용하여 작업하는 방법을 설명합니다.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 2d248817-ae97-4c10-8f5d-5c207a8019ea
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 45a27be400753b56c42310a340334feba8a420c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73906868"
---
# <a name="working-with-security-policies"></a>보안 정책 작업

이 문서에서는 보안 정책을 구성하는 방법 및 Security Center에서 보안 정책을 보는 방법을 설명합니다. 

## <a name="introduction-to-security-policies"></a>보안 정책 소개

보안 정책은 워크로드의 원하는 구성을 정의하고 회사 또는 레귤레이터의 보안 요구 사항을 준수하는데 도움이 됩니다.

Azure 보안 센터는 선택한 정책에 따라 보안 권장 사항을 제공합니다. 보안 센터 정책은 Azure 정책에서 만든 정책 이니셔티브를 기반으로 합니다. [Azure 정책을](../governance/policy/overview.md) 사용하여 정책을 관리하고 관리 그룹 및 여러 구독에서 정책을 설정할 수 있습니다.

보안 센터는 보안 정책 작업을 위한 다음 옵션을 제공합니다.

* **기본 제공 기본 정책 보기 및 편집** - 보안 센터를 사용하도록 설정하면 'ASC 기본값'이라는 기본 제공 이니셔티브가 모든 보안 센터 등록 구독(무료 또는 표준 계층)에 자동으로 할당됩니다. 이 이니셔티브를 사용자 지정하려면 해당 이니셔티브 내에서 개별 정책을 활성화하거나 사용하지 않도록 설정할 수 있습니다. 기본 제공 [보안 정책](security-center-policy-definitions.md) 목록을 참조하여 기본 제공 옵션을 확인하십시오.

* **사용자 지정 정책 추가** - 구독에 적용되는 보안 이니셔티브를 사용자 지정하려면 보안 센터 내에서 정책을 적용할 수 있습니다. 그러면 컴퓨터가 만든 정책을 따르지 않는 경우 권장 사항을 받게 됩니다. 사용자 지정 정책을 빌드하고 할당하는 방법에 대한 지침은 [사용자 지정 보안 정책 사용을](custom-security-policies.md)참조하십시오.

* **규정 준수 정책 추가** - 보안 센터의 규정 준수 대시보드는 특정 표준 또는 규정(예: Azure CIS, NIST SP 800-53 R4, SWIFT CSP CSCF-v2020)의 컨텍스트에서 환경 내의 모든 평가 상태를 표시합니다. 자세한 내용은 [규정 준수 개선을](security-center-compliance-dashboard.md)참조하십시오.


## <a name="managing-your-security-policies"></a>보안 정책 관리

Security Center에서 보안 정책을 보려면

1. **Security Center** 대시보드에서 **보안 정책**을 선택합니다.

    ![정책 관리 창](./media/security-center-policies/security-center-policy-mgt.png)

   **정책 관리** 화면에서 관리 그룹 구조뿐만 아니라 관리 그룹, 구독 및 작업 영역 수를 확인할 수 있습니다.

1. 확인하려는 구독 또는 관리 그룹의 정책을 선택합니다.

1. 해당 구독 또는 관리 그룹의 보안 정책 페이지가 나타납니다. 사용 가능하고 할당된 정책을 보여 주며, 이 정책은 사용 가능하고 할당된 정책을 보여 주며, 이 정책은 사용 가능

   ![정책 화면](./media/tutorial-security-policy/security-policy-page.png)

    > [!NOTE]
    > 기본 정책과 함께 "MG 상속"이라는 레이블이 있는 경우 정책이 관리 그룹에 할당되어 보고 있는 구독에 의해 상속되었음을 의미합니다.


1. 이 페이지에서 사용 가능한 옵션 중에서 선택합니다.

    1. 산업 정책에서 작업하려면 **표준 추가를 클릭합니다.** 자세한 내용은 [동적 규정 준수 패키지 업데이트를](update-regulatory-compliance-packages.md)참조하십시오.

    1. 사용자 지정 이니셔티브를 할당하고 관리하려면 **사용자 지정 이니셔티브 추가를**클릭합니다. 자세한 내용은 [사용자 지정 보안 정책 사용을](custom-security-policies.md)참조하십시오.

    1. 기본 정책을 보고 편집하려면 **유효 정책 보기를** 클릭하고 아래에 설명된 대로 진행합니다. 

       ![정책 화면](./media/security-center-policies/policy-screen.png)
       
       이 **보안 정책** 화면에는 선택한 구독 또는 관리 그룹에 할당된 정책이 수행한 작업이 반영됩니다.
       
       * 상단의 링크를 사용하여 구독 또는 관리 그룹에 적용되는 정책 **할당을** 엽니다. 이러한 링크를 사용하면 할당에 액세스하고 정책을 편집하거나 사용하지 않도록 설정할 수 있습니다. 예를 들어 특정 정책 할당이 엔드포인트 보호를 효과적으로 거부하는 경우 링크를 사용하여 정책을 편집하거나 사용하지 않도록 설정합니다.
       
       * 정책 목록에서 구독 또는 관리 그룹에 정책을 효과적으로 적용하는 것을 확인할 수 있습니다. 범위에 적용되는 각 정책의 설정이 고려되고 정책이 수행한 작업의 누적 결과가 표시됩니다. 예를 들어 정책의 한 할당에서 비활성화되었지만 다른 할당에서 AuditIfNotExist로 설정된 경우 누적 효과는 AuditIfNotExist를 적용합니다. 더 활성화된 효과가 항상 우선합니다.
       
       * 정책의 효과는 Append, Audit, AuditIfNotExists, Deny, DeployIfNotExists, Disabled일 수 있습니다. 효과를 적용하는 방법에 대한 자세한 내용은 [Policy 효과 이해](../governance/policy/concepts/effects.md)를 참조하세요.

       > [!NOTE]
       > 할당된 정책을 보는 경우 여러 할당을 확인할 수 있으며 각 할당이 자체적으로 구성되는 방법도 확인할 수 있습니다.


## <a name="who-can-edit-security-policies"></a>보안 정책은 누가 편집할 수 있나요?

REST API를 통해 또는 Windows PowerShell을 사용하여 Azure Policy 포털을 통해 보안 정책을 편집할 수 있습니다.

Security Center는 Azure에서 사용자, 그룹 및 서비스에 할당할 수 있는 기본 제공 역할을 제공하는 RBAC(역할 기반 액세스 제어)를 사용합니다. 사용자가 Security Center를 열면 액세스 권한이 있는 리소스와 관련된 정보만 표시됩니다. 즉, 사용자는 리소스의 구독에 *소유자,* *기여자*또는 *판독기의* 역할이 할당됩니다. 이러한 역할뿐만 아니라 두 가지 특정 보안 센터 역할이 있습니다.

- **보안 판독기**: 권장 사항, 경고, 정책 및 상태를 포함하는 보안 센터에 대한 보기 권한이 있지만 변경할 수는 없습니다.
- **보안 관리자**: *보안 판독기와*동일한 보기 권한을 가지며 보안 정책을 업데이트하고 권장 사항 및 경고를 해제할 수도 있습니다.


## <a name="disable-security-policies"></a>보안 정책 사용 안 함
기본 보안 정책이 사용자 환경과 관련이 없는 권장 사항을 생성하는 경우 권장 사항을 보내는 정책 정의를 사용하지 않도록 설정하여 중지할 수 있습니다.
권장 사항에 대한 자세한 내용은 [보안 권장 사항 관리를](security-center-recommendations.md)참조하십시오.

1. 보안 센터에서 **정책 & 준수** 섹션에서 보안 정책을 **클릭합니다.**

   ![정책 관리](./media/tutorial-security-policy/policy-management.png)

2. 권장 사항을 사용하지 않도록 설정하려는 구독 또는 관리 그룹을 클릭합니다.

   > [!NOTE]
   > 관리 그룹은 해당 구독에 정책을 적용합니다. 따라서 정책을 사용하지 않도록 설정한 구독이 동일한 정책을 사용하는 관리 그룹에 속해 있는 경우 정책 권장 사항을 계속 받게 됩니다. 관리 수준에서 계속 정책이 적용되고 권장 사항이 생성됩니다.

1. **유효 정책 보기를 클릭합니다.**

   ![정책 사용 안 함](./media/tutorial-security-policy/view-effective-policy.png)

1. 할당된 정책을 클릭합니다.

   ![정책 사용 안 함](./media/tutorial-security-policy/security-policy.png)

1. 매개 **변수** 섹션에서 비활성화할 권장 사항을 호출하는 정책을 검색하고 드롭다운 목록에서 사용 안 **함** 옵션을 선택합니다.

   ![정책 사용 안 함](./media/tutorial-security-policy/disable-policy.png)

1. **저장**을 클릭합니다.

   > [!NOTE]
   > 사용 안 함 정책 변경 사항이 적용되기까지 최대 12시간이 걸릴 수 있습니다.



## <a name="next-steps"></a>다음 단계
이 문서에서는 보안 정책에 대해 배웠습니다. 관련 정보는 다음 문서를 참조하십시오.

* PowerShell을 사용하여 정책을 설정하는 방법에 대한 지침은 [빠른 시작: Azure PowerShell 모듈을 사용하여 비준수 리소스를 식별하는 정책 할당 만들기를](../governance/policy/assign-policy-powershell.md) 참조하십시오.

* Azure Policy에서 보안 정책을 편집하는 방법에 대한 지침은 [규정 준수를 적용하기 위한 정책 만들기 및 관리를](../governance/policy/tutorials/create-and-manage.md)참조하십시오.

* Azure 정책을 사용하는 구독 또는 관리 그룹에서 정책을 설정하는 방법에 대한 지침은 [Azure 정책이란 무엇입니까?](../governance/policy/overview.md)