---
title: Azure Active Directory 새로운 변경 참조 | Microsoft Docs
description: 애플리케이션에 영향을 줄 수 있는 Azure AD 프로토콜의 변경 내용을 알아봅니다.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 68517c83-1279-4cc7-a7c1-c7ccc3dbe146
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 3da99506d50ec12140c188cd86cde2556be4a308
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/28/2019
ms.locfileid: "55099144"
---
# <a name="whats-new-for-authentication"></a>인증의 새로운 기능? 

>이 페이지의 업데이트에 대한 알림을 받습니다. RSS 피드 리더에 [이 URL](https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20for%20authentication%22&locale=en-us)을 추가하면 됩니다.

인증 시스템에서는 보안 및 표준 준수를 개선하기 위해 지속적으로 변경하고 기능을 추가합니다. 최신 개발 정보를 확인할 수 있도록 이 문서에서는 다음과 같은 세부 정보를 제공합니다.

- 최신 기능
- 알려진 문제
- 프로토콜 변경 내용
- 사용되지 않는 기능

> [!TIP] 
> 이 페이지는 정기적으로 업데이트되므로 자주 방문해 주세요. 별도로 명시되지 않는 한, 이 변경 내용은 새로 등록된 애플리케이션에만 적용됩니다.  

## <a name="upcoming-changes"></a>예정된 변경

지금은 예약이 없습니다. 

## <a name="october-2018"></a>2018년 10월

### <a name="authorization-codes-can-no-longer-be-reused"></a>권한 부여 코드는 더 이상 재사용할 수 없습니다.

**개시 날짜**: 2018년 11월 15일

**영향을 받는 엔드포인트**: v1.0 및 v2.0 모두

**영향을 받는 프로토콜**: [코드 흐름](v2-oauth2-auth-code-flow.md)

2018년 11월 15일부터 Azure AD는 앱에서 이전에 사용된 인증 코드를 더 이상 수락하지 않습니다. 이 보안 변경은 Azure AD를 OAuth 사양에 맞추는 데 도움이 되며 v1 및 v2 엔드포인트 모두에 적용됩니다.

앱에서 여러 리소스에 대한 토큰을 얻기 위해 인증 코드를 재사용하는 경우에는 코드를 사용하여 새로 고침 토큰을 얻은 다음, 이 새로 고침 토큰을 사용하여 다른 리소스에 대한 추가 토큰을 얻는 것이 좋습니다. 인증 코드는 한 번만 사용할 수 있지만 새로 고침 토큰은 여러 리소스에서 여러 번 사용할 수 있습니다. OAuth 코드 흐름 중에 인증 코드를 다시 사용하려고 하는 새 앱에서는 invalid_grant 오류가 발생합니다.

새로 고침 토큰에 대한 자세한 내용은 [액세스 토큰 새로 고침](v1-protocols-oauth-code.md#refreshing-the-access-tokens)을 참조하세요.  ADAL 또는 MSAL을 사용하는 경우 이 작업은 라이브러리를 통해 처리됩니다. 'AcquireTokenByAuthorizationCodeAsync'의 두 번째 인스턴스를 'AcquireTokenSilentAsync'로 바꿉니다. 

## <a name="may-2018"></a>2018년 5월

### <a name="id-tokens-cannot-be-used-for-the-obo-flow"></a>ID 토큰은 OBO 흐름에 사용할 수 없습니다.

**날짜**: 2018년 5월 1일

**영향을 받는 엔드포인트**: v1.0 및 v2.0 모두

**영향을 받는 프로토콜**: 암시적 흐름 및 [OBO 흐름](v1-oauth2-on-behalf-of-flow.md)

2018년 5월 1일 이 OBO 흐름에서 새 애플리케이션의 어설션으로 id_token을 사용할 수 없습니다. API를 보호하려면 클라이언트와 동일한 애플리케이션의 중간 계층 사이에서도 액세스 토큰을 대신 사용해야 합니다. 2018년 5월 1일 전에 등록된 앱은 계속 작동하고 id_token을 액세스 토큰으로 교환할 수 있지만 이 패턴은 모범 사례로 간주되지 않습니다.

이 변경 문제를 해결하려면 다음을 수행할 수 있습니다.

1. 하나 이상의 범위가 있는 애플리케이션의 Web API를 만듭니다. 이 명시적 진입점으로 인해 더 세분화된 제어 및 보안이 가능합니다.
1. 앱 매니페스트, [Azure Portal](https://portal.azure.com) 또는 [앱 등록 포털](https://apps.dev.microsoft.com)에서 앱이 암시적 흐름을 통해 액세스 토큰을 발급할 수 있는지 확인합니다. 이 작업은 `oauth2AllowImplicitFlow` 키를 통해 제어됩니다.
1. 클라이언트 애플리케이션에서 `response_type=id_token`을 통해 id_token을 요청할 때 위에서 만든 Web API의 액세스 토큰(`response_type=token`)도 요청합니다. 따라서 v2.0 엔드포인트를 사용하는 경우 `scope` 매개 변수는 `api://GUID/SCOPE`와 비슷하게 표시되어야 합니다. V1.0 엔드포인트에서 `resource` 매개 변수는 Web API의 앱 URI여야 합니다.
1. Id_token 대신 중간 계층으로 이 액세스 토큰을 전달합니다.  
