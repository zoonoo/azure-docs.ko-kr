---
title: 동적 그룹 규칙 업데이트 및 관리 및 멤버 자격 문제 해결-Azure Active Directory | Microsoft Docs
description: Azure Portal에서 그룹 멤버 자격 규칙을 만드는 방법 상태를 확인 합니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 08/12/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ce3bce5e2656efea0a4fc3d7aa6be46f1e6926ec
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2019
ms.locfileid: "69657347"
---
# <a name="update-a-dynamic-group-to-manage-membership-in-azure-active-directory"></a>Azure Active Directory의 멤버 자격을 관리 하도록 동적 그룹 업데이트

Azure Active Directory (Azure AD)에서 규칙을 사용 하 여 사용자 또는 장치 속성에 따라 그룹 멤버 자격을 확인할 수 있습니다. 이 문서에서는 Azure Portal에서 동적 그룹에 대 한 규칙을 설정 하는 방법을 설명 합니다.
동적 멤버 자격은 보안 그룹 또는 Office 365 그룹에 대해 지원 됩니다. 그룹 멤버 자격 규칙을 적용 하는 경우 사용자 및 장치 특성은 멤버 자격과 일치 하는지 평가 됩니다. 사용자 또는 장치에 대 한 특성이 변경 되 면 조직의 모든 동적 그룹 규칙이 멤버 자격 변경에 대해 처리 됩니다. 사용자 및 장치는 그룹에 대 한 조건을 충족 하는 경우 추가 되거나 제거 됩니다.

멤버 자격 규칙에 대 한 구문, 지원 되는 속성, 연산자 및 값의 예는 [Azure Active Directory의 그룹에 대 한 동적 멤버 자격 규칙](groups-dynamic-membership.md)을 참조 하세요.

## <a name="to-update-a-group-membership-rule"></a>그룹 멤버 자격 규칙을 업데이트 하려면

1. 전역 관리자, Intune 관리자 또는 테 넌 트의 사용자 관리자 역할에 있는 계정을 사용 하 여 [AZURE AD 관리 센터](https://aad.portal.azure.com) 에 로그인 합니다.
1. **그룹** > **모든 그룹**을 선택 합니다.
1. 그룹을 선택 하 여 해당 프로필을 엽니다.
1. 그룹에 대 한 프로필 페이지에서 **동적 멤버 자격 규칙**을 선택 합니다. 규칙 작성기는 최대 5 개의 식을 지원 합니다. 여섯 번째 또는 임의의 후속 식을 추가 하려면 텍스트 상자를 사용 해야 합니다.

   ![동적 그룹에 대 한 멤버 자격 규칙 추가](./media/groups-update-rule/update-dynamic-group-rule.png)

1. 멤버 자격 규칙에 사용할 수 있는 사용자 지정 확장 속성을 보려면 다음을 수행 합니다.
   1. **사용자 지정 확장 속성 가져오기** 선택
   2. 응용 프로그램 ID를 입력 하 고 **속성 새로 고침**을 선택 합니다.
1. 규칙을 업데이트 한 후 **저장**을 선택 합니다.

입력 한 규칙이 유효 하지 않은 경우에는 포털의 Azure 알림에 규칙이 처리 되지 않는 이유에 대 한 설명이 표시 됩니다. 규칙을 수정 하는 방법을 이해 하려면 신중 하 게 읽어 보세요.

## <a name="check-processing-status-for-a-rule"></a>규칙에 대 한 처리 상태 확인

그룹에 대한 **개요** 페이지에서 멤버 자격 처리 상태 및 마지막으로 업데이트한 날짜를 확인할 수 있습니다.
  
  ![동적 그룹 상태 표시](./media/groups-create-rule/group-status.png)

**멤버 자격 처리** 상태에 대해 다음 상태 메시지가 표시될 수 있습니다.

* **평가 중**:  그룹 변경 내용이 수신되었으며 업데이트가 평가 중입니다.
* **처리 중**: 업데이트가 처리 중입니다.
* **업데이트 완료**: 처리가 완료되었으며 적용 가능한 모든 업데이트가 수행되었습니다.
* **처리 오류**:  멤버 자격 규칙을 평가 하는 동안 오류가 발생 하 여 처리를 완료할 수 없습니다.
* **업데이트 일시 중지됨**: 관리자가 동적 멤버 자격 규칙 업데이트를 일시 중지했습니다. MembershipRuleProcessingState가 “일시 중지됨”으로 설정됩니다.

**마지막으로 업데이트된 멤버 자격** 상태에 대해 다음 상태 메시지가 표시될 수 있습니다.

* **날짜 및 시간**: 멤버 자격이 마지막으로 업데이트된 시간입니다.
* **진행 중**: 업데이트가 현재 진행 중입니다.
* **알 수 없음**: 마지막 업데이트 시간을 검색할 수 없습니다. 새 그룹 일 수 있습니다.

특정 그룹에 대한 멤버 자격 규칙을 처리하는 동안 오류가 발생하면 해당 그룹에 대한 **개요 페이지** 맨 위에 경고가 표시됩니다. 테넌트 내의 모든 그룹에 대해 보류 중인 동적 멤버 자격 업데이트를 24시간 이상 동안 처리할 수 없는 경우 **모든 그룹** 위에 경고가 표시됩니다.

![오류 메시지 경고 처리](./media/groups-create-rule/processing-error.png)

## <a name="next-steps"></a>다음 단계

이러한 문서는 Azure AD의 동적 그룹 작업에 대 한 추가 정보를 제공 합니다.

* 동적 규칙 구조에 대 한 전체 참조는 [동적 멤버 관리 규칙 구문](groups-dynamic-membership.md)을 참조 하세요.
* [정적 멤버 자격 그룹을 만들고 멤버를 추가](../fundamentals/active-directory-groups-create-azure-portal.md)합니다.
