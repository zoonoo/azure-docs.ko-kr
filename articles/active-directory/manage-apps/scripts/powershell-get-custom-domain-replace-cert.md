---
title: PowerShell 샘플 - 응용 프로그램 프록시 앱에서 인증서 교체
description: Azure Active Directory(Azure AD) 응용 프로그램 프록시 응용 프로그램에서 인증서를 대량 으로 대체하는 PowerShell 예제입니다.
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
ms.openlocfilehash: 91aafe00856f37e9d7e6b3babafecd2cbdf5f856
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75481998"
---
# <a name="get-all-application-proxy-applications-published-with-the-identical-certificate-and-replace-it"></a>동일한 인증서로 게시된 모든 응용 프로그램 프록시 응용 프로그램을 가져옵니다.

이 PowerShell 스크립트 예제에서는 동일한 인증서로 게시된 모든 Azure Active Directory(Azure AD) 응용 프로그램 프록시 응용 프로그램에 대해 인증서를 대량으로 교체할 수 있습니다.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

이 샘플에는 [그래프 모듈용 AzureAD V2 PowerShell(AzureAD)](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) 또는 [그래프 모듈 미리 보기 버전에 대한 AzureAD V2 PowerShell(AzureADPreview)이](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) 필요합니다.

## <a name="sample-script"></a>샘플 스크립트

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-custom-domain-replace-cert.ps1 "Get all Application Proxy applications published with the identical certificate and replace it")]

## <a name="script-explanation"></a>스크립트 설명

| 명령 | 메모 |
|---|---|
|[Get-AzureADServicePrincipal](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0) | 서비스 주체를 가져옵니다. |
|[Get-AzureADApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplication?view=azureadps-2.0) | Azure AD 응용 프로그램을 가져옵니다. |
|[Get-AzureADApplicationProxyApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplicationproxyapplication?view=azureadps-2.0) | Azure AD에서 응용 프로그램 프록시에 대해 구성된 응용 프로그램을 검색합니다. |
|[Set-AzureADApplicationProxyApplicationCustomDomainCertificate](https://docs.microsoft.com/powershell/module/azuread/set-azureadapplicationproxyapplicationcustomdomaincertificate?view=azureadps-2.0) | Azure AD에서 응용 프로그램 프록시에 대해 구성된 응용 프로그램에 인증서를 할당합니다. 이 명령은 인증서를 업로드하고 응용 프로그램이 사용자 지정 도메인을 사용할 수 있도록 합니다. |

## <a name="next-steps"></a>다음 단계

Azure AD PowerShell 모듈에 대한 자세한 내용은 [Azure AD PowerShell 모듈 개요를](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0)참조하십시오.

응용 프로그램 프록시에 대한 다른 PowerShell 예제는 [Azure AD 응용 프로그램 프록시에 대한 Azure AD PowerShell 예제를](../application-proxy-powershell-samples.md)참조하십시오.