---
title: '자습서: 4me와 Azure Active Directory 통합 | Microsoft 문서'
description: Azure Active Directory와 4me 간에 Single Sign-On을 구성하는 방법을 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 983eecc6-41f8-49b7-b7f6-dcf833dde121
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/03/2018
ms.author: jeedes
ms.openlocfilehash: c33edf13a8bcafd4a6c3d4885553fc856ec941d8
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/26/2018
ms.locfileid: "50158128"
---
# <a name="tutorial-azure-active-directory-integration-with-4me"></a>자습서: 4me와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 4me를 통합하는 방법에 대해 알아봅니다.

4me와 Azure AD를 통합하면 다음과 같은 이점이 제공됩니다.

- 4me에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정으로 4me에 자동으로 로그온(Single Sign-On)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](../manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

4me와 Azure AD의 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- 4me Single Sign-On을 사용하도록 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [1개월 평가판을 얻을](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다.  이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 4me 추가
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-4me-from-the-gallery"></a>갤러리에서 4me 추가
Azure AD에 4me를 통합하도록 구성하려면 갤러리의 4me를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 4me를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Azure Active Directory 단추][1]

2. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 응용 프로그램**으로 이동합니다.

    ![엔터프라이즈 응용 프로그램 블레이드][2]
    
3. 새 응용 프로그램을 추가하려면 대화 상자 맨 위 있는 **새 응용 프로그램** 단추를 클릭합니다.

    ![새 애플리케이션 단추][3]

4. 검색 상자에서 **4me**를 입력하고, 결과 패널에서 **4me**를 선택한 다음, **추가** 단추를 클릭하여 응용 프로그램을 추가합니다.

    ![결과 목록의 4me](./media/4me-tutorial/tutorial_4me_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 하여 4me에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 4me 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 4me의 관련 사용자 간에 연결 관계를 설정해야 합니다.

4me에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
3. **[4me 테스트 사용자 만들기](#create-a-4me-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 4me에 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고, 4me 응용 프로그램에서 Single Sign-On을 구성합니다.

**4me에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **4me** 응용 프로그램 통합 페이지에서 **Single Sign-On**을 클릭합니다.

    ![Single Sign-On 구성 링크][4]

2. **Single Sign-On** 대화 상자에서 **모드**를 **SAML 기반 로그온**으로 선택하여 Single Sign-On을 사용하도록 설정합니다.
 
    ![Single Sign-On 대화 상자](./media/4me-tutorial/tutorial_4me_samlbase.png)

3. **4me 도메인 및 URL** 섹션에서 다음 단계를 수행합니다.

    ![4me 도메인 및 URL Single Sign-On 정보](./media/4me-tutorial/tutorial_4me_url.png)

    a. **로그온 URL** 텍스트 상자에서 다음 패턴으로 URL을 입력합니다.

    | Environment| URL|
    |---|---|
    | 프로덕션 | `https://<SUBDOMAIN>.4me.com`|
    | QA| `https://<SUBDOMAIN>.4me.qa`|
  
    b. **식별자** 텍스트 상자에서 다음 패턴을 사용하여 URL을 입력합니다.
    
    | Environment| URL|
    |---|---|
    | 프로덕션 | `https://<SUBDOMAIN>.4me.com`|
    | QA| `https://<SUBDOMAIN>.4me.qa`|

    > [!NOTE] 
    > 이러한 값은 실제 값이 아닙니다. 실제 로그온 URL 및 식별자로 값을 업데이트합니다. 이러한 값을 얻으려면 [4me 클라이언트 지원 팀](mailto:support@4me.com)에 문의하세요. 
 
4. 4me 응용 프로그램에 특정 형식의 SAML 어설션이 필요합니다. 이 애플리케이션에 대해 다음 클레임을 구성합니다. 애플리케이션 통합 페이지의 **사용자 특성** 섹션에서 이러한 특성의 값을 관리할 수 있습니다. 다음 스크린샷은 이에 대한 예제를 보여 줍니다.
    
    ![Configure Single Sign-On](./media/4me-tutorial/tutorial_4me_attribute.png)

5. **Single sign-on** 대화 상자의 **사용자 특성** 섹션에서 위의 이미지에 표시된 것과 같이 SAML 토큰 특성을 구성하고 다음 단계를 수행합니다.
    
    | 특성 이름 | 특성 값 |
    | ---------------| --------------- |    
    | first_name | user.givenname |
    | last_name | user.surname |

    a. **특성 추가**를 클릭하여 **특성 추가** 대화 상자를 엽니다.

    ![Configure Single Sign-On](./media/4me-tutorial/tutorial_attribute_04.png)

    ![Configure Single Sign-On](./media/4me-tutorial/tutorial_attribute_05.png)
    
    b. **이름** 텍스트 상자에서 해당 행에 표시된 특성 이름을 입력합니다.
    
    다. **값** 목록에서 해당 행에 대해 표시된 특성을 입력합니다.

    d. **네임스페이스**를 비워 둡니다.
    
    d. **확인**을 클릭합니다.

6. **SAML 서명 인증서** 섹션에서 컴퓨터에 대한 **지문** 값을 복사합니다.

    ![인증서 다운로드 링크](./media/4me-tutorial/tutorial_4me_certificate.png) 

7. **저장** 단추를 클릭합니다.

    ![Single Sign-On 구성 저장 단추](./media/4me-tutorial/tutorial_general_400.png)

8. **4me 구성** 섹션에서 **4me 구성**을 클릭하여 **로그온 구성** 창을 엽니다. **빠른 참조 섹션**에서 **로그아웃 URL 및 SAML Single Sign-On 서비스 URL**을 복사합니다.

    ![4me 구성](./media/4me-tutorial/tutorial_4me_configure.png) 

9. 다른 웹 브라우저 창에서 4me에 관리자 권한으로 로그인합니다.

10. 왼쪽 위에서 **설정** 로고를 클릭하고, 왼쪽 사이드바에서 **Single Sign-On**을 클릭합니다.

    ![4me 설정](./media/4me-tutorial/tutorial_4me_settings.png)

11. **Single Sign-On** 페이지에서 다음 단계를 수행합니다.

    ![4me Single Sign-On](./media/4me-tutorial/tutorial_4me_singlesignon.png)

    a. **사용** 옵션을 선택합니다.

    b. Azure Portal에서 복사한 **로그아웃 URL** 값을 **원격 로그아웃 URL** 텍스트 상자에 붙여넣습니다.

    다. Azure Portal에서 복사한 **SAML Single Sign-On 서비스 URL** 값을 **SAML** 섹션 아래의 **SAML SSO URL** 텍스트 상자에 붙여넣습니다.

    d. Azure Portal에서 복사한, 이중 입자(duplet) 순서의 콜론으로 구분된 **지문** 값(AA:BB:CC:DD:EE:FF:GG:HH:II)을 **인증서 지문** 텍스트 상자에 붙여넣습니다.

    e. **저장**을 클릭합니다.

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

   ![Azure AD 테스트 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory** 단추를 클릭합니다.

    ![Azure Active Directory 단추](./media/4me-tutorial/create_aaduser_01.png)

2. 사용자 목록을 표시하려면 **사용자 및 그룹**으로 이동한 후 **모든 사용자**를 클릭합니다.

    !["사용자 및 그룹" 및 "모든 사용자" 링크](./media/4me-tutorial/create_aaduser_02.png)

3. **사용자** 대화 상자를 열려면 **모든 사용자** 대화 상자 위쪽에서 **추가**를 클릭합니다.

    ![추가 단추](./media/4me-tutorial/create_aaduser_03.png)

4. **사용자** 대화 상자에서 다음 단계를 수행합니다.

    ![사용자 대화 상자](./media/4me-tutorial/create_aaduser_04.png)

    a. **이름** 상자에 **BrittaSimon**을 입력합니다.

    나. **사용자 이름** 상자에 사용자인 Britta Simon의 전자 메일 주소를 입력합니다.

    다. **암호 표시** 확인란을 선택한 다음 **암호** 상자에 표시된 값을 적어둡니다.

    d. **만들기**를 클릭합니다.
 
### <a name="create-a-4me-test-user"></a>4me 테스트 사용자 만들기

이 섹션은 4me에서 Britta Simon이라는 사용자를 만들기 위한 것입니다. 4me는 기본적으로 사용하도록 설정되는 JIT(Just-In-Time) 프로비전을 지원합니다. 이 섹션에 작업 항목이 없습니다. 새 사용자가 아직 없는 경우 4me에 액세스하는 동안 만들어집니다.

>[!Note]
>사용자를 수동으로 만들어야 하는 경우  [4me 지원 팀](mailto:support@4me.com)에 문의하세요.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 4me에 대한 액세스 권한을 부여합니다.

![사용자 역할 할당][200] 

**Britta Simon을 4me에 할당하려면 다음 단계를 수행합니다.**

1. Azure Portal에서 애플리케이션 보기를 연 다음 디렉터리 보기로 이동하고 **엔터프라이즈 애플리케이션**으로 이동한 후 **모든 애플리케이션**을 클릭합니다.

    ![사용자 할당][201] 

2. 응용 프로그램 목록에서 **4me**를 선택합니다.

    ![응용 프로그램 목록의 4me 링크](./media/4me-tutorial/tutorial_4me_app.png)  

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    !["사용자 및 그룹" 링크][202]

4. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창][203]

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다.

6. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.
    
### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 4me 타일을 클릭하면 4me 응용 프로그램에 자동으로 로그온됩니다.
액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](../active-directory-saas-access-panel-introduction.md)를 참조하세요. 

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/4me-tutorial/tutorial_general_01.png
[2]: ./media/4me-tutorial/tutorial_general_02.png
[3]: ./media/4me-tutorial/tutorial_general_03.png
[4]: ./media/4me-tutorial/tutorial_general_04.png

[100]: ./media/4me-tutorial/tutorial_general_100.png

[200]: ./media/4me-tutorial/tutorial_general_200.png
[201]: ./media/4me-tutorial/tutorial_general_201.png
[202]: ./media/4me-tutorial/tutorial_general_202.png
[203]: ./media/4me-tutorial/tutorial_general_203.png

