---
title: PowerShell 샘플-동일한 인증서를 사용 하는 응용 프로그램 프록시 앱
description: 동일한 인증서를 사용 하 여 게시 된 모든 Azure Active Directory (Azure AD) 응용 프로그램 프록시 응용 프로그램을 나열 하는 PowerShell 예제입니다.
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
ms.openlocfilehash: ef654226d041a7602c03e1ce4670d2f8eea0c2e7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "75482011"
---
# <a name="get-all-azure-ad-proxy-application-apps-that-are-published-with-the-identical-certificate"></a>동일한 인증서를 사용 하 여 게시 된 모든 Azure AD 프록시 응용 프로그램 앱 가져오기

이 PowerShell 스크립트 예제는 동일한 인증서를 사용 하 여 게시 된 모든 Azure Active Directory (Azure AD) 응용 프로그램 프록시 응용 프로그램을 나열 합니다.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

이 샘플에는 [AzureAD V2 powershell For graph 모듈](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) (AzureAD) 또는 [AzureAD V2 powershell for graph module preview 버전](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) (AzureADPreview)이 필요 합니다.

## <a name="sample-script"></a>샘플 스크립트

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-custom-domain-identical-cert.ps1 "Get all Azure AD Proxy application apps published with the identical certificate")]

## <a name="script-explanation"></a>스크립트 설명

| 명령 | 메모 |
|---|---|
|[Get-AzureADServicePrincipal](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0) | 서비스 사용자를 가져옵니다. |
|[Get-AzureADApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplication?view=azureadps-2.0) | Azure AD 응용 프로그램을 가져옵니다. |
|[Get-AzureADApplicationProxyApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplicationproxyapplication?view=azureadps-2.0) | Azure AD에서 응용 프로그램 프록시에 대해 구성 된 응용 프로그램을 검색 합니다. |

## <a name="next-steps"></a>다음 단계

Azure AD PowerShell 모듈에 대 한 자세한 내용은 [AZURE Ad powershell 모듈 개요](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0)를 참조 하세요.

응용 프로그램 프록시에 대 한 다른 PowerShell 예제는 azure [AD 응용 프로그램 프록시에 대 한 AZURE AD PowerShell 예제](../application-proxy-powershell-samples.md)를 참조 하세요.