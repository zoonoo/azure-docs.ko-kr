---
title: 보고용 Azure AD PowerShell cmdlet | Microsoft Docs
description: 보고용 Azure AD PowerShell cmdlet에 대 한 참조입니다.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 08/07/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 25f3402864e2ba873ce63acb6cedcd3f2ea018f7
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93123135"
---
# <a name="azure-ad-powershell-cmdlets-for-reporting"></a>보고용 Azure AD PowerShell cmdlet

> [!NOTE] 
> 이러한 PowerShell cmdlet은 현재 [AZURE AD 미리 보기](/powershell/module/azuread/?view=azureadps-2.0-preview#directory_auditing) 모듈 에서만 작동 합니다. 미리 보기 모듈은 프로덕션 환경에서 사용 하지 않는 것이 좋습니다. 

공개 미리 보기 릴리스를 설치 하려면 다음을 사용 합니다. 

```powershell
Install-module AzureADPreview
```

PowerShell을 사용 하 여 Azure AD에 연결 하는 방법에 대 한 자세한 정보 [Graph 용 AZURE Ad PowerShell](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0)문서를 참조 하세요.  

Azure Active Directory (Azure AD) 보고서를 사용 하 여 사용자 방향 (감사 로그) 및 인증 데이터 (로그인 로그)의 모든 쓰기 작업에 대 한 세부 정보를 얻을 수 있습니다. MS Graph API를 사용 하 여 정보를 사용할 수 있지만 이제는 보고를 위해 Azure AD PowerShell cmdlet을 사용 하 여 동일한 데이터를 검색할 수 있습니다.

이 문서에서는 감사 로그 및 로그인 로그에 사용할 PowerShell cmdlet에 대 한 개요를 제공 합니다.

## <a name="audit-logs"></a>감사 로그

[감사 로그](concept-audit-logs.md) 는 Azure AD 내에서 다양 한 기능을 통해 수행 되는 모든 변경 내용에 대 한 추적 기능을 제공 합니다. 감사 로그의 예로는 사용자, 앱, 그룹, 역할 및 정책 추가 또는 제거와 같은 Azure AD 내의 모든 리소스에 대 한 변경 내용이 포함 됩니다.

' AzureADAuditDirectoryLogs cmdlet을 사용 하 여 감사 로그에 액세스할 수 있습니다.


| 시나리오                      | PowerShell 명령 |
| :--                           | :--                |
| 응용 프로그램 표시 이름      | Get-AzureADAuditDirectoryLogs-"initiatedBy/app/displayName eq ' Azure AD 클라우드 동기화 '"를 필터링 합니다. |
| 범주                      | Get-AzureADAuditDirectoryLogs 필터 "category eq ' ApplicationManagement '" |
| 작업 날짜 시간            | Get-AzureADAuditDirectoryLogs 필터 "activityDateTime gt 2019-04-18" |
| 위 항목 모두              | Get-AzureADAuditDirectoryLogs-"initiatedBy/app/displayName eq ' Azure AD 클라우드 동기화 ' 및 category eq ' ApplicationManagement ' 및 activityDateTime gt 2019-04-18"|


다음 이미지는이 명령에 대 한 예를 보여 줍니다. 

![스크린샷 Get-Azure D 감사 디렉터리 로그 명령의 결과를 보여 줍니다.](./media/reference-powershell-reporting/get-azureadauditdirectorylogs.png)



## <a name="sign-in-logs"></a>로그인 로그

[로그인](concept-sign-ins.md) 로그는 관리 되는 응용 프로그램 및 사용자 로그인 활동 사용에 대 한 정보를 제공 합니다.

' AzureADAuditSignInLogs cmdlet을 사용 하 여 로그인 로그에 액세스할 수 있습니다.


| 시나리오                      | PowerShell 명령 |
| :--                           | :--                |
| 사용자 표시 이름             | Get-AzureADAuditSignInLogs-"userDisplayName eq ' TimosPerkins '" 필터 |
| 만든 날짜 시간              | Get-AzureADAuditSignInLogs-필터 "createdDateTime gt 2019-04-18T17:30:00.0 Z" (4/18 5:30 pm 이후의 모든 항목) |
| 상태                        | Get-AzureADAuditSignInLogs 필터 "status/errorCode eq 50105" |
| 응용 프로그램 표시 이름      | Get-AzureADAuditSignInLogs-"appDisplayName eq ' StoreFrontStudio [wsfed enabled] '" 필터 |
| 위 항목 모두              | Get-AzureADAuditSignInLogs 필터 "userDisplayName eq ' TimosPerkins ' 및 status/errorCode ne 0 및 appDisplayName eq ' StoreFrontStudio [wsfed enabled] '" |


다음 이미지는이 명령에 대 한 예를 보여 줍니다. 

![스크린샷 Get-Azure D 감사 로그인 로그 명령의 결과를 보여 줍니다.](./media/reference-powershell-reporting/get-azureadauditsigninlogs.png)



## <a name="next-steps"></a>다음 단계

- [Azure AD 보고서 개요](overview-reports.md).
- [감사 로그 보고서](concept-audit-logs.md) 
- [Azure AD 보고서에 대한 프로그래밍 방식 액세스](concept-reporting-api.md)