<properties pageTitle="자습서: Canvas LMS와 Azure Active Directory 통합 | Microsoft Azure" description="Azure Active Directory에서 Canvas LMS를 사용하여 Single Sign-On, 자동화된 프로비전 등을 사용하도록 설정하는 방법을 알아봅니다." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#자습서: Canvas LMS와 Azure Active Directory 통합
>[AZURE.TIP]사용자 의견을 보내려면 [여기](http://go.microsoft.com/fwlink/?LinkId=524182)를 클릭하십시오.

이 자습서는 Azure 및 Canvas의 통합을 보여주기 위한 것입니다. 이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

-   유효한 Azure 구독
-   Canvas 테넌트

이 자습서를 완료한 후 Canvas에 할당한 Azure AD 사용자가 Canvas 회사 사이트 (서비스 공급자가 시작한 로그온)에서나 [액세스 패널 소개](https://msdn.microsoft.com/library/dn308586)를 사용하여 응용 프로그램에 Single Sign-On 할 수 있습니다.

이 자습서에 설명된 시나리오는 다음 구성 요소로 이루어져 있습니다.

1.  Canvas에 응용 프로그램 통합 사용
2.  Single Sign-On 구성
3.  사용자 프로비전 구성
4.  사용자 할당

![시나리오](./media/active-directory-saas-canvas-lms-tutorial/IC775984.png "시나리오")
##Canvas에 응용 프로그램 통합 사용

이 섹션은 Canvas에 응용 프로그램 통합을 사용하도록 설정하는 방법을 간략하게 설명하기 위한 것입니다.

###Canvas에 응용 프로그램 통합을 사용하도록 설정하려면 다음 단계를 수행합니다.

1.  Azure 관리 포털의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.

    ![Active Directory](./media/active-directory-saas-canvas-lms-tutorial/IC700993.png "Active Directory")

2.  **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.

3.  응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램**을 클릭합니다.

    ![응용 프로그램](./media/active-directory-saas-canvas-lms-tutorial/IC700994.png "응용 프로그램")

4.  페이지 맨 아래에 있는 **추가**를 클릭합니다.

    ![응용 프로그램 추가](./media/active-directory-saas-canvas-lms-tutorial/IC749321.png "응용 프로그램 추가")

5.  **원하는 작업을 선택하십시오.** 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.

    ![갤러리에서 응용 프로그램 추가](./media/active-directory-saas-canvas-lms-tutorial/IC749322.png "갤러리에서 응용 프로그램 추가")

6.  **검색 상자**에 **Canvas**를 입력합니다.

    ![응용 프로그램 갤러리](./media/active-directory-saas-canvas-lms-tutorial/IC775985.png "응용 프로그램 갤러리")

7.  결과 창에서 **Canvas**를 선택하고 **완료**를 클릭하여 응용 프로그램을 추가합니다.

    ![캔버스](./media/active-directory-saas-canvas-lms-tutorial/IC775986.png "캔버스")
##Single Sign-On 구성

이 섹션은 사용자가 SAML 프로토콜 기반 페더레이션을 사용하여 Azure AD의 계정으로 Canvas에 인증할 수 있게 하는 방법을 간략하게 설명하기 위한 것입니다. Canvas에 대한 Single Sign-on을 구성하려면 인증서의 손도장(thumbprint) 값을 검색해야 합니다. 이 절차를 잘 모르는 경우 [인증서의 손도장 값을 검색하는 방법](http://youtu.be/YKQF266SAxI)을 참조하십시오.

###Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1.  Azure AD 포털의 **Canvas** 응용 프로그램 통합 페이지에서 **Single Sign-on 구성**을 클릭하여 **Single Sign-on 구성** 대화 상자를 엽니다.

    ![Single Sign-On 구성](./media/active-directory-saas-canvas-lms-tutorial/IC771709.png "Single Sign-On 구성")

2.  **Canvas에 대한 사용자 로그온 방법을 선택하십시오.** 페이지에서 **Microsoft Azure AD Single Sign-on**을 선택하고 **다음**을 클릭합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-canvas-lms-tutorial/IC775987.png "Single Sign-On 구성")

3.  **앱 URL 구성** 페이지의 **Canvas 로그인 URL** 텍스트 상자에 다음 패턴 `https://<tenant-name>.instructure.com`을 사용하여 URL을 입력한 다음 **다음**을 클릭합니다.

    ![앱 URL 구성](./media/active-directory-saas-canvas-lms-tutorial/IC775988.png "앱 URL 구성")

4.  **Canvas에서 Single Sign-On 구성** 페이지에서 인증서를 다운로드하려면 **인증서 다운로드**를 클릭한 다음 컴퓨터에 로컬로 인증서 파일을 저장합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-canvas-lms-tutorial/IC775989.png "Single Sign-On 구성")

5.  다른 웹 브라우저 창에서 Canvas 회사 사이트에 관리자로 로그인합니다.

6.  **코스 > 관리된 계정 > Microsoft**로 이동합니다.

    ![캔버스](./media/active-directory-saas-canvas-lms-tutorial/IC775990.png "캔버스")

7.  왼쪽 탐색 창에서 **인증**을 선택한 다음 **새 SAML Config 추가**를 클릭합니다.

    ![인증](./media/active-directory-saas-canvas-lms-tutorial/IC775991.png "인증")

8.  현재 통합 페이지에서 다음 단계를 수행합니다.

    ![현재 통합](./media/active-directory-saas-canvas-lms-tutorial/IC775992.png "현재 통합")

    1.  Azure 포털의 **Canvas에서 Single Sign-on 구성** 대화 상자 페이지에서 **엔터티 ID** 값을 복사한 다음 **IdP 엔터티 ID** 텍스트 상자에 붙여넣습니다.
    2.  Azure 포털의 **Canvas에서 Single Sign-on 구성** 대화 상자 페이지에서 **원격 로그인 URL** 값을 복사한 다음 **로그인 URL** 텍스트 상자에 붙여넣습니다.
    3.  Azure 포털의 **Canvas에서 Single Sign-on 구성** 대화 상자 페이지에서 **원격 로그인 URL** 값을 복사한 다음 **로그아웃 URL** 텍스트 상자에 붙여넣습니다.
    4.  Azure 포털의 **Canvas에서 Single Sign-on 구성** 대화 상자 페이지에서 **변경 암호 URL** 값을 복사한 다음 **변경 암호 링크** 텍스트 상자에 붙여넣습니다.
    5.  내보낸 인증서에서 **지문** 값을 복사한 다음 **인증서 지문** 텍스트 상자에 붙여 넣습니다.  

        >[AZURE.TIP]자세한 내용은 [인증서의 지문 값을 검색하는 방법](http://youtu.be/YKQF266SAxI)을 참조하십시오.

    6.  **로그인 특성**목록에서 **NameID**를 선택합니다.
    7.  **식별자 형식**목록에서**emailAddress**를 선택합니다.
    8.  **인증 설정 저장**을 클릭합니다.

9.  Azure AD 포털에서 Single Sign-on 구성 확인을 선택하고 **완료**를 클릭하여 **Single Sign-on 구성** 대화 상자를 닫습니다.

    ![Single Sign-On 구성](./media/active-directory-saas-canvas-lms-tutorial/IC775993.png "Single Sign-On 구성")
##사용자 프로비전 구성

Azure AD 사용자가 Canvas에 로그인할 수 있도록 하려면 Canvas로 프로비전되어야 합니다. Canvas의 경우 프로비전은 수동 작업입니다.

###사용자 계정을 프로비전하려면 다음 단계를 수행합니다.

1.  **Canvas** 테넌트에 로그인합니다.

2.  **코스 > 관리된 계정 > Microsoft**로 이동합니다.

    ![캔버스](./media/active-directory-saas-canvas-lms-tutorial/IC775990.png "캔버스")

3.  **사용자**를 클릭합니다.

    ![사용자](./media/active-directory-saas-canvas-lms-tutorial/IC775995.png "사용자")

4.  **새 사용자 추가**를 클릭합니다.

    ![사용자](./media/active-directory-saas-canvas-lms-tutorial/IC775996.png "사용자")

5.  새 사용자 추가 대화 상자 페이지에서 다음 단계를 수행합니다.

    ![사용자 추가](./media/active-directory-saas-canvas-lms-tutorial/IC775997.png "사용자 추가")

    1.  **전체 이름** 텍스트 상자에서 사용자의 이름을 입력합니다.
    2.  **전자 메일** 텍스트 상자에서 사용자의 전자 메일 주소를 입력합니다.
    3.  **로그인** 텍스트 상자에서 사용자의 Azure AD 전자 메일 주소를 입력합니다.
    4.  **이 계정 만들기에 대해 사용자에 게 전자 메일**을 선택합니다.
    5.  **사용자 추가**를 클릭합니다.

>[AZURE.NOTE]다른 Canvas 사용자 계정 생성 도구 또는 Canvas가 제공한 API를 사용하여 AAD 사용자 계정을 프로비전할 수 있습니다.

##사용자 할당

구성을 테스트하려면 응용 프로그램 사용을 허용하려는 Azure AD 사용자를 할당하여 액세스 권한을 부여해야 합니다.

###Canvas에 사용자를 할당하려면 다음 단계를 수행합니다.

1.  Azure AD 포털에서 테스트 계정을 만듭니다.

2.  **Canvas ** 응용 프로그램 통합 페이지에서 **사용자 할당**을 클릭합니다.

    ![사용자 할당](./media/active-directory-saas-canvas-lms-tutorial/IC775998.png "사용자 할당")

3.  테스트 사용자를 선택하고 **할당**을 클릭한 다음 **예**를 클릭하여 사용자의 할당을 확인합니다.

    ![예](./media/active-directory-saas-canvas-lms-tutorial/IC767830.png "예")

Single sign-on 설정을 테스트하려면 액세스 패널을 엽니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://msdn.microsoft.com/library/dn308586)를 참조하십시오.

<!---HONumber=August15_HO7-->