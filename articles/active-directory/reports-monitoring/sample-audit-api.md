---
title: Azure Active Directory Reporting 감사 API 샘플 | Microsoft Docs
description: Azure Active Directory Reporting API를 시작하는 방법
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: de8b8ec3-49b3-4aa8-93fb-e38f52c99743
ms.service: active-directory
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 05/30/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 345daaa8ac991cad9f098b317a9b59d52de73ead
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/09/2018
ms.locfileid: "41918774"
---
# <a name="azure-active-directory-reporting-audit-api-samples"></a>Azure Active Directory Reporting 감사 API 샘플
이 아티클은 Azure Active Directory 보고 API에 대한 아티클 컬렉션의 일부입니다.  
Azure AD Reporting은 코드 또는 관련된 도구를 사용하여 감사 데이터에 액세스할 수 있는 API를 제공합니다.
이 아티클은 **감사 API**에 대한 샘플 코드를 제공하는 방법을 다룹니다.

다음을 참조하세요.

* [감사 로그](overview-reports.md#activity-reports) 를 참조하세요.
* [Azure Active Directory Reporting API 시작](concept-reporting-api.md) 을 참조하세요.

질문, 문제 또는 피드백은 [AAD Reporting 도움말](mailto:aadreportinghelp@microsoft.com)에 문의하세요.


## <a name="prerequisites"></a>필수 조건
이 아티클에서 샘플을 사용하기 전에 [Azure AD Reporting API에 액세스하기 위한 필수 구성 요소](howto-configure-prerequisites-for-reporting-api.md)를 완료해야 합니다.  

## <a name="known-issue"></a>알려진 문제
테넌트가 EU 지역에 있는 경우는 앱 인증이 작동하지 않습니다. 이 문제를 해결하기 전까지는 해결 방법으로 감사 API에 액세스하는 데 사용자 인증을 사용하세요. 

## <a name="powershell-script"></a>PowerShell 스크립트


```powershell

# This script will require the Web Application and permissions setup in Azure Active Directory
$clientID       = "<appid>"             # ApplicationId
$clientSecret   = "<key>"         # Should be a ~44 character string insert your info here
$loginURL       = "https://login.windows.net/"
$tenantdomain   = "<domain>"            # For example, contoso.onmicrosoft.com
$msgraphEndpoint = "https://graph.microsoft.com"
$countOfAuditDocsToBeSavedInAFile = 2000
    
# Get an Oauth 2 access token based on client id, secret and tenant domain
$body       = @{grant_type="client_credentials";resource=$msgraphEndpoint;client_id=$clientID;client_secret=$clientSecret}
$oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body
    
if ($oauth.access_token -ne $null) {
    $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}
    
    $url = "$msgraphEndpoint/beta/auditLogs/directoryAudits"
    Write-Output "Fetching data using Uri: $url"
    $i=0
    $docCount=0
    Do{
        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)
        $jsonReport = ($myReport.Content | ConvertFrom-Json).value
        $fetchedRecordCount = $jsonReport.Count
        $docCount = $docCount + $fetchedRecordCount
        $totalFetchedRecordCount = $totalFetchedRecordCount + $fetchedRecordCount
        Write-Output "Fetched $fetchedRecordCount records and saved into Audits$i.json"
        if($docCount -le $countOfAuditDocsToBeSavedInAFile)
        {
            $myReport.Content | Out-File -FilePath Audits$i.json -append  -Force        
        }
        else
        {           
            $docCount=0
            $i = $i+1
        }
            
        #Get url from next link
        $url = ($myReport.Content | ConvertFrom-Json).'@odata.nextLink'         
    }while($url -ne $null)
    Write-Output "Total Fetched record count is : $totalFetchedRecordCount"
                
} else {
    Write-Host "ERROR: No Access Token"
}

```

### <a name="executing-the-powershell-script"></a>PowerShell 스크립트 실행
스크립트를 편집한 후에는 실행하여 감사 로그 보고서에서 예상한 데이터가 반환되는지 확인합니다.

스크립트는 JSON 형식으로 감사 보고서에서 출력을 반환합니다. 또한 동일한 출력으로 `Audits.json` 파일을 만듭니다. 다른 보고서의 데이터를 반환하도록 스크립트를 수정하고 필요 없는 출력 형식을 주석으로 처리할 수 있습니다.





## <a name="next-steps"></a>다음 단계
* 이 아티클의 샘플을 사용자 지정하시겠습니까? [Azure Active Directory 감사 API 참조](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit)를 확인하세요. 
* Azure Active Directory Reporting API를 사용하는 전체적인 개요를 확인하려는 경우 [Azure Active Directory Reporting API 시작](concept-reporting-api.md)을 참조하세요.
* Azure Active Directory Reporting에 대한 자세한 내용을 알아보려면 [Azure Active Directory Reporting 가이드](overview-reports.md)를 참조하세요.  

