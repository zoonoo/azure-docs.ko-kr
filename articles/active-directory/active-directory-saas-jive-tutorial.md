<properties 
    pageTitle="자습서: Jive와 Azure Active Directory 통합 | Microsoft Azure" 
    description="Azure Active Directory에서 Jive를 사용하여 Single Sign-On, 자동화된 프로비저닝 등을 사용하도록 설정하는 방법을 알아봅니다." 
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

#자습서: Jive와 Azure Active Directory 통합

  
이 자습서는 Azure와 Jive의 통합을 보여주기 위한 것입니다. 이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

-   유효한 Azure 구독
-   Jive 테넌트
  
이 자습서에 설명된 시나리오는 다음 구성 요소로 이루어져 있습니다.

1.  Jive에 응용 프로그램 통합 사용
2.  사용자 프로비저닝 구성

##Jive에 응용 프로그램 통합 사용
  
이 섹션에서는 Jive에 응용 프로그램 통합을 사용하도록 설정하는 방법을 간략하게 설명합니다.

###Jive에 응용 프로그램 통합을 사용하도록 설정하려면 다음 단계를 수행합니다.

1.  Azure 관리 포털의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.

    ![Active Directory](./media/active-directory-saas-jive-tutorial/IC700993.png "Active Directory")

2.  **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.

3.  응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램**을 클릭합니다.

    ![응용 프로그램](./media/active-directory-saas-jive-tutorial/IC700994.png "응용 프로그램")

4.  **응용 프로그램 갤러리**를 열려면 **앱 추가**를 클릭한 다음 **조직에서 사용할 응용 프로그램 추가**를 클릭합니다.

    ![원하는 작업을 선택하세요.](./media/active-directory-saas-jive-tutorial/IC700995.png "무엇을 하시겠습니까?")

5.  **검색 상자**에 **Jive**를 입력합니다.

    ![Jive](./media/active-directory-saas-jive-tutorial/IC701001.png "Jive")

6.  결과 창에서 **Jive**를 선택하고 **완료**를 클릭하여 응용 프로그램을 추가합니다.

    ![Jive](./media/active-directory-saas-jive-tutorial/IC701005.png "Jive")
##사용자 프로비저닝 구성
  
이 섹션에서는 Jive에 Active Directory 사용자 계정을 프로비저닝할 수 있도록 설정하는 방법을 간략하게 설명합니다. 이 절차의 일부로 Jive.com에 요청해야 하는 사용자 보안 토큰을 입력해야 합니다.
  
다음 스크린샷은 Azure AD의 관련 대화 상자 예를 보여줍니다.

![사용자 프로비저닝 구성](./media/active-directory-saas-jive-tutorial/IC698794.png "사용자 프로비저닝 구성")

###사용자 프로비저닝을 구성하려면 다음 단계를 수행합니다.

1.  Azure 관리 포털의 **Jive** 응용 프로그램 통합 페이지에서 **사용자 프로비저닝 구성**을 클릭하여 **사용자 프로비저닝 구성** 대화 상자를 엽니다.

2.  **Jive 자격 증명을 입력하여 자동 사용자 프로비저닝 사용** 페이지에서 다음 구성 설정을 입력합니다.

    1.  **Jive 관리자 사용자 이름** 텍스트 상자에 Jive.com에서 할당된 **시스템 관리자** 프로필을 가진 Jive 계정 이름을 입력합니다.

    2.  **Jive 관리자 암호** 텍스트 상자에 이 계정의 암호를 입력합니다.

    3.  **Jive 테넌트 URL** 텍스트 상자에 Jive 테넌트 URL을 입력합니다.

        >[AZURE.NOTE]Jive 테넌트 URL은 조직에서 Jive에 로그인할 때 사용하는 URL입니다. 일반적으로 URL의 형식은 **www.<organization>.jive.com**입니다.

    4.  **유효성 검사**를 클릭하여 구성을 확인합니다.

    5.  **다음** 단추를 클릭하여 **확인** 페이지를 엽니다.

3.  **확인** 페이지에서 확인 표시를 클릭하여 구성을 저장합니다.
  
이제 테스트 계정을 만들 수 있습니다. 10분 동안 기다린 후 계정이 Jive.com에 동기화되었는지 확인해 보십시오.

<!---HONumber=Nov15_HO1-->