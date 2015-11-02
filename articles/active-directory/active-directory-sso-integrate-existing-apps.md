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
   ms.date="10/16/2015"
   ms.author="kgremban; liviodlc"/>

# 기존 앱과 Azure Active Directory SSO 통합

## 개요

조직이 사용하는 응용 프로그램에 Single Sign-On(SSO)을 설치하는 작업은 새 응용 프로그램에 새 계정을 만드는 것과 다른 프로세스입니다. 다음 몇 가지 준비 단계를 포함합니다. 응용 프로그램에서 Azure Active Directory(AD) ID에 사용자 ID를 매핑하고 통합된 후에 사용자가 응용 프로그램에 로그인하는 방법을 이해합니다.

> [AZURE.NOTE]기존 응용 프로그램에 대한 SSO를 설정하려면 Azure AD와 SaaS 응용 프로그램에서 전역 관리자 권한이 있어야 합니다.

## 사용자 계정 매핑

사용자의 ID에는 일반적으로 전자 메일 주소 또는 범용 개인 이름(UPN)일 수 있는 고유 식별자가 있습니다. 각 사용자의 응용 프로그램 ID를 해당 Azure AD ID에 링크(맵핑)해야 합니다. 응용 프로그램 인증이 요구하는 방법에 따라 이 작업을 수행하는 몇 가지 방법이 있습니다.

응용 프로그램 ID를 Azure AD ID와 매핑하는 방법에 대한 자세한 내용은 [SAML 토큰에서 발급된 클레임 사용자 지정](http://social.technet.microsoft.com/wiki/contents/articles/31257.azure-active-directory-customizing-claims-issued-in-the-saml-token-for-pre-integrated-apps.aspx) 및 [프로비저닝을 위한 특성 매핑 사용자 지정](active-directory-saas-customizing-attribute-mappings.md)을 참조하세요.

## 사용자의 로그인 환경 이해

이미 사용 중인 응용 프로그램에 대한 SSO를 통합할 때 사용자 환경이 영향을 받게 된다는 사실을 알고 있어야 합니다. 모든 응용 프로그램의 경우 사용자는 먼저 Azure AD 자격 증명을 사용하여 로그인합니다. 또한 다른 포털을 사용하여 응용 프로그램에 액세스해야 할 수도 있습니다.

응용 프로그램에 따라 SSO가 응용 프로그램의 로그인 인터페이스에서 수행될 수도 있고, 사용자가 중앙 포털([내 앱](http://myapps.microsoft.com) 또는 [Office365](http://portal.office.com/myapps)과 같은)을 통해 로그인해야 할 수도 있습니다. 다양한 유형의 SSO 및 해당 사용자 환경에 대한 자세한 내용은 [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On이란 무엇입니까?](active-directory-appssoaccess-whatis.md)를 참조하세요.

또한 [개발자 가이드](active-directory-applications-guiding-developers-for-lob-applications.md) 문서에서 *사용자 동의 억제*를 참조하세요.

## 관련 문서
사용자 동의 억제 및 기타 개발자 하위 문서 가이드에 연결

<!---HONumber=Oct15_HO4-->