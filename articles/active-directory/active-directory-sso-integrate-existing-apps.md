<properties
   pageTitle="기존 앱과 Azure Active Directory SSO 통합 | Microsoft Azure"
   description="Azure Active Directory에서 Single Sign-On 인증과 사용자 프로비저닝을 사용하도록 설정하여 이미 사용 중인 SaaS 앱을 관리합니다."
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="10/05/2015"
   ms.author="kgremban; liviodlc"/>

# 기존 앱과 Azure Active Directory SSO 통합

[AZURE.INCLUDE [active-directory-sso-use-case-intro](../../includes/active-directory-sso-use-case-intro.md)]

## 기타 고려 사항

이미 사용 중인 앱에 대한 SSO를 설정하는 작업은 새 앱에서 계정을 새로 만드는 것과 다른 프로세스입니다. 관리자와 사용자 모두 앱이 Azure AD에 통합될 때 변화를 경험합니다.

### 이미 사용 중인 앱에 대한 Single Sign-On을 설정하기 위해 관리자가 알아야 하는 것은 무엇일까요?

기존 앱에 대한 SSO를 설정하려면 Azure AD와 SaaS 응용 프로그램에서 전역 관리자 권한이 있어야 합니다.

응용 프로그램이 이미 사용 중이므로 사용자의 설정된 앱 ID를 개별 Azure AD ID에 연결해야 합니다. 해당 앱이 로그온을 위한 고유 식별자로 메일 주소, UPN(Universal Personal Name) 또는 사용자 이름 중에서 무엇을 사용하는지를 알아야 합니다. 이 항목은 Azure AD에서 사용자의 고유 식별자에 연결됩니다. 앱 ID를 Azure AD ID와 연결하는 방법에 대한 자세한 내용은 [SAML 토큰에서 발급된 클레임 사용자 지정](http://social.technet.microsoft.com/wiki/contents/articles/31257.azure-active-directory-customizing-claims-issued-in-the-saml-token-for-pre-integrated-apps.aspx) 및 [프로비저닝을 위한 특성 매핑 사용자 지정](active-directory-saas-customizing-attribute-mappings.md)을 참조하세요.

### 최종 사용자에게 미치는 영향

이미 사용 중인 응용 프로그램에 대한 SSO를 통합할 때 사용자 환경이 영향을 받게 된다는 사실을 알고 있어야 합니다. 모든 앱의 경우 사용자는 먼저 Azure AD 자격 증명을 사용하여 로그인합니다. 또한 다른 포털을 사용하여 응용 프로그램에 액세스해야 할 수도 있습니다. 응용 프로그램에 따라 SSO가 앱의 자체 웹 사이트에서 수행될 수도 있고, 사용자가 중앙 앱 포털([내 앱](myapps.microsoft.com) 또는 [Office365](portal.office.com/myapps))을 통해 로그인해야 할 수도 있습니다. 다양한 유형의 SSO 및 해당 사용자 환경에 대한 자세한 내용은 [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On이란 무엇입니까?](active-directory-appssoaccess-whatis.md)를 참조하세요.

## 다음 단계
- [새로 얻은 앱과 Azure Active Directory Single Sign-On을 통합](active-directory-sso-newly-acquired-saas-apps.md)하는 방법 확인하기
- [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On](active-directory-appssoaccess-whatis.md)에 대해 자세히 알아보기
- [SaaS 앱을 통합하는 방법에 대한 자습서](active-directory-saas-tutorial-list.md) 찾기
-	[Azure AD 셀프 서비스 SAML 구성](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx)을 사용하여 사용자 지정 앱 추가

<!---HONumber=Oct15_HO3-->