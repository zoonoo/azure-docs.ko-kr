---
title: "자습서: SuccessFactors와 Azure Active Directory 통합 | Microsoft 문서"
description: "Azure Active Directory에서 SuccessFactors를 사용하여 Single Sign-On, 자동화된 프로비전 등을 사용하도록 설정하는 방법을 알아봅니다."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 32bd8898-c2d2-4aa7-8c46-f1f5c2aa05f1
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/22/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 0837cb33bf438fb7fd9665d21d411f0170cdd393
ms.openlocfilehash: a0a101c34ffd4adb6816d9db8313d10b0ffbb3ae
ms.lasthandoff: 02/23/2017


---
# <a name="tutorial-azure-active-directory-integration-with-successfactors"></a>자습서: SuccessFactors와 Azure Active Directory 통합
이 자습서에서는 SuccessFactors와 Azure AD(Azure Active Directory)를 통합하는 방법을 보여 줍니다.

SuccessFactors를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

* SuccessFactors에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
* 사용자가 해당 Azure AD 계정으로 SuccessFactors에 자동으로 로그온(Single Sign-on)되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure 클래식 포털에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On](active-directory-appssoaccess-whatis.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건
SuccessFactors와의 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

* 유효한 Azure 구독
* SuccessFactors의 테넌트

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.
> 
> 

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

* 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 않도록 합니다.
* Azure AD 평가판 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서&1;개월 평가판을 얻을 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서는 테스트 환경에서 Azure AD Single Sign-on을 테스트하는 데 도움을 주기 위해 제공되었습니다.

이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 SuccessFactors 추가
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-successfactors-from-the-gallery"></a>갤러리에서 SuccessFactors 추가
SuccessFactors의 Azure AD 통합을 구성하려면 갤러리의 SuccessFactors를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 SuccessFactors를 추가하려면 다음 단계를 수행합니다.**

1. Azure 클래식 포털의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.
   
    ![Single Sign-On 구성][1]
2. **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.
3. 응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램** 을 클릭합니다.
   
    ![Single Sign-On 구성][2]
4. 페이지 맨 아래에 있는 **추가** 를 클릭합니다.
   
    ![응용 프로그램][3]
5. **수행할 작업** 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.
   
    ![Single Sign-On 구성][4]
6. **검색 상자**에서 **SuccessFactors**를 입력합니다.
   
    ![Single Sign-On 구성][5]
7. 결과 창에서 **SuccessFactors**를 선택한 다음 **완료**를 클릭하여 응용 프로그램을 추가합니다.
   
    ![Single Sign-On 구성][6]

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD Single Sign-on 구성 및 테스트
이 섹션은 "Britta Simon"이라는 테스트 사용자를 기반으로 SuccessFactors에서 Azure AD Single Sign-On을 구성하고 테스트하는 방법을 보여 주기 위해 작성되었습니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 SuccessFactors 사용자가 누군지 알고 있어야 합니다. 즉, Azure AD 사용자와 SuccessFactors의 관련 사용자 간에 연결이 형성되어야 합니다.

이 연결 관계는 Azure AD의 **사용자 이름** 값을 SuccessFactors의 **Username** 값으로 할당하여 설정합니다.

SuccessFactors에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#creating-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
3. **[SuccessFactors 테스트 사용자 만들기](#creating-a-successfactors-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 SuccessFactors에 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assigning-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 구성이 작동하는지 확인합니다.

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성
이 섹션에서는 클래식 포털에서 Azure AD Single Sign-On을 사용하도록 설정하고 SuccessFactors 응용 프로그램에서 Single Sign-On을 구성합니다.

**SuccessFactors에서 Azure AD Single Sign-on을 구성하려면 다음 단계를 수행합니다.**

1. Azure 클래식 포털의 **SuccessFactors** 응용 프로그램 통합 페이지에서 **Single Sign-On 구성**을 클릭하여 **Single Sign-On 구성** 대화 상자를 엽니다.
   
    ![Single Sign-On 구성][7]
2. **사용자가 SuccessFactors에 로그인하는 방법을 선택하십시오.** 페이지에서 **Microsoft Azure AD Single Sign-On**을 선택하고 **다음**을 클릭합니다.
   
    ![Single Sign-On 구성][8]
3. **앱 URL 구성** 페이지에서 다음 단계를 수행하고 **다음**을 클릭합니다.
   
    ![Single Sign-On 구성][9]
   
    a. **로그인 URL** 텍스트 상자에서 다음 패턴 중 하나를 사용하여 URL을 입력합니다. 
   
    |  |
    | --- |
    | `https://<company name>.successfactors.com/<company name>` |
    | `https://<company name>.sapsf.com/<company name>` |
    | `https://<company name>.successfactors.eu/<company name>` |
    | `https://<company name>.sapsf.eu` |
   
    b. **회신 URL** 텍스트 상자에서 다음 패턴 중 하나를 사용하여 URL을 입력합니다. 
   
    |  |
    | --- |
    | `https://<company name>.successfactors.com/<company name>` |
    | `https://<company name>.sapsf.com/<company name>` |
    | `https://<company name>.successfactors.eu/<company name>` |
    | `https://<company name>.sapsf.eu` |
    | `https://<company name>.sapsf.eu/<company name>` |
   
    c. **Next**를 클릭합니다. 

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 로그온 URL 및 회신 URL로 값을 업데이트해야 합니다. 이러한 값을 얻으려면 [SuccessFactors 지원 팀](https://www.successfactors.com/en_us/support.html)에 문의합니다.

1. **SuccessFactors에서 Single Sign-On 구성** 페이지에서 **인증서 다운로드**를 클릭한 다음 인증서 파일을 컴퓨터에 로컬로 저장합니다.
   
    ![Single Sign-On 구성][10]

2. 다른 웹 브라우저 창에서 **SuccessFactors 관리 포털** 에 관리자로 로그인합니다.

3. **응용 프로그램 보안**을 방문하고 **Single Sign On 기능**으로 이동합니다. 

4. **토큰 재설정**에 값을 입력하고 **토큰 저장**을 클릭하여 SAML SSO를 사용하도록 설정합니다.
   
    ![앱 쪽에서 Single Sign-On 구성][11]

    > [!NOTE] 
    > 이 값은 설정/해제 스위치로만 사용됩니다. 값이 저장된 경우 SAML SSO는 ON입니다. 빈 값이 저장된 경우 SAML SSO는 OFF입니다.

1. 아래 스크린샷으로 이동하고 다음 작업을 수행합니다.
   
    ![앱 쪽에서 Single Sign-On 구성][12]
   
    a. **SAML v2 SSO** 라디오 단추를 선택합니다.
   
    b. SAML 어설션 파티 이름(예: SAml 발급자 + 회사 이름)을 설정합니다.
   
    c. **SAML 발급자** 텍스트 상자에서 Azure AD 응용 프로그램 구성 마법사의 **발급자 URL** 값을 입력합니다.
   
    d. **필수 서명 요구**로 **응답(고객 생성/IdP/AP)**을 선택합니다.
   
    e. **SAML 플래그 사용**으로 **사용**을 선택합니다.
   
    f. **로그인 요청 서명(SF 생성/SP/RP)**으로 **아니요**를 선택합니다.
   
    g. **SAML 프로필**로 **브라우저/게시 프로필**을 선택합니다.
   
    h. **인증서 유효 기간 적용**으로 **아니요**를 선택합니다.
   
    i. 다운로드한 인증서 파일의 내용을 복사한 다음 **SAML 확인 인증서** 텍스트 상자에 붙여넣습니다.

    > [!NOTE] 
    > 인증서 내용에는 시작 인증서 및 끝 인증서 태그가 있어야 합니다.

1. SAML V2로 이동한 후 다음 단계를 수행합니다.
   
    ![앱 쪽에서 Single Sign-On 구성][13]
   
    a. **SP 시작 전역 로그아웃 지원**으로 **예**를 선택합니다.
   
    b. **전역 로그아웃 서비스 URL (LogoutRequest 대상)** 텍스트 상자에서 Azure AD 응용 프로그램 구성 마법사의 **원격 로그아웃 URL** 값을 입력합니다.
   
    c. **요청 SP는 모든 NameID 요소를 암호화해야 합니다.**로 **아니요**를 선택합니다.
   
    d. **NameID 형식**으로 **지정되지 않음**을 선택합니다.
   
    e. **SP 시작 로그인 사용(AuthnRequest)**으로 **예**를 선택합니다.
   
    f. **전사적 발급자로 보내기 요청** 텍스트 상자에서 Azure AD 응용 프로그램 구성 마법사의 **원격 로그인 URL** 값을 입력합니다.
2. 로그인 사용자 이름의 대/소문자를 구분하지 않으려면 이 단계를 수행합니다.
   
    a. a. **회사 설정**(아래쪽)을 방문합니다.
   
    b. **사용자 이름 대/소문자 구분하지 않음 사용**근처의 확인란을 선택합니다.
   
    c. **저장**을 클릭합니다.
   
    ![Single Sign-On 구성][29]

    > [!NOTE] 
    > 이 기능을 사용하려고 하면 시스템에서 중복되는 SAML 로그인 이름이 만들어지는지 확인합니다. 예를 들어 고객의 사용자 이름에 User1 및 user1이 있는 경우입니다. 대/소문자를 구분하지 않으면 이러한 중복 항목을 만듭니다. 시스템에서 오류 메시지를 제공하고 기능이 비활성화됩니다. 실제로 다른 철자가 되도록 고객은 사용자 이름 중 하나를 변경해야 합니다. 

1. Azure 클래식 포털에서 Single Sign-On 구성 확인을 선택하고 **완료**를 클릭하여 **Single Sign-On 구성** 대화 상자를 닫습니다.
   
    ![응용 프로그램][14]
2. **Single Sign-On 확인** 페이지에서 **완료**를 클릭합니다.
   
    ![응용 프로그램][15]

### <a name="creating-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기
이 섹션의 목적은 클래식 포털에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

![Azure AD 사용자 만들기][16]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. **Azure 클래식 포털**의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.
   
    ![Azure AD 테스트 사용자 만들기][17]
2. **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.
3. 사용자 목록을 표시하려면 위쪽 메뉴에서 **사용자**를 클릭합니다.
   
    ![Azure AD 테스트 사용자 만들기][18]
4. **사용자 추가** 대화 상자를 열려면 아래쪽 도구 모음에서 **사용자 추가**를 클릭합니다.
   
    ![Azure AD 테스트 사용자 만들기][19]
5. **이 사용자에 대한 정보 입력** 대화 상자 페이지에서 다음 단계를 수행합니다.
   
    ![Azure AD 테스트 사용자 만들기][20]
   
    a. 사용자 유형에서 조직의 새 사용자를 선택합니다.
   
    b. 사용자 이름 **텍스트 상자**에 **BrittaSimon**을 입력합니다.
   
    c. **다음**을 클릭합니다.
6. **사용자 프로필** 대화 상자 페이지에서 다음 단계를 수행합니다.
   
    ![Azure AD 테스트 사용자 만들기][21]
   
    a. **이름** 텍스트 상자에 **Britta**를 입력합니다.  
   
    b. **성** 텍스트 상자에 **Simon**을 입력합니다.
   
    c. **표시 이름** 텍스트 상자에 **Britta Simon**을 입력합니다.
   
    d. **역할** 목록에서 **사용자**를 선택합니다.
   
    e. **다음**을 클릭합니다.
7. **임시 암호 가져오기** 대화 상자 페이지에서 **만들기**를 클릭합니다.
   
    ![Azure AD 테스트 사용자 만들기][22]
8. **임시 암호 가져오기** 대화 상자 페이지에서 다음 단계를 수행합니다.
   
    ![Azure AD 테스트 사용자 만들기][23]
   
    a. **새 암호**값을 적어둡니다.
   
    b. **완료**를 클릭합니다.  

### <a name="creating-a-successfactors-test-user"></a>SuccessFactors 테스트 사용자 만들기
Azure AD 사용자가 SuccessFactors에 로그인할 수 있도록 하려면 SuccessFactors로 프로비전되어야 합니다.  
SuccessFactors의 경우 프로비전은 수동 작업입니다.

SuccessFactors에서 사용자를 생성하려면 [SuccessFactors 지원 팀](https://www.successfactors.com/en_us/support.html)에 문의해야 합니다.

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당
이 섹션은 Britta Simon에게 SuccessFactors에 대한 액세스 권한을 부여하여 Single Sign-On을 사용할 수 있도록 하기 위한 것입니다.

![사용자 할당][24]

**Britta Simon을 SuccessFactors에 할당하려면 다음 단계를 수행합니다.**

1. 클래식 포털에서 응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램** 을 클릭합니다.
   
    ![사용자 할당][25]
2. 응용 프로그램 목록에서 **SuccessFactors**를 선택합니다.
   
    ![Single Sign-On 구성][26]
3. 위쪽의 메뉴에서 **사용자**를 클릭합니다.
   
    ![사용자 할당][27]
4. 사용자 목록에서 **Britta Simon**을 선택합니다.
5. 아래쪽 도구 모음에서 **할당**을 클릭합니다.
   
    ![사용자 할당][28]

### <a name="testing-single-sign-on"></a>Single Sign-On 테스트
이 섹션은 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트하기 위한 것입니다.

액세스 패널에서 SuccessFactors 타일을 클릭하면 SuccessFactors 응용 프로그램에 자동으로 로그온됩니다.

## <a name="additional-resources"></a>추가 리소스
* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On이란 무엇입니까?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[0]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_00.png
[1]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_01.png
[6]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_02.png
[7]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_03.png
[8]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_04.png
[9]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_05.png
[10]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_06.png

[11]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_07.png
[12]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_08.png
[13]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_09.png
[14]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_05.png
[15]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_06.png

[16]: ./media/active-directory-saas-successfactors-tutorial/create_aaduser_00.png
[17]: ./media/active-directory-saas-successfactors-tutorial/create_aaduser_01.png
[18]: ./media/active-directory-saas-successfactors-tutorial/create_aaduser_02.png
[19]: ./media/active-directory-saas-successfactors-tutorial/create_aaduser_03.png
[20]: ./media/active-directory-saas-successfactors-tutorial/create_aaduser_04.png
[21]: ./media/active-directory-saas-successfactors-tutorial/create_aaduser_05.png
[22]: ./media/active-directory-saas-successfactors-tutorial/create_aaduser_06.png
[23]: ./media/active-directory-saas-successfactors-tutorial/create_aaduser_07.png

[24]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_07.png
[25]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_08.png
[26]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_11.png
[27]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_09.png
[28]: ./media/active-directory-saas-successfactors-tutorial/tutorial_general_10.png
[29]: ./media/active-directory-saas-successfactors-tutorial/tutorial_successfactors_10.png

