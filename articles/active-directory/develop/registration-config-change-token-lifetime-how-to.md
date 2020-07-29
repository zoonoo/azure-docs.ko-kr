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
ms.date: 04/08/2019
ms.author: ryanwi
ms.custom: aaddev, seoapril2019
ms.openlocfilehash: fc71ebe61fba8c1bdb6b7625b16a50d8995a581a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "80883356"
---
# <a name="how-to-change-the-token-lifetime-defaults-for-a-custom-developed-application"></a>사용자 지정 개발 애플리케이션에 대한 토큰 수명 기본값을 변경하는 방법

이 문서에서는 Azure AD PowerShell을 사용 하 여 토큰 수명 정책을 설정 하는 방법을 보여 줍니다. Azure AD Premium을 사용하면 앱 개발자 및 테넌트 관리자가 기밀이 아닌 클라이언트에 대해 발급된 토큰의 수명을 구성할 수 있습니다. 토큰 수명 정책은 테넌트 전체 또는 액세스 중인 리소스에 설정됩니다.

1. 토큰 수명 정책을 설정하려면 [Azure AD PowerShell 모듈](https://www.powershellgallery.com/packages/AzureADPreview)을 다운로드해야 합니다.
1. **Connect-AzureAD -Confirm** 명령을 실행합니다.

    다음은 최대 사용 기간 단일 요소 새로 고침 토큰을 설정하는 예제 정책입니다. 다음 정책을 만듭니다. ```New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"```

## <a name="next-steps"></a>다음 단계

* 조직의 모든 앱, 다중 테넌트 앱 또는 조직의 특정 서비스 주체에 대해 토큰 수명을 구성하는 방법을 비롯하여 Azure AD에서 발급하는 토큰 수명을 구성하는 방법을 알아보려면 [Azure AD에서 구성 가능한 토큰 수명](https://docs.microsoft.com/azure/active-directory/active-directory-configurable-token-lifetimes)을 참조하세요. 
* [Azure AD 토큰 참조](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims)
