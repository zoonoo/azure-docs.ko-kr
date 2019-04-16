---
title: '자습서: Cisco Webex와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 Cisco Webex 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: c47894b1-f5df-4755-845d-f12f4c602dc4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/28/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 05127e8ecfe68b4cb6330f838f252557bbd5e11d
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59272702"
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-webex"></a>자습서: Cisco Webex와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Cisco Webex를 통합하는 방법에 대해 알아봅니다.
Cisco Webex를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

* Cisco Webex에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
* 사용자가 해당 Azure AD 계정으로 Cisco Webex에 자동으로 로그인(Single Sign-On)되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)을 참조하세요.
Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>필수 조건

Cisco Webex와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 구할 수 있습니다.
* Cisco Webex Single Sign-On을 사용하도록 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* Cisco Webex에서 **SP** 시작 SSO를 지원합니다.

* Cisco Webex에서 **자동** 사용자 프로비전을 지원합니다.

## <a name="adding-cisco-webex-from-the-gallery"></a>갤러리에서 Cisco Webex 추가

Cisco Webex의 Azure AD 통합을 구성하려면 갤러리의 Cisco Webex를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Cisco Webex를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure Active Directory 단추](common/select-azuread.png)

2. **엔터프라이즈 응용 프로그램**으로 이동한 다음, **모든 응용 프로그램** 옵션을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션** 단추를 클릭합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에서 **Cisco Webex**를 입력하고, 결과 패널에서 **Cisco Webex**를 선택한 다음, **추가** 단추를 클릭하여 애플리케이션을 추가합니다.

     ![결과 목록의 Cisco Webex](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 **Britta Simon**이라는 테스트 사용자를 기반으로 하여 Cisco Webex에서 Azure AD Single Sign-On을 구성하고 테스트합니다.
Single Sign-On이 작동하려면 Azure AD 사용자와 Cisco Webex의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Cisco Webex에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Cisco Webex Single Sign-On 구성](#configure-cisco-webex-single-sign-on)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
3. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Cisco Webex 테스트 사용자 만들기](#create-cisco-webex-test-user)** - Azure AD 표현과 연결된 Britta Simon에 해당하는 사용자를 Cisco Webex에 만듭니다.
6. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정합니다.

Cisco Webex에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **Cisco Webex** 애플리케이션 통합 페이지에서 **Single Sign-On**을 선택합니다.

    ![Single Sign-On 구성 링크](common/select-sso.png)

2. **Single Sign-On 방법 선택** 대화 상자에서 **SAML/WS-Fed** 모드를 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Single Sign-On 선택 모드](common/select-saml-option.png)

3. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 아이콘을 클릭하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![기본 SAML 구성 편집](common/edit-urls.png)

4. **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    ![Cisco Webex 도메인 및 URL Single Sign-On 정보](common/sp-identifier.png)

    a. **로그온 URL** 텍스트 상자에서 URL을 다음과 같이 입력합니다. `https://web.ciscospark.com/#/signin`

    b. **식별자(엔터티 ID)** 텍스트 상자에 다음 패턴을 사용하여 URL을 입력합니다. `https://idbroker.webex.com/<Org Id>`

    > [!NOTE]
    > 식별자 값은 실제 값이 아닙니다. 실제 식별자로 이 값을 업데이트하세요. 서비스 공급 기업 메타데이터가 있는 경우 **기본 SAML 구성** 섹션에 업로드한 다음, **식별자(엔터티 ID)** 값이 자동으로 채워집니다.

5. Cisco Webex 애플리케이션은 특정 서식에서 SAML 어설션이 필요합니다. 이를 위해서는 SAML 토큰 특성 구성에 사용자 지정 특성 매핑을 추가해야 합니다. 다음 스크린샷에서는 기본 특성의 목록을 보여 줍니다. **편집** 아이콘을 클릭하여 특성을 추가합니다.

    ![이미지](common/edit-attribute.png)

6. 위에서 언급한 특성 외에도, Cisco Webex 애플리케이션에는 SAML 응답에 다시 전달되어야 하는 몇 가지 특성이 추가로 필요합니다. **사용자 특성** 대화 상자의 **사용자 클레임** 섹션에서 다음 단계를 수행하여 아래 표와 같은 SAML 토큰 특성을 추가합니다.
    
    | Name |  원본 특성|
    | ---------------|--------- |
    | uid | user.userprincipalname |

    a. **새 클레임 추가**를 클릭하여 **사용자 클레임 관리** 대화 상자를 엽니다.

    ![이미지](common/new-save-attribute.png)

    ![이미지](common/new-attribute-details.png)

    b. **이름** 텍스트 상자에서 해당 행에 표시된 특성 이름을 입력합니다.

    다. **네임스페이스**를 비워 둡니다.

    d. 원본을 **특성**으로 선택합니다.

    e. **원본 특성** 목록에서 해당 행에 표시된 특성 값을 입력합니다.

    f. **확인**을 클릭합니다.

    g. **저장**을 클릭합니다.

7. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드**를 클릭하여 요구 사항에 따라 제공된 옵션에서 **페더레이션 메타데이터 XML**을 다운로드하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

8. **Cisco Webex 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

    a. 로그인 URL

    b. Azure AD 식별자

    다. 로그아웃 URL

### <a name="configure-cisco-webex-single-sign-on"></a>Cisco Webex Single Sign-On 구성

1. 전체 관리자 자격 증명으로 [Cisco Cloud Collaboration Management](https://admin.ciscospark.com/)에 로그인합니다.

2. **설정**을 선택하고 **인증** 섹션 아래에서 **수정**을 클릭합니다.

    ![Configure Single Sign-On](./media/cisco-spark-tutorial/tutorial_cisco_spark_10.png)
  
3. **타사 ID 공급자 통합 (고급)** 을 선택하고 다음 화면으로 이동합니다.

4. **Idp 메타데이터 가져오기** 페이지에서 Azure AD 메타데이터 파일을 페이지로 끌어다 놓거나 파일 브라우저 옵션을 사용하여 Azure AD 메타데이터 파일을 찾아 업로드합니다. 그런 다음 **메타데이터에서 인증 기관이 서명한 인증서 필요(더 안전)** 를 선택하고 **다음**을 클릭합니다.

    ![Configure Single Sign-On](./media/cisco-spark-tutorial/tutorial_cisco_spark_11.png)

5. **SSO 연결 테스트**를 선택하고 새 브라우저 탭이 열리면 로그인하여 Azure AD로 인증합니다.

6. **Cisco Cloud Collaboration Management** 브라우저 탭으로 돌아갑니다. 테스트에 성공하면 **이 테스트에 성공했습니다. Single Sign-On 사용 옵션**을 선택하고 **다음**을 클릭합니다.

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**를 차례로 선택하고 **모든 사용자**를 선택합니다.

    !["사용자 및 그룹" 및 "모든 사용자" 링크](common/users.png)

2. 화면 위쪽에서 **새 사용자**를 선택합니다.

    ![새 사용자 단추](common/new-user.png)

3. 사용자 속성에서 다음 단계를 수행합니다.

    ![사용자 대화 상자](common/user-properties.png)

    a. **이름** 필드에 **BrittaSimon**을 입력합니다.
  
    b. **사용자 이름** 필드에 **brittasimon\@yourcompanydomain.extension**을 입력합니다.  
    예를 들어 BrittaSimon@contoso.com

    c. **암호 표시** 확인란을 선택한 다음, [암호] 상자에 표시된 값을 적어둡니다.

    d. **만들기**를 클릭합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Cisco Webex에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**, **모든 애플리케이션**, **Cisco Webex**를 차례로 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **Cisco Webex**를 선택합니다.

    ![애플리케이션 목록의 Cisco Webex 링크](common/all-applications.png)

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 선택합니다.

    !["사용자 및 그룹" 링크](common/users-groups-blade.png)

4. **사용자 추가** 단추를 클릭한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창](common/add-assign-user.png)

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택하고 화면 아래쪽에서 **선택** 단추를 클릭합니다.

6. SAML 어설션 및 **역할 선택** 대화 상자에서 모든 역할 값이 필요한 경우 목록에서 적절한 사용자 역할을 선택한 다음, 화면 맨 아래에 있는 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

### <a name="create-cisco-webex-test-user"></a>Cisco Webex 테스트 사용자 만들기

이 섹션에서는 Cisco Webex에서 Britta Simon이라는 사용자를 만듭니다. 이 섹션에서는 Cisco Webex에서 Britta Simon이라는 사용자를 만듭니다.

1. 전체 관리자 자격 증명으로 [Cisco Cloud Collaboration Management](https://admin.ciscospark.com/)로 이동합니다.

2. **사용자**, **사용자 관리**를 차례로 클릭합니다.
   
    ![Configure Single Sign-On](./media/cisco-spark-tutorial/tutorial_cisco_spark_12.png) 

3. **사용자 관리** 창에서 **수동으로 사용자 추가 또는 수정**을 선택하고 **다음**을 클릭합니다.

4. **이름 및 전자 메일 주소**를 선택합니다. 그런 다음 아래와 같이 텍스트 상자에 내용을 입력합니다.

    ![Configure Single Sign-On](./media/cisco-spark-tutorial/tutorial_cisco_spark_13.png) 

    a. **이름** 텍스트 상자에서 사용자의 이름(예: **Britta**)을 입력합니다.

    b. **성** 텍스트 상자에서 사용자의 성(예: **Simon**)을 입력합니다.

    다. **이메일 주소** 텍스트 상자에서 사용자의 이메일 주소(예: **britta.simon\@contoso.com**)를 입력합니다.

5. 더하기 기호를 클릭하여 Britta Simon을 추가합니다. 그런 후 **다음**을 클릭합니다.

6. **사용자에 대한 서비스 추가** 창에서 **저장**을 클릭한 후 **마침**을 클릭합니다.

### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Cisco Webex 타일을 클릭하면 SSO를 설정한 Cisco Webex에 자동으로 로그인됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On이란 무엇입니까?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [사용자 프로비저닝 구성](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-spark-provisioning-tutorial) 
