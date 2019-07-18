---
title: Azure Active Directory B2C란? | Microsoft Docs
description: Azure Active Directory B2C를 사용하여 애플리케이션에서 등록 로그인 및 프로필 관리와 같은 ID 환경을 만들고 관리하는 방법을 알아봅니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 02/20/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 5cceac260979b4322d41843038eab0998c8e8ba4
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66509756"
---
# <a name="what-is-azure-active-directory-b2c"></a>Azure Active Directory B2C란?

Azure AD(Azure Active Directory) B2C는 비즈니스-소비자 ID 관리 서비스입니다. 이 서비스를 사용하면 사용자가 웹, 데스크톱, 모바일 또는 단일 페이지 애플리케이션과 안전하게 상호 작용하는 방식을 사용자 지정하고 제어할 수 있습니다. Azure AD B2C를 사용하여 사용자는 등록, 로그인, 암호 재설정 및 프로필 편집을 수행할 수 있습니다. Azure AD B2C는 OpenID Connect 및 OAuth 2.0 프로토콜의 형태를 구현합니다. 이러한 프로토콜을 구현하는 데 있어 중요한 키는 리소스에 대한 보안 액세스를 제공할 수 있는 보안 토큰 및 해당 클레임입니다.

*사용자 경험*은 사용자 및 애플리케이션에서 Azure AD B2C와 상호 작용하는 방법에 대한 동작을 제어하는 정책을 지정하는 요청입니다. Azure AD B2C에서 사용자 경험을 정의하는 데 두 가지 경로를 사용할 수 있습니다. 

ID 전문 지식의 유무에 관계없이 애플리케이션 개발자인 경우 Azure Portal을 사용하여 일반적인 ID 사용자 흐름을 정의하도록 선택할 수 있습니다. ID 전문가, 시스템 통합자, 컨설턴트 또는 사내 ID 팀 멤버이고 OpenID Connect 흐름에 익숙하며 ID 공급자 및 클레임 기반 인증을 이해하고 있는 경우 XML 기반 사용자 지정 정책을 선택할 수 있습니다.

사용자 경험 정의를 시작하기 전에 Azure AD B2C 테넌트를 만들고 이 테넌트에서 애플리케이션과 API를 등록해야 합니다. 이러한 작업이 완료되면 사용자 흐름 또는 사용자 지정 정책을 사용하여 사용자 경험 정의를 시작할 수 있습니다. 또한 필요에 따라 ID 공급자를 추가하거나 변경하거나 사용자가 사용자 경험을 사용하는 방식을 사용자 지정할 수도 있습니다.

## <a name="protocols-and-tokens"></a>프로토콜 및 토큰

Azure AD B2C는 사용자 경험에 대해 [OpenID Connect 및 OAuth 2.0 프로토콜](active-directory-b2c-reference-protocols.md)을 지원합니다. OpenID Connect의 Azure AD B2C 구현에서 애플리케이션은 Azure AD B2C로 인증 요청을 발급하여 이러한 사용자 이동을 시작합니다. 

Azure AD B2C에 대한 요청의 결과는 [ID 토큰 또는 액세스 토큰](active-directory-b2c-reference-tokens.md)과 같은 보안 토큰입니다. 이 보안 토큰은 사용자의 ID를 정의합니다. 토큰은 `/token` 또는 `/authorize` 엔드포인트와 같은 Azure AD B2C 엔드포인트로부터 받습니다. 이러한 토큰에서 ID의 유효성을 검사하고 보안 리소스에 대한 액세스를 허용하는 데 사용할 수 있는 클레임에 액세스할 수 있습니다.

## <a name="tenants-and-applications"></a>테넌트 및 애플리케이션

Azure AD B2C에서 *테넌트*는 조직을 나타내며 사용자의 디렉터리입니다. 각 Azure AD B2C 테넌트는 서로 전혀 다르고 다른 Azure AD B2C 테넌트와 별개입니다. Azure Active Directory 테넌트가 이미 있을 수 있으며, Azure AD B2C 테넌트는 다른 테넌트입니다. 테넌트에는 애플리케이션을 사용할 수 있도록 등록한 사용자에 대한 정보가 포함되어 있습니다. 암호, 프로필 데이터, 권한 등을 예로 들 수 있습니다. 자세한 내용은 [자습서: Azure Active Directory B2C 테넌트 만들기](tutorial-create-tenant.md)의 단계를 사용하여 만들 수 있습니다.

Azure AD B2C를 사용하도록 애플리케이션을 구성하기 전에 먼저 Azure Portal을 사용하여 테넌트에 등록해야 합니다. 등록 프로세스는 값을 수집하고 애플리케이션에 할당합니다. 이러한 값에는 애플리케이션을 고유하게 식별하는 애플리케이션 ID 및 응답을 애플리케이션에 다시 전달하는 데 사용되는 리디렉션 URI가 포함됩니다.

모든 애플리케이션의 상호 작용은 높은 수준에서 비슷한 패턴을 따릅니다.

1. 애플리케이션에서 사용자가 정책을 실행하도록 지시합니다.
2. 사용자는 정책 정의에 따라 정책을 완료합니다.
3. 애플리케이션에서 토큰을 받습니다.
4. 애플리케이션에서 토큰을 사용하여 리소스에 액세스하려고 합니다.
5. 리소스 서버에서 토큰의 유효성을 검사하여 액세스 권한을 부여할 수 있는지 확인합니다.
6. 애플리케이션에서 주기적으로 토큰을 새로 고칩니다.

웹 애플리케이션을 등록하려면 [자습서: Azure AD B2C를 가입 및 로그인에 사용하도록 애플리케이션 등록](tutorial-register-applications.md)의 단계를 완료합니다. [웹 API 애플리케이션을 Azure Active Directory B2C 테넌트에 추가](add-web-application.md)하거나 [네이티브 클라이언트 애플리케이션을 Azure Active Directory B2C 테넌트에 추가](add-native-application.md)할 수도 있습니다.

## <a name="user-journeys"></a>사용자 경험

사용자 경험의 정책은 [사용자 흐름](active-directory-b2c-reference-policies.md) 또는 [사용자 지정 정책](active-directory-b2c-overview-custom.md)으로 정의할 수 있습니다. 등록, 로그인 및 프로필 편집과 같은 가장 일반적인 ID 작업에 대해 미리 정의된 사용자 흐름은 Azure Portal에서 사용할 수 있습니다.

사용자 경험을 사용하면 다음 설정을 구성하여 동작을 제어할 수 있습니다.

- 사용자가 애플리케이션을 등록하는 데 사용할 수 있는 소셜 계정
- 사용자로부터 수집한 데이터(예: 이름 또는 우편 번호)
- Multi-Factor Authentication
- 페이지의 모양과 느낌
- 애플리케이션에 반환되는 정보

사용자 지정 정책은 Azure AD B2C 테넌트에서 [Identity Experience Framework](trustframeworkpolicy.md)의 동작을 정의하는 구성 파일입니다. Identity Experience Framework는 다자간 신뢰를 설정하고 사용자 경험의 단계를 수행하는 기본 플랫폼입니다. 

다양한 작업을 수행할 수 있게 사용자 지정 정책을 변경할 수 있습니다. 사용자 지정 정책은 계층 구조 체인에서 서로를 참조하는 하나 또는 여러 XML 형식 파일입니다. 사용자 지정 정책에서 일반적인 ID 작업을 사용하도록 설정하는 [스타터 팩](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip)을 사용할 수 있습니다. 

다양한 형식의 사용자 지정 정책 또는 사용자 흐름은 필요에 따라 Azure AD B2C 테넌트에서 사용되며 애플리케이션에서 다시 사용할 수 있습니다. 이러한 유연성을 통해 코드를 최소한으로 변경하거나 전혀 변경하지 않고도 사용자 ID 환경을 정의하고 수정할 수 있습니다. HTTP 인증 요청에 특수 쿼리 매개 변수를 추가하여 정책을 사용합니다. 사용자 고유의 사용자 지정 정책을 만들려면 [Azure Active Directory B2C에서 사용자 지정 정책 시작](active-directory-b2c-get-started-custom.md)을 참조하세요.

## <a name="identity-providers"></a>ID 공급자 

애플리케이션에서 사용자가 다른 ID 공급자로 로그인하도록 허용할 수 있습니다. ‘ID 공급자’는 애플리케이션에 인증 서비스를 제공하는 동시에 ID 정보를 만들고 유지 관리합니다.  Azure Portal을 사용하여 Azure AD B2C에서 지원되는 ID 공급자를 추가할 수 있습니다. 

일반적으로 애플리케이션에는 하나의 ID 공급자만 사용되지만, ID 공급자를 추가할 수 있는 옵션이 있습니다. Azure AD B2C 테넌트에서 ID 공급자를 구성하려면 먼저 애플리케이션을 ID 공급자 개발자 사이트에 만든 다음, 생성한 ID 공급자 애플리케이션에서 애플리케이션 식별자, 클라이언트 식별자와 암호 또는 클라이언트 비밀을 기록합니다. 그런 다음, 이 식별자와 암호를 사용하여 애플리케이션을 구성합니다. 

몇 가지 일반적인 ID 공급자를 사용자 흐름에 추가하는 단계를 설명하는 문서는 다음과 같습니다.

- [Amazon](active-directory-b2c-setup-amzn-app.md)
- [Facebook](active-directory-b2c-setup-fb-app.md)
- [Microsoft 계정](active-directory-b2c-setup-msa-app.md)

몇 가지 일반적인 ID 공급자를 사용자 지정 정책에 추가하는 단계를 설명하는 문서는 다음과 같습니다.
- [Amazon](setup-amazon-custom.md)
- [Google](active-directory-b2c-custom-setup-goog-idp.md)
- [Microsoft 계정](active-directory-b2c-custom-setup-msa-idp.md)

자세한 내용은 [자습서: Azure Active Directory B2C의 애플리케이션에 ID 공급자 추가](tutorial-add-identity-providers.md)를 참조하세요.


## <a name="page-customization"></a>페이지 사용자 지정

사용자 경험에서 고객에게 표시되는 대부분의 HTML 및 CSS 콘텐츠는 제어할 수 있습니다. 페이지 사용자 지정을 사용하면 모든 사용자 지정 정책 또는 사용자 흐름의 모양과 느낌을 사용자 지정할 수 있습니다. 이 사용자 지정 기능을 통해 애플리케이션과 Azure AD B2C 간에 브랜드와 시각적 개체 일관성을 유지할 수 있습니다. 

Azure AD B2C는 사용자의 브라우저에서 코드를 실행하고, CORS(원본 간 리소스 공유)라고 하는 최신 방법을 사용합니다. 먼저, 사용자 지정된 HTML 콘텐츠가 있는 정책에서 URL을 지정합니다. Azure AD B2C는 URL에서 로드된 HTML 콘텐츠와 사용자 인터페이스 요소를 병합한 다음, 사용자에게 해당 페이지를 표시합니다.

쿼리 문자열에서 Azure AD B2C에 매개 변수를 보냅니다. 매개 변수를 HTML 엔드포인트로 전달하면 페이지 콘텐츠가 동적으로 변경됩니다. 예를 들어 웹 또는 모바일 애플리케이션에서 전달한 매개 변수에 따라 등록 또는 로그인 페이지의 배경 이미지를 변경합니다.

사용자 흐름의 페이지를 사용자 지정하려면 [자습서: Azure Active Directory B2C의 사용자 환경 인터페이스 사용자 지정](tutorial-customize-ui.md)을 참조하세요. 사용자 지정 정책의 페이지를 사용자 지정하려면 [Azure Active Directory B2C에서 사용자 지정 정책을 사용하여 애플리케이션의 사용자 인터페이스 사용자 지정](active-directory-b2c-ui-customization-custom.md) 또는 [Azure Active Directory B2C에서 사용자 지정 정책을 사용하여 동적 콘텐츠로 UI 구성](active-directory-b2c-ui-customization-custom-dynamic.md)을 참조하세요.

## <a name="developer-resources"></a>개발자 리소스

### <a name="client-applications"></a>클라이언트 애플리케이션

무엇보다도 [iOS](active-directory-b2c-devquickstarts-ios.md), [Android](active-directory-b2c-devquickstarts-android.md) 및 .NET용 애플리케이션을 선택할 수 있습니다. Azure AD B2C를 사용하면 사용자 ID를 보호하면서 이러한 작업을 수행할 수 있습니다.

ASP.NET 웹 애플리케이션 개발자인 경우 [자습서: 웹 애플리케이션이 Azure AD B2C를 사용하여 계정을 인증하도록 설정](active-directory-b2c-tutorials-web-app.md)의 단계를 사용하여 계정을 인증하도록 애플리케이션을 설정합니다.

데스크톱 애플리케이션 개발자인 경우 [자습서: 데스크톱 애플리케이션이 Azure AD B2C를 사용하여 계정을 인증하도록 설정](active-directory-b2c-tutorials-desktop-app.md)의 단계를 사용하여 계정을 인증하도록 애플리케이션을 설정합니다.

Node.js를 사용하는 단일 페이지 애플리케이션 개발자인 경우 [자습서: 단일 페이지 애플리케이션이 Azure AD B2C를 사용하여 계정을 인증하도록 설정](active-directory-b2c-tutorials-spa.md)의 단계를 사용하여 계정을 인증하도록 애플리케이션을 설정합니다.

### <a name="apis"></a>API
클라이언트 또는 웹 애플리케이션에서 API를 호출해야 하는 경우 Azure AD B2C에서 이러한 리소스에 대한 보안 액세스를 설정할 수 있습니다.

ASP.NET 웹 애플리케이션 개발자인 경우 [자습서: Azure Active Directory B2C를 사용하여 ASP.NET Web API로의 액세스 권한 부여](active-directory-b2c-tutorials-web-api.md)의 단계를 사용하여 보호된 API를 호출하도록 애플리케이션을 설정합니다.

데스크톱 애플리케이션 개발자인 경우 [자습서: Azure Active Directory B2C를 사용하여 데스크톱 앱에서 Node.js Web API에 대한 액세스 권한 부여](active-directory-b2c-tutorials-desktop-app-webapi.md)의 단계를 사용하여 보호된 API를 호출하도록 애플리케이션을 설정합니다.

Node.js를 사용한 단일 페이지 애플리케이션 개발자인 경우 [자습서: Azure Active Directory B2C를 사용하여 단일 페이지 애플리케이션에서 ASP.NET Core 웹 API로의 액세스 권한 부여](active-directory-b2c-tutorials-spa-webapi.md)의 단계를 사용하여 계정을 인증하도록 애플리케이션을 설정합니다.

### <a name="javascript"></a>JavaScript

Azure AD B2C에서 사용자 고유의 JavaScript 클라이언트 쪽 코드를 애플리케이션에 추가할 수 있습니다. 애플리케이션에서 JavaScript를 설정하려면 사용자 흐름 또는 사용자 지정 정책에서 [페이지 계약](page-contract.md)을 정의하고 [JavaScript](javascript-samples.md)를 사용하도록 설정합니다.

### <a name="user-accounts"></a>사용자 계정

많은 일반 테넌트 관리 작업을 프로그래밍 방식으로 수행해야 합니다. 주요 예제는 사용자 관리입니다. 기존 사용자 저장소를 Azure AD B2C 테넌트로 마이그레이션해야 할 수도 있습니다. 고유한 페이지에서 사용자 등록을 호스팅하고 백그라운드에서 Azure AD B2C 디렉터리에 사용자 계정을 만들려고 할 수 있습니다. 이러한 형식의 태스크는 사용자 계정을 만들고 읽고 업데이트 및 삭제하는 기능이 필요합니다. 이러한 작업은 [Azure AD Graph API](active-directory-b2c-devquickstarts-graph-dotnet.md)를 사용하여 수행할 수 있습니다.

## <a name="next-steps"></a>다음 단계

자습서를 계속해 나가면서 가입 및 로그인 환경에 대한 애플리케이션 구성을 시작해 봅니다.

> [!div class="nextstepaction"]
> [자습서: Azure Active Directory B2C 테넌트 만들기](tutorial-create-tenant.md)
