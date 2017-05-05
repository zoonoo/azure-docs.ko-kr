---
title: "자습서: Atlassian Cloud와 Azure Active Directory 통합 | Microsoft Docs"
description: "Azure Active Directory와 Atlassian Cloud 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
editor: na
ms.assetid: 729b8eb6-efc4-47fb-9f34-8998ca2c9545
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 8928581d9636f571008f965185eeb61b414a16e7
ms.lasthandoff: 04/03/2017


---
# <a name="tutorial-azure-active-directory-integration-with-atlassian-cloud"></a>자습서: Atlassian Cloud와 Azure Active Directory 통합

이 자습서에서는 Atlassian Cloud와 Azure AD(Azure Active Directory)를 통합하는 방법에 대해 알아봅니다.

Atlassian Cloud를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- Azure AD에서는 Atlassian Cloud에 대한 액세스 권한이 있는 사용자를 제어할 수 있습니다.
- 사용자가 Azure AD 계정으로 Atlassian Cloud에 자동으로 로그인(Single Sign-on)할 수 있도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure 클래식 포털에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On](active-directory-appssoaccess-whatis.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건

Atlassian Cloud와 Azure AD를 통합하도록 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- Atlassian Cloud Single Sign-On 사용이 설정된 구독


>[!NOTE] 
>이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 않도록 합니다.
- Azure AD 평가판 환경이 없으면 [1개월 평가판을 얻을](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다.


## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다.

이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 Atlassian Cloud 추가
2. Azure AD SSO 구성 및 테스트


## <a name="add-atlassian-cloud-from-the-gallery"></a>갤러리에서 Atlassian Cloud 추가
Azure AD에 Atlassian Cloud와 Azure AD를 통합하도록 구성하려면 갤러리의 Atlassian Cloud를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Atlassian Cloud를 추가하려면 다음 단계를 수행합니다.**

1. **Azure 클래식 포털**의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.

    ![Active Directory][1]
2. **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.

3. 응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램** 을 클릭합니다.

    ![응용 프로그램][2]

4. 페이지 맨 아래에 있는 **추가** 를 클릭합니다.

    ![응용 프로그램][3]

5. **수행할 작업** 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.

    ![응용 프로그램][4]

6. 검색 상자에서 **Atlassian Cloud**를 입력합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_01.png)

7. 결과 창에서 **Atlassian Cloud**를 선택한 다음 **완료**를 클릭하여 응용 프로그램을 추가합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_02.png)

##  <a name="configure-and-test-azure-ad-sso"></a>Azure AD SSO 구성 및 테스트
이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 하여 Atlassian Cloud에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

SSO가 작동하려면 Azure AD에서 Azure AD 사용자에 대응하는 Atlassian Cloud 사용자가 누구인지 알고 있어야 합니다. 즉 Azure AD 사용자와 Atlassian Cloud의 관련 사용자 간에 연결 관계가 설정되어야 합니다.

이 연결 관계는 Azure AD의 **사용자 이름** 값을 Atlassian Cloud의 **Username** 값으로 할당하여 설정합니다.

Atlassian Cloud에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configuring-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#creating-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
3. **[Atlassian Cloud 테스트 사용자 만들기](#creating-Atlassian-cloud-test-user)** - Britta Simon의 Azure AD 표현과 연결되는 대응 사용자를 Atlassian Cloud에 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assigning-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Single Sign-On 테스트](#testing-single-sign-on)** - 구성이 작동하는지 확인합니다.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

이 섹션에서는 클래식 포털에서 Azure AD Single Sign-On을 사용하도록 설정하고 Atlassian Cloud 응용 프로그램에서 SSO를 구성합니다.


**Atlassian Cloud에서 Azure AD Single Sign-on을 구성하려면 다음 단계를 수행합니다.**

1. 클래식 포털의 **Atlassian Cloud** 응용 프로그램 통합 페이지에서 **Single Sign-On 구성**을 클릭하여 **Single Sign-On 구성** 대화 상자를 엽니다.
     
    ![Single Sign-on 구성][6] 

2. **사용자가 Atlassian Cloud에 로그인하는 방법을 선택하십시오.** 페이지에서 **Azure AD Single Sign-On**을 선택하고 **다음**을 클릭합니다.

    ![Single Sign-on 구성](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_03.png) 

3. **앱 설정 구성** 대화 상자 페이지에서 다음 단계를 수행합니다.

    ![Single Sign-on 구성](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_04.png) 
 1. **로그인 URL** 텍스트 상자에서 `https://<instancename>.atlassian.net` 패턴으로 사용자가 Atlassian Cloud 응용 프로그램에 로그인하는 데 사용하는 URL을 입력합니다.    
 2. **식별자** 텍스트 상자에서 `https://id.atlassian.com/login` 패턴으로 URL을 입력합니다.

    >[!NOTE] 
    >[Atlassian Cloud SAML 구성] 화면에서 정확한 **식별자** 값을 가져올 수 있습니다.
    >

 3. **다음**을 클릭합니다.
 
4. **Atlassian Cloud에서 Single Sign-On 구성** 페이지에서 다음 단계를 수행합니다.

    ![Single Sign-on 구성](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_05.png)
 1. **인증서 다운로드**를 클릭하고 파일을 컴퓨터에 저장합니다.
 2. **다음**을 클릭합니다.

5. 응용 프로그램에 SSO를 구성하려면 관리자 권한으로 Atlassian Portal에 로그인합니다.

6. 왼쪽 탐색 모음의 [인증] 섹션에서 **도메인**을 클릭합니다.

    ![Single Sign-on 구성](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_06.png)
 1. 텍스트 상자에서 도메인 이름을 입력한 다음 **도메인 추가**를 클릭합니다.
        
    ![Single Sign-on 구성](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_07.png)
 2. 도메인을 확인하려면 **확인**을 클릭합니다. 

    ![Single Sign-on 구성](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_08.png) 
  3. 도메인 확인 HTML 파일을 다운로드하여 도메인 웹 사이트의 루트 폴더에 업로드한 다음 **도메인 확인**을 클릭합니다.
    
    ![Single Sign-On 구성](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_09.png)
  4. 도메인이 확인되었으면 **상태** 필드의 값이 **확인됨**으로 표시됩니다.

    ![Single Sign-on 구성](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_10.png)

7. 왼쪽 탐색 모음에서 **SAML**을 클릭합니다.
 
    ![Single Sign-on 구성](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_11.png)

8. 새 SAML 구성을 만들고 ID 공급자 구성을 추가합니다.
  1. Azure AD에서 [엔터티 ID] 값을 복사하여 [ID 공급자 엔터티 ID] 필드에 붙여넣습니다.
  2. SAML SSO URL을 복사하여 [ID 공급자 SSO URL] 상자에 붙여넣습니다.
  3. Azure AD에서 다운로드한 인증서를 [메모장]에서 열고 시작 및 끝 행을 제외한 값을 복사하여 [공용 X509 인증서] 상자에 붙여넣습니다.
  4. 설정을 저장합니다.

      ![Single Sign-on 구성](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_12.png)
 
9. Azure AD 설정을 업데이트하여 올바른 식별자 URL을 설정했는지 확인합니다.
  1. SAML 화면에서 [SP Entity ID]를 복사하여 Azure AD에 **식별자** 값으로 붙여넣습니다.
  2. [로그인 URL]은 Atlassian Cloud의 테넌트 URL입니다.     

     ![Single Sign-on 구성](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_13.png)
    
10. 클래식 포털에서 Single Sign-On 구성 확인을 선택하고 **다음**을 클릭합니다.
    
    ![Azure AD Single Sign-On][10]

7. **Single Sign-On 확인** 페이지에서 **완료**를 클릭합니다.  
 
    ![Azure AD Single Sign-On][11]


### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기
이 섹션에서는 클래식 포털에서 Britta Simon이라는 테스트 사용자를 만듭니다.

![Azure AD 사용자 만들기][20]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. **Azure 클래식 포털**의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_09.png) 

2. **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.

3. 사용자 목록을 표시하려면 위쪽 메뉴에서 **사용자**를 클릭합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_03.png) 

4. **사용자 추가** 대화 상자를 열려면 아래쪽 도구 모음에서 **사용자 추가**를 클릭합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_04.png) 

5. **이 사용자에 대한 정보 입력** 대화 상자 페이지에서 다음 단계를 수행합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_05.png) 
  1. 사용자 유형에서 조직의 새 사용자를 선택합니다.
  2. 사용자 이름 **텍스트 상자**에 **BrittaSimon**을 입력합니다.
  3. **다음**을 클릭합니다.

6.  **사용자 프로필** 대화 상자 페이지에서 다음 단계를 수행합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_06.png) 
   1. **이름** 텍스트 상자에 **Britta**를 입력합니다.  
   2. **성** 텍스트 상자에 **Simon**을 입력합니다.
   3. **표시 이름** 텍스트 상자에 **Britta Simon**을 입력합니다.
   4. **역할** 목록에서 **사용자**를 선택합니다.
   5. **다음**을 클릭합니다.

7. **임시 암호 가져오기** 대화 상자 페이지에서 **만들기**를 클릭합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_07.png) 

8. **임시 암호 가져오기** 대화 상자 페이지에서 다음 단계를 수행합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_08.png) 
  1. **새 암호**값을 적어둡니다.
  2. **완료**를 클릭합니다.   

### <a name="create-an-atlassian-cloud-test-user"></a>Atlassian Cloud 테스트 사용자 만들기

이 섹션에서는 Atlassian Cloud에서 Britta Simon이라는 사용자를 만듭니다. SSO를 수행하기 전에 사용자가 Atlassian Cloud에 있어야 합니다. 

관리자 권한으로 Atlassian Cloud 인스턴스에 로그인하고 다음 단계를 수행합니다.

>[!NOTE] 
>또한 [사용자] 섹션에서 **대량 만들기** 단추를 클릭하여 운이 좋은 사용자를 만들 수도 있습니다.

1. [사이트 관리] 섹션에서 **사용자** 단추를 클릭합니다.

    ![Atlassian Cloud 사용자 만들기](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_14.png) 

2. Atlassian Cloud에서 사용자를 만들기 위해 **사용자 만들기** 단추를 클릭합니다.

    ![Atlassian Cloud 사용자 만들기](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_15.png) 

3. 사용자의 [전자 메일 주소], [사용자 이름] 및 [전체 이름]을 입력하고 응용 프로그램 액세스 권한을 할당합니다. 

    ![Atlassian Cloud 사용자 만들기](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_16.png)
 
4. **사용자 만들기** 단추를 클릭하면 사용자에게 전자 메일 초대장을 보내고 이 초대를 수락하면 해당 사용자가 시스템에서 활성화됩니다. 

### <a name="assig-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure SSO를 사용할 수 있도록 Britta Simon에게 Atlassian Cloud에 대한 액세스 권한을 부여합니다.

![사용자 할당][200] 

**Britta Simon을 Atlassian Cloud에 할당하려면 다음 단계를 수행합니다.**

1. 클래식 포털에서 응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램** 을 클릭합니다.

    ![사용자 할당][201] 

2. 응용 프로그램 목록에서 **Atlassian Cloud**를 선택합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_50.png) 

3. 위쪽의 메뉴에서 **사용자**를 클릭합니다.

    ![사용자 할당][203]

4. 사용자 목록에서 **Britta Simon**을 선택합니다.

5. 아래쪽 도구 모음에서 **할당**을 클릭합니다.

    ![사용자 할당][205]

### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD SSO 구성을 테스트합니다.

[액세스 패널]에서 [Atlassian Cloud] 타일을 클릭하면 Atlassian Cloud 응용 프로그램에 자동으로 로그인됩니다.


## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On이란 무엇입니까?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_205.png

