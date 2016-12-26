---
title: "자습서: Image Relay와 Azure Active Directory 통합 | Microsoft 문서"
description: "Azure Active Directory와 Image Relay 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 65bb5990-07ef-4244-9f41-cd28fc2cb5a2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/21/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 4f7a99e82c73d446c928a821f80fc640a1994d67
ms.openlocfilehash: cfcb97cbf5b1d2beff5c868ce8371918dbba4e3e


---
# <a name="tutorial-azure-active-directory-integration-with-image-relay"></a>자습서: Image Relay와 Azure Active Directory 통합
이 자습서에서는 Image Relay와 Azure AD(Azure Active Directory)를 통합하는 방법을 보여 줍니다.  
Image Relay와 Azure AD를 통합하면 다음과 같은 이점이 제공됩니다.

* Azure AD에서는 Image Relay에 대한 액세스 권한이 있는 사용자를 제어할 수 있습니다.
* 사용자가 Azure AD 계정으로 Image Relay에 자동으로 로그인(Single Sign-on)할 수 있도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure 클래식 포털에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On](active-directory-appssoaccess-whatis.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건
Image Relay와 Azure AD를 통합하도록 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독
* Image Relay Single Sign-On 사용이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.
> 
> 

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

* 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 않도록 합니다.
* Azure AD 평가판 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 얻을 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서는 테스트 환경에서 Azure AD Single Sign-on을 테스트하는 데 도움을 주기 위해 제공되었습니다.  
이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 Image Relay 추가
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-image-relay-from-the-gallery"></a>갤러리에서 Image Relay 추가
Azure AD에 Image Relay를 통합하도록 구성하려면 갤러리의 Image Relay를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Image Relay를 추가하려면 다음 단계를 수행합니다.**

1. Azure 클래식 포털의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다. 
   
    ![Active Directory][1]
2. **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.
3. 응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램** 을 클릭합니다.
   
    ![응용 프로그램][2]
4. 페이지 맨 아래에 있는 **추가** 를 클릭합니다.
   
    ![응용 프로그램][3]
5. **수행할 작업** 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.
   
    ![응용 프로그램][4]
6. 검색 상자에서 **Image Relay**를 입력합니다.
   
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_01.png)
7. 결과 창에서 **Image Relay**를 선택한 다음 **완료**를 클릭하여 응용 프로그램을 추가합니다.
   
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD Single Sign-on 구성 및 테스트
이 섹션은 "Britta Simon"이라는 테스트 사용자를 기반으로 ImageRelay에서 Azure AD Single Sign-On을 구성하고 테스트하는 방법을 보여 주기 위해 작성되었습니다.

Single Sign-On이 작동하려면 Azure AD에서 Image Relay의 관련 사용자를 나타내는 사용자 계정이 필요합니다.  즉 Azure AD 사용자와 Image Relay의 관련 사용자 간에 연결 관계가 설정되어야 합니다.  
이 연결 관계는 Azure AD의 **사용자 이름** 값을 Image Relay의 **Username** 값으로 할당하여 설정합니다.

Image Relay에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#creating-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-on 테스트하는 데 사용합니다.
3. **[Image Relay 테스트 사용자 만들기](#creating-a-userecho-test-user)** - Britta Simon의 Azure AD 표현과 연결되는 대응 사용자를 Image Relay에 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assigning-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Single Sign-On 테스트](#testing-single-sign-on)** - 구성이 작동하는지 확인합니다.

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성
이 섹션에서는 Azure 클래식 포털에서 Azure AD Single Sign-On을 사용하도록 설정하고 Image Relay 응용 프로그램에서 Single Sign-On을 구성합니다.

**Image Relay에서 Azure AD Single Sign-on을 구성하려면 다음 단계를 수행합니다.**

1. Azure 클래식 포털의 **Image Relay** 응용 프로그램 통합 페이지에서 **Single Sign-On 구성**을 클릭하여 **Single Sign-On 구성** 대화 상자를 엽니다.
   
     ![Single Sign-on 구성][6] 
2. **사용자가 Image Relay에 로그인하는 방법을 선택하십시오.** 페이지에서 **Azure AD Single Sign-On**을 선택하고 **다음**을 클릭합니다.
   
    ![Single Sign-on 구성](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_03.png) 
3. **앱 설정 구성** 대화 상자 페이지에서 다음 단계를 수행합니다.
   
     ![Single Sign-On 구성](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_04.png) 
   
    a. **로그인 URL** 텍스트 상자에서 사용자가 Image Relay 응용 프로그램에 로그인하는 데 사용하는 URL을 입력합니다(예: *https://fabrikam.ImageRelay.com/*).
   
    b. **다음**을 클릭합니다.
4. **Image Relay에서 Single Sign-On 구성** 페이지에서 다음 단계를 수행합니다.
   
    ![Single Sign-on 구성](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_05.png) 
   
    a. **인증서 다운로드**를 클릭하고 파일을 컴퓨터에 저장합니다.
   
    b. **다음**을 클릭합니다.
5. 다른 브라우저 창에서 Image Relay 회사 사이트에 관리자로 로그인합니다.
6. 위쪽의 도구 모음에서 **사용자 및 권한** 워크로드를 클릭합니다.
   
    ![Single Sign-on 구성](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_06.png) 
7. **새 사용 권한 만들기**를 클릭합니다.
   
    ![Single Sign-on 구성](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_08.png) 
8. **Single Sign-On 설정** 워크로드에서 **이 그룹은 Single Sign On을 통해서만 로그인 가능** 확인란을 선택한 다음 **저장**을 클릭합니다.
   
    ![Single Sign-on 구성](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_09.png) 
9. **계정 설정**으로 이동합니다.
   
    ![Single Sign-On 구성](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_10.png) 
10. **Single Sign On 설정** 작업으로 이동합니다.
    
     ![Single Sign-On 구성](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_11.png)
11. **SAML 설정** 대화 상자에서 다음 단계를 수행합니다.
    
    ![Single Sign-On 구성](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_12.png)
    
    a. Azure 클래식 포털에서 **Single Sign-On 서비스 URL**을 복사한 다음 **로그인 URL** 텍스트 상자에 붙여넣습니다.

    b. Azure 클래식 포털에서 **Single Sign-Out 서비스 URL**을 복사한 다음 **로그아웃 URL** 텍스트 상자에 붙여넣습니다.

    c. **이름 ID 형식**으로 **urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress**를 선택합니다.

    d. **서비스 공급자(Image Relay) 요청에 대한 바인딩 옵션**으로 **POST 바인딩**을 선택합니다.

    e. **x.509 인증서**에서 **인증서 업데이트**를 클릭합니다.

    ![Single Sign-on 구성](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_17.png)

    f. 다운로드한 인증서를 메모장에서 열고, 내용을 복사한 다음 전체 인증서를 x.509 인증서 텍스트 상자에 붙여 넣습니다.

    ![Single Sign-On 구성](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_18.png)

    g. **Just-In-Time 사용자 프로비전** 섹션에서 **Just-In-Time 사용자 프로비전 사용**을 선택합니다.

    ![Single Sign-on 구성](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_19.png)

    h. Single Sign-On을 통해서만 로그인할 수 있는 사용 권한 그룹(예: **SSO 기본**)을 선택합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_20.png)

    i. **Save**를 클릭합니다.

1. Azure 클래식 포털에서 Single Sign-On 구성 확인을 선택하고 **다음**을 클릭합니다.
   
    ![Azure AD Single Sign-On][10]
2. **Single Sign-On 확인** 페이지에서 **완료**를 클릭합니다.
   
    ![Azure AD Single Sign-On][11]

### <a name="creating-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기
이 섹션의 목적은 Azure 클래식 포털에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

![Azure AD 사용자 만들기][20]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. **Azure 클래식 포털**의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.
   
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_09.png) 
2. **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.
3. 사용자 목록을 표시하려면 위쪽 메뉴에서 **사용자**를 클릭합니다.
   
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_03.png) 
4. **사용자 추가** 대화 상자를 열려면 아래쪽 도구 모음에서 **사용자 추가**를 클릭합니다.
   
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_04.png) 
5. **이 사용자에 대한 정보 입력** 대화 상자 페이지에서 다음 단계를 수행합니다.
   
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_05.png) 
   
    a. 사용자 유형에서 조직의 새 사용자를 선택합니다.
   
    b. 사용자 이름 **텍스트 상자**에 **BrittaSimon**을 입력합니다.
   
    c. **다음**을 클릭합니다.
6. **사용자 프로필** 대화 상자 페이지에서 다음 단계를 수행합니다.
   
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_06.png) 
   
    a. **이름** 텍스트 상자에 **Britta**를 입력합니다.  
   
    b. **성** 텍스트 상자에 **Simon**을 입력합니다.
   
    c. **표시 이름** 텍스트 상자에 **Britta Simon**을 입력합니다.
   
    d. **역할** 목록에서 **사용자**를 선택합니다.
   
    e. **다음**을 클릭합니다.

7. **임시 암호 가져오기** 대화 상자 페이지에서 **만들기**를 클릭합니다.
   
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_07.png) 
8. **임시 암호 가져오기** 대화 상자 페이지에서 다음 단계를 수행합니다.
   
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_08.png) 
   
    a. **새 암호**값을 적어둡니다.
   
    b. **완료**를 클릭합니다.   

### <a name="creating-an-image-relay-test-user"></a>Image Relay 테스트 사용자 만들기
이 섹션에서는 Image Relay에서 Britta Simon이라는 사용자를 만듭니다.

**Image Relay에서 Britta Simon이라는 사용자를 만들려면 다음 단계를 수행합니다.**

1. Image Relay 회사 사이트에 관리자로 로그인합니다.
2. **사용자 및 권한**으로 이동하여 **SSO 사용자 만들기**를 선택합니다.
   
    ![Single Sign-On 구성](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_21.png) 
3. 프로비전하려는 사용자의 **전자 메일**, **이름**, **성** 및 **회사**를 입력하고 Single Sign-On을 통해서만 로그인할 수 있는 그룹인 권한 그룹(예: SSO 기본)을 선택합니다.
   
    ![Single Sign-on 구성](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_22.png) 
4. **만들기**를 클릭합니다.

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당
이 섹션에서는 Single Sign-On을 사용할 수 있도록 Britta Simon에게 Image Relay에 대한 액세스 권한을 부여합니다.

![사용자 할당][200] 

**Britta Simon을 Image Relay에 할당하려면 다음 단계를 수행합니다.**

1. Azure 클래식 포털에서 응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램** 을 클릭합니다.
   
    ![사용자 할당][201] 
2. 응용 프로그램 목록에서 **Image Relay**를 선택합니다.
   
    ![Single Sign-on 구성](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_23.png) 
3. 위쪽의 메뉴에서 **사용자**를 클릭합니다.
   
    ![사용자 할당][203] 
4. 사용자 목록에서 **Britta Simon**을 선택합니다.
5. 아래쪽 도구 모음에서 **할당**을 클릭합니다.
   
    ![사용자 할당][205]

### <a name="testing-single-sign-on"></a>Single Sign-On 테스트
이 섹션은 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트하기 위한 것입니다.  
[액세스 패널]에서 [Image Relay] 타일을 클릭하면 Image Relay 응용 프로그램에 자동으로 로그온됩니다.

## <a name="additional-resources"></a>추가 리소스
* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On이란 무엇입니까?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_205.png



<!--HONumber=Nov16_HO4-->


