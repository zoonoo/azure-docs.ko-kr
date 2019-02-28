---
title: '자습서: G Suite와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 G Suite 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 38a6ca75-7fd0-4cdc-9b9f-fae080c5a016
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/04/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: be5023696d6aa937672482d618294102505f9f91
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/26/2019
ms.locfileid: "56881212"
---
# <a name="tutorial-azure-active-directory-integration-with-g-suite"></a>자습서: G Suite와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 G Suite를 통합하는 방법에 대해 알아봅니다.
G Suite를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

* G Suite에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
* 사용자가 자신의 Azure AD 계정으로 G Suite에 자동으로 로그온(Single Sign-On)되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)을 참조하세요.
Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>필수 조건

G Suite와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- G Suite Single Sign-on이 설정된 구독
- Google Apps 구독 또는 Google Cloud Platform 구독.

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다. 이 문서는 새 사용자 Single Sign-On 환경을 사용하여 만들어졌습니다. 이전 환경을 계속 사용하고 있으면 설정이 다르게 표시됩니다. G Suite 애플리케이션의 Single Sign-On 설정에서 새로운 환경을 설정할 수 있습니다. **Azure AD, 엔터프라이즈 애플리케이션**으로 이동하고 **G Suite**를 선택하고 **Single Sign-On**을 선택한 다음, **새 환경 사용해 보기**를 클릭합니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [1개월 평가판을 얻을](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다.

## <a name="frequently-asked-questions"></a>질문과 대답

1. **Q: 이 통합이 Google Cloud Platform SSO와 Azure AD의 통합을 지원하나요?**

    A: 예. Google Cloud Platform과 Google Apps는 동일한 인증 플랫폼을 공유합니다. 따라서 GCP 통합을 수행하려면 Google Apps로 SSO를 구성해야 합니다.

2. **Q: Chromebooks 및 기타 크롬 디바이스는 Azure AD Single Sign-On과 호환되나요?**
  
    A: 예, 사용자는 Azure AD 자격 증명을 사용하여 Chromebook 디바이스에 로그인할 수 있습니다. 사용자가 자격 증명을 두 번 입력해야 하는 이유는 이 [G Suite 지원 문서](https://support.google.com/chrome/a/answer/6060880)를 참조하세요.

3. **Q: Single Sign-On을 사용하도록 설정한 경우 사용자가 자신의 Azure AD 자격 증명을 사용하여 Google 클래스룸, GMail, Google 드라이브, YouTube 등과 같은 Google 제품에 로그인할 수 있나요?**

    A: 예, 조직에 대해 어떤 [G Suite](https://support.google.com/a/answer/182442?hl=en&ref_topic=1227583)를 사용하도록 설정할지 또는 사용하지 않도록 설정할지에 따라 다릅니다.

4. **Q: 내 G Suite 사용자의 일부에 대해서 Single Sign-On을 사용하도록 설정할 수 있나요?**

    A: 아니요, Single Sign-On을 켜는 즉시 모든 G Suite 사용자가 Azure AD 자격 증명으로 인증해야 합니다. G Suite는 여러 ID 공급자를 지원하지 않으므로 G Suite 환경의 ID 공급자는 Azure AD 또는 Google 중 하나가 될 수 있지만 동시에 둘 다가 될 수는 없습니다.

5. **Q: 사용자가 Windows를 통해 로그인한 경우 암호 입력을 요청하는 메시지가 표시되지 않고 G Suite에 자동으로 인증되나요?**

    A: 이 시나리오에는 두 가지 옵션을 사용할 수 있습니다. 첫째, [Azure Active Directory 조인](../device-management-introduction.md)을 통해 Windows 10 디바이스에 로그인할 수 있습니다. 또는 [AD FS(Active Directory Federation Services)](../hybrid/plan-connect-user-signin.md) 배포를 통해 Azure AD에 Single Sign-On을 사용할 수 있도록 설정한 온-프레미스 Active Directory에 도메인 가입한 Windows 디바이스에 로그인할 수 있습니다. 두 가지 옵션 모두 Azure AD와 G Suite 간에 Single Sign-On을 사용하도록 설정하려면 다음 자습서의 단계를 수행해야 합니다.

6. **Q: "잘못된 이메일" 오류 메시지가 표시되면 어떻게 해야 하나요?**

    A: 이 설치의 경우 사용자가 로그인할 수 있으려면 이메일 특성이 필요합니다. 이 특성은 수동으로 설정할 수 없습니다.

    이메일 특성은 유효한 Exchange 라이선스가 있는 모든 사용자에 대한 자동으로 채워집니다. 사용자가 이메일을 사용할 수 없는 경우 애플리케이션이 액세스 권한을 얻기 위해 이 특성을 가져와야 하므로 이 오류가 수신됩니다.

    관리자 계정으로 portal.office.com으로 이동한 다음, 관리 센터, 청구서 발송, 구독을 클릭하고 Office 365 구독을 선택한 다음, 사용자에게 할당을 클릭하고 가입을 확인할 사용자를 선택한 후 오른쪽 창에서 라이선스 편집을 클릭합니다.

    O365 라이선스가 할당되면 적용하는 데 몇 분 정도 걸립니다. 그 후 user.mail 특성이 자동으로 채워지며, 문제가 해결됩니다.

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* G Suite가 **SP**에서 시작된 SSO를 지원
* G Suite가 **[자동 사용자 프로비전](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-provisioning-tutorial)** 을 지원

## <a name="adding-g-suite-from-the-gallery"></a>갤러리에서 G Suite 추가

G Suite의 Azure AD 통합을 구성하려면 갤러리의 G Suite를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 G Suite를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure Active Directory 단추](common/select-azuread.png)

2. **엔터프라이즈 응용 프로그램**으로 이동한 다음, **모든 응용 프로그램** 옵션을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션** 단추를 클릭합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에 **G Suite**를 입력하고 결과 패널에서 **G Suite**를 선택한 후 **추가** 단추를 클릭하여 애플리케이션을 추가합니다.

     ![결과 목록의 G Suite](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 **Britta Simon**이라는 테스트 사용자를 기반으로 G Suite에서 Azure AD Single Sign-On을 구성하고 테스트합니다.
Single Sign-On이 작동하려면 Azure AD 사용자와 G Suite의 관련 사용자 간에 연결 관계를 설정해야 합니다.

G Suite에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[G Suite Single Sign-On 구성](#configure-g-suite-single-sign-on)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
3. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[G Suite 테스트 사용자 만들기](#create-g-suite-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 G Suite에 만듭니다.
6. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정합니다.

G Suite에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1. [Azure Pportal](https://portal.azure.com/)의 **G Suite** 애플리케이션 통합 페이지에서 **Single Sign-On**을 선택합니다.

    ![Single Sign-On 구성 링크](common/select-sso.png)

2. **Single Sign-On 방법 선택** 대화 상자에서 **SAML/WS-Fed** 모드를 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Single Sign-On 선택 모드](common/select-saml-option.png)

3. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 아이콘을 클릭하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![기본 SAML 구성 편집](common/edit-urls.png)

4. **기본 SAML 구성** 섹션에서 **Gmail**에 대해 구성하려는 경우 다음 단계를 수행합니다.

    ![G Suite 도메인 및 URL Single Sign-On 정보](common/sp-identifier.png)

    a. **로그온 URL** 텍스트 상자에서 다음 패턴으로 URL을 입력합니다. `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://mail.google.com`

    b. **식별자** 텍스트 상자에서 다음 패턴을 사용하여 URL을 입력합니다.
    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `http://google.com` |
    | `http://google.com/a/<yourdomain.com>` |

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 로그온 URL 및 식별자로 값을 업데이트합니다. 이러한 값을 얻으려면 [G Suite 클라이언트 지원 팀](https://www.google.com/contact/)에 문의하세요.

5. **기본 SAML 구성** 섹션에서 **Google Cloud Platform**에 대해 구성하려는 경우 다음 단계를 수행합니다.

    ![G Suite 도메인 및 URL Single Sign-On 정보](common/sp-identifier.png)

    a. **로그온 URL** 텍스트 상자에서 다음 패턴으로 URL을 입력합니다. `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://console.cloud.google.com `

    b. **식별자** 텍스트 상자에서 다음 패턴을 사용하여 URL을 입력합니다.
    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `http://google.com` |
    | `http://google.com/a/<yourdomain.com>` |
    
    > [!NOTE] 
    > 이러한 값은 실제 값이 아닙니다. 실제 로그온 URL 및 식별자로 값을 업데이트합니다. 이러한 값을 얻으려면 [G Suite 클라이언트 지원 팀](https://www.google.com/contact/)에 문의하세요.

6. G Suite 애플리케이션에는 특정 서식의 SAML 어설션이 필요하기 때문에 SAML 토큰 특성 구성에 사용자 지정 특성 매핑을 추가해야 합니다. 다음 스크린샷은 이에 대한 예제를 보여 줍니다. **고유한 사용자 ID**의 기본값은 **user.userprincipalname**이지만 G Suite에서는 이것이 사용자의 이메일 주소와 매핑되어야 합니다. 목록에서 **user.mail** 특성을 사용하거나 조직 구성을 기반으로 적절한 특성 값을 사용할 수 있기 위해서입니다.

    ![이미지](common/edit-attribute.png)

7. 위의 이미지와 같이 SAML 토큰 특성을 구성하기 위해 **사용자 특성** 대화 상자의 **사용자 클레임** 섹션에서 **편집 아이콘**을 사용하여 클레임을 편집하거나 **새 클레임 추가**를 사용하여 클레임을 추가하고, 다음 단계를 수행합니다.

    | Name | 원본 특성 |
    | ---------------| --------------- |
    | 고유한 사용자 ID | User.mail |

    a. **새 클레임 추가**를 클릭하여 **사용자 클레임 관리** 대화 상자를 엽니다.

    ![이미지](common/new-save-attribute.png)

    ![이미지](common/new-attribute-details.png)

    b. **이름** 텍스트 상자에서 해당 행에 표시된 특성 이름을 입력합니다.

    다. **네임스페이스**를 비워 둡니다.

    d. 원본을 **특성**으로 선택합니다.

    e. **원본 특성** 목록에서 해당 행에 표시된 특성 값을 입력합니다.

    f. **확인**을 클릭합니다.

    g. **저장**을 클릭합니다.

8. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드**를 클릭하여 요구 사항에 따라 제공된 옵션에서 **인증서(Base64)** 를 다운로드한 다음, 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/certificatebase64.png)

9. **G Suite 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

    a. 로그인 URL

    b. Azure AD 식별자

    다. 로그아웃 URL

### <a name="configure-g-suite-single-sign-on"></a>G Suite Single Sign-On 구성

1. 브라우저에서 새 탭을 열고 관리자 계정을 사용하여 [G Suite 관리 콘솔](http://admin.google.com/) 에 로그인합니다.

2. **보안**을 클릭합니다. 링크가 보이지 않으면 화면 아래쪽에 있는 **기타 컨트롤** 메뉴에 숨겨져 있을 수 있습니다.

    ![보안을 클릭합니다.][10]

3. **보안** 페이지에서 **SSO(Single Sign-On) 설정**을 클릭합니다.

    ![SSO를 클릭합니다.][11]

4. 다음 구성을 변경합니다.

    ![SSL 구성][12]

    a. **Setup SSO with third party identity provider**(타사 ID 공급자로 SSO 설정)을 선택합니다.

    b. G Suite의 **로그인 페이지 URL** 필드에 Azure Portal에서 복사한 **로그인 URL** 값을 붙여넣습니다.

    다. G Suite의 **로그아웃 페이지 URL** 필드에 Azure Portal에서 복사한 **로그아웃 URL** 값을 붙여넣습니다.

    d. G Suite의 **암호 변경 URL** 필드에 Azure Portal에서 복사한 **암호 변경 URL** 값을 붙여넣습니다.

    e. G Suite에서 **확인 인증서**에 대해 Azure Portal에서 다운로드한 인증서를 업로드합니다.

    f. **도메인별 발급자 사용**을 선택합니다.

    g. **변경 내용 저장**을 클릭합니다.

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**를 차례로 선택하고 **모든 사용자**를 선택합니다.

    !["사용자 및 그룹" 및 "모든 사용자" 링크](common/users.png)

2. 화면 위쪽에서 **새 사용자**를 선택합니다.

    ![새 사용자 단추](common/new-user.png)

3. 사용자 속성에서 다음 단계를 수행합니다.

    ![사용자 대화 상자](common/user-properties.png)

    a. **이름** 필드에 **BrittaSimon**을 입력합니다.
  
    b. **사용자 이름** 필드에 **brittasimon@yourcompanydomain.extension**을 입력합니다.  
    예를 들어 BrittaSimon@contoso.com

    c. **암호 표시** 확인란을 선택한 다음, [암호] 상자에 표시된 값을 적어둡니다.

    d. **만들기**를 클릭합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 G Suite에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**과 **G Suite**를 차례로 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에 **G Suite**를 입력하고 선택합니다.

    ![애플리케이션 목록의 G Suite 링크](common/all-applications.png)

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 선택합니다.

    !["사용자 및 그룹" 링크](common/users-groups-blade.png)

4. **사용자 추가** 단추를 클릭한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창](common/add-assign-user.png)

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택하고 화면 아래쪽에서 **선택** 단추를 클릭합니다.

6. SAML 어설션 및 **역할 선택** 대화 상자에서 모든 역할 값이 필요한 경우 목록에서 적절한 사용자 역할을 선택한 다음, 화면 맨 아래에 있는 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

### <a name="create-g-suite-test-user"></a>G Suite 테스트 사용자 만들기

이 섹션의 목적은 G Suite Software에서 Britta Simon이라는 사용자를 만드는 것입니다. G Suite는 자동 프로비전을 지원하며 기본적으로 사용하도록 설정됩니다. 이 섹션에는 사용자의 작업이 없습니다. G Suite Software에 사용자가 없는 경우 G Suite Software에 액세스하려고 하면 새 사용자가 만들어집니다.

> [!NOTE]
> Single Sign-On을 테스트하기 전에 Azure AD에서 프로비전을 설정하지 않은 경우 G Suite에 사용자가 이미 있는지 확인하세요.

> [!NOTE]
> 사용자를 수동으로 만들어야 하는 경우 [Google 지원 팀](https://www.google.com/contact/)에 문의하세요.

### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 G Suite 타일을 클릭하면 SSO를 설정한 G Suite에 자동으로 로그인되어야 합니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
- [사용자 프로비저닝 구성](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-provisioning-tutorial)

<!--Image references-->

[10]: ./media/google-apps-tutorial/gapps-security.png
[11]: ./media/google-apps-tutorial/security-gapps.png
[12]: ./media/google-apps-tutorial/gapps-sso-config.png
