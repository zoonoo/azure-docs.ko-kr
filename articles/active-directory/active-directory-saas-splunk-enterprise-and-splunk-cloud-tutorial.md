---
title: "자습서: Splunk Enterprise and Splunk Cloud와 Azure Active Directory 통합 | Microsoft Docs"
description: "Azure Active Directory 및 Splunk Enterprise and Splunk Cloud 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: b3e2b4a9-749c-4895-813d-db46f8dfdbf8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/09/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: b78e9b7161207a74880e912241d5e965b353d1c5
ms.lasthandoff: 03/28/2017


---

# <a name="tutorial-azure-active-directory-integration-with-splunk-enterprise-and-splunk-cloud"></a>자습서: Splunk Enterprise and Splunk Cloud와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Splunk Enterprise and Splunk Cloud를 통합하는 방법에 대해 알아봅니다.

Splunk Enterprise and Splunk Cloud를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- Azure AD에서 사용자의 Splunk Enterprise and Splunk Cloud에 대한 액세스 권한을 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정으로 Splunk Enterprise and Splunk Cloud SSO(Single Sign-On)에 자동으로 로그온되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure 클래식 포털에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On](active-directory-appssoaccess-whatis.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건

Splunk Enterprise and Splunk Cloud와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- Splunk Enterprise and Splunk Cloud SSO가 사용하도록 설정된 구독


>[!NOTE]
>이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.
>

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 않도록 합니다.
- Azure AD 평가판 환경이 없으면 [1개월 평가판을 얻을](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다.


## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다.

이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 Splunk Enterprise and Splunk Cloud 추가
2. Azure AD SSO 구성 및 테스트


## <a name="add-splunk-enterprise-and-splunk-cloud-from-the-gallery"></a>갤러리에서 Splunk Enterprise and Splunk Cloud 추가
Splunk Enterprise and Splunk Cloud의 Azure AD 통합을 구성하려면 갤러리의 Splunk Enterprise and Splunk Cloud를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Splunk Enterprise and Splunk Cloud를 추가하려면 다음 단계를 수행합니다.**

1. **Azure 클래식 포털**의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.

    ![Active Directory][1]

2. **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.

3. 응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램** 을 클릭합니다.

    ![응용 프로그램][2]

4. 페이지 맨 아래에 있는 **추가** 를 클릭합니다.

    ![응용 프로그램][3]

5. **수행할 작업** 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.

    ![응용 프로그램][4]

6. 검색 상자에 **Splunk Enterprise or Splunk Cloud**를 입력합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_splunk_01.png)

7. 결과 창에서 **Splunk Enterprise and Splunk Cloud**를 선택하고 **완료**를 클릭하여 응용 프로그램을 추가합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_splunk_02.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트
이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 Splunk Enterprise and Splunk Cloud에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 Splunk Enterprise and Splunk Cloud 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 Splunk Enterprise and Splunk Cloud의 관련 사용자 간에 연결이 형성되어야 합니다.

이 연결 관계는 Azure AD의 **사용자 이름** 값을 Splunk Enterprise and Splunk Cloud의 **Username** 값으로 할당하여 설정합니다.

Splunk Enterprise and Splunk Cloud에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configuring-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#creating-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
3. **[Splunk Enterprise and Splunk Cloud 테스트 사용자 만들기](#creating-a-splunk-enterprise-and-splunk-cloud-test-user)** - Britta Simon의 Azure AD 표현과 연결되는 대응 사용자를 Splunk Enterprise and Splunk Cloud에 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assigning-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Single Sign-On 테스트](#testing-single-sign-on)** - 구성이 작동하는지 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 클래식 포털에서 Azure AD SSO를 사용하도록 설정하고 Splunk Enterprise and Splunk Cloud 응용 프로그램에서 SSO를 구성합니다.


**Splunk Enterprise and Splunk Cloud에서 Azure AD Single Sign-on을 구성하려면 다음 단계를 수행합니다.**

1. 클래식 포털의 **Splunk Enterprise and Splunk Cloud** 응용 프로그램 통합 페이지에서 **Single Sign-On 구성**을 클릭하여 **Single Sign-On 구성** 대화 상자를 엽니다.
     
    ![Single Sign-on 구성][6] 

2. **Splunk Enterprise and Splunk Cloud에 대한 사용자 로그온 방법을 선택하십시오.** 페이지에서 **Azure AD Single Sign-On**을 선택하고 **다음**을 클릭합니다.

    ![Single Sign-on 구성](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_splunk_03.png) 

3. **앱 설정 구성** 대화 상자 페이지에서 다음 단계를 수행합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_splunk_04.png) 
  1. **로그인 URL** 텍스트 상자에서 `https://<splunkserverUrl>/en-US/app/launcher/home` 패턴으로 사용자가 Splunk Enterprise and Splunk Cloud 응용 프로그램에 로그인하는 데 사용하는 URL을 입력합니다.
  2. **식별자** 텍스트 상자에 Splunk Server의 URL을 입력합니다.
  3. **회신 URL** 텍스트 상자에 다음 패턴으로 URL을 입력합니다. `https://<splunkserver>/saml/acs`
  4. **다음**을 클릭합니다.
 
4. **Splunk Enterprise and Splunk Cloud에서 Azure AD Single Sign-on 구성** 페이지에서 다음 단계를 수행합니다.

    ![Single Sign-on 구성](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_splunk_05.png)
  1. **메타데이터 다운로드**를 클릭하고 파일을 컴퓨터에 저장합니다.
  2. **다음**을 클릭합니다.

5. 응용 프로그램에 대해 구성된 SSO를 얻으려면 Splunk Enterprise and Splunk Cloud 지원 팀에 문의하고 다음을 제공하세요.

    * 다운로드한 **페더레이션 메타데이터**
6. 클래식 포털에서 Single Sign-On 구성 확인을 선택하고 **다음**을 클릭합니다.
    
    ![Azure AD Single Sign-On][10]

7. **Single Sign-On 확인** 페이지에서 **완료**를 클릭합니다.  
 
    ![Azure AD Single Sign-On][11]

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기
이 섹션에서는 클래식 포털에서 Britta Simon이라는 테스트 사용자를 만듭니다.

![Azure AD 사용자 만들기][20]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. **Azure 클래식 포털**의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/create_aaduser_09.png) 

2. **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.

3. 사용자 목록을 표시하려면 위쪽 메뉴에서 **사용자**를 클릭합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/create_aaduser_03.png) 

4. **사용자 추가** 대화 상자를 열려면 아래쪽 도구 모음에서 **사용자 추가**를 클릭합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/create_aaduser_04.png) 

5. **이 사용자에 대한 정보 입력** 대화 상자 페이지에서 다음 단계를 수행합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/create_aaduser_05.png) 
  1. 사용자 유형에서 조직의 새 사용자를 선택합니다.
  2. 사용자 이름 **텍스트 상자**에 **BrittaSimon**을 입력합니다.
  3. **다음**을 클릭합니다.

6.  **사용자 프로필** 대화 상자 페이지에서 다음 단계를 수행합니다.
  
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/create_aaduser_06.png) 
  1. **이름** 텍스트 상자에 **Britta**를 입력합니다.  
  2. **성** 텍스트 상자에 **Simon**을 입력합니다.
  3. **표시 이름** 텍스트 상자에 **Britta Simon**을 입력합니다.
  4. **역할** 목록에서 **사용자**를 선택합니다.
  5. **다음**을 클릭합니다.

7. **임시 암호 가져오기** 대화 상자 페이지에서 **만들기**를 클릭합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/create_aaduser_07.png) 

8. **임시 암호 가져오기** 대화 상자 페이지에서 다음 단계를 수행합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/create_aaduser_08.png) 
  1. **새 암호**값을 적어둡니다.
  2. **완료**를 클릭합니다.   

### <a name="create-a-splunk-enterprise-and-splunk-cloud-test-user"></a>Splunk Enterprise and Splunk Cloud 테스트 사용자 만들기

이 섹션에서는 Splunk Enterprise and Splunk Cloud에서 Britta Simon이라는 사용자를 만듭니다. Splunk Enterprise and Splunk Cloud 지원 팀과 협의하여 Splunk Enterprise and Splunk Cloud 플랫폼에 사용자를 추가하세요.


### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure SSO를 사용할 수 있도록 Britta Simon에게 Splunk Enterprise and Splunk Cloud에 대한 액세스 권한을 부여합니다.

![사용자 할당][200] 

**Splunk Enterprise and Splunk Cloud에 Britta Simon을 할당하려면 다음 단계를 수행합니다.**

1. 클래식 포털에서 응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램** 을 클릭합니다.

    ![사용자 할당][201] 

2. 응용 프로그램 목록에서 **Splunk Enterprise and Splunk Cloud**를 선택합니다.

    ![Single Sign-on 구성](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_splunk_50.png) 

3. 위쪽의 메뉴에서 **사용자**를 클릭합니다.

    ![사용자 할당][203]

4. 사용자 목록에서 **Britta Simon**을 선택합니다.

5. 아래쪽 도구 모음에서 **할당**을 클릭합니다.

    ![사용자 할당][205]

### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD SSO 구성을 테스트합니다.

액세스 패널에서 Splunk Enterprise and Splunk Cloud 타일을 클릭하면 Splunk Enterprise and Splunk Cloud 응용 프로그램에 자동으로 로그온됩니다.


## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On이란 무엇입니까?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_205.png

