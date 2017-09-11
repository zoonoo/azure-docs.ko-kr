---
title: "자습서: Help Scout와 Azure Active Directory 통합 | Microsoft Docs"
description: "Azure Active Directory 및 Help Scout 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 0aad9910-0bc1-4394-9f73-267cf39973ab
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/11/2017
ms.author: jeedes
ms.translationtype: HT
ms.sourcegitcommit: 5b6c261c3439e33f4d16750e73618c72db4bcd7d
ms.openlocfilehash: 84cee39c28a0f7e6b9878441e504131795673020
ms.contentlocale: ko-kr
ms.lasthandoff: 08/28/2017

---
# <a name="tutorial-azure-active-directory-integration-with-help-scout"></a>자습서: Help Scout와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Help Scout를 통합하는 방법에 대해 알아봅니다.

Azure AD와 Help Scout를 통합하면 다음과 같은 이점을 얻을 수 있습니다.

- Azure AD에서 Help Scout에 대한 액세스 권한이 있는 사용자를 제어할 수 있습니다.
- 사용자가 Single Sign-On 및 사용자의 Azure AD 계정을 사용하여 Help Scout에 자동으로 로그인할 수 있습니다.
- 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS(Software as a Service) 앱 통합에 대해 자세히 알아보려면 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On이란?](active-directory-appssoaccess-whatis.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건

Help Scout와 Azure AD 통합을 설정하려면 다음 항목이 필요합니다.

- Azure AD 구독
- Single Sign-On이 설정된 Help Scout 구독 

> [!NOTE]
> 이 자습서의 단계를 테스트하는 경우 프로덕션 환경에서 테스트하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하는 권장 사항:

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [1개월 평가판을 얻을](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 

이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리의 Help Scout를 추가합니다.
2. Azure AD Single Sign-On 설정 및 테스트

## <a name="add-help-scout-from-the-gallery"></a>갤러리의 Help Scout 추가
Azure AD와 Help Scout의 통합을 설정하려면 갤러리에서 Help Scout를 관리되는 SaaS 앱 목록에 추가합니다.

갤러리의 Help Scout를 추가하려면:

1. [Azure Portal](https://portal.azure.com)의 왼쪽 메뉴에서 **Azure Active Directory**를 선택합니다. 

    ![Azure Active Directory 단추][1]

2. **엔터프라이즈 응용 프로그램**을 선택한 다음 **모든 응용 프로그램**을 선택합니다.

    ![엔터프라이즈 응용 프로그램 페이지][2]
    
3. 새 응용 프로그램을 추가하려면 **새 응용 프로그램**을 선택합니다.

    ![새 응용 프로그램 단추][3]

4. 검색 상자에 **Help Scout**를 입력합니다. 검색 결과에서 **Help Scout**를 선택한 후 **추가**를 선택합니다.

    ![결과 목록의 Help Scout](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_addfromgallery.png)

## <a name="set-up-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 설정 및 테스트

이 섹션에서는 *Britta Simon*이라는 테스트 사용자를 기반으로 Help Scout에서 Azure AD Single Sign-On을 설정하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서는 Azure AD 사용자에 해당하는 Help Scout 사용자가 누구인지 알고 있어야 합니다. Azure AD 사용자와 Help Scout의 관련 사용자 간에 연결이 형성되어야 합니다.

연결 관계를 설정하려면 Help Scout에서 Azure AD의 **사용자 이름** 값을 **Username** 값에 할당합니다.

Help Scout에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 작업을 완료합니다.

1. [Azure AD Single Sign-On 설정](#set-up-azure-ad-single-sign-on). 사용자가 이 기능을 사용하도록 설정합니다.
2. [Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user). Britta Simon 사용자로 Azure AD Single Sign-On을 테스트합니다.
3. [Help Scout 테스트 사용자 만들기](#create-a-help-scout-test-user) Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 Help Scout에 만듭니다.
4. [Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user). Britta Simon이 Azure AD Single Sign-On을 사용하도록 설정합니다.
5. [Single Sign-On 테스트](#test-single-sign-on). 구성이 작동하는지 확인합니다.

### <a name="set-up-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 설정

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 설정합니다. 그런 다음 Help Scout 응용 프로그램에서 Single Sign-On을 설정합니다.

Help Scout에서 Azure AD Single Sign-On을 설정하려면:

1. Azure Portal의 **Help Scout** 응용 프로그램 통합 페이지에서 **Single Sign-On**을 선택합니다.
 
    ![Single Sign-On 설정 링크][4]

2. **Single Sign-On** 페이지에서 **모드**로 **SAML 기반 로그온**을 선택합니다.
 
    ![Single Sign-On 대화 상자](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_samlbase.png)

3. **Help Scout 도메인 및 URL** 아래에서 IDP 시작 모드로 응용 프로그램을 설정하려는 경우 다음 단계를 완료합니다.

    1. **식별자** 상자에 다음 패턴이 있는 URL을 입력합니다.`urn:auth0:helpscout:<instancename>`

    2. **회신 URL** 상자에 다음 패턴이 있는 URL을 입력합니다.`https://helpscout.auth0.com/login/callback?connection=<instancename>`

    ![Help Scout 도메인 및 URL Single Sign-On 정보](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_url.png)

4. SP 시작 모드에서 응용 프로그램을 설정하려면 **고급 URL 설정 표시** 확인란을 선택하고 다음을 수행합니다.

    * **로그온 URL** 상자에 다음 형식이 있는 URL을 입력합니다.`https://secure.helpscout.net/members/login/`

    ![Help Scout 도메인 및 URL Single Sign-On 정보](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_url1.png)
 
    > [!NOTE] 
    > 이러한 URL의 값은 데모용으로만 사용합니다. 실제 식별자 URL 및 회신 URL로 해당 값을 업데이트합니다. 이러한 값을 얻으려면 [Help Scout 지원팀](mailto:help@helpscout.com)에 문의하세요. 

5. **SAML 서명 인증서** 아래에서 **메타데이터 XML**을 선택한 다음 컴퓨터에 메타데이터 파일을 저장합니다.

    ![인증서 다운로드 링크](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_certificate.png) 

6. **저장**을 선택합니다.

    ![Single Sign-On 구성 저장 단추](./media/active-directory-saas-helpscout-tutorial/tutorial_general_400.png)
    
7. Help Scout 쪽에서 Single Sign-On을 설정하려면 다운로드한 XML 파일을 [Help Scout 지원팀](mailto:help@helpscout.com)에 전송합니다. SAML Single Sign-On 연결이 양쪽 모두에서 올바르게 설정되도록 Help Scout 지원팀은 이 설정을 적용합니다.

> [!TIP]
> 앱을 설정하는 동안 [Azure Portal](https://portal.azure.com)에서 이러한 지침의 간결한 버전을 읽을 수 있습니다. **Active Directory** > **엔터프라이즈 응용 프로그램**을 선택하여 앱을 추가한 후에 **Single Sign-On** 탭을 선택합니다. 페이지의 맨 아래에 있는 **구성** 섹션에서 포함된 설명서에 액세스할 수 있습니다. 자세한 내용은 [Azure AD 포함 설명서]( https://go.microsoft.com/fwlink/?linkid=845985)를 참조하세요.

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션에서는 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만듭니다.

![Azure AD 테스트 사용자 만들기][100]

Azure AD에서 테스트 사용자를 만들려면:

1. Azure Portal의 왼쪽 메뉴에서 **Azure Active Directory**를 선택합니다.

    ![Azure Active Directory 단추](./media/active-directory-saas-helpscout-tutorial/create_aaduser_01.png)

2. 사용자 목록을 표시하려면 **사용자 및 그룹**을 선택한 후 **모든 사용자**를 선택합니다.

    ![사용자 및 그룹 선택 및 모든 사용자 선택](./media/active-directory-saas-helpscout-tutorial/create_aaduser_02.png)

3. **사용자** 대화 상자를 열려면 **모든 사용자** 페이지 위쪽에서 **추가**를 선택합니다.

    ![추가 단추](./media/active-directory-saas-helpscout-tutorial/create_aaduser_03.png)

4. **사용자** 대화 상자에서 다음 단계를 완료합니다.

    1. **이름** 상자에 **BrittaSimon**을 입력합니다.

    2. **사용자 이름** 상자에 사용자인 Britta Simon의 이메일 주소를 입력합니다.

    3. **암호 표시** 확인란을 선택한 다음 **암호** 상자에 표시된 값을 적어둡니다.

    4. **만들기**를 선택합니다.

        ![사용자 대화 상자](./media/active-directory-saas-helpscout-tutorial/create_aaduser_04.png)

 
### <a name="create-a-help-scout-test-user"></a>Help Scout 테스트 사용자 만들기

이 섹션은 Help Scout에서 Britta Simon이라는 사용자를 만들기 위한 것입니다. Help Scout는 JIT(Just-In-Time) 프로비전을 지원하며 이 기능은 기본적으로 설정됩니다.

이 섹션에서는 완료할 작업이 없습니다. 사용자가 Help Scout에 존재하지 않는 경우 Help Scout에 액세스하려고 할 때 새 사용자를 만듭니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Help Scout에 대한 사용자 계정 액세스 권한을 부여하여 사용자인 Britta Simon이 Azure Single Sign-On을 사용할 수 있도록 합니다.

![사용자 역할 할당][200] 

Britta Simon을 Help Scout에 할당하려면:

1. Azure Portal에서 응용 프로그램 보기를 연 다음 디렉터리 보기로 이동합니다. **엔터프라이즈 응용 프로그램**을 선택한 다음 **모든 응용 프로그램**을 선택합니다.

    ![사용자 할당][201] 

2. 응용 프로그램 목록에서 **Help Scout**를 선택합니다.

    ![응용 프로그램 목록의 Help Scout 링크](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_app.png)  

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 및 그룹 링크][202]

4. **추가**를 선택합니다. 그런 다음 **할당 추가** 창에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창][203]

5. **사용자 및 그룹** 페이지의 사용자 목록에서 **Britta Simon**을 선택합니다.

6. **사용자 및 그룹** 페이지에서 **선택**을 선택합니다.

7. **할당 추가** 페이지에서 **할당**을 선택합니다.
    
### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Help Scout 타일을 선택하면 Help Scout 응용 프로그램에 자동으로 로그온됩니다.

액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하세요. 

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory를 사용하여 SaaS 앱을 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On이란 무엇입니까?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_203.png


