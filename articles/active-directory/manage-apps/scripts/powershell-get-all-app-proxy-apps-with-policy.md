---
title: PowerShell 샘플-정책을 포함 하는 모든 응용 프로그램 프록시 앱 나열
description: 수명 토큰 정책을 포함 하는 디렉터리의 모든 Azure Active Directory (Azure AD) 응용 프로그램 프록시 응용 프로그램을 나열 하는 PowerShell 예제입니다.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "75482128"
---
# <a name="get-all-application-proxy-apps-with-a-token-lifetime-policy"></a>토큰 수명 정책을 사용하여 모든 애플리케이션 프록시 앱 가져오기

이 PowerShell 스크립트 예제는 디렉터리에서 토큰 수명 정책이 있는 모든 Azure Active Directory (Azure AD) 응용 프로그램 프록시 응용 프로그램을 나열 하 고 정책에 대 한 세부 정보를 나열 합니다.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

이 샘플에는 [AzureAD V2 PowerShell For Graph module preview 버전](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) (AzureADPreview)이 필요 합니다.

## <a name="sample-script"></a>샘플 스크립트

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-appproxy-apps-with-policy.ps1 "Get all Application Proxy apps with a token lifetime policy")]

## <a name="script-explanation"></a>스크립트 설명

| 명령 | 메모 |
|---|---|
|[Get-AzureADServicePrincipal](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0) | 서비스 사용자를 가져옵니다. |
|[Get-AzureADApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplication?view=azureadps-2.0) | Azure AD 응용 프로그램을 가져옵니다. |
|[Get-AzureADPolicy](https://docs.microsoft.com/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview) | Azure AD에서 정책을 가져옵니다. |
|[Get-AzureADServicePrincipalPolicy](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipalpolicy?view=azureadps-2.0-preview) | Azure AD의 서비스 주체에 대 한 정책을 가져옵니다. |


## <a name="next-steps"></a>다음 단계

Azure AD PowerShell 모듈에 대 한 자세한 내용은 [AZURE Ad powershell 모듈 개요](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0)를 참조 하세요.

응용 프로그램 프록시에 대 한 다른 PowerShell 예제는 azure [AD 응용 프로그램 프록시에 대 한 AZURE AD PowerShell 예제](../application-proxy-powershell-samples.md)를 참조 하세요.