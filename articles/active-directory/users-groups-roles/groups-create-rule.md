---
title: Azure Active Directory에서 동적 그룹 만들기 및 상태 확인 | Microsoft Docs
description: Azure Portal에서 그룹 멤버 자격 규칙을 만들고 상태를 확인하는 방법입니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 09/20/2018
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: e418316a74ccf27ec730261957a8b6c64de5d063
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47040604"
---
# <a name="create-a-dynamic-group-and-check-status"></a>동적 그룹 만들기 및 상태 확인

Azure AD(Azure Active Directory)에서는 사용자 또는 장치 속성에 따라 멤버 자격을 결정하는 규칙을 적용하여 그룹을 만들 수 있습니다. 사용자 또는 장치의 특성이 변경되면 Azure AD에서 Azure AD 테넌트의 모든 동적 그룹 규칙을 평가하고 추가 또는 제거 작업을 수행합니다. 사용자 또는 장치에서 그룹에 대한 규칙이 충족되면 해당 사용자 또는 장치를 멤버로 추가하고, 규칙이 더 이상 충족되지 않으면 제거합니다.

이 문서에서는 Azure Portal에서 보안 그룹 또는 Office 365 그룹의 동적 멤버 자격에 대한 규칙을 설정하는 방법에 대해 자세히 설명합니다. 규칙 구문의 예제와 멤버 자격 규칙에 지원되는 속성, 연산자 및 값의 전체 목록은 [Azure Active Directory의 그룹에 대한 동적 멤버 자격 규칙](groups-dynamic-membership.md)을 참조하세요.

## <a name="to-create-a-group-membership-rule"></a>그룹 멤버 자격 규칙 만들기

1. 테넌트의 글로벌 관리자, Intune 서비스 관리자 또는 사용자 계정 관리자 역할인 계정으로 [Azure AD 관리 센터](https://aad.portal.azure.com)에 로그인합니다.
2. **그룹**을 선택합니다.
3. **모든 그룹**을 선택하고 **새 그룹**을 선택합니다.

   ![새 그룹 추가](./media/groups-create-rule/new-group-creation.png)

4. **그룹** 블레이드에서 새 그룹에 대한 이름 및 설명을 입력합니다. 사용자 또는 장치에 대한 규칙을 만들지 여부에 따라 **동적 사용자** 또는 **동적 장치** 중에서 **멤버 자격 유형**을 선택한 다음 **동적 쿼리 추가**를 선택합니다. 규칙 작성기를 사용하여 간단한 규칙을 작성하거나 멤버 자격 규칙을 직접 작성할 수 있습니다. 이 문서에는 멤버 자격 규칙의 예제뿐만 아니라 사용 가능한 사용자 및 장치 특성에 대한 자세한 정보를 포함되어 있습니다.

   ![동적 멤버 자격 규칙 추가](./media/groups-create-rule/add-dynamic-group-rule.png)

5. 규칙을 만든 후 블레이드 맨 아래에서 **쿼리 추가**를 선택합니다.
6. **만들기** on the **그룹**을 선택하여 그룹을 만듭니다.

> [!TIP]
> 입력한 규칙의 형식이 잘못되었거나 규칙이 유효하지 않은 경우, 그룹 만들기가 실패합니다. 규칙을 처리할 수 없는 이유에 대한 설명을 포함하여 포털의 오른쪽 상단 모서리에 알림이 표시됩니다. 이 알림을 신중하게 읽고 규칙을 유효하게 수정하는 방법을 이해하세요.

## <a name="check-processing-status-for-a-membership-rule"></a>멤버 자격 규칙에 대한 처리 상태 확인

그룹에 대한 **개요** 페이지에서 멤버 자격 처리 상태 및 마지막으로 업데이트한 날짜를 확인할 수 있습니다.
  
  ![동적 그룹 상태 표시](./media/groups-create-rule/group-status.png)

**멤버 자격 처리** 상태에 대해 다음 상태 메시지가 표시될 수 있습니다.

* **평가 중**: 그룹 변경 내용이 수신되었으며 업데이트가 평가 중입니다.
* **처리 중**: 업데이트가 처리 중입니다.
* **업데이트 완료**: 처리가 완료되었으며 적용 가능한 모든 업데이트가 수행되었습니다.
* **처리 오류**: 멤버 자격 규칙을 평가하는 동안 오류가 발생했으며 처리를 완료할 수 없습니다.
* **업데이트 일시 중지됨**: 관리자가 동적 멤버 자격 규칙 업데이트를 일시 중지했습니다. MembershipRuleProcessingState가 “일시 중지됨”으로 설정됩니다.

**마지막으로 업데이트된 멤버 자격** 상태에 대해 다음 상태 메시지가 표시될 수 있습니다.

* &lt;**날짜 및 시간**&gt;: 멤버 자격이 마지막으로 업데이트된 시간입니다.
* **진행 중**: 업데이트가 현재 진행 중입니다.
* **알 수 없음**: 마지막 업데이트 시간을 검색할 수 없습니다. 그룹이 새로 작성되고 있기 때문일 수 있습니다.

특정 그룹에 대한 멤버 자격 규칙을 처리하는 동안 오류가 발생하면 해당 그룹에 대한 **개요 페이지** 맨 위에 경고가 표시됩니다. 테넌트 내의 모든 그룹에 대해 보류 중인 동적 멤버 자격 업데이트를 24시간 이상 동안 처리할 수 없는 경우 **모든 그룹** 위에 경고가 표시됩니다.

![처리 오류 메시지](./media/groups-create-rule/processing-error.png)

이러한 문서는 Azure Active Directory의 그룹에 대한 추가 정보를 제공합니다.

* [기존 그룹 보기](../fundamentals/active-directory-groups-view-azure-portal.md)
* [새 그룹을 만들고 멤버 추가](../fundamentals/active-directory-groups-create-azure-portal.md)
* [그룹의 설정 관리](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [그룹의 멤버 자격 관리](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [그룹의 사용자에 대한 동적 규칙 관리](groups-dynamic-membership.md)
