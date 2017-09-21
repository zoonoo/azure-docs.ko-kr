---
title: "자습서: Absorb LMS와 Azure Active Directory 통합 | Microsoft 문서"
description: "Azure Active Directory 및 Absorb LMS 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: ba9f1b3d-a4a0-4ff7-b0e7-428e0ed92142
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2017
ms.author: jeedes
ms.translationtype: HT
ms.sourcegitcommit: 2c6cf0eff812b12ad852e1434e7adf42c5eb7422
ms.openlocfilehash: f5f20fee648cf26b4db49b9a8f5f5e5bcecd453e
ms.contentlocale: ko-kr
ms.lasthandoff: 09/13/2017

---
# <a name="tutorial-azure-active-directory-integration-with-absorb-lms"></a>자습서: Absorb LMS와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Absorb LMS를 통합하는 방법에 대해 알아봅니다.

Absorb LMS를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- Azure AD에서는 Absorb LMS에 대한 액세스 권한이 있는 사용자를 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정으로 Absorb LMS에 자동으로 로그온(Single Sign-On)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On이란 무엇인가요?](active-directory-appssoaccess-whatis.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

Absorb LMS와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- Absorb LMS Single Sign-On이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [1개월 평가판을 얻을](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 Absorb LMS 추가
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-absorb-lms-from-the-gallery"></a>갤러리에서 Absorb LMS 추가
Absorb LMS의 Azure AD 통합을 구성하려면 갤러리의 Absorb LMS를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Absorb LMS를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)**의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Azure Active Directory 단추][1]

2. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 응용 프로그램**으로 이동합니다.

    ![엔터프라이즈 응용 프로그램 블레이드][2]
    
3. 새 응용 프로그램을 추가하려면 대화 상자 맨 위 있는 **새 응용 프로그램** 단추를 클릭합니다.

    ![새 응용 프로그램 단추][3]

4. 검색 상자에 **Absorb LMS**를 입력하고 결과 패널에서 **Absorb LMS**를 선택한 후 **추가** 단추를 클릭하여 응용 프로그램을 추가합니다.

    ![결과 목록의 Absorb LMS](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 Absorb LMS에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 Absorb LMS 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 Absorb LMS의 관련 사용자 간에 연결이 형성되어야 합니다.

이 연결 관계는 Azure AD의 **사용자 이름** 값을 Absorb LMS의 **Username** 값으로 할당하여 설정합니다.

Absorb LMS에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
3. **[Absorb LMS 테스트 사용자 만들기](#create-an-absorb-lms-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 Absorb LMS에 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 Absorb LMS 응용 프로그램에서 Single Sign-On을 구성합니다.

**Absorb LMS에서 Azure AD Single Sign-on을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **Absorb LMS** 응용 프로그램 통합 페이지에서 **Single Sign-On**을 클릭합니다.

    ![Single Sign-On 구성 링크][4]

2. **Single Sign-On** 대화 상자에서 **모드**를 **SAML 기반 로그온**으로 선택하여 Single Sign-On을 사용하도록 설정합니다.
 
    ![Single Sign-On 대화 상자](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_samlbase.png)

3. **Absorb LMS 도메인 및 URL** 섹션에서 다음 단계를 수행합니다.

    ![Absorb LMS 도메인 및 URL Single Sign-On 정보](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_url.png)

    a. **식별자** 텍스트 상자에서 `https://<subdomain>.myabsorb.com/Account/SAML` 패턴을 사용하여 URL을 입력합니다.

    b. **회신 URL** 텍스트 상자에 다음 패턴으로 URL을 입력합니다.`https://<subdomain>.myabsorb.com/Account/SAML`
     
    > [!NOTE] 
    > 이러한 값은 실제 값이 아닙니다. 실제 식별자 및 회신 URL로 해당 값을 업데이트합니다. 이러한 값을 얻으려면 [Absorb LMS 클라이언트 지원 팀](https://www.absorblms.com/support)에 문의하세요. 

4. **SAML 서명 인증서** 섹션에서 **메타데이터 XML**을 클릭한 후 컴퓨터에 메타데이터 파일을 저장합니다.

    ![인증서 다운로드 링크](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_certificate.png) 

6. **저장** 단추를 클릭합니다.

    ![Single Sign-On 구성 저장 단추](./media/active-directory-saas-absorblms-tutorial/tutorial_general_400.png)
    
7. **Absorb LMS 구성** 섹션에서 **Absorb LMS 구성**을 클릭하여 **로그온 구성** 창을 엽니다. **빠른 참조 섹션**에서 **로그아웃 URL**을 복사합니다.

    ![Absorb LMS 구성](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_configure.png) 

8. 다른 웹 브라우저 창에서 Absorb LMS 회사 사이트에 관리자로 로그인합니다.

9. 관리자 인터페이스에서 **계정 아이콘**을 클릭합니다. 

    ![Single Sign-On 구성](./media/active-directory-saas-absorblms-tutorial/1.png)

10. **포털 설정**을 클릭합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-absorblms-tutorial/2.png)
    
11. **사용자** 탭을 클릭합니다.

    ![Single Sign-on 구성](./media/active-directory-saas-absorblms-tutorial/3.png)

12. Single Sign-On 구성 필드에 액세스하려면 다음 단계를 수행합니다.

    ![Single Sign-on 구성](./media/active-directory-saas-absorblms-tutorial/4.png)

    a. **ID 공급자 시작됨**을 선택합니다.

    b. Azure Portal에서 다운로드한 인증서를 메모장에서 열고, **---BEGIN CERTIFICATE---** 및 **---END CERTIFICATE---** 태그를 제거하고, 나머지 내용을 **Key** 텍스트 상자에 붙여넣습니다.
    
    c. **ID 속성**에서, Azure AD에서 사용자 식별자로 구성한 해당 특성을 선택합니다. 예를 들어 Azure AD에서 userprinciplename을 선택한 경우 여기서도 Username을 선택합니다.

    d. Azure Portal 응용 프로그램 **속성** 페이지의 **사용자 액세스 URL**을 **로그인 URL**에 붙여넣습니다.

    e. Azure Portal의 **로그온 구성** 창에서 복사한 **로그아웃 URL** 값을 **로그아웃 URL**에 붙여넣습니다.

13. **‘Only Allow SSO Login’**(SSO 로그인만 허용)을 사용하도록 설정합니다.

    ![Single Sign-on 구성](./media/active-directory-saas-absorblms-tutorial/5.png)

14. **"저장"**을 클릭합니다.

> [!TIP]
> 이제 앱을 설정하는 동안 [Azure Portal](https://portal.azure.com) 내에서 이러한 지침의 간결한 버전을 읽을 수 있습니다.  **Active Directory > 엔터프라이즈 응용 프로그램** 섹션에서 이 앱을 추가한 후에는 **Single Sign-On** 탭을 클릭하고 맨 아래에 있는 **구성** 섹션을 통해 포함된 설명서에 액세스하면 됩니다. 포함된 설명서 기능에 대한 자세한 내용은 [Azure AD 포함된 설명서]( https://go.microsoft.com/fwlink/?linkid=845985)에서 확인할 수 있습니다.

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

![Azure AD 테스트 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. **Azure Portal**의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure Active Directory 단추](./media/active-directory-saas-absorblms-tutorial/create_aaduser_01.png) 

2. 사용자 목록을 표시하려면 **사용자 및 그룹**으로 이동한 후 **모든 사용자**를 클릭합니다.
    
    !["사용자 및 그룹" 및 "모든 사용자" 링크](./media/active-directory-saas-absorblms-tutorial/create_aaduser_02.png) 

3. 대화 상자 위쪽에서 **추가**를 클릭하여 **사용자** 대화 상자를 엽니다.
 
    ![추가 단추](./media/active-directory-saas-absorblms-tutorial/create_aaduser_03.png) 

4. **사용자** 대화 상자 페이지에서 다음 단계를 수행합니다.
 
    ![사용자 대화 상자](./media/active-directory-saas-absorblms-tutorial/create_aaduser_04.png) 

    a. **이름** 텍스트 상자에 **BrittaSimon**을 입력합니다.

    b. **사용자 이름** 텍스트 상자에 BrittaSimon의 **전자 메일 주소**를 입력합니다.

    c. **암호 표시**를 선택하고 **암호** 값을 적어둡니다.

    d. **만들기**를 클릭합니다.

### <a name="create-an-absorb-lms-test-user"></a>Absorb LMS 테스트 사용자 만들기

Azure AD 사용자가 Absorb LMS에 로그인할 수 있도록 하려면 Absorb LMS로 프로비전되어야 합니다.  
Absorb LMS의 경우 프로비전은 수동 작업입니다.

**사용자 계정을 프로비전하려면 다음 단계를 수행합니다.**

1. Absorb LMS 회사 사이트에 관리자 권한으로 로그인합니다.

2. **사용자** 탭을 클릭합니다.

    ![피플 초대](./media/active-directory-saas-absorblms-tutorial/absorblms_users.png)

3. **사용자** 탭에서 **사용자**를 클릭합니다.

    ![피플 초대](./media/active-directory-saas-absorblms-tutorial/absorblms_userssub.png)

4.  **새로 추가** 드롭다운에서 **사용자**를 선택합니다.

    ![피플 초대](./media/active-directory-saas-absorblms-tutorial/absorblms_createuser.png)

5. **사용자 추가** 페이지에서 다음 단계를 수행합니다.

    ![피플 초대](./media/active-directory-saas-absorblms-tutorial/user.png)

    a. **이름** 텍스트 상자에 이름(예: Britta)을 입력합니다.

    b. **성** 텍스트 상자에 성(예: Simon)을 입력합니다.
    
    c. **사용자 이름** 텍스트 상자에 사용자 이름(예: Britta Simon)을 입력합니다.

    d. **암호** 텍스트 상자에 Britta Simon의 암호를 입력합니다.

    e. **암호 확인** 텍스트 상자에 동일한 암호를 입력합니다.
    
    f. **활성**으로 설정합니다.   

6. **"저장"**을 클릭합니다.
 
### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Absorb LMS에 대한 액세스 권한을 부여합니다.

![사용자 역할 할당][200]

**Britta Simon을 Absorb LMS에 할당하려면 다음 단계를 수행합니다.**

1. Azure Portal에서 응용 프로그램 보기를 연 다음 디렉터리 보기로 이동하고 **엔터프라이즈 응용 프로그램**으로 이동한 후 **모든 응용 프로그램**을 클릭합니다.

    ![사용자 할당][201] 

2. 응용 프로그램 목록에서 **Absorb LMS**를 선택합니다.

    ![응용 프로그램 목록의 Absorb LMS 링크](./media/active-directory-saas-absorblms-tutorial/tutorial_absorblms_app.png) 

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    !["사용자 및 그룹" 링크][202] 

4. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창][203]

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다.

6. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.
    
### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Absorb LMS 타일을 클릭하면 Absorb LMS 응용 프로그램에 자동으로 로그온됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://msdn.microsoft.com/library/dn308586)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On이란 무엇입니까?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-absorblms-tutorial/tutorial_general_203.png


