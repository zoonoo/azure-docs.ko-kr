---
title: Azure AD 응용 프로그램 프록시에 대 한 PowerShell 샘플
description: Azure AD 응용 프로그램 프록시에 대해 이러한 PowerShell 샘플을 사용 하 여 디렉터리의 응용 프로그램 프록시 앱 및 커넥터에 대 한 정보를 가져오고, 앱에 사용자 및 그룹을 할당 하 고, 인증서 정보를 가져옵니다.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79481265"
---
# <a name="azure-ad-powershell-examples-for-azure-ad-application-proxy"></a>Azure AD 응용 프로그램 프록시에 대 한 azure AD PowerShell 예제

다음 표에서는 Azure AD 응용 프로그램 프록시에 대 한 PowerShell 스크립트 예제에 대 한 링크를 제공 합니다. 이러한 샘플을 보려면 [AzureAD V2 powershell For graph 모듈이](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) 필요 하거나 다른 설명이 없는 한 [AzureAD V2 powershell for graph module preview 버전이](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview)필요 합니다.


이러한 샘플에 사용 된 cmdlet에 대 한 자세한 내용은 [응용 프로그램 프록시 응용 프로그램 관리](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_application_management) 및 [응용 프로그램 프록시 커넥터 관리](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_connector_management)를 참조 하세요.

| | |
|---|---|
|**응용 프로그램 프록시 앱**||
| [모든 응용 프로그램 프록시 앱에 대 한 기본 정보 나열](scripts/powershell-get-all-app-proxy-apps-basic.md) | 디렉터리의 모든 응용 프로그램 프록시 앱에 대 한 기본 정보 (AppId, DisplayName, ObjId)를 나열 합니다. |
| [모든 응용 프로그램 프록시 앱에 대 한 확장 정보 나열](scripts/powershell-get-all-app-proxy-apps-extended.md) | 디렉터리의 모든 응용 프로그램 프록시 앱에 대 한 확장 정보 (AppId, DisplayName, ExternalUrl, InternalUrl, ExternalAuthenticationType)를 나열 합니다.  |
| [커넥터 그룹별로 모든 응용 프로그램 프록시 앱 나열](scripts/powershell-get-all-app-proxy-apps-by-connector-group.md) | 디렉터리의 모든 응용 프로그램 프록시 앱에 대 한 정보 및 앱이 할당 된 커넥터 그룹을 나열 합니다. |
| [토큰 수명 정책을 사용 하 여 모든 응용 프로그램 프록시 앱 가져오기](scripts/powershell-get-all-app-proxy-apps-with-policy.md) | 토큰 수명 정책 및 세부 정보를 사용 하 여 디렉터리의 모든 응용 프로그램 프록시 앱을 나열 합니다. 이 샘플을 사용 하려면 [AzureAD V2 PowerShell For Graph module preview 버전이](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview)필요 합니다. |
|**커넥터 그룹**||
| [디렉터리의 모든 커넥터 그룹 및 커넥터 가져오기](scripts/powershell-get-all-connectors.md) | 디렉터리의 커넥터 그룹 및 커넥터를 모두 나열 합니다. |
| [커넥터 그룹에 할당 된 모든 앱을 다른 커넥터 그룹으로 이동](scripts/powershell-move-all-apps-to-connector-group.md) | 현재 커넥터 그룹에 할당 된 모든 응용 프로그램을 다른 커넥터 그룹으로 이동 합니다. |
|**할당 된 사용자 및 그룹**||
| [응용 프로그램 프록시 응용 프로그램에 할당 된 사용자 및 그룹 표시](scripts/powershell-display-users-group-of-app.md) | 특정 응용 프로그램 프록시 응용 프로그램에 할당 된 사용자 및 그룹을 나열 합니다. |
| [응용 프로그램에 사용자 할당](scripts/powershell-assign-user-to-app.md) | 응용 프로그램에 특정 사용자를 할당 합니다. |
| [응용 프로그램에 그룹 할당](scripts/powershell-assign-group-to-app.md) | 응용 프로그램에 특정 그룹을 할당 합니다. |
|**외부 URL 구성**||
| [기본 도메인 (. msappproxy.net)을 사용 하 여 모든 응용 프로그램 프록시 앱 가져오기](scripts/powershell-get-all-default-domain-apps.md)  | 기본 도메인 (. msappproxy.net)을 사용 하는 모든 응용 프로그램 프록시 응용 프로그램을 나열 합니다. |
| [와일드 카드 게시를 사용 하 여 모든 응용 프로그램 프록시 앱 가져오기](scripts/powershell-get-all-wildcard-apps.md) | 와일드 카드 게시를 사용 하는 모든 응용 프로그램 프록시 앱을 나열 합니다. |
|**사용자 지정 도메인 구성**||
| [사용자 지정 도메인 및 인증서 정보를 사용 하 여 모든 응용 프로그램 프록시 앱 가져오기](scripts/powershell-get-all-custom-domains-and-certs.md) | 사용자 지정 도메인 및 사용자 지정 도메인과 연결 된 인증서 정보를 사용 하는 모든 응용 프로그램 프록시 앱을 나열 합니다. |
| [인증서를 업로드 하지 않고 게시 된 모든 Azure AD 프록시 응용 프로그램 앱 가져오기](scripts/powershell-get-all-custom-domain-no-cert.md) | 사용자 지정 도메인을 사용 하지만 유효한 TLS/SSL 인증서를 업로드 하지 않은 모든 응용 프로그램 프록시 앱을 나열 합니다. |
| [동일한 인증서로 게시 된 모든 Azure AD 프록시 응용 프로그램 앱 가져오기](scripts/powershell-get-custom-domain-identical-cert.md) | 동일한 인증서를 사용 하 여 게시 된 모든 Azure AD 프록시 응용 프로그램 앱을 나열 합니다. |
| [동일한 인증서로 게시 된 모든 Azure AD 프록시 응용 프로그램 앱을 가져와 바꿉니다.](scripts/powershell-get-custom-domain-replace-cert.md) | 동일한 인증서를 사용 하 여 게시 된 Azure AD 프록시 응용 프로그램 앱의 경우 인증서를 대량으로 바꿀 수 있습니다. |
