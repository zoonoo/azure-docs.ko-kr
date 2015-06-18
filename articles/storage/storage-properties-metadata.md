
<properties 
pageTitle="Blob 저장소에 대한 속성 및 메타 데이터 설정 및 검색 | Microsoft Azure" 
description="Azure 저장소 컨테이너 및 Blob에 대한 속성 및 메타 데이터를 설정하고 검색하는 방법에 대해 알아봅니다." 
services="storage" 
documentationCenter="" 
authors="tamram" 
manager="adinah" 
editor=""/>

<tags 
ms.service="storage" 
ms.workload="storage" 
ms.tgt_pltfrm="na" 
ms.devlang="na" 
ms.topic="article" 
ms.date="04/21/2015" 
ms.author="tamram"/>


# 속성 및 메타 데이터 설정 및 검색 #

## 개요

컨테이너 및 Blob는 포함된 데이터 외에도 연결된 데이터의 두 형태를 지원합니다.

*   **시스템 속성.** 시스템 속성은 각 컨테이너에 또는 Blob 리소스에 존재합니다. 그 중 일부를 읽거나 설정할 수 있지만 나머지는 읽기 전용입니다. 일부 시스템 속성은 내부적으로 Azure Managed Library에서 유지 관리 하는 특정 표준 HTTP 헤더에 해당합니다.  

*   **사용자 정의 메타 데이터.** 사용자 정의 메타 데이터는 이름-값 쌍의 형태로 제공된 리소스에서 지정 하는 메타 데이터입니다. 메타 데이터를 사용하면 추가 값을 컨테이너 또는 Blob로 저장할 수 있습니다. 이 값은 고유한 목적으로만 사용되어 컨테이너 또는 Blob의 동작 방식에 영향을 주지 않습니다.

> [AZURE.IMPORTANT]리소스에 대한 속성 및 메타 데이터 값 검색은 두 단계로 이루어집니다. 이 값을 읽으려면 [CloudBlobContainer](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblobcontainer.aspx), [CloudBlockBlob](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblockblob.aspx), 또는 [CloudPageBlob](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudpageblob.aspx) 개체에 명시적으로 페치해야 합니다. 속성 및 메타 데이터를 동기적으로 가져오려면 컨테이너 또는 Blob에서 **FetchAttributes** 메서드를 호출하고, 비동기적으로 가져오려면 **FetchAttributesAsync**를 호출합니다.

## 속성 설정 및 검색

컨테이너는 읽기 전용 속성만 있지만 Blob에는 읽기 전용 및 읽기 / 쓰기 속성이 모두 있습니다. Blob에서 속성을 설정하려면 속성 값을 지정한 다음 [SetProperties](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblockblob.setproperties.aspx) 메서드 또는 [SetProperties](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudpageblob.setproperties.aspx) 메서드를 호출합니다.

컨테이너 또는 Blob에서 속성을 읽으려면 **FetchAttributes** 메서드를 호출한 다음 속성 값을 검색합니다.

다음 코드 예제는 컨테이너 및 Blob를 만들고 콘솔 창에 속성 값을 씁니다. 이 코드가 작동 하려면 에뮬레이트된 저장소 서비스를 실행해야 하므로 이 예제에서는 저장소 에뮬레이터를 사용합니다.

	// Use the storage emulator account.
	CloudStorageAccount storageAccount = CloudStorageAccount.DevelopmentStorageAccount;

	// As an alternative, you can create the credentials from the account name and key.
	// string accountName = "myaccount";
	// string accountKey = "SzlFqgzqhfkj594cFoveYqCuvo8v9EESAnOLcTBeBIo31p16rJJRZx/5vU/oY3ZsK/VdFNaVpm6G8YSD2K48Nw==";
	// StorageCredentials credentials = new StorageCredentials(accountName, accountKey);
	// CloudStorageAccount storageAccount = new CloudStorageAccount(credentials, true);

	CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

	// Retrieve a reference to a container. 
	CloudBlobContainer container = blobClient.GetContainerReference(<span style="color:#A31515;">"mycontainer");

	// Create the container if it does not already exist.
	container.CreateIfNotExists();

	// Fetch container properties and write out their values.
	container.FetchAttributes();
	Console.WriteLine(<span style="color:#A31515;">"Properties for container " + container.Uri + <span style="color:#A31515;">":");
	Console.WriteLine(<span style="color:#A31515;">"LastModifiedUTC: " + container.Properties.LastModified);
	Console.WriteLine(<span style="color:#A31515;">"ETag: " + container.Properties.ETag);
	Console.WriteLine();

	// Create a blob.
	CloudBlockBlob blob = container.GetBlockBlobReference(<span style="color:#A31515;">"myblob.txt");

	// Create or overwrite the "myblob.txt" blob with contents from a local file.
	<span style="color:Blue;">using (<span style="color:Blue;">var fileStream = System.IO.File.OpenRead(<span style="color:#A31515;">@"c:\test\myblob.txt"))
	{
	   blob.UploadFromStream(fileStream);
	} 

	// Fetch container properties and write out their values.
	container.FetchAttributes();
	Console.WriteLine(<span style="color:#A31515;">"Properties for container " + container.Uri + <span style="color:#A31515;">":");
	Console.WriteLine(<span style="color:#A31515;">"LastModifiedUTC: " + container.Properties.LastModified);
	Console.WriteLine(<span style="color:#A31515;">"ETag: " + container.Properties.ETag);
	Console.WriteLine();

	// Create a blob.
	Uri blobUri =<span style="color:Blue;">new UriBuilder(containerUri.AbsoluteUri + <span style="color:#A31515;">"/ablob.txt").Uri;
	CloudPageBlob blob = <span style="color:Blue;">new CloudPageBlob(blobUri, credentials);
	blob.Create(1024);
				

	// Set the CacheControl property.
	blob.Properties.CacheControl = <span style="color:#A31515;">"public, max-age=31536000";
	blob.SetProperties();

	// Fetch blob attributes.
	blob.FetchAttributes();

	Console.WriteLine(<span style="color:#A31515;">"Read-only properties for blob " + blob.Uri + <span style="color:#A31515;">":");
	Console.WriteLine(<span style="color:#A31515;">"BlobType: " + blob.Properties.BlobType);
	Console.WriteLine(<span style="color:#A31515;">"ETag: " + blob.Properties.ETag);
	Console.WriteLine(<span style="color:#A31515;">"LastModifiedUtc: " + blob.Properties.LastModified);
	Console.WriteLine(<span style="color:#A31515;">"Length: " + blob.Properties.Length);
	Console.WriteLine();

	Console.WriteLine(<span style="color:#A31515;">"Read-write properties for blob " + blob.Uri + <span style="color:#A31515;">":");
	Console.WriteLine(<span style="color:#A31515;">"CacheControl: " +
	   (blob.Properties.CacheControl == <span style="color:Blue;">null ? <span style="color:#A31515;">"Not set" : blob.Properties.CacheControl));
	Console.WriteLine(<span style="color:#A31515;">"ContentEncoding: " +
	   (blob.Properties.ContentEncoding == <span style="color:Blue;">null ? <span style="color:#A31515;">"Not set" : blob.Properties.ContentEncoding));
	Console.WriteLine(<span style="color:#A31515;">"ContentLanguage: " +
	   (blob.Properties.ContentLanguage == <span style="color:Blue;">null ? <span style="color:#A31515;">"Not set" : blob.Properties.ContentLanguage));
	Console.WriteLine(<span style="color:#A31515;">"ContentMD5: " +
	   (blob.Properties.ContentMD5 == <span style="color:Blue;">null ? <span style="color:#A31515;">"Not set" : blob.Properties.ContentMD5));
	Console.WriteLine(<span style="color:#A31515;">"ContentType: " +
	   (blob.Properties.ContentType == <span style="color:Blue;">null ? <span style="color:#A31515;">"Not set" : blob.Properties.ContentType));

	// Clean up.
	blob.DeleteIfExists();
	container.Delete();
## 메타 데이터 설정 및 검색

Blob 또는 컨테이너 리소스에 하나 이상의 이름-값 쌍으로 메타 데이터를 지정할 수 있습니다. 메타 데이터를 설정하려면 이름-값 쌍을 리소스의 **메타 데이터** 컬렉션에 추가한 다음, **SetMetadata** 메서드를 호출하여 값을 서비스에 저장합니다.

> [AZURE.NOTE]: 메타 데이터의 이름은 C# 식별자에 대한 명명 규칙을 준수해야 합니다.
 
메타 데이터를 검색하려면 Blob 또는 컨테이너에서 **FetchAttributes** 메서드를 호출하여 **메타 데이터** 컬렉션을 채운 다음, 값을 읽습니다.

다음 코드 예제에서는 새 컨테이너를 만들고, 이 컨테이너에 대한 메타 데이터를 설정한 후 메타 데이터 값을 다시 읽습니다.

         
	// Account name and key.  Modify for your account.
	<span style="color:Blue;">string accountName = <span style="color:#A31515;">"myaccount";
	<span style="color:Blue;">string accountKey = <span style="color:#A31515;">"SzlFqgzqhfkj594cFoveYqCuvo8v9EESAnOLcTBeBIo31p16rJJRZx/5vU/oY3ZsK/VdFNaVpm6G8YSD2K48Nw==";

	// Get a reference to the storage account and client with authentication credentials.
	StorageCredentials credentials = <span style="color:Blue;">new StorageCredentials(accountName, accountKey);
	CloudStorageAccount storageAccount = <span style="color:Blue;">new CloudStorageAccount(credentials, <span style="color:Blue;">true);
	CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

	// Retrieve a reference to a container. 
	CloudBlobContainer container = blobClient.GetContainerReference(<span style="color:#A31515;">"mycontainer");

	// Create the container if it does not already exist.
	container.CreateIfNotExists();

	// Set metadata for the container.
	container.Metadata[<span style="color:#A31515;">"category"] = <span style="color:#A31515;">"images";
	container.Metadata[<span style="color:#A31515;">"owner"] = <span style="color:#A31515;">"azure";
	container.SetMetadata();

	// Get container metadata.
	container.FetchAttributes();
	<span style="color:Blue;">foreach (<span style="color:Blue;">string key <span style="color:Blue;">in container.Metadata.Keys)
	{
	   Console.WriteLine(<span style="color:#A31515;">"Metadata key: " + key);
	   Console.WriteLine(<span style="color:#A31515;">"Metadata value: " + container.Metadata[key]);
	}

	//Clean up.
	container.Delete();

## 참고 항목  

- [Azure Storage Client Library 참조](http://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx)
- [.NET용 Blob 저장소 시작](storage-dotnet-how-to-use-blobs.md)  

<!--HONumber=52-->
 