---
title: PowerShell 샘플-응용 프로그램 프록시 앱을 다른 그룹으로 이동
description: Azure Active Directory (Azure AD) 응용 프로그램 프록시 PowerShell 예제는 현재 커넥터 그룹에 할당 된 모든 응용 프로그램을 다른 커넥터 그룹으로 이동 하는 데 사용 됩니다.
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
ms.openlocfilehash: f6dccdaa96dadb061b168bbdf6148ed4d759776c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "75481985"
---
# <a name="move-all-apps-assigned-to-a-connector-group-to-another-connector-group"></a>커넥터 그룹에 할당된 모든 앱을 다른 커넥터 그룹으로 이동

이 PowerShell 스크립트 예제는 현재 커넥터 그룹에 할당 된 모든 Azure Active Directory (Azure AD) 응용 프로그램 프록시 응용 프로그램을 다른 커넥터 그룹으로 이동 합니다.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

이 샘플에는 [AzureAD V2 powershell For graph 모듈](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) (AzureAD) 또는 [AzureAD V2 powershell for graph module preview 버전](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) (AzureADPreview)이 필요 합니다.

## <a name="sample-script"></a>샘플 스크립트

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/move-all-apps-to-a-connector-group.ps1 "Move all apps assigned to a connector group to another connector group")]

## <a name="script-explanation"></a>스크립트 설명

| 명령 | 메모 |
|---|---|
|[Get-AzureADServicePrincipal](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0) | 서비스 사용자를 가져옵니다. |
|[Get-AzureADApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplication?view=azureadps-2.0) | Azure AD 응용 프로그램을 가져옵니다. |
| [Get-AzureADApplicationProxyConnectorGroup](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplicationproxyconnectorgroup?view=azureadps-2.0) | 모든 커넥터 그룹 목록을 검색 하거나 지정 된 경우 지정 된 커넥터 그룹의 세부 정보를 검색 합니다. |
| [Set-AzureADApplicationProxyConnectorGroup](https://docs.microsoft.com/powershell/module/azuread/set-azureadapplicationproxyapplicationconnectorgroup?view=azureadps-2.0) | 지정 된 응용 프로그램에 지정 된 커넥터 그룹을 할당 합니다.|

## <a name="next-steps"></a>다음 단계

Azure AD PowerShell 모듈에 대 한 자세한 내용은 [AZURE Ad powershell 모듈 개요](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0)를 참조 하세요.

응용 프로그램 프록시에 대 한 다른 PowerShell 예제는 azure [AD 응용 프로그램 프록시에 대 한 AZURE AD PowerShell 예제](../application-proxy-powershell-samples.md)를 참조 하세요.