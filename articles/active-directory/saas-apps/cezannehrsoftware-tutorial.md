---
title: '자습서: Cezanne HR 소프트웨어와 Azure Active Directory 통합| Microsoft Docs'
description: Azure Active Directory 및 Cezanne HR Software 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 62b42e15-c282-492d-823a-a7c1c539f2cc
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: fb6fb443440ce9af26a3152f7dcc536c4e881cd9
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56165437"
---
# <a name="tutorial-azure-active-directory-integration-with-cezanne-hr-software"></a>자습서: Cezanne HR Software와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Cezanne HR Software를 통합하는 방법에 대해 알아봅니다.

Cezanne HR Software를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- Cezanne HR Software에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정으로 Cezanne HR Software에 자동으로 로그온(Single Sign-on)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](../manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

Cezanne HR Software와의 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- Cezanne HR Software Single Sign-on이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [1개월 평가판을 얻을](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다.  이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 Cezanne HR Software 추가
1. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-cezanne-hr-software-from-the-gallery"></a>갤러리에서 Cezanne HR Software 추가
Cezanne HR Software의 Azure AD 통합을 구성하려면 갤러리의 Cezanne HR Software를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Cezanne HR Software를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Azure Active Directory 단추][1]

1. **엔터프라이즈 애플리케이션**으로 이동합니다. 그런 후 **모든 애플리케이션**으로 이동합니다.

    ![엔터프라이즈 애플리케이션 블레이드][2]
    
1. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션** 단추를 클릭합니다.

    ![새 애플리케이션 단추][3]

1. 검색 상자에 **Cezanne HR Software**를 입력하고 결과 패널에서 **Cezanne HR Software**를 선택한 후 **추가** 단추를 클릭하여 애플리케이션을 추가합니다.

    ![결과 목록의 Cezanne HR Software](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 Cezanne HR Software에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-on이 작동되려면 Azure AD는 Azure AD의 사용자에 해당하는 Cezanne HR Software의 사용자가 누군지 알고 있어야 합니다. 즉, Azure AD 사용자와 Cezanne HR Software의 관련 사용자 간에 연결이 형성되어야 합니다.

Cezanne HR Software에서 Azure AD의 **사용자 이름** 값을 **Username** 값으로 할당하여 링크 관계를 설정합니다.

Cezanne HR Software에서 Azure AD Single Sign-on을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
1. **Cezanne HR Software 테스트 사용자 만들기** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 Cezanne HR Software에 만듭니다.
1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
1. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 Cezanne HR Software 애플리케이션에서 Single Sign-On을 구성합니다.

**Cezanne HR Software에서 Azure AD Single Sign-on을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **Cezanne HR Software** 애플리케이션 통합 페이지에서 **Single sign-on**을 클릭합니다.

    ![Single Sign-On 구성 링크][4]

1. **Single Sign-On** 대화 상자에서 **모드**를 **SAML 기반 로그온**으로 선택하여 Single Sign-On을 사용하도록 설정합니다.
 
    ![Single Sign-On 대화 상자](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_samlbase.png)

1. **Cezanne HR Software 도메인 및 URL** 섹션에서 다음 단계를 수행합니다.

    ![Cezanne HR Software 도메인 및 URL Single Sign-On 정보](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_url.png)

    a. **로그온 URL** 텍스트 상자에서 URL `https://w3.cezanneondemand.com/CezanneOnDemand/-/<tenantidentifier>`을 입력합니다.

    b. **식별자** 텍스트 상자에 URL `https://w3.cezanneondemand.com/CezanneOnDemand/`을 입력합니다.

    다. **회신 URL** 텍스트 상자에 URL `https://w3.cezanneondemand.com:443/cezanneondemand/-/<tenantidentifier>/Saml/samlp`를 입력합니다.
    
    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 로그온 URL 및 회신 URL을 사용하여 이러한 값을 업데이트합니다. 이러한 값을 가져오려면 [Cezanne HR Software 클라이언트 지원 팀](https://cezannehr.com/services/support/)에 문의합니다.

1. **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 를 클릭한 후 컴퓨터에 인증서 파일을 저장합니다.

    ![인증서 다운로드 링크](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_certificate.png) 

1. **저장** 단추를 클릭합니다.

    ![Single Sign-On 구성 저장 단추](./media/cezannehrsoftware-tutorial/tutorial_general_400.png)

1. **Cezanne HR Software 구성** 섹션에서 **Cezanne HR Software 구성**을 클릭하여 **로그온 구성** 창을 엽니다.

    ![Cezanne HR Software 구성](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_configure.png)

1. **빠른 참조** 섹션으로 스크롤합니다. **빠른 참조 섹션**에서 **SAML Single Sign-On Service URL 및 SAML 엔터티 ID**를 복사합니다.

    ![Cezanne HR Software 구성](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_configure1.png)

1. 다른 웹 브라우저 창에서 Cezanne HR Software 테넌트에 관리자로 로그인합니다.

1. 왼쪽 탐색 창에서 **시스템 설정**을 클릭합니다. **보안 설정**으로 이동합니다. 그런 다음 **Single Sign-On 구성**으로 이동합니다.

    ![앱 쪽에서 Single Sign-On 구성](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_000.png)

1. **다음 SSO(Single Sign-On) Service를 사용한 사용자 로그온 허용** 패널에서 **SAML 2.0** 상자를 선택하고 **고급 구성** 옵션을 선택합니다.

    ![앱 쪽에서 Single Sign-On 구성](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_001.png)

1. **새로 추가** 단추를 클릭합니다.

    ![앱 쪽에서 Single Sign-On 구성](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_002.png)

1. **SAML 2.0 ID 공급자** 섹션에서 다음 단계를 수행합니다.

    ![앱 쪽에서 Single Sign-On 구성](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_003.png)
    
    a. **표시 이름**으로 ID 공급자의 이름을 입력합니다.

    b. Azure Portal에서 복사한 **SAML 엔터티 ID**를 **엔터티 식별자** 텍스트 상자에 붙여넣습니다. 

    다. **SAML 바인딩** 을 'POST'로 변경합니다.

    d. Azure Portal에서 복사한 **SAML Single Sign-On 서비스 URL**의 값을 **보안 토큰 서비스 엔드포인트** 텍스트 상자에 붙여넣습니다.

    e. 사용자 ID 특성 이름 텍스트 상자에 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`을 입력합니다.
    
    f. **업로드** 아이콘을 클릭하여 Azure Portal에서 다운로드한 인증서를 업로드합니다.
    
    g. **확인** 단추를 클릭합니다. 

1. **저장** 단추를 클릭합니다.

    ![앱 쪽에서 Single Sign-On 구성](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_004.png)

> [!TIP]
> 이제 앱을 설정하는 동안 [Azure Portal ](https://portal.azure.com) 내에서 이러한 지침의 간결한 버전을 읽을 수 있습니다.  **Active Directory &gt; 엔터프라이즈 애플리케이션** 섹션에서 이 앱을 추가한 후에는 **Single Sign-On** 탭을 클릭하고 맨 아래에 있는 **구성** 섹션을 통해 포함된 설명서에 액세스하면 됩니다. 포함된 설명서 기능에 대한 자세한 내용은 [Azure AD 포함된 설명서]( https://go.microsoft.com/fwlink/?linkid=845985)에서 확인할 수 있습니다.
> 

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

   ![Azure AD 테스트 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory** 단추를 클릭합니다.

    ![Azure Active Directory 단추](./media/cezannehrsoftware-tutorial/create_aaduser_01.png)

1. 사용자 목록을 표시하려면 **사용자 및 그룹**으로 이동한 후 **모든 사용자**를 클릭합니다.

    !["사용자 및 그룹" 및 "모든 사용자" 링크](./media/cezannehrsoftware-tutorial/create_aaduser_02.png)

1. **사용자** 대화 상자를 열려면 **모든 사용자** 대화 상자 위쪽에서 **추가**를 클릭합니다.

    ![추가 단추](./media/cezannehrsoftware-tutorial/create_aaduser_03.png)

1. **사용자** 대화 상자에서 다음 단계를 수행합니다.

    ![사용자 대화 상자](./media/cezannehrsoftware-tutorial/create_aaduser_04.png)

    a. **이름** 상자에 **BrittaSimon**을 입력합니다.

    나. **사용자 이름** 상자에 사용자인 Britta Simon의 전자 메일 주소를 입력합니다.

    다. **암호 표시** 확인란을 선택한 다음 **암호** 상자에 표시된 값을 적어둡니다.

    d. **만들기**를 클릭합니다.
 
### <a name="create-a-cezanne-hr-software-test-user"></a>Cezanne HR Software 테스트 사용자 만들기

Azure AD 사용자가 Cezanne HR Software에 로그인 할 수 있도록 하려면 Cezanne HR Software로 프로비전되어야 합니다. Cezanne HR Software의 경우 프로비저닝은 수동 작업입니다.

**사용자 계정을 프로비전하려면 다음 단계를 수행합니다.**

1.  Cezanne HR Software 회사 사이트에 관리자 권한으로 로그인합니다.

1.  왼쪽 탐색 창에서 **시스템 설정**을 클릭합니다. **사용자 관리**로 이동합니다. 그런 다음 **새 사용자 추가**로 이동합니다.

    ![새 사용자](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_005.png "새 사용자")

1.  **사람 세부 정보** 섹션에서 다음 단계를 수행합니다.

    ![새 사용자](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_006.png "새 사용자")
    
    a. **내부 사용자** 를 OFF로 설정합니다.
    
    b. **이름** 텍스트 상자에 사용자의 이름(예: **Britta**)을 입력합니다.  
 
    다. **성** 텍스트 상자에 사용자의 성(예: **Simon**)을 입력합니다.
    
    d. **전자 메일** 텍스트 상자에서 Brittasimon@contoso.com과 같은 사용자의 전자 메일 주소를 입력합니다.

1.  **계정 정보** 섹션에서 다음 단계를 수행합니다.

    ![새 사용자](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_007.png "새 사용자")
    
    a. **사용자 이름** 텍스트 상자에서 Brittasimon@contoso.com과 같은 사용자의 이메일 주소를 입력합니다.
    
    b. **암호** 텍스트 상자에서 사용자에 대한 암호를 입력합니다.
    
    다. **보안 역할**로 **HR 전문가**를 선택합니다.
    
    d. **확인**을 클릭합니다.

1. **Single Sign-On** 탭으로 이동하고 **SAML 2.0 식별자** 영역에서 **새로 추가**를 선택합니다.

    ![사용자](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_008.png "사용자")

1. **ID 공급자**에 대해 사용자의 ID 공급자를 선택하고 **사용자 식별자** 텍스트 상자에서 Britta Simon 계정의 전자 메일 주소를 입력합니다.

    ![사용자](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_009.png "사용자")
    
1. **저장** 단추를 클릭합니다.

    ![사용자](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_010.png "사용자")

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Cezanne HR Software에 대한 액세스 권한을 부여합니다.

![사용자 역할 할당][200] 

**Britta Simon을 Cezanne HR Software에 할당하려면 다음 단계를 수행합니다.**

1. Azure Portal에서 애플리케이션 보기를 연 다음 디렉터리 보기로 이동하고 **엔터프라이즈 애플리케이션**으로 이동한 후 **모든 애플리케이션**을 클릭합니다.

    ![사용자 할당][201] 

1. 애플리케이션 목록에서 **Cezanne HR Software**를 선택합니다.

    ![애플리케이션 목록의 Cezanne HR Software 링크](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_app.png)  

1. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    !["사용자 및 그룹" 링크][202]

1. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창][203]

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다.

1. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다.

1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.
    
### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Cezanne HR Software 타일을 클릭하면 Cezanne HR Software 애플리케이션에 자동으로 로그온됩니다.
액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](../user-help/active-directory-saas-access-panel-introduction.md)를 참조하세요. 

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](tutorial-list.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/cezannehrsoftware-tutorial/tutorial_general_01.png
[2]: ./media/cezannehrsoftware-tutorial/tutorial_general_02.png
[3]: ./media/cezannehrsoftware-tutorial/tutorial_general_03.png
[4]: ./media/cezannehrsoftware-tutorial/tutorial_general_04.png

[100]: ./media/cezannehrsoftware-tutorial/tutorial_general_100.png

[200]: ./media/cezannehrsoftware-tutorial/tutorial_general_200.png
[201]: ./media/cezannehrsoftware-tutorial/tutorial_general_201.png
[202]: ./media/cezannehrsoftware-tutorial/tutorial_general_202.png
[203]: ./media/cezannehrsoftware-tutorial/tutorial_general_203.png

