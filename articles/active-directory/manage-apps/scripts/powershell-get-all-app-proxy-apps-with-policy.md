---
title: PowerShell 샘플 - 모든 응용 프로그램 프록시 앱을 정책으로 나열합니다.
description: PowerShell 예제는 수명 토큰 정책이 있는 디렉터리에 모든 Azure Active Directory(Azure AD) 응용 프로그램 프록시 응용 프로그램을 나열합니다.
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
ms.openlocfilehash: c2ad7b769dc890917e5364fe57582acdfe16acac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75482128"
---
# <a name="get-all-application-proxy-apps-with-a-token-lifetime-policy"></a>토큰 수명 정책을 사용한 모든 응용 프로그램 프록시 앱 받기

이 PowerShell 스크립트 예제에서는 토큰 수명 정책이 있는 디렉터리내의 모든 Azure Active Directory(Azure AD) 응용 프로그램 프록시 응용 프로그램을 나열하고 정책에 대한 세부 정보를 나열합니다.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

이 샘플에는 [그래프 모듈 미리 보기 버전(AzureADPreview)에 대한 AzureAD V2 PowerShell이](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) 필요합니다.

## <a name="sample-script"></a>샘플 스크립트

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-appproxy-apps-with-policy.ps1 "Get all Application Proxy apps with a token lifetime policy")]

## <a name="script-explanation"></a>스크립트 설명

| 명령 | 메모 |
|---|---|
|[Get-AzureADServicePrincipal](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0) | 서비스 주체를 가져옵니다. |
|[Get-AzureADApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplication?view=azureadps-2.0) | Azure AD 응용 프로그램을 가져옵니다. |
|[Get-AzureADPolicy](https://docs.microsoft.com/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview) | Azure AD에서 정책을 가져옵니다. |
|[Get-AzureADServicePrincipalPolicy](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipalpolicy?view=azureadps-2.0-preview) | Azure AD에서 서비스 주체의 정책을 가져옵니다. |


## <a name="next-steps"></a>다음 단계

Azure AD PowerShell 모듈에 대한 자세한 내용은 [Azure AD PowerShell 모듈 개요를](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0)참조하십시오.

응용 프로그램 프록시에 대한 다른 PowerShell 예제는 [Azure AD 응용 프로그램 프록시에 대한 Azure AD PowerShell 예제를](../application-proxy-powershell-samples.md)참조하십시오.