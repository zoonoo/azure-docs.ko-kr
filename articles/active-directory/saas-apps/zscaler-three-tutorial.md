---
title: '자습서: Azure Active Directory와 Zscaler Three 통합 | Microsoft Docs'
description: Azure Active Directory와 Zscaler Three 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: f352e00d-68d3-4a77-bb92-717d055da56f
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2018
ms.author: jeedes
ms.openlocfilehash: b148967af0882993d8ab113bdf0fd3ad3835296f
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50092613"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-three"></a>자습서: Azure Active Directory와 Zscaler Three 통합

이 문서에서는 Azure AD(Azure Active Directory)와 Zscaler Three를 통합하는 방법에 대해 알아봅니다.

Zscaler Three를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- Zscaler Three에 액세스할 수 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 자신의 Azure AD 계정으로 Zscaler Three에 자동으로 로그온(Single Sign-On)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](../manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

Zscaler Three와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- Zscaler Three Single Sign-On이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [평가판 제품](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 얻을 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다.
 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 Zscaler Three 추가
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-zscaler-three-from-the-gallery"></a>갤러리에서 Zscaler Three 추가

Zscaler Three가 Azure AD에 통합되도록 구성하려면 갤러리의 Zscaler Three를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Zscaler Three를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Active Directory][1]

2. **엔터프라이즈 응용 프로그램**으로 이동한 다음, **모든 응용 프로그램** 옵션을 선택합니다.

    ![애플리케이션][2]

3. 대화 상자 맨 위에서 **새 애플리케이션** 단추를 클릭합니다.

    ![애플리케이션][3]

4. 결과 패널에서 **Zscaler Three**를 선택하고 **추가** 단추를 클릭하여 애플리케이션을 추가합니다.

    ![Azure AD 테스트 사용자 만들기](./media/zscaler-three-tutorial/tutorial_zscalerthree_addfromgallery.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD Single Sign-on 구성 및 테스트

이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 사용하여 Zscaler Three에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 Zscaler Three 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 Zscaler Three의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Zscaler Three에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[프록시 설정 구성](#configuring-proxy-settings)** - Internet Explorer에서 프록시 설정을 구성합니다.
3. **[Azure AD 테스트 사용자 만들기](#creating-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On 테스트하는 데 사용합니다.
4. **[Zscaler Three 테스트 사용자 만들기](#creating-a-zscaler-three-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 Zscaler Three에 만듭니다.
5. **[Azure AD 테스트 사용자 할당](#assigning-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
6. **[Single Sign-On 테스트](#testing-single-sign-on)** - 구성이 작동하는지 확인합니다.

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 Zscaler Three 애플리케이션에서 Single Sign-On을 구성합니다.

**Zscaler Three에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **Zscaler Three** 애플리케이션 통합 페이지에서 **Single Sign-On**을 클릭합니다.

    ![Configure Single Sign-On][4]

2. **Single Sign-On 방법 선택** 대화 상자에서 **SAML/WS-Fed** 모드를 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Configure Single Sign-On](./media/zscaler-three-tutorial/tutorial_general_301.png)

3. 다른 모드에서 **SAML** 모드로 변경해야 하는 경우 화면 맨 위에서 **Single Sign-On 모드 변경**을 클릭합니다.

    ![Configure Single Sign-On](./media/zscaler-three-tutorial/tutorial_general_300.png)

4. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 아이콘을 클릭하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![Configure Single Sign-On](./media/zscaler-three-tutorial/tutorial_general_302.png)

5. **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    ![Configure Single Sign-On](./media/zscaler-three-tutorial/tutorial_zscalerthree_url.png)

    로그온 URL 텍스트 상자에 `https://login.zscalerthree.net/sfc_sso`를 입력합니다.

6. **SAML 서명 인증서** 섹션에서 **다운로드**를 클릭하여 **인증서(Base64)** 를 다운로드한 다음, 컴퓨터에 인증서 파일을 저장합니다.

    ![Configure Single Sign-On](./media/zscaler-three-tutorial/tutorial_zscalerthree_certificate.png)

8. **Zscaler Three 설정** 섹션에서 **로그인 URL**을 복사합니다.

    ![Configure Single Sign-On](./media/zscaler-three-tutorial/tutorial_zscalerthree_configure.png)

9. 다른 웹 브라우저 창에서 Zscaler Three 회사 사이트에 관리자로 로그인합니다.

10. 위쪽의 메뉴에서 **관리**를 클릭합니다.

    ![관리](./media/zscaler-three-tutorial/ic800206.png "관리")

9. **관리자 & 역할 관리**에서 **사용자 및 인증 관리**를 클릭합니다.

    ![사용자 및 인증 관리](./media/zscaler-three-tutorial/ic800207.png "사용자 및 인증 관리")

10. **구성에 대한 인증 옵션 선택** 섹션에서, 다음 단계를 수행합니다.

    ![인증](./media/zscaler-three-tutorial/ic800208.png "인증")

    a. **SAML Single Sign-On을 사용하여 인증**을 선택합니다.

    b. **SAML Single Sign-On 매개 변수 구성**을 클릭합니다.

11. **SAML Single Sign-On 매개 변수 구성** 대화 상자 페이지에서 다음 단계를 수행하고 **완료**를 클릭합니다.

    ![Single Sign-On](./media/zscaler-three-tutorial/ic800209.png "Single Sign-On")

    a. Azure Portal에서 복사한 **로그인 URL** 값을 **인증할 사용자가 전송되는 SAML 포털의 URL** 텍스트 상자에 붙여넣습니다.

    b. **로그인 이름을 포함한 특성** 텍스트 상자에 **NameID**를 입력합니다.

    다. **Zscaler pem**을 클릭하여 다운로드한 인증서를 업로드합니다.

    d. **SAML 자동 프로비전 사용**을 선택합니다.

12. **사용자 인증 구성** 대화 상자 페이지에서 다음 단계를 수행합니다.

    ![관리](./media/zscaler-three-tutorial/ic800210.png "관리")

    a. **저장**을 클릭합니다.

    b. **지금 활성화**를 클릭합니다.

## <a name="configuring-proxy-settings"></a>프록시 설정 구성

### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Internet Explorer에서 프록시 설정을 구성하려면

1. **Internet Explorer**를 시작합니다.

2. **도구** 메뉴에서 **인터넷 옵션**을 선택하여 **인터넷 옵션** 대화 상자를 엽니다.

     ![인터넷 옵션](./media/zscaler-three-tutorial/ic769492.png "인터넷 옵션")

3. **연결** 탭을 클릭합니다.
  
     ![연결](./media/zscaler-three-tutorial/ic769493.png "연결")

4. **LAN 설정**을 클릭하여 **LAN 설정** 대화 상자를 엽니다.

5. 프록시 서버 섹션에서 다음 단계를 수행합니다.

    ![프록시 서버](./media/zscaler-three-tutorial/ic769494.png "프록시 서버")

    a. **사용자 LAN의 프록시 서버 사용**을 선택합니다.

    b. 주소 텍스트 상자에 **gateway.zscalerthree.net**을 입력합니다.

    다. 포트 텍스트 상자에 **80**을 입력합니다.

    d. **로컬 주소의 바이패스 프록시 서버**를 선택합니다.

    e. **확인**을 클릭하여 **LAN(Local Area Network) 설정** 대화 상자를 닫습니다.

6. **확인**을 클릭하여 **인터넷 옵션** 대화 상자를 닫습니다.

### <a name="creating-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**를 차례로 선택하고 **모든 사용자**를 선택합니다.

    ![Azure AD 사용자 만들기][100]

2. 화면 위쪽에서 **새 사용자**를 선택합니다.

    ![Azure AD 테스트 사용자 만들기](./media/zscaler-three-tutorial/create_aaduser_01.png) 

3. 사용자 속성에서 다음 단계를 수행합니다.

    ![Azure AD 테스트 사용자 만들기](./media/zscaler-three-tutorial/create_aaduser_02.png)

    a. **이름** 필드에 **BrittaSimon**을 입력합니다.
  
    b. **사용자 이름** 필드에 **brittasimon@yourcompanydomain.extension**을 입력합니다.  
    예를 들어 BrittaSimon@contoso.com

    다. **속성**을 선택하고 **암호 표시** 확인란을 선택한 다음, 암호 상자에 표시된 값을 적어 둡니다.

    d. **만들기**를 클릭합니다.

### <a name="creating-a-zscaler-three-test-user"></a>Zscaler Three 테스트 사용자 만들기

Azure AD 사용자가 Zscaler Three에 로그인할 수 있도록 하려면 Zscaler Three로 프로비전되어야 합니다. Zscaler Three의 경우 수동으로 프로비전합니다.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>사용자 프로비전을 구성하려면

1. **Zscaler Three** 테넌트에 로그인합니다.

2. **관리**를 클릭합니다.

    ![관리](./media/zscaler-three-tutorial/ic781035.png "관리")

3. **사용자 관리**를 클릭합니다.

     ![추가](./media/zscaler-three-tutorial/ic781036.png "추가")

4. **사용자** 탭에서 **추가**를 클릭합니다.

    ![추가](./media/zscaler-three-tutorial/ic781037.png "추가")

5. 사용자 추가 섹션에서 다음 단계를 수행합니다.

    ![사용자 추가](./media/zscaler-three-tutorial/ic781038.png "사용자 추가")

    a. **사용자ID**, **사용자 표시 이름**, **암호**, **암호 확인**을 입력하고, 프로비전할 유효한 Azure AD 계정의 **그룹** 및 **부서**를 선택합니다.

    b. **저장**을 클릭합니다.

> [!NOTE]
> 다른 Zscaler Three 사용자 계정 생성 도구 또는 Zscaler Three에서 제공한 API를 사용하여 Azure AD 사용자 계정을 프로비전할 수 있습니다.

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Zscaler Three에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**을 선택합니다.

    ![사용자 할당][201]

2. 애플리케이션 목록에서 **Zscaler Three**를 선택합니다.

    ![Configure Single Sign-On](./media/zscaler-three-tutorial/tutorial_zscalerthree_app.png)

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    ![사용자 할당][202]

4. **할당 추가** 대화 상자에서 **사용자 추가** 단추를 클릭한 다음, **사용자 및 그룹**을 선택합니다.

    ![사용자 할당][203]

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택하고 화면 아래쪽에서 **선택** 단추를 클릭합니다.

6. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

### <a name="testing-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Zscaler Three 타일을 클릭하면 Zscaler Three 애플리케이션에 자동으로 로그온됩니다.
액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](../user-help/active-directory-saas-access-panel-introduction.md)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/zscaler-three-tutorial/tutorial_general_01.png
[2]: ./media/zscaler-three-tutorial/tutorial_general_02.png
[3]: ./media/zscaler-three-tutorial/tutorial_general_03.png
[4]: ./media/zscaler-three-tutorial/tutorial_general_04.png

[100]: ./media/zscaler-three-tutorial/tutorial_general_100.png

[200]: ./media/zscaler-three-tutorial/tutorial_general_200.png
[201]: ./media/zscaler-three-tutorial/tutorial_general_201.png
[202]: ./media/zscaler-three-tutorial/tutorial_general_202.png
[203]: ./media/zscaler-three-tutorial/tutorial_general_203.png