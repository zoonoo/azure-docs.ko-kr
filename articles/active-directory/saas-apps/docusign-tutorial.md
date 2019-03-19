---
title: '자습서: DocuSign과 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 DocuSign 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: a691288b-84c1-40fb-84bd-5b06878865f0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2fda9df8e7781a9e0c45fb1aead9f8167f89a833
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57850873"
---
# <a name="tutorial-azure-active-directory-integration-with-docusign"></a>자습서: DocuSign와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 DocuSign을 통합하는 방법에 대해 알아봅니다.

DocuSign을 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- DocuSign에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정으로 DocuSign에 자동으로 로그온(Single Sign-On)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](../manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

DocuSign과 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- DocuSign Single Sign-on이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [1개월 평가판을 얻을](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다.  이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 DocuSign 추가
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-docusign-from-the-gallery"></a>갤러리에서 DocuSign 추가

DocuSign의 Azure AD 통합을 구성하려면 갤러리의 DocuSign을 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 DocuSign을 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Azure Active Directory 단추][1]

2. **엔터프라이즈 애플리케이션**으로 이동합니다. 그런 후 **모든 애플리케이션**으로 이동합니다.

    ![엔터프라이즈 애플리케이션 블레이드][2]

3. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션** 단추를 클릭합니다.

    ![새 애플리케이션 단추][3]

4. 검색 상자에 **DocuSign**을 입력하고 결과 패널에서 **DocuSign**을 선택한 다음, **추가** 단추를 클릭하여 애플리케이션을 추가합니다.

    ![결과 목록의 DocuSign](./media/docusign-tutorial/tutorial_docusign_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 “Britta Simon”이라는 테스트 사용자를 기반으로 DocuSign에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 DocuSign 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 DocuSign의 관련 사용자 간에 연결이 형성되어야 합니다.

DocuSign에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#creating-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On 테스트하는 데 사용합니다.
3. **[DocuSign 테스트 사용자 만들기](#creating-a-docusign-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 DocuSign에 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assigning-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Single Sign-On 테스트](#testing-single-sign-on)** - 구성이 작동하는지 확인합니다.

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 DocuSign 애플리케이션에서 Single Sign-On을 구성합니다.

**DocuSign에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **DocuSign** 애플리케이션 통합 페이지에서 **Single Sign-On**을 클릭합니다.

    ![Single Sign-On 구성 링크][4]

2. **Single Sign-On 방법 선택** 대화 상자에서 **SAML** 모드에 대해 **선택**을 클릭하여 Single Sign-On을 사용하도록 설정합니다.

    ![Configure Single Sign-On](common/tutorial_general_301.png)

3. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 아이콘을 클릭하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![Configure Single Sign-On](common/editconfigure.png)

4. **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    ![DocuSign 도메인 및 URL Single Sign-On 정보](./media/docusign-tutorial/tutorial_docusign_url.png)

    a. **로그온 URL** 텍스트 상자에서 다음 패턴으로 URL을 입력합니다. `https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2/login/sp/<IDPID>`

    b. **식별자** 텍스트 상자에서 `https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 자습서 뒷부분에 있는 **SAML 2.0 엔드포인트 보기** 섹션에 설명된 실제 로그온 URL 및 식별자로 값을 업데이트합니다.

5. **SAML 서명 인증서** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드**를 클릭하고 **인증서(Base64)** 다운로드한 다음, 컴퓨터에 인증서 파일을 저장합니다.

    ![인증서 다운로드 링크](./media/docusign-tutorial/tutorial_docusign_certificate.png) 

6. **DocuSign 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    a. 로그인 URL

    b. Azure AD 식별자

    다. 로그아웃 URL

    ![DocuSign 구성](common/configuresection.png)

7. 다른 웹 브라우저 창에서 **DocuSign 관리 포털**에 관리자로 로그인합니다.

8. 오른쪽 위 모서리에서 프로필 **로고**를 클릭한 다음, **관리자로 이동**을 클릭합니다.
  
    ![Single Sign-On 구성][51]

9. 도메인 솔루션 페이지에서 **도메인**을 클릭합니다.

    ![Single Sign-On 구성][50]

10. **도메인** 섹션에서 **도메인 클레임**을 클릭합니다.

    ![Single Sign-On 구성][52]

11. **도메인 클레임** 대화 상자의 **도메인 이름** 텍스트 상자에 회사 도메인을 입력한 다음, **클레임**을 클릭합니다. 도메인을 확인하고 상태가 활성인지 확인합니다.

    ![Single Sign-On 구성][53]

12. 도메인 솔루션 페이지에서 **ID 공급 기업**을 클릭합니다.
  
    ![Single Sign-On 구성][54]

13. **ID 공급 기업** 섹션에서 **ID 공급 기업 추가**를 클릭합니다. 

    ![Single Sign-On 구성][55]

14. **ID 공급자 설정** 페이지에서 다음 단계를 수행합니다.

    ![Single Sign-On 구성][56]

    a. **이름** 텍스트 상자에 구성할 고유한 이름을 입력합니다. 공백을 사용하지 마세요.

    b. Azure Portal에서 복사한 **Azure AD ID** 값을 **ID 공급 기업 발급자 텍스트 상자**에 붙여넣습니다.

    다. Azure Portal에서 복사한 **로그인 URL** 값을 **ID 공급 기업 로그인 URL** 텍스트 상자에 붙여넣습니다.

    d. Azure Portal에서 복사한 **로그인 URL** 값을 **ID 공급 기업 로그아웃 URL** 텍스트 상자에 붙여넣습니다.

    e. **Sign AuthN 요청**을 선택합니다.

    f. **AuthN 요청 보내기**로 **POST**를 선택합니다.

    g. **로그아웃 요청 보내기**로 **GET**을 선택합니다.

    h. **사용자 지정 특성 매핑** 섹션에서 **새 매핑 추가**를 클릭합니다.

    ![Single Sign-On 구성][62]

    i. Azure AD 클레임을 사용하여 매핑할 필드를 선택합니다. 이 예제의 **emailaddress** 클레임은 **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress** 값에 매핑됩니다. 이는 이메일 클레임에 대한 Azure AD의 기본 클레임입니다. 그런 다음, **저장**을 클릭합니다.

    ![Single Sign-On 구성][57]

    > [!NOTE]
    > 적절한 **사용자 ID**를 사용하여 Azure AD에서 DocuSign 사용자 매핑으로 사용자를 매핑합니다. 적절한 필드를 선택하고 조직 설정에 따라 적절한 값을 입력합니다.

    j. **ID 공급 기업 인증서** 섹션에서 **인증서 추가**를 클릭한 다음, Azure AD 포털에서 다운로드한 인증서를 업로드하고 **저장**을 클릭합니다.

    ![Single Sign-On 구성][58]

    k. **ID 공급 기업** 섹션에서 **작업**을 클릭한 다음, **엔드포인트**를 클릭합니다.

    ![Single Sign-On 구성][59]

    l. **DocuSign 관리자 포털**의 **SAML 2.0 엔드포인트 보기** 섹션에서 다음 단계를 수행합니다.

    ![Single Sign-On 구성][60]

    * **서비스 공급 기업 발급자 URL**을 복사한 다음, Azure Portal의 **DocuSign 도메인 및 URL** 섹션에 있는 **식별자** 텍스트 상자에 붙여넣습니다.

    * **서비스 공급 기업 로그인 URL**을 복사한 다음, Azure Portal의 **DocuSign 도메인 및 URL** 섹션에 있는 **로그인 URL** 텍스트 상자에 붙여넣습니다.

    * 페이지 맨 아래에 있는 **닫기**

### <a name="creating-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**를 차례로 선택하고 **모든 사용자**를 선택합니다.

    ![Azure AD 사용자 만들기][100]

2. 화면 위쪽에서 **새 사용자**를 선택합니다.

    ![Azure AD 테스트 사용자 만들기](common/create_aaduser_01.png) 

3. 사용자 속성에서 다음 단계를 수행합니다.

    ![Azure AD 테스트 사용자 만들기](common/create_aaduser_02.png)

    a. **이름** 필드에 **BrittaSimon**을 입력합니다.
  
    b. 에 **사용자 이름** 필드에 입력 **brittasimon\@yourcompanydomain.extension**  
    예를 들어 BrittaSimon@contoso.com

    다. **속성**을 선택하고 **암호 표시** 확인란을 선택한 다음, 암호 상자에 표시된 값을 적어 둡니다.

    d. **만들기**를 선택합니다.

### <a name="creating-a-docusign-test-user"></a>DocuSign 테스트 사용자 만들기

이 섹션은 DocuSign에서 Britta Simon이라는 사용자를 만들기 위한 것입니다. DocuSign은 Just-In-Time 프로비전을 지원하며 기본적으로 사용하도록 설정됩니다. 이 섹션에 작업 항목이 없습니다. 새 사용자가 아직 존재하지 않는 경우 DocuSign에 액세스하는 동안 만들어집니다.
>[!Note]
>사용자를 수동으로 만들어야 하는 경우  [DocuSign 지원 팀](https://support.docusign.com/)에 문의하세요.

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 DocuSign에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**을 선택합니다.

    ![사용자 할당][201]

2. 애플리케이션 목록에서 **DocuSign**을 선택합니다.

    ![Configure Single Sign-On](./media/docusign-tutorial/tutorial_docusign_app.png)

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    ![사용자 할당][202]

4. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 할당][203]

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택하고 화면 아래쪽에서 **선택** 단추를 클릭합니다.

6. **할당 추가** 대화 상자에서 **할당** 단추를 선택합니다.

### <a name="testing-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 DocuSign 타일을 클릭하면 DocuSign 애플리케이션에 자동으로 로그온됩니다.
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
[50]: ./media/docusign-tutorial/tutorial_docusign_18.png
[51]: ./media/docusign-tutorial/tutorial_docusign_21.png
[52]: ./media/docusign-tutorial/tutorial_docusign_22.png
[53]: ./media/docusign-tutorial/tutorial_docusign_23.png
[54]: ./media/docusign-tutorial/tutorial_docusign_19.png
[55]: ./media/docusign-tutorial/tutorial_docusign_20.png
[56]: ./media/docusign-tutorial/tutorial_docusign_24.png
[57]: ./media/docusign-tutorial/tutorial_docusign_25.png
[58]: ./media/docusign-tutorial/tutorial_docusign_26.png
[59]: ./media/docusign-tutorial/tutorial_docusign_27.png
[60]: ./media/docusign-tutorial/tutorial_docusign_28.png
[61]: ./media/docusign-tutorial/tutorial_docusign_29.png
[62]: ./media/docusign-tutorial/tutorial_docusign_30.png
