<properties 
	pageTitle="Media Services SDK for .NET 시작 - Azure" 
	description="이 자습서에서는 .NET을 사용한 Azure 미디어 서비스로 VoD(주문형 비디오) 콘텐츠 배달 응용 프로그램을 구현하는 단계를 안내합니다." 
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
	ms.date="02/24/2015" 
	ms.author="juliako"/>


# 퀵 스타트: Media Services SDK for .NET을 사용하여 주문형 비디오 배달 

[AZURE.INCLUDE [media-services-selector-get-started](../../includes/media-services-selector-get-started.md)]


>[AZURE.NOTE]
> 이 자습서를 완료하려면 Azure 계정이 필요합니다. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A8A8397B5" target="_blank">Azure 무료 평가판</a>을 참조하세요.

이 자습서에서는 Azure Media Services(AMS) SDK for .NET를 사용하여 VoD(주문형 비디오) 콘텐츠 배달 응용 프로그램을 구현하는 단계를 안내합니다. 


기본적인 미디어 서비스 워크플로와 미디어 서비스 개발에 필요한 가장 일반적인 프로그래밍 개체 및 작업을 소개합니다. 자습서를 마치면 업로드하고 인코딩하고 다운로드한 샘플 미디어 파일을 스트리밍하거나 점진적으로 다운로드할 수 있습니다.  

## 필수 조건
Media Services SDK for .NET을 사용하여 개발을 시작하려면 다음 필수 조건이 필요합니다.

- 운영 체제: Windows 7, Windows 2008 R2, Windows 8 이상
- .NET Framework 4.5 또는 .NET Framework 4.0
- Visual Studio 2013, Visual Studio 2012, Visual Studio 2010 SP1(Professional, Premium, Ultimate 또는 Express)


다음 작업은 본 퀵 스타트에 표시됩니다.

1.  포털을 사용하여 미디어 서비스 계정 만들기
2.  스트리밍 끝점 구성(포털 사용)
3.  Visual Studio 프로젝트 만들기 및 구성
5.  미디어 서비스 계정에 연결
1.  새 자산 만들기 및 비디오 파일 업로드
1.  원본 파일을 적응 비트 전송률 MP4 파일 집합으로 인코딩
1.  인코딩된 자산에 대한 배달 정책 구성
1.  자산 게시, 스트리밍 기능 사용 및 URL 점진적으로 다운로드  
1.  콘텐츠 재생 


## 포털을 사용하여 미디어 서비스 계정 만들기

1. [관리 포털][]에서 **새로 만들기**, **미디어 서비스**, **빠른 생성**을 차례로 클릭합니다.
   
	![미디어 서비스 빠른 생성](./media/media-services-dotnet-get-started/wams-QuickCreate.png)

2. **이름**에 새 계정의 이름을 입력합니다. 미디어 서비스 계정 이름은 공백 없이 모두 소문자로 이루어진 3-24자의 숫자 또는 문자입니다. 

3. **지역**에서 미디어 서비스 계정에 대한 메타데이터 레코드를 저장하는 데 사용할 지역을 선택합니다. 사용 가능한 미디어 서비스 지역만 드롭다운에 표시됩니다. 

4. **저장소 계정**에서 미디어 서비스 계정의 미디어 콘텐츠가 포함된 Blob 저장소를 제공할 저장소 계정을 선택합니다. 미디어 서비스 계정과 동일한 지역의 기존 저장소 계정을 선택하거나 새 저장소 계정을 만들 수 있습니다. 동일한 지역에 새 저장소 계정이 생성됩니다. 

5. 새 저장소 계정을 만든 경우 **새 저장소 계정 이름**에 저장소 계정의 이름을 입력합니다. 저장소 계정 이름에 대한 규칙은 미디어 서비스 계정의 경우와 같습니다.

6. 양식 맨 아래에 있는 **빠른 생성**을 클릭합니다.

	창 맨 아래에 있는 메시지 영역에서 프로세스의 상태를 모니터링할 수 있습니다.

	계정이 만들어지면 상태가 활성으로 변경됩니다. 
	
	페이지의 맨 아래에 **키 관리** 단추가 나타납니다. 이 단추를 클릭하면 미디어 서비스 계정 이름과 기본 키 및 보조 키가 포함된 대화 상자가 표시됩니다. 프로그래밍 방식으로 미디어 서비스 계정에 액세스하려면 계정 이름과 기본 키 정보가 필요합니다. 

	
	![미디어 서비스 페이지](./media/media-services-dotnet-get-started/wams-mediaservices-page.png)

	계정 이름을 두 번 클릭하면 기본적으로 퀵 스타트 페이지가 표시됩니다. 이 페이지를 통해 포털의 다른 페이지에서도 사용할 수 있는 몇 가지 관리 작업을 수행할 수 있습니다. 예를 들어 이 페이지에서 비디오 파일을 업로드하거나 콘텐츠 페이지에서 업로드할 수 있습니다.

	 
## 포털을 사용하여 스트리밍 끝점 구성

Azure 미디어 서비스 작업 시 가장 일반적인 시나리오 중 하나는 클라이언트에 적응 비트 전송률 스트리밍을 제공하는 것입니다. 적응 비트 전송률 스트리밍을 사용하면 현재 네트워크 대역폭, CPU 사용률 및 기타 요인에 따라 비디오가 표시되므로 클라이언트는 더 높거나 낮은 비트 전송률 스트림으로 전환할 수 있습니다. 미디어 서비스가 지원하는 적응 비트 전송률 스트리밍 기술은 HLS(HTTP 라이브 스트리밍), 부드러운 스트리밍, MPEG DASH 및 HDS(Adobe PrimeTime/Access 정식 사용자만 해당)입니다. 

미디어 서비스는 적응 비트 전송률 MP4 또는 부드러운 스트리밍 인코딩 콘텐츠를 미디어 서비스에서 지원되는 스트리밍 형식(MPEG DASH, HLS, 부드러운 스트리밍, HDS)으로 다시 패키지하지 않고도 이런 스트리밍 형식으로 배달할 수 있게 하는 동적 패키징을 제공합니다. 

동적 패키징을 이용하려면 다음을 수행해야 합니다.

- mezzanine (원본) 파일을 적응 비트 전송률 MP4 파일 또는 적응 비트 전송률 부드러운 스트리밍 파일 집합으로 인코딩하거나 트랜스코딩합니다(인코딩 단계는 이 자습서의 뒷부분에서 설명).  
- 콘텐츠를 배달하는 출발점이 될 **스트리밍 끝점**에 하나 이상의 스트리밍 단위를 구성합니다.

동적 패키징에서는 단일 저장소 형식으로 파일을 저장하고 비용을 지불하기만 하면 됩니다. 그러면 미디어 서비스가 클라이언트의 요청에 따라 적절한 응답을 빌드 및 제공합니다. 

스트리밍 예약 단위의 수를 변경하려면 다음을 수행합니다.

1. [관리 포털](https://manage.windowsazure.com/)에서 **미디어 서비스**를 클릭합니다. 그런 다음 미디어 서비스의 이름을 클릭합니다.

2. 스트리밍 끝점 페이지를 선택합니다. 그리고 수정할 스트리밍 끝점을 클릭합니다.

3. 스트리밍 단위 수를 지정하려면 크기 조정 탭을 선택하고 **예약된 용량** 슬라이더를 움직입니다.

	![크기 조정 페이지](./media/media-services-dotnet-get-started/media-services-origin-scale.png)

4. 저장 단추를 눌러 변경 내용을 저장합니다.

	새 단위를 할당하는 작업은 완료하는 데 20분 정도 걸립니다. 

	 
	>[AZURE.NOTE] 현재, 스트리밍 단위의 양수 값을 0으로 변경하면 최대 1시간 동안 스트리밍을 사용하지 않을 수 있습니다.
	>
	> 24시간 동안 가장 많은 단위 수가 비용 계산에 사용됩니다. 가격 정보에 대한 자세한 내용은 [미디어 서비스 가격 정보](http://go.microsoft.com/fwlink/?LinkId=275107)를 참조하세요.



## Visual Studio 프로젝트 만들기 및 구성

1. Visual Studio 2013, Visual Studio 2012 또는 Visual Studio 2010 SP1에서 새 C# 콘솔 응용 프로그램을 만듭니다. **이름**, **위치** 및 **솔루션 이름**을 입력하고 확인을 클릭합니다. 

2. [windowsazure.mediaservices.extensions](https://www.nuget.org/packages/windowsazure.mediaservices.extensions) Nuget 패키지를 사용하여 **Azure 미디어 서비스 .NET SDK 확장**을 설치합니다.  미디어 서비스 .NET SDK Extensions는 코드를 단순화하고 미디어 서비스를 사용하여 더욱 쉽게 개발할 수 있도록 지원하는 일련의 확장 메서드 및 도우미 함수입니다. 이 패키지를 설치하면 **미디어 서비스 .NET SDK**도 설치되고 다른 모든 필수 종속성이 추가됩니다.
 
3. System.Configuration 어셈블리에 참조를 추가합니다. 이 어셈블리는 구성 파일 (예: App.config)에 액세스하는 데 사용되는 System.Configuration.ConfigurationManager 클래스를 포함합니다. 

4. App.config 파일을 열고(기본적으로 추가되지 않은 경우 프로젝트에 파일 추가) *appSettings* 섹션을 파일에 추가합니다. Azure 미디어 서비스 계정 이름 및 계정 키의 값을 다음 예제와 같이 설정합니다. 계정 이름 및 키 정보를 얻으려면 Azure 관리 포털을 열고 미디어 서비스 계정을 선택한 후 **키 관리** 단추를 클릭합니다.


	<pre><code>
	&lt;configuration&gt;
        &lt;appSettings&gt;
    	&lt;add key="MediaServicesAccountName" value="Media-Services-Account-Name" /&gt;
        	&lt;add key="MediaServicesAccountKey" value="Media-Services-Account-Key" /&gt;
  	    &lt;/appSettings&gt;
	&lt;/configuration&gt;
	</code></pre>


5. 다음 코드를 사용하여 Program.cs 파일의 앞부분에 있는 기존 using 문을 덮어씁니다.

		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Text;
		using System.Threading.Tasks;
		using System.Configuration;
		using System.Threading;
		using System.IO;
		using Microsoft.WindowsAzure.MediaServices.Client;
		using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;

6. 프로젝트 디렉터리 아래에 새 폴더를 만들고 인코딩하고 스트리밍 또는 점진적으로 다운로드하려는 .mp4 또는 .wmv 파일을 복사합니다. 이 예제에서는 "C:\VideoFiles" 경로가 사용됩니다. 

## 미디어 서비스 계정에 연결

미디어 서비스를 .NET과 함께 사용하는 경우 미디어 서비스 계정에 연결하는 작업이나 자산, 자산 파일, 작업, 액세스 정책, 로케이터 등의 개체를 만들고 업데이트하고 액세스하고 삭제하는 작업을 포함한 대부분의 미디어 서비스 프로그래밍 작업에 **CloudMediaContext** 클래스를 사용해야 합니다. 
 
기본 Program 클래스를 다음 코드로 덮어씁니다. 이 코드는 App.config 파일에서 연결 값을 읽는 방법 및 미디어 서비스에 연결하기 위해 CloudMediaContext 개체를 만드는 방법을 보여 줍니다. 미디어 서비스에 연결하는 방법에 대한 자세한 내용은 [Media Services SDK for .NET을 사용하여 미디어 서비스에 연결](http://msdn.microsoft.com/library/azure/jj129571.aspx)을 참조하세요.

**Main** 함수는 이 섹션에서 자세히 정의되는 메서드를 호출합니다.
	
    class Program
    {
        // Read values from the App.config file.
        private static readonly string _mediaServicesAccountName =
            ConfigurationManager.AppSettings["MediaServicesAccountName"];
        private static readonly string _mediaServicesAccountKey =
            ConfigurationManager.AppSettings["MediaServicesAccountKey"];

        // Field for service context.
        private static CloudMediaContext _context = null;
        private static MediaServicesCredentials _cachedCredentials = null;

        static void Main(string[] args)
        {
            try
            {
                // 정적 클래스 변수에서 미디어 서비스 자격 증명을 만들고 캐시합니다.
                _cachedCredentials = new MediaServicesCredentials(
                                _mediaServicesAccountName,
                                _mediaServicesAccountKey);
                // 캐시된 자격 증명을 만드는 데 사용 CloudMediaContext.
                _context = new CloudMediaContext(_cachedCredentials);

                // 이 섹션에 정의된 메서드에 대한 호출을 추가합니다.

                IAsset inputAsset =
                    UploadFile(@"C:\VideoFiles\BigBuckBunny.mp4", AssetCreationOptions.None);

                IAsset encodedAsset =
                    EncodeToAdaptiveBitrateMP4s(inputAsset, AssetCreationOptions.None);

                ConfigureClearAssetDeliveryPolicy(encodedAsset);

                PublishAssetGetURLs(encodedAsset);
            }
            catch (Exception exception)
            {
                // 미디어 서비스 응답의 XML 오류 메시지를 구문 분석하고 
                // 해당 콘텐츠로 새 예외를 만듭니다.
                exception = MediaServicesExceptionParser.Parse(exception);

                Console.Error.WriteLine(exception.Message);
            }
            finally
            {
                Console.ReadLine();
            }
        }

## 새 자산 만들기 및 비디오 파일 업로드

미디어 서비스에서 자산에 디지털 파일을 업로드(수집)합니다. **자산** 엔터티에는 비디오, 오디오, 이미지, 미리 보기 컬렉션, 텍스트 트랙 및 선택 캡션 파일(및 이 파일에 대한 메타데이터)이 포함될 수 있습니다.  파일이 업로드되면 이후 처리 및 스트리밍을 위해 콘텐츠가 클라우드에 안전하게 저장됩니다. 자산에 포함된 파일을 **자산 파일**이라고 합니다.

아래에서 정의된 **UploadFile** 메서드는 **CreateFromFile**(.NET SDK Extensions에 정의됨)을 호출합니다. **CreateFromFile**은 지정된 원본 파일이 업로드되는 새 자산을 만듭니다. 

**CreateFromFile** 메서드는 다음 자산 만들기 옵션 중 하나를 지정할 수 있는 **AssetCreationOptions**를 사용합니다.
 
- **없음** - 암호화가 사용되지 않습니다. 기본값입니다. 이 옵션을 사용하면 콘텐츠가 전송 중인 상태이거나 저장소에 저장된 상태일 때 보호되지 않습니다.
MP4를 배달하려는 경우 이 옵션을 사용하세요. 
- **StorageEncrypted** - 암호화 되어 있지 않은 콘텐츠를 AES-256 비트 암호화를 사용하여 로컬에서 암호화한 다음 암호화되어 저장되는 Azure 저장소에 업로드합니다. 저장소 암호화로 보호된 자산은 자동으로 암호 해제되어 인코딩되기 전에 암호화된 파일 시스템에 배치됩니다. 그리고 필요에 따라 새 출력 자산으로 다시 업로드되기 전에 다시 암호화됩니다. 저장소 암호화를 사용하는 기본적인 사례는 디스크에 저장된 상태일 때 강력한 암호화로 고품질의 입력 미디어 파일을 보호하려는 경우입니다.
- **CommonEncryption** - 일반적인 암호화 또는 PlayReady DRM(예: PlayReady DRM으로 보호되는 부드러운 스트리밍)으로 이미 보호된 콘텐츠를 업로드하는 경우 이 옵션을 사용합니다.
- **EnvelopeEncrypted** - AES로 암호화된 HLS를 업로드하는 경우 이 옵션을 사용합니다. 파일을 Transform Manager로 인코딩 및 암호화해야 합니다.

**CreateFromFile** 메서드는 또한 파일의 업로드 진행 상태를 보고하기 위해 콜백을 지정할 수 있습니다.

다음 예제에서는 자산 옵션으로 **없음**을 지정합니다.

Program 클래스에 다음 메서드를 추가합니다.

	static public IAsset UploadFile(string fileName, AssetCreationOptions options)
	{
	    IAsset inputAsset = _context.Assets.CreateFromFile(
	        fileName,
	        options,
	        (af, p) =>
	        {
	            Console.WriteLine("Uploading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
	        });
	
	    Console.WriteLine("Asset {0} created.", inputAsset.Id);
	
	    return inputAsset;
	}


## 원본 파일을 적응 비트 전송률 MP4 파일 집합으로 인코딩

미디어 서비스에 자산을 삽입하고 나면 미디어를 클라이언트에 배달하기 전에 인코딩, 트랜스믹싱, 워터마크 지정 등을 수행할 수 있습니다. 이러한 활동은 높은 성능과 가용성을 보장하기 위해 여러 백그라운드 역할 인스턴스에 대해 예약 및 실행합니다. 이러한 활동을 작업이라고 하며, 각 작업은 자산 파일에서 실제 작업을 수행하는 원자성 작업으로 구성됩니다. 

앞에서 언급한 대로, Azure 미디어 서비스 작업 시 가장 일반적인 시나리오 중 하나는 적응 비트 전송률 스트리밍을 클라이언트에 제공하는 것입니다. 미디어 서비스는 적응 비트 전송률 MP4 파일 집합을 HLS(HTTP 라이브 스트리밍), 부드러운 스트리밍, MPEG DASH 및 HDS(Adobe PrimeTime/Access 정식 사용자만 해당) 형식 중 하나로 동적으로 패키징할 수 있습니다. 

동적 패키징을 이용하려면 다음을 수행해야 합니다.

- mezzanine (원본) 파일을 적응 비트 전송률 MP4 파일 또는 적응 비트 전송률 부드러운 스트리밍 파일 집합으로 인코딩하거나 트랜스코딩합니다.  
- 콘텐츠를 배달하는 출발점이 될 스트리밍 끝점에 하나 이상의 스트리밍 단위를 구성합니다. 

다음 코드는 인코딩 작업을 제출하는 방법을 보여 줍니다. 이 작업에는 **Azure 미디어 인코더**를 사용하여 mezzanine 파일을 적응 비트 전송률 MP4 집합으로 트랜스코딩하도록 지정하는 태스크 하나가 포함됩니다. 이 코드는 작업을 제출하고 완료될 때까지 기다립니다. 

작업이 완료되면 자산을 스트리밍하거나 트랜스코딩 결과로 생성된 MP4 파일을 점진적으로 다운로드할 수 있습니다.
MP4 파일을 점진적으로 다운로드하려면 하나 이상의 스트리밍 단위가 필요하지 않습니다. 


Program 클래스에 다음 메서드를 추가합니다.

	static public IAsset EncodeToAdaptiveBitrateMP4s(IAsset asset, AssetCreationOptions options)
	{
		// 지정된 자산을 트랜스코딩하는 단일 태스크가 있는 작업을 준비
        // into a multi-bitrate asset.

	    IJob job = _context.Jobs.CreateWithSingleTask(
	        MediaProcessorNames.AzureMediaEncoder,
	        MediaEncoderTaskPresetStrings.H264AdaptiveBitrateMP4Set720p,
	        asset,
	        "Adaptive Bitrate MP4",
	        options);
	
		Console.WriteLine("Submitting transcoding job...");
	

	    // 작업을 제출하고 완료될 때까지 대기합니다.
	    job.Submit();
	
	    job = job.StartExecutionProgressTask(
	        j =>
	        {
	            Console.WriteLine("Job state: {0}", j.State);
	            Console.WriteLine("Job progress: {0:0.##}%", j.GetOverallProgress());
	        },
	        CancellationToken.None).Result;
	
	    Console.WriteLine("Transcoding job finished.");
	
	    IAsset outputAsset = job.OutputMediaAssets[0];
	
	    return outputAsset;
	}

## 인코딩된 자산에 대한 배달 정책 구성

미디어 서비스 콘텐츠 배달 워크플로의 단계 중 하나는 자산 배달 정책 구성입니다. 자산 배달 정책 구성에 포함되는 사항으로는 자산 배달에 사용할 수 있는 프로토콜(예: MPEG DASH, HLS, HDS, 부드러운 스트리밍 또는 모두), 동적 자산 암호화를 사용할지 여부 및 방법(봉투(Envelope) 또는 일반 암호화) 등이 있습니다. 

다음 **ConfigureClearAssetDeliveryPolicy** 메서드는 동적 암호화를 적용하지 않고 스트림을  MPEG DASH, HLS 및 부드러운 스트리밍 프로토콜 
  
Program 클래스에 다음 메서드를 추가합니다.

    static public void ConfigureClearAssetDeliveryPolicy(IAsset asset)
    {
        IAssetDeliveryPolicy policy =
            _context.AssetDeliveryPolicies.Create("Clear Policy",
            AssetDeliveryPolicyType.NoDynamicEncryption, 
            AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.Dash, null);

        asset.DeliveryPolicies.Add(policy);
    }


## 자산 게시, 스트리밍 기능 사용 및 URL 점진적으로 다운로드

자산을 스트리밍하거나 다운로드하려면 먼저 로케이터를 만들어 자산을 "게시"해야 합니다. 로케이터는 자산에 포함된 파일에 대한 액세스를 제공합니다. 미디어 서비스는 두 가지 유형의 로케이터를 지원합니다. 하나는 OnDemandOrigin 로케이터로서 미디어를 스트리밍하는 데 사용되고(예: MPEG DASH, HLS 또는 부드러운 스트리밍) 다른 하나는 SAS(공유 액세스 서명) 로케이터로서 미디어 파일을 다운로드하는 데 사용됩니다.

로케이터를 만든 후에 파일을 스트리밍하거나 다운로드하는 데 사용되는 URL을 작성할 수 있습니다. 


부드러운 스트리밍에 대한 스트리밍 URL의 형식은 다음과 같습니다.

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

HLS에 대한 스트리밍 URL의 형식은 다음과 같습니다.

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

MPEG DASH에 대한 스트리밍 URL의 형식은 다음과 같습니다.

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

파일을 다운로드하는 데 사용되는 SAS URL의 형식은 다음과 같습니다.

	{blob container name}/{asset name}/{file name}/{SAS signature}

Media Services .NET SDK Extensions는 게시된 자산에 대한 서식 지정된 URL을 반환하는 편리한 도우미 메서드를 제공합니다.
 
다음 코드는 .NET SDK Extensions를 사용하여 로케이터를 만들고, 스트리밍 또는 점진적 다운로드를 통해 URL을 가져옵니다. 코드는 또한 파일을 로컬 폴더에 다운로드하는 방법을 보여 줍니다.

Program 클래스에 다음 메서드를 추가합니다.

    static public void PublishAssetGetURLs(IAsset asset)
    {
        // Publish the output asset by creating an Origin locator for adaptive streaming, 
        // and a SAS locator for progressive download.

        _context.Locators.Create(
            LocatorType.OnDemandOrigin,
            asset,
            AccessPermissions.Read,
            TimeSpan.FromDays(30));

        _context.Locators.Create(
            LocatorType.Sas,
            asset,
            AccessPermissions.Read,
            TimeSpan.FromDays(30));


        IEnumerable<IAssetFile> mp4AssetFiles = asset
                .AssetFiles
                .ToList()
                .Where(af => af.Name.EndsWith(".mp4", StringComparison.OrdinalIgnoreCase));

        // Get the Smooth Streaming, HLS and MPEG-DASH URLs for adaptive streaming, 
        // and the Progressive Download URL.
        Uri smoothStreamingUri = asset.GetSmoothStreamingUri();
        Uri hlsUri = asset.GetHlsUri();
        Uri mpegDashUri = asset.GetMpegDashUri();

        // 인코딩 결과로 생성된 각 MP4 파일에 대한 점진적 다운로드 URL을
		// 가져옵니다.
		List<Uri> mp4ProgressiveDownloadUris = mp4AssetFiles.Select(af => af.GetSasUri()).ToList();


        // 스트리밍 URL을 표시합니다.
        Console.WriteLine("Use the following URLs for adaptive streaming: ");
        Console.WriteLine(smoothStreamingUri);
        Console.WriteLine(hlsUri);
        Console.WriteLine(mpegDashUri);
        Console.WriteLine();

		// 점진적 다운로드 URL을 표시합니다.
        Console.WriteLine("Use the following URLs for progressive download.");
        mp4ProgressiveDownloadUris.ForEach(uri => Console.WriteLine(uri + "\n"));
        Console.WriteLine();

        // 로컬 폴더에 출력 자산을 다운로드합니다.
        string outputFolder = "job-output";
        if (!Directory.Exists(outputFolder))
        {
            Directory.CreateDirectory(outputFolder);
        }

        Console.WriteLine();
        Console.WriteLine("Downloading output asset files to a local folder...");
        asset.DownloadToFolder(
            outputFolder,
            (af, p) =>
            {
                Console.WriteLine("Downloading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
            });

        Console.WriteLine("Output asset files available at '{0}'.", Path.GetFullPath(outputFolder));
    }

## 콘텐츠 재생  

이전 섹션에 정의된 프로그램을 실행하고 나면 다음과 유사한 URL이 콘솔 창에 표시됩니다.

적응 스트리밍 URL:

부드러운 스트리밍

	http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest

HLS

	http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=m3u8-aapl)

MPEG DASH

	http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=mpd-time-csf)

점진적 다운로드 URL(오디오 및 비디오)    
	
	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
	
	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
	
	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
	
	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
	
	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1500kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
	
	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1000kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
	
	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
	
	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_56kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z


부드러운 스트리밍을 테스트하려면 [http://amsplayer.azurewebsites.net/](http://amsplayer.azurewebsites.net/) 또는 [http://smf.cloudapp.net/healthmonitor](http://smf.cloudapp.net/healthmonitor)를 사용합니다.

MPEG DASH를 테스트하려면 [http://dashif.org](http://dashif.org/reference/players/javascript/)를 사용합니다.

HLS를 테스트하려면 iOS 또는 Safari 장치나 [3ivx-hls-player](http://apps.microsoft.com/windows/app/3ivx-hls-player/f79ce7d0-2993-4658-bc4e-83dc182a0614)를 사용합니다. 


점진적 다운로드를 테스트하려면 IE, Chrome, Safari 등의 브라우저에 URL을 붙여 넣습니다.


### 추가 리소스
- <a href="http://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-101-Get-your-video-online-now-">Azure 미디어 서비스 101 - 지금 온라인으로 비디오 받기!</a>
- <a href="http://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-102-Dynamic-Packaging-and-Mobile-Devices">Azure 미디어 서비스 102 - 동적 패키징 및 모바일 장치</a>


<!-- Anchors. -->


<!-- URLs. -->
  [웹 플랫폼 설치 관리자]: http://go.microsoft.com/fwlink/?linkid=255386
  [관리 포털]: http://manage.windowsazure.com/



<!--HONumber=52--> 