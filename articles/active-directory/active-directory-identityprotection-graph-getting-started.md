<properties
	pageTitle="Azure Active Directory ID 보호 및 Microsoft Graph 시작 | Microsoft Azure"
	description="Azure Active Directory에서 위험 이벤트 및 관련된 정보의 목록에 Microsoft Graph를 쿼리하는 방법을 소개합니다."
	services="active-directory"
	keywords="Azure Active Directory ID 보호, 위험 이벤트, 취약점, 보안 정책, Microsoft Graph"
	documentationCenter=""
	authors="markusvi"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/15/2016"
	ms.author="markvi"/>

# Azure Active Directory ID 보호 및 Microsoft Graph 시작

Microsoft Graph는 Microsoft의 통합된 API 끝점이며 [Azure Active Directory ID 보호](active-directory-identityprotection.md) API의 시작점입니다. 첫 번째 API인 **identityRiskEvents**를 사용하면 [위험 이벤트](active-directory-identityprotection-risk-events-types.md) 및 관련 정보의 목록에 대한 Microsoft Graph를 쿼리할 수 있습니다. 이 문서는 이 API를 쿼리하는 작업부터 시작합니다. 자세한 소개, 전체 설명서 및 Graph Explorer에 대한 액세스는 [Microsoft Graph 사이트](https://graph.microsoft.io/)를 참조하세요.

Microsoft Graph를 통해 ID 보호 데이터에 액세스하려면 세 가지 단계가 있습니다.

1. 클라이언트 암호와 응용 프로그램을 추가합니다. 

2. 이 암호와 다른 몇 가지 정보를 사용하여 Microsoft Graph에 인증하면 인증 토큰을 받게됩니다.

3. 이 토큰을 사용하여 API 끝점에 요청을 수행하고 ID 보호 데이터를 다시 가져옵니다.

시작하기 전에 다음 항목이 필요합니다.

- Azure AD에서 응용 프로그램을 만드는 관리자 권한
- 테넌트의 도메인 이름(예: contoso.onmicrosoft.com)



## 클라이언트 암호와 응용 프로그램을 추가합니다.


1. 관리자 권한으로 Azure 클래식 포털에 [로그인](https://manage.windowsazure.com)합니다. 

1. 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.

	![응용 프로그램 만들기](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_01.png)

2. **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.

3. 위쪽 메뉴에서 **응용 프로그램**을 클릭합니다.

	![응용 프로그램 만들기](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_02.png)

4. 페이지 맨 아래에 있는 **추가**를 클릭합니다.

	![응용 프로그램 만들기](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_03.png)

5. **무엇을 하고 싶나요** 대화 상자에서 **조직에서 개발 중인 응용 프로그램 추가**를 클릭합니다.

	![응용 프로그램 만들기](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_04.png)


5. **응용 프로그램에 대한 정보 제공** 대화 상자 페이지에서 다음 단계를 수행합니다.

	![응용 프로그램 만들기](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_05.png)

	a. **이름** 텍스트 상자에 응용 프로그램의 이름으 입력합니다(예: AADIP 위험 이벤트 API 응용 프로그램).

	b. **유형**으로 **웹 응용 프로그램 및/또는 웹 API**를 선택합니다.

	c. **다음**을 클릭합니다.


5. **앱 속성** 대화 상자에서 다음 단계를 수행합니다.

	![응용 프로그램 만들기](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_06.png)

	a. **로그온 URL** 텍스트 상자에 `http://localhost`를 입력합니다.

	b. **앱 ID URI** 텍스트 상자에 `http://localhost`를 입력합니다.

	c. **완료**를 클릭합니다.


이제 응용 프로그램을 구성할 수 있습니다.

![응용 프로그램 만들기](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_07.png)



## API를 사용하도록 응용 프로그램에 권한 부여


1. 응용 프로그램 페이지의 위쪽에 있는 메뉴에서 **구성**을 클릭합니다. 

	![응용 프로그램 만들기](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_08.png)

2. **다른 응용 프로그램에 대한 권한** 섹션에서 **응용 프로그램 추가**를 클릭합니다.

	![응용 프로그램 만들기](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_09.png)


2. **다른 응용 프로그램에 대한 사용 권한** 대화 상자에서 다음 단계를 수행합니다.

	![응용 프로그램 만들기](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_10.png)

	a. **Microsoft Graph**를 선택합니다.

	b. **완료**를 클릭합니다.


1. **응용 프로그램 사용 권한: 0**을 클릭한 다음 **모든 ID 위험 이벤트 정보 참고**를 선택합니다.

	![응용 프로그램 만들기](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_11.png)

1. 페이지 맨 아래에서 **저장**을 클릭합니다.

	![응용 프로그램 만들기](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_12.png)


## 선택키 가져오기

1. 응용 프로그램 페이지의 **키** 섹션에서 기간으로 1년을 선택합니다.

	![응용 프로그램 만들기](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_13.png)

1. 페이지 맨 아래에서 **저장**을 클릭합니다.

	![응용 프로그램 만들기](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_12.png)

1. 키 섹션에서 새로 만든 키 값을 복사하고 안전한 위치에 붙여 넣습니다.

	![응용 프로그램 만들기](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_14.png)

	> [AZURE.NOTE] 이 키를 분실하면 이 섹션으로 돌아와서 새 키를 만들어야 합니다. 이 키의 비밀을 유지합니다. 키를 가진 사람은 누구나 데이터에 액세스할 수 있습니다.


1. **속성** 섹션에서 **클라이언트 ID**를 복사한 다음 안전한 위치에 붙여 넣습니다.


## Microsoft Graph에 인증하고 ID 위험 이벤트 API를 쿼리합니다.

이 시점에서 다음 항목이 만들어 집니다.

- 위에서 복사한 클라이언트 ID

- 위에서 복사한 키

- 테넌트 도메인의 이름


인증하려면 본문에서 다음 매개 변수를 ㅏ용하여 `https://login.microsoft.com`에 게시 요청을 전송합니다.

- grant\_type: “**client\_credentials**”

- resource: “**https://graph.microsoft.com**”

- client\_id: <your client ID>

- client\_secret: <your key>


> [AZURE.NOTE] **client\_id** 및 **client\_secret** 매개 변수에 대한 값을 제공해야 합니다.

성공하면 인증 토큰을 반환합니다. API를 호출하려면 다음 매개 변수를 사용하여 헤더를 만듭니다.

	`Authorization`=”<token_type> <access_token>"


인증할 경우 반환된 토큰에서 토큰 유형 및 액세스 토큰을 찾을 수 있습니다.

다음 API URL에 대한 요청으로 이 헤더를 보냅니다. `https://graph.microsoft.com/beta/identityRiskEvents`

성공한 경우 응답은 ID 위험 이벤트 및 OData JSON 형식으로 연결된 데이터의 컬렉션입니다. 이를 구문 분석하고 적절하게 처리할 수 있습니다.

다음은 Powershell을 사용하여 API를 인증하고 호출하는 샘플 코드입니다. 클라이언트 ID, 키 및 테넌트 도메인을 추가합니다.

	$ClientID       = "<your client ID here>"        # Should be a ~36 hex character string; insert your info here
	$ClientSecret   = "<your client secret here>"    # Should be a ~44 character string; insert your info here
	$tenantdomain   = "<your tenant domain here>"    # For example, contoso.onmicrosoft.com

	$loginURL       = "https://login.microsoft.com"
	$resource       = "https://graph.microsoft.com"

	$body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
	$oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

	Write-Output $oauth

	if ($oauth.access_token -ne $null) {
    	$headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

    	$url = "https://graph.microsoft.com/beta/identityRiskEvents"
    	Write-Output $url

    	$myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)

    	foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
        	Write-Output $event
    	}

	} else {
    	Write-Host "ERROR: No Access Token"
	} 


## 다음 단계

축하합니다! Microsoft Graph에 대한 호출을 처음으로 만들었습니다. 이제 ID 위험 이벤트를 쿼리하고 적절하게 데이터를 사용할 수 있습니다.

Microsoft Graph 및 Graph API를 사용하여 응용 프로그램을 구축하는 방법에 대한 자세한 내용은 [설명서](https://graph.microsoft.io/docs) 및 [Microsoft Graph 사이트](https://graph.microsoft.io/)에서 확인합니다. 또한 Graph에서 사용할 수 있는 ID 보호 API를 모두 나열하는 [Azure AD ID 보호 API](https://graph.microsoft.io/docs/api-reference/beta/resources/identityprotection_root) 페이지에 책갈피를 설정해야 합니다. API를 통해 ID 보호를 사용하는 새로운 방법을 추가하는 대로 해당 페이지에 표시됩니다.


## 추가 리소스

- [Azure Active Directory ID 보호](active-directory-identityprotection.md)

- [Azure Active Directory ID 보호에서 검색한 위험 이벤트의 유형](active-directory-identityprotection-risk-events-types.md)

- [Microsoft Graph](https://graph.microsoft.io/)

- [Microsoft Graph 개요](https://graph.microsoft.io/docs)

- [Azure AD ID 보호 서비스 루트](https://graph.microsoft.io/docs/api-reference/beta/resources/identityprotection_root)

<!---HONumber=AcomDC_0622_2016-->