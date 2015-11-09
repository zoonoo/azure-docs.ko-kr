<properties 
    pageTitle="자습서: SAP HANA Cloud Platform과 Azure Active Directory 통합 | Microsoft Azure" 
    description="Azure Active Directory에서 SAP HANA Cloud Platform을 사용하여 Single Sign-On, 자동화된 프로비전 등을 사용하도록 설정하는 방법을 알아봅니다." 
    services="active-directory" 
    authors="markusvi"  
    documentationCenter="na" 
    manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="10/22/2015" 
    ms.author="markvi" />

#자습서: SAP HANA Cloud Platform과 Azure Active Directory 통합
  
이 자습서는 Azure 및 SAP HANA Cloud Platform의 통합을 보여 주기 위한 것입니다. 이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

-   유효한 Azure 구독
-   SAP HANA Cloud Platform 계정
  
이 자습서를 완료한 후 SAP HANA Cloud Platform에 할당한 Azure AD 사용자가 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 사용하여 응용 프로그램에 Single Sign-On 할 수 있습니다.

>[AZURE.IMPORTANT]사용자 고유의 응용 프로그램을 배포하거나 SAP HANA Cloud Platform 계정에 대 한 single sign 테스트하도록 응용 프로그램을 구독해야 합니다. 이 자습서에서는 응용 프로그램이 계정에 배포됩니다.
  
이 자습서에 설명된 시나리오는 다음 구성 요소로 이루어져 있습니다.

1.  SAP HANA Cloud Platform에 응용 프로그램 통합 사용
2.  Single Sign-On 구성
3.  사용자에게 역할 할당
4.  사용자 할당

![시나리오](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790795.png "시나리오")
##SAP HANA Cloud Platform에 응용 프로그램 통합 사용
  
이 섹션은 SAP HANA Cloud Platform에 응용 프로그램 통합을 사용하도록 설정하는 방법을 간략하게 설명하기 위한 것입니다.

###SAP HANA Cloud Platform에 응용 프로그램 통합을 사용하도록 설정하려면 다음 단계를 수행합니다.

1.  Azure 관리 포털의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.

    ![Active Directory](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC700993.png "Active Directory")

2.  **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.

3.  응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램**을 클릭합니다.

    ![응용 프로그램](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC700994.png "응용 프로그램")

4.  페이지 맨 아래에 있는 **추가**를 클릭합니다.

    ![응용 프로그램 추가](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC749321.png "응용 프로그램 추가")

5.  **원하는 작업을 선택하세요.** 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.

    ![갤러리에서 응용 프로그램 추가](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC749322.png "갤러리에서 응용 프로그램 추가")

6.  **검색 상자**에 **SAP HANA Cloud Platform**을 입력합니다.

    ![응용 프로그램 갤러리](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790796.png "응용 프로그램 갤러리")

7.  결과 창에서 **SAP HANA Cloud Platform**을 선택하고 **완료**를 클릭하여 응용 프로그램을 추가합니다.

    ![SAP Hana](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC793929.png "SAP Hana")
##Single Sign-On 구성
  
이 섹션은 사용자가 SAML 프로토콜 기반 페더레이션을 사용하여 Azure AD의 계정으로 SAP HANA Cloud Platform에 인증할 수 있게 하는 방법을 간략하게 설명하기 위한 것입니다. 이 절차의 일부로 base-64로 인코딩된 인증서 파일을 SAP HANA Cloud Platform 테넌트에 업로드해야 합니다. 이 절차를 잘 모르는 경우 [이진 인증서를 텍스트 파일로 변환하는 방법](http://youtu.be/PlgrzUZ-Y1o)을 참조하십시오.

###Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1.  Azure AD 포털의 **SAP HANA Cloud Platform** 응용 프로그램 통합 페이지에서 **Single Sign-On 구성**을 클릭하여 **Single Sign-On 구성 ** 대화 상자를 엽니다.

    ![Single Sign-On 구성](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC778552.png "Single Sign-On 구성")

2.  **SAP HANA Cloud Platform에 대한 사용자 로그온 방법을 선택하십시오.** 페이지에서 **Microsoft Azure AD Single Sign-On**을 선택하고 **다음**을 클릭합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790797.png "Single Sign-On 구성")

3.  다른 웹 브라우저 창에서, https://account.\<landscape host>.ondemand.com/cockpit (예: **https://account.hanatrial.ondemand.com/cockpit*)의 SAP HANA Cloud Platform Cockpit에 로그온합니다.

4.  **신뢰** 탭을 클릭합니다.

    ![트러스트](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790800.png "트러스트")

5.  신뢰 관리 섹션에서 다음 단계를 수행합니다.

    ![메타데이터 가져오기](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC793930.png "메타데이터 가져오기")

    1.  **로컬 서비스 공급자** 탭을 클릭합니다.
    2.  SAP HANA Cloud Platform 메타데이터 파일을 다운로드하려면 **메타데이터 가져오기**를 클릭합니다.

6.  Azure Active Directory 포털의 **앱 URL 구성** 페이지에서 다음 단계를 수행한 후 **다음**을 선택합니다.

    ![앱 URL 구성](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790798.png "앱 URL 구성")

    1.  **로그온 URL** 텍스트 상자에 **SAP HANA Cloud Platform** 응용 프로그램에 사용자가 로그인하기 위해 사용한 URL을 입력합니다. SAP HANA Cloud Platform 응용 프로그램에서 보호 된 리소스의 계정 관련 URL입니다. URL은 다음 패턴 *https://\<applicationName><accountName>.<landscape host>.ondemand.com/<path\_to\_protected\_resource>* (예: **https://xleavep1941203872trial.hanatrial.ondemand.com/xleave*)을 기반으로 합니다.

		>[AZURE.NOTE]사용자의 인증을 필요로 하는 SAP HANA Cloud Platform 응용 프로그램의 URL입니다.

    2.  다운로드한 SAP HANA Cloud Platform 메타데이터 파일을 연 다음 **ns3:AssertionConsumerService** 태그를 찾습니다.
    3.  **위치** 특성의 값을 복사한 다음 **SAP HANA Cloud Platform 회신 URL** 텍스트 상자로 붙여넣습니다.

7.  **SAP HANA Cloud Platform에서 Single Sign-On 구성** 페이지에서 메타데이터를 다운로드하려면 **메타데이터 다운로드**를 클릭한 다음 파일을 컴퓨터에 저장합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790799.png "Single Sign-On 구성")

8.  SAP HANA Cloud Platform Cockpit의 **로컬 서비스 공급자** 섹션에서 다음 단계를 수행합니다.

    ![트러스트 관리](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC793931.png "트러스트 관리")

    1.  **편집**을 클릭합니다.
    2.  **구성 유형**으로 **사용자 지정**을 선택합니다.
    3.  **로컬 공급자 이름**으로 기본값을 그대로 사용합니다.
    4.  **서명 키** 및 **서명 인증서** 키 쌍을 생성하려면 **키 쌍 생성**을 클릭합니다.
    5.  **주 전파**로 **사용 안 함**을 선택합니다.
    6.  **강제 인증**으로 **사용 안 함**을 선택합니다.
    7.  **Save**를 클릭합니다.

9.  **신뢰할 수 있는 ID 공급자** 탭을 클릭한 다음 **신뢰할 수 있는 ID 공급자 추가**를 클릭합니다.

    ![트러스트 관리](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790802.png "트러스트 관리")

    >[AZURE.NOTE]신뢰할 수 있는 ID 공급자 목록을 관리하려면, 로컬 서비스 공급자 섹션에서 사용자 할당 구성 형식을 선택해야 합니다. 기본 구성 유형으로 SAP ID 서비스에 대한 편집할 수 없는 암시적 트러스트가 있습니다. 없음의 경우 신뢰 설정이 필요 없습니다.

10. **일반** 탭을 클릭한 다음 **찾아보기**를 클릭하여 다운로드한 메타데이터 파일을 업로드합니다.

    ![트러스트 관리](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC793932.png "트러스트 관리")

    >[AZURE.NOTE]메타데이터 파일을 업로드한 후 **Single Sign-On URL**, **Single Logout URL** 및 **서명 인증서**의 값이 자동으로 채워집니다.

11. **특성** 탭을 클릭합니다.

12. **특성** 탭에서 다음 단계를 수행합니다.

    ![특성](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790804.png "특성")

    1.  **어설션 기반 특성 추가**를 클릭하여 다음 어설션 기반 특성을 추가합니다.

        |어설션 특성| 보안 주체 특성|
		|-------------------|--------------------|
        |http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname|이름|--------------------|--------------------| |http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname|성|---| |http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress|email|

    >[AZURE.NOTE]특성의 구성은 SAML 응답에서 예상되는 특성 및 코드의 이 특성에 액세스하는 이름(보안 주체 특성)과 같은 HCP의 응용 프로그램이 개발된 방법에 따라 다릅니다.
    >  
    >a. 스크린샷의 **기본 특성**은 설명 목적입니다. 시나리오 작업을 작성할 필요가 없습니다.
    >
    >b. 스크린샷에 표시되는 **보안 주체 특성**의 이름 및 값은 응용 프로그램 개발 방법에 따라 달라집니다. 응용 프로그램에 다른 매핑이 필요할 수 있습니다.

13. Azure 포털의 **SAP HANA Cloud Platform에서 Single Sign-On 구성** 대화 상자 페이지에서 Single Sign-On 구성 확인을 선택한 다음 **완료**를 클릭합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC796933.png "Single Sign-On 구성")
  
선택적 단계로, Azure Active Directory ID 공급자에 대한 어설션 기반 그룹을 구성할 수 있습니다.

>[AZURE.NOTE]SAP HANA Cloud Platform에서 그룹을 사용하면 한 명 이상의 사용자를 SAP HANA Cloud Platform 응용 프로그램의 하나 이상의 역할에 동적으로 할당할 수 있으며 SAML 2.0 어셜션의 특성 값으로 결정됩니다. 예를 들어, 어설션이 특성 "*contract=temporary*"를 포함하는 경우 적용되는 모든 사용자가 그룹 "*임시*"에 추가됩니다. 그룹 "*임시*"는 SAP HANA Cloud Platform 계정에 배포되는 하나 이상의 응용 프로그램에서 하나 이상의 역할을 포함할 수 있습니다.
>  
>SAP HANA Cloud Platform 계정에서 응용 프로그램의 하나 이상의 역할에 여러 사용자를 대량 할당하는 경우 어셜션 기반 그룹을 사용합니다. 특정 역할에 단일 또는 적은 수의 사용자를 할당하려는 경우, SAP HANA Cloud Platform cockpit의 “**권한 부여**” 탭에 직접 할당하는 것이 좋습니다.

##사용자에게 역할 할당
  
Azure AD 사용자가 SAP HANA Cloud Platform으로 로그를 사용하려면, SAP HANA Cloud Platform에서 역할을 할당해야 합니다.

###사용자에게 역할을 할당하려면 다음 단계를 수행합니다.

1.  **SAP HANA Cloud Platform** cockpit에 로그인합니다.

2.  다음 단계를 수행합니다.

    ![권한 부여](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790805.png "권한 부여")

    1.  **권한 부여**를 클릭합니다.
    2.  **사용자** 탭을 클릭합니다.
    3.  **사용자** 텍스트 상자에 사용자의 이메일 주소를 입력합니다.
    4.  **할당**을 클릭하여 사용자에 역할을 할당합니다.
    5.  **Save**를 클릭합니다.

##사용자 할당
  
구성을 테스트하려면 응용 프로그램 사용을 허용하려는 Azure AD 사용자를 할당하여 액세스 권한을 부여해야 합니다.

###SAP HANA Cloud Platform에 사용자를 할당하려면 다음 단계를 수행합니다.

1.  Azure AD 포털에서 테스트 계정을 만듭니다.

2.  **SAP HANA Cloud Platform** 응용 프로그램 통합 페이지에서 **사용자 할당**을 클릭합니다.

    ![사용자 할당](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790806.png "사용자 할당")

3.  테스트 사용자를 선택하고 **할당**을 클릭한 다음 **예**를 클릭하여 사용자의 할당을 확인합니다.

    ![예](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC767830.png "예")
  
Single Sign-On 설정을 테스트하려면 액세스 패널을 엽니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하십시오.

<!---HONumber=Nov15_HO1-->