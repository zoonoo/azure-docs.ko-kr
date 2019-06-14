---
title: Azure AD 권한 관리 (미리 보기)-Azure Active Directory에서에서 보고서 및 로그 보기
description: 사용자 할당 보고서를 보려면 Azure Active Directory 권한 관리 (미리 보기)에서 로그를 감사 하는 방법을 알아봅니다.
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: jocastel-MSFT
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/19/2019
ms.author: rolyon
ms.reviewer: jocastel
ms.collection: M365-identity-device-management
ms.openlocfilehash: 60a61a581574c77a57939ea23fdadc7b060b82af
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "64541542"
---
# <a name="view-reports-and-logs-in-azure-ad-entitlement-management-preview"></a>보고서 보기 및 로그에서 Azure AD 권한 관리 (미리 보기)

> [!IMPORTANT]
> Azure AD(Azure Active Directory) 권한 관리는 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="view-resources-a-user-has-access-to"></a>사용자 리소스 보기에 대 한 액세스

1. **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽된 메뉴에서 클릭 **사용자 할당 보고서**합니다.

1. 클릭 **사용자 선택** 사용자 선택 창을 엽니다.

1. 액세스할 수 있는 리소스를 보고 하려는 목록에서 사용자를 찾습니다.

1. 사용자를 클릭 한 다음 클릭 **선택**합니다.

    사용자가 액세스할 수 있는 리소스 목록이 표시 됩니다. 패키지 액세스, 정책 및 날짜를 포함합니다.

    ![사용자 할당 보고서](./media/entitlement-management-reports/user-assignments-report.png)

## <a name="determine-the-status-of-a-users-request"></a>사용자의 요청 상태를 확인 합니다.

사용자 요청 하 고 액세스 패키지에 대 한 액세스를 수신 하는 방법에 대 한 추가 세부 정보를 가져오려면 Azure AD 감사 로그를 사용할 수 있습니다. 특히의 로그 레코드를 사용할 수 있습니다 합니다 `EntitlementManagement` 및 `UserManagement` 범주 각 요청에 대 한 처리 단계에서 추가 세부 정보를 가져오려고 합니다.  

1. 클릭 **Azure Active Directory** 을 클릭 한 다음 **감사 로그**합니다.

1. 맨 위에 있는 변경 합니다 **범주** 로 `EntitlementManagement` 또는 `UserManagement`원하는 감사 레코드에 따라 합니다.  

1. **적용**을 클릭합니다.

1. 로그를 다운로드 하려면 **다운로드**합니다.

Azure AD에 새 요청을 수신 하는 경우에 감사 레코드는 씁니다 합니다 **범주** 됩니다 `EntitlementManagement` 및 **활동** 일반적으로 `User requests access package assignment`합니다.  Azure portal에서 생성 하는 직접 할당의 경우는 **활동** 감사 레코드의 필드가 `Administrator directly assigns user to access package`, 할당을 수행 하는 사용자로 식별 됩니다는 **ActorUserPrincipalName**.

Azure AD는 요청이 진행 중인 동안 추가 감사 레코드를 기록 합니다 포함:

| Category | 작업 | 요청 상태 |
| :---- | :------------ | :------------ |
| `EntitlementManagement` | `Auto approve access package assignment request` | 요청 승인이 필요 하지 않습니다. |
| `UserManagement` | `Create request approval` | 요청 승인 필요 |
| `UserManagement` | `Add approver to request approval` | 요청 승인 필요 |
| `EntitlementManagement` | `Approve access package assignment request` | 승인 요청 |
| `EntitlementManagement` | `Ready to fulfill access package assignment request` |요청 승인 또는 승인이 필요 하지 않습니다. |

Azure AD는 사용자가 액세스를 할당 하는 경우에 대 한 감사 레코드를 씁니다 합니다 `EntitlementManagement` 사용 하 여 범주 **활동** `Fulfill access package assignment`합니다.  액세스를 받은 사용자를 식별 **ActorUserPrincipalName** 필드입니다.

액세스를 할당 하지 않은 경우 Azure AD에 대 한 감사 레코드를 씁니다 합니다 `EntitlementManagement` 범주 **활동** 중 하나 `Deny access package assignment request`요청을 승인자가 거부 된 경우, 또는 `Access package assignment request timed out (no approver action taken)`요청 하기 전에 시간이 초과 하는 경우는 승인자를 승인할 수 없습니다.

사용자의 액세스 패키지 할당 만료 되 면 사용자가 취소 되었거나 Azure AD에 대 한 감사 레코드를 작성 한 다음 관리자가 제거 된 `EntitlementManagement` 사용 하 여 범주 **활동** 의 `Remove access package assignment`합니다.

## <a name="next-steps"></a>다음 단계

- [Azure AD 권한 관리 문제 해결](entitlement-management-troubleshoot.md)
- [일반적인 시나리오](entitlement-management-scenarios.md)
