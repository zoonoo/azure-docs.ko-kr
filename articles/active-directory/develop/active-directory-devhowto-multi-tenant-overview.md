---
title: "모든 Azure AD 사용자에게 로그인할 수 있는 앱 작성 방법 | Microsoft Docs"
description: "모든 Azure Active Directory 테넌트로부터 사용자를 로그인할 수 있게 하는 응용 프로그램(또는 다중 테넌트 응용 프로그램으로 알려짐)을 빌드하기 위한 단계별 지침."
services: active-directory
documentationcenter: 
author: skwan
manager: mbaldwin
editor: 
ms.assetid: 35af95cb-ced3-46ad-b01d-5d2f6fd064a3
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/23/2017
ms.author: skwan;bryanla
translationtype: Human Translation
ms.sourcegitcommit: 1e6ae31b3ef2d9baf578b199233e61936aa3528e
ms.openlocfilehash: f87aedd989ab091efeac5f99e198fb60b6781ab2
ms.lasthandoff: 03/03/2017


---
# <a name="how-to-sign-in-any-azure-active-directory-ad-user-using-the-multi-tenant-application-pattern"></a>다중 테넌트 응용 프로그램 패턴을 사용하여 모든 Azure Active Directory (AD) 사용자를 로그인하는 방법
소프트웨어를 서비스 응용 프로그램으로 많은 조직에 제공하는 경우, 어떠한 Azure AD 테넌트에서나 로그인을 허용하도록 응용 프로그램을 구성할 수 있습니다.  Azure AD에서는 이를 두고 다중 테넌트 응용 프로그램을 만드는 것이라고 합니다.  모든 Azure AD 테넌트의 사용자는 응용 프로그램에 계정을 사용하기로 동의한 후 응용 프로그램에 로그인할 수 있습니다.  

자체 계정 시스템이 있거나 다른 클라우드 공급자로부터 다른 종류의 로그인을 지원받는 기존 응용 프로그램인 경우, 모든 테넌트로부터 Azure AD 로그인을 추가하려면 앱을 등록하고, OAuth2, OpenID Connect 또는 SAML을 통해 로그인 코드를 추가하고, 응용 프로그램에 Microsoft 로그인 단추를 붙이는 것으로 간단히 끝납니다. 아래 단추를 클릭하여 응용 프로그램을 브랜딩하는 방법에 대해 자세히 알아보세요.

[![Sign in button][AAD-Sign-In]][AAD-App-Branding]

이 문서에서는 사용자가 Azure AD에 대한 단일 테넌트 응용 프로그램을 빌드하는 것에 이미 익숙하다고 가정합니다.  잘 알지 못할 경우 [개발자 가이드 홈페이지][AAD-Dev-Guide]로 돌아가 빠른 시작 중 하나를 시도합니다!

다음과 같은 간단한 4 단게를 통해 응용 프로그램을 Azure AD 다중 테넌트 앱으로 변환할 수 있습니다.

1. 응용 프로그램 등록을 다중 테넌트로 업데이트합니다.
2. /common 끝점에 요청을 보내도록 코드를 업데이트합니다. 
3. 여러 발급자 값을 처리하는 코드를 업데이트합니다.
4. 사용자 및 관리자 동의를 이해하고 적절한 코드 변경을 합니다.

각 단계를 자세히 살펴보겠습니다. 또한 [이 다중 테넌트 샘플 목록][AAD-Samples-MT]으로 바로 건너뛸 수 있습니다.

## <a name="update-registration-to-be-multi-tenant"></a>등록을 다중 테넌트로 업데이트합니다.
기본적으로 Azure AD에서 웹앱/API 등록은 단일 테넌트입니다.  [Azure Portal][AZURE-portal]에 있는 응용 프로그램 등록의 속성 페이지에서 "다중 테넌트" 스위치를 찾아 "예"로 설정함으로써 등록을 다중 테넌트로 만들 수 있습니다.

응용 프로그램을 다중 테넌트로 만들기 위해서는 먼저 응용 프로그램의 앱 ID URI이 전역적으로 고유하게 되는 것을 Azure AD에서 필요로 합니다. 앱 ID URI는 프로토콜 메시지에서 응용 프로그램을 식별하는 방법 중 하나입니다.  단일 테넌트 앱의 경우 앱 ID URI이 해당 테넌트 내에서 고유한 것으로 충분합니다.  다중 테넌트 응용 프로그램의 경우, 앱 ID URI이 전역적으로 고유해야 Azure AD가 모든 테넌트에서 응용 프로그램을 찾을 수 있습니다.  앱 ID URI이 Azure AD 테넌트의 확인된 도메인과 일치하는 호스트 이름을 갖게 함으로써 전역 고유성이 적용됩니다.  예를 들어, 테넌트의 이름이 contoso.onmicrosoft.com이라면 유효한 앱 ID URI은 `https://contoso.onmicrosoft.com/myapp`이 될 것입니다.  테넌트가 확인된 도메인 `contoso.com`를 가진 경우, 유효한 앱 ID URI은 또한 `https://contoso.com/myapp`이 됩니다.  앱 ID URI이 이 패턴을 따르지 않는다면 응용 프로그램을 다중 테넌트로 설정하는 것은 실패합니다.

기본 클라이언트 등록은 기본적으로 다중 테넌트입니다.  기본 클라이언트 응용 프로그램 등록을 다중 테넌트로 만들기 위해 아무 조치도 취할 필요가 없습니다.

## <a name="update-your-code-to-send-requests-to-common"></a>/common에 요청을 보내도록 코드를 업데이트합니다.
단일 테넌트 응용 프로그램에서 로그인 요청은 테넌트의 로그인 끝점에 전송됩니다.   예를 들어 contoso.onmicrosoft.com의 끝점은 다음과 같습니다.

    https://login.microsoftonline.com/contoso.onmicrosoft.com

테넌트의 끝점에 보내진 요청은 그 테넌트에 있는 사용자 (또는 게스트)를 그 테넌트의 응용 프로그램으로 로그인하게 할 수 있습니다.  다중 테넌트 응용 프로그램을 사용할 경우, 응용 프로그램은 사용자가 어떤 테넌트에서 오는지 미리 알지 못하므로 요청을 테넌트 끝점에 보낼 수 없습니다.  대신, 요청은 모든 Azure AD 테넌트 간에 멀티플렉싱하는 끝점에 전송됩니다.

    https://login.microsoftonline.com/common

Azure AD는 /common 끝점에서 요청을 받을 때, 사용자를 로그인하고 그 결과 사용자가 어떤 테넌트에서 오는지 검색합니다.  /common 끝점은 Azure AD에서 지원하는 OpenID Connect, OAuth 2.0, SAML 2.0 및 WS-Federation과 같은 모든 인증 프로토콜에서 작동합니다.

그 경우 응용 프로그램에 대한 로그인 응답에는 사용자를 나타내는 토큰이 들어 있습니다.  응용 프로그램은 토큰에 든 발급자 값을 보고 사용자가 어떤 테넌트에서 오는지 알게 됩니다.  응답이 /Common 끝점에서 반환될 때, 토큰에 든 발급자 값이 사용자의 테넌트에 해당합니다.  /common 끝점은 테넌트도 아니고 발급자도 아니며, 단지 멀티플렉서이라는 것을 인식하는 것이 중요합니다.  /Common을 사용할 때, 응용 프로그램에서 토큰의 유효성을 검사하는 논리는 이 점을 고려하도록 업데이트되어야 합니다. 

앞서 언급한 대로, 다중 테넌트 응용 프로그램은 Azure AD 응용 프로그램 브랜딩 지침에 따라 사용자에 대한 일관된 로그인 환경도 제공해야 합니다. 아래 단추를 클릭하여 응용 프로그램을 브랜딩하는 방법에 대해 자세히 알아보세요.

[![Sign in button][AAD-Sign-In]][AAD-App-Branding]

/공통된 끝점과 코드 구현 사용에 대해 자세히 알아보겠습니다.

## <a name="update-your-code-to-handle-multiple-issuer-values"></a>여러 발급자 값을 처리하는 코드를 업데이트합니다.
웹 응용 프로그램 및 웹 API는 Azure AD에서 토큰을 받아 유효성을 검사합니다.  

> [!NOTE]
> 네이티브 클라이언트 응용 프로그램은 Azure AD에 토큰을 요청하고 수신하는 동안에 API로 보내 유효성 검사를 합니다.  네이티브 응용 프로그램은 토큰의 유효성을 검사하지 않으며 그것을 불투명한 것으로 처리해야 합니다.
> 
> 

응용 프로그램이 Azure AD에서 수신한 토큰의 유효성을 검사하는 방법을 살펴보겠습니다.  단일 테넌트 응용 프로그램은 일반적으로 다음과 같은 끝점 값을 취합니다.

    https://login.microsoftonline.com/contoso.onmicrosoft.com

그리고 그것을 사용하여 다음과 같은 메타데이터 URL(이 경우에는 OpenID Connect)을 생성합니다.

    https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration

이는 토큰의 유효성을 검사하는 데 사용할 두 가지 중요한 정보인 테넌트 서명 키와 발급자 값을 다운로드하기 위해서입니다.  각 Azure AD 테넌트에는 양식의 고유한 발급자 값이 있습니다.

    https://sts.windows.net/31537af4-6d77-4bb9-a681-d2394888ea26/

여기서 GUID 값은 테넌트의 테넌트 ID 이름 바꾸기 안전 버전입니다.  `contoso.onmicrosoft.com`에 대한 위의 메타데이터 링크를 클릭하면 문서에서 이 발급자 값을 볼 수 있습니다.

단일 테넌트 응용 프로그램이 토큰의 유효성을 검사할 때, 토큰의 서명을 메타데이터 문서에서 서명 키와 비교하여 맞는지 확인하고, 토큰에 든 발급자 값이 메타 데이터 문서에서 찾은 값과 일치하는지 확인합니다.

/common 끝점은 테넌트에 해당하지 않고 발급자가 아니기 때문에, /common에 대해 메타데이터의 발급자 값을 검토할 때 실제값 대신에 템플릿 기반 URL이 있습니다.

    https://sts.windows.net/{tenantid}/

따라서 다중 테넌트 응용 프로그램은 메타데이터의 발급자 값을 토큰의 `issuer` 값과 맞춰보는 것만으로는 토큰의 유효성을 검사할 수 없습니다.  다중 테넌트 응용 프로그램은 발급자 값의 테넌트 ID 부분을 기반으로 어떤 발급자 값이 유효하고 어떤 값이 아닌지 판단할 논리를 필요로 합니다.  

예를 들어, 다중 테넌트 응용 프로그램이 해당 서비스에 등록한 특정 테넌트로부터의 로그인 만을 허용한다면, 테넌트가 구독자 목록에 들어 있는지 확인하기 위해 발급자 값 또는 토큰의 `tid` 클레임 값을 확인해야 합니다.  다중 테넌트 응용 프로그램이 개인 만을 다루고 테넌트 기반으로 어떠한 액세스 결정도 하지 않는다면, 발급자 값 전체를 무시할 수 있습니다.

다중 테넌트 샘플에서, 이 문서의 끝에 나오는 [관련 콘텐츠](#related-content) 섹션에서 로그인할 수 있도록 Azure AD 테넌트를 사용 설정하기 위해 발급자 유효성 검사가 사용하지 않도록 설정됩니다.

이제 다중 테넌트 응용 프로그램에 로그인하는 사용자를 위한 사용자 환경을 살펴보겠습니다.

## <a name="understanding-user-and-admin-consent"></a>사용자 및 관리자 동의 이해하기
사용자가 Azure AD에서 응용 프로그램에 로그인하려면 응용 프로그램이 사용자의 테넌트에 나타나야 합니다.  이를 통해 조직은 사용자가 테넌트로부터 응용 프로그램에 로그인할 때 고유한 정책을 적용하는 것과 같은 작업을 수행할 수 있습니다.  단일 테넌트 응용 프로그램의 경우 이 등록은 간단합니다. [Azure Portal][AZURE-portal]에서 응용 프로그램을 등록할 때 이루어집니다.

다중 테넌트 응용 프로그램의 경우, 응용 프로그램에 대한 초기 등록은 개발자가 사용한 Azure AD 테넌트에 있습니다.  다른 테넌트에서 사용자가 처음으로 응용 프로그램에 로그인할 때, Azure AD는 응용 프로그램에서 요청하는 사용 권한에 동의하는지 묻습니다.  동의한다면 *서비스 주체* 라는 응용 프로그램의 표현이 사용자 테넌트에 생성되고 로그인은 계속 진행할 수 있습니다. 위임이 또한 사용자의 동의를 응용 프로그램에 기록하는 디렉토리에 만들어집니다. 응용 프로그램의 Application 및 ServicePrincipal 개체와 서로 간의 관계에 대한 자세한 내용은 [응용 프로그램 개체 및 서비스 주체 개체][AAD-App-SP-Objects]를 참조하세요.

![단일 계층 앱에 동의][Consent-Single-Tier] 

이 동의 환경이 응용 프로그램에서 요청한 사용 권한에 따른 영향을 받습니다.  Azure AD에서는 응용 프로그램 전용과 위임이란 두 유형의 사용 권한을 지원합니다.

* 위임된 권한은 응용 프로그램에 사용자가 할 수 있는 작업의 하위 집합에 대해 로그인한 사용자 역할을 할 기능을 부여합니다,  예를 들어 응용 프로그램에 위임된 권한을 부여하여 로그인한 사용자의 일정을 읽게 할 수 있습니다.
* 응용 프로그램 전용 권한은 응용 프로그램의 id에 직접 부여됩니다.  예를 들어 응용 프로그램에 응용 프로그램 전용 권한을 부여하여 테넌트의 사용자 목록을 읽게 할 수 있으며, 누가 응용 프로그램에 로그인했는지에 상관없이 이 작업을 할 수 있습니다.

일부 사용 권한은 일반 사용자가 동의할 수 있는 반면 또 다른 사용 권한은 테넌트 관리자의 동의가 필요합니다. 

### <a name="admin-consent"></a>관리자 동의
응용 프로그램 전용 권한은 테넌트 관리자의 동의를 항상 필요로 합니다.  응용 프로그램이 응용 프로그램 전용 사용 권한을 요청하고 일반 사용자가 응용 프로그램에 로그인을 시도하는 경우, 응용 프로그램에 사용자가 동의할 수 없음을 알리는 오류 메시지가 나타납니다.

위임된 특정 권한은 또한 테넌트 관리자의 동의를 필요로 합니다.  예를 들어, 로그인한 사용자로 Azure AD에 쓰기 저장 기능은 테넌트 관리자의 동의가 필요합니다.  응용 프로그램 전용 권한과 같이, 일반 사용자가 관리자 동의가 필요한 위임된 권한을 요청하는 응용 프로그램에 로그인하려는 경우 응용 프로그램에 오류가 발생합니다.  사용 권한이 관리자 동의를 필요로 하는지 여부는 리소스를 게시한 개발자에 의해 결정되며 리스스에 대한 설명서에서 찾아볼 수 있습니다.  Azure AD Graph API 및 Microsoft Graph API에 대해 사용할 수 있는 권한을 설명하는 항목에 대한 링크는 이 문서의 [관련 콘텐츠](#related-content) 섹션에 있습니다.

응용 프로그램이 관리자 동의가 필요한 권한을 사용할 경우, 응용 프로그램에 관리자가 작업을 시작할 수 있도록 단추나 링크와 같은 제스처가 있어야 합니다.  응용 프로그램에서 이 작업에 대해 보내는 요청은 일반적인 OAuth2/OpenID Connect 권한 부여 요청이지만, 또한 `prompt=admin_consent` 쿼리 문자열 매개 변수도 포함합니다.  일단 관리자가 동의했고 서비스 주체가 고객 테넌트에 만들어졌다면 차후의 로그인 요청은 `prompt=admin_consent` 매개 변수를 필요로 하지 않습니다.   관리자가 요청된 권한이 허용된다고 결정했다면 테넌트의 다른 사용자들에게 그 시점 이후로 동의하라는 메시지가 표시되지 않습니다.

`prompt=admin_consent` 매개 변수는 또한 관리자 동의가 필요하지 않은 권한을 요청하지만, 테넌트 관리자가 응용 프로그램에 한 번 “로그인”하고, 다른 사용자들에게 그 시점 이후로 동의하라는 메시지가 표시되지 않는 환경을 제공하고자 하는 응용 프로그램에서 사용할 수 있습니다.

응용 프로그램이 관리자 동의를 필요로 하고 관리자가 응용 프로그램에 로그인하지만 `prompt=admin_consent` 매개 변수는 전송되지 않는 경우, 관리자는 응용 프로그램에 성공적으로 동의할 수 있지만 자신의 사용자 계정에 대해서만 동의하는 것입니다.  일반 사용자는 여전히 응용 프로그램에 로그인하여 동의할 수 없습니다.  다른 사용자들에게 액세스를 허용하기 전에 응용 프로그램을 탐색하는 기능을 테넌트 관리자에게 주고자 할 때 유용합니다.

테넌트 관리자는 일반 사용자가 응용 프로그램에 동의하는 기능을 사용하지 않도록 설정할 수 있습니다.  이 기능을 사용하지 않도록 설정한다면, 테넌트에 응용 프로그램을 설정할 때 항상 관리자 동의가 필요합니다.  일반 사용자 동의를 사용하지 않도록 설정한 응용 프로그램을 테스트하려면 [Azure Portal][AZURE-portal]의 Azure AD 테넌트 구성 섹션에서 구성 스위치를 찾을 수 있습니다.

> [!NOTE]
> 일부 응용 프로그램은 처음에는 일반 사용자가 동의할 수 있고 나중에는 응용 프로그램이 관리자를 참여시키고 관리자 동의가 필요한 권한을 요청할 수 있는 환경을 원합니다.  현재 Azure AD에서 단일 응용 프로그램 등록을 이렇게 할 방법은 없습니다.  앞으로 제공될 Azure AD v2 끝점은 응용 프로그램이 등록 시가 아니라 런타임 시 사용 권한을 요청할 수 있으며, 이를 통해 이 시나리오를 사용하도록 설정할 수 있습니다.  자세한 내용은 [Azure AD 앱 모델 v2 개발자 가이드][AAD-V2-Dev-Guide]를 참조하세요.
> 
> 

### <a name="consent-and-multi-tier-applications"></a>동의 및 다중 계층 응용 프로그램
응용 프로그램은 여러 계층을 가질 수 있으며, 각 계층은 Azure AD에서 자체 등록에 의해 표현될 수 있습니다.  예를 들어, 웹 API를 호출하는 네이티브 응용 프로그램 또는 웹 API를 호출하는 웹 응용 프로그램.  두 경우 모두, 클라이언트(네이티브 앱 또는 웹앱)는 리소스(웹 API)를 호출하는 권한을 요청합니다.  클라이언트가 고객의 테넌트로 성공적으로 동의되려면 사용 권한을 요청한 모든 리소스가 고객의 테넌트에 이미 있어야 합니다.  이 조건이 충족되지 않으면, Azure AD는 리소스가 먼저 추가되어야 한다는 오류를 반환합니다.

논리 응용 프로그램이 예를 들어 별도의 클라이언트와 리소스와 같은 두 개 이상의 응용 프로그램 등록으로 구성되어 있다면 이것이 문제일 수 있습니다.  우선 리소스를 고객 테넌트에 가져가려면 어떻게 해야 합니까?  Azure AD는 클라이언트와 리소스가 한 번에 동의될 수 있게 하여 이 경우를 다룹니다. 사용자는 동의 페이지에서 클라이언트와 리소스가 모두 요청한 사용 권한의 총계를 볼 수 있습니다.  이 동작을 사용하려면 리소스의 응용 프로그램 등록은 클라이언트의 앱 ID를 응용 프로그램 매니페스트에 `knownClientApplications`로 포함해야 합니다.  예:

    knownClientApplications": ["94da0930-763f-45c7-8d26-04d5938baab2"]

이 속성은 [응용 프로그램의 매니페스트][AAD-App-Manifest] 리소스를 통해 업데이트할 수 있으며, 이 문서 뒷부분의 [관련 콘텐츠](#related-content) 섹션에 있는 웹 API 샘플을 호출하는 다중 계층 네이티브 클라이언트에서 보여 줄 수 있습니다. 아래 다이어그램은 다중 계층 앱에 대한 동의의 개요를 제공합니다.

![알려진 다중 계층 클라이언트 앱에 동의][Consent-Multi-Tier-Known-Client] 

응용 프로그램의 다른 계층이 다른 테넌트에 등록되어 있다면 유사한 사례가 발생합니다.  예를 들어 Office 365 Exchange Online API를 호출하는 네이티브 클라이언트 응용 프로그램을 구축하는 경우를 생각해 보겠습니다.  네이티브 응용 프로그램을 개발하고, 그 후 네이티브 응용 프로그램이 고객 테넌트에서 실행되도록 하려면, Exchange Online 서비스 주체가 있어야 합니다.  이 경우 고객이 자신의 테넌트에 서비스 주체가 생성되도록 하려면 Exchange Online를 구매해야 합니다.  Microsoft 이외의 조직에서 빌드한 API의 경우, API 개발자는 예를 들어 이 문서에 설명된 메커니즘을 사용하여 동의를 하게 하는 웹 페이지와 같이 고객이 응용 프로그램을 고객 테넌트에 동의하는 방법을 제공해야 합니다.  서비스 주체가 테넌트에 만들어진 후 네이티브 응용 프로그램은 API에 대한 토큰을 가져올 수 있습니다.

아래 다이어그램은 다른 테넌트에 등록된 다중 계층 앱에 대한 동의의 개요를 제공합니다.

![다중 계층 다자 앱에 동의][Consent-Multi-Tier-Multi-Party] 

### <a name="revoking-consent"></a>동의 취소
사용자와 관리자는 언제든지 응용 프로그램에 대한 동의를 해지할 수 있습니다.

* 사용자는 자신의 [액세스 패널 응용 프로그램][AAD-Access-Panel] 목록에서 개별 응용 프로그램을 제거하여 해당 응용 프로그램에 대한 액세스 권한을 취소합니다.
* 관리자는 [Azure Portal][AZURE-portal]의 Azure AD 관리 섹션을 사용하여 Azure Ad에서 응용 프로그램을 제거하여 해당 응용 프로그램에 대한 액세스 권한을 취소합니다.

관리자가 테넌트의 모든 사용자에 대해 응용 프로그램에 동의하는 경우 사용자는 개별적으로 액세스를 해지할 수 없습니다.  관리자만이 액세스를 해지할 수 있으며 전체 응용 프로그램에 대해서만 해지할 수 있습니다.

### <a name="consent-and-protocol-support"></a>동의 및 프로토콜 지원
동의는 OAuth, OpenID Connect, WS-federation 및 SAML 프로토콜을 통해 Azure AD에서 지원됩니다.  SAML 및 WS-federation 프로토콜은 `prompt=admin_consent` 매개 변수를 지원하지 않으므로, 관리자 동의는 OAuth 및 OpenID Connect 통해서만 가능합니다.

## <a name="multi-tenant-applications-and-caching-access-tokens"></a>다중 테넌트 응용 프로그램및 액세스 토큰 캐시
다중 테넌트 응용 프로그램은 또한 Azure AD로 보호되는 API를 호출하는 액세스 토큰을 가져올 수도 있습니다.  다중 테넌트 응용 프로그램과 함께 ADAL(Active Directory 인증 라이브러리)을 사용할 경우 일반적인 오류는 먼저 /common을 사용하여 사용자에 대한 토큰을 요청하고, 응답을 받은 후, 또 다시 /common을 사용하여 같은 사용자에 대한 후속 토큰을 요청하는 것입니다.  Azure AD에서 온 응답은 /common이 아닌 테넌트에서 오기 때문에, ADAL은 토큰을 테넌트로부터 온 것인양 캐시합니다. 사용자에 대한 액세스 토큰을 가져오기 위한 /common에 대한 후속 호출은 캐시 항목이 누락되어, 사용자에게 다시 로그인하라는 메시지가 표시됩니다.  캐시 누락을 방지하려면 이미 로그인한 사용자에 대한 후속 호출이 테넌트의 끝점에 대해 있었는지 확인합니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 모든 Azure Active Directory 테넌트에서 사용자를 로그인할 수 있는 응용 프로그램을 구축하는 방법을 알아보았습니다. 앱 및 Azure Active Directory 간에 Single Sign On을 사용하도록 설정하면 Office 365와 같은 Microsoft 리소스에 의해 노출되는 API에 액세스하도록 응용 프로그램을 업데이트할 수도 있습니다. 따라서 사용하는 응용 프로그램에 사용자에 맞는 컨텍스트 정보(예: 프로필 사진 또는 다음 일정 약속)를 표시하는 것과 같은 개인 설정된 환경을 제공할 수 있습니다. Azure Active Directory 및 Exchange, SharePoint, OneDrive, OneNote, Planner, Excel 등과 같은 Office 365 서비스에 대한 API 호출 방법을 자세히 알아보려면 [Microsoft Graph API][MSFT-Graph-overview]를 방문하세요.


## <a name="related-content"></a>관련 콘텐츠
* [다중 테넌트 응용 프로그램 샘플][AAD-Samples-MT]
* [응용 프로그램에 대한 브랜딩 지침][AAD-App-Branding]
* [Azure AD 개발자 가이드][AAD-Dev-Guide]
* [응용 프로그램 개체 및 서비스 주체 개체][AAD-App-SP-Objects]
* [Azure Active Directory와 응용 프로그램 통합][AAD-Integrating-Apps]
* [동의 프레임워크 개요][AAD-Consent-Overview]
* [Microsoft Graph API 권한 범위][MSFT-Graph-permision-scopes](영문)
* [Azure AD Graph API 권한 범위][AAD-Graph-Perm-Scopes]

아래의 설명 섹션을 사용하여 피드백을 제공하고 콘텐츠를 구체화하고 모양을 갖출 수 있습니다.

<!--Reference style links IN USE -->
[AAD-Access-Panel]:  https://myapps.microsoft.com
[AAD-App-Branding]: ./active-directory-branding-guidelines.md
[AAD-App-Manifest]: ./active-directory-application-manifest.md
[AAD-App-SP-Objects]: ./active-directory-application-objects.md
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Consent-Overview]: ./active-directory-integrating-applications.md#overview-of-the-consent-framework
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Graph-Overview]: https://azure.microsoft.com/en-us/documentation/articles/active-directory-graph-api/
[AAD-Graph-Perm-Scopes]: https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes
[AAD-Integrating-Apps]: ./active-directory-integrating-applications.md
[AAD-Samples-MT]: https://azure.microsoft.com/documentation/samples/?service=active-directory&term=multitenant
[AAD-Why-To-Integrate]: ./active-directory-how-to-integrate.md
[AZURE-portal]: https://portal.azure.com
[MSFT-Graph-overview]: https://graph.microsoft.io/en-us/docs/overview/overview
[MSFT-Graph-permision-scopes]: https://graph.microsoft.io/en-us/docs/authorization/permission_scopes

<!--Image references-->
[AAD-Sign-In]: ./media/active-directory-devhowto-multi-tenant-overview/sign-in-with-microsoft-light.png
[Consent-Single-Tier]: ./media/active-directory-devhowto-multi-tenant-overview/consent-flow-single-tier.png
[Consent-Multi-Tier-Known-Client]: ./media/active-directory-devhowto-multi-tenant-overview/consent-flow-multi-tier-known-clients.png
[Consent-Multi-Tier-Multi-Party]: ./media/active-directory-devhowto-multi-tenant-overview/consent-flow-multi-tier-multi-party.png

<!--Reference style links -->
[AAD-App-Manifest]: ./active-directory-application-manifest.md
[AAD-App-SP-Objects]: ./active-directory-application-objects.md
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Integrating-Apps]: ./active-directory-integrating-applications.md
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Graph-Perm-Scopes]: https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity
[AAD-Graph-User-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity
[AAD-How-To-Integrate]: ./active-directory-how-to-integrate.md
[AAD-Security-Token-Claims]: ./active-directory-authentication-scenarios/#claims-in-azure-ad-security-tokens
[AAD-Tokens-Claims]: ./active-directory-token-and-claims.md
[AAD-V2-Dev-Guide]: ../active-directory-appmodel-v2-overview.md
[AZURE-portal]: https://portal.azure.com
[Duyshant-Role-Blog]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/
[JWT]: https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32
[O365-Perm-Ref]: https://msdn.microsoft.com/en-us/office/office365/howto/application-manifest
[OAuth2-Access-Token-Scopes]: https://tools.ietf.org/html/rfc6749#section-3.3
[OAuth2-AuthZ-Code-Grant-Flow]: https://msdn.microsoft.com/library/azure/dn645542.aspx
[OAuth2-AuthZ-Grant-Types]: https://tools.ietf.org/html/rfc6749#section-1.3 
[OAuth2-Client-Types]: https://tools.ietf.org/html/rfc6749#section-2.1
[OAuth2-Role-Def]: https://tools.ietf.org/html/rfc6749#page-6
[OpenIDConnect]: http://openid.net/specs/openid-connect-core-1_0.html
[OpenIDConnect-ID-Token]: http://openid.net/specs/openid-connect-core-1_0.html#IDToken















