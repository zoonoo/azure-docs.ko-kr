---
title: '자습서: SAP NetWeaver와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 SAP NetWeaver 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 1b9e59e3-e7ae-4e74-b16c-8c1a7ccfdef3
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2018
ms.author: jeedes
ms.openlocfilehash: fac22508e679c1e1c93ec62a5b120ba9c7c52317
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2018
ms.locfileid: "52162384"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-netweaver"></a>자습서: SAP NetWeaver와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 SAP NetWeaver를 통합하는 방법에 대해 알아봅니다.

SAP NetWeaver를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- SAP NetWeaver에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정으로 SAP NetWeaver에 자동으로 로그온(Single Sign-On)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](../manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

SAP NetWeaver와의 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- SAP NetWeaver Single Sign-On이 설정된 구독
- SAP NetWeaver V7.20 필수

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [1개월 평가판을 얻을](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다.
 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 SAP NetWeaver 추가
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-sap-netweaver-from-the-gallery"></a>갤러리에서 SAP NetWeaver 추가

SAP NetWeaver의 Azure AD 통합을 구성하려면 갤러리의 SAP NetWeaver를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 SAP NetWeaver를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Azure Active Directory 단추][1]

2. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 애플리케이션**으로 이동합니다.

    ![엔터프라이즈 응용 프로그램 블레이드][2]

3. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션** 단추를 클릭합니다.

    ![새 애플리케이션 단추][3]

4. 검색 상자에 **SAP NetWeaver**를 입력하고 결과 패널에서 **SAP NetWeaver**를 선택한 다음, **추가** 단추를 클릭하여 애플리케이션을 추가합니다.

    ![결과 목록의 SAP NetWeaver](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 SAP NetWeaver에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 SAP NetWeaver 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 SAP NetWeaver의 관련 사용자 간에 연결 관계가 형성되어야 합니다.

SAP NetWeaver에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#creating-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On 테스트하는 데 사용합니다.
3. **[SAP NetWeaver 테스트 사용자 만들기](#creating-sapnetweaver-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 SAP NetWeaver에 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assigning-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Single Sign-On 테스트](#testing-single-sign-on)** - 구성이 작동하는지 확인합니다.

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 SAP NetWeaver 응용 프로그램에서 Single Sign-On을 구성합니다.

**SAP NetWeaver에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.**

1. 새 웹 브라우저 창을 열고 SAP NetWeaver 회사 사이트에 관리자로 로그인합니다.

2. **http** 및 **https** 서비스가 활성화되고 적절한 포트가 **SMICM** T-코드에서 할당되었는지 확인합니다.

3. SSO가 필요한 SAP 시스템(T01)의 비즈니스 클라이언트에 로그온하고 HTTP 보안 세션 관리를 활성화합니다.

    a. 트랜잭션 코드 **SICF_SESSIONS**로 이동합니다. 현재 값을 사용하여 모든 관련 프로필 매개 변수를 표시합니다. 아래처럼 보입니다.
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
    > 조직 요구 사항에 따라 위의 매개 변수를 조정합니다. 위의 매개 변수는 여기에서 표시용으로만 제공됩니다.

    b. SAP 시스템의 인스턴스/기본 프로필에서 매개 변수 조정이 필요한 경우 SAP 시스템을 다시 시작합니다.

    다. HTTP 보안 세션을 사용하도록 설정하려면 해당 클라이언트를 두 번 클릭합니다.

    ![인증서 다운로드 링크](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_profileparameter.png)

    d. 아래 SICF 서비스 활성화:
    ```
    /sap/public/bc/sec/saml2
    /sap/public/bc/sec/cdc_ext_service
    /sap/bc/webdynpro/sap/saml2
    /sap/bc/webdynpro/sap/sec_diag_tool (This is only to enable / disable trace)
    ```
4. SAP 시스템[T01/122]의 비즈니스 클라이언트에서 트랜잭션 코드 **SAML2**로 이동합니다. 브라우저에서 사용자 인터페이스가 열립니다. 이 예제에서는 SAP 비즈니스 클라이언트로 122를 가정합니다.

    ![인증서 다운로드 링크](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_sapbusinessclient.png)

5. 사용자 이름 및 암호를 제공하여 사용자 인터페이스에 입력하고 **편집**을 클릭합니다.

    ![인증서 다운로드 링크](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_userpwd.png)

6. T01122에서 **http://T01122**로 **공급자 이름**을 바꾸고 **저장**을 클릭합니다.

    > [!NOTE]
    > 기본적으로 공급자 이름은 <sid><client> 형식이지만 Azure AD는 <protocol>://<name>의 형식으로 이름을 예상합니다. 공급자 이름을 https://<sid><client>로 유지하기 위해 Azure AD에서 여러 SAP NetWeaver ABAP 엔진을 구성하도록 허용하는 것이 좋습니다.

    ![인증서 다운로드 링크](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_providername.png)

7. **서비스 공급자 메타데이터 생성**: SAML 2.0 사용자 인터페이스에서 **로컬 공급자** 및 **신뢰할 수 있는 공급자** 설정 구성을 완료한 후 다음 단계는 서비스 공급자의 메타데이터 파일을 생성하는 것입니다(모든 설정, 인증 컨텍스트 및 SAP의 다른 구성 포함). 이 파일이 생성되면 Azure AD에서 업로드해야 합니다.

    ![인증서 다운로드 링크](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_generatesp.png)

    a. **로컬 공급자 탭**으로 이동합니다.

    b. **메타데이터**를 클릭합니다.

    다. 컴퓨터에 생성된 **메타데이터 XML 파일**을 저장하고 **기본 SAML 구성** 섹션에 업로드하면 Azure Portal에서 **식별자** 및 **회신 URL** 값이 자동으로 입력됩니다.

8. Azure Portal의 **SAP NetWeaver** 응용 프로그램 통합 페이지에서 **Single Sign-On**을 클릭합니다.

    ![Single Sign-On 구성 링크][4]

9. **Single Sign-On 방법 선택** 대화 상자에서 **SAML** 모드에 대해 **선택**을 클릭하여 Single Sign-On을 사용하도록 설정합니다.

    ![Configure Single Sign-On](common/tutorial_general_301.png)

10. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 아이콘을 클릭하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![Configure Single Sign-On](common/editconfigure.png)

11. **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    a. **메타데이터 파일 업로드**를 클릭하여 이전에 얻은 **서비스 공급자 메타데이터 파일**을 업로드합니다.

    ![메타데이터 파일 업로드](common/editmetadataupload.png)

    b. **폴더 로고**를 클릭하여 메타데이터 파일을 선택하고 **업로드**를 클릭합니다.

    ![메타데이터 파일 업로드](common/uploadmetadata.png)

    다. 메타데이터 파일이 정상적으로 업로드되면 아래 그림과 같이 **기본 SAML 구성** 섹션에서 **식별자** 및 **회신 URL** 값이 자동으로 입력됩니다.

    ![SAP NetWeaver 도메인 및 URL Single Sign-On 정보](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_url.png)

    d. **로그온 URL** 텍스트 상자에서 다음 패턴으로 URL을 입력합니다. `https://<your company instance of SAP NetWeaver>`

12. SAP NetWeaver 애플리케이션은 특정 형식의 SAML 어설션이 필요합니다. 이 애플리케이션에 대해 다음 클레임을 구성합니다. 애플리케이션 통합 페이지의 **사용자 특성** 섹션에서 이러한 특성의 값을 관리할 수 있습니다. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 단추를 클릭하여 **사용자 특성** 대화 상자를 엽니다.

    ![특성 섹션](./media/sapnetweaver-tutorial/edit_attribute.png)

13. **사용자 특성** 대화 상자의 **사용자 클레임** 섹션에서 위의 이미지에 표시된 것과 같이 SAML 토큰 특성을 구성하고 다음 단계를 수행합니다.

    a. **편집** 아이콘을 클릭하여 **사용자 클레임 관리** 대화 상자를 엽니다.
    
    ![특성 섹션](./media/sapnetweaver-tutorial/nameidattribute.png)

    b. **사용자 클레임 관리** 탭에서 다음 단계를 수행합니다.

    ![특성 섹션](./media/sapnetweaver-tutorial/nameidattribute1.png)

    * **변환**을 선택합니다.
  
    * **변환** 목록에서 `ExtractMailPrefix()`를 선택합니다.
  
    * **매개 변수 1** 목록에서 `user.userprincipalname`을 선택합니다.

    * **저장**을 클릭합니다.

14. **SAML 서명 인증서** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드**를 클릭하고 **페더레이션 메타데이터 XML**을 다운로드한 다음, 컴퓨터에 인증서 파일을 저장합니다.

    ![인증서 다운로드 링크](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_certificate.png)

15. **SAP NetWeaver 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    a. 로그인 URL

    b. Azure AD 식별자

    다. 로그아웃 URL

    ![SAP NetWeaver 구성](common/configuresection.png)

16. SAP 시스템에 로그온하고 트랜잭션 코드 SAML2로 이동합니다. SAML 구성 화면으로 새 브라우저 창이 열립니다.

17. 신뢰할 수 있는 ID 공급자(Azure AD)에 대한 엔드포인트 구성은 **신뢰할 수 있는 공급자** 탭으로 이동합니다.

    ![Configure Single Sign-On](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_samlconfig.png)

18. **추가** 키를 누르고 바로 가기 메뉴에서 **메타데이터 파일 업로드**를 선택합니다.

    ![Configure Single Sign-On](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_uploadmetadata.png)

19. Azure Portal에서 다운로드한 메타데이터 파일을 업로드합니다.

    ![Configure Single Sign-On](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_metadatafile.png)

20. 다음 화면에서 별칭 이름을 입력합니다. 예를 들어 aadsts를 입력하고 **다음**을 눌러 계속합니다.

    ![Configure Single Sign-On](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_aliasname.png)

21. **다이제스트 알고리즘**은 **SHA-256**이어야 하며 변경이 필요하지 않으며 **다음**을 누릅니다.

    ![Configure Single Sign-On](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_identityprovider.png)

22. **Single Sign-On 엔드포인트**에서 **HTTP POST**를 사용하고 **다음**을 클릭하여 계속합니다.

    ![Configure Single Sign-On](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_httpredirect.png)

23. **단일 로그아웃 엔드포인트**에서 **HTTPRedirect**를 선택하고 **다음**을 클릭하여 계속합니다.

    ![Configure Single Sign-On](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_httpredirect1.png)

24. **아티팩트 엔드포인트**에서 **다음**을 눌러 계속합니다.

    ![Configure Single Sign-On](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_artifactendpoint.png)

25. **인증 요구 사항**에서 **마침**을 클릭합니다.

    ![Configure Single Sign-On](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_authentication.png)

26. 탭 **신뢰할 수 있는 공급자** > **ID 페더레이션**(화면 맨 아래에서)으로 이동합니다. **편집**을 클릭합니다.

    ![Configure Single Sign-On](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_trustedprovider.png)

27. **ID 페더레이션** 탭(아래쪽 창) 아래에서 **추가**를 클릭합니다.

    ![Configure Single Sign-On](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_addidentityprovider.png)

28. 팝업 창의 **지원되는 NameID 형식**에서 **지정되지 않음**을 선택하고 확인을 클릭합니다.

    ![Configure Single Sign-On](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_nameid.png)

29. **사용자 ID 원본** 및 **사용자 ID 매핑 모드** 값은 SAP 사용자와 Azure AD 클레임 간의 링크를 결정합니다.  

    ####<a name="scenario-sap-user-to-azure-ad-user-mapping"></a>시나리오: Azure AD 사용자 매핑으로 SAP 사용자

    a. SAP의 NameID 세부 정보 스크린샷

    ![Configure Single Sign-On](./media/sapnetweaver-tutorial/nameiddetails.png)

    b. Azure AD에서 필요한 클레임을 언급하는 스크린샷

    ![Configure Single Sign-On](./media/sapnetweaver-tutorial/claimsaad1.png)

    ####<a name="scenario-select-sap-user-id-based-on-configured-email-address-in-su01-in-this-case-email-id-should-be-configured-in-su01-for-each-user-who-requires-sso"></a>시나리오: SU01에 구성된 이메일 주소를 기반으로 SAP 사용자 ID를 선택합니다. 이 경우 이메일 ID는 SSO를 필요로 하는 각 사용자에 대해 su01에서 구성되어야 합니다.

    a.  SAP의 NameID 세부 정보 스크린샷

    ![Configure Single Sign-On](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_nameiddetails1.png)

    b. Azure AD에서 필요한 클레임을 언급하는 스크린샷

    ![Configure Single Sign-On](./media/sapnetweaver-tutorial/claimsaad2.png)

30. **저장**을 클릭한 다음, **사용**을 클릭하여 ID 공급자를 사용하도록 설정합니다.

    ![Configure Single Sign-On](./media/sapnetweaver-tutorial/configuration1.png)

31. 메시지가 표시되면 **확인**을 클릭합니다.

    ![Configure Single Sign-On](./media/sapnetweaver-tutorial/configuration2.png)

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

### <a name="creating-sap-netweaver-test-user"></a>SAP NetWeaver 테스트 사용자 만들기

이 섹션에서는 SAP NetWeaver에서 Britta Simon이라는 사용자를 만듭니다. SAP NetWeaver 플랫폼에서 사용자를 추가하려면 사내 SAP 전문가 팀과 작업하거나 조직 SAP 파트너와 함께 작업하세요.

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 SAP NetWeaver에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 응용 프로그램**을 선택한 다음, **모든 응용 프로그램**을 선택합니다.

    ![사용자 할당][201]

2. 애플리케이션 목록에서 **SAP NetWeaver**를 선택합니다.

    ![Configure Single Sign-On](./media/sapnetweaver-tutorial/tutorial_sapnetweaver_app.png) 

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    ![사용자 할당][202]

4. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 할당][203]

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택하고 화면 아래쪽에서 **선택** 단추를 클릭합니다.

6. **할당 추가** 대화 상자에서 **할당** 단추를 선택합니다.

### <a name="testing-single-sign-on"></a>Single Sign-On 테스트

1. ID 공급자 Azure AD가 활성화되면 아래 URL에 액세스하여 SSO를 확인합니다(사용자 이름 및 암호에 대한 메시지가 표시되지 않음).

    `https://<sapurl>/sap/bc/bsp/sap/it00/default.htm`

    (또는) 아래의 URL 사용

    `https://<sapurl>/sap/bc/bsp/sap/it00/default.htm`

    > [!NOTE]
    > sapurl을 실제 SAP 호스트 이름으로 바꿉니다.

2. 위의 URL은 아래 언급한 화면으로 안내합니다. 아래 페이지에 연결할 수 있는 경우 Azure AD SSO 설정이 성공적으로 수행된 것입니다.

    ![Configure Single Sign-On](./media/sapnetweaver-tutorial/testingsso.png)

3. 사용자 이름 및 암호에 대한 메시지가 표시되는 경우 아래 URL을 사용하여 추적을 활성화하여 문제를 진단하세요.

    `https://<sapurl>/sap/bc/webdynpro/sap/sec_diag_tool?sap-client=122&sap-language=EN#`

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
