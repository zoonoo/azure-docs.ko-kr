<properties 
	pageTitle="미디어 서비스에서 자산을 관리하는 방법 - Azure" 
	description="미디어 서비스에서 자산을 관리하는 방법에 대해 알아봅니다. 또한 작업을 관리하고 정책과 로케이터에 액세스하는 방법에 대해서도 설명합니다. 코드 샘플은 C#으로 작성되었으며 Media Services SDK for .NET을 사용합니다." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/30/2014" 
	ms.author="juliako"/>




<h1>방법: 저장소의 자산 관리</h1>

이 문서는 Azure 미디어 서비스 프로그래밍을 소개하는 시리즈 중 하나입니다. 이전 항목은 [방법: 자산 보호](../media-services-protect-asset/)(영문)입니다.

미디어 자산을 만들어 미디어 서비스에 업로드한 후에는 서버에서 자산에 액세스하고 자산을 관리할 수 있습니다. 서버에서 미디어 서비스의 일부인 작업, 태스크, 액세스 정책, 로케이터 등의 다른 개체도 관리할 수 있습니다.

다음 예는 assetId를 사용하여 자산을 쿼리하는 방법을 보여 줍니다. 
<pre><code>
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
</code></pre> 

서버에서 사용할 수 있는 모든 자산을 나열하려면 자산 컬렉션을 반복하고 각 자산에 대한 정보를 표시하는 다음 메서드를 사용할 수 있습니다.
<pre><code> 
static void ListAssets()
{
    string waitMessage = "Building the list. This may take a few "
        + "seconds to a few minutes depending on how many assets "
        + "you have."
        + Environment.NewLine + Environment.NewLine
        + "Please wait..."
        + Environment.NewLine;
    Console.Write(waitMessage);

    // Create a Stringbuilder to store the list that we build. 
    StringBuilder builder = new StringBuilder();

    foreach (IAsset asset in _context.Assets)
    {
        // Display the collection of assets.
        builder.AppendLine("");
        builder.AppendLine("******ASSET******");
        builder.AppendLine("Asset ID: " + asset.Id);
        builder.AppendLine("Name: " + asset.Name);
        builder.AppendLine("==============");
        builder.AppendLine("******ASSET FILES******");

        // Display the files associated with each asset. 
        foreach (IAssetFile fileItem in asset.AssetFiles)
        {
            builder.AppendLine("Name: " + fileItem.Name);
            builder.AppendLine("Size: " + fileItem.ContentFileSize);
            builder.AppendLine("==============");
        }
    }

    // Display output in console.
    Console.Write(builder.ToString());
}
</code></pre>
다음 코드 조각은 미디어 서비스 계정에서 모든 자산을 삭제합니다.
<pre><code>
foreach (IAsset asset in _context.Assets)
{
    asset.Delete();
}
</code></pre>

자산 관리에 대한 자세한 내용은 다음을 참조하세요.
<ul>
<li><a href="http://msdn.microsoft.com/ko-kr/library/jj129589.aspx">Media Services SDK for .NET을 사용하여 자산 관리(영문)</a></li>
<li><a href="http://msdn.microsoft.com/ko-kr/library/jj129583.aspx">Media Services REST API를 사용하여 자산 관리(영문)</a></li></ul>


<h2>다음 단계</h2>
자산 관리 방법을 알아보았습니다. [다운로드를 통해 자산을 제공하는 방법](../media-services-deliver-asset-download/)(영문) 항목으로 이동하세요.

<!--HONumber=42-->
