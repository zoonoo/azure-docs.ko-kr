---
title: 보고를 위한 Azure AD PowerShell cmdlet | 마이크로 소프트 문서
description: 보고를 위한 Azure AD PowerShell cmdlet의 참조입니다.
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
ms.date: 07/12/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2192c472e00d123780ec6bc5574e7b9fe326258b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75495314"
---
# <a name="azure-ad-powershell-cmdlets-for-reporting"></a>보고용 Azure AD PowerShell cmdlet

> [!NOTE] 
> 이러한 Powershell cmdlet은 현재 [Azure AD 미리 보기](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#directory_auditing) 모듈에서만 작동합니다. 미리 보기 모듈은 프로덕션 용으로 권장되지 않습니다. 

공개 미리 보기 릴리스를 설치하려면 다음을 사용합니다. 

```powershell
Install-module AzureADPreview
```
powershell을 사용하여 Azure AD에 연결하는 방법에 대한 자세한 내용은 [그래프용 Azure AD Powershell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0)문서를 참조하십시오.  

Azure Active Directory(Azure AD) 보고서를 사용하면 방향의 모든 쓰기 작업(감사 로그) 및 인증 데이터(로그인 로그)에 대한 활동에 대한 세부 정보를 얻을 수 있습니다. MS 그래프 API를 사용하여 정보를 사용할 수 있지만 이제 보고를 위해 Azure AD PowerShell cmdlet을 사용하여 동일한 데이터를 검색할 수 있습니다.

이 문서에서는 감사 로그 및 로그인 로그에 사용할 PowerShell cmdlet에 대한 개요를 제공합니다.

## <a name="audit-logs"></a>감사 로그

[감사 로그는](concept-audit-logs.md) Azure AD 내의 다양한 기능에 의해 수행된 모든 변경 사항에 대해 로그를 통해 추적 기능을 제공합니다. 감사 로그의 예제로는 사용자, 앱, 그룹, 역할 및 정책 추가 또는 제거와 같은 Azure AD 내의 모든 리소스에 대한 변경 내용이 있습니다.

'Get-AzureADAuditDirectorys cmdlet'을 사용하여 감사 로그에 액세스할 수 있습니다.


| 시나리오                      | PowerShell 명령 |
| :--                           | :--                |
| 응용 프로그램 표시 이름      | Get-AzureADAuditDirectorylogs -필터 "시작된 By/앱/디스플레이 이름 eq 'Azure AD 클라우드 동기화' |
| Category                      | Get-AzureADAuditDirectorylogs -필터 "카테고리 eq '응용 프로그램 관리' " |
| 활동 날짜 시간            | Get-AzureADAuditDirectorylogs -filter "activityDateTime gt 2019-04-18" |
| 위의 모든 항목              | Get-AzureADAuditDirectorylogs -Filter "시작By/앱/디스플레이이름 eq 'Azure AD 클라우드 동기화' 및 카테고리 eq '응용 프로그램 관리' 및 활동DateTime gt 2019-04-18"|


다음 이미지는 이 명령에 대한 예제를 보여 주십습니다. 

!["데이터 요약" 단추](./media/reference-powershell-reporting/get-azureadauditdirectorylogs.png)



## <a name="sign-in-logs"></a>로그인 로그

[로그인](concept-sign-ins.md) 로그는 관리되는 응용 프로그램 및 사용자 로그인 활동의 사용에 대한 정보를 제공합니다.

'Get-AzureADAuditSignInLogs cmdlet'을 사용하여 로그인 로그에 액세스할 수 있습니다.


| 시나리오                      | PowerShell 명령 |
| :--                           | :--                |
| 사용자 표시 이름             | Get-AzureADAuditSign로그인 -필터 "사용자디스플레이네임 eq '티모시 퍼킨스'" |
| 날짜 시간 만들기              | Get-AzureADAuditSignInLogs -필터 "생성DateTime gt 2019-04-18T17:00.0Z" (4/18에 오후 5시 30분 이후) |
| 상태                        | Get-AzureADAuditSign로그인 -필터 "상태/오류 코드 eq 50105" |
| 응용 프로그램 표시 이름      | Get-AzureADAuditSign로그인 -필터 "appDisplayName eq 'StoreFrontStudio [sfed 사용]"" |
| 위의 모든 항목              | Get-AzureADAuditSignInLogs -filter "userDisplayName eq '티모시 퍼킨스' 및 상태/오류코드 ne 0 및 appDisplayName eq 'StoreFrontStudio [sfed enabled]'" |


다음 이미지는 이 명령에 대한 예제를 보여 주십습니다. 

!["데이터 요약" 단추](./media/reference-powershell-reporting/get-azureadauditsigninlogs.png)



## <a name="next-steps"></a>다음 단계

- [Azure AD 보고서 개요](overview-reports.md).
- [감사 로그 보고서](concept-audit-logs.md). 
- [Azure AD 보고서에 대한 프로그래밍 방식 액세스](concept-reporting-api.md)
