<properties 
	pageTitle="REST API를 사용하여 미디어 서비스 계정에 파일 업로드" 
	description="자산을 만들고 업로드하여 미디어 서비스에 미디어 콘텐츠를 가져오는 방법에 대해 알아봅니다." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/05/2015" 
	ms.author="juliako"/>



#REST API를 사용하여 미디어 서비스 계정에 파일 업로드
[AZURE.INCLUDE [media-services-selector-upload-files](../../includes/media-services-selector-upload-files.md)]

이 문서는 [미디어 서비스 주문형 비디오 워크플로](media-services-video-on-demand-workflow.md) 시리즈의 일부입니다.

미디어 서비스에서 자산에 디지털 파일을 업로드합니다. [자산](https://msdn.microsoft.com/library/azure/hh974277.aspx) 엔터티에는 비디오, 오디오, 이미지, 미리 보기 컬렉션, 텍스트 트랙 및 선택 캡션 파일(및 이러한 파일에 대한 메타데이터)이 포함될 수 있습니다. 자산에 파일이 업로드되면 이후 처리 및 스트리밍을 위해 콘텐츠가 클라우드에 안전하게 저장됩니다.


>[AZURE.NOTE]미디어 서비스는 스트리밍 콘텐트에 대해 URL을 작성할 때 IAssetFile.Name 속성의 값을 사용합니다(예: http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.). 이러한 이유로 퍼센트 인코딩은 허용되지 않습니다. **Name** 속성 값에는 !*'();:@&=+$,/?%#"과 같은 [퍼센트 인코딩 예약 문자](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters)를 사용할 수 없습니다. 또한 파일 이름 확장명에는 ‘.’ 하나만 사용할 수 있습니다.

자산 수집을 위한 기본 워크플로는 다음 섹션으로 구분됩니다.

- 자산 만들기
- 자산 암호화(선택 사항)
- Blob 저장소에 파일 업로드


##자산 만들기

>[AZURE.NOTE]미디어 서비스 REST API를 사용할 때는 다음 사항을 고려해야 합니다.
>
>미디어 서비스에서 엔터티에 액세스할 때는 HTTP 요청에서 구체적인 헤더 필드와 값을 설정해야 합니다. 자세한 내용은 [미디어 서비스 REST API 개발 설정](media-services-rest-how-to-use.md)을 참조하세요.

>https://media.windows.net에 연결하면 다른 미디어 서비스 URI를 지정하는 301 리디렉션을 받게 됩니다. [REST API를 사용하여 미디어 서비스에 연결](media-services-rest-connect_programmatically.md)에서 설명한 대로 새 URI에 대한 후속 호출을 만들어야 합니다.
 
자산은 여러 유형이나 비디오, 오디오, 이미지, 미리 보기 컬렉션, 텍스트 트랙 및 선택된 캡션 파일을 포함한 미디어 서비스의 개체 집합에 대한 컨테이너입니다. REST API에서 자산을 만들려면 미디어 서비스에 POST 요청을 보내고 자산에 대한 속성 정보를 요청 본문에 배치해야 합니다.

자산을 만들 때 지정할 수 있는 속성 중 하나는 **옵션**입니다. **옵션**은 자산으로 만들 수 있는 암호화 옵션을 설명하는 열거 값입니다. 유효한 값은 값의 조합이 아니라 아래 목록에 있는 값 중 하나입니다.

- **None** = **0**: 암호화가 사용되지 않습니다. 기본값입니다. 이 옵션을 사용하면 콘텐츠가 전송 중인 상태이거나 저장소에 저장된 상태일 때 보호되지 않습니다. MP4를 배달하려는 경우 이 옵션을 사용하세요. 

- **StorageEncrypted** = **1**: 파일 업로드 및 저장 시 AES-256비트 암호화로 암호화할 것인지를 지정합니다.

	자산이 암호화된 저장소인 경우 자산 배달 정책을 구성해야 합니다. 자세한 내용은 [자산 배달 정책 구성](media-services-rest-configure-asset-delivery-policy.md)을 참조하세요.

- **CommonEncryptionProtected** = **2**: 일반적인 암호화 방식(예: PlayReady)으로 보호된 파일을 업로드하는 경우 지정합니다.

- **EnvelopeEncryptionProtected** = **4**: AES로 암호화된 HLS 파일을 업로드하는 경우 지정합니다. 파일을 Transform Manager로 인코딩 및 암호화해야 합니다.

>[AZURE.NOTE]자산에서 암호화를 사용하는 경우 **ContentKey**를 만들어 이를 [ContentKey를 만드는 방법](media-services-rest-create-contentkey.md) 항목에서 설명한 대로 자산에 연결해야 합니다. 자산에 파일을 업로드한 후에는 **자산**을 암호화하는 동안 얻은 값으로 **AssetFile** 엔터티의 암호화 속성을 업데이트해야 합니다. **MERGE** HTTP 요청을 사용하여 이를 수행합니다.


다음 예제에서는 자산을 만드는 방법을 보여 줍니다.

**HTTP 요청**

	POST https://media.windows.net/api/Assets HTTP/1.1
	Content-Type: application/json
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-6753-2233-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
	x-ms-version: 2.11
	Host: media.windows.net
	
	{"Name":"BigBuckBunny.mp4"}
	

**HTTP 응답**

성공하면 다음이 반환됩니다.
	
	HTP/1.1 201 Created
	Cache-Control: no-cache
	Content-Length: 452
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Assets('nb%3Acid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')
	Server: Microsoft-IIS/8.5
	x-ms-client-request-id: c59de965-bc89-4295-9a57-75d897e5221e
	request-id: e98be122-ae09-473a-8072-0ccd234a0657
	x-ms-request-id: e98be122-ae09-473a-8072-0ccd234a0657
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Sun, 18 Jan 2015 22:06:40 GMT
	{  
	   "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Assets/@Element",
	   "Id":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
	   "State":0,
	   "Created":"2015-01-18T22:06:40.6010903Z",
	   "LastModified":"2015-01-18T22:06:40.6010903Z",
	   "AlternateId":null,
	   "Name":"BigBuckBunny.mp4",
	   "Options":0,
	   "Uri":"https://storagetestaccount001.blob.core.windows.net/asset-9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
	   "StorageAccountName":"storagetestaccount001"
	}
	
##AssetFile 만들기

[AssetFile](http://msdn.microsoft.com/library/azure/hh974275.aspx) 엔터티는 blob 컨테이너에 저장된 비디오 또는 오디오 파일을 나타냅니다. 자산 파일은 항상 자산에 연결되며 자산에는 하나 이상의 자산 파일이 포함될 수 있습니다. 자산 파일 개체가 blob 컨테이너의 디지털 파일과 연결되지 않은 경우 미디어 서비스 인코더 작업을 하지 못합니다.

**AssetFile** 인스턴스와 실제 미디어 파일은 뚜렷이 다른 두 개체입니다. AssetFile 인스턴스는 미디어 파일에 대한 메타데이터를 포함하는 반면 미디어 파일은 실제 미디어 콘텐츠를 포함합니다.

Blob 컨테이너에 디지털 미디어 파일을 업로드 한 후 **MERGE** HTTP 요청을 사용하여 미디어 파일에 대한 정보로 AssetFile을 업데이트하게 됩니다(이 항목의 뒷부분 참조).

**HTTP 요청**

	POST https://media.windows.net/api/Files HTTP/1.1
	Content-Type: application/json
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-6753-4ca2-2233-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
	x-ms-version: 2.11
	Host: media.windows.net
	Content-Length: 164
	
	{  
	   "IsEncrypted":"false",
	   "IsPrimary":"false",
	   "MimeType":"video/mp4",
	   "Name":"BigBuckBunny.mp4",
	   "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1"
	}


**HTTP 응답**

	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Content-Length: 535
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Files('nb%3Acid%3AUUID%3Af13a0137-0a62-9d4c-b3b9-ca944b5142c5')
	Server: Microsoft-IIS/8.5
	request-id: 98a30e2d-f379-4495-988e-0b79edc9b80e
	x-ms-request-id: 98a30e2d-f379-4495-988e-0b79edc9b80e
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Mon, 19 Jan 2015 00:34:07 GMT
	
	{  
	   "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Files/@Element",
	   "Id":"nb:cid:UUID:f13a0137-0a62-9d4c-b3b9-ca944b5142c5",
	   "Name":"BigBuckBunny.mp4",
	   "ContentFileSize":"0",
	   "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
	   "EncryptionVersion":null,
	   "EncryptionScheme":null,
	   "IsEncrypted":false,
	   "EncryptionKeyId":null,
	   "InitializationVector":null,
	   "IsPrimary":false,
	   "LastModified":"2015-01-19T00:34:08.1934137Z",
	   "Created":"2015-01-19T00:34:08.1934137Z",
	   "MimeType":"video/mp4",
	   "ContentChecksum":null
	}


## 쓰기 권한으로 AccessPolicy를 만듭니다. 

blob 저장소에 모든 파일을 업로드하기 전에 자산에 쓰기 위한 액세스 정책 권한을 설정합니다. 이렇게 하려면 AccessPolicies 엔터티 집합에 HTTP 요청을 게시합니다. 작성 시 DurationInMinutes 값을 정의하지 않으면 응답에서 500 내부 서버 오류 메시지가 다시 나타납니다. AccessPolicies에 대한 자세한 내용은 [AccessPolicy](http://msdn.microsoft.com/library/azure/hh974297.aspx)를 참조하세요.

다음 예제에서는 AccessPolicy를 만드는 방법을 보여 줍니다.
		
**HTTP 요청**

	POST https://media.windows.net/api/AccessPolicies HTTP/1.1
	Content-Type: application/json
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-6753-2233-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
	x-ms-version: 2.11
	Host: media.windows.net
	
	{"Name":"NewUploadPolicy", "DurationInMinutes":"440", "Permissions":"2"} 

**HTTP 요청**

	If successful, the following response is returned:
	
	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Content-Length: 312
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Location: https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies('nb%3Apid%3AUUID%3Abe0ac48d-af7d-4877-9d60-1805d68bffae')
	Server: Microsoft-IIS/8.5
	request-id: 74c74545-7e0a-4cd6-a440-c1c48074a970
	x-ms-request-id: 74c74545-7e0a-4cd6-a440-c1c48074a970
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Sun, 18 Jan 2015 22:18:06 GMT

	{  
	   "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#AccessPolicies/@Element",
	   "Id":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
	   "Created":"2015-01-18T22:18:06.6370575Z",
	   "LastModified":"2015-01-18T22:18:06.6370575Z",
	   "Name":"NewUploadPolicy",
	   "DurationInMinutes":440.0,
	   "Permissions":2
	}

##업로드 URL 가져오기

실제 업로드 URL을 받으려면 SAS 로케이터를 만듭니다. 로케이터는 자산에 있는 파일에 액세스하려는 클라이언트에 대한 시작 시간과 연결 끝점의 형식을 정의합니다. 다양한 클라이언트 요청 및 요구 사항을 처리하기 위해 지정된 AccessPolicy 및 자산 쌍에 대해 여러 로케이터 엔터티를 만들 수 있습니다. 이러한 각 로케이터는 AccessPolicy의 StartTime 값과 DurationInMinutes 값을 사용하여 URL이 사용될 수는 시간의 길이를 결정합니다. 자세한 내용은 [로케이터](http://msdn.microsoft.com/library/azure/hh974308.aspx)를 참조하세요.


SAS URL의 형식은 다음과 같습니다.

	{https://myaccount.blob.core.windows.net}/{asset name}/{video file name}?{SAS signature}

다음과 같은 몇 가지 고려 사항이 적용됩니다.

- 지정된 자산과 연관된 고유 로케이터는 한 번에 5개 이상 가질 수 없습니다. 자세한 내용은 로케이터를 참조하세요.
- 파일을 즉시 업로드해야 하는 경우 StartTime 값을 현재 시간에서 5분 전으로 설정해야 합니다. 클라이언트 컴퓨터와 미디어 서비스 사이에 시간차가 있을 수 있기 때문입니다. 또한 StartTime 값은 다음 DateTime 형식이어야 합니다. YYYY-MM-DDTHH:mm:ssZ(예: "2014-05-23T17:53:50Z").	
- 로케이터를 만든 후 사용할 수 있을 때까지 30-40초의 지연이 있을 수 있습니다. 이 문제는 SAS URL 및 원본 로케이터 모두에 적용됩니다.

다음 예제에서는 요청 본문의 형식 속성에서 정의한 대로(SAS 로케이터의 경우 "1" 그리고 주문형 원본 로케이터의 경우 "2") SAS URL 로케이터를 만드는 방법을 보여 줍니다. 반환된 **경로** 속성은 파일 업로드 시 반드시 사용해야 하는 URL을 포함합니다.
	
**HTTP 요청**
	
	POST https://media.windows.net/api/Locators HTTP/1.1
	Content-Type: application/json
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-6753-4ca2-2233-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
	x-ms-version: 2.11
	Host: media.windows.net
	{  
	   "AccessPolicyId":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
	   "AssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
	   "StartTime":"2015-02-18T16:45:53",
	   "Type":1
	}


**HTTP 응답**

성공하면 다음 응답이 반환됩니다.
		
	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Content-Length: 949
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Locators('nb%3Alid%3AUUID%3Aaf57bdd8-6751-4e84-b403-f3c140444b54')
	Server: Microsoft-IIS/8.5
	request-id: 2adeb1f8-89c5-4cc8-aa4f-08cdfef33ae0
	x-ms-request-id: 2adeb1f8-89c5-4cc8-aa4f-08cdfef33ae0
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Mon, 19 Jan 2015 03:01:29 GMT
	
	{  
	   "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Locators/@Element",
	   "Id":"nb:lid:UUID:af57bdd8-6751-4e84-b403-f3c140444b54",
	   "ExpirationDateTime":"2015-02-19T00:05:53",
	   "Type":1,
	   "Path":"https://storagetestaccount001.blob.core.windows.net/asset-f438649c-313c-46e2-8d68-7d2550288247?sv=2012-02-12&sr=c&si=af57bdd8-6751-4e84-b403-f3c140444b54&sig=fE4btwEfZtVQFeC0Wh3Kwks2OFPQfzl5qTMW5YytiuY%3D&st=2015-02-18T16%3A45%3A53Z&se=2015-02-19T00%3A05%3A53Z",
	   "BaseUri":"https://storagetestaccount001.blob.core.windows.net/asset-f438649c-313c-46e2-8d68-7d2550288247",
	   "ContentAccessComponent":"?sv=2012-02-12&sr=c&si=af57bdd8-6751-4e84-b403-f3c140444b54&sig=fE4btwEfZtVQFeC0Wh3Kwks2OFPQfzl5qTMW5YytiuY%3D&st=2015-02-18T16%3A45%3A53Z&se=2015-02-19T00%3A05%3A53Z",
	   "AccessPolicyId":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
	   "AssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
	   "StartTime":"2015-02-18T16:45:53",
	   "Name":null
	}

## Blob 저장소 컨테이너에 파일 업로드
	
AccessPolicy와 로케이터를 설정했으면 실제 파일은 Azure 저장소 REST API를 사용하여 Azure Blob 저장소 컨테이너에 업로드됩니다. 페이지 또는 블록 blob에 업로드할 수 있습니다.

>[AZURE.NOTE]이전 섹션에서 받은 로케이터 **경로** 값에 업로드하려는 파일에 대한 파일 이름을 추가해야 합니다. 예: https://storagetestaccount001.blob.core.windows.net/asset-e7b02da4-5a69-40e7-a8db-e8f4f697aac0/BigBuckBunny.mp4? . . .

Azure 저장소 Blob 작업에 대한 자세한 내용은 [Blob 서비스 REST API](http://msdn.microsoft.com/library/azure/dd135733.aspx)를 참조하세요.


## AssetFile 업데이트 

이제 파일을 업로드했으므로 FileAsset 크기(및 기타) 정보를 업데이트합니다. 예:
	
	MERGE https://media.windows.net/api/Files('nb%3Acid%3AUUID%3Af13a0137-0a62-9d4c-b3b9-ca944b5142c5') HTTP/1.1
	Content-Type: application/json
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-6753-4ca2-2233-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421662918&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=utmoXXbm9Q7j4tW1yJuMVA3egRiQy5FPygwadkmPeaY%3d
	x-ms-version: 2.11
	Host: media.windows.net
	
	{  
	   "ContentFileSize":"1186540",
	   "Id":"nb:cid:UUID:f13a0137-0a62-9d4c-b3b9-ca944b5142c5",
	   "MimeType":"video/mp4",
	   "Name":"BigBuckBunny.mp4",
	   "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1"
	}


**HTTP 응답**

성공하면 HTTP/1.1 204 내용 없음이 반환됩니다.

## 로케이터와 AccessPolicy 삭제 

**HTTP 요청**


	DELETE https://media.windows.net/api/Locators('nb%3Alid%3AUUID%3Aaf57bdd8-6751-4e84-b403-f3c140444b54') HTTP/1.1
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-6753-2233-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421662918&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=utmoXXbm9Q7j4tW1yJuMVA3egRiQy5FPygwadkmPeaY%3d
	x-ms-version: 2.11
	Host: media.windows.net

	
**HTTP 응답**

성공하면 다음이 반환됩니다.

	HTTP/1.1 204 No Content 
	...

**HTTP 요청**

	DELETE https://media.windows.net/api/AccessPolicies('nb%3Apid%3AUUID%3Abe0ac48d-af7d-4877-9d60-1805d68bffae') HTTP/1.1
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-6753-2233-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421662918&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=utmoXXbm9Q7j4tW1yJuMVA3egRiQy5FPygwadkmPeaY%3d
	x-ms-version: 2.11
	Host: media.windows.net

**HTTP 응답**

성공하면 다음이 반환됩니다.

	HTTP/1.1 204 No Content 
	...

 
[How to Get a Media Processor]: media-services-get-media-processor.md
 

<!---HONumber=July15_HO3-->