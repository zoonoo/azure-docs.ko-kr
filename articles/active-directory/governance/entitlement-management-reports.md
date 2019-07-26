---
title: Azure AD 자격 관리 (미리 보기)에서 보고서 및 로그 보기-Azure Active Directory
description: Azure Active Directory 자격 관리 (미리 보기)에서 사용자 할당 보고서 및 감사 로그를 보는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: jocastel-MSFT
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/19/2019
ms.author: ajburnle
ms.reviewer: jocastel
ms.collection: M365-identity-device-management
ms.openlocfilehash: d33b4751b421f5af1536af9a88d15e060ab59bdb
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68489072"
---
# <a name="view-reports-and-logs-in-azure-ad-entitlement-management-preview"></a>Azure AD 자격 관리 (미리 보기)에서 보고서 및 로그 보기

> [!IMPORTANT]
> Azure AD(Azure Active Directory) 권한 관리는 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="view-resources-a-user-has-access-to"></a>사용자가 액세스할 수 있는 리소스 보기

1. **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽 메뉴에서 **사용자 할당 보고서**를 클릭 합니다.

1. **사용자 선택** 을 클릭 하 여 사용자 선택 창을 엽니다.

1. 목록에서 액세스 권한이 있는 리소스를 보려는 사용자를 찾습니다.

1. 사용자를 클릭 한 다음 **선택**을 클릭 합니다.

    사용자가 액세스할 수 있는 리소스 목록이 표시 됩니다. 여기에는 액세스 패키지, 정책 및 날짜가 포함 됩니다.

    ![사용자 할당 보고서](./media/entitlement-management-reports/user-assignments-report.png)

## <a name="determine-the-status-of-a-users-request"></a>사용자의 요청 상태 확인

사용자가 액세스 패키지에 대 한 액세스를 요청 하 고 수신 하는 방법에 대 한 자세한 내용을 보려면 Azure AD 감사 로그를 사용할 수 있습니다. 특히 `EntitlementManagement` 및`UserManagement` 범주의 로그 레코드를 사용 하 여 각 요청에 대 한 처리 단계에 대 한 추가 정보를 가져올 수 있습니다.  

1. **Azure Active Directory** 클릭 하 고 **감사 로그**를 클릭 합니다.

1. 위쪽에서 검색 하려는 감사 레코드  에 따라 범주 `EntitlementManagement` 를 `UserManagement`또는로 변경 합니다.  

1. **적용**을 클릭합니다.

1. 로그를 다운로드 하려면 **다운로드**를 클릭 합니다.

Azure AD가 새 요청을 받으면 **범주가** `EntitlementManagement` 이 고 **활동이** 일반적으로 `User requests access package assignment`인 감사 레코드를 작성 합니다.  Azure Portal에서 생성 된 직접 할당의 경우 감사 레코드의 **작업** 필드는이 `Administrator directly assigns user to access package`고 할당을 수행 하는 사용자는 **ActorUserPrincipalName**로 식별 됩니다.

다음을 포함 하 여 요청이 진행 되는 동안 Azure AD에서 추가 감사 레코드를 작성 합니다.

| 범주 | 활동 | 요청 상태 |
| :---- | :------------ | :------------ |
| `EntitlementManagement` | `Auto approve access package assignment request` | 요청에 승인이 필요 하지 않음 |
| `UserManagement` | `Create request approval` | 요청에 승인이 필요 함 |
| `UserManagement` | `Add approver to request approval` | 요청에 승인이 필요 함 |
| `EntitlementManagement` | `Approve access package assignment request` | 승인 된 요청 |
| `EntitlementManagement` | `Ready to fulfill access package assignment request` |요청이 승인 되었거나 승인이 필요 하지 않음 |

사용자에 게 액세스 권한이 할당 되 면 Azure AD는 **활동** `Fulfill access package assignment`을 사용 하 `EntitlementManagement` 여 범주에 대 한 감사 레코드를 작성 합니다.  액세스를 받은 사용자는 **ActorUserPrincipalName** 필드로 식별 됩니다.

액세스가 할당 되지 않은 경우 Azure AD는 승인자가 요청을 거부 한 경우 `EntitlementManagement` **활동이** `Deny access package assignment request`있는 범주에 대 한 감사 레코드를 기록 하거나 `Access package assignment request timed out (no approver action taken)`승인자가 승인할 수 있도록 요청 시간이 초과 된 경우을 (를) 기록 합니다.

사용자의 액세스 패키지 할당이 만료 되거나 사용자가 취소 하거나 관리자가 제거 하면 Azure AD는 `EntitlementManagement` **활동** `Remove access package assignment`을 포함 하는 범주에 대 한 감사 레코드를 작성 합니다.

## <a name="next-steps"></a>다음 단계

- [Azure AD 자격 관리 문제 해결](entitlement-management-troubleshoot.md)
- [일반적인 시나리오](entitlement-management-scenarios.md)
