<properties linkid="develop-media-services-tutorials-get-started" urlDisplayName="Get Started with Media Services" pageTitle="Get Started with Media Services - Azure" metaKeywords="Azure media services" description="An introduction to using Media Services with Azure." metaCanonical="" services="media-services" documentationCenter="" title="Get started with Media Services" authors="" solutions="" manager="" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author></tags>

# <a name="getting-started"></a>미디어 서비스 시작

이 자습서에서는 Azure 미디어 서비스를 사용하여 개발을 시작하는 방법을 설명합니다. 기본적인 미디어 서비스 워크플로와 미디어 서비스 개발에 필요한 가장 일반적인 프로그래밍 개체 및 작업을 소개합니다. 자습서를 마치면 업로드하고 인코딩하고 다운로드한 샘플 미디어 파일을 재생할 수 있습니다. 또는 서버에서 인코딩된 자산을 찾아 재생할 수 있습니다.

이 자습서의 코드가 들어 있는 C# Visual Studio 프로젝트를 [다운로드][]할 수 있습니다.

이 자습서에서는 다음 기본 단계를 단계별로 안내합니다.

-   [프로젝트 설정][]
-   [미디어 서비스 서버 컨텍스트 가져오기][]
-   [자산 생성 및 자산과 연결된 파일을 미디어 서비스로 업로드][]
-   [자산 인코딩 및 출력 자산 다운로드][]

## 필수 조건

Azure Media Services SDK를 기반으로 하는 연습 및 개발에는 다음 필수 조건이 필요합니다.

-   신규 또는 기존 Azure 구독의 미디어 서비스 계정. 자세한 내용은 [미디어 서비스 계정을 만드는 방법][](영문)을 참조하세요.
-   운영 체제: Windows 7, Windows 2008 R2 또는 Windows 8
-   .NET Framework 4.5 또는 .NET Framework 4
-   Visual Studio 2012 또는 Visual Studio 2010 SP1(Professional, Premium, Ultimate 또는 Express)
-   **Azure SDK for .NET.**, **Azure Media Services SDK for .NET** 및 **WCF Data Services 5.0 for OData V3 라이브러리**를 설치하고 [windowsazure.mediaservices Nuget][] 패키지를 사용하여 프로젝트에 참조를 추가합니다. 다음 섹션에서는 이러한 참조를 설치하고 추가하는 방법을 설명합니다.

<div class="dev-callout"><strong>참고</strong> <p>이 자습서를 완료하려면 Azure 계정이 필요합니다. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 <a href="http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=A8A8397B5" target="_blank">Azure 무료 평가판</a>을 참조하세요.</p></div>

## <span id="Step1"></span></a>프로젝트 설정

1.  Visual Studio 2012 또는 Visual Studio 2010 SP1에서 새 C# 콘솔 응용 프로그램을 만듭니다. **이름**, **위치** 및 **솔루션 이름**을 입력하고 확인을 클릭합니다.

2.  System.Configuration 어셈블리에 참조를 추가합니다.

    **참조 관리** 대화 상자를 사용하여 참조를 추가하려면 다음과 같이 합니다. **솔루션 탐색기**에서 **참조** 노드를 마우스 오른쪽 단추로 클릭하고 **참조 추가**를 선택합니다. **참조 관리** 대화 상자에서 해당 어셈블리(이 경우 System.Configuration)를 선택합니다.

3.  아직 참조를 추가하지 않은 경우 [windowsazure.mediaservices Nuget][] 패키지를 사용하여 **Azure SDK for .NET**(Microsoft.WindowsAzure.StorageClient.dll), **Azure Media Services SDK for .NET**(Microsoft.WindowsAzure.MediaServices.Client.dll) 및 **WCF Data Services 5.0 for OData V3**(Microsoft.Data.OData.dll) 라이브러리에 참조를 추가합니다.

    Nuget을 사용하여 참조를 추가하려면 다음과 같이 합니다. Visual Studio 주 메뉴에서 도구 -\> 라이브러리 패키지 관리자 -\> 패키지 관리자 콘솔을 선택합니다. 콘솔 창에 *Install-Package [패키지 이름]*을 입력하고 Enter 키를 누릅니다(이 경우 *Install-Package windowsazure.mediaservices* 명령 사용).

4.  **app.config** 파일에 *appSettings* 섹션을 추가하고 Azure 미디어 서비스 계정 이름 및 계정 키 값을 설정합니다. 계정 설정 프로세스 중 미디어 서비스 계정 이름 및 계정 키를 받았습니다. 이 값을 Visual Studio 프로젝트의 app.config 파일에서 각 설정에 대한 값 특성에 추가합니다.

    > [WACOM.NOTE]
    > Visual Studio 2012에는 App.config 파일이 기본적으로 추가되어 있습니다. Visual Studio 2010에서는 응용 프로그램 구성 파일을 수동으로 추가해야 합니다.

        <configuration>
        . . . 
        <appSettings>
            <add key="accountName" value="Add-Media-Services-Account-Name" />
            <add key="accountKey" value="Add-Media-Services-Account-Key" />
        </appSettings>
        </configuration>

5.  로컬 컴퓨터에 새 폴더를 만들고 이름을 supportFiles로 지정합니다(이 예에서 supportFiles는 MediaServicesGettingStarted 프로젝트 디렉터리에 있음). 이 연습과 함께 제공되는 [프로젝트][다운로드]에 supportFiles 디렉터리가 포함되어 있습니다. 이 디렉터리의 내용을 supportFiles 폴더로 복사할 수 있습니다.

6.  다음 코드를 사용하여 Program.cs 파일의 앞부분에 있는 기존 using 문을 덮어씁니다.

        using System;
        using System.Linq;
        using System.Configuration;
        using System.IO;
        using System.Text;
        using System.Threading;
        using System.Threading.Tasks;
        using System.Collections.Generic;
        using Microsoft.WindowsAzure;
        using Microsoft.WindowsAzure.MediaServices.Client;

7.  다음 클래스 수준의 경로 변수를 추가합니다. \*\*\_supportFiles\*\* 경로는 이전 단계에서 만든 폴더를 가리켜야 합니다.

        // Base support files path.  Update this field to point to the base path  
        // for the local support files folder that you create. 
        private static readonly string _supportFiles =
                    Path.GetFullPath(@"../..\supportFiles");

        // Paths to support files (within the above base path). You can use 
        // the provided sample media files from the "supportFiles" folder, or 
        // provide paths to your own media files below to run these samples.
        private static readonly string _singleInputFilePath =
            Path.GetFullPath(_supportFiles + @"\multifile\interview2.wmv");
        private static readonly string _outputFilesFolder =
            Path.GetFullPath(_supportFiles + @"\outputfiles");

8.  다음 클래스 수준의 변수를 추가하여 인증 및 연결 설정을 검색합니다. 이 설정은 App.Config 파일에서 가져오며 미디어 서비스에 연결하고 인증한 후 서버 컨텍스트에 액세스할 수 있도록 토큰을 받아야 합니다. 프로젝트의 코드는 이 변수를 참조하여 서버 컨텍스트의 인스턴스를 만듭니다.

        private static readonly string _accountKey = ConfigurationManager.AppSettings["accountKey"];
        private static readonly string _accountName = ConfigurationManager.AppSettings["accountName"];

9.  정적 참조로 사용되는 다음 클래스 수준의 변수를 서버 컨텍스트에 추가합니다.

        // Field for service context.
        private static CloudMediaContext _context = null;

## <span id="Step2"></span></a>미디어 서비스 컨텍스트 가져오기

미디어 서비스 컨텍스트 개체에는 미디어 서비스 프로그래밍에 액세스하는 기본적인 개체 및 컬렉션이 모두 들어 있습니다. 컨텍스트에는 작업, 자산, 파일, 액세스 정책, 로케이터 및 기타 개체를 비롯하여 중요한 컬렉션에 대한 참조가 포함됩니다. 대부분의 미디어 서비스 프로그래밍 작업의 경우 서버 컨텍스트를 가져와야 합니다.

Program.cs 파일에서 **Main** 메서드의 첫 번째 항목으로 다음 코드를 추가합니다. 이 코드는 app.config 파일의 미디어 서비스 계정 이름 및 계정 키 값을 사용하여 서버 컨텍스트의 인스턴스를 만듭니다. 인스턴스는 클래스 수준에서 만든 \*\*\_context\*\* 변수에 할당됩니다.

    // Get the service context.
    _context = new CloudMediaContext(_accountName, _accountKey);

## <span id="Step3"></span></a>자산 생성 및 파일 업로드

이 섹션의 코드는 다음과 같은 작업을 수행합니다.

1.  빈 자산을 생성합니다.
     자산을 생성할 때 암호화에 대해 세 가지 다른 옵션을 지정할 수 있습니다.

    -   **AssetCreationOptions.None**: 암호화하지 않습니다. 암호화하지 않은 자산을 생성하려면 이 옵션을 설정해야 합니다.
    -   **AssetCreationOptions.CommonEncryptionProtected**: CENC(Common Encryption Protected) 파일의 경우. 예: PlayReady로 이미 암호화된 파일 집합
    -   **AssetCreationOptions.StorageEncrypted**: 저장소 암호화. Azure 저장소로 업로드하기 전에 암호화되지 않은 입력 파일을 암호화합니다.

        <div class="dev-callout"> 
<strong>참고</strong> 
<p>미디어 서비스는 DRM(Digital Rights Manager)처럼 네트워크상이 아니라 디스크에 있는 저장소 암호화를 제공합니다.</p> 
</div>

2.  자산과 연결하려는 AssetFile 인스턴스를 생성합니다.
3.  자산에 대한 액세스 권한과 기간을 정의하는 AccessPolicy 인스턴스를 생성합니다.
4.  자산에 액세스 권한을 제공하는 Locator 인스턴스를 생성합니다.
5.  단일 미디어 파일을 미디어 서비스로 업로드합니다. 생성 및 업로드 프로세스를 자산 수집이라고도 부릅니다.

클래스에 다음 메서드를 추가합니다.

    static private IAsset CreateEmptyAsset(string assetName, AssetCreationOptions assetCreationOptions)
    {
        var asset = _context.Assets.Create(assetName, assetCreationOptions);

        Console.WriteLine("Asset name: " + asset.Name);
        Console.WriteLine("Time created: " + asset.Created.Date.ToString());

        return asset;
    }

    static public IAsset CreateAssetAndUploadSingleFile(AssetCreationOptions assetCreationOptions, string singleFilePath)
    {
        var assetName = "UploadSingleFile_" + DateTime.UtcNow.ToString();
        var asset = CreateEmptyAsset(assetName, assetCreationOptions);

        var fileName = Path.GetFileName(singleFilePath);

        var assetFile = asset.AssetFiles.Create(fileName);

        Console.WriteLine("Created assetFile {0}", assetFile.Name);

        var accessPolicy = _context.AccessPolicies.Create(assetName, TimeSpan.FromDays(3),
                                                            AccessPermissions.Write | AccessPermissions.List);

        var locator = _context.Locators.CreateLocator(LocatorType.Sas, asset, accessPolicy);

        Console.WriteLine("Upload {0}", assetFile.Name);

        assetFile.Upload(singleFilePath);
        Console.WriteLine("Done uploading of {0} using Upload()", assetFile.Name);

        locator.Delete();
        accessPolicy.Delete();

        return asset;
    }

Main 메서드에서 \*\*\_context = new CloudMediaContext(\_accountName, \_accountKey);\*\* 줄 다음에 메서드에 대한 호출을 추가합니다.

    IAsset asset = CreateAssetAndUploadSingleFile(AssetCreationOptions.None, _singleInputFilePath)

## <span id="Step4"></span></a>서버의 자산 인코딩 및 출력 자산 다운로드

미디어 서비스에서 인코딩, 암호화, 형식 변환 등 여러 가지 방법으로 미디어 콘텐츠를 처리하는 작업을 생성할 수 있습니다. 미디어 서비스 작업에는 작업 처리에 대한 세부 정보를 지정하는 태스크가 항상 하나 이상 포함됩니다. 이 섹션에서는 기본적인 인코딩 태스크를 생성한 다음 Azure Media Encoder를 사용하여 수행하는 작업을 실행합니다. 태스크는 기본 설정 문자열을 사용하여 수행하는 인코딩의 유형을 지정합니다. 사용 가능한 기본 설정 인코딩 값을 보려면 [Azure Media Encoder용 태스크 기본 설정 문자열][]을 참조하세요. 미디어 서비스는 Microsoft Expression Encoder와 같은 미디어 파일 입력 및 출력 형식을 지원합니다. 지원되는 형식 목록은 [미디어 서비스에 대해 지원되는 파일 형식][]을 참조하세요.

1.  클래스에 다음과 같은 **CreateEncodingJob** 메서드 정의를 추가합니다. 이 메서드는 인코딩 작업에 대해 필요한 여러 태스크를 수행하는 방법을 설명합니다.

    -   새 작업을 선언합니다.
    -   작업을 처리하는 미디어 프로세서를 선언합니다. 미디어 프로세서는 인코딩, 암호화, 형식 변환 및 기타 관련 처리 작업을 다루는 구성 요소입니다. 사용 가능한 미디어 프로세스에는 여러 가지 유형이 있습니다(\_context.MediaProcessors를 사용하여 모두 반복할 수 있음). 이 연습의 뒷부분에 나오는 GetLatestMediaProcessorByName 메서드는 Azure Media Encoder 프로세서를 반환합니다.
    -   새 태스크를 선언합니다. 모든 작업에는 하나 이상의 태스크가 있습니다. 태스크와 함께 식별 이름, 미디어 프로세서 인스턴스, 태스크 구성 문자열 및 태스크 생성 옵션을 전달합니다. 구성 문자열은 인코딩 설정을 지정합니다. 이 예제에서는 **H264 Broadband 720p** 설정을 사용합니다. 이 기본 설정은 단일 MP4 파일을 생성합니다. 이 기본 설정과 다른 기본 설정에 대한 자세한 내용은 [Azure Media Encoder용 태스크 기본 설정 문자열][1]을 참조하세요.
    -   태스크에 입력 자산을 추가합니다. 이 예제에서 입력 자산은 이전 섹션에서 생성한 자산입니다.
    -   태스크에 출력 자산을 추가합니다. 출력 자산의 경우 식별 이름, 작업 완료 후 서버에 출력을 저장할지 여부를 나타내는 부울 값, 저장소 및 전송에 대해 출력이 암호화되지 않음을 나타내는 **AssetCreationOptions.None** 값을 지정합니다.
    -   작업을 제출합니다.
         작업 제출은 인코딩 작업을 수행하는 데 필요한 마지막 단계입니다.

    메서드는 작업 진행 상태 추적, 인코딩 작업이 생성하는 자산에 액세스 등 다른 유용한(그러나 선택적인) 태스크를 수행하는 방법도 보여 줍니다.

        static IJob CreateEncodingJob(IAsset asset, string inputMediaFilePath, string outputFolder)
        {
            // Declare a new job.
            IJob job = _context.Jobs.Create("My encoding job");
            // Get a media processor reference, and pass to it the name of the 
            // processor to use for the specific task.
            IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Encoder");

            // Create a task with the encoding details, using a string preset.
            ITask task = job.Tasks.AddNew("My encoding task",
                processor,
                "H264 Broadband 720p",
                Microsoft.WindowsAzure.MediaServices.Client.TaskOptions.ProtectedConfiguration);

            // Specify the input asset to be encoded.
            task.InputAssets.Add(asset);
            // Add an output asset to contain the results of the job. 
            // This output is specified as AssetCreationOptions.None, which 
            // means the output asset is not encrypted. 
            task.OutputAssets.AddNew("Output asset",
                AssetCreationOptions.None);
            // Use the following event handler to check job progress.  
            job.StateChanged += new
                    EventHandler<JobStateChangedEventArgs>(StateChanged);

            // Launch the job.
            job.Submit();

            // Optionally log job details. This displays basic job details
            // to the console and saves them to a JobDetails-{JobId}.txt file 
            // in your output folder.
            LogJobDetails(job.Id);

            // Check job execution and wait for job to finish. 
            Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
            progressJobTask.Wait();

            // **********
            // Optional code.  Code after this point is not required for 
            // an encoding job, but shows how to access the assets that 
            // are the output of a job, either by creating URLs to the 
            // asset on the server, or by downloading. 
            // **********

            // Get an updated job reference.
            job = GetJob(job.Id);

            // If job state is Error the event handling 
            // method for job progress should log errors.  Here we check 
            // for error state and exit if needed.
            if (job.State == JobState.Error)
            {
                Console.WriteLine("\nExiting method due to job error.");
                return job;
            }

            // Get a reference to the output asset from the job.
            IAsset outputAsset = job.OutputMediaAssets[0];
            IAccessPolicy policy = null;
            ILocator locator = null;

            // Declare an access policy for permissions on the asset. 
            // You can call an async or sync create method. 
            policy =
                _context.AccessPolicies.Create("My 30 days readonly policy",
                    TimeSpan.FromDays(30),
                    AccessPermissions.Read);

            // Create a SAS locator to enable direct access to the asset 
            // in blob storage. You can call a sync or async create method.  
            // You can set the optional startTime param as 5 minutes 
            // earlier than Now to compensate for differences in time  
            // between the client and server clocks. 

            locator = _context.Locators.CreateLocator(LocatorType.Sas, outputAsset,
                policy,
                DateTime.UtcNow.AddMinutes(-5));

            // Build a list of SAS URLs to each file in the asset. 
            List<String> sasUrlList = GetAssetSasUrlList(outputAsset, locator);

            // Write the URL list to a local file. You can use the saved 
            // SAS URLs to browse directly to the files in the asset.
            if (sasUrlList != null)
            {
                string outFilePath = Path.GetFullPath(outputFolder + @"\" + "FileSasUrlList.txt");
                StringBuilder fileList = new StringBuilder();
                foreach (string url in sasUrlList)
                {
                    fileList.AppendLine(url);
                    fileList.AppendLine();
                }
                WriteToFile(outFilePath, fileList.ToString());

                // Optionally download the output to the local machine.
                DownloadAssetToLocal(job.Id, outputFolder);
            }

            
            return job;
        }

2.  **Main** 메서드에서 이전에 추가한 줄 다음에 **CreateEncodingJob** 메서드에 대한 호출을 추가합니다.

        CreateEncodingJob(asset, _singleInputFilePath, _outputFilesFolder);

3.  클래스에 다음과 같은 도우미 메서드를 추가합니다. 도우미 메서드는 **CreateEncodingJob** 메서드를 지원하는 데 필요합니다. 다음은 도우미 메서드에 대한 요약입니다.
    -   **GetLatestMediaProcessorByName** 메서드는 인코딩, 암호화 또는 기타 관련 처리 태스크를 다루는 적절한 미디어 프로세서를 반환합니다. 생성하려는 프로세스의 적절한 문자열 이름을 사용하여 미디어 프로세서를 생성합니다. mediaProcessor 매개 변수에 대해 메서드에 전달될 수 있는 가능한 문자열은 **Azure Media Encoder**, **Azure Media Packager**, **Azure Media Encryptor**, **Storage Decryption**입니다.

            private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
            {
                // The possible strings that can be passed into the 
                // method for the mediaProcessor parameter:
                //   Azure Media Encoder
                //   Azure Media Packager
                //   Azure Media Encryptor
                //   Storage Decryption

                var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
                    ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

                if (processor == null)
                    throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

                return processor;
            }

    -   작업을 실행할 때 작업 진행 상태를 추적하는 방법이 종종 필요합니다. 다음 코드 예제는 StateChanged 이벤트 처리기를 정의합니다. 이 이벤트 처리기는 작업 진행 상태를 추적하고 상태에 따라 업데이트된 상태를 제공합니다. 또한 다음 코드는 LogJobStop 메서드를 정의합니다. 이 도우미 메서드는 오류 세부 정보를 기록합니다.

            private static void StateChanged(object sender, JobStateChangedEventArgs e)
            {
                Console.WriteLine("Job state changed event:");
                Console.WriteLine("  Previous state: " + e.PreviousState);
                Console.WriteLine("  Current state: " + e.CurrentState);

                switch (e.CurrentState)
                {
                    case JobState.Finished:
                        Console.WriteLine();
                        Console.WriteLine("********************");
                        Console.WriteLine("Job is finished.");
                        Console.WriteLine("Please wait while local tasks or downloads complete...");
                        Console.WriteLine("********************");
                        Console.WriteLine();
                        Console.WriteLine();
                        break;
                    case JobState.Canceling:
                    case JobState.Queued:
                    case JobState.Scheduled:
                    case JobState.Processing:
                        Console.WriteLine("Please wait...\n");
                        break;
                    case JobState.Canceled:
                    case JobState.Error:
                        // Cast sender as a job.
                        IJob job = (IJob)sender;
                        // Display or log error details as needed.
                        LogJobStop(job.Id);
                        break;
                    default:
                        break;
                }
            }

            private static void LogJobStop(string jobId)
            {
                StringBuilder builder = new StringBuilder();
                IJob job = GetJob(jobId);

                builder.AppendLine("\nThe job stopped due to cancellation or an error.");
                builder.AppendLine("***************************");
                builder.AppendLine("Job ID: " + job.Id);
                builder.AppendLine("Job Name: " + job.Name);
                builder.AppendLine("Job State: " + job.State.ToString());
                builder.AppendLine("Job started (server UTC time): " + job.StartTime.ToString());
                builder.AppendLine("Media Services account name: " + _accountName);
                // Log job errors if they exist.  
                if (job.State == JobState.Error)
                {
                    builder.Append("Error Details: \n");
                    foreach (ITask task in job.Tasks)
                    {
                        foreach (ErrorDetail detail in task.ErrorDetails)
                        {
                            builder.AppendLine("  Task Id: " + task.Id);
                            builder.AppendLine("    Error Code: " + detail.Code);
                            builder.AppendLine("    Error Message: " + detail.Message + "\n");
                        }
                    }
                }
                builder.AppendLine("***************************\n");
                // Write the output to a local file and to the console. The template 
                // for an error output file is:  JobStop-{JobId}.txt
                string outputFile = _outputFilesFolder + @"\JobStop-" + JobIdAsFileName(job.Id) + ".txt";
                WriteToFile(outputFile, builder.ToString());
                Console.Write(builder.ToString());
            }

            private static void LogJobDetails(string jobId)
            {
                StringBuilder builder = new StringBuilder();
                IJob job = GetJob(jobId);

                builder.AppendLine("\nJob ID: " + job.Id);
                builder.AppendLine("Job Name: " + job.Name);
                builder.AppendLine("Job submitted (client UTC time): " + DateTime.UtcNow.ToString());
                builder.AppendLine("Media Services account name: " + _accountName);

                // Write the output to a local file and to the console. The template 
                // for an error output file is:  JobDetails-{JobId}.txt
                string outputFile = _outputFilesFolder + @"\JobDetails-" + JobIdAsFileName(job.Id) + ".txt";
                WriteToFile(outputFile, builder.ToString());
                Console.Write(builder.ToString());
            }
                    
            private static string JobIdAsFileName(string jobID)
            {
                return jobID.Replace(":", "_");
            }

    -   WriteToFile 메서드는 지정한 출력 폴더에 파일을 씁니다.

            static void WriteToFile(string outFilePath, string fileContent)
            {
                StreamWriter sr = File.CreateText(outFilePath);
                sr.Write(fileContent);
                sr.Close();
            }

    -   미디어 서비스에 자산을 인코딩한 후 인코딩 작업의 결과인 출력 자산에 액세스할 수 있습니다. 이 연습에서는 인코딩 작업의 출력에 액세스하는 두 가지 방법을 안내합니다.

        -   서버의 자산에 대한 SAS URL을 만듭니다.
        -   서버에서 출력 자산을 다운로드합니다.

        GetAssetSasUrlList 메서드는 자산의 모든 파일에 대한 SAS URL의 목록을 만듭니다.

            static List<String> GetAssetSasUrlList(IAsset asset, ILocator locator)
            {
                // Declare a list to contain all the SAS URLs.
                List<String> fileSasUrlList = new List<String>();

                // If the asset has files, build a list of URLs to 
                // each file in the asset and return. 
                foreach (IAssetFile file in asset.AssetFiles)
                {
                    string sasUrl = BuildFileSasUrl(file, locator);
                    fileSasUrlList.Add(sasUrl);
                }

                // Return the list of SAS URLs.
                return fileSasUrlList;
            }

            // Create and return a SAS URL to a single file in an asset. 
            static string BuildFileSasUrl(IAssetFile file, ILocator locator)
            {
                // Take the locator path, add the file name, and build 
                // a full SAS URL to access this file. This is the only 
                // code required to build the full URL.
                var uriBuilder = new UriBuilder(locator.Path);
                uriBuilder.Path += "/" + file.Name;

                // Optional:  print the locator.Path to the asset, and 
                // the full SAS URL to the file
                Console.WriteLine("Locator path: ");
                Console.WriteLine(locator.Path);
                Console.WriteLine();
                Console.WriteLine("Full URL to file: ");
                Console.WriteLine(uriBuilder.Uri.AbsoluteUri);
                Console.WriteLine();


                //Return the SAS URL.
                return uriBuilder.Uri.AbsoluteUri;
            }

    -   **DownloadAssetToLocal** 메서드는 자산의 각 파일을 로컬 폴더로 다운로드합니다. 이 예제에서는 자산이 하나의 입력 미디어 파일에 생성되었기 때문에 출력 자산 파일 컬렉션에 파일 2개 즉, 인코딩된 미디어 파일(.mp4 파일 1개)과 자산에 관한 메타데이터가 있는 .xml 파일 1개가 들어 있습니다. 이 메서드는 두 파일을 모두 다운로드합니다.

            static IAsset DownloadAssetToLocal(string jobId, string outputFolder)
            {
                // This method illustrates how to download a single asset. 
                // However, you can iterate through the OutputAssets
                // collection, and download all assets if there are many. 

                // Get a reference to the job. 
                IJob job = GetJob(jobId);
                // Get a reference to the first output asset. If there were multiple 
                // output media assets you could iterate and handle each one.
                IAsset outputAsset = job.OutputMediaAssets[0];

                IAccessPolicy accessPolicy = _context.AccessPolicies.Create("File Download Policy", TimeSpan.FromDays(30), AccessPermissions.Read);
                ILocator locator = _context.Locators.CreateSasLocator(outputAsset, accessPolicy);
                BlobTransferClient blobTransfer = new BlobTransferClient
                {
                    NumberOfConcurrentTransfers = 10,
                    ParallelTransferThreadCount = 10
                };

                var downloadTasks = new List<Task>();
                foreach (IAssetFile outputFile in outputAsset.AssetFiles)
                {
                    // Use the following event handler to check download progress.
                    outputFile.DownloadProgressChanged += DownloadProgress;

                    string localDownloadPath = Path.Combine(outputFolder, outputFile.Name);

                    Console.WriteLine("File download path:  " + localDownloadPath);

                    downloadTasks.Add(outputFile.DownloadAsync(Path.GetFullPath(localDownloadPath), blobTransfer, locator, CancellationToken.None));

                    outputFile.DownloadProgressChanged -= DownloadProgress;
                }

                Task.WaitAll(downloadTasks.ToArray());

                return outputAsset;
            }

            static void DownloadProgress(object sender, DownloadProgressChangedEventArgs e)
            {
                Console.WriteLine(string.Format("{0} % download progress. ", e.Progress));
            }

    -   GetJob 및 GetAsset 도우미 메서드는 지정된 ID가 있는 작업 개체 및 자산 개체를 쿼리하고 이에 대한 참조를 반환합니다. 비슷한 유형의 LINQ 쿼리를 사용하여 서버의 다른 미디어 서비스 개체에 대한 참조를 반환할 수 있습니다.

            static IJob GetJob(string jobId)
            {
                // Use a Linq select query to get an updated 
                // reference by Id. 
                var jobInstance =
                    from j in _context.Jobs
                    where j.Id == jobId
                    select j;
                // Return the job reference as an Ijob. 
                IJob job = jobInstance.FirstOrDefault();

                return job;
            }
            static IAsset GetAsset(string assetId)
            {
                // Use a LINQ Select query to get an asset.
                var assetInstance =
                    from a in _context.Assets
                    where a.Id == assetId
                    select a;
                // Reference the asset as an IAsset.
                IAsset asset = assetInstance.FirstOrDefault();

                return asset;
            }

## 코드 테스트

프로그램을 실행합니다(F5를 누릅니다). 콘솔에 다음과 비슷한 출력이 표시됩니다.

    Asset name: UploadSingleFile_11/14/2012 10:09:11 PM
    Time created: 11/14/2012 12:00:00 AM
    Created assetFile interview2.wmv
    Upload interview2.wmv
    Done uploading of interview2.wmv using Upload()

    Job ID: nb:jid:UUID:ea8d5a66-86b8-9b4d-84bc-6d406259acb8
    Job Name: My encoding job
    Job submitted (client UTC time): 11/14/2012 10:09:39 PM
    Media Services account name: Add-Media-Services-Account-Name
    Media Services account location: Add-Media-Services-account-location-name

    Job(My encoding job) state: Queued.
    Please wait...

    Job(My encoding job) state: Processing.
    Please wait...

    ********************
    Job(My encoding job) is finished.
    Please wait while local tasks or downloads complete...
    ********************

    Locator path:
    https://mediasvcd08mtz29tcpws.blob.core.windows-int.net/asset-4f5b42f4-3ade-4c2c
    -9d48-44900d4f6b62?st=2012-11-14T22%3A07%3A01Z&se=2012-11-14T23%3A07%3A01Z&sr=c&
    si=d07ec40c-02d7-4642-8e54-443b79f3ba3c&sig=XKMo0qJI5w8Fod3NsV%2FBxERnav8Jb6hL7f
    xylq3oESc%3D

    Full URL to file:
    https://mediasvcd08mtz29tcpws.blob.core.windows-int.net/asset-4f5b42f4-3ade-4c2c
    -9d48-44900d4f6b62/interview2.mp4?st=2012-11-14T22%3A07%3A01Z&se=2012-11-14T23%3
    A07%3A01Z&sr=c&si=d07ec40c-02d7-4642-8e54-443b79f3ba3c&sig=XKMo0qJI5w8Fod3NsV%2F
    BxERnav8Jb6hL7fxylq3oESc%3D

    Locator path:
    https://mediasvcd08mtz29tcpws.blob.core.windows-int.net/asset-4f5b42f4-3ade-4c2c
    -9d48-44900d4f6b62?st=2012-11-14T22%3A07%3A01Z&se=2012-11-14T23%3A07%3A01Z&sr=c&
    si=d07ec40c-02d7-4642-8e54-443b79f3ba3c&sig=XKMo0qJI5w8Fod3NsV%2FBxERnav8Jb6hL7f
    xylq3oESc%3D

    Full URL to file:
    https://mediasvcd08mtz29tcpws.blob.core.windows-int.net/asset-4f5b42f4-3ade-4c2c
    -9d48-44900d4f6b62/interview2_metadata.xml?st=2012-11-14T22%3A07%3A01Z&se=2012-1
    1-14T23%3A07%3A01Z&sr=c&si=d07ec40c-02d7-4642-8e54-443b79f3ba3c&sig=XKMo0qJI5w8F
    od3NsV%2FBxERnav8Jb6hL7fxylq3oESc%3D

    Downloads are in progress, please wait.

    File download path:  C:\supportFiles\outputfiles\interview2.mp4
    1.70952185308162 % download progress.
    3.68508804454907 % download progress.
    6.48870388360293 % download progress.
    6.83808741232649 % download progress.
    . . . 
    99.0763740574049 % download progress.
    99.1522674787341 % download progress.
    100 % download progress.
    File download path:  C:\supportFiles\outputfiles\interview2_metadata.xml
    100 % download progress.

1.  이 응용 프로그램을 실행한 결과로 다음이 발생합니다.

2.  .wmv 파일이 미디어 서비스에 로드됩니다.

3.  그런 다음 이 파일은 **Azure Media Encoder**의 기본 설정인 **H264 Broadband 720p**를 사용하여 인코딩됩니다.

4.  FileSasUrlList.txt 파일이 \\supportFiles\\outputFiles 폴더에 생성됩니다. 이 파일에는 인코딩된 자산에 대한 URL이 들어 있습니다.

    미디어 파일을 재생하려면 텍스트 파일에서 자산에 대한 URL을 복사하여 브라우저에 붙여넣습니다.

5.  .mp4 미디어 파일 및 \_metadata.xml 파일이 outputFiles 폴더에 다운로드됩니다.

<div class="dev-callout"> 
<strong>참고</strong> 
<p>미디어 서비스 개체 모델에서 자산은 많은 파일에 대해 하나를 대표하는 미디어 서비스 콘텐츠 컬렉션 개체입니다. 로케이터 경로는 Azure 저장소에서 이 자산에 대한 기본 경로인 Azure Blob URL을 제공합니다. 자산 내에서 특정 파일에 액세스하려면 기본 로케이터 경로에 파일 이름을 추가합니다.</p> 
</div>

## 다음 단계

이 연습에서는 간단한 미디어 서비스 응용 프로그램을 빌드하기 위한 일련의 프로그래밍 작업을 설명했습니다. 서버 컨텍스트 가져오기, 자산 생성, 자산 인코딩, 서버의 자산 다운로드 또는 액세스를 비롯하여 기본적인 미디어 서비스 프로그래밍 작업에 대해 배웠습니다. 다음 단계 및 고급 개발 작업에 대해서는 다음을 참조하세요.

-   [미디어 서비스 사용 방법][]
-   [Media Services REST API를 사용하여 응용 프로그램 작성][]

<!-- Anchors. -->

  [다운로드]: http://go.microsoft.com/fwlink/?linkid=253275
  [프로젝트 설정]: #Step1
  [미디어 서비스 서버 컨텍스트 가져오기]: #Step2
  [자산 생성 및 자산과 연결된 파일을 미디어 서비스로 업로드]: #Step3
  [자산 인코딩 및 출력 자산 다운로드]: #Step4
  [미디어 서비스 계정을 만드는 방법]: http://go.microsoft.com/fwlink/?LinkId=256662
  [windowsazure.mediaservices Nuget]: http://nuget.org/packages/windowsazure.mediaservices
  [Azure 무료 평가판]: http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=A8A8397B5
  [Azure Media Encoder용 태스크 기본 설정 문자열]: http://msdn.microsoft.com/en-us/library/windowsazure/jj129582.aspx
  [미디어 서비스에 대해 지원되는 파일 형식]: http://msdn.microsoft.com/en-us/library/windowsazure/hh973634.aspx
  [1]: http://msdn.microsoft.com/library/windowsazure/jj129582.aspx
  [미디어 서비스 사용 방법]: http://www.windowsazure.com/en-us/develop/net/how-to-guides/media-services/
  [Media Services REST API를 사용하여 응용 프로그램 작성]: http://msdn.microsoft.com/en-us/library/windowsazure/hh973618.aspx
