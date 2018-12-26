---
title: '자습서: Silverback과 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 Silverback 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 32cfc96f-2137-49ff-818b-67feadcd73b7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2018
ms.author: jeedes
ms.openlocfilehash: e7cb3049f680f81026e09388066001413922600a
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/18/2018
ms.locfileid: "46123856"
---
# <a name="tutorial-azure-active-directory-integration-with-silverback"></a>자습서: Silverback과 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Silverback을 통합하는 방법에 대해 알아봅니다.

Silverback과 Azure AD를 통합하면 다음과 같은 이점이 제공됩니다.

- Silverback에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정으로 Silverback에 자동으로 로그온(Single Sign-On)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](../manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

Silverback과 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- 활성 Silverback 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [1개월 평가판을 얻을](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다.  이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 Silverback 추가
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-silverback-from-the-gallery"></a>갤러리에서 Silverback 추가
Azure AD에 Silverback을 통합하도록 구성하려면 갤러리의 Silverback을 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Silverback을 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Azure Active Directory 단추][1]

2. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 응용 프로그램**으로 이동합니다.

    ![엔터프라이즈 응용 프로그램 블레이드][2]
    
3. 새 응용 프로그램을 추가하려면 대화 상자 맨 위 있는 **새 응용 프로그램** 단추를 클릭합니다.

    ![새 애플리케이션 단추][3]

4. 검색 상자에 **Silverback**을 입력하고, 결과 패널에서 **Silverback**를 선택한 다음, **추가** 단추를 클릭하여 응용 프로그램을 추가합니다.

    ![결과 목록의 Silverback](./media/silverback-tutorial/tutorial_silverback_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 Silverback에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 Silverback 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 Silverback의 관련 사용자 간에 연결이 형성되어야 합니다.

Silverback에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
3. **[Silverback 테스트 사용자 만들기](#create-a-silverback-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 Silverback에 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 Silverback 응용 프로그램에서 Single Sign-On을 구성합니다.

**Silverback에서 Azure AD Single Sign-on을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **Silverback** 응용 프로그램 통합 페이지에서 **Single Sign-On**을 클릭합니다.

    ![Single Sign-On 구성 링크][4]

2. **Single Sign-On** 대화 상자에서 **모드**를 **SAML 기반 로그온**으로 선택하여 Single Sign-On을 사용하도록 설정합니다.
 
    ![Single Sign-On 대화 상자](./media/silverback-tutorial/tutorial_silverback_samlbase.png)

3. **Silverback 도메인 및 URL** 섹션에서 다음 단계를 수행합니다.

    ![Silverback 도메인 및 URL Single Sign-On 정보](./media/silverback-tutorial/tutorial_silverback_url.png)

    a. **로그온 URL** 텍스트 상자에서 다음 패턴으로 URL을 입력합니다. `https://<YOURSILVERBACKURL>.com/ssp`

    b. **식별자** 텍스트 상자에서 `<YOURSILVERBACKURL>.com` 패턴을 사용하여 URL을 입력합니다.

    다. **회신 URL** 텍스트 상자에 다음 패턴으로 URL을 입력합니다.`https://<YOURSILVERBACKURL>.com/sts/authorize/login`

    > [!NOTE] 
    > 이러한 값은 실제 값이 아닙니다. 실제 로그온 URL, 식별자 및 회신 URL로 값을 업데이트합니다. 이러한 값을 가져오려면 [Silverback 클라이언트 지원 팀](mailto:helpdesk@matrix42.com)에 문의하세요. 

4. **SAML 서명 인증서** 섹션에서 복사 단추를 클릭하여 **앱 페더레이션 메타데이터 URL**을 복사하고 메모장에 붙여넣습니다.

    ![인증서 다운로드 링크](./media/silverback-tutorial/tutorial_silverback_certificate.png) 

5. **저장** 단추를 클릭합니다.

    ![Single Sign-On 구성 저장 단추](./media/silverback-tutorial/tutorial_general_400.png)

6.  관리자 권한으로 Silverback 서버에 로그인하고 다음 단계를 수행합니다.

    a.  **관리자** > **인증 공급자**로 이동합니다.

    b. **인증 공급자 설정** 페이지에서 다음 단계를 수행합니다.

    ![관리자 ](./media/silverback-tutorial/tutorial_silverback_admin.png)

    다.  **URL에서 가져오기**를 클릭합니다.
    
    d.  복사한 메타데이터 URL을 붙여넣고, **확인**을 클릭합니다.
    
    e.  **확인**을 사용하여 확인하면 값이 자동으로 채워집니다.
    
    f.  **로그인 페이지 표시**를 사용합니다.
    
    g.  권한이 부여된 Azure AD 사용자를 자동으로 추가하려는 경우 **동적 사용자 생성**을 사용합니다(선택 사항).
    
    h.  셀프 서비스 포털에 있는 단추에 대해 **제목**을 만듭니다.

    i.  **파일 선택**을 클릭하여 **아이콘**을 업로드합니다.
    
    j.  단추의 배경**색**을 선택합니다.
    
    k.  **저장**을 클릭합니다.

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

   ![Azure AD 테스트 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory** 단추를 클릭합니다.

    ![Azure Active Directory 단추](./media/silverback-tutorial/create_aaduser_01.png)

2. 사용자 목록을 표시하려면 **사용자 및 그룹**으로 이동한 후 **모든 사용자**를 클릭합니다.

    !["사용자 및 그룹" 및 "모든 사용자" 링크](./media/silverback-tutorial/create_aaduser_02.png)

3. **사용자** 대화 상자를 열려면 **모든 사용자** 대화 상자 위쪽에서 **추가**를 클릭합니다.

    ![추가 단추](./media/silverback-tutorial/create_aaduser_03.png)

4. **사용자** 대화 상자에서 다음 단계를 수행합니다.

    ![사용자 대화 상자](./media/silverback-tutorial/create_aaduser_04.png)

    a. **이름** 상자에 **BrittaSimon**을 입력합니다.

    나. **사용자 이름** 상자에 사용자인 Britta Simon의 전자 메일 주소를 입력합니다.

    다. **암호 표시** 확인란을 선택한 다음 **암호** 상자에 표시된 값을 적어둡니다.

    d. **만들기**를 클릭합니다.
 
### <a name="create-a-silverback-test-user"></a>Silverback 테스트 사용자 만들기

Azure AD 사용자가 Silverback에 로그인할 수 있도록 하려면 Silverback으로 프로비전되어야 합니다. Silverback의 경우 수동으로 프로비전합니다.

**사용자 계정을 프로비전하려면 다음 단계를 수행합니다.**

1. 관리자 권한으로 Silverback 서버에 로그인합니다.

2. **사용자**로 이동하고 **새 장치 사용자를 추가**합니다.

3. **기본** 페이지에서 다음 단계를 수행합니다.

    ![사용자 ](./media/silverback-tutorial/tutorial_silverback_user.png)

    a. **사용자 이름** 텍스트 상자에 사용자 이름(예: **Britta**)을 입력합니다.

    b. **이름** 텍스트 상자에 사용자의 이름(예: **Britta**)을 입력합니다.

    다. **성** 텍스트 상자에 사용자의 성(예: **Simon**)을 입력합니다.

    d. **이메일 주소** 텍스트 상자에 **Brittasimon@contoso.com**과 같은 사용자의 이메일을 입력합니다.

    e. **암호** 텍스트 상자에 암호를 입력합니다.
    
    f. **암호 확인** 텍스트 상자에 암호를 다시 입력하고 확인합니다.

    g. **저장**을 클릭합니다.

>[!NOTE]
>각 사용자를 수동으로 만들지 않으려는 경우 **관리자** > **인증 공급자** 아래에서 **동적 사용자 생성** 확인란을 선택합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Silverback에 대한 액세스 권한을 부여합니다.

![사용자 역할 할당][200] 

**Britta Simon을 Silverback에 할당하려면 다음 단계를 수행합니다.**

1. Azure Portal에서 애플리케이션 보기를 연 다음 디렉터리 보기로 이동하고 **엔터프라이즈 애플리케이션**으로 이동한 후 **모든 애플리케이션**을 클릭합니다.

    ![사용자 할당][201] 

2. 응용 프로그램 목록에서 **Silverback**을 선택합니다.

    ![응용 프로그램 목록의 Silverback 링크](./media/silverback-tutorial/tutorial_silverback_app.png)  

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    !["사용자 및 그룹" 링크][202]

4. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창][203]

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다.

6. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.
    
### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Silverback 타일을 클릭하면 Silverback 응용 프로그램에 자동으로 로그온됩니다.
액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](../active-directory-saas-access-panel-introduction.md)를 참조하세요. 

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/silverback-tutorial/tutorial_general_01.png
[2]: ./media/silverback-tutorial/tutorial_general_02.png
[3]: ./media/silverback-tutorial/tutorial_general_03.png
[4]: ./media/silverback-tutorial/tutorial_general_04.png

[100]: ./media/silverback-tutorial/tutorial_general_100.png

[200]: ./media/silverback-tutorial/tutorial_general_200.png
[201]: ./media/silverback-tutorial/tutorial_general_201.png
[202]: ./media/silverback-tutorial/tutorial_general_202.png
[203]: ./media/silverback-tutorial/tutorial_general_203.png

