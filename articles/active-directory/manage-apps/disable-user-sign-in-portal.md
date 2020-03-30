---
title: Azure AD에서 엔터프라이즈 앱에 대한 사용자 로그인 을 사용하지 않도록 설정
description: Azure Active Directory에서 사용자가 로그인하지 않도록 엔터프라이즈 애플리케이션을 비활성화하는 방법
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: mimart
ms.reviewer: asteen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 10553898376c4b9236ee62718fffccd45b12d70b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74274084"
---
# <a name="disable-user-sign-ins-for-an-enterprise-app-in-azure-active-directory"></a>Azure Active Directory에서 엔터프라이즈 앱에 대한 사용자 로그인 비활성화

Azure Active Directory(Azure AD)에서 사용자가 로그인할 수 없도록 엔터프라이즈 응용 프로그램을 쉽게 비활성화할 수 있습니다. 엔터프라이즈 앱을 관리하려면 적절한 권한이 필요합니다. 또한 디렉터리에 대한 전역 관리자여야 합니다.

## <a name="how-do-i-disable-user-sign-ins"></a>사용자 로그인을 비활성화하려면 어떻게 합니까?

1. 디렉터리에 대한 전역 관리자인 계정으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **모든 서비스**를 선택하고 텍스트 상자에 **Azure Active Directory**를 입력한 다음, **입력**을 선택합니다.
1. Azure **Active Directory** -  ***디렉터리 이름*** 창(즉, 관리하는 디렉터리의 Azure AD 창)에서 **엔터프라이즈 응용 프로그램을 선택합니다.**
1. 엔터프라이즈 **응용 프로그램 - 모든 응용 프로그램** 창에서 관리할 수 있는 앱 목록이 표시됩니다. 앱을 선택합니다.
1. ***appname*** 창, 즉 제목에서 선택된 앱의 이름을 사용한 창에서 **속성**을 선택합니다.
1. 앱 ***이름*** - **속성** 창에서 사용자가 **로그인할**수 있도록 활성화 **되지 않는** 옵션을 선택합니다.
1. **저장** 명령을 선택합니다.

## <a name="use-azure-ad-powershell-to-disable-an-unlisted-app"></a>Azure AD PowerShell을 사용하여 나열되지 않은 앱을 사용하지 않도록 설정

엔터프라이즈 앱 목록에 나타나지 않는 앱의 AppId를 알고 있는 경우(예: 앱을 삭제했거나 Microsoft에서 사전 승인을 받은 앱으로 인해 서비스 주체가 아직 생성되지 않았기 때문에) [AzureAD PowerShell cmdlet을](https://docs.microsoft.com/powershell/module/azuread/New-AzureADServicePrincipal?view=azureadps-2.0)사용하여 앱의 서비스 주체를 수동으로 만든 다음 비활성화할 수 있습니다.

```PowerShell
# The AppId of the app to be disabled
$appId = "{AppId}"

# Check if a service principal already exists for the app
$servicePrincipal = Get-AzureADServicePrincipal -Filter "appId eq '$appId'"
if ($servicePrincipal) {
    # Service principal exists already, disable it
    Set-AzureADServicePrincipal -ObjectId $servicePrincipal.ObjectId -AccountEnabled $false
} else {
    # Service principal does not yet exist, create it and disable it at the same time
    $servicePrincipal = New-AzureADServicePrincipal -AppId $appId -AccountEnabled $false
}
```

## <a name="next-steps"></a>다음 단계

* [내 그룹 모두 보기](../fundamentals/active-directory-groups-view-azure-portal.md)
* [엔터프라이즈 앱에 사용자 또는 그룹 할당](assign-user-or-group-access-portal.md)
* [엔터프라이즈 앱에서 사용자 또는 그룹 할당 제거](remove-user-or-group-access-portal.md)
* [엔터프라이즈 앱의 이름 또는 로고 변경](change-name-or-logo-portal.md)
