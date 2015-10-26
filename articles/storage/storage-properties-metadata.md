
<properties 
  pageTitle="Azure 저장소에서 개체에 대한 속성 및 메타데이터를 설정 및 검색 | Microsoft Azure" 
  description="Azure 저장소의 개체에 사용자 지정 메타데이터를 저장하고 시스템 속성을 설정 및 검색합니다." 
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
  ms.date="09/03/2015" 
  ms.author="tamram"/>


# 속성 및 메타 데이터 설정 및 검색 #

## 개요

Azure 저장소의 개체는 시스템 속성 및 사용자 정의 메타데이터와 포함된 데이터를 지원합니다.

*   **시스템 속성.** 각 저장소 리소스에 시스템 속성이 있습니다. 그 중 일부를 읽거나 설정할 수 있지만 나머지는 읽기 전용입니다. 일부 시스템 속성은 내부적으로 특정 표준 HTTP 헤더에 해당합니다. Azure 저장소 클라이언트 라이브러리에서 유지 관리합니다.  

*   **사용자 정의 메타 데이터.** 사용자 정의 메타 데이터는 이름-값 쌍의 형태로 제공된 리소스에서 지정 하는 메타 데이터입니다. 메타데이터를 사용하면 저장소 리소스와 함께 추가 값을 저장할 수 있습니다. 이 값은 고유한 목적으로만 사용되며 리소스의 동작 방식에 영향을 주지 않습니다.

## 속성 설정 및 검색

저장소 리소스에 대한 속성 및 메타데이터 값 검색은 두 단계로 이루어집니다. 이러한 값을 읽으려면 먼저 **FetchAttributes** 또는 **FetchAttributesAsync** 메서드를 호출하여 명시적으로 가져와야 합니다.

> [AZURE.IMPORTANT]**FetchAttributes** 메서드 중 하나를 호출하지 않으면 저장소 리소스에 대한 속성 및 메타데이터 값이 채워지지 않습니다.

Blob에서 속성을 설정하려면 속성 값을 지정한 다음 **SetProperties** 또는 **SetPropertiesAsync** 메서드를 호출합니다.

다음 코드 예제는 컨테이너를 만들고 콘솔 창에 속성 값을 씁니다.

    //Parse the connection string for the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));
	
	//Create the service client object for credentialed access to the Blob service.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve a reference to a container. 
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Create the container if it does not already exist.
    container.CreateIfNotExists();

    // Fetch container properties and write out their values.
    container.FetchAttributes();
    Console.WriteLine("Properties for container {0}", container.StorageUri.PrimaryUri.ToString());
    Console.WriteLine("LastModifiedUTC: {0}", container.Properties.LastModified.ToString());
    Console.WriteLine("ETag: {0}", container.Properties.ETag);
    Console.WriteLine();

## 메타 데이터 설정 및 검색

Blob 또는 컨테이너 리소스에 하나 이상의 이름-값 쌍으로 메타 데이터를 지정할 수 있습니다. 메타 데이터를 설정하려면 이름-값 쌍을 리소스의 **메타 데이터** 컬렉션에 추가한 다음, **SetMetadata** 메서드를 호출하여 값을 서비스에 저장합니다.

> [AZURE.NOTE]\: 메타 데이터의 이름은 C# 식별자에 대한 명명 규칙을 준수해야 합니다.
 
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
 

<!---HONumber=Oct15_HO3-->