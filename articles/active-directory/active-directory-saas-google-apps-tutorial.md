---
title: "자습서: Google Apps와 Azure Active Directory 통합 | Microsoft 문서"
description: "Azure Active Directory와 Google Apps 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 38a6ca75-7fd0-4cdc-9b9f-fae080c5a016
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/16/2017
ms.author: jeedes
ms.openlocfilehash: 1d92e673a948dd139ff2d4a24f2e602180be43c5
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2017
---
# <a name="tutorial-azure-active-directory-integration-with-google-apps"></a>자습서: Google Apps와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Google Apps를 통합하는 방법에 대해 알아봅니다.

Google Apps를 Azure AD와 통합하면 다음과 같은 이점이 있습니다.

- Google Apps에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 Azure AD 계정으로 Google Apps에 자동으로 로그온(Single Sign-on)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 응용 프로그램 액세스 및 Single Sign-On이란 무엇인가요?](active-directory-appssoaccess-whatis.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

Google Apps와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- Google Apps Single Sign-On이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [1개월 평가판을 얻을](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다.

## <a name="video-tutorial"></a>비디오 자습서
2분 안에 Google Apps에 Single Sign-On을 사용하는 방법

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Enable-single-sign-on-to-Google-Apps-in-2-minutes-with-Azure-AD/player]

## <a name="frequently-asked-questions"></a>질문과 대답
1. **Q: Chromebooks 및 기타 크롬 장치는 Azure AD Single Sign-On과 호환되나요?**
   
    A: 예, 사용자는 Azure AD 자격 증명을 사용하여 Chromebook 장치에 로그인할 수 있습니다. 사용자가 자격 증명을 두 번 입력해야 하는 이유는 이 [Google Apps 지원 문서](https://support.google.com/chrome/a/answer/6060880) 를 참조하세요.

2. **Q: Single Sign-On을 사용하도록 설정한 경우 사용자가 자신의 Azure AD 자격 증명을 사용하여 Google 클래스룸, GMail, Google 드라이브, YouTube 등과 같은 Google 제품에 로그인할 수 있나요?**
   
    A: 예, [Google 앱](https://support.google.com/a/answer/182442?hl=en&ref_topic=1227583) 에 따라 조직에 사용하거나 사용하지 않도록 설정할 수 있습니다.

3. **Q: 내 Google 앱 사용자의 하위 집합에만 Single Sign-On을 사용할 수 있나요?**
   
    A: 아니요, Single Sign-On을 켜는 즉시 모든 Google Apps 사용자가 Azure AD 자격 증명으로 인증해야 합니다. Google 앱은 여러 ID 공급자를 지원하지 않으므로 Google 앱 환경의 ID 공급자는 Azure AD 또는 Google 중 하나가 될 수 있지만 동시에 둘 다가 될 수는 없습니다.

4. **Q: 사용자가 Windows를 통해 로그인한 경우 암호 입력을 요청하는 메시지가 표시되지 않고 Google Apps에 자동으로 인증되나요?**
   
    A: 이 시나리오에는 두 가지 옵션을 사용할 수 있습니다. 첫째, [Azure Active Directory 조인](active-directory-azureadjoin-overview.md)을 통해 Windows 10 장치에 로그인할 수 있습니다. 또는 [AD FS(Active Directory Federation Services)](active-directory-aadconnect-user-signin.md) 배포를 통해 Azure AD에 Single Sign-On을 사용할 수 있도록 설정한 온-프레미스 Active Directory에 도메인 가입한 Windows 장치에 로그인할 수 있습니다. 두 가지 옵션 모두 Azure AD와 Google Apps간에 Single Sign-On을 사용하도록 설정하려면 다음 자습서의 단계를 수행해야 합니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 Google Apps 추가
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-google-apps-from-the-gallery"></a>갤러리에서 Google Apps 추가
Google Apps가 Azure AD에 통합되도록 구성하려면 갤러리에서 Google Apps를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Google Apps를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)**의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Azure Active Directory 단추][1]

2. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 응용 프로그램**으로 이동합니다.

    ![엔터프라이즈 응용 프로그램 블레이드][2]
    
3. 새 응용 프로그램을 추가하려면 대화 상자 맨 위 있는 **새 응용 프로그램** 단추를 클릭합니다.

    ![새 응용 프로그램 단추][3]

4. 검색 상자에 **Google Apps**를 입력하고 결과 패널에서 **Google Apps**를 선택한 다음 **추가** 단추를 클릭하여 응용 프로그램을 추가합니다.

    ![결과 목록의 Google Apps](./media/active-directory-saas-googleapps-tutorial/tutorial_googleapps_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 “Britta Simon”이라는 테스트 사용자를 기반으로 Google Apps에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 Google Apps 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 Google Apps의 관련 사용자 간에 링크 관계가 설정되어야 합니다.

Google Apps에서 Azure AD의 **사용자 이름** 값을 **Username** 값으로 할당하여 연결 관계를 설정합니다.

Google Apps에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
3. **[Google Apps 테스트 사용자 만들기](#create-a-google-apps-test-user)** - Azure AD의 Britta Simon 사용자에 연결된 Google Apps 사용자를 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 Google Apps 응용 프로그램에서 Single Sign-On을 구성합니다.

**Google Apps에서 Azure AD Single Sign-on을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **Google Apps** 응용 프로그램 통합 페이지에서 **Single Sign-On**을 클릭합니다.

    ![Single Sign-On 구성 링크][4]

2. **Single Sign-On** 대화 상자에서 **모드**를 **SAML 기반 로그온**으로 선택하여 Single Sign-On을 사용하도록 설정합니다.
 
    ![Single Sign-On 대화 상자](./media/active-directory-saas-googleapps-tutorial/tutorial_googleapps_samlbase.png)

3. **Google Apps 도메인 및 URL** 섹션에서 다음 단계를 수행합니다.

    ![Google Apps 도메인 및 URL Single Sign-On 정보](./media/active-directory-saas-googleapps-tutorial/tutorial_googleapps_url.png)

    a. **로그온 URL** 텍스트 상자에서 다음 패턴으로 URL을 입력합니다. `https://mail.google.com/a/<yourdomain.com>`

    b. **식별자** 텍스트 상자에서 다음 패턴을 사용하여 URL을 입력합니다.

    | |
    |--|
    | `http://google.com/a/<yourdomain.com>`|
    | `http://google.com`|    
    | `google.com/<yourdomain.com>`|
    | `google.com`|

    > [!NOTE] 
    > 이러한 값은 실제 값이 아닙니다. 실제 로그온 URL 및 식별자로 값을 업데이트합니다. 이러한 값을 얻으려면 [Google Apps 클라이언트 지원 팀](https://www.google.com/contact/)에 문의하세요. 

4. **SAML 서명 인증서** 섹션에서 **인증서**를 클릭한 후 컴퓨터에 인증서 파일을 저장합니다.

    ![인증서 다운로드 링크](./media/active-directory-saas-googleapps-tutorial/tutorial_googleapps_certificate.png) 

5. **저장** 단추를 클릭합니다.

    ![Single Sign-On 구성 저장 단추](./media/active-directory-saas-googleapps-tutorial/tutorial_general_400.png)

6. **Google Apps 구성** 섹션에서 **Google Apps 구성**을 클릭하여 **로그온 구성** 창을 엽니다. **빠른 참조 섹션**에서 **로그아웃 URL, SAML Single Sign-On 서비스 URL 및 암호 변경 URL**을 복사합니다.

    ![Google Apps 구성](./media/active-directory-saas-googleapps-tutorial/tutorial_googleapps_configure.png) 

7. 브라우저에서 새 탭을 열고 관리자 계정을 사용하여 [관리 콘솔](http://admin.google.com/) 에 로그인합니다.

8. **보안**을 클릭합니다. 링크가 보이지 않으면 화면 아래쪽에 있는 **기타 컨트롤** 메뉴에 숨겨져 있을 수 있습니다.
   
    ![보안을 클릭합니다.][10]

9. **보안** 페이지에서 **SSO(Single Sign-On) 설정**을 클릭합니다.
   
    ![SSO를 클릭합니다.][11]

10. 다음 구성을 변경합니다.
   
    ![SSL 구성][12]
   
    a. **Setup SSO with third party identity provider**(타사 ID 공급자로 SSO 설정)을 선택합니다.

    b. Google Apps의 **로그인 페이지 URL** 필드에 Azure Portal에서 복사한 **Single Sign-On 서비스 URL** 값을 붙여넣습니다.

    c. Google Apps의 **로그아웃 페이지 URL** 필드에 Azure Portal에서 복사한 **로그아웃 URL** 값을 붙여넣습니다. 

    d. Google Apps의 **암호 변경 URL** 필드에 Azure Portal에서 복사한 **암호 변경 URL** 값을 붙여넣습니다. 

    e. Google Apps에서 **확인 인증서**에 대해 Azure Portal에서 다운로드한 인증서를 업로드합니다.

    f. **변경 내용 저장**을 클릭합니다.

> [!TIP]
> 이제 앱을 설정하는 동안 [Azure Portal](https://portal.azure.com) 내에서 이러한 지침의 간결한 버전을 읽을 수 있습니다.  **Active Directory > 엔터프라이즈 응용 프로그램** 섹션에서 이 앱을 추가한 후에는 **Single Sign-On** 탭을 클릭하고 맨 아래에 있는 **구성** 섹션을 통해 포함된 설명서에 액세스하면 됩니다. 포함된 설명서 기능에 대한 자세한 내용은 [Azure AD 포함된 설명서]( https://go.microsoft.com/fwlink/?linkid=845985)에서 확인할 수 있습니다.
> 

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

   ![Azure AD 테스트 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory** 단추를 클릭합니다.

    ![Azure Active Directory 단추](./media/active-directory-saas-googleapps-tutorial/create_aaduser_01.png)

2. 사용자 목록을 표시하려면 **사용자 및 그룹**으로 이동한 후 **모든 사용자**를 클릭합니다.

    !["사용자 및 그룹" 및 "모든 사용자" 링크](./media/active-directory-saas-googleapps-tutorial/create_aaduser_02.png)

3. **사용자** 대화 상자를 열려면 **모든 사용자** 대화 상자 위쪽에서 **추가**를 클릭합니다.

    ![추가 단추](./media/active-directory-saas-googleapps-tutorial/create_aaduser_03.png)

4. **사용자** 대화 상자에서 다음 단계를 수행합니다.

    ![사용자 대화 상자](./media/active-directory-saas-googleapps-tutorial/create_aaduser_04.png)

    a. **이름** 상자에 **BrittaSimon**을 입력합니다.

    b. **사용자 이름** 상자에 사용자인 Britta Simon의 전자 메일 주소를 입력합니다.

    c. **암호 표시** 확인란을 선택한 다음 **암호** 상자에 표시된 값을 적어둡니다.

    d. **만들기**를 클릭합니다.
 
### <a name="create-a-google-apps-test-user"></a>Google Apps 테스트 사용자 만들기

이 섹션의 목적은 Google Apps 소프트웨어에서 Britta Simon이라는 사용자를 만드는 것입니다. Google Apps는 자동 프로비전을 지원하며 기본적으로 사용하도록 설정됩니다. 이 섹션에는 사용자의 작업이 없습니다. Google Apps 소프트웨어에 사용자가 없는 경우 Google Apps 소프트웨어에 액세스하려고 하면 새 사용자가 만들어집니다.

>[!NOTE] 
>사용자를 수동으로 만들어야 하는 경우 [Google 지원 팀](https://www.google.com/contact/)에 문의하세요.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Google Apps에 대한 액세스 권한을 부여합니다.

![사용자 역할 할당][200] 

**Britta Simon을 Google Apps에 할당하려면 다음 단계를 수행합니다.**

1. Azure Portal에서 응용 프로그램 보기를 연 다음 디렉터리 보기로 이동하고 **엔터프라이즈 응용 프로그램**으로 이동한 후 **모든 응용 프로그램**을 클릭합니다.

    ![사용자 할당][201] 

2. 응용 프로그램 목록에서 **Google Apps**를 선택합니다.

    ![응용 프로그램 목록의 Google Apps 링크](./media/active-directory-saas-googleapps-tutorial/tutorial_googleapps_app.png)  

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    !["사용자 및 그룹" 링크][202]

4. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창][203]

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다.

6. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.
    
### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Google Apps 타일을 클릭하면 Google Apps 응용 프로그램에 자동으로 로그온됩니다.
액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하세요. 

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On이란 무엇입니까?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-googleapps-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-googleapps-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-googleapps-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-googleapps-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-googleapps-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-googleapps-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-googleapps-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-googleapps-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-googleapps-tutorial/tutorial_general_203.png
[10]: ./media/active-directory-saas-googleapps-tutorial/gapps-security.png
[11]: ./media/active-directory-saas-googleapps-tutorial/security-gapps.png
[12]: ./media/active-directory-saas-googleapps-tutorial/gapps-sso-config.png

