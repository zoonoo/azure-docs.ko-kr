---
title: 리디렉션 URI & 회신 URL 제한 - Microsoft ID 플랫폼 | Azure
description: URL 회신/리디렉션 URls 제한 & 제한
author: SureshJa
ms.author: sureshja
manager: CelesteDG
ms.date: 06/29/2019
ms.topic: conceptual
ms.subservice: develop
ms.custom: aaddev
ms.service: active-directory
ms.reviewer: lenalepa, manrath
ms.openlocfilehash: 8fdc64632be8b5fcb3dca8de2ee833fef25719fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77656741"
---
# <a name="redirect-urireply-url-restrictions-and-limitations"></a>리디렉션 URI/회신 URL 제한 및 제한 사항

리디렉션 URI 또는 회신 URL은 앱이 성공적으로 승인되고 권한 부여 코드 또는 액세스 토큰이 부여되면 권한 부여 서버가 사용자에게 보내는 위치입니다. 코드 또는 토큰은 리디렉션 URI 또는 회신 토큰에 포함되어 있으므로 앱 등록 프로세스의 일부로 올바른 위치를 등록하는 것이 중요합니다.

 회신 URL에는 다음과 같은 제한 사항이 적용됩니다.

    * 회신 URL은 스키마로 `https`시작해야 합니다.
    * 회신 URL은 대/소문자를 구분합니다. 해당 케이스는 실행 중인 응용 프로그램의 URL 경로의 대/소문자와 일치해야 합니다. 예를 들어 응용 프로그램이 경로의 `.../abc/response-oidc`일부로 포함된 경우 `.../ABC/response-oidc` 회신 URL에 지정하지 마십시오. 웹 브라우저는 경로를 대/소문자 구분으로 처리하므로 대/소문자 가 일치하지 않는 `.../abc/response-oidc` `.../ABC/response-oidc` URL로 리디렉션되는 경우 연결된 쿠키가 제외될 수 있습니다.
    
## <a name="maximum-number-of-redirect-uris"></a>리디렉션 URI의 최대 수

다음 표에서는 앱을 등록할 때 추가할 수 있는 최대 리디렉션 URI의 수를 보여 줍니다.

| 로그인중인 계정 | 리디렉션 URI의 최대 수 | 설명 |
|--------------------------|---------------------------------|-------------|
| 모든 조직의 Azure Active Directory(Azure AD) 테넌트의 Microsoft 작업 또는 학교 계정 | 256 | `signInAudience`응용 프로그램 매니페스트의 필드가 *AzureADMyOrg* 또는 *AzureADMultipleOrg로 설정됩니다.* |
| 개인 Microsoft 계정 및 직장 및 학교 계정 | 100 | `signInAudience`응용 프로그램 매니페스트의 필드가 *AzureADandPersonalMicrosoftAccount로* 설정됩니다. |

## <a name="maximum-uri-length"></a>최대 URI 길이

앱 등록에 추가하는 리디렉션 URI에 대해 최대 256자를 사용할 수 있습니다.

## <a name="supported-schemes"></a>지원되는 체계
현재 Azure AD 응용 프로그램 모델은 조직의 Azure Active Directory(Azure AD) 테넌트에서 Microsoft 회사 또는 학교 계정에 로그인하는 앱에 대한 HTTP 및 HTTPS 스키마를 모두 지원합니다. 응용 `signInAudience` 프로그램 매니페스트의 필드는 *AzureADMyOrg* 또는 *AzureADMultipleOrg로 설정됩니다.* 개인 Microsoft 계정 및 회사 및 학교 `signInAudience` 계정(AzureADandPersonalMicrosoftAccount로 설정된)에 로그인하는 앱의 경우 HTTPS 체계만 허용됩니다. *AzureADandPersonalMicrosoftAccount*

> [!NOTE]
> 새로운 [앱 등록](https://go.microsoft.com/fwlink/?linkid=2083908) 환경은 개발자가 UI에서 HTTP 체계를 사용하여 URI를 추가할 수 없습니다. 직장 또는 학교 계정에 로그인하는 앱에 대한 HTTP URI 추가는 앱 매니페스트 편집기에서만 지원됩니다. 앞으로 새 앱은 리디렉션 URI에서 HTTP 스키마를 사용할 수 없습니다. 그러나 리디렉션 URI에 HTTP 체계가 포함된 이전 앱은 계속 작동합니다. 개발자는 리디렉션 URI에서 HTTPS 스키마를 사용해야 합니다.

## <a name="restrictions-using-a-wildcard-in-uris"></a>URIIs에서 와일드카드사용 제한

와 같은 `https://*.contoso.com`와일드카드 URI는 편리하지만 피해야 합니다. 리디렉션 URI에서 와일드카드를 사용하면 보안에 영향을 미칩니다. OAuth 2.0[사양(RFC 6749의 섹션 3.1.2)에](https://tools.ietf.org/html/rfc6749#section-3.1.2)따라 리디렉션 끝점 URI는 절대 URI여야 합니다. 

Azure AD 응용 프로그램 모델은 개인 Microsoft 계정 및 직장 또는 학교 계정에 로그인하도록 구성된 앱에 대해 와일드카드 URI를 지원하지 않습니다. 그러나 와일드카드 URI는 현재 조직의 Azure AD 테넌트에서 직장 또는 학교 계정에 로그인하도록 구성된 앱에 대해 허용됩니다. 
 
> [!NOTE]
> 새로운 [앱 등록](https://go.microsoft.com/fwlink/?linkid=2083908) 환경은 개발자가 UI에 와일드카드 URI를 추가할 수 없습니다. 직장 이나 학교 계정에 로그인 하는 애플 리 케이 션에 대 한 wilcard URI 추가 응용 프로그램 매니페스트 편집기만 지원 됩니다. 앞으로 새 앱은 리디렉션 URI에서 와일드카드를 사용할 수 없습니다. 그러나 리디렉션 URI에 와일드카드가 포함된 이전 앱은 계속 작동합니다.

와일드카드 리디렉션 URI를 추가하는 대신 허용된 최대 한도보다 더 많은 리디렉션 URI가 필요한 시나리오에 다음 방법을 고려하십시오.

### <a name="use-a-state-parameter"></a>상태 매개 변수 사용

여러 하위 도메인이 있고 시나리오에서 성공적인 인증을 받을 때 사용자를 시작한 동일한 페이지로 리디렉션해야 하는 경우 상태 매개 변수를 사용하는 것이 유용할 수 있습니다. 

이 방법에서는 다음을 수행합니다.

1. 응용 프로그램당 "공유" 리디렉션 URI를 만들어 권한 부여 끝점에서 받는 보안 토큰을 처리합니다.
1. 응용 프로그램은 상태 매개 변수에 응용 프로그램 별 매개 변수(예: 사용자가 시작된 하위 도메인 URL 또는 브랜딩 정보와 같은 항목)를 보낼 수 있습니다. 상태 매개 변수를 사용하는 경우 [RFC 6749의 섹션 10.12에](https://tools.ietf.org/html/rfc6749#section-10.12)지정된 CSRF 보호에 대해 보호합니다. 
1. 응용 프로그램 별 매개 변수에는 사용자에게 올바른 환경, 즉 적절한 응용 프로그램 상태를 생성하는 데 필요한 모든 정보가 포함됩니다. Azure AD 권한 부여 끝점은 상태 매개 변수에서 HTML을 제거하므로 이 매개 변수에서 HTML 콘텐츠를 전달하지 않는지 확인합니다.
1. Azure AD가 "공유" 리디렉션 URI에 대한 응답을 보내면 상태 매개 변수를 응용 프로그램으로 다시 보냅니다.
1. 그런 다음 응용 프로그램은 상태 매개 변수의 값을 사용하여 사용자를 추가로 보낼 URL을 결정할 수 있습니다. CSRF 보호를 위해 유효성을 검사해야 합니다.

> [!NOTE]
> 이 방법을 사용하면 손상된 클라이언트가 상태 매개 변수에서 전송된 추가 매개 변수를 수정하여 사용자를 RFC 6819에 설명된 [열린 redirector 위협인](https://tools.ietf.org/html/rfc6819#section-4.2.4) 다른 URL로 리디렉션할 수 있습니다. 따라서 클라이언트는 상태를 암호화하거나 토큰에 대해 리디렉션 URI에서 도메인 이름 유효성을 검사하는 등의 다른 방법으로 이러한 매개 변수를 보호해야 합니다.

## <a name="next-steps"></a>다음 단계

- [응용 프로그램 매니페스트에](reference-app-manifest.md) 대해 알아보기
