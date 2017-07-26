---
title: "자습서: SAP Business Object Cloud와 Azure Active Directory 통합 | Microsoft Docs"
description: "Azure Active Directory 및 SAP Business Object Cloud 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 6c5e44f0-4e52-463f-b879-834d80a55cdf
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: ef1e603ea7759af76db595d95171cdbe1c995598
ms.openlocfilehash: 85723eeb9c030dc51bdad47ea8e0996ff7868336
ms.contentlocale: ko-kr
ms.lasthandoff: 06/16/2017

---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-object-cloud"></a>자습서: SAP Business Object Cloud와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 SAP Business Object Cloud를 통합하는 방법에 대해 알아봅니다.

SAP Business Object Cloud를 Azure AD와 통합하면 다음과 같은 이점이 있습니다.

- SAP Business Object Cloud에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정으로 SAP Business Object Cloud에 자동으로 로그온(Single Sign-On)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 응용 프로그램 액세스 및 Single Sign-On이란 무엇인가요?](active-directory-appssoaccess-whatis.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

SAP Business Object Cloud와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- SAP Business Object Cloud Single Sign-On이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 얻을 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 SAP Business Object Cloud 추가
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-sap-business-object-cloud-from-the-gallery"></a>갤러리에서 SAP Business Object Cloud 추가
SAP Business Object Cloud의 Azure AD 통합을 구성하려면 갤러리의 SAP Business Object Cloud를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 SAP Business Object Cloud를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)**의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Active Directory][1]

2. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 응용 프로그램**으로 이동합니다.

    ![응용 프로그램][2]
    
3. 새 응용 프로그램을 추가하려면 대화 상자 맨 위 있는 **새 응용 프로그램** 단추를 클릭합니다.

    ![응용 프로그램][3]

4. 검색 상자에 **SAP Business Object Cloud**를 입력합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_search.png)

5. 결과 창에서 **SAP Business Object Cloud**를 선택하고 **추가** 단추를 클릭하여 해당 응용 프로그램을 추가합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD Single Sign-on 구성 및 테스트
이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 SAP Business Object Cloud에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 SAP Business Object Cloud 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 SAP Business Object Cloud의 관련 사용자 간에 연결 관계가 형성되어야 합니다.

SAP Business Object Cloud에서 Azure AD의 **사용자 이름** 값을 **Username** 값으로 할당하여 연결 관계를 설정합니다.

SAP Business Object Cloud에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configuring-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#creating-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
3. **[SAP Business Object Cloud 테스트 사용자 만들기](#creating-an-sap-business-object-cloud-test-user)** - Azure AD를 대표하여 SAP Business Object Cloud에서 Britta Simon에 해당하는 사용자가 있어야 합니다.
4. **[Azure AD 테스트 사용자 할당](#assigning-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 구성이 작동하는지 확인합니다.

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 SAP Business Object Cloud 응용 프로그램에서 Single Sign-On을 구성합니다.

**SAP Business Object Cloud에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **SAP Business Object Cloud** 응용 프로그램 통합 페이지에서 **Single Sign-On**을 클릭합니다.

    ![Single Sign-on 구성][4]

2. **Single Sign-On** 대화 상자에서 **모드**를 **SAML 기반 로그온**으로 선택하여 Single Sign-On을 사용하도록 설정합니다.
 
    ![Single Sign-on 구성](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_samlbase.png)

3. **SAP Business Object Cloud 도메인 및 URL** 섹션에서 다음 단계를 수행합니다.

    ![Single Sign-on 구성](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_url.png)

    a. **로그온 URL** 텍스트 상자에서 다음 패턴으로 URL을 입력합니다. `https://<sub-domain>.projectorca.cloud/`

    b. **식별자** 텍스트 상자에서 `<sub-domain>.projectorca.cloud` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE] 
    > 이러한 값은 실제 값이 아닙니다. 실제 로그온 URL 및 식별자로 값을 업데이트합니다. 식별자가 관리 콘솔에서 SAP Business Object Cloud 메타데이터를 다운로드하게 하려면 [SAP Business Object Cloud 클라이언트 지원 팀](https://www.sap.com/product/analytics/cloud-analytics.support.html)에 문의하여 로그온 URL을 얻어야 하며, 관리 콘솔은 이 자습서의 후반부에서 설명합니다. 
 
4. **SAML 서명 인증서** 섹션에서 **메타데이터 XML**을 클릭한 후 컴퓨터에 메타데이터 파일을 저장합니다.

    ![Single Sign-on 구성](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_certificate.png) 

5. **저장** 단추를 클릭합니다.

    ![Single Sign-on 구성](./media/active-directory-saas-sapboc-tutorial/tutorial_general_400.png)

6. 다른 웹 브라우저 창에서 SAP Business Object Cloud 회사 사이트에 관리자로 로그인합니다.

7. **메뉴 > 시스템 > 관리**로 이동합니다.
    
    ![Single Sign-on 구성](./media/active-directory-saas-sapboc-tutorial/config1.png)    
8. **보안** 탭에서 **[편집 – 펜 아이콘]**을 클릭합니다.
    
    ![Single Sign-on 구성](./media/active-directory-saas-sapboc-tutorial/config2.png)    
9. 인증 방법으로 **SAML SSO(Single Sign-On)**를 선택합니다.

    ![Single Sign-on 구성](./media/active-directory-saas-sapboc-tutorial/config3.png)  

10. **다운로드**를 클릭하여 서비스 공급자 메타데이터를 다운로드합니다. 파일에서 **entityID** 값을 검색하여 Azure Portal **SAP Business Object Cloud 도메인 및 URL** 섹션의 **식별자** 텍스트 상자에 붙여넣습니다.

    ![Single Sign-on 구성](./media/active-directory-saas-sapboc-tutorial/config4.png)  

11. **ID 공급자 메타데이터 업로드** 단계에서 **업로드..** 단추를 클릭하여 Azure Portal에서 다운로드한 메타데이터 파일을 업로드합니다. 

    ![Single Sign-on 구성](./media/active-directory-saas-sapboc-tutorial/config5.png)

12. 목록에서 구현에 사용할 적절한 **사용자 특성**을 선택하여 ID 공급자에 매핑합니다. "사용자 지정 SAML 매핑" 옵션을 사용하여 사용자 페이지에 사용자 지정 특성을 입력하거나 사용자 특성으로 "이메일" 또는 "사용자 ID"를 선택합니다. 예: 이 자습서에서 사용자 ID 클레임을 Azure Portal **"사용자 특성"** 섹션의 userprincipalname 특성에 매핑했으므로 여기서는 **이메일**을 선택합니다. 이렇게 하면 고유의 사용자 이메일이 제공되고, 이 이메일은 성공한 모든 SAML 응답의 SAP Business Object Cloud 응용 프로그램으로 전달됩니다.

    ![Single Sign-on 구성](./media/active-directory-saas-sapboc-tutorial/config6.png)

13. **로그인 자격 증명(이메일)** 텍스트 상자에 이메일을 입력하고 **계정 확인** 단추를 클릭하여 시스템에서 로그인 자격 증명을 계정에 추가할 수 있도록 허용합니다.

    ![Single Sign-on 구성](./media/active-directory-saas-sapboc-tutorial/config7.png)

14. **저장** 아이콘을 클릭합니다.

    ![저장](./media/active-directory-saas-sapboc-tutorial/save.png)

> [!TIP]
> 이제 앱을 설정하는 동안 [Azure Portal](https://portal.azure.com) 내에서 이러한 지침의 간결한 버전을 읽을 수 있습니다.  **Active Directory > 엔터프라이즈 응용 프로그램** 섹션에서 이 앱을 추가한 후에는 **Single Sign-On** 탭을 클릭하고 맨 아래에 있는 **구성** 섹션을 통해 포함된 설명서에 액세스하면 됩니다. 포함된 설명서 기능에 대한 자세한 내용은 [Azure AD 포함된 설명서]( https://go.microsoft.com/fwlink/?linkid=845985)에서 확인할 수 있습니다.
> 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기
이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

![Azure AD 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. **Azure Portal**의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-sapboc-tutorial/create_aaduser_01.png) 

2. 사용자 목록을 표시하려면 **사용자 및 그룹**으로 이동한 후 **모든 사용자**를 클릭합니다.
    
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-sapboc-tutorial/create_aaduser_02.png) 

3. **사용자** 대화 상자를 열려면 대화 상자 위쪽에서 **추가**를 클릭합니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-sapboc-tutorial/create_aaduser_03.png) 

4. **사용자** 대화 상자 페이지에서 다음 단계를 수행합니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-sapboc-tutorial/create_aaduser_04.png) 

    a. **이름** 텍스트 상자에 **BrittaSimon**을 입력합니다.

    b. **사용자 이름** 텍스트 상자에 BrittaSimon의 **전자 메일 주소**를 입력합니다.

    c. **암호 표시**를 선택하고 **암호** 값을 적어둡니다.

    d. **만들기**를 클릭합니다.
 
### <a name="creating-an-sap-business-object-cloud-test-user"></a>SAP Business Object Cloud 테스트 사용자 만들기

Azure AD 사용자가 SAP Business Object Cloud에 로그인할 수 있도록 하려면 SAP Business Object Cloud로 프로비전되어야 합니다.  
SAP Business Object Cloud의 경우 프로비전은 수동 작업입니다.

**사용자 계정을 프로비전하려면 다음 단계를 수행합니다.**

1. SAP Business Object Cloud 회사 사이트에 관리자 권한으로 로그인합니다.

2. **메뉴 > 보안 > 사용자**로 이동합니다.

    ![직원 추가](./media/active-directory-saas-sapboc-tutorial/user1.png)

3. **사용자** 페이지에서 **"+"**를 클릭하여 새 사용자 세부 정보를 추가하고 다음 단계를 수행합니다.

    ![피플 초대](./media/active-directory-saas-sapboc-tutorial/user4.png)

    a. **사용자 ID** 텍스트 상자에 사용자 ID(예: Britta)를 입력합니다.

    b. **이름** 텍스트 상자에 사용자의 이름(예: Britta)을 입력합니다.

    c. **성** 텍스트 상자에 사용자의 성(예: Simon)을 입력합니다.

    d. **표시 이름** 텍스트 상자에 사용자의 전체 이름(예: Britta Simon)을 입력합니다.

    e. **전자 메일** 텍스트 상자에 사용자의 이메일 주소(예: brittasimon@contoso.com)를 입력합니다.

    f. 사용자에게 적합한 역할을 선택합니다.

      ![역할](./media/active-directory-saas-sapboc-tutorial/user3.png)

    g. **저장** 아이콘을 클릭합니다. 


### <a name="assigning-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 SAP Business Object Cloud에 대한 액세스 권한을 부여합니다.

![사용자 할당][200] 

**Britta Simon을 SAP Business Object Cloud에 할당하려면 다음 단계를 수행합니다.**

1. Azure Portal에서 응용 프로그램 보기를 연 다음 디렉터리 보기로 이동하고 **엔터프라이즈 응용 프로그램**으로 이동한 후 **모든 응용 프로그램**을 클릭합니다.

    ![사용자 할당][201] 

2. 응용 프로그램 목록에서 **SAP Business Object Cloud**을 선택합니다.

    ![Single Sign-on 구성](./media/active-directory-saas-sapboc-tutorial/tutorial_sapboc_app.png) 

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    ![사용자 할당][202] 

4. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 할당][203]

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다.

6. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.
    
### <a name="testing-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 SAP Business Object Cloud 타일을 클릭하면 SAP Business Object Cloud 응용 프로그램에 자동으로 로그온됩니다.
액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On이란 무엇입니까?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sapboc-tutorial/tutorial_general_203.png


