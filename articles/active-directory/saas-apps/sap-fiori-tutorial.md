---
title: '자습서: SAP Fiori 와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 SAP Fiori 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 77ad13bf-e56b-4063-97d0-c82a19da9d56
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/11/2019
ms.author: jeedes
ms.openlocfilehash: 9e7993ee1cb439ebeaa9f64bee55429aa54f9cee
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2019
ms.locfileid: "65903952"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-fiori"></a>자습서: SAP Fiori 와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 SAP Fiori를 통합하는 방법에 대해 알아봅니다.

SAP Fiori를 Azure AD와 통합하면 다음과 같은 이점이 있습니다.

* Azure AD를 사용하여 SAP Fiori에 대한 액세스 권한이 있는 사용자를 제어할 수 있습니다.
* 사용자는 자신의 Azure AD 계정으로 SAP Fiori에 자동으로 로그인(Single Sign-on)할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS(Software as a Service) 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 애플리케이션에 대한 Single Sign-On](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)을 참조하세요.

## <a name="prerequisites"></a>필수 조건

SAP Fiori와 Azure AD의 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/) 을 만듭니다.
* Single Sign-On을 사용하도록 설정된 SAP Fiori 구독
* SAP Fiori 7.20 이상이 필요합니다.

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트한 후 Azure AD에 SAP Fiori를 통합합니다.

SAP Fiori에서 지원하는 기능은 다음과 같습니다.

* **SP 시작 Single Sign-On**

## <a name="add-sap-fiori-in-the-azure-portal"></a>Azure Portal에서 SAP Fiori 추가

SAP Fiori를 Azure AD와 통합하려면 관리형 SaaS 앱 목록에 SAP Fiori를 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. 왼쪽 창에서 **Azure Active Directory**를 선택합니다.

    ![Azure Active Directory 옵션](common/select-azuread.png)

1. **엔터프라이즈 애플리케이션** > **모든 애플리케이션**을 선택합니다.

    ![엔터프라이즈 애플리케이션 창](common/enterprise-applications.png)

1. 애플리케이션을 추가하려면 **새 애플리케이션**을 선택합니다.

    ![새 애플리케이션 옵션](common/add-new-app.png)

1. 검색 상자에 **SAP Fiori**를 입력합니다. 검색 결과에서 **SAP Fiori**를 선택한 후 **추가**를 선택합니다.

    ![결과 목록의 SAP Fiori](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 **Britta Simon**이라는 테스트 사용자를 기반으로 SAP Fiori에서 Azure AD Single Sign-On을 구성하고 테스트합니다. Single Sign-On이 작동하려면 Azure AD 사용자와 SAP Fiori의 관련 사용자 간에 연결 관계를 설정해야 합니다.

SAP Fiori에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

| Task | 설명 |
| --- | --- |
| **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** | 사용자가 이 기능을 사용하도록 설정합니다. |
| **[SAP Fiori Single Sign-On 구성](#configure-sap-fiori-single-sign-on)** | 애플리케이션에서 Single Sign-On 설정을 구성합니다. |
| **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** | Britta Simon 사용자에 대해 Azure AD Single Sign-On을 테스트합니다. |
| **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** | Britta Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다. |
| **[SAP Fiori 테스트 사용자 만들기](#create-an-sap-fiori-test-user)** | Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 SAP Fiori에 만듭니다. |
| **[Single Sign-On 테스트](#test-single-sign-on)** | 구성이 작동하는지 확인합니다. |

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 SAP Fiori를 사용하여 Azure AD Single Sign-On을 구성합니다.

1. 새 웹 브라우저 창을 열고 SAP Fiori 회사 사이트에 관리자로 로그인합니다.

1. **http** 및 **https** 서비스가 활성화되고 적절한 포트가 트랜잭션 코드 **SMICM**에 할당되었는지 확인합니다.

1. SAP Business Client for SAP 시스템 **T01**에 로그인합니다. 여기서는 반드시 Single Sign-On이 필요합니다. 그런 다음, HTTP 보안 세션 관리를 활성화합니다.

    1. 트랜잭션 코드 **SICF_SESSIONS**로 이동합니다. 모든 관련 프로필 매개 변수와 현재 값이 표시됩니다. 다음 예제와 같아야 합니다.

        ```
        login/create_sso2_ticket = 2
        login/accept_sso2_ticket = 1
        login/ticketcache_entries_max = 1000
        login/ticketcache_off = 0  login/ticket_only_by_https = 0 
        icf/set_HTTPonly_flag_on_cookies = 3
        icf/user_recheck = 0  http/security_session_timeout = 1800
        http/security_context_cache_size = 2500
        rdisp/plugin_auto_logout = 1800
        rdisp/autothtime = 60
        ```

        >[!NOTE]
        > 조직 요구 사항에 따라 매개 변수를 조정합니다. 위의 매개 변수는 예제로만 제공됩니다.

    1. 필요한 경우 SAP 시스템의 인스턴스(기본) 프로필에서 매개 변수를 조정하고 SAP 시스템을 다시 시작합니다.

    1. 해당 클라이언트를 두 번 클릭하여 HTTP 보안 세션을 사용하도록 설정합니다.

        ![SAP의 관련 프로필 매개 변수 페이지에 표시되는 현재 값](./media/sapfiori-tutorial/tutorial-sapnetweaver-profileparameter.png)

    1. 다음 SICF 서비스를 활성화합니다.

        ```
        /sap/public/bc/sec/saml2
        /sap/public/bc/sec/cdc_ext_service
        /sap/bc/webdynpro/sap/saml2
        /sap/bc/webdynpro/sap/sec_diag_tool (This is only to enable / disable trace)
        ```

1. Business Client for SAP 시스템[**T01/122**]의 트랜잭션 코드 **SAML2**로 이동합니다. 구성 UI가 새 브라우저 창에서 열립니다. 이 예제에서는 Business Client for SAP 시스템 122를 사용합니다.

    ![SAP Fiori 비즈니스 클라이언트 로그인 페이지](./media/sapfiori-tutorial/tutorial-sapnetweaver-sapbusinessclient.png)

1. 사용자 이름 및 암호를 입력한 다음, **로그온**을 선택합니다.

    ![SAP에서 ABAP 시스템 T01/122의 SAML 2.0 구성 페이지](./media/sapfiori-tutorial/tutorial-sapnetweaver-userpwd.png)

1. **공급자 이름** 상자에서 **T01122**를 **http:\//T01122**로 바꾼 후 **저장**을 선택합니다.

    > [!NOTE]
    > 기본적으로 공급자 이름은 \<sid>\<client> 형식입니다. Azure AD에서는 \<protocol>://\<name> 형식의 이름을 요구합니다. Azure AD에서 여러 SAP Fiori ABAP 엔진을 구성할 수 있도록 공급자 이름으로 https\://\<sid>\<client>를 유지하는 것이 좋습니다.

    ![SAP에서 ABAP 시스템 T01/122의 SAML 2.0 구성 페이지에서 업데이트된 공급자 이름](./media/sapfiori-tutorial/tutorial-sapnetweaver-providername.png)

1. **로컬 공급자 탭** > **메타데이터**를 선택합니다.

1. **SAML 2.0 메타데이터** 대화 상자에서 생성된 메타데이터 XML 파일을 다운로드하고 컴퓨터에 저장합니다.

    ![SAML 2.0 메타데이터 SAP 대화 상자의 메타데이터 다운로드 링크](./media/sapfiori-tutorial/tutorial-sapnetweaver-generatesp.png)

1. [Azure Portal](https://portal.azure.com/)의 **SAP Fiori** 애플리케이션 통합 페이지에서 **Single Sign-On**을 선택합니다.

    ![Single Sign-On 옵션](common/select-sso.png)

1. **Single Sign-On 방법 선택** 창에서 **SAML** 또는 **SAML/WS-Fed** 모드를 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Single Sign-On 선택 모드](common/select-saml-option.png)

1. **SAML로 Single Sign-On 설정** 창에서 **편집**(연필 모양 아이콘)을 선택하여 **기본 SAML 구성** 창을 엽니다.

    ![기본 SAML 구성 편집](common/edit-urls.png)

1. **기본 SAML 구성** 섹션에서 다음 단계를 완료합니다.

    1. **메타데이터 파일 업로드**를 선택합니다.

        ![메타데이터 파일 업로드 옵션](common/upload-metadata.png)

   1. 메타데이터 파일을 선택하려면 폴더 아이콘을 선택하고 **업로드**를 선택합니다.

       ![메타데이터 파일 선택 및 업로드 단추 선택](common/browse-upload-metadata.png)

1. 메타데이터 파일이 성공적으로 업로드되면 **식별자** 및 **회신 URL** 값이 **기본 SAML 구성** 창에 자동으로 채워집니다. **로그온 URL** 상자에 패턴 https:\//\<SAP Fiori의 회사 인스턴스\>를 사용하는 URL을 입력합니다.

    ![SAP Fiori 도메인 및 URL Single Sign-On 정보](common/sp-identifier-reply.png)

    > [!NOTE]
    > 소수의 고객이 잘못 구성된 **회신 URL** 값과 관련된 오류를 보고합니다. 이러한 오류가 표시되면 다음 PowerShell 스크립트를 사용하여 인스턴스에 대해 올바른 회신 URL을 설정할 수 있습니다.
    >
    > ```
    > Set-AzureADServicePrincipal -ObjectId $ServicePrincipalObjectId -ReplyUrls "<Your Correct Reply URL(s)>"
    > ``` 
    > 
    > 스크립트를 실행하기 전에 `ServicePrincipal` 개체 ID를 직접 설정하거나 여기서 제공할 수 있습니다.

1. SAP Fiori 애플리케이션에는 특정 형식의 SAML 어설션이 필요합니다. 이 애플리케이션에 대해 다음 클레임을 구성합니다. 이러한 특성 값을 관리하려면 **SAML로 Single Sign-On 설정** 창에서 **편집**을 선택합니다.

    ![사용자 특성 창](common/edit-attribute.png)

1. **사용자 특성 및 클레임** 창에서 위의 이미지에 표시된 것과 같이 SAML 토큰 특성을 구성합니다. 그리고 나서 다음 단계를 완료합니다.

    1. **편집**을 선택하여 **사용자 클레임 관리** 창을 엽니다.

    1. **변환** 목록에서 **ExtractMailPrefix()** 를 선택합니다.

    1. **매개 변수 1** 목록에서 **user.userprinicipalname**을 선택합니다.

    1. **저장**을 선택합니다.

       ![사용자 클레임 관리 창](./media/sapfiori-tutorial/nameidattribute.png)

       ![사용자 클레임 관리 창의 변환 섹션](./media/sapfiori-tutorial/nameidattribute1.png)


1. **SAML로 Single Sign-On 설정** 창의 **SAML 서명 인증서** 섹션에서 **페더레이션 메타데이터 XML** 옆에 있는 **다운로드**를 선택합니다. 요구 사항에 따라 다운로드 옵션을 선택합니다. 컴퓨터에 인증서를 저장합니다.

    ![인증서 다운로드 옵션](common/metadataxml.png)

1. **SAP Fiori 설정** 섹션에서 요구 사항에 따라 다음 URL을 복사합니다.

    * 로그인 URL
    * Azure AD 식별자
    * 로그아웃 URL

    ![구성 URL 복사](common/copy-configuration-urls.png)

### <a name="configure-sap-fiori-single-sign-on"></a>SAP Fiori Single Sign-On 구성

1. SAP 시스템에 로그인하여 트랜잭션 코드 **SAML2**로 이동합니다. 새 브라우저 창에 SAML 구성 페이지가 열립니다.

1. 신뢰할 수 있는 ID 공급자(Azure AD)에 대한 엔드포인트를 구성하려면 **신뢰할 수 있는 공급자** 탭을 선택합니다.

    ![SAP의 신뢰할 수 있는 공급자 탭](./media/sapfiori-tutorial/tutorial-sapnetweaver-samlconfig.png)

1. **추가**를 선택하고 바로 가기 메뉴에서 **메타데이터 파일 업로드**를 선택합니다.

    ![SAP의 메타데이터 파일 추가 및 업로드 옵션](./media/sapfiori-tutorial/tutorial-sapnetweaver-uploadmetadata.png)

1. Azure Portal에서 다운로드한 메타데이터 파일을 업로드합니다. **다음**을 선택합니다.

    ![SAP에서 업로드할 메타데이터 파일 선택](./media/sapfiori-tutorial/tutorial-sapnetweaver-metadatafile.png)

1. 다음 페이지의 **별칭** 상자에 별칭 이름을 입력합니다. 예를 들어 **aadsts**를 입력합니다. **다음**을 선택합니다.

    ![SAP의 별칭 상자](./media/sapfiori-tutorial/tutorial-sapnetweaver-aliasname.png)

1. **다이제스트 알고리즘** 상자의 값이 **SHA-256**인지 확인합니다. **다음**을 선택합니다.

    ![SAP의 다이제스트 알고리즘 값 확인](./media/sapfiori-tutorial/tutorial-sapnetweaver-identityprovider.png)

1. **Single Sign-On 엔드포인트**에서 **HTTP POST**를 선택한 후 **다음**을 선택합니다.

    ![SAP의 Single Sign-On 엔드포인트 옵션](./media/sapfiori-tutorial/tutorial-sapnetweaver-httpredirect.png)

1. **단일 로그아웃 엔드포인트**에서 **HTTP 리디렉션**을 선택한 후 **다음**을 선택합니다.

    ![SAP의 단일 로그아웃 엔드포인트 옵션](./media/sapfiori-tutorial/tutorial-sapnetweaver-httpredirect1.png)

1. **아티팩트 엔드포인트** 아래에서 **다음**을 선택하여 계속 진행합니다.

    ![SAP의 아티팩트 엔드포인트 옵션](./media/sapfiori-tutorial/tutorial-sapnetweaver-artifactendpoint.png)

1. **인증 요구 사항** 아래에서 **마침**을 선택합니다.

    ![SAP의 인증 요구 사항 옵션 및 마침 옵션](./media/sapfiori-tutorial/tutorial-sapnetweaver-authentication.png)

1. **신뢰할 수 있는 공급자** > **ID 페더레이션**(페이지 맨 위에 있음)을 선택합니다. **편집**을 선택합니다.

    ![SAP의 신뢰할 수 있는 공급자 및 ID 페더레이션 탭](./media/sapfiori-tutorial/tutorial-sapnetweaver-trustedprovider.png)

1. **추가**를 선택합니다.

    ![ID 페더레이션이 탭의 추가 옵션](./media/sapfiori-tutorial/tutorial-sapnetweaver-addidentityprovider.png)

1. **지원되는 NameID 형식** 대화 상자에서 **지정되지 않음**을 선택합니다. **확인**을 선택합니다.

    ![SAP의 지원되는 NameID 형식 대화 상자 및 옵션](./media/sapfiori-tutorial/tutorial-sapnetweaver-nameid.png)

    **사용자 ID 원본** 및 **사용자 ID 매핑 모드** 값은 SAP 사용자와 Azure AD 클레임 간의 링크를 결정합니다.  

    **시나리오 1**: SAP 사용자-Azure AD 사용자 매핑

    1. SAP의 **NameID 형식 "지정되지 않음"의 세부 정보** 아래에 표시되는 세부 정보를 기록해 둡니다.

        ![SAP의 NameID 형식 "지정되지 않음" 세부 정보 대화 상자](./media/sapfiori-tutorial/nameiddetails.png)

    1. Azure Portal의 **사용자 특성 및 클레임** 아래에서 Azure AD의 필수 클레임을 기록해 둡니다.

        ![Azure Portal의 사용자 특성 및 클레임 대화 상자](./media/sapfiori-tutorial/claimsaad1.png)

    **시나리오 2**: SU01에 구성된 이메일 주소를 기반으로 SAP 사용자 ID를 선택합니다. 이 경우 이메일 ID는 SSO를 필요로 하는 각 사용자에 대해 SU01에서 구성되어야 합니다.

    1.  SAP의 **NameID 형식 "지정되지 않음"의 세부 정보** 아래에 표시되는 세부 정보를 기록해 둡니다.

        ![SAP의 NameID 형식 "지정되지 않음" 세부 정보 대화 상자](./media/sapfiori-tutorial/tutorial-sapnetweaver-nameiddetails1.png)

    1. Azure Portal의 **사용자 특성 및 클레임** 아래에서 Azure AD의 필수 클레임을 기록해 둡니다.

       ![Azure Portal의 사용자 특성 및 클레임 대화 상자](./media/sapfiori-tutorial/claimsaad2.png)

1. **저장**을 선택한 다음, **사용**을 선택하여 ID 공급자를 사용하도록 설정합니다.

    ![SAP의 저장 및 사용 옵션](./media/sapfiori-tutorial/configuration1.png)

1. 메시지가 표시되면 **확인**을 선택합니다.

    ![SAP의 SAML 2.0 구성 대화 상자, 확인 옵션](./media/sapfiori-tutorial/configuration2.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션에서는 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만듭니다.

1. Azure Portal에서 **Azure Active Directory** > **사용자** > **모든 사용자**를 선택합니다.

    ![사용자 및 모든 사용자 옵션](common/users.png)

1. **새 사용자**를 선택합니다.

    ![새 사용자 옵션](common/new-user.png)

1. **사용자** 창에서 다음 단계를 완료합니다.

    1. **이름** 상자에 **BrittaSimon**을 입력합니다.
  
    1. **사용자 이름** 상자에서 **brittasimon\@\<your-company-domain>.\<extension>** 을 입력합니다. 예: **brittasimon\@contoso.com**

    1. **암호 표시** 확인란을 선택합니다. **암호** 상자에 표시된 값을 적어둡니다.

    1. **만들기**를 선택합니다.

    ![사용자 창](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Britta Simon이 Azure Single Sign-On을 사용할 수 있도록 SAP Fiori에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** > **모든 애플리케이션** > **SAP Fiori**를 선택합니다.

    ![엔터프라이즈 애플리케이션 창](common/enterprise-applications.png)

1. 애플리케이션 목록에서 **SAP Fiori**를 선택합니다.

    ![애플리케이션 목록의 SAP Fiori](common/all-applications.png)

1. 메뉴에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 및 그룹 옵션](common/users-groups-blade.png)

1. **사용자 추가**를 선택합니다. 그런 다음, **할당 추가** 창에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창](common/add-assign-user.png)

1. **사용자 및 그룹** 창의 사용자 목록에서 **Britta Simon**을 선택합니다. **선택**을 선택합니다.

1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 창의 목록에서 사용자에게 적합한 역할을 선택합니다. **선택**을 선택합니다.

1. **할당 추가** 창에서 **할당**을 선택합니다.

### <a name="create-an-sap-fiori-test-user"></a>SAP Fiori 테스트 사용자 만들기

이 섹션에서는 SAP Fiori에서 Britta Simon이라는 사용자를 만듭니다. 사내 SAP 전문가 팀 또는 조직 SAP 파트너와 협력하여 SAP Fiori 플랫폼에서 사용자를 추가합니다.

### <a name="test-single-sign-on"></a>Single Sign-On 테스트

1. SAP Fiori에서 ID 공급자 Azure AD를 활성화한 후 다음 URL 중 하나에 액세스하여 Single Sign-On을 테스트해 봅니다(사용자 이름 및 암호를 묻는 메시지가 표시되지 않아야 함).

    * https:\//\<sapurl\>/sap/bc/bsp/sap/it00/default.htm
    * https:\//\<sapurl\>/sap/bc/bsp/sap/it00/default.htm

    > [!NOTE]
    > *sapurl*를 실제 SAP 호스트 이름으로 바꿉니다.

1. 테스트 URL을 클릭하면 SAP의 다음 테스트 애플리케이션 페이지로 이동됩니다. 이 페이지가 열리면 Azure AD Single Sign-On이 성공적으로 설정된 것입니다.

    ![SAP의 표준 테스트 애플리케이션 페이지](./media/sapfiori-tutorial/testingsso.png)

1. 사용자 이름 및 암호를 묻는 메시지가 표시되면 추적을 사용하도록 설정하면 문제를 진단합니다. 추적을 위해 URL https:\//\<sapurl\>/sap/bc/webdynpro/sap/sec_diag_tool?sap-client=122&sap-language=EN#을 사용합니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 검토하세요.

- [Azure Active Directory와 SaaS 앱 통합에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Azure Active Directory의 애플리케이션에 대한 Single Sign-On](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
