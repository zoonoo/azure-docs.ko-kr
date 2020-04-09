---
title: Azure AD 앱 SAML 토큰 클레임 사용자 지정
titleSuffix: Microsoft identity platform
description: Azure AD의 엔터프라이즈 애플리케이션에 대한 SAML 토큰에 발급된 클레임을 사용자 지정하는 방법을 알아보세요.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: article
ms.date: 10/22/2019
ms.author: ryanwi
ms.reviewer: luleon, paulgarn, jeedes
ms.custom: aaddev
ms.openlocfilehash: 87a9632ec2433b8698e3ae3761ba733aa6bc63a5
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885687"
---
# <a name="how-to-customize-claims-issued-in-the-saml-token-for-enterprise-applications"></a>방법: 엔터프라이즈 응용 프로그램에 대한 SAML 토큰에서 발행된 클레임 사용자 지정

현재 Azure Active Directory(Azure AD)는 Azure AD 앱 갤러리에 미리 통합된 응용 프로그램과 사용자 지정 응용 프로그램을 포함하여 대부분의 엔터프라이즈 응용 프로그램에서 단일 사인온(SSO)을 지원합니다. 사용자가 SAML 2.0 프로토콜을 사용하여 Azure AD를 통해 애플리케이션을 인증하면 Azure AD는 (HTTP POST를 통해) 애플리케이션에 토큰을 보냅니다. 그런 다음 애플리케이션이 토큰의 유효성을 검사하고 사용하여 사용자 이름과 암호를 묻는 대신 사용자를 로그인합니다. 이러한 SAML 토큰에는 *클레임이라고*하는 사용자에 대한 정보가 포함되어 있습니다.

‘클레임’은 해당 사용자에 대해 발급하는 토큰 내에서 ID 공급자가 사용자에 대해 나타내는 정보입니다.** [SAML 토큰 ](https://en.wikipedia.org/wiki/SAML_2.0)에서 이러한 데이터는 일반적으로 SAML 특성 문에 포함됩니다. 사용자 고유의 ID는 대개 이름 식별자라고도 하는 SAML Subject에 나타납니다.

기본적으로 Azure AD는 사용자를 고유하게 식별할 수 `NameIdentifier` 있는 Azure AD에서 사용자 이름(사용자 주체 이름이라고도 함)의 값을 포함하는 클레임을 포함하는 SAML 토큰을 응용 프로그램에 발급합니다. 또한 SAML 토큰에는 사용자의 메일 주소, 이름 및 성을 포함하는 추가 클레임이 포함됩니다.

SAML 토큰이 애플리케이션에 발급한 클레임을 보거나 편집하려면 Azure Portal에서 애플리케이션을 엽니다. 그런 다음 **사용자 속성 & 클레임** 섹션을 엽니다.

![Azure 포털에서 사용자 속성 & 클레임 섹션 열기](./media/active-directory-saml-claims-customization/sso-saml-user-attributes-claims.png)

SAML 토큰에 발급된 클레임을 편집해야 할만한 두 가지 이유는 다음과 같습니다.

* 응용 프로그램에서 `NameIdentifier` 또는 NameID 클레임Azure AD에 저장된 사용자 이름(또는 사용자 주체 이름)이 아닌 다른 클레임이어야 합니다.
* 애플리케이션이 다른 클레임 URI 또는 클레임 값 집합을 요구하도록 작성되었습니다.

## <a name="editing-nameid"></a>네임ID 편집

NameID(이름 식별자 값)를 편집하려면 다음을 변경합니다.

1. 이름 **식별자 값** 페이지를 엽니다.
1. 특성에 적용할 특성 또는 변환을 선택합니다. 선택적으로 NameID 클레임을 갖도록 형식을 지정할 수 있습니다.

   ![NameID(이름 식별자) 값 편집](./media/active-directory-saml-claims-customization/saml-sso-manage-user-claims.png)

### <a name="nameid-format"></a>NameID 형식

SAML 요청에 특정 형식의 Element NameIDPolicy가 포함된 경우 Azure AD는 요청의 형식을 존중합니다.

SAML 요청에 NameIDPolicy에 대한 요소가 포함되어 있지 않으면 Azure AD는 지정한 형식으로 NameID를 발급합니다. 형식이 지정되지 않은 경우 Azure AD는 선택한 클레임 원본과 연결된 기본 원본 형식을 사용합니다.

이름 **식별자 형식 드롭다운 선택에서** 다음 옵션 중 하나를 선택할 수 있습니다.

| NameID 형식 | Description |
|---------------|-------------|
| **기본** | Azure AD는 기본 소스 형식을 사용합니다. |
| **영구적** | Azure AD는 영구를 NameID 형식으로 사용합니다. |
| **EmailAddress** | Azure AD는 EmailAddress를 NameID 형식으로 사용합니다. |
| **불특정** | Azure AD는 NameID 형식으로 지정되지 않음을 사용합니다. |
| **Windows 도메인 정규화된 이름** | Azure AD는 WindowsDomainQualifiedNameNameName을 NameID 형식으로 사용합니다. |

Transient NameID도 지원되지만 드롭다운에서 사용할 수 없으며 Azure 측에서 구성할 수 없습니다. NameIDPolicy 특성에 대한 자세한 내용은 [단일 사인온 SAML 프로토콜을](single-sign-on-saml-protocol.md)참조하십시오.

### <a name="attributes"></a>특성

`NameIdentifier`(또는 NameID) 클레임의 원하는 소스를 선택합니다. 다음 옵션 중에서 선택할 수 있습니다.

| 속성 | Description |
|------|-------------|
| Email | 사용자의 이메일 주소입니다. |
| userprincipalName | 사용자의 UPN(사용자 주체 이름) |
| onpremisessamaccount | 온-프레미스 Azure AD에서 동기화된 SAM 계정 이름입니다. |
| objectid | Azure AD에서 사용자의 개체id |
| employeeid | 사용자의 직원 ID |
| 디렉터리 확장 | [Azure AD Connect 동기화를 사용하여 온-프레미스 Active Directory에서 동기화되는](../hybrid/how-to-connect-sync-feature-directory-extensions.md) 디렉터리 확장입니다. |
| 확장 특성 1-15 | Azure AD 스키마를 확장하는 데 사용되는 온-프레미스 확장 특성입니다. |

자세한 내용은 [표 3: 소스당 유효한 ID 값을](active-directory-claims-mapping.md#table-3-valid-id-values-per-source)참조하십시오.

Azure AD에서 정의하는 모든 클레임에 상수(정적) 값을 할당할 수도 있습니다. 상수 값을 할당하려면 아래 단계를 따르십시오.

1. Azure [포털에서](https://portal.azure.com/)사용자 **속성 & 클레임** 섹션에서 **편집** 아이콘을 클릭하여 클레임을 편집합니다.

1. 수정하려는 필수 클레임을 클릭합니다.

1. 조직에 따라 **소스 특성에** 따옴표 없이 상수 값을 입력 하고 **저장을**클릭합니다.

    ![Azure 포털에서 사용자 속성 & 클레임 섹션 열기](./media/active-directory-saml-claims-customization/organization-attribute.png)

1. 상수 값은 아래와 같이 표시됩니다.

    ![Azure 포털에서 사용자 속성 & 클레임 섹션 열기](./media/active-directory-saml-claims-customization/edit-attributes-claims.png)

### <a name="special-claims---transformations"></a>특별 클레임 - 변환

클레임 변환 함수를 사용할 수도 있습니다.

| 함수 | Description |
|----------|-------------|
| **ExtractMailPrefix()** | 이메일 주소 또는 사용자 주 체이름에서 도메인 접미사를 제거합니다. 그러면 전달되는 사용자 이름의 첫 부분만 추출됩니다(예: joe_smith@contoso.com 대신 "joe_smith"). |
| **조인()** | 확인된 도메인에 특성을 조인합니다. 선택한 사용자 식별자 값에 도메인이 있으면 사용자 이름을 추출하여 선택한 확인된 도메인을 추가합니다. 예를 들어, 사용자 식별자 값으로 이메일(joe_smith@contoso.com)을 선택하고 확인된 도메인으로 contoso.onmicrosoft.com을 선택하면 joe_smith@contoso.onmicrosoft.com이 됩니다. |
| **토로어()** | 선택한 특성의 문자를 소문자로 변환합니다. |
| **토어퍼()** | 선택한 특성의 문자를 대문자로 변환합니다. |

## <a name="adding-application-specific-claims"></a>응용 프로그램별 클레임 추가

응용 프로그램별 클레임을 추가하려면 다음을 수행합니다.

1. **사용자 속성 & 클레임에서**새 클레임 **추가를** 선택하여 사용자 클레임 관리 페이지를 **엽니다.**
1. 클레임의 **이름을** 입력합니다. SAML 사양에 따라 URI 패턴을 따를 필요는 없습니다. URI 패턴이 필요한 경우 **네임스페이스** 필드에 넣을 수 있습니다.
1. 클레임이 해당 값을 검색할 **소스를** 선택합니다. 소스 특성 드롭다운에서 사용자 특성을 선택하거나 사용자 특성에 변환을 적용한 다음 클레임으로 내보내도 됩니다.

### <a name="claim-transformations"></a>클레임 변환

사용자 특성에 변환을 적용하려면 다음을 수행합니다.

1. **클레임 관리에서** *변환을* 클레임 소스로 선택하여 **변환 관리** 페이지를 엽니다.
2. 변환 드롭다운에서 함수를 선택합니다. 선택한 함수에 따라 변환에서 평가할 매개 변수와 상수 값을 제공해야 합니다. 사용 가능한 기능에 대한 자세한 내용은 아래 표를 참조하십시오.
3. 여러 변환을 적용하려면 **변환 추가를**클릭합니다. 클레임에 최대 두 개의 변환을 적용할 수 있습니다. 예를 들어 먼저 의 전자 메일 접두사를 추출할 수 있습니다. `user.mail` 그런 다음 문자열 대문자를 만듭니다.

   ![NameID(이름 식별자) 값 편집](./media/active-directory-saml-claims-customization/sso-saml-multiple-claims-transformation.png)

다음 함수를 사용하여 클레임을 변환할 수 있습니다.

| 함수 | Description |
|----------|-------------|
| **ExtractMailPrefix()** | 이메일 주소 또는 사용자 주 체이름에서 도메인 접미사를 제거합니다. 그러면 전달되는 사용자 이름의 첫 부분만 추출됩니다(예: joe_smith@contoso.com 대신 "joe_smith"). |
| **조인()** | 두 특성을 결합하여 새 값을 만듭니다. 선택적으로 두 특성 간에 구분 기호를 사용할 수 있습니다. NameID 클레임 변환의 경우 조인은 확인된 도메인으로 제한됩니다. 선택한 사용자 식별자 값에 도메인이 있으면 사용자 이름을 추출하여 선택한 확인된 도메인을 추가합니다. 예를 들어, 사용자 식별자 값으로 이메일(joe_smith@contoso.com)을 선택하고 확인된 도메인으로 contoso.onmicrosoft.com을 선택하면 joe_smith@contoso.onmicrosoft.com이 됩니다. |
| **토로어()** | 선택한 특성의 문자를 소문자로 변환합니다. |
| **토어퍼()** | 선택한 특성의 문자를 대문자로 변환합니다. |
| **포함()** | 입력이 지정된 값과 일치하는 경우 특성 또는 상수를 출력합니다. 그렇지 않으면 일치하는 출력이 없는 경우 다른 출력을 지정할 수 있습니다.<br/>예를 들어 도메인을 포함하는 경우 값이 사용자의 전자 메일 주소인 클레임을 내보내려면 ""@contoso.com그렇지 않으면 사용자 주체 이름을 출력하려고 합니다. 이렇게 하려면 다음 값을 구성합니다.<br/>*매개 변수 1(입력)*: user.email<br/>*값*:@contoso.com" "<br/>매개 변수 2(출력): user.email<br/>매개 변수 3(일치가 없는 경우 출력): user.userprincipalname |
| **끝위()** | 입력이 지정된 값으로 끝나는 경우 특성 또는 상수를 출력합니다. 그렇지 않으면 일치하는 출력이 없는 경우 다른 출력을 지정할 수 있습니다.<br/>예를 들어 직원 ID가 "000"으로 끝나는 경우 값이 사용자의 직원 ID인 클레임을 내보하려는 경우 확장 특성을 출력하려고 합니다. 이렇게 하려면 다음 값을 구성합니다.<br/>*매개 변수 1(입력)*: user.employeeid<br/>*값*: "000"<br/>매개 변수 2(출력): user.employeeid<br/>매개 변수 3(일치항목이 없는 경우 출력): user.extensionattribute1 |
| **시작시작()** | 입력이 지정된 값으로 시작하는 경우 특성 또는 상수를 출력합니다. 그렇지 않으면 일치하는 출력이 없는 경우 다른 출력을 지정할 수 있습니다.<br/>예를 들어 국가/지역이 "US"로 시작하는 경우 값이 사용자의 직원 ID인 클레임을 내보하려는 경우 확장 특성을 출력하려는 경우 이렇게 하려면 다음 값을 구성합니다.<br/>*매개 변수 1(입력)*: user.country<br/>*값*: "미국"<br/>매개 변수 2(출력): user.employeeid<br/>매개 변수 3(일치항목이 없는 경우 출력): user.extensionattribute1 |
| **추출() - 일치 후** | 지정된 값과 일치하면 하위 문자열을 반환합니다.<br/>예를 들어 입력 값이 "Finance_BSimon"인 경우 일치 값은 "Finance_"이며 클레임의 출력은 "BSimon"입니다. |
| **추출() - 일치하기 전에** | 지정된 값과 일치할 때까지 하위 문자열을 반환합니다.<br/>예를 들어 입력 값이 "BSimon_US"인 경우 일치 값은 "_US"이며 클레임의 출력은 "BSimon"입니다. |
| **추출() - 일치 사이** | 지정된 값과 일치할 때까지 하위 문자열을 반환합니다.<br/>예를 들어 입력 값이 "Finance_BSimon_US"인 경우 첫 번째 일치 값은 "Finance_", 두 번째 일치 값은 "_US", 클레임의 출력은 "BSimon"입니다. |
| **추출알파() - 접두사** | 문자열의 접두사 알파벳 부분을 반환합니다.<br/>예를 들어 입력 값이 "BSimon_123"이면 "BSimon"을 반환합니다. |
| **추출물알파() - 접미사** | 문자열의 접미사 알파벳 부분을 반환합니다.<br/>예를 들어 입력 값이 "123_Simon"이면 "Simon"을 반환합니다. |
| **ExtractNumeric() - 접두사** | 문자열의 접두사 숫자 부분을 반환합니다.<br/>예를 들어 입력 값이 "123_BSimon"이면 "123"을 반환합니다. |
| **추출물숫자() - 접미사** | 문자열의 접미사 숫자 부분을 반환합니다.<br/>예를 들어 입력 값이 "BSimon_123"이면 "123"을 반환합니다. |
| **IfEmpty()** | 입력이 null이거나 비어 있는 경우 특성 또는 상수를 출력합니다.<br/>예를 들어 지정된 사용자의 직원 ID가 비어 있는 경우 extensionattribute에 저장된 특성을 출력하려는 경우 이렇게 하려면 다음 값을 구성합니다.<br/>매개 변수 1(입력): user.employeeid<br/>매개 변수 2(출력): user.extensionattribute1<br/>매개 변수 3(일치하는 항목이 없는 경우 출력): user.employeeid |
| **IfNotEmpty()** | 입력이 null 또는 비어 있지 않은 경우 특성 또는 상수를 출력합니다.<br/>예를 들어 지정된 사용자의 직원 ID가 비어 있지 않은 경우 extensionattribute에 저장된 특성을 출력하려는 경우입니다. 이렇게 하려면 다음 값을 구성합니다.<br/>매개 변수 1(입력): user.employeeid<br/>매개 변수 2(출력): user.extensionattribute1 |

추가 변환이 필요한 경우 *SaaS 응용 프로그램* 범주 아래 [Azure AD의 피드백 포럼에](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=160599) 아이디어를 제출합니다.

## <a name="emitting-claims-based-on-conditions"></a>조건에 따라 클레임 내보낸다

사용자 유형과 사용자가 속한 그룹에 따라 클레임의 소스를 지정할 수 있습니다. 

사용자 유형은 다음과 같은 것입니다.
- **모든**: 모든 사용자가 응용 프로그램에 액세스 할 수 있습니다.
- **구성원**: 테넌트의 네이티브 멤버
- **모든 게스트**: 사용자는 Azure AD유무에 관계없이 외부 조직에서 이월됩니다.
- **AAD 게스트**: 게스트 사용자는 Azure AD를 사용하여 다른 조직에 속합니다.
- **외부 게스트**: 게스트 사용자는 Azure AD가 없는 외부 조직에 속합니다.


이 유용한 한 가지 시나리오는 클레임의 소스가 게스트및 응용 프로그램에 액세스하는 직원에 대해 다른 경우입니다. 사용자가 직원인 경우 NameID가 user.email에서 원본으로 지정되지만 사용자가 게스트인 경우 NameID가 user.extensionattribute1에서 소스되도록 지정할 수 있습니다.

클레임 조건을 추가하려면 다음 을 수행합니다.

1. **클레임 관리에서**클레임 조건을 확장합니다.
2. 사용자 유형을 선택합니다.
3. 사용자가 속해야 하는 그룹을 선택합니다. 지정된 응용 프로그램에 대한 모든 클레임에서 최대 10개의 고유 그룹을 선택할 수 있습니다. 
4. 클레임이 해당 값을 검색할 **소스를** 선택합니다. 소스 특성 드롭다운에서 사용자 특성을 선택하거나 사용자 특성에 변환을 적용한 다음 클레임으로 내보내도 됩니다.

조건을 추가하는 순서가 중요합니다. Azure AD는 클레임에서 내할 값을 결정하기 위해 조건을 위에서 아래로 평가합니다. 

예를 들어, Brita Simon은 Contoso 테넌트의 게스트 사용자입니다. 그녀는 Azure AD를 사용하는 다른 조직에 속합니다. Fabrikam 응용 프로그램에 대한 아래 구성을 감안할 때, Brita가 Fabrikam에 로그인하려고 할 때 Azure AD는 다음과 같이 조건을 평가합니다.

첫째, Azure AD는 Brita의 사용자 `All guests`유형이 있는지 확인합니다. 이후 이것은 True다음 Azure AD에 대 한 `user.extensionattribute1`소스를 할당 합니다. 둘째, Azure AD는 Brita의 사용자 `AAD guests`유형이 다음과 같은지 확인한 다음 Azure AD가 `user.mail`클레임의 소스를 에 할당합니다. 마지막으로, 클레임은 Brita에 `user.email` 대한 값으로 내보내됩니다.

![클레임 조건부 구성](./media/active-directory-saml-claims-customization/sso-saml-user-conditional-claims.png)

## <a name="next-steps"></a>다음 단계

* [Azure AD의 애플리케이션 관리](../manage-apps/what-is-application-management.md)
* [Azure AD 애플리케이션 갤러리에 있지 않은 애플리케이션에 Single Sign-On 구성](../manage-apps/configure-federated-single-sign-on-non-gallery-applications.md)
* [SAML 기반 Single Sign-On 문제 해결](../azuread-dev/howto-v1-debug-saml-sso-issues.md)
