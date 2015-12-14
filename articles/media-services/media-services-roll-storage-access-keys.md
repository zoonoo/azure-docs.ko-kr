<properties 
	pageTitle="저장소 액세스 키 롤링 후 미디어 서비스 업데이트" 
	description="이 문서에서는 저장소 액세스 키를 롤링한 후 미디어 서비스를 업데이트하는 방법에 대한 지침을 제공합니다." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako,milangada,cenkdin" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/15/2015"
	ms.author="juliako"/>

#방법: 저장소 액세스 키 롤링 후 미디어 서비스 업데이트

새 Azure 미디어 서비스 계정을 만들 때 미디어 콘텐츠를 저장하는 데 사용되는 Azure 저장소 계정을 선택하도록 요청받습니다. 하나 이상의 저장소 계정을 미디어 서비스 계정에 추가할 수 있습니다.

새 저장소 계정이 만들어지면 Azure는 2개의 512비트 저장소 액세스 키를 만들며, 이는 저장소 계정에 대한 액세스를 인증하는 데 사용됩니다. 저장소 연결을 보다 안전하게 유지하려면 저장소 액세스 키를 주기적으로 다시 생성하고 회전하는 것이 좋습니다. 2개의 액세스 키(기본 및 보조)는 다른 액세스 키를 다시 생성하는 동안 하나의 액세스 키를 사용하여 저장소 계정에 대한 연결을 유지할 수 있도록 제공됩니다. 이 과정은 "액세스 키 롤링"이라고도 합니다.

미디어 서비스는 저장소 키(기본 또는 보조) 중 하나에서 종속성을 지닙니다. 특히, 자산을 스트림 또는 다운로드하는 데 사용되는 로케이터는 액세스 키에 따라 달라집니다. 저장소 액세스 키를 롤링할 때 로케이터 업데이트를 확인해야 스트리밍 서비스에서의 중단이 없습니다.

>[AZURE.NOTE]저장소 키를 다시 생성한 후 미디어 서비스와 업데이트를 동기화해야 합니다.

이 항목에서는 저장소 키를 롤링하고 미디어 서비스를 업데이트하여 적합한 저장소 키를 사용하도록 하는 단계를 설명합니다. 여러 저장소 계정이 있는 경우, 각각의 저장소 계정마다 이 과정을 수행해야 합니다.

>[AZURE.NOTE]이 항목에서 설명한 단계를 프로덕션 계정에서 실행하기 전에 사전 프로덕션 계정에서 테스트해야 합니다.


## 1단계: 보조 저장소 액세스 키 다시 생성

보조 저장소 키 다시 생성을 시작합니다. 기본적으로 보조 키는 미디어 서비스에서 사용됩니다. 저장소 키를 롤링하는 방법에 대한 자세한 내용은 [방법: 저장소 액세스 키 보기, 복사 및 다시 생성](../storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys)을 참조하세요.
  
##<a id="step2"></a>2단계: 미디어 서비스를 업데이트하여 새 보조 저장소 키 사용

미디어 서비스를 업데이트하여 보조 저장소 액세스 키를 사용합니다. 다음 두 가지 방법 중 하나를 사용하여 미디어 서비스와 다시 생성된 저장소 키를 동기화할 수 있습니다.

- Azure 클래식 포털 사용: 미디어 서비스 계정을 선택하고 포털 창 맨 아래에 있는 “키 관리” 아이콘을 클릭합니다. 미디어 서비스와 동기화하려는 저장소 키에 따라 기본 키 동기화 또는 보조 키 동기화 단추를 선택합니다. 이 경우에는 보조 키를 사용합니다.

- 미디어 서비스 관리 REST API를 사용합니다.

다음 코드 예제에서는 미디어 서비스와 지정된 저장소 키를 동기화하기 위해 https://endpoint/<subscriptionId>/services/mediaservices/Accounts/<accountName>/StorageAccounts/<storageAccountName>/Key 요청을 생성하는 방법을 보여 줍니다. 이 경우에는 보조 저장소 키 값이 사용됩니다. 자세한 내용은 [방법: 미디어 서비스 관리 REST API 사용](http://msdn.microsoft.com/library/azure/dn167656.aspx)을 참조하세요.
 
		public void UpdateMediaServicesWithStorageAccountKey(string mediaServicesAccount, string storageAccountName, string storageAccountKey)
		{
		    var clientCert = GetCertificate(CertThumbprint);
		
		    HttpWebRequest request = (HttpWebRequest)WebRequest.Create(string.Format("{0}/{1}/services/mediaservices/Accounts/{2}/StorageAccounts/{3}/Key",
		        Endpoint, SubscriptionId, mediaServicesAccount, storageAccountName));
		    request.Method = "PUT";
		    request.ContentType = "application/json; charset=utf-8";
		    request.Headers.Add("x-ms-version", "2011-10-01");
		    request.Headers.Add("Accept-Encoding: gzip, deflate");
		    request.ClientCertificates.Add(clientCert);
		
		
		    using (var streamWriter = new StreamWriter(request.GetRequestStream()))
		    {
		        streamWriter.Write(""");
		        streamWriter.Write(storageAccountKey);
		        streamWriter.Write(""");
		        streamWriter.Flush();
		    }
		
		    using (var response = (HttpWebResponse)request.GetResponse())
		    {
		        string jsonResponse;
		        Stream receiveStream = response.GetResponseStream();
		        Encoding encode = Encoding.GetEncoding("utf-8");
		        if (receiveStream != null)
		        {
		            var readStream = new StreamReader(receiveStream, encode);
		            jsonResponse = readStream.ReadToEnd();
		        }
		    }
		}

이후에 기존 로케이터(이전 저장소 키에 대한 종속성이 있음)를 업데이트합니다.

>[AZURE.NOTE]보류 중인 작업에 영향을 받지 않기 위해 미디어 서비스와 함께 작업을 수행하기 전에 30분 가량 기다립니다.

##3단계: 로케이터 업데이트 

30분 후 주문형 로케이터를 다시 만들어 새 보조 저장소 키에 대한 종속성을 가져오고 기존의 URL을 유지할 수 있습니다.

SAS 로케이터를 업데이트하거나 다시 만들 때마다 URL이 변경됩니다.

>[AZURE.NOTE]주문형 로케이터의 기존 URL을 유지하려면 기존 로케이터를 삭제하고 동일한 ID로 새 로케이터를 만들어야 합니다.
 
아래 .NET 예제에서는 동일한 ID로 로케이터를 다시 만드는 방법을 보여줍니다.
	
	private static ILocator RecreateLocator(CloudMediaContext context, ILocator locator)
	{
	    // Save properties of existing locator.
	    var asset = locator.Asset;
	    var accessPolicy = locator.AccessPolicy;
	    var locatorId = locator.Id;
	    var startDate = locator.StartTime;
	    var locatorType = locator.Type;
	    var locatorName = locator.Name;
	
	    // Delete old locator.
	    locator.Delete();
	
	    if (locator.ExpirationDateTime <= DateTime.UtcNow)
	    {
	        throw new Exception(String.Format(
	            "Cannot recreate locator Id={0} because its locator expiration time is in the past",
	            locator.Id));
	    }
	
	    // Create new locator using saved properties.
	    var newLocator = context.Locators.CreateLocator(
	        locatorId,
	        locatorType,
	        asset,
	        accessPolicy,
	        startDate,
	        locatorName);
	
	
	
	    return newLocator;
	}


##5단계: 기본 저장소 액세스 키 다시 생성

기본 저장소 액세스 키를 다시 생성합니다. 저장소 키를 롤링하는 방법에 대한 자세한 내용은 [방법: 저장소 액세스 키 보기, 복사 및 다시 생성](../storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys)을 참조하세요.

##6단계: 미디어 서비스를 업데이트하여 새 기본 저장소 키 사용
	
이번에만 [2단계](media-services-roll-storage-access-keys.md#step2)에서 설명한 것과 동일한 과정을 통해 새 기본 저장소 액세스 키와 미디어 서비스 계정을 동기화합니다.

>[AZURE.NOTE]보류 중인 작업에 영향을 받지 않기 위해 미디어 서비스와 함께 작업을 수행하기 전에 30분 가량 기다립니다.

##7단계: 로케이터 업데이트  

30분 후 주문형 로케이터를 다시 만들어 새 기본 저장소 키에 대한 종속성을 가져오고 기존의 URL을 유지할 수 있습니다.

[3단계](media-services-roll-storage-access-keys.md#step-3-update-locators)에 설명된 것과 동일한 절차를 사용하세요.


##미디어 서비스 학습 경로

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##피드백 제공

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]



###승인 

이 문서를 만들 때 기여한 다음 사람들에게 감사 드리고자 합니다. Cenk Dingiloglu, Milan Gada, Seva Titov

<!---HONumber=AcomDC_1203_2015-->