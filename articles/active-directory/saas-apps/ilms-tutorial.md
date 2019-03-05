---
title: '자습서: iLMS와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 iLMS 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d6e11639-6cea-48c9-b008-246cf686e726
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3d528195161b987dda783c4b22721e950f702f94
ms.sourcegitcommit: 1afd2e835dd507259cf7bb798b1b130adbb21840
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/28/2019
ms.locfileid: "56984461"
---
# <a name="tutorial-azure-active-directory-integration-with-ilms"></a>자습서: iLMS와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 iLMS를 통합하는 방법에 대해 알아봅니다.
iLMS를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

* iLMS에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
* 사용자가 해당 Azure AD 계정으로 iLMS에 자동으로 로그인(Single Sign-On)되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)을 참조하세요.
Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>필수 조건

iLMS와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 구할 수 있습니다.
* iLMS Single Sign-On이 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* iLMS에서 **SP 및 IDP** 시작 SSO를 지원합니다.

## <a name="adding-ilms-from-the-gallery"></a>갤러리에서 iLMS 추가

iLMS가 Azure AD에 통합되도록 구성하려면 갤러리에서 iLMS를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 iLMS를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure Active Directory 단추](common/select-azuread.png)

2. **엔터프라이즈 응용 프로그램**으로 이동한 다음, **모든 응용 프로그램** 옵션을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션** 단추를 클릭합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에 **iLMS**를 입력하고 결과 패널에서 **iLMS**를 선택한 후 **추가** 단추를 클릭하여 애플리케이션을 추가합니다.

     ![결과 목록의 iLMS](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 **Britta Simon**이라는 테스트 사용자를 기반으로 iLMS에서 Azure AD Single Sign-On을 구성하고 테스트합니다.
Single Sign-On이 작동하려면 Azure AD 사용자와 iLMS의 관련 사용자 간에 연결 관계를 설정해야 합니다.

iLMS에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[iLMS Single Sign-On 구성](#configure-ilms-single-sign-on)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
3. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[iLMS 테스트 사용자 만들기](#create-ilms-test-user)** - Britta Simon의 Azure AD 표현과 연결되는 대응 사용자를 iLMS에 만듭니다.
6. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정합니다.

iLMS에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **iLMS** 애플리케이션 통합 페이지에서 **Single Sign-On**을 선택합니다.

    ![Single Sign-On 구성 링크](common/select-sso.png)

2. **Single Sign-On 방법 선택** 대화 상자에서 **SAML/WS-Fed** 모드를 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Single Sign-On 선택 모드](common/select-saml-option.png)

3. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 아이콘을 클릭하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![기본 SAML 구성 편집](common/edit-urls.png)

4. **IDP** 시작 모드에서 애플리케이션을 구성하려면 **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    ![iLMS 도메인 및 URL Single Sign-On 정보](common/idp-intiated.png)

    a. **식별자** 텍스트 상자에 iLMS 관리 포털에 있는 SAML 설정의 **서비스 공급자** 섹션에서 복사한 **식별자** 값을 붙여 넣습니다.

    b. **회신 URL** 텍스트 상자에 iLMS 관리 포털에 있는 SAML 설정의 **서비스 공급자** 섹션에서 복사한 다음과 같은 패턴의 **엔드포인트(URL)** 값을 붙여 넣습니다. `https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`

5. **SP** 시작 모드에서 애플리케이션을 구성하려면 **추가 URL 설정**을 클릭하고 다음 단계를 수행합니다.

    ![iLMS 도메인 및 URL Single Sign-On 정보](common/metadata-upload-additional-signon.png)

    **로그온 URL** 텍스트 상자에 iLMS 관리 포털에 있는 SAML 설정의 **서비스 공급자** 섹션에서 복사한 **엔드포인트(URL)** 값을 다음과 같은 패턴으로 붙여 넣습니다. `https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`

6. JIT 프로비전을 사용하도록 설정하려면 iLMS 애플리케이션에서 특정 서식의 SAML 어설션이 필요합니다. 이에 따라 SAML 토큰 특성 구성에 사용자 지정 특성 매핑을 추가해야 합니다. 다음 스크린샷에서는 기본 특성의 목록을 보여 줍니다.  **편집**  단추를 클릭하여 사용자 특성 대화 상자를 엽니다.

    ![이미지](common/edit-attribute.png)

    > [!NOTE]
    > 이러한 특성을 매핑하려면 iLMS에서 **Create Un-recognized User Account(인식할 수 없는 사용자 계정 만들기)** 를 사용하도록 설정해야 합니다. 특성 구성을 이해하려면 [여기](http://support.inspiredelearning.com/customer/portal/articles/2204526)의 지침을 참조하세요.

7. 위에서 언급한 특성 외에도, iLMS 애플리케이션에는 SAML 응답에서 다시 전달되어야 하는 몇 가지 특성이 추가로 필요합니다. **사용자 특성** 대화 상자의 **사용자 클레임** 섹션에서 다음 단계를 수행하여 아래 표와 같은 SAML 토큰 특성을 추가합니다.

    | Name | 원본 특성|
    | --------|------------- |
    | division | user.department |
    | region | user.state |
    | department | user.jobtitle |

    a. **새 클레임 추가**를 클릭하여 **사용자 클레임 관리** 대화 상자를 엽니다.

    ![이미지](common/new-save-attribute.png)

    ![이미지](common/new-attribute-details.png)

    b. **이름** 텍스트 상자에서 해당 행에 표시된 특성 이름을 입력합니다.

    다. **네임스페이스**를 비워 둡니다.

    d. 원본을 **특성**으로 선택합니다.

    e. **원본 특성** 목록에서 해당 행에 표시된 특성 값을 입력합니다.

    f. **확인**을 클릭합니다.

    g. **저장**을 클릭합니다.

8. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드**를 클릭하여 요구 사항에 따라 제공된 옵션에서 **페더레이션 메타데이터 XML**을 다운로드하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

9. **iLMS 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

    a. 로그인 URL

    b. Azure AD 식별자

    다. 로그아웃 URL

### <a name="configure-ilms-single-sign-on"></a>iLMS Single Sign-On 구성

1. 다른 웹 브라우저 창에서 **iLMS 관리 포털**에 관리자로 로그인합니다.

2. **Settings(설정)** 탭에서 **SSO:SAML**을 클릭하여 SAML 설정을 열고 다음 단계를 수행합니다.

    ![Configure Single Sign-On](./media/ilms-tutorial/1.png)

3. **Service Provider(서비스 공급자)** 섹션을 확장하고 **Identifier(식별자)** 및 **Endpoint (URL)(엔드포인트 URL)** 값을 복사합니다.

    ![Configure Single Sign-On](./media/ilms-tutorial/2.png) 

4. **Identity Provider(ID 공급자)** 섹션에서 **Import Metadata(메타데이터 가져오기)** 를 클릭합니다.

5. Azure Portal에서 다운로드한 **페더레이션 메타데이터** 파일을 **SAML 서명 인증서** 섹션에서 선택합니다.

    ![Configure Single Sign-On](./media/ilms-tutorial/tutorial_ilms_ssoconfig1.png)

6. 인식할 수 없는 사용자의 iLMS 계정을 만들기 위해 JIT 프로비전을 사용하도록 설정하려면 아래 단계를 수행합니다.

    a. **Create Un-recognized User Account(인식할 수 없는 사용자 계정 만들기)** 를 선택합니다.

    ![Configure Single Sign-On](./media/ilms-tutorial/tutorial_ilms_ssoconfig2.png)

    b. Azure AD의 특성을 iLMS의 특성과 매핑합니다. 특성 열에 특성 이름 또는 기본값을 지정합니다.

    다. **Business Rules(비즈니스 규칙)** 탭으로 이동하여 다음 단계를 수행합니다.

    ![Configure Single Sign-On](./media/ilms-tutorial/5.png)

    d. Single Sign-on 시 존재하지 않는 지역, 국 및 부서를 만들려면 **Create Un-recognized Regions, Divisions and Departments(인식할 수 없는 지역, 국 및 부서 만들기)** 를 선택합니다.

    e. 사용자의 프로필을 Single Sign-on마다 업데이트할지 여부를 지정하려면 **Update User Profile During Sign-in(로그인하는 동안 사용자 프로필 업데이트)** 를 선택합니다.

    f. **Update Blank Values for Non Mandatory Fields in User Profile(사용자 프로필의 필수가 아닌 필드에 대한 빈 값 업데이트)** 옵션이 선택되어 있으면 로그인 시 비어 있는 선택적 프로필 필드로 인해 사용자의 iLMS 프로필에 해당 필드에 대한 빈 값을 포함하게 됩니다.

    g. **Send Error Notification Email(오류 알림 전자 메일 보내기)** 를 선택하고 오류 알림 전자 메일을 수신할 사용자의 전자 메일을 입력합니다.

7. **저장** 단추를 클릭하여 설정을 저장합니다.

    ![Configure Single Sign-On](./media/ilms-tutorial/save.png)

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 iLMS에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**과 **iLMS**를 차례로 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **iLMS**를 선택합니다.

    ![애플리케이션 목록의 iLMS 링크](common/all-applications.png)

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 선택합니다.

    !["사용자 및 그룹" 링크](common/users-groups-blade.png)

4. **사용자 추가** 단추를 클릭한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창](common/add-assign-user.png)

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택하고 화면 아래쪽에서 **선택** 단추를 클릭합니다.

6. SAML 어설션 및 **역할 선택** 대화 상자에서 모든 역할 값이 필요한 경우 목록에서 적절한 사용자 역할을 선택한 다음, 화면 맨 아래에 있는 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

### <a name="create-ilms-test-user"></a>iLMS 테스트 사용자 만들기

애플리케이션이 Just-In-Time 사용자 프로비전을 지원하며 인증 후에는 애플리케이션에서 자동으로 사용자가 생성됩니다. JIT는 iLMS 관리 포털에서 SAML 구성을 설정하는 동안 **Create Un-recognized User Account(인식할 수 없는 사용자 계정 만들기)** 확인란을 클릭한 경우에 작동합니다.

사용자를 수동으로 만들어야 하는 경우에는 아래 단계를 수행합니다.

1. iLMS 회사 사이트에 관리자 권한으로 로그인합니다.

2. **사용자** 탭에서 **사용자 등록**을 클릭하여 **사용자 등록** 페이지를 엽니다.

   ![직원 추가](./media/ilms-tutorial/3.png)

3. **사용자 등록** 페이지에서 다음 단계를 수행합니다.

    ![직원 추가](./media/ilms-tutorial/create_testuser_add.png)

    a. **이름** 텍스트 상자에 이름(예: Britta)을 입력합니다.

    b. **성** 텍스트 상자에 성(예: Simon)을 입력합니다.

    다. **이메일 ID** 텍스트 상자에 사용자의 이메일 주소(예: BrittaSimon@contoso.com)를 입력합니다.

    d. **Region(지역)** 드롭다운에서 지역에 대한 값을 선택합니다.

    e. **Division(국)** 드롭다운에서 국에 대한 값을 선택합니다.

    f. **Department(부서)** 드롭다운에서 부서에 대한 값을 선택합니다.

    g. **저장**을 클릭합니다.

    > [!NOTE]
    > **Send Registration Mail(등록 메일 보내기)** 확인란을 선택하여 사용자에게 등록 메일을 보낼 수 있습니다.

### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 iLMS 타일을 클릭하면 SSO를 설정한 iLMS에 자동으로 로그인됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS 앱을 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)