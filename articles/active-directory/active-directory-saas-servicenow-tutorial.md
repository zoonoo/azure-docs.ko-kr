<properties pageTitle="자습서: ServiceNow와 Azure Active Directory 통합 | Microsoft Azure" description="Azure Active Directory에서 ServiceNow를 사용하여 Single Sign-On, 자동화된 프로비전 등을 사용하도록 설정하는 방법을 알아봅니다!." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#자습서: ServiceNow와 Azure Active Directory 통합
>[AZURE.TIP]피드백을 보내려면 [여기](http://go.microsoft.com/fwlink/?LinkId=521880)를 클릭하십시오.
  
이 자습서는 Azure와 ServiceNow의 통합을 보여주기 위한 것입니다.
이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

-   유효한 Azure 구독
-   ServiceNow의 테넌트
  
이 자습서를 완료한 후 ServiceNow에 할당한 Azure AD 사용자가 ServiceNow 회사 사이트 (서비스 공급자가 시작한 로그온)에서나 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 사용하여 응용 프로그램에 Single Sign-On할 수 있습니다.
  
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

5.  **수행할 작업** 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.

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

2.  **ServiceNow에 대한 사용자 로그온 방법을 선택하십시오.** 페이지에서 **Microsoft Azure AD Single Sign-On**을 선택하고 **다음**을 클릭합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-servicenow-tutorial/IC749324.png "Single Sign-On 구성")

3.  **ServiceNow 로그인 URL** 텍스트 상자의 **앱 URL 구성** 페이지에서 "*https://<InstanceName>.servicenow.com*" 패턴을 사용하여 URL을 입력하고 **다음**을 누릅니다.

    ![앱 URL 구성](./media/active-directory-saas-servicenow-tutorial/IC769497.png "앱 URL 구성")

4.  **ServiceNow에서 Single Sign-On 구성** 페이지에서 **인증서 다운로드**를 클릭하고, 컴퓨터에 로컬로 인증서 파일을 저장하고 **다음**을 클릭합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-servicenow-tutorial/IC749325.png "Single Sign-On 구성")

5.  ServiceNow 테넌트의 왼쪽에 있는 탐색 모음에서 **속성**을 클릭하여 **SAML 2.0 Single Sign on 속성** 페이지를 엽니다.


6. **SAML 2.0 Single sign-on 속성** 페이지에서 다음 단계를 수행합니다.

     6.1. **외부 인증 사용**으로 **예**를 선택합니다.


     6.2. **사용자 정보로 SAML2 보안 토큰을 발급할 수 있는 ID 공급자 URL** 텍스트 상자에 **https://sts.windows.net/<테넌트의 GUID>/**를 입력합니다.


     6.3. **ID 공급자의 AuthnRequest 서비스에 대한 기본 URL** 텍스트 상자에 **https://login.windows.net/<테넌트의 GUID>/saml2**를 입력합니다.


     6.4. **ID 공급자의 SingleLogoutRequest 서비스에 대한 기본 URL** 텍스트 상자에 **https://login.windows.net/<테넌트의 GUID>/saml2**를 입력합니다.


     6.5. **ID 공급자의 SingleLogoutRequest 서비스에 대한 프로토콜 바인딩** 텍스트 상자에 **urn: oasis:이름:tc:SAML:2.0:bindings:HTTP-리디렉션**를 입력합니다.

     6.6. **Sign LogoutRequest**으로 **예**를 선택합니다.

     6.7. **세션이 인증되지 않거나, 처음으로 로그인 하기 때문에 SAML 2.0 single sign-on을 할 수 없는 경우, 이 URL로 리디렉션** 텍스트 상자에 **https://login.windows.net/<테넌트의 GUID>/saml2**를 입력합니다.

  

7. **서비스 공급자 (Service-now) 속성** 섹션에서 다음 단계를 수행합니다.

     7.1. **Service-now 인스턴스 홈페이지의 URL** 텍스트 상자에 ServiceNow 인스턴스 홈페이지 URL을 입력합니다. ServiceNow 인스턴스 홈페이지의 URL은 **ServiceNow 테넌트 URL** 및 **/navpage.do**: **https://<InstanceName>.service-now.com/navpage.do** <br><br>   ![Service-now 인스턴스 홈페이지](./media/active-directory-saas-servicenow-tutorial/IC700342.png "Service-now 인스턴스 홈페이지")의 연결입니다.


     7.2. **엔터티 ID 또는 발급자** 텍스트 상자에, 테넌트의 URL을 입력합니다.

     7.3. **SAML2 토큰을 허용하는 대상 그룹 uri** 텍스트 상자에, 테넌트의 URL을 입력합니다.

     7.4. **SAMLResponse의 주체 NameID 요소와 일치하는 사용자 테이블 필드** 텍스트 상자에 **메일**을 입력합니다.

     7.5. **SAMLResponse의 주체 NameID 반환에 사용할 NameID 정책** 텍스트 상자에 **urn:oasis:이름:tc:SAML:1.1:nameid-형식: 지정되지 않음**을 입력합니다.

     7.6 **AuthnRequest 문에서 AuthnContextClass 요청 만들기** 옵션을 선택 취소 상태로 그대로 둡니다.

     7.7 **ID 공급자에 대한 SAML 2.0 AuthnRequest에 포함 될 AuthnContextClassRef 메서드** 텍스트 상자에 **http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password**을 입력합니다.



8. **고급 설정** 섹션에서 다음 단계를 수행 합니다.

     8.1. **"notBefore" 제약 조건 이전 또는 "notOnOrAfter" 제약 조건 이후에 여전히 유효한 지 고려해야 할 초 단위 수** 텍스트 상자에 **60**를 입력합니다.


9. 구성을 저장 하려면 **저장**을 클릭합니다.

10. 왼쪽의 탐색 모음에서 **인증서**를 클릭하여 **인증서** 페이지를 엽니다.



11. 인증서 페이지에서 인증서를 업로드 하려면 다음 단계를 수행 합니다.

     11.1. **새로 만들기**를 클릭합니다.

     11.2. **이름** 텍스트 상자에 **SAML 2.0**을 입력합니다.

     11.3. **활성**을 선택합니다.

     11.4. **형식**으로 **PEM**을 선택합니다.

     11.5. 다운로드한 인증서에서 Base-64로 인코딩된 파일을 만듭니다.  > [AZURE.NOTE]자세한 내용은 [이진 인증서를 텍스트 파일로 변환하는 방법](http://youtu.be/PlgrzUZ-Y1o)을 참조하십시오.

     11.6. **메모장**에서 Base-64로 인코딩된 파일을 열고 이 파일의 내용을 클립보드에 복사합니다.

     11.7. 클립보드의 내용을 **PEM Certificate** 텍스트 상자에 붙여 넣습니다.

     11.8. **Submit**를 클릭합니다.



12. Azure AD 포털에서 Single Sign-On 구성 확인을 선택하고 완료를 클릭하여 Single Sign-On 구성 대화 상자를 닫습니다. <br><br> ![Single Sign-On 구성](./media/active-directory-saas-servicenow-tutorial/IC749326.png "Single Sign-On 구성")




##사용자 프로비전 구성
  
이 섹션에서는 ServiceNow에 Active Directory 사용자 계정을 프로비저닝할 수 있도록 설정하는 방법을 간략하게 설명합니다.


### 사용자 프로비전을 구성하려면

1. Azure 관리 포털의 **ServiceNow** 응용 프로그램 통합 페이지에서 **사용자 프로비저닝 구성**을 클릭합니다. <br><br> ![사용자 프로비전](./media/active-directory-saas-servicenow-tutorial/IC769498.png "사용자 프로비전")


2. **ServiceNow 자격 증명을 입력하여 자동 사용자 프로비전 사용** 페이지에서 다음 구성 설정을 제공: 사용자 프로비젼 구성

     2.1. **ServiceNow 인스턴스 이름** 텍스트 상자에 ServiceNow 인스턴스 이름을 입력합니다.

     2.2. **ServiceNow 관리자 사용자 이름** 텍스트 상자에, ServiceNow 관리자 계정의 이름을 입력합니다.

     2.3. **ServiceNow 관리자 암호** 텍스트 상자에 이 계정의 암호를 입력합니다.

     2.4. **유효성 검사**를 클릭하여 구성을 확인합니다.

     2.5. **다음** 단추를 클릭하여 **다음 단계** 페이지를 엽니다.

     2.6. 이 응용 프로그램에 모든 사용자를 프로비전 하려는 경우 "** 이 응용 프로그램에 대한 디렉터리의 모든 사용자 계정을 자동으로 프로비전**" 을 선택합니다. <br><br> ![다음 단계](./media/active-directory-saas-servicenow-tutorial/IC698804.png "다음 단계")

     2.7. **다음 단계** 페이지에서 **완료**를 클릭하여 구성을 저장합니다.











##사용자 할당
  
구성을 테스트하려면 응용 프로그램 사용을 허용하려는 Azure AD 사용자를 할당하여 액세스 권한을 부여해야 합니다.

###ServiceNow에 사용자를 할당하려면 다음 단계를 수행합니다.

1.  Azure AD 포털에서 테스트 계정을 만듭니다.

2.  **ServiceNow** 응용 프로그램 통합 페이지에서 **사용자 할당**을 클릭합니다.

    ![사용자 할당](./media/active-directory-saas-servicenow-tutorial/IC769499.png "사용자 할당")

3.  테스트 사용자를 선택하고 **할당**을 클릭한 다음 **예**를 클릭하여 사용자의 할당을 확인합니다.

    ![예](./media/active-directory-saas-servicenow-tutorial/IC767830.png "예")
  
Single Sign-On 설정을 테스트하려면 액세스 패널을 엽니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하십시오.


## 추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On이란 무엇입니까?](active-directory-appssoaccess-whatis.md)

<!-----HONumber=August15_HO8-->