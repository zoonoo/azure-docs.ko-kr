<properties pageTitle="자습서: AirWatch와 Azure Active Directory 통합 | Microsoft Azure" description="Azure Active Directory에서 AirWatch를 사용하여 Single Sign-On, 자동화된 프로비전 등을 사용하도록 설정하는 방법을 알아봅니다." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#자습서: AirWatch와 Azure Active Directory 통합
>[AZURE.TIP]사용자 의견을 보내려면 [여기](http://go.microsoft.com/fwlink/?LinkId=529791)를 클릭하십시오.

이 자습서는 Azure 및 AirWatch의 통합을 보여주기 위한 것입니다. 이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

-   유효한 Azure 구독
-   AirWatch Single Sign-on이 설정된 구독

이 자습서를 완료한 후 AirWatch에 할당한 Azure AD 사용자가 AirWatch 회사 사이트 (서비스 공급자가 시작한 로그온)에서나 [액세스 패널 소개](https://msdn.microsoft.com/library/dn308586)를 사용하여 응용 프로그램에 Single Sign-On 할 수 있습니다.

이 자습서에 설명된 시나리오는 다음 구성 요소로 이루어져 있습니다.

1.  AirWatch에 응용 프로그램 통합 사용
2.  Single Sign-On 구성
3.  사용자 프로비전 구성
4.  사용자 할당

![AirWatch](./media/active-directory-saas-airwatch-tutorial/IC791913.png "AirWatch")
##AirWatch에 응용 프로그램 통합 사용

이 섹션은 AirWatch에 응용 프로그램 통합을 사용하도록 설정하는 방법을 간략하게 설명하기 위한 것입니다.

###AirWatch에 응용 프로그램 통합을 사용하도록 설정하려면 다음 단계를 수행합니다.

1.  Azure 관리 포털의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.

    ![Active Directory](./media/active-directory-saas-airwatch-tutorial/IC700993.png "Active Directory")

2.  **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.

3.  응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램**을 클릭합니다.

    ![응용 프로그램](./media/active-directory-saas-airwatch-tutorial/IC700994.png "응용 프로그램")

4.  페이지 맨 아래에 있는 **추가**를 클릭합니다.

    ![응용 프로그램 추가](./media/active-directory-saas-airwatch-tutorial/IC749321.png "응용 프로그램 추가")

5.  **원하는 작업을 선택하십시오.** 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.

    ![갤러리에서 응용 프로그램 추가](./media/active-directory-saas-airwatch-tutorial/IC749322.png "갤러리에서 응용 프로그램 추가")

6.  **검색 상자**에 **AirWatch**를 입력합니다.

    ![응용 프로그램 갤러리](./media/active-directory-saas-airwatch-tutorial/IC791914.png "응용 프로그램 갤러리")

7.  결과 창에서 **AirWatch**을 선택하고 **완료**를 클릭하여 응용 프로그램을 추가합니다.

    ![AirWatch](./media/active-directory-saas-airwatch-tutorial/IC791915.png "AirWatch")
##Single Sign-On 구성

이 섹션은 사용자가 SAML 프로토콜 기반 페더레이션을 사용하여 Azure AD의 계정으로 AirWatch에 인증할 수 있게 하는 방법을 간략하게 설명하기 위한 것입니다. 이 절차의 일부로 base 64로 인코딩된 인증서 파일을 만들어야 합니다. 이 절차를 잘 모르는 경우 [이진 인증서를 텍스트 파일로 변환하는 방법](http://youtu.be/PlgrzUZ-Y1o)을 참조하십시오.

###Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1.  Azure AD 포털의 **AirWatch** 응용 프로그램 통합 페이지에서 **Single Sign-on 구성**을 클릭하여 **Single Sign-on 구성** 대화 상자를 엽니다.

    ![Single Sign-On 구성](./media/active-directory-saas-airwatch-tutorial/IC791916.png "Single Sign-On 구성")

2.  **AirWatch에 대한 사용자 로그온 방법을 선택하십시오.** 페이지에서 **Microsoft Azure AD Single Sign-on**을 선택하고 **다음**을 클릭합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-airwatch-tutorial/IC791917.png "Single Sign-On 구성")

3.  **앱 URL 구성** 페이지의 **AirWatch Sign On URL** 텍스트 상자에서 사용자가 사용한 URL을 입력하여 AirWatch 응용 프로그램에 로그인하고(예: "*https:// companycode.awmdm.com/AirWatch/Login?gid=companycode*") **다음**을 클릭합니다.

    ![앱 URL 구성](./media/active-directory-saas-airwatch-tutorial/IC791918.png "앱 URL 구성")

4.  **AirWatch에서 Single Sign-On 구성** 페이지에서 **인증서 다운로드**를 클릭한 다음 컴퓨터에 인증서 파일을 저장합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-airwatch-tutorial/IC791919.png "Single Sign-On 구성")

5.  다른 웹 브라우저 창에서 AirWatch 회사 사이트에 관리자로 로그인합니다.

6.  왼쪽 탐색 창에서 **계정**과 **관리자**를 차례로 클릭합니다.

    ![관리자](./media/active-directory-saas-airwatch-tutorial/IC791920.png "관리자")

7.  **설정** 메뉴를 확장한 다음 **디렉터리 서비스**를 클릭합니다.

    ![설정](./media/active-directory-saas-airwatch-tutorial/IC791921.png "설정")

8.  **사용자** 탭을 클릭하고 **Base DN** 텍스트 필드에 도메인 이름을 입력한 다음 **저장**을 클릭합니다.

    ![사용자](./media/active-directory-saas-airwatch-tutorial/IC791922.png "사용자")

9.  **서버** 탭을 클릭합니다.

    ![서버](./media/active-directory-saas-airwatch-tutorial/IC791923.png "서버")

10. 다음 단계를 수행합니다.

    ![업로드](./media/active-directory-saas-airwatch-tutorial/IC791924.png "업로드")

    1.  **디렉터리 유형**으로 **없음**을 선택합니다.
    2.  **인증에 SAML 사용**을 선택합니다.
    3.  다운로드한 인증서를 업로드하려면 **업로드**를 클릭합니다.

11. **요청** 섹션에서 다음 단계를 수행합니다.

    ![요청](./media/active-directory-saas-airwatch-tutorial/IC791925.png "요청")

    1.  **요청 바인딩 형식**으로 **게시**를 선택합니다.
    2.  Azure 포털의 **Airwatch에서 Single Sign-on 구성** 대화 상자 페이지에서 **Single Sign-On 서비스 URL** 값을 복사한 다음 **ID 공급자 Single Sign On URL** 텍스트 상자에 붙여넣습니다.
    3.  **NameID 형식**으로 **전자 메일 주소**를 선택합니다.
    4.  **저장**을 클릭합니다.

12. **사용자** 탭을 다시 클릭합니다.

    ![사용자](./media/active-directory-saas-airwatch-tutorial/IC791926.png "사용자")

13. **특성** 섹션에서 다음 단계를 수행합니다.

    ![특성](./media/active-directory-saas-airwatch-tutorial/IC791927.png "특성")

    1.  **개체 식별자** 텍스트 상자에 ****http://schemas.microsoft.com/identity/claims/objectidentifier**를 입력합니다.
2.  **사용자 이름** 텍스트 상자에 ****http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**를 입력합니다.
3.  **표시 이름** 텍스트 상자에 ****http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**를 입력합니다.
4.  **이름** 텍스트 상자에 ****http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**를 입력합니다.
5.  **성** 텍스트 상자에 ****http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**를 입력합니다.
6.  **전자 메일** 텍스트 상자에 ****http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**를 입력합니다.
7.  **Save**를 클릭합니다.

14. Azure AD 포털에서 Single Sign-on 구성 확인을 선택하고 **완료**를 클릭하여 **Single Sign-on 구성** 대화 상자를 닫습니다.

    ![Single Sign-On 구성](./media/active-directory-saas-airwatch-tutorial/IC791928.png "Single Sign-On 구성")
##사용자 프로비전 구성

Azure AD 사용자가 AirWatch에 로그인할 수 있도록 하려면 AirWatch로 프로비전되어야 합니다. AirWatch의 경우 프로비전은 수동 작업입니다.

###사용자 계정을 프로비전하려면 다음 단계를 수행합니다.

1.  **AirWatch** 회사 사이트에 관리자 권한으로 로그인합니다.

2.  왼쪽의 탐색 창에서 **계정**과 **사용자**를 차례로 클릭합니다.

    ![사용자](./media/active-directory-saas-airwatch-tutorial/IC791929.png "사용자")

3.  **사용자** 메뉴에서 **목록 보기**와 **추가 > 사용자 추가**를 차례로 클릭합니다.

    ![사용자 추가](./media/active-directory-saas-airwatch-tutorial/IC791930.png "사용자 추가")

4.  **사용자 추가/편집** 대화 상자에서 다음 단계를 수행합니다.

    ![사용자 추가](./media/active-directory-saas-airwatch-tutorial/IC791931.png "사용자 추가")

    1.  관련된 텍스트 상자에 프로비전할 유효한 Azure Active Directory 계정의 **사용자 이름**, **암호**, **암호 확인**, **이름**, **성**, **전자 메일 주소**를 입력합니다.
    2.  **Save**를 클릭합니다.

>[AZURE.NOTE]다른 AirWatch 사용자 계정 생성 도구 또는 AirWatch가 제공한 API를 사용하여 AAD 사용자 계정을 프로비전할 수 있습니다.

##사용자 할당

구성을 테스트하려면 응용 프로그램 사용을 허용하려는 Azure AD 사용자를 할당하여 액세스 권한을 부여해야 합니다.

###AirWatch에 사용자를 할당하려면 다음 단계를 수행합니다.

1.  Azure AD 포털에서 테스트 계정을 만듭니다.

2.  **AirWatch ** 응용 프로그램 통합 페이지에서 **사용자 할당**을 클릭합니다.

    ![사용자 할당](./media/active-directory-saas-airwatch-tutorial/IC791932.png "사용자 할당")

3.  테스트 사용자를 선택하고 **할당**을 클릭한 다음 **예**를 클릭하여 사용자의 할당을 확인합니다.

    ![예](./media/active-directory-saas-airwatch-tutorial/IC767830.png "예")

Single Sign-On 설정을 테스트하려면 액세스 패널을 엽니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://msdn.microsoft.com/library/dn308586)를 참조하십시오.

<!---HONumber=August15_HO7-->