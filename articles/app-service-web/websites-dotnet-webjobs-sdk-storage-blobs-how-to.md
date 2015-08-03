<properties 
	pageTitle="WebJob SDK를 사용하여 Azure Blob 저장소로 작업하는 방법" 
	description="WebJobs SDK를 사용하여 Azure Blob 저장소로 작업하는 방법에 대해 알아봅니다. 새 Blob이 컨테이너에 표시된 경우 프로세스를 트리거하고 'poison blobs'을 처리합니다." 
	services="app-service\web, storage" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/08/2015" 
	ms.author="tdykstra"/>

# WebJob SDK를 사용하여 Azure Blob 저장소로 작업하는 방법

## 개요

이 가이드에서는 Azure Blob를 만들거나 업데이트할 때 프로세스를 트리거하는 방법을 보여 주는 Azure Blob C# 코드 샘플을 제공합니다. 코드 샘플에서는 [WebJobs SDK](websites-dotnet-webjobs-sdk.md) 버전 1.x를 사용합니다.

Blob를 만드는 방법을 보여 주는 코드 샘플은 [WebJobs SDK를 사용하여 Azure 큐 저장소로 작업하는 방법](websites-dotnet-webjobs-sdk-storage-queues-how-to.md)을 참조하세요
		
이 가이드에서는 [저장소 계정을 가리키는 연결 문자열을 사용하여 Visual Studio에서 WebJob 프로젝트를 만드는 방법](websites-dotnet-webjobs-sdk-get-started.md)을 알고 있는 것으로 가정합니다.

## <a id="trigger"></a> Blob가 만들어지거나 업데이트될 때 함수를 트리거하는 방법

이 섹션에서는 `BlobTrigger` 특성을 사용하는 방법을 보여 줍니다.

> **참고:** WebJobs SDK는 로그 파일을 검사하여 새 BLOB 또는 변경된 BLOB을 확인합니다. 이 프로세스는 기본적으로 느리므로, BLOB를 만든 후 몇 분이 경과할 때까지 함수가 트리거되지 않을 수도 있습니다. 응용 프로그램에서 BLOB을 즉시 처리해야 하는 경우 BLOB을 만들 때 큐 메시지를 만들고 BLOB을 처리하는 함수의 `BlobTrigger` 특성 대신 [QueueTrigger](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#trigger) 특성을 사용하는 것이 좋습니다.

### 확장명을 포함하는 Blob 이름에 대한 단일 자리 표시자  

다음 코드 샘플은 *input* 컨테이너에 표시된 텍스트 Blob를 *output* 컨테이너에 복사합니다.

		public static void CopyBlob([BlobTrigger("input/{name}")] TextReader input,
		    [Blob("output/{name}")] out string output)
		{
		    output = input.ReadToEnd();
		}

특성 생성자는 컨테이너 이름과 Blob 이름의 자리 표시자를 지정하는 문자열 매개 변수를 가져옵니다. 이 예제에서는 *Blob1.txt*라는 Blob이 *input* 컨테이너에 생성된 경우 함수가 *output*컨테이너에 *Blob1.txt*라는 Blob를 만듭니다.

다음 코드 샘플과 같이 Blob 이름 자리 표시자를 사용하여 이름 패턴을 지정할 수 있습니다.

		public static void CopyBlob([BlobTrigger("input/original-{name}")] TextReader input,
		    [Blob("output/copy-{name}")] out string output)
		{
		    output = input.ReadToEnd();
		}

이 코드는 이름이 "original-"로 시작하는 Blob만 복사합니다. 예를 들어 *input* 컨테이너의 *original-Blob1.txt*가 *output* 컨테이너의 *copy-Blob1.txt*에 복사됩니다.

이름에 중괄호가 있는 Blob 이름에 대한 이름 패턴을 지정해야 하는 경우 이중 중괄호를 사용합니다. 예를 들어 *images* 컨테이너에서 이름이 다음과 같은 Blob를 찾은 경우

		{20140101}-soundfile.mp3

패턴에 다음을 사용합니다.

		images/{{20140101}}-{name}

예제에서는 *name* 번호 자리 표시자 값은 *soundfile.mp3*입니다.

### Blob 이름과 확장명에 대한 별도의 자리 표시자

다음 코드 샘플은 *input* 컨테이너에 표시된 텍스트 Blob을 *output* 컨테이너에 복사할 때 파일 확장명을 변경합니다. *input* Blob의 확장명을 기록하고 *output* Blob의 확장명을 *.txt*로 변경합니다.

		public static void CopyBlobToTxtFile([BlobTrigger("input/{name}.{ext}")] TextReader input,
		    [Blob("output/{name}.txt")] out string output,
		    string name,
		    string ext,
		    TextWriter logger)
		{
		    logger.WriteLine("Blob name:" + name);
		    logger.WriteLine("Blob extension:" + ext);
		    output = input.ReadToEnd();
		}

## <a id="types"></a> Blob에 바인딩할 수 있는 유형

다음 유형에서 `BlobTrigger` 특성을 사용할 수 있습니다.

* `string`
* `TextReader`
* `Stream`
* `ICloudBlob`
* `CloudBlockBlob`
* `CloudPageBlob`
* [ICloudBlobStreamBinder](#icbsb)에 의해 deserialize된 다른 유형 

Azure 저장소 계정으로 직접 작업하려는 경우 메서드 서명에 `CloudStorageAccount` 매개 변수를 추가할 수도 있습니다.

## <a id="string"></a> 문자열에 바인딩하여 텍스트 Blob 콘텐츠 가져오기

텍스트 Blob이 필요한 경우 `BlobTrigger`를 `string` 매개 변수에 적용할 수 있습니다. 다음 코드 샘플은 텍스트 Blob을 `logMessage`라는 `string` 매개 변수에 바인딩합니다. 이 함수에서는 이 매개 변수를 사용하여 Blob의 콘텐츠를 WebJobs SDK 대시보드에 작성합니다.
 
		public static void WriteLog([BlobTrigger("input/{name}")] string logMessage,
		    string name, 
		    TextWriter logger)
		{
		     logger.WriteLine("Blob name: {0}", name);
		     logger.WriteLine("Content:");
		     logger.WriteLine(logMessage);
		}

## <a id="icbsb"></a> ICloudBlobStreamBinder를 사용하여 serialize된 Blob 콘텐츠 가져오기

다음 코드 샘플은 `ICloudBlobStreamBinder`를 구현하여 `BlobTrigger` 특성을 사용하도록 설정하는 클래스를 사용하여 Blob을 `WebImage` 유형에 바인딩합니다.

		public static void WaterMark(
		    [BlobTrigger("images3/{name}")] WebImage input,
		    [Blob("images3-watermarked/{name}")] out WebImage output)
		{
		    output = input.AddTextWatermark("WebJobs SDK", 
		        horizontalAlign: "Center", verticalAlign: "Middle",
		        fontSize: 48, opacity: 50);
		}
		public static void Resize(
		    [BlobTrigger("images3-watermarked/{name}")] WebImage input,
		    [Blob("images3-resized/{name}")] out WebImage output)
		{
		    var width = 180;
		    var height = Convert.ToInt32(input.Height * 180 / input.Width);
		    output = input.Resize(width, height);
		}

`WebImage` 바인딩 코드는 `ICloudBlobStreamBinder`에서 파생된 `WebImageBinder` 클래스에서 제공됩니다.

		public class WebImageBinder : ICloudBlobStreamBinder<WebImage>
		{
		    public Task<WebImage> ReadFromStreamAsync(Stream input, 
		        System.Threading.CancellationToken cancellationToken)
		    {
		        return Task.FromResult<WebImage>(new WebImage(input));
		    }
		    public Task WriteToStreamAsync(WebImage value, Stream output,
		        System.Threading.CancellationToken cancellationToken)
		    {
		        var bytes = value.GetBytes();
		        return output.WriteAsync(bytes, 0, bytes.Length, cancellationToken);
		    }
		}

## <a id="poison"></a> 포이즌 Blob를 처리하는 방법

`BlobTrigger` 함수가 일시적인 오류로 인해 실패한 경우 SDK는 이 함수를 다시 호출합니다. 그러나 Blob의 콘텐츠로 인해 실패한 경우에는 Blob를 처리하려고 할 때마다 함수가 실패합니다. 기본적으로 SDK는 지정된 Blob에 대해 함수를 최대 5번 호출합니다. 5번의 시도에 실패하면 *webjobs-blobtrigger-poison*이라는 큐에 메시지를 추가합니다.

최대 다시 시도 횟수는 구성 가능합니다. 동일한 [MaxDequeueCount](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#configqueue) 설정이 포이즌 Blob 처리와 포이즌 큐 메시지 처리에 사용됩니다.

포이즌 Blob에 대한 큐 메시지는 다음 속성을 포함하는 JSON 개체입니다.

* FunctionId(*{WebJob name}*.Functions.*{Function name}* 형식, 예: WebJob1.Functions.CopyBlob)
* BlobType("BlockBlob" 또는 "PageBlob")
* ContainerName
* BlobName
* ETag(Blob 버전 식별자, 예: "0x8D1DC6E70A277EF")

다음 코드 샘플의 `CopyBlob` 함수에는 호출될 때마다 실패하게 만드는 코드가 있습니다. SDK에서 이 함수를 최대 재시도 횟수만큼 호출한 후에는 포이즌 Blob 큐에 메시지가 생성되고, 이 메시지가 `LogPoisonBlob` 함수에 의해 처리됩니다.

		public static void CopyBlob([BlobTrigger("input/{name}")] TextReader input,
		    [Blob("textblobs/output-{name}")] out string output)
		{
		    throw new Exception("Exception for testing poison blob handling");
		    output = input.ReadToEnd();
		}
		
		public static void LogPoisonBlob(
		[QueueTrigger("webjobs-blobtrigger-poison")] PoisonBlobMessage message,
		    TextWriter logger)
		{
		    logger.WriteLine("FunctionId: {0}", message.FunctionId);
		    logger.WriteLine("BlobType: {0}", message.BlobType);
		    logger.WriteLine("ContainerName: {0}", message.ContainerName);
		    logger.WriteLine("BlobName: {0}", message.BlobName);
		    logger.WriteLine("ETag: {0}", message.ETag);
		}

SDK는 JSON 메시지를 자동으로 deserialize합니다. 다음은 `PoisonBlobMessage` 클래스입니다.

		public class PoisonBlobMessage
		{
		    public string FunctionId { get; set; }
		    public string BlobType { get; set; }
		    public string ContainerName { get; set; }
		    public string BlobName { get; set; }
		    public string ETag { get; set; }
		}

### <a id="polling"></a> Blob 폴링 알고리즘

WebJobs SDK는 응용 프로그램이 시작될 때 `BlobTrigger` 특성에 지정된 모든 컨테이너를 검사합니다. 대용량 저장소 계정의 경우 이러한 검사에 약간의 시간이 걸릴 수 있으므로 새 Blob를 찾고 `BlobTrigger` 함수를 실행하기까지 조금 기다려야 할 수 있습니다.

응용 프로그램이 시작된 후 새 Blob 또는 변경된 Blob를 검색하기 위해 SDK는 Blob 저장소 로그를 주기적으로 읽습니다. Blob 로그는 버퍼되고 약 10분마다 실제로 작성되므로 Blob가 생성되거나 업데이트된 후 해당 `BlobTrigger` 함수가 실행되기 전에 지연이 발생할 수 있습니다.

`Blob` 특성을 사용하여 만드는 Blob에 대한 예외가 있습니다. WebJobs SDK는 새 Blob을 만든 경우 일치하는 모든 `BlobTrigger` 함수에 새 Blob을 즉시 전달합니다. 따라서 Blob 입력 및 출력 체인이 있는 경우 SDK는 이를 효율적으로 처리할 수 있습니다. 그러나 다른 방법으로 만들거나 업데이트한 Blob에 대해 Blob 처리 함수를 실행하는 데 소요되는 지연 시간을 줄이려면 `BlobTrigger`보다 `QueueTrigger`를 사용하는 것이 좋습니다.

### <a id="receipts"></a> Blob 수신 확인

WebJobs SDK는 동일한 새 Blob 또는 업데이트된 Blob에 대해 `BlobTrigger` 함수가 두 번 이상 호출되지 않도록 합니다. 이를 위해 *blob 수신 확인*을 유지 관리하여 지정된 Blob 버전이 처리되었는지 확인합니다.

Blob 수신 확인은 AzureWebJobsStorage 연결 문자열에 지정된 Azure 저장소 계정의 *azure-webjobs-hosts*라는 컨테이너에 저장됩니다. Blob 수신 확인에는 다음 정보가 포함됩니다.

* blob에 대해 호출된 함수("*{WebJob 이름}*.Functions.*{Function 이름}*", 예: "WebJob1.Functions.CopyBlob")
* 컨테이너 이름
* Blob 유형("BlockBlob" 또는 "PageBlob")
* Blob 이름
* ETag(Blob 버전 식별자, 예: "0x8D1DC6E70A277EF")

Blob를 강제로 처리하려면 *azure-webjobs-hosts* 컨테이너에서 해당 Blob에 대한 Blob 수신 확인을 수동으로 삭제하면 됩니다.

## <a id="queues"></a>큐 문서에서 다루는 관련 항목

큐 메시지에 의해 트리거되는 Blob을 처리하는 방법 또는 Blob 처리에 특정하지 않은 WebJobs SDK 시나리오에 대한 자세한 내용은 [WebJobs SDK를 사용하여 Azure 큐 저장소로 작업하는 방법](websites-dotnet-webjobs-sdk-storage-queues-how-to.md)을 참조하세요

이 문서에서 다루는 관련 항목은 다음과 같습니다.

* 비동기 함수
* 여러 인스턴스
* 정상 종료
* 함수 본문에 WebJobs SDK 특성 사용
* 코드에서 SDK 연결 문자열 설정
* 코드에서 WebJobs SDK 생성자 매개 변수 값 설정
* 포이즌 Blob 처리를 위한 `MaxDequeueCount` 구성
* 수동으로 함수 트리거
* 로그 작성

## <a id="nextsteps"></a> 다음 단계

이 가이드에서는 Azure Blob 작업에 대한 일반적인 시나리오를 처리하는 방법을 보여 주는 코드 샘플을 제공했습니다. Azure WebJob 및 WebJob SDK를 사용하는 방법에 대한 자세한 내용은 [Azure WebJob 권장 리소스](http://go.microsoft.com/fwlink/?linkid=390226)를 참조하세요.
 

<!---HONumber=July15_HO4-->