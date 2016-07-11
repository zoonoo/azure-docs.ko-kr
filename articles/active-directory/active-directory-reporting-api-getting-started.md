<properties
   pageTitle="Azure AD Reporting API 시작 | Microsoft Azure"
   description="Azure Active Directory Reporting API를 시작하는 방법"
   services="active-directory"
   documentationCenter=""
   authors="dhanyahk"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="06/28/2016"
   ms.author="dhanyahk"/>

# Azure Active Directory Reporting API 시작

*이 설명서는 [Azure Active Directory Reporting 가이드](active-directory-reporting-guide.md)의 일부입니다.*

Azure AD(Active Directory)에서는 다양한 작업, 보안 및 감사 보고서를 제공합니다. 이 데이터는 Azure 클래식 포털을 통해 사용할 수 있지만 SIEM 시스템, 감사 및 비즈니스 인텔리전스 도구와 같은 다른 많은 응용 프로그램에서도 매우 유용할 수 있습니다.

[Azure AD Reporting API](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview)는 다양한 프로그래밍 언어 및 도구에서 호출될 수 있는 일련의 REST 기반 API를 통해 이러한 데이터에 프로그래밍 방식의 액세스를 제공합니다.

이 문서에서는 PowerShell을 사용한 Azure AD Reporting API 호출 과정을 단계적으로 안내합니다. 사용 가능한 보고서의 데이터를 시나리오가 요구하는 JSON, XML 또는 텍스트 형식으로 액세스하도록 샘플 PowerShell 스크립트를 수정할 수 있습니다.

이 샘플을 사용하려면 [Azure Active Directory](active-directory-whatis.md) 테넌트가 필요합니다.

## Azure AD 응용 프로그램을 만들어 API에 액세스

Reporting API는 [OAuth](https://msdn.microsoft.com/library/azure/dn645545.aspx)를 사용하여 웹 API에 대한 액세스 권한을 부여합니다. 디렉터리의 정보에 액세스하려면 Azure AD 테넌트에 응용 프로그램을 만들고 Azure AD 데이터에 액세스하도록 적절한 사용 권한을 부여해야 합니다.


### Azure AD 응용 프로그램 등록
Azure AD 응용 프로그램 등록 작업을 완료하려면, Azure AD 테넌트에서 전역 관리자 디렉터리 역할의 멤버이기도 한 Azure 구독 관리자 계정을 사용하여 Azure 클래식 포털에 로그인해야 합니다. 이는 전역 관리자 권한이 있는 계정을 사용하여 등록/동의하는 것을 필요로 하는 권한으로 Azure AD 응용 프로그램을 등록해야 하기 때문입니다

> [AZURE.IMPORTANT] 이 같은 "admin" 권한이 있는 자격 증명 하에서 실행 중인 응용 프로그램은 매우 강력할 수 있으므로, 응용 프로그램의 ID/암호 자격 증명을 안전하게 보관해야 합니다.


1. [Azure 클래식 포털](https://manage.windowsazure.com/)로 이동합니다.
2. 왼쪽 창에 있는 **Active Directory** 확장에서 Azure AD 테넌트로 이동합니다.
3. **응용 프로그램** 탭으로 이동합니다.
4. 아래 표시줄에서 **추가**를 클릭합니다.
	- “내 조직에서 개발 중인 응용 프로그램 추가”를 클릭합니다.
	- **이름**: 아무 이름이나 가능합니다. "Reporting API 응용 프로그램" 같은 것이 좋습니다.
	- **유형**: "웹 응용 프로그램 및/또는 웹 API"를 선택합니다.
	- 화살표를 클릭하여 다음 페이지로 이동합니다.
	- **Sign-on URL**: ```https://localhost```.
	- **앱 ID URI**: ```https://localhost/ReportingApiApp```.
	- 확인 표시를 클릭하여 응용 프로그램 추가를 완료합니다.

### API를 사용하도록 응용 프로그램에 권한 부여
1. **응용 프로그램** 탭으로 이동합니다.
2. 새로 만든 응용 프로그램으로 이동합니다.
3. **구성** 탭을 클릭합니다.
4. "다른 응용 프로그램에 대한 권한" 섹션의
	- “Windows Azure Active Directory” 리소스(Azure AD Graph API에 대한 권한)에 관해 알아보려면, “응용 프로그램 권한” 드롭다운 목록을 클릭하여 **디렉터리 데이터 읽기**를 선택합니다.

        > [AZURE.IMPORTANT] 올바른 사용 권한을 지정해야 합니다. "위임된 권한"이 아닌 "응용 프로그램 사용 권한"을 적용합니다. 그러지 않으면 응용 프로그램은 Reporting API에 액세스하는 데 필요한 권한 수준을 갖지 못하게 되며 스크립트는 *"appId에 대한 디렉터리 읽기 액세스를 확인할 수 없습니다"* 오류를 받게 됩니다.


5. 아래 표시줄에서 **저장**을 클릭합니다.

### 디렉터리 ID, 클라이언트 ID, 클라이언트 암호 가져오기

아래 단계는 응용 프로그램의 클라이언트 ID 및 클라이언트 암호를 가져오는 방법을 안내합니다. 테넌트 이름을 알고 있어야 하며, *.onmicrosoft.com이나 사용자 지정 도메인 이름 중 하나일 수 있습니다. 스크립트를 수정하는 데 사용되니 이 값들을 별도로 복사해 놓으세요.

#### Azure AD 테넌트의 도메인 이름 가져오기
1. 왼쪽 창에 있는 **Active Directory** 확장에서 Azure AD 테넌트로 이동합니다.
2. **도메인** 탭으로 이동합니다.
4. 테넌트의 "<tenant-name>. onmicrosoft.com" 도메인 이름이 사용자 지정 도메인이 구성되었다면 그 이름과 함께 표시됩니다.

#### 응용 프로그램의 클라이언트 ID 가져오기
1. **응용 프로그램** 탭으로 이동합니다.
2. 새로 만든 응용 프로그램으로 이동합니다.
3. **구성** 탭으로 이동합니다.
4. **클라이언트 ID** 필드에 응용 프로그램의 클라이언트 ID가 나열됩니다.

#### 응용 프로그램의 클라이언트 암호 가져오기
1. **응용 프로그램** 탭으로 이동합니다.
2. 새로 만든 응용 프로그램으로 이동합니다.
3. **구성** 탭으로 이동합니다.
4. "키" 섹션에서 기간을 선택하여 응용 프로그램의 새 비밀 키를 생성합니다.
5. 키가 저장 중이라고 표시됩니다. 이 키는 나중에 검색할 수 없으므로 복사하여 안전한 위치에 붙여넣으세요.

## 스크립트 수정
$ClientID, $ClientSecret, $tenantdomain을 위 섹션의 올바른 값으로 대체하여 디렉터리에서 작동하도록 아래 스크립트 중 하나를 편집합니다.

### PowerShell 스크립트
    # This script will require registration of a Web Application in Azure Active Directory (see https://azure.microsoft.com/documentation/articles/active-directory-reporting-api-getting-started/)

    # Constants
    $ClientID       = "your-client-application-id-here"       # Insert your application's Client ID, a Globally Unique ID (registered by Global Admin)
    $ClientSecret   = "your-client-application-secret-here"   # Insert your application's Client Key/Secret string
    $loginURL       = "https://login.microsoftonline.com"     # AAD Instance; for example https://login.microsoftonline.com
    $tenantdomain   = "your-tenant-domain.onmicrosoft.com"    # AAD Tenant; for example, contoso.onmicrosoft.com
    $resource       = "https://graph.windows.net"             # Azure AD Graph API resource URI
    $7daysago       = "{0:s}" -f (get-date).AddDays(-7) + "Z" # Use 'AddMinutes(-5)' to decrement minutes, for example
    Write-Output "Searching for events starting $7daysago"

    # Create HTTP header, get an OAuth2 access token based on client id, secret and tenant domain
    $body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
    $oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

    # Parse auditEvents report items, save output to file(s): auditEventsX.json, where X = 0 thru n for number of nextLink pages
    if ($oauth.access_token -ne $null) {   
        $i=0
        $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}
        $url = 'https://graph.windows.net/$tenantdomain/reports/auditEvents?api-version=beta&$filter=eventTime gt ' + $7daysago

        # loop through each query page (1 through n)
        Do{
            # display each event on the console window
            Write-Output "Fetching data using Uri: $url"
            $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)
            foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
                Write-Output ($event | ConvertTo-Json)
            }
        
            # save the query page to an output file
            Write-Output "Save the output to a file auditEvents$i.json"
            $myReport.Content | Out-File -FilePath auditEvents$i.json -Force
            $url = ($myReport.Content | ConvertFrom-Json).'@odata.nextLink'
            $i = $i+1
        } while($url -ne $null)
    } else {
        Write-Host "ERROR: No Access Token"
        }

    Write-Host "Press any key to continue ..."
    $x = $host.UI.RawUI.ReadKey("NoEcho,IncludeKeyDown")

### Bash 스크립트

    #!/bin/bash

    # Author: Ken Hoff (kenhoff@microsoft.com)
    # Date: 2015.08.20
    # NOTE: This script requires jq (https://stedolan.github.io/jq/)

    CLIENT_ID="your-application-client-id-here"         # Should be a ~35 character string insert your info here
    CLIENT_SECRET="your-application-client-secret-here" # Should be a ~44 character string insert your info here
    LOGIN_URL="https://login.windows.net"
    TENANT_DOMAIN="your-directory-name-here.onmicrosoft.com"    # For example, contoso.onmicrosoft.com

    TOKEN_INFO=$(curl -s --data-urlencode "grant_type=client_credentials" --data-urlencode "client_id=$CLIENT_ID" --data-urlencode "client_secret=$CLIENT_SECRET" "$LOGIN_URL/$TENANT_DOMAIN/oauth2/token?api-version=1.0")

    TOKEN_TYPE=$(echo $TOKEN_INFO | ./jq-win64.exe -r '.token_type')
    ACCESS_TOKEN=$(echo $TOKEN_INFO | ./jq-win64.exe -r '.access_token')

    # get yesterday's date

    YESTERDAY=$(date --date='1 day ago' +'%Y-%m-%d')

    URL="https://graph.windows.net/$TENANT_DOMAIN/reports/auditEvents?api-version=beta&$filter=eventTime%20gt%20$YESTERDAY"


    REPORT=$(curl -s --header "Authorization: $TOKEN_TYPE $ACCESS_TOKEN" $URL)

    echo $REPORT | ./jq-win64.exe -r '.value' | ./jq-win64.exe -r ".[]"

### Python
	# Author: Michael McLaughlin (michmcla@microsoft.com)
	# Date: January 20, 2016
	# This requires the Python Requests module: http://docs.python-requests.org

	import requests
	import datetime
	import sys

	client_id = 'your-application-client-id-here'
	client_secret = 'your-application-client-secret-here'
	login_url = 'https://login.windows.net/'
	tenant_domain = 'your-directory-name-here.onmicrosoft.com'

	# Get an OAuth access token
	bodyvals = {'client_id': client_id,
	            'client_secret': client_secret,
	            'grant_type': 'client_credentials'}

	request_url = login_url + tenant_domain + '/oauth2/token?api-version=1.0'
	token_response = requests.post(request_url, data=bodyvals)

	access_token = token_response.json().get('access_token')
	token_type = token_response.json().get('token_type')

	if access_token is None or token_type is None:
	    print "ERROR: Couldn't get access token"
	    sys.exit(1)

	# Use the access token to make the API request
	yesterday = datetime.date.strftime(datetime.date.today() - datetime.timedelta(days=1), '%Y-%m-%d')

	header_params = {'Authorization': token_type + ' ' + access_token}
	request_string = 'https://graph.windows.net/' + tenant_domain + '/reports/auditEvents?api-version=beta&$filter=eventTime%20gt%20' + yesterday   
	response = requests.get(request_string, headers = header_params)

	if response.status_code is 200:
	    print response.content
	else:
	    print 'ERROR: API request failed'


## 스크립트 실행
스크립트를 편집한 후에는 실행하여 AuditEvents에서 예상한 데이터가 반환되는지 확인합니다.

스크립트는 JSON 형식으로 auditEvents 보고서에서 출력을 반환합니다. 또한 동일한 출력으로 `auditEvents.json` 파일을 만듭니다. 다른 보고서의 데이터를 반환하도록 스크립트를 수정하고 필요 없는 출력 형식을 주석으로 처리할 수 있습니다.

## 참고 사항

- Azure AD Reporting API에서 (OData 페이지 매김을 사용하여) 반환되는 이벤트의 수가 제한됩니다.
- 데이터 보고에서 보존 제한은 [보존 정책 보고](active-directory-reporting-retention.md)를 확인하세요.


## 다음 단계
- 사용 가능한 보안, 감사 및 작업 보고서가 궁금하신가요? [Azure AD 보안, 감사 및 작업 보고서](active-directory-view-access-usage-reports.md) 확인. 또한 [Azure AD 보고서 및 이벤트(미리 보기)](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-reports-and-events-preview) 문서에 설명되어 있는, [https://graph.windows.net/tenant-name/reports/$metadata?api-version=beta](https://graph.windows.net/tenant-name/reports/$metadata?api-version=beta)로 이동하여 모든 사용 가능한 Azure AD Graph API 끝점을 볼 수 있습니다.
- 감사 보고서에 대한 자세한 내용은 [Azure AD 감사 보고서 이벤트](active-directory-reporting-audit-events.md) 참조
- Azure AD Graph API REST 서비스에 대한 자세한 내용은 [Azure AD 보고서 및 이벤트(미리 보기)](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview)를 참조하세요.

<!---HONumber=AcomDC_0629_2016-->