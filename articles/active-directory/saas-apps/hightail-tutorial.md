---
title: '자습서: Hightail과 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 Hightail 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: e15206ac-74b0-46e4-9329-892c7d242ec0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/15/2018
ms.author: jeedes
ms.openlocfilehash: 1151044d5c1002c808ae1214086aff5fad84a55e
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39431337"
---
# <a name="tutorial-azure-active-directory-integration-with-hightail"></a>자습서:Azure Active Directory와 Hightail 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Hightail을 통합하는 방법에 대해 알아봅니다.

Hightail을 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- Hightail에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정으로 Hightail에 자동으로 로그온(Single Sign-on)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 응용 프로그램 액세스 및 Single Sign-On이란 무엇인가요?](../manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

Hightail과 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- Hightail Single Sign-On이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 얻을 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다.  이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 Hightail 추가
1. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-hightail-from-the-gallery"></a>갤러리에서 Hightail 추가
Hightail과 Azure AD 통합을 구성하려면 갤러리의 Hightail을 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Hightail을 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Active Directory][1]

1. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 응용 프로그램**으로 이동합니다.

    ![애플리케이션][2]
    
1. 새 응용 프로그램을 추가하려면 대화 상자 맨 위 있는 **새 응용 프로그램** 단추를 클릭합니다.

    ![애플리케이션][3]

1. 검색 상자에 **Hightail**을 입력합니다.

    ![Azure AD 테스트 사용자 만들기](./media/hightail-tutorial/tutorial_hightail_search.png)

1. 결과 패널에서 **Hightail**을 선택하고 **추가** 단추를 클릭하여 응용 프로그램을 추가합니다.

    ![Azure AD 테스트 사용자 만들기](./media/hightail-tutorial/tutorial_hightail_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD Single Sign-on 구성 및 테스트
이 섹션에서는 “Britta Simon”이라는 테스트 사용자를 기반으로 Hightail에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 Hightail 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자 및 Hightail의 관련 사용자 간에 연결이 설정되어야 합니다.

Hightail에서 Azure AD의 **사용자 이름** 값을 **Username** 값으로 할당하여 링크 관계를 설정합니다.

Hightail에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
1. **[Azure AD 테스트 사용자 만들기](#creating-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On 테스트하는 데 사용합니다.
1. **[Hightail 테스트 사용자 만들기](#creating-a-hightail-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 Hightail에 만듭니다.
1. **[Azure AD 테스트 사용자 할당](#assigning-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
1. **[Single Sign-On 테스트](#testing-single-sign-on)** - 구성이 작동하는지 확인합니다.

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 Hightail 응용 프로그램에서 Single Sign-On을 구성합니다.

**Hightail에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **Hightail** 응용 프로그램 통합 페이지에서 **Single Sign-On**을 클릭합니다.

    ![Configure Single Sign-On][4]

1. **Single Sign-On** 대화 상자에서 **모드**를 **SAML 기반 로그온**으로 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Configure Single Sign-On](./media/hightail-tutorial/tutorial_hightail_samlbase.png)

1. **IDP** 시작 모드에서 응용 프로그램을 구성하려면 **Hightail 도메인 및 URL** 섹션에서 다음 단계를 수행합니다.

    ![Configure Single Sign-On](./media/hightail-tutorial/tutorial_hightail_url.png)

    **회신 URL** 텍스트 상자에서 URL `https://www.hightail.com/samlLogin?phi_action=app/samlLogin&subAction=handleSamlResponse`를 입력합니다.

    > [!NOTE]
    > 회신 URL 값은 실제 값이 아닙니다. 이 회신 URL 값은 자습서 뒷부분에서 설명하는 실제 회신 URL로 업데이트하게 됩니다.

1. **SP** 시작 모드에서 응용 프로그램을 구성하려면 **고급 URL 설정 표시**를 확인하고 다음 단계를 수행합니다.

    ![Configure Single Sign-On](./media/hightail-tutorial/tutorial_hightail_url1.png)

    **로그온 URL** 텍스트 상자에서 URL `https://www.hightail.com/loginSSO`를 입력합니다.

1. **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 를 클릭한 후 컴퓨터에 인증서 파일을 저장합니다.

    ![Configure Single Sign-On](./media/hightail-tutorial/tutorial_hightail_certificate.png) 

1. Hightail 응용 프로그램은 특정 형식의 SAML 어설션이 필요합니다. 이 응용 프로그램에 대한 다음 클레임을 구성하세요. 이러한 특성의 값은 응용 프로그램의 **"특성"** 탭에서 관리할 수 있습니다. 다음 스크린샷은 이에 대한 예제를 보여 줍니다. 

    ![Configure Single Sign-On](./media/hightail-tutorial/tutorial_hightail_attribute.png) 

1. **Single Sign-On** 대화 상자의 **사용자 특성** 섹션에서 이미지에 표시된 것과 같이 SAML 토큰 특성을 구성하고 다음 단계를 수행합니다.
    
    | 특성 이름 | 특성 값 |
    | ------------------- | -------------------- |
    | FirstName | user.givenname |
    | LastName | user.surname |
    | Email | user.mail |    
    | UserIdentity | user.mail |
    
    a. **특성 추가**를 클릭하여 **특성 추가** 대화 상자를 엽니다.

    ![Configure Single Sign-On](./media/hightail-tutorial/tutorial_officespace_04.png)

    ![Configure Single Sign-On](./media/hightail-tutorial/tutorial_officespace_05.png)

    나. **이름** 텍스트 상자에서 해당 행에 표시된 특성 이름을 입력합니다.

    다. **값** 목록에서 해당 행에 대해 표시된 특성을 입력합니다.

    d. **네임스페이스**를 비워 둡니다.

    e. **Ok**를 클릭합니다.

1. **저장** 단추를 클릭합니다.

    ![Configure Single Sign-On](./media/hightail-tutorial/tutorial_general_400.png)

1. **Hightail 구성** 섹션에서 **Hightail 구성**을 클릭하여 **로그인 구성** 창을 엽니다. **빠른 참조 섹션**에서 **SAML Single Sign-On 서비스 URL**을 복사합니다.

    ![Configure Single Sign-On](./media/hightail-tutorial/tutorial_hightail_configure.png)

    >[!NOTE]
    >Hightail 앱에서 Single Sign On을 구성하기 전에 이 도메인을 사용하는 모든 사용자가 Single Sign On 기능을 활용할 수 있도록 Hightail 팀을 통해 전자 메일 도메인을 허용 목록에 포함시키세요.

1. 다른 브라우저 창에서 **Hightail** 관리 포털을 엽니다.

1. 페이지의 오른쪽 위 모서리에서 **사용자 아이콘**을 클릭합니다. 

    ![Configure Single Sign-On](./media/hightail-tutorial/configure1.png)

1. **View Admin Console**(관리 콘솔 보기) 탭을 클릭합니다.

    ![Configure Single Sign-On](./media/hightail-tutorial/configure2.png)

1. 위쪽의 메뉴에서 **SAML** 탭을 클릭하고 다음 단계를 수행합니다.

    ![Configure Single Sign-On](./media/hightail-tutorial/configure3.png)

    a. Azure Portal에서 복사한 **SAML Single Sign-On 서비스 URL** 값을 **Login URL**(로그인 URL) 텍스트 상자에 붙여넣습니다.

    나. Azure Portal에서 다운로드한 base-64로 인코딩된 인증서를 메모장에서 열고, 클립보드에 내용을 복사한 다음, **SAML Certificate**(SAML 인증서) 텍스트 상자에 붙여넣습니다.

    다. **COPY**(복사)를 클릭하여 인스턴스에 대한 SAML 소비자 URL을 복사하고, Azure Portal에서 **Hightail 도메인 및 URL** 섹션의 **회신 URL** 텍스트 상자에 붙여넣습니다.

    d. **Save Configurations**(구성 저장)를 클릭합니다.

### <a name="creating-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기
이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

![Azure AD 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. **Azure Portal**의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure AD 테스트 사용자 만들기](./media/hightail-tutorial/create_aaduser_01.png) 

1. 사용자 목록을 표시하려면 **사용자 및 그룹**으로 이동한 후 **모든 사용자**를 클릭합니다.
    
    ![Azure AD 테스트 사용자 만들기](./media/hightail-tutorial/create_aaduser_02.png) 

1. **사용자** 대화 상자를 열려면 대화 상자 위쪽에서 **추가**를 클릭합니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/hightail-tutorial/create_aaduser_03.png) 

1. **사용자** 대화 상자 페이지에서 다음 단계를 수행합니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/hightail-tutorial/create_aaduser_04.png) 

    a. **이름** 텍스트 상자에 **BrittaSimon**을 입력합니다.

    나. **사용자 이름** 텍스트 상자에 BrittaSimon의 **전자 메일 주소**를 입력합니다.

    다. **암호 표시**를 선택하고 **암호** 값을 적어둡니다.

    d. **만들기**를 클릭합니다.
 
### <a name="creating-a-hightail-test-user"></a>Hightail 테스트 사용자 만들기

이 섹션은 Hightail에서 Britta Simon이라는 사용자를 만들기 위한 것입니다. 

이 섹션에 작업 항목이 없습니다. Hightail은 사용자 지정 클레임을 기반으로 하는 Just-In-Time 사용자 프로비전을 지원합니다. 위의 **[Azure AD Single Sign-On 구성](#configuring-azure-ad-single-sign-on)** 섹션에 나와 있는 것처럼 사용자 지정 클레임을 구성한 경우 사용자가 없으면 응용 프로그램에 자동으로 만들어집니다. 

>[!NOTE]
>사용자를 수동으로 만들어야 하는 경우 [Hightail 지원 팀](mailto:support@hightail.com)에 문의해야 합니다. 

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Hightail에 대한 액세스 권한을 부여합니다.

![사용자 할당][200] 

**Britta Simon을 Hightail에 할당하려면 다음 단계를 수행합니다.**

1. Azure Portal에서 응용 프로그램 보기를 연 다음 디렉터리 보기로 이동하고 **엔터프라이즈 응용 프로그램**으로 이동한 후 **모든 응용 프로그램**을 클릭합니다.

    ![사용자 할당][201] 

1. 응용 프로그램 목록에서 **Hightail**을 선택합니다.

    ![Configure Single Sign-On](./media/hightail-tutorial/tutorial_hightail_app.png) 

1. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    ![사용자 할당][202]

1. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 할당][203]

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다.

1. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다.

1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

### <a name="testing-single-sign-on"></a>Single Sign-On 테스트

이 섹션은 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트하기 위한 것입니다.

액세스 패널에서 Hightail 타일을 클릭하면 Hightail 응용 프로그램에 자동으로 로그온됩니다.


## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/hightail-tutorial/tutorial_general_01.png
[2]: ./media/hightail-tutorial/tutorial_general_02.png
[3]: ./media/hightail-tutorial/tutorial_general_03.png
[4]: ./media/hightail-tutorial/tutorial_general_04.png

[100]: ./media/hightail-tutorial/tutorial_general_100.png

[200]: ./media/hightail-tutorial/tutorial_general_200.png
[201]: ./media/hightail-tutorial/tutorial_general_201.png
[202]: ./media/hightail-tutorial/tutorial_general_202.png
[203]: ./media/hightail-tutorial/tutorial_general_203.png

