---
title: "자습서: Questetra BPM Suite와 Azure Active Directory 통합 | Microsoft 문서"
description: "Azure Active Directory와 Questetra BPM Suite 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: fb6d5b73-e491-4dd2-92d6-94e5aba21465
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: a17069b9992e36b36bad71941b0f8062d7594e95
ms.openlocfilehash: 74959c5694ebb2a6d83b76b76f69d2fd0bb1d885


---
# <a name="tutorial-azure-active-directory-integration-with-questetra-bpm-suite"></a>자습서: Questetra BPM Suite와 Azure Active Directory 통합
이 자습서에서는 Questetra BPM Suite와 Azure AD(Azure Active Directory)를 통합하는 방법을 보여 줍니다.  
Questetra BPM Suite를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다. 

* Azure AD에서 사용자의 Questetra BPM Suite에 대한 액세스 권한을 제어할 수 있습니다. 
* 사용자가 Azure AD 계정으로 Questetra BPM Suite에 자동으로 로그온(Single Sign-on)할 수 있도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure 클래식 포털에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On](active-directory-appssoaccess-whatis.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건
Questetra BPM Suite와 Azure AD의 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독
* [Questetra BPM Suite](https://senbon-imadegawa-988.questetra.net/) Single-Sign-On이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.
> 
> 

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

* 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 않도록 합니다.
* Azure AD 평가판 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 얻을 수 있습니다. 

## <a name="scenario-description"></a>시나리오 설명
이 자습서는 테스트 환경에서 Azure AD Single Sign-on을 테스트하는 데 도움을 주기 위해 제공되었습니다.  
이 자습서에 설명된 시나리오는 다음 세 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 Questetra BPM Suite 추가 
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-questetra-bpm-suite-from-the-gallery"></a>갤러리에서 Questetra BPM Suite 추가
Questetra BPM Suite가 Azure AD에 통합되도록 구성하려면 갤러리의 Questetra BPM Suite를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Questetra BPM Suite를 추가하려면 다음 단계를 수행합니다.**

1. **Azure 클래식 포털**의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다. 
   
    ![Active Directory][1]

2. **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.

3. 응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램** 을 클릭합니다.
   
    ![응용 프로그램][2]

4. 페이지 맨 아래에 있는 **추가** 를 클릭합니다.
   
    ![응용 프로그램][3]

5. **수행할 작업** 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.
   
    ![응용 프로그램][4]

6. 검색 상자에 입력 **Questetra BPM Suite**를 입력합니다.
   
    ![응용 프로그램][5]

7. 결과 창에서 **Questetra BPM Suite**를 선택한 다음 **완료**를 클릭하여 응용 프로그램을 추가합니다.

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD Single Sign-on 구성 및 테스트
이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 Questetra BPM Suite에서 Azure AD Single Sign-on을 구성하고 테스트하는 방법을 설명합니다.

Single Sign-On이 작동하려면 Azure AD는 Questetra BPM Suite를 사용하려는 사용자가 Azure AD의 어떤 사용자인지 알아야 합니다. 즉 Azure AD 사용자와 Questetra BPM Suite 사용자 간 연결이 필요합니다.  
이 연결 관계는 Azure AD의 **사용자 이름** 값을 Questetra BPM Suite의 **Username** 값으로 할당하여 설정합니다.

Questetra BPM Suite에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 단계를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configuring-azure-ad-single-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#creating-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
3. **[Questetra BPM Suite 테스트 사용자 만들기](#creating-a-questetra-bpm-suite-test-user)** - Britta Simon이라는 Azure AD 사용자와 연결된 Questetra BPM Suite 사용자를 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assigning-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
5. **[Single Sign-On 테스트](#testing-single-sign-on)** - 구성이 작동하는지 확인합니다.

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성
이 섹션에서는 Azure 클래식 포털에서 Azure AD Single Sign-On을 사용하도록 설정하고 Questetra BPM Suite에서 Single Sign-On을 구성하는 방법을 설명합니다.

**Questetra BPM Suite에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.**

1. Azure 클래식 포털의 **Questetra BPM Suite** 응용 프로그램 통합 페이지에서 **Single Sign-On 구성**을 클릭하여 **Single Sign-On 구성** 대화 상자를 엽니다.
   
    ![Single Sign-on 구성][8]

2. **사용자가 Questetra BPM Suite에 로그인하는 방법을 선택하십시오.** 페이지에서 **Azure AD Single Sign-On**을 선택하고 **다음**을 클릭합니다.
   
    ![Azure AD Single Sign-On][9]

3. 다른 웹 브라우저 창에서 **Questetra BPM Suite** 회사 사이트에 관리자로 로그인합니다.

4. 위쪽의 메뉴에서 **System Settings**를 클릭합니다. 
   
    ![Azure AD Single Sign-On][10]

5. **SingleSignOnSAML** 페이지를 열려면 **SSO(SAML)**를 클릭합니다. 
   
    ![Azure AD Single Sign-On][11]

6. Azure 클래식 포털의 **앱 설정 구성** 대화 상자 페이지에서 다음 단계를 수행합니다. 
   
    ![앱 설정 구성][13]
   
    a. **Questetra BPM Suite** 회사 사이트의 [SP 정보] 섹션에서 **ACS URL**을 복사한 다음 **로그인 URL** 텍스트 상자에 붙여넣습니다.
   
    b. **Questetra BPM Suite** 회사 사이트의 [SP 정보] 섹션에서 **Entity ID**를 복사한 다음 **발급자 URL** 텍스트 상자에 붙여넣습니다.
   
    c. **Questetra BPM Suite** 회사 사이트의 [SP 정보] 섹션에서 **ACS URL**을 복사한 다음 **회신 URL** 텍스트 상자에 붙여넣습니다.
   
    d. **다음**을 클릭합니다.

7. **Questetra BPM Suite에서 Single Sign-On 구성** 페이지에서 **인증서 다운로드**를 클릭한 다음 인증서 파일을 컴퓨터에 로컬로 저장합니다.
   
    ![Single Sign-on 구성][14]

8. **Questetra BPM Suite** 회사 사이트에서 다음 단계를 수행합니다. 
   
    ![Single Sign-On 구성][15]
   
    a. **Single Sign-On 사용**을 선택합니다.
   
    b. Azure 클래식 포털에서 **발급자 URL** 값을 복사한 다음 **Entity ID** 텍스트 상자에 붙여넣습니다.
   
    c. Azure 클래식 포털에서 **Single Sign-On 서비스 URL** 값을 복사한 다음 **로그인 페이지 URL** 텍스트 상자에 붙여넣습니다.
   
    d. Azure 클래식 포털에서 **Single Sign-Out 서비스 URL** 값을 복사한 다음 **로그아웃 페이지 URL** 텍스트 상자에 붙여넣습니다.
   
    e. **NameID format** 텍스트 상자에서 **urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress**를 입력합니다.

    f. 다운로드한 인증서에서 Base-64로 인코딩된 파일을 만듭니다. 

    >[!TIP] 
    >자세한 내용은 [이진 인증서를 텍스트 파일로 변환하는 방법](http://youtu.be/PlgrzUZ-Y1o)을 참조하세요.

    g. Base-64로 인코딩된 인증서를 메모장에서 열고 내용을 클립보드에 복사한 다음 **인증서 유효성 검사** 텍스트 상자에 붙여넣습니다. 

    h. **Save**를 클릭합니다.

1. Azure 클래식 포털에서 Single Sign-On 구성 확인을 선택하고 **다음**을 클릭합니다. 
   
    ![Azure AD Connect의 정의][17]

2. **Single Sign-On 확인** 페이지에서 **완료**를 클릭합니다.  
   
    ![Azure AD Connect의 정의][18]


### <a name="creating-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기
이 섹션의 목적은 Azure 클래식 포털에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. **Azure 클래식 포털**의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.
   
    ![Azure AD 테스트 사용자 만들기][100] 

2. **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.

3. 사용자 목록을 표시하려면 위쪽 메뉴에서 **사용자**를 클릭합니다.
   
    ![Azure AD 테스트 사용자 만들기][101] 

4. **사용자 추가** 대화 상자를 열려면 아래쪽 도구 모음에서 **사용자 추가**를 클릭합니다. 
   
    ![Azure AD 테스트 사용자 만들기][102] 

5. **이 사용자에 대한 정보 입력** 대화 상자 페이지에서 다음 단계를 수행합니다.
   
    ![Azure AD 테스트 사용자 만들기][103]
   
    a. **사용자 유형**에서 **조직의 새 사용자**를 선택합니다.
   
    b. 사용자 이름 **텍스트 상자**에 **BrittaSimon**을 입력합니다.
   
    c. 다음을 클릭합니다.

6. **사용자 프로필** 대화 상자 페이지에서 다음 단계를 수행합니다. 
   
    ![Azure AD 테스트 사용자 만들기][104] 
   
    a. **이름** 텍스트 상자에 **Britta**를 입력합니다. 
   
    b. **성** 텍스트 상자에 **Simon**을 입력합니다.
   
    c. **표시 이름** 텍스트 상자에 **Britta Simon**을 입력합니다.
   
    d. **역할** 목록에서 **사용자**를 선택합니다.
   
    e. **다음**을 클릭합니다.

7. **임시 암호 가져오기** 대화 상자 페이지에서 **만들기**를 클릭합니다.
   
    ![Azure AD 테스트 사용자 만들기][105]  

8. **임시 암호 가져오기** 대화 상자 페이지에서 다음 단계를 수행합니다.
   
    ![Azure AD 테스트 사용자 만들기][106]   
   
    a. **새 암호**값을 적어둡니다.
   
    b. **완료**를 클릭합니다.   

### <a name="creating-a-questetra-bpm-suite-test-user"></a>Questetra BPM Suite 테스트 사용자 만들기
이 섹션에서는 Questetra BPM Suite에서 Britta Simon이라는 사용자를 만들어 보겠습니다.

**Questetra BPM Suite에서 Britta Simon이라는 사용자를 만들려면 다음 단계를 수행합니다.**

1. Questetra BPM Suite 회사 사이트에 관리자 권한으로 로그온합니다.
2. **시스템 설정 > 사용자 목록 > 새 사용자**로 이동합니다. 
3. 새 사용자 대화 상자에서 다음 단계를 수행합니다. 
   
    ![테스트 사용자 만들기][300] 
   
    a. **Name** 텍스트 상자에 Britta 사용자의 Azure AD 내 사용자 이름을 입력합니다.
   
    b. **메일** 텍스트 상자에 Azure AD 내에서 Britta의 사용자 이름을 입력합니다.
   
    c. **암호** 텍스트 상자에 암호를 입력합니다.

4. **새 사용자 추가**를 클릭합니다.

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당
이 섹션에서는 Britta Simon에게 Questetra BPM Suite에 대한 액세스 권한을 부여하여 Azure Single Sign-On을 사용할 수 있도록 하는 방법을 설명합니다.

![Azure AD Connect의 정의][200]

**Britta Simon을 Questetra BPM Suite에 할당하려면 다음 단계를 수행합니다.**

1. Azure 클래식 포털에서 응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램** 을 클릭합니다.
   
    ![Azure AD Connect의 정의][201]
2. 응용 프로그램 목록에서 **Questetra BPM Suite**를 선택합니다.
   
    ![Azure AD Connect의 정의][205]
3. 위쪽의 메뉴에서 **사용자**를 클릭합니다.
   
    ![Azure AD Connect의 정의][202]
4. 사용자 목록에서 **Britta Simon**을 선택합니다.
   
    ![Azure AD Connect의 정의][203]
5. 아래쪽 도구 모음에서 **할당**을 클릭합니다.
   
    ![Azure AD Connect의 정의][204]

### <a name="testing-single-sign-on"></a>Single Sign-On 테스트
이 섹션은 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트하기 위한 것입니다.  
액세스 패널에서 Questetra BPM Suite 타일을 클릭하면 Questetra BPM Suite 응용 프로그램에 자동으로 로그온됩니다.

## <a name="additional-resources"></a>추가 리소스
* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On이란 무엇입니까?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/questera_bpm_suite_01.png


[8]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_06.png
[9]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/questera_bpm_suite_02.png
[10]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/questera_bpm_suite_03.png
[11]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/questera_bpm_suite_04.png
[12]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/questera_bpm_suite_05.png
[13]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/questera_bpm_suite_06.png
[14]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/questera_bpm_suite_07.png
[15]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/questera_bpm_suite_08.png
[16]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/questera_bpm_suite_09.png
[17]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/questera_bpm_suite_10.png
[18]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_08.png


[100]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_09.png 
[101]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_10.png 
[102]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_11.png 
[103]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_12.png 
[104]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_13.png 
[105]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_14.png 
[106]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_15.png 


[200]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_16.png 
[201]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_17.png 
[202]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_18.png
[203]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_19.png
[204]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_20.png
[205]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/questera_bpm_suite_12.png

[300]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/questera_bpm_suite_11.png 



<!--HONumber=Nov16_HO4-->


