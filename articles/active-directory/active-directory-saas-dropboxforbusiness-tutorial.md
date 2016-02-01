<properties 
    pageTitle="자습서: Dropbox for Business와 Azure Active Directory 통합 | Microsoft Azure" 
    description="Azure Active Directory에서 Dropbox for Business를 사용하여 Single Sign-On, 자동화된 프로비전 등을 사용하도록 설정하는 방법을 알아봅니다." 
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
    ms.date="01/14/2016" 
    ms.author="jeedes" />

#자습서: Dropbox for Business와 Azure Active Directory 통합
  
이 자습서는 Azure 및 Dropbox for Business의 통합을 보여주기 위한 것입니다. 이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

-   유효한 Azure 구독
-   Dropbox for Business의 테스트 테넌트
  
이 자습서를 완료한 후 Dropbox for Business에 할당한 Azure AD 사용자가 Dropbox for Business 회사 사이트(서비스 공급자가 시작한 로그온)에서 또는 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 사용하여 응용 프로그램에 Single Sign-On할 수 있습니다.
  
이 자습서에 설명된 시나리오는 다음 구성 요소로 이루어져 있습니다.

1.  Dropbox for Business에 응용 프로그램 통합 사용
2.  Single Sign-On 구성
3.  사용자 프로비전 구성
4.  사용자 할당

![시나리오](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769508.png "시나리오")



##Dropbox for Business에 응용 프로그램 통합 사용
  
이 섹션은 Dropbox for Business에 응용 프로그램 통합을 사용하도록 설정하는 방법을 간략하게 설명하기 위한 것입니다.

###Dropbox for Business에 응용 프로그램 통합을 사용하도록 설정하려면 다음 단계를 수행합니다.

1.  Azure 관리 포털의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.

    ![Active Directory](./media/active-directory-saas-dropboxforbusiness-tutorial/IC700993.png "Active Directory")

2.  **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.

3.  응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램**을 클릭합니다.

    ![응용 프로그램](./media/active-directory-saas-dropboxforbusiness-tutorial/IC700994.png "응용 프로그램")

4.  페이지 맨 아래에 있는 **추가**를 클릭합니다.

    ![응용 프로그램 추가](./media/active-directory-saas-dropboxforbusiness-tutorial/IC749321.png "응용 프로그램 추가")

5.  **원하는 작업을 선택하세요.** 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.

    ![갤러리에서 응용 프로그램 추가](./media/active-directory-saas-dropboxforbusiness-tutorial/IC749322.png "갤러리에서 응용 프로그램 추가")

6.  **검색 상자**에 **Dropbox for Business**를 입력합니다.

    ![응용 프로그램 갤러리](./media/active-directory-saas-dropboxforbusiness-tutorial/IC701010.png "응용 프로그램 갤러리")

7.  결과 창에서 **Dropbox for Businesss**를 선택하고 **완료**를 클릭하여 응용 프로그램을 추가합니다.

    ![Dropbox for Business](./media/active-directory-saas-dropboxforbusiness-tutorial/IC701011.png "Dropbox for Business")
##Single Sign-On 구성
  
이 섹션은 사용자가 SAML 프로토콜 기반 페더레이션을 사용하여 Azure AD의 계정으로 Dropbox for Business에 인증할 수 있게 하는 방법을 간략하게 설명하기 위한 것입니다.

이 절차의 일부로 base-64로 인코딩된 인증서를 Dropbox for Business 테넌트로 업로드 해야 합니다. 이 절차를 잘 모르는 경우 [이진 인증서를 텍스트 파일로 변환하는 방법](http://youtu.be/PlgrzUZ-Y1o)을 참조하십시오.

###Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1.  Azure AD 포털의 **Dropbox for Busines** 응용 프로그램 통합 페이지에서 **Single Sign-On 구성**을 클릭하여 **Single Sign-On 구성** 대화 상자를 엽니다.

    ![Single Sign-On 구성](./media/active-directory-saas-dropboxforbusiness-tutorial/IC749323.png "Single Sign-On 구성")

2.  **Dropbox for Busines에 대한 사용자 로그온 방법을 선택하십시오.** 페이지에서 **Microsoft Azure AD Single Sign-on**을 선택하고 **다음**을 클릭합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-dropboxforbusiness-tutorial/IC749327.png "Single Sign-On 구성")

3.  **앱 URL 구성** 페이지에서 다음 단계를 수행합니다.

     3\.1. Dropbox for business 테넌트에 로그온합니다. <br><br> ![Single Sign-On 구성](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769509.png "Single Sign-On 구성")

     3\.2. 왼쪽의 탐색 창에서 **관리 콘솔**을 클릭합니다. <br><br> ![Single Sign-On 구성](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769510.png "Single Sign-On 구성")

     3\.3. **관리 콘솔**에서 왼쪽 탐색 창의**인증**을 클릭합니다. <br><br> ![Single Sign-On 구성](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769511.png "Single Sign-On 구성")

     3\.4. **Single sign-on**섹션에서 **single sign-on 사용**을 선택한 다음 **추가**를 클릭하여 이 섹션을 확장합니다. <br><br> ![Single Sign-On 구성](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769512.png "Single Sign-On 구성")

     3\.5. **자신의 전자 메일 주소를 입력하여 사용자가 로그인하거나 직접 이동할 수 있습니다.** 옆의 URL을 복사합니다. <br><br> ![Single Sign-On 구성](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769513.png "Single Sign-On 구성")

     3\.6. Azure 포털의 **DropBox for business 로그인** URL 텍스트 상자에 URL을 붙여넣습니다. <br><br> ![Single Sign-On 구성](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769514.png "Single Sign-On 구성")



4. **Dropbox for Business에서 Single Sign-On 구성** 페이지에서 **인증서 다운로드**를 클릭한 다음 컴퓨터에 인증서 파일을 저장합니다. <br><br> ![Single Sign-On 구성](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769515.png "Single Sign-On 구성")


5. Dropbox for Business 테넌트의 **인증** 페이지의 **Single sign-on** 섹션에서 다음 단계를 수행합니다. <br><br> ![Single Sign-On 구성](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769516.png "Single Sign-On 구성")

     5\.1. **필수**를 클릭합니다.

     5\.2. Azure 포털의 **Dropbox for Business에서 Single Sign-On 설정** 대화상자 페이지에서 **로그인 페이지 URL** 값을 복사하여, **로그인 URL** 텍스트 상자에 붙여넣습니다.


     5\.3. 다운로드한 인증서에서 **Base-64로 인코딩된** 파일을 만듭니다. >[AZURE.TIP]자세한 내용은 [이진 인증서를 텍스트 파일로 변환하는 방법](http://youtu.be/PlgrzUZ-Y1o)을 참조하십시오.


     5\.4. **인증서 선택**을 클릭한 다음 **base-64로 인코딩된 인증서 파일**을 업로드합니다.


     5\.5. **변경 내용 저장**을 클릭하여 DropBox for Business 테넌트의 구성을 완료할 수 있습니다.


6. Azure AD 포털에서 Single Sign-On 구성 확인을 선택하고 **완료**를 클릭하여 **Single Sign-On 구성** 대화 상자를 닫습니다. <br><br> ![Single Sign-On 구성](./media/active-directory-saas-dropboxforbusiness-tutorial/IC749329.png "Single Sign-On 구성")





##사용자 프로비전 구성
  
이 섹션에서는 Dropbox for Business에 Active Directory 사용자 계정을 프로비저닝할 수 있도록 설정하는 방법을 간략하게 설명합니다.


### 사용자 프로비전을 구성하려면

1. Azure 관리 포털의 **Dropbox for Business** 응용 프로그램 통합 페이지에서 **사용자 프로비전 구성**을 클릭하여 **사용자 프로비전 구성** 대화 상자를 엽니다.

2. DropBox for Business에 사용자 프로비전 사용 페이지에서, Azure AD 대화 상자와 연결하려면 사용자 프로비전을 사용을 클릭하여 로그인을 엽니다. <br><br> ![사용자 프로비전](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769517.png "사용자 프로비전")

3. **Azure AD와 연결하기 위해 Dropbox에 로그인** 대화 상자에서 Dropbox for Business 테넌트에 로그인합니다. <br><br> ![사용자 프로비전](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769518.png "사용자 프로비전")



4. **허용**을 클릭하여 Azure AD에 Dropbox에 대한 액세스를 허용합니다. <br><br> ![사용자 프로비전](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769519.png "사용자 프로비전")



5. 구성을 마치려면 **완료** 단추를 클릭합니다. <br><br> ![사용자 프로비전](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769520.png "사용자 프로비전")




##사용자 할당
  
구성을 테스트하려면 응용 프로그램 사용을 허용하려는 Azure AD 사용자를 할당하여 액세스 권한을 부여해야 합니다.

###Dropbox for Business에 사용자를 할당하려면 다음 단계를 수행합니다.

1.  Azure AD 포털에서 테스트 계정을 만듭니다.

2.  **Dropbox for Business ** 응용 프로그램 통합 페이지에서 **사용자 할당**을 클릭합니다.

    ![사용자 할당](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769521.png "사용자 할당")

3.  테스트 사용자를 선택하고 **할당**을 클릭한 다음 **예**를 클릭하여 할당을 확인합니다.

    ![예](./media/active-directory-saas-dropboxforbusiness-tutorial/IC767830.png "예")
  


이제 10분 동안 기다린 후 계정이 비즈니스용 Dropbox에 동기화되었는지 확인해야 합니다.

첫 번째 확인 단계로 Azure 관리 포털의 **Dropbox for Business** 응용 프로그램 통합 페이지의 **대시보드**를 클릭하여 프로비전 상태를 확인할 수 있습니다.

<br><br> ![사용자 할당](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769522.png "사용자 할당")


주기를 프로비전하는 성공적으로 완료된 사용자는 관련된 상태에서 표시됩니다.

<br><br> ![사용자 할당](./media/active-directory-saas-dropboxforbusiness-tutorial/IC769523.png "사용자 할당")


Single Sign-On 설정을 테스트하려면 액세스 패널을 엽니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하십시오.




## 추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On이란 무엇입니까?](active-directory-appssoaccess-whatis.md)

<!---HONumber=AcomDC_0121_2016-->