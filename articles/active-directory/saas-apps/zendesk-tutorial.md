---
title: '자습서: Azure Active Directory와 Zendesk 통합 | Microsoft Docs'
description: Azure Active Directory와 Zendesk 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 9d7c91e5-78f5-4016-862f-0f3242b00680
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2018
ms.author: jeedes
ms.openlocfilehash: 37d20eabfc8fb883cda346dc8b55a17b8b959218
ms.sourcegitcommit: f58fc4748053a50c34a56314cf99ec56f33fd616
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/04/2018
ms.locfileid: "48268177"
---
# <a name="tutorial-azure-active-directory-integration-with-zendesk"></a>자습서:Azure Active Directory와 Zendesk 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Zendesk를 통합하는 방법에 대해 알아봅니다.

Zendesk와 Azure AD를 통합하면 다음과 같은 이점이 제공됩니다.

- Zendesk에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정으로 Zendesk에 자동으로 로그온(Single Sign-on)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](../manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

Zendesk와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- Zendesk Single Sign-On이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [1개월 평가판을 얻을](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다.
 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 Zendesk 추가
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-zendesk-from-the-gallery"></a>갤러리에서 Zendesk 추가

Zendesk의 Azure AD 통합을 구성하려면 갤러리의 Zendesk를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Zendesk를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![이미지](./media/zendesk-tutorial/selectazuread.png)

2. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 애플리케이션**으로 이동합니다.

    ![이미지](./media/zendesk-tutorial/a_select_app.png)
    
3. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션** 단추를 클릭합니다.

    ![이미지](./media/zendesk-tutorial/a_new_app.png)

4. 검색 상자에 **Zendesk**를 입력하고 결과 패널에서 **Zendesk**를 선택한 다음, **추가** 단추를 클릭하여 애플리케이션을 추가합니다.

     ![이미지](./media/zendesk-tutorial/a_add_app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 Zendesk에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 Zendesk 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 Zendesk의 관련 사용자 간에 연결이 형성되어야 합니다.

Zendesk에서 Azure AD의 **사용자 이름** 값을 **Username** 값으로 할당하여 연결 관계를 설정합니다.

Zendesk에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
3. **[Zendesk 테스트 사용자 만들기](#create-a-zendesk-test-user)** - Azure AD의 Britta Simon 사용자에 연결된 Zendesk 사용자를 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 Zendesk 애플리케이션에서 Single Sign-On을 구성합니다.

**Zendesk에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.**

1. [Azure Portal](https://portal.azure.com/)의 **Zendesk** 응용 프로그램 통합 페이지에서 **Single Sign-On**을 선택합니다.

    ![이미지](./media/zendesk-tutorial/b1_b2_select_sso.png)

2. 화면 위쪽에서 **Single Sign-On 모드 변경**을 클릭하여 **SAML** 모드를 선택합니다.

      ![이미지](./media/zendesk-tutorial/b1_b2_saml_ssso.png)

3. **Single Sign-On 방법 선택** 대화 상자에서 **SAML** 모드에 대해 **선택**을 클릭하여 Single Sign-On을 사용하도록 설정합니다.

    ![이미지](./media/zendesk-tutorial/b1_b2_saml_sso.png)

4. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 단추를 클릭하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![이미지](./media/zendesk-tutorial/b1-domains_and_urlsedit.png)

5. **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    a. **로그온 URL** 텍스트 상자에서 `https://<subdomain>.zendesk.com` 패턴을 사용하여 URL을 입력합니다.

    b. **식별자** 텍스트 상자에서 `<subdomain>.zendesk.com` 패턴을 사용하여 URL을 입력합니다.

    ![이미지](./media/zendesk-tutorial/b1-domains_and_urls.png)

    > [!NOTE] 
    > 이러한 값은 실제 값이 아닙니다. 실제 로그온 URL 및 식별자로 값을 업데이트합니다. 이러한 값을 얻으려면 [Zendesk 클라이언트 지원 팀](https://support.zendesk.com/hc/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise)에 문의하세요.

6. Zendesk에는 특정 형식의 SAML 어설션이 필요합니다. 필수 SAML 특성은 없지만 필요에 따라 애플리케이션 통합 페이지의 **사용자 특성** 섹션에서 특성을 추가할 수 있습니다. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 단추를 클릭하여 **사용자 특성** 대화 상자를 엽니다.

    ![이미지](./media/zendesk-tutorial/i4-attribute.png)

7. **사용자 특성** 대화 상자의 **사용자 클레임** 섹션에서 위의 이미지에 표시된 것과 같이 SAML 토큰 특성을 구성하고 다음 단계를 수행합니다.

    a. **새 클레임 추가**를 클릭하여 **사용자 클레임 관리** 대화 상자를 엽니다.

    ![이미지](./media/zendesk-tutorial/i2-attribute.png)

    ![이미지](./media/zendesk-tutorial/i3-attribute.png)
    
    b. **이름** 텍스트 상자에서 해당 행에 표시된 특성 이름을 입력합니다.

    다. **네임스페이스**를 비워 둡니다.

    d. 원본을 **특성**으로 선택합니다.
    
    e. **원본 특성** 목록에서 해당 행에 표시된 특성 값을 입력합니다.
    
    f. **확인**을 클릭합니다.

    g. **저장**을 클릭합니다.

    > [!NOTE]
    > 기본적으로 Azure AD에 없는 특성을 추가하려면 확장 특성을 사용합니다. **Zendesk**에서 허용하는 전체 SAML 특성 목록을 가져오려면 [User attributes that can be set in SAML](https://support.zendesk.com/hc/en-us/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise-)(SAML에서 설정할 수 있는 사용자 특성)을 클릭하세요.

8. SAML 서명 인증서 섹션의 **SAML 서명 인증서** 섹션에서 **지문**을 복사한 후 컴퓨터에 저장합니다.

    ![이미지](./media/zendesk-tutorial/C3_certificate.png)

    a. 필요한 경우 **서명 옵션**에 대한 적절한 옵션을 선택합니다.

    b. 필요한 경우 **서명 알고리즘**에 대한 적절한 옵션을 선택합니다.

    다. 페이지 맨 아래에 있는 **저장**

9. **Zendesk 설정** 섹션으로 이동한 다음, **단계별 지침 보기**를 클릭하여 **로그온 구성** 창을 엽니다. **빠른 참조 섹션**에서 아래 URL을 복사합니다.

    이 URL은 다음과 같을 수 있습니다.

    a. SAML Single Sign-On 서비스 URL

    b. 엔터티 ID

    다. 로그아웃 URL

    ![이미지](./media/zendesk-tutorial/d1_saml.png) 

10. Zendesk를 자동 및 수동의 두 가지 방법 중 하나로 구성할 수 있습니다.
  
11. Zendesk 내에서 구성을 자동화하려면 **확장 설치**를 클릭하여 **내 앱 보안 로그인 브라우저 확장**을 설치해야 합니다.

    ![이미지](./media/zendesk-tutorial/install_extension.png)

12. 브라우저에 확장을 추가한 후 **Zendesk 설정**을 클릭하면 Zendesk 애플리케이션으로 이동됩니다. 응용 프로그램에서 관리자 자격 증명을 입력하여 Zendesk에 로그인합니다. 브라우저 확장이 애플리케이션을 자동으로 구성하고 13단계를 자동으로 수행합니다.

     ![이미지](./media/zendesk-tutorial/d2_saml.png) 

13. Zendesk를 수동으로 설치하려는 경우 새 웹 브라우저 창을 열고 Zendesk 회사 사이트에 관리자로 로그인한 후에 다음 단계를 수행합니다.

    * **Admin**을 클릭합니다.

    * **설정**을 클릭하고 왼쪽 탐색 창에서 **보안**을 클릭합니다.

    * **보안** 페이지에서 다음 단계를 수행합니다.

      ![보안](././media/zendesk-tutorial/ic773089.png "보안")

      ![Single Sign-On](././media/zendesk-tutorial/ic773090.png "Single Sign-On")

      a. **관리자 및 에이전트** 탭을 클릭합니다.

      b. **SSO(Single Sign-On) 및 SAML**을 선택하고**SAML**을 선택합니다.

      다. Azure Portal에서 복사한 **SAML Single Sign-On 서비스 URL** 값을 **SAML SSO URL** 텍스트 상자에 붙여넣습니다.

      d. Azure Portal에서 복사한 **로그아웃 URL** 값을 **원격 로그아웃 URL** 텍스트 상자에 붙여넣습니다.

      e. Azure Portal에서 복사한 인증서의 **지문** 값을 **인증서 지문** 텍스트 상자에 붙여넣습니다.

      f. **저장**을 클릭합니다.

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기 

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**를 차례로 선택하고 **모든 사용자**를 선택합니다.

    ![이미지](./media/zendesk-tutorial/d_users_and_groups.png)

2. 화면 위쪽에서 **새 사용자**를 선택합니다.

    ![이미지](./media/zendesk-tutorial/d_adduser.png)

3. 사용자 속성에서 다음 단계를 수행합니다.

    ![이미지](./media/zendesk-tutorial/d_userproperties.png)

    a. **이름** 필드에 **BrittaSimon**을 입력합니다.
  
    b. **사용자 이름** 필드에 **brittasimon@yourcompanydomain.extension**을 입력합니다.  
    예를 들어 BrittaSimon@contoso.com

    다. **속성**을 선택하고 **암호 표시** 확인란을 선택한 다음 암호 상자에 표시된 값을 적어 둡니다.

    d. **만들기**를 선택합니다.

### <a name="create-a-zendesk-test-user"></a>Zendesk 테스트 사용자 만들기

이 섹션은 Zendesk에서 Britta Simon이라는 사용자를 만들기 위한 것입니다. Zendesk는 자동 사용자 프로비전을 지원하며 기본적으로 사용하도록 설정되어 있습니다. 자동 사용자 프로비전 구성 방법에 대한 자세한 내용은 [여기](Zendesk-provisioning-tutorial.md)에서 제공합니다.

**사용자를 수동으로 만들어야 할 경우 다음 단계를 수행합니다.**

> [!NOTE]
> **최종 사용자** 계정은 로그인할 때 자동으로 프로비전됩니다. **에이전트** 및 **관리자** 계정은 로그인하기 전에 **Zendesk**에서 수동으로 프로비전해야 합니다.

1. **Zendesk** 테넌트에 로그인합니다.

2. **고객 목록** 탭을 선택합니다.

3. **사용자** 탭을 선택하고 **추가**를 클릭합니다.

    ![사용자 추가](././media/zendesk-tutorial/ic773632.png "사용자 추가")
4. 프로비전하려는 기존 Azure AD 계정의 **이름**과 **메일**을 입력하고 **저장**을 클릭합니다.

    ![새 사용자](././media/zendesk-tutorial/ic773633.png "새 사용자")

> [!NOTE]
> 다른 Zendesk 사용자 계정 생성 도구 또는 Zendesk에서 제공하는 APIs를 사용하여 AAD 사용자 계정을 프로비전할 수 있습니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Zendesk에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**을 선택합니다.

    ![이미지](./media/zendesk-tutorial/d_all_applications.png)

2. 애플리케이션 목록에서 **Zendesk**를 선택합니다.

    ![이미지](./media/zendesk-tutorial/d_all_zendeskapplications.png)

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 선택합니다.

    ![이미지](./media/zendesk-tutorial/d_leftpaneusers.png)

4. **추가** 단추를 선택하고 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![이미지](./media/zendesk-tutorial/d_assign_user.png)

4. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택하고 화면 아래쪽에서 **선택** 단추를 클릭합니다.

5. **할당 추가** 대화 상자에서 **할당** 단추를 선택합니다.

### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Zendesk 타일을 클릭하면 Zendesk 애플리케이션에 자동으로 로그온됩니다.
액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](../user-help/active-directory-saas-access-panel-introduction.md)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)
* [사용자 프로비저닝 구성](zendesk-provisioning-tutorial.md)
