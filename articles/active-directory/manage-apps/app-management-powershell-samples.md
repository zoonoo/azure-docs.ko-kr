---
title: Azure Active Directory 애플리케이션 관리용 PowerShell 샘플
description: 이러한 PowerShell 샘플은 Azure Active Directory 테넌트에서 관리하는 앱에 사용됩니다. 이러한 샘플 스크립트를 사용하여 비밀 및 인증서에 대한 만료 정보를 찾을 수 있습니다.
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
ms.openlocfilehash: e63931f62398e1344d001bedb27cc8d0d776bef1
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102193282"
---
# <a name="azure-active-directory-powershell-examples-for-application-management"></a>애플리케이션 관리용 Azure Active Directory PowerShell 예제

다음 표에는 Azure AD 애플리케이션 관리의 PowerShell 스크립트 예제에 대한 링크가 포함되어 있습니다. 이러한 샘플에는 다음 중 하나가 필요합니다.
- [AzureAD V2 PowerShell for Graph 모듈](/powershell/azure/active-directory/install-adv2) 또는
- 달리 명시되지 않는 한 [AzureAD V2 PowerShell for Graph 모듈 미리 보기 버전](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true).

이러한 샘플에 사용된 cmdlet에 대한 자세한 내용은 [애플리케이션](/powershell/module/azuread/?view=azureadps-2.0#applications&preserve-view=true)을 참조하세요.

| 링크 | 설명 |
|---|---|
|**애플리케이션 관리 스크립트**||
| [모든 앱 등록, 비밀 및 인증서 내보내기](scripts/powershell-export-all-app-registrations-secrets-and-certs.md) | 디렉터리에 있는 지정된 앱에 대한 모든 앱 등록, 비밀 및 인증서를 내보냅니다. |
