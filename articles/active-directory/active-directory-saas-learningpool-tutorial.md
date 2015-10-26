<properties pageTitle="자습서: Learningpool과 Azure Active Directory 통합 | Microsoft Azure" description="Azure Active Directory에서 Learningpool을 사용하여 Single Sign-On, 자동화된 프로비전 등을 사용하도록 설정하는 방법을 알아봅니다." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#자습서: Learningpool과 Azure Active Directory 통합
>[AZURE.TIP]피드백을 보내려면 [여기](http://go.microsoft.com/fwlink/?LinkId=533920)를 클릭하십시오.
  
이 자습서는 Azure 및 Learningpool의 통합을 보여주기 위한 것입니다. 이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

-   유효한 Azure 구독
-   Learningpool Single Sign-on이 설정된 구독
  
이 자습서를 완료한 후 Learningpool에 할당한 Azure AD 사용자가 Learningpool 회사 사이트 (서비스 공급자가 시작한 로그온)에서나 [액세스 패널 소개](https://msdn.microsoft.com/library/dn308586)를 사용하여 응용 프로그램에 Single Sign-On 할 수 있습니다.
  
이 자습서에 설명된 시나리오는 다음 구성 요소로 이루어져 있습니다.

1.  Learningpool에 응용 프로그램 통합 사용
2.  Single Sign-On 구성
3.  사용자 프로비전 구성
4.  사용자 할당

![시나리오](./media/active-directory-saas-learningpool-tutorial/IC791166.png "시나리오")
##Learningpool에 응용 프로그램 통합 사용
  
이 섹션은 Learningpool에 응용 프로그램 통합을 사용하도록 설정하는 방법을 간략하게 설명하기 위한 것입니다.

###Learningpool에 응용 프로그램 통합을 사용하도록 설정하려면 다음 단계를 수행합니다.

1.  Azure 관리 포털의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.

    ![Active Directory](./media/active-directory-saas-learningpool-tutorial/IC700993.png "Active Directory")

2.  **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.

3.  응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램**을 클릭합니다.

    ![응용 프로그램](./media/active-directory-saas-learningpool-tutorial/IC700994.png "응용 프로그램")

4.  페이지 맨 아래에 있는 **추가**를 클릭합니다.

    ![응용 프로그램 추가](./media/active-directory-saas-learningpool-tutorial/IC749321.png "응용 프로그램 추가")

5.  **수행할 작업** 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.

    ![갤러리에서 응용 프로그램 추가](./media/active-directory-saas-learningpool-tutorial/IC749322.png "갤러리에서 응용 프로그램 추가")

6.  **검색 상자**에 **Learningpool**을 입력합니다.

    ![응용 프로그램 갤러리](./media/active-directory-saas-learningpool-tutorial/IC795073.png "응용 프로그램 갤러리")

7.  결과 창에서 **Learningpool**을 선택하고 **완료**를 클릭하여 응용 프로그램을 추가합니다.

    ![Learningpool](./media/active-directory-saas-learningpool-tutorial/IC809577.png "Learningpool")
##Single Sign-On 구성
  
이 섹션은 사용자가 SAML 프로토콜 기반 페더레이션을 사용하여 Azure AD의 계정으로 Learningpool에 인증할 수 있게 하는 방법을 간략하게 설명하기 위한 것입니다.
  
Learningpool 응용 프로그램은 특정 서식에서 SAML 어설션을 예상하며 이는 **SAML 토큰 특성** 구성에 사용자 지정 특성 매핑을 추가합니다. 다음 스크린샷은 이에 대한 예제를 보여 줍니다.

![SAML 토큰 특성](./media/active-directory-saas-learningpool-tutorial/IC795074.png "SAML 토큰 특성")

###Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1.  Azure AD 포털의 **Learningpool** 응용 프로그램 통합 페이지에 있는 상단의 메뉴에서 **특성**을 클릭하여 **SAML 토큰 특성** 대화 상자를 엽니다.

    ![특성](./media/active-directory-saas-learningpool-tutorial/IC795075.png "특성")

2.  필요한 특성 매핑을 추가하려면 다음 단계를 수행합니다.

    ###

    |특성 이름 |특성 값 |
	|------------------------------|---------------------------|

     urn:oid:1.2.840.113556.1.4.221 | User.userprincipalname
	|-------------------------------|--------------------------|  
	 urn:oid:2.5.4.42|User.givenname   
    |urn:oid:0.9.2342.19200300.100.1.3|User.mail
    |urn:oid:2.5.4.4|User.surname

    1.  위의 테이블의 각 데이터 행에서 **사용자 특성 추가**를 클릭합니다.
    2.  **특성 이름** 텍스트 상자에서 해당 행에 표시된 특성 이름을 입력합니다.
    3.  **특성 값** 목록에서 해당 행에 표시된 특성 값을 선택합니다.
    4.  **완료**를 클릭합니다.

3.  **변경 내용 적용**을 클릭합니다.

4.  브라우저에서 **뒤로**를 클릭하여 **빠른 시작** 대화 상자를 다시 엽니다.

5.  **Single Sign-On 구성**을 클릭하여 **Single Sign-On 구성 ** 대화 상자를 엽니다.

    ![Single Sign-On 구성](./media/active-directory-saas-learningpool-tutorial/IC795076.png "Single Sign-On 구성")

6.  **Learningpool에 대한 사용자 로그온 방법을 선택하십시오.** 페이지에서 **Microsoft Azure AD Single Sign-on**을 선택하고 **다음**을 클릭합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-learningpool-tutorial/IC795077.png "Single Sign-On 구성")

7.  **앱 URL 구성** 페이지의 **Learningpool 로그온 URL** 텍스트 상자에 Learningpool 응용 프로그램에 로그온하기 위해 사용자가 사용하는 URL(예: https://parliament.preview.learningpool.com/auth/shibboleth/index.php))을 입력한 후 **다음**을 클릭합니다.

    ![앱 URL 구성](./media/active-directory-saas-learningpool-tutorial/IC795078.png "앱 URL 구성")

8.  **Learningpool에서 Single Sign-On 구성** 페이지에서 메타데이터를 다운로드 하려면 **메타데이터 다운로드**를 클릭한 다음 인증서 파일을 컴퓨터에 로컬로 저장합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-learningpool-tutorial/IC795079.png "Single Sign-On 구성")

9.  Learningpool 지원팀에 해당 메타데이터 파일을 전달합니다.

    >[AZURE.NOTE]Single Sign-on은 Learningpool 지원팀에서 사용할 수 있어야 합니다.

10. Azure AD 포털에서 Single Sign-On 구성 확인을 선택한 다음 **완료**를 클릭하여 **Single Sign-On 구성** 대화 상자를 닫습니다.

    ![Single Sign-On 구성](./media/active-directory-saas-learningpool-tutorial/IC795080.png "Single Sign-On 구성")
##사용자 프로비전 구성
  
Azure AD 사용자가 Learningpool에 로그인할 수 있도록 하려면 Learningpool로 프로비전되어야 합니다.
  
Learningpool을 프로비전하는 사용자를 구성할 작업 항목이 없습니다. Learningpool 지원 팀에서 사용자를 만들어야 합니다.

>[AZURE.NOTE]다른 Learningpool 사용자 계정 생성 도구 또는 Learningpool이 제공한 API를 사용하여 AAD 사용자 계정을 프로비전할 수 있습니다.

##사용자 할당
  
구성을 테스트하려면 응용 프로그램 사용을 허용하려는 Azure AD 사용자를 할당하여 액세스 권한을 부여해야 합니다.

###Learningpool에 사용자를 할당하려면 다음 단계를 수행합니다.

1.  Azure AD 포털에서 테스트 계정을 만듭니다.

2.  **Learningpool ** 응용 프로그램 통합 페이지에서 **사용자 할당**을 클릭합니다.

    ![사용자 할당](./media/active-directory-saas-learningpool-tutorial/IC795081.png "사용자 할당")

3.  테스트 사용자를 선택하고 **할당**을 클릭한 다음 **예**를 클릭하여 사용자의 할당을 확인합니다.

    ![예](./media/active-directory-saas-learningpool-tutorial/IC767830.png "예")
  
Single Sign-On 설정을 테스트하려면 액세스 패널을 엽니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://msdn.microsoft.com/library/dn308586)를 참조하십시오.

<!---HONumber=Oct15_HO3-->