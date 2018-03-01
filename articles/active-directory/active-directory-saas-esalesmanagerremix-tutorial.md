---
title: "자습서: Azure Active Directory와 E Sales Manager Remix 통합 | Microsoft Docs"
description: "Azure Active Directory 및 E Sales Manager Remix 간에 Single Sign-On을 구성하는 방법을 알아봅니다."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 89b5022c-0d5b-4103-9877-ddd32b6e1c02
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2018
ms.author: jeedes
ms.openlocfilehash: 56c2860b6aeac9fdc66f2b1fdd1ed9126bf77d3f
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/22/2018
---
# <a name="tutorial-azure-active-directory-integration-with-e-sales-manager-remix"></a>자습서: Azure Active Directory와 E Sales Manager Remix 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 E Sales Manager Remix를 통합하는 방법을 알아봅니다.

E Sales Manager Remix를 Azure AD와 통합하면 다음과 같은 혜택이 있습니다.

- E Sales Manager Remix에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정으로 E Sales Manager Remix에 자동으로 로그온(Single Sign-On)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 응용 프로그램 액세스 및 Single Sign-On이란 무엇인가요?](active-directory-appssoaccess-whatis.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

E Sales Manager Remix와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- E Sales Manager Remix Single Sign-On이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [1개월 평가판을 얻을](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 E Sales Manager Remix추가
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-e-sales-manager-remix-from-the-gallery"></a>갤러리에서 E Sales Manager Remix추가
E Sales Manager Remix의 Azure AD 통합을 구성하려면 갤러리의 E Sales Manager Remix를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 E Sales Manager Remix를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)**의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Azure Active Directory 단추][1]

2. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 응용 프로그램**으로 이동합니다.

    ![엔터프라이즈 응용 프로그램 블레이드][2]
    
3. 새 응용 프로그램을 추가하려면 대화 상자 맨 위 있는 **새 응용 프로그램** 단추를 클릭합니다.

    ![새 응용 프로그램 단추][3]

4. 검색 상자에 **E Sales Manager Remix**를 입력하고 결과 창에서 **E Sales Manager Remix**를 선택한 다음, **추가** 단추를 클릭하여 응용 프로그램을 추가합니다.

    ![결과 목록의 E Sales Manager Remix](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 “Britta Simon”이라는 테스트 사용자를 기반으로 E Sales Manager Remix에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 E Sales Manager Remix 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 E Sales Manager Remix의 관련 사용자 간에 연결 관계가 형성되어야 합니다.

E Sales Manager Remix에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
3. **[E Sales Manager Remix 테스트 사용자 만들기](#create-an-e-sales-manager-remix-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 E Sales Manager Remix에 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 E Sales Manager Remix 응용 프로그램에서 Single Sign-On을 구성합니다.

**E Sales Manager Remix에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **E Sales Manager Remix** 응용 프로그램 통합 페이지에서 **Single Sign-On**을 클릭합니다.

    ![Single Sign-On 구성 링크][4]

2. **Single Sign-On** 대화 상자에서 **모드**를 **SAML 기반 로그온**으로 선택하여 Single Sign-On을 사용하도록 설정합니다.
 
    ![Single Sign-On 대화 상자](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_samlbase.png)

3. **E Sales Manager Remix 도메인 및 URL** 섹션에서 다음 단계를 수행합니다.

    ![E Sales Manager Remix 도메인 및 URL Single Sign-On 정보](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_url.png)

    a. **로그온 URL** 텍스트 상자에서 다음 패턴으로 URL을 입력합니다. `https://<Server-Based-URL>/<sub-domain>/esales-pc`

    나. **식별자** 텍스트 상자에서 `https://<Server-Based-URL>/<sub-domain>/` 패턴을 사용하여 URL을 입력합니다.

    다. 메모장에 **식별자** 값을 복사합니다. 이 자습서의 뒷부분에서 이 식별자를 사용하게 됩니다.
    
    > [!NOTE] 
    > 이러한 값은 실제 값이 아닙니다. 실제 로그온 URL 및 식별자로 값을 업데이트합니다. 이러한 값을 얻으려면 [E Sales Manager Remix 고객 지원 팀](mailto:esupport@softbrain.co.jp)에 문의하세요.

4. **SAML 서명 인증서** 섹션에서 **인증서(Base64)**를 클릭한 후 컴퓨터에 인증서 파일을 저장합니다.

    ![인증서 다운로드 링크](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_certificate.png) 

5. **기타 모든 사용자 특성 보기 및 편집**을 선택하고 **emailaddress** 특성을 클릭합니다.
    
    ![E Sales Manager Remix 구성](./media/active-directory-saas-esalesmanagerremix-tutorial/configure1.png)

6. 텍스트 상자의 클레임에서 **네임스페이스** 및 **이름** 값을 복사합니다. `<Namespace>/<Name>` 패턴에서 값을 생성합니다. 이 자습서의 뒷부분에서 이 값을 사용할 것입니다.

    ![E Sales Manager Remix 구성](./media/active-directory-saas-esalesmanagerremix-tutorial/configure2.png)

7. **E Sales Manager Remix 구성** 섹션에서 **E Sales Manager Remix 구성**을 클릭하여 **로그온 구성** 창을 엽니다. **빠른 참조 섹션**에서 **로그아웃 URL 및 SAML Single Sign-On 서비스 URL**을 복사합니다.

    ![E Sales Manager Remix 구성](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_configure.png) 

8. **저장** 단추를 클릭합니다.

    ![Single Sign-On 구성 저장 단추](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_400.png)

9. E Sales Manager Remix 응용 프로그램에 관리자로 로그온합니다.

10. 오른쪽 상단 메뉴에서 **관리자 메뉴**를 선택합니다.

    ![E Sales Manager Remix 구성](./media/active-directory-saas-esalesmanagerremix-tutorial/configure4.png)

11. **시스템 설정**>**외부 시스템 협력** 선택

    ![E Sales Manager Remix 구성](./media/active-directory-saas-esalesmanagerremix-tutorial/configure5.png)
    
12. **SAML** 선택

    ![E Sales Manager Remix 구성](./media/active-directory-saas-esalesmanagerremix-tutorial/configure6.png)

13. **SAML 인증 설정** 섹션에서 다음 단계를 수행합니다.

    ![E Sales Manager Remix 구성](./media/active-directory-saas-esalesmanagerremix-tutorial/configure3.png)
    
    a. **PC 버전** 선택
    
    나. 공동 작업 항목 섹션의 드롭다운에서 **이메일**을 선택합니다.

    다. 공동 작업 항목 텍스트 상자에서 Azure Portal에서 복사한 **클레임 값**을 붙여넣습니다. `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    d. **발급자(엔터티 ID)** 텍스트 상자에 **E Sales Manager Remix 도메인 및 URL**에서 복사한 **식별자** 값을 붙여넣습니다.

    e. Azure Portal에서 다운로드한 **인증서**를 업로드하려면 **파일 선택**을 선택합니다.

    f. **ID 공급자 로그인 URL** 텍스트 상자에서 Azure Portal에서 복사한 **SAML Single Sign-On 서비스 URL**을 붙여넣습니다.

    g. **ID 공급자 로그아웃 URL** 텍스트 상자에 Azure Portal에서 복사한 **로그아웃 URL** 값을 붙여넣습니다.

    h. **설정 완료**를 클릭합니다.

> [!TIP]
> 이제 앱을 설정하는 동안 [Azure Portal](https://portal.azure.com) 내에서 이러한 지침의 간결한 버전을 읽을 수 있습니다.  **Active Directory > 엔터프라이즈 응용 프로그램** 섹션에서 이 앱을 추가한 후에는 **Single Sign-On** 탭을 클릭하고 맨 아래에 있는 **구성** 섹션을 통해 포함된 설명서에 액세스하면 됩니다. 포함된 설명서 기능에 대한 자세한 내용은 [Azure AD 포함된 설명서]( https://go.microsoft.com/fwlink/?linkid=845985)에서 확인할 수 있습니다.
> 

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

   ![Azure AD 테스트 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory** 단추를 클릭합니다.

    ![Azure Active Directory 단추](./media/active-directory-saas-esalesmanagerremix-tutorial/create_aaduser_01.png)

2. 사용자 목록을 표시하려면 **사용자 및 그룹**으로 이동한 후 **모든 사용자**를 클릭합니다.

    !["사용자 및 그룹" 및 "모든 사용자" 링크](./media/active-directory-saas-esalesmanagerremix-tutorial/create_aaduser_02.png)

3. **사용자** 대화 상자를 열려면 **모든 사용자** 대화 상자 위쪽에서 **추가**를 클릭합니다.

    ![추가 단추](./media/active-directory-saas-esalesmanagerremix-tutorial/create_aaduser_03.png)

4. **사용자** 대화 상자에서 다음 단계를 수행합니다.

    ![사용자 대화 상자](./media/active-directory-saas-esalesmanagerremix-tutorial/create_aaduser_04.png)

    a. **이름** 상자에 **BrittaSimon**을 입력합니다.

    나. **사용자 이름** 상자에 사용자인 Britta Simon의 전자 메일 주소를 입력합니다.

    다. **암호 표시** 확인란을 선택한 다음 **암호** 상자에 표시된 값을 적어둡니다.

    d. **만들기**를 클릭합니다.
 
### <a name="create-an-e-sales-manager-remix-test-user"></a>E Sales Manager Remix 테스트 사용자 만들기

1. E Sales Manager Remix 응용 프로그램에 관리자로 로그온합니다.

2. 오른쪽 상단 메뉴에서 **관리자 메뉴**를 선택합니다.

    ![E Sales Manager Remix 구성](./media/active-directory-saas-esalesmanagerremix-tutorial/configure4.png)

3. **회사 설정**>**부서 및 직원 유지 관리** 및 **등록된 직원**을 선택합니다.

    ![사용자](./media/active-directory-saas-esalesmanagerremix-tutorial/user1.png)

4. **새 직원 등록** 섹션에서 다음 단계를 수행합니다.
    
    ![사용자](./media/active-directory-saas-esalesmanagerremix-tutorial/user2.png)

    a. **직원 이름** 텍스트 상자에 사용자 이름(예: Britta)을 입력합니다.

    나. 사용자 정보로 모든 해당 필수 필드를 입력합니다.
    
    다. SAML을 사용할 경우 관리자가 로그인 화면에서 로그인할 수 없으므로 **관리자 로그인**을 선택하여 사용자에게 관리자 로그인 권한을 부여합니다.

    d. **등록** 클릭

5. 앞으로 관리자로 로그인하려면 관리자 권한을 받은 사용자로 로그인하고 오른쪽 상단의 메뉴에서 **관리자 메뉴**를 클릭합니다.

    ![사용자](./media/active-directory-saas-esalesmanagerremix-tutorial/configure4.png)

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 E Sales Manager Remix에 대한 액세스 권한을 부여합니다.

![사용자 역할 할당][200] 

**Britta Simon을 E Sales Manager Remix에 할당하려면 다음 단계를 수행합니다.**

1. Azure Portal에서 응용 프로그램 보기를 연 다음 디렉터리 보기로 이동하고 **엔터프라이즈 응용 프로그램**으로 이동한 후 **모든 응용 프로그램**을 클릭합니다.

    ![사용자 할당][201] 

2. 응용 프로그램 목록에서 **E Sales Manager Remix**를 선택합니다.

    ![응용 프로그램 목록의 E Sales Manager Remix 링크](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_app.png)  

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    !["사용자 및 그룹" 링크][202]

4. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창][203]

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다.

6. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.
    
### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 E Sales Manager Remix 타일을 클릭하면 E Sales Manager Remix 응용 프로그램에 자동으로 로그온됩니다.
액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하세요. 

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_203.png

