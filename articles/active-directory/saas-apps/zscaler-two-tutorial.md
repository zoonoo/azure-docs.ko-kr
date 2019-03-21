---
title: '자습서: Azure Active Directory와 Zscaler Two 통합 | Microsoft Docs'
description: Azure Active Directory와 Zscaler Two 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 1fd8a940-7320-47e0-a176-2dd4eeca6db2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/10/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: cdfd60cf91df013e097e7e09c04b589416d1f531
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2019
ms.locfileid: "57880673"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-two"></a>자습서: Azure Active Directory와 Zscaler Two 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Zscaler Two를 통합하는 방법에 대해 알아봅니다.

Zscaler Two를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- Zscaler Two에 액세스할 수 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 자신의 Azure AD 계정으로 Zscaler Two에 자동으로 로그온(Single Sign-On)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](../manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

Zscaler Two와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- Zscaler Two Single Sign-On이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [1개월 평가판을 얻을](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 Zscaler Two 추가
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-zscaler-two-from-the-gallery"></a>갤러리에서 Zscaler Two 추가

Zscaler Two가 Azure AD에 통합되도록 구성하려면 갤러리의 Zscaler Two를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Zscaler Two를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Azure Active Directory 단추][1]

2. **엔터프라이즈 애플리케이션**으로 이동합니다. 그런 후 **모든 애플리케이션**으로 이동합니다.

    ![엔터프라이즈 애플리케이션 블레이드][2]

3. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션** 단추를 클릭합니다.

    ![새 애플리케이션 단추][3]

4. 검색 상자에 **Zscaler Two**를 입력하고 결과 패널에서 **Zscaler Two**를 선택한 후 **추가** 단추를 클릭하여 애플리케이션을 추가합니다.

    ![결과 목록에 표시된 Zscaler Two](./media/zscaler-two-tutorial/tutorial_zscalertwo_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 사용하여 Zscaler Two에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 Zscaler Two 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 Zscaler Two의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Zscaler Two에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#creating-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On 테스트하는 데 사용합니다.
3. **[Zscaler Two 테스트 사용자 만들기](#creating-a-zscaler-two-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 Zscaler Two에 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assigning-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Single Sign-On 테스트](#testing-single-sign-on)** - 구성이 작동하는지 확인합니다.

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 Zscaler Two 애플리케이션에서 Single Sign-On을 구성합니다.

**Zscaler Two에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **Zscaler Two** 애플리케이션 통합 페이지에서 **Single Sign-On**을 클릭합니다.

    ![Single Sign-On 구성 링크][4]

2. **Single Sign-On 방법 선택** 대화 상자에서 **SAML** 모드에 대해 **선택**을 클릭하여 Single Sign-On을 사용하도록 설정합니다.

    ![Configure Single Sign-On](common/tutorial_general_301.png)

3. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 아이콘을 클릭하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![Configure Single Sign-On](common/editconfigure.png)

4. **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    ![Zscaler Two 도메인 및 URL Single Sign-On 정보](./media/zscaler-two-tutorial/tutorial_zscalertwo_url.png)

    [로그온 URL] 텍스트 상자에 사용자가 ZScaler Two 애플리케이션에 로그인하는 데 사용하는 URL을 입력합니다.

    > [!NOTE] 
    > 이 값은 실제 로그온 URL로 업데이트해야 합니다. 이러한 값을 얻으려면 [Zscaler Two 클라이언트 지원 팀](https://www.zscaler.com/company/contact)에 문의하세요.

5. Zscaler Two 애플리케이션에는 특정 형식의 SAML 어설션이 필요합니다. 이 애플리케이션에 대해 다음 클레임을 구성합니다. 애플리케이션 통합 페이지의 **사용자 특성 및 클레임** 섹션에서 이러한 특성의 값을 관리할 수 있습니다. **SAML로 Single Sign-On 설정 페이지**에서 **편집** 단추를 클릭하여 **사용자 특성 및 클레임** 대화 상자를 엽니다.

    ![특성 링크](./media/zscaler-two-tutorial/tutorial_zscalertwo_attribute.png)

6. **사용자 특성** 대화 상자의 **사용자 클레임** 섹션에서 위의 이미지에 표시된 것과 같이 SAML 토큰 특성을 구성하고 다음 단계를 수행합니다.

    | 이름  | 원본 특성  |
    | ---------| ------------ |
    | memberOf     | user.assignedroles |

    a. **새 클레임 추가**를 클릭하여 **사용자 클레임 관리** 대화 상자를 엽니다.

    ![이미지](./common/new_save_attribute.png)
    
    ![이미지](./common/new_attribute_details.png)

    b. **원본 특성** 목록에서 특성 값을 선택합니다.

    다. **Ok**를 클릭합니다.

    d. **저장**을 클릭합니다.

    > [!NOTE]
    > [여기](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management)를 클릭하여 Azure AD에서 역할을 구성하는 방법을 알아봅니다.

7. **SAML 서명 인증서** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드**를 클릭하고 **인증서(Base64)** 다운로드한 다음, 컴퓨터에 인증서 파일을 저장합니다.

    ![인증서 다운로드 링크](./media/zscaler-two-tutorial/tutorial_zscalertwo_certificate.png) 

8. **Zscaler Two 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    a. 로그인 URL

    b. Azure AD 식별자

    다. 로그아웃 URL

    ![Zscaler Two 구성](common/configuresection.png)

9. 다른 웹 브라우저 창에서 Zscaler Two 회사 사이트에 관리자로 로그인합니다.

10. **관리 > 인증 > 인증 설정**으로 이동하고 다음 단계를 수행합니다.
   
    ![관리](./media/zscaler-two-tutorial/ic800206.png "관리")

    a. 인증 형식에서 **SAML**을 선택합니다.

    b. **SAML 구성**을 클릭합니다.

11. **SAML 편집** 창에서 다음 단계를 수행하고 저장을 클릭합니다.  
            
    ![사용자 및 인증 관리](./media/zscaler-two-tutorial/ic800208.png "사용자 및 인증 관리")
    
    a. Azure Portal에서 복사한 **로그인 URL** 값을 **SAML 포털 URL** 텍스트 상자에 붙여넣습니다.

    b. **로그인 이름 특성** 텍스트 상자에 **NameID**을 입력합니다.

    다. **업로드**를 클릭하여 Azure Portal에서 다운로드한 Azure SAML 서명 인증서를 **공용 SSL 인증서**에 업로드합니다.

    d. **SAML 자동 프로비전 사용**을 선택/해제합니다.

    e. displayName 특성에 대해 SAML 자동 프로비전을 사용하도록 설정하려는 경우 **사용자 표시 이름 특성** 텍스트 상자에 **displayName**을 입력합니다.

    f. memberOf 특성에 대해 SAML 자동 프로비전을 사용하도록 설정하려는 경우 **그룹 이름 특성** 텍스트 상자에 **memberOf**를 입력합니다.

    g. 부서 특성에 대해 SAML 자동 프로비전을 사용하도록 설정하려는 경우 **부서 이름 특성** 텍스트 상자에 **부서**를 입력합니다.

    i. **저장**을 클릭합니다.

12. **사용자 인증 구성** 대화 상자 페이지에서 다음 단계를 수행합니다.

    ![관리](./media/zscaler-two-tutorial/ic800207.png)

    a. 왼쪽 아래 근처에 있는 **활성화** 메뉴를 마우스로 가리킵니다.

    b. **활성화**를 클릭합니다.

## <a name="configuring-proxy-settings"></a>프록시 설정 구성
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Internet Explorer에서 프록시 설정을 구성하려면

1. **Internet Explorer**를 시작합니다.

1. **도구** 메뉴에서 **인터넷 옵션**을 선택하여 **인터넷 옵션** 대화 상자를 엽니다.   
    
     ![인터넷 옵션](./media/zscaler-two-tutorial/ic769492.png "인터넷 옵션")

1. **연결** 탭을 클릭합니다.   
  
     ![연결](./media/zscaler-two-tutorial/ic769493.png "연결")

1. **LAN 설정**을 클릭하여 **LAN 설정** 대화 상자를 엽니다.

1. 프록시 서버 섹션에서 다음 단계를 수행합니다.   
   
    ![프록시 서버](./media/zscaler-two-tutorial/ic769494.png "프록시 서버")

    a. **사용자 LAN의 프록시 서버 사용**을 선택합니다.

    b. 주소 텍스트 상자에 **gateway.Zscaler Two.net**를 입력합니다.

    다. 포트 텍스트 상자에 **80**을 입력합니다.

    d. **로컬 주소의 바이패스 프록시 서버**를 선택합니다.

    e. **확인**을 클릭하여 **LAN(Local Area Network) 설정** 대화 상자를 닫습니다.

1. **확인**을 클릭하여 **인터넷 옵션** 대화 상자를 닫습니다.

### <a name="creating-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**를 차례로 선택하고 **모든 사용자**를 선택합니다.

    ![Azure AD 사용자 만들기][100]

2. 화면 위쪽에서 **새 사용자**를 선택합니다.

    ![Azure AD 테스트 사용자 만들기](common/create_aaduser_01.png) 

3. 사용자 속성에서 다음 단계를 수행합니다.

    ![Azure AD 테스트 사용자 만들기](common/create_aaduser_02.png)

    a. **이름** 필드에 **BrittaSimon**을 입력합니다.
  
    b. 에 **사용자 이름** 필드에 입력 **brittasimon\@yourcompanydomain.extension**  
    예를 들어 BrittaSimon@contoso.com

    다. **속성**을 선택하고 **암호 표시** 확인란을 선택한 다음, 암호 상자에 표시된 값을 적어 둡니다.

    d. **만들기**를 선택합니다.

### <a name="creating-a-zscaler-two-test-user"></a>Zscaler Two 테스트 사용자 만들기

이 섹션은 Zscaler Two에서 Britta Simon이라는 사용자를 만들기 위한 것입니다. Zscaler Two는 Just-In-Time 프로비전을 지원하며 기본적으로 사용하도록 설정합니다. 이 섹션에 작업 항목이 없습니다. 새 사용자가 아직 존재하지 않는 경우 Zscaler Two에 액세스하는 동안 만들어집니다.
>[!Note]
>사용자를 수동으로 만들어야 하는 경우  [Zscaler Two 지원 팀](https://www.zscaler.com/company/contact)에 문의하세요.

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Zscaler Two에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**을 선택합니다.

    ![사용자 할당][201]

2. 애플리케이션 목록에서 **Zscaler Two**를 선택합니다.

    ![Configure Single Sign-On](./media/zscaler-two-tutorial/tutorial_zscalertwo_app.png)

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    ![사용자 할당][202]

4. **할당 추가** 대화 상자에서 **추가** 단추를 클릭한 다음, **사용자 및 그룹**을 선택합니다.

    ![사용자 할당][203]

5. **사용자 및 그룹** 대화 상자의 목록에서 **Britta Simon** 등의 사용자를 선택한 다음, 화면 맨 아래에서 **선택** 단추를 클릭합니다.

    ![이미지](./media/zscaler-two-tutorial/tutorial_zscalertwo_users.png)

6. **역할 선택** 대화 상자의 목록에서 적절한 사용자 역할을 선택한 다음, 화면 맨 아래에서 **선택** 단추를 클릭합니다.

    ![이미지](./media/zscaler-two-tutorial/tutorial_zscalertwo_roles.png)

7. **할당 추가** 대화 상자에서 **할당** 단추를 선택합니다.

    ![이미지](./media/zscaler-two-tutorial/tutorial_zscalertwo_assign.png)

### <a name="testing-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Zscaler Two 타일을 클릭하면 Zscaler Two 애플리케이션에 자동으로 로그온됩니다.
액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](../user-help/active-directory-saas-access-panel-introduction.md)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](tutorial-list.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
