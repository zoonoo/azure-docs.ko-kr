<properties 
	pageTitle="Azure 미디어 인덱서를 사용하여 미디어 파일 인덱싱" 
	description="Azure 미디어 인덱서를 사용하면 미디어 파일 콘텐츠를 검색 가능하게 만들고 선택 캡션 및 키워드용 전체 텍스트 기록을 생성할 수 있습니다. 이 항목에서는 미디어 인덱서를 사용하는 방법을 보여 줍니다." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="03/25/2015" 
	ms.author="juliako"/>


# Azure 미디어 인덱서를 사용하여 미디어 파일 인덱싱

이 문서는 [미디어 서비스 주문형 비디오 워크플로](media-services-video-on-demand-workflow.md) 시리즈의 일부입니다. 

Azure 미디어 인덱서를 사용하면 미디어 파일 콘텐츠를 검색 가능하게 만들고 선택 캡션 및 키워드용 전체 텍스트 기록을 생성할 수 있습니다. 하나의 미디어 파일 또는 일괄 처리에서 여러 미디어 파일을 처리할 수 있습니다.  

>[AZURE.NOTE] 콘텐츠를 인덱싱할 때, 음성이 매우 분명한(배경 음악, 소음, 효과 또는 마이크 소음) 미디어 파일을 사용해야 합니다. 적절한 콘텐츠의 예: 회의, 강의 또는 프레젠테이션 녹음. 인덱싱에 적합하지 않을 수 있는 콘텐츠: 영화, TV 프로그램, 오디오 및 사운드 효과가 혼합된 콘텐츠, 배경 소음(기계 소음)이 들어간 녹음 품질이 좋지 않은 콘텐츠.


인덱싱 작업은 모든 인덱싱 파일에 대해 네 개의 출력을 생성합니다.

- SAMI 형식의 선택 캡션 파일.
- TTML(Timed Text Markup Language) 형식의 선택 캡션 파일.

	SAMI과 TTML 둘 다 Recognizability라는 태그를 포함합니다. 이 태그는 원본 비디오의 음성 인지 방식에 따라 인덱싱 작업에 점수를 냅니다.  유용성을 위해 화면 출력 파일에 Recognizability 값을 사용할 수 있습니다. 낮은 점수는 오디오 품질로 인해 결과가 좋지 않음을 의미합니다.
- 키워드 파일(XML).
- SQL server에서 사용할 AIB(오디오 인덱싱 Blob) 파일.
	
	자세한 내용은 [Azure 미디어 인덱서 및 SQL Server에서 AIB 파일 사용](http://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/)(영문)을 참조하세요.


이 항목에서는 **자산 인덱스** 및 **여러 파일 색인**에 대한.인덱싱 작업을 만드는 방법을 보여줍니다.

최신 Azure 미디어 인덱서 업데이트는 [미디어 서비스 블로그](http://azure.microsoft.com/blog/topics/media-services/)(영문)를 참조하세요.

## 인덱싱 태스크에 대한 구성 및 매니페스트 파일 사용

태스크 구성을 사용하여 인덱싱 태스크에 대한 세부 사항을 지정할 수 있습니다. 예를 들어, 미디어 파일에 사용할 메타데이터를 지정할 수 있습니다. 이 메타데이터는 그 언어의 어휘를 확장하고 음성 인식 정확성을 크게 향상하기 위해 언어 엔진이 사용합니다.

매니페스트 파일을 사용하여 여러 미디어 파일을 한 번에 처리할 수도 있습니다.

자세한 내용은 [Azure 미디어 인덱서의 태스크 사전 설정](https://msdn.microsoft.com/library/azure/dn783454.aspx)(영문)을 참조하세요.

## 자산 인덱스

다음 메서드는 미디어 파일을 자산으로 업로드하고 자산을 인덱스하기 위해 작업을 만듭니다.

구성 파일이 지정되지 않은 경우 해당 미디어 파일은 모든 기본 설정으로 인덱스됩니다.
	
	static bool RunIndexingJob(string inputMediaFilePath, string outputFolder, string configurationFile = "")
	{
	    // Create an asset and upload the input media file to storage.
	    IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
	        "My Indexing Input Asset",
	        AssetCreationOptions.None);
	
	    // Declare a new job.
	    IJob job = _context.Jobs.Create("My Indexing Job");
	
	    // Get a reference to the Azure Media Indexer.
	    string MediaProcessorName = "Azure Media Indexer",
	    IMediaProcessor processor = GetLatestMediaProcessorByName(MediaProcessorName);
	
	    // Read configuration from file if specified.
	    string configuration = string.IsNullOrEmpty(configurationFile) ? "" : File.ReadAllText(configurationFile);
	
	    // Create a task with the encoding details, using a string preset.
	    ITask task = job.Tasks.AddNew("My Indexing Task",
	        processor,
	        configuration,
	        TaskOptions.None);
	
	    // Specify the input asset to be indexed.
	    task.InputAssets.Add(asset);
	
	    // Add an output asset to contain the results of the job. 
	    task.OutputAssets.AddNew("My Indexing Output Asset", AssetCreationOptions.None);
	
	    // Use the following event handler to check job progress.  
	    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);
	
	    // Launch the job.
	    job.Submit();
	
	    // Check job execution and wait for job to finish. 
	    Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
	    progressJobTask.Wait();
	
	    // If job state is Error, the event handling 
	    // method for job progress should log errors.  Here we check 
	    // for error state and exit if needed.
	    if (job.State == JobState.Error)
	    {
	        Console.WriteLine("Exiting method due to job error.");
	        return false;
	    }
	
	    // Download the job outputs.
	    DownloadAsset(task.OutputAssets.First(), outputFolder);
	
	    return true;
	}
	
	static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
	{
	    IAsset asset = _context.Assets.Create(assetName, options);
	
	    var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
	    assetFile.Upload(filePath);
	
	    return asset;
	}
	        
	static void DownloadAsset(IAsset asset, string outputDirectory)
	{
	    foreach (IAssetFile file in asset.AssetFiles)
	    {
	        file.Download(Path.Combine(outputDirectory, file.Name));
	    }
	}
	
	static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
	{
	    var processor = _context.MediaProcessors
	    .Where(p => p.Name == mediaProcessorName)
	    .ToList()
	    .OrderBy(p => new Version(p.Version))
	    .LastOrDefault();
	
	    if (processor == null)
	        throw new ArgumentException(string.Format("Unknown media processor",
	                                                   mediaProcessorName));
	
	    return processor;
	} 
	
### <a id="output_files"></a>출력 파일

인덱싱 작업은 다음 출력 파일을 생성합니다. 다음 파일은 첫 번째 출력 자산에 저장됩니다.


<table border="1">
<tr><th>파일 이름</th><th>설명</th></tr>
<tr><td>InputFileName.aib </td>
<td>Audio indexing blob file.<br/><br/>
AIB(Audio Indexing Blob) 파일은 전체 텍스트 검색을 사용하는 Microsoft SQL에서 검색할 수 있는 이진 파일입니다.  AIB 파일은 더 풍부한 검색 환경을 허용하여 각각의 단어에 대체 항목을 포함하기 때문에 단일 캡션 파일보다 훨씬 강력합니다.
<br/>
<br/>
Microsoft SQL server 2008 이상을 실행하는 컴퓨터에서 Indexer SQL 추가 기능을 설치해야 합니다. Microsoft SQL server의 전체 텍스트 검색을 사용하는 AIB 검색은 WAMI에 의해 생성된 선택 캡션 파일을 검색할 때보다 훨씬 정교한 검색 결과를 제공합니다. 선택 캡션 파일은 오디오의 각 세그먼트에 가장 신뢰성이 높은 단어를 포함하지만 AIB는 사운드가 유사한 대체 단어를 포함하기 때문입니다. 음성 단어 검색이 가장 중요한 경우, Microsoft SQL Server와 함께 AIB를 사용하는 것이 좋습니다.
<br/><br/>
추가 기능을 다운로드하려면 <a href="http://aka.ms/indexersql">Azure 미디어 인덱서 SQL 추가 기능</a>을 클릭합니다.
<br/><br/>
선택 캡션에 기반한 비디오 및 키워드 XML 파일을 간단하게 인덱스하기 위해 Apache Lucene/Solr와 같은 기타 검색 엔진을 활용할 수도 있습니다. 하지만 이는 검색 결과의 정확성이 떨어집니다.</td></tr>
<tr><td>InputFileName.smi<br/>InputFileName.ttml</td>
<td>SAMI 및 TTML 형식의 CC(선택 캡션) 파일.
<br/><br/>
청각 장애가 있는 사용자가 액세스할 수 있는 오디오 및 비디오 파일을 만드는 데 사용할 수 있습니다.
<br/><br/>
SAMI과 TTML 둘 다 <b>Recognizability</b>라는 태그를 포함합니다. 이 태그는 원본 비디오의 음성 인지 방식에 따라 인덱싱 작업에 점수를 냅니다.  유용성을 위해 화면 출력 파일에 <b>Recognizability</b> 값을 사용할 수 있습니다. 낮은 점수는 오디오 품질로 인해 결과가 좋지 않음을 의미합니다.</td></tr>
<tr><td>InputFileName.kw.xml</td>
<td>키워드 파일.
<br/><br/>
키워드 파일은 빈도 및 오프셋 정보를 포함하며 음성 콘텐츠에서 추출된 키워드를 포함하는 XML 파일입니다.
<br/><br/>
음성 분석을 수행하거나, Bing, Google 또는 Microsoft SharePoint와 같은 검색 엔진에 노출하여 미디어 파일을 보다 검색하기 쉽게 하거나, 보다 관련있는 광고를 제공하는 데 사용하는 등의 여러 가지 목적을 위해 이 파일을 사용할 수 있습니다.</td></tr>
</table>

모든 입력 미디어 파일이 성공적으로 인덱스되지 않은 경우, 오류 코드 4000으로 인덱싱 작업이 실패합니다. 자세한 내용은 [오류 코드](#error_codes)를 참조하세요.

## 여러 파일 인덱스

다음 메서드는 여러 파일을 한 자산으로 업로드하고 이러한 모든 파일을 일괄로 인덱스하기 위해 작업을 만듭니다.

확장명이 .lst인 매니페스트 파일이 만들어지고 자산으로 업로드됩니다. 매니페스트 파일은 모든 자산 파일 목록을 포함합니다. 자세한 내용은 [Azure 미디어 인덱서의 태스크 사전 설정](https://msdn.microsoft.com/library/azure/dn783454.aspx)(영문)을 참조하세요.
	
	static bool RunBatchIndexingJob(string[] inputMediaFiles, string outputFolder)
	{
	    // 자산을 만들고 저장소에 업로드합니다.
	    IAsset asset = CreateAssetAndUploadMultipleFiles(inputMediaFiles,
	        "My Indexing Input Asset - Batch Mode",
	        AssetCreationOptions.None);
	
	    // 모든 자산 파일 이름을 포함하는 매니페스트 파일을 만들고 저장소에 업로드합니다.
	    string manifestFile = "input.lst";            
	    File.WriteAllLines(manifestFile, asset.AssetFiles.Select(f => f.Name).ToArray());
	    var assetFile = asset.AssetFiles.Create(Path.GetFileName(manifestFile));
	    assetFile.Upload(manifestFile);
	
	    // Declare a new job.
	    IJob job = _context.Jobs.Create("My Indexing Job - Batch Mode");
	
	    // Get a reference to the Azure Media Indexer.
	    string MediaProcessorName = "Azure Media Indexer";
	    IMediaProcessor processor = GetLatestMediaProcessorByName(MediaProcessorName);
	
	    // Read configuration.
	    string configuration = File.ReadAllText("batch.config");
	
	    // Create a task with the encoding details, using a string preset.
	    ITask task = job.Tasks.AddNew("My Indexing Task - Batch Mode",
	        processor,
	        configuration,
	        TaskOptions.None);
	
	    // Specify the input asset to be indexed.
	    task.InputAssets.Add(asset);
	
	    // Add an output asset to contain the results of the job.
	    task.OutputAssets.AddNew("My Indexing Output Asset - Batch Mode", AssetCreationOptions.None);
	
	    // Use the following event handler to check job progress.  
	    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);
	
	    // Launch the job.
	    job.Submit();
	
	    // Check job execution and wait for job to finish. 
	    Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
	    progressJobTask.Wait();
	
	    // If job state is Error, the event handling 
	    // method for job progress should log errors.  Here we check 
	    // for error state and exit if needed.
	    if (job.State == JobState.Error)
	    {
	        Console.WriteLine("Exiting method due to job error.");
	        return false;
	    }
	
	    // Download the job outputs.
	    DownloadAsset(task.OutputAssets.First(), outputFolder);
	
	    return true;
	}
	
	private static IAsset CreateAssetAndUploadMultipleFiles(string[] filePaths, string assetName, AssetCreationOptions options)
	{
	    IAsset asset = _context.Assets.Create(assetName, options);
	
	    foreach (string filePath in filePaths)
	    {
	        var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
	        assetFile.Upload(filePath);
	    }
	
	    return asset;
	}


### 출력 파일

하나 이상의 입력 미디어 파일이 있을 때, WAMI는 'JobResult.txt'라는 작업 출력을 위한 매니페스트 파일을 생성합니다. 각각의 입력 미디어 파일의 경우, AIB, SAMI, TTML 및 키워드 파일의 결과는 아래 나열된 대로 순차적으로 번호가 매겨집니다.

출력 파일 설명은 [출력 파일](#output_files)을 참조하세요. 


<table border="1">
<tr><th>파일 이름</th><th>설명</th></tr>
<tr><td>JobResult.txt</td>
<td>출력 매니페스트
<br/><br/>다음은 출력 매니페스트 파일(JobResult.txt)의 형식입니다.
<br/><br/>

<table border="1">
<tr><th>InputFile</th><th>Alias</th><th>MediaLength</th><th>오류</th></tr>
<tr><td>a.mp4</td><td>Media_1</td><td>300</td><td>0</td></tr>
<tr><td>b.mp4</td><td>Media_2</td><td>0</td><td>3000</td></tr>
<tr><td>c.mp4</td><td>Media_3</td><td>600</td><td>0</td></tr>
</table><br/>
각 행은 하나의 입력 미디어 파일을 나타냅니다.
<br/><br/>
InputFile: 자산 파일 이름 또는 입력 미디어 파일의 URL.
<br/><br/>
Alias: 대리 출력 파일 이름.
<br/><br/>
MediaLength: 입력 미디어 파일의 길이(초). 0은 이 입력에 오류를 발생할 수 있습니다.
<br/><br/>
Error: 이 미디어 파일이 성공적으로 인덱스되었음을 나타냅니다. 0인 경우 성공, 0이 아닌 경우 실패입니다. 구체적인 오류는 <a href="#error_codes">오류 코드</a>를 참조하세요.
</td></tr>
<tr><td>Media_1.aib </td>
<td>File #0 - 오디오 인덱싱 blob 파일.</td></tr>
<tr><td>Media_1.smi<br/>Media_1.ttml</td>
<td>File #0 - SAMI 및 TTML 형식의 CC(선택 캡션) 파일.</td></tr>
<tr><td>Media_1.kw.xml</td>
<td>File #0 - 키워드 파일.</td></tr>
<tr><td>Media_2.aib </td>
<td>File #1 - 오디오 인덱싱 blob 파일.</td></tr>
</table>

모든 입력 미디어 파일이 성공적으로 인덱스되지 않은 경우, 오류 코드 4000으로 인덱싱 작업이 실패합니다. 자세한 내용은 [오류 코드](#error_codes)를 참조하세요.

### 부분적으로 성공된 작업

모든 입력 미디어 파일이 성공적으로 인덱스되지 않은 경우, 오류 코드 4000으로 인덱싱 작업이 실패합니다. 자세한 내용은 [오류 코드](#error_codes)를 참조하세요.


동일한 출력(성공된 작업)이 생성됩니다. 출력 매니페스트 파일을 참조하여 오류 열 값에 따라 실패한 입력 파일을 알아볼 수 있습니다. 실패한 입력 파일의 경우, AIB, SAMI, TTML 및 키워드 파일의 결과가 생성되지 않습니다.


### <a id="error_codes"></a>오류 코드


<table border="1">
<tr><th>코드</th><th>이름</th><th>가능한 이유</th></tr>
<tr><td>2000</td><td>유효하지 않은 구성</td><td>유효하지 않은 구성</td></tr>
<tr><td>2001</td><td>유효하지 않은 입력 자산</td><td>입력 자산 유실 또는 빈 자산.</td></tr>
<tr><td>2002</td><td>유효하지 않은 매니페스트</td><td>매니페스트가 비어 있거나 유효하지 않은 항목을 포함합니다.</td></tr>
<tr><td>2003</td><td>미디어 파일 다운로드에 실패</td><td>매니페스트 파일에 유효하지 않은 URL.</td></tr>
<tr><td>2004</td><td>지원되지 않는 프로토콜</td><td>미디어 URL 프로토콜이 지원되지 않습니다.</td></tr>
<tr><td>2005</td><td>지원되지 않는 파일 유형</td><td>입력 미디어 파일 유형이 지원되지 않습니다.</td></tr>
<tr><td>2006</td><td>너무 많은 입력 파일</td><td>입력 매니페스트에 10개 이상의 파일이 있습니다. </td></tr>
<tr><td>3000</td><td>미디어 파일 디코딩 실패</td>
<td>지원되지 않는 미디어 코덱.
<br/>또는<br/>
손상된 미디어 파일.
<br/>또는<br/>
입력 미디어에 오디오 스트림 없음.</td></tr>
<tr><td>4000</td><td>인덱싱 일괄 처리 부분적으로 성공</td><td>일부 입력 미디어 파일 인덱싱에 실패했습니다. 자세한 내용은 <a href="output_files">출력 파일</a>을 참조하세요.</td></tr>
<tr><td>기타</td><td>내부 오류</td><td>지원 팀에 문의하시기 바랍니다.</td></tr>
</table>


## <a id="supported_languages"></a>지원되는 언어

현재 영어만 지원됩니다.

## 관련 링크

[Azure 미디어 인덱서 및 SQL Server에서 AIB 파일 사용](http://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/)(영문)

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->

<!--HONumber=52-->