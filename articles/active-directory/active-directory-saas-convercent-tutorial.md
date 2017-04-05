---
title: "자습서: Convercent와 Azure Active Directory 통합 | Microsoft Docs"
description: "Azure Active Directory와 Convercent 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: f9c9d290-0e13-490b-b559-0be772d6a690
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: 187fe8af432d2521e3b9efa59b788280c32692a9
ms.lasthandoff: 03/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-convercent"></a>자습서: Convercent와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Convercent를 통합하는 방법에 대해 알아봅니다.

Convercent를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- Convercent에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정으로 Convercent SSO(Single Sign-On)에 자동으로 로그온되도록 설정할 수 있습니다.
- 단일 중앙 위치인 새로운 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On](active-directory-appssoaccess-whatis.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건

Convercent와의 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- Convercent SSO가 설정된 구독

>[!NOTE]
>이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.
>

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 않도록 합니다.
- Azure AD 평가판 환경이 없으면 [1개월 평가판을 얻을](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다.


## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD SSO를 테스트합니다. 

이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 Convercent 추가
2. Azure AD SSO 구성 및 테스트

## <a name="add-convercent-from-the-gallery"></a>갤러리에서 Convercent 추가
Convercent의 Azure AD 통합을 구성하려면 갤러리의 Convercent를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Convercent를 추가하려면 다음 단계를 수행합니다.**

1. **Azure 관리 포털**의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Active Directory][1]

2. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 응용 프로그램**으로 이동합니다.

    ![응용 프로그램][2]
    
3. 대화 상자 위쪽에 있는 **추가** 단추를 클릭합니다.

    ![응용 프로그램][3]

4. 검색 상자에 **Convercent**를 입력합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-convercent-tutorial/tutorial_convercent_001.png)

5. 결과 창에서 **Convercent**를 선택하고 **추가** 단추를 클릭하여 응용 프로그램을 추가합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-convercent-tutorial/tutorial_convercent_0001.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트
이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 Convercent에서 Azure AD SSO를 구성하고 테스트합니다.

SSO가 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 Convercent 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 Convercent의 관련 사용자 간에 연결이 형성되어야 합니다.

이 연결 관계는 Azure AD의 **사용자 이름** 값을 Convercent의 **Username** 값으로 할당하여 설정합니다.

Convercent에서 Azure AD SSO를 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configuring-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#creating-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
3. **[Convercent 테스트 사용자 만들기](#creating-a-works-mobile-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 Convercent에 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assigning-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Single Sign-On 테스트](#testing-single-sign-on)** - 구성이 작동하는지 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 새로운 Azure Portal에서 Azure AD SSO를 사용하도록 설정하고 Convercent 응용 프로그램에서 Single Sign-On을 구성합니다.

**Convercent에서 Azure AD Single Sign-on을 구성하려면 다음 단계를 수행합니다.**

1. 새로운 Azure Portal의 **Convercent** 응용 프로그램 통합 페이지에서 **Single sign-on**을 클릭합니다.

    ![Single Sign-On 구성][4]

2. **Single sign on** 대화 상자 페이지에서 **모드**로 **SAML 기반 로그온**을 선택하여 Single Sign-On을 사용하도록 설정합니다.
 
    ![Single Sign-on 구성](./media/active-directory-saas-convercent-tutorial/tutorial_convercent_01.png)

3. **Convercent 도메인 및 URL** 섹션에서 **IDP 시작 모드**로 응용 프로그램을 구성하려는 경우 다음 단계를 수행합니다.

    ![Single Sign-on 구성](./media/active-directory-saas-convercent-tutorial/tutorial_convercent_02.png)
  1. **식별자** 텍스트 상자에 `https://sts.convercent.com/`를 입력합니다.
  2. **“고급 URL 설정 표시”**를 클릭합니다.
  3. **릴레이 상태** 텍스트 상자에 다음을 입력합니다. `https://app.convercent.com/`
    
4. **SP 시작 모드**에서 응용 프로그램을 구성하려는 경우 **Convercent 도메인 및 URL** 섹션에서 다음 단계를 수행합니다.
    
    ![Single Sign-on 구성](./media/active-directory-saas-convercent-tutorial/tutorial_convercent_03.png)
  * **로그온 URL** 텍스트 상자에 `https://app.convercent.com/`를 입력합니다.
    >[!NOTE] 
    >여기에서 지정된 고유 식별자를 사용할 것을 제안합니다. 이 값을 얻으려면 [Convercent 지원 팀](mailTo:support@convercent.com)에 문의하세요.
    >

5. **Convercent 구성** 섹션에서 **Convercent 구성**을 클릭하여 **로그온 구성** 대화 상자를 엽니다. **SAML XML 메타데이터**를 클릭한 후, 컴퓨터에 메타데이터 파일을 저장합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-convercent-tutorial/tutorial_convercent_04.png) 

    ![Single Sign-on 구성](./media/active-directory-saas-convercent-tutorial/tutorial_convercent_05.png)

6. 응용 프로그램에 대해 SSO를 구성하려면 [Convercent 지원 팀](mailTo:support@convercent.com)에 문의하고 다운로드한 **메타데이터**를 제공합니다.

7. 새로운 Azure Portal에서 **저장** 단추를 클릭합니다.  
  
### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기
이 섹션의 목적은 새로운 포털에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

![Azure AD 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. **Azure 관리 포털**의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-convercent-tutorial/create_aaduser_01.png) 

2. **사용자 및 그룹**으로 이동한 후 **모든 사용자**를 클릭하여 사용자 목록을 표시합니다.
    
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-convercent-tutorial/create_aaduser_02.png) 

3. 대화 상자 위쪽에서 **추가**를 클릭하여 **사용자** 대화 상자를 엽니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-convercent-tutorial/create_aaduser_03.png) 

4. **사용자** 대화 상자 페이지에서 다음 단계를 수행합니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-convercent-tutorial/create_aaduser_04.png) 
 1. **이름** 텍스트 상자에 **BrittaSimon**을 입력합니다.
 2. **사용자 이름** 텍스트 상자에 BrittaSimon의 **전자 메일 주소**를 입력합니다.
 3. **암호 표시**를 선택하고 **암호** 값을 적어둡니다.
 4. **만들기**를 클릭합니다. 

### <a name="create-a-convercent-test-user"></a>Convercent 테스트 사용자 만들기

이 섹션에서는 Convercent에서 Britta Simon이라는 사용자를 만듭니다. Convercent 플랫폼에서 사용자를 추가하려면 [Convercent 지원 팀](emailto:support@convercent.com)에 문의하세요.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure SSO를 사용할 수 있도록 Britta Simon에게 Convercent에 대한 액세스 권한을 부여합니다.

![사용자 할당][200] 

**Britta Simon을 Convercent에 할당하려면 다음 단계를 수행합니다.**

1. Azure 관리 포털에서 응용 프로그램 보기를 열고 디렉터리 보기로 이동하고 **엔터프라이즈 응용 프로그램**으로 이동한 후 **모든 응용 프로그램**을 클릭합니다.

    ![사용자 할당][201] 

2. 응용 프로그램 목록에서 **Convercent**를 선택합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-convercent-tutorial/tutorial_convercent_50.png) 

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    ![사용자 할당][202] 

4. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 할당][203]

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다.

6. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.
    
### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD SSO 구성을 테스트합니다.

액세스 패널에서 Convercent 타일을 클릭하면 Convercent 응용 프로그램에 자동으로 로그온됩니다.


## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On이란 무엇입니까?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-convercent-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-convercent-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-convercent-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-convercent-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-convercent-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-convercent-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-convercent-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-convercent-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-convercent-tutorial/tutorial_general_203.png
