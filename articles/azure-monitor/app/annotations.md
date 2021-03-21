---
title: Application Insights에 대한 릴리스 주석 | Microsoft Docs
description: Application Insights에서 배포 또는 빌드 표식을 메트릭 탐색기 차트에 추가합니다.
ms.topic: conceptual
ms.date: 08/14/2020
ms.openlocfilehash: 776efd56aaa523d1c2621c51cba0446a42bb7411
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103461915"
---
# <a name="annotations-on-metric-charts-in-application-insights"></a>Application Insights의 메트릭 차트에 대한 주석

주석은 새 빌드를 배포한 위치 또는 기타 중요 한 이벤트를 표시 합니다. 주석을 사용 하면 변경 내용이 응용 프로그램의 성능에 어떤 영향을 미칠 수 있는지 쉽게 확인할 수 있습니다. [Azure Pipelines](/azure/devops/pipelines/tasks/) 빌드 시스템에서 자동으로 만들 수 있습니다. PowerShell에서 주석을 만들어 원하는 이벤트에 대한 플래그를 지정하는 주석을 만들 수도 있습니다.

## <a name="release-annotations-with-azure-pipelines-build"></a>Azure Pipelines 빌드를 사용 하 여 주석 릴리스

릴리스 주석은 Azure DevOps의 클라우드 기반 Azure Pipelines 서비스에 대 한 기능입니다.

### <a name="install-the-annotations-extension-one-time"></a>주석 확장 설치(한 번)

릴리스 주석을 만들 수 있으려면 Visual Studio Marketplace에서 사용할 수 있는 여러 Azure DevOps 확장 중 하나를 설치 해야 합니다.

1. [Azure DevOps](https://azure.microsoft.com/services/devops/) 프로젝트에 로그인 합니다.
   
1. Visual Studio Marketplace [릴리스 주석 확장](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations) 페이지에서 Azure devops 조직을 선택 하 고 **설치** 를 선택 하 여 azure devops 조직에 확장을 추가 합니다.
   
   ![Azure DevOps 조직을 선택한 다음 설치를 선택 합니다.](./media/annotations/1-install.png)
   
Azure DevOps 조직에 대해 확장을 한 번만 설치 하면 됩니다. 이제 조직의 모든 프로젝트에 대 한 릴리스 주석을 구성할 수 있습니다.

### <a name="configure-release-annotations"></a>릴리스 주석 구성

각 Azure Pipelines 릴리스 템플릿에 대 한 별도의 API 키를 만듭니다.

1. [Azure Portal](https://portal.azure.com) 에 로그인 하 고 응용 프로그램을 모니터링 하는 Application Insights 리소스를 엽니다. 또는 [새 Application Insights 리소스를 만듭니다](./app-insights-overview.md).
   
1. **API 액세스** 탭을 열고 **Application Insights ID** 를 복사 합니다.
   
   ![API 액세스에서 응용 프로그램 ID를 복사 합니다.](./media/annotations/2-app-id.png)

1. 별도의 브라우저 창에서 Azure Pipelines 배포를 관리 하는 릴리스 템플릿을 열거나 만듭니다.
   
1. **작업 추가** 를 선택한 후 메뉴에서 **Application Insights 릴리스 주석** 작업을 선택 합니다.
   
   ![작업 추가를 선택 하 고 Application Insights 릴리스 주석을 선택 합니다.](./media/annotations/3-add-task.png)

   > [!NOTE]
   > 릴리스 주석 작업은 현재 Windows 기반 에이전트만 지원 합니다. Linux, macOS 또는 다른 유형의 에이전트에서는 실행 되지 않습니다.
   
1. **API 액세스** 탭에서 복사한 Application Insights Id를 **응용 프로그램 id** 아래에 붙여 넣습니다.
   
   ![Application Insights ID 붙여넣기](./media/annotations/4-paste-app-id.png)
   
1. **Api 액세스** Application Insights 창으로 돌아가서 **api 키 만들기** 를 선택 합니다. 
   
   ![API 액세스 탭에서 API 키 만들기를 선택 합니다.](./media/annotations/5-create-api-key.png)
   
1. **API 키 만들기** 창에서 설명을 입력 하 고 **주석 작성** 을 선택한 다음 **키 생성** 을 선택 합니다. 새 키를 복사합니다.
   
   ![API 키 만들기 창에서 설명을 입력 하 고 주석 작성을 선택한 다음 키 생성을 선택 합니다.](./media/annotations/6-create-api-key.png)
   
1. 릴리스 템플릿 창의 **변수** 탭에서 **추가** 를 선택 하 여 새 API 키에 대 한 변수 정의를 만듭니다.

1. **이름** 에을 입력 하 `ApiKey` 고 **값** 아래에 **api 액세스** 탭에서 복사한 api 키를 붙여넣습니다.
   
   ![Azure DevOps 변수 탭에서 추가를 선택 하 고, 변수 이름을 ApiKey로 하 고, API 키를 값 아래에 붙여 넣습니다.](./media/annotations/7-paste-api-key.png)
   
1. 주 릴리스 템플릿 창에서 **저장** 을 선택 하 여 템플릿을 저장 합니다.


   > [!NOTE]
   > API 키에 대 한 제한은 [REST API rate limits 설명서](https://dev.applicationinsights.io/documentation/Authorization/Rate-limits)에 설명 되어 있습니다.

## <a name="view-annotations"></a>주석 보기


   > [!NOTE]
   > 현재 Application Insights의 메트릭 창에서는 릴리스 주석을 사용할 수 없습니다.

이제 릴리스 템플릿을 사용 하 여 새 릴리스를 배포할 때마다 주석이 Application Insights으로 전송 됩니다. 다음 위치에서 주석을 볼 수 있습니다.

또한 릴리스 주석을 수동으로 만들 수 있는 **사용** 창:

![일정 시간 동안 표시 되는 사용자 방문 수를 포함 하는 가로 막대형 차트 스크린샷 릴리스 주석은 릴리스 발생 시점을 나타내는 차트 위에 녹색 표시 표시로 표시 됩니다.](./media/annotations/usage-pane.png)

시각화가 x 축을 따라 시간을 표시 하는 모든 로그 기반 통합 문서 쿼리

![주석이 표시 된 시계열 로그 기반 쿼리를 포함 하는 통합 문서 창의 스크린샷](./media/annotations/workbooks-annotations.png)

통합 문서에서 주석을 사용 하도록 설정 하려면 **고급 설정** 으로 이동 하 여 **주석 표시** 를 선택 합니다.

![주석을 사용 하도록 설정 하는 설정 옆에 있는 확인 표시가 강조 표시 된 상태로 강조 표시 된 고급 설정 메뉴의 스크린샷](./media/annotations/workbook-show-annotations.png)

주석 마커를 선택 하 여 요청자, 소스 제어 분기, 릴리스 파이프라인 및 환경을 비롯 한 릴리스에 대 한 세부 정보를 엽니다.

## <a name="create-custom-annotations-from-powershell"></a>PowerShell에서 사용자 지정 주석 만들기
GitHub의 CreateReleaseAnnotation PowerShell 스크립트를 사용 하 여 Azure DevOps를 사용 하지 않고 원하는 모든 프로세스에서 주석을 만들 수 있습니다.

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
   
1. 이전 절차의 단계를 사용 하 여 Application Insights ID를 가져오고 Application Insights **Api 액세스** 탭에서 api 키를 만듭니다.
   
1. 다음 코드를 사용 하 여 PowerShell 스크립트를 호출 하 여 꺾쇠 괄호 자리 표시자를 값으로 바꿉니다. 는 `-releaseProperties` 선택 사항입니다. 
   
   ```powershell
   
        .\CreateReleaseAnnotation.ps1 `
         -applicationId "<applicationId>" `
         -apiKey "<apiKey>" `
         -releaseName "<releaseName>" `
         -releaseProperties @{
             "ReleaseDescription"="<a description>";
             "TriggerBy"="<Your name>" }
   ```

예를 들어 이전에 주석을 만들 수 있도록 스크립트를 수정할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [작업 항목 만들기](./diagnostic-search.md#create-work-item)
* [PowerShell을 사용한 Automation](./powershell.md)

