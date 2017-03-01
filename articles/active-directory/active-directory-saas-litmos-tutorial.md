---
title: "자습서: Litmos와 Azure Active Directory 통합 | Microsoft Docs"
description: "Azure Active Directory와 Litmos 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: cfaae4bb-e8e5-41d1-ac88-8cc369653036
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: ed2fc2b34ff10acc806daec84986f8db58e713c3
ms.openlocfilehash: 84cd0c3eb2753a209d0aebda405f0b98a487140d
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-litmos"></a>자습서: Litmos와 Azure Active Directory 통합
이 자습서에서는 Litmos와 Azure AD(Azure Active Directory)를 통합하는 방법을 보여 줍니다.  

Litmos를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다. 

* Litmos에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다. 
* 사용자가 해당 Azure AD 계정으로 Litmos SSO(Single Sign-on)에 자동으로 로그온되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Active Directory에서 계정을 관리할 수 있습니다. 

Azure AD와의 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On](active-directory-appssoaccess-whatis.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건
Litmos와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독
* Litmos Single Sign-on이 설정된 구독

>[!NOTE]
>이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.
> 

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

* 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 않도록 합니다.
* Azure AD 평가판 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서&1;개월 평가판을 얻을 수 있습니다. 

## <a name="scenario-description"></a>시나리오 설명
이 자습서는 테스트 환경에서 Azure AD Single Sign-on을 테스트하는 데 도움을 주기 위해 제공되었습니다.  

이 자습서에 설명된 시나리오는 다음 세 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 Litmos 추가 
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="add-litmos-from-the-gallery"></a>갤러리에서 Litmos 추가
Litmos의 Azure AD 통합을 구성하려면 갤러리의 Litmos를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Litmos를 추가하려면 다음 단계를 수행합니다.**

1. **Azure 클래식 포털**의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다. 
   
    ![Active Directory][1]
2. **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.
3. 응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램** 을 클릭합니다.
   
    ![응용 프로그램][2]
4. 페이지 맨 아래에 있는 **추가** 를 클릭합니다.
   
    ![응용 프로그램][3]
5. **수행할 작업** 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.
   
    ![응용 프로그램][4]
6. 검색 상자에서 **Litmos**를 입력합니다.
   
    ![응용 프로그램][5]
7. 결과 창에서 **Litmos**를 선택하고 **완료**를 클릭하여 응용 프로그램을 추가합니다.
   
    ![응용 프로그램][500]

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD Single Sign-on 구성 및 테스트
이 섹션은 "Britta Simon"이라는 테스트 사용자를 기반으로 Litmos에서 Azure AD Single Sign-On을 구성하고 테스트하는 방법을 보여 주기 위해 작성되었습니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 Litmos 사용자가 누군지 알고 있어야 합니다. 즉, Azure AD 사용자와 Litmos의 관련 사용자 간에 연결이 형성되어야 합니다.  

이 연결 관계는 Azure AD의 **사용자 이름** 값을 Litmos의 **Username** 값으로 할당하여 설정합니다.

Litmos에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configuring-azure-ad-single-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#creating-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
3. **[Litmos 테스트 사용자 만들기](#creating-a-halogen-software-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 Litmos에 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assigning-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Single Sign-on 테스트](#testing-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성
이 섹션은 Azure AD 클래식 포털에서 Azure AD Single Sign-On을 사용하도록 설정하고 Litmos 응용 프로그램에서 Single Sign-On을 구성하는 방법을 설명하기 위한 것입니다.  

이 절차의 일부로 base-64로 인코딩된 인증서 파일을 만들어야 합니다.  

이 절차를 잘 모르는 경우 [이진 인증서를 텍스트 파일로 변환하는 방법](http://youtu.be/PlgrzUZ-Y1o)을 참조하십시오.

구성의 일부로 Litmos 응용 프로그램에 대한 **SAML 토큰 특성** 을 사용자 지정해야 합니다.  

![Azure AD Single Sign-On][17] 

**Litmos에서 Azure AD Single Sign-on을 구성하려면 다음 단계를 수행합니다.**

1. Azure AD 클래식 포털의 **Litmos** 응용 프로그램 통합 페이지에서 **Single Sign-On 구성**을 클릭하여 **Single Sign-On 구성** 대화 상자를 엽니다.
   
    ![Single Sign-On 구성][6] 
2. **Litmos에 대한 사용자 로그온 방법 선택** 페이지에서 **Azure AD Single Sign-On**을 선택하고 **다음**을 클릭합니다.
   
    ![Azure AD Single Sign-On][7] 
3. 관리자 권한으로 Litmos 회사 사이트(예: *https://azureapptest.litmos.com/account/Login*)에 로그온합니다.
   
    ![Azure AD Single Sign-On][21] 
4. 왼쪽의 탐색 모음에서 **계정**을 클릭합니다.
   
    ![Azure AD Single Sign-On][22] 
5. **통합** 탭을 클릭합니다.
   
    ![Azure AD Single Sign-On][23] 
6. **통합** 탭에서 **3rd 파티 통합**에 아래로 스크롤한 다음 **SAML 2.0** 탭을 클릭합니다.
   
    ![Azure AD Single Sign-On][24] 
7. **litmos에 대한 SAML 끝점은 다음과 같습니다.**에 있는 값을 복사합니다.
   
    ![Azure AD Single Sign-On][26] 
8. Azure 클래식 포털의 **앱 설정 구성** 대화 상자 페이지에서 다음 단계를 수행합니다.
   
    ![Azure AD Single Sign-On][8] 
   
    1. **식별자** 텍스트 상자에 사용자가 Litmos 응용 프로그램에 로그인하는 데 사용하는 URL을 입력합니다(예: *https://azureapptest.litmos.com/account/Login*).
   
    2. **회신 URL** 텍스트 상자에 이전 단계의 Litmos 응용 프로그램에서 복사한 값을 붙여넣습니다.
   
    3. **다음**을 클릭합니다.
9. **Litmos의 Single Sign-On 구성** 페이지에서 다음 단계를 수행합니다.
   
    ![Azure AD Single Sign-On][2] 
   
    * 인증서 다운로드를 클릭하고 파일을 컴퓨터에 저장합니다.
10. **Litmos** 응용 프로그램에서 다음 단계를 수행합니다.
    
     ![Azure AD Single Sign-On][25] 
    
     1. **SAML 사용**을 클릭합니다.
    
     2. 다운로드한 인증서에서 **Base-64로 인코딩된** 파일을 만듭니다.  
    >[!TIP]
    >자세한 내용은 [이진 인증서를 텍스트 파일로 변환하는 방법](http://youtu.be/PlgrzUZ-Y1o)을 참조하세요.
     >

     3. Base&64;로 인코딩된 인증서를 메모장에서 열고, 내용을 클립보드에 복사한 다음 전체 인증서를 **SAML X.509 인증서** 텍스트 상자에 붙여넣습니다.
    
     4. **변경 내용 저장**을 클릭합니다.
11. Azure AD 클래식 포털에서 Single Sign-On을 구성했음을 확인한다는 확인란을 선택하고 **다음**을 클릭합니다. 
    
     ![Azure AD Single Sign-On][10]
12. **Single Sign-On 확인** 페이지에서 **완료**를 클릭합니다.  
    
     ![Azure AD Single Sign-On][11]
13. 위쪽 메뉴에서 **특성** to open the **SAML Token 특성** 대화 상자를 엽니다. 
    
    ![Single Sign-on 구성][12]
14. **사용자 특성 추가** 대화 상자에서 다음 단계를 수행합니다. 
    
    ![Single Sign-On 구성][14]
    
    | 특성 이름 | 특성 값 |
    | --- | --- |
    | Email |user.mail |
    | FirstName |user.givenname |
    | Lastname |user.surname |
    
    위의 테이블에서 각 데이터 행에 대해 다음 단계를 수행합니다.
    
 1. **사용자 특성 추가**를 클릭합니다.    
   ![Single Sign-On 구성][15]
 2. **특성 이름** 텍스트 상자에서 해당 행에 표시된 **특성 이름**을 입력합니다.
 3. 해당 행에 표시된 **특성 값** 을 선택합니다.
 4. **완료**를 클릭하여 **사용자 특성 추가** 대화 상자를 닫습니다.


1. **변경 내용 적용**을 클릭합니다. 
   
   ![Single Sign-On 구성][16]

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기
이 섹션의 목적은 Azure 클래식 포털에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.  

![Azure AD 사용자 만들기][20]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. **Azure 클래식 포털**의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.
   
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-litmos-tutorial/create_aaduser_09.png)  
2. **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.
3. 사용자 목록을 표시하려면 위쪽 메뉴에서 **사용자**를 클릭합니다.
   
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-litmos-tutorial/create_aaduser_03.png) 
4. **사용자 추가** 대화 상자를 열려면 아래쪽 도구 모음에서 **사용자 추가**를 클릭합니다. 
   
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-litmos-tutorial/create_aaduser_04.png) 
5. **이 사용자에 대한 정보 입력** 대화 상자 페이지에서 다음 단계를 수행합니다. 
   
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-litmos-tutorial/create_aaduser_05.png)  
   
    1. **사용자 유형**에서 **조직의 새 사용자**를 선택합니다.
   
    2. 사용자 이름 **텍스트 상자**에 **BrittaSimon**을 입력합니다.
   
    3. **다음**을 클릭합니다.
6. **사용자 프로필** 대화 상자 페이지에서 다음 단계를 수행합니다. 
   
   ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-litmos-tutorial/create_aaduser_06.png) 
   
   1. **이름** 텍스트 상자에 **Britta**를 입력합니다.  
   
   2. **성** 텍스트 상자에 **Simon**을 입력합니다.
   
   3. **표시 이름** 텍스트 상자에 **Britta Simon**을 입력합니다.
   
   4. **역할** 목록에서 **사용자**를 선택합니다.
   5. **다음**을 클릭합니다.
7. **임시 암호 가져오기** 대화 상자 페이지에서 **만들기**를 클릭합니다.
   
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-litmos-tutorial/create_aaduser_07.png) 
8. **임시 암호 가져오기** 대화 상자 페이지에서 다음 단계를 수행합니다.
   
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-litmos-tutorial/create_aaduser_08.png) 
   
    1. **새 암호**값을 적어둡니다.
   
    2. **완료**를 클릭합니다.   

### <a name="create-a-litmos-test-user"></a>Litmos 테스트 사용자 만들기
이 섹션은 Litmos에서 Britta Simon이라는 사용자를 만들기 위한 것입니다.  
Litmos 응용 프로그램은 적시에 프로비전을 지원합니다. 즉, 필요한 경우 액세스 패널을 사용하여 응용 프로그램에 액세스하는 동안 사용자 계정은 자동으로 만들어집니다.

**Litmos에서 Britta Simon이라는 사용자를 만들려면 다음 단계를 수행합니다.**

1. 관리자 권한으로 Litmos 회사 사이트(예: *https://azureapptest.litmos.com/account/Login*)에 로그온합니다.
   
    ![Azure AD Single Sign-On][21] 
2. 왼쪽의 탐색 모음에서 **계정**을 클릭합니다.
   
    ![Azure AD Single Sign-On][22] 
3. **통합** 탭을 클릭합니다.
   
    ![Azure AD Single Sign-On][23] 
4. **통합** 탭에서 **3rd 파티 통합**에 아래로 스크롤한 다음 **SAML 2.0** 탭을 클릭합니다.
   
    ![Azure AD Single Sign-On][24] 
5. **자동 생성 사용자**를 선택합니다.
   
    ![Azure AD Single Sign-On][27] 

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당
이 섹션의 목적은 Britta Simon에게 Litmos에 대한 액세스 권한을 부여하여 Single Sign-On을 사용할 수 있도록 하는 것입니다.

![사용자 할당][200] 

**Britta Simon을 Litmos에 할당하려면 다음 단계를 수행합니다.**

1. Azure 클래식 포털에서 응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램** 을 클릭합니다.
   
    ![사용자 할당][201] 
2. 응용 프로그램 목록에서 **Litmos**를 선택합니다.
   
    ![사용자 할당][202] 
3. 위쪽의 메뉴에서 **사용자**를 클릭합니다.
   
    ![사용자 할당][203] 
4. 사용자 목록에서 **Britta Simon**을 선택합니다.
5. 아래쪽 도구 모음에서 **할당**을 클릭합니다.
   
    ![사용자 할당][205]

### <a name="test-single-sign-on"></a>Single Sign-On 테스트
이 섹션은 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트하기 위한 것입니다.  

액세스 패널에서 Litmos 타일을 클릭하면 Litmos 응용 프로그램에 자동으로 로그온됩니다.

## <a name="additional-resources"></a>추가 리소스
* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On이란 무엇입니까?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_01.png
[500]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_02.png

[6]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_03.png
[8]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_04.png
[9]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_05.png
[10]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_07.png
[12]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_80.png
[13]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_81.png
[14]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_82.png
[15]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_81.png
[16]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_19.png
[17]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_67.png


[20]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_100.png
[21]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_60.png
[22]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_61.png
[23]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_62.png
[24]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_63.png
[25]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_64.png
[26]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_65.png
[27]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_66.png

[200]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_68.png
[203]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_205.png


[400]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_400.png
[401]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_401.png
[402]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_402.png






