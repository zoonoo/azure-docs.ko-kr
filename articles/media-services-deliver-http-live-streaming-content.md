<properties linkid="develop-media-services-how-to-guides-deliver-apple-live-streaming" urlDisplayName="Deliver Apple HTTP Live Streaming (HLS)" pageTitle="How to Deliver Apple HTTP Live Streaming (HLS) - Azure" metaKeywords="" description="Learn how to create a locator to Apple HTTP Live Stream (HLS) content on Media Services origin server. Code samples are written in C# and use the Media Services SDK for .NET." metaCanonical="" services="media-services" documentationCenter="" title="How to: Deliver Apple HLS streaming content" authors="migree" solutions="" manager="" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="migree"></tags>

# 방법: Apple HLS 스트리밍 콘텐츠 제공

이 문서는 Azure 미디어 서비스 프로그래밍을 소개하는 시리즈 중 하나입니다. 이전 항목은 [방법: 스트리밍 콘텐츠 제공][]입니다.

이 항목에서는 미디어 서비스 원본 서버의 Apple HLS(HTTP 라이브 스트리밍) 콘텐츠에 대한 로케이터를 만드는 방법을 보여 줍니다. 이 방법을 사용하여 Apple HLS 콘텐츠에 대한 URL을 빌드하고 재생을 위해 Apple iOS 장치에 제공할 수 있습니다. 로케이터 URL을 빌드하는 기본 방법은 동일합니다. 원본 서버의 Apple HLS 스트리밍 자산 경로에 대한 로케이터를 빌드한 후 스트리밍 콘텐츠에 대한 매니페스트에 연결하는 전체 URL을 빌드합니다.

다음 코드 예제에서는 HLS 스트리밍 자산에 대한 참조를 이미 얻었으며 코드에 **assetToStream**이라는 변수가 참조되었다고 가정합니다. 이 코드를 실행하여 자산에 원본 로케이터를 생성한 후 결과 URL을 사용하여 iPad 또는 iPhone과 같은 iOS 장치에서 스트리밍 콘텐츠를 재생할 수 있습니다.

Apple HLS 스트리밍 콘텐츠에 대한 로케이터를 빌드하려면

1.  자산의 매니페스트 파일에 대한 참조를 가져옵니다.
2.  액세스 정책을 정의합니다.
3.  CreateLocator를 호출하여 원본 로케이터를 만듭니다.
4.  매니페스트 파일에 대한 URL을 작성합니다.

다음 코드는 단계를 실행하는 방법을 보여 줍니다.

    static ILocator GetStreamingHLSOriginLocator( string targetAssetID)
    {
        // Get a reference to the asset you want to stream.
        IAsset assetToStream = GetAsset(targetAssetID);

        // Get a reference to the HLS streaming manifest file from the  
        // collection of files in the asset. 
        var theManifest =
                            from f in assetToStream.AssetFiles
                            where f.Name.EndsWith(".ism")
                            select f;

        // Cast the reference to a true IAssetFile type. 
        IAssetFile manifestFile = theManifest.First();
        IAccessPolicy policy = null;
        ILocator originLocator = null;

        // Create a 30-day readonly access policy. 
        policy = _context.AccessPolicies.Create("Streaming HLS Policy",
            TimeSpan.FromDays(30),
            AccessPermissions.Read);

        originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, assetToStream,
                    policy,
                    DateTime.UtcNow.AddMinutes(-5));

        // Create a URL to the HLS streaming manifest file. Use this for playback
        // in Apple iOS streaming clients.
        string urlForClientStreaming = originLocator.Path
            + manifestFile.Name + "/manifest(format=m3u8-aapl)";
        Console.WriteLine("URL to manifest for client streaming: ");
        Console.WriteLine(urlForClientStreaming);
        Console.WriteLine();

        // Return the locator. 
        return originLocator;
    }

자산 제공에 대한 자세한 내용은 다음을 참조하세요.

-   [Media Services for .NET을 사용하여 자산 제공(영문)][]
-   [Media Services REST API를 사용하여 자산 제공(영문)][]

</p>
## 다음 단계

이제 Azure 미디어 서비스 사용 항목을 마쳤습니다. 미디어 서비스 개발을 위해 컴퓨터를 설정하고 일반적인 프로그래밍 작업을 수행하는 방법을 설명했습니다. 미디어 서비스 프로그래밍에 대한 자세한 내용은 다음 리소스를 참조하세요.

-   [Azure 미디어 서비스 설명서][]
-   [Media Services SDK for .NET 시작][]
-   [Media Services SDK for .NET을 사용하여 응용 프로그램 빌드][]
-   [Azure Media Services REST API를 사용하여 응용 프로그램 빌드][]
-   [미디어 서비스 포럼][]
-   [미디어 서비스 계정을 모니터링하는 방법][]
-   [미디어 서비스에서 콘텐츠를 관리하는 방법][]

  [방법: 스트리밍 콘텐츠 제공]: http://go.microsoft.com/fwlink/?LinkID=301942&clcid=0x409
  [Media Services for .NET을 사용하여 자산 제공(영문)]: http://msdn.microsoft.com/en-us/library/jj129575.aspx
  [Media Services REST API를 사용하여 자산 제공(영문)]: http://msdn.microsoft.com/en-us/library/jj129578.aspx
  [Azure 미디어 서비스 설명서]: http://go.microsoft.com/fwlink/?linkid=245437
  [Media Services SDK for .NET 시작]: http://go.microsoft.com/fwlink/?linkid=252966
  [Media Services SDK for .NET을 사용하여 응용 프로그램 빌드]: http://go.microsoft.com/fwlink/?linkid=247821
  [Azure Media Services REST API를 사용하여 응용 프로그램 빌드]: http://go.microsoft.com/fwlink/?linkid=252967
  [미디어 서비스 포럼]: http://social.msdn.microsoft.com/Forums/en-US/MediaServices/threads
  [미디어 서비스 계정을 모니터링하는 방법]: http://www.windowsazure.com/en-us/manage/services/media-services/how-to-monitor-a-media-services-account/
  [미디어 서비스에서 콘텐츠를 관리하는 방법]: http://www.windowsazure.com/en-us/manage/services/media-services/how-to-manage-content-in-media-services/
