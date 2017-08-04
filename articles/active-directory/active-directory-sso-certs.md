---
title: "Azure AD | Microsoft Docs에서 페더레이션 인증서를 관리하는 방법"
description: "페더레이션 인증서에 대한 만료 날짜를 사용자 지정하는 방법 및 곧 만료되는 인증서를 갱신하는 방법에 대해 알아봅니다."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: f516f7f0-b25a-4901-8247-f5964666ce23
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/09/2017
ms.author: jeedes
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 4b859d631617fc3d375711876cf23e201662e43a
ms.contentlocale: ko-kr
ms.lasthandoff: 07/21/2017

---
# <a name="managing-certificates-for-federated-single-sign-on-in-azure-active-directory"></a>Azure Active Directory에서 페더레이션된 Single Sign-On에 대한 인증서 관리
이 문서에서는 Azure Active Directory가 SaaS 응용 프로그램에 페더레이션된 SSO(Single Sign-On)를 설정하기 위해 만드는 인증서와 관련된 일반적인 질문과 정보를 다룹니다. 이러한 응용 프로그램은 Azure AD 앱 갤러리에서 또는 비갤러리 응용 프로그램 템플릿을 사용하여 추가할 수 있습니다. 응용 프로그램은 페더레이션된 Single Sign-On 옵션을 사용하여 구성되어야 합니다.

이 문서는 아래 예에 표시된 것처럼 **SAML 페더레이션**을 사용하여 **Azure AD Single Sign-On**을 사용하도록 구성된 앱에만 해당됩니다.

![Azure AD Single Sign-On](./media/active-directory-sso-certs/saml_sso.PNG)

## <a name="auto-generated-certificate-for-gallery-and-non-gallery-applications"></a>갤러리 및 비갤러리 응용 프로그램에 대해 자동 생성된 인증서
갤러리에서 새 응용 프로그램을 추가하고 SAML 기반 로그온을 구성하면 Azure AD는 응용 프로그램에 대해 3년간 유효한 인증서를 생성합니다. 이 인증서는 **SAML 서명 인증서** 섹션에서 다운로드할 수 있습니다. 갤러리 응용 프로그램의 경우 이 섹션에 응용 프로그램의 요구 사항에 따라 **인증서** 또는 **페더레이션 메타데이터**를 다운로드하는 옵션이 표시될 수 있습니다.

![Azure AD Single Sign-On](./media/active-directory-sso-certs/saml_certificate_download.png)

## <a name="how-to-customize-the-expiration-date-for-your-federation-certificate-and-roll-over-the-new-certificate"></a>페더레이션 인증서의 만료 날짜를 사용자 지정하고 새 인증서를 롤오버하는 방법
기본적으로 인증서는 3년 후에 만료되도록 설정됩니다. 아래 단계에 따라 인증서에 다른 만료 날짜를 선택할 수 있습니다. 포함된 스크린샷은 Salesforce를 예로 사용하지만 이러한 단계는 모든 페더레이션된 SaaS 앱에 적용할 수 있습니다.

1. [Azure Active Directory](https://aad.portal.azure.com) 관리 포털에서 **엔터프라이즈 응용 프로그램** 탐색 창을 클릭한 다음 아래와 같이 **개요** 페이지에서 **새 응용 프로그램**을 클릭합니다.

   ![SSO 구성 마법사를 엽니다.](./media/active-directory-sso-certs/enterprise_application_new_application.png)

2. 갤러리 응용 프로그램을 검색하여 추가할 응용 프로그램을 선택합니다. 필요한 응용 프로그램을 찾을 수 없으면 **비갤러리 응용 프로그램** 옵션을 사용하여 응용 프로그램을 추가합니다. 이 기능은 **Azure AD Premium(P1 및 P2)** SKU에서만 사용할 수 있습니다.

    ![Azure AD Single Sign-On](./media/active-directory-sso-certs/add_gallery_application.png)

3. 왼쪽 탐색 창에서 **Single Sign-On** 링크를 클릭하고 **Single Sign-on 모드**를 **SAML 기반 로그인**으로 변경합니다. 그러면 응용 프로그램에 대해 3년간 유효한 인증서가 생성됩니다.

4. 새 인증서를 만들려면 **SAML 서명 인증서** 섹션에서 **새 인증서 만들기** 링크를 클릭합니다.

    ![새 인증서 생성](./media/active-directory-sso-certs/create_new_certficate.png)

5. 새 인증서 만들기 링크에서 일정 제어가 열리면 원하는 날짜와 시간을 최대 3년까지 설정합니다. 선택한 날짜와 시간은 새 인증서의 만료 날짜와 시간입니다. **저장** 단추를 클릭하여 인증서를 저장합니다.

    ![다운로드한 다음 인증서 업로드](./media/active-directory-sso-certs/certifcate_date_selection.PNG)

6. 이제 새 인증서를 다운로드할 수 있습니다. 다운로드하려면 **인증서** 링크를 누릅니다. 이 시점에서 인증서는 활성 상태가 아닙니다. 해당 시점에 인증서를 롤오버하려면 **Make new certificate active**(새 인증서를 활성으로 만들기) 확인란을 클릭하고 저장을 클릭합니다. 이후로 Azure AD는 새 인증서를 사용하여 응답하기 시작합니다.

7.  특정 SaaS 응용 프로그램에 인증서를 업로드하는 방법을 알아보려면 페이지에서 View application configuration tutorial(응용 프로그램 구성 자습서 보기) 링크를 클릭합니다.

## <a name="how-to-renew-a-certificate-that-will-soon-expire"></a>곧 만료되는 인증서를 갱신하는 방법
아래에 표시된 갱신 단계를 수행하면 가동 중지 시간이 발생하지 않습니다. 이 섹션에 사용된 스크린샷은 Salesforce를 예로 사용하지만, 이러한 단계를 페더레이션된 모든 SaaS 앱에 적용할 수 있습니다.

1. **Azure Active Directory** 응용 프로그램 **Single Sign-On** 페이지에서 응용 프로그램의 새 인증서를 생성합니다. **SAML 서명 인증서** 섹션에서 **새 인증서 만들기** 링크를 클릭하면 이 작업을 수행할 수 있습니다.

    ![새 인증서 생성](./media/active-directory-sso-certs/create_new_certficate.png)

2. 새 인증서에 대해 원하는 만료 날짜와 시간을 선택하고 **저장** 단추를 클릭합니다.

3. SAML 서명 인증서 옵션에서 인증서를 다운로드합니다. SaaS 앱의 Single Sign-On 구성 화면으로 새 인증서를 업로드합니다. 특정 SaaS 응용 프로그램에 대해 이 작업을 수행하는 방법을 알아보려면 페이지에서 **View application configuration tutorial**(응용 프로그램 구성 자습서 보기) 링크를 클릭합니다.
   
4. Azure AD에서 새 인증서를 활성화하려면 Make new certificate active(새 인증서를 활성으로 만들기) 확인란을 선택하고 페이지 맨 위에서 **저장** 단추를 클릭합니다. 그러면 Azure AD 쪽에서 새 인증서를 롤백합니다. 인증서의 상태가 **신규**에서 **활성**으로 변경됩니다. 이후로 Azure AD는 새 인증서를 사용하여 응답하기 시작합니다. 
   
    ![새 인증서 생성](./media/active-directory-sso-certs/new_certificate_download.png)

## <a name="related-articles"></a>관련 문서
* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory의 응용 프로그램 관리를 위한 문서 인덱스](active-directory-apps-index.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On](active-directory-appssoaccess-whatis.md)
* [SAML 기반 Single Sign-On 문제 해결](active-directory-saml-debugging.md)
