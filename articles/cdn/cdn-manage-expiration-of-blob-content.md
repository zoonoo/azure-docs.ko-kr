<properties
 pageTitle="Azure CDN(콘텐츠 배달 네트워크)에서 Blob 콘텐츠의 만료를 관리하는 방법 | Microsoft Azure"
 description="Azure CDN 캐싱의 Blob에 대한 TTL(Time-To-Live)을 제어하기 위한 옵션에 대해 알아봅니다."
 services="cdn"
 documentationCenter=".NET"
 authors="camsoper"
 manager="erikre"
 editor=""/>
<tags
 ms.service="cdn"
 ms.workload="media"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.date="07/28/2016"
 ms.author="casoper"/>


#Azure CDN(콘텐츠 배달 네트워크)에서 Blob 콘텐츠의 만료를 관리하는 방법  

Azure CDN 캐싱이 가장 도움이 되는 Blob은 TTL(Time-To-Live) 기간 중 자주 액세스되는 Blob입니다. Blob은 TTL 기간 동안 캐시에 유지되고 해당 기간이 경과된 후 Blob 서비스에 의해 새로 고쳐집니다. 그런 다음 프로세스가 반복됩니다.

TTL을 제어하는 두 가지 옵션이 있습니다.

1.	캐시 값을 설정하지 않으므로 기본 TTL인 7일을 사용합니다.
2.	**Put Blob**, **Put Block List** 또는 **Set Blob Properties** 요청의 *x-ms-blob-cache-control* 속성을 명시적으로 설정하거나 Azure Managed Library를 사용하여 [BlobProperties.CacheControl](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.blobproperties.cachecontrol.aspx) 속성을 설정합니다. 이 속성을 설정하면 Blob에 대한 *Cache-Control* 헤더의 값이 설정됩니다. 헤더 또는 속성의 값은 적절한 값(초)을 지정해야 합니다. 예를 들어 최대 캐싱 기간을 1년으로 설정하려면 요청 헤더를 `x-ms-blob-cache-control: public, max-age=31556926`으로 지정할 수 있습니다. 캐싱 헤더 설정에 대한 자세한 내용은 [HTTP/1.1 사양](http://www.w3.org/Protocols/rfc2616/rfc2616-sec13.html)을 참조하세요.

CDN을 통해 캐싱하려는 콘텐츠는 Azure 저장소 계정에 공개적으로 액세스할 수 있는 Blob로 저장해야 합니다. Azure Blob 서비스에 대한 자세한 내용은 [Blob 서비스 개념](https://msdn.microsoft.com/library/dd179376.aspx)을 참조하세요.

Blob 서비스에서 콘텐츠를 사용할 수 있는 몇 가지 방법이 있습니다.

-	[.NET용 Azure 저장소 클라이언트 라이브러리](https://msdn.microsoft.com/library/azure/mt347887.aspx)에서 제공된 관리되는 API 사용
-	타사 저장소 관리 도구 사용
-	[Azure 저장소 서비스 REST API](https://msdn.microsoft.com/library/azure/dd179355.aspx) 사용

다음 코드 예제는 Azure 저장소 클라이언트 라이브러리를 사용하여 컨테이너를 만들고, 공용 액세스를 위해 사용 권한을 설정하고, 컨테이너 내에 Blob을 만드는 콘솔 응용 프로그램입니다. 또한 Blob의 Cache-Control 헤더를 설정하여 원하는 새로 고침 간격을 명시적으로 지정합니다.

위에 표시된 대로 CDN을 사용하도록 설정한 경우 만든 Blob이 CDN에 의해 캐시됩니다. 고유한 저장소 계정과 액세스 키를 사용하여 계정 자격 증명을 지정해야 합니다.

```csharp
using System;
using Microsoft.WindowsAzure;
using Microsoft.WindowsAzure.StorageClient;

namespace BlobsInCDN
{
	class Program
	{
		static void Main(string[] args)
		{
			//Specify storage credentials.
			StorageCredentialsAccountAndKey credentials = new StorageCredentialsAccountAndKey("storagesample",
				"<your storage account key>");

			//Create a reference to your storage account, passing in your credentials.
			CloudStorageAccount storageAccount = new CloudStorageAccount(credentials, true);

			//Create a new client object, which will provide access to Blob service resources.
			CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

			//Create a new container.
			CloudBlobContainer container = blobClient.GetContainerReference("cdncontent");
			container.CreateIfNotExist();

			//Specify that the container is publicly accessible.
			BlobContainerPermissions containerAccess = new BlobContainerPermissions();
			containerAccess.PublicAccess = BlobContainerPublicAccessType.Container;
			container.SetPermissions(containerAccess);

			//Create a new blob and write some text to it.
			CloudBlob blob = blobClient.GetBlobReference("cdncontent/testblob.txt");
			blob.UploadText("This is a test blob.");

			//Set the Cache-Control header on the blob to specify your desired refresh interval.
			blob.SetCacheControl("public, max-age=31536000");
		}
	}

	public static class BlobExtensions
	{
		//A convenience method to set the Cache-Control header.
		public static void SetCacheControl(this CloudBlob blob, string value)
		{
			blob.Properties.CacheControl = value;
			blob.SetProperties();
		}
	}
}
```

CDN 특정 URL을 통해 Blob을 사용할 수 있는지 테스트합니다. 위에 표시된 Blob의 경우 URL이 다음과 유사합니다.

	http://<endpoint>.azureedge.net/cdncontent/testblob.txt  

원하는 경우 **wget** 또는 Fiddler와 같은 도구를 사용하여 요청과 응답의 세부 정보를 검사할 수 있습니다.

##참고 항목

[Azure CDN(콘텐츠 배달 네트워크)에서 클라우드 서비스 콘텐츠의 만료를 관리하는 방법](./cdn-manage-expiration-of-cloud-service-content.md)

<!---HONumber=AcomDC_0803_2016-->