---
title: Azure AD에서 엔터프라이즈 앱에 대 한 SAML 토큰 클레임 사용자 지정 | Microsoft Docs
description: Azure AD의 엔터프라이즈 애플리케이션에 대한 SAML 토큰에 발급된 클레임을 사용자 지정하는 방법을 알아보세요.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: f1daad62-ac8a-44cd-ac76-e97455e47803
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: ryanwi
ms.reviewer: luleon, paulgarn, jeedes
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8e7681afe3f5361b17670312c8391349c650a89d
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/03/2019
ms.locfileid: "71936782"
---
# <a name="how-to-customize-claims-issued-in-the-saml-token-for-enterprise-applications"></a>방법: 엔터프라이즈 애플리케이션에 대한 SAML 토큰에 발급된 클레임 사용자 지정

현재 Azure Active Directory (Azure AD)는 Azure AD 앱 갤러리에 사전 통합 된 응용 프로그램과 사용자 지정 응용 프로그램을 포함 하 여 대부분의 엔터프라이즈 응용 프로그램에서 SSO (Single Sign-On)를 지원 합니다. 사용자가 SAML 2.0 프로토콜을 사용하여 Azure AD를 통해 애플리케이션을 인증하면 Azure AD는 (HTTP POST를 통해) 애플리케이션에 토큰을 보냅니다. 그런 다음 애플리케이션이 토큰의 유효성을 검사하고 사용하여 사용자 이름과 암호를 묻는 대신 사용자를 로그인합니다. 이러한 SAML 토큰에는 *클레임*이라는 사용자에 대 한 정보가 포함 되어 있습니다.

‘클레임’은 해당 사용자에 대해 발급하는 토큰 내에서 ID 공급자가 사용자에 대해 나타내는 정보입니다. [SAML 토큰 ](https://en.wikipedia.org/wiki/SAML_2.0)에서 이러한 데이터는 일반적으로 SAML 특성 문에 포함됩니다. 사용자 고유의 ID는 대개 이름 식별자라고도 하는 SAML Subject에 나타납니다.

기본적으로 Azure ad는 사용자를 고유 하 게 식별할 수 있는 사용자의 사용자 이름 (사용자 계정 이름이 라고도 함) 값이 포함 된 @no__t 0 클레임을 포함 하는 응용 프로그램에 대 한 SAML 토큰을 발급 합니다. 또한 SAML 토큰에는 사용자의 메일 주소, 이름 및 성을 포함하는 추가 클레임이 포함됩니다.

SAML 토큰이 애플리케이션에 발급한 클레임을 보거나 편집하려면 Azure Portal에서 애플리케이션을 엽니다. 그런 다음 **클레임 & 사용자 특성** 섹션을 엽니다.

![Azure Portal에서 클레임 & 사용자 특성 섹션을 엽니다.](./media/active-directory-saml-claims-customization/sso-saml-user-attributes-claims.png)

SAML 토큰에 발급된 클레임을 편집해야 할만한 두 가지 이유는 다음과 같습니다.

* 응용 프로그램을 사용 하려면 `NameIdentifier` 또는 NameID 클레임이 Azure AD에 저장 된 사용자 이름 (또는 사용자 계정 이름) 이외의 항목 이어야 합니다.
* 애플리케이션이 다른 클레임 URI 또는 클레임 값 집합을 요구하도록 작성되었습니다.

## <a name="editing-nameid"></a>NameID 편집

NameID (name identifier value)를 편집 하려면 다음을 수행 합니다.

1. **이름 식별자 값** 페이지를 엽니다.
1. 특성에 적용 하려는 특성 또는 변환을 선택 합니다. 필요에 따라 NameID 클레임에 포함할 형식을 지정할 수 있습니다.

   ![NameID (이름 식별자) 값 편집](./media/active-directory-saml-claims-customization/saml-sso-manage-user-claims.png)

### <a name="nameid-format"></a>NameID 형식

SAML 요청에 특정 형식의 NameIDPolicy 요소가 포함 된 경우 Azure AD는 요청에 형식을 적용 합니다.

SAML 요청에 NameIDPolicy에 대 한 요소가 포함 되지 않은 경우 Azure AD는 사용자가 지정한 형식으로 NameID를 발급 합니다. 형식이 지정 되지 않은 경우 Azure AD는 선택한 클레임 원본과 연결 된 기본 원본 형식을 사용 합니다.

**이름 식별자 형식 선택** 드롭다운 목록에서 다음 옵션 중 하나를 선택할 수 있습니다.

| NameID 형식 | 설명 |
|---------------|-------------|
| **Default** | Azure AD는 기본 원본 형식을 사용 합니다. |
| **지속적** | Azure AD는 NameID 형식으로 영구를 사용 합니다. |
| **EmailAddress** | Azure AD는 EmailAddress을 NameID 형식으로 사용 합니다. |
| **지정 되지 않은** | Azure AD는 NameID 형식으로 지정 되지 않은를 사용 합니다. |

NameIDPolicy 특성에 대해 자세히 알아보려면 [Single SIGN-ON SAML 프로토콜](single-sign-on-saml-protocol.md)을 참조 하세요.

### <a name="attributes"></a>특성

`NameIdentifier`(또는 NameID) 클레임의 원하는 소스를 선택합니다. 다음 옵션 중에서 선택할 수 있습니다.

| 이름 | 설명 |
|------|-------------|
| EMail | 사용자의 이메일 주소입니다. |
| userprincipalName | 사용자의 UPN (사용자 계정 이름) |
| onpremisessamaccount | 온-프레미스 Azure AD에서 동기화된 SAM 계정 이름입니다. |
| objectid | Azure AD에서 사용자의 objectid |
| employeeid | 사용자의 employeeid |
| 디렉터리 확장 | [Azure AD Connect 동기화를 사용하여 온-프레미스 Active Directory에서 동기화되는](../hybrid/how-to-connect-sync-feature-directory-extensions.md) 디렉터리 확장입니다. |
| 확장 특성 1-15 | Azure AD 스키마를 확장하는 데 사용되는 온-프레미스 확장 특성입니다. |

자세한 내용은 [Table 3: 원본 @ no__t에 대 한 유효한 ID 값은 0입니다.

### <a name="special-claims---transformations"></a>특수 클레임-변환

클레임 변환 함수를 사용할 수도 있습니다.

| 기능 | 설명 |
|----------|-------------|
| **ExtractMailPrefix()** | 전자 메일 주소 또는 사용자 계정 이름에서 도메인 접미사를 제거 합니다. 그러면 전달되는 사용자 이름의 첫 부분만 추출됩니다(예: joe_smith@contoso.com 대신 "joe_smith"). |
| **Join()** | 확인된 도메인에 특성을 조인합니다. 선택한 사용자 식별자 값에 도메인이 있으면 사용자 이름을 추출하여 선택한 확인된 도메인을 추가합니다. 예를 들어, 사용자 식별자 값으로 이메일(joe_smith@contoso.com)을 선택하고 확인된 도메인으로 contoso.onmicrosoft.com을 선택하면 joe_smith@contoso.onmicrosoft.com이 됩니다. |
| **ToLower()** | 선택한 특성의 문자를 소문자로 변환합니다. |
| **ToUpper()** | 선택한 특성의 문자를 대문자로 변환합니다. |

## <a name="adding-application-specific-claims"></a>응용 프로그램 관련 클레임 추가

응용 프로그램 관련 클레임을 추가 하려면:

1. **클레임 & 사용자 특성**에서 **새 클레임 추가** 를 선택 하 여 **사용자 클레임 관리** 페이지를 엽니다.
1. 클레임의 **이름을** 입력 합니다. 값은 SAML 사양에 따라 URI 패턴을 따를 필요가 없습니다. URI 패턴이 필요한 경우 **네임 스페이스** 필드에 추가할 수 있습니다.
1. 클레임에서 값을 검색할 **원본을** 선택 합니다. 원본 특성 드롭다운에서 사용자 특성을 선택 하거나 클레임으로 내보내기 전에 사용자 특성에 변환을 적용할 수 있습니다.

### <a name="application-specific-claims---transformations"></a>응용 프로그램별 클레임-변환

클레임 변환 함수를 사용할 수도 있습니다.

| 기능 | 설명 |
|----------|-------------|
| **ExtractMailPrefix()** | 전자 메일 주소 또는 사용자 계정 이름에서 도메인 접미사를 제거 합니다. 그러면 전달되는 사용자 이름의 첫 부분만 추출됩니다(예: joe_smith@contoso.com 대신 "joe_smith"). |
| **Join()** | 두 특성을 조인 하 여 새 값을 만듭니다. 필요에 따라 두 특성 사이에 구분 기호를 사용할 수 있습니다. |
| **ToLower()** | 선택한 특성의 문자를 소문자로 변환합니다. |
| **ToUpper()** | 선택한 특성의 문자를 대문자로 변환합니다. |
| **Contains()** | 입력이 지정 된 값과 일치 하는 경우 특성 또는 상수를 출력 합니다. 그렇지 않으면 일치 하는 항목이 없는 경우 다른 출력을 지정할 수 있습니다.<br/>예를 들어 "@contoso.com" 도메인이 포함 된 경우 값이 사용자의 전자 메일 주소인 클레임을 내보내려면이 고, 그렇지 않으면 사용자 계정 이름을 출력 합니다. 이렇게 하려면 다음 값을 구성 합니다.<br/>*매개 변수 1 (입력)* : user. email<br/>*값*: "@contoso.com"<br/>매개 변수 2 (출력): user. email<br/>매개 변수 3 (일치 하지 않는 경우 출력): user. userprincipalname |
| **EndWith()** | 입력이 지정 된 값으로 끝나는 경우 특성 또는 상수를 출력 합니다. 그렇지 않으면 일치 하는 항목이 없는 경우 다른 출력을 지정할 수 있습니다.<br/>예를 들어 employeeid가 "000"으로 끝나는 경우 값이 사용자의 employeeid 인 클레임을 내보내려면이 고, 그렇지 않으면 확장 특성을 출력 하려고 합니다. 이렇게 하려면 다음 값을 구성 합니다.<br/>*매개 변수 1 (입력)* : user. employeeid<br/>*값*: 10000<br/>매개 변수 2 (출력): user. employeeid<br/>매개 변수 3 (일치 하지 않는 경우 출력): user. extensionattribute1 |
| **StartWith()** | 입력이 지정 된 값으로 시작 하는 경우 특성 또는 상수를 출력 합니다. 그렇지 않으면 일치 하는 항목이 없는 경우 다른 출력을 지정할 수 있습니다.<br/>예를 들어 국가/지역이 "US"로 시작 하는 경우 값이 사용자의 employeeid 인 클레임을 내보내려면이 고, 그렇지 않으면 확장 특성을 출력 하는 것입니다. 이렇게 하려면 다음 값을 구성 합니다.<br/>*매개 변수 1 (입력)* : user. country<br/>*값*: "당사"<br/>매개 변수 2 (출력): user. employeeid<br/>매개 변수 3 (일치 하지 않는 경우 출력): user. extensionattribute1 |
| **Extract ()-일치 후** | 지정 된 값과 일치 하는 부분 문자열을 반환 합니다.<br/>예를 들어 입력 값이 "Finance_BSimon" 인 경우 일치 하는 값은 "Finance_"이 고 클레임의 출력은 "따르면"입니다. |
| **Extract ()-일치 하기 전에** | 지정 된 값과 일치 하는 부분 문자열을 반환 합니다.<br/>예를 들어 입력 값이 "BSimon_US" 인 경우 일치 하는 값은 "_US"이 고 클레임의 출력은 "따르면"입니다. |
| **Extract ()-일치 항목 사이** | 지정 된 값과 일치 하는 부분 문자열을 반환 합니다.<br/>예를 들어 입력 값이 "Finance_BSimon_US" 인 경우 일치 하는 첫 번째 값은 "Finance_"이 고, 두 번째 일치 하는 값은 "_US"이 고, 클레임의 출력은 "따르면"입니다. |
| **ExtractAlpha() - Prefix** | 문자열의 접두사 사전순 부분을 반환 합니다.<br/>예를 들어 입력의 값이 "BSimon_123" 인 경우 "따르면"를 반환 합니다. |
| **ExtractAlpha() - Suffix** | 문자열의 접미사 사전순 부분을 반환 합니다.<br/>예를 들어 입력의 값이 "123_Simon" 인 경우 "Simon"를 반환 합니다. |
| **ExtractNumeric() - Prefix** | 문자열의 접두사 숫자 부분을 반환 합니다.<br/>예를 들어 입력의 값이 "123_BSimon" 인 경우 "123"을 반환 합니다. |
| **ExtractNumeric() - Suffix** | 문자열의 접미사 숫자 부분을 반환 합니다.<br/>예를 들어 입력의 값이 "BSimon_123" 인 경우 "123"을 반환 합니다. |
| **IfEmpty()** | 입력이 null 이거나 비어 있는 경우 특성 또는 상수를 출력 합니다.<br/>예를 들어 지정 된 사용자에 대 한 employeeid가 비어 있는 경우 extensionattribute에 저장 된 특성을 출력 하려고 합니다. 이렇게 하려면 다음 값을 구성 합니다.<br/>매개 변수 1 (입력): user. employeeid<br/>매개 변수 2 (출력): extensionattribute1<br/>매개 변수 3 (일치 하지 않는 경우 출력): user. employeeid |
| **IfNotEmpty()** | 입력이 null 이거나 비어 있지 않은 경우 특성 또는 상수를 출력 합니다.<br/>예를 들어 지정 된 사용자에 대 한 employeeid가 비어 있지 않은 경우 extensionattribute에 저장 된 특성을 출력 하려고 합니다. 이렇게 하려면 다음 값을 구성 합니다.<br/>매개 변수 1 (입력): user. employeeid<br/>매개 변수 2 (출력): extensionattribute1 |

추가 변환이 필요한 경우 [AZURE AD의 사용자 의견 포럼](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=160599) 에서 *SaaS 응용 프로그램* 범주에 따라 아이디어를 제출 합니다.

## <a name="next-steps"></a>다음 단계

* [Azure AD의 애플리케이션 관리](../manage-apps/what-is-application-management.md)
* [Azure AD 애플리케이션 갤러리에 있지 않은 애플리케이션에 Single Sign-On 구성](../manage-apps/configure-federated-single-sign-on-non-gallery-applications.md)
* [SAML 기반 Single Sign-On 문제 해결](howto-v1-debug-saml-sso-issues.md)
