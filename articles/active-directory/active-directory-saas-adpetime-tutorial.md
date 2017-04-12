---
title: "자습서: ADP eTime과 Azure Active Directory 통합 | Microsoft Docs"
description: "Azure Active Directory와 ADP eTime 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: a3e9f0be-19ed-4b99-a180-619e7624fc26
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2d8d925f80830a0d7047e9567fdd413af2e8c5c3
ms.openlocfilehash: 84c23a43b87c7357ed99f33c275717c68cf7c8bb
ms.lasthandoff: 02/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-adp-etime"></a>자습서: ADP eTime과 Azure Active Directory 통합
이 자습서에서는 ADP eTime과 Azure AD(Azure Active Directory)를 통합하는 방법을 보여 줍니다.

ADP eTime을 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

* ADP eTime에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
* 사용자가 해당 Azure AD 계정으로 ADP eTime SSO(Single Sign-on)에 자동으로 로그온되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure 클래식 포털에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On](active-directory-appssoaccess-whatis.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건
ADP eTime과의 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독
* ADP eTime SSO가 설정된 구독

>[!NOTE]
>이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다. 
> 

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

* 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 않도록 합니다.
* Azure AD 평가판 환경이 없으면 [1개월 평가판을 얻을](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서는 테스트 환경에서 Azure AD Single Sign-on을 테스트하는 데 도움을 주기 위해 제공되었습니다.

이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 ADP eTime 추가
2. Azure AD SSO 구성 및 테스트

## <a name="add-adp-etime-from-the-gallery"></a>갤러리에서 ADP eTime 추가
ADP eTime의 Azure AD 통합을 구성하려면 갤러리의 ADP eTime을 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 ADP eTime을 추가하려면 다음 단계를 수행합니다.**

1. **Azure 클래식 포털**의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다. 
   
    ![Active Directory][1]
2. **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.
3. 응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램** 을 클릭합니다.
   
    ![응용 프로그램][2]
4. 페이지 맨 아래에 있는 **추가** 를 클릭합니다.
   
    ![응용 프로그램][3]
5. **수행할 작업** 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.
   
    ![응용 프로그램][4]
6. 검색 상자에 **ADP eTime**을 입력합니다.
   
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_01.png)
7. 결과 창에서 **ADP eTime**을 선택한 다음 **완료**를 클릭하여 응용 프로그램을 추가합니다.
   
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_06.png)

## <a name="configure-and-test-azure-ad-sso"></a>Azure AD SSO 구성 및 테스트
이 섹션은 "Britta Simon"이라는 테스트 사용자를 기반으로 ADP eTime에서 Azure AD SSO를 구성하고 테스트하는 방법을 보여 주기 위해 작성되었습니다.

SSO가 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 ADP eTime 사용자가 누군지 알고 있어야 합니다. 즉, Azure AD 사용자와 ADP eTime의 관련 사용자 간에 연결이 형성되어야 합니다.  

이 연결 관계는 Azure AD의 **사용자 이름** 값을 ADP eTime의 **Username** 값으로 할당하여 설정합니다.

ADP eTime에서 Azure AD SSO를 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configuring-azure-ad-single-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#creating-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
3. **[ADP eTime 테스트 사용자 만들기](#creating-a-adpetime-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 ADP eTime에 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assigning-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Single Sign-On 테스트](#testing-single-sign-on)** - 구성이 작동하는지 확인합니다.

### <a name="configuring-azure-ad-sso"></a>Azure AD SSO 구성
이 섹션은 Azure 클래식 포털에서 Azure AD Single Sign-on을 사용하도록 설정하고 ADP eTime 응용 프로그램에서 Single Sign-On을 구성하는 방법을 설명하기 위한 것입니다.

ADP eTime 응용 프로그램은 특정 서식에서 SAML 어설션을 예상하며 이는 SAML 토큰 특성 구성에 사용자 할당 특성 매핑을 추가합니다. 다음 스크린샷은 이에 대한 예제를 보여 줍니다. 클레임 이름은 항상 **"PersonImmutableID"** 및 사용자의 EmployeeID를 포함하는 ExtensionAttribute2에 매핑한 값입니다. 

여기에서 Azure AD에서 ADP eTime에 매핑하는 사용자는 EmployeeID에서 수행되지만 응용 프로그램 설정에 따라 다른 값에 이를 매핑할 수 있습니다. 따라서 사용자의 올바른 식별자를 사용하고 해당 값을 **"PersonImmutableID"** 클레임과 매핑하려면 ADP eTime 팀과 먼저 작업해 보세요.  

![Single Sign-On 구성](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_02.png) 

SAML 어설션을 구성하기 전에 ADP eTime 지원 팀에 문의하고 테넌트에 대한 고유 식별자 특성 값을 요청합니다. 응용 프로그램에 대한 사용자 지정 클레임을 구성하려면 이 값이 필요합니다.

**ADP eTime에서 Azure AD Single Sign-on을 구성하려면 다음 단계를 수행합니다.**

1. Azure 클래식 포털의 **ADP eTime** 응용 프로그램 통합 페이지에서 **Single Sign-On 구성**을 클릭하여 **Single Sign-On 구성** 대화 상자를 엽니다.
   
    ![Single Sign-on 구성][6] 
2. **ADP eTime에 대한 사용자 로그온 방법 선택** 페이지에서 **Azure AD Single Sign-on**을 선택한 후 **다음**을 클릭합니다.
   
    ![Single Sign-on 구성](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_03.png) 
3. **앱 설정 구성** 대화 상자 페이지에서 다음 단계를 수행합니다.
   
    ![Single Sign-On 구성](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_04.png) 
  1. **회신 URL** 텍스트 상자에서 `https://<server name>.adp.com/affwebservices/public/saml2assertionconsumer` 패턴을 사용하여 사용자가 ADP eTime 응용 프로그램에 로그인하는 데 사용할 URL을 입력합니다.
  2. **다음**을 클릭합니다.
4. **ADP eTime에서 Single Sign-On 구성** 페이지에서 다음 단계를 수행합니다.
   
    ![Single Sign-On 구성](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_05.png)  
  1. **메타데이터 다운로드**를 클릭하고 파일을 컴퓨터에 저장합니다. 
  2. **다음**을 클릭합니다.
2. 응용 프로그램에 대해 구성된 SSO를 가져오려면 ADP eTime 지원 팀에 다운로드한 메타데이터 파일을 첨부하여 메일로 보냅니다. 그러면 SSO 통합에 대해 구성할 수 있습니다.
   
   >[!NOTE]
   >**ADP eTime** 팀이 인스턴스를 구성한 후 **RelayState** 값을 가져옵니다. 아래에서 언급하는 단계에 따라 인스턴스를 구성합니다. 이 구성을 완료한 후에 통합을 테스트할 수 있습니다. 따라서 이는 작업할 응용 프로그램 통합에 대한 중요한 구성임을 유의하세요.
   >  
6. Azure AD에서 RelayState 값을 구성하려면 다음 단계를 수행합니다. 
  1. [Azure 관리 포털](https://portal.azure.com)에 관리자 권한으로 로그인합니다.
  2. 왼쪽 탐색 창에서 **더 많은 서비스**를 클릭합니다.  

    ![Single Sign-On 구성](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_07.png) 
 3. **검색** 텍스트 상자에서 **Azure Active Directory**를 입력한 다음 관련 링크를 클릭합니다. 

    ![Single Sign-on 구성](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_08.png)   
 4. **엔터프라이즈 응용 프로그램**을 클릭합니다. 

    ![Single Sign-on 구성](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_09.png)  
 5. **관리** 섹션에서 **모든 응용 프로그램**을 클릭합니다.
 
    ![Single Sign-On 구성](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_10.png) 
 6. **검색** 텍스트 상자에서 **ADP eTime**을 입력한 다음 관련 링크를 클릭합니다. 
   
    ![Single Sign-on 구성](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_11.png)  
 7. **관리** 섹션에서 **Single Sign-On**을 클릭합니다. 
 
    ![Single Sign-On 구성](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_12.png) 
 8. **고급 URL 설정 표시**를 선택합니다.
 
     ![Single Sign-on 구성](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_13.png) 
 9. **RelayState** 텍스트 상자에서 다음 패턴을 사용하여 값을 입력한 다음 설정을 저장합니다. 
 
    * 프로덕션 환경: `https://fed.adp.com/saml/fedlanding.html?<id>` 
    * 스테이징 환경: `https://fed-stag.adp.com/saml/fedlanding.html?PORTAL`
     
    ![Single Sign-on 구성](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_14.png) 

4. Azure 클래식 포털에서 Single Sign-On 구성 확인을 선택하고 **다음**을 클릭합니다.
 
 ![Azure AD Single Sign-On][10]
5. **Single Sign-On 확인** 페이지에서 **완료**를 클릭합니다.  
   
 ![Azure AD Single Sign-On][11]

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기
이 섹션의 목적은 Azure 클래식 포털에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.  

* 사용자 목록에서 **Britta Simon**을 선택합니다.

![Azure AD 사용자 만들기][20]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. **Azure 클래식 포털**의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.
   
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-adpetime-tutorial/create_aaduser_09.png) 
2. **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.
3. 사용자 목록을 표시하려면 위쪽 메뉴에서 **사용자**를 클릭합니다.
   
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-adpetime-tutorial/create_aaduser_03.png) 
4. **사용자 추가** 대화 상자를 열려면 아래쪽 도구 모음에서 **사용자 추가**를 클릭합니다.
   
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-adpetime-tutorial/create_aaduser_04.png) 
5. **이 사용자에 대한 정보 입력** 대화 상자 페이지에서 다음 단계를 수행합니다.
   
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-adpetime-tutorial/create_aaduser_05.png)  
 1. **사용자 유형**에서 **조직의 새 사용자**를 선택합니다.
 2. 사용자 이름 **텍스트 상자**에 **BrittaSimon**을 입력합니다. 
 3. **다음**을 클릭합니다.
6. **사용자 프로필** 대화 상자 페이지에서 다음 단계를 수행합니다.
   
   ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-adpetime-tutorial/create_aaduser_06.png)  
 1. **이름** 텍스트 상자에 **Britta**를 입력합니다.   
 2. **성** 텍스트 상자에 **Simon**을 입력합니다. 
 3. **표시 이름** 텍스트 상자에 **Britta Simon**을 입력합니다. 
 4. **역할** 목록에서 **사용자**를 선택합니다. 
 5. **다음**을 클릭합니다.
7. **임시 암호 가져오기** 대화 상자 페이지에서 **만들기**를 클릭합니다.
   
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-adpetime-tutorial/create_aaduser_07.png) 
8. **임시 암호 가져오기** 대화 상자 페이지에서 다음 단계를 수행합니다.
   
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-adpetime-tutorial/create_aaduser_08.png)  
 1. **새 암호**값을 적어둡니다.
 2. 페이지 맨 아래에 있는 **완료**을 참조하세요.   

### <a name="create-a-adp-etime-test-user"></a>ADP eTime 테스트 사용자 만들기
이 섹션은 ADP eTime에서 Britta Simon이라는 사용자를 만들기 위한 것입니다. ADP eTime 계정에 사용자를 추가하려면 ADP eTime 지원 팀에 문의하세요. 

>[!NOTE]
>사용자를 수동으로 만들어야 하는 경우 ADP eTime 지원 팀에 문의해야 합니다.
> 

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당
이 섹션은 Britta Simon에게 ADP eTime에 대한 액세스 권한을 부여하여 Azure SSO를 사용할 수 있도록 하기 위한 것입니다.

![사용자 할당][200] 

**Britta Simon을 ADP eTime에 할당하려면 다음 단계를 수행합니다.**

1. Azure 클래식 포털에서 응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램** 을 클릭합니다.
   
    ![사용자 할당][201] 
2. 응용 프로그램 목록에서 **ADP eTime**을 선택합니다.
   
    ![Single Sign-On 구성](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_50.png) 
3. 위쪽의 메뉴에서 **사용자**를 클릭합니다.
   
    ![사용자 할당][203] 
4. 사용자 목록에서 **Britta Simon**을 선택합니다.
5. 아래쪽 도구 모음에서 **할당**을 클릭합니다.
   
    ![사용자 할당][205]

### <a name="test-single-sign-on"></a>Single Sign-On 테스트
이 섹션은 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트하기 위한 것입니다.

액세스 패널에서 ADP eTime 타일을 클릭하면 ADP eTime 응용 프로그램에 자동으로 로그온됩니다.

## <a name="additional-resources"></a>추가 리소스
* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On이란 무엇입니까?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_205.png

