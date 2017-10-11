---
title: "Azure Active Directory Single Sign-On과 SaaS 앱 통합 | Microsoft Docs"
description: "Azure Active Directory에서 SaaS 앱의 Single Sign-On 인증과 사용자 프로비전 집중식 액세스 관리를 사용하도록 설정합니다. SaaS 앱에 Azure Active Directory를 통합하는 방법의 개요입니다."
services: active-directory
keywords: "Azure AD와 SaaS 앱 통합"
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 53b9d341-d1fc-4bbb-ac7c-3f4c68fcf00a
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/17/2017
ms.author: curtand
ms.reviewer: aaronsm
ms.openlocfilehash: fc0d297598c334ca8f6f8a2bd3ae948c87956342
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2017
---
# <a name="integrate-azure-active-directory-single-sign-on-with-saas-apps"></a>Azure Active Directory Single Sign-On과 SaaS 앱 통합
> [!div class="op_single_selector"]
> * [Azure Portal](active-directory-enterprise-apps-manage-sso.md)
> * [Azure 클래식 포털](active-directory-sso-integrate-saas-apps.md)
>
>

[!INCLUDE [active-directory-sso-use-case-intro](../../includes/active-directory-sso-use-case-intro.md)]

조직으로 가져오는 앱에 대한 Single Sign-On 설정을 시작하려면 Azure AD(Azure Active Directory)에서 기존 디렉터리를 사용하게 됩니다. Microsoft Azure, Office 365 또는 Windows Intune을 통해 얻은 Azure AD 디렉터리를 사용할 수 있습니다. 이러한 항목이 두 개 이상 있는 경우 사용할 것을 결정하려면 [Azure AD 디렉터리 관리](active-directory-administer.md) 를 참조하세요.

> [!IMPORTANT]
> 이 문서에서 참조되는 Azure 클래식 포털을 사용하는 대신 Azure Portal에서 [Azure AD 관리 센터](https://aad.portal.azure.com)를 사용하여 Azure AD를 관리하는 것이 좋습니다. Azure AD 관리 센터에서 관리자 역할을 할당하는 방법은 [Azure Active Directory에서 관리자 역할 할당](active-directory-enterprise-apps-manage-sso.md)을 참조하세요.

## <a name="authentication"></a>인증
SAML 2.0, WS-Federation 또는 OpenID Connect 프로토콜을 지원하는 응용 프로그램의 경우 Azure Active Directory는 서명 인증서를 사용하여 트러스트 관계를 설정합니다. 이에 대한 자세한 내용은 [페더레이션된 Single Sign-On에 대한 인증서 관리](active-directory-sso-certs.md)를 참조하세요.

HTML 폼 기반 로그인만 지원하는 응용 프로그램의 경우 Azure Active Directory는 ‘암호 보관'을 사용하여 트러스트 관계를 설정합니다. 이에 따라 조직의 사용자가 SaaS 응용 프로그램에서 사용자 계정 정보를 사용하여 Azure AD에 의해 SaaS 응용 프로그램에 자동으로 로그인될 수 있습니다. Azure AD는 사용자 계정 정보 및 관련된 암호를 수집하고 안전하게 저장합니다. 자세한 내용은 [암호 기반 Single Sign-On](active-directory-appssoaccess-whatis.md#password-based-single-sign-on)을 참조하세요.

## <a name="authorization"></a>권한 부여
Single Sign-On을 통해 인증한 후에는 사용자에게 프로비전된 계정을 사용하여 응용 프로그램을 사용하도록 권한을 부여할 수 있습니다. 사용자 프로비저닝은 수동으로 수행할 수 있으며, Azure Active Directory에서 변경한 사항에 따라 SaaS 앱에서 사용자 정보를 추가하고 제거할 수 있는 경우도 있습니다. 자동화된 프로비저닝을 위해 기존 Azure AD 커넥터를 사용하는 방법에 대한 자세한 내용은 [SaaS 응용 프로그램에 대한 자동화된 사용자 프로비저닝 및 프로비저닝 해제](active-directory-saas-app-provisioning.md)를 참조하세요.

또는 수동으로 사용자 정보를 앱에 추가하거나 마켓플레이스에서 사용할 수 있는 다른 프로비저닝 솔루션을 사용할 수 있습니다.

## <a name="access"></a>Access
Azure AD는 조직의 최종 사용자에게 응용 프로그램을 배포하는 여러 가지 사용자 지정 가능한 방법을 제공합니다. 특정 배포 또는 액세스 솔루션으로 제한되지 않으며, [요구 사항에 가장 적합한 솔루션](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users)을 사용할 수 있습니다.

## <a name="additional-considerations-for-applications-already-in-use"></a>이미 사용 중인 응용 프로그램에 대한 추가 고려 사항
조직이 사용하는 응용 프로그램에 Single Sign-On을 설치하는 작업은 새 응용 프로그램에 새 계정을 만드는 것과 다른 프로세스입니다. 다음 몇 가지 준비 단계를 포함합니다. 응용 프로그램에서 Azure AD ID에 사용자 ID를 매핑하고 통합된 후에 사용자가 응용 프로그램에 로그인하는 방법을 이해합니다.

> [!NOTE]
> 기존 응용 프로그램에 대한 SSO를 설정하려면 Azure AD와 SaaS 응용 프로그램에서 전역 관리자 권한이 있어야 합니다.
>
>

### <a name="mapping-user-accounts"></a>사용자 계정 매핑
사용자의 ID에는 일반적으로 전자 메일 주소 또는 사용자 계정 이름(UPN)일 수 있는 고유 식별자가 있습니다. 각 사용자의 응용 프로그램 ID를 해당 Azure AD ID에 링크(맵핑)해야 합니다. 응용 프로그램 인증이 요구하는 방법에 따라 이 작업을 수행하는 몇 가지 방법이 있습니다.

응용 프로그램 ID를 Azure AD ID와 매핑하는 방법에 대한 자세한 내용은 [SAML 토큰에서 발급된 클레임 사용자 지정](http://social.technet.microsoft.com/wiki/contents/articles/31257.azure-active-directory-customizing-claims-issued-in-the-saml-token-for-pre-integrated-apps.aspx) 및 [프로비전을 위한 특성 매핑 사용자 지정](active-directory-saas-customizing-attribute-mappings.md)을 참조하세요.

### <a name="understanding-the-users-log-in-experience"></a>사용자의 로그인 환경 이해
이미 사용 중인 응용 프로그램에 대한 SSO를 통합할 때 사용자 환경이 영향을 받게 된다는 사실을 알고 있어야 합니다. 모든 응용 프로그램의 경우 사용자는 먼저 Azure AD 자격 증명을 사용하여 로그인합니다. 또한 다른 포털을 사용하여 응용 프로그램에 액세스해야 할 수도 있습니다.

응용 프로그램에 따라 SSO가 응용 프로그램의 로그인 인터페이스에서 수행될 수도 있고, 사용자가 [내 앱](http://myapps.microsoft.com) 또는 [Office365](http://portal.office.com/myapps)와 같은 중앙 포털을 통해 로그인해야 할 수도 있습니다. 다양한 유형의 SSO 및 해당 사용자 환경에 대한 자세한 내용은 [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On이란 무엇입니까?](active-directory-appssoaccess-whatis.md)를 참조하세요.

또 다른 중요한 리소스는 *개발자 가이드* 문서에 있는 [사용자 동의 억제](active-directory-applications-guiding-developers-for-lob-applications.md) 입니다.

## <a name="next-steps"></a>다음 단계
앱 갤러리에서 찾은 SaaS 앱의 경우 Azure AD는 [SaaS 앱을 통합하는 방법에 대한 자습서](active-directory-saas-tutorial-list.md)를 다양하게 제공합니다.

앱이 앱 갤러리에 없는 경우 [해당 앱을 사용자 지정 응용 프로그램으로 Azure AD 앱 갤러리에 추가](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx)할 수 있습니다.

[Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On이란 무엇입니까?](active-directory-appssoaccess-whatis.md)부터 시작하여 Azure.com 라이브러리에는 이러한 모든 문제에 대한 자세한 내용이 포함되어 있습니다.

또한 [Azure Active Directory의 응용 프로그램 관리를 위한 문서 인덱스](active-directory-apps-index.md)를 놓치지 마세요.
