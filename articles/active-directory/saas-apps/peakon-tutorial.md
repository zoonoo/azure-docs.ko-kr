---
title: '자습서: Peakon과 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 Peakon 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: a944c397-ed3f-4d45-b9b2-6d4bcb6b0a09
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e32f0ac6fcc6839bad973c3d014436d08ae43896
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56211311"
---
# <a name="tutorial-azure-active-directory-integration-with-peakon"></a>자습서: Peakon과 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Peakon을 통합하는 방법에 대해 알아봅니다.

Peakon을 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- Peakon에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정으로 Peakon에 자동으로 로그온(Single Sign-on)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](../manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

Peakon과 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- Peakon Single Sign-on이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [1개월 평가판을 얻을](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다.  이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 Peakon 추가
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-peakon-from-the-gallery"></a>갤러리에서 Peakon 추가

Peakon의 Azure AD 통합을 구성하려면 갤러리의 Peakon을 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Peakon을 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Azure Active Directory 단추][1]

2. **엔터프라이즈 애플리케이션**으로 이동합니다. 그런 후 **모든 애플리케이션**으로 이동합니다.

    ![엔터프라이즈 애플리케이션 블레이드][2]

3. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션** 단추를 클릭합니다.

    ![새 애플리케이션 단추][3]

4. 검색 상자에 **Peakon**을 입력하고 결과 패널에서 **Peakon**을 선택한 다음, **추가** 단추를 클릭하여 애플리케이션을 추가합니다.

    ![결과 목록의 Peakon](./media/peakon-tutorial/tutorial_peakon_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 Peakon에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 Peakon 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 Peakon의 관련 사용자 간에 연결이 형성되어야 합니다.

Peakon에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#creating-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On 테스트하는 데 사용합니다.
3. **[Peakon 테스트 사용자 만들기](#creating-a-peakon-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 Peakon에 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assigning-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Single Sign-On 테스트](#testing-single-sign-on)** - 구성이 작동하는지 확인합니다.

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 Peakon 애플리케이션에서 Single Sign-On을 구성합니다.

**Peakon에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **Peakon** 애플리케이션 통합 페이지에서 **Single sign-on**을 클릭합니다.

    ![Single Sign-On 구성 링크][4]

2. **Single Sign-On 방법 선택** 대화 상자에서 **SAML** 모드에 대해 **선택**을 클릭하여 Single Sign-On을 사용하도록 설정합니다.

    ![Configure Single Sign-On](common/tutorial_general_301.png)

3. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 아이콘을 클릭하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![Configure Single Sign-On](common/editconfigure.png)

4. **IDP** 시작 모드에서 애플리케이션을 구성하려면 **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    ![Peakon 도메인 및 URL Single Sign-On 정보](./media/peakon-tutorial/tutorial_peakon_url.png)

    a. **식별자** 텍스트 상자에서 `https://app.peakon.com/saml/<companyid>/metadata` 패턴을 사용하여 URL을 입력합니다.

    b. **회신 URL** 텍스트 상자에 다음 패턴으로 URL을 입력합니다.`https://app.peakon.com/saml/<companyid>/assert`

5. **SP** 시작 모드에서 애플리케이션을 구성하려면 **추가 URL 설정**을 클릭하고 다음 단계를 수행합니다.

    ![Peakon 도메인 및 URL Single Sign-On 정보](./media/peakon-tutorial/tutorial_peakon_url1.png)

    **로그온 URL** 텍스트 상자에 URL을 입력합니다. `https://app.peakon.com/login`

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 이러한 값을 자습서 뒷부분에 설명된 실제 식별자 및 회신 URL로 업데이트합니다.

6. **SAML 서명 인증서** 섹션에서 **다운로드**를 클릭하여 **인증서(Raw)** 를 다운로드한 다음, 컴퓨터에 인증서 파일을 저장합니다.

    ![인증서 다운로드 링크](./media/peakon-tutorial/tutorial_peakon_certificate.png) 

7. **Peakon 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    a. 로그인 URL

    b. Azure AD 식별자

    다. 로그아웃 URL

    ![Peakon 구성](common/configuresection.png)

8. 다른 웹 브라우저 창에서 Peakon에 관리자로 로그인합니다.

9. 페이지의 왼쪽에 있는 메뉴 모음에서  **구성**을 클릭한 다음,  **통합**으로 이동합니다.

    ![구성](./media/peakon-tutorial/tutorial_peakon_config.png)

10. **통합** 페이지에서 **Single Sign-On**을 클릭합니다.

    ![단일](./media/peakon-tutorial/tutorial_peakon_single.png)

11. **Single Sign-On** 섹션 아래에서 **사용**을 클릭합니다.

    ![사용](./media/peakon-tutorial/tutorial_peakon_enable.png)

12. **SAML을 사용하여 직원에 대한 Single Sign-On** 섹션에서 다음 단계를 수행합니다.

    ![saml](./media/peakon-tutorial/tutorial_peakon_saml.png)

    a. Azure Portal에서 복사한 **로그인 URL** 값을 **SSO 로그인 URL** 텍스트 상자에 붙여넣습니다.

    b. Azure Portal에서 복사한 **로그아웃 URL** 값을 **SSO 로그아웃 URL** 텍스트 상자에 붙여넣습니다.

    다. **파일 선택**을 클릭하여 Azure Portal에서 다운로드한 인증서를 인증서 상자에 업로드합니다.

    d.  **아이콘** 을 클릭하여 **엔터티 ID** 를 복사하고 Azure Portal의  **기본 SAML 구성** 섹션의  **식별자** 텍스트 상자에 붙여넣습니다.

    e.  **아이콘** 을 클릭하여 **회신 URL(ACS)** 을 복사하고 Azure Portal의  **기본 SAML 구성** 섹션의  **회신 URL** 텍스트 상자에 붙여넣습니다.

    f. 페이지 맨 아래에 있는 **저장**

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

### <a name="creating-a-peakon-test-user"></a>Peakon 테스트 사용자 만들기

Azure AD 사용자가 Peakon에 로그인할 수 있도록 하려면 Peakon으로 프로비전되어야 합니다.  
Peakon의 경우 프로비전은 수동 작업입니다.

**사용자 계정을 프로비전하려면 다음 단계를 수행합니다.**

1. Peakon 회사 사이트에 관리자 권한으로 로그인합니다.

2. 페이지의 왼쪽에 있는 메뉴 모음에서  **구성**을 클릭한 다음,  **직원**으로 이동합니다.

    ![직원](./media/peakon-tutorial/tutorial_peakon_employee.png)

3. 페이지 오른쪽 상단에서  **직원 추가**를 클릭합니다.

      ![직원 추가](./media/peakon-tutorial/tutorial_peakon_addemployee.png)

3. **새 직원** 대화 상자 페이지에서 다음 단계를 수행합니다.

     ![새 직원](./media/peakon-tutorial/tutorial_peakon_create.png)

    a. **이름** 텍스트 상자에 **Britta**로 이름 및 **simon**으로 성을 입력합니다.

    b. **이메일** 텍스트 상자에 **Brittasimon@contoso.com**과 같은 이메일 주소를 입력합니다.

    다. **직원 만들기**를 클릭합니다.

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Peakon에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**을 선택합니다.

    ![사용자 할당][201]

2. 애플리케이션 목록에서 **Peakon**을 선택합니다.

    ![Configure Single Sign-On](./media/peakon-tutorial/tutorial_peakon_app.png) 

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    ![사용자 할당][202]

4. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 할당][203]

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택하고 화면 아래쪽에서 **선택** 단추를 클릭합니다.

6. **할당 추가** 대화 상자에서 **할당** 단추를 선택합니다.

### <a name="testing-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Peakon 타일을 클릭하면 Peakon 애플리케이션에 자동으로 로그온됩니다.
액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](../user-help/active-directory-saas-access-panel-introduction.md)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](tutorial-list.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
