---
title: '자습서: G Suite와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 G Suite 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 38a6ca75-7fd0-4cdc-9b9f-fae080c5a016
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2018
ms.author: jeedes
ms.openlocfilehash: 6974350c6abbc6c5f5a8e10b22e91796e2564b08
ms.sourcegitcommit: f58fc4748053a50c34a56314cf99ec56f33fd616
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/04/2018
ms.locfileid: "48268062"
---
# <a name="tutorial-azure-active-directory-integration-with-g-suite"></a>자습서: Azure Active Directory와 G Suite 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 G Suite를 통합하는 방법에 대해 알아봅니다.

G Suite를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- G Suite에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정으로 G Suite에 자동으로 로그온(Single Sign-on)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 응용 프로그램 액세스 및 Single Sign-On이란 무엇인가요?](../manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

G Suite와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- G Suite Single Sign-on이 설정된 구독
- Google Apps 구독 또는 Google Cloud Platform 구독.

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [1개월 평가판을 얻을](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다.

## <a name="frequently-asked-questions"></a>질문과 대답

1.  **Q: 이 통합이 Google Cloud Platform SSO과 Azure AD의 통합을 지원하나요?**
    
    A: 예. Google Cloud Platform과 Google Apps는 동일한 인증 플랫폼을 공유합니다. 따라서 GCP 통합을 수행하려면 Google Apps로 SSO를 구성해야 합니다.


1. **Q: Chromebooks 및 기타 크롬 장치는 Azure AD Single Sign-On과 호환되나요?**
   
    A: 예, 사용자는 Azure AD 자격 증명을 사용하여 Chromebook 장치에 로그인할 수 있습니다. 사용자가 자격 증명을 두 번 입력해야 하는 이유는 이 [G Suite 지원 문서](https://support.google.com/chrome/a/answer/6060880)를 참조하세요.

1. **Q: Single Sign-On을 사용하도록 설정한 경우 사용자가 자신의 Azure AD 자격 증명을 사용하여 Google 클래스룸, GMail, Google 드라이브, YouTube 등과 같은 Google 제품에 로그인할 수 있나요?**
   
    A: 예. 조직에 대해 어떤 [G Suite](https://support.google.com/a/answer/182442?hl=en&ref_topic=1227583)를 사용하도록 설정할지 또는 사용하지 않도록 설정할지에 따라 다릅니다.

1. **Q: 내 G Suite 사용자의 일부에 대해서에 Single Sign-On을 사용하도록 설정할 수 있나요?**
   
    A: 아니요. Single Sign-On을 켜는 즉시 모든 G Suite 사용자가 Azure AD 자격 증명으로 인증해야 합니다. G Suite는 여러 ID 공급자를 지원하지 않으므로 G Suite 환경의 ID 공급자는 Azure AD 또는 Google 중 하나가 될 수 있지만 동시에 둘 다가 될 수는 없습니다.

1. **Q: 사용자가 Windows를 통해 로그인한 경우 암호 입력을 요청하는 메시지가 표시되지 않고 G Suite에 자동으로 인증되나요?**
   
    A: 이 시나리오에는 두 가지 옵션을 사용할 수 있습니다. 첫째, [Azure Active Directory 조인](../device-management-introduction.md)을 통해 Windows 10 장치에 로그인할 수 있습니다. 또는 [AD FS(Active Directory Federation Services)](../hybrid/plan-connect-user-signin.md) 배포를 통해 Azure AD에 Single Sign-On을 사용할 수 있도록 설정한 온-프레미스 Active Directory에 도메인 가입한 Windows 장치에 로그인할 수 있습니다. 두 가지 옵션 모두 Azure AD와 G Suite 간에 Single Sign-On을 사용하도록 설정하려면 다음 자습서의 단계를 수행해야 합니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 G Suite 추가
1. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-g-suite-from-the-gallery"></a>갤러리에서 G Suite 추가

G Suite의 Azure AD 통합을 구성하려면 갤러리의 G Suite를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 G Suite를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![이미지](./media/google-apps-tutorial/selectazuread.png)

2. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 응용 프로그램**으로 이동합니다.

    ![이미지](./media/google-apps-tutorial/a_select_app.png)
    
3. 새 응용 프로그램을 추가하려면 대화 상자 맨 위 있는 **새 응용 프로그램** 단추를 클릭합니다.

    ![이미지](./media/google-apps-tutorial/a_new_app.png)

4. 검색 상자에 **G Suite**를 입력하고 결과 패널에서 **G Suite**를 선택한 후 **추가** 단추를 클릭하여 응용 프로그램을 추가합니다.

     ![이미지](./media/google-apps-tutorial/a_add_app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 G Suite에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 G Suite 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 G Suite의 관련 사용자 간에 연결이 형성되어야 합니다.

G Suite에서 Azure AD의 **사용자 이름** 값을 **Username** 값으로 할당하여 링크 관계를 설정합니다.

G Suite에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
1. **[G Suite 테스트 사용자 만들기](#create-a-g-suite-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 G Suite에 만듭니다.
1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
1. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 G Suite 응용 프로그램에서 Single Sign-On을 구성합니다.

**G Suite에서 Azure AD Single Sign-on을 구성하려면 다음 단계를 수행합니다.**

1. [Azure Pportal](https://portal.azure.com/)의 **G Suite** 응용 프로그램 통합 페이지에서 **Single Sign-On**을 선택합니다.

    ![이미지](./media/google-apps-tutorial/b1_b2_select_sso.png)

2. 화면 위쪽에서 **Single Sign-On 모드 변경**을 클릭하여 **SAML** 모드를 선택합니다.

      ![이미지](./media/google-apps-tutorial/b1_b2_saml_ssso.png)

3. **Single Sign-On 방법 선택** 대화 상자에서 **SAML** 모드에 대해 **선택**을 클릭하여 Single Sign-On을 사용하도록 설정합니다.

    ![이미지](./media/google-apps-tutorial/b1_b2_saml_sso.png)

4. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 단추를 클릭하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![이미지](./media/google-apps-tutorial/b1-domains_and_urlsedit.png)

5. **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    a. **로그온 URL** 텍스트 상자에서 다음 패턴으로 URL을 입력합니다. `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://mail.google.com`

    b. **식별자** 텍스트 상자에서 다음 패턴을 사용하여 URL을 입력합니다. 
    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `http://google.com` |
    | `http://google.com/a/<yourdomain.com>` |

    ![이미지](./media/google-apps-tutorial/b1-domains_and_urls.png)
 
    > [!NOTE] 
    > 이러한 값은 실제 값이 아닙니다. 실제 로그온 URL 및 식별자로 값을 업데이트합니다. 이러한 값을 얻으려면 [G Suite 클라이언트 지원 팀](https://www.google.com/contact/)에 문의하세요.

6. G Suite 응용 프로그램에는 특정 형식의 SAML 어설션이 필요합니다. 이 응용 프로그램에 대해 다음 클레임을 구성합니다. 응용 프로그램 통합 페이지의 **사용자 특성** 섹션에서 이러한 특성의 값을 관리할 수 있습니다. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 단추를 클릭하여 **사용자 특성** 대화 상자를 엽니다.

    ![이미지](./media/google-apps-tutorial/i3-attribute.png)

7. **사용자 특성** 대화 상자의 **사용자 클레임** 섹션에서 위의 이미지에 표시된 것과 같이 SAML 토큰 특성을 구성하고 다음 단계를 수행합니다.
    
    a. **편집** 단추를 클릭하여 **사용자 클레임 관리** 대화 상자를 엽니다.

    ![이미지](./media/google-apps-tutorial/i2-attribute.png)

    ![이미지](./media/google-apps-tutorial/i4-attribute.png)

    b. **원본 특성** 목록에서 특성 값을 선택합니다.

    다. **저장**을 클릭합니다.

8. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드**를 클릭하여 요구 사항에 따라 적절한 인증서를 다운로드한 다음 컴퓨터에 저장합니다.

    ![이미지](./media/google-apps-tutorial/certificatebase64.png)

9. **G Suite 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    이 URL은 다음과 같을 수 있습니다.

    a. 로그인 URL

    b. Azure AD 식별자

    다. 로그아웃 URL

    ![이미지](./media/google-apps-tutorial/d1_saml.png) 

10. 브라우저에서 새 탭을 열고 관리자 계정을 사용하여 [G Suite 관리 콘솔](http://admin.google.com/) 에 로그인합니다.

11. **보안**을 클릭합니다. 링크가 보이지 않으면 화면 아래쪽에 있는 **기타 컨트롤** 메뉴에 숨겨져 있을 수 있습니다.
   
    ![보안을 클릭합니다.][10]

12. **보안** 페이지에서 **SSO(Single Sign-On) 설정**을 클릭합니다.
   
    ![SSO를 클릭합니다.][11]

13. 다음 구성을 변경합니다.
   
    ![SSL 구성][12]
   
    a. **Setup SSO with third party identity provider**(타사 ID 공급자로 SSO 설정)을 선택합니다.

    b. G Suite의 **로그인 페이지 URL** 필드에 Azure Portal에서 복사한 **Single Sign-On 서비스 URL** 값을 붙여넣습니다.

    다. G Suite의 **로그아웃 페이지 URL** 필드에 Azure Portal에서 복사한 **로그아웃 URL** 값을 붙여넣습니다. 

    d. G Suite의 **암호 변경 URL** 필드에 Azure Portal에서 복사한 **암호 변경 URL** 값을 붙여넣습니다. 

    e. G Suite에서 **확인 인증서**에 대해 Azure Portal에서 다운로드한 인증서를 업로드합니다.

    f. **도메인별 발급자 사용**을 선택합니다.

    g. **변경 내용 저장**을 클릭합니다.

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**를 차례로 선택하고 **모든 사용자**를 선택합니다.

    ![이미지](./media/google-apps-tutorial/d_users_and_groups.png)

2. 화면 위쪽에서 **새 사용자**를 선택합니다.

    ![이미지](./media/google-apps-tutorial/d_adduser.png)

3. 사용자 속성에서 다음 단계를 수행합니다.

    ![이미지](./media/google-apps-tutorial/d_userproperties.png)

    a. **이름** 필드에 **BrittaSimon**을 입력합니다.
  
    b. **사용자 이름** 필드에 **brittasimon@yourcompanydomain.extension**을 입력합니다.  
    예를 들어 BrittaSimon@contoso.com

    다. **속성**을 선택하고 **암호 표시** 확인란을 선택한 다음 암호 상자에 표시된 값을 적어 둡니다.

    d. **만들기**를 선택합니다.
 
### <a name="create-a-g-suite-test-user"></a>G Suite 테스트 사용자 만들기

이 섹션의 목적은 G Suite Software에서 Britta Simon이라는 사용자를 만드는 것입니다. G Suite는 자동 프로비전을 지원하며 기본적으로 사용하도록 설정됩니다. 이 섹션에는 사용자의 작업이 없습니다. G Suite Software에 사용자가 없는 경우 G Suite Software에 액세스하려고 하면 새 사용자가 만들어집니다.

>[!NOTE]
>Single Sign-On을 테스트하기 전에 Azure AD에서 프로비전을 설정하지 않은 경우 G Suite에 사용자가 이미 있는지 확인하세요.

>[!NOTE] 
>사용자를 수동으로 만들어야 하는 경우 [Google 지원 팀](https://www.google.com/contact/)에 문의하세요.



### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 G Suite에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 응용 프로그램**을 선택한 다음, **모든 응용 프로그램**을 선택합니다.

    ![이미지](./media/google-apps-tutorial/d_all_applications.png)

2. 응용 프로그램 목록에서 **G Suite**를 선택합니다.

    ![이미지](./media/google-apps-tutorial/d_all_proapplications.png)

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 선택합니다.

    ![이미지](./media/google-apps-tutorial/d_leftpaneusers.png)

4. **추가** 단추를 선택하고 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![이미지](./media/google-apps-tutorial/d_assign_user.png)

4. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택하고 화면 아래쪽에서 **선택** 단추를 클릭합니다.

5. **할당 추가** 대화 상자에서 **할당** 단추를 선택합니다.
    
### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 G Suite 타일을 클릭하면 G Suite 응용 프로그램에 자동으로 로그온됩니다.
액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](../user-help/active-directory-saas-access-panel-introduction.md)를 참조하세요. 

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[10]: ./media/google-apps-tutorial/gapps-security.png
[11]: ./media/google-apps-tutorial/security-gapps.png
[12]: ./media/google-apps-tutorial/gapps-sso-config.png

