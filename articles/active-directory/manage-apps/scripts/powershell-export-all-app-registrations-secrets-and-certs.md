---
title: PowerShell 샘플 - Azure Active Directory 테넌트에서 앱 등록, 비밀 및 인증서를 내보냅니다.
description: Azure Active Directory 테넌트에서 지정된 앱에 대한 모든 앱 등록, 비밀 및 인증서를 내보내는 PowerShell 예제입니다.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 02/18/2021
ms.author: kenwith
ms.reviewer: mifarca
ms.openlocfilehash: 768f2f3241144085acb7a218b60034cdfa9e45b9
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102193274"
---
# <a name="export-app-registrations-secrets-and-certificates"></a>앱 등록, 비밀 및 인증서 내보내기

PowerShell 스크립트 예제는 디렉터리에 있는 지정된 앱에 대한 모든 앱 등록, 비밀 및 인증서를 내보냅니다.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

이러한 샘플을 사용하려면 [Graph용 AzureAD V2 PowerShell 모듈](/powershell/azure/active-directory/install-adv2)(AzureAD) 또는 [Graph용 AzureAD V2 PowerShell 모듈 미리 보기 버전](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true)(AzureADPreview)이 필요합니다.

## <a name="sample-script"></a>샘플 스크립트

[!code-azurepowershell[main](~/powershell_scripts/application-management/export-all-app-registrations-secrets-and-certs.ps1 "Exports all app registrations, secrets, and certificates for the specified apps in your directory.")]

## <a name="script-explanation"></a>스크립트 설명

| 명령 | 메모 |
|---|---|
| [Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication?view=azureadps-2.0&preserve-view=true) | 디렉터리에 있는 지정된 앱에 대한 모든 앱 등록, 비밀 및 인증서를 내보냅니다. |

## <a name="next-steps"></a>다음 단계

Azure AD PowerShell 모듈에 대한 자세한 내용은 [Azure PowerShell 모듈 개요](/powershell/azure/active-directory/overview)를 참조하세요.

애플리케이션 관리에 대한 다른 PowerShell 예제는 [애플리케이션 관리에 대한 Azure AD PowerShell 예제](../app-management-powershell-samples.md)를 참조하세요.
