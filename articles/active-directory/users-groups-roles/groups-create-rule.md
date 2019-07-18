---
title: 동적 그룹 만들기 및 상태 확인 - Azure Active Directory | Microsoft Docs
description: Azure portal에서 그룹 멤버 자격 규칙을 만드는, 상태를 확인 하는 방법.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: f828ff83e6b9c60eb08edef7f47e88185fb5aef8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60472173"
---
# <a name="create-a-dynamic-group-and-check-status"></a>동적 그룹 만들기 및 상태 확인

Azure Active Directory (Azure AD)에서 사용자 또는 장치 속성을 기반으로 그룹 멤버 자격을 확인 하려면 규칙을 사용할 수 있습니다. 이 문서에서는 Azure portal에서 동적 그룹에 대 한 규칙을 설정 하는 방법을 설명 합니다.
보안 그룹 또는 Office 365 그룹에 대 한 동적 멤버 자격 지원 됩니다. 그룹 멤버 자격 규칙을 적용 하면 사용자 및 장치 특성 멤버 관리 규칙을 사용 하 여 일치 항목에 대 한 평가 됩니다. 사용자 또는 장치에 대 한 특성 변경 되 면 조직에서 모든 동적 그룹 규칙은 멤버 자격 변경에 대 한 처리 됩니다. 사용자 및 장치를 추가 하거나 그룹에 대 한 조건을 충족 하는 경우 제거 합니다.

구문, 지원 되는 속성, 연산자 및 멤버 자격 규칙에 대 한 값의 예제를 참조 하세요 [Azure Active Directory에서 그룹에 대 한 동적 멤버 자격 규칙](groups-dynamic-membership.md)합니다.

## <a name="to-create-a-group-membership-rule"></a>그룹 멤버 자격 규칙 만들기

1. 에 로그인 합니다 [Azure AD 관리 센터](https://aad.portal.azure.com) 전역 관리자, Intune 관리자 또는 테 넌 트의 사용자 관리자 역할에 있는 계정으로 합니다.
2. **그룹**을 선택합니다.
3. **모든 그룹**을 선택하고 **새 그룹**을 선택합니다.

   ![새 그룹을 추가 하는 명령을 선택 합니다.](./media/groups-create-rule/new-group-creation.png)

4. 에 **그룹** 페이지에서 이름 및 새 그룹에 대 한 설명을 입력 합니다. 선택 된 **멤버 자격 유형** 사용자 또는 장치 및 선택 **동적 쿼리 추가**합니다. 규칙 작성기를 사용 하 여 간단한 규칙을 작성 하려면 또는 [멤버 자격 규칙을 직접 작성할](groups-dynamic-membership.md)합니다.

   ![동적 그룹 멤버 자격 규칙 추가](./media/groups-create-rule/add-dynamic-group-rule.png)

5. 멤버 자격 쿼리를 사용할 수 있는 사용자 지정 확장 속성을 확인 하려면
   1. 선택 **사용자 지정 확장 속성 가져오기**
   2. 응용 프로그램 ID를 입력 하 고 선택한 **속성을 새로 고침**합니다. 
6. 규칙을 만든 후 블레이드 맨 아래에서 **쿼리 추가**를 선택합니다.
7. **만들기** on the **그룹** 을 선택하여 그룹을 만듭니다.

입력 한 규칙을 유효 하지 않으면 규칙을 처리할 수 없습니다는 이유 설명은 포털의 오른쪽 위 모서리에 표시 됩니다. 규칙을 수정 하는 방법을 이해 하도록 신중 하 게를 읽습니다.

## <a name="turn-on-or-off-welcome-email"></a>환영 이메일 켜기 또는 끄기

새 Office 365 그룹을 만들 때 시작 알림 그룹에 추가 된 사용자를 전송 됩니다. 나중에 사용자 또는 장치의 특성이 변경, 조직에서 모든 동적 그룹 규칙은 멤버 자격 변경에 대 한 처리 됩니다. 그런 다음, 추가된 사용자도 환영 알림을 받습니다. 이 동작을 해제할 수 있습니다 [Exchange PowerShell](https://docs.microsoft.com/powershell/module/exchange/users-and-groups/Set-UnifiedGroup?view=exchange-ps)합니다. 

## <a name="check-processing-status-for-a-rule"></a>규칙에 대 한 처리 상태를 확인 합니다.

그룹에 대한 **개요** 페이지에서 멤버 자격 처리 상태 및 마지막으로 업데이트한 날짜를 확인할 수 있습니다.
  
  ![동적 그룹 상태 표시](./media/groups-create-rule/group-status.png)

**멤버 자격 처리** 상태에 대해 다음 상태 메시지가 표시될 수 있습니다.

* **평가 중**:  그룹 변경 내용이 수신되었으며 업데이트가 평가 중입니다.
* **처리 중**: 업데이트가 처리 중입니다.
* **업데이트 완료**: 처리가 완료되었으며 적용 가능한 모든 업데이트가 수행되었습니다.
* **처리 오류**:  멤버 자격 규칙을 평가 하는 오류로 인해 처리를 완료할 수 없습니다.
* **업데이트 일시 중지됨**: 관리자가 동적 멤버 자격 규칙 업데이트를 일시 중지했습니다. MembershipRuleProcessingState가 “일시 중지됨”으로 설정됩니다.

**마지막으로 업데이트된 멤버 자격** 상태에 대해 다음 상태 메시지가 표시될 수 있습니다.

* &lt;**날짜 및 시간**&gt;: 멤버 자격이 마지막으로 업데이트된 시간입니다.
* **진행 중**: 업데이트가 현재 진행 중입니다.
* **알 수 없음**: 마지막 업데이트 시간을 검색할 수 없습니다. 그룹을 새 수 있습니다.

특정 그룹에 대한 멤버 자격 규칙을 처리하는 동안 오류가 발생하면 해당 그룹에 대한 **개요 페이지** 맨 위에 경고가 표시됩니다. 테넌트 내의 모든 그룹에 대해 보류 중인 동적 멤버 자격 업데이트를 24시간 이상 동안 처리할 수 없는 경우 **모든 그룹** 위에 경고가 표시됩니다.

![오류 메시지 경고를 처리합니다.](./media/groups-create-rule/processing-error.png)

이러한 문서는 Azure Active Directory의 그룹에 대한 추가 정보를 제공합니다.

* [기존 그룹 보기](../fundamentals/active-directory-groups-view-azure-portal.md)
* [새 그룹을 만들고 멤버 추가](../fundamentals/active-directory-groups-create-azure-portal.md)
* [그룹의 설정 관리](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [그룹의 멤버 자격 관리](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [그룹의 사용자에 대한 동적 규칙 관리](groups-dynamic-membership.md)
