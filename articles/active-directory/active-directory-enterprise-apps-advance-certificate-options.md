---
title: "Azure Active Directory의 사전 통합된 앱에 대한 SAML 토큰의 고급 인증서 서명 옵션 | Microsoft Docs"
description: "Azure Active Directory에서 사전 통합된 앱에 대한 SAML 토큰의 고급 인증서 서명 옵션을 사용하는 방법을 알아봅니다."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/07/2017
ms.author: jeedes
ms.custom: aaddev
ms.translationtype: HT
ms.sourcegitcommit: 1868e5fd0427a5e1b1eeed244c80a570a39eb6a9
ms.openlocfilehash: 70e495965287a0edcb31493b69311fe28e04f6dc
ms.contentlocale: ko-kr
ms.lasthandoff: 09/19/2017

---
# <a name="advanced-certificate-signing-options-in-the-saml-token-for-gallery-apps-in-azure-active-directory"></a>Azure Active Directory의 갤러리 앱에 대한 SAML 토큰의 고급 인증서 서명 옵션
현재 Azure Active Directory에서는 SAML 2.0 프로토콜을 사용하여 Single Sign-On을 지원하는 500개 이상의 응용 프로그램을 포함하여 Azure AD 응용 프로그램 갤러리에서 사전 통합된 수천 개의 응용 프로그램을 지원합니다. 사용자가 SAML을 사용하여 Azure AD를 통해 응용 프로그램을 인증하면 Azure AD는 응용 프로그램에 토큰을 보냅니다(HTTP POST를 통해). 그런 다음 응용 프로그램이 토큰을 유효성 검사하고 사용하여 사용자 이름과 암호를 묻는 대신 사용자를 로그인합니다. 이러한 SAML 토큰은 Azure AD에서 생성된 고유한 인증서로 서명됩니다. 이 SAML 토큰은 특정 표준 알고리즘으로 서명됩니다.

Azure Active Directory는 갤러리 응용 프로그램의 기본 설정 중 일부를 사용합니다. 응용 프로그램 요구 사항에 따라 기본값이 설정됩니다.

Azure Active Directory는 고급 인증서 서명 설정을 지원합니다. 이러한 옵션을 선택하려면 먼저 다음과 같이 **SAML 고급 인증서 서명 설정** 확인란을 선택합니다.

![인증서 서명 옵션][1]

이 확인란을 선택하면 **인증서 서명 옵션**과 **인증서 서명 알고리즘**을 설정할 수 있습니다.

## <a name="certificate-signing-options"></a>인증서 서명 옵션

다음은 Azure AD에서 지원하는 세 가지 유형의 인증서 서명 옵션입니다.

1. **SAML 어설션 서명** - 대부분의 갤러리 응용 프로그램에 설정되는 기본 옵션입니다. 이 옵션을 선택하면 IDP인 Azure AD에서 응용 프로그램의 X509 인증서로 SAML 어설션 및 인증서에 서명합니다. 또한 아래의 드롭다운에서 선택되는 서명 알고리즘도 사용합니다.

2. **SAML 응답 서명** - 이 옵션을 선택하면 IDP인 Azure AD에서 응용 프로그램의 X509 인증서로 SAML 응답에 서명합니다. 또한 아래의 드롭다운에서 선택되는 서명 알고리즘도 사용합니다.

3. **SAML 응답 및 어설션 서명** - 이 옵션을 선택하면 IDP인 Azure AD에서 응용 프로그램의 X509 인증서로 전체 SAML 토큰에 서명합니다. 또한 아래의 드롭다운에서 선택되는 서명 알고리즘도 사용합니다.

    ![인증서 서명 옵션][4]

## <a name="certificate-signing-algorithm"></a>인증서 서명 알고리즘

Azure Active Directory는 SAML 응답에 서명하는 두 가지 유형의 서명 알고리즘을 지원합니다.

1. SHA256 - Azure Active Directory에서 SAML 응답에 서명하는 데 사용하는 기본 알고리즘입니다. 최신 알고리즘이며, SHA1보다 더 안전한 것으로 간주됩니다. 대부분의 응용 프로그램에서 SHA256 알고리즘을 지원합니다. 응용 프로그램에서 서명 알고리즘으로 SHA1을 지원하는 경우에만 변경할 수 있습니다. 그렇지 않으면 SAML 응답에 서명하는 데 SHA256 알고리즘을 사용하는 것이 좋습니다.

    ![SHA256 인증서 서명 알고리즘][3]

2. SHA1 - 이전 알고리즘이며, 안전한 것으로 간주되지 않습니다. 응용 프로그램에서 이 서명 알고리즘을 지원하는 경우에만 드롭다운에서 이 옵션을 선택할 수 있습니다. 이 옵션을 사용하면 Azure AD에서 SHA1 알고리즘으로 SAML 응답에 서명합니다.

    ![SHA1 인증서 서명 알고리즘][2]

## <a name="next-steps"></a>다음 단계
* [Azure Active Directory의 응용 프로그램 관리를 위한 문서 인덱스](active-directory-apps-index.md)
* [Azure Active Directory 응용 프로그램 갤러리에 있지 않은 응용 프로그램에 Single Sign-On 구성](active-directory-saas-custom-apps.md)
* [SAML 기반 Single Sign-On 문제 해결](develop/active-directory-saml-debugging.md)

<!--Image references-->

[1]: ./media/active-directory-enterprise-apps-advance-certificate-options/saml-advance-certificate.png
[2]: ./media/active-directory-enterprise-apps-advance-certificate-options/saml-signing-algo-sha1.png
[3]: ./media/active-directory-enterprise-apps-advance-certificate-options/saml-signing-algo-sha256.png
[4]: ./media/active-directory-enterprise-apps-advance-certificate-options/saml-signing-options.png
