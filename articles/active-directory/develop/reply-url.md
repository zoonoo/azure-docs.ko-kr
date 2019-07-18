---
title: 리디렉션 URI/회신 URL 제한 사항 및 제한 사항-Microsoft id 플랫폼
description: 회신 Url/리디렉션 Url 제한 사항 및 제한 사항
author: SureshJa
ms.author: sureshja
manager: CelesteDG
ms.date: 06/29/2019
ms.topic: article
ms.subservice: develop
ms.service: active-directory
ms.reviewer: lenalepa, manrath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 07be7d0c70193fec88782fea681e33d6b4cf4b40
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67486234"
---
# <a name="redirect-urireply-url-restrictions-and-limitations"></a>리디렉션 URI/회신 URL 제한 및 제한 사항

리디렉션 URI 또는 회신 URL에는 권한 부여 서버는 보내는 위치 사용자 한 번에 앱을 성공적으로 인증 되 면 이며 권한 부여 코드 또는 액세스를 부여 토큰입니다. 코드 또는 토큰에 포함 된 리디렉션에서 URI 또는 회신 토큰 이므로 앱 등록 프로세스의 일부로 올바른 위치를 등록 하는 것이 중요 합니다.

## <a name="maximum-number-of-redirect-uris"></a>리디렉션 Uri의 최대 수

다음 표에서 리디렉션 Uri 앱을 등록할 때 추가할 수 있는 최대 수를 보여 줍니다. 

| 로그인 계정 | 리디렉션 Uri의 최대 수 | 설명 |
|--------------------------|---------------------------------|-------------|
| Microsoft 회사 또는 학교 계정에서 조직의 Azure Active Directory (Azure AD) 테 넌 트 | 256 | `signInAudience` 응용 프로그램 매니페스트에서 필드로 설정 되어 *AzureADMyOrg* 또는 *AzureADMultipleOrgs* |
| 개인 Microsoft 계정 및 회사 및 학교 계정 | 100 | `signInAudience` 응용 프로그램 매니페스트에 필드를로 *AzureADandPersonalMicrosoftAccount* |

## <a name="maximum-uri-length"></a>최대 URI 길이

각 리디렉션 앱 등록에 추가 하는 URI에 대 한 최대 256 자를 사용할 수 있습니다.

## <a name="restrictions-using-a-wildcard-in-uris"></a>Uri에서 와일드 카드를 사용 하는 제한

와일드 카드 Uri와 같은 `https://*.contoso.com`는 편리 하 게 되지만 하지 않아야 합니다. 와일드 카드를 사용 하 여 리디렉션에서 URI에는 보안에 미치는 영향 OAuth 2.0 사양에 따라 ([단원 3.1.2 RFC 6749의](https://tools.ietf.org/html/rfc6749#section-3.1.2)), 리디렉션 끝점 URI는 절대 URI 여야 합니다. 

Azure AD 응용 프로그램 모델 개인 Microsoft 계정에 로그인 하 고 작동 하도록 구성 된 앱에 대 한 와일드 카드 Uri를 지원 하지 않습니다 회사 또는 학교 계정. 그러나 와일드 카드 Uri는 작업에 로그인 하거나 학교 조직의 Azure AD 테 넌 트의 계정에 현재 구성 된 앱에 대 한 허용 됩니다. 
 
> [!NOTE]
> 새 [앱 등록](https://go.microsoft.com/fwlink/?linkid=2083908) 환경을 개발자가 UI에서 와일드 카드 Uri를 추가할 수 없습니다. 학교 계정 또는 작업에 로그인 하는 앱에 대 한 URI 시키려면 와일드 카드를 추가 응용 프로그램 매니페스트 편집기를 통해서만 지원 됩니다. 앞으로 새 앱 리디렉션 URI에서에서 와일드 카드를 사용 하는 일을 할 수 없습니다. 그러나 이전 앱 리디렉션에서 와일드 카드를 포함 하는 Uri는 계속 작동 합니다.

자세한 내용은 리디렉션 Uri 리디렉션 URI를 와일드 카드를 추가 하는 대신 허용 된 최대 제한 해야 하는 시나리오의 경우 다음 방법 중 하나를 고려 합니다.

### <a name="use-a-state-parameter"></a>상태 매개 변수 사용

하위 도메인의 수 및 시작 위치는 동일한 페이지에 인증 성공 시 사용자를 리디렉션할 수 있습니다 시나리오에 필요한 경우에 상태 매개 변수를 사용 하 여 유용할 수 있습니다. 

이 접근 방식:

1. 권한 부여 끝점에서 수신 하는 보안 토큰을 처리 하는 응용 프로그램당 "공유" 리디렉션 URI를 만듭니다.
1. 응용 프로그램 상태 매개 변수에서 응용 프로그램 관련 매개 변수 (예: 여기서 시작 된 사용자 또는 그와 같은 브랜딩 정보 하위 도메인 URL)을 보낼 수 있습니다. 상태 매개 변수를 사용 하는 경우에 지정 된 대로 CSRF 보호 방지할 [RFC 6749의 10.12 섹션](https://tools.ietf.org/html/rfc6749#section-10.12)). 
1. 응용 프로그램 관련 매개 변수를 렌더링할 올바른 응용 프로그램에 대 한 환경을 사용자에 대 한 즉, 적절 한 응용 프로그램 상태를 생성 합니다. 필요한 모든 정보가 포함 됩니다. State 매개 변수에서 HTML 따라서 해야 Azure AD 권한 부여 끝점 줄무늬 전달 되지 않고 HTML 콘텐츠를이 매개 변수입니다.
1. Azure AD "공유" 리디렉션 URI에 대 한 응답을 보내면 상태 매개 변수는 응용 프로그램에 다시 전송 됩니다.
1. 응용 프로그램에서에서 사용할 수 값 상태 매개 변수는 추가 사용자를 보낼 URL을 확인 하려면. CSRF 보호에 대 한 유효성을 검사 해야 합니다.

> [!NOTE]
> 이 방법은 손상 된 클라이언트가 전송 하므로 사용자는 다른 URL로 리디렉션 상태 매개 변수를 추가 매개 변수를 수정 하는 데 사용 합니다 [리디렉터 위협 엽니다](https://tools.ietf.org/html/rfc6819#section-4.2.4) RFC 6819에 설명 된 합니다. 따라서 클라이언트 상태를 암호화 하거나 도메인 이름 토큰에 대 한 리디렉션 URI의에서 유효성 검사와 같은 다른 방법으로 확인 하 여 이러한 매개 변수를 보호 해야 합니다.

### <a name="add-redirect-uris-to-service-principals"></a>추가 서비스 주체에 리디렉션 Uri

추가 하는 또 다른 방법은 리디렉션 Uri에는 [서비스 주체](app-objects-and-service-principals.md#application-and-service-principal-relationship) 모든 Azure AD 테 넌 트에서 앱 등록을 나타냅니다. 상태 매개 변수를 사용할 수 없습니다 또는 시나리오를 지 원하는 모든 새 테 넌 트에 대 한 앱 등록에 새 리디렉션 Uri를 추가 해야 하는 경우이 방법을 사용할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

- 에 대 한 자세한는 [응용 프로그램 매니페스트](reference-app-manifest.md)
