<properties pageTitle="자습서: Salesforce Sandbox와 Azure Active Directory 통합 | Microsoft Azure" description="Azure Active Directory에서 Salesforce Sandbox를 사용하여 Single Sign-On, 자동화된 프로비저닝 등을 사용하도록 설정하는 방법을 알아봅니다." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="10/07/2015" ms.author="markvi" />
#자습서: Salesforce Sandbox와 Azure Active Directory 통합
>[AZURE.TIP]피드백을 보내려면 [여기](http://go.microsoft.com/fwlink/?LinkId=521878)를 클릭하세요.
  
이 자습서는 Azure 및 Salesforce Sandbox의 통합을 보여 주기 위한 것입니다.  
샌드박스는 Salesforce 프로덕션 조직에서 데이터 및 응용 프로그램을 손상시키지 않고 개발, 테스트 및 훈련과 같은 다양한 목적으로 별도의 환경에서 조직의 여러 복사본을 만들 수 있는 기능을 제공합니다.  
자세한 내용은 [샌드박스 개요](https://help.salesforce.com/HTViewHelpDoc?id=create_test_instance.htm&language=en_US)를 참조하세요.
  
이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

-   유효한 Azure 구독
-   Salesforce.com에서 샌드박스
  
Salesforce.com에 유효한 샌드박스가 없는 경우 Salesforce에 문의해야 합니다.
  
이 자습서에 설명된 시나리오는 다음 구성 요소로 이루어져 있습니다.

1.  Salesforce Sandbox에 응용 프로그램 통합 사용
2.  Single Sign-On 구성
3.  도메인 사용
4.  사용자 프로비저닝 구성
5.  사용자 할당

![시나리오](./media/active-directory-saas-salesforce-sandbox-tutorial/IC769571.png "시나리오")
##Salesforce Sandbox에 응용 프로그램 통합 사용
  
이 섹션에서는 Salesforce 샌드박스에 응용 프로그램 통합을 사용하도록 설정하는 방법을 간략하게 설명합니다.

###Salesforce 샌드박스에 응용 프로그램 통합을 사용하도록 설정하려면 다음 단계를 수행합니다.

1.  Azure 관리 포털의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.

    ![Active Directory](./media/active-directory-saas-salesforce-sandbox-tutorial/IC700993.png "Active Directory")

2.  **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.

3.  응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램**을 클릭합니다.

    ![응용 프로그램](./media/active-directory-saas-salesforce-sandbox-tutorial/IC700994.png "응용 프로그램")

4.  **응용 프로그램 갤러리**를 열려면 **앱 추가**를 클릭한 다음 **조직에서 사용할 응용 프로그램 추가**를 클릭합니다.

    ![원하는 작업을 선택하세요.](./media/active-directory-saas-salesforce-sandbox-tutorial/IC700995.png "원하는 작업을 선택하세요.")

5.  **검색 상자**에 **Salesforce 샌드박스**를 입력합니다.

    ![응용 프로그램 갤러리](./media/active-directory-saas-salesforce-sandbox-tutorial/IC710978.png "응용 프로그램 갤러리")

6.  결과 창에서 **Salesforce Sandbox**를 선택하고 **완료**를 클릭하여 응용 프로그램을 추가합니다.

    ![Salesforce Sandbox](./media/active-directory-saas-salesforce-sandbox-tutorial/IC746474.png "Salesforce Sandbox")
##Single Sign-On 구성
  
이 섹션은 사용자가 SAML 프로토콜 기반 페더레이션을 사용하여 Azure AD의 계정으로 Salesforce에 인증할 수 있게 하는 방법을 간략하게 설명하기 위한 것입니다.

###Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1.  Azure AD 포털의 **Salesforce Sandbox** 응용 프로그램 통합 페이지에서 **Single Sign-On 구성**을 클릭하여 **Single Sign-On 구성** 대화 상자를 엽니다.

    ![Single Sign-On 구성](./media/active-directory-saas-salesforce-sandbox-tutorial/IC749323.png "Single Sign-On 구성")

2.  **Salesforce Sandbox에 대한 사용자 로그온 방법을 선택하세요.** 페이지에서 **Microsoft Azure AD Single Sign-On**을 선택하고 **다음**을 클릭합니다.

    ![Salesforce Sandbox](./media/active-directory-saas-salesforce-sandbox-tutorial/IC746479.png "Salesforce Sandbox")

3.  **앱 URL 구성** 페이지의 **로그온 URL** 텍스트 상자에 다음 패턴 `http://company.my.salesforce.com`을 사용하여 URL을 입력하고 **다음**을 클릭합니다.

    ![앱 URL 구성](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781022.png "앱 URL 구성")

4. 디렉터리의 다른 Salesforce Sandbox 인스턴스에 대한 Single Sign-On을 이미 구성한 경우 **로그온 URL**과 같은 값으로 **식별자**도 구성해야 합니다. 대화 상자의 **앱 URL 구성** 페이지에서 **고급 설정 표시** 확인란을 선택하여 **식별자** 필드를 찾을 수 있습니다.

4.  **Salesforce Sandbox에서 Single Sign-On 구성** 페이지에서 **인증서 다운로드**를 클릭한 다음 컴퓨터에 인증서 파일을 저장합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781023.png "Single Sign-On 구성")

5.  다른 웹 브라우저 창에서 Salesforce 샌드박스에 관리자로 로그인합니다.

6.  위쪽의 메뉴에서 **설정**을 클릭합니다.

    ![설정](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781024.png "설정")

7.  왼쪽의 탐색 창에서 **보안 제어**를 클릭한 다음 **Single Sign-On 설정**을 클릭합니다.

    ![Single Sign-On 설정](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781025.png "Single Sign-On 설정")

8.  Single Sign-On 설정 섹션에서 다음 단계를 수행합니다.

    ![Single Sign-On 설정](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781026.png "Single Sign-On 설정")

    1.  **SAML 사용**을 선택합니다.
    2.  **새로 만들기**를 클릭합니다.

9.  SAML Single Sign-On 설정 섹션에서 다음 단계를 수행합니다.

    ![SAML Singl Sign On 설정](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781027.png "SAML Singl Sign On 설정")

    1.  이름 텍스트 상자에 구성의 이름을 입력합니다(예: *SPSSOWAAD\_Test*).
    2.  Azure 포털의 **Salesforce Sandbox에서 Single Sign-On 구성** 대화 상자 페이지에서 **발급자 URL** 값을 복사한 다음 **발급자** 텍스트 상자에 붙여 넣습니다.
    3.  디렉터리에 처음으로 추가하는 Salesforce Sandbox 인스턴스인 경우 **엔터티 ID** 텍스트 상자에 **https://test.salesforce.com**을 입력합니다. Salesforce Sandbox의 인스턴스를 이미 추가한 경우에는 **엔터티 ID**에 **로그온 URL**을 입력합니다. 형식은 다음과 같아야 합니다. `http://company.my.salesforce.com`
    4.  다운로드한 인증서를 업로드하려면 **찾아보기**를 클릭합니다.
    5.  **SAML ID 유형**으로 **사용자 개체에서 페더레이션 ID를 포함하는 어설션**을 선택합니다.
    6.  **SAML ID 위치**로 **Subject 문의 NameIdentifier 요소에 ID 포함**을 선택합니다.
    7.  Azure 포털의 **Salesforce Sandbox에서 Single Sign-On 구성** 대화 상자 페이지에서 **원격 로그인 URL** 값을 복사한 다음 **ID 공급자 로그인 URL** 텍스트 상자에 붙여 넣습니다.
    8.  Azure 포털의 **Salesforce Sandbox에서 Single Sign-On 구성** 대화 상자 페이지에서 **원격 로그아웃 URL** 값을 복사한 다음 **ID 공급자 로그아웃 URL** 텍스트 상자에 붙여 넣습니다.
    9.  **서비스 공급자가 시작한 요청 바인딩**에서 **HTTP POST**를 선택합니다.
    10. **Save**를 클릭합니다.

10. Azure AD 포털에서 Single Sign-On 구성 확인을 선택하고 **완료**를 클릭하여 **Single Sign-On 구성** 대화 상자를 닫습니다.

    ![Single Sign-On 구성](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781028.png "Single Sign-On 구성")

##도메인 사용
  
이 섹션에서는 이미 도메인을 만들었다고 가정합니다. 자세한 내용은 [도메인 이름 정의](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_define.htm&language=en_US)를 참조하세요.

###도메인을 사용하려면 다음 단계를 수행합니다.

1.  왼쪽 탐색 창에서 **도메인 관리**를 클릭한 다음 **내 도메인**을 클릭합니다.

    ![내 도메인](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781029.png "내 도메인")

    >[AZURE.NOTE]도메인이 올바르게 구성되었는지 확인합니다.

2.  **로그인 페이지 설정** 섹션에서 **편집**을 클릭한 다음 **인증 서비스**로 이전 섹션에서 SAML Single Sign-On 설정의 이름을 선택하고 마지막으로 **저장**을 클릭합니다.

    ![내 도메인](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781030.png "내 도메인")
  
도메인이 구성되면 바로 사용자가 Salesforce 샌드박스에 로그인하는 도메인 URL을 사용해야 합니다. URL의 값을 가져오려면 이전 섹션에서 만든 SSO 프로필을 클릭합니다.
##사용자 프로비저닝 구성
  
이 섹션은 Salesforce Sandbox에 Active Directory 사용자 계정을 사용자 프로비저닝할 수 있도록 설정하는 방법을 간략하게 설명하기 위한 것입니다.

###사용자 프로비저닝을 구성하려면

1.  Salesforce 포털의 위쪽 탐색 모음에서 사용자 메뉴를 확장하려면 사용자 이름을 선택합니다.

    ![내 설정](./media/active-directory-saas-salesforce-sandbox-tutorial/IC698773.png "내 설정")

2.  사용자 메뉴에서 **내 설정**을 선택하여 **내 설정** 페이지를 엽니다.

3.  왼쪽 창에서 **개인**을 클릭하여 개인 섹션을 확장한 다음 **내 보안 토큰 재설정**을 클릭합니다.

    ![내 설정](./media/active-directory-saas-salesforce-sandbox-tutorial/IC698774.png "내 설정")

4.  **내 보안 토큰 재설정** 페이지에서 **보안 토큰 재설정**을 클릭하여 Salesforce.com 보안 토큰이 포함된 메일을 요청합니다.

    ![새 토큰](./media/active-directory-saas-salesforce-sandbox-tutorial/IC698776.png "새 토큰")

5.  "**Salesforce.com.com 보안 확인**"을 제목으로 Salesforce.com에서 온 이메일의 받은 편지함을 확인합니다.

6.  이 이메일을 검토하고 보안 토큰 값을 복사합니다.

7.  Azure 관리 포털의 **Salesforce Sandbox** 응용 프로그램 통합 페이지에서 **사용자 프로비저닝 구성**을 클릭하여 **사용자 프로비저닝 구성** 대화 상자를 엽니다.

    ![사용자 프로비저닝 구성](./media/active-directory-saas-salesforce-sandbox-tutorial/IC769573.png "사용자 프로비저닝 구성")

8.  **Salesforce Sandbox 자격 증명을 입력하여 자동 사용자 프로비저닝 사용** 페이지에서 다음 구성 설정을 제공합니다.

    ![Salesforce Sandbox](./media/active-directory-saas-salesforce-sandbox-tutorial/IC746476.png "Salesforce Sandbox")

    1.  **Salesforce Sandbox 관리자 이름** 텍스트 상자에 Salesforce.com의 **시스템 관리자** 프로필이 할당된 Salesforce Sandbox 계정 이름을 입력합니다.

    2.  **Salesforce Sandbox 관리자 암호** 텍스트 상자에 이 계정의 암호를 입력합니다.

    3.  **사용자 보안 토큰** 텍스트 상자에 보안 토큰 값을 붙여 넣습니다.

    4.  **유효성 검사**를 클릭하여 구성을 확인합니다.

    5.  **다음** 단추를 클릭하여 **확인** 페이지를 엽니다.

9.  **확인** 페이지에서 **완료**를 클릭하여 구성을 저장합니다.
##사용자 할당
  
구성을 테스트하려면 응용 프로그램 사용을 허용하려는 Azure AD 사용자를 할당하여 액세스 권한을 부여해야 합니다.

###Salesforce Sandbox에 사용자를 할당하려면 다음 단계를 수행합니다.

1.  Azure AD 포털에서 테스트 계정을 만듭니다.

2.  **Salesforce Sandbox**응용 프로그램 통합 페이지에서 **사용자 할당**을 클릭합니다.

    ![사용자 할당](./media/active-directory-saas-salesforce-sandbox-tutorial/IC769574.png "사용자 할당")

3.  테스트 사용자를 선택하고 **할당**을 클릭한 다음 **예**를 클릭하여 할당을 확인합니다.

    ![예](./media/active-directory-saas-salesforce-sandbox-tutorial/IC767830.png "예")
  
이제 10분 동안 기다린 후 계정이 Salesforce Sandbox에 동기화되었는지 확인해야 합니다.
  
Single Sign-On 설정을 테스트하려면 액세스 패널을 엽니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://msdn.microsoft.com/library/dn308586)를 참조하세요.

<!---HONumber=AcomDC_1217_2015-->