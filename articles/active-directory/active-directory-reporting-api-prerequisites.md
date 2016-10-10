<properties
    pageTitle="Azure AD Reporting API에 액세스하기 위한 필수 구성 요소 | Microsoft Azure"
    description="Azure AD Reporting API에 액세스하기 위한 필수 구성 요소에 대해 알아보기"
    services="active-directory"
    documentationCenter=""
    authors="dhanyahk"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="09/25/2016"
    ms.author="dhanyahk;markvi"/>

# Azure AD Reporting API에 액세스하기 위한 필수 구성 요소 

[Azure AD Reporting API](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview)는 일련의 REST 기반 API를 통해 데이터에 프로그래밍 방식으로 액세스합니다. 다양한 프로그래밍 언어 및 도구에서 이러한 API를 호출할 수 있습니다.

Reporting API는 [OAuth](https://msdn.microsoft.com/library/azure/dn645545.aspx)를 사용하여 Web API에 대한 액세스 권한을 부여합니다.

Reporting API에 액세스하도록 준비하려면 다음을 수행해야 합니다.

1. Azure AD 테넌트에서 응용 프로그램 만들기

2. Azure AD 데이터에 액세스하려면 응용 프로그램에 적절한 사용 권한을 부여합니다

3. 디렉터리에서 구성 설정 수집

질문, 문제 또는 피드백은 [AAD Reporting 도움말](mailto:aadreportinghelp@microsoft.com)에 문의하세요.


## Azure AD 응용 프로그램 만들기

Azure AD Reporting API에 액세스하기 위해 디렉터리를 구성하려면 Azure AD 테넌트에서 전역 관리자 디렉터리 역할의 구성원이기도 한 Azure 구독 관리자 계정을 사용하여 Azure 클래식 포털에 로그인해야 합니다.

> [AZURE.IMPORTANT] 이 같은 "admin" 권한이 있는 자격 증명 하에서 실행 중인 응용 프로그램은 매우 강력할 수 있으므로, 응용 프로그램의 ID/암호 자격 증명을 안전하게 보관해야 합니다.


1. [Azure 클래식 포털](https://manage.windowsazure.com)의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.

	![응용 프로그램 등록](./media/active-directory-reporting-api-prerequisites/01.png)

2. **Active Directory** 목록에서 디렉터리를 선택합니다.

3. 위쪽 메뉴에서 **응용 프로그램**을 클릭합니다.

	![응용 프로그램 등록](./media/active-directory-reporting-api-prerequisites/02.png)

4. 아래 표시줄에서 **추가**를 클릭합니다.

	![응용 프로그램 등록](./media/active-directory-reporting-api-prerequisites/03.png)

5. **무엇을 하고 싶나요?** 대화 상자에서 **조직에서 개발 중인 응용 프로그램 추가**를 클릭합니다.

	![응용 프로그램 등록](./media/active-directory-reporting-api-prerequisites/04.png)


6. **응용 프로그램에 대한 정보 제공** 대화 상자 페이지에서 다음 단계를 수행합니다.

	![응용 프로그램 등록](./media/active-directory-reporting-api-prerequisites/05.png)

    a. **이름** 텍스트 상자에 이름(예: Reporting API 응용 프로그램)을 입력합니다.

    b. **웹 응용 프로그램 및/또는 Web API**를 선택합니다.

    c. **다음**을 클릭합니다.


7. **앱 속성** 대화 상자에서 다음 단계를 수행합니다.

	![응용 프로그램 등록](./media/active-directory-reporting-api-prerequisites/06.png)

    a. **로그온 URL** 텍스트 상자에 `https://localhost`을 입력합니다.

    b. **앱 ID URI** 텍스트 상자에 ```https://localhost/ReportingApiApp```를 입력합니다.

    c. **완료**를 클릭합니다.



## API를 사용하도록 응용 프로그램에 권한 부여

1. [Azure 클래식 포털](https://manage.windowsazure.com/)의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.

	![응용 프로그램 등록](./media/active-directory-reporting-api-prerequisites/01.png)

2. **Active Directory** 목록에서 디렉터리를 선택합니다.

3. 위쪽 메뉴에서 **응용 프로그램**을 클릭합니다.

	![응용 프로그램 등록](./media/active-directory-reporting-api-prerequisites/02.png)


3. 응용 프로그램 목록에서 새로 만든 응용 프로그램을 선택합니다.

	![응용 프로그램 등록](./media/active-directory-reporting-api-prerequisites/07.png)

4. 위쪽 메뉴에서 **구성**을 클릭합니다.

	![응용 프로그램 등록](./media/active-directory-reporting-api-prerequisites/08.png)


5. **다른 응용 프로그램에 대한 사용 권한**에서 **Azure Active Directory** 리소스의 경우 **응용 프로그램 사용 권한** 드롭다운 목록을 클릭한 다음 **디렉터리 데이터 읽기**를 선택합니다.

	![응용 프로그램 등록](./media/active-directory-reporting-api-prerequisites/09.png)


5. 아래 표시줄에서 **저장**을 클릭합니다.

	![응용 프로그램 등록](./media/active-directory-reporting-api-prerequisites/10.png)


## 디렉터리에서 구성 설정 수집

이 섹션에서는 디렉터리에서 다음 설정을 가져오는 방법을 보여 줍니다.

- 도메인 이름
- 클라이언트 ID
- 클라이언트 암호

Reporting API에 대한 호출을 구성하는 경우 이 값이 필요합니다.


### 도메인 이름 가져오기

1. [Azure 클래식 포털](https://manage.windowsazure.com)의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.

	![응용 프로그램 등록](./media/active-directory-reporting-api-prerequisites/01.png)

2. **Active Directory** 목록에서 디렉터리를 선택합니다.

3. 위쪽의 메뉴에서 **도메인**을 클릭합니다.

	![응용 프로그램 등록](./media/active-directory-reporting-api-prerequisites/11.png)

4. **도메인 이름** 필드에서 도메인 이름을 복사합니다.

	![응용 프로그램 등록](./media/active-directory-reporting-api-prerequisites/12.png)


### 응용 프로그램의 클라이언트 ID 가져오기

1. [Azure 클래식 포털](https://manage.windowsazure.com)의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.

	![응용 프로그램 등록](./media/active-directory-reporting-api-prerequisites/01.png)

2. **Active Directory** 목록에서 디렉터리를 선택합니다.

3. 위쪽 메뉴에서 **응용 프로그램**을 클릭합니다.

	![응용 프로그램 등록](./media/active-directory-reporting-api-prerequisites/02.png)

4. 응용 프로그램 목록에서 새로 만든 응용 프로그램을 선택합니다.

	![응용 프로그램 등록](./media/active-directory-reporting-api-prerequisites/07.png)

4. 위쪽 메뉴에서 **구성**을 클릭합니다.

	![응용 프로그램 등록](./media/active-directory-reporting-api-prerequisites/08.png)

4. **클라이언트 ID**를 복사합니다.

	![응용 프로그램 등록](./media/active-directory-reporting-api-prerequisites/13.png)


### 응용 프로그램의 클라이언트 암호 가져오기

응용 프로그램의 클라이언트 암호를 가져오려면 새 키를 만들고 새 키를 저장할 때 해당 값을 저장해야 합니다. 나중에 이 값을 검색하는 것이 불가능하기 때문입니다.

1. [Azure 클래식 포털](https://manage.windowsazure.com)의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.

	![응용 프로그램 등록](./media/active-directory-reporting-api-prerequisites/01.png)

2. **Active Directory** 목록에서 디렉터리를 선택합니다.

3. 위쪽 메뉴에서 **응용 프로그램**을 클릭합니다.

	![응용 프로그램 등록](./media/active-directory-reporting-api-prerequisites/02.png)

4. 응용 프로그램 목록에서 새로 만든 응용 프로그램을 선택합니다.

	![응용 프로그램 등록](./media/active-directory-reporting-api-prerequisites/07.png)

4. 위쪽 메뉴에서 **구성**을 클릭합니다.

	![응용 프로그램 등록](./media/active-directory-reporting-api-prerequisites/08.png)

5. **키** 섹션에서 다음 단계를 수행합니다.

	![응용 프로그램 등록](./media/active-directory-reporting-api-prerequisites/14.png)

    a. 기간 목록에서 기간을 선택합니다.

    b. 아래 표시줄에서 **저장**을 클릭합니다.

	![응용 프로그램 등록](./media/active-directory-reporting-api-prerequisites/10.png)

    c. 키 값을 복사합니다.

## 다음 단계

- Azure AD Reporting API의 데이터에 프로그래밍 방식으로 액세스하시겠습니까? [Azure Active Directory Reporting API 시작](active-directory-reporting-api-getting-started.md)을 확인하세요.

- Azure Active Directory Reporting에 대한 자세한 내용을 알아보려면 [Azure Active Directory Reporting 가이드](active-directory-reporting-guide.md)를 참조하세요.

<!---HONumber=AcomDC_0928_2016-->