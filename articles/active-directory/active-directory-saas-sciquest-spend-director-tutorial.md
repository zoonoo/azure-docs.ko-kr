---
title: "자습서: SciQuest Spend Director와 Azure Active Directory 통합 | Microsoft 문서"
description: "Azure Active Directory 및 SciQuest Spend Director 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 9fab641b-292e-4bef-91d1-8ccc4f3a0c1f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/17/2017
ms.author: jeedes
ms.openlocfilehash: 84b707668dc45e92e6151f422f1c919f638533b1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-sciquest-spend-director"></a>자습서: SciQuest Spend Director와 Azure Active Directory 통합
이 자습서에서는 SciQuest Spend Director와 Azure AD(Azure Active Directory)를 통합하는 방법을 보여 줍니다.  
SciQuest Spend Director를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다. 

* SciQuest Spend Director에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다. 
* 사용자가 해당 Azure AD 계정으로 SciQuest Spend Director에 자동으로 로그온(Single Sign-on)되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure 클래식 포털에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On](active-directory-appssoaccess-whatis.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건
SciQuest Spend Director와의 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독
* SciQuest Spend Director Single Sign-On이 설정된 구독

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

1. 갤러리에서 SciQuest Spend Director 추가 
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-sciquest-spend-director-from-the-gallery"></a>갤러리에서 SciQuest Spend Director 추가
SciQuest Spend Director의 Azure AD 통합을 구성하려면 갤러리의 SciQuest Spend Director를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 SciQuest Spend Director를 추가하려면 다음 단계를 수행합니다.**

1. **Azure 클래식 포털**의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다. 
   
    ![Active Directory][1]

2. **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.

3. 응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램** 을 클릭합니다.
   
    ![응용 프로그램][2]

4. 페이지 맨 아래에 있는 **추가** 를 클릭합니다.
   
    ![응용 프로그램][3]

5. **수행할 작업** 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.
   
    ![응용 프로그램][4]

6. 검색 상자에 **sciQuest spend director**를 입력합니다.
   
    ![응용 프로그램][5]

7. 결과 창에서 **SciQuest Spend Director**를 선택한 다음 **완료**를 클릭하여 응용 프로그램을 추가합니다.
   
    ![응용 프로그램][6]

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD Single Sign-on 구성 및 테스트
이 섹션은 "Britta Simon"이라는 테스트 사용자를 기반으로 SciQuest Spend Director에서 Azure AD Single Sign-on을 구성하고 테스트하는 방법을 보여 주기 위해 작성되었습니다.

Single Sign-on이 작동되려면 Azure AD는 Azure AD의 사용자에 해당하는 SciQuest Spend Director의 사용자가 누군지 알고 있어야 합니다. 즉, Azure AD 사용자와 SciQuest Spend Director의 관련 사용자 간에 연결이 형성되어야 합니다.  
이 연결 관계는 Azure AD의 **사용자 이름** 값을 SciQuest Spend Director의 **Username** 값으로 할당하여 설정합니다.

SciQuest Spend Director에서 Azure AD Single Sign-on을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-on 구성](#configuring-azure-ad-single-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#creating-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On 테스트하는 데 사용합니다.
3. **[SciQuest Spend Director 테스트 사용자 만들기](#creating-a-halogen-software-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 SciQuest Spend Director에 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assigning-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
5. **[Single Sign-On 테스트](#testing-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configuring-azure-ad-single-single-sign-on"></a>Azure AD Single Sign-on 구성
이 섹션은 Azure 클래식 포털에서 Azure AD Single Sign-On을 사용하도록 설정하고 SciQuest Spend Director 응용 프로그램에서 Single Sign-On을 구성하는 방법을 설명하기 위해 제공되었습니다.

**SciQuest Spend Director에서 Azure AD Single Sign-on을 구성하려면 다음 단계를 수행합니다.**

1. Azure 클래식 포털의 **SciQuest Spend Director** 응용 프로그램 통합 페이지에서 **Single Sign-On 구성**을 클릭하여 **Single Sign-On 구성** 대화 상자를 엽니다.
   
    ![Single Sign-on 구성][8]

2. **사용자가 SciQuest Spend Director에 로그인하는 방법을 선택하십시오.** 페이지에서 **Azure AD Single Sign-On**을 선택하고 **다음**을 클릭합니다.
   
    ![Azure AD Single Sign-On][9]

3. **앱 설정 구성** 대화 상자 페이지에서 다음 단계를 수행합니다. 
   
    ![앱 설정 구성][10]
   
     a. **로그인 URL** 텍스트 상자에서 *https://.*sciquest.com/.** 패턴을 사용하여 사용자가 SciQuest Spend Director 응용 프로그램에 로그온하는 데 사용하는 URL을 입력합니다.
   
     b. **회신 URL** 텍스트 상자의 **로그인 URL** 텍스트 상자에서 입력한 것과 동일한 값을 입력합니다. 
   
     c. **다음**을 누릅니다.

4. **SciQuest Spend Director에서 Single Sign-On 구성** 페이지에서 **메타데이터 다운로드**를 클릭한 다음 메타데이터 파일을 컴퓨터에 로컬로 저장합니다.
   
    ![Azure AD Connect의 정의][11]

5. 위에서 다운로드한 메타데이터를 사용하여 이 인증 방법을 설정하도록 SciQuest 지원 팀에 요청합니다.

6. Azure 클래식 포털에서 Single Sign-On 구성 확인을 선택하고 **완료**를 클릭하여 **Single Sign-On 구성** 대화 상자를 닫습니다. 
   
    ![Azure AD Connect의 정의][15]

7. **Single Sign-On 확인** 페이지에서 **완료**를 클릭합니다.  

### <a name="creating-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기
이 섹션의 목적은 Azure 클래식 포털에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. **Azure 클래식 포털**의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.
   
    ![Azure AD Connect의 정의][100] 

2. **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.

3. 사용자 목록을 표시하려면 위쪽 메뉴에서 **사용자**를 클릭합니다.
   
    ![Azure AD Connect의 정의][101] 

4. **사용자 추가** 대화 상자를 열려면 아래쪽 도구 모음에서 **사용자 추가**를 클릭합니다. 
   
    ![Azure AD Connect의 정의][102] 

5. **이 사용자에 대한 정보 입력** 대화 상자 페이지에서 다음 단계를 수행합니다.
   
    ![Azure AD Connect의 정의][103] 
   
    a. **사용자 유형**에서 **조직의 새 사용자**를 선택합니다.
   
    b. 사용자 이름 **텍스트 상자**에 **BrittaSimon**을 입력합니다.
   
    c. **다음**을 클릭합니다.

6. **사용자 프로필** 대화 상자 페이지에서 다음 단계를 수행합니다. 
   
    ![Azure AD Connect의 정의][104] 
   
    a. **이름** 텍스트 상자에 **Britta**를 입력합니다.  
   
    b. **성** 텍스트 상자에 **Simon**을 입력합니다.
   
    c. **표시 이름** 텍스트 상자에 **Britta Simon**을 입력합니다.
   
    d. **역할** 목록에서 **사용자**를 선택합니다.
   
    e. **다음**을 클릭합니다.

7. **임시 암호 가져오기** 대화 상자 페이지에서 **만들기**를 클릭합니다.
   
    ![Azure AD Connect의 정의][105]  

8. **임시 암호 가져오기** 대화 상자 페이지에서 다음 단계를 수행합니다.
   
    ![Azure AD Connect의 정의][106]   
   
    a. **새 암호**값을 적어둡니다.
   
    b. **완료**를 클릭합니다.   

### <a name="creating-a-sciquest-spend-director-test-user"></a>SciQuest Spend Director 테스트 사용자 만들기
이 섹션의 목적은 SciQuest Spend Director에서 Britta Simon이라는 사용자를 만드는 것입니다.

테스트 계정을 만들려면 SciQuest Spend Director 지원 팀에 문의한 후 테스트 계정에 대한 세부 정보를 제공해야 합니다.

또는 SciQuest Spend Director에서 지원되는 Single Sign-On 기능인 Just-in-Tme 프로비저닝을 활용할 수도 있습니다.  
Just-in-Tme 프로비전을 사용하도록 설정한 경우 계정이 없는 사용자가 Single Sign-On 시도를 수행하는 동안 SciQuest Spend Director에서 사용자 계정이 자동으로 만들어집니다. 이 기능을 사용하여 Single Sign-On 해당 사용자를 수동으로 만들 필요가 없습니다.

Just-in-Tme 프로비저닝을 사용하도록 설정하려면 SciQuest Spend Director 지원 팀에 문의해야 합니다.

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당
이 섹션의 목적은 Britta Simon에게 SciQuest Spend Director에 대한 액세스 권한을 부여하여 Single Sign-On을 사용할 수 있도록 하는 것입니다.

![Azure AD Connect의 정의][200]

**SciQuest Spend Director에 Britta Simon를 할당하려면 다음 단계를 수행합니다.**

1. Azure 클래식 포털에서 응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램** 을 클릭합니다.
   
    ![Azure AD Connect의 정의][201]

2. 응용 프로그램 목록에서 **SciQuest Spend Director**를 선택합니다.
   
    ![Azure AD Connect의 정의][202]

3. 위쪽의 메뉴에서 **사용자**를 클릭합니다.
   
    ![Azure AD Connect의 정의][203]

4. 사용자 목록에서 **Britta Simon**을 선택합니다.
   
    ![Azure AD Connect의 정의][204]

5. 아래쪽 도구 모음에서 **할당**을 클릭합니다.
   
    ![Azure AD Connect의 정의][205]

### <a name="testing-single-sign-on"></a>Single Sign-On 테스트
이 섹션은 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트하기 위한 것입니다.  
액세스 패널에서 SciQuest Spend Director 타일을 클릭하면 SciQuest Spend Director 응용 프로그램에 자동으로 로그온됩니다.

## <a name="additional-resources"></a>추가 리소스
* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On이란 무엇입니까?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_sciquest_spend_director_01.png
[6]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_sciquest_spend_director_05.png
[8]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_06.png
[9]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_07.png
[10]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_08.png
[11]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_sciquest_spend_director_03.png
[15]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_sciquest_spend_director_04.png

[100]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_09.png 
[101]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_10.png 
[102]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_11.png 
[103]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_12.png 
[104]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_13.png 
[105]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_14.png 
[106]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_15.png 
[200]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_16.png 
[201]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_17.png 
[202]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_sciquest_spend_director_06.png
[203]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_18.png
[204]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_19.png
[205]: ./media/active-directory-saas-sciquest-spend-director-tutorial/tutorial_general_20.png

