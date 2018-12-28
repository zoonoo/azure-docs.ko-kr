---
title: '자습서: TextMagic과 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 TextMagic 간 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3e5b49d2-7096-46bc-a9ce-90e09177ba28
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2018
ms.author: jeedes
ms.openlocfilehash: 5ab193d908063230946ebb2bb6320ab50bf14971
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/07/2018
ms.locfileid: "53014891"
---
# <a name="tutorial-azure-active-directory-integration-with-textmagic"></a>자습서: TextMagic과 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 TextMagic을 통합하는 방법에 대해 알아봅니다.

TextMagic을 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- Azure AD에서 TextMagic에 대한 액세스 권한이 있는 사용자를 제어할 수 있습니다.
- 사용자가 자신의 Azure AD 계정으로 TextMagic에 자동으로 로그온(Single Sign-on)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 응용 프로그램 액세스 및 Single Sign-On이란 무엇인가요?](../manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

TextMagic과 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- TextMagic Single Sign-On이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [1개월 평가판을 얻을](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다.  이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 TextMagic 추가
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-textmagic-from-the-gallery"></a>갤러리에서 TextMagic 추가

Azure AD에 TextMagic 통합을 구성하려면 갤러리의 TextMagic을 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 TextMagic을 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Azure Active Directory 단추][1]

2. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 응용 프로그램**으로 이동합니다.

    ![엔터프라이즈 응용 프로그램 블레이드][2]
    
3. 새 응용 프로그램을 추가하려면 대화 상자 맨 위 있는 **새 응용 프로그램** 단추를 클릭합니다.

    ![새 응용 프로그램 단추][3]

4. 검색 상자에 **TextMagic**을 입력하고 결과 패널에서 **TextMagic**을 선택한 후 **추가** 단추를 클릭하여 응용 프로그램을 추가합니다.

    ![결과 목록의 TextMagic](./media/textmagic-tutorial/tutorial_textmagic_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 “Britta Simon”이라는 테스트 사용자를 기반으로 TextMagic에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 TextMagic 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 TextMagic의 관련 사용자 간에 연결 관계가 형성되어야 합니다.

TextMagic에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#creating-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On 테스트하는 데 사용합니다.
3. **[TextMagic 테스트 사용자 만들기](#creating-a-textmagic-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 TextMagic에 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assigning-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Single Sign-On 테스트](#testing-single-sign-on)** - 구성이 작동하는지 확인합니다.

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 TextMagic 응용 프로그램에서 Single Sign-On을 구성합니다.

**TextMagic에서 Azure AD Single Sign-on을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **TextMagic** 응용 프로그램 통합 페이지에서 **Single Sign-On**을 클릭합니다.

    ![Single Sign-On 구성 링크][4]

2. **Single Sign-On 방법 선택** 대화 상자에서 **SAML** 모드에 대해 **선택**을 클릭하여 Single Sign-On을 사용하도록 설정합니다.

    ![Configure Single Sign-On](common/tutorial_general_301.png)

3. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 아이콘을 클릭하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![Configure Single Sign-On](common/editconfigure.png)

4. **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    ![TextMagic 도메인 및 URL Single Sign-On 정보](./media/textmagic-tutorial/tutorial_textmagic_url.png)

    **식별자** 텍스트 상자에 URL `https://my.textmagic.com/saml/metadata`를 입력합니다.

5. TextMagic 애플리케이션은 특정 형식의 SAML 어설션이 필요합니다. 이 응용 프로그램에 대해 다음 클레임을 구성합니다. 응용 프로그램 통합 페이지의 **사용자 특성 및 클레임** 섹션에서 이러한 특성의 값을 관리할 수 있습니다. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 단추를 클릭하여 **사용자 특성 및 클레임** 대화 상자를 엽니다.

    ![이미지](./media/textmagic-tutorial/i4-attribute.png)

6. **사용자 특성 및 클레임** 대화 상자의 **사용자 클레임** 섹션에서 위의 이미지에 표시된 것과 같이 SAML 토큰 특성을 구성하고 다음 단계를 수행합니다.

    | 이름  | 원본 특성  | 네임스페이스 |
    | --------------- | --------------- | --------------- |
    | company | user.companyname | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | firstname               | user.givenname |  http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | Lastname            | user.surname |  http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | phone               | user.telephonenumber |  http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    
    a. **아이콘 편집**을 클릭하여 **user.userprinicipalname**의 **이름 식별자 값**을 **user.mail**로 편집합니다.

    ![TextMagic 특성](./media/textmagic-tutorial/tutorial_textmagic_email.png)

    b. **새 클레임 추가**를 클릭하여 **사용자 클레임 관리** 대화 상자를 엽니다.

    ![이미지](./common/new_save_attribute.png)

    ![이미지](./common/new_attribute_details.png)

    다. **이름** 텍스트 상자에서 해당 행에 표시된 특성 이름을 입력합니다.

    d. **네임스페이스** 값을 입력합니다.

    e. 원본을 **특성**으로 선택합니다.

    f. **원본 특성** 목록에서 해당 행에 표시된 특성 값을 입력합니다.

    g. **Ok**를 클릭합니다.

    h. **저장**을 클릭합니다. 

7. **SAML 서명 인증서** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드**를 클릭하고 **인증서(Base64)** 다운로드한 다음, 컴퓨터에 인증서 파일을 저장합니다.

    ![인증서 다운로드 링크](./media/textmagic-tutorial/tutorial_textmagic_certificate.png) 

8. **TextMagic 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    a. 로그인 URL

    b. Azure AD 식별자

    다. 로그아웃 URL

    ![TextMagic 구성](common/configuresection.png)

9. 다른 웹 브라우저 창에서 TextMagic 회사 사이트에 관리자로 로그인합니다.

10. 사용자 이름에서 **계정 설정**을 선택합니다.

    ![TextMagic 구성](./media/textmagic-tutorial/config1.png)

11. **SSO(Single Sign-On)** 탭을 클릭하고 다음 필드를 입력합니다.  
    
    ![TextMagic 구성](./media/textmagic-tutorial/config2.png)

    a. Azure Portal에서 복사한  **Azure AD 식별자** 값을  **ID 공급 기업 엔터티 ID:**  텍스트 상자에 붙여넣습니다.

    b. Azure Portal에서 복사한  **로그인 URL** 값을  **ID 공급 기업 SSO URL:**  텍스트 상자에 붙여넣습니다.

    다. Azure Portal에서 복사한  **로그인 URL** 값을  **ID 공급 기업 SLO URL:**  텍스트 상자에 붙여넣습니다.

    d. Azure Portal에서 다운로드한 **base-64로 인코딩된 인증서**를 메모장에서 열고, 콘텐츠를 클립보드에 복사한 다음,  **공용 x509 인증서**  텍스트 상자에 붙여넣습니다.

### <a name="creating-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**를 차례로 선택하고 **모든 사용자**를 선택합니다.

    ![Azure AD 사용자 만들기][100]

2. 화면 위쪽에서 **새 사용자**를 선택합니다.

    ![Azure AD 테스트 사용자 만들기](common/create_aaduser_01.png) 

3. 사용자 속성에서 다음 단계를 수행합니다.

    ![Azure AD 테스트 사용자 만들기](common/create_aaduser_02.png)

    a. **이름** 필드에 **BrittaSimon**을 입력합니다.
  
    b. **사용자 이름** 필드에 **brittasimon@yourcompanydomain.extension**을 입력합니다.  
    예를 들어 BrittaSimon@contoso.com

    다. **속성**을 선택하고 **암호 표시** 확인란을 선택한 다음, 암호 상자에 표시된 값을 적어 둡니다.

    d. **만들기**를 선택합니다.
  
### <a name="creating-a-textmagic-test-user"></a>TextMagic 테스트 사용자 만들기

애플리케이션이 **JIT(Just-in-time) 사용자 프로비저닝**을 지원하며 인증 후에 애플리케이션에서 사용자가 자동으로 만들어집니다. 시스템에서 하위 계정을 활성화하려면 처음 로그인할 때 정보를 한 번만 입력해야 합니다.
이 섹션에 작업 항목이 없습니다.

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 TextMagic에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 응용 프로그램**을 선택한 다음, **모든 응용 프로그램**을 선택합니다.

    ![사용자 할당][201]

2. 응용 프로그램 목록에서 **TextMagic**을 선택합니다.

    ![Configure Single Sign-On](./media/textmagic-tutorial/tutorial_textmagic_app.png) 

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    ![사용자 할당][202]

4. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 할당][203]

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택하고 화면 아래쪽에서 **선택** 단추를 클릭합니다.

6. **할당 추가** 대화 상자에서 **할당** 단추를 선택합니다.

### <a name="testing-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 TextMagic 타일을 클릭하면 TextMagic 응용 프로그램에 자동으로 로그온됩니다.
액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](../user-help/active-directory-saas-access-panel-introduction.md)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
