<properties pageTitle="자습서: Mindflash와 Azure Active Directory 통합 | Microsoft Azure" description="Azure Active Directory에서 Mindflash를 사용하여 Single Sign-On, 자동화된 프로비전 등을 사용하도록 설정하는 방법을 알아봅니다." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#자습서: Mindflash와 Azure Active Directory 통합
>[AZURE.TIP]피드백을 보내려면 [여기](http://go.microsoft.com/fwlink/?LinkId=528416)를 클릭하십시오.
  
이 자습서는 Azure 및 Mindflash의 통합을 보여주기 위한 것입니다. 이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

-   유효한 Azure 구독
-   Mindflash Single Sign-On이 설정된 구독
  
이 자습서를 완료한 후 Mindflashc에 할당한 Azure AD 사용자가 Mindflash 회사 사이트 (서비스 공급자가 시작한 로그온)에서나 [액세스 패널 소개](https://msdn.microsoft.com/library/dn308586)를 사용하여 응용 프로그램에 Single Sign-On할 수 있습니다.
  
이 자습서에 설명된 시나리오는 다음 구성 요소로 이루어져 있습니다.

1.  Mindflash에 응용 프로그램 통합 사용
2.  Single Sign-On 구성
3.  사용자 프로비전 구성
4.  사용자 할당

![시나리오](./media/active-directory-saas-mindflash-tutorial/IC787132.png "시나리오")
##Mindflash에 응용 프로그램 통합 사용
  
이 섹션은 Mindflash에 응용 프로그램 통합을 사용하도록 설정하는 방법을 간략하게 설명하기 위한 것입니다.

###Mindflash에 응용 프로그램 통합을 사용하도록 설정하려면

1.  Azure 관리 포털의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.

    ![Active Directory](./media/active-directory-saas-mindflash-tutorial/IC700993.png "Active Directory")

2.  **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.

3.  응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램**을 클릭합니다.

    ![응용 프로그램](./media/active-directory-saas-mindflash-tutorial/IC700994.png "응용 프로그램")

4.  페이지 맨 아래에 있는 **추가**를 클릭합니다.

    ![응용 프로그램 추가](./media/active-directory-saas-mindflash-tutorial/IC749321.png "응용 프로그램 추가")

5.  **원하는 작업을 선택하세요.** 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.

    ![갤러리에서 응용 프로그램 추가](./media/active-directory-saas-mindflash-tutorial/IC749322.png "갤러리에서 응용 프로그램 추가")

6.  **검색 상자**에 **Mindflash**를 입력합니다.

    ![응용 프로그램 갤러리](./media/active-directory-saas-mindflash-tutorial/IC787133.png "응용 프로그램 갤러리")

7.  결과 창에서 **Mindflash**를 선택하고 **완료**를 클릭하여 응용 프로그램을 추가합니다.

    ![Mindflash](./media/active-directory-saas-mindflash-tutorial/IC787134.png "Mindflash")
##Single Sign-On 구성
  
이 섹션은 사용자가 SAML 프로토콜 기반 페더레이션을 사용하여 Azure AD의 계정으로 Mindflash에 인증할 수 있게 하는 방법을 간략하게 설명하기 위한 것입니다.

###Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1.  Azure AD 포털의 **Mindflash** 응용 프로그램 통합 페이지에서 **Single Sign-On 구성**을 클릭하여 **Single Sign-On 구성** 대화 상자를 엽니다.

    ![Single Sign-On 구성](./media/active-directory-saas-mindflash-tutorial/IC787135.png "Single Sign-On 구성")

2.  **Mindflash에 대한 사용자 로그온 방법을 선택하십시오.** 페이지에서 **Microsoft Azure AD Single Sign-On**을 선택하고 **다음**을 클릭합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-mindflash-tutorial/IC787136.png "Single Sign-On 구성")

3.  **앱 URL 구성** 페이지의 **Sign-on URL** 텍스트 상자에 다음 패턴 "**http://company.mindflash.com*"을 사용하여 URL을 입력한 후 **다음**을 클릭합니다.

    ![앱 URL 구성](./media/active-directory-saas-mindflash-tutorial/IC787137.png "앱 URL 구성")

4.  **Mindflash에서 Single Sign-On 구성** 페이지에서 **메타데이터 다운로드**를 클릭한 다음 컴퓨터에 메타데이터 파일을 저장합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-mindflash-tutorial/IC787138.png "Single Sign-On 구성")

5.  메타데이터를 Mindflash 지원 팀에 보냅니다.

    >[AZURE.NOTE]Single Sign-On 구성을 Mindflash 지원 팀에서 수행할 수 있습니다. 구성이 완료되는 즉시 알림을 받아볼 수 있습니다.

6.  Azure AD 포털에서 Single Sign-On 구성 확인을 선택하고 **완료**를 클릭하여 **Single Sign-On 구성** 대화 상자를 닫습니다.

    ![Single Sign-On 구성](./media/active-directory-saas-mindflash-tutorial/IC787139.png "Single Sign-On 구성")
##사용자 프로비전 구성
  
Azure AD 사용자가 Mindflash에 로그인할 수 있도록 하려면 Mindflash로 프로비전되어야 합니다. Mindflash의 경우 프로비전은 수동 작업입니다.

###사용자 계정을 프로비전하려면 다음 단계를 수행합니다.

1.  **Mindflash** 회사 사이트에 관리자 권한으로 로그인합니다.

2.  **사용자 관리**로 이동합니다.

    ![사용자 관리](./media/active-directory-saas-mindflash-tutorial/IC787140.png "사용자 관리")

3.  **사용자 추가**를 클릭한 다음 **새로 만들기**를 클릭합니다.

4.  **새 사용자 추가** 섹션에서 다음 단계를 수행합니다.

    ![새 사용자 추가](./media/active-directory-saas-mindflash-tutorial/IC787141.png "새 사용자 추가")

    1.  관련된 텍스트 상자에 프로비전할 유효한 AAD 계정의 **이름**, **성** 및 **이메일**을 입력합니다.
    2.  **추가**를 클릭합니다.

>[AZURE.NOTE]다른 Mindflash 사용자 계정 생성 도구 또는 Mindflash가 제공한 API를 사용하여 AAD 사용자 계정을 프로비전할 수 있습니다.

##사용자 할당
  
구성을 테스트하려면 응용 프로그램 사용을 허용하려는 Azure AD 사용자를 할당하여 액세스 권한을 부여해야 합니다.

###Mindflash에 사용자를 할당하려면 다음 단계를 수행합니다.

1.  Azure AD 포털에서 테스트 계정을 만듭니다.

2.  **Mindflash ** 응용 프로그램 통합 페이지에서 **사용자 할당**을 클릭합니다.

    ![사용자 할당](./media/active-directory-saas-mindflash-tutorial/IC787142.png "사용자 할당")

3.  테스트 사용자를 선택하고 **할당**을 클릭한 다음 **예**를 클릭하여 사용자의 할당을 확인합니다.

    ![예](./media/active-directory-saas-mindflash-tutorial/IC767830.png "예")
  
Single Sign-On 설정을 테스트하려면 액세스 패널을 엽니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://msdn.microsoft.com/library/dn308586)를 참조하십시오.

<!---HONumber=August15_HO7-->