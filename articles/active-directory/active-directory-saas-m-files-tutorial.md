---
title: "자습서: M-Files와 Azure Active Directory 통합 | Microsoft Docs"
description: "Azure Active Directory와 M-Files 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 4536fd49-3a65-4cff-9620-860904f726d0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/24/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 4a07f946d396a9263d5b00202cd5229ddc86d1be
ms.openlocfilehash: 78f0a065b675326a9a507e0cf480bc779fd458a9
ms.lasthandoff: 03/01/2017


---
# <a name="tutorial-azure-active-directory-integration-with-m-files"></a>자습서: Azure Active Directory와 M-Files 통합
이 자습서에서는 Azure AD(Azure Active Directory)와 M-Files를 통합하는 방법에 대해 알아봅니다.

M-Files를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

* M-Files에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
* 사용자가 해당 Azure AD 계정으로 M-Files SSO(Single Sign-On)에 자동으로 로그온되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure 클래식 포털에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On](active-directory-appssoaccess-whatis.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건
M-Files와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독
* **M-Files** SSO가 설정된 구독

>[!NOTE]
>이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다. 
> 

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

* 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 않도록 합니다.
* Azure AD 평가판 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서&1;개월 평가판을 얻을 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 M-Files 추가
2. Azure AD SSO 구성 및 테스트

## <a name="adding-m-files-from-the-gallery"></a>갤러리에서 M-Files 추가
M-Files의 Azure AD 통합을 구성하려면 갤러리의 M-Files를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 M-Files를 추가하려면 다음 단계를 수행합니다.**

1. **Azure 클래식 포털**의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다. 
   
    ![Active Directory][1]
2. **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.
3. 응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램** 을 클릭합니다.
   
    ![응용 프로그램][2]
4. 페이지 맨 아래에 있는 **추가** 를 클릭합니다.
   
    ![응용 프로그램][3]
5. **수행할 작업** 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.
   
    ![응용 프로그램][4]
6. 검색 상자에 **M-Files**를 입력합니다.
   
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-m-files-tutorial/tutorial_m_files_01.png)
7. 결과 창에서 **M-Files**를 선택하고 **완료**를 클릭하여 응용 프로그램을 추가합니다.
   
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-m-files-tutorial/tutorial_m_files_02.png)

## <a name="configure-and-test-azure-ad-sso"></a>Azure AD SSO 구성 및 테스트
이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 M-Files에서 Azure AD SSO를 구성하고 테스트합니다.

SSO가 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 M-Files 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 M-Files의 관련 사용자 간에 연결이 형성되어야 합니다.

이 연결 관계는 Azure AD의 **사용자 이름** 값을 M-Files의 **Username** 값으로 할당하여 설정합니다. M-Files에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configuring-azure-ad-single-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#creating-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-on 테스트하는 데 사용합니다.
3. **[M-Files 테스트 사용자 만들기](#creating-a-m-file-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 M-Files에 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assigning-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Single Sign-On 테스트](#testing-single-sign-on)** - 구성이 작동하는지 확인합니다.

### <a name="configuring-azure-ad-sso"></a>Azure AD SSO 구성
이 섹션은 Azure 클래식 포털에서 Azure AD SSO를 사용하도록 설정하고 M-Files 응용 프로그램에서 SSO를 구성하는 방법을 설명하기 위한 것입니다.

**M-Files에서 Azure AD SSO를 구성하려면 다음 단계를 수행합니다.**

1. 클래식 포털의 **M-Files** 응용 프로그램 통합 페이지에서 **Single Sign-On 구성**을 클릭하여 **Single Sign-On 구성** 대화 상자를 엽니다.
   
    ![Single Sign-on 구성][7] 
2. **M-Files에 대한 사용자 로그온 방법 선택** 페이지에서 **Azure AD Single Sign-On**을 선택하고 **다음**을 클릭합니다.
   
    ![Single Sign-on 구성](./media/active-directory-saas-m-files-tutorial/tutorial_m_files_06.png)
3. **앱 설정 구성** 대화 상자 페이지에서 다음 단계를 수행합니다.
   
    ![Single Sign-On 구성](./media/active-directory-saas-m-files-tutorial/tutorial_m_files_07.png)
  1. 로그인 URL 텍스트 상자에서 다음과 같은 패턴을 사용하여 URL을 입력합니다. `https://<tenant-name>.cloudvault.m-files.com/authentication/MFiles.AuthenticationProviders.Core/sso`
  2. **다음**을 클릭합니다.
4. **M-Files에서 Single Sign-On 구성** 페이지에서 **메타데이터 다운로드**를 클릭한 다음 컴퓨터에 파일을 저장합니다. 메타데이터 파일을 저장합니다.
   
    ![Single Sign-on 구성](./media/active-directory-saas-m-files-tutorial/tutorial_m_files_09.png)
5. 응용 프로그램에 대해 구성된 SSO를 얻으려면 M-Files 지원 팀(<mailto:support@m-files.com>)에 문의하고 다운로드한 메타데이터를 제공합니다.
   
   >[!NOTE]
   >M-Files 데스크톱 응용 프로그램에 대한 SSO를 구성하려면 다음 단계를 따릅니다. M-Files 웹 버전에 대해서만 SSO를 구성하려는 경우 추가 단계가 필요하지 않습니다.  
   > 
6. 다음 단계에 따라 Azure AD에서 SSO를 사용하도록 M-Files 데스크톱 응용 프로그램을 구성합니다. M-Files를 다운로드하려면 [M-Files 다운로드](https://www.m-files.com/en/download-latest-version) 페이지로 이동합니다.
7. **M-Files 데스크톱 설정** 창을 엽니다. 그런 다음 **추가**를 클릭합니다.
   
    ![Single Sign-On 구성](./media/active-directory-saas-m-files-tutorial/tutorial_m_files_10.png)
8. **문서 자격 증명 모음 연결 속성** 창에서 다음 단계를 수행합니다.
   
  ![Single Sign-On 구성](./media/active-directory-saas-m-files-tutorial/tutorial_m_files_11.png)  
    **서버** 섹션 아래에서 다음과 같이 값을 입력합니다.  
 1. **이름**에 `<tenant-name>.cloudvault.m-files.com`을 입력합니다.  
 2. **포트 번호**에 **4466**을 입력합니다. 
 3. **프로토콜**에서 **HTTPS**를 선택합니다. 
 4. **인증** 필드에서 **특정 Windows 사용자**를 선택합니다. 그러면 서명 페이지가 표시됩니다. Azure AD 자격 증명을 삽입하세요. 
 5. **서버의 자격 증명 모음**에서 서버에 있는 해당 자격 증명 모음을 선택합니다. 
 6. **확인**을 클릭합니다.

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기
이 섹션에서는 클래식 포털에서 Britta Simon이라는 테스트 사용자를 만듭니다.

![Azure AD 사용자 만들기][20]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. **Azure 클래식 포털**의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.
   
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-m-files-tutorial/create_aaduser_09.png) 
2. **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.
3. 사용자 목록을 표시하려면 위쪽 메뉴에서 **사용자**를 클릭합니다.
   
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-m-files-tutorial/create_aaduser_03.png) 
4. **사용자 추가** 대화 상자를 열려면 아래쪽 도구 모음에서 **사용자 추가**를 클릭합니다.
   
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-m-files-tutorial/create_aaduser_04.png) 
5. **이 사용자에 대한 정보 입력** 대화 상자 페이지에서 다음 단계를 수행합니다.
   
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-m-files-tutorial/create_aaduser_05.png) 
 1. 사용자 유형에서 조직의 새 사용자를 선택합니다.  
 2. 사용자 이름 **텍스트 상자**에 **BrittaSimon**을 입력합니다. 
 3. **다음**을 클릭합니다.
6. **사용자 프로필** 대화 상자 페이지에서 다음 단계를 수행합니다.
   
   ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-m-files-tutorial/create_aaduser_06.png)  
 1. **이름** 텍스트 상자에 **Britta**를 입력합니다.   
 2. **성** 텍스트 상자에 **Simon**을 입력합니다. 
 3. **표시 이름** 텍스트 상자에 **Britta Simon**을 입력합니다. 
 4. **역할** 목록에서 **사용자**를 선택합니다. 
 5. **다음**을 클릭합니다.
7. **임시 암호 가져오기** 대화 상자 페이지에서 **만들기**를 클릭합니다.
   
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-m-files-tutorial/create_aaduser_07.png) 
8. **임시 암호 가져오기** 대화 상자 페이지에서 다음 단계를 수행합니다.
   
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-m-files-tutorial/create_aaduser_08.png)  
 1. **새 암호**값을 적어둡니다. 
 2. **완료**를 클릭합니다.   

### <a name="create-a-m-files-test-user"></a>M-Files 테스트 사용자 만들기
이 섹션에서는 M-Files에서 Britta Simon이라는 사용자를 만듭니다. M-Files에서 사용자를 만드는 방법을 모를 경우 M-Files 지원(<mailto:support@m-files.com>)에 문의하세요.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당
이 섹션에서는 Azure SSO를 사용할 수 있도록 Britta Simon에게 M-Files에 대한 액세스 권한을 부여합니다.

![사용자 할당][200]

**Britta Simon을 M-Files에 할당하려면 다음 단계를 수행합니다.**

1. 클래식 포털에서 응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램** 을 클릭합니다.
   
    ![사용자 할당][201] 
2. 응용 프로그램 목록에서 **M-Files**를 선택합니다.
   
    ![Single Sign-on 구성](./media/active-directory-saas-m-files-tutorial/tutorial_m_files_12.png)
3. 위쪽의 메뉴에서 **사용자**를 클릭합니다.
   
    ![사용자 할당][203] 
4. 모든 사용자 목록에서 **Britta Simon**을 선택합니다.
5. 아래쪽 도구 모음에서 **할당**을 클릭합니다.
   
    ![사용자 할당][205]

### <a name="test-single-sign-on"></a>Single Sign-On 테스트
이 섹션은 액세스 패널을 사용하여 Azure AD SSO 구성을 테스트하기 위한 것입니다.

액세스 패널에서 M-Files 타일을 클릭하면 M-Files 응용 프로그램에 자동으로 로그온됩니다.

## <a name="additional-resources"></a>추가 리소스
* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On이란 무엇입니까?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_06.png
[7]:  ./media/active-directory-saas-m-files-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-m-files-tutorial/tutorial_general_205.png

