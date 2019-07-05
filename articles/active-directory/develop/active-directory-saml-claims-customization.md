---
title: Azure AD의 엔터프라이즈 애플리케이션에 대한 SAML 토큰에 발급된 클레임 사용자 지정 | Microsoft Docs
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
ms.topic: article
ms.date: 04/03/2019
ms.author: ryanwi
ms.reviewer: luleon, paulgarn, jeedes
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 636086ce0d055ab8de1d1b95dbbf7e5d96c7d7ef
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67483060"
---
# <a name="how-to-customize-claims-issued-in-the-saml-token-for-enterprise-applications"></a>방법: 엔터프라이즈 애플리케이션에 대한 SAML 토큰에 발급된 클레임 사용자 지정

현재, Azure Active Directory (Azure AD)에서 single sign-on (SSO) 사용자 지정 응용 프로그램 뿐만 아니라 Azure AD 앱 갤러리에서 미리 통합 된 응용 프로그램 모두를 포함 하 여 대부분의 엔터프라이즈 응용 프로그램을 지원 합니다. 사용자가 SAML 2.0 프로토콜을 사용하여 Azure AD를 통해 애플리케이션을 인증하면 Azure AD는 (HTTP POST를 통해) 애플리케이션에 토큰을 보냅니다. 그런 다음 애플리케이션이 토큰의 유효성을 검사하고 사용하여 사용자 이름과 암호를 묻는 대신 사용자를 로그인합니다. 이러한 SAML 토큰 이라고 알려진 사용자에 대 한 정보가 포함 될 *클레임*합니다.

‘클레임’은 해당 사용자에 대해 발급하는 토큰 내에서 ID 공급자가 사용자에 대해 나타내는 정보입니다.  [SAML 토큰 ](https://en.wikipedia.org/wiki/SAML_2.0)에서 이러한 데이터는 일반적으로 SAML 특성 문에 포함됩니다. 사용자 고유의 ID는 대개 이름 식별자라고도 하는 SAML Subject에 나타납니다.

기본적으로 Azure AD 포함 된 응용 프로그램에 SAML 토큰을 발급 한 `NameIdentifier` 클레임 값이 사용자의 사용자 이름 (라고도 사용자 주체 이름)의 Azure ad에서 사용자를 식별할 수 있습니다. 또한 SAML 토큰에는 사용자의 메일 주소, 이름 및 성을 포함하는 추가 클레임이 포함됩니다.

SAML 토큰이 애플리케이션에 발급한 클레임을 보거나 편집하려면 Azure Portal에서 애플리케이션을 엽니다. 엽니다는 **사용자 특성 및 클레임** 섹션입니다.

![Azure portal에서 사용자 특성 및 클레임 섹션을 엽니다.](./media/active-directory-saml-claims-customization/sso-saml-user-attributes-claims.png)

SAML 토큰에 발급된 클레임을 편집해야 할만한 두 가지 이유는 다음과 같습니다.

* 응용 프로그램에 필요한는 `NameIdentifier` 또는 NameID 클레임을 Azure AD에 저장 된 사용자 이름 (또는 사용자 계정 이름) 이외의 있을 수 있습니다.
* 애플리케이션이 다른 클레임 URI 또는 클레임 값 집합을 요구하도록 작성되었습니다.

## <a name="editing-nameid"></a>NameID를 편집합니다.

NameID (이름 식별자 값)을 편집 합니다.

1. 엽니다는 **이름 식별자 값** 페이지입니다.
1. 특성 또는 특성에 적용할 변환을 선택 합니다. 필요에 따라 할 NameID 클레임 형식을 지정할 수 있습니다.

   ![NameID (이름 식별자) 값 편집](./media/active-directory-saml-claims-customization/saml-sso-manage-user-claims.png)

### <a name="nameid-format"></a>NameID 형식

SAML 요청 특정 형식 지닌 NameIDPolicy 요소에 있으면 Azure AD는 요청의 형식에에서 적용 됩니다.

SAML 요청 NameIDPolicy에 대 한 요소를 포함 하지 않습니다, Azure AD를 지정한 형식으로 NameID를 발급 됩니다. 경우 아무런 서식도 지정 Azure AD 선택한 클레임 소스에 연결 된 기본 소스 형식을 사용 합니다.

**이름 식별자 형식 선택** 드롭다운에서 다음 옵션 중 하나를 선택할 수 있습니다.

| NameID 형식 | 설명 |
|---------------|-------------|
| **기본값** | Azure AD 기본 소스 형식을 사용 합니다. |
| **영구** | Azure AD는 영구 NameID 형식으로 사용 합니다. |
| **EmailAddress** | NameID 형식으로 azure AD 전자 메일 주소를 사용 합니다. |
| **지정 되지 않은** | Azure AD는 지정 되지 않음 NameID 형식으로 사용 합니다. |
| **Transient** | Azure AD는 NameID 형식으로 일시적이 지를 사용 합니다. |

NameIDPolicy 특성에 대 한 자세한 내용은 참조 하세요 [Single Sign On SAML 프로토콜](single-sign-on-saml-protocol.md)합니다.

### <a name="attributes"></a>특성

`NameIdentifier`(또는 NameID) 클레임의 원하는 소스를 선택합니다. 다음 옵션 중에서 선택할 수 있습니다.

| Name | 설명 |
|------|-------------|
| Email | 사용자의 이메일 주소입니다. |
| userprincipalName | 사용자의 사용자 계정 이름 (UPN) |
| onpremisessamaccount | 온-프레미스 Azure AD에서 동기화된 SAM 계정 이름입니다. |
| objectId | Azure AD에서 사용자의 objectid |
| employeeid | 사용자의 employeeid |
| 디렉터리 확장 | [Azure AD Connect 동기화를 사용하여 온-프레미스 Active Directory에서 동기화되는](../hybrid/how-to-connect-sync-feature-directory-extensions.md) 디렉터리 확장입니다. |
| 확장 특성 1-15 | Azure AD 스키마를 확장하는 데 사용되는 온-프레미스 확장 특성입니다. |

자세한 내용은 참조 하세요. [표 3: 원본별 유효한 ID 값](active-directory-claims-mapping.md#table-3-valid-id-values-per-source)합니다.

### <a name="special-claims---transformations"></a>특별 한 클레임으로 변환

또한 클레임 변환 함수를 사용할 수 있습니다.

| 함수 | 설명 |
|----------|-------------|
| **ExtractMailPrefix()** | 전자 메일 주소 또는 사용자 계정 이름에서 도메인 접미사를 제거합니다. 그러면 전달되는 사용자 이름의 첫 부분만 추출됩니다(예: joe_smith@contoso.com 대신 "joe_smith"). |
| **Join()** | 확인된 도메인에 특성을 조인합니다. 선택한 사용자 식별자 값에 도메인이 있으면 사용자 이름을 추출하여 선택한 확인된 도메인을 추가합니다. 예를 들어, 사용자 식별자 값으로 이메일(joe_smith@contoso.com)을 선택하고 확인된 도메인으로 contoso.onmicrosoft.com을 선택하면 joe_smith@contoso.onmicrosoft.com이 됩니다. |
| **ToLower()** | 선택한 특성의 문자를 소문자로 변환합니다. |
| **ToUpper()** | 선택한 특성의 문자를 대문자로 변환합니다. |

## <a name="adding-application-specific-claims"></a>응용 프로그램별 클레임 추가

응용 프로그램별 클레임을 추가 합니다.

1. **사용자 특성 및 클레임**를 선택 **추가 새 클레임** 열려는 **사용자 클레임을 관리할** 페이지입니다.
1. 입력 된 **이름을** 클레임입니다. 값 당 SAML 사양에서 URI 패턴을 따라 엄격 하 게 필요 하지 않습니다. URI 패턴을 할 경우에 넣을 수 있습니다 하는 **Namespace** 필드입니다.
1. 선택 된 **원본** 클레임 해당 값을 검색 하는 것입니다. 원본 특성 드롭다운 목록에서 사용자 특성을 선택할 수도 있고 사용자 특성을 클레임으로 내보내기 전에 변환을 적용할 수 있습니다.

### <a name="application-specific-claims---transformations"></a>응용 프로그램별 클레임으로 변환

또한 클레임 변환 함수를 사용할 수 있습니다.

| 함수 | 설명 |
|----------|-------------|
| **ExtractMailPrefix()** | 전자 메일 주소 또는 사용자 계정 이름에서 도메인 접미사를 제거합니다. 그러면 전달되는 사용자 이름의 첫 부분만 추출됩니다(예: joe_smith@contoso.com 대신 "joe_smith"). |
| **Join()** | 두 가지 특성을 조인 하 여 새 값을 만듭니다. 필요에 따라 두 특성 간의 구분 기호를 사용할 수 있습니다. |
| **ToLower()** | 선택한 특성의 문자를 소문자로 변환합니다. |
| **ToUpper()** | 선택한 특성의 문자를 대문자로 변환합니다. |
| **Contains()** | 입력에 지정 된 값과 일치 하는 경우 특성 또는 상수를 출력 합니다. 그렇지 않으면 일치 하는 경우 다른 출력을 지정할 수 있습니다.<br/>예를 들어, 클레임 값 인 사용자의 전자 메일 주소 도메인을 포함 하는 경우 내보내려는 하려는 경우 "@contoso.com", 사용자 계정 이름 출력 하려는 경우. 이렇게 하려면 다음 값을 구성할는 있습니다.<br/>*매개 변수 1(input)* : user.email<br/>*값*: "@contoso.com"<br/>매개 변수 (출력) 2: user.email<br/>매개 변수 3 (출력 일치 항목이 없는 경우): user.userprincipalname |
| **EndWith()** | 지정된 된 값을 사용 하 여 입력이 종료 되는 경우 특성 또는 상수를 출력 합니다. 그렇지 않으면 일치 하는 경우 다른 출력을 지정할 수 있습니다.<br/>예를 들어, 여기서 값은 사용자의 employeeid employeeid "000"로 끝나는 경우 클레임을 내보낼 하려는 경우이 고, 그렇지 하려는 확장 특성을 출력 합니다. 이렇게 하려면 다음 값을 구성할는 있습니다.<br/>*매개 변수 1(input)* : user.employeeid<br/>*값*: "000"<br/>매개 변수 (출력) 2: user.employeeid<br/>매개 변수 3 (출력 일치 항목이 없는 경우): user.extensionattribute1 |
| **StartWith()** | 지정된 된 값을 사용 하 여 입력을 시작 하는 경우 특성 또는 상수를 출력 합니다. 그렇지 않으면 일치 하는 경우 다른 출력을 지정할 수 있습니다.<br/>예를 들어, 클레임 값 인 사용자의 employeeid 국가/지역을 "미국"로 시작 하는 경우 내보내려는 원한다 면이 고, 그렇지 하려는 확장 특성을 출력 합니다. 이렇게 하려면 다음 값을 구성할는 있습니다.<br/>*매개 변수 1(input)* : user.country<br/>*값*: "당사"<br/>매개 변수 (출력) 2: user.employeeid<br/>매개 변수 3 (출력 일치 항목이 없는 경우): user.extensionattribute1 |
| **Extract ()-검색 한 후** | 지정 된 값과 일치 하는 후 부분 문자열을 반환 합니다.<br/>예를 들어, "Finance_BSimon" 입력의 값을 사용 하는 경우 일치 하는 값은 "Finance_" 다음 클레임의 출력은 "예를 들어" 합니다. |
| **Extract ()-일치 하기 전에** | 지정된 된 값을 찾을 때까지 부분 문자열을 반환 합니다.<br/>예를 들어, "BSimon_US" 입력의 값을 사용 하는 경우 일치 하는 값은 "_US" 다음 클레임의 출력은 "예를 들어" 합니다. |
| **Extract ()-일치 하는 사이** | 지정된 된 값을 찾을 때까지 부분 문자열을 반환 합니다.<br/>예를 들어, 입력의 값이 "Finance_BSimon_US" 이면 첫 번째 일치 하는 값은 "Finance_" 두 번째 일치 하는 값은 "_US" 차례로 클레임의 출력은 "예를 들어"입니다. |
| **ExtractAlpha() - Prefix** | 문자열의 접두사 사전순 부분을 반환 합니다.<br/>예를 들어, "BSimon_123" 입력의 값을 사용 하는 경우 "예를 들어" 반환 합니다. |
| **ExtractAlpha() - Suffix** | 접미사 사전순 부분 문자열을 반환합니다.<br/>예를 들어, 입력의 값이 "123_Simon" 이면 "Simon" 반환 합니다. |
| **ExtractNumeric() - Prefix** | 문자열의 접두사 숫자 부분을 반환 합니다.<br/>예를 들어, "123_BSimon" 입력의 값을 사용 하는 경우 "123" 반환 합니다. |
| **ExtractNumeric() - Suffix** | 문자열의 숫자 접미사 부분을 반환합니다.<br/>예를 들어, "BSimon_123" 입력의 값을 사용 하는 경우 "123" 반환 합니다. |
| **IfEmpty()** | 입력이 null 이거나 비워 둘 경우 특성 또는 상수를 출력 합니다.<br/>예를 들어, employeeid 지정된 된 사용자에 대 한 비어 있는 경우는 extensionattribute에 저장 된 특성을 출력 하도록 하려는 경우. 이렇게 하려면 다음 값을 구성할는 있습니다.<br/>매개 변수 1(input): user.employeeid<br/>매개 변수 (출력) 2: user.extensionattribute1<br/>매개 변수 3 (출력 일치 항목이 없는 경우): user.employeeid |
| **IfNotEmpty()** | 입력이 null 이거나 비워 둘 경우 특성 또는 상수를 출력 합니다.<br/>예를 들어, 지정된 된 사용자에 대 한 employeeid 비어 있지 않은 경우는 extensionattribute에 저장 된 특성을 출력 하도록 하려는 경우. 이렇게 하려면 다음 값을 구성할는 있습니다.<br/>매개 변수 1(input): user.employeeid<br/>매개 변수 (출력) 2: user.extensionattribute1 |

추가 변환에 필요한 경우에 아이디어를 제출 합니다 [Azure AD의 사용자 의견 포럼](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=160599) 아래를 *SaaS 응용 프로그램* 범주입니다.

## <a name="next-steps"></a>다음 단계

* [Azure AD의 애플리케이션 관리](../manage-apps/what-is-application-management.md)
* [Azure AD 애플리케이션 갤러리에 있지 않은 애플리케이션에 Single Sign-On 구성](../manage-apps/configure-federated-single-sign-on-non-gallery-applications.md)
* [SAML 기반 Single Sign-On 문제 해결](howto-v1-debug-saml-sso-issues.md)
