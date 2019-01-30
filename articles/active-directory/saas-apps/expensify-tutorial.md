---
title: '자습서: Expensify와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 Expensify 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 1e761484-7a2f-4321-91f4-6d5d0b69344e
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2018
ms.author: jeedes
ms.openlocfilehash: 281850a1ceab71df374f9b62a93029adcf655d6b
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2019
ms.locfileid: "54812117"
---
# <a name="tutorial-azure-active-directory-integration-with-expensify"></a>자습서: Expensify와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Expensify를 통합하는 방법에 대해 알아봅니다.

Expensify를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- Expensify에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정으로 Expensify에 자동으로 로그온(Single Sign-On)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](../manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

Expensify와 Azure AD의 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- Expensify Single Sign-On이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [1개월 평가판을 얻을](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다.  이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 Expensify 추가
1. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-expensify-from-the-gallery"></a>갤러리에서 Expensify 추가

Expensify와 Azure AD의 통합을 구성하려면 갤러리의 Expensify를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Expensify를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![이미지](./media/expensify-tutorial/selectazuread.png)

2. **엔터프라이즈 애플리케이션**으로 이동합니다. 그런 후 **모든 애플리케이션**으로 이동합니다.

    ![이미지](./media/expensify-tutorial/a_select_app.png)
    
3. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션** 단추를 클릭합니다.

    ![이미지](./media/expensify-tutorial/a_new_app.png)

4. 검색 상자에서 **Expensify**를 입력하고, 결과 패널에서 **Expensify**를 선택한 다음, **추가** 단추를 클릭하여 애플리케이션을 추가합니다.

     ![이미지](./media/expensify-tutorial/a_add_app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 Expensify에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 Expensify 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 Expensify의 관련 사용자 간에 연결이 설정되어야 합니다.

Expensify에서 Azure AD의 **사용자 이름** 값을 **Username** 값으로 할당하여 링크 관계를 설정합니다.

Expensify에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
1. **[Expensify 테스트 사용자 만들기](#create-an-expensify-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 Expensify에 만듭니다.
1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
1. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 Expensify 애플리케이션에서 Single Sign-On을 구성합니다.

**Expensify에서 Azure AD Single Sign-on을 구성하려면 다음 단계를 수행합니다.**

1. [Azure Portal](https://portal.azure.com/)의 **Expensify** 애플리케이션 통합 페이지에서 **Single Sign-On**을 선택합니다.

    ![이미지](./media/expensify-tutorial/b1_b2_select_sso.png)

2. 화면 위쪽에서 **Single Sign-On 모드 변경**을 클릭하여 **SAML** 모드를 선택합니다.

      ![이미지](./media/expensify-tutorial/b1_b2_saml_ssso.png)

3. **Single Sign-On 방법 선택** 대화 상자에서 **SAML** 모드에 대해 **선택**을 클릭하여 Single Sign-On을 사용하도록 설정합니다.

    ![이미지](./media/expensify-tutorial/b1_b2_saml_sso.png)

4. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 단추를 클릭하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![이미지](./media/expensify-tutorial/b1-domains_and_urlsedit.png)

5. **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    a. **로그온 URL** 텍스트 상자에 URL을 다음과 같이 입력합니다. `https://www.expensify.com/authentication/saml/login`

    b. **식별자** 텍스트 상자에서 `https://www.<companyname>.expensify.com` 패턴을 사용하여 URL을 입력합니다.

    ![이미지](./media/expensify-tutorial/b1-domains_and_urls.png)

    > [!NOTE] 
    > 식별자 URL의 <companyname> 섹션을 회사의 도메인으로 바꿉니다. 위의 `https://contoso.expensify.com` 예를 참조하세요. 이 도메인은 Expensify에서 **설정 > 도메인 제어**에 표시되는 도메인의 이름입니다.

    ![Expensify 도메인 정보](./media/expensify-tutorial/tutorial_expensify_domain.png)

6. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드**를 클릭하여 요구 사항에 따라 적절한 인증서를 다운로드한 다음 컴퓨터에 저장합니다.

    ![이미지](./media/expensify-tutorial/certificatebase64.png)

7. Expensify에서 SSO를 사용하도록 설정하려면 먼저 애플리케이션에서 **도메인 컨트롤**을 사용하도록 설정해야 합니다. [여기에](https://help.expensify.com/domain-control) 나열된 단계를 수행하여 애플리케이션에서 도메인 컨트롤을 사용하도록 설정할 수 있습니다. 추가 지원은 [Expensify 클라이언트 지원 팀](mailto:help@expensify.com)에 문의하세요. 도메인 컨트롤을 사용하도록 설정한 후에는 다음 단계를 따릅니다.
   
    ![Configure Single Sign-On](./media/expensify-tutorial/tutorial_expensify_51.png)
    
    a. Expensify 애플리케이션에 로그인합니다.
    
    b. 왼쪽 패널에서 **설정**을 클릭한 다음 **SAML**로 이동합니다.
    
    다. **SAML 로그인** 옵션을 **사용**으로 전환합니다.
    
    d. Azure AD에서 다운로드한 페더레이션 메타데이터를 메모장으로 열고 콘텐츠를 복사하여 **ID 공급자 메타데이터** 텍스트 상자에 붙여넣습니다.

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기 

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**를 차례로 선택하고 **모든 사용자**를 선택합니다.

    ![이미지](./media/expensify-tutorial/d_users_and_groups.png)

2. 화면 위쪽에서 **새 사용자**를 선택합니다.

    ![이미지](./media/expensify-tutorial/d_adduser.png)

3. 사용자 속성에서 다음 단계를 수행합니다.

    ![이미지](./media/expensify-tutorial/d_userproperties.png)

    a. **이름** 필드에 **BrittaSimon**을 입력합니다.
  
    b. **사용자 이름** 필드에 **brittasimon@yourcompanydomain.extension**을 입력합니다.  
    예를 들어 BrittaSimon@contoso.com

    다. **속성**을 선택하고 **암호 표시** 확인란을 선택한 다음, 암호 상자에 표시된 값을 적어 둡니다.

    d. **만들기**를 선택합니다.
 
### <a name="create-an-expensify-test-user"></a>Expensify 테스트 사용자 만들기

이 섹션에서는 Expensify에서 Britta Simon이라는 사용자를 만듭니다. Expensify 플랫폼에서 사용자를 추가하려면 [Expensify 클라이언트 지원 팀](mailto:help@expensify.com)에 문의하세요.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Expensify에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**을 선택합니다.

    ![이미지](./media/expensify-tutorial/d_all_applications.png)

2. 애플리케이션 목록에서 **Expensify**를 선택합니다.

    ![이미지](./media/expensify-tutorial/d_all_proapplications.png)

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 선택합니다.

    ![이미지](./media/expensify-tutorial/d_leftpaneusers.png)

4. **추가** 단추를 선택하고 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![이미지](./media/expensify-tutorial/d_assign_user.png)

4. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택하고 화면 아래쪽에서 **선택** 단추를 클릭합니다.

5. **할당 추가** 대화 상자에서 **할당** 단추를 선택합니다.
    
### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Expensify 타일을 클릭하면 Expensify 애플리케이션에 자동으로 로그온됩니다.
액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](../user-help/active-directory-saas-access-panel-introduction.md)를 참조하세요. 

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](tutorial-list.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)




