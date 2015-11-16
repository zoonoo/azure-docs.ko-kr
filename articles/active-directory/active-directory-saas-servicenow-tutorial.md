<properties 
    pageTitle="자습서: ServiceNow와 Azure Active Directory 통합 | Microsoft Azure" 
    description="Azure Active Directory에서 ServiceNow를 사용하여 Single Sign-On, 자동화된 프로비전 등을 사용하도록 설정하는 방법에 대해 알아봅니다." 
    services="active-directory" 
    authors="MarkusVi"  
    documentationCenter="na" 
    manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="11/02/2015" 
    ms.author="markvi" />

#자습서: ServiceNow와 Azure Active Directory 통합
  
이 자습서는 Azure와 ServiceNow의 통합을 보여주기 위한 것입니다.
이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

-   유효한 Azure 구독
-   ServiceNow의 테넌트
  
이 자습서를 완료한 후, ServiceNow에 할당한 Azure AD 사용자는 ServiceNow 회사 사이트 (서비스 공급자가 시작한 로그온)에서나 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 사용하여 응용 프로그램에 Single Sign-On할 수 있습니다.
  
이 자습서에 설명된 시나리오는 다음 구성 요소로 이루어져 있습니다.

1.  ServiceNow에 응용 프로그램 통합 사용
2.  Single Sign-On 구성
3.  사용자 프로비전 구성
4.  사용자 할당

![시나리오](./media/active-directory-saas-servicenow-tutorial/IC769496.png "시나리오")
##ServiceNow에 응용 프로그램 통합 사용
  
이 섹션은 ServiceNow에 응용 프로그램 통합을 사용하도록 설정하는 방법을 간략하게 설명하기 위한 것입니다.

###ServiceNow에 응용 프로그램 통합을 사용하도록 설정하려면 다음 단계를 수행합니다.

1.  Azure 관리 포털의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.

    ![Active Directory](./media/active-directory-saas-servicenow-tutorial/IC700993.png "Active Directory")

2.  **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.

3.  응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램**을 클릭합니다.

    ![응용 프로그램](./media/active-directory-saas-servicenow-tutorial/IC700994.png "응용 프로그램")

4.  페이지 맨 아래에 있는 **추가**를 클릭합니다.

    ![응용 프로그램 추가](./media/active-directory-saas-servicenow-tutorial/IC749321.png "응용 프로그램 추가")

5.  **원하는 작업을 선택하세요.** 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.

    ![갤러리에서 응용 프로그램 추가](./media/active-directory-saas-servicenow-tutorial/IC749322.png "갤러리에서 응용 프로그램 추가")

6.  **검색 상자**에 **ServiceNow**를 입력합니다.

    ![응용 프로그램 갤러리](./media/active-directory-saas-servicenow-tutorial/IC701016.png "응용 프로그램 갤러리")

7.  결과 창에서 **ServiceNow**를 선택하고 **완료**를 클릭하여 응용 프로그램을 추가합니다.

    ![ServiceNow](./media/active-directory-saas-servicenow-tutorial/IC701017.png "ServiceNow")
##Single Sign-On 구성
  
이 섹션은 사용자가 SAML 프로토콜 기반 페더레이션을 사용하여 Azure AD의 계정으로 ServiceNow에 인증할 수 있게 하는 방법을 간략하게 설명하기 위한 것입니다.

이 절차의 일부로 base-64로 인코딩된 인증서를 Dropbox for Business 테넌트로 업로드 해야 합니다. 이 절차를 잘 모르는 경우 [이진 인증서를 텍스트 파일로 변환하는 방법](http://youtu.be/PlgrzUZ-Y1o)을 참조하십시오.

###Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1.  Azure AD 포털의 **ServiceNow** 응용 프로그램 통합 페이지에서 **Single Sign-On 구성**을 클릭하여 **Single Sign-On 구성** 대화 상자를 엽니다.

    ![Single Sign-On 구성](./media/active-directory-saas-servicenow-tutorial/IC749323.png "Single Sign-On 구성")

2.  **ServiceNow에 대한 사용자 로그온 방법을 선택하세요.** 페이지에서 **Microsoft Azure AD Single Sign-On**을 선택하고 **다음**을 클릭합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-servicenow-tutorial/IC749324.png "Single Sign-On 구성")

3.  **앱 설정 구성** 페이지에서 다음 단계를 수행합니다.

    ![앱 URL 구성](./media/active-directory-saas-servicenow-tutorial/IC769497.png "앱 URL 구성")

    a. **ServiceNow 로그인 URL** 텍스트 상자에 사용자가 ServiceNow 응용 프로그램에 로그인하는 데 사용한 URL을 입력합니다.(예: *https://\<InstanceName>.service-now.com*)

    b. **발급자 URL** 텍스트 상자에 사용자가 ServiceNow 응용 프로그램에 로그인하는 데 사용한 URL을 입력합니다.(예: *https://\<InstanceName>.service-now.com*)

    c. **다음**를 클릭합니다.

4.  **Single Sign-On 자동 구성** 페이지에서 **수동으로 Single Sign On하도록 이 응용 프로그램 구성**을 선택하고 **다음**을 클릭합니다.

    ![앱 URL 구성](./media/active-directory-saas-servicenow-tutorial/IC7694971.png "앱 URL 구성")



4.  **ServiceNow에서 Single Sign-On 구성** 페이지에서 **인증서 다운로드**를 클릭하고 컴퓨터에 로컬로 인증서 파일을 저장한 후 **다음**을 클릭합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-servicenow-tutorial/IC749325.png "Single Sign-On 구성")

1. ServiceNow 응용 프로그램에 관리자 권한으로 로그온합니다.

2. 왼쪽의 탐색 창에서 **속성**을 클릭합니다.

    ![앱 URL 구성](./media/active-directory-saas-servicenow-tutorial/IC7694980.png "앱 URL 구성")


3. **여러 공급자 SSO 속성** 대화 상자에서 다음 단계를 수행합니다.

    ![앱 URL 구성](./media/active-directory-saas-servicenow-tutorial/IC7694981.png "앱 URL 구성")

    a. **여러 공급자 SSO 사용**을 **예**로 선택합니다.

    b. **디버그 로깅에 여러 공급자 SSO 통합을 사용하도록 설정**을 **예**로 선택합니다.

    c. **...하는 사용자 테이블의 필드** 텍스트 상자에서 **user\_name**을 입력합니다.

    d. **Save**를 클릭합니다.



1. 왼쪽의 탐색 창에서 **x509 Certificates**을 클릭합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-servicenow-tutorial/IC7694973.png "Single Sign-On 구성")


1. **X.509 인증서** 대화 상자에서 **새로 만들기**를 클릭합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-servicenow-tutorial/IC7694974.png "Single Sign-On 구성")


1. **X.509 Certificates** 대화 상자에서 다음 단계를 수행합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-servicenow-tutorial/IC7694975.png "Single Sign-On 구성")

    a. **새로 만들기**를 클릭합니다.

    b. **이름** 텍스트 상자에서 구성할 이름을 입력합니다.(예: **TestSAML2.0**).

    c. **활성**을 선택합니다.

    d. **형식**으로 **PEM**을 선택합니다.

    e. **형식**으로 **저장소 인증서 신뢰**를 선택합니다.

    f. 다운로드한 인증서에서 Base-64로 인코딩한 파일을 만듭니다.
    > [AZURE.NOTE]자세한 내용은 [이진 인증서를 텍스트 파일로 변환하는 방법](http://youtu.be/PlgrzUZ-Y1o)을 참조하세요.
    
    g. Base 64로 인코딩된 인증서를 메모장에서 열고, 내용을 클립보드에 복사한 다음 **PEM 인증서** 텍스트 상자에 붙여 넣습니다.

    h. **업데이트**를 클릭합니다.


1. 왼쪽의 탐색 창에서 **ID 공급자**를 클릭합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-servicenow-tutorial/IC7694976.png "Single Sign-On 구성")

1. **ID 공급자** 대화 상자에서 **새로 만들기**를 클릭합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-servicenow-tutorial/IC7694977.png "Single Sign-On 구성")


1. **ID 공급자** 대화 상자에서 **SAML2 Update1?**를 클릭합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-servicenow-tutorial/IC7694978.png "Single Sign-On 구성")


1. SAML2 Update1 속성 대화 상자에서 다음 단계를 수행합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-servicenow-tutorial/IC7694982.png "Single Sign-On 구성")


    a. **이름** 텍스트 상자에서 구성할 이름을 입력합니다.(예: **SAML 2.0**)

    b. **사용자 필드** 텍스트 상자에 **전자 메일**을 입력합니다.

    c. Azure AD 포털에서 **ID 공급자 ID** 값을 복사한 다음 **ID 공급자 URL** 텍스트 상자에 붙여넣습니다.

    d. Azure AD 포털에서 **인증 요청 URL** 값을 복사한 다음 **ID 공급자의 AuthnRequest** 텍스트 상자에 붙여넣습니다.

    e. Azure AD 포털에서 **Single Sign-Out 서비스 URL** 값을 복사한 다음 **ID 공급자의 SingleLogoutRequest** 텍스트 상자에 붙여넣습니다.

    f. **ServiceNow 홈페이지** 텍스트 상자에 ServiceNow 인스턴스 홈페이지의 URL을 입력합니다.

    > [AZURE.NOTE]ServiceNow 인스턴스 홈페이지의 URL은 **ServieNow 테넌트 URL** 및 **/navpage.do**의 연결입니다.(e.g.: **https://fabrikam.service-now.com/navpage.do*).
 

    g. **엔터티 ID/발급자** 텍스트 상자에 ServiceNow 테넌트의 URL을 입력합니다.

    h. **대상 URL** 텍스트 상자에 ServiceNow 테넌트의 URL을 입력합니다.

    i **IDP의 SingleLogoutRequest에 대한 프로토콜 바인딩** 텍스트 상자에 **urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect**를 입력합니다.

    j. NameID 정책 텍스트 상자에 **urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified**를 입력합니다.

    k. **AuthnContextClass 만들기**의 선택을 취소합니다.

    l. **AuthnContextClassRef 메서드**에 ****http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password**를 입력합니다.

    m. **시계 기울이기** 텍스트 상자에 **60**을 입력합니다.

    n. **단일 로그인 스크립트**로 **MultiSSO\_SAML2\_Update1**를 선택합니다.

    o. **x509 인증서**로 이전 단계에서 만든 인증서를 선택합니다.

    p. **제출**을 클릭합니다.



6. Azure AD 포털에서 Single Sign-On 구성 확인을 선택하고 **다음**을 클릭합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-servicenow-tutorial/IC7694990.png "Single Sign-On 구성")

7. **Single Sign-On 확인** 페이지에서 **완료**를 클릭합니다.
 
    ![Single Sign-On 구성](./media/active-directory-saas-servicenow-tutorial/IC7694991.png "Single Sign-On 구성")



##사용자 프로비전 구성
  
이 섹션에서는 ServiceNow에 Active Directory 사용자 계정을 프로비저닝할 수 있도록 설정하는 방법을 간략하게 설명합니다.


### 사용자 프로비전을 구성하려면

1. Azure 관리 포털의 **ServiceNow** 응용 프로그램 통합 페이지에서 **사용자 프로비전 구성**을 클릭합니다. <br><br> ![사용자 프로비전](./media/active-directory-saas-servicenow-tutorial/IC769498.png "사용자 프로비전")


2. **ServiceNow 자격 증명을 입력하여 자동 사용자 프로비전 사용** 페이지에서 사용자 프로비전 구성 설정을 제공합니다.

     2\.1. **ServiceNow 인스턴스 이름** 텍스트 상자에 ServiceNow 인스턴스 이름을 입력합니다.

     2\.2. **ServiceNow 관리자 사용자 이름** 텍스트 상자에 ServiceNow 관리자 계정의 이름을 입력합니다.

     2\.3. **ServiceNow 관리자 암호** 텍스트 상자에 이 계정의 암호를 입력합니다.

     2\.4. **유효성 검사**를 클릭하여 구성을 확인합니다.

     2\.5. **다음** 단추를 클릭하여 **다음 단계** 페이지를 엽니다.

     2\.6. 이 응용 프로그램에 모든 사용자를 프로비전하려는 경우 "** 이 응용 프로그램에 대한 디렉터리의 모든 사용자 계정을 자동으로 프로비전**"을 선택합니다. <br><br> ![다음 단계](./media/active-directory-saas-servicenow-tutorial/IC698804.png "다음 단계")

     2\.7. **다음 단계** 페이지에서 **완료**를 클릭하여 구성을 저장합니다.











##사용자 할당
  
구성을 테스트하려면 응용 프로그램 사용을 허용하려는 Azure AD 사용자를 할당하여 액세스 권한을 부여해야 합니다.

###ServiceNow에 사용자를 할당하려면 다음 단계를 수행합니다.

1.  Azure AD 포털에서 테스트 계정을 만듭니다.

2.  **ServiceNow ** 응용 프로그램 통합 페이지에서 **사용자 할당**을 클릭합니다.

    ![사용자 할당](./media/active-directory-saas-servicenow-tutorial/IC769499.png "사용자 할당")

3.  테스트 사용자를 선택하고 **할당**을 클릭한 다음 **예**를 클릭하여 사용자의 할당을 확인합니다.

    ![예](./media/active-directory-saas-servicenow-tutorial/IC767830.png "예")
  
Single Sign-On 설정을 테스트하려면 액세스 패널을 엽니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하십시오.


## 추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On이란 무엇입니까?](active-directory-appssoaccess-whatis.md)

<!---HONumber=Nov15_HO2-->