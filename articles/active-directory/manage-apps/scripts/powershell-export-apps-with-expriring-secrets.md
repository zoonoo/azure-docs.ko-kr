---
title: PowerShell 샘플 - Azure Active Directory 테넌트에서 만료된 비밀 및 인증서가 있는 앱을 내보냅니다.
description: Azure Active Directory 테넌트에 있는 지정된 앱에 대해 만료되는 비밀 및 인증서가 있는 모든 앱을 내보내는 PowerShell 예제입니다.
services: active-directory
author: mtillman
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 03/09/2021
ms.author: mtillman
ms.reviewer: mifarca
ms.openlocfilehash: 38b7b424e23c196f89004b443b8945d6f7bfe44b
ms.sourcegitcommit: 3bb9f8cee51e3b9c711679b460ab7b7363a62e6b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/14/2021
ms.locfileid: "112076783"
---
# <a name="export-apps-with-expiring-secrets-and-certificates"></a>만료된 비밀 및 인증서가 있는 앱 내보내기

이 PowerShell 스크립트 예제에서는 만료된 비밀, 인증서 및 지정된 앱에 대한 해당 소유자를 포함하는 모든 앱 등록을 CSV 파일의 디렉터리에서 내보냅니다.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

이러한 샘플을 사용하려면 [Graph용 AzureAD V2 PowerShell 모듈](/powershell/azure/active-directory/install-adv2)(AzureAD) 또는 [Graph용 AzureAD V2 PowerShell 모듈 미리 보기 버전](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true)(AzureADPreview)이 필요합니다.

## <a name="sample-script"></a>샘플 스크립트

[!code-azurepowershell[main](~/powershell_scripts/application-management/export-apps-with-expiring-secrets.ps1 "Exports all apps with expiring secrets and certificates for the specified apps in your directory.")]

## <a name="script-explanation"></a>스크립트 설명

스크립트는 수정 없이 직접 사용할 수 있습니다. 만료 날짜와 이미 만료된 비밀 또는 인증서를 표시할지 여부를 관리자에게 묻는 메시지가 표시됩니다.

"Add-Member" 명령은 CSV 파일에서 열을 만드는 역할을 담당합니다.
"New-Object" 명령은 CSV 파일 내보내기의 열에 사용할 개체를 만듭니다.
내보내기를 비대화형으로 사용하려는 경우에는 CSV 파일 경로를 사용하여 PowerShell에서 직접 "$Path" 변수를 수정할 수 있습니다.

| 명령 | 메모 |
|---|---|
| [Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication?view=azureadps-2.0&preserve-view=true) | 디렉터리에서 애플리케이션을 검색합니다. |
| [Get-AzureADApplicationOwner](/powershell/module/azuread/Get-AzureADApplicationOwner?view=azureadps-2.0&preserve-view=true) | 디렉터리에서 애플리케이션의 소유자를 검색합니다. |

## <a name="next-steps"></a>다음 단계

Azure AD PowerShell 모듈에 대한 자세한 내용은 [Azure PowerShell 모듈 개요](/powershell/azure/active-directory/overview)를 참조하세요.

애플리케이션 관리에 대한 다른 PowerShell 예제는 [애플리케이션 관리에 대한 Azure AD PowerShell 예제](../app-management-powershell-samples.md)를 참조하세요.
