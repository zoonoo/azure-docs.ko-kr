---
title: "자습서: PerformanceCentre와 Azure Active Directory 통합 | Microsoft Docs"
description: "Azure Active Directory 및 PerformanceCentre 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 65288c32-f7e6-4eb3-a6dc-523c3d748d1c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/20/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 04a045f41965b093aab71e59cd9b5f328b44de84
ms.openlocfilehash: 801c0f7085034cc3a3ed74722a11204f6421c27d


---
# <a name="tutorial-azure-active-directory-integration-with-performancecentre"></a>자습서: PerformanceCentre와 Azure Active Directory 통합
이 자습서에서는 PerformanceCentre와 Azure AD(Azure Active Directory)를 통합하는 방법을 보여 줍니다.  

PerformanceCentre를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다. 

* PerformanceCentre에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다. 
* 사용자가 해당 Azure AD 계정으로 PerformanceCentre에 자동으로 로그온(Single Sign-on)되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Active Directory 클래식 포털에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On](active-directory-appssoaccess-whatis.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건
PerformanceCentre와의 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독
* PerformanceCentre Single Sign-on이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.
> 
> 

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

* 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 않도록 합니다.
* Azure AD 평가판 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서&1;개월 평가판을 얻을 수 있습니다. 

## <a name="scenario-description"></a>시나리오 설명
이 자습서는 테스트 환경에서 Azure AD Single Sign-on을 테스트하는 데 도움을 주기 위해 제공되었습니다.  

이 자습서에 설명된 시나리오는 다음 주요 구성 요소로 이루어져 있습니다.

*  갤러리에서 PerformanceCentre 추가 
*  Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-performancecentre-from-the-gallery"></a>갤러리에서 PerformanceCentre 추가
PerformanceCentre의 Azure AD 통합을 구성하려면 갤러리의 PerformanceCentre를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 PerformanceCentre를 추가하려면 다음 단계를 수행합니다.**

1. **Azure 클래식 포털**의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다. 
   
    ![Active Directory][1]
2. **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.
3. 응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램** 을 클릭합니다.
   
    ![응용 프로그램][2]
4. 페이지 맨 아래에 있는 **추가** 를 클릭합니다.
   
    ![응용 프로그램][3]
5. **수행할 작업** 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.
   
    ![응용 프로그램][4]
6. 검색 상자에 **PerformanceCentre**를 입력합니다.
   
    ![응용 프로그램][5]
7. 결과 창에서 **PerformanceCentre**를 선택하고 **완료**를 클릭하여 응용 프로그램을 추가합니다.
   
    ![응용 프로그램][500]

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD Single Sign-on 구성 및 테스트
이 섹션은 "Britta Simon"이라는 테스트 사용자를 기반으로 PerformanceCentre에서 Azure AD Single Sign-on을 구성하고 테스트하는 방법을 보여 주기 위해 작성되었습니다.

Single Sign-on이 작동되려면 Azure AD는 Azure AD의 사용자에 해당하는 PerformanceCentre의 사용자가 누군지 알고 있어야 합니다. 즉, Azure AD 사용자와 PerformanceCentre의 관련 사용자 간에 연결이 형성되어야 합니다.  

이 연결 관계는 Azure AD의 **사용자 이름** 값을 PerformanceCentre의 **Username** 값으로 할당하여 설정합니다.

**PerformanceCentre에서 Azure AD Single Sign-on을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.**

1. **[Azure AD Single Sign-On 구성](#configuring-azure-ad-single-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#creating-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On 테스트하는 데 사용합니다.
3. **[PerformanceCentre 테스트 사용자 만들기](#creating-a-halogen-software-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 PerformanceCentre에 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assigning-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
5. **[Single Sign-On 테스트](#testing-single-sign-on)** - 구성이 작동하는지 확인합니다.

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성
이 섹션은 Azure AD 클래식 포털에서 Azure AD Single Sign-On을 사용하도록 설정하고 PerformanceCentre 응용 프로그램에서 Single Sign-On을 구성하는 방법을 설명하기 위해 제공되었습니다.

**PerformanceCentre에서 Azure AD Single Sign-on을 구성하려면 다음 단계를 수행합니다.**

1. Azure AD 클래식 포털의 **PerformanceCentre** 응용 프로그램 통합 페이지에서 **Single Sign-On 구성**을 클릭하여 **Single Sign-On 구성** 대화 상자를 엽니다.
   
    ![Single Sign-On 구성][6] 
2. **PerformanceCentre에 대한 사용자 로그온 방법 선택** 페이지에서 **Azure AD Single Sign-On**을 선택하고**다음**을 클릭합니다.
   
    ![Azure AD Single Sign-On][7] 
3. **앱 설정 구성** 대화 상자 페이지에서 다음 단계를 수행합니다.
   
    ![Azure AD Single Sign-On][8] 
   
   1. **로그온 URL** 텍스트 상자에 사용자가 PerformanceCentre 사이트에 로그인하는 데 사용하는 URL(예: *http://companyname.performancecentre.com/saml/SSO*)을 입력합니다.
   2. **다음**을 클릭합니다.
4. **PerformanceCentre의 Single Sign-On 구성** 페이지에서 다음 단계를 수행합니다.
   
    ![Azure AD Single Sign-On][9] 
   
  * **메타데이터 다운로드**를 클릭하고 파일을 컴퓨터에 저장합니다.
5. **PerformanceCentre** 회사 사이트에 관리자로 로그인합니다.
6. 왼쪽 탭에서 **구성**을 클릭합니다.
   
    ![Azure AD Single Sign-On][10]
7. 왼쪽 탭에서 **Miscellaneous**를 클릭하고 **Single Sign-On**을 클릭합니다.
   
    ![Azure AD Single Sign-On][11]
8. **프로토콜**로 **SAML**을 선택합니다.
   
    ![Azure AD Single Sign-On][12]
9. 다운로드한 메타데이터 파일을 메모장에서 열고 내용을 복사한 다음 **ID 공급자 메타데이터** 텍스트 상자에 붙여넣은 다음 **저장**을 클릭합니다.
   
    ![Azure AD Single Sign-On][13]
10. **엔터티 기준 URL** 및 **엔터티 ID URL**의 값이 올바른지 확인합니다.
    
     ![Azure AD Single Sign-On][14]
11. Azure AD 클래식 포털에서 Single Sign-On을 구성했음을 확인한다는 확인란을 선택하고 **다음**을 클릭합니다. 
    
     ![Azure AD Single Sign-On][15]
12. **Single Sign-On 확인** 페이지에서 **완료**를 클릭합니다.  
    
     ![Azure AD Single Sign-On][16]

### <a name="creating-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기
이 섹션의 목적은 Azure 클래식 포털에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.  

![Azure AD 사용자 만들기][20]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. **Azure 클래식 포털**의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.
   
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_09.png)  
2. **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.
3. 사용자 목록을 표시하려면 위쪽 메뉴에서 **사용자**를 클릭합니다.
   
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_03.png) 
4. **사용자 추가** 대화 상자를 열려면 아래쪽 도구 모음에서 **사용자 추가**를 클릭합니다. 
   
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_04.png) 
5. **이 사용자에 대한 정보 입력** 대화 상자 페이지에서 다음 단계를 수행합니다. 
   
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_05.png)  
   
   1. 사용자 유형에서 조직의 새 사용자를 선택합니다.
   2. 사용자 이름 **텍스트 상자**에 **BrittaSimon**을 입력합니다.
   3. **다음**을 클릭합니다.
   
6. **사용자 프로필** 대화 상자 페이지에서 다음 단계를 수행합니다. 
   
   ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_06.png) 
   
   1. **이름** 텍스트 상자에 **Britta**를 입력합니다.  
   2. **성** 텍스트 상자에 **Simon**을 입력합니다.
   3. **표시 이름** 텍스트 상자에 **Britta Simon**을 입력합니다.
   4. **역할** 목록에서 **사용자**를 선택합니다.
   5. **다음**을 클릭합니다.
   
7. **임시 암호 가져오기** 대화 상자 페이지에서 **만들기**를 클릭합니다.
   
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_07.png) 
8. **임시 암호 가져오기** 대화 상자 페이지에서 다음 단계를 수행합니다.
   
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_08.png) 
   
    1. **새 암호**값을 적어둡니다.
    2. **완료**를 클릭합니다.   

### <a name="creating-a-performancecentre-test-user"></a>PerformanceCentre 테스트 사용자 만들기
이 섹션은 PerformanceCentre에서 Britta Simon이라는 사용자를 만들기 위한 것입니다.

**PerformanceCentre에서 Britta Simon이라는 사용자를 만들려면 다음 단계를 수행합니다.**

1. PerformanceCentre 회사 사이트에 관리자로 로그인합니다.
2. 왼쪽 메뉴에서 **밀접한 연관**을 클릭한 다음 **참가자 만들기**를 클릭합니다.
   
    ![사용자 만들기][400]
3. **밀접한 연관 - 참가자 만들기** 대화 상자에서 다음 단계를 수행합니다.
   
    ![사용자 만들기][401]
   
   1. Britta Simon에 대한 필수 특성을 관련된 텍스트 상자에 입력합니다.
   > [!IMPORTANT]
   > PerformanceCentre에서 Britta의 사용자 이름 특성은 Azure AD에서 사용자 이름과 동일해야 합니다.
   > 
   > 
 
   2. **클라이언트 관리자**로 **역할 선택**을 수행합니다.
   3. **Save**를 클릭합니다.   


### <a name="assigning-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당
이 섹션의 목적은 Britta Simon에게 PerformanceCentre에 대한 액세스 권한을 부여하여 Single Sign-On을 사용할 수 있도록 하는 것입니다.

![사용자 할당][200] 

**Britta Simon을 PerformanceCentre에 할당하려면 다음 단계를 수행합니다.**

1. Azure 클래식 포털에서 응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램** 을 클릭합니다.
   
    ![사용자 할당][201]
2. 응용 프로그램 목록에서 **PerformanceCentre**를 선택합니다.
   
    ![사용자 할당][202]
3. 위쪽의 메뉴에서 **사용자**를 클릭합니다.
   
    ![사용자 할당][203]
4. 사용자 목록에서 **Britta Simon**을 선택합니다.
5. 아래쪽 도구 모음에서 **할당**을 클릭합니다.
   
    ![사용자 할당][205]

### <a name="testing-single-sign-on"></a>Single Sign-On 테스트
이 섹션은 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트하기 위한 것입니다.  

액세스 패널에서 PerformanceCentre 타일을 클릭하면 PerformanceCentre 응용 프로그램에 자동으로 로그온됩니다.

## <a name="additional-resources"></a>추가 리소스
* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On이란 무엇입니까?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_01.png
[500]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_02.png

[6]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_03.png
[8]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_04.png
[9]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_05.png
[10]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_06.png
[11]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_07.png
[12]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_08.png
[13]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_09.png
[14]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_10.png
[15]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_06.png
[16]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_07.png


[20]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_50.png
[203]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_205.png


[400]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_11.png
[401]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_12.png
[402]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_402.png






<!--HONumber=Feb17_HO1-->


