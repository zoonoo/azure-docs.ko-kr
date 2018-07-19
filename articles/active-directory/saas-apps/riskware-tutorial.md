---
title: '자습서: Riskware와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 Riskware 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 81866167-b163-4695-8978-fd29a25dac7a
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2018
ms.author: jeedes
ms.openlocfilehash: d234b34f7583441f496fb96d37166c5472cadb4c
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37110830"
---
# <a name="tutorial-azure-active-directory-integration-with-riskware"></a>자습서: Riskware와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Riskware를 통합하는 방법에 대해 알아봅니다.

Riskware를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- Riskware에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정으로 Riskware에 자동으로 로그온(Single Sign-On)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 응용 프로그램 액세스 및 Single Sign-On이란 무엇인가요?](../manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

Riskware와의 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- Riskware Single Sign-On이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [1개월 평가판을 얻을](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 Riskware 추가
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-riskware-from-the-gallery"></a>갤러리에서 Riskware 추가
Riskware의 Azure AD 통합을 구성하려면 갤러리의 Riskware를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Riskware를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Azure Active Directory 단추][1]

2. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 응용 프로그램**으로 이동합니다.

    ![엔터프라이즈 응용 프로그램 블레이드][2]

3. 새 응용 프로그램을 추가하려면 대화 상자 맨 위 있는 **새 응용 프로그램** 단추를 클릭합니다.

    ![새 응용 프로그램 단추][3]

4. 검색 상자에 **Riskware**를 입력하고 결과 패널에서 **Riskware**를 선택한 다음, **추가** 단추를 클릭하여 응용 프로그램을 추가합니다.

    ![결과 목록의 Riskware](./media/riskware-tutorial/tutorial_riskware_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 “Britta Simon”이라는 테스트 사용자를 기반으로 Riskware에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 Riskware 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 Riskware의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Riskware에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
3. **[Riskware 테스트 사용자 만들기](#create-a-riskware-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 Riskware에 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 Riskware 응용 프로그램에서 Single Sign-On을 구성합니다.

**Riskware에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **Riskware** 응용 프로그램 통합 페이지에서 **Single Sign-On**을 클릭합니다.

    ![Single Sign-On 구성 링크][4]

2. **Single Sign-On** 대화 상자에서 **모드**를 **SAML 기반 로그온**으로 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Single Sign-On 대화 상자](./media/riskware-tutorial/tutorial_riskware_samlbase.png)

3. **Riskware 도메인 및 URL** 섹션에서 다음 단계를 수행합니다.

    ![Riskware 도메인 및 URL Single Sign-On 정보](./media/riskware-tutorial/tutorial_riskware_url.png)

    a. **로그온 URL** 텍스트 상자에서 다음 패턴을 사용하여 URL을 입력합니다.
    | Environment| URL 패턴|
    |--|--|
    | UAT|  `https://riskcloud.net/uat?ccode=<COMPANYCODE>` |
    | PROD| `https://riskcloud.net/prod?ccode=<COMPANYCODE>` |
    | DEMO| `https://riskcloud.net/demo?ccode=<COMPANYCODE>` |
    |||

    나. **식별자(엔터티 ID)** 텍스트 상자에 URL을 입력합니다.
    | Environment| URL 패턴|
    |--|--|
    | UAT| `https://riskcloud.net/uat` |
    | PROD| `https://riskcloud.net/prod` |
    | DEMO| `https://riskcloud.net/demo` |
    |||

    > [!NOTE]
    > 로그온 URL 값은 실제 값이 아닙니다. 이 값을 실제 로그온 URL로 업데이트합니다. 값을 얻으려면 [Riskware 클라이언트 지원 팀](mailto:support@pansoftware.com.au)에 문의하세요.

4. **SAML 서명 인증서** 섹션에서 **메타데이터 XML**을 클릭한 후 컴퓨터에 메타데이터 파일을 저장합니다.

    ![인증서 다운로드 링크](./media/riskware-tutorial/tutorial_riskware_certificate.png) 

5. **저장** 단추를 클릭합니다.

    ![Single Sign-On 구성 저장 단추](./media/riskware-tutorial/tutorial_general_400.png)

6. **Riskware 구성** 섹션에서 **Riskware 구성**을 클릭하여 **로그온 구성** 창을 엽니다. **빠른 참조 섹션**에서 **로그아웃 URL 및 SAML Single Sign-On 서비스 URL**을 복사합니다.

    ![Riskware 구성](./media/riskware-tutorial/tutorial_riskware_configure.png)

7. 다른 웹 브라우저 창에서 Riskware 회사 사이트에 관리자로 로그인합니다.

8. 오른쪽 상단에 있는 **유지 관리**를 클릭하여 유지 관리 페이지를 엽니다.

    ![Riskware 구성 유지 관리](./media/riskware-tutorial/tutorial_riskware_maintain.png)

9. 유지 관리 페이지에서 **인증**을 클릭합니다.

    ![Riskware 구성 인증](./media/riskware-tutorial/tutorial_riskware_authen.png)

10. **인증 구성** 페이지에서 다음 단계를 수행합니다.

    ![Riskware 구성 인증 구성](./media/riskware-tutorial/tutorial_riskware_config.png)

    a. 인증 **형식**으로 **SAML**을 선택합니다.

    나. **코드** 텍스트 상자에 AZURE_UAT와 같은 코드를 입력합니다.

    다. **설명** 텍스트 상자에 SSO용 AZURE 구성과 같은 설명을 입력합니다.

    d. **Single Sign-On URL** 텍스트 상자에 Azure Portal에서 복사한 **SAML Single Sign-On 서비스 URL** 값을 붙여넣습니다.

    e. Azure Portal에서 복사한 **로그아웃 URL** 값을 **로그아웃 페이지** 텍스트 상자에 붙여넣습니다.

    f. **게시 양식 필드** 텍스트 상자에 SAMLResponse와 같은 SAML이 포함된 게시 응답에 있는 필드 이름을 입력합니다.

    g. **XML ID 태그 이름** 텍스트 상자에 SAML 응답에서 고유 식별자를 포함하는 NameID와 같은 특성을 입력합니다.

    h. Azure Portal에서 다운로드한 **메타데이터 Xml**을 메모장에서 열고 메타데이터 파일에서 인증서를 복사하여 **인증서** 텍스트 상자에 붙여넣습니다.

    i. **소비자 URL** 텍스트 상자에 지원 팀으로부터 받은 **회신 URL** 값을 붙여넣습니다.

    j. **발급자** 텍스트 상자에 지원 팀으로부터 받은 **식별자** 값을 붙여넣습니다.

    > [!Note]
    > 이러한 값을 얻으려면 [Riskware 클라이언트 지원 팀](mailto:support@pansoftware.com.au)에 문의하세요.

    k. **POST 사용** 확인란을 선택합니다.

    l. **SAML 요청 사용** 확인란을 선택합니다.

    m. **저장**을 클릭합니다.

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

   ![Azure AD 테스트 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory** 단추를 클릭합니다.

    ![Azure Active Directory 단추](./media/riskware-tutorial/create_aaduser_01.png)

2. 사용자 목록을 표시하려면 **사용자 및 그룹**으로 이동한 후 **모든 사용자**를 클릭합니다.

    !["사용자 및 그룹" 및 "모든 사용자" 링크](./media/riskware-tutorial/create_aaduser_02.png)

3. **사용자** 대화 상자를 열려면 **모든 사용자** 대화 상자 위쪽에서 **추가**를 클릭합니다.

    ![추가 단추](./media/riskware-tutorial/create_aaduser_03.png)

4. **사용자** 대화 상자에서 다음 단계를 수행합니다.

    ![사용자 대화 상자](./media/riskware-tutorial/create_aaduser_04.png)

    a. **이름** 상자에 **BrittaSimon**을 입력합니다.

    나. **사용자 이름** 상자에 사용자인 Britta Simon의 전자 메일 주소를 입력합니다.

    다. **암호 표시** 확인란을 선택한 다음 **암호** 상자에 표시된 값을 적어둡니다.

    d. **만들기**를 클릭합니다.

### <a name="create-a-riskware-test-user"></a>Riskware 테스트 사용자 만들기

Azure AD 사용자가 Riskware에 로그인할 수 있도록 하려면 Riskware로 프로비전되어야 합니다. Riskware의 경우 수동으로 프로비전합니다.

**사용자 계정을 프로비전하려면 다음 단계를 수행합니다.**

1. 보안 관리자 권한으로 Riskware에 로그인합니다.

2. 오른쪽 상단에 있는 **유지 관리**를 클릭하여 유지 관리 페이지를 엽니다. 

    ![Riskware 구성 유지 관리](./media/riskware-tutorial/tutorial_riskware_maintain.png)

3. 유지 관리 페이지에서 **피플**을 클릭합니다.

    ![Riskware 구성 피플](./media/riskware-tutorial/tutorial_riskware_people.png)

4. **세부 정보** 탭을 선택하고 다음 단계를 수행합니다.

    ![Riskware 구성 세부 정보](./media/riskware-tutorial/tutorial_riskware_details.png)

    a. 직원과 같은 **사람 유형**을 선택합니다.

    나. **이름** 텍스트 상자에 사용자의 이름(예: **Britta**)을 입력합니다.

    다. **성** 텍스트 상자에 사용자의 성(예: **Simon**)을 입력합니다.

5. **보안** 탭에서 다음 단계를 수행합니다.

    ![Riskware 구성 보안](./media/riskware-tutorial/tutorial_riskware_security.png)

    a. **인증** 섹션에서 SSO용 AZURE 구성과 같이 설정한 **인증** 모드를 선택합니다.

    나. **로그온 세부 정보** 섹션에서 **사용자 ID** 텍스트 상자에 **brittasimon@contoso.com**과 같은 사용자의 이메일을 입력합니다.

    다. **암호** 텍스트 상자에 사용자 암호를 입력합니다.

6. **조직** 탭에서 다음 단계를 수행합니다.

    ![Riskware 구성 조직](./media/riskware-tutorial/tutorial_riskware_org.png)

    a. 옵션을 **Level1** 조직으로 선택합니다.

    나. **사용자의 주 작업 공간** 섹션에서 **위치** 텍스트 상자에 사용자의 위치를 입력합니다.

    다. **직원** 섹션에서 일반과 같은 **직원 상태**를 선택합니다.

7. **저장**을 클릭합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Riskware에 대한 액세스 권한을 부여합니다.

![사용자 역할 할당][200]

**Britta Simon을 Riskware에 할당하려면 다음 단계를 수행합니다.**

1. Azure Portal에서 응용 프로그램 보기를 연 다음 디렉터리 보기로 이동하고 **엔터프라이즈 응용 프로그램**으로 이동한 후 **모든 응용 프로그램**을 클릭합니다.

    ![사용자 할당][201] 

2. 응용 프로그램 목록에서 **Riskware**를 선택합니다.

    ![응용 프로그램 목록의 Riskware 링크](./media/riskware-tutorial/tutorial_riskware_app.png)  

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    !["사용자 및 그룹" 링크][202]

4. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창][203]

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다.

6. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.
    
### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Riskware 타일을 클릭하면 Riskware 응용 프로그램에 자동으로 로그온됩니다.
액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](../active-directory-saas-access-panel-introduction.md)를 참조하세요. 

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/riskware-tutorial/tutorial_general_01.png
[2]: ./media/riskware-tutorial/tutorial_general_02.png
[3]: ./media/riskware-tutorial/tutorial_general_03.png
[4]: ./media/riskware-tutorial/tutorial_general_04.png

[100]: ./media/riskware-tutorial/tutorial_general_100.png

[200]: ./media/riskware-tutorial/tutorial_general_200.png
[201]: ./media/riskware-tutorial/tutorial_general_201.png
[202]: ./media/riskware-tutorial/tutorial_general_202.png
[203]: ./media/riskware-tutorial/tutorial_general_203.png

