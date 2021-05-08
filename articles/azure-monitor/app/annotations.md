---
title: Application Insights에 대한 릴리스 주석 | Microsoft Docs
description: Application Insights에서 배포 또는 빌드 표식을 메트릭 탐색기 차트에 추가합니다.
ms.topic: conceptual
ms.date: 08/14/2020
ms.openlocfilehash: 776efd56aaa523d1c2621c51cba0446a42bb7411
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103461915"
---
# <a name="annotations-on-metric-charts-in-application-insights"></a>Application Insights의 메트릭 차트에 대한 주석

주석은 새 빌드를 배포한 위치나 다른 중요한 이벤트를 표시합니다. 주석으로 변경 내용이 애플리케이션의 성능에 영향을 주었는지 여부를 쉽게 확인할 수 있습니다. 주석은 [Azure Pipelines](/azure/devops/pipelines/tasks/) 빌드 시스템에서 자동으로 만들 수 있습니다. PowerShell에서 주석을 만들어 원하는 이벤트에 대한 플래그를 지정하는 주석을 만들 수도 있습니다.

## <a name="release-annotations-with-azure-pipelines-build"></a>Azure Pipelines 빌드를 사용한 릴리스 주석

릴리스 주석은 Azure DevOps의 클라우드 기반 Azure Pipelines 서비스 기능입니다.

### <a name="install-the-annotations-extension-one-time"></a>주석 확장 설치(한 번)

릴리스 주석을 만들려면 Visual Studio Marketplace에서 사용 가능한 여러 Azure DevOps 확장 중 하나를 설치해야 합니다.

1. [Azure DevOps](https://azure.microsoft.com/services/devops/) 프로젝트에 로그인합니다.
   
1. Visual Studio Marketplace [릴리스 주석 확장](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations) 페이지에서 Azure DevOps 조직을 선택하고 **설치** 를 선택하여 Azure DevOps 조직에 확장을 추가합니다.
   
   ![Azure DevOps 조직을 선택한 다음 설치를 선택합니다.](./media/annotations/1-install.png)
   
Azure DevOps 조직에 대해 한 번만 확장을 설치하면 됩니다. 이제 조직의 모든 프로젝트에 대해 릴리스 주석을 구성할 수 있습니다.

### <a name="configure-release-annotations"></a>릴리스 주석 구성

Azure Pipelines 릴리스 템플릿 각각에 대해 별도의 API 키를 만듭니다.

1. [Azure Portal](https://portal.azure.com)에 로그인하고 애플리케이션을 모니터링하는 Application Insights 리소스를 엽니다. Application Insights 리소스가 없다면, [새 Application Insights 리소스를 만듭니다](./app-insights-overview.md).
   
1. **API 액세스** 탭을 열고 **Application Insights ID** 를 복사합니다.
   
   ![API 액세스 아래에 애플리케이션 ID를 복사합니다.](./media/annotations/2-app-id.png)

1. 별도의 브라우저 창에서, Azure Pipelines 배포를 관리하는 릴리스 템플릿을 열거나 만듭니다.
   
1. **작업 추가** 를 선택한 후 메뉴에서 **Application Insights 릴리스 주석** 작업을 선택합니다.
   
   ![작업 추가를 선택하고 Application Insights 릴리스 주석을 선택합니다.](./media/annotations/3-add-task.png)

   > [!NOTE]
   > 릴리스 주석 작업은 현재 Windows 기반 에이전트만 지원합니다. Linux, macOS 또는 다른 유형의 에이전트에서는 실행되지 않습니다.
   
1. **API 액세스** 탭에서 복사한 Application Insights ID를 **애플리케이션 ID** 아래에 붙여넣습니다.
   
   ![Application Insights ID를 붙여넣기](./media/annotations/4-paste-app-id.png)
   
1. Application Insights 창으로 돌아가서 **API 액세스** 창에서 **API 키 만들기** 를 선택합니다. 
   
   ![API 액세스 탭에서 API 키 만들기를 선택합니다.](./media/annotations/5-create-api-key.png)
   
1. **API 키 만들기** 창에 설명을 입력하고, **주석 작성** 을 선택한 다음 **키 생성** 을 선택합니다. 새 키를 복사합니다.
   
   ![API 키 만들기 창에 설명을 입력하고, 주석 작성을 선택한 다음 키 생성을 선택합니다.](./media/annotations/6-create-api-key.png)
   
1. 릴리스 템플릿 창의 **변수** 탭에서 **추가** 를 선택하여 새 API 키에 대한 변수 정의를 만듭니다.

1. **이름** 아래에 `ApiKey`를 입력하고, **값** 아래에 **API 액세스** 탭에서 복사한 API 키를 붙여넣습니다.
   
   ![Azure DevOps 변수 탭에서 추가를 선택하고, 변수 이름을 ApiKey로 하고, 값 아래에 API 키를 붙여넣습니다.](./media/annotations/7-paste-api-key.png)
   
1. 메인 릴리스 템플릿 창에서 **저장** 을 선택하여 템플릿을 저장합니다.


   > [!NOTE]
   > API 키의 한도는 [REST API 속도 한도 설명서](https://dev.applicationinsights.io/documentation/Authorization/Rate-limits)에 설명되어 있습니다.

## <a name="view-annotations"></a>주석 보기


   > [!NOTE]
   > 현재 Application Insights의 메트릭 창에서 릴리스 주석 사용 불가능

이제 릴리스 템플릿을 사용하여 새 릴리스를 배포할 때마다 주석이 Application Insights로 전송됩니다. 다음 위치에서 주석을 볼 수 있습니다.

릴리스 주석을 수동으로 만들 수도 있는 **사용량** 창입니다.

![사용자 방문 횟수를 일정 시간 동안 표시하는 막대 차트 스크린샷입니다. 릴리스 주석이 차트 위에 녹색 체크 표시로 나타나 릴리스가 발생한 시점 표시](./media/annotations/usage-pane.png)

모든 로그 기반 통합 문서 쿼리에서는 x축이 시간을 나타내도록 시각화됩니다.

![주석이 표시된 시계열 로그 기반 쿼리가 있는 통합 문서 창의 스크린샷](./media/annotations/workbooks-annotations.png)

통합 문서에서 주석을 사용하려면 **고급 설정** 으로 가서 **주석 표시** 를 선택합니다.

![주석 표시라는 단어가 강조되어 있고, 주석 표시를 활성화하는 설정 옆에 체크 표시가 된 고급 설정 메뉴의 스크린샷.](./media/annotations/workbook-show-annotations.png)

주석 마커를 클릭하면 요청자, 소스 제어 분기, 릴리스 파이프라인, 환경 등 릴리스에 대한 세부 정보를 열어볼 수 있습니다.

## <a name="create-custom-annotations-from-powershell"></a>PowerShell에서 사용자 지정 주석 만들기
GitHub에서 CreateReleaseAnnotation PowerShell 스크립트를 사용하여 Azure DevOps를 사용하지 않고도 원하는 모든 프로세스에서 주석을 만들 수 있습니다.

1. CreateReleaseAnnotation.ps1의 로컬 복사본을 만듭니다.

    ```powershell
    
    # Copyright (c) Microsoft Corporation. All rights reserved. 
    # Licensed under the MIT License. See License.txt in the project root for license information. 
    
    # Sample usage .\CreateReleaseAnnotation.ps1 -applicationId "<appId>" -apiKey "<apiKey>" -releaseFilePath "<path to .exe with file version>" -releaseProperties @{"ReleaseDescription"="Release with annotation";"TriggerBy"="John Doe"}
    param(
        [parameter(Mandatory = $true)][string]$applicationId,
        [parameter(Mandatory = $true)][string]$apiKey,
        [parameter(Mandatory = $true)][string]$releaseFilePath,
        [parameter(Mandatory = $false)]$releaseProperties
    )
    
    $releaseName = (Get-Item $releaseFilePath).VersionInfo.FileVersion
    Write-Host "Creating release annotation $releaseName in ApplicationInsights" -ForegroundColor Cyan
    
    # background info on how fwlink works: After you submit a web request, many sites redirect through a series of intermediate pages before you finally land on the destination page.
    # So when calling Invoke-WebRequest, the result it returns comes from the final page in any redirect sequence. Hence, I set MaximumRedirection to 0, as this prevents the call to 
    # be redirected. By doing this, we get a response with status code 302, which indicates that there is a redirection link from the response body. We grab this redirection link and 
    # construct the url to make a release annotation.
    # Here's how this logic is going to works
    # 1. Client send http request, such as:  http://go.microsoft.com/fwlink/?LinkId=625115
    # 2. FWLink get the request and find out the destination URL for it, such as:  http://www.bing.com
    # 3. FWLink generate a new http response with status code “302” and with destination URL “http://www.bing.com”. Send it back to Client.
    # 4. Client, such as a powershell script, knows that status code “302” means redirection to new a location, and the target location is “http://www.bing.com”
    function GetRequestUrlFromFwLink($fwLink)
    {
        $request = Invoke-WebRequest -Uri $fwLink -MaximumRedirection 0 -UseBasicParsing -ErrorAction Ignore
        if ($request.StatusCode -eq "302") {
            return $request.Headers.Location
        }
        
        return $null
    }
    
    function CreateAnnotation($grpEnv)
    {
        $retries = 1
        $success = $false
        while (!$success -and $retries -lt 6) {
            $location = "$grpEnv/applications/$applicationId/Annotations?api-version=2015-11"
                
            Write-Host "Invoke a web request for $location to create a new release annotation. Attempting $retries"
            set-variable -Name createResultStatus -Force -Scope Local -Value $null
            set-variable -Name createResultStatusDescription -Force -Scope Local -Value $null
            set-variable -Name result -Force -Scope Local
    
            try {
                $result = Invoke-WebRequest -Uri $location -Method Put -Body $bodyJson -Headers $headers -ContentType "application/json; charset=utf-8" -UseBasicParsing
            } catch {
                if ($_.Exception){
                    if($_.Exception.Response) {
                        $createResultStatus = $_.Exception.Response.StatusCode.value__
                        $createResultStatusDescription = $_.Exception.Response.StatusDescription
                    }
                    else {
                        $createResultStatus = "Exception"
                        $createResultStatusDescription = $_.Exception.Message
                    }
                }
            }
    
            if ($result -eq $null) {
                if ($createResultStatus -eq $null) {
                    $createResultStatus = "Unknown"
                }
                if ($createResultStatusDescription -eq $null) {
                    $createResultStatusDescription = "Unknown"
                }
            }
            else {
                    $success = $true                     
            }
    
            if ($createResultStatus -eq 409 -or $createResultStatus -eq 404 -or $createResultStatus -eq 401) # no retry when conflict or unauthorized or not found
            {
                break
            }
    
            $retries = $retries + 1
            sleep 1
        }
    
        $createResultStatus
        $createResultStatusDescription
        return
    }
    
    # Need powershell version 3 or greater for script to run
    $minimumPowershellMajorVersion = 3
    if ($PSVersionTable.PSVersion.Major -lt $minimumPowershellMajorVersion) {
       Write-Host "Need powershell version $minimumPowershellMajorVersion or greater to create release annotation"
       return
    }
    
    $currentTime = (Get-Date).ToUniversalTime()
    $annotationDate = $currentTime.ToString("MMddyyyy_HHmmss")
    set-variable -Name requestBody -Force -Scope Script
    $requestBody = @{}
    $requestBody.Id = [GUID]::NewGuid()
    $requestBody.AnnotationName = $releaseName
    $requestBody.EventTime = $currentTime.GetDateTimeFormats("s")[0] # GetDateTimeFormats returns an array
    $requestBody.Category = "Deployment"
    
    if ($releaseProperties -eq $null) {
        $properties = @{}
    } else {
        $properties = $releaseProperties    
    }
    $properties.Add("ReleaseName", $releaseName)
    
    $requestBody.Properties = ConvertTo-Json($properties) -Compress
    
    $bodyJson = [System.Text.Encoding]::UTF8.GetBytes(($requestBody | ConvertTo-Json))
    $headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
    $headers.Add("X-AIAPIKEY", $apiKey)
    
    set-variable -Name createAnnotationResult1 -Force -Scope Local -Value $null
    set-variable -Name createAnnotationResultDescription -Force -Scope Local -Value ""
    
    # get redirect link from fwlink
    $requestUrl = GetRequestUrlFromFwLink("http://go.microsoft.com/fwlink/?prd=11901&pver=1.0&sbp=Application%20Insights&plcid=0x409&clcid=0x409&ar=Annotations&sar=Create%20Annotation")
    if ($requestUrl -eq $null) {
        $output = "Failed to find the redirect link to create a release annotation"
        throw $output
    }
    
    $createAnnotationResult1, $createAnnotationResultDescription = CreateAnnotation($requestUrl)
    if ($createAnnotationResult1) 
    {
         $output = "Failed to create an annotation with Id: {0}. Error {1}, Description: {2}." -f $requestBody.Id, $createAnnotationResult1, $createAnnotationResultDescription
         throw $output
    }
    
    $str = "Release annotation created. Id: {0}." -f $requestBody.Id
    Write-Host $str -ForegroundColor Green
    
    ```
   
1. 이전 프로시저의 단계에 따라 Application Insights ID를 가져오고 Application Insights **API Access** 탭에서 API 키를 만드세요.
   
1. 다음 코드로 PowerShell 스크립트를 호출하여 꺽쇠 괄호로 묶인 자리 표시자를 고유한 값으로 바꿉니다. `-releaseProperties`은 선택 사항입니다. 
   
   ```powershell
   
        .\CreateReleaseAnnotation.ps1 `
         -applicationId "<applicationId>" `
         -apiKey "<apiKey>" `
         -releaseName "<releaseName>" `
         -releaseProperties @{
             "ReleaseDescription"="<a description>";
             "TriggerBy"="<Your name>" }
   ```

과거에 대한 주석을 만드는 등 해당 스크립트를 수정할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [작업 항목 만들기](./diagnostic-search.md#create-work-item)
* [PowerShell을 사용한 Automation](./powershell.md)

