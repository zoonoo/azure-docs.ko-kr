---
title: 동적 그룹을 만들거나 편집하고 상태를 가져옵니다 - Azure AD | 마이크로 소프트 문서
description: Azure 포털에서 그룹 구성원 규칙을 만들거나 업데이트하고 처리 상태를 확인하는 방법
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/07/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d2ed7f27e2145f666f38eec5ddc6c985a4d32138
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266378"
---
# <a name="create-or-update-a-dynamic-group-in-azure-active-directory"></a>Azure Active Directory에서 동적 그룹 만들기 또는 업데이트

Azure Active Directory(Azure AD)에서 규칙을 사용하여 사용자 또는 장치 속성에 따라 그룹 구성원 자격을 결정할 수 있습니다. 이 문서에서는 Azure Portal에서 동적 그룹에 대한 규칙을 설정하는 방법을 설명합니다.
동적 구성원은 보안 그룹 또는 Office 365 그룹에 대해 지원됩니다. 그룹 구성원 규칙이 적용되면 구성원 규칙과 일치하는 사용자 및 장치 특성이 평가됩니다. 사용자 또는 장치에 대한 특성이 변경되면 조직의 모든 동적 그룹 규칙이 구성원 변경에 대해 처리됩니다. 사용자 및 장치가 그룹의 조건을 충족하는 경우 추가되거나 제거됩니다. 보안 그룹은 장치 또는 사용자에 대해 사용할 수 있지만 Office 365 그룹은 사용자 그룹일 수 있습니다.

## <a name="rule-builder-in-the-azure-portal"></a>Azure 포털의 규칙 작성기

Azure AD는 중요한 규칙을 더 빨리 만들고 업데이트할 수 있는 규칙 빌더를 제공합니다. 규칙 작성기는 최대 5개의 식을 구성을 지원합니다. 규칙 작성기를 사용하면 몇 가지 간단한 표현식으로 규칙을 쉽게 만들 수 있지만 모든 규칙을 재현하는 데 사용할 수는 없습니다. 규칙 작성기에서 만들려는 규칙을 지원하지 않는 경우 텍스트 상자를 사용할 수 있습니다.

다음은 텍스트 상자를 사용하여 구성하는 것이 좋습니다 고급 규칙 또는 구문의 몇 가지 예입니다.

- 식이 5개 이상인 규칙
- 직접 보고서 규칙
- [연산자 우선 순위](groups-dynamic-membership.md#operator-precedence) 설정
- [복잡한 식이 있는 규칙;](groups-dynamic-membership.md#rules-with-complex-expressions) 예를 들어`(user.proxyAddresses -any (_ -contains "contoso"))`

> [!NOTE]
> 규칙 작성기는 텍스트 상자에 생성된 일부 규칙을 표시하지 못할 수 있습니다. 규칙 작성기에서 규칙을 표시할 수 없는 경우 메시지가 표시될 수 있습니다. 규칙 작성기는 지원되는 구문, 유효성 검사 또는 동적 그룹 규칙 처리를 어떤 식으로든 변경하지 않습니다.

![동적 그룹에 대한 멤버 자격 규칙 추가](./media/groups-create-rule/update-dynamic-group-rule.png)

멤버 자격 규칙에 대한 구문, 지원되는 속성, 연산자 및 값의 예는 [Azure Active Directory의 그룹에 대한 동적 구성원 자격 규칙을](groups-dynamic-membership.md)참조하십시오.

## <a name="to-create-a-group-membership-rule"></a>그룹 멤버 자격 규칙 만들기

1. 전역 관리자, Intune 관리자 또는 테넌트의 사용자 관리자 역할에 있는 계정으로 [Azure AD 관리자 센터에](https://aad.portal.azure.com) 로그인합니다.
1. **그룹을**검색하고 선택합니다.
1. **모든 그룹**을 선택하고 **새 그룹**을 선택합니다.

   ![새 그룹을 추가하는 명령을 선택합니다.](./media/groups-create-rule/create-new-group-azure-active-directory.png)

1. **그룹** 페이지에서 새 그룹에 대한 이름과 설명을 입력합니다. 사용자 또는 장치에 대한 **멤버 자격 유형을** 선택한 다음 동적 쿼리 **추가를**선택합니다. 규칙 작성기는 최대 5개의 식을 지원합니다. 다섯 개 이상의 식을 추가하려면 텍스트 상자를 사용해야 합니다.

   ![동적 그룹에 대한 멤버 자격 규칙 추가](./media/groups-create-rule/add-dynamic-group-rule.png)

1. 멤버 자격 쿼리에 사용할 수 있는 사용자 지정 확장 속성을 보려면 다음을 수행합니다.
   1. **사용자 지정 확장 속성 받기** 선택
   1. 응용 프로그램 ID를 입력한 다음 **속성 새로 고침을**선택합니다.
1. 규칙을 만든 후 **저장을**선택합니다.
1. 그룹을 만들려면 **새 그룹** 페이지에서 **만들기를** 선택합니다.

입력한 규칙이 유효하지 않으면 규칙을 처리할 수 없는 이유에 대한 설명이 포털의 Azure 알림에 표시됩니다. 규칙을 수정하는 방법을 이해하려면 주의 깊게 읽으십시오.

## <a name="to-update-an-existing-rule"></a>기존 규칙을 업데이트하려면

1. 전역 관리자, 그룹 관리자, Intune 관리자 또는 테넌트의 사용자 관리자 역할에 있는 계정으로 [Azure AD 관리자 센터에](https://aad.portal.azure.com) 로그인합니다.
1. **그룹** > **모든 그룹**.
1. 그룹을 선택하여 해당 프로파일을 엽니다.
1. 그룹의 프로필 페이지에서 **동적 멤버 자격 규칙을**선택합니다. 규칙 작성기는 최대 5개의 식을 지원합니다. 다섯 개 이상의 식을 추가하려면 텍스트 상자를 사용해야 합니다.

   ![동적 그룹에 대한 멤버 자격 규칙 추가](./media/groups-create-rule/update-dynamic-group-rule.png)

1. 멤버 자격 규칙에 사용할 수 있는 사용자 지정 확장 속성을 보려면 다음을 수행하십시오.
   1. **사용자 지정 확장 속성 받기** 선택
   1. 응용 프로그램 ID를 입력한 다음 **속성 새로 고침을**선택합니다.
1. 규칙을 업데이트한 후 **저장을**선택합니다.

## <a name="turn-on-or-off-welcome-email"></a>환영 이메일 켜기 또는 끄기

새 Office 365 그룹을 만들면 그룹에 추가된 사용자에게 환영 전자 메일 알림이 전송됩니다. 나중에 사용자 또는 장치 속성이 변경되면 조직의 모든 동적 그룹 규칙이 구성원 변경에 대해 처리됩니다. 그런 다음, 추가된 사용자도 환영 알림을 받습니다. [Exchange PowerShell](https://docs.microsoft.com/powershell/module/exchange/users-and-groups/Set-UnifiedGroup?view=exchange-ps)에서 이 동작을 끌 수 있습니다.

## <a name="check-processing-status-for-a-rule"></a>규칙에 대한 처리 상태 확인

그룹에 대한 **개요** 페이지에서 멤버 자격 처리 상태 및 마지막으로 업데이트한 날짜를 확인할 수 있습니다.
  
  ![동적 그룹 상태 표시](./media/groups-create-rule/group-status.png)

**멤버 자격 처리** 상태에 대해 다음 상태 메시지가 표시될 수 있습니다.

- **평가 중**: 그룹 변경 내용이 수신되었으며 업데이트가 평가 중입니다.
- **처리 중**: 업데이트가 처리 중입니다.
- **업데이트 완료**: 처리가 완료되었으며 적용 가능한 모든 업데이트가 수행되었습니다.
- **오류 처리**: 멤버 자격 규칙을 평가하는 오류로 인해 처리를 완료할 수 없습니다.
- **업데이트 일시 중지됨**: 관리자가 동적 멤버 자격 규칙 업데이트를 일시 중지했습니다. MembershipRuleProcessingState가 “일시 중지됨”으로 설정됩니다.

**마지막으로 업데이트된 멤버 자격** 상태에 대해 다음 상태 메시지가 표시될 수 있습니다.

- &lt;**일시 :** 멤버십이 마지막으로 업데이트된 시간입니다.&gt;
- **진행 중**: 업데이트가 현재 진행 중입니다.
- **알 수 없음**: 마지막 업데이트 시간을 검색할 수 없습니다. 그룹이 새 그룹일 수 있습니다.

특정 그룹에 대한 멤버 자격 규칙을 처리하는 동안 오류가 발생하면 해당 그룹에 대한 **개요 페이지** 맨 위에 경고가 표시됩니다. 테넌트 내의 모든 그룹에 대해 24시간 이상 보류 중인 동적 멤버 자격 업데이트를 처리할 수 없는 경우 **모든 그룹의**맨 위에 경고가 표시됩니다.

![오류 메시지 경고 처리](./media/groups-create-rule/processing-error.png)

이러한 문서는 Azure Active Directory의 그룹에 대한 추가 정보를 제공합니다.

- [기존 그룹 보기](../fundamentals/active-directory-groups-view-azure-portal.md)
- [새 그룹을 만들고 멤버 추가](../fundamentals/active-directory-groups-create-azure-portal.md)
- [그룹의 설정 관리](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [그룹의 멤버 자격 관리](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [그룹의 사용자에 대한 동적 규칙 관리](groups-dynamic-membership.md)
