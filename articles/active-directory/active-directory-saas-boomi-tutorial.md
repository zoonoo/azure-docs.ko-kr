---
title: "자습서: Boomi와 Azure Active Directory 통합 | Microsoft Docs"
description: "Azure Active Directory와 Boomi 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
editor: na
ms.assetid: 8e05afa9-2eda-4975-a0cc-6d408065860f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 6a60db808388bf1fdf9441518920f2eb4e5fcc4d
ms.openlocfilehash: bafbfb710d8cdb370d21d9299c447a0dfed3c468


---
# <a name="tutorial-azure-active-directory-integration-with-boomi"></a>자습서: Boomi와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Boomi를 통합하는 방법에 대해 알아봅니다.

Boomi를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- Boomi에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정으로 Boomi에 자동으로 로그온(Single Sign-on)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure 클래식 포털에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On](active-directory-appssoaccess-whatis.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건

Boomi와의 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- Boomi Single Sign-On이 설정된 구독


> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.


이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 않도록 합니다.
- Azure AD 평가판 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서&1;개월 평가판을 얻을 수 있습니다.


## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 Boomi 추가
2. Azure AD Single Sign-on 구성 및 테스트


## <a name="adding-boomi-from-the-gallery"></a>갤러리에서 Boomi 추가
Boomi의 Azure AD 통합을 구성하려면 갤러리의 Boomi를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Boomi를 추가하려면 다음 단계를 수행합니다.**

1. **Azure 클래식 포털**의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다. 

    ![Active Directory][1]

2. **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.

3. 응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램** 을 클릭합니다.

    ![응용 프로그램][2]

4. 페이지 맨 아래에 있는 **추가** 를 클릭합니다.

    ![응용 프로그램][3]

5. **수행할 작업** 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.

    ![응용 프로그램][4]

6. 검색 상자에 **Boomi**를 입력합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_01.png)

7. 결과 창에서 **Boomi**를 선택하고 **완료**를 클릭하여 응용 프로그램을 추가합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_02.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD Single Sign-on 구성 및 테스트
이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 Boomi에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 Boomi 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 Boomi의 관련 사용자 간에 연결이 형성되어야 합니다.

이 연결 관계는 Azure AD의 **사용자 이름** 값을 Boomi의 **Username** 값으로 할당하여 설정합니다.

Boomi에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configuring-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#creating-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
3. **[Boomi 테스트 사용자 만들기](#creating-a-boomi-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 Boomi에 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assigning-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 구성이 작동하는지 확인합니다.

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션은 Azure 클래식 포털에서 Azure AD Single Sign-on을 사용하도록 설정하고 Boomi 응용 프로그램에서 Single Sign-On을 구성하는 방법을 설명하기 위한 것입니다.

Boomi 응용 프로그램은 특정 형식의 SAML 어설션이 필요하므로, 사용자의 페더레이션 ID를 사용하여 NameIdentifier 특성 값을 설정해야 합니다. 기본적으로 Azure AD는 NameIdentifier 특성으로 UserPrincipalName을 사용합니다. 하지만 성공적인 통합을 위해서는 Boomi에서 사용자의 페더레이션 ID와 일치하도록 이 값을 조정해야 합니다. 아래 스크린샷에 표시된 것처럼 "**특성**" 탭에서 이를 변경할 수 있습니다. 올바른 매핑을 완료해야만 통합이 작동합니다.

![Single Sign-on 구성](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_51.png)

**Boomi에서 Azure AD Single Sign-on을 구성하려면 다음 단계를 수행합니다.**

1. 클래식 포털의 **Boomi** 응용 프로그램 통합 페이지에서 **Single Sign-On 구성**을 클릭하여 **Single Sign-On 구성** 대화 상자를 엽니다.

    ![Single Sign-on 구성][6] 

2. **Domo에 대한 사용자 로그온 방법 선택** 페이지에서 **Azure AD Single Sign-On**을 선택하고 **다음**을 클릭합니다.
 
    ![Single Sign-on 구성](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_03.png)

3. **앱 설정 구성** 대화 상자 페이지에서 다음 단계를 수행합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_04.png)

    a. **회신 URL** 텍스트 상자에 다음 패턴으로 URL을 입력합니다.`https://platform.boomi.com/sso/<account name>/saml`

    b. **다음**을 클릭합니다.

    > [!NOTE] 
    > 이러한 값은 실제 값이 아닙니다. 이 값은 실제 회신 URL로 업데이트해야 합니다. 이 값을 가져오려면 Boomi 지원 팀에 문의합니다.

4. **Boomi에서 Single Sign-On 구성** 페이지에서 **인증서 다운로드**를 클릭한 다음 파일을 컴퓨터에 저장합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_05.png) 

    > [!NOTE]
    > 응답에 있는 NameID 클레임 값은 Boomi 시스템에 구성된 페더레이션 ID와 일치해야 합니다. 따라서 Boomi 지원 팀과 협력하여 조직의 적절한 사용자 ID를 페더레이션 ID로 매핑하세요. 기본적으로 Azure AD는 NameIdentifier를 UPN 값으로 설정합니다. 아래 스크린샷에 표시된 것처럼 특성 탭에서 이를 변경할 수 있습니다. 올바른 매핑을 완료해야만 통합이 작동합니다. 
     
    ![Single Sign-On 구성](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_51.png)

5. 다른 웹 브라우저 창에서 Boomi 회사 사이트에 관리자로 로그인합니다. 

6. **회사 이름**, **설정**으로 이동합니다.

7. **SSO 옵션** 탭을 클릭하고 아래 단계를 수행합니다.

    ![앱 쪽에서 Single Sign-On 구성](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_06.png)

    a. **SAML Single Sign-On 사용** 확인란을 선택합니다.

    b. **가져오기**를 클릭하여 Azure AD에서 다운로드한 인증서를 **ID 공급자 인증서**로 업로드합니다.
    
    c. **ID 공급자 로그인 URL** 텍스트 상자에 Azure AD 응용 프로그램 구성 마법사에서 나온 **원격 로그인 URL** 값을 저장합니다.

    ㄹ. **페더레이션 ID 위치**로 **페더레이션 ID는 제목의 NameID 요소입니다** 라디오 단추를 선택합니다. 

    e. **저장** 단추를 클릭합니다.

8. 클래식 포털에서 Single Sign-On 구성 확인을 선택하고 **다음**을 클릭합니다.

    ![Azure AD Single Sign-On][10]

9. **Single Sign-On 확인** 페이지에서 **완료**를 클릭합니다.  
  
    ![Azure AD Single Sign-On][11]


### <a name="creating-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기
이 섹션의 목적은 클래식 포털에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

![Azure AD 사용자 만들기][20]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. **Azure 클래식 포털**의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-boomi-tutorial/create_aaduser_09.png) 

2. **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.

3. 사용자 목록을 표시하려면 위쪽 메뉴에서 **사용자**를 클릭합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-boomi-tutorial/create_aaduser_03.png) 

4. **사용자 추가** 대화 상자를 열려면 아래쪽 도구 모음에서 **사용자 추가**를 클릭합니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-boomi-tutorial/create_aaduser_04.png) 

5. **이 사용자에 대한 정보 입력** 대화 상자 페이지에서 다음 단계를 수행합니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-boomi-tutorial/create_aaduser_05.png) 

    a. 사용자 유형에서 조직의 새 사용자를 선택합니다.

    b. 사용자 이름 **텍스트 상자**에 **BrittaSimon**을 입력합니다.

    c. **다음**을 클릭합니다.

6.  **사용자 프로필** 대화 상자 페이지에서 다음 단계를 수행합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-boomi-tutorial/create_aaduser_06.png) 

    a. **이름** 텍스트 상자에 **Britta**를 입력합니다.  

    b. **성** 텍스트 상자에 **Simon**을 입력합니다.

    c. **표시 이름** 텍스트 상자에 **Britta Simon**을 입력합니다.

    d. **역할** 목록에서 **사용자**를 선택합니다.

    e. **다음**을 클릭합니다.

7. **임시 암호 가져오기** 대화 상자 페이지에서 **만들기**를 클릭합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-boomi-tutorial/create_aaduser_07.png) 

8. **임시 암호 가져오기** 대화 상자 페이지에서 다음 단계를 수행합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-boomi-tutorial/create_aaduser_08.png) 

    a. **새 암호**값을 적어둡니다.

    b. **완료**를 클릭합니다.   



### <a name="creating-a-boomi-test-user"></a>Boomi 테스트 사용자 만들기

Azure AD 사용자가 Boomi에 로그인할 수 있도록 하려면 Boomi로 프로비전되어야 합니다. Boomi의 경우 프로비전은 수동 작업입니다.

####<a name="to-provision-a-user-account-perform-the-following-steps"></a>사용자 계정을 프로비저닝하려면 다음 단계를 수행합니다.

1. Boomi 회사 사이트에 관리자 권한으로 로그인합니다.

2. 로그인 후 **사용자 관리**, **사용자**로 이동합니다.

    ![사용자](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_001.png "사용자")

3. ** + ** 아이콘을 클릭하면 **사용자 역할 추가/유지 관리** 대화 상자가 열립니다.

    ![사용자](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_002.png "사용자")

    ![사용자](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_003.png "사용자")

4. 사용자의 **사용자 전자 메일 주소**를 입력합니다.

5. 사용자의 **이름** 및 **성**을 입력합니다.

6. 사용자의 **페더레이션 ID**를 입력합니다. 각 사용자는 계정 내에서 사용자를 고유하게 식별하는 페더레이션 ID가 있어야 합니다. 

7. **표준 사용자** 역할을 사용자에게 할당합니다. 관리자 역할은 일반적인AtomSphere 액세스 권한과 Single Sign-On 액세스 권한을 모두 제공하므로 이 역할을 할당하지 마세요.

8. **확인**을 클릭합니다.

    > [!NOTE]
    > 사용자는 해당 암호가 ID 공급자를 통해 관리되므로 AtomSphere 계정에 로그인하는 데 사용할 수 있는 암호가 포함된 시작 알림 전자 메일이 제공되지 않습니다. 다른 Boomi 사용자 계정 생성 도구 또는 Boomi가 제공한 API를 사용하여 AAD 사용자 계정을 프로비전할 수 있습니다. 

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Boomi에 대한 액세스 권한을 부여합니다.

![사용자 할당][200] 

**Britta Simon을 Boomi에 할당하려면 다음 단계를 수행합니다.**

1. 클래식 포털에서 응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램** 을 클릭합니다.

    ![사용자 할당][201] 

2. 응용 프로그램 목록에서 **Boomi**를 선택합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-boomi-tutorial/tutorial_boomi_50.png) 

3. 위쪽의 메뉴에서 **사용자**를 클릭합니다.

    ![사용자 할당][203] 

4. 사용자 목록에서 **Britta Simon**을 선택합니다.

5. 아래쪽 도구 모음에서 **할당**을 클릭합니다.
    
    ![사용자 할당][205]



### <a name="testing-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Boomi 타일을 클릭하면 Boomi 응용 프로그램에 자동으로 로그온됩니다.


## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On이란 무엇입니까?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-boomi-tutorial/tutorial_general_205.png



<!--HONumber=Feb17_HO2-->


