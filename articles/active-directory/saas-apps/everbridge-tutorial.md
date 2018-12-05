---
title: '자습서: EverBridge와 Azure Active Directory 통합 | Microsoft 문서'
description: Azure Active Directory 및 EverBridge 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 58d7cd22-98c0-4606-9ce5-8bdb22ee8b3e
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2018
ms.author: jeedes
ms.openlocfilehash: 75bd4325063ff90e4a065c0cf854fa375e58fa95
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/29/2018
ms.locfileid: "52584794"
---
# <a name="tutorial-azure-active-directory-integration-with-everbridge"></a>자습서: Everbridge와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 EverBridge를 통합하는 방법에 대해 알아봅니다.

EverBridge를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- EverBridge에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정으로 EverBridge에 자동으로 로그온(Single Sign-On)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 응용 프로그램 액세스 및 Single Sign-On이란 무엇인가요?](../manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

EverBridge와의 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- EverBridge Single Sign-On이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [1개월 평가판을 얻을](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다.  이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 EverBridge 추가
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-everbridge-from-the-gallery"></a>갤러리에서 EverBridge 추가

EverBridge의 Azure AD 통합을 구성하려면 갤러리의 EverBridge를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 EverBridge를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Azure Active Directory 단추][1]

2. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 응용 프로그램**으로 이동합니다.

    ![엔터프라이즈 응용 프로그램 블레이드][2]
    
3. 새 응용 프로그램을 추가하려면 대화 상자 맨 위 있는 **새 응용 프로그램** 단추를 클릭합니다.

    ![새 응용 프로그램 단추][3]

4. 검색 상자에 **EverBridge**를 입력하고 결과 패널에서 **EverBridge**를 선택한 다음, **추가** 단추를 클릭하여 애플리케이션을 추가합니다.

    ![결과 목록의 EverBridge](./media/everbridge-tutorial/tutorial_everbridge_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 “Britta Simon”이라는 테스트 사용자를 기반으로 EverBridge에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 EverBridge 사용자가 누군지 알고 있어야 합니다. 즉, Azure AD 사용자와 EverBridge의 관련 사용자 간에 연결이 형성되어야 합니다.

EverBridge에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#creating-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On 테스트하는 데 사용합니다.
3. **[EverBridge 테스트 사용자 만들기](#creating-an-everbridge-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 EverBridge에 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assigning-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Single Sign-On 테스트](#testing-single-sign-on)** - 구성이 작동하는지 확인합니다.

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 EverBridge 응용 프로그램에서 Single Sign-On을 구성합니다.

**EverBridge에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **EverBridge** 응용 프로그램 통합 페이지에서 **Single Sign-On**을 클릭합니다.

    ![Single Sign-On 구성 링크][4]

2. **Single Sign-On 방법 선택** 대화 상자에서 **SAML** 모드에 대해 **선택**을 클릭하여 Single Sign-On을 사용하도록 설정합니다.

    ![Configure Single Sign-On](common/tutorial_general_301.png)

3. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 아이콘을 클릭하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![Configure Single Sign-On](common/editconfigure.png)

    >[!NOTE]
    >양쪽 끝, 즉 Azure Portal 및 Everbridge Portal에서 애플리케이션을 관리자 포털 또는 멤버 포털로 구성해야 합니다.

4. **EverBridge** 애플리케이션을 **EverBridge 관리자 포털**로 구성하려면 **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    ![EverBridge 도메인 및 URL Single Sign-On 정보](./media/everbridge-tutorial/tutorial_everbridge_url.png)

    a. **식별자** 텍스트 상자에서 `https://sso.everbridge.net/<API_Name>` 패턴을 사용하여 URL을 입력합니다.

    b. **회신 URL** 텍스트 상자에 다음 패턴으로 URL을 입력합니다.`https://manager.everbridge.net/saml/SSO/<API_Name>/alias/defaultAlias`

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 식별자 및 회신 URL로 해당 값을 업데이트합니다. 이러한 값을 얻으려면 [EverBridge 지원 팀](mailto:support@everbridge.com)에 문의합니다.

5. **EverBridge** 애플리케이션을 **EverBridge 멤버 포털**로 구성하려면 **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    * **IDP** 시작 모드로 애플리케이션을 구성하려는 경우 다음 단계를 수행합니다.

        ![EverBridge 도메인 및 URL Single Sign-On 정보](./media/everbridge-tutorial/tutorial_everbridge_url1.png)

        * **식별자** 텍스트 상자에서 `https://sso.everbridge.net/<API_Name>/<Organization_ID>` 패턴을 사용하여 URL을 입력합니다.

        * **회신 URL** 텍스트 상자에 다음 패턴으로 URL을 입력합니다.`https://member.everbridge.net/saml/SSO/<API_Name>/<Organization_ID>/alias/defaultAlias`

    * **SP** 시작 모드에서 응용 프로그램을 구성하려면 **추가 URL 설정**을 클릭하고 다음 단계를 수행합니다.

        ![EverBridge 도메인 및 URL Single Sign-On 정보](./media/everbridge-tutorial/tutorial_everbridge_url2.png)

        * **로그온 URL** 텍스트 상자에서 다음 패턴으로 URL을 입력합니다. `https://member.everbridge.net/saml/login/<API_Name>/<Organization_ID>/alias/defaultAlias?disco=true`

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 해당 값을 실제 식별자, 회신 URL 및 로그온 URL로 업데이트합니다. 이러한 값을 얻으려면 [EverBridge 지원 팀](mailto:support@everbridge.com)에 문의합니다.

6. **SAML 서명 인증서** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드**를 클릭하고 **페더레이션 메타데이터 XML**을 다운로드한 다음, 컴퓨터에 인증서 파일을 저장합니다.

    ![인증서 다운로드 링크](./media/everbridge-tutorial/tutorial_everbridge_certificate.png) 

7. **EverBridge 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    a. 로그인 URL

    b. Azure AD 식별자

    다. 로그아웃 URL

    ![EverBridge 구성](common/configuresection.png)

8. **EverBridge 관리자 포털** 애플리케이션으로 **EverBridge**에 대해 SSO를 구성하려면 다음 단계를 수행합니다. 
 
9. 다른 웹 브라우저 창에서 EverBridge에 관리자 권한으로 로그인합니다.

9. 위쪽 메뉴에서 **설정** 탭을 클릭하고 **보안**에서 **Single Sign-On**을 선택합니다.
   
    ![Configure Single Sign-On](./media/everbridge-tutorial/tutorial_everbridge_002.png)
   
    a. **이름** 텍스트 상자에 식별자 공급자의 이름(예: 회사 이름)을 입력합니다.
   
    b. **API 이름** 텍스트 상자에 API의 이름을 입력합니다.
   
    다. **파일 선택** 단추를 클릭하여 Azure Portal에서 다운로드한 메타데이터 파일을 업로드합니다.
   
    d. SAML ID 위치에서 **Subject 문의 NameIdentifier 요소에 ID 포함**을 선택합니다.
   
    e. Azure Portal에서 복사한 **로그인 URL** 값을 **ID 공급자 로그인 URL** 텍스트 상자에 붙여넣습니다.
   
    f. 서비스 공급자가 시작한 요청 바인딩에서 **HTTP 리디렉션**을 선택합니다.

    g. 페이지 맨 아래에 있는 **저장**

10. **EverBridge** 애플리케이션에서 **EverBridge 멤버 포털**로 Single Sign-On을 구성하려면 다운로드한 **페더레이션 메타데이터 XML**을 [Everbridge 지원 팀](mailto:support@everbridge.com)에 보내야 합니다. 이렇게 설정하면 SAML SSO 연결이 양쪽에서 제대로 설정됩니다.

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
  
### <a name="creating-an-everbridge-test-user"></a>EverBridge 테스트 사용자 만들기

이 섹션에서는 Everbridge에서 Britta Simon이라는 사용자를 만듭니다. Everbridge 플랫폼에서 사용자를 추가하려면 [EverBridge 지원 팀](mailto:support@everbridge.com)에 문의하세요.

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 EverBridge에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 응용 프로그램**을 선택한 다음, **모든 응용 프로그램**을 선택합니다.

    ![사용자 할당][201]

2. 응용 프로그램 목록에서 **EverBridge**를 선택합니다.

    ![Configure Single Sign-On](./media/everbridge-tutorial/tutorial_everbridge_app.png) 

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    ![사용자 할당][202]

4. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 할당][203]

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택하고 화면 아래쪽에서 **선택** 단추를 클릭합니다.

6. **할당 추가** 대화 상자에서 **할당** 단추를 선택합니다.

### <a name="testing-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 EverBridge 타일을 클릭하면 EverBridge 애플리케이션에 자동으로 로그온됩니다.
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
