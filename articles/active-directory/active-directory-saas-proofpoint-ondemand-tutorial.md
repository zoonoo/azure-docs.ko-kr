---
title: "자습서: Proofpoint on Demand와 Azure Active Directory 통합 | Microsoft Docs"
description: "Azure Active Directory 및 Proofpoint on Demand 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 773e7f7d-ec31-411b-860d-6a6633335d43
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 7cc133d6289bffbc3b7fc591104bc51ebfc67ddd
ms.openlocfilehash: 2ea87726b06a0e8ab20515031424bf4e93f45a80
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-proofpoint-on-demand"></a>자습서: Proofpoint on Demand와 Azure Active Directory 통합
이 자습서에서는 Azure AD(Azure Active Directory)와 Proofpoint on Demand를 통합하는 방법에 대해 알아봅니다.

Proofpoint on Demand를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

* Proofpoint on Demand에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
* 사용자가 해당 Azure AD 계정으로 Proofpoint on Demand에 자동으로 로그온(Single Sign-on 또는 SSO)되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure 클래식 포털에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On이란 무엇입니까?](active-directory-appssoaccess-whatis.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건
Proofpoint on Demand와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독
* Proofpoint on Demand SSO(Single Sign-On) 구독

이 자습서의 단계를 테스트하려면 다음 권장 사항을 따릅니다.

* 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마십시오.
* Azure AD 평가판 환경이 없으면 [1개월 평가판을 얻을](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다.

이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

* 갤러리에서 Proofpoint on Demand를 추가합니다.
* Azure AD Single Sign-On을 구성하고 테스트합니다.

## <a name="add-proofpoint-on-demand-from-the-gallery"></a>갤러리에서 Proofpoint on Demand 추가
Proofpoint on Demand의 Azure AD 통합을 구성하려면 갤러리의 Proofpoint on Demand를 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. Azure 클래식 포털의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.
   
    ![Active Directory 아이콘][1]
2. **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.
3. 응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램**을 클릭합니다.
   
    ![응용 프로그램 메뉴 항목][2]
4. 페이지 맨 아래에 있는 **추가**를 클릭합니다.
   
    ![추가 단추][3]
5. **수행할 작업** 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.
   
    ![갤러리에서 응용 프로그램 추가 선택][4]
6. 검색 상자에 **Proofpoint on Demand**를 입력합니다.
   
    !["Proofpoint on Demand"를 입력하는 상자](./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_proofpointondemand_01.png)
7. 결과 창에서 **Proofpoint on Demand**를 선택하고 **완료**를 클릭하여 응용 프로그램을 추가합니다.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트
이 섹션에서는 Britta Simon이라는 테스트 사용자를 기반으로 Proofpoint on Demand에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 Proofpoint on Demand 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 Proofpoint on Demand의 관련 사용자 간에 연결이 형성되어야 합니다.

이 연결 관계는 Azure AD의 **사용자 이름** 값을 Proofpoint on Demand의 **Username** 값으로 할당하여 설정합니다.

Proofpoint on Demand에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 절차를 완료합니다.

1. [Azure AD Single Sign-On 구성](#configuring-azure-ad-single-sign-on) - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. [Azure AD 테스트 사용자 만들기](#creating-an-azure-ad-test-user), Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
3. [Proofpoint on Demand 테스트 사용자 만들기](#creating-a-proofpoint-ondemand-test-user), Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 Proofpoint on Demand에 만듭니다.
4. [Azure AD 테스트 사용자 할당](#assigning-the-azure-ad-test-user), Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. [Single Sign-On 테스트](#testing-single-sign-on), 구성이 작동하는지 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성
이 섹션에서는 클래식 포털에서 Azure AD Single Sign-On을 사용하도록 설정하고 Proofpoint on Demand 응용 프로그램에서 Single Sign-On을 구성합니다.

1. Azure 클래식 포털의 **Proofpoint on Demand** 응용 프로그램 통합 페이지에서 **Single Sign-On 구성**을 클릭하여 **Single Sign-On 구성** 대화 상자를 엽니다.
   
    ![“Single Sign-On 구성” 단추][6]
2. **Proofpoint on Demand에 대한 사용자 로그온 방법 선택** 페이지에서 **Microsoft Azure AD Single Sign-On**을 선택하고 **다음**을 클릭합니다.
   
    ![“Microsoft Azure AD Single Sign-On” 옵션 단추](./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_proofpointondemand_03.png)
3. **앱 설정 구성** 페이지에서 다음 단계를 수행합니다.
   
    ![입력된 상자가 있는 "앱 설정 구성" 페이지](./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_proofpointondemand_04.png)
   1. **로그온 URL** 상자에 사용자가 Proofpoint on Demand 응용 프로그램에 로그인하는 URL을 입력합니다. 다음 패턴 **https://\<hostname\>.pphosted.com/ppssamlsp_hostname**을 사용합니다.
   2. **식별자** 상자에 다음 패턴 **https://\<hostname/>.pphosted.com/ppssamlsp**를 사용하여 URL을 입력합니다.
   3. **회신 URL** 상자에 다음 패턴 **https://\<hostname/>.pphosted.com:portnumber/v1/samlauth/samlconsumer**를 사용하여 URL을 입력합니다.  
   4. **다음**을 클릭합니다.
4. **Proofpoint on Demand에서 Single Sign-On 구성** 페이지에서 다음 단계를 수행합니다.
   
    !["인증서 다운로드" 단추가 있는 "Proofpoint on Demand에서 Single Sign-On 구성" 페이지](./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_proofpointondemand_05.png)
   1. **인증서 다운로드**를 클릭하고 파일을 컴퓨터에 저장합니다.   
   2. **다음**을 클릭합니다.
5. 응용 프로그램에 대해 구성된 SSO를 얻으려면 Proofpoint on Demand 지원 팀에 문의하고 다음을 제공하세요.
   * 다운로드한 인증서
   * 엔터티 ID
   * SAML SSO URL
6. 클래식 포털에서 Single Sign-On 구성 확인을 선택하고 **다음**을 클릭합니다.
   
    ![Single Sign-On을 구성했는지 확인하는 확인란][10]
7. **Single Sign-On 확인** 페이지에서 **완료**를 클릭합니다.  
   
    ![확인 페이지][11]

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기
이 섹션에서는 클래식 포털에서 Britta Simon이라는 테스트 사용자를 만듭니다.

![사용자 목록의 테스트 사용자][20]

1. Azure 클래식 포털의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.
   
    ![Active Directory 아이콘](./media/active-directory-saas-proofpoint-ondemand-tutorial/create_aaduser_09.png)
2. **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.
3. 사용자 목록을 표시하려면 위쪽 메뉴에서 **사용자**를 클릭합니다.
   
    ![사용자 메뉴 항목](./media/active-directory-saas-proofpoint-ondemand-tutorial/create_aaduser_03.png)
4. **사용자 추가** 대화 상자를 열려면 아래쪽 도구 모음에서 **사용자 추가**를 클릭합니다.
   
    ![사용자 추가 단추](./media/active-directory-saas-proofpoint-ondemand-tutorial/create_aaduser_04.png)
5. **이 사용자에 대한 정보 입력** 페이지에서 다음 단계를 수행합니다.

    ![입력된 상자가 있는 [이 사용자에 대한 정보 입력] 페이지](./media/active-directory-saas-proofpoint-ondemand-tutorial/create_aaduser_05.png)   
   1. **사용자 유형** 상자에서 **조직의 새 사용자**를 선택합니다.
   2. **사용자 이름** 상자에 **BrittaSimon**을 입력합니다.
   3. **다음**을 클릭합니다.
6. **사용자 프로필** 페이지에서 다음 단계를 수행합니다.

  ![입력된 상자가 있는 [사용자 프로필] 페이지](./media/active-directory-saas-proofpoint-ondemand-tutorial/create_aaduser_06.png)   
   1. **이름** 상자에 **Britta**를 입력합니다.  
   2. **성** 상자에 **Simon**을 입력합니다.
   3. **표시 이름** 상자에 **Britta Simon**을 입력합니다.
   4. **역할** 목록에서 **사용자**를 선택합니다.
   5. **다음**을 클릭합니다.
7. **임시 암호 가져오기** 페이지에서 **만들기**를 클릭합니다.
   
   ![임시 암호를 만들기 위한 단추](./media/active-directory-saas-proofpoint-ondemand-tutorial/create_aaduser_07.png)
8. **임시 암호 가져오기** 페이지에서 다음 단계를 수행합니다.
   
   ![암호 정보가 있는 "임시 암호 가져오기" 페이지](./media/active-directory-saas-proofpoint-ondemand-tutorial/create_aaduser_08.png)  
   1. **새 암호** 상자의 값을 적어둡니다.
   2. **완료**를 클릭합니다.   

### <a name="create-a-proofpoint-on-demand-test-user"></a>Proofpoint on Demand 테스트 사용자 만들기
이 섹션에서는 Proofpoint on Demand에서 Britta Simon이라는 사용자를 만듭니다. Proofpoint on Demand 지원 팀과 함께 Proofpoint on Demand 플랫폼에 사용자를 추가하세요.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당
이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Proofpoint on Demand에 대한 액세스 권한을 부여합니다.

![직접 메서드를 통해 활성화된 액세스를 보여 주는 사용자 정보][200]

1. 클래식 포털에서 응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램**을 클릭합니다.
   
    ![응용 프로그램 메뉴 항목][201]
2. 응용 프로그램 목록에서 **Proofpoint on Demand**를 선택합니다.
   
    ![Proofpoint on Demand가 선택된 응용 프로그램 목록](./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_proofpointondemand_50.png)
3. 위쪽의 메뉴에서 **사용자**를 클릭합니다.
   
    ![사용자 메뉴 항목][203]
4. 사용자 목록에서 **Britta Simon**을 선택합니다.
5. 아래쪽 도구 모음에서 **할당**을 클릭합니다.
   
    ![할당 단추][205]

### <a name="test-single-sign-on"></a>Single Sign-On 테스트
이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 **Proofpoint on Demand** 타일을 클릭하면 Proofpoint on Demand 응용 프로그램에 자동으로 로그인되어야 합니다.

## <a name="additional-resources"></a>추가 리소스
* [Azure Active Directory를 사용하여 SaaS 앱을 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On이란 무엇입니까?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-proofpoint-ondemand-tutorial/tutorial_general_205.png

