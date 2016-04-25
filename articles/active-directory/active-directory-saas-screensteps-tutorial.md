<properties 
    pageTitle="자습서: ScreenSteps와 Azure Active Directory 통합 | Microsoft Azure" 
    description="Azure Active Directory에서 ScreenSteps를 사용하여 Single Sign-On, 자동화된 프로비전 등을 사용하도록 설정하는 방법을 알아봅니다." 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
    manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="04/06/2016" 
    ms.author="jeedes" />

#자습서: ScreenSteps와 Azure Active Directory 통합
  
이 자습서는 Azure와 ScreenSteps의 통합을 보여주기 위한 것입니다. 이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

-   유효한 Azure 구독
-   ScreenSteps 테넌트
  
이 자습서를 완료한 후 ScreenSteps에 할당한 Azure AD 사용자가 ScreenSteps 회사 사이트 (서비스 공급자가 시작한 로그온)에서나 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 사용하여 응용 프로그램에 Single Sign-On할 수 있습니다.
  
이 자습서에 설명된 시나리오는 다음 구성 요소로 이루어져 있습니다.

1.  ScreenSteps에 응용 프로그램 통합 사용
2.  Single Sign-On 구성
3.  사용자 프로비전 구성
4.  사용자 할당

![시나리오](./media/active-directory-saas-screensteps-tutorial/IC778516.png "시나리오")
##ScreenSteps에 응용 프로그램 통합 사용
  
이 섹션은 ScreenSteps에 응용 프로그램 통합을 사용하도록 설정하는 방법을 간략하게 설명하기 위한 것입니다.

###ScreenSteps에 응용 프로그램 통합을 사용하도록 설정하려면

1.  Azure 관리 포털의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.

    ![Active Directory](./media/active-directory-saas-screensteps-tutorial/IC700993.png "Active Directory")

2.  **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.

3.  응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램**을 클릭합니다.

    ![응용 프로그램](./media/active-directory-saas-screensteps-tutorial/IC700994.png "응용 프로그램")

4.  페이지 맨 아래에 있는 **추가**를 클릭합니다.

    ![응용 프로그램 추가](./media/active-directory-saas-screensteps-tutorial/IC749321.png "응용 프로그램 추가")

5.  **원하는 작업을 선택하세요.** 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.

    ![갤러리에서 응용 프로그램 추가](./media/active-directory-saas-screensteps-tutorial/IC749322.png "갤러리에서 응용 프로그램 추가")

6.  **검색 상자**에 **ScreenSteps**를 입력합니다.

    ![응용 프로그램 갤러리](./media/active-directory-saas-screensteps-tutorial/IC778517.png "응용 프로그램 갤러리")

7.  결과 창에서 **ScreenSteps**를 선택하고 **완료**를 클릭하여 응용 프로그램을 추가합니다.

    ![ScreenSteps](./media/active-directory-saas-screensteps-tutorial/IC778518.png "ScreenSteps")
##Single Sign-On 구성
  
이 섹션은 사용자가 SAML 프로토콜 기반 페더레이션을 사용하여 Azure AD의 계정으로 ScreenSteps에 인증할 수 있게 하는 방법을 간략하게 설명하기 위한 것입니다.

###Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1.  Azure AD 포털의 **ScreenSteps** 응용 프로그램 통합 페이지에서 **Single Sign-On 구성**을 클릭하여 **Single Sign-On 구성** 대화 상자를 엽니다.

    ![Single Sign-On 구성](./media/active-directory-saas-screensteps-tutorial/IC778519.png "Single Sign-On 구성")

2.  **ScreenSteps에 대한 사용자 로그온 방법을 선택하십시오.** 페이지에서 **Microsoft Azure AD Single Sign-On**을 선택하고 **다음**을 클릭합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-screensteps-tutorial/IC778520.png "Single Sign-On 구성")

3.  **앱 URL 구성** 페이지의 **ScreenSteps 로그인 URL** 텍스트 상자에 다음 패턴 "*https://\<tenant-name>.ScreenSteps.com*"을 사용하여 URL을 입력한 후 **다음**을 클릭합니다.

    ![앱 URL 구성](./media/active-directory-saas-screensteps-tutorial/IC778521.png "앱 URL 구성")

4.  **ScreenSteps에서 Single Sign-On 구성** 페이지에서 인증서를 다운로드하려면 **인증서 다운로드**를 클릭한 다음 컴퓨터에 인증서 파일을 저장합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-screensteps-tutorial/IC778522.png "Single Sign-On 구성")

5.  다른 웹 브라우저 창에서 ScreenSteps 회사 사이트에 관리자로 로그인합니다.

6.  **계정 관리**를 클릭합니다.

    ![계정 관리](./media/active-directory-saas-screensteps-tutorial/IC778523.png "계정 관리")

7.  **원격 인증**을 클릭합니다.

    ![원격 인증](./media/active-directory-saas-screensteps-tutorial/IC778524.png "원격 인증")

8.  **인증 끝점 만들기**를 클릭합니다.

    ![원격 인증](./media/active-directory-saas-screensteps-tutorial/IC778525.png "원격 인증")

9.  **인증 끝점 만들기** 섹션에서 다음 단계를 수행합니다.

    ![인증 끝점 만들기](./media/active-directory-saas-screensteps-tutorial/IC778526.png "인증 끝점 만들기")

    1.  **제목** 텍스트 상자에 제목을 입력합니다.
    2.  **모드**목록에서**SAML**을 선택합니다.
    3.  **만들기**를 클릭합니다.

10. **원격 인증 끝점** 섹션에서 다음 단계를 수행합니다.

    ![원격 인증 끝점](./media/active-directory-saas-screensteps-tutorial/IC778527.png "원격 인증 끝점")

    1.  Azure AD 포털의 **ScreenSteps에서 Single Sign-On 구성** 대화 상자 페이지에서 **원격 로그인 URL** 값을 복사한 다음 **원격 로그인 URL** 텍스트 상자에 붙여넣습니다.
    2.  Azure AD 포털의 **ScreenSteps에서 Single Sign-On 구성** 대화 상자 페이지에서 **원격 로그아웃 URL** 값을 복사한 다음 **로그아웃 URL** 텍스트 상자에 붙여넣습니다.
    3.  **파일 선택**을 클릭한 다음 다운로드한 인증서를 업로드합니다.
    4.  **업데이트**를 클릭합니다.

11. Azure AD 포털에서 Single Sign-on 구성 확인을 선택하고 **완료**를 클릭하여 **Single Sign-on 구성** 대화 상자를 닫습니다.

    ![Single Sign-On 구성](./media/active-directory-saas-screensteps-tutorial/IC778542.png "Single Sign-On 구성")
##사용자 프로비전 구성
  
Azure AD 사용자가 **ScreenSteps**에 로그인할 수 있도록 하려면 **ScreenSteps**로 프로비전되어야 합니다. **ScreenSteps**의 경우 프로비전은 수동 작업입니다.

###사용자 계정을 ScreenSteps에 프로비전하려면 다음 단계를 수행합니다.

1.  **ScreenSteps** 테넌트에 로그인합니다.

2.  **계정 관리**를 클릭합니다.

    ![계정 관리](./media/active-directory-saas-screensteps-tutorial/IC778523.png "계정 관리")

3.  **사용자**를 클릭합니다.

    ![사용자](./media/active-directory-saas-screensteps-tutorial/IC778544.png "사용자")

4.  **사용자 만들기**를 클릭합니다.

    ![모든 사용자](./media/active-directory-saas-screensteps-tutorial/IC778545.png "모든 사용자")

5.  **사용자 역할** 목록에서 해당 사용자에 대한 역할을 선택합니다.

6.  사용자 역할 섹션에 관련 텍스트 상자에 프로비전하려는 유효한 AAD 계정의 "**이름****성**,**메일**,**로그인**,**암호** 및 **암호 확인**"을 입력하십시오.

    ![새 사용자](./media/active-directory-saas-screensteps-tutorial/IC778546.png "새 사용자")

7.  그룹 섹션에서 **인증 그룹 (SAML)**을 선택하고 **사용자 만들기**를 클릭하십시오.

    ![그룹](./media/active-directory-saas-screensteps-tutorial/IC778547.png "그룹")

>[AZURE.NOTE]다른 ScreenSteps 사용자 계정 생성 도구 또는 ScreenSteps가 제공한 API를 사용하여 AAD 사용자 계정을 프로비전할 수 있습니다.

##사용자 할당
  
구성을 테스트하려면 응용 프로그램 사용을 허용하려는 Azure AD 사용자를 할당하여 액세스 권한을 부여해야 합니다.

###ScreenSteps에 사용자를 할당하려면 다음 단계를 수행합니다.

1.  Azure AD 포털에서 테스트 계정을 만듭니다.

2.  **ScreenSteps ** 응용 프로그램 통합 페이지에서 **사용자 할당**을 클릭합니다.

    ![사용자 할당](./media/active-directory-saas-screensteps-tutorial/IC773094.png "사용자 할당")

3.  테스트 사용자를 선택하고 **할당**을 클릭한 다음 **예**를 클릭하여 할당을 확인합니다.

    ![사용자 할당](./media/active-directory-saas-screensteps-tutorial/IC778548.png "사용자 할당")
  
Single Sign-On 설정을 테스트하려면 액세스 패널을 엽니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하십시오.

<!---HONumber=AcomDC_0413_2016-->