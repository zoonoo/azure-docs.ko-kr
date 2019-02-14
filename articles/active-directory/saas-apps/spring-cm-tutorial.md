---
title: '자습서: SpringCM과 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 SpringCM 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 4a42f797-ac58-4aca-a8e6-53bfe5529083
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/26/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2187d0e986a173e1b8dc37fd0abdfa3bfd9e9505
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56168990"
---
# <a name="tutorial-azure-active-directory-integration-with-springcm"></a>자습서: SpringCM과 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 SpringCM을 통합하는 방법에 대해 알아봅니다.

SpringCM과 Azure AD를 통합하면 다음과 같은 이점이 제공됩니다.

- SpringCM에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정으로 SpringCM(Single Sign-on)에 자동으로 로그인하도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](../manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

SpringCM과 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- SpringCM Single Sign-On이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 얻을 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다.  이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 SpringCM 추가
1. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-springcm-from-the-gallery"></a>갤러리에서 SpringCM 추가
SpringCM의 Azure AD 통합을 구성하려면 갤러리의 SpringCM을 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 SpringCM을 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Active Directory][1]

1. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 애플리케이션**으로 이동합니다.

    ![애플리케이션][2]
    
1. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션** 단추를 클릭합니다.

    ![애플리케이션][3]

1. 검색 상자에 **SpringCM**을 입력합니다.

    ![Azure AD 테스트 사용자 만들기](./media/spring-cm-tutorial/tutorial_springcm_search.png)

1. 결과 패널에서 **SpringCM**을 선택하고 **추가** 단추를 클릭하여 애플리케이션을 추가합니다.

    ![Azure AD 테스트 사용자 만들기](./media/spring-cm-tutorial/tutorial_springcm_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD Single Sign-on 구성 및 테스트
이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 SpringCM에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 SpringCM 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 SpringCM의 관련 사용자 간에 연결 관계가 형성되어야 합니다.

SpringCM에서 Azure AD의 **사용자 이름** 값을 **Username** 값으로 할당하여 연결 관계를 설정합니다.

SpringCM에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
1. **[Azure AD 테스트 사용자 만들기](#creating-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On 테스트하는 데 사용합니다.
1. **[SpringCM 테스트 사용자 만들기](#creating-a-springcm-test-user)** - Britta Simon의 Azure AD 표현과 연결되는 대응 사용자를 SpringCM에 만듭니다.
1. **[Azure AD 테스트 사용자 할당](#assigning-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
1. **[Single Sign-On 테스트](#testing-single-sign-on)** - 구성이 작동하는지 확인합니다.

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 SpringCM 애플리케이션에서 Single Sign-On을 구성합니다.

**SpringCM에서 Azure AD Single Sign-on을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **SpringCM** 애플리케이션 통합 페이지에서 **Single sign-on**을 클릭합니다.

    ![Configure Single Sign-On][4]

1. **Single Sign-On** 대화 상자에서 **모드**를 **SAML 기반 로그온**으로 선택하여 Single Sign-On을 사용하도록 설정합니다.
 
    ![Configure Single Sign-On](./media/spring-cm-tutorial/tutorial_springcm_samlbase.png)

1. **SpringCM 도메인 및 URL** 섹션에서 다음 단계를 수행합니다.

    ![Configure Single Sign-On](./media/spring-cm-tutorial/tutorial_springcm_url.png)

    **로그온 URL** 텍스트 상자에서 다음 패턴으로 URL을 입력합니다. `https://na11.springcm.com/atlas/SSO/SSOEndpoint.ashx?aid=<identifier>`

    > [!NOTE] 
    > 이 값은 실제 값이 아닙니다. 이 값을 실제 로그온 URL로 업데이트합니다. 이 값을 얻으려면 [SpringCM 클라이언트 지원 팀](https://knowledge.springcm.com/support)에 문의하세요. 
 
1. **SAML 서명 인증서** 섹션에서 **인증서(원시)** 를 클릭한 후 컴퓨터에 인증서 파일을 저장합니다.

    ![Configure Single Sign-On](./media/spring-cm-tutorial/tutorial_springcm_certificate.png) 

1. **저장** 단추를 클릭합니다.

    ![Configure Single Sign-On](./media/spring-cm-tutorial/tutorial_general_400.png)

1. **SpringCM 구성** 섹션에서 **SpringCM 구성**을 클릭하여 **로그온 구성** 창을 엽니다. **빠른 참조** 섹션에서 **SAML 엔터티 ID 및 SAML Single Sign-On 서비스 URL**을 복사합니다.

    ![Configure Single Sign-On](./media/spring-cm-tutorial/tutorial_springcm_configure.png)     

1. 다른 웹 브라우저 창에서 **SpringCM** 회사 사이트에 관리자로 로그인합니다.

1. 위쪽에 있는 메뉴에서 **이동**을 클릭하고 **기본 설정**을 클릭한 다음 **계정 기본 설정** 섹션에서 **SAML SSO**를 클릭합니다.
   
    ![SAML SSO](./media/spring-cm-tutorial/ic797051.png "SAML SSO")

1. ID 공급자 구성 섹션에서 다음 단계를 수행합니다.
   
    ![ID 공급자 구성](./media/spring-cm-tutorial/ic797052.png "ID 공급자 구성")
    
    a. 다운로드한 Azure Active Directory 인증서를 업로드하려면 **발급자 인증서 선택** 또는 **발급자 인증서 변경**을 클릭합니다.
    
    b. Azure Portal에서 복사한 **SAML 엔터티 ID** 값을 **발급자** 텍스트 상자에 붙여넣습니다.
    
    다. Azure Portal에서 복사한 **SAML Single Sign-On 서비스 URL** 값을 **SP(서비스 공급자)가 시작한 엔드포인트** 텍스트 상자에 붙여넣습니다.
            
    d. **사용**으로 **SAML 사용**을 선택합니다.

    e. **저장**을 클릭합니다.
 
> [!TIP]
> 이제 앱을 설정하는 동안 [Azure Portal ](https://portal.azure.com) 내에서 이러한 지침의 간결한 버전을 읽을 수 있습니다.  **Active Directory &gt; 엔터프라이즈 애플리케이션** 섹션에서 이 앱을 추가한 후에는 **Single Sign-On** 탭을 클릭하고 맨 아래에 있는 **구성** 섹션을 통해 포함된 설명서에 액세스하면 됩니다. 포함된 설명서 기능에 대한 자세한 내용은 [Azure AD 포함된 설명서]( https://go.microsoft.com/fwlink/?linkid=845985)에서 확인할 수 있습니다.
> 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기
이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

![Azure AD 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. **Azure Portal**의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure AD 테스트 사용자 만들기](./media/spring-cm-tutorial/create_aaduser_01.png) 

1. 사용자 목록을 표시하려면 **사용자 및 그룹**으로 이동한 후 **모든 사용자**를 클릭합니다.
    
    ![Azure AD 테스트 사용자 만들기](./media/spring-cm-tutorial/create_aaduser_02.png) 

1. **사용자** 대화 상자를 열려면 대화 상자 위쪽에서 **추가**를 클릭합니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/spring-cm-tutorial/create_aaduser_03.png) 

1. **사용자** 대화 상자 페이지에서 다음 단계를 수행합니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/spring-cm-tutorial/create_aaduser_04.png) 

    a. **이름** 텍스트 상자에 **BrittaSimon**을 입력합니다.

    b. **사용자 이름** 텍스트 상자에 BrittaSimon의 **전자 메일 주소**를 입력합니다.

    다. **암호 표시**를 선택하고 **암호** 값을 적어둡니다.

    d. **만들기**를 클릭합니다.
 
### <a name="creating-a-springcm-test-user"></a>SpringCM 테스트 사용자 만들기

Azure Active Directory 사용자가 SpringCM에 로그인할 수 있도록 하려면 SpringCM으로 프로비전되어야 합니다. SpringCM의 경우 프로비전은 수동 작업입니다.

>[!NOTE]
>자세한 내용은 [SpringCM 사용자 만들기 및 편집](https://knowledge.springcm.com/create-and-edit-a-springcm-user)을 참조하세요. 

**사용자 계정을 SpringCM에 프로비전하려면 다음 단계를 수행합니다.**

1. **SpringCM** 회사 사이트에 관리자 권한으로 로그인합니다.

1. **GOTO**를 클릭하고 **주소록**을 클릭합니다.
   
    ![사용자 만들기](./media/spring-cm-tutorial/ic797054.png "사용자 만들기")

1. **사용자 만들기**를 클릭합니다.

1. **사용자 역할**을 선택합니다.

1. **활성화 메일 보내기**를 선택합니다.

1. 관련된 텍스트 상자에 프로비전할 유효한 Azure Active Directory 사용자 계정의 이름, 성 및 이메일 주소를 입력합니다.

1. **보안 그룹**에 사용자를 추가합니다.

1. **저장**을 클릭합니다.

  >[!NOTE]
  >다른 SpringCM 사용자 계정 생성 도구 또는 SpringCM이 제공한 API를 사용하여 AAD 사용자 계정을 프로비전할 수 있습니다.  
  > 

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 SpringCM에 대한 액세스 권한을 부여합니다.

![사용자 할당][200] 

**Britta Simon을 SpringCM에 할당하려면 다음 단계를 수행합니다.**

1. Azure Portal에서 애플리케이션 보기를 연 다음 디렉터리 보기로 이동하고 **엔터프라이즈 애플리케이션**으로 이동한 후 **모든 애플리케이션**을 클릭합니다.

    ![사용자 할당][201] 

1. 애플리케이션 목록에서 **SpringCM**을 선택합니다.

    ![Configure Single Sign-On](./media/spring-cm-tutorial/tutorial_springcm_app.png) 

1. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    ![사용자 할당][202] 

1. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 할당][203]

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다.

1. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다.

1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.
    
### <a name="testing-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.
 
액세스 패널에서 SpringCM 타일을 클릭하면 SpringCM 애플리케이션에 자동으로 로그온됩니다.

액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](../user-help/active-directory-saas-access-panel-introduction.md)를 참조하세요. 

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](tutorial-list.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/spring-cm-tutorial/tutorial_general_01.png
[2]: ./media/spring-cm-tutorial/tutorial_general_02.png
[3]: ./media/spring-cm-tutorial/tutorial_general_03.png
[4]: ./media/spring-cm-tutorial/tutorial_general_04.png

[100]: ./media/spring-cm-tutorial/tutorial_general_100.png

[200]: ./media/spring-cm-tutorial/tutorial_general_200.png
[201]: ./media/spring-cm-tutorial/tutorial_general_201.png
[202]: ./media/spring-cm-tutorial/tutorial_general_202.png
[203]: ./media/spring-cm-tutorial/tutorial_general_203.png

