---
title: Azure AD 자격 관리에서 보고서 및 로그 보기-Azure Active Directory
description: Azure Active Directory 자격 관리에서 사용자 할당 보고서 및 감사 로그를 보는 방법에 대해 알아봅니다.
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
ms.date: 10/28/2019
ms.author: ajburnle
ms.reviewer: jocastel
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3302fc3b2513794cd66d1ebf6db2cbcdb0f713dd
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73173890"
---
# <a name="view-reports-and-logs-in-azure-ad-entitlement-management"></a>Azure AD 자격 관리에서 보고서 및 로그 보기

Azure AD 자격 관리 보고서 및 Azure AD 감사 로그는 사용자가 액세스할 수 있는 리소스에 대 한 추가 정보를 제공 합니다. 관리자는 사용자에 대 한 액세스 패키지 및 리소스 할당을 보고 감사 목적으로 요청 로그를 보거나 사용자 요청 상태를 확인할 수 있습니다. 이 문서에서는 자격 관리 보고서 및 Azure AD 감사 로그를 사용 하는 방법을 설명 합니다.

자격 관리에서 사용자가 액세스할 수 있는 리소스를 확인 하는 방법을 알아보려면 다음 비디오를 시청 하세요.

>[!VIDEO https://www.youtube.com/embed/omtNJ7ySjS0]

## <a name="view-access-packages-for-a-user"></a>사용자에 대 한 액세스 패키지 보기

이 보고서를 사용 하면 사용자가 요청할 수 있는 모든 액세스 패키지와 현재 사용자에 게 할당 된 액세스 패키지를 나열할 수 있습니다.

**필수 역할:** 전역 관리자 또는 사용자 관리자

1. **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽 메뉴에서 **보고서**를 클릭 합니다.

1. **사용자에 대 한 패키지 액세스**를 클릭 합니다.

1. **사용자 선택** 을 클릭 하 여 사용자 선택 창을 엽니다.

1. 목록에서 사용자를 찾은 다음 **선택**을 클릭 합니다.

    **요청 가능** 탭에는 사용자가 요청할 수 있는 액세스 패키지 목록이 표시 됩니다. 이 목록은 액세스 패키지에 대해 정의 된 [요청 정책](entitlement-management-access-package-request-policy.md#for-users-in-your-directory) 에 따라 결정 됩니다. 

    ![사용자에 대 한 패키지 액세스](./media/entitlement-management-reports/access-packages-report.png)

1. 액세스 패키지에 대 한 리소스 역할 또는 정책이 둘 이상 있는 경우 리소스 역할 또는 정책 항목을 클릭 하 여 선택 세부 정보를 확인 합니다.

1. **할당 됨** 탭을 클릭 하 여 현재 사용자에 게 할당 된 액세스 패키지의 목록을 확인 합니다. 액세스 패키지가 사용자에 게 할당 된 경우 사용자는 액세스 패키지의 모든 리소스 역할에 액세스할 수 있습니다.

## <a name="view-resource-assignments-for-a-user"></a>사용자에 대 한 리소스 할당 보기

이 보고서를 사용 하면 자격 관리에서 사용자에 게 현재 할당 된 리소스를 나열할 수 있습니다. 이 보고서는 권한 관리를 사용 하 여 관리 되는 리소스에 대 한 것입니다. 사용자가 권한 관리 외부의 디렉터리에 있는 다른 리소스에 액세스할 수 있습니다.

**필수 역할:** 전역 관리자 또는 사용자 관리자

1. **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽 메뉴에서 **보고서**를 클릭 합니다.

1. **사용자에 대 한 리소스 할당**을 클릭 합니다.

1. **사용자 선택** 을 클릭 하 여 사용자 선택 창을 엽니다.

1. 목록에서 사용자를 찾은 다음 **선택**을 클릭 합니다.

    현재 사용자에 게 할당 된 리소스 목록이 표시 됩니다. 이 목록에는 액세스에 대 한 시작 및 종료 날짜와 함께 리소스 역할을 받은 액세스 패키지 및 정책도 표시 됩니다.
    
    사용자가 두 개 이상의 패키지에서 동일한 리소스에 액세스할 수 있는 경우 화살표를 클릭 하 여 각 패키지와 정책을 볼 수 있습니다.

    ![사용자에 대 한 리소스 할당](./media/entitlement-management-reports/resource-assignments-report.png)

## <a name="determine-the-status-of-a-users-request"></a>사용자의 요청 상태 확인

사용자가 액세스 패키지에 대 한 액세스를 요청 하 고 수신 하는 방법에 대 한 자세한 내용을 보려면 Azure AD 감사 로그를 사용할 수 있습니다. 특히 `EntitlementManagement`의 로그 레코드와 `UserManagement` 범주를 사용 하 여 각 요청에 대 한 처리 단계에 대 한 추가 정보를 가져올 수 있습니다.  

1. **Azure Active Directory** 클릭 하 고 **감사 로그**를 클릭 합니다.

1. 위쪽에서 검색 하려는 감사 레코드에 따라 **범주** 를 `EntitlementManagement` 또는 `UserManagement`로 변경 합니다.  

1. **Apply**를 클릭합니다.

1. 로그를 다운로드 하려면 **다운로드**를 클릭 합니다.

Azure AD가 새 요청을 받으면 **범주가** `EntitlementManagement` 되 고 **활동이** 일반적으로 `User requests access package assignment`되는 감사 레코드를 작성 합니다.  Azure Portal에서 직접 할당을 만든 경우 감사 레코드의 **작업** 필드가 `Administrator directly assigns user to access package`되며 할당을 수행 하는 사용자는 **ActorUserPrincipalName**로 식별 됩니다.

다음을 포함 하 여 요청이 진행 되는 동안 Azure AD에서 추가 감사 레코드를 작성 합니다.

| 범주 | 작업 | 요청 상태 |
| :---- | :------------ | :------------ |
| `EntitlementManagement` | `Auto approve access package assignment request` | 요청에 승인이 필요 하지 않음 |
| `UserManagement` | `Create request approval` | 요청에 승인이 필요 함 |
| `UserManagement` | `Add approver to request approval` | 요청에 승인이 필요 함 |
| `EntitlementManagement` | `Approve access package assignment request` | 승인 된 요청 |
| `EntitlementManagement` | `Ready to fulfill access package assignment request` |요청이 승인 되었거나 승인이 필요 하지 않음 |

사용자에 게 액세스 권한이 할당 되 면 Azure AD는 **활동** `Fulfill access package assignment`를 사용 하 여 `EntitlementManagement` 범주에 대 한 감사 레코드를 작성 합니다.  액세스를 받은 사용자는 **ActorUserPrincipalName** 필드로 식별 됩니다.

액세스가 할당 되지 않은 경우, 승인자가 요청을 거부 한 경우 Azure AD는 **활동** 을 사용 하 여 `EntitlementManagement` 범주에 대 한 감사 레코드를 `Deny access package assignment request`하거나 승인자가 승인할 수 있도록 요청 시간이 초과 된 경우 `Access package assignment request timed out (no approver action taken)`합니다.

사용자가 사용자의 액세스 패키지 할당을 취소 하거나 사용자가 취소 하거나 관리자가 제거 하면 Azure AD는 `Remove access package assignment`**활동** 을 사용 하 여 `EntitlementManagement` 범주에 대 한 감사 레코드를 작성 합니다.

## <a name="next-steps"></a>다음 단계

- [Azure AD 자격 관리 문제 해결](entitlement-management-troubleshoot.md)
- [일반적인 시나리오](entitlement-management-scenarios.md)
