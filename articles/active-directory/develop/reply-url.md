---
title: 리디렉션 URI 및 회신 URL 제한 사항 - Microsoft ID 플랫폼 | Microsoft
description: 회신 URL/리디렉션 URI 제한 사항
author: SureshJa
ms.author: sureshja
manager: CelesteDG
ms.date: 06/29/2019
ms.topic: conceptual
ms.subservice: develop
ms.custom: aaddev
ms.service: active-directory
ms.reviewer: lenalepa, manrath
ms.openlocfilehash: b7aefc54a20e23ae969750532e7e3bc824f69c56
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83725315"
---
# <a name="redirect-urireply-url-restrictions-and-limitations"></a>리디렉션 URI/회신 URL 제한 및 제한 사항

리디렉션 URI 또는 회신 URL은 앱이 성공적으로 인증되고 인증 코드 또는 액세스 토큰이 부여된 후 권한 부여 서버가 사용자를 보내는 위치입니다. 코드 또는 토큰은 리디렉션 URI 또는 회신 토큰에 포함되어 있으므로 앱 등록 프로세스의 일부로 올바른 위치를 등록하는 것이 중요합니다.

 회신 URL에는 다음 제한 사항이 적용됩니다.

* 회신 URL은 스키마 `https`로 시작해야 합니다.

* 회신 URL은 대/소문자를 구분합니다. 해당 사례는 실행 중인 애플리케이션의 URL 경로에 대한 대/소문자와 일치해야 합니다. 예를 들어 애플리케이션의 경로 `.../abc/response-oidc`의 일부로 포함하는 경우 회신 URL에 `.../ABC/response-oidc`를 지정하지 마세요. 웹 브라우저는 경로를 대/소문자 구분으로 처리하므로 `.../abc/response-oidc`와 연결된 쿠키는 대/소문자가 일치하지 않는 `.../ABC/response-oidc` URL로 리디렉션되는 경우 제외될 수 있습니다.
    
## <a name="maximum-number-of-redirect-uris"></a>최대 리디렉션 URI 수

다음 표에서는 앱을 등록할 때 추가할 수 있는 리디렉션 URI의 최대 개수를 보여 줍니다.

| 로그인한 계정 | 최대 리디렉션 URI 수 | Description |
|--------------------------|---------------------------------|-------------|
| 모든 조직의 Azure AD(Azure Active Directory) 테넌트에 있는 Microsoft 회사 또는 학교 계정 | 256 | 애플리케이션 매니페스트의 `signInAudience` 필드가 *AzureADMyOrg* 또는 *AzureADMultipleOrgs*로 설정됨 |
| 개인 Microsoft 계정 및 회사/학교 계정 | 100 | 애플리케이션 매니페스트의 `signInAudience` 필드가 *AzureADandPersonalMicrosoftAccount*로 설정됨 |

## <a name="maximum-uri-length"></a>최대 URI 길이

앱 등록에 추가하는 각 리디렉션 URI에 대해 최대 256문자를 사용할 수 있습니다.

## <a name="supported-schemes"></a>지원되는 스키마
현재 Azure AD 애플리케이션 모델은 모든 조직의 Azure AD(Azure Active Directory) 테넌트에서 Microsoft 회사 또는 학교 계정에 로그인하는 앱에 HTTP 및 HTTPS 스키마를 모두 지원합니다. 즉. 애플리케이션 매니페스트의 `signInAudience` 필드가 *AzureADMyOrg* 또는 *AzureADMultipleOrgs*로 설정되어 있습니다. 개인 Microsoft 계정 및 회사/학교 계정에 로그인하는 앱의 경우(즉 `signInAudience`가 *AzureADandPersonalMicrosoftAccount*로 설정됨) HTTPS 스키마만 허용됩니다.

> [!NOTE]
> 새 [앱 등록](https://go.microsoft.com/fwlink/?linkid=2083908) 환경에서는 개발자가 UI에서 HTTP 스키마를 사용하는 URI를 추가할 수 없습니다. 회사 또는 학교 계정에 로그인하는 앱에 대한 HTTP URI 추가는 앱 매니페스트 편집기를 통해서만 지원됩니다. 앞으로 새 앱은 리디렉션 URI에서 HTTP 스키마를 사용할 수 없습니다. 그러나 리디렉션 URI에 HTTP 스키마가 포함된 이전 앱은 계속 작동합니다. 개발자는 리디렉션 URI에서 HTTPS 스키마를 사용해야 합니다.

## <a name="restrictions-using-a-wildcard-in-uris"></a>URI에서 와일드카드 사용 제한

`https://*.contoso.com`와 같은 와일드카드 URI는 편리하지만 이를 피해야 합니다. 리디렉션 URI에서 와일드카드를 사용하면 보안에 영향을 미칩니다. OAuth 2.0 사양([RFC 6749의섹션 3.1.2](https://tools.ietf.org/html/rfc6749#section-3.1.2))에 따라 리디렉션 엔드포인트 URI는 절대 URI여야 합니다. 

Azure AD 애플리케이션 모델은 개인 Microsoft 계정 및 회사 또는 학교 계정에 로그인하도록 구성된 앱에 대한 와일드카드 URI를 지원하지 않습니다. 그러나 현재 조직의 Azure AD 테넌트에서 회사 또는 학교 계정에 로그인하도록 구성되어 있는 앱에는 와일드카드 URI를 사용할 수 있습니다. 
 
> [!NOTE]
> 새 [앱 등록](https://go.microsoft.com/fwlink/?linkid=2083908) 환경에서는 개발자가 UI에서 와일드카드 URI를 추가할 수 없습니다. 회사 또는 학교 계정에 로그인하는 앱에 대한 와일드카드 URI 추가는 앱 매니페스트 편집기를 통해서만 지원됩니다. 앞으로 새 앱은 리디렉션 URI에서 와일드카드를 사용할 수 없습니다. 그러나 리디렉션 URI에 와일드카드가 포함된 이전 앱은 계속 작동합니다.

시나리오에 허용되는 최대 제한보다 많은 리디렉션 URI가 필요한 경우 와일드카드 리디렉션 URI를 추가하는 대신 다음 방법을 고려합니다.

### <a name="use-a-state-parameter"></a>상태 매개 변수 사용

다수의 하위 도메인을 사용하는 경우 그리고 인증이 성공하면 사용자를 인증이 시작된 동일한 페이지로 리디렉션해야 하는 시나리오에서는 상태 매개 변수를 사용하는 것이 도움이 될 수 있습니다. 

이 방법의 경우 다음을 수행합니다.

1. 애플리케이션당 "공유" 리디렉션 URI를 만들어 권한 부여 엔드포인트에서 수신하는 보안 토큰을 처리합니다.
1. 애플리케이션은 관련 매개 변수에서 애플리케이션 특정 매개 변수(예: 사용자가 시작한 하위 도메인 URL 또는 브랜딩 정보와 같은 항목)를 보낼 수 있습니다. 상태 매개 변수를 사용하는 경우 [RFC 6749의 섹션 10.12](https://tools.ietf.org/html/rfc6749#section-10.12)에 지정된 대로 CSRF 보호를 제공합니다. 
1. 애플리케이션 관련 매개 변수에는 애플리케이션이 사용자에 대한 올바른 환경을 렌더링하는 데 필요한, 즉 적절한 애플리케이션 상태를 생성하는 데 필요한 모든 정보가 포함됩니다. Azure AD 권한 부여 엔드포인트는 상태 매개 변수에서 HTML을 제거하므로 이 매개 변수에 HTML 콘텐츠를 전달하지 않아야 합니다.
1. Azure AD가 "공유" 리디렉션 URI에 대한 응답을 보내면 이 URI가 상태 매개 변수를 다시 애플리케이션으로 보냅니다.
1. 그런 다음 애플리케이션은 상태 매개 변수의 값을 사용하여 사용자를 추가로 보낼 URL을 결정할 수 있습니다. CSRF 보호의 유효성을 검사해야 합니다.

> [!NOTE]
> 이 접근 방식에서는 손상된 클라이언트가 상태 매개 변수에 전송된 추가 매개 변수를 수정하여 사용자를 다른 URL로 리디렉션할 수 있습니다. 이것이 RFC 6819에 설명된 [개방형 리디렉터 위협](https://tools.ietf.org/html/rfc6819#section-4.2.4)입니다. 따라서 클라이언트는 상태를 암호화하거나 토큰에 대해 리디렉션 URI의 도메인 이름을 확인하는 등의 다른 방법으로 상태를 확인하여 이러한 매개 변수를 보호해야 합니다.

## <a name="next-steps"></a>다음 단계

- [애플리케이션 매니페스트](reference-app-manifest.md)에 대해 알아봅니다.
