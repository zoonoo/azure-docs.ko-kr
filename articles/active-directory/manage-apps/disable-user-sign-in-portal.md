---
title: Azure AD에서 엔터프라이즈 앱에 대한 사용자 로그인 사용 안 함
description: Azure Active Directory에서 사용자가 로그인하지 않도록 엔터프라이즈 애플리케이션을 비활성화하는 방법
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/12/2019
ms.author: kenwith
ms.reviewer: asteen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 783de636d4520f48f624f3380f811f1f18366330
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99259239"
---
# <a name="disable-user-sign-ins-for-an-enterprise-app-in-azure-active-directory"></a>Azure Active Directory에서 엔터프라이즈 앱에 대한 사용자 로그인 비활성화

Azure AD(Azure Active Directory)에서 사용자가 로그인하지 않도록 엔터프라이즈 애플리케이션을 사용하지 않도록 설정하는 것은 쉽습니다. 엔터프라이즈 앱을 관리하려면 적절한 권한이 있어야 합니다. 또한 디렉터리에 대한 전역 관리자여야 합니다.

## <a name="how-do-i-disable-user-sign-ins"></a>사용자 로그인을 비활성화하려면 어떻게 합니까?

1. 디렉터리에 대한 전역 관리자인 계정으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **모든 서비스** 를 선택하고 텍스트 상자에 **Azure Active Directory** 를 입력한 다음, **입력** 을 선택합니다.
1. **Azure Active Directory** -  **_directoryname_*창, 즉 관리 중인 디렉터리에 대한 Azure AD 창에서* 엔터프라이즈 애플리케이션** 을 선택합니다.
1. **엔터프라이즈 애플리케이션 - 모든 애플리케이션** 창에 관리할 수 있는 앱 목록이 표시됩니다. 앱을 선택합니다.
1. **appname** 창, 즉 제목에서 선택된 앱의 이름을 사용한 창에서 속성을 선택합니다.
1. **appname** *속성* 창에서 **사용자가 로그인할 수 있습니까?** 에 대해 **아니요** 를 선택합니다.
1. **저장** 명령을 선택합니다.

## <a name="use-azure-ad-powershell-to-disable-an-unlisted-app"></a>Azure AD PowerShell을 사용하여 목록에 없는 앱을 사용하지 않도록 설정

엔터프라이즈 앱 목록에 표시되지 않는 앱의 AppId를 알고 있는 경우(예: 앱을 삭제했거나 Microsoft에서 사전 인증되는 앱으로 인해 서비스 주체를 아직 만들지 않은 경우) 앱에 대한 서비스 주체를 수동으로 만든 다음, [AzureAD PowerShell cmdlet](/powershell/module/azuread/New-AzureADServicePrincipal)을 사용하여 사용하지 않도록 설정할 수 있습니다.

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
* [엔터프라이즈 앱에서 사용자 또는 그룹 할당 제거](./assign-user-or-group-access-portal.md)
* [엔터프라이즈 앱의 이름 또는 로고 변경](./add-application-portal-configure.md)
