---
title: PowerShell 샘플 - Azure Active Directory 테넌트에서 엔터프라이즈 앱에 대한 비밀 및 인증서를 내보냅니다.
description: Azure Active Directory 테넌트에서 지정된 엔터프라이즈 앱에 대한 모든 비밀 및 인증서를 내보내는 PowerShell 예제입니다.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 03/09/2021
ms.author: kenwith
ms.reviewer: mifarca
ms.openlocfilehash: 20caefe74a7c047fb8690bb1d9e6f4eb9da7e9b7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102635198"
---
# <a name="export-secrets-and-certificates-for-enterprise-apps"></a>엔터프라이즈 앱에 대한 비밀 및 인증서 내보내기
이 PowerShell 스크립트 예제에서는 지정된 엔터프라이즈 앱에 대한 모든 비밀, 인증서 및 소유자를 디렉터리에서 CSV 파일로 내보냅니다.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

이러한 샘플을 사용하려면 [Graph용 AzureAD V2 PowerShell 모듈](/powershell/azure/active-directory/install-adv2)(AzureAD) 또는 [Graph용 AzureAD V2 PowerShell 모듈 미리 보기 버전](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true)(AzureADPreview)이 필요합니다.

## <a name="sample-script"></a>샘플 스크립트

[!code-azurepowershell[main](~/powershell_scripts/application-management/export-all-enterprise-apps-secrets-and-certs.ps1 "Exports all secrets and certificates for the specified enterprise apps in your directory.")]

## <a name="script-explanation"></a>스크립트 설명

"Add-Member" 명령은 CSV 파일에서 열을 만드는 역할을 담당합니다.
내보내기를 비대화형으로 사용하려는 경우에는 CSV 파일 경로를 사용하여 PowerShell에서 직접 "$Path" 변수를 수정할 수 있습니다.

| 명령 | 메모 |
|---|---|
| [Get-AzureADServicePrincipal](/powershell/module/azuread/Get-azureADServicePrincipal?view=azureadps-2.0&preserve-view=true) | 디렉터리에서 엔터프라이즈 애플리케이션을 검색합니다. |
| [Get-AzureADServicePrincipalOwner](/powershell/module/azuread/Get-AzureADServicePrincipalOwner?view=azureadps-2.0&preserve-view=true) | 디렉터리에서 엔터프라이즈 애플리케이션의 소유자를 검색합니다. |


## <a name="next-steps"></a>다음 단계

Azure AD PowerShell 모듈에 대한 자세한 내용은 [Azure PowerShell 모듈 개요](/powershell/azure/active-directory/overview)를 참조하세요.

애플리케이션 관리에 대한 다른 PowerShell 예제는 [애플리케이션 관리에 대한 Azure AD PowerShell 예제](../app-management-powershell-samples.md)를 참조하세요.
