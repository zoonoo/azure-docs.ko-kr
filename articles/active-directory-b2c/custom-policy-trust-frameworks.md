---
title: 사용자 지정 정책 개요 Azure AD B2C | Microsoft Docs
description: Azure Active Directory B2C 사용자 지정 정책 및 ID 환경 프레임워크에 대한 항목입니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: cb33e11af26d5f5a2676f5b236ac142179bdb550
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99592843"
---
# <a name="azure-ad-b2c-custom-policy-overview"></a>Azure AD B2C 사용자 지정 정책 개요

사용자 지정 정책은 Azure AD B2C(Azure Active Directory B2C) 테넌트의 동작을 정의하는 구성 파일입니다. [사용자 흐름](user-flow-overview.md) 은 가장 일반적인 id 작업에 대해 Azure AD B2C 포털에 미리 정의 되어 있지만 다양 한 작업을 완료 하기 위해 id 개발자가 사용자 지정 정책을 완전히 편집할 수 있습니다.

사용자 지정 정책은 완전히 구성 가능 하며 정책 기반입니다. 사용자 지정 정책은 Openid connect Connect, OAuth, SAML와 같은 표준 프로토콜 형식 및 REST API 기반 시스템 간 클레임 교환 등의 몇 가지 비표준 항목 간에 트러스트를 오케스트레이션 합니다. 프레임 워크는 사용자에 게 친숙 한 흰색의 레이블이 지정 된 환경을 만듭니다.

사용자 지정 정책은 계층적 체인에서 서로를 참조하는 하나 이상의 XML 형식 파일로 표시됩니다. XML 요소는 빌딩 블록, 사용자와의 상호 작용, 기타 당사자 및 비즈니스 논리를 정의 합니다. 

## <a name="custom-policy-starter-pack"></a>사용자 지정 정책 시작 팩

사용자 지정 정책 [시작 팩](custom-policy-get-started.md#get-the-starter-pack) 은 신속 하 게 진행 하기 위해 몇 가지 미리 작성 된 정책과 함께 제공 됩니다. Azure AD B2C 이러한 각 시작 팩 각각에는 설명한 시나리오를 수행하는 데 필요한 최소한의 기술 프로필 및 사용자 경험이 포함되어 있습니다.

- **LocalAccounts** - 로컬 계정만 사용할 수 있습니다.
- **SocialAccounts** - 소셜(또는 페더레이션) 계정만 사용할 수 있습니다.
- **SocialAndLocalAccounts** - 로컬 계정과 소셜 계정을 모두 사용할 수 있습니다. 대부분의 샘플은이 정책을 참조 합니다.
- **SocialAndLocalAccountsWithMFA** - 소셜, 로컬 및 다단계 인증 옵션을 사용할 수 있습니다.

## <a name="understanding-the-basics"></a>기본 사항 이해 

### <a name="claims"></a>클레임

클레임은 Azure AD B2C 정책 실행 중에 데이터의 임시 스토리지를 제공합니다. 이름, 성, 사용자 또는 다른 시스템에서 가져온 기타 클레임 (클레임 교환)과 같은 사용자에 대 한 정보를 저장할 수 있습니다. [클레임 스키마](claimsschema.md)에서 클레임을 선언합니다. 

정책이 실행 될 때 Azure AD B2C는 내부 및 외부 당사자와의 클레임을 보내고 받으며 이러한 클레임의 하위 집합을 신뢰 당사자 응용 프로그램에 토큰의 일부로 보냅니다. 클레임은 다음과 같은 방법으로 사용 됩니다. 

- 디렉터리 사용자 개체에 대해 클레임이 저장, 읽기 또는 업데이트 됩니다.
- 외부 id 공급자 로부터 클레임을 수신 합니다.
- 클레임은 사용자 지정 REST API 서비스를 사용 하 여 보내거나 받습니다.
- 등록 또는 프로필 흐름 편집 중에 사용자의 클레임으로 데이터가 수집 됩니다.

### <a name="manipulating-your-claims"></a>클레임 조작

[클레임 변환은](claimstransformations.md) 지정 된 클레임을 다른 클레임으로 변환 하거나 클레임을 평가 하거나 클레임 값을 설정 하는 데 사용할 수 있는 미리 정의 된 기능입니다. 예를 들어 문자열 컬렉션에 항목을 추가 하거나, 문자열의 대/소문자를 변경 하거나, 날짜 및 시간 클레임을 평가 합니다. 클레임 변환은 transform 메서드를 지정 합니다. 

### <a name="customize-and-localize-your-ui"></a>UI 사용자 지정 및 지역화

웹 브라우저에 페이지를 표시 하 여 사용자 로부터 정보를 수집 하려면 [자체 어설션된 기술 프로필](self-asserted-technical-profile.md)을 사용 합니다. 자체 어설션된 기술 프로필을 편집 하 여 클레임을 [추가 하 고 사용자 입력](./configure-user-input.md)을 사용자 지정할 수 있습니다.

자체 어설션된 기술 프로필에 대 한 [사용자 인터페이스를 사용자](customize-ui-with-html.md) 지정 하려면 사용자 지정 된 HTML 콘텐츠가 있는 [콘텐츠 정의](contentdefinitions.md) 요소에 URL을 지정 합니다. 자체 어설션된 기술 프로필에서는이 콘텐츠 정의 ID를 가리킵니다.

언어별 문자열을 사용자 지정 하려면 [지역화](localization.md) 요소를 사용 합니다. 콘텐츠 정의에는 로드할 지역화 된 리소스 목록을 지정 하는 [지역화](localization.md) 참조가 포함 될 수 있습니다. Azure AD B2C는 URL에서 로드된 HTML 콘텐츠와 사용자 인터페이스 요소를 병합한 다음, 사용자에게 해당 페이지를 표시합니다. 

## <a name="relying-party-policy-overview"></a>신뢰 당사자 정책 개요

SAML 프로토콜에서 신뢰 당사자 응용 프로그램을 서비스 공급자 라고 하 고, [신뢰 당사자 정책을](relyingparty.md) 호출 하 여 특정 사용자 경험을 실행 합니다. 신뢰 당사자 정책은 실행할 사용자 경험 및 토큰에 포함 된 클레임 목록을 지정 합니다. 

![정책 실행 흐름을 보여 주는 다이어그램](./media/custom-policy-trust-frameworks/custom-policy-execution.png)

동일한 정책을 사용 하는 모든 신뢰 당사자 응용 프로그램은 동일한 토큰 클레임을 수신 하 고 사용자는 동일한 사용자 경험을 거칩니다.

### <a name="user-journeys"></a>사용자 경험

[사용자 경험](userjourneys.md) 를 사용 하 여 응용 프로그램에 대 한 액세스 권한을 얻기 위해 사용자가 따라야 하는 경로를 사용 하 여 비즈니스 논리를 정의할 수 있습니다. 사용자는 응용 프로그램에 제공 되는 클레임을 검색 하기 위해 사용자 경험을 통해 수행 됩니다. 사용자 경험은 [오케스트레이션 단계](userjourneys.md#orchestrationsteps)시퀀스에서 빌드됩니다. 사용자는 토큰을 획득 하기 위한 마지막 단계에 도달 해야 합니다. 

다음 지침에서는 [소셜 및 로컬 계정 시작 팩](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccounts) 정책에 오케스트레이션 단계를 추가 하는 방법에 대해 설명 합니다. 다음은 추가 된 REST API 호출의 예제입니다.

![사용자 지정 된 사용자 경험](media/custom-policy-trust-frameworks/user-journey-flow.png)


### <a name="orchestration-steps"></a>오케스트레이션 단계

오케스트레이션 단계는 의도 한 용도 또는 기능을 구현 하는 메서드에 대 한 참조를 제공 합니다. 이 방법을 [기술 프로필](technicalprofiles.md)이라고 합니다. 비즈니스 논리를 더 잘 나타내도록 사용자 경험을 전환 해야 하는 경우 오케스트레이션 단계는 [하위](subjourneys.md)경험을 참조 합니다. 하위 경험에는 자체 오케스트레이션 단계 집합이 포함 되어 있습니다.

사용자는 토큰을 획득 하기 위해 사용자 경험의 마지막 오케스트레이션 단계에 도달 해야 합니다. 그러나 사용자가 모든 오케스트레이션 단계를 통과할 필요는 없습니다. 오케스트레이션 단계에 정의 된 [전제 조건](userjourneys.md#preconditions) 에 따라 오케스트레이션 단계를 조건부로 실행할 수 있습니다. 

오케스트레이션 단계가 완료 된 후 Azure AD B2C는 **클레임 모음** 에 출력 된 클레임을 저장 합니다. 클레임 모음의 클레임은 사용자 경험의 추가 오케스트레이션 단계에서 활용 될 수 있습니다.

다음 다이어그램에서는 사용자 경험의 오케스트레이션 단계가 클레임 모음에 액세스할 수 있는 방법을 보여 줍니다.

![사용자 경험 Azure AD B2C](media/custom-policy-trust-frameworks/user-journey-diagram.png)

### <a name="technical-profile"></a>기술 프로필

기술 프로필은 여러 종류의 당사자와 통신 하는 인터페이스를 제공 합니다. 사용자 경험에서는 오케스트레이션 단계를 통해 호출 하는 기술 프로필을 결합 하 여 비즈니스 논리를 정의 합니다.

모든 유형의 기술 프로필은 동일한 개념을 공유합니다. 입력 클레임을 보내고 클레임 변환을 실행 하 고 구성 된 파티와 통신 합니다. 프로세스가 완료 된 후 기술 프로필은 클레임 모음에 대 한 출력 클레임을 반환 합니다. 자세한 내용은 [기술 프로필 개요](technicalprofiles.md)를 참조 하세요.

### <a name="validation-technical-profile"></a>유효성 검사 기술 프로필

사용자가 사용자 인터페이스와 상호 작용 하는 경우 수집 된 데이터의 유효성을 검사 하는 것이 좋습니다. 사용자와 상호 작용 하려면 [자체 어설션된 기술 프로필](self-asserted-technical-profile.md) 을 사용 해야 합니다.

사용자 입력의 유효성을 검사 하기 위해 자체 어설션된 기술 프로필에서 [유효성 검사 기술 프로필](validation-technical-profile.md) 을 호출 합니다. 유효성 검사 기술 프로필은 비 대화형 기술 프로필을 호출 하는 방법입니다. 이 경우 기술 프로필은 출력 클레임 또는 오류 메시지를 반환할 수 있습니다. 오류 메시지는 화면에 사용자에 게 렌더링 되어 사용자가 다시 시도할 수 있습니다.

다음 다이어그램에서는 Azure AD B2C 유효성 검사 기술 프로필을 사용 하 여 사용자 자격 증명의 유효성을 검사 하는 방법을 보여 줍니다.

![유효성 검사 기술 프로필 다이어그램](media/custom-policy-trust-frameworks/validation-technical-profile.png)

## <a name="inheritance-model"></a>상속 모델

각 스타터 팩에는 다음 파일이 포함 되어 있습니다.

- **기본 파일** - 대부분의 정의를 포함합니다. 정책에 대 한 문제 해결 및 장기적인 유지 관리에 도움이 되도록이 파일에 대 한 변경 내용 수를 최소화 합니다.
- 테 넌 트에 대 한 고유한 구성 변경 내용이 포함 된 **확장** 파일입니다. 이 정책 파일은 기본 파일에서 파생됩니다. 이 파일을 사용하여 새 기능을 추가하거나 기존 기능을 재정의할 수 있습니다. 예를 들어 이 파일을 사용하여 새 ID 공급자와 페더레이션합니다.
- **RP(신뢰 당사자)** 파일 - 웹, 모바일 또는 데스크톱 애플리케이션과 같은 신뢰 당사자 애플리케이션이 직접 호출하는 단일 작업 중심 파일입니다. 등록, 로그인, 암호 재설정 또는 프로필 편집 등의 각 고유 작업에는 자체 신뢰 당사자 정책 파일이 필요 합니다. 이 정책 파일은 확장 파일에서 파생 됩니다.

상속 모델은 다음과 같습니다.

- 모든 수준의 자식 정책은 부모 정책에서 상속하고 새 요소를 추가하여 확장할 수 있습니다.
- 더 복잡 한 시나리오의 경우 더 많은 상속 수준을 추가할 수 있습니다 (총 10 개).
- 신뢰 당사자 정책을 더 추가할 수 있습니다. 예를 들어 내 계정 삭제, 전화 번호, SAML 신뢰 당사자 정책 등을 변경 합니다.

다음 다이어그램은 정책 파일과 신뢰 당사자 애플리케이션 간의 관계를 보여 줍니다.

![보안 프레임 워크 정책 상속 모델을 보여 주는 다이어그램](media/custom-policy-trust-frameworks/policies.png)


## <a name="guidance-and-best-practices"></a>지침 및 모범 사례

### <a name="best-practices"></a>모범 사례

Azure AD B2C 사용자 지정 정책 내에서 고유한 비즈니스 논리를 통합 하 여 필요한 사용자 환경을 구축 하 고 서비스의 기능을 확장할 수 있습니다. 시작 하기 위한 모범 사례 및 권장 사항 집합이 있습니다.

- **확장 정책** 또는 **신뢰 당사자 정책** 내에서 논리를 만듭니다. 동일한 ID를 참조 하 여 기본 정책을 재정의 하는 새 요소를 추가할 수 있습니다. 이렇게 하면 나중에 Microsoft에서 새로운 시작 팩을 릴리스할 때 기본 정책을 더 쉽게 업그레이드할 수 있는 동시에 프로젝트를 확장할 수 있습니다.
- **기본 정책** 내에서 변경 하는 것을 방지 하는 것이 좋습니다. 필요한 경우 변경 내용이 적용 되는 위치를 설명 합니다.
- 기술 프로필 메타 데이터와 같은 요소를 재정의 하는 경우에는 기본 정책에서 전체 기술 프로필을 복사 하지 않습니다. 대신 요소의 필수 섹션만 복사 합니다. 변경을 수행 하는 방법에 대 한 예제는 [메일 확인 사용 안 함](./disable-email-verification.md) 을 참조 하세요.
- 핵심 기능이 공유 되는 기술 프로필의 중복을 줄이려면 [기술 프로필 포함](technicalprofiles.md#include-technical-profile)을 사용 합니다.
- 로그인 하는 동안 Azure AD 디렉터리에 쓰지 말고 제한 문제가 발생할 수 있습니다.
- 정책에 REST Api와 같은 외부 종속성이 있는 경우 항상 사용 가능 해야 합니다.
- 사용자 환경을 개선 하려면 사용자 지정 HTML 템플릿이 [온라인 콘텐츠 배달을](../cdn/index.yml)사용 하 여 전역적으로 배포 되었는지 확인 합니다. CDN (Azure Content Delivery Network)을 사용 하면 로드 시간을 줄이고, 대역폭을 절약 하 고, 응답 속도를 향상 시킬 수 있습니다.
- 사용자 경험을 변경 하려는 경우 기본 정책에서 확장 정책으로 전체 사용자 경험을 복사 합니다. 복사한 사용자 경험에 고유한 사용자 경험 ID를 제공 합니다. 그런 다음 [신뢰 당사자 정책](relyingparty.md)에서 [기본 사용자](relyingparty.md#defaultuserjourney) 경험 요소를 변경 하 여 새로운 사용자 경험을 가리키도록 합니다.

## <a name="troubleshooting"></a>문제 해결

Azure AD B2C 정책을 사용 하 여 개발할 때 사용자 경험을 실행 하는 동안 오류나 예외가 발생할 수 있습니다. Application Insights를 사용 하 여을 조사할 수 있습니다.

- Azure AD B2C와 Application Insights를 통합 하 여 [예외를 진단](troubleshoot-with-application-insights.md)합니다.
- [Visual Studio Code에 대 한 Azure AD B2C 확장](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c) 은 정책 이름 및 시간에 따라 [로그에](https://github.com/azure-ad-b2c/vscode-extension/blob/master/src/help/app-insights.md) 액세스 하 고 시각화 하는 데 도움이 될 수 있습니다.
- 사용자 지정 정책 설정에서 가장 일반적인 오류는 형식이 잘못 지정된 XML입니다. Xml 파일을 업로드 하기 전에 [xml 스키마 유효성 검사](troubleshoot-custom-policies.md) 를 사용 하 여 오류를 식별 합니다.

## <a name="continuous-integration"></a>연속 통합

Azure Pipelines에서 설정 하는 CI/CD (지속적인 통합 및 배달) 파이프라인을 사용 하 여 소프트웨어 제공 및 코드 제어 자동화 [에 Azure AD B2C 사용자 지정 정책을 포함할](deploy-custom-policies-devops.md) 수 있습니다. 개발, 테스트 및 프로덕션과 같은 다양 한 Azure AD B2C 환경에 배포 하는 경우 Azure Pipelines를 사용 하 여 수동 프로세스를 제거 하 고 자동화 된 테스트를 수행 하는 것이 좋습니다.

## <a name="prepare-your-environment"></a>환경 준비

Azure AD B2C 사용자 지정 정책을 시작 합니다.

1. [Azure AD B2C 테넌트 만들기](tutorial-create-tenant.md)
1. Azure Portal를 사용 하 여 [웹 응용 프로그램을 등록](tutorial-register-applications.md) 하면 정책을 테스트할 수 있습니다.
1. 필요한 [정책 키](custom-policy-get-started.md#add-signing-and-encryption-keys) 를 추가 하 고 [Id 경험 프레임 워크 응용 프로그램을 등록](custom-policy-get-started.md#register-identity-experience-framework-applications)합니다.
1. [Azure AD B2C 정책 시작 팩을 가져오고](custom-policy-get-started.md#get-the-starter-pack) 테 넌 트에 업로드 합니다. 
1. 시작 팩을 업로드 한 후 [등록 또는 로그인 정책을 테스트](custom-policy-get-started.md#test-the-custom-policy)합니다.
1. [Visual Studio Code](https://code.visualstudio.com/) (VS Code)를 다운로드 하 여 설치 하는 것이 좋습니다. Visual Studio Code은 데스크톱에서 실행 되며 Windows, macOS 및 Linux에 사용할 수 있는 간단 하지만 강력한 소스 코드 편집기입니다. VS Code를 사용 하 여 [에 대 한 Azure AD B2C 확장](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c) 을 설치 하 여 Azure AD B2C 사용자 지정 정책 XML 파일을 신속 하 게 탐색 하 고 편집할 수 있습니다 VS Code
 
## <a name="next-steps"></a>다음 단계

Azure AD B2C 정책을 설정 하 고 테스트 한 후에는 정책 사용자 지정을 시작할 수 있습니다. 다음 문서를 통해 다음을 수행 하는 방법을 알아보세요.

- 사용자 지정 정책을 사용 하 여 [클레임을 추가 하 고 사용자 입력을 사용자 지정](./configure-user-input.md) 합니다. 일부 시작 팩 기술 프로필을 사용자 지정 하 여 클레임을 정의 하 고 사용자 인터페이스에 클레임을 추가 하는 방법에 대해 알아봅니다.
- 사용자 지정 정책을 사용 하 여 응용 프로그램의 [사용자 인터페이스를 사용자 지정](customize-ui-with-html.md) 합니다. 고유한 HTML 콘텐츠를 만들고 콘텐츠 정의를 사용자 지정 하는 방법을 알아봅니다.
- 사용자 지정 정책을 사용 하 여 응용 프로그램의 [사용자 인터페이스를 지역화](./language-customization.md) 합니다. 지역화 된 리소스 요소를 추가 하 여 지원 되는 언어 목록을 설정 하 고 언어별 레이블을 제공 하는 방법에 대해 알아봅니다.
- 정책 개발 및 테스트 중에 [전자 메일 확인을 사용 하지 않도록 설정할](./disable-email-verification.md)수 있습니다. 기술 프로필 메타 데이터를 덮어쓰는 방법에 대해 알아봅니다.
- 사용자 지정 정책을 사용 하 여 [Google 계정으로 로그인을 설정](./identity-provider-google.md) 합니다. OAuth2 기술 프로필을 사용 하 여 새 클레임 공급자를 만드는 방법에 대해 알아봅니다. 그런 다음 Google 로그인 옵션을 포함 하도록 사용자 경험을 사용자 지정 합니다.
- 사용자 지정 정책을 사용 하 여 문제를 진단 하려면 [Application Insights를 사용 하 여 Azure Active Directory B2C 로그를 수집할](troubleshoot-with-application-insights.md)수 있습니다. 새 기술 프로필을 추가 하 고 신뢰 당사자 정책을 구성 하는 방법을 알아봅니다.
