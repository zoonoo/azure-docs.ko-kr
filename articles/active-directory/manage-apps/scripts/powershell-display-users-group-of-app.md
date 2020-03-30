---
title: PowerShell 샘플 - 응용 프로그램 프록시 앱에 대한 사용자 & 그룹 목록
description: 특정 Azure Active Directory(Azure AD) 응용 프로그램 프록시 응용 프로그램에 할당된 모든 사용자 및 그룹을 나열하는 PowerShell 예제입니다.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: aab5ac5d4712dd9e72fc0b9665d740859b5a6775
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75483389"
---
# <a name="display-users-and-groups-assigned-to-an-application-proxy-application"></a>응용 프로그램 프록시 응용 프로그램에 할당된 사용자 및 그룹 표시

이 PowerShell 스크립트 예제에서는 특정 Azure Active Directory(Azure AD) 응용 프로그램 프록시 응용 프로그램에 할당된 사용자 및 그룹을 나열합니다.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

이 샘플에는 [그래프 모듈용 AzureAD V2 PowerShell(AzureAD)](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) 또는 [그래프 모듈 미리 보기 버전에 대한 AzureAD V2 PowerShell(AzureADPreview)이](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) 필요합니다.

## <a name="sample-script"></a>샘플 스크립트

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/display-users-group-of-an-app.ps1 "Display users and groups assigned to an Application Proxy application")]

## <a name="script-explanation"></a>스크립트 설명

| 명령 | 메모 |
|---|---|
| [Get-AzureADUser](https://docs.microsoft.com/powershell/module/AzureAD/get-azureaduser?view=azureadps-2.0)| 사용자를 가져옵니다. |
| [Get-AzureADGroup](https://docs.microsoft.com/powershell/module/AzureAD/get-azureadgroup?view=azureadps-2.0)| 그룹을 가져옵니다. |
| [Get-AzureADServicePrincipal](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0) | 서비스 주체를 가져옵니다. |
| [Get-AzureADUserAppRoleAssignment](https://docs.microsoft.com/powershell/module/AzureAD/get-azureaduserapproleassignment?view=azureadps-2.0) | 사용자 응용 프로그램 역할 할당을 가져옵니다. |
| [Get-AzureADGroupAppRoleAssignment](https://docs.microsoft.com/powershell/module/AzureAD/get-azureadgroupapproleassignment?view=azureadps-2.0) | 그룹 응용 프로그램 역할 할당을 가져옵니다. |

## <a name="next-steps"></a>다음 단계

Azure AD PowerShell 모듈에 대한 자세한 내용은 [Azure AD PowerShell 모듈 개요를](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0)참조하십시오.

응용 프로그램 프록시에 대한 다른 PowerShell 예제는 [Azure AD 응용 프로그램 프록시에 대한 Azure AD PowerShell 예제를](../application-proxy-powershell-samples.md)참조하십시오.