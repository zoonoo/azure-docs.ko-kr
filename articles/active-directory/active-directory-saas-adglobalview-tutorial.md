---
title: "자습서: ADP GlobalView와 Azure Active Directory 통합 | Microsoft Docs"
description: "Azure Active Directory와 ADP GlobalView 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: ffb6464f-714d-41a9-869a-2b7e5ae9f125
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 0f767b96fa8f8f9d1a22709633bee64f0477ffb3
ms.openlocfilehash: 46fbe3233af22605d4cd89227b91313128e523f9
ms.lasthandoff: 03/01/2017


---
# <a name="tutorial-azure-active-directory-integration-with-adp-globalview"></a>자습서: ADP GlobalView와 Azure Active Directory 통합
이 자습서에서는 ADP GlobalView와 Azure AD(Azure Active Directory)를 통합하는 방법을 보여 줍니다.  

ADP GlobalView를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

* ADP GlobalView에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
* 사용자가 해당 Azure AD 계정으로 ADP GlobalView SSO(Single Sign-on)에 자동으로 로그온되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure 클래식 포털에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On](active-directory-appssoaccess-whatis.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건
ADP GlobalView와의 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독
* ADP GlobalView SSO가 설정된 구독

>[!NOTE]
>이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다. 
> 

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

* 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 않도록 합니다.
* Azure AD 평가판 환경이 없으면 [1개월 평가판을 얻을](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서는 테스트 환경에서 Azure AD SSO를 테스트하는 데 도움을 주기 위해 제공되었습니다.  

이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 ADP GlobalView 추가
2. Azure AD SSO 구성 및 테스트

## <a name="add-adp-globalview-from-the-gallery"></a>갤러리에서 ADP GlobalView 추가
ADP GlobalView의 Azure AD 통합을 구성하려면 갤러리의 ADP GlobalView를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 ADP GlobalView를 추가하려면 다음 단계를 수행합니다.**

1. **Azure 클래식 포털**의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다. 
   
    ![Active Directory][1]
2. **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.
3. 응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램** 을 클릭합니다.
   
    ![응용 프로그램][2]
4. 페이지 맨 아래에 있는 **추가** 를 클릭합니다.
   
    ![응용 프로그램][3]
5. **수행할 작업** 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.
   
    ![응용 프로그램][4]
6. 검색 상자에 **ADP GlobalView**를 입력합니다.
   
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_01.png)
7. 결과 창에서 **ADP GlobalView**를 선택한 다음 **완료**를 클릭하여 응용 프로그램을 추가합니다.
   
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_06.png)

## <a name="configure-and-test-azure-ad-sso"></a>Azure AD SSO 구성 및 테스트
이 섹션은 "Britta Simon"이라는 테스트 사용자를 기반으로 ADP GlobalView에서 Azure AD SSO를 구성하고 테스트하는 방법을 보여 주기 위해 작성되었습니다.

SSO가 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 ADP GlobalView 사용자가 누군지 알고 있어야 합니다. 즉, Azure AD 사용자와 ADP GlobalView의 관련 사용자 간에 연결이 형성되어야 합니다.  

이 연결 관계는 Azure AD의 **사용자 이름** 값을 ADP GlobalView의 **Username** 값으로 할당하여 설정합니다.

ADP GlobalView에서 Azure AD SSO를 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configuring-azure-ad-single-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#creating-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
3. **[ADP GlobalView 테스트 사용자 만들기](#creating-a-adp-globalview-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 ADP GlobalView에 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assigning-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Single Sign-On 테스트](#testing-single-sign-on)** - 구성이 작동하는지 확인합니다.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성
이 섹션은 Azure 클래식 포털에서 Azure AD SSO를 사용하도록 설정하고 ADP GlobalView 응용 프로그램에서 SSO를 구성하는 방법을 설명하기 위한 것입니다.

ADP GlobalView 응용 프로그램은 특정 서식에서 SAML 어설션을 예상하며 이는 SAML 토큰 특성 구성에 사용자 할당 특성 매핑을 추가합니다. 

다음 스크린샷은 이에 대한 예제를 보여 줍니다. 클레임 이름은 항상 **"PersonImmutableID"** 및 사용자의 EmployeeID를 포함하는 ExtensionAttribute2에 매핑한 값입니다. 여기에서 Azure AD에서 ADP GlobalView에 매핑하는 사용자는 EmployeeID에서 수행되지만 응용 프로그램 설정에 따라 다른 값에 이를 매핑할 수 있습니다. 

사용자의 올바른 식별자를 사용하고 해당 값을 **"PersonImmutableID"** 클레임과 매핑하기 위해 ADP GlobalView 팀과 먼저 작업할 수 있습니다. 그림에 나와 있는 것처럼 전자 메일과 UserID 클레임을 매핑할 수도 있습니다.

![Single Sign-On 구성](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_02.png) 

SAML 어설션을 구성하기 전에 ADP GlobalView 지원 팀에 문의하고 테넌트에 대한 고유 식별자 특성 값을 요청합니다. 응용 프로그램에 대한 사용자 지정 클레임을 구성하려면 이 값이 필요합니다.

**ADP GlobalView에서 Azure AD SSO를 구성하려면 다음 단계를 수행합니다.**

1. Azure 클래식 포털의 **ADP GlobalView** 응용 프로그램 통합 페이지에서 **Single Sign-On 구성**을 클릭하여 **Single Sign-On 구성** 대화 상자를 엽니다.
   
    ![Single Sign-On 구성][6] 
2. **ADP GlobalView에 대한 사용자 로그온 방법 선택** 페이지에서 **Azure AD Single Sign-On**을 선택한 후 **다음**을 클릭합니다.
   
    ![Single Sign-On 구성](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_03.png) 
3. **앱 설정 구성** 대화 상자 페이지에서 다음 단계를 수행합니다.
   
    ![Single Sign-on 구성](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_04.png) 
  1. **식별자** 텍스트 상자에서 `https://<server name>.globalview.adp.com/federate2` 또는 `https://<server name>.globalview.adp.com/federate` 패턴 중 하나를 사용하여 ADP GlobalView 응용 프로그램을 식별하는 데 사용할 URL을 입력합니다.
  2. **회신 URL** 텍스트 상자에서 `https://<server name>.globalview.adp.com/federate2/sp/ACS.saml2` 또는 `https://<server name>.globalview.adp.com/federate/sp/ACS.saml2` 패턴 중 하나를 사용하여 ADP GlobalView 응용 프로그램에 대한 응답을 게시하기 위해 Azure AD에서 사용할 URL을 입력합니다.
  3. **다음**을 클릭합니다.
4. **ADP GlobalView에서 Single Sign-On 구성** 페이지에서 다음 단계를 수행합니다.
   
    ![Single Sign-on 구성](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_05.png)   
  1. **인증서 다운로드**를 클릭하고 파일을 컴퓨터에 저장합니다.
  2. **다음**을 클릭합니다.
5. 응용 프로그램에 대해 구성된 SSO를 얻으려면 ADP GlobalView 지원 팀에 문의하고 다음을 제공하십시오. 
   
   * 다운로드한 **인증서**
   * **엔터티 ID**
   * **SAML SSO URL**
   * **Single Sign-Out Service URL**

    >[!NOTE]
    >**ADP GlobalView** 팀이 인스턴스를 구성한 후 **RelayState** 값을 가져옵니다. 아래에서 언급하는 단계에 따라 인스턴스를 구성합니다. 이 구성을 완료한 후에 통합을 테스트할 수 있습니다. 따라서 이는 작업할 응용 프로그램 통합에 대한 중요한 구성임을 유의하세요.
    >

6. Azure AD에서 RelayState 값을 구성하려면 다음 단계를 수행합니다.   
 1. [Azure 관리 포털](https://portal.azure.com)에 관리자 권한으로 로그인합니다.
 2. 왼쪽 탐색 창에서 **더 많은 서비스**를 클릭합니다. 
   
    ![Single Sign-on 구성](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_07.png)  
 3. **검색** 텍스트 상자에서 **Azure Active Directory**를 입력한 다음 관련 링크를 클릭합니다.
   
    ![Single Sign-On 구성](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_08.png)  
 4. **엔터프라이즈 응용 프로그램**을 클릭합니다.
   
    ![Single Sign-on 구성](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_09.png) 
 5. **관리** 섹션에서 **모든 응용 프로그램**을 클릭합니다.
   
    ![Single Sign-on 구성](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_10.png) 
 6. **검색** 텍스트 상자에서 **ADP eTime**을 입력한 다음 관련 링크를 클릭합니다. 
   
    ![Single Sign-on 구성](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_11.png) 
 7. **관리** 섹션에서 **Single Sign-On**을 클릭합니다.
   
    ![Single Sign-on 구성](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_12.png)  
 8. **고급 URL 설정 표시**를 선택합니다.
   
    ![Single Sign-on 구성](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_13.png)
 9. **릴레이 상태** 텍스트 상자에서 다음 패턴을 사용하여 값을 입력합니다.
   
    `https://<server name>.globalview.adp.com/gvolution/session/<instance name>/sso` 
   
    ![Single Sign-on 구성](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_14.png)  
 10. 설정을 저장합니다.
7. Azure 클래식 포털에서 Single Sign-On 구성 확인을 선택하고 **다음**을 클릭합니다.
   
    ![Azure AD Single Sign-On][10]
8. **Single Sign-On 확인** 페이지에서 **완료**를 클릭합니다.  
   
    ![Azure AD Single Sign-On][11]

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기
이 섹션의 목적은 Azure 클래식 포털에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.  

* 사용자 목록에서 **Britta Simon**을 선택합니다.

![Azure AD 사용자 만들기][20]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. **Azure 클래식 포털**의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.
   
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-adpglobalview-tutorial/create_aaduser_09.png) 
2. **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.
3. 사용자 목록을 표시하려면 위쪽 메뉴에서 **사용자**를 클릭합니다.
   
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-adpglobalview-tutorial/create_aaduser_03.png) 
4. **사용자 추가** 대화 상자를 열려면 아래쪽 도구 모음에서 **사용자 추가**를 클릭합니다.
   
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-adpglobalview-tutorial/create_aaduser_04.png) 
5. **이 사용자에 대한 정보 입력** 대화 상자 페이지에서 다음 단계를 수행합니다.
   
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-adpglobalview-tutorial/create_aaduser_05.png)  
 1. 사용자 유형에서 조직의 새 사용자를 선택합니다.  
 2. 사용자 이름 **텍스트 상자**에 **BrittaSimon**을 입력합니다. 
 3. **다음**을 클릭합니다.
6. **사용자 프로필** 대화 상자 페이지에서 다음 단계를 수행합니다.
   
   ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-adpglobalview-tutorial/create_aaduser_06.png)  
 1. **이름** 텍스트 상자에 **Britta**를 입력합니다.    
 2. **성** 텍스트 상자에 **Simon**을 입력합니다. 
 3. **표시 이름** 텍스트 상자에 **Britta Simon**을 입력합니다. 
 4. **역할** 목록에서 **사용자**를 선택합니다. 
 5. **다음**을 클릭합니다.
7. **임시 암호 가져오기** 대화 상자 페이지에서 **만들기**를 클릭합니다.
   
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-adpglobalview-tutorial/create_aaduser_07.png) 
8. **임시 암호 가져오기** 대화 상자 페이지에서 다음 단계를 수행합니다.
   
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-adpglobalview-tutorial/create_aaduser_08.png)  
 1. **새 암호**값을 적어둡니다.  
 2. 페이지 맨 아래에 있는 **완료**을 참조하세요.   

### <a name="create-a-adp-globalview-test-user"></a>ADP GlobalView 테스트 사용자 만들기
이 섹션은 ADP GlobalView에서 Britta Simon이라는 사용자를 만들기 위한 것입니다. ADP GlobalView 계정에 사용자를 추가하려면 ADP GlobalView 지원 팀에 문의하세요. 

>[!NOTE]
>사용자를 수동으로 만들어야 하는 경우 ADP GlobalView 지원 팀에 문의해야 합니다.
> 

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당
이 섹션은 Britta Simon에게 ADP GlobalView에 대한 액세스 권한을 부여하여 Azure SSO를 사용할 수 있도록 하기 위한 것입니다.

![사용자 할당][200] 

**Britta Simon을 ADP GlobalView에 할당하려면 다음 단계를 수행합니다.**

1. Azure 클래식 포털에서 응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램** 을 클릭합니다.
   
    ![사용자 할당][201] 
2. 응용 프로그램 목록에서 **ADP GlobalView**를 선택합니다.
   
    ![Single Sign-On 구성](./media/active-directory-saas-adpglobalview-tutorial/tutorial_adpglobalview_50.png) 
3. 위쪽의 메뉴에서 **사용자**를 클릭합니다.
   
    ![사용자 할당][203] 
4. 사용자 목록에서 **Britta Simon**을 선택합니다.
5. 아래쪽 도구 모음에서 **할당**을 클릭합니다.
   
    ![사용자 할당][205]

### <a name="test-single-sign-on"></a>Single Sign-On 테스트
이 섹션은 액세스 패널을 사용하여 Azure AD SSO 구성을 테스트하기 위한 것입니다.  

액세스 패널에서 ADP GlobalView 타일을 클릭하면 ADP GlobalView 응용 프로그램에 자동으로 로그온됩니다.

## <a name="additional-resources"></a>추가 리소스
* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On이란 무엇입니까?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-adpglobalview-tutorial/tutorial_general_205.png

