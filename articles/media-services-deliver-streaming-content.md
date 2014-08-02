<properties linkid="develop-media-services-how-to-guides-deliver-streaming-content" urlDisplayName="Deliver Streaming Content from Media Services" pageTitle="How to Deliver Streaming Content from Media Services a€“ Azure" metaKeywords="" description="Learn how to deliver streaming content from Media Services using a direct URL. Code samples are written in C# and use the Media Services SDK for .NET." metaCanonical="" disqusComments="1" umbracoNaviHide="0" title="How to: Deliver streaming content" authors="" />

방법: 스트리밍 콘텐츠 제공
==========================

이 문서는 Azure 미디어 서비스 프로그래밍을 소개하는 시리즈 중 하나입니다. 이전 항목은 [방법: 다운로드를 통해 자산 제공](http://go.microsoft.com/fwlink/?LinkID=301734&clcid=0x409)(영문)이었습니다.

미디어 서비스에서 미디어 콘텐츠를 다운로드하는 것뿐만 아니라 적응 비트 전송률 스트리밍을 사용하여 콘텐츠를 제공할 수 있습니다. 예를 들어 미디어 서비스 원본 서버의 스트리밍 콘텐츠에 대해 로케이터라고 하는 직접 URL을 만들 수 있습니다. Microsoft Silverlight 같은 클라이언트 응용 프로그램은 로케이터에서 바로 스트리밍 콘텐츠를 재생할 수 있습니다.

다음 예제에서는 작업으로 생성된 출력 자산에 대해 원본 로케이터를 만드는 방법을 보여 줍니다. 예제에서는 부드러운 스트리밍 파일이 들어 있는 자산에 대한 참조를 이미 얻었으며 코드에 **assetToStream**이라는 이름의 변수가 참조되었다고 가정합니다.

스트리밍 콘텐츠에 대해 원본 로케이터를 만드는 방법

1.  자산에서 스트리밍 매니페스트 파일(.ism)에 대한 자산을 가져옵니다.
2.  액세스 정책을 정의합니다.
3.  CreateLocator 메서드라고 하는 원본 로케이터를 만듭니다.
4.  매니페스트 파일에 대한 URL을 작성합니다.

다음 코드는 단계를 실행하는 방법을 보여 줍니다.

``` {}
private static ILocator GetStreamingOriginLocator( string targetAssetID)
{
    // Get a reference to the asset you want to stream.
    IAsset assetToStream = GetAsset(targetAssetID);

    // Get a reference to the streaming manifest file from the  
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
    policy = _context.AccessPolicies.Create("Streaming policy",
        TimeSpan.FromDays(30),
        AccessPermissions.Read);

    // Create an OnDemandOrigin locator to the asset. 
    originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, assetToStream,
        policy,
        DateTime.UtcNow.AddMinutes(-5));
            
    // Display some useful values based on the locator.
    Console.WriteLine("Streaming asset base path on origin: ");
    Console.WriteLine(originLocator.Path);
    Console.WriteLine();
    
    // Create a full URL to the manifest file. Use this for playback
    // in streaming media clients. 
    string urlForClientStreaming = originLocator.Path + manifestFile.Name + "/manifest";
    Console.WriteLine("URL to manifest for client streaming: ");
    Console.WriteLine(urlForClientStreaming);
    Console.WriteLine();
    
    // Display the ID of the origin locator, the access policy, and the asset.
    Console.WriteLine("Origin locator Id: " + originLocator.Id);
    Console.WriteLine("Access policy Id: " + policy.Id);
    Console.WriteLine("Streaming asset Id: " + assetToStream.Id);

    // Return the locator. 
    return originLocator;
}
```

자산 제공에 대한 자세한 내용은 다음을 참조하십시오.

-   [Media Services for .NET을 사용하여 자산 제공(영문)](http://msdn.microsoft.com/en-us/library/jj129575.aspx)
-   [Media Services REST API를 사용하여 자산 제공(영문)](http://msdn.microsoft.com/en-us/library/jj129578.aspx)

다음 단계
---------

지금까지 부드러운 스트리밍을 사용하여 Azure 저장소에서 다운로드하여 미디어를 제공하는 방법을 살펴봤습니다. 다음 항목인 [HLS 콘텐츠를 제공하는 방법](http://go.microsoft.com/fwlink/?LinkId=301817)(영문)에서는 Apple HLS(HTTP 라이브 스트리밍)를 사용하여 스트리밍 콘텐츠를 제공하는 방법을 살펴보겠습니다.

