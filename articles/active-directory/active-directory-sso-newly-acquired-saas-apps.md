<properties
   pageTitle="새로 얻은 앱과 Azure Active Directory Single Sign-On 통합 | Microsoft Azure"
   description="Azure Active Directory는 Azure 포털에서 중앙 집중화된 액세스 관리를 가능하게 하기 위해 새로 얻은 SaaS 앱에 대한 Single Sign-On을 지원합니다."
   services="active-directory"
   documentationCenter=""
   authors="curtand"
   manager="stevenpo"
   editor=""/>

   <tags
      ms.service="active-directory"
      ms.devlang="na"
      ms.topic="article"
      ms.tgt_pltfrm="na"
      ms.workload="identity"
      ms.date="10/09/2015"
      ms.author="curtand"/>

# 새로 얻은 앱과 Azure Active Directory Single Sign-On 통합  

[AZURE.INCLUDE [active-directory-sso-use-case-intro](../../includes/active-directory-sso-use-case-intro.md)]

조직으로 가져오는 앱에 대한 Single Sign-On 설정을 시작하려면 Azure Active Directory에서 기존 디렉터리를 사용하게 됩니다. Microsoft Azure, Office 365 또는 Windows Intune을 통해 얻은 Azure AD 디렉터리를 사용할 수 있습니다. 이러한 항목이 두 개 이상 있는 경우 사용할 것을 결정하려면 [Azure AD 디렉터리 관리](active-directory-administer.md)를 참조하세요.

## 기타 고려 사항

### 인증

SAML 2.0, WS-Federation 또는 OpenID Connect 프로토콜을 지원하는 응용 프로그램의 경우 Azure Active Directory는 서명 인증서를 사용하여 트러스트 관계를 설정합니다. 이에 대한 자세한 내용은 [페더레이션된 Single Sign-On에 대한 인증서 관리](active-directory-sso-certs.md)를 참조하세요.

HTML 폼 기반 로그인만 지원하는 응용 프로그램의 경우 Azure Active Directory는 ‘암호 보관'을 사용하여 트러스트 관계를 설정합니다. 이에 따라 조직의 사용자가 SaaS 응용 프로그램에서 사용자 계정 정보를 사용하여 Azure AD에 의해 SaaS 응용 프로그램에 자동으로 로그인될 수 있습니다. Azure AD는 사용자 계정 정보 및 관련된 암호를 수집하고 안전하게 저장합니다. 자세한 내용은 [암호 기반 Single Sign-On](active-directory-appssoaccess-whatis.md#password-based-single-sign-on)을 참조하세요.

### 권한 부여

Single Sign-On을 통해 인증한 후에는 사용자에게 프로비전된 계정을 사용하여 응용 프로그램을 사용하도록 권한을 부여할 수 있습니다. 사용자 프로비저닝은 수동으로 수행할 수 있으며, Azure Active Directory에서 변경한 사항에 따라 SaaS 앱에서 사용자 정보를 추가하고 제거할 수 있는 경우도 있습니다. 자동화된 프로비저닝을 위해 기존 Azure AD 커넥터를 사용하는 방법에 대한 자세한 내용은 [SaaS 응용 프로그램에 대한 자동화된 사용자 프로비저닝 및 프로비저닝 해제](active-directory-saas-app-provisioning.md)를 참조하세요.

또는 수동으로 사용자 정보를 앱에 추가하거나 마켓플레이스에서 사용할 수 있는 다른 프로비저닝 솔루션을 사용할 수 있습니다.

### Access

Azure AD는 조직의 최종 사용자에게 응용 프로그램을 배포하는 여러 가지 사용자 지정 가능한 방법을 제공합니다. 특정 배포 또는 액세스 솔루션으로 제한되지 않으며, [요구 사항에 가장 적합한 솔루션](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users)을 사용할 수 있습니다.

## 다음 단계

Single Sign-On을 사용하도록 설정할 조직의 기존 SaaS 앱의 경우 [기존 앱과 Azure Active Directory SSO 통합](active-directory-sso-integrate-existing-apps.md)을 참조하세요.

응용 프로그램 갤러리에서 찾은 SaaS 앱의 경우 Azure Active Directory는 [SaaS 앱을 통합하는 방법에 대한 자습서](active-directory-saas-tutorial-list.md)를 다양하게 제공합니다.

앱이 응용 프로그램 갤러리에 없는 경우 [해당 앱을 사용자 지정 응용 프로그램으로 Azure Active Directory 응용 프로그램 갤러리에 추가](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx)할 수 있습니다.

[Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On이란 무엇입니까?](active-directory-appssoaccess-whatis.md)부터 시작하여 Azure.com 라이브러리에는 이러한 모든 문제에 대한 자세한 내용이 포함되어 있습니다.

<!---HONumber=Oct15_HO3-->