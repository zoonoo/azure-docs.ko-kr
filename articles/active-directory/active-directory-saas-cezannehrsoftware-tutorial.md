---
title: "자습서: Cezanne HR Software와 Azure Active Directory 통합| Microsoft Docs"
description: "Azure Active Directory 및 Cezanne HR Software 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다."
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
ms.date: 01/25/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 9585494ebff68891d374a29e8e3e4b7756914bcc
ms.openlocfilehash: d8a654340df56002e503f2f61e910facb51696c5


---
# <a name="tutorial-azure-active-directory-integration-with-cezanne-hr-software"></a>자습서: Cezanne HR Software와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Cezanne HR Software를 통합하는 방법에 대해 알아봅니다.

Cezanne HR Software를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- Cezanne HR Software에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정으로 Cezanne HR Software에 자동으로 로그온(Single Sign-on)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure 관리 포털에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On](active-directory-appssoaccess-whatis.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건

Cezanne HR Software와의 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- Cezanne HR Software Single Sign-on이 설정된 구독


> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.


이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 않도록 합니다.
- Azure AD 평가판 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서&1;개월 평가판을 얻을 수 있습니다.


## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 Cezanne HR Software 추가
2. Azure AD Single Sign-on 구성 및 테스트


## <a name="adding-cezanne-hr-software-from-the-gallery"></a>갤러리에서 Cezanne HR Software 추가
Cezanne HR Software의 Azure AD 통합을 구성하려면 갤러리의 Cezanne HR Software를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Cezanne HR Software를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure 관리 포털](https://portal.azure.com)**의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Active Directory][1]

2. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 응용 프로그램**으로 이동합니다.

    ![응용 프로그램][2]
    
3. 대화 상자 위쪽에 있는 **추가** 단추를 클릭합니다.

    ![응용 프로그램][3]

4. 검색 상자에 **Cezanne HR Software**를 입력합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_00001.png)

5. 결과 창에서 **Cezanne HR Software**를 선택한 다음 **추가**를 클릭하여 응용 프로그램을 추가합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD Single Sign-on 구성 및 테스트
이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 Cezanne HR Software에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-on이 작동되려면 Azure AD는 Azure AD의 사용자에 해당하는 Cezanne HR Software의 사용자가 누군지 알고 있어야 합니다. 즉, Azure AD 사용자와 Cezanne HR Software의 관련 사용자 간에 연결이 형성되어야 합니다.

이 연결 관계는 Azure AD의 **사용자 이름** 값을 Cezanne HR Software의 **Username** 값으로 할당하여 설정합니다.

Cezanne HR Software에서 Azure AD Single Sign-on을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#creating-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
3. **[Cezanne HR Software 테스트 사용자 만들기](#creating-a-cezanne-hr-software-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 Cezanne HR Software에 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assigning-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 구성이 작동하는지 확인합니다.

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure 관리 포털에서 Azure AD Single Sign-On을 사용하도록 설정하고 Cezanne HR Software 응용 프로그램에서 Single Sign-On을 구성합니다.

**Cezanne HR Software에서 Azure AD Single Sign-on을 구성하려면 다음 단계를 수행합니다.**

1. Azure 관리 포털의 **Cezanne HR Software** 응용 프로그램 통합 페이지에서 **Single sign-on**을 클릭합니다.

    ![Single Sign-on 구성][4]

2. **Single sign on** 대화 상자 페이지에서 **모드**로 **SAML 기반 로그온**을 선택하여 Single Sign-On을 사용하도록 설정합니다.
 
    ![Single Sign-on 구성](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_01.png)

3. **Cezanne HR Software 도메인 및 URL** 섹션에서 다음 단계를 수행합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_02.png)

    a. **로그온 URL** 텍스트 상자에서 다음 패턴 `https://w3.cezanneondemand.com/cezannehr/-/<tenant id>`을 사용하여 URL을 입력합니다.
    
    b. **식별자** 텍스트 상자에 `https://w3.cezanneondemand.com/CezanneOnDemand/`를 입력합니다.

    > [!NOTE] 
    > 이러한 값은 실제 값이 아닙니다. 실제 로그온 URL 및 식별자로 값을 업데이트해야 합니다. 식별자에는 고유한 URL 값을 사용하는 것이 좋습니다. 이러한 값을 가져오려면 [Cezanne HR Software 지원 팀](mailto:info@cezannehr.com)에 문의합니다.

4. **SAML 서명 인증서** 섹션에서 **새 인증서 만들기**를 클릭합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_03.png)     

5. **새 인증서 만들기** 대화 상자에서 달력 아이콘을 클릭하고 **만료 날짜**를 선택합니다. 그런 후 **저장** 단추를 클릭합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_300.png)

6. **SAML 서명 인증서** 섹션에서 **새 인증서 활성화**를 선택한 후 **저장** 단추를 클릭합니다.

    ![Single Sign-on 구성](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_04.png)

7. 팝업 **롤오버 인증서** 창에서 **확인**을 클릭합니다.

    ![Single Sign-on 구성](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_400.png)

8. **SAML 서명 인증서** 섹션에서 **인증서(base64)**를 클릭한 후 컴퓨터에 인증서 파일을 저장합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_05.png) 

9. **Cezanne HR Software 구성** 섹션에서 **Cezanne HR Software 구성**을 클릭하여 **로그온 구성** 창을 엽니다.

    ![Single Sign-on 구성](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_06.png) 

    ![Single Sign-on 구성](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_07.png)

10. 다른 웹 브라우저 창에서 Cezanne HR Software 테넌트에 관리자로 로그인합니다.

11. 왼쪽 탐색 창에서 **시스템 설정**을 클릭합니다. **보안 설정**으로 이동합니다. 그런 다음 **Single Sign-On 구성**으로 이동합니다.

    ![앱 쪽에서 Single Sign-On 구성](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_000.png)

12. **다음 SSO(Single Sign-On) Service를 사용한 사용자 로그온 허용** 패널에서 **SAML 2.0** 상자를 선택하고 **고급 구성** 옵션을 선택합니다.

    ![앱 쪽에서 Single Sign-On 구성](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_001.png)

13. **새로 추가** 단추를 클릭합니다.

    ![앱 쪽에서 Single Sign-On 구성](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_002.png)

14. **SAML 2.0 ID 공급자** 섹션에서 다음 단계를 수행합니다.

    ![앱 쪽에서 Single Sign-On 구성](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_003.png)

    a. **표시 이름**으로 ID 공급자의 이름을 입력합니다.

    b. **엔터티 식별자** 텍스트 상자에서 Azure AD 응용 프로그램 구성 창의 **SAML 엔터티 ID** 값을 입력합니다.

    c. **SAML 바인딩** 을 'POST'로 변경합니다.

    d. **Security Token Service 끝점** 텍스트 상자에서 Azure AD 응용 프로그램 구성 창의 **SAML Single Sign-On 서비스 URL** 값을 입력합니다.

    e. **사용자 ID 특성 이름** 텍스트 상자에 'http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name'을 입력합니다.

    f. **업로드** 아이콘을 클릭하여 Azure AD에서 다운로드한 인증서를 업로드합니다.

    g. **확인** 단추를 클릭합니다. 

15. **저장** 단추를 클릭합니다.

    ![앱 쪽에서 Single Sign-On 구성](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_004.png)



### <a name="creating-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기
이 섹션의 목적은 Azure 관리 포털에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

![Azure AD 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. **Azure 관리 포털**의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_01.png) 

2. **사용자 및 그룹**으로 이동한 후 **모든 사용자**를 클릭하여 사용자 목록을 표시합니다.
    
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_02.png) 

3. 대화 상자 위쪽에서 **추가**를 클릭하여 **사용자** 대화 상자를 엽니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_03.png) 

4. **사용자** 대화 상자 페이지에서 다음 단계를 수행합니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_04.png) 

    a. **이름** 텍스트 상자에 **BrittaSimon**을 입력합니다.

    b. **사용자 이름** 텍스트 상자에 BrittaSimon의 **전자 메일 주소**를 입력합니다.

    c. **암호 표시**를 선택하고 **암호** 값을 적어둡니다.

    d. **만들기**를 클릭합니다. 



### <a name="creating-a-cezanne-hr-software-test-user"></a>Cezanne HR Software 테스트 사용자 만들기

Azure AD 사용자가 Cezanne HR Software에 로그인 할 수 있도록 하려면 Cezanne HR Software로 프로비전되어야 합니다. Cezanne HR Software의 경우 프로비저닝은 수동 작업입니다.

####<a name="to-provision-a-user-account-perform-the-following-steps"></a>사용자 계정을 프로비저닝하려면 다음 단계를 수행합니다.

1.  Cezanne HR Software 회사 사이트에 관리자 권한으로 로그인합니다.

2.  왼쪽 탐색 창에서 **시스템 설정**을 클릭합니다. **사용자 관리**로 이동합니다. 그런 다음 **새 사용자 추가**로 이동합니다.

    ![새 사용자](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_005.png "새 사용자")

3.  **사람 세부 정보** 섹션에서 다음 단계를 수행합니다.

    ![새 사용자](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_006.png "새 사용자")

    a. **내부 사용자** 를 OFF로 설정합니다.

    b. **이름** 텍스트 상자에 **Britta**를 입력합니다.  

    c. **성** 텍스트 상자에 **Simon**을 입력합니다.

    d. **전자 메일** 텍스트 상자에 Britta Simon 계정의 전자 메일 주소를 입력합니다.

4.  **계정 정보** 섹션에서 다음 단계를 수행합니다.

    ![새 사용자](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_007.png "새 사용자")

    a. **사용자 이름** 텍스트 상자에 Britta Simon의 전자 메일 주소를 입력합니다.

    b. **암호** 텍스트 상자에 Britta Simon 계정의 암호를 입력합니다.

    c. **보안 역할**로 **HR 전문가**를 선택합니다.

    d. **확인**을 클릭합니다.

5. **Single Sign-On** 탭으로 이동하고 **SAML 2.0 식별자** 영역에서 **새로 추가**를 선택합니다.

    ![사용자](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_008.png "사용자")

6. **ID 공급자**에 대해 사용자의 ID 공급자를 선택하고 **사용자 식별자** 텍스트 상자에서 Britta Simon 계정의 전자 메일 주소를 입력합니다.

    ![사용자](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_009.png "사용자")
    
7. **저장** 단추를 클릭합니다.

    ![사용자](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_010.png "사용자")



### <a name="assigning-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Cezanne HR Software에 대한 액세스 권한을 부여합니다.

![사용자 할당][200] 

**Britta Simon을 Cezanne HR Software에 할당하려면 다음 단계를 수행합니다.**

1. Azure 관리 포털에서 응용 프로그램 보기를 열고 디렉터리 보기로 이동하고 **엔터프라이즈 응용 프로그램**으로 이동한 후 **모든 응용 프로그램**을 클릭합니다.

    ![사용자 할당][201] 

2. 응용 프로그램 목록에서 **Cezanne HR Software**를 선택합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_50.png) 

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    ![사용자 할당][202] 

4. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 할당][203]

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다.

6. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.
    


### <a name="testing-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Cezanne HR Software 타일을 클릭하면 Cezanne HR Software 응용 프로그램에 자동으로 로그온됩니다.


## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On이란 무엇입니까?](active-directory-appssoaccess-whatis.md)



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


<!--HONumber=Feb17_HO1-->


