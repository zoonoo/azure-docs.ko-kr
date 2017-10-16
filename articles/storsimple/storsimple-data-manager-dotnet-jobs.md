---
title: "Microsoft Azure StorSimple 데이터 관리자 작업에 .NET SDK 사용 | Microsoft Docs"
description: ".NET SDK를 사용하여 StorSimple 데이터 관리자 작업을 시작하는 방법에 대해 알아보기(비공개 미리 보기)"
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/22/2016
ms.author: vidarmsft
ms.openlocfilehash: 44d243a034b20b99faf284c8615e470bc6f9d020
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-net-sdk-to-initiate-data-transformation-private-preview"></a>.NET SDK를 사용하여 데이터 변환 시작(비공개 미리 보기)

## <a name="overview"></a>개요

이 문서에서는 StorSimple 데이터 관리자 서비스 내에서 데이터 변환 기능을 사용하여 StorSimple 장치 데이터를 변환하는 방법에 대해 설명합니다. 그러면 변환된 데이터는 클라우드에 있는 다른 Azure 서비스에서 사용됩니다. 또한 이 문서에는 샘플 .NET 콘솔 응용 프로그램을 만들어 데이터 변환 작업을 시작하고 완료하기 위해 추적할 수 있는 연습이 있습니다.

## <a name="prerequisites"></a>필수 조건

시작하기 전에 다음 항목이 있어야 합니다.
*   시스템에 Visual Studio 2012, 2013, 2015 또는 2017을 설치합니다.
*   Azure Powershell을 설치합니다. [Azure Powershell을 다운로드합니다](https://azure.microsoft.com/documentation/articles/powershell-install-configure/).
*   데이터 변환 작업을 초기화하는 구성 설정 및 이러한 설정을 가져오는 지침도 여기에 포함됩니다.
*   리소스 그룹 내의 하이브리드 데이터 리소스에서 올바르게 구성된 작업 정의입니다.
*   모든 필수 dll입니다. 이러한 dll을 [GitHub 리포지토리](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/tree/master/Data_Manager_Job_Run/dlls)에서 다운로드합니다.
*   Github 리포지토리의 `Get-ConfigurationParams.ps1` [스크립트](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Data_Manager_Job_Run/Get-ConfigurationParams.ps1)입니다.

## <a name="step-by-step"></a>단계별 과정

데이터 변환 작업을 시작하려면 다음 단계를 수행하여 .NET을 사용합니다.

1. 구성 매개 변수를 검색하려면 다음 단계를 수행합니다.
    1. `C:\DataTransformation` 위치에서 GitHub 리포지토리 스크립트의 `Get-ConfigurationParams.ps1`을(를) 다운로드합니다.
    1. Github 리포지토리의 `Get-ConfigurationParams.ps1` 스크립트를 실행합니다. 다음 명령을 입력합니다.

        ```
        C:\DataTransformation\Get-ConfigurationParams.ps1 -SubscriptionName "AzureSubscriptionName" -ActiveDirectoryKey "AnyRandomPassword" -AppName "ApplicationName"
         ```
        ActiveDirectoryKey 및 AppName의 값을 전달할 수 있습니다.


2. 이 스크립트는 다음 값을 출력합니다.
    * 클라이언트 ID
    * 테넌트 ID
    * Active Directory 키(위에서 입력한 것과 동일)
    * 구독 ID

3. Visual Studio 2012, 2013 또는 2015를 사용하여 C# .NET 콘솔 응용 프로그램을 만듭니다.

    1. **Visual Studio 2012/2013/2015**을 실행합니다.
    1. **File**을 클릭하고 **New**를 가리킨 다음 **프로젝트**를 클릭합니다.
    2. **템플릿**을 확장하고 **Visual C#**를 선택합니다.
    3. 오른쪽의 프로젝트 형식 목록에서 **콘솔 응용 프로그램**을 선택합니다.
    4. **이름**으로 **DataTransformationApp**을 입력합니다.
    5. **위치**로 **C:\DataTransformation**을 선택합니다.
    6. **확인**을 클릭하여 프로젝트를 만듭니다.

4.  이제 만든 프로젝트에서 [dlls](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/tree/master/Data_Manager_Job_Run/dlls) 폴더에서 **참조**로 나타난 모든 DLL을 추가합니다. dll 파일을 다운로드하려면 다음을 수행합니다.

    1. Visual Studio에서 **보기 > 솔루션 탐색기**로 이동합니다.
    1. 데이터 변환 앱 프로젝트의 왼쪽에 있는 화살표를 클릭합니다. **참조**를 클릭하고 **참조 추가**를 마우스 오른쪽 단추로 선택합니다.
    2. 패키지 폴더의 위치로 이동하고 모든 DLL을 선택한 다음 **추가**를 클릭한 다음 **확인**을 클릭합니다.

5. 다음 **using** 문을 프로젝트의 원본 파일(Program.cs)에 추가합니다.

    ```
    using System;
    using System.Collections.Generic;
    using System.Threading;
    using Microsoft.Azure.Management.HybridData.Models;
    using Microsoft.Internal.Dms.DmsWebJob;
    using Microsoft.Internal.Dms.DmsWebJob.Contracts;
    ```


6. 다음 코드는 데이터 변환 작업 인스턴스를 초기화합니다. **Main 메서드**에 이 코드를 추가합니다. 앞에서 가져온 대로 구성 매개 변수 값을 바꿉니다. **리소스 그룹 이름** 및 **하이브리드 데이터 리소스 이름**의 값에 연결합니다. **리소스 그룹 이름**은 작업 정의를 구성한 하이브리드 데이터 리소스를 호스트하는 값입니다.

    ```
    // Setup the configuration parameters.
    var configParams = new ConfigurationParams
    {
        ClientId = "client-id",
        TenantId = "tenant-id",
        ActiveDirectoryKey = "active-directory-key",
        SubscriptionId = "subscription-id",
        ResourceGroupName = "resource-group-name",
        ResourceName = "resource-name"
    };

    // Initialize the Data Transformation Job instance.
    DataTransformationJob dataTransformationJob = new DataTransformationJob(configParams);

    ```

7. 작업 정의를 실행해야 하는 매개 변수를 지정합니다.

    ```
    string jobDefinitionName = "job-definition-name";

    DataTransformationInput dataTransformationInput = dataTransformationJob.GetJobDefinitionParameters(jobDefinitionName);

    ```

    (또는)

    런타임 시 작업 정의 매개 변수를 변경하려는 경우 다음 코드를 추가합니다.

    ```
    string jobDefinitionName = "job-definition-name";
    // Must start with a '\'
    var rootDirectories = new List<string> {@"\root"};

    // Name of the volume on the StorSimple device.
    var volumeNames = new List<string> {"volume-name"};

    var dataTransformationInput = new DataTransformationInput
    {
        // If you require the latest existing backup to be picked else use TakeNow to trigger a new backup.
        BackupChoice = BackupChoice.UseExistingLatest.ToString(),
        // Name of the StorSimple device.
        DeviceName = "device-name",
        // Name of the container in Azure storage where the files will be placed after execution.
        ContainerName = "container-name",
        // File name filter (search pattern) to be applied on files under the root directory. * - Match all files.
        FileNameFilter = "*",
        // List of root directories.
        RootDirectories = rootDirectories,
        // Name of the volume on StorSimple device on which the relevant data is present. 
        VolumeNames = volumeNames
    };
    
    ```

8. 초기화 후에 작업 정의에서 다음 코드를 추가하여 데이터 변환 작업을 트리거합니다. 적절한 **작업 정의 이름**에 연결합니다.

    ```
    // Trigger a job, retrieve the jobId and the retry interval for polling.
    int retryAfter;
    string jobId = dataTransformationJob.RunJobAsync(jobDefinitionName, 
    dataTransformationInput, out retryAfter);

    ```

9. 이 작업은 StorSimple 볼륨의 루트 디렉터리 아래에서 일치하는 파일을 지정된 컨테이너에 업로드합니다. 파일을 업로드하는 경우 메시지는 컨테이너와 동일한 저장소 계정에 있는 작업 정의와 동일한 이름을 갖는 큐에서 삭제됩니다. 이 메시지를 트리거로 사용하여 파일의 추가 처리를 시작할 수 있습니다.

10. 작업이 트리거되면 다음 코드를 추가하여 완성을 위해 작업을 추적합니다.

    ```
    Job jobDetails = null;

    // Poll the job.
    do
    {
        jobDetails = dataTransformationJob.GetJob(jobDefinitionName, jobId);

        // Wait before polling for the status again.
        Thread.Sleep(TimeSpan.FromSeconds(retryAfter));

    } while (jobDetails.Status == JobStatus.InProgress);

    // Completion status of the job.
    Console.WriteLine("JobStatus: {0}", jobDetails.Status);
    
    // To hold the console before exiting.
    Console.Read();

    ```


## <a name="next-steps"></a>다음 단계

[StorSimple 데이터 관리자 UI를 사용하여 데이터를 변환합니다](storsimple-data-manager-ui.md).
