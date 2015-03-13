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
	ms.date="02/15/2015" 
	ms.author="juliako"/>


# 방법: 저장소의 자산 관리

이 기사는 [필요 시 워크플로 관련 미디어 서비스 비디오](../media-services-video-on-demand-workflow) 및 [미디어 서비스 라이브 스트리밍 워크플로](../media-services-live-streaming-workflow) 시리즈의 일부입니다.  


미디어 자산을 만든 후에 서버에서 자산에 액세스하여 관리할 수 있습니다. 서버에서 미디어 서비스의 일부인 작업, 태스크, 액세스 정책, 로케이터 등의 다른 개체도 관리할 수 있습니다.

다음 예는 assetId를 사용하여 자산을 쿼리하는 방법을 보여 줍니다. 

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

서버에서 사용할 수 있는 모든 자산을 나열하려면 자산 컬렉션을 반복하고 각 자산에 대한 정보를 표시하는 다음 메서드를 사용할 수 있습니다.
	
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

다음 코드 조각은 미디어 서비스 계정에서 모든 자산을 삭제합니다. 자산이 프로그램에 연결된 경우, 먼저 해당 프로그램을 삭제해야 합니다.

	foreach (IAsset asset in _context.Assets)
	{
	    asset.Delete();
	}


<!--HONumber=45--> 
