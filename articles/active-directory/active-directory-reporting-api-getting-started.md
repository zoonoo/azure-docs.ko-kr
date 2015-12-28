<properties
   pageTitle="Azure AD Reporting API 시작하기"
   description="Azure Active Directory Reporting API를 시작하는 방법"
   services="active-directory"
   documentationCenter=""
   authors="kenhoff"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="12/07/2015"
   ms.author="kenhoff"/>


# Azure AD Reporting API 시작하기

*이 설명서는 [Azure Active Directory Reporting 가이드](active-directory-reporting-guide.md)의 일부입니다.*

Azure Active Directory는 다양한 작업, 보안 및 감사 보고서를 제공합니다. 이 데이터는 Azure 포털을 통해 사용할 수 있지만 SIEM 시스템, 감사 및 비즈니스 인텔리전스 도구와 같은 다른 많은 응용 프로그램에서도 매우 유용할 수 있습니다.

Azure AD Reporting API는 다양한 프로그래밍 언어 및 도구에서 호출될 수 있는 일련의 REST 기반 API를 통해 이러한 데이터에 프로그래밍 방식의 액세스를 제공합니다.

이 문서에서는 PowerShell을 사용한 Azure AD Reporting API 호출 과정을 단계적으로 안내합니다. 사용 가능한 보고서의 데이터를 시나리오가 요구하는 JSON, XML 또는 텍스트 형식으로 액세스하도록 샘플 PowerShell 스크립트를 수정할 수 있습니다.

이 샘플을 사용하려면 [Azure Active Directory](active-directory-whatis.md)가 필요합니다.

## Azure AD 응용 프로그램을 만들어 API에 액세스

Reporting API는 [OAuth](https://msdn.microsoft.com/library/azure/dn645545.aspx)를 사용하여 웹 API에 대한 액세스 권한을 부여합니다. 디렉터리의 정보에 액세스하려면 Active Directory에 응용 프로그램을 만들고 AAD 데이터에 액세스하도록 적절한 사용 권한을 부여해야 합니다.


### 응용 프로그램 만들기
- [Azure 관리 포털](https://manage.windowsazure.com/)로 이동합니다.
- 해당 디렉터리로 이동합니다.
- 응용 프로그램으로 이동합니다.
- 아래 표시줄에서 "추가"를 클릭합니다.
	- "내 조직에서 개발 중인 응용 프로그램 추가"를 클릭합니다.
	- **이름**: 아무 이름이나 가능합니다. "Reporting API 응용 프로그램" 같은 것이 좋습니다.
	- **유형**: "웹 응용 프로그램 및/또는 웹 API"를 선택합니다.
	- 화살표를 클릭하여 다음 페이지로 이동합니다.
	- **Sign-on URL**: ```http://localhost```.
	- **앱 ID URI**: ```http://localhost```.
	- 확인 표시를 클릭하여 응용 프로그램 추가를 완료합니다.

### API를 사용하도록 응용 프로그램에 권한 부여
- 응용 프로그램 탭으로 이동합니다.
- 새로 만든 응용 프로그램으로 이동합니다.
- **구성** 탭을 클릭합니다.
- "다른 응용 프로그램에 대한 권한" 섹션의
	- Microsoft Azure Active Directory > 응용 프로그램 사용 권한에서 **디렉터리 데이터 읽기**를 선택합니다.
- 아래 표시줄에서 **저장**을 클릭합니다.


### 디렉터리 ID, 클라이언트 ID, 클라이언트 암호 가져오기

아래 단계는 응용 프로그램의 클라이언트 ID 및 클라이언트 암호를 가져오는 방법을 안내합니다. 테넌트 이름을 알고 있어야 하며, *.onmicrosoft.com이나 사용자 지정 도메인 이름 중 하나일 수 있습니다. 스크립트를 수정하는 데 사용되니 이 이름을 별도로 복사해 놓으세요.

#### 응용 프로그램 클라이언트 ID
- 응용 프로그램 탭으로 이동합니다.
- 새로 만든 응용 프로그램으로 이동합니다.
- **구성** 탭으로 이동합니다.
- **클라이언트 ID** 필드에 응용 프로그램의 클라이언트 ID가 나열됩니다.

#### 응용 프로그램 클라이언트 암호
- 응용 프로그램 탭으로 이동합니다.
- 새로 만든 응용 프로그램으로 이동합니다.
- 구성 탭으로 이동합니다.
- "키" 섹션에서 기간을 선택하여 응용 프로그램의 새 비밀 키를 생성합니다.
- 키가 저장 중이라고 표시됩니다. 이 키는 나중에 검색할 수 없으므로 복사하여 안전한 위치에 붙여넣으세요.


## 스크립트 수정
$ClientID, $ClientSecret, $tenantdomain을 "Azure AD에서 액세스 위임"의 올바른 값으로 대체하여 디렉터리에서 작동하도록 아래 스크립트 중 하나를 편집합니다.

### PowerShell 스크립트

    # This script will require the Web Application and permissions setup in Azure Active Directory
    $ClientID	  	= "your-application-client-id-here"				# Should be a ~35 character string insert your info here
    $ClientSecret  	= "your-application-client-secret-here"			# Should be a ~44 character string insert your info here
    $loginURL		= "https://login.windows.net"
    $tenantdomain	= "your-directory-name-here.onmicrosoft.com"			# For example, contoso.onmicrosoft.com

    # Get an Oauth 2 access token based on client id, secret and tenant domain
    $body		= @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
    $oauth		= Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

    $7daysago = "{0:s}" -f (get-date).AddDays(-7) + "Z"
    # or, AddMinutes(-5)

    Write-Output $7daysago

    if ($oauth.access_token -ne $null) {
    	$headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

        $url = "https://graph.windows.net/$tenantdomain/reports/auditEvents?api-version=beta&`$filter=eventTime gt $7daysago"

    	$myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)
    	foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
    		Write-Output ($event | ConvertTo-Json)
    	}
        $myReport.Content | Out-File -FilePath auditEvents.json -Force
    } else {
    	Write-Host "ERROR: No Access Token"
    }

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

    URL="https://graph.windows.net/$TENANT_DOMAIN/reports/auditEvents?api-version=beta&\$filter=eventTime%20gt%20$YESTERDAY"


    REPORT=$(curl -s --header "Authorization: $TOKEN_TYPE $ACCESS_TOKEN" $URL)

    echo $REPORT | ./jq-win64.exe -r '.value' | ./jq-win64.exe -r ".[]"





## 스크립트 실행
스크립트를 편집한 후에는 실행하여 AuditEvents에서 예상한 데이터가 반환되는지 확인합니다.

스크립트는 사용 가능한 모든 보고서 목록을 반환하고 PowerShell 창에 AccountProvisioningEvents 보고서의 출력을 JSON 형식으로 반환합니다. 또한 JSON, 텍스트, XML 형식으로 동일한 출력 내용을 가진 파일도 만듭니다. 다른 보고서의 데이터를 반환하도록 스크립트를 수정하는 시도 및 필요 없는 출력 형식을 주석으로 처리할 수 있습니다.

## 참고

- Azure AD Reporting API에서 (OData 페이지 매김을 사용하여) 반환되는 이벤트의 수가 제한됩니다.
	- 데이터 보고에서 보존 제한은 [Reporting 보존 정책](active-directory-reporting-retention.md)을 확인하세요.


## 다음 단계
- 사용 가능한 보안, 감사 및 작업 보고서는 [Azure AD 보안, 감사 및 작업 보고서](active-directory-view-access-usage-reports.md) 확인
- 감사 보고서에 대한 자세한 내용은 [Azure AD 감사 보고서 이벤트](active-directory-reporting-audit-events.md) 참조
- Graph API REST 서비스에 대한 자세한 내용은 [Azure AD 보고서 및 이벤트(미리 보기)](https://msdn.microsoft.com/library/azure/mt126081.aspx) 참조

<!---HONumber=AcomDC_1217_2015-->