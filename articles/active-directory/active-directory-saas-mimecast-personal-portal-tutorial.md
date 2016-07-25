<properties 
    pageTitle="자습서: Mimecast Personal Portal과 Azure Active Directory 통합 | Microsoft Azure" 
    description="Azure Active Directory에서 Mimecast Personal Portal을 사용하여 Single Sign-On, 자동화된 프로비전 등을 사용하도록 설정하는 방법을 알아봅니다." 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
    manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="07/08/2016" 
    ms.author="jeedes" />

#자습서: Mimecast Personal Portal과 Azure Active Directory 통합
  
이 자습서는 Azure 및 Mimecast Personal Portal의 통합을 보여 주기 위한 것입니다. 이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

-   유효한 Azure 구독
-   Mimecast Personal Portal에서 Single Sign-On이 설정된 구독
  
이 자습서를 완료한 후 Mimecast Personal Portal에 할당한 Azure AD 사용자가 Mimecast Personal Portal 회사 사이트(서비스 공급자가 시작한 로그온)에서 또는 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 사용하여 응용 프로그램에 Single Sign-On할 수 있습니다.
  
이 자습서에 설명된 시나리오는 다음 구성 요소로 이루어져 있습니다.

1.  Mimecast Personal Portal에 응용 프로그램 통합 사용
2.  Single Sign-On 구성
3.  사용자 프로비전 구성
4.  사용자 할당

![시나리오](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794991.png "시나리오")
##Mimecast Personal Portal에 응용 프로그램 통합 사용
  
이 섹션은 Mimecast Personal Portal에 응용 프로그램 통합을 사용하도록 설정하는 방법을 간략하게 설명하기 위한 것입니다.

###Mimecast Personal Portal에 응용 프로그램 통합을 사용하도록 설정하려면 다음 단계를 수행합니다.

1.  Azure 클래식 포털의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.

    ![Active Directory](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC700993.png "Active Directory")

2.  **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.

3.  응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램**을 클릭합니다.

    ![응용 프로그램](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC700994.png "응용 프로그램")

4.  페이지 맨 아래에 있는 **추가**를 클릭합니다.

    ![응용 프로그램 추가](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC749321.png "응용 프로그램 추가")

5.  **원하는 작업을 선택하세요.** 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.

    ![갤러리에서 응용 프로그램 추가](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC749322.png "갤러리에서 응용 프로그램 추가")

6.  **검색 상자**에 **Mimecast Personal Portal**을 입력합니다.

    ![응용 프로그램 갤러리](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794992.png "응용 프로그램 갤러리")

7.  결과 창에서 **Mimecast Personal Portal**을 선택하고 **완료**를 클릭하여 응용 프로그램을 추가합니다.

    ![Mimecast Personal Portal](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794993.png "Mimecast Personal Portal")
##Single Sign-On 구성
  
이 섹션은 사용자가 SAML 프로토콜 기반 페더레이션을 사용하여 Azure AD의 계정으로 Mimecast Personal Portal에 인증할 수 있게 하는 방법을 간략하게 설명하기 위한 것입니다. 이 절차의 일부로 base-64로 인코딩된 인증서 파일을 만들어야 합니다. 이 절차를 잘 모르는 경우 [이진 인증서를 텍스트 파일로 변환하는 방법](http://youtu.be/PlgrzUZ-Y1o)을 참조하십시오.

###Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1.  Azure 클래식 포털의 **Mimecast Personal Portal** 응용 프로그램 통합 페이지에서 **Single Sign-On 구성**을 클릭하여 **Single Sign-On 구성** 대화 상자를 엽니다.

    ![Single Sign-On 구성](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794994.png "Single Sign-On 구성")

2.  **Mimecast Personal Portal에 대한 사용자 로그온 방법을 선택하십시오.** 페이지에서 **Microsoft Azure AD Single Sign-On**을 선택하고 **다음**을 클릭합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794995.png "Single Sign-On 구성")

3.  **앱 URL 구성** 페이지의 **Mimecast Personal Portal Sign On URL** 텍스트 상자에서 Mimecast Personal Portal 응용 프로그램에 로그인하기 위해 사용자가 사용한 URL을 입력한 다음(예:: "https://webmail-uk.mimecast.com" 또는 "https://webmail-us.mimecast.com") **다음**을 클릭합니다.

    >[AZURE.NOTE] 로그인 URL은 특정 영역입니다.

    ![앱 URL 구성](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794996.png "앱 URL 구성")

4.  **Mimecast Personal Portal에서 Single Sign-On 구성** 페이지에서 인증서를 다운로드하려면 **인증서 다운로드**를 클릭한 다음 컴퓨터에 로컬로 인증서 파일을 저장합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794997.png "Single Sign-On 구성")

5.  다른 웹 브라우저 창에서 Mimecast Personal Portal 포털에 관리자로 로그인합니다.

6.  **서비스 > 응용 프로그램**으로 이동합니다.

    ![응용 프로그램](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794998.png "응용 프로그램")

7.  **인증 프로필**을 클릭합니다.

    ![인증 프로필](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC794999.png "인증 프로필")

8.  **새 인증 프로필**을 클릭합니다.

    ![새 인증 프로필](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795000.png "새 인증 프로필")

9.  **인증 프로필** 섹션에서 다음 단계를 수행합니다.

    ![인증 프로필](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795001.png "인증 프로필")

    1.  **설명** 텍스트 상자에 구성 이름을 입력합니다.
    2.  **Mimecast Personal Portal에 대한 SAML 인증 적용**을 선택합니다.
    3.  **공급자**로 **Azure Active Directory**를 선택합니다.
    4.  Azure 클래식 포털의 **Mimecast Personal Portal에서 Single Sign-On 구성** 대화 상자 페이지에서 **발급자 URL** 값을 복사한 다음 **발급자 URL** 텍스트 상자에 붙여넣습니다.
    5.  Azure 클래식 포털의 **Mimecast Personal Portal에서 Single Sign-On 구성** 대화 상자 페이지에서 **원격 로그인 URL** 값을 복사한 다음 **로그인 URL** 텍스트 상자에 붙여넣습니다.
    6.  Azure 클래식 포털의 **Mimecast Personal Portal에서 Single Sign-On 구성** 대화 상자 페이지에서 **원격 로그인 URL** 값을 복사한 다음 **로그아웃 URL** 텍스트 상자에 붙여넣습니다.

        >[AZURE.NOTE] 로그인 URL 값과 로그아웃 URL 값은 Mimecast Personal Portal의 -on에 대해 동일합니다.

    7.  다운로드한 인증서에서 **Base-64로 인코딩된** 파일을 만듭니다.

        >[AZURE.TIP]자세한 내용은 [이진 인증서를 텍스트 파일로 변환하는 방법](http://youtu.be/PlgrzUZ-Y1o)을 참조하세요.

    8.  메모장에서 base-64로 인코딩된 인증서를 열고, 첫 줄("*--*") 및 마지막 줄("*--*")을 제거하고, 나머지 내용을 클립보드에 복사한 다음 **ID 공급자 인증서(메타데이터)** 텍스트 상자로 붙여넣습니다.
    9.  **Single Sign-On 허용**을 선택합니다.
    10. **Save**를 클릭합니다.

10. Azure 클래식 포털에서 Single Sign-On 구성 확인을 선택하고 **완료**를 클릭하여 **Single Sign-On 구성** 대화 상자를 닫습니다.

    ![Single Sign-On 구성](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795002.png "Single Sign-On 구성")
##사용자 프로비전 구성
  
Azure AD 사용자가 Mimecast Personal Portal에 로그인하게 하려면 Mimecast Personal Portal에 프로비전해야 합니다. Mimecast Personal Portal의 경우 프로비전은 수동 작업입니다.
  
사용자를 만들려면 먼저 도메인을 등록해야 합니다.

###사용자 프로비전을 구성하려면

1.  **Mimecast Personal Portal**에 관리자 권한으로 로그인합니다.

2.  **디렉터리 > 내부**로 이동합니다.

    ![디렉터리](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795003.png "디렉터리")

3.  **새 도메인에 등록**을 클릭합니다.

    ![새 도메인에 등록](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795004.png "새 도메인에 등록")

4.  새 도메인을 만든 후 **새 주소**를 클릭합니다.

    ![새 주소](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795005.png "새 주소")

5.  새 주소 대화 상자에서 다음 단계를 수행합니다.

    ![저장](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795006.png "저장")

    1.  관련된 텍스트 상자에 프로비전할 유효한 AAD 계정의 **이메일 주소**, **전역 이름**, **암호** 및 **암호 확인** 특성을 입력합니다.
    2.  **Save**를 클릭합니다.

>[AZURE.NOTE]Mimecast Personal Portal 사용자 계정 만들기 도구 또는 Mimecast Personal Portal에서 제공된 API를 사용하여 AAD 사용자 계정을 프로비전합니다.

##사용자 할당

구성을 테스트하려면 응용 프로그램 사용을 허용하려는 Azure AD 사용자를 할당하여 액세스 권한을 부여해야 합니다.

###Mimecast Personal Portal에 사용자를 할당하려면 다음 단계를 수행합니다.

1.  Azure 클래식 포털에서 테스트 계정을 만듭니다.

2.  **Mimecast Personal Portal** 응용 프로그램 통합 페이지에서 **사용자 할당**을 클릭합니다.

    ![사용자 할당](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC795007.png "사용자 할당")

3.  테스트 사용자를 선택하고 **할당**을 클릭한 다음 **예**를 클릭하여 할당을 확인합니다.

    ![예](./media/active-directory-saas-mimecast-personal-portal-tutorial/IC767830.png "예")
  
Single Sign-On 설정을 테스트하려면 액세스 패널을 엽니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하십시오.

<!---HONumber=AcomDC_0713_2016-->