---
title: '자습서: Snowflake와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 Snowflake 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3488ac27-0417-4ad9-b9a3-08325fe8ea0d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2018
ms.author: jeedes
ms.openlocfilehash: 6e1f76548e2433f9bc4b0b26b0894a92b7ec0aa0
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50092562"
---
# <a name="tutorial-azure-active-directory-integration-with-snowflake"></a>자습서: Snowflake와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Snowflake를 통합하는 방법에 대해 알아봅니다.

Snowflake를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- Snowflake에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정으로 Snowflake(Single Sign-on)에 자동으로 로그인하도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](../manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

Snowflake와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- Snowflake Single Sign-on이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [1개월 평가판을 얻을](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다.  이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 Snowflake 추가
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-snowflake-from-the-gallery"></a>갤러리에서 Snowflake 추가

Snowflake의 Azure AD 통합을 구성하려면 갤러리의 Snowflake를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Snowflake를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Azure Active Directory 단추][1]

2. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 애플리케이션**으로 이동합니다.

    ![엔터프라이즈 애플리케이션 블레이드][2]

3. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션** 단추를 클릭합니다.

    ![새 애플리케이션 단추][3]

4. 검색 상자에 **Snowflake**를 입력하고 결과 패널에서 **Snowflake**를 선택한 후 **추가** 단추를 클릭하여 애플리케이션을 추가합니다.

    ![결과 목록의 Snowflake](./media/snowflake-tutorial/tutorial_snowflake_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 Snowflake에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 Snowflake 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 Snowflake의 관련 사용자 간에 연결이 형성되어야 합니다.

Snowflake에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#creating-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On 테스트하는 데 사용합니다.
3. **[Snowflake 테스트 사용자 만들기](#creating-snowflake-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 Snowflake에 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assigning-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Single Sign-On 테스트](#testing-single-sign-on)** - 구성이 작동하는지 확인합니다.

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 Snowflake 애플리케이션에서 Single Sign-On을 구성합니다.

**Snowflake에서 Azure AD Single Sign-on을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **Snowflake** 애플리케이션 통합 페이지에서 **Single sign-on**을 클릭합니다.

    ![Single Sign-On 구성 링크][4]

2. **Single Sign-On 방법 선택** 대화 상자에서 **SAML** 모드에 대해 **선택**을 클릭하여 Single Sign-On을 사용하도록 설정합니다.

    ![Configure Single Sign-On](common/tutorial_general_301.png)

3. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 아이콘을 클릭하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![Configure Single Sign-On](common/editconfigure.png)

4. **IDP** 시작 모드에서 응용 프로그램을 구성하려면 **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    ![Snowflake 도메인 및 URL Single Sign-On 정보](./media/snowflake-tutorial/tutorial_snowflake_url1.png)

    a. **식별자** 텍스트 상자에서 `https://<SNOWFLAKE-URL>.snowflakecomputing.com` 패턴을 사용하여 URL을 입력합니다.

    b. **회신 URL** 텍스트 상자에 다음 패턴으로 URL을 입력합니다.`https://<SNOWFLAKE-URL>.snowflakecomputing.com/fed/login`

5. **SP** 시작 모드에서 응용 프로그램을 구성하려면 **추가 URL 설정**을 클릭하고 다음 단계를 수행합니다.

    ![Snowflake 도메인 및 URL Single Sign-On 정보](./media/snowflake-tutorial/tutorial_snowflake_url2.png)

    **로그온 URL** 텍스트 상자에서 다음 패턴으로 URL을 입력합니다. `https://<SNOWFLAKE-URL>.snowflakecomputing.com`

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 이러한 값을 실제 식별자, 회신 URL 및 로그온 URL로 업데이트합니다.

6. **SAML 서명 인증서** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드**를 클릭하고 **인증서(Base64)** 다운로드한 다음, 컴퓨터에 인증서 파일을 저장합니다.

    ![인증서 다운로드 링크](./media/snowflake-tutorial/tutorial_snowflake_certificate.png) 

7.  **설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    a. 로그인 URL

    b. Azure AD 식별자

    다. 로그아웃 URL

    ![Snowflake 구성](common/configuresection.png)

8. 다른 웹 브라우저 창에서 Snowflake에 보안 관리자 권한으로 로그인합니다.

9. 페이지의 오른쪽 위에 있는 **프로필**을 클릭하여 **역할 전환**을 **ACCOUNTADMIN**으로 설정합니다.

    > [!NOTE]
    > 이 작업은 오른쪽 위 모서리의 사용자 이름 아래에서 선택한 컨텍스트와는 별개입니다.
    
    ![Snowflake 관리자 ](./media/snowflake-tutorial/tutorial_snowflake_accountadmin.png)

10. **다운로드한 Base 64 인증서**를 메모장에서 엽니다. “-----BEGIN CERTIFICATE-----”와 “-----END CERTIFICATE-----" 사이의 값을 복사한 후 아래의 **인증서** 옆에 있는 큰따옴표에 붙여 넣습니다. **ssoUrl**에 Azure Portal에서 복사한 **로그인 URL** 값을 붙여 넣습니다. **모든 쿼리**를 선택하고 **실행**을 클릭합니다.

    ![Snowflake sql](./media/snowflake-tutorial/tutorial_snowflake_sql.png)

    ```
    use role accountadmin;
    alter account set saml_identity_provider = '{
    "certificate": "<Paste the content of downloaded certificate from Azure portal>",
    "ssoUrl":"<Login URL value which you have copied from the Azure portal>",
    "type":"custom",
    "label":"AzureAD"
    }';
    alter account set sso_login_page = TRUE;
    ```

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

### <a name="creating-snowflake-test-user"></a>Snowflake 테스트 사용자 만들기

Azure AD 사용자가 Snowflake에 로그인할 수 있도록 하려면 해당 사용자를 Snowflake로 프로비전해야 합니다. Snowflake에서 프로비전 작업은 수동으로 수행됩니다.

**사용자 계정을 프로비전하려면 다음 단계를 수행합니다.**

1. Snowflake에 보안 관리자 권한으로 로그인합니다.

2. 페이지의 오른쪽 위에 있는 **프로필**을 클릭하여 **역할 전환**을 **ACCOUNTADMIN**으로 설정합니다.  

    ![Snowflake 관리자 ](./media/snowflake-tutorial/tutorial_snowflake_accountadmin.png)

3. 아래 SQL 쿼리를 실행하여 사용자를 만듭니다. 아래와 같이 "로그인 이름"이 워크시트의 Azure AD 사용자 이름으로 설정되어 있는지 확인합니다.

    ![Snowflake adminsql ](./media/snowflake-tutorial/tutorial_snowflake_usersql.png)

    ```
    use role accountadmin;
    CREATE USER britta_simon PASSWORD = '' LOGIN_NAME = 'BrittaSimon@contoso.com' DISPLAY_NAME = 'Britta Simon';
    ```

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Snowflake에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**을 선택합니다.

    ![사용자 할당][201]

2. 애플리케이션 목록에서 **Snowflake**를 선택합니다.

    ![Configure Single Sign-On](./media/snowflake-tutorial/tutorial_snowflake_app.png) 

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    ![사용자 할당][202]

4. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 할당][203]

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택하고 화면 아래쪽에서 **선택** 단추를 클릭합니다.

6. **할당 추가** 대화 상자에서 **할당** 단추를 선택합니다.

### <a name="testing-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Snowflake 타일을 클릭하면 Snowflake 애플리케이션에 자동으로 로그온됩니다.
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
