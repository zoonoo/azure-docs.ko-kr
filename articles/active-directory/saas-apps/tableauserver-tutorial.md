---
title: '자습서: Tableau Server와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 Tableau Server 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: c1917375-08aa-445c-a444-e22e23fa19e0
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2018
ms.author: jeedes
ms.openlocfilehash: 9986e7984f24e065886f6d0a8b2d06968f778702
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/29/2018
ms.locfileid: "52582720"
---
# <a name="tutorial-azure-active-directory-integration-with-tableau-server"></a>자습서: Tableau Server와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Tableau Server를 통합하는 방법에 대해 알아봅니다.

Tableau Server를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- Tableau Server에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정으로 Tableau Server에 자동으로 로그온(Single Sign-on)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 응용 프로그램 액세스 및 Single Sign-On이란 무엇인가요?](../manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

Tableau Server와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- Tableau Server Single Sign-On이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [1개월 평가판을 얻을](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다.  이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 Tableau Server 추가
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-tableau-server-from-the-gallery"></a>갤러리에서 Tableau Server 추가

Tableau Server의 Azure AD 통합을 구성하려면 갤러리의 Tableau Server를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Tableau Server를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Azure Active Directory 단추][1]

2. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 응용 프로그램**으로 이동합니다.

    ![엔터프라이즈 응용 프로그램 블레이드][2]
    
3. 새 응용 프로그램을 추가하려면 대화 상자 맨 위 있는 **새 응용 프로그램** 단추를 클릭합니다.

    ![새 응용 프로그램 단추][3]

4. 검색 상자에 **Tableau Server**를 입력하고, 결과 패널에서 **Tableau Server**를 선택한 다음, **추가** 단추를 클릭하여 애플리케이션을 추가합니다.

    ![결과 목록의 Tableau Server](./media/tableauserver-tutorial/tutorial-tableauserver-addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 Tableau Server에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 Tableau Server 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 Tableau Server의 관련 사용자 간에 연결이 설정되어야 합니다.

Tableau Server에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#creating-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On 테스트하는 데 사용합니다.
3. **[Tableau Server 테스트 사용자 만들기](#creating-a-tableau-server-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 Tableau Server에 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assigning-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Single Sign-On 테스트](#testing-single-sign-on)** - 구성이 작동하는지 확인합니다.

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 Tableau Server 응용 프로그램에서 Single Sign-On을 구성합니다.

**Tableau Server에서 Azure AD Single Sign-on을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **Tableau Server** 응용 프로그램 통합 페이지에서 **Single Sign-On**을 클릭합니다.

    ![Single Sign-On 구성 링크][4]

2. **Single Sign-On 방법 선택** 대화 상자에서 **SAML** 모드에 대해 **선택**을 클릭하여 Single Sign-On을 사용하도록 설정합니다.

    ![Configure Single Sign-On](common/tutorial-general-301.png)

3. Tableau Server 애플리케이션에서는 사용자 지정 클레임 **username**을 아래와 같이 정의해야 합니다. 이것이 고유한 사용자 ID 클레임 대신 사용자 ID로 사용되고 있습니다. 응용 프로그램 통합 페이지의 **사용자 특성 및 클레임** 섹션에서 이러한 특성의 값을 관리할 수 있습니다. **편집** 단추를 클릭하여 **사용자 특성 및 클레임** 대화 상자를 엽니다.

    ![이미지](./media/tableauserver-tutorial/tutorial-tableauserver-attribute.png)

4. **사용자 특성 및 클레임** 대화 상자의 **사용자 클레임** 섹션에서 위의 이미지에 표시된 것과 같이 SAML 토큰 특성을 구성하고 다음 단계를 수행합니다.
    
    | 특성 이름 | 특성 값 |
    | ---------------| --------------- |    
    | 사용자 이름 | user.userprincipalname |

    a. **새 클레임 추가**를 클릭하여 **사용자 클레임 관리** 대화 상자를 엽니다.

    ![이미지](./media/tableauserver-tutorial/tutorial-tableauserver-add-attribute.png)

    ![이미지](./media/tableauserver-tutorial/tutorial-tableauserver-manage-attribute.png)

    b. **이름** 텍스트 상자에서 해당 행에 표시된 특성 이름을 입력합니다.

    다. **네임스페이스** 값을 입력합니다.

    d. 원본을 **특성**으로 선택합니다.

    e. **원본 특성** 목록에서 해당 행에 표시된 특성 값을 입력합니다.

    f. **저장**을 클릭합니다.

5. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 아이콘을 클릭하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![Configure Single Sign-On](common/editconfigure.png)

6. **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    a. **로그온 URL** 텍스트 상자에서 다음 패턴으로 URL을 입력합니다. `https://azure.<domain name>.link`
    
    b. **식별자** 텍스트 상자에서 `https://azure.<domain name>.link` 패턴을 사용하여 URL을 입력합니다.

    다. **회신 URL** 텍스트 상자에 다음 패턴으로 URL을 입력합니다.`https://azure.<domain name>.link/wg/saml/SSO/index.html`

    ![이미지](./media/tableauserver-tutorial/tutorial-tableauserver-url.png)
     
    > [!NOTE] 
    > 위의 값은 실제 값이 아닙니다. 이 자습서에서 나중에 설명하겠지만, 이 값을 Tableau Server 구성 페이지의 실제 URL과 식별자로 업데이트합니다.

7. **SAML 서명 인증서** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드**를 클릭하고 **페더레이션 메타데이터 XML**을 다운로드한 다음, 컴퓨터에 인증서 파일을 저장합니다.

    ![인증서 다운로드 링크](./media/tableauserver-tutorial/tutorial-tableauserver-certificate.png) 

8. 응용 프로그램에 대해 구성된 SSO를 가져오려면 관리자 권한으로 Tableau Server 테넌트에 로그온해야 합니다.

9. **Tableau Server 구성** 페이지에서 다음 단계를 수행합니다.
   
    ![Configure Single Sign-On](./media/tableauserver-tutorial/tutorial-tableauserver-001.png)

    a. Tableau Server 구성에서 **SAML** 탭을 클릭합니다. 
  
    b. **Single Sign-On에 SAML 사용**확인란을 선택합니다.
   
    다. Tableau Server 반환 URL - Tableau Server 사용자가 액세스하는 URL(예: http://tableau_server)입니다. http://localhost을 사용하는 것은 권장되지 않습니다. 후행 슬래시가 있는 URL(예: http://tableau_server/))은 지원되지 않습니다. **Tableau Server 반환 URL**을 복사하여 **Tableau Server 도메인 및 URL** 섹션의 Azure AD **로그온 URL** 텍스트 상자에 붙여넣습니다.
   
    d. SAML 엔터티 ID - 엔터티 ID는 IdP에 대한 Tableau Server 설치를 고유하게 식별합니다. 원하는 경우 여기에 Tableau Server URL을 다시 입력할 수 있지만 반드시 Tableau Server URL을 입력해야 하는 것은 아닙니다. **SAML 엔터티 ID**를 복사하여 **Tableau Server 도메인 및 URL** 섹션의 Azure AD **식별자** 텍스트 상자에 붙여넣습니다.
     
    e. **메타데이터 파일 내보내기**를 클릭하여 텍스트 편집기 응용 프로그램에서 엽니다. Http Post 및 인덱스 0이 포함된 어설션 소비자 서비스 URL을 찾아서 복사합니다. **Tableau Server 도메인 및 URL** 섹션의 Azure AD **응답 URL** 텍스트 상자에 붙여넣습니다.
   
    f. Azure Portal에서 다운로드한 페더레이션 메타데이터 파일을 찾은 다음 **SAML Idp 메타데이터 파일**에 업로드합니다.
   
    g. Tableau Server 구성 페이지에서 **확인** 단추를 클릭합니다.
   
    >[!NOTE] 
    >고객은 Tableau Server SAML SSO 구성의 모든 인증서를 업로드해야 하며, 인증서는 SSO 흐름에서 무시됩니다.
    >Tableau Server에서 SAML을 구성하는 데 도움이 필요한 경우 [SAML 구성](http://onlinehelp.tableau.com/current/server/en-us/config_saml.htm) 문서를 참조하세요.

### <a name="creating-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**를 차례로 선택하고 **모든 사용자**를 선택합니다.

    ![Azure AD 사용자 만들기][100]

2. 화면 위쪽에서 **새 사용자**를 선택합니다.

    ![Azure AD 테스트 사용자 만들기](common/create-aaduser-01.png) 

3. 사용자 속성에서 다음 단계를 수행합니다.

    ![Azure AD 테스트 사용자 만들기](common/create-aaduser-02.png)

    a. **이름** 필드에 **BrittaSimon**을 입력합니다.
  
    b. **사용자 이름** 필드에 **brittasimon@yourcompanydomain.extension**을 입력합니다.  
    예를 들어 BrittaSimon@contoso.com

    다. **속성**을 선택하고 **암호 표시** 확인란을 선택한 다음, 암호 상자에 표시된 값을 적어 둡니다.

    d. **만들기**를 선택합니다.
  
### <a name="creating-a-tableau-server-test-user"></a>Tableau Server 테스트 사용자 만들기

이 섹션은 Tableau Server에서 Britta Simon이라는 사용자를 만들기 위한 것입니다. Tableau Server의 모든 사용자를 프로비전해야 합니다. 

사용자의 사용자 이름은 Azure AD에서 구성한 사용자 지정 특성 **username**의 값과 일치해야 합니다. 올바른 매핑을 사용해야 통합 시 [Azure AD Single Sign-On 구성](#configuring-azure-ad-single-sign-on)이 작동합니다.

>[!NOTE]
>사용자를 수동으로 만들어야 하는 경우 조직의 Tableau Server 관리자에게 문의해야 합니다.

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Tableau Server에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 응용 프로그램**을 선택한 다음, **모든 응용 프로그램**을 선택합니다.

    ![사용자 할당][201]

2. 응용 프로그램 목록에서 **Tableau Server**를 선택합니다.

    ![Configure Single Sign-On](./media/tableauserver-tutorial/tutorial-tableauserver-app.png) 

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    ![사용자 할당][202]

4. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 할당][203]

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택하고 화면 아래쪽에서 **선택** 단추를 클릭합니다.

6. **할당 추가** 대화 상자에서 **할당** 단추를 선택합니다.

### <a name="testing-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Tableau Server 타일을 클릭하면 Tableau Server 응용 프로그램에 자동으로 로그온됩니다.
액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](../user-help/active-directory-saas-access-panel-introduction.md)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial-general-01.png
[2]: common/tutorial-general-02.png
[3]: common/tutorial-general-03.png
[4]: common/tutorial-general-04.png

[100]: common/tutorial-general-100.png

[201]: common/tutorial-general-201.png
[202]: common/tutorial-general-202.png
[203]: common/tutorial-general-203.png
