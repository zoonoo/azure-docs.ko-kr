---
title: PowerShell 샘플 - 애플리케이션 프록시 앱에 대한 자세한 정보 나열
description: 모든 Azure AD(Azure Active Directory) 애플리케이션 프록시 애플리케이션과 애플리케이션 ID(AppId), 이름(DisplayName), 외부 URL(ExternalUrl), 내부 URL(InternalUrl) 및 인증 유형(ExternalAuthenticationType)을 나열하는 PowerShell 예제입니다.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 12/05/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: ccd0c7be7fd0dd533028faa0dc2bbdad30d74c79
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99258696"
---
# <a name="get-all-application-proxy-apps-and-list-extended-information"></a>모든 애플리케이션 프록시 앱을 가져오고 자세한 정보 나열

이 PowerShell 스크립트 예제는 응용 프로그램 ID (AppId), 이름 (DisplayName), 외부 URL (ExternalUrl), 내부 URL (InternalUrl), 인증 유형 (ExternalAuthenticationType), SSO 모드 및 추가 설정을 포함 하 여 모든 Azure Active Directory (Azure AD) 응용 프로그램 프록시 응용 프로그램에 대 한 정보를 나열 합니다.

$SsoMode 변수의 값을 변경 하면 SSO 모드에서 필터링 된 출력을 사용할 수 있습니다. 자세한 내용은 스크립트에 설명 되어 있습니다.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

이 샘플에는 [AzureAD V2 PowerShell For Graph 모듈이](/powershell/azure/active-directory/install-adv2) 필요 합니다 (AzureAD).

## <a name="sample-script"></a>샘플 스크립트

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-appproxy-apps-extended.ps1 "Get all Application Proxy apps")]

## <a name="script-explanation"></a>스크립트 설명

| 명령 | 메모 |
|---|---|
|[Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) | 서비스 주체를 가져옵니다. |
|[Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) | Azure AD 애플리케이션을 가져옵니다. |
|[Get-AzureADApplicationProxyApplication](/powershell/module/azuread/get-azureadapplicationproxyapplication) | Azure AD에서 애플리케이션 프록시에 대해 구성된 애플리케이션을 검색합니다. |

## <a name="next-steps"></a>다음 단계

Azure AD PowerShell 모듈에 대한 자세한 내용은 [Azure PowerShell 모듈 개요](/powershell/azure/active-directory/overview)를 참조하세요.

애플리케이션 프록시에 대한 다른 PowerShell 예제는 [Azure AD 애플리케이션 프록시에 대한 Azure AD PowerShell 예제](../application-proxy-powershell-samples.md)를 참조하세요.
