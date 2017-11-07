---
title: "자습서: Cezanne HR 소프트웨어와 Azure Active Directory 통합| Microsoft Docs"
description: "Azure Active Directory 및 Cezanne HR 소프트웨어 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: fb8f95bf-c3c1-4e1f-b2b3-3b67526be72d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: jeedes
ms.openlocfilehash: 623c438edfce5f98c2d32d8bb25a97d86aa77909
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-integrate-azure-active-directory-with-cezanne-hr-software"></a>자습서: Cezanne HR 소프트웨어와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Cezanne HR 소프트웨어를 통합하는 방법에 대해 알아봅니다.

Cezanne HR 소프트웨어를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다. 다음을 수행할 수 있습니다.

- Cezanne HR 소프트웨어에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어합니다.
- 사용자가 해당 Azure AD 계정으로 Cezanne HR 소프트웨어에 자동으로 SSO(Single Sign-On)되도록 설정합니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

Azure AD와의 SaaS(Software as a Service) 앱 통합에 대해 자세히 알아보려면 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 SSO](active-directory-appssoaccess-whatis.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

Cezanne HR 소프트웨어와의 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- Cezanne HR 소프트웨어 SSO가 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하려면 프로덕션 환경을 사용하지 않는 것이 좋습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 따릅니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [1개월 평가판을 얻을](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD SSO를 테스트합니다. 

이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

* 갤러리에서 Cezanne HR 소프트웨어 추가
* Azure AD SSO 구성 및 테스트

## <a name="add-cezanne-hr-software-from-the-gallery"></a>갤러리에서 Cezanne HR 소프트웨어 추가
Cezanne HR 소프트웨어의 Azure AD 통합을 구성하려면 갤러리의 Cezanne HR 소프트웨어를 관리되는 SaaS 앱 목록에 추가합니다.

갤러리에서 Cezanne HR 소프트웨어를 추가하려면 다음을 수행합니다.

1. **[Azure Portal](https://portal.azure.com)**의 왼쪽 창에서 **Azure Active Directory** 단추를 선택합니다. 

    !["Azure Active Directory" 단추][1]

2. **엔터프라이즈 응용 프로그램** > **모든 응용 프로그램**을 선택합니다.

    !["모든 응용 프로그램" 링크][2]
    
3. 새 응용 프로그램을 추가하려면 **모든 응용 프로그램** 대화 상자 맨 위 있는 **새 응용 프로그램**을 선택합니다.

    !["새 응용 프로그램" 단추][3]

4. 검색 상자에 **Cezanne HR Software**를 입력합니다.

    ![검색 상자](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_search.png)

5. 결과 목록에서 **Cezanne HR Software**를 선택한 다음 **추가** 단추를 선택하여 응용 프로그램을 추가합니다.

    ![결과 목록](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트
이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 Cezanne HR 소프트웨어에서 Azure AD SSO를 구성하고 테스트합니다.

SSO가 작동되려면 Azure AD는 Azure AD 사용자에 해당하는 Cezanne HR 소프트웨어의 사용자가 누군지 알고 있어야 합니다. 즉, Azure AD 사용자와 Cezanne HR 소프트웨어의 관련 사용자 간에 연결을 형성해야 합니다.

연결을 형성하려면 Cezanne HR 소프트웨어 **사용자 이름** 값을 Azure AD **Username** 값으로 할당합니다.

Cezanne HR 소프트웨어를 사용하여 Azure AD SSO를 구성하고 테스트하려면 다음 구성 요소를 완료합니다.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

이 섹션에서는 Azure Portal에서 Azure AD SSO를 사용하도록 설정하고 다음을 수행하여 Cezanne HR 소프트웨어 응용 프로그램에서 SSO를 구성할 수 있습니다.

1. Azure Portal의 **Cezanne HR Software** 응용 프로그램 통합 페이지에서 **Single sign-on**을 선택합니다.

    !["Single Sign-On" 명령][4]

2. SSO를 사용하도록 설정하려면 **Single Sign-On** 대화 상자에서 **모드**를 **SAML 기반 로그온**으로 선택합니다.
 
    !["모드" 상자](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_samlbase.png)

3. **Cezanne HR Software 도메인 및 URL** 아래에서 다음을 수행합니다.

    !["Cezanne HR Software 도메인 및 URL" 섹션](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_url.png)

    a. **로그온 URL** 상자에 다음 `https://w3.cezanneondemand.com/cezannehr/-/<tenant id>` 구문이 있는 URL을 입력합니다.

    b. **회신 URL** 상자에 다음 `https://w3.cezanneondemand.com:443/<tenantid>` 구문이 있는 URL을 입력합니다.    
     
    > [!NOTE] 
    > 위의 값은 실제가 아닙니다. 실제 회신 URL 및 로그온 URL로 업데이트합니다. 해당 값을 가져오려면 [Cezanne HR 소프트웨어 클라이언트 지원 팀](mailto:info@cezannehr.com)에 문의합니다.

4. **SAML 서명 인증서** 아래에서 **인증서(Base64)**를 선택한 후 사용자의 컴퓨터에 인증서 파일을 저장합니다.

    !["SAML 서명 인증서" 섹션](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_certificate.png) 

5. **저장**을 선택합니다.

    !["저장" 단추](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_400.png)
    
6. **Cezanne HR Software 구성** 아래에서 **Cezanne HR Software 구성**을 선택하여 **로그온 구성** 창을 엽니다. **빠른 참조** 섹션에서 **SAML 엔터티 ID** 및 **SAML Single Sign-On 서비스** URL을 복사합니다.

    !["Cezanne HR Software 구성" 섹션](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_configure.png) 

7. 다른 웹 브라우저 창에서 Cezanne HR 소프트웨어 테넌트에 관리자로 로그인합니다.

8. 왼쪽 창에서 **시스템 설정**을 선택합니다. **보안 설정** > **Single Sign-on 구성**을 선택합니다.

    !["Single Sign-On 구성" 링크](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_000.png)

9. **다음 SSO(Single Sign-On) 서비스를 사용한 사용자 로그온 허용** 창에서 **SAML 2.0** 확인란을 선택하고 **고급 구성** 옵션을 선택합니다.

    ![Single Sign-On 서비스 옵션](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_001.png)

10. **새로 추가**를 선택합니다.

    !["새로 추가" 단추](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_002.png)

11. **SAML 2.0 ID 공급자** 아래에서 다음을 수행합니다.

    !["SAML 2.0 ID 공급자" 섹션](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_003.png)
    
    a. **표시 이름** 상자에 ID 공급자의 이름을 입력합니다.

    b. **엔터티 식별자** 상자에 Azure Portal에서 복사한 **SAML 엔터티 ID**를 붙여 넣습니다. 

    c. **SAML 바인딩** 목록 상자에서 **POST**를 선택합니다.

    d. **보안 토큰 서비스 끝점** 상자에 Azure Portal에서 복사한 **SAML Single Sign-On 서비스** URL을 붙여 넣습니다. 
    
    e. **사용자 ID 특성 이름** 상자에 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`을 입력합니다.
    
    f. Azure AD에서 다운로드한 인증서를 업로드하려면 **업로드** 버튼을 선택합니다.
    
    g. **확인**을 선택합니다. 

12. **저장**을 선택합니다.

    !["저장" 단추](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_004.png)

> [!TIP]
> 앱을 설정할 때 [Azure Portal](https://portal.azure.com)에서 이전 지침의 간결한 버전을 읽을 수 있습니다. **Active Directory** > **엔터프라이즈 응용 프로그램** 섹션에서 앱을 추가한 후 **Single Sign-On** 탭을 선택합니다. 그런 다음 **구성** 섹션에서 포함된 설명서에 액세스합니다. 

포함된 설명서 기능에 대한 자세한 내용은 [Azure AD 포함된 설명서]( https://go.microsoft.com/fwlink/?linkid=845985)를 참조하세요.
> 

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기
이 섹션에서는 Azure Portal에서 테스트 사용자인 Britta Simon을 만듭니다.

![테스트 사용자 Britta Simon][100]

Azure AD에서 테스트 사용자를 만들려면 다음을 수행합니다.

1. **Azure Portal**의 왼쪽 창에서 **Azure Active Directory** 단추를 선택합니다.

    !["Azure Active Directory" 단추](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_01.png) 

2. 사용자 목록을 표시하려면 **사용자 및 그룹** > **모든 사용자**를 선택합니다.
    
    !["모든 사용자" 링크](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_02.png) 
    
    **모든 사용자** 대화 상자가 열립니다.

3. **사용자** 대화 상자를 열려면 **추가**를 선택합니다.
 
    ![“추가” 단추](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_03.png) 

4. **사용자** 대화 상자에서 다음을 수행합니다.
 
    !["사용자" 대화 상자](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_04.png) 

    a. **이름** 상자에 **BrittaSimon**을 입력합니다.

    b. **사용자 이름** 상자에 사용자인 Britta Simon의 **이메일 주소**를 입력합니다.

    c. **암호 표시** 확인란을 선택한 다음 **암호** 상자에서 생성된 값을 적어둡니다.

    d. **만들기**를 선택합니다.
 
### <a name="create-a-cezanne-hr-software-test-user"></a>Cezanne HR 소프트웨어 테스트 사용자 만들기

Azure AD 사용자가 Cezanne HR 소프트웨어에 로그인할 수 있도록 하려면 Cezanne HR 소프트웨어로 프로비전되어야 합니다. Cezanne HR 소프트웨어의 경우 프로비저닝은 수동 작업입니다.

다음을 수행하여 사용자 계정을 프로비전합니다.

1.  Cezanne HR 소프트웨어 회사 사이트에 관리자 권한으로 로그인합니다.

2.  왼쪽 창에서 **시스템 설정** > **사용자 관리** > **새 사용자 추가**를 선택합니다.

    !["새 사용자 추가" 링크](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_005.png "새 사용자")

3.  **사용자 세부 정보** 아래에서 다음을 수행합니다.

    !["사용자 세부 정보" 섹션](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_006.png "새 사용자")
    
    a. **내부 사용자**를 **OFF**로 설정합니다.
    
    b. **이름** 상자에 사용자의 이름, 예를 들어 **Britta**를 입력합니다.  
 
    c. **성** 상자에 사용자의 성, 예를 들어 **Simon**을 입력합니다.
    
    d. **전자 메일** 상자에 사용자의 전자 메일 주소, 예를 들어 Brittasimon@contoso.com을 입력합니다.

4.  **계정 정보** 아래에서 다음을 수행합니다.

    !["계정 정보" 섹션](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_007.png "새 사용자")
    
    a. **Username** 상자에 사용자의 이메일 주소, 예를 들어 Brittasimon@contoso.com을 입력합니다.
    
    b. **암호** 상자에 사용자의 암호를 입력합니다.
    
    c. **보안 역할** 상자에 **HR 전문가**를 선택합니다.
    
    d. **확인**을 선택합니다.

5. **Single Sign-On** 탭의 **SAML 2.0 식별자** 섹션에서 **새로 추가**를 선택합니다.

    !["새로 추가" 단추](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_008.png "사용자")

6. **ID 공급자** 목록 상자에 ID 공급자를 선택합니다. **사용자 ID** 상자에 테스트 사용자인 Britta Simon의 계정에 대한 이메일 주소를 입력합니다.

    !["ID 공급자" 및 "사용자 ID" 상자](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_009.png "사용자")
    
7. **저장**을 선택합니다.

    !["저장" 단추](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_010.png "사용자")

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure SSO를 사용할 수 있도록 테스트 사용자인 Britta Simon에게 Cezanne HR 소프트웨어에 대한 액세스 권한을 부여합니다.

![테스트 사용자 액세스][200] 

1. Azure Portal에서 응용 프로그램 보기를 연 다음 디렉터리 보기로 이동합니다. **엔터프라이즈 응용 프로그램** > **모든 응용 프로그램**을 선택합니다.

    !["모든 응용 프로그램" 링크][201] 

2. 응용 프로그램 목록에서 **Cezanne HR Software**를 선택합니다.

    !["응용 프로그램" 목록](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_app.png) 

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 할당][202] 

4. **추가**를 선택합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    !["사용자 및 그룹" 링크][203]

5. **사용자 및 그룹** 대화 상자의 **사용자 목록**에서 **Britta Simon**을 선택합니다.

6. **사용자 및 그룹** 대화 상자에서 **선택**을 선택합니다.

7. **할당 추가** 대화 상자에서 **할당**을 선택합니다.
    
### <a name="test-sso"></a>SSO 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD SSO 구성을 테스트합니다.

액세스 패널에서 Cezanne HR 소프트웨어 타일을 선택하면 Cezanne HR 소프트웨어 응용 프로그램에 자동으로 로그온됩니다.

## <a name="next-steps"></a>다음 단계

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 SSO란 무엇입니까?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_203.png

