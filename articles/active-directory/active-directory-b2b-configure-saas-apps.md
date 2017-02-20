---
title: "Azure Active Directory에서 B2B 공동 작업을 위한 SaaS 앱 구성 | Microsoft Docs"
description: "Azure Active Directory B2B 공동 작업을 위한 코드 및 PowerShell 샘플"
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 02/06/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 0c05cd490ee9125f7e5182cb502db6f4e9390094
ms.openlocfilehash: 3f41fdaa4f0ec31c9f11f2826b5cb9ccbf4db30d


---

# <a name="configure-saas-apps-for-b2b-collaboration"></a>B2B 공동 작업을 위한 SaaS 앱 구성

Azure AD(Azure Active Directory) B2B 공동 작업은 Azure AD와 통합되는 대부분의 응용 프로그램에서 작동합니다. 이 섹션에서는 Azure AD B2B와 함께 사용되는 일부 인기 있는 SAS 앱을 구성하는 지침을 안내합니다.
응용 프로그램별 지침을 살펴보기 전에 다음과 같이 경험에 근거한 몇 가지 규칙이 있습니다.

* 대부분의 응용 프로그램에서는 사용자 프로비전을 수동으로 수행해야 합니다(즉 사용자도 응용 프로그램에서 수동으로 만들어야 함).

* Dropbox와 같은 자동 프로비전을 지원하는 앱의 경우 응용 프로그램에서 만들어진 별도의 초대가 있습니다. 사용자는 각 초대를 수락해야 합니다.

* 게스트 사용자의 잘못된(mangled) UPD 관련 문제를 완화하기 위해 사용자 특성에서 항상 사용자 식별자를 user.mail로 설정합니다.


##<a name="dropbox-for-business"></a>DropBox for Business

사용자가 조직 계정을 사용하여 로그인할 수 있게 하려면 Azure AD를 SAML ID 공급자로 사용하도록 Dropbox for Business를 수동으로 구성해야 합니다. Dropbox for Business에서 메시지를 표시하지 않거나 다른 방법으로 Azure AD를 사용하도록 구성되지 않은 경우 Dropbox for Business에서 사용자가 Azure AD를 통해 로그인하도록 허용하지 않습니다.

1. 스크린샷과 같이 DropBox for Business 응용 프로그램을 Azure AD에 추가합니다.

  ![Azure AD에 Dropbox 추가](media/active-directory-b2b-configure-saas-apps/add-dropbox.png)

  ![Azure AD에 Dropbox 추가](media/active-directory-b2b-configure-saas-apps/add-app-dialog.png)

2. 응용 프로그램을 구성합니다.

  ![앱에 Single Sign-On 구성](media/active-directory-b2b-configure-saas-apps/configure-app-sso.png)

3. Single Sign-On 구성을 선택하고 사용자 식별자를 user.mail(기본적으로 UPN)로 변경합니다.

4. DropBox 구성에 사용할 인증서를 다운로드합니다.

  ![인증서 다운로드](media/active-directory-b2b-configure-saas-apps/download-certificate.png)

5. [DropBox 구성] 옵션을 사용합니다(아래의 스크린샷에서 프로세스를 자세히 설명함).

6. 구성에 사용될 SAML Single Sign-On URL을 가져옵니다.

7. DropBox 구성 페이지에서 로그온 URL을 가져옵니다.

  ![DropBox에 로그인](media/active-directory-b2b-configure-saas-apps/sign-in-to-dropbox.png)

  ![DropBox 메뉴](media/active-directory-b2b-configure-saas-apps/dropbox-menu.png)

  ![축소된 DropBox 인증 대화 상자](media/active-directory-b2b-configure-saas-apps/dropbox-auth-01.png)

  ![확장된 DropBox 인증 대화 상자](media/active-directory-b2b-configure-saas-apps/dropbox-auth-02.png)

8. 인증서를 업로드하고 여기에 SAML Single Sign-On URL을 붙여넣습니다.

  ![SAML SSO URL에 붙여넣기](media/active-directory-b2b-configure-saas-apps/paste-single-sign-on-URL.png)

9. Azure Portal에서 자동 사용자 프로비전을 구성합니다.

  ![자동 사용자 프로비전 설정](media/active-directory-b2b-configure-saas-apps/set-up-automatic-provisioning.png)

10. 일단 DropBox 응용 프로그램에 프로비전되면 게스트/구성원 사용자는 DropBox에서 별도의 초대를 받게 됩니다. 초대 대상자는 링크를 클릭하여 DropBox에 Single Sign-On을 사용하여 이 초대를 수락해야 합니다.

## <a name="box"></a>Box
이 섹션에서는 사용자가 SAML 프로토콜 기반 페더레이션을 사용하여 Azure AD의 계정으로 Box에 대한 게스트 사용자를 인증할 수 있게 하는 방법을 간략하게 설명합니다. 이 절차의 일부로 Box.com에 메타데이터를 업로드해야 합니다.

1. 엔터프라이즈 응용 프로그램에서 Box 추가

2. Single Sign-On 구성

  ![Box Single Sign-On 구성](media/active-directory-b2b-configure-saas-apps/configure-box-sso.png)

3. 먼저 Azure 관리 포털에서 로그온 URL이 Box에 적합하게 설정되었는지 확인합니다. 이 URL은 Box.com 테넌트의 URL이며 https://.box.com 형식을 따라야 합니다.

4. 식별자는 이 응용 프로그램에 적용할 수 없지만 여전히 필수 필드로 표시됩니다. 이 사실을 알고 있어야 합니다.

5. user.mail에 대한 사용자 식별자(게스트 계정에 대한 SSO 확인용)

6. 새 SAML 인증서를 만듭니다.

7. Azure Active Directory를 ID 공급자로 사용하도록 Box.com 테넌트를 구성하려면 다음 메타데이터 파일을 다운로드하고 컴퓨터에 로컬로 저장합니다. 이제 메타데이터 파일을 다운로드합니다(활성으로 설정함).

8. Box 지원 팀에 해당 메타데이터 파일을 전달합니다. 지원팀은 Single Sign-On을 구성합니다.

9. Azure AD 자동 사용자 프로비전을 설정합니다.

  ![Box에 연결하기 위해 Azure AD에 권한 부여](media/active-directory-b2b-configure-saas-apps/auth-azure-ad-to-connect-to-box.png)

초대 대상자는 Box 응용 프로그램의 초대도 교환해야 합니다.

## <a name="next-steps"></a>다음 단계

Azure AD B2B 공동 작업에 대한 다른 문서 찾아보기:

* [Azure AD B2B 공동 작업이란?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [B2B 공동 작업 사용자 속성](active-directory-b2b-user-properties.md)
* [역할에 B2B 공동 작업 사용자 추가](active-directory-b2b-add-guest-to-role.md)
* [B2B 공동 작업 초대 위임](active-directory-b2b-delegate-invitations.md)
* [동적 그룹 및 B2B 공동 작업](active-directory-b2b-dynamic-groups.md)
* [B2B 공동 작업 코드 및 PowerShell 샘플](active-directory-b2b-code-samples.md)
* [B2B 공동 작업 사용자 토큰](active-directory-b2b-user-token.md)
* [B2B 공동 작업 사용자 클레임 매핑](active-directory-b2b-claims-mapping.md)
* [Office 365 외부 공유](active-directory-b2b-o365-external-user.md)
* [B2B 공동 작업 현재 제한](active-directory-b2b-current-limitations.md)



<!--HONumber=Feb17_HO1-->


