---
title: "자습서: ClickTime과 Azure Active Directory 통합 | Microsoft Docs"
description: "Azure Active Directory에서 ClickTime을 사용하여 Single Sign-On, 자동화된 프로비전 등을 사용하도록 설정하는 방법을 알아봅니다."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: d437b5ab-4d71-4c13-96d0-79018cebbbd4
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/15/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 786ffe97c794aa44dc5d2b8e1a9597387752409c


---
# <a name="tutorial-azure-active-directory-integration-with-clicktime"></a>자습서: ClickTime과 Azure Active Directory 통합
이 자습서에서는 Azure AD(Azure Active Directory)와 ClickTime을 통합하는 방법에 대해 알아봅니다.

ClickTime을 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

* ClickTime에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
* 사용자가 해당 Azure AD 계정으로 ClickTime(Single Sign-on)에 자동으로 로그온되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure 클래식 포털에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On](active-directory-appssoaccess-whatis.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건
ClickTime과 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독
* ClickTime Single Sign-On이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.
> 
> 

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

* 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 않도록 합니다.
* Azure AD 평가판 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 얻을 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다.

이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 ClickTime 추가
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-clicktime-from-the-gallery"></a>갤러리에서 ClickTime 추가
이 섹션은 ClickTime에 응용 프로그램 통합을 사용하도록 설정하는 방법을 간략하게 설명하기 위한 것입니다.

### <a name="to-enable-the-application-integration-for-clicktime-perform-the-following-steps"></a>ClickTime에 응용 프로그램 통합을 사용하도록 설정하려면 다음 단계를 수행합니다.
1. Azure 클래식 포털의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.
   
   ![Active Directory](./media/active-directory-saas-clicktime-tutorial/tic700993.png "Active Directory")
2. **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.
3. 응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램** 을 클릭합니다.
   
   ![응용 프로그램](./media/active-directory-saas-clicktime-tutorial/tic700994.png "Applications")
4. 페이지 맨 아래에 있는 **추가** 를 클릭합니다.
   
   ![응용 프로그램 추가](./media/active-directory-saas-clicktime-tutorial/tic749321.png "Add application")
5. **수행할 작업** 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.
   
   ![갤러리에서 응용 프로그램 추가](./media/active-directory-saas-clicktime-tutorial/tic749322.png "Add an application from gallerry")
6. **검색 상자**에 **ClickTime**을 입력합니다.
   
   ![응용 프로그램 갤러리](./media/active-directory-saas-clicktime-tutorial/tic777275.png "Application gallery")
7. 결과 창에서 **ClickTime**을 선택하고 **완료**를 클릭하여 응용 프로그램을 추가합니다.
   
   ![ClickTime](./media/active-directory-saas-clicktime-tutorial/tic777276.png "ClickTime")

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD Single Sign-on 구성 및 테스트
이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 ClickTime에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 ClickTime 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 ClickTime의 관련 사용자 간에 연결이 형성되어야 합니다.

이 연결 관계는 Azure AD의 **사용자 이름** 값을 ClickTime의 **Username** 값으로 할당하여 설정합니다.

ClickTime에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configuring-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#creating-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
3. **[ClickTime 테스트 사용자 만들기](#creating-a-clicktime-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 ClickTime에 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assigning-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 구성이 작동하는지 확인합니다.

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성
이 섹션은 사용자가 SAML 프로토콜 기반 페더레이션을 사용하여 Azure AD의 계정으로 ClickTime에 인증할 수 있게 하는 방법을 간략하게 설명하기 위한 것입니다.  

> [!IMPORTANT]
> ClickTime 테넌트에 대한 single sign-on을 구성하기 위해 먼저 ClickTime 기술 지원에 문의하여 이 기능을 사용하도록 설정합니다.
> 
> 

**ClickTime에서 Azure AD Single Sign-on을 구성하려면 다음 단계를 수행합니다.**

1. Azure 클래식 포털의 **ClickTime** 응용 프로그램 통합 페이지에서 **Single Sign-On 구성**을 클릭하여 **Single Sign-On 구성** 대화 상자를 엽니다.
   
   ![Single Sign-On 사용](./media/active-directory-saas-clicktime-tutorial/tic777277.png "Enable single sign-on")
2. **ClickTime에 대한 사용자 로그온 방법을 선택하세요.** 페이지에서 **Microsoft Azure AD Single Sign-on**을 선택하고 **다음**을 클릭합니다.
   
   ![Single Sign-On 구성](./media/active-directory-saas-clicktime-tutorial/tic777278.png "Configure single sign-on")
3. **앱 설정 구성** 대화 상자 페이지에서 다음 단계를 수행합니다.
   
    ![Single Sign-On 구성](./media/active-directory-saas-clicktime-tutorial/tic777286.png) 
   
    a. **식별자** 텍스트 상자에 다음 패턴으로 URL을 입력합니다. **https://app.clicktime.com/sp/**
   
    b. **회신 URL** 텍스트 상자에 다음 패턴으로 URL을 입력합니다. **https://app.clicktime.com/Login/**
   
    c. click **다음**
4. **ClickTime에서 Single Sign-On 구성** 페이지에서 인증서를 다운로드하려면 **인증서 다운로드**를 클릭한 다음 컴퓨터에 인증서 파일을 저장합니다.
   
   ![Single Sign-On 구성](./media/active-directory-saas-clicktime-tutorial/tic777279.png "Configure single sign-on")
5. 다른 웹 브라우저 창에서 ClickTime 회사 사이트에 관리자로 로그인합니다.
6. 위쪽에 도구 모음에서 **기본 설정**을 클릭한 다음 **보안 설정** 클릭합니다.
7. **Single Sign-On 선호도** 섹션에서 다음 단계를 수행합니다.
   
   ![보안 설정](./media/active-directory-saas-clicktime-tutorial/tic777280.png "Security Settings")
   
   a.  **Azure AD**와 SSO(Single Sign-On)를 사용하여 로그인 **허용**을 선택합니다.
   
   b.  Azure 클래식 포털의 **ClickTime에 대한 Single Sign-On 구성** 대화 상자 페이지에서 **Single Sign-On 서비스 URL** 값을 복사한 다음 **ID 공급자 끝점** 텍스트 상자에 붙여 넣습니다.
   
   c.  Base 64로 인코딩된 인증서를 **메모장**에서 열고, 내용을 복사한 다음 전체 인증서를 **X.509 인증서** 텍스트 상자에 붙여넣습니다.
   
   d.  **Save**를 클릭합니다.
8. Azure 클래식 포털에서 Single Sign-On 구성 확인을 선택하고 **완료**를 클릭하여 **Single Sign-On 구성** 대화 상자를 닫습니다.
   
   ![Single Sign-On 구성](./media/active-directory-saas-clicktime-tutorial/tic777281.png "Configure single sign-on")

## <a name="configuring-user-provisioning"></a>사용자 프로비전 구성
Azure AD 사용자가 ClickTime에 로그인할 수 있도록 하려면 ClickTime로 프로비전되어야 합니다.  
ClickTime의 경우 프로비전은 수동 작업입니다.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>사용자 계정을 프로비저닝하려면 다음 단계를 수행합니다.
1. **ClickTime** 테넌트에 로그인합니다.
2. 위쪽에 도구 모음에서 **회사**를 클릭한 다음 **피플**을 클릭합니다.
   
   ![사람](./media/active-directory-saas-clicktime-tutorial/tic777282.png "People")
3. **사람 추가**를 클릭합니다.
   
   ![사람 추가](./media/active-directory-saas-clicktime-tutorial/tic777283.png "Add Person")
4. 새 사람 섹션에서 다음 단계를 수행합니다.
   
   ![피플](./media/active-directory-saas-clicktime-tutorial/tic777284.png "People")
   
   a.  **전자 메일** 텍스트 상자에 Azure AD 계정의 전자 메일 주소를 입력합니다.
   
   b.  **전체 이름** 텍스트 상자에 Azure AD 계정의 이름을 입력합니다.  
   
   > [!NOTE]
   > 원한다면 새 사람 개체의 추가 속성을 설정할 수 있습니다.
   > 
   > 
   
   c.  **Save**를 클릭합니다.

> [!NOTE]
> 다른 ClickTime 사용자 계정 생성 도구 또는 ClickTime이 제공한 API를 사용하여 Azure AD 사용자 계정을 프로비전할 수 있습니다.
> 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당
이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 ClickTime에 대한 액세스 권한을 부여합니다.

![사용자 할당][200]

구성을 테스트하려면 응용 프로그램 사용을 허용하려는 Azure AD 사용자를 할당하여 액세스 권한을 부여해야 합니다.

**Britta Simon을 ClickTime에 할당하려면 다음 단계를 수행합니다.**

1. 클래식 포털에서 응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램** 을 클릭합니다.
   
    ![사용자 할당][201] 
2. 응용 프로그램 목록에서 **ClickTime**을 선택합니다.
   
    ![Single Sign-On 구성](./media/active-directory-saas-clicktime-tutorial/tutorial_clicktime_50.png) 
3. 위쪽의 메뉴에서 **사용자**를 클릭합니다.
   
    ![사용자 할당][203]
4. 사용자 목록에서 **Britta Simon**을 선택합니다.
5. 아래쪽 도구 모음에서 **할당**을 클릭합니다.
   
    ![사용자 할당][205]

## <a name="testing-single-sign-on"></a>Single Sign-On 테스트
이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 ClickTime 타일을 클릭하면 ClickTime 응용 프로그램에 자동으로 로그온됩니다.

## <a name="additional-resources"></a>추가 리소스
* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On이란 무엇입니까?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[200]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_203.png
[205]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_205.png



<!--HONumber=Nov16_HO3-->


