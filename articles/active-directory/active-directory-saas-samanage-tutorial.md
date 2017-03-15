---
title: "자습서: Samanage와 Azure Active Directory 통합 | Microsoft Docs"
description: "Azure Active Directory에서 Samanage를 사용하여 Single Sign-On, 자동화된 프로비전 등을 사용하도록 설정하는 방법을 알아봅니다."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: f0db4fb0-7eec-48c2-9c7a-beab1ab49bc2
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/24/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: eacd69207e21a5a7c3ef71902bc6c20c37f2003c
ms.openlocfilehash: 0936f7e0b5e1967b629b31cac3c817d04a75287f
ms.lasthandoff: 03/01/2017


---
# <a name="tutorial-azure-active-directory-integration-with-samanage"></a>자습서: Samanage와 Azure Active Directory 통합
이 자습서에서는 Samanage와 Azure AD(Azure Active Directory)를 통합하는 방법을 보여 줍니다.

Samanage를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

* Samanage에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
* 사용자가 해당 Azure AD 계정으로 Samanage SSO(Single Sign-On)에 자동으로 로그인하도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure 클래식 포털에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On](active-directory-appssoaccess-whatis.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건
Samanage와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

* 유효한 Azure 구독
* Samanage의 테넌트

>[!NOTE]
>이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다. 
> 

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

* 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 않도록 합니다.
* Azure AD 평가판 환경이 없으면 [1개월 평가판을 얻을](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서는 테스트 환경에서 Azure AD SSO를 테스트하는 데 도움을 주기 위해 제공되었습니다.

이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 Samanage 추가
2. Azure AD SSO 구성 및 테스트

## <a name="adding-samanage-from-the-gallery"></a>갤러리에서 Samanage 추가
Samanage의 Azure AD 통합을 구성하려면 갤러리의 Samanage를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Samanage를 추가하려면 다음 단계를 수행합니다.**

1. Azure 클래식 포털의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.
   
   ![Active Directory](./media/active-directory-saas-samanage-tutorial/tutorial_general_01.png "Active Directory")
2. **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.
3. 응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램** 을 클릭합니다.
   
   ![응용 프로그램](./media/active-directory-saas-samanage-tutorial/tutorial_general_02.png "응용 프로그램")
4. 페이지 맨 아래에 있는 **추가** 를 클릭합니다.
   
   ![응용 프로그램 추가](./media/active-directory-saas-samanage-tutorial/tutorial_general_03.png "응용 프로그램 추가")
5. **수행할 작업** 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.
   
   ![갤러리에서 응용 프로그램 추가](./media/active-directory-saas-samanage-tutorial/tutorial_general_04.png "갤러리에서 응용 프로그램 추가")
6. **검색 상자**에 **Samanage**를 입력합니다.
   
   ![응용 프로그램 갤러리](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_01.png "응용 프로그램 갤러리")
7. 결과 창에서 **Samanage**를 선택하고 **완료**를 클릭하여 응용 프로그램을 추가합니다.
   
   ![Samanage](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_02.png "Samanage")

## <a name="configure-and-test-azure-ad-sso"></a>Azure AD SSO 구성 및 테스트
이 섹션은 "Britta Simon"이라는 테스트 사용자를 기반으로 Samanage에서 Azure AD SSO를 구성하고 테스트하는 방법을 보여 주기 위해 작성되었습니다.

SSO가 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 Samanage 사용자가 누군지 알고 있어야 합니다. 즉, Azure AD 사용자와 Samanage의 관련 사용자 간에 연결이 형성되어야 합니다.

이 연결 관계는 Azure AD의 **사용자 이름** 값을 Samanage의 **Username** 값으로 할당하여 설정합니다.

Samanage에서 Azure AD SSO를 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configuring-azure-ad-single-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#creating-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-on 테스트하는 데 사용합니다.
3. **[Samanage 테스트 사용자 만들기](#creating-a-Samanage-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 Samanage에 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assigning-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Single Sign-On 테스트](#testing-single-sign-on)** - 구성이 작동하는지 확인합니다.

### <a name="configuring-azure-ad-sso"></a>Azure AD SSO 구성
이 섹션에서는 클래식 포털에서 Azure AD Single Sign-On을 사용하도록 설정하고 Samanage 응용 프로그램에서 SSO를 구성합니다.

**Samanage에서 Azure AD SSO를 구성하려면 다음 단계를 수행합니다.**

1. Azure 클래식 포털의 **Samanage** 응용 프로그램 통합 페이지에서 **Single Sign-On 구성**을 클릭하여 **Single Sign-On 구성** 대화 상자를 엽니다.
   
   ![Single Sign-On 구성](./media/active-directory-saas-samanage-tutorial/tutorial_general_05.png "Single Sign-On 구성")
2. **Samanage에 대한 사용자 로그온 방법을 선택하세요.** 페이지에서 **Microsoft Azure AD Single Sign-On**을 선택하고 **다음**을 클릭합니다.
   
   ![Microsoft Azure AD Single Sign-on](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_03.png "Microsoft Azure AD Single Sign-on")
3. 앱 설정 구성 대화 상자 페이지에서 다음 단계를 수행합니다.
   
   ![앱 URL 구성](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_04.png "앱 URL 구성") 
 1. **로그온 URL** 텍스트 상자에서 `https://<Company Name>.samanage.com/saml_login/<Company Name>` 패턴을 사용하여 URL을 입력합니다. 
 2. **다음**을 클릭합니다.
 
   >[!NOTE]
   >이러한 값은 실제 값이 아닙니다. 이러한 값을 실제 로그온 URL로 업데이트해야 합니다. 이러한 값을 가져오려면 8.c단계에서 자세한 내용을 참조하거나 Samanage에 문의하십시오.
   > 
 
4. **Samanage에서 Single Sign-On 구성** 페이지에서 **인증서 다운로드**를 클릭한 다음 컴퓨터에 인증서 파일을 저장합니다.
   
   ![Single Sign-On 구성](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_05.png "Single Sign-On 구성")
5. 다른 웹 브라우저 창에서 Samanage 회사 사이트에 관리자로 로그인합니다.
6. **대시보드**를 클릭하고 왼쪽 창에서 **설정**을 선택합니다.
   
   ![대시보드](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_001.png "대시보드")
7. **Single Sign-On**을 클릭합니다.
   
   ![Single Sign-On](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_002.png "Single Sign-On")
8. **SAML을 사용하여 로그인** 섹션으로 이동하고 다음 단계를 수행합니다.
   
   ![SAML을 사용하여 로그인](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_003.png "SAML을 사용하여 로그인")
 1. **SAML로 Single Sign-On 사용**을 클릭합니다.  
 2. **ID 공급자 URL** 텍스트 상자에 Azure AD 응용 프로그램 구성 마법사에서 나온 **ID 공급자 ID** 값을 저장합니다.    
 3. **로그인 URL**이 3단계의 **로그온 URL**과 일치하는지 확인합니다.
 4. **로그아웃 URL** 텍스트 상자에 Azure AD 응용 프로그램 구성 마법사의 **원격 로그아웃 ID** 값을 입력합니다.
 5. **SAML 발급자** 텍스트 상자에 ID 공급자에서 설정한 앱 ID URI를 입력합니다.
 6. Base&64;로 인코딩된 인증서를 메모장에서 열고, 내용을 클립보드에 복사한 다음 **아래 ID 공급자 x.509 인증서 붙여 넣기** 텍스트 상자에 붙여 넣습니다.
 7. **Samanage에 없는 경우 사용자 만들기**를 클릭합니다.
 8. **업데이트**를 클릭합니다.
9. 클래식 포털에서 Single Sign-On 구성 확인을 선택하고 **다음**을 클릭합니다.
   
   ![Single Sign-On 구성](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_06.png "Single Sign-On 구성")
10. **Single Sign-On 확인** 페이지에서 **완료**를 클릭합니다.
    
    ![Single Sign-On 구성](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_07.png "Single Sign-On 구성")

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기
이 섹션의 목적은 클래식 포털에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-samanage-tutorial/create_aaduser_00.png)

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. **Azure 클래식 포털**의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.
   
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-samanage-tutorial/create_aaduser_01.png)
2. **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.
3. 사용자 목록을 표시하려면 위쪽 메뉴에서 **사용자**를 클릭합니다.
   
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-samanage-tutorial/create_aaduser_02.png)
4. **사용자 추가** 대화 상자를 열려면 아래쪽 도구 모음에서 **사용자 추가**를 클릭합니다.
   
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-samanage-tutorial/create_aaduser_03.png)
5. **이 사용자에 대한 정보 입력** 대화 상자 페이지에서 다음 단계를 수행합니다.
   
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-samanage-tutorial/create_aaduser_04.png)
 1. 사용자 유형에서 조직의 새 사용자를 선택합니다. 
 2. 사용자 이름 **텍스트 상자**에 **BrittaSimon**을 입력합니다. 
 3. **다음**을 클릭합니다.
6. **사용자 프로필** 대화 상자 페이지에서 다음 단계를 수행합니다.
   
   ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-samanage-tutorial/create_aaduser_05.png) 
 1. **이름** 텍스트 상자에 **Britta**를 입력합니다.   
 2. **성** 텍스트 상자에 **Simon**을 입력합니다. 
 3. **표시 이름** 텍스트 상자에 **Britta Simon**을 입력합니다. 
 4. **역할** 목록에서 **사용자**를 선택합니다. 
 5. **다음**을 클릭합니다.
7. **임시 암호 가져오기** 대화 상자 페이지에서 **만들기**를 클릭합니다.
   
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-samanage-tutorial/create_aaduser_06.png)
8. **임시 암호 가져오기** 대화 상자 페이지에서 다음 단계를 수행합니다.
   
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-samanage-tutorial/create_aaduser_07.png)  
 1. **새 암호**값을 적어둡니다. 
 2. **완료**를 클릭합니다.   

### <a name="create-a-samanage-test-user"></a>Samanage 테스트 사용자 만들기
Azure AD 사용자가 Samanage에 로그인할 수 있도록 하려면 Samanage로 프로비전되어야 합니다. Samanage의 경우 프로비전은 수동 작업입니다.

**사용자 계정을 프로비전하려면 다음 단계를 수행합니다.**

1. Samanage 회사 사이트에 관리자 권한으로 로그인합니다.
2. **대시보드**를 클릭하고 왼쪽 창에서 **설정**을 선택합니다.
   
   ![설치](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_001.png "설치")
3. **사용자** 탭을 클릭합니다.
   
   ![사용자](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_006.png "사용자")
4. **새 사용자**를 클릭합니다.
   
   ![새 사용자](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_007.png "새 사용자")
5. 프로비전할 Azure AD 계정의 **이름** 및 **이메일 주소**를 입력하고 **사용자 만들기**를 클릭합니다.
   
   ![사용자 만들기](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_008.png "사용자 만들기")
   
   >[!NOTE]
   >AAD 계정 보유자에게 전자 메일이 발송되며 여기에 포함된 링크를 클릭하여 계정을 확인하면 계정이 활성화됩니다. 다른 Samanage 사용자 계정 생성 도구 또는 Samanage가 제공한 API를 사용하여 AAD 사용자 계정을 프로비전할 수 있습니다.
   >  

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당
이 섹션은 Britta Simon에게 Samanage에 대한 액세스 권한을 부여하여 Azure SSO를 사용할 수 있도록 하기 위한 것입니다.

![사용자 할당](./media/active-directory-saas-samanage-tutorial/assign_aaduser_00.png "사용자 할당")

**Britta Simon을 Samanage에 할당하려면 다음 단계를 수행합니다.**

1. 클래식 포털에서 응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램** 을 클릭합니다.
   
    ![사용자 할당](./media/active-directory-saas-samanage-tutorial/assign_aaduser_01.png "사용자 할당")
2. 응용 프로그램 목록에서 **Samanage**를 선택합니다.
   
    ![Single Sign-On 구성](./media/active-directory-saas-samanage-tutorial/tutorial_samanage_08.png)
3. 위쪽의 메뉴에서 **사용자**를 클릭합니다.
   
    ![사용자 할당](./media/active-directory-saas-samanage-tutorial/assign_aaduser_02.png "사용자 할당")
4. 사용자 목록에서 **Britta Simon**을 선택합니다.
5. 아래쪽 도구 모음에서 **할당**을 클릭합니다.
   
    ![사용자 할당](./media/active-directory-saas-samanage-tutorial/assign_aaduser_03.png "사용자 할당")

### <a name="test-single-sign-on"></a>Single Sign-On 테스트
이 섹션은 액세스 패널을 사용하여 Azure AD SSO 구성을 테스트하기 위한 것입니다.

액세스 패널에서 Samanage 타일을 클릭하면 Samanage 응용 프로그램에 자동으로 로그온됩니다.

## <a name="additional-resources"></a>추가 리소스
* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On이란 무엇입니까?](active-directory-appssoaccess-whatis.md)


