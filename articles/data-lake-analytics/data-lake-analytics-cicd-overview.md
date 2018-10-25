---
title: Azure Data Lake Analytics에 대해 CI/CD 파이프라인을 설정하는 방법
description: Azure Data Lake Analytics에 대해 연속 통합 및 지속적인 배포를 설정하는 방법을 알아봅니다.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 09/14/2018
ms.openlocfilehash: 974ef7a51736c2e2b0a0de3c13d23ddc37fa13b7
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/08/2018
ms.locfileid: "48855020"
---
# <a name="how-to-set-up-a-cicd-pipeline-for-azure-data-lake-analytics"></a>Azure Data Lake Analytics에 대해 CI/CD 파이프라인을 설정하는 방법  

이 문서에서는 U-SQL 작업 및 U-SQL 데이터베이스에 대한 CI/CD(지속적인 통합 및 배포) 파이프라인을 설정하는 방법을 알아봅니다.  

## <a name="use-cicd-for-u-sql-jobs"></a>U-SQL 작업에 대한 CI/CD 사용

Azure Data Lake Tools for Visual Studio에서는 U-SQL 스크립트를 구성하는 데 도움이 되는 U-SQL 프로젝트 형식을 제공합니다. U-SQL 프로젝트를 사용하여 U-SQL 코드를 관리하면 추가 CI/CD 시나리오가 쉽게 구성됩니다.

## <a name="build-a-u-sql-project"></a>U-SQL 프로젝트 빌드

해당 매개 변수를 전달하여 Microsoft Build Engine(MSBuild)으로 U-SQL 프로젝트를 빌드할 수 있습니다. U-SQL 프로젝트에 대한 빌드 프로세스를 설정하려면 이 문서의 단계를 수행합니다.

### <a name="project-migration"></a>프로젝트 마이그레이션

U-SQL 프로젝트용 빌드 작업을 설정하기 전에 최신 버전의 U-SQL 프로젝트를 사용하는지 확인합니다. 편집기에서 U-SQL 프로젝트 파일을 열고 이러한 가져오기 항목이 있는지 확인합니다.

```   
<!-- check for SDK Build target in current path then in USQLSDKPath-->
<Import Project="UsqlSDKBuild.targets" Condition="Exists('UsqlSDKBuild.targets')" />
<Import Project="$(USQLSDKPath)\UsqlSDKBuild.targets" Condition="!Exists('UsqlSDKBuild.targets') And '$(USQLSDKPath)' != '' And Exists('$(USQLSDKPath)\UsqlSDKBuild.targets')" />
``` 

해당 항목이 없으면 프로젝트를 마이그레이션하는 다음 두 가지 옵션 중 하나를 사용할 수 있습니다.

- 옵션 1: 이전 가져오기 항목을 위의 항목으로 변경합니다.
- 옵션 2: Azure Data Lake Tools for Visual Studio에서 이전 프로젝트를 엽니다. 2.3.3000.0보다 최신 버전을 사용합니다. 이전 프로젝트 템플릿은 최신 버전으로 자동으로 업그레이드됩니다. 2.3.3000.0보다 최신 버전을 사용하여 만든 새로운 프로젝트는 새 템플릿을 사용합니다.

### <a name="get-nuget"></a>NuGet 가져오기

MSBuild는 U-SQL 프로젝트를 기본적으로 지원하지 않습니다. 이 지원을 추가하려면 필요한 언어 서비스를 추가하는 [Microsoft.Azure.DataLake.USQL.SDK](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) NuGet 패키지에 솔루션에 대한 참조를 추가해야 합니다.

NuGet 패키지 참조를 추가하려면 Visual Studio 솔루션 탐색기에서 솔루션을 마우스 오른쪽 단추로 클릭한 후 **NuGet 패키지 관리**를 선택합니다. 또는 솔루션 폴더에 `packages.config`라는 파일을 추가하고 다음 콘텐츠를 추가할 수 있습니다.

```xml 
<?xml version="1.0" encoding="utf-8"?>
<packages>
  <package id="Microsoft.Azure.DataLake.USQL.SDK" version="1.3.180620" targetFramework="net452" />
</packages>
``` 

### <a name="manage-u-sql-database-references"></a>U-SQL 데이터베이스 참조 관리

U-SQL 프로젝트의 U-SQL 스크립트에는 U-SQL 데이터베이스 개체에 대한 쿼리 문이 있을 수 있습니다. 이 경우 U-SQL 프로젝트를 빌드하기 전에 개체의 정의를 포함하는 해당 U-SQL 데이터베이스 프로젝트를 참조해야 합니다. U-SQL 테이블을 쿼리하거나 어셈블리를 참조하는 경우가 그 예입니다. 

[U-SQL 데이터베이스 프로젝트](data-lake-analytics-data-lake-tools-develop-usql-database.md)에 대해 자세히 알아봅니다.

>[!NOTE]
>U-SQL 데이터베이스 프로젝트는 현재 공개 미리 보기로 제공됩니다. 프로젝트에 DROP 문이 있는 경우 빌드가 실패합니다. DROP 문은 곧 허용될 예정입니다.
>

### <a name="build-a-u-sql-project-with-the-msbuild-command-line"></a>MSBuild 명령줄을 사용하여 U-SQL 프로젝트 빌드

먼저 프로젝트를 마이그레이션하고 NuGet 패키지를 가져옵니다. 그런 다음, U-SQL 프로젝트를 빌드하도록 다음 추가 인수를 사용하여 표준 MSBuild 명령줄을 호출합니다. 

``` 
msbuild USQLBuild.usqlproj /p:USQLSDKPath=packages\Microsoft.Azure.DataLake.USQL.SDK.1.3.180615\build\runtime;USQLTargetType=SyntaxCheck;DataRoot=datarootfolder;/p:EnableDeployment=true
``` 

인수 정의 및 값은 다음과 같습니다.

* **USQLSDKPath=<U-SQL Nuget package>\build\runtime**. 이 매개 변수는 U-SQL 언어 서비스에 대한 NuGet 패키지의 설치 경로를 나타냅니다.
* **USQLTargetType=Merge 또는 SyntaxCheck**:
    * **Merge**. Merge 모드는 코드 숨김 파일을 컴파일합니다. 예로 **.cs**, **.py** 및 **.r** 파일입니다. U-SQL 스크립트로 결과 사용자 정의 코드 라이브러리를 인라인합니다. 예로는 dll 이진, Python 또는 R 코드가 있습니다.
    * **SyntaxCheck**. SyntaxCheck 모드는 먼저 U-SQL 스크립트로 코드 숨김 파일을 병합합니다. 그런 다음, U-SQL 스크립트를 컴파일하여 코드의 유효성을 검사합니다.
* **DataRoot=<DataRoot path>**. DataRoot는 SyntaxCheck 모드에만 필요합니다. SyntaxCheck 모드를 사용하여 스크립트를 빌드하는 경우 MSBuild는 스크립트에서 데이터베이스 개체에 대한 참조를 확인합니다. 빌드하기 전에 빌드 머신의 DataRoot 폴더에서 U-SQL 데이터베이스의 참조된 개체를 포함하는 일치하는 로컬 환경을 설정합니다. [U-SQL 데이터베이스 프로젝트를 참조](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project)하여 이러한 데이터베이스 종속성을 관리할 수도 있습니다. MSBuild는 파일이 아닌 데이터베이스 개체 참조만 확인합니다.
* **EnableDeployment=true** 또는 **false**. EnableDeployment는 빌드 프로세스 동안 참조된 U-SQL 데이터베이스를 배포하도록 허용되는지를 나타냅니다. U-SQL 데이터베이스 프로젝트를 참조하고 U-SQL 스크립트에서 데이터베이스 개체를 사용하는 경우 이 매개 변수를 **true**로 설정합니다.

### <a name="continuous-integration-through-azure-pipelines"></a>Azure Pipelines를 통한 연속 통합

명령줄 외에 Visual Studio Build 또는 MSBuild 작업을 사용하여 Azure Pipelines에서 U-SQL 프로젝트를 빌드할 수도 있습니다. 빌드 파이프라인을 설정하려면 빌드 파이프라인의 두 가지 작업인 NuGet 복원 작업 및 MSBuild 작업을 추가해야 합니다.

![U-SQL 프로젝트에 대한 MSBuild 작업](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-task.png) 

1.  MSBuild에서 U-SQL 언어 대상을 찾을 수 있도록 NuGet 복원 작업을 추가하여 `Azure.DataLake.USQL.SDK`를 포함하는 솔루션 참조 NuGet 패키지를 가져옵니다. 2단계에서 직접 MSBuild 인수 예제를 사용하려는 경우 **고급** > **대상 디렉터리**를 `$(Build.SourcesDirectory)/packages`로 설정합니다.

    ![U-SQL 프로젝트에 대한 NuGet 복원 작업](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-nuget-task.png)

2.  다음 예제와 같이 MSBuild 인수를 Visual Studio 빌드 도구 또는 MSBuild 작업에서 설정합니다. 또는 Azure Pipelines 빌드 파이프라인에서 이러한 인수에 대한 변수를 정의할 수 있습니다.

    ![U-SQL 프로젝트에 대한 CI/CD MSBuild 변수 정의](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-variables.png) 

    ```
    /p:USQLSDKPath=/p:USQLSDKPath=$(Build.SourcesDirectory)/packages/Microsoft.Azure.DataLake.USQL.SDK.1.3.180615/build/runtime /p:USQLTargetType=SyntaxCheck /p:DataRoot=$(Build.SourcesDirectory) /p:EnableDeployment=true
    ```

### <a name="u-sql-project-build-output"></a>U-SQL 프로젝트 빌드 출력

빌드를 실행한 후, U-SQL 프로젝트의 모든 스크립트가 빌드되고 `USQLProjectName.usqlpack`이라는 zip 파일로 출력됩니다. 프로젝트의 폴더 구조는 zip으로 압축된 빌드 출력으로 유지됩니다.

> [!NOTE]
>
> 각 U-SQL 스크립트에 대한 코드 숨김 파일은 스크립트 빌드 출력에 인라인 문으로 병합됩니다.
>

## <a name="test-u-sql-scripts"></a>U-SQL 스크립트 테스트

Azure Data Lake는 U-SQL 스크립트 및 C# UDO/UDAG/UDF에 대한 테스트 프로젝트를 제공합니다.
* [U-SQL 스크립트 및 확장 C# 코드에 대한 테스트 사례 추가](data-lake-analytics-cicd-test.md#test-u-sql-scripts) 방법을 알아봅니다.
* [Azure Pipelines에서 테스트 사례를 실행](data-lake-analytics-cicd-test.md#run-test-cases-in-azure-devops)하는 방법을 알아봅니다.

## <a name="deploy-a-u-sql-job"></a>U-SQL 작업 배포

빌드 및 테스트 프로세스를 통해 코드를 확인한 후 Azure PowerShell 작업을 통해 Azure Pipelines에서 직접 U-SQL 작업을 제출할 수 있습니다. 또한 Azure Data Lake Store 또는 Azure Blob Storage에 스크립트를 배포하고 [Azure Data Factory를 통해 예약된 작업을 실행](https://docs.microsoft.com/azure/data-factory/transform-data-using-data-lake-analytics)할 수 있습니다.

### <a name="submit-u-sql-jobs-through-azure-pipelines"></a>Azure Pipelines를 통해 U-SQL 작업 제출

U-SQL 프로젝트의 빌드 출력은 **USQLProjectName.usqlpack**이라는 zip 파일입니다. zip 파일에는 프로젝트의 모든 U-SQL 스크립트가 포함되어 있습니다. Pipelines에서 다음 샘플 PowerShell 스크립트와 함께 [Azure PowerShell 작업](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-powershell?view=vsts)을 사용하여 Azure Pipelines에서 직접 U-SQL 작업을 제출할 수 있습니다.

```powershell
<#
    This script can be used to submit U-SQL Jobs with given U-SQL project build output(.usqlpack file).
    This will unzip the U-SQL project build output, and submit all scripts one-by-one.

    Note: the code behind file for each U-SQL script will be merged into the built U-SQL script in build output.
          
    Example :
        USQLJobSubmission.ps1 -ADLAAccountName "myadlaaccount" -ArtifactsRoot "C:\USQLProject\bin\debug\" -DegreeOfParallelism 2
#>

param(
    [Parameter(Mandatory=$true)][string]$ADLAAccountName, # ADLA account name to submit U-SQL jobs
    [Parameter(Mandatory=$true)][string]$ArtifactsRoot, # Root folder of U-SQL project build output
    [Parameter(Mandatory=$false)][string]$DegreeOfParallelism = 1
)

function Unzip($USQLPackfile, $UnzipOutput)
{
    $USQLPackfileZip = Rename-Item -Path $USQLPackfile -NewName $([System.IO.Path]::ChangeExtension($USQLPackfile, ".zip")) -Force -PassThru
    Expand-Archive -Path $USQLPackfileZip -DestinationPath $UnzipOutput -Force
    Rename-Item -Path $USQLPackfileZip -NewName $([System.IO.Path]::ChangeExtension($USQLPackfileZip, ".usqlpack")) -Force
}

## Get U-SQL scripts in U-SQL project build output(.usqlpack file)
Function GetUsqlFiles()
{

    $USQLPackfiles = Get-ChildItem -Path $ArtifactsRoot -Include *.usqlpack -File -Recurse -ErrorAction SilentlyContinue

    $UnzipOutput = Join-Path $ArtifactsRoot -ChildPath "UnzipUSQLScripts"

    foreach ($USQLPackfile in $USQLPackfiles)
    {
        Unzip $USQLPackfile $UnzipOutput
    }

    $USQLFiles = Get-ChildItem -Path $UnzipOutput -Include *.usql -File -Recurse -ErrorAction SilentlyContinue

    return $USQLFiles
}

## Submit U-SQL scripts to ADLA account one-by-one
Function SubmitAnalyticsJob()
{
    $usqlFiles = GetUsqlFiles

    Write-Output "$($usqlFiles.Count) jobs to be submitted..."

    # Submit each usql script and wait for completion before moving ahead.
    foreach ($usqlFile in $usqlFiles)
    {
        $scriptName = "[Release].[$([System.IO.Path]::GetFileNameWithoutExtension($usqlFile.fullname))]"

        Write-Output "Submitting job for '{$usqlFile}'"

        $jobToSubmit = Submit-AzureRmDataLakeAnalyticsJob -Account $ADLAAccountName -Name $scriptName -ScriptPath $usqlFile -DegreeOfParallelism $DegreeOfParallelism
        
        LogJobInformation $jobToSubmit
        
        Write-Output "Waiting for job to complete. Job ID:'{$($jobToSubmit.JobId)}', Name: '$($jobToSubmit.Name)' "
        $jobResult = Wait-AzureRmDataLakeAnalyticsJob -Account $ADLAAccountName -JobId $jobToSubmit.JobId  
        LogJobInformation $jobResult
    }
}

Function LogJobInformation($jobInfo)
{
    Write-Output "************************************************************************"
    Write-Output ([string]::Format("Job Id: {0}", $(DefaultIfNull $jobInfo.JobId)))
    Write-Output ([string]::Format("Job Name: {0}", $(DefaultIfNull $jobInfo.Name)))
    Write-Output ([string]::Format("Job State: {0}", $(DefaultIfNull $jobInfo.State)))
    Write-Output ([string]::Format("Job Started at: {0}", $(DefaultIfNull  $jobInfo.StartTime)))
    Write-Output ([string]::Format("Job Ended at: {0}", $(DefaultIfNull  $jobInfo.EndTime)))
    Write-Output ([string]::Format("Job Result: {0}", $(DefaultIfNull $jobInfo.Result)))
    Write-Output "************************************************************************"
}

Function DefaultIfNull($item)
{
    if ($item -ne $null)
    {
        return $item
    }
    return ""
}

Function Main()
{
    Write-Output ([string]::Format("ADLA account: {0}", $ADLAAccountName))
    Write-Output ([string]::Format("Root folde for usqlpack: {0}", $ArtifactsRoot))
    Write-Output ([string]::Format("AU count: {0}", $DegreeOfParallelism))

    Write-Output "Starting USQL script deployment..."
    
    SubmitAnalyticsJob

    Write-Output "Finished deployment..."
}

Main
```

### <a name="deploy-u-sql-jobs-through-azure-data-factory"></a>Azure Data Factory를 통해 U-SQL 작업 배포

Azure Pipelines에서 직접 U-SQL 작업을 제출할 수 있습니다. 또는 Azure Data Lake Store 또는 Azure Blob Storage에 빌드된 스크립트를 업로드하고 [Azure Data Factory를 통해 예약된 작업을 실행](https://docs.microsoft.com/azure/data-factory/transform-data-using-data-lake-analytics)할 수 있습니다.

Azure Pipelines에서 다음 샘플 PowerShell 스크립트와 함께 [Azure PowerShell 작업](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-powershell?view=vsts)을 사용하여 U-SQL 스크립트를 Azure Data Lake Store 계정으로 업로드합니다.

```powershell
<#
    This script can be used to upload U-SQL files to ADLS with given U-SQL project build output(.usqlpack file).
    This will unzip the U-SQL project build output, and upload all scripts to ADLS one-by-one.
          
    Example :
        FileUpload.ps1 -ADLSName "myadlsaccount" -ArtifactsRoot "C:\USQLProject\bin\debug\"
#>

param(
    [Parameter(Mandatory=$true)][string]$ADLSName, # ADLS account name to upload U-SQL scripts
    [Parameter(Mandatory=$true)][string]$ArtifactsRoot, # Root folder of U-SQL project build output
    [Parameter(Mandatory=$false)][string]$DesitinationFolder = "USQLScriptSource" # Desitination folder in ADLS
)

Function UploadResources()
{
    Write-Host "************************************************************************"
    Write-Host "Uploading files to $ADLSName"
    Write-Host "***********************************************************************"

    $usqlScripts = GetUsqlFiles

    $files = @(get-childitem $usqlScripts -recurse)
    foreach($file in $files)
    {
        Write-Host "Uploading file: $($file.Name)"
        Import-AzureRmDataLakeStoreItem -AccountName $ADLSName -Path $file.FullName -Destination "/$(Join-Path $DesitinationFolder $file)" -Force
    }
}

function Unzip($USQLPackfile, $UnzipOutput)
{
    $USQLPackfileZip = Rename-Item -Path $USQLPackfile -NewName $([System.IO.Path]::ChangeExtension($USQLPackfile, ".zip")) -Force -PassThru
    Expand-Archive -Path $USQLPackfileZip -DestinationPath $UnzipOutput -Force
    Rename-Item -Path $USQLPackfileZip -NewName $([System.IO.Path]::ChangeExtension($USQLPackfileZip, ".usqlpack")) -Force
}

Function GetUsqlFiles()
{

    $USQLPackfiles = Get-ChildItem -Path $ArtifactsRoot -Include *.usqlpack -File -Recurse -ErrorAction SilentlyContinue

    $UnzipOutput = Join-Path $ArtifactsRoot -ChildPath "UnzipUSQLScripts"

    foreach ($USQLPackfile in $USQLPackfiles)
    {
        Unzip $USQLPackfile $UnzipOutput
    }

    return Get-ChildItem -Path $UnzipOutput -Include *.usql -File -Recurse -ErrorAction SilentlyContinue
}

UploadResources
```

## <a name="cicd-for-a-u-sql-database"></a>U-SQL 데이터베이스에 대한 CI/CD

Azure Data Lake Tools for Visual Studio는 U-SQL 데이터베이스 프로젝트 템플릿을 제공하여 개발자들이 U-SQL 데이터베이스를 개발, 관리 및 배포할 수 있도록 합니다. [U-SQL 데이터베이스 프로젝트](data-lake-analytics-data-lake-tools-develop-usql-database.md)에 대해 자세히 알아보세요.

## <a name="build-u-sql-database-project"></a>U-SQL 데이터베이스 프로젝트 빌드

### <a name="get-the-nuget-package"></a>NuGet 패키지 가져오기

MSBuild는 U-SQL 데이터베이스 프로젝트를 기본적으로 지원하지 않습니다. 이 기능을 추가하려면 필요한 언어 서비스를 추가하는 [Microsoft.Azure.DataLake.USQL.SDK](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) NuGet 패키지에 솔루션에 대한 참조를 추가해야 합니다.

NuGet 패키지 참조를 추가하려면 Visual Studio 솔루션 탐색기에서 솔루션을 마우스 오른쪽 단추로 클릭합니다. **NuGet 패키지 관리**를 선택합니다. 그런 다음, NuGet 패키지를 검색하고 설치합니다. 또는 솔루션 폴더에 **packages.config**라는 파일을 추가하고 다음 콘텐츠를 추가할 수 있습니다.

```xml
<?xml version="1.0" encoding="utf-8"?>
<packages>
  <package id="Microsoft.Azure.DataLake.USQL.SDK" version="1.3.180615" targetFramework="net452" />
</packages>
```

### <a name="build-u-sql-a-database-project-with-the-msbuild-command-line"></a>MSBuild 명령줄을 사용하여 U-SQL 데이터베이스 프로젝트 빌드

U-SQL 데이터베이스 프로젝트를 빌드하려면 표준 MSBuild 명령줄을 호출하고 U-SQL SDK NuGet 패키지 참조를 추가 인수로 전달합니다. 다음 예제를 참조하세요. 

```
msbuild DatabaseProject.usqldbproj /p:USQLSDKPath=packages\Microsoft.Azure.DataLake.USQL.SDK.1.3.180615\build\runtime
```

인수 `USQLSDKPath=<U-SQL Nuget package>\build\runtime`은 U-SQL 언어 서비스에 대한 NuGet 패키지의 설치 경로를 나타냅니다.

### <a name="continuous-integration-with-azure-pipelines"></a>Azure Pipelines를 사용한 연속 통합

명령줄 외에 Visual Studio Build 또는 MSBuild 작업을 사용하여 Azure Pipelines에서 U-SQL 데이터베이스 프로젝트를 빌드할 수 있습니다. 빌드 작업을 설정하려면 빌드 파이프라인의 두 가지 작업인 NuGet 복원 작업 및 MSBuild 작업을 추가해야 합니다.

   ![U-SQL 프로젝트에 대한 CI/CD MSBuild 작업](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-task.png) 


1.  MSBuild에서 U-SQL 언어 대상을 찾을 수 있도록 NuGet 복원 작업을 추가하여 `Azure.DataLake.USQL.SDK`를 포함하는 솔루션 참조 NuGet 패키지를 가져옵니다. 2단계에서 직접 MSBuild 인수 예제를 사용하려는 경우 **고급** > **대상 디렉터리**를 `$(Build.SourcesDirectory)/packages`로 설정합니다.

    ![U-SQL 프로젝트를 위한 CI/CD NuGet 작업](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-nuget-task.png)

2.  다음 예제와 같이 MSBuild 인수를 Visual Studio 빌드 도구 또는 MSBuild 작업에서 설정합니다. 또는 Azure Pipelines 빌드 파이프라인에서 이러한 인수에 대한 변수를 정의할 수 있습니다.

   ![U-SQL 데이터베이스 프로젝트에 대한 CI/CD MSBuild 변수 정의](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-variables-database-project.png) 

    ```
    /p:USQLSDKPath=/p:USQLSDKPath=$(Build.SourcesDirectory)/packages/Microsoft.Azure.DataLake.USQL.SDK.1.3.180615/build/runtime
    ```
 
### <a name="u-sql-database-project-build-output"></a>U-SQL 데이터베이스 프로젝트 빌드 출력

U-SQL 데이터베이스 프로젝트에 대한 빌드 출력은 접미사 `.usqldbpack`으로 명명된 U-SQL 데이터베이스 배포 패키지입니다. `.usqldbpack` 패키지는 DDL 폴더의 단일 U-SQL 스크립트에 모든 DDL 문이 포함된 zip 파일입니다. 모든 **.dll** 및 임시 폴더에 어셈블리를 위한 추가 파일을 포함합니다.

## <a name="test-table-valued-functions-and-stored-procedures"></a>테이블 반환 함수 및 저장 프로시저 테스트

현재 테이블 반환 함수 및 저장 프로시저에 대한 테스트 사례를 직접 추가하는 것은 지원되지 않습니다. 대안으로, 해당 함수를 호출하는 U-SQL 스크립트가 있는 U-SQL 프로젝트를 만든 후 해당 테스트 사례를 쓸 수 있습니다. U-SQL 데이터베이스 프로젝트에 정의된 테이블 반환 함수 및 저장 프로시저에 대한 테스트 사례를 설정하려면 다음 단계를 따릅니다.

1.  테스트 목적으로 U-SQL 프로젝트를 만들고 테이블 반환 함수 및 저장 프로시저를 호출하는 U-SQL 스크립트를 씁니다.
2.  U-SQL 프로젝트에 데이터베이스 참조를 추가합니다. 테이블 반환 함수 및 저장 프로시저 정의를 가져오려면 DDL 문을 포함하는 데이터베이스 프로젝트를 참조해야 합니다. [데이터베이스 참조](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project)에 대해 자세히 알아보세요.
3.  테이블 반환 함수 및 저장 프로시저를 호출하는 U-SQL 스크립트에 대한 테스트 사례를 추가합니다. [U-SQL 스크립트에 대한 테스트 사례 추가](data-lake-analytics-cicd-test.md#test-u-sql-scripts) 방법을 알아봅니다.

## <a name="deploy-u-sql-database-through-azure-pipelines"></a>Azure Pipelines를 통해 U-SQL 데이터베이스 배포

`PackageDeploymentTool.exe`는 U-SQL 데이터베이스 배포 패키지 **.usqldbpack**를 배포하는 데 도움이 되는 프로그래밍 및 명령줄 인터페이스를 제공합니다. SDK는 **build/runtime/PackageDeploymentTool.exe**에 있는 [U-SQL SDK NuGet 패키지](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/)에 포함되어 있습니다. `PackageDeploymentTool.exe`를 사용하여 Azure Data Lake Analytics와 로컬 계정 둘 다에 U-SQL 데이터베이스를 배포할 수 있습니다.

> [!NOTE]
>
> U-SQL 데이터베이스 배포를 위한 PowerShell 명령줄 지원 및 Azure Pipelines 릴리스 작업 지원은 현재 보류 중입니다.
>

Azure Pipelines에서 데이터베이스 배포 작업을 설정하려면 다음 단계를 따릅니다.

1. 빌드 또는 릴리스 파이프라인에 PowerShell 스크립트 작업을 추가하고 다음 PowerShell 스크립트를 실행합니다. 이 태스크는 `PackageDeploymentTool.exe` 및 `PackageDeploymentTool.exe`에 대한 Azure SDK 종속성을 가져오는 데 도움이 됩니다. **-AzureSDK** 및 **-DBDeploymentTool** 매개 변수를 설정하여 종속성 및 배포 도구를 일부 특정 폴더로 로드할 수 있습니다. 2단계에서 `PackageDeploymentTool.exe`에 대한 **-AzureSDK** 경로를 **-AzureSDKPath** 매개 변수로서 전달합니다. 

    ```powershell
    <#
        This script is used for getting dependencies and SDKs for U-SQL database deployment.
        PowerShell command line support for deploying U-SQL database package(.usqldbpack file) will come soon.
        
        Example :
            GetUSQLDBDeploymentSDK.ps1 -AzureSDK "AzureSDKFolderPath" -DBDeploymentTool "DBDeploymentToolFolderPath"
    #>

    param (
        [string]$AzureSDK = "AzureSDK", # Folder to cache Azure SDK dependencies
        [string]$DBDeploymentTool = "DBDeploymentTool", # Folder to cache U-SQL database deployment tool
        [string]$workingfolder = "" # Folder to execute these command lines
    )

    if ([string]::IsNullOrEmpty($workingfolder))
    {
        $scriptpath = $MyInvocation.MyCommand.Path
        $workingfolder = Split-Path $scriptpath
    }
    cd $workingfolder

    echo "workingfolder=$workingfolder, outputfolder=$outputfolder"
    echo "Downloading required packages..."

    iwr https://www.nuget.org/api/v2/package/Microsoft.Azure.Management.DataLake.Analytics/3.5.1-preview -outf Microsoft.Azure.Management.DataLake.Analytics.3.5.1-preview.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.Azure.Management.DataLake.Store/2.4.1-preview -outf Microsoft.Azure.Management.DataLake.Store.2.4.1-preview.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.IdentityModel.Clients.ActiveDirectory/2.28.3 -outf Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.Rest.ClientRuntime/2.3.11 -outf Microsoft.Rest.ClientRuntime.2.3.11.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.Rest.ClientRuntime.Azure/3.3.7 -outf Microsoft.Rest.ClientRuntime.Azure.3.3.7.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.Rest.ClientRuntime.Azure.Authentication/2.3.3 -outf Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3.zip
    iwr https://www.nuget.org/api/v2/package/Newtonsoft.Json/6.0.8 -outf Newtonsoft.Json.6.0.8.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.Azure.DataLake.USQL.SDK/ -outf USQLSDK.zip

    echo "Extracting packages..."

    Expand-Archive Microsoft.Azure.Management.DataLake.Analytics.3.5.1-preview.zip -DestinationPath Microsoft.Azure.Management.DataLake.Analytics.3.5.1-preview -Force
    Expand-Archive Microsoft.Azure.Management.DataLake.Store.2.4.1-preview.zip -DestinationPath Microsoft.Azure.Management.DataLake.Store.2.4.1-preview -Force
    Expand-Archive Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3.zip -DestinationPath Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3 -Force
    Expand-Archive Microsoft.Rest.ClientRuntime.2.3.11.zip -DestinationPath Microsoft.Rest.ClientRuntime.2.3.11 -Force
    Expand-Archive Microsoft.Rest.ClientRuntime.Azure.3.3.7.zip -DestinationPath Microsoft.Rest.ClientRuntime.Azure.3.3.7 -Force
    Expand-Archive Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3.zip -DestinationPath Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3 -Force
    Expand-Archive Newtonsoft.Json.6.0.8.zip -DestinationPath Newtonsoft.Json.6.0.8 -Force
    Expand-Archive USQLSDK.zip -DestinationPath USQLSDK -Force

    echo "Copy required DLLs to output folder..."

    mkdir $AzureSDK -Force
    mkdir $DBDeploymentTool -Force
    copy Microsoft.Azure.Management.DataLake.Analytics.3.5.1-preview\lib\net452\*.dll $AzureSDK
    copy Microsoft.Azure.Management.DataLake.Store.2.4.1-preview\lib\net452\*.dll $AzureSDK
    copy Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3\lib\net45\*.dll $AzureSDK
    copy Microsoft.Rest.ClientRuntime.2.3.11\lib\net452\*.dll $AzureSDK
    copy Microsoft.Rest.ClientRuntime.Azure.3.3.7\lib\net452\*.dll $AzureSDK
    copy Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3\lib\net452\*.dll $AzureSDK
    copy Newtonsoft.Json.6.0.8\lib\net45\*.dll $AzureSDK
    copy USQLSDK\build\runtime\*.* $DBDeploymentTool
    ```

2. 빌드 또는 릴리스 파이프라인에 **명령줄 작업**을 추가하고, `PackageDeploymentTool.exe`를 호출하여 스크립트를 채웁니다. `PackageDeploymentTool.exe`는 정의된 **$DBDeploymentTool** 폴더에 있습니다. 샘플 스크립트는 다음과 같습니다. 

    * 로컬로 U-SQL 데이터베이스를 배포합니다.

        ```
        PackageDeploymentTool.exe deploylocal -Package <package path> -Database <database name> -DataRoot <data root path>
        ```

    * 대화형 인증 모드를 사용하여 Azure Data Lake Analytics 계정에 U-SQL 데이터베이스를 배포합니다.

        ```
        PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tenant name> -AzureSDKPath <azure sdk path> -Interactive
        ```

    * **비밀** 인증을 사용하여 Azure Data Lake Analytics 계정에 U-SQL 데이터베이스를 배포합니다.

        ```
        PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tenant name> -ClientId <client id> -Secrete <secrete>
        ```

    * **certFile** 인증을 사용하여 Azure Data Lake Analytics 계정에 U-SQL 데이터베이스를 배포합니다.

        ```
        PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tenant name> -ClientId <client id> -Secrete <secrete> -CertFile <certFile>
        ```

### <a name="packagedeploymenttoolexe-parameter-descriptions"></a>PackageDeploymentTool.exe 매개 변수 설명

#### <a name="common-parameters"></a>일반 매개 변수

| 매개 변수 | 설명 | 기본값 | 필수 |
|---------|-----------|-------------|--------|
|패키지|배포할 U-SQL 데이터베이스 배포 패키지의 경로입니다.|null|true|
|데이터베이스|배포하거나 만들 데이터베이스 이름입니다.|master|false|
|LogFile|로깅을 위한 파일의 경로입니다. 표준 출력에 대한 기본값입니다(콘솔).|null|false|
|LogLevel|로그 수준: 자세한 정보, 보통, 경고 또는 오류입니다.|LogLevel.Normal|false|

#### <a name="parameter-for-local-deployment"></a>로컬 배포에 대한 매개 변수입니다.

|매개 변수|설명|기본값|필수|
|---------|-----------|-------------|--------|
|DataRoot|로컬 데이터 루트 폴더의 경로입니다.|null|true|

#### <a name="parameters-for-azure-data-lake-analytics-deployment"></a>Azure Data Lake Analytics 배포에 대한 매개 변수입니다.

|매개 변수|설명|기본값|필수|
|---------|-----------|-------------|--------|
|계좌|계정 이름별로 배포할 Azure Data Lake Analytics 계정을 지정합니다.|null|true|
|ResourceGroup|Azure Data Lake Analytics 계정에 대한 Azure 리소스 그룹 이름입니다.|null|true|
|SubscriptionId|Azure Data Lake Analytics 계정에 대한 Azure 구독 ID입니다.|null|true|
|테넌트|테넌트 이름은 Azure AD(Azure Active Directory) 도메인 이름입니다. Azure Portal의 구독 관리 페이지에서 찾을 수 있습니다.|null|true|
|AzureSDKPath|Azure SDK에서 종속 어셈블리를 검색하기 위한 경로입니다.|null|true|
|대화형|인증에 대화형 모드를 사용할지 여부입니다.|false|false|
|clientid|비대화형 인증을 위해 필요한 Azure AD 응용 프로그램 ID입니다.|null|비대화형 인증에 필요합니다.|
|Secrete|비대화형 인증을 위한 비밀 또는 암호입니다. 신뢰할 수 있고 안전한 환경에서만 사용해야 합니다.|null|비대화형 인증에서 필수입니다. 그렇지 않으면 SecreteFile을 사용합니다.|
|SecreteFile|비대화형 인증을 위한 비밀 또는 암호를 저장하는 파일입니다. 현재 사용자만 읽을 수 있도록 유지해야 합니다.|null|비대화형 인증에서 필수입니다. 그렇지 않으면 비밀을 사용합니다.|
|CertFile|비대화형 인증을 위한 X.509 인증서를 저장하는 파일입니다. 기본값은 클라이언트 비밀 인증을 사용하는 것입니다.|null|false|
| JobPrefix | U-SQL DDL 작업의 데이터베이스 배포에 대한 접두사입니다. | Deploy_ + DateTime.Now | false |

## <a name="next-steps"></a>다음 단계

- [Azure Data Lake Analytics 코드를 테스트하는 방법](data-lake-analytics-cicd-test.md).
- [로컬 머신에서 U-SQL 스크립트 실행](data-lake-analytics-data-lake-tools-local-run.md).
- [U-SQL 데이터베이스 프로젝트를 사용하여 U-SQL 데이터베이스 개발](data-lake-analytics-data-lake-tools-develop-usql-database.md).
