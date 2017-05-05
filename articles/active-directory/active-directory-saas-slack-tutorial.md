---
title: "자습서: Slack과 Azure Active Directory 통합 | Microsoft 문서"
description: "Azure Active Directory와 Slack 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: ffc5e73f-6c38-4bbb-876a-a7dd269d4e1c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 538f282b28e5f43f43bf6ef28af20a4d8daea369
ms.openlocfilehash: a1d7c2f01d3bf345f63077c4d42d79476235e941
ms.lasthandoff: 04/07/2017


---
# <a name="tutorial-azure-active-directory-integration-with-slack"></a>자습서: Slack과 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Slack을 통합하는 방법에 대해 알아봅니다.

Azure AD와 Slack을 통합하는 경우 다음을 수행할 수 있습니다.

* Azure AD에서는 Slack에 대한 액세스 권한이 있는 사용자를 제어합니다.
* 사용자는 해당 Azure AD 계정으로 Slack SSO(Single Sign-On)에 자동으로 로그인하도록 설정됩니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

Azure AD와의 SaaS(Software as a Service) 앱 통합에 대해 자세히 알아보려면 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On](active-directory-appssoaccess-whatis.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건

Slack과 Azure AD를 통합하도록 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독
* Slack SSO가 설정된 구독

>[!NOTE]
>프로덕션 환경을 사용하여 이 자습서의 단계를 테스트하는 것은 바람직하지 않습니다.
>
>

이 자습서의 단계를 테스트하려면 다음 권장 사항을 따릅니다.

* 필요한 경우에 프로덕션 환경을 사용합니다.
* Azure AD 평가판 환경이 없으면 [1개월 평가판](https://azure.microsoft.com/pricing/free-trial/)을 가져옵니다.


## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD SSO를 테스트합니다. 수행할 시나리오는 두 가지 주요 구성 요소로 이루어져 있습니다.

* 갤러리에서 Slack 추가
* Azure AD SSO 구성 및 테스트

## <a name="add-slack-from-the-gallery"></a>갤러리에서 Slack 추가
Azure AD와 Slack을 통합하도록 구성하려면 다음을 수행하여 Slack을 갤러리에서 관리되는 SaaS 앱 목록에 추가합니다.

1. [Azure 포털](https://portal.azure.com)을 엽니다.
2. 왼쪽 창에서 **Azure Active Directory** 단추를 클릭합니다.

    !["Azure Active Directory" 단추][1]

3. **엔터프라이즈 응용 프로그램**으로 이동한 다음 **모든 응용 프로그램**을 선택합니다.

    !["엔터프라이즈 응용 프로그램" 블레이드의 "모든 응용 프로그램" 단추][2]

4. **모든 응용 프로그램** 대화 상자의 맨 위에서 **추가**를 클릭합니다.

    !["모든 응용 프로그램" 대화 상자의 "추가" 단추][3]

5. 검색 상자에서 **Slack**을 입력합니다.

    !["응용 프로그램 추가" 검색 상자](./media/active-directory-saas-slack-tutorial/tutorial_slack_000.png)

6. 결과 창에서 **Slack**을 선택하고 **추가** 단추를 클릭하여 응용 프로그램을 추가합니다.

     ![결과 창에서 "Slack"을 선택합니다.](./media/active-directory-saas-slack-tutorial/tutorial_slack_0001.png)

## <a name="configure-and-test-azure-ad-sso"></a>Azure AD SSO 구성 및 테스트
이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 사용하여 Slack에서 Azure AD SSO를 구성하고 테스트합니다.

SSO가 작동하기 위해 Azure AD는 Azure AD 사용자 및 해당하는 Slack의 담당자 간에 연결 관계를 설정해야 합니다. 이 연결 관계는 Azure AD의 **사용자 이름** 값을 Slack의 **Username** 값으로 할당하여 설정됩니다.

Slack에서 Azure AD SSO를 구성하고 테스트하려면 다음 구성 요소를 완료합니다.

1. [Azure AD Single Sign-On 구성](#configuring-azure-ad-single-sign-on) - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. [Azure AD 테스트 사용자 만들기](#creating-an-azure-ad-test-user)는 사용자인 Britta Simon으로 Azure AD SSO를 테스트합니다.
3. [Slack 테스트 사용자 만들기](#creating-a-slack-test-user)는 Azure AD 사용자 Britta Simon에게 연결할 Slack 담당자를 제공합니다.
4. [Azure AD 테스트 사용자 할당](#assigning-the-azure-ad-test-user)은 사용자인 Britta Simon이 Azure AD SSO를 사용할 수 있도록 합니다.
5. [Single Sign-On 테스트](#testing-single-sign-on) - 구성이 작동하는지 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD SSO를 사용하도록 설정하고 다음을 수행하여 Slack 응용 프로그램에서 SSO를 구성합니다.

1. Azure Portal의 **Slack** 응용 프로그램 통합 페이지에서 **Single Sign-On**을 클릭합니다.

    ![Slack 응용 프로그램 통합 페이지][4]

2. **Single Sign-On** 대화 상자의 **모드** 목록에서 **SAML 기반 로그온**을 선택하여 SSO를 사용하도록 설정합니다.

    !["Single Sign-On" 대화 상자](./media/active-directory-saas-slack-tutorial/tutorial_slack_01.png)

3. **Slack 도메인 및 URL** 아래에서 다음 단계를 수행합니다.

    !["Single Sign-On" 대화 상자](./media/active-directory-saas-slack-tutorial/tutorial_slack_02.png)
  1. **로그온 URL** 상자에서 명명 규칙 _https://<company name>.slack.com_을 사용하는 URL을 입력합니다.
  2. **식별자** 상자에서 **https://slack.com**을 입력합니다.

     >[!NOTE]
     >위의 값은 실제 값이 아닙니다. 여기서는 URL과 식별자에 대한 고유한 값을 사용하는 것이 좋습니다. 나중에 실제 URL과 식별자로 값을 업데이트합니다. 값을 가져오려면 [Slack 지원 팀](https://slack.com/help/contact)에 문의하세요.
     >
     >

4. Slack 응용 프로그램은 SAML(Security Assertion Markup Language) 어설션이 특정 형식으로 표시되도록 합니다. 다음 스크린샷에 표시된 대로 Slack 응용 프로그램 통합 페이지의 **사용자 특성** 섹션에서 클레임을 구성하고 특성의 값을 관리합니다.

    ![사용자 속성 섹션에서 클레임 구성](./media/active-directory-saas-slack-tutorial/tutorial_slack_03.png)

5. **Single Sign-On** 대화 상자의 **사용자 특성** 섹션에서 **user.mail**을 **사용자 식별자**로 선택합니다. 테이블에서 각 행에 대해 다음 단계를 수행합니다.

    | 특성 이름 | 특성 값 |
    | --- | --- |    
    | User.Email | user.userprincipalname |
    | first_name | user.givenname |
    | last_name | user.surname |
    | User.Username | extractmailprefix([userprincipalname]) |

    1. **특성 추가** 단추를 클릭합니다.

    ![Single Sign-on 구성](./media/active-directory-saas-slack-tutorial/tutorial_slack_04.png)
    2. **특성 추가** 대화 상자의 **이름** 상자에 있는 테이블의 **특성 이름** 열에서 첫 번째 이름을 입력합니다.

    ![Single Sign-on 구성](./media/active-directory-saas-slack-tutorial/tutorial_slack_05.png)
    3. **값** 상자에 있는 테이블의 **특성 값** 열에서 첫 번째 값을 입력합니다.
    4. **확인**을 클릭합니다.
    5. 다음 세 개의 테이블 행에서 "a"부터 "d"까지의 단계를 반복합니다.

6. **SAML 서명 인증서**에서 **새 인증서 만들기**를 클릭합니다.

    ![인증서 만들기](./media/active-directory-saas-slack-tutorial/tutorial_slack_06.png)     

7. **새 인증서 만들기** 대화 상자에서 **달력** 단추를 클릭하고 만료 날짜를 선택한 다음 **저장**을 클릭합니다.

    ![인증서 만료 날짜 선택](./media/active-directory-saas-slack-tutorial/tutorial_general_300.png)

8. **SAML 서명 인증서**에서 **새 인증서 활성화** 확인란을 선택한 후 **저장**을 클릭합니다.

    ![SAML 서명 인증서 활성화](./media/active-directory-saas-slack-tutorial/tutorial_slack_07.png)

9. **롤오버 인증서** 팝업 창에서 **확인**을 클릭합니다.

    ![롤오버 인증서 팝업 창](./media/active-directory-saas-slack-tutorial/tutorial_general_400.png)

10. **SAML 서명 인증서** 아래에서 **인증서(base64)**를 클릭한 후 로컬 드라이브에 인증서 파일을 저장합니다.

    ![로컬 드라이브에 인증서 저장](./media/active-directory-saas-slack-tutorial/tutorial_slack_08.png)

11. **Slack 구성** 아래에서 **Slack 구성**을 클릭하여 **로그온 구성** 창을 엽니다.

    !["Slack 구성"을 클릭하여 구성 로그인 창을 엽니다.](./media/active-directory-saas-slack-tutorial/tutorial_slack_09.png)

    !["로그인 구성" 창](./media/active-directory-saas-slack-tutorial/tutorial_slack_10.png)

12. 새 브라우저 창을 열고 Slack 회사 사이트에 관리자 권한으로 로그인합니다.

13. **Microsoft Azure AD**, **팀 설정**으로 차례로 이동합니다.

    ![Slack 회사 사이트에서 Microsoft Azure AD "팀 설정" 단추](./media/active-directory-saas-slack-tutorial/tutorial_slack_001.png)

14. **팀 설정** 아래에서 **인증** 탭을 클릭한 다음 **설정 변경**을 클릭합니다.

    ![팀 설정 페이지의 "설정 변경" 단추](./media/active-directory-saas-slack-tutorial/tutorial_slack_002.png)

15. **SAML 인증 설정** 대화 상자에서 다음 단계를 수행합니다.

    !["SAML 인증 설정" 대화 상자](./media/active-directory-saas-slack-tutorial/tutorial_slack_003.png)
  1. **SAML 2.0 끝점(HTTP)** 상자에서 Azure AD 응용 프로그램 구성 창의 **SAML Single Sign-On 서비스 URL** 값을 입력합니다.
  2. **ID 공급자 발급자** 상자에서 Azure AD 응용 프로그램 구성 창의 **SAML 엔터티 ID** 값을 입력합니다.
  3. 다운로드한 인증서 파일을 메모장에서 열고, 내용을 복사한 다음 **공용 인증서** 상자에 붙여 넣습니다.
  4. 이전 세 가지 설정을 Slack 팀에 적합하게 구성합니다. 설정에 대한 자세한 내용은 [Slack을 사용하여 Single Sign-On 가이드](https://get.slack.help/hc/en-us/articles/220403548-Guide-to-single-sign-on-with-Slack)를 참조하세요.
  5. **구성 저장**을 클릭하십시오.
  6. **사용자가 이메일 주소를 변경할 수 있도록 허용**을 선택 취소하십시오.
  7. **사용자가 자신의 사용자 이름을 선택할 수 있도록 허용**을 선택하십시오.
  8. **팀에서 사용해야 하는 인증**으로 **선택 사항**을 선택합니다.
  
### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기
이 섹션에서는 다음을 수행하여 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만듭니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory** 단추를 클릭합니다.

    !["Azure Active Directory" 단추](./media/active-directory-saas-slack-tutorial/create_aaduser_01.png)

2. **사용자 및 그룹**으로 이동하고 **모든 사용자**를 클릭하여 사용자 목록을 표시합니다.

    ![Azure AD "모든 사용자" 단추](./media/active-directory-saas-slack-tutorial/create_aaduser_02.png)

3. **모든 사용자** 대화 상자의 맨 위에서 **추가**를 클릭합니다.

    ![사용자 추가 대화 상자의 "추가" 단추](./media/active-directory-saas-slack-tutorial/create_aaduser_03.png)

4. **사용자** 대화 상자에서 다음 정보를 입력합니다.

    !["사용자" 대화 상자](./media/active-directory-saas-slack-tutorial/create_aaduser_04.png)
  1. **이름** 상자에 **BrittaSimon**을 입력합니다.
  2. **사용자 이름** 상자에 사용자인 Britta Simon의 전자 메일 주소를 입력합니다.
  3. **암호 표시** 확인란을 선택한 다음 **암호** 상자에 표시된 값을 적어둡니다.
  4. **만들기**를 클릭합니다.

### <a name="create-a-slack-test-user"></a>Slack 테스트 사용자 만들기

이 섹션에서는 Slack에서 Britta Simon이라는 사용자를 만듭니다. Slack은 기본적으로 사용하도록 설정된 Just-In-Time 프로비전을 지원합니다.

이 섹션에 작업 항목이 없습니다. 사용자가 Slack에 존재하지 않는 경우 Slack에 액세스하려고 할 때 새 사용자를 만듭니다.

>[!NOTE]
>사용자를 수동으로 만들어야 하는 경우 [Slack 지원 팀에 문의](https://slack.com/help/contact)하세요.
>
>

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure SSO를 사용할 수 있도록 사용자인 Britta Simon에게 Slack에 대한 액세스 권한을 부여합니다.

![Azure SSO의 사용자 지정][200]

사용자인 Britta Simon을 Slack에 할당하려면 다음 단계를 수행합니다.

1. Azure Portal에서 응용 프로그램 보기를 열고 디렉터리 보기로 이동한 후 **엔터프라이즈 응용 프로그램**으로 이동하고 **모든 응용 프로그램**을 클릭합니다.

    ![사용자 할당][201]

2. **응용 프로그램** 목록에서 **Slack**을 선택합니다.

    ![Azure Portal 응용 프로그램 목록](./media/active-directory-saas-slack-tutorial/tutorial_slack_50.png)

3. 왼쪽 창에서 **사용자 및 그룹**을 클릭합니다.

    ![왼쪽 창 "사용자 및 그룹" 단추][202]

4. **추가** 단추를 클릭한 다음 **할당 추가** 블레이드에서 **사용자 및 그룹**을 선택합니다.

    !["추가" 단추 및 "할당 추가" 블레이드][203]

5. **사용자 및 그룹** 대화 상자의 **사용자 목록**에서 **Britta Simon**을 선택합니다.

6. **선택** 단추를 클릭합니다.

7. **할당 추가** 블레이드에서 **할당** 단추를 클릭합니다.

### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD SSO 구성을 테스트합니다.

구성을 테스트하려면 액세스 패널로 이동하고 **Slack** 타일을 클릭합니다. 사용자는 Slack 응용 프로그램에 자동으로 로그인됩니다.


## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory를 사용하여 SaaS 앱을 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On이란 무엇입니까?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-slack-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-slack-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-slack-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-slack-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-slack-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-slack-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-slack-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-slack-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-slack-tutorial/tutorial_general_203.png

