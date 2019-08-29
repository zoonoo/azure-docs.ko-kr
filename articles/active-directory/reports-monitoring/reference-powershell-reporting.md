---
title: 보고용 Azure AD PowerShell cmdlet | Microsoft Docs
description: 보고용 Azure AD PowerShell cmdlet에 대 한 참조입니다.
services: active-directory
documentationcenter: ''
author: cawrites
manager: daveba
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 07/12/2019
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d34204b936a608158a0ca3e8af2264059ffc6aa
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/29/2019
ms.locfileid: "70136563"
---
# <a name="azure-ad-powershell-cmdlets-for-reporting"></a>보고용 Azure AD PowerShell cmdlet

> [!NOTE] 
> 이러한 Powershell cmdlet은 현재 [AZURE AD 미리 보기](https://docs.microsoft.com/en-us/powershell/module/azuread/?view=azureadps-2.0-preview#directory_auditing) 모듈 에서만 작동 합니다. 미리 보기 모듈은 프로덕션 환경에서 사용 하지 않는 것이 좋습니다. 

Azure Active Directory (Azure AD) 보고서를 사용 하 여 사용자 방향 (감사 로그) 및 인증 데이터 (로그인 로그)의 모든 쓰기 작업에 대 한 세부 정보를 얻을 수 있습니다. MS Graph API를 사용 하 여 정보를 사용할 수 있지만 이제는 보고를 위해 Azure AD PowerShell cmdlet을 사용 하 여 동일한 데이터를 검색할 수 있습니다.

이 문서에서는 감사 로그 및 로그인 로그에 사용할 PowerShell cmdlet에 대 한 개요를 제공 합니다.

## <a name="audit-logs"></a>감사 로그

[감사 로그](concept-audit-logs.md) 는 Azure AD 내에서 다양 한 기능을 통해 수행 되는 모든 변경 내용에 대 한 추적 기능을 제공 합니다. 감사 로그의 예제로는 사용자, 앱, 그룹, 역할 및 정책 추가 또는 제거와 같은 Azure AD 내의 모든 리소스에 대한 변경 내용이 있습니다.

' AzureADAuditDirectoryLogs cmdlet을 사용 하 여 감사 로그에 액세스할 수 있습니다.


| 시나리오                      | PowerShell 명령 |
| :--                           | :--                |
| 응용 프로그램 표시 이름      | AzureADAuditDirectoryLogs-Filter "initiatedBy/app/displayName eq ' Azure AD 클라우드 동기화 '" |
| 범주                      | AzureADAuditDirectoryLogs-Filter "category eq ' Application Management '" |
| 작업 날짜 시간            | AzureADAuditDirectoryLogs-Filter "activityDateTime gt 2019-04-18" |
| 위의 모든 항목              | AzureADAuditDirectoryLogs-Filter "initiatedBy/app/displayName eq ' Azure AD 클라우드 Sync ' 및 category eq ' Application Management ' 및 activityDateTime gt 2019-04-18"|


다음 이미지는이 명령에 대 한 예를 보여 줍니다. 

!["데이터 요약" 단추](./media/reference-powershell-reporting/get-azureadauditdirectorylogs.png)



## <a name="sign-in-logs"></a>로그인 로그

[로그인](concept-sign-ins.md) 로그는 관리 되는 응용 프로그램 및 사용자 로그인 활동 사용에 대 한 정보를 제공 합니다.

' AzureADAuditSignInLogs cmdlet을 사용 하 여 로그인 로그에 액세스할 수 있습니다.


| 시나리오                      | PowerShell 명령 |
| :--                           | :--                |
| 사용자 표시 이름             | AzureADAuditSignInLogs-Filter "userDisplayName eq ' TimosPerkins '" |
| 만든 날짜 시간              | AzureADAuditSignInLogs-Filter "createdDateTime gt 2019-04-18T17:30:00.0 Z" (4/18 5:30 pm 이후의 모든 항목) |
| Status                        | AzureADAuditSignInLogs-Filter "status/errorCode eq 50105" |
| 응용 프로그램 표시 이름      | AzureADAuditSignInLogs-Filter "appDisplayName eq ' StoreFrontStudio [wsfed enabled] '" |
| 위의 모든 항목              | AzureADAuditSignInLogs-Filter "userDisplayName eq ' TimosPerkins ' 및 status/errorCode ne 0 및 appDisplayName eq ' StoreFrontStudio [wsfed enabled] '" |


다음 이미지는이 명령에 대 한 예를 보여 줍니다. 

!["데이터 요약" 단추](./media/reference-powershell-reporting/get-azureadauditsigninlogs.png)



## <a name="next-steps"></a>다음 단계

- [Azure AD 보고서 개요](overview-reports.md).
- [감사 로그 보고서](concept-audit-logs.md). 
- [Azure AD 보고서에 대한 프로그래밍 방식 액세스](concept-reporting-api.md)
