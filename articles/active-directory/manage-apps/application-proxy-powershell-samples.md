---
title: Azure AD 응용 프로그램 프록시에 대한 PowerShell 샘플
description: Azure AD 응용 프로그램 프록시에 대해 이러한 PowerShell 샘플을 사용하여 디렉터리에서 응용 프로그램 프록시 앱 및 커넥터에 대한 정보를 얻고, 사용자 및 그룹을 앱에 할당하고, 인증서 정보를 가져옵니다.
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
ms.openlocfilehash: 476bdfdd2c0b81d86900cee18024cc261ee80a07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481265"
---
# <a name="azure-ad-powershell-examples-for-azure-ad-application-proxy"></a>Azure AD 응용 프로그램 프록시에 대한 Azure AD PowerShell 예제

다음 표에는 Azure AD 응용 프로그램 프록시에 대한 PowerShell 스크립트 예제에 대한 링크가 포함되어 있습니다. 이러한 샘플에는 별도로 명시되지 않는 한 [그래프 모듈용 AzureAD V2 PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) 또는 [그래프 모듈 미리 보기 버전용 AzureAD V2 PowerShell이](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview)필요합니다.


이러한 샘플에 사용된 cmdlet에 대한 자세한 내용은 [응용 프로그램 프록시 응용 프로그램 관리](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_application_management) 및 응용 프로그램 프록시 커넥터 [관리를](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_connector_management)참조하십시오.

| | |
|---|---|
|**응용 프로그램 프록시 앱**||
| [모든 응용 프로그램 프록시 앱에 대한 기본 정보 나열](scripts/powershell-get-all-app-proxy-apps-basic.md) | 디렉터리에서 모든 응용 프로그램 프록시 앱에 대한 기본 정보(AppId, DisplayName, ObjId)를 나열합니다. |
| [모든 응용 프로그램 프록시 앱에 대한 확장 정보 목록](scripts/powershell-get-all-app-proxy-apps-extended.md) | 디렉터리내의 모든 응용 프로그램 프록시 앱에 대한 확장 정보(AppId, DisplayName, ExternalUrl, InternalUrl, 외부 인증 유형)를 나열합니다.  |
| [커넥터 그룹별로 모든 응용 프로그램 프록시 앱 나열](scripts/powershell-get-all-app-proxy-apps-by-connector-group.md) | 디렉터리에서 모든 응용 프로그램 프록시 앱과 앱이 할당된 커넥터 그룹에 대한 정보를 나열합니다. |
| [토큰 수명 정책을 사용한 모든 응용 프로그램 프록시 앱 받기](scripts/powershell-get-all-app-proxy-apps-with-policy.md) | 디렉터리에서 토큰 수명 정책 및 세부 정보를 통해 모든 응용 프로그램 프록시 앱을 나열합니다. 이 샘플에는 [그래프 모듈 미리 보기 버전에 대한 AzureAD V2 PowerShell이](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview)필요합니다. |
|**커넥터 그룹**||
| [디렉터리에서 모든 커넥터 그룹 및 커넥터 받기](scripts/powershell-get-all-connectors.md) | 디렉터리에 모든 커넥터 그룹과 커넥터가 나열됩니다. |
| [커넥터 그룹에 할당된 모든 앱을 다른 커넥터 그룹으로 이동](scripts/powershell-move-all-apps-to-connector-group.md) | 현재 커넥터 그룹에 할당된 모든 응용 프로그램을 다른 커넥터 그룹으로 이동합니다. |
|**할당된 사용자 및 그룹**||
| [응용 프로그램 프록시 응용 프로그램에 할당된 사용자 및 그룹 표시](scripts/powershell-display-users-group-of-app.md) | 특정 응용 프로그램 프록시 응용 프로그램에 할당된 사용자 및 그룹을 나열합니다. |
| [응용 프로그램에 사용자 할당](scripts/powershell-assign-user-to-app.md) | 특정 사용자를 응용 프로그램에 할당합니다. |
| [응용 프로그램에 그룹 할당](scripts/powershell-assign-group-to-app.md) | 응용 프로그램에 특정 그룹을 할당합니다. |
|**외부 URL 구성**||
| [기본 도메인(.msappproxy.net)을 사용하여 모든 애플리케이션 프록시 앱 받기](scripts/powershell-get-all-default-domain-apps.md)  | 기본 도메인(.msappproxy.net)을 사용하여 모든 응용 프로그램 프록시 응용 프로그램을 나열합니다. |
| [와일드카드 게시를 사용하여 모든 응용 프로그램 프록시 앱 받기](scripts/powershell-get-all-wildcard-apps.md) | 와일드카드 게시를 사용하여 모든 응용 프로그램 프록시 앱을 나열합니다. |
|**사용자 지정 도메인 구성**||
| [사용자 지정 도메인 및 인증서 정보를 사용하여 모든 응용 프로그램 프록시 앱 받기](scripts/powershell-get-all-custom-domains-and-certs.md) | 사용자 지정 도메인을 사용하는 모든 응용 프로그램 프록시 앱과 사용자 지정 도메인과 연결된 인증서 정보를 나열합니다. |
| [인증서업로드 없이 게시된 모든 Azure AD 프록시 응용 프로그램 앱 받기](scripts/powershell-get-all-custom-domain-no-cert.md) | 사용자 지정 도메인을 사용하지만 업로드된 유효한 TLS/SSL 인증서가 없는 모든 응용 프로그램 프록시 앱을 나열합니다. |
| [동일한 인증서로 게시된 모든 Azure AD 프록시 응용 프로그램 앱 받기](scripts/powershell-get-custom-domain-identical-cert.md) | 동일한 인증서로 게시된 모든 Azure AD 프록시 응용 프로그램 응용 프로그램을 나열합니다. |
| [동일한 인증서로 게시된 모든 Azure AD 프록시 응용 프로그램 앱을 가져옵니다.](scripts/powershell-get-custom-domain-replace-cert.md) | 동일한 인증서로 게시된 Azure AD 프록시 응용 프로그램 앱의 경우 인증서를 일괄로 교체할 수 있습니다. |
