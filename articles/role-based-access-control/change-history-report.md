---
title: 액세스 보고 - Azure RBAC | Microsoft Docs
description: 지난 90일 동안 역할 기반 액세스 제어와 함께 Azure 구독 액세스에 대한 모든 변경 내용을 나열하는 보고서를 생성합니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 2bc68595-145e-4de3-8b71-3a21890d13d9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/17/2017
ms.author: rolyon
ms.reviewer: rqureshi
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: deef89e62dcec3141be46549cc0fb34b33a6335a
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="create-an-access-report-for-role-based-access-control"></a>역할 기반 Access Control에 대한 액세스 보고서 만들기
언제든지 누군가가 구독 내부의 액세스를 부여하거나 취소하면 변경 내용이 Azure 이벤트에 기록됩니다. 지난 90일 동안 모든 변경 내용을 보려면 액세스 변경 기록 보고서를 만들 수 있습니다.

## <a name="create-a-report-with-azure-powershell"></a>Azure PowerShell에서 보고서 만들기
PowerShell에서 액세스 변경 기록 보고서를 만들려면 [Get-AzureRMAuthorizationChangeLog](/powershell/module/azurerm.resources/get-azurermauthorizationchangelog) 명령을 사용합니다.

이 명령을 호출하는 경우 다음을 비롯하여 나열하려는 할당의 속성을 지정할 수 있습니다.

| 자산 | 설명 |
| --- | --- |
| **작업** |액세스를 부여 또는 취소했는지 여부 |
| **Caller** |액세스 변경을 담당하는 소유자 |
| **PrincipalId** | 역할이 할당된 사용자, 그룹 또는 응용 프로그램의 고유 식별자 |
| **PrincipalName** |사용자, 그룹 또는 응용 프로그램의 이름 |
| **PrincipalType** |사용자, 그룹 또는 응용 프로그램에 대한 할당인지 여부 |
| **RoleDefinitionId** |부여되었거나 해지된 역할의 GUID |
| **RoleName** |부여되었거나 해지된 역할 |
| **범위** | 할당이 적용되는 구독, 리소스 그룹 또는 리소스의 고유 식별자 | 
| **ScopeName** |구독, 리소스 그룹 또는 리소스의 이름 |
| **ScopeType** |할당이 구독, 리소스 그룹 또는 리소스 범위에서 이루어졌는지 여부 |
| **Timestamp** |액세스가 변경된 날짜 및 시간 |

이 예제 명령은 지난 7일 동안 구독에서 발생한 모든 액세스 변경 내용을 나열합니다.

```
Get-AzureRMAuthorizationChangeLog -StartTime ([DateTime]::Now - [TimeSpan]::FromDays(7)) | FT Caller,Action,RoleName,PrincipalType,PrincipalName,ScopeType,ScopeName
```

![PowerShell Get-AzureRMAuthorizationChangeLog - 스크린샷](./media/change-history-report/access-change-history.png)

## <a name="create-a-report-with-azure-cli"></a>Azure CLI에서 보고서 만들기
Azure 명령줄 인터페이스(CLI)에서 액세스 변경 기록 보고서를 만들려면 `azure role assignment changelog list` 명령을 사용합니다.

## <a name="export-to-a-spreadsheet"></a>스프레드시트로 내보내기
보고서를 저장하거나 데이터를 조작하거나 액세스 변경을 .csv 파일로 내보냅니다. 그런 다음 검토를 위해 스프레드시트에서 보고서를 볼 수 있습니다.

![Changelog가 스크린샷으로 표시됨 - 스크린샷](./media/change-history-report/change-history-spreadsheet.png)

## <a name="next-steps"></a>다음 단계
* [Azure RBAC에서 사용자 지정 역할](custom-roles.md)
* [PowerShell을 사용하여 Azure RBAC](role-assignments-powershell.md)를 관리하는 방법을 알아봅니다.

