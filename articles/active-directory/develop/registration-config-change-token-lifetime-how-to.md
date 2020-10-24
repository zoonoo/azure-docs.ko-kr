---
title: 사용자 지정 Azure AD 앱에 대 한 토큰 수명 기본값 변경
description: Azure AD에서 개발 중인 애플리케이션에 대한 토큰 수명 정책을 업데이트하는 방법
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 10/23/2020
ms.author: ryanwi
ms.custom: aaddev, seoapril2019
ms.openlocfilehash: 9483fe972cf1a4dce4fb285ced3cb390d0bda725
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/24/2020
ms.locfileid: "92516786"
---
# <a name="how-to-change-the-token-lifetime-defaults-for-a-custom-developed-application"></a>사용자 지정 개발 애플리케이션에 대한 토큰 수명 기본값을 변경하는 방법

이 문서에서는 Azure AD PowerShell을 사용 하 여 토큰 수명 정책을 설정 하는 방법을 보여 줍니다. Azure AD Premium을 사용하면 앱 개발자 및 테넌트 관리자가 기밀이 아닌 클라이언트에 대해 발급된 토큰의 수명을 구성할 수 있습니다. 토큰 수명 정책은 테넌트 전체 또는 액세스 중인 리소스에 설정됩니다.

> [!IMPORTANT]
> 2021 년 1 월 30 일 후에는 테 넌 트가 더 이상 새로 고침 및 세션 토큰 수명을 구성할 수 없으며, Azure Active Directory 해당 날짜 이후 정책에서 기존 새로 고침 및 세션 토큰 구성을 다시 설정 하는 것이 중지 됩니다. 사용 중단 후에도 액세스 토큰 수명을 구성할 수 있습니다. 자세한 내용은 [AZURE AD의 구성 가능한 토큰 수명](./active-directory-configurable-token-lifetimes.md)을 참조 하세요.
> Azure AD 조건부 액세스에서 [인증 세션 관리 기능](../conditional-access/howto-conditional-access-session-lifetime.md)   을 구현 했습니다. 이 새로운 기능을 사용 하 여 로그인 빈도를 설정 하 여 새로 고침 토큰 수명을 구성할 수 있습니다.  

토큰 수명 정책을 설정하려면 [Azure AD PowerShell 모듈](https://www.powershellgallery.com/packages/AzureADPreview)을 다운로드해야 합니다.
**Connect-AzureAD -Confirm** 명령을 실행합니다.

사용자가 웹 앱에서 더 자주 인증 해야 하는 정책 예제는 다음과 같습니다. 이 정책은 액세스/ID 토큰의 수명 및 다단계 세션 토큰의 최대 기간을 웹앱의 서비스 주체로 설정합니다. 정책을 만들고 서비스 주체에 할당 합니다. 서비스 주체의 ObjectId도 가져와야 합니다.

```powershell
$policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"02:00:00","MaxAgeSessionSingleFactor":"02:00:00"}}') -DisplayName "WebPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"

$sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"

Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
```

## <a name="next-steps"></a>다음 단계

* 조직의 모든 앱, 다중 테넌트 앱 또는 조직의 특정 서비스 주체에 대해 토큰 수명을 구성하는 방법을 비롯하여 Azure AD에서 발급하는 토큰 수명을 구성하는 방법을 알아보려면 [Azure AD에서 구성 가능한 토큰 수명](./active-directory-configurable-token-lifetimes.md)을 참조하세요. 
* [Azure AD 토큰 참조](./id-tokens.md)