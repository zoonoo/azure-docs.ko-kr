---
title: '자습서: NetSuite와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 NetSuite 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: dafa0864-aef2-4f5e-9eac-770504688ef4
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2018
ms.author: jeedes
ms.openlocfilehash: 511fdcf587d16a59ff2bb11dfc55504b2218a569
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39431413"
---
# <a name="tutorial-azure-active-directory-integration-with-netsuite"></a>자습서: NetSuite와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 NetSuite를 통합하는 방법에 대해 알아봅니다.

NetSuite를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- NetSuite에 액세스할 수 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 자신의 Azure AD 계정으로 NetSuite에 자동으로 로그온(Single Sign-on)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On](../manage-apps/what-is-single-sign-on.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건

NetSuite와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- NetSuite Single Sign-On이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 얻을 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다.  이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 NetSuite 추가
1. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-netsuite-from-the-gallery"></a>갤러리에서 NetSuite 추가
NetSuite를 Azure AD에 통합하도록 구성하려면 갤러리에서 NetSuite를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 NetSuite를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Active Directory][1]

1. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 응용 프로그램**으로 이동합니다.

    ![애플리케이션][2]

1. 대화 상자 맨 위 있는 **새 응용 프로그램** 단추를 클릭합니다.

    ![애플리케이션][3]

1. 검색 상자에 **NetSuite**를 입력하고 결과 패널에서 **NetSuite**를 선택한 다음, **추가** 단추를 클릭하여 응용 프로그램을 추가합니다.

    ![결과 목록의 NetSuite](./media/netsuite-tutorial/tutorial_netsuite_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD Single Sign-on 구성 및 테스트
이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 사용하여 NetSuite에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 NetSuite 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 NetSuite의 관련 사용자 간에 연결 관계를 설정해야 합니다.

이 연결 관계는 Azure AD의 **사용자 이름** 값을 NetSuite의 **Username** 값으로 할당하여 설정합니다.

NetSuite에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
1. **[Azure AD 테스트 사용자 만들기](#creating-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On 테스트하는 데 사용합니다.
1. **[NetSuite 테스트 사용자 만들기](#creating-a-netsuite-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 NetSuite에 만듭니다.
1. **[Azure AD 테스트 사용자 할당](#assigning-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
1. **[Single Sign-On 테스트](#testing-single-sign-on)** - 구성이 작동하는지 확인합니다.

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 NetSuite 응용 프로그램에서 Single Sign-On을 구성합니다.

**NetSuite에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **NetSuite** 응용 프로그램 통합 페이지에서 **Single Sign-On**을 클릭합니다.

    ![Configure Single Sign-On][4]

1. **Single Sign-On** 대화 상자에서 **모드**를 **SAML 기반 로그온**으로 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Configure Single Sign-On](./media/NetSuite-tutorial/tutorial_NetSuite_samlbase.png)

1. **NetSuite 도메인 및 URL** 섹션에서 다음 단계를 수행합니다.

    ![Configure Single Sign-On](./media/NetSuite-tutorial/tutorial_NetSuite_url.png)

    **회신 URL** 텍스트 상자에 다음 패턴으로 URL을 입력합니다.

    `https://<tenant-name>.NetSuite.com/saml2/acs`

    `https://<tenant-name>.na1.NetSuite.com/saml2/acs`

    `https://<tenant-name>.na2.NetSuite.com/saml2/acs`

    `https://<tenant-name>.sandbox.NetSuite.com/saml2/acs`

    `https://<tenant-name>.na1.sandbox.NetSuite.com/saml2/acs`

    `https://<tenant-name>.na2.sandbox.NetSuite.com/saml2/acs`
    
    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 회신 URL로 값을 업데이트하세요. 이러한 값을 얻으려면 [NetSuite 지원 팀](http://www.NetSuite.com/portal/services/support.shtml)에 문의하세요.

1. **SAML 서명 인증서** 섹션에서 **메타데이터 XML**을 클릭한 후 컴퓨터에 XML 파일을 저장합니다.

    ![Configure Single Sign-On](./media/NetSuite-tutorial/tutorial_NetSuite_certificate.png) 

1. **저장** 단추를 클릭합니다.

    ![Configure Single Sign-On](./media/NetSuite-tutorial/tutorial_general_400.png)

1. **NetSuite 구성** 섹션에서 **NetSuite 구성**을 클릭하여 **로그온 구성** 창을 엽니다. **빠른 참조 섹션**에서 **SAML Single Sign-On 서비스 URL**을 복사합니다.

    ![Configure Single Sign-On](./media/NetSuite-tutorial/tutorial_NetSuite_configure.png)

1. 브라우저에서 새 탭을 열고 관리자 권한으로 NetSuite 회사 사이트에 로그인합니다.

1. 페이지의 맨 위에 있는 도구 모음에서 **설정**을 클릭한 다음, **회사**로 이동하고, **기능 사용**을 클릭합니다.

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-setupsaml.png)

1. 페이지의 가운데에 있는 도구 모음에서 **SuiteCloud**를 클릭합니다.

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-suitecloud.png)

1. **인증 관리** 섹션 아래에서 **SAML Single Sign-On**을 선택하여 NetSuite에서 SAML Single Sign-On 옵션을 사용하도록 설정합니다.

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-ticksaml.png)

1. 페이지의 위쪽에 있는 도구 모음에서 **설정**을 클릭합니다.

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-setup.png)

1. **설정 작업** 목록에서 **통합**을 클릭합니다.

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-integration.png)

1. **인증 관리** 섹션에서 **SAML Single Sign-On**을 클릭합니다.

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-saml.png)

1. **SAML 설정** 페이지의 **NetSuite 구성** 섹션에서 다음 단계를 수행합니다.

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-saml-setup.png)
  
    a. **기본 인증 방법**을 선택합니다.

    나. **SAMLV2 ID 공급자 메타데이터**라는 레이블이 지정된 필드에서 **IDP 메타데이터 파일 업로드**를 선택합니다. 그런 다음 **찾아보기**를 클릭하여 Azure Portal에서 다운로드한 메타데이터 파일을 업로드합니다.

    다. **제출**을 클릭합니다.

1. Azure AD에서 **기타 모든 사용자 특성 보기 및 편집** 확인란을 클릭하고 특성을 추가합니다.

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-attributes.png)

1. **특성 이름** 필드에 `account`을(를) 입력합니다. **특성 값** 필드에 NetSuite 계정 ID를 입력합니다. 이 값은 상수이며 계정에 따라 변경됩니다. 사용자의 계정 ID를 찾는 방법에 대한 지침은 아래에 포함되어 있습니다.

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-add-attribute.png)

    a. NetSuite에서 **설정**을 클릭한 다음, **회사**로 이동하고, 위쪽 탐색 메뉴에서 **회사 정보**를 클릭합니다.

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-com.png)

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-account-id.png)

    나. 오른쪽 열의 **회사 정보** 페이지에서 **계정 ID**를 복사합니다.

    다. NetSuite 계정에서 복사한 **계정 ID**를 Azure AD의 **특성 값** 필드에 붙여넣습니다. 

1. 사용자가 NetSuite에 Single Sign-On을 수행하려면 먼저 NetSuite에 적절한 권한을 할당해야 합니다. 이러한 사용 권한을 할당하려면 아래 지침을 따릅니다.

    a. 위쪽 탐색 메뉴에서 **설정**을 클릭합니다.

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-setup.png)

    나. 왼쪽 탐색 메뉴에서 **사용자/역할**을 선택한 다음 **역할 관리**를 클릭합니다.

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-manage-roles.png)

    다. **새 역할**을 클릭합니다.

    d. 새 역할에 **이름**을 입력합니다.

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-new-role.png)

    e. **저장**을 클릭합니다.

    f. 위쪽에 있는 메뉴에서 **사용 권한**을 클릭합니다. **설치**를 클릭합니다.

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-sso.png)

    g. **SAML Single Sign-On**을 선택한 다음, **추가**를 클릭합니다.

    h. **저장**을 클릭합니다.

    i. 위쪽 탐색 메뉴에서 **설치**를 클릭한 다음 **설치 관리자**를 클릭합니다.

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-setup.png)

    j. 왼쪽 탐색 메뉴에서 **사용자/역할**을 선택한 다음 **사용자 관리**를 클릭합니다.

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-manage-users.png)

    k. 테스트 사용자를 선택합니다. **편집**을 클릭한 다음, **액세스** 탭으로 이동합니다.

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-edit-user.png)

    l. 역할 대화 상자에서 만든 적합한 역할을 할당합니다.

    ![Configure Single Sign-On](./media/NetSuite-tutorial/ns-add-role.png)

    m. **저장**을 클릭합니다.
 
### <a name="creating-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기
이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

![Azure AD 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. **Azure Portal**의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure AD 테스트 사용자 만들기](./media/NetSuite-tutorial/create_aaduser_01.png) 

1.  사용자 목록을 표시하려면 **사용자 및 그룹**으로 이동한 후 **모든 사용자**를 클릭합니다.
    
    ![Azure AD 테스트 사용자 만들기](./media/NetSuite-tutorial/create_aaduser_02.png) 

1. 대화 상자 위쪽에서 **추가**를 클릭하여 **사용자** 대화 상자를 엽니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/NetSuite-tutorial/create_aaduser_03.png) 

1. **사용자** 대화 상자 페이지에서 다음 단계를 수행합니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/NetSuite-tutorial/create_aaduser_04.png) 

    a. **이름** 텍스트 상자에 **BrittaSimon**을 입력합니다.

    나. **사용자 이름** 텍스트 상자에 BrittaSimon의 **전자 메일 주소**를 입력합니다.

    다. **암호 표시**를 선택하고 **암호** 값을 적어둡니다.

    d. **만들기**를 클릭합니다. 

### <a name="creating-a-netsuite-test-user"></a>NetSuite 테스트 사용자 만들기

이 섹션에서는 NetSuite에서 Britta Simon이라는 사용자를 만듭니다. NetSuite는 Just-In-Time 프로비전을 지원하며, 이 프로비전은 기본적으로 사용하도록 설정됩니다.
이 섹션에 작업 항목이 없습니다. NetSuite에 아직 사용자가 없으면 NetSuite에 액세스하려고 할 때 새 사용자가 만들어집니다.


### <a name="assigning-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 NetSuite에 대한 액세스 권한을 부여합니다.

![사용자 할당][200] 

**Britta Simon을 NetSuite에 할당하려면 다음 단계를 수행합니다.**

1. Azure Portal에서 응용 프로그램 보기를 연 다음 디렉터리 보기로 이동하고 **엔터프라이즈 응용 프로그램**으로 이동한 후 **모든 응용 프로그램**을 클릭합니다.

    ![사용자 할당][201] 

1. 응용 프로그램 목록에서 **NetSuite**를 선택합니다.

    ![Configure Single Sign-On](./media/NetSuite-tutorial/tutorial_NetSuite_app.png) 

1. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    ![사용자 할당][202] 

1. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 할당][203]

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다.

1. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다.

1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.
    
### <a name="testing-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

Single Sign-On 설정을 테스트하려면 [https://myapps.microsoft.com](https://myapps.microsoft.com/)에서 액세스 패널을 연 다음, 테스트 계정에 로그인하고 **NetSuite**를 클릭합니다.

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)
* [사용자 프로비저닝 구성](NetSuite-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/NetSuite-tutorial/tutorial_general_01.png
[2]: ./media/NetSuite-tutorial/tutorial_general_02.png
[3]: ./media/NetSuite-tutorial/tutorial_general_03.png
[4]: ./media/NetSuite-tutorial/tutorial_general_04.png

[100]: ./media/NetSuite-tutorial/tutorial_general_100.png

[200]: ./media/NetSuite-tutorial/tutorial_general_200.png
[201]: ./media/NetSuite-tutorial/tutorial_general_201.png
[202]: ./media/NetSuite-tutorial/tutorial_general_202.png
[203]: ./media/NetSuite-tutorial/tutorial_general_203.png

