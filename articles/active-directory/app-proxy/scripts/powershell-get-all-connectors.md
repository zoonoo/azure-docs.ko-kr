---
title: PowerShell 샘플 - 모든 Azure Active Directory 애플리케이션 프록시 커넥터 그룹 나열
description: 사용자 디렉터리의 모든 Azure AD(Azure Active Directory) 애플리케이션 프록시 커넥터 그룹 및 커넥터를 나열하는 PowerShell 예제입니다.
services: active-directory
author: kenwith
manager: mtillman
ms.service: active-directory
ms.subservice: app-proxy
ms.workload: identity
ms.topic: sample
ms.date: 04/29/2021
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: a8b820ce6859b4a6093bb4986e1e4f2d0458cdf6
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108293729"
---
# <a name="get-all-application-proxy-connector-groups-and-connectors-in-the-directory"></a>디렉터리의 모든 애플리케이션 프록시 커넥터 그룹 및 커넥터 가져오기

이 PowerShell 스크립트 예제에서는 사용자 디렉터리의 모든 Azure AD(Azure Active Directory) 애플리케이션 프록시 커넥터 그룹 및 커넥터를 나열합니다.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

이러한 샘플을 사용하려면 [Graph용 AzureAD V2 PowerShell 모듈](/powershell/azure/active-directory/install-adv2)(AzureAD) 또는 [Graph용 AzureAD V2 PowerShell 모듈 미리 보기 버전](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true)(AzureADPreview)이 필요합니다.

## <a name="sample-script"></a>샘플 스크립트

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-connectors.ps1 "Get all connector groups and connectors in the directory")]

## <a name="script-explanation"></a>스크립트 설명

| 명령 | 메모 |
|---|---|
| [Get-AzureADApplicationProxyConnectorGroup](/powershell/module/azuread/get-azureadapplicationproxyconnectorgroup) | 모든 커넥터 그룹 목록을 검색하거나 지정된 커넥터 그룹의 세부 정보를 검색합니다(지정된 경우). |
| [Get-AzureADApplicationProxyConnectorGroupMembers](/powershell/module/azuread/get-azureadapplicationproxyconnectorgroupmembers) | 각 커넥터 그룹과 연관된 모든 애플리케이션 프록시 커넥터를 가져옵니다.|

## <a name="next-steps"></a>다음 단계

Azure AD PowerShell 모듈에 대한 자세한 내용은 [Azure PowerShell 모듈 개요](/powershell/azure/active-directory/overview)를 참조하세요.

애플리케이션 프록시에 대한 다른 PowerShell 예제는 [Azure AD 애플리케이션 프록시에 대한 Azure AD PowerShell 예제](../application-proxy-powershell-samples.md)를 참조하세요.
