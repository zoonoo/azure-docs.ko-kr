---
title: 권한 관리에서 보고서 & 로그 보기 - Azure AD
description: Azure Active Directory 권한 관리에서 사용자 할당 보고서 및 감사 로그를 보는 방법을 알아봅니다.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: jocastel-MSFT
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: jocastel
ms.collection: M365-identity-device-management
ms.openlocfilehash: 514f8e86d6bd28cc5212e0f0058f00e270f43e35
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128427"
---
# <a name="view-reports-and-logs-in-azure-ad-entitlement-management"></a>Azure AD 권한 관리에서 보고서 및 로그 보기

Azure AD 권한 관리 보고서 및 Azure AD 감사 로그는 사용자가 액세스할 수 있는 리소스에 대한 추가 세부 정보를 제공합니다. 관리자는 사용자에 대한 액세스 패키지 및 리소스 할당을 보고 감사 목적으로 요청 로그를 보거나 사용자 요청 상태를 확인할 수 있습니다. 이 문서에서는 권한 관리 보고서 및 Azure AD 감사 로그를 사용하는 방법을 설명합니다.

다음 비디오를 시청하여 사용자가 권한 부여 관리에서 액세스할 수 있는 리소스를 보는 방법을 알아봅니다.

>[!VIDEO https://www.youtube.com/embed/omtNJ7ySjS0]

## <a name="view-access-packages-for-a-user"></a>사용자에 대한 액세스 패키지 보기

이 보고서를 사용하면 사용자가 요청할 수 있는 모든 액세스 패키지와 현재 사용자에게 할당된 액세스 패키지를 나열할 수 있습니다.

**필수 구성 조건 역할:** 글로벌 관리자 또는 사용자 관리자

1. **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽 메뉴에서 **보고서**를 클릭합니다.

1. **사용자의 패키지 액세스 를 클릭합니다.**

1. **사용자 선택을** 클릭하여 사용자 선택 창을 엽니다.

1. 목록에서 사용자를 찾은 다음 **선택**을 클릭합니다.

    **수 요청** 탭에는 사용자가 요청할 수 있는 액세스 패키지 목록이 표시됩니다. 이 목록은 액세스 패키지에 대해 정의된 [요청 정책에](entitlement-management-access-package-request-policy.md#for-users-in-your-directory) 따라 결정됩니다. 

    ![사용자를 위한 패키지 액세스](./media/entitlement-management-reports/access-packages-report.png)

1. 액세스 패키지에 대한 리소스 역할 또는 정책이 두 개 이상 있는 경우 리소스 역할 또는 정책 항목을 클릭하여 선택 세부 정보를 확인합니다.

1. **할당된** 탭을 클릭하여 현재 사용자에게 할당된 액세스 패키지 목록을 확인합니다. 액세스 패키지가 사용자에게 할당되면 사용자가 액세스 패키지의 모든 리소스 역할에 액세스할 수 있음을 의미합니다.

## <a name="view-resource-assignments-for-a-user"></a>사용자에 대한 리소스 할당 보기

이 보고서를 사용하면 권한 관리에서 사용자에게 현재 할당된 리소스를 나열할 수 있습니다. 이 보고서는 권한 관리로 관리되는 리소스에 대한 것입니다. 사용자는 권한 관리 외부의 디렉터리에서 다른 리소스에 액세스할 수 있습니다.

**필수 구성 조건 역할:** 글로벌 관리자 또는 사용자 관리자

1. **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽 메뉴에서 **보고서**를 클릭합니다.

1. **사용자의 리소스 할당을 클릭합니다.**

1. **사용자 선택을** 클릭하여 사용자 선택 창을 엽니다.

1. 목록에서 사용자를 찾은 다음 **선택**을 클릭합니다.

    현재 사용자에게 할당된 리소스 목록이 표시됩니다. 또한 목록에는 액세스 시작 및 종료 날짜와 함께 리소스 역할이 있는 액세스 패키지 및 정책이 표시됩니다.
    
    사용자가 두 개 이상의 패키지에서 동일한 리소스에 액세스할 수 있는 경우 화살표를 클릭하여 각 패키지 및 정책을 볼 수 있습니다.

    ![사용자에 대한 리소스 할당](./media/entitlement-management-reports/resource-assignments-report.png)

## <a name="determine-the-status-of-a-users-request"></a>사용자 요청 상태 확인

사용자가 액세스 패키지에 대한 액세스를 요청하고 받은 방법에 대한 추가 세부 정보를 얻으려면 Azure AD 감사 로그를 사용할 수 있습니다. 특히 `EntitlementManagement` 및 `UserManagement` 범주의 로그 레코드를 사용하여 각 요청에 대한 처리 단계에 대한 추가 세부 정보를 얻을 수 있습니다.  

1. **Azure Active Directory를** 클릭한 다음 **감사 로그를 클릭합니다.**

1. 맨 위에 있는 **범주를** `EntitlementManagement` 찾고 `UserManagement`있는 감사 레코드에 따라 범주를 중 하나 또는 로 변경합니다.  

1. **적용**을 클릭합니다.

1. 로그를 다운로드하려면 **다운로드를**클릭합니다.

Azure AD가 새 요청을 받으면 **범주가** `EntitlementManagement` 있고 **활동이** 일반적으로 `User requests access package assignment`있는 감사 레코드를 씁니다.  Azure 포털에서 만든 직접 할당의 경우 감사 레코드의 **활동** `Administrator directly assigns user to access package`필드가 이며 할당을 수행하는 사용자는 **ActorUserPrincipalName**에 의해 식별됩니다.

Azure AD는 요청이 진행 되는 동안 추가 감사 레코드를 작성 합니다.

| Category | 활동 | 요청 상태 |
| :---- | :------------ | :------------ |
| `EntitlementManagement` | `Auto approve access package assignment request` | 요청은 승인이 필요하지 않습니다. |
| `UserManagement` | `Create request approval` | 요청에 승인이 필요합니다. |
| `UserManagement` | `Add approver to request approval` | 요청에 승인이 필요합니다. |
| `EntitlementManagement` | `Approve access package assignment request` | 승인된 요청 |
| `EntitlementManagement` | `Ready to fulfill access package assignment request` |승인된 요청 또는 승인이 필요하지 않음 |

사용자에게 액세스 가 할당되면 Azure AD는 `EntitlementManagement` **활동** `Fulfill access package assignment`이 있는 범주에 대한 감사 레코드를 씁니다.  액세스를 받은 사용자는 **ActorUserPrincipalName** 필드에 의해 식별됩니다.

액세스가 할당되지 않은 경우 Azure AD는 `EntitlementManagement` 승인자가 요청이 거부된 경우 또는 `Access package assignment request timed out (no approver action taken)`승인자가 승인하기 전에 요청 시간이 초과된 경우 **Activity가** `Deny access package assignment request`있는 범주에 대한 감사 레코드를 씁니다.

사용자의 액세스 패키지 할당이 만료되거나, 사용자가 취소되거나, 관리자가 제거하면 Azure AD는 `EntitlementManagement` `Remove access package assignment`의 **활동이** 있는 범주에 대한 감사 레코드를 씁니다.

## <a name="next-steps"></a>다음 단계

- [보고서 및 로그 보관](entitlement-management-logs-and-reporting.md)
- [Azure AD 권한 관리 문제 해결](entitlement-management-troubleshoot.md)
- [일반적인 시나리오](entitlement-management-scenarios.md)
