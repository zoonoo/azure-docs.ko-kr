<properties 
	pageTitle="주문형 비디오는 미디어 서비스 REST API와 함께 제공됩니다." 
	description="이 자습서에서는 Azure 미디어 서비스(AMS) REST API를 사용하여 주문형 비디오(VoD) 콘텐츠 제공 응용 프로그램을 구현하는 단계를 안내합니다." 
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
	ms.date="04/14/2015" 
	ms.author="juliako"/>

# 퀵 스타트: 주문형 비디오는 미디어 서비스 REST API와 함께 제공됩니다. 

[AZURE.INCLUDE [media-services-selector-get-started](../includes/media-services-selector-get-started.md)]


>[AZURE.NOTE]
> 이 자습서를 완료하려면 Azure 계정이 필요합니다. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A8A8397B5" target="_blank">Azure 무료 평가판</a>을 참조하세요.

이 자습서에서는 AMS(Azure 미디어 서비스) REST API를 사용하여 주문형 비디오(VoD) 콘텐츠 제공 응용 프로그램을 구현하는 단계를 안내합니다. 

기본적인 미디어 서비스 워크플로와 미디어 서비스 개발에 필요한 가장 일반적인 프로그래밍 개체 및 작업을 소개합니다. 자습서를 마치면 업로드하고 인코딩하고 다운로드한 샘플 미디어 파일을 스트리밍하거나 점진적으로 다운로드할 수 있습니다.  

## 필수 조건
미디어 서비스 REST API를 사용하여 개발을 시작하려면 다음 필수 조건이 필요합니다.

- 미디어 서비스 REST API를 사용하여 개발하는 방법을 이해합니다. 자세한 내용은 [media-services-rest-overview](http://msdn.microsoft.com/library/azure/hh973616.aspx)를 참조하세요.
- HTTP 요청 및 응답을 보낼 수 있도록 선택한 응용 프로그램입니다. 이 자습서에서는 [Fiddler](http://www.telerik.com/download/fiddler)를 사용합니다. 

다음 작업은 본 퀵 스타트에 표시됩니다.

1.  포털을 사용하여 미디어 서비스 계정을 만듭니다.
1.  REST API를 통해 미디어 서비스 계정에 연결합니다.
1.  REST API를 통해 새 자산을 만들고 비디오를 업로드합니다.
1.  REST API를 통해 스트리밍 단위를 구성합니다.
2.  REST API를 통해 원본 파일을 적응 비트 전송률 MP4 파일 집합으로 인코딩합니다.
1.  REST API를 통해 인코딩된 자산에 대한 배달 정책을 구성합니다.
1.  REST API를 통해 자산을 게시하고 스트리밍 기능 및 URL 점진적 다운로드를 사용합니다. 
1.  콘텐츠를 재생합니다. 


## <a id="create_ams"></a>포털을 사용하여 미디어 서비스 계정 만들기

1. [관리 포털][]에서 **새로 만들기**, **미디어 서비스**, **빠른 생성**을 차례로 클릭합니다.
   
	![Media Services Quick Create](./media/media-services-create-account/wams-QuickCreate.png)

2. **이름**에 새 계정의 이름을 입력합니다. 미디어 서비스 계정 이름은 공백 없이 모두 소문자로 이루어진 3-24자의 숫자 또는 문자입니다. 

3. **지역**에서 미디어 서비스 계정에 대한 메타데이터 레코드를 저장하는 데 사용할 지역을 선택합니다. 사용 가능한 미디어 서비스 지역만 드롭다운에 표시됩니다. 

4. **저장소 계정**에서 미디어 서비스 계정의 미디어 콘텐츠가 포함된 Blob 저장소를 제공할 저장소 계정을 선택합니다. 미디어 서비스 계정과 동일한 지역의 기존 저장소 계정을 선택하거나 새 저장소 계정을 만들 수 있습니다. 동일한 지역에 새 저장소 계정이 생성됩니다. 

5. 새 저장소 계정을 만든 경우 **새 저장소 계정 이름**에 저장소 계정의 이름을 입력합니다. 저장소 계정 이름에 대한 규칙은 미디어 서비스 계정의 경우와 같습니다.

6. 양식 맨 아래에 있는 **빠른 생성**을 클릭합니다.

	창 맨 아래에 있는 메시지 영역에서 프로세스의 상태를 모니터링할 수 있습니다.

	계정이 만들어지면 상태가 활성으로 변경됩니다. 
	
	페이지의 맨 아래에 **키 관리** 단추가 나타납니다. 이 단추를 클릭하면 미디어 서비스 계정 이름과 기본 키 및 보조 키가 포함된 대화 상자가 표시됩니다. 프로그래밍 방식으로 미디어 서비스 계정에 액세스하려면 계정 이름과 기본 키 정보가 필요합니다. 

	
	![Media Services Page](./media/media-services-create-account/wams-mediaservices-page.png)

	계정 이름을 두 번 클릭하면 기본적으로 퀵 스타트 페이지가 표시됩니다. 이 페이지를 통해 포털의 다른 페이지에서도 사용할 수 있는 몇 가지 관리 작업을 수행할 수 있습니다. 예를 들어 이 페이지에서 비디오 파일을 업로드하거나 콘텐츠 페이지에서 업로드할 수 있습니다.


## <a id="connect"></a>REST API를 통해 미디어 서비스 계정에 연결

Azure 미디어 서비스에 액세스하려면 두 가지가 필요합니다. Azure 액세스 제어 서비스(ACS)에서 제공하는 액세스 토큰과 미디어 서비스 자체 URI입니다. 미디어 서비스를 호출할 때 올바른 헤더 값을 지정하고 액세스 토큰을 올바르게 통과하면 이 요청을 할 때 사용자가 원하는 방법을 사용할 수 있습니다.

다음 단계는 미디어 서비스 REST API를 사용하여 미디어 서비스에 연결할 때 가장 일반적인 워크플로를 설명합니다.

1. 액세스 토큰을 가져옵니다. 
2. 미디어 서비스 URI에 연결합니다. 
	
	>[AZURE.NOTE]
	https://media.windows.net에 연결하면 다른 미디어 서비스 URI를 지정하는 301 리디렉션을 받게 됩니다. 사용자는 새 URI에 대한 후속 호출을 해야 합니다.
	> 
	> ODATA API 메타데이터 설명을 포함하는 HTTP/1.1 200 응답을 받을 수도 있습니다.
3. 새 URL에 후속 API 호출을 게시합니다. 
	
	예를 들어 연결을 시도한 후 다음 항목을 받은 경우.
		
		HTTP/1.1 301 Moved Permanently
		Location: https://wamsbayclus001rest-hs.cloudapp.net/api/

	다음에 대한 후속 API 호출을 게시해야 합니다. https://wamsbayclus001rest-hs.cloudapp.net/api/.

### 액세스 토큰 가져오기

REST API를 통해 바로 미디어 서비스에 액세스하려면 ACS에서 액세스 토큰을 검색하여 서비스에 HTTP 요청을 할 때마다 이를 사용합니다. 이 토큰은 HTTP 요청 헤더에서 제공하는 액세스 클레임을 기반으로 하고 OAuth v2 프로토콜을 사용하는 ACS에서 제공하는 다른 토큰과 비슷합니다. 미디어 서비스에 직접 연결하기 전에는 다른 필수 조건은 필요하지 않습니다.

다음 예제에서는 HTTP 요청 헤더와 토큰을 검색하는 데 사용되는 본문을 보여줍니다.

**헤더**:

	POST https://wamsprodglobal001acs.accesscontrol.windows.net/v2/OAuth2-13 HTTP/1.1
	Content-Type: application/x-www-form-urlencoded
	Host: wamsprodglobal001acs.accesscontrol.windows.net
	Content-Length: 120
	Expect: 100-continue
	Connection: Keep-Alive
	Accept: application/json

	
**본문**:

이 요청의 본문에 있는 client_id와 client_secret 값을 입증해야 합니다. client_id와 client_secret은 각각 AccountName과 AccountKey 값에 해당합니다. 이러한 값은 계정을 설정할 때 미디어 서비스에서 제공합니다. 

미디어 서비스 계정에 대한 AccountKey는 액세스 토큰 요청에서 client_secret 값으로 사용하는 경우 URL로 인코딩되어야 합니다.

	grant_type=client_credentials&client_id=ams_account_name&client_secret=URL_encoded_ams_account_key&scope=urn%3aWindowsAzureMediaServices


예를 들면 다음과 같습니다. 

	grant_type=client_credentials&client_id=amstestaccount001&client_secret=wUNbKhNj07oqjqU3Ah9R9f4kqTJ9avPpfe6Pk3YZ7ng%3d&scope=urn%3aWindowsAzureMediaServices


다음 예제에서는 응답 본문에 액세스 토큰을 포함하는 HTTP 응답을 보여 줍니다.

	HTTP/1.1 200 OK
	Cache-Control: no-cache, no-store
	Pragma: no-cache
	Content-Type: application/json; charset=utf-8
	Expires: -1
	request-id: a65150f5-2784-4a01-a4b7-33456187ad83
	X-Content-Type-Options: nosniff
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Thu, 15 Jan 2015 08:07:20 GMT
	Content-Length: 670
	
	{  
	   "token_type":"http://schemas.xmlsoap.org/ws/2009/11/swt-token-profile-1.0",
	   "access_token":"http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421330840&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=uf69n82KlqZmkJDNxhJkOxpyIpA2HDyeGUTtSnq1vlE%3d",
	   "expires_in":"21600",
	   "scope":"urn:WindowsAzureMediaServices"
	}
	

>[AZURE.NOTE]
외부 저장소에 "access_token" 및 "expires_in" 값을 캐시하는 것이 좋습니다. 나중에 저장소에서 토큰 데이터를 검색하여 미디어 서비스 REST API 호출에서 다시 사용할 수 있습니다. 여러 프로세스 또는 컴퓨터 사이에서 토큰을 안전하게 공유할 수 있는 시나리오에 특히 유용합니다.

액세스 토큰의 "expires_in" 값을 모니터링하고 필요에 따라 REST API 호출을 새 토큰으로 업데이트해야 합니다.

### 미디어 서비스 URI에 연결

미디어 서비스의 루트 URI는 https://media.windows.net/입니다. 이 URI에 처음으로 연결해야 하며 응답으로 301 리디렉션을 받은 경우 새 URI에 대한 후속 호출을 해야합니다. 또한 요청에서 자동 리디렉션/팔로우 논리를 사용하지 마세요 HTTP 동사와 요청 본문은 새 URI로 전달되지 않습니다.

자산 파일을 업로드하고 다운로드하기 위한 루트 URI는 https://yourstorageaccount.blob.core.windows.net/으로 저장소 계정 이름은 미디어 서비스 계정을 설정하는 동안 사용한 이름과 동일합니다.

다음 예제에서는 미디어 서비스 루트 URI(https://media.windows.net/)에 대한 HTTP 요청을 설명합니다. 요청은 응답에서 301 리디렉션을 받습니다. 후속 요청은 새 URI((https://wamsbayclus001rest-hs.cloudapp.net/api/))를 사용합니다.     

**HTTP 요청**:
	
	GET https://media.windows.net/ HTTP/1.1
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421500579&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=ElVWXOnMVggFQl%2ft9vhdcv1qH1n%2fE8l3hRef4zPmrzg%3d
	x-ms-version: 2.8
	Accept: application/json
	Host: media.windows.net


**HTTP 응답**:
	
	HTTP/1.1 301 Moved Permanently
	Location: https://wamsbayclus001rest-hs.cloudapp.net/api/
	Server: Microsoft-IIS/8.5
	request-id: 987d7652-497a-44e5-b815-f492e02aef97
	x-ms-request-id: 987d7652-497a-44e5-b815-f492e02aef97
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Sat, 17 Jan 2015 07:44:53 GMT
	Content-Length: 164
	
	<html><head><title>Object moved</title></head><body>
	<h2>Object moved to <a href="https://wamsbayclus001rest-hs.cloudapp.net/api/">here</a>.</h2>
	</body></html>


**HTTP 요청**(새 URI 사용):
			
	GET https://wamsbayclus001rest-hs.cloudapp.net/api/ HTTP/1.1
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421500579&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=ElVWXOnMVggFQl%2ft9vhdcv1qH1n%2fE8l3hRef4zPmrzg%3d
	x-ms-version: 2.8
	Accept: application/json
	Host: wamsbayclus001rest-hs.cloudapp.net


**HTTP 응답**:
	
	HTTP/1.1 200 OK
	Cache-Control: no-cache
	Content-Length: 1250
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Server: Microsoft-IIS/8.5
	request-id: 5f52ea9d-177e-48fe-9709-24953d19f84a
	x-ms-request-id: 5f52ea9d-177e-48fe-9709-24953d19f84a
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Sat, 17 Jan 2015 07:44:52 GMT
	
	{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata","value":[{"name":"AccessPolicies","url":"AccessPolicies"},{"name":"Locators","url":"Locators"},{"name":"ContentKeys","url":"ContentKeys"},{"name":"ContentKeyAuthorizationPolicyOptions","url":"ContentKeyAuthorizationPolicyOptions"},{"name":"ContentKeyAuthorizationPolicies","url":"ContentKeyAuthorizationPolicies"},{"name":"Files","url":"Files"},{"name":"Assets","url":"Assets"},{"name":"AssetDeliveryPolicies","url":"AssetDeliveryPolicies"},{"name":"IngestManifestFiles","url":"IngestManifestFiles"},{"name":"IngestManifestAssets","url":"IngestManifestAssets"},{"name":"IngestManifests","url":"IngestManifests"},{"name":"StorageAccounts","url":"StorageAccounts"},{"name":"Tasks","url":"Tasks"},{"name":"NotificationEndPoints","url":"NotificationEndPoints"},{"name":"Jobs","url":"Jobs"},{"name":"TaskTemplates","url":"TaskTemplates"},{"name":"JobTemplates","url":"JobTemplates"},{"name":"MediaProcessors","url":"MediaProcessors"},{"name":"EncodingReservedUnitTypes","url":"EncodingReservedUnitTypes"},{"name":"Operations","url":"Operations"},{"name":"StreamingEndpoints","url":"StreamingEndpoints"},{"name":"Channels","url":"Channels"},{"name":"Programs","url":"Programs"}]}
	 


>[AZURE.NOTE] 지금부터 이 자습서에서는 새 URI를 사용합니다.

## <a id="upload"></a>REST API를 통해 새 자산 만들기 및 비디오 업로드

미디어 서비스에서 자산에 디지털 파일을 업로드합니다. **자산** 엔터티에는 비디오, 오디오, 이미지, 미리 보기 컬렉션, 텍스트 트랙 및 선택 캡션 파일(및 이 파일에 대한 메타데이터)이 포함될 수 있습니다.  자산에 파일이 업로드되면 이후 처리 및 스트리밍을 위해 콘텐츠가 클라우드에 안전하게 저장됩니다. 

자산을 만들 때 제공해야 하는 값 중 하나는 자산 생성 옵션입니다. **옵션** 속성은 자산으로 만들 수 있는 암호화 옵션을 설명하는 열거 값입니다. 유효한 값은 이 목록의 값의 조합이 아니라 아래 목록에 있는 값 중 하나입니다.

 
- **None** = **0** -암호화가 사용되지 않습니다. 이 옵션을 사용하면 콘텐츠가 전송 중인 상태이거나 저장소에 저장된 상태일 때 보호되지 않습니다.

	MP4를 배달하려는 경우 이 옵션을 사용하세요. 
- **StorageEncrypted** = **1** - AES-256비트 암호화를 사용하여 암호화되어 있지 않은 콘텐츠를 로컬에서 암호화한 다음 암호화되어 저장된 Azure 저장소에 업로드합니다. 저장소 암호화로 보호된 자산은 자동으로 암호 해제되어 인코딩되기 전에 암호화된 파일 시스템에 배치됩니다. 그리고 필요에 따라 새 출력 자산으로 다시 업로드되기 전에 다시 암호화됩니다. 저장소 암호화를 사용하는 기본적인 사례는 디스크에 저장된 상태일 때 강력한 암호화로 고품질의 입력 미디어 파일을 보호하려는 경우입니다.
- **CommonEncryption** = **2** - 이미 암호화되어 일반적인 암호화 또는 PlayReady DRM(예: PlayReady DRM으로 보호되는 부드러운 스트리밍)으로 보호된 콘텐츠를 업로드하는 경우 이 옵션을 사용합니다.
- **EnvelopeEncrypted** = **4** - AES로 암호화된 HLS를 업로드하는 경우 이 옵션을 사용합니다. 파일을 Transform Manager로 인코딩 및 암호화해야 합니다.

### 자산 만들기

자산은 여러 유형이나 비디오, 오디오, 이미지, 미리 보기 컬렉션, 텍스트 트랙 및 선택된 캡션 파일을 포함한 미디어 서비스의 개체 집합에 대한 컨테이너입니다. REST API에서 자산을 만들려면 미디어 서비스에 POST 요청을 보내고 자산에 대한 속성 정보를 요청 본문에 배치해야 합니다.

다음 예제에서는 자산을 만드는 방법을 보여 줍니다.

**HTTP 요청**

	POST https://wamsbayclus001rest-hs.cloudapp.net/api/Assets HTTP/1.1
	Content-Type: application/json
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
	x-ms-version: 2.8
	x-ms-client-request-id: c59de965-bc89-4295-9a57-75d897e5221e
	Host: wamsbayclus001rest-hs.cloudapp.net
	Content-Length: 45
	
	{"Name":"BigBuckBunny.mp4", "Options":"0"}
	

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
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Sun, 18 Jan 2015 22:06:40 GMT
		
	{  
	   "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Assets/@Element",
	   "Id":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
	   "State":0,
	   "Created":"2015-01-18T22:06:40.6010903Z",
	   "LastModified":"2015-01-18T22:06:40.6010903Z",
	   "AlternateId":null,
	   "Name":"BigBuckBunny2.mp4",
	   "Options":0,
	   "Uri":"https://storagetestaccount001.blob.core.windows.net/asset-9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
	   "StorageAccountName":"storagetestaccount001"
	}
	
### AssetFile 만들기

[AssetFile](http://msdn.microsoft.com/library/azure/hh974275.aspx) 엔터티는 blob 컨테이너에 저장된 비디오 또는 오디오 파일을 나타냅니다. 자산 파일은 항상 자산에 연결되며 자산에는 하나 이상의 AssetFile이 포함될 수 있습니다. 자산 파일 개체가 blob 컨테이너의 디지털 파일과 연결되지 않은 경우 미디어 서비스 인코더 작업을 하지 못합니다.

Blob 컨테이너에 디지털 미디어 파일을 업로드 한 후 **MERGE** HTTP 요청을 사용하여 미디어 파일에 대한 정보로 AssetFile을 업데이트하게 됩니다(이 항목의 뒷부분 참조). 

**HTTP 요청**

	POST https://wamsbayclus001rest-hs.cloudapp.net/api/Files HTTP/1.1
	Content-Type: application/json
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
	x-ms-version: 2.8
	Host: wamsbayclus001rest-hs.cloudapp.net
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


### 쓰기 권한으로 AccessPolicy를 만듭니다. 

blob 저장소에 모든 파일을 업로드하기 전에 자산에 쓰기 위한 액세스 정책 권한을 설정합니다. 이렇게 하려면 AccessPolicies 엔터티 집합에 HTTP 요청을 게시합니다. 작성 시 DurationInMinutes 값을 정의하지 않으면 응답에서 500 내부 서버 오류 메시지가 다시 나타납니다. AccessPolicies에 대한 자세한 내용은 [AccessPolicy](http://msdn.microsoft.com/library/azure/hh974297.aspx)를 참조하세요.

다음 예제에서는 AccessPolicy를 만드는 방법을 보여 줍니다.
		
**HTTP 요청**

	POST https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies HTTP/1.1
	Content-Type: application/json
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
	x-ms-version: 2.8
	Host: wamsbayclus001rest-hs.cloudapp.net
	Content-Length: 74
	
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
	X-Powered-By: ASP.NET
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

### 업로드 URL 가져오기

실제 업로드 URL을 받으려면 SAS 로케이터를 만듭니다. 로케이터는 자산에 있는 파일에 액세스하려는 클라이언트에 대한 시작 시간과 연결 끝점의 형식을 정의합니다. 다양한 클라이언트 요청 및 요구 사항을 처리하기 위해 지정된 AccessPolicy 및 자산 쌍에 대해 여러 로케이터 엔터티를 만들 수 있습니다. 이러한 각 로케이터는 AccessPolicy의 StartTime 값과 DurationInMinutes 값을 사용하여 URL이 사용될 수는 시간의 길이를 결정합니다. 자세한 내용은 [로케이터](http://msdn.microsoft.com/library/azure/hh974308.aspx)를 참조하세요.


SAS URL의 형식은 다음과 같습니다.

	{https://myaccount.blob.core.windows.net}/{asset name}/{video file name}?{SAS signature}

다음과 같은 몇 가지 고려 사항이 적용됩니다.

- 지정된 자산과 연관된 고유 로케이터는 한 번에 5개 이상 가질 수 없습니다. 자세한 내용은 로케이터를 참조하세요.
- 파일을 즉시 업로드해야 하는 경우 StartTime 값을 현재 시간에서 5분 전으로 설정해야 합니다. 클라이언트 컴퓨터와 미디어 서비스 사이에 시간차가 있을 수 있기 때문입니다. 또한 StartTime 값은 다음 DateTime 형식이어야 합니다. YYYY-m M-DDTHH:mm:ssZ (예: "2014-05-23T17:53:50Z").	
- 로케이터를 만든 후 사용할 수 있을 때까지 30-40초의 지연이 있을 수 있습니다. 이 문제는 SAS URL 및 원본 로케이터 모두에 적용됩니다.

다음 예제에서는 요청 본문의 형식 속성에서 정의한 대로(SAS 로케이터의 경우 "1" 그리고 주문형 원본 로케이터의 경우 "2") SAS URL 로케이터를 만드는 방법을 보여 줍니다. 반환된 **경로** 속성은 파일 업로드 시 반드시 사용해야 하는 URL을 포함합니다.
	
**HTTP 요청**
	
	POST https://wamsbayclus001rest-hs.cloudapp.net/api/Locators HTTP/1.1
	Content-Type: application/json
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=f7f09258-6753-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421640053&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=vlG%2fPYdFDMS1zKc36qcFVWnaNh07UCkhYj3B71%2fk1YA%3d
	x-ms-version: 2.8
	Host: wamsbayclus001rest-hs.cloudapp.net
	Content-Length: 178
	
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

### Blob 저장소 컨테이너에 파일 업로드
	
AccessPolicy와 로케이터를 설정했으면 실제 파일은 Azure 저장소 REST API를 사용하여 Azure Blob 저장소 컨테이너에 업로드됩니다. 페이지 또는 블록 blob에 업로드할 수 있습니다. 

>[AZURE.NOTE] 이전 섹션에서 받은 로케이터 **경로** 값에 업로드하려는 파일에 대한 파일 이름을 추가해야 합니다. 예: https://storagetestaccount001.blob.core.windows.net/asset-e7b02da4-5a69-40e7-a8db-e8f4f697aac0/BigBuckBunny.mp4? . . . 

Azure 저장소 Blob 작업에 대한 자세한 내용은 [Blob 서비스 REST API](http://msdn.microsoft.com/library/azure/dd135733.aspx)를 참조하세요.


### AssetFile 업데이트 

이제 파일을 업로드했으므로 FileAsset 크기(및 기타) 정보를 업데이트합니다. 예를 들면 다음과 같습니다.
	
	MERGE https://wamsbayclus001rest-hs.cloudapp.net/api/Files('nb%3Acid%3AUUID%3Af13a0137-0a62-9d4c-b3b9-ca944b5142c5') HTTP/1.1
	Content-Type: application/json
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421662918&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=utmoXXbm9Q7j4tW1yJuMVA3egRiQy5FPygwadkmPeaY%3d
	x-ms-version: 2.8
	Host: wamsbayclus001rest-hs.cloudapp.net
	
	{  
	   "ContentFileSize":"1186540",
	   "Id":"nb:cid:UUID:f13a0137-0a62-9d4c-b3b9-ca944b5142c5",
	   "MimeType":"video/mp4",
	   "Name":"BigBuckBunny.mp4",
	   "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1"
	}


**HTTP 응답**

성공하면 다음이 반환됩니다.
	HTTP/1.1 204 No Content

## 로케이터와 AccessPolicy 삭제 

**HTTP 요청**


	DELETE https://wamsbayclus001rest-hs.cloudapp.net/api/Locators('nb%3Alid%3AUUID%3Aaf57bdd8-6751-4e84-b403-f3c140444b54') HTTP/1.1
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421662918&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=utmoXXbm9Q7j4tW1yJuMVA3egRiQy5FPygwadkmPeaY%3d
	x-ms-version: 2.8
	Host: wamsbayclus001rest-hs.cloudapp.net

	
**HTTP 응답**

성공하면 다음이 반환됩니다.

	HTTP/1.1 204 No Content 
	...

**HTTP 요청**

	DELETE https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies('nb%3Apid%3AUUID%3Abe0ac48d-af7d-4877-9d60-1805d68bffae') HTTP/1.1
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421662918&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=utmoXXbm9Q7j4tW1yJuMVA3egRiQy5FPygwadkmPeaY%3d
	x-ms-version: 2.8
	Host: wamsbayclus001rest-hs.cloudapp.net

**HTTP 응답**

성공하면 다음이 반환됩니다.

	HTTP/1.1 204 No Content 
	...

 
## <a id="configure_streaming_units"></a>REST API를 통해 스트리밍 단위 구성

Azure 미디어 서비스 작업 시 가장 일반적인 시나리오 중 하나는 클라이언트에 적응 비트 전송률 스트리밍을 제공하는 것입니다. 적응 비트 전송률 스트리밍을 사용하면 현재 네트워크 대역폭, CPU 사용률 및 기타 요인에 따라 비디오가 표시되므로 클라이언트는 더 높거나 낮은 비트 전송률 스트림으로 전환할 수 있습니다. 미디어 서비스가 지원하는 적응 비트 전송률 스트리밍 기술은 HLS(HTTP 라이브 스트리밍), 부드러운 스트리밍, MPEG DASH 및 HDS(Adobe PrimeTime/Access 정식 사용자만 해당)입니다. 

미디어 서비스는 적응 비트 전송률 MP4 또는 부드러운 스트리밍 인코딩 콘텐츠를 미디어 서비스에서 지원되는 스트리밍 형식(MPEG DASH, HLS, 부드러운 스트리밍, HDS)으로 다시 패키지하지 않고도 이런 스트리밍 형식으로 배달할 수 있게 하는 동적 패키징을 제공합니다. 

동적 패키징을 이용하려면 다음을 수행해야 합니다.

- 콘텐츠를 배달하는 출발점이 될 **스트리밍 끝점**에 하나 이상의 스트리밍 단위를 구성합니다(이 섹션에서 설명).
- mezzanine (원본) 파일을 적응 비트 전송률 MP4 파일 또는 적응 비트 전송률 부드러운 스트리밍 파일 집합으로 인코딩하거나 트랜스코딩합니다(인코딩 단계는 이 자습서의 뒷부분에서 설명).  

동적 패키징에서는 단일 저장소 형식으로 파일을 저장하고 비용을 지불하기만 하면 됩니다. 그러면 미디어 서비스가 클라이언트의 요청에 따라 적절한 응답을 빌드 및 제공합니다. 


>[AZURE.NOTE] 가격 정보에 대한 자세한 내용은 [미디어 서비스 가격 정보](http://go.microsoft.com/fwlink/?LinkId=275107)를 참조하세요.

스트리밍 예약 단위의 수를 변경하려면 다음을 수행합니다.
	
### 업데이트하려는 스트리밍 끝점 가져오기

예를 들어, 첫 번째 스트리밍 끝점을 계정으로 가져와보겠습니다(동시에 최대 2개의 스트리밍 끝점을 실행 상태로 설정할 수 있음).

**HTTP 요청**:

	GET https://wamsbayclus001rest-hs.cloudapp.net/api/StreamingEndpoints()?$top=1 HTTP/1.1
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json;odata=verbose
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421466122&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=TiKGEOTporft4pFGU24sSZRZk5GRAWszFXldl5NXAhY%3d
	x-ms-version: 2.8
	Host: wamsbayclus001rest-hs.cloudapp.net

**HTTP 응답**
	
성공하면 다음이 반환됩니다.
	
	HTTP/1.1 200 OK
	. . . 
	
### 스트리밍 끝점 조정
 
**HTTP 요청**:

	POST https://wamsbayclus001rest-hs.cloudapp.net/api/StreamingEndpoints('nb:oid:UUID:cd57670d-cc1c-0f86-16d8-3ad478bf9486')/Scale HTTP/1.1
	Content-Type: application/json;odata=verbose
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json;odata=verbose
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421466122&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=TiKGEOTporft4pFGU24sSZRZk5GRAWszFXldl5NXAhY%3d
	x-ms-version: 2.8
	x-ms-client-request-id: 39f96c93-a4b1-43ce-b97e-b2aaa44ee2dd
	Host: wamsbayclus001rest-hs.cloudapp.net
	
	{"scaleUnits":1}

**HTTP 응답**

	HTTP/1.1 202 Accepted
	Cache-Control: no-cache
	Server: Microsoft-IIS/8.5
	x-ms-client-request-id: 39f96c93-a4b1-43ce-b97e-b2aaa44ee2dd
	request-id: 3c1ba1c7-281c-4b2d-a898-09cb70a3a424
	x-ms-request-id: 3c1ba1c7-281c-4b2d-a898-09cb70a3a424
	operation-id: nb:opid:UUID:1853bcbf-b71f-4ed5-a4c7-a581d4f45ae7
	X-Content-Type-Options: nosniff
	DataServiceVersion: 1.0;
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Fri, 16 Jan 2015 22:16:43 GMT
	Content-Length: 0

	
### <a id="long_running_op_status"></a> 장기 실행 작업의 상태 확인

새 단위를 할당하는 작업은 완료하는 데 20분 정도 걸립니다. 작업 상태를 확인하려면 **작업** 메서드를 사용하여 작업 Id를 지정합니다. 응답에서 작업 Id는 **조정** 요청으로 반환됩니다.

	operation-id: nb:opid:UUID:1853bcbf-b71f-4ed5-a4c7-a581d4f45ae7
 
**HTTP 요청**:
	
	GET https://wamsbayclus001rest-hs.cloudapp.net/api/Operations('nb:opid:UUID:1853bcbf-b71f-4ed5-a4c7-a581d4f45ae7') HTTP/1.1
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json;odata=verbose
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421466122&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=TiKGEOTporft4pFGU24sSZRZk5GRAWszFXldl5NXAhY%3d
	x-ms-version: 2.8
	Host: wamsbayclus001rest-hs.cloudapp.net
	
**HTTP 응답**
	
	HTTP/1.1 200 OK
	Cache-Control: no-cache
	Content-Length: 515
	Content-Type: application/json;odata=verbose;charset=utf-8
	Server: Microsoft-IIS/8.5
	x-ms-client-request-id: 829e1a89-3ec2-4836-a04d-802b5aeff5e8
	request-id: f7ae8a78-af8d-4881-b9ea-ca072cfe0b60
	x-ms-request-id: f7ae8a78-af8d-4881-b9ea-ca072cfe0b60
	X-Content-Type-Options: nosniff
	DataServiceVersion: 1.0;
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Fri, 16 Jan 2015 22:57:39 GMT
	
	{  
	   "d":{  
	      "__metadata":{  
	         "id":"https://wamsbayclus001rest-hs.cloudapp.net/api/Operations('nb%3Aopid%3AUUID%3Acc339c28-6bba-4f7d-bee5-91ea4a0a907e')",
	         "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Operations('nb%3Aopid%3AUUID%3Acc339c28-6bba-4f7d-bee5-91ea4a0a907e')",
	         "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Operation"
	      },
	      "Id":"nb:opid:UUID:cc339c28-6bba-4f7d-bee5-91ea4a0a907e",
	      "State":"Succeeded",
	      "TargetEntityId":"nb:oid:UUID:cd57670d-cc1c-0f86-16d8-3ad478bf9486",
	      "ErrorCode":null,
	      "ErrorMessage":null
	   }
	}


## <a id="encode"></a>원본 파일을 적응 비트 전송률 MP4 파일 집합으로 인코딩

미디어 서비스에 자산을 삽입하고 나면 미디어를 클라이언트에 배달하기 전에 인코딩, 트랜스믹싱, 워터마크 지정 등을 수행할 수 있습니다. 이러한 활동은 높은 성능과 가용성을 보장하기 위해 여러 백그라운드 역할 인스턴스에 대해 예약 및 실행합니다. 이러한 활동을 작업이라고 하며 각 [작업](http://msdn.microsoft.com/library/azure/hh974289.aspx)은 자산 파일에서 실제 작업을 수행하는 원자성 작업으로 구성됩니다. 

앞에서 언급한 대로, Azure 미디어 서비스 작업 시 가장 일반적인 시나리오 중 하나는 적응 비트 전송률 스트리밍을 클라이언트에 제공하는 것입니다. 미디어 서비스는 적응 비트 전송률 MP4 파일 집합을 HLS(HTTP 라이브 스트리밍), 부드러운 스트리밍, MPEG DASH 및 HDS(Adobe PrimeTime/Access 정식 사용자만 해당)입니다. 

동적 패키징을 이용하려면 다음을 수행해야 합니다.

- mezzanine (원본) 파일을 적응 비트 전송률 MP4 파일 또는 적응 비트 전송률 부드러운 스트리밍 파일 집합으로 인코딩하거나 트랜스코딩합니다.  
- 콘텐츠를 배달하는 출발점이 될 스트리밍 끝점에 하나 이상의 스트리밍 단위를 구성합니다. 

다음 섹션에서는 하나의 인코딩 작업을 포함하는 작업을 만드는 방법을 보여줍니다. 이 작업은 **Azure 미디어 인코더**를 사용하여 mezzanine 파일을 적응 비트 전송률 MP4 집합으로 트랜스코딩하도록 지정합니다. 이 섹션에서는 작업 진행 상황을 모니터링하는 방법도 보여줍니다. 작업이 완료되면 자산에 대한 액세스하는 데 필요한 로케이터를 만들 수 있습니다. 

### 미디어 프로세서 가져오기

미디어 서비스에서 미디어 프로세서는 인코딩, 형식 변환, 콘텐츠, 암호화 또는 암호 해독 미디어와 같은 특정 처리 작업을 처리 하는 구성 요소입니다. 이 자습서에 표시된 인코딩 작업에서는 Azure 미디어 인코더를 사용하겠습니다.

다음 코드는 인코더의 id를 요청합니다. 

**HTTP 요청**

	GET https://wamsbayclus001rest-hs.cloudapp.net/api/MediaProcessors()?$filter=Name%20eq%20'Azure%20Media%20Encoder' HTTP/1.1
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=f7f09258-6753-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421675491&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=9hUudHYnATpi5hN3cvTfgw%2bL4N3tL0fdsRnQnm6ZYIU%3d
	x-ms-version: 2.8
	Host: wamsbayclus001rest-hs.cloudapp.net
	

**HTTP 응답**

	HTTP/1.1 200 OK
	Cache-Control: no-cache
	Content-Length: 273
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Server: Microsoft-IIS/8.5
	request-id: 6beb04b4-55a7-480d-8aa8-e5c5d59ffa1f
	x-ms-request-id: 6beb04b4-55a7-480d-8aa8-e5c5d59ffa1f
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Mon, 19 Jan 2015 07:54:09 GMT
	
	{  
	   "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#MediaProcessors",
	   "value":[  
	      {  
	         "Id":"nb:mpid:UUID:1b1da727-93ae-4e46-a8a1-268828765609",
	         "Description":"Azure Media Encoder",
	         "Name":"Azure Media Encoder",
	         "Sku":"",
	         "Vendor":"Microsoft",
	         "Version":"4.4"
	      }
	   ]
	}

### 작업 만들기

각 작업을 수행하려는 처리 유형에 따라 하나 이상의 작업을 가질 수 있습니다. REST API를 통해 다음 두 가지 방법 중 하나로 작업 및 관련된 작업을 만들 수 있습니다. 작업은 작업 엔터티의 작업 탐색 속성을 통해 떠는 OData 일괄 처리를 통해 인라인으로 정의할 수 있습니다. 미디어 서비스 SDK 는 일괄 처리를 사용하지만 이 항목에 있는 코드 예제 가독성의 경우 작업은 인라인으로 정의됩니다. 일괄 처리에 대한 정보는 [Open Data Protocol(OData) 일괄 처리](http://www.odata.org/documentation/odata-version-3-0/batch-processing/)를 참조하세요.

다음 예제에서는 특정 해상도와 품질로 비디오를 인코딩하기 위해 하나의 작업 집합으로 작업을 만들어 게시하는 방법을 보여 줍니다. 다음 설명서 섹션은 Azure 미디어 프로세서에서 지원하는 모든 [작업 미리 설정](http://msdn.microsoft.com/library/azure/dn619392.aspx) 목록을 포함합니다.  

**HTTP 요청**
	
	POST https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs HTTP/1.1
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Content-Type: application/json
	Accept: application/json;odata=verbose
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421675491&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=9hUudHYnATpi5hN3cvTfgw%2bL4N3tL0fdsRnQnm6ZYIU%3d
	x-ms-version: 2.8
	Host: wamsbayclus001rest-hs.cloudapp.net
	Content-Length: 482
	
	{  
	   "Name":"NewTestJob",
	   "InputMediaAssets":[  
	      {  
	         "__metadata":{  
	            "uri":"https://wamsbayclus001rest-hs.net/api/Assets('nb%3Acid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')"
	         }
	      }
	   ],
	   "Tasks":[  
	      {  
	         "Configuration":"H264 Adaptive Bitrate MP4 Set 720p",
	         "MediaProcessorId":"nb:mpid:UUID:1b1da727-93ae-4e46-a8a1-268828765609",
	         "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset>
				<outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"
	      }
	   ]
	}

**HTTP 응답**

성공하면 다음 응답이 반환됩니다.

	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Content-Length: 1215
	Content-Type: application/json;odata=verbose;charset=utf-8
	Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')
	Server: Microsoft-IIS/8.5
	request-id: 532ac1ec-a475-4dce-b2d5-7c8ce94ac87c
	x-ms-request-id: 532ac1ec-a475-4dce-b2d5-7c8ce94ac87c
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Mon, 19 Jan 2015 08:04:35 GMT
		
	{  
	   "d":{  
	      "__metadata":{  
	         "id":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')",
	         "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')",
	         "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Job"
	      },
	      "Tasks":{  
	         "__deferred":{  
	            "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/Tasks"
	         }
	      },
	      "OutputMediaAssets":{  
	         "__deferred":{  
	            "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/OutputMediaAssets"
	         }
	      },
	      "InputMediaAssets":{  
	         "__deferred":{  
	            "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')/InputMediaAssets"
	         }
	      },
	      "Id":"nb:jid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c",
	      "Name":"NewTestJob",
	      "Created":"2015-01-19T08:04:34.3287057Z",
	      "LastModified":"2015-01-19T08:04:34.3287057Z",
	      "EndTime":null,
	      "Priority":0,
	      "RunningDuration":0,
	      "StartTime":null,
	      "State":0,
	      "TemplateId":null,
	      "JobNotificationSubscriptions":{  
	         "__metadata":{  
	            "type":"Collection(Microsoft.Cloud.Media.Vod.Rest.Data.Models.JobNotificationSubscription)"
	         },
	         "results":[  
	
	         ]
	      }
	   }
	}


모든 작업 요청에는 몇 가지 중요한 사항이 있습니다.

- TaskBody 속성은 리터럴 XML을 사용하여 작업에서 사용될 입력이나 출력 수를 정의해야 합니다. 작업 항목은 해당 XML에 대한 XML 스키마 정의를 포함합니다.
- TaskBody 정의에서 <inputAsset> 및 <outputAsset>에 대한 각각의 내부 값은 JobInputAsset(value) 또는 JobOutputAsset(value)으로 설정되어야 합니다.
- 작업 출력 자산은 여러 개일 수 있습니다. 작업에서 하나의 JobOutputAsset(x)을 작업 출력으로 한 번만 사용할 수 있습니다.
- JobInputAsset 또는 JobOutputAsset을 작업의 입력 자산으로 지정할 수 있습니다.
- 작업은 주기를 형성해서는 안됩니다.
- JobInputAsset 또는 JobOutputAsset에 전달하는 값 매개변수는 자산에 대한 인덱스 값을 나타냅니다. 실제 자산은 작업 엔터티 정의에 있는 InputMediaAssets 및 OutputMediaAssets 탐색 속성에 정의됩니다. 

>[AZURE.NOTE] 미디어 서비스는 OData v 3를 기반으로 하기 때문에 InputMediaAssets 및 OutputMediaAssets 탐색 속성 컬렉션에 있는 개별 자산은 "__metadata : uri" 이름 값 쌍으로 참조됩니다. 

- InputMediaAsset은 미디어 서비스에서 만든 하나 이상의 자산에 매핑됩니다. OutputMediaAsset은 시스템에 의해 생성됩니다. 기존 자산을 참조하지 않습니다.
- OutputMediaAsset은 assetName 특성을 사용하여 명명할 수 있습니다. 이 특성이 없을 경우 OutputMediaAsset의 이름은 <outputAsset> 요소의 내부 텍스트 값이 작업 이름 값 또는 작업 Id 값(이름 속성이 정의되어 있지 않은 경우)의 접미사를 갖는 어떤 것이든 가능합니다. 예를 들어, assetName에 대한 값을 "Sample"로 설정하는 경우 OutputMediaAsset 이름 속성은 "Sample"로 설정됩니다. 하지만 assetName에 대한 값은 설정하지 않았지만 작업 이름을 "NewJob"으로 설정한 경우 OutputMediaAsset 이름은 "JobOutputAsset(값)_NewJob"이 됩니다. 

	The following example shows how to set the assetName attribute:
	
		"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"CustomOutputAssetName\">JobOutputAsset(0)</outputAsset></taskBody>"


- 작업 체인을 사용하려면,

	- 작업에 작업이 2개 이상 있어야 합니다.
	- 작업에서 입력이 다른 작업의 출력인 작업이 하나 이상 있어야 합니다.

자세한 내용은 [미디어 서비스 REST API를 사용하여 인코딩 작업 만들기](http://msdn.microsoft.com/library/azure/jj129574.aspx)를 참조하세요.

### 처리 진행 상태 모니터링

다음 예제와 같이 State 속성을 사용하여 작업 상태를 검색할 수 있습니다. 

**HTTP 요청**

	GET https://wamsbayclus001rest-hs.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/State HTTP/1.1
	Content-Type: application/json;odata=verbose
	Accept: application/json;odata=verbose
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	x-ms-version: 2.8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=youraccountname&urn%3aSubscriptionId=zf84471d-2233-4e75-aa09-010f0fc0cf5b&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1336908022&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=RYXOraO6Z%2f7l9whWZQN%2bypeijgHwIk8XyikA01Kx1%2bk%3d
	Host: wamsbayclus001rest-hs.net
	Content-Length: 0


**HTTP 응답**

성공하면 다음 응답이 반환됩니다.

	HTTP/1.1 200 OK
	Cache-Control: no-cache
	Content-Length: 17
	Content-Type: application/json;odata=verbose;charset=utf-8
	Server: Microsoft-IIS/7.5
	x-ms-request-id: 01324d81-18e2-4493-a3e5-c6186209f0c8
	X-Content-Type-Options: nosniff
	DataServiceVersion: 1.0;
	X-AspNet-Version: 4.0.30319
	X-Powered-By: ASP.NET
	Date: Sun, 13 May 2012 05:16:53 GMT
	
	{"d":{"State":2}}


### 작업 취소

미디어 서비스를 사용하면 CancelJob 함수를 통해 실행 중인 작업을 취소할 수 있습니다. 이 호출은 작업 상태가 취소됨, 취소 중, 오류 또는 완료일 때 작업을 취소하려고 하면 400 오류 코드를 반환합니다.

다음 예제에서는 CancelJob을 호출하는 방법을 보여줍니다.


**HTTP 요청**


	GET https://wamsbayclus001rest-hs.net/API/CancelJob?jobid='nb%3ajid%3aUUID%3a71d2dd33-efdf-ec43-8ea1-136a110bd42c' HTTP/1.1
	Content-Type: application/json;odata=verbose
	Accept: application/json;odata=verbose
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	x-ms-version: 2.2
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=youraccountname&urn%3aSubscriptionId=2f84471d-b1ae-4e75-aa09-010f0fc0cf5b&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1336908753&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=kolAgnFfbQIeRv4lWxKSFa4USyjWXRm15Kd%2bNd5g8eA%3d
	Host: wamsbayclus001rest-hs.net


성공하는 경우 메시지 본문 없이 204 응답 코드가 반환됩니다.

>[AZURE.NOTE] 작업 id(일반적으로 nb:jid:UUID: somevalue)가 CancelJob에 매개변수로 전달되는 경우 작업 id를 URL 인코딩해야 합니다.


### 출력 자산 가져오기 

다음 섹션에서는 작업의 출력 자산(인코딩된 자산)에 대한 전달 정책을 구성해보겠습니다. 다음 코드에서는 출력 자산 id를 요청하는 방법을 보여줍니다. 


**HTTP 요청**

	GET https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/OutputMediaAssets() HTTP/1.1
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	User-Agent: Microsoft ADO.NET Data Services
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421675491&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=9hUudHYnATpi5hN3cvTfgw%2bL4N3tL0fdsRnQnm6ZYIU%3d
	x-ms-version: 2.8
	Host: wamsbayclus001rest-hs.cloudapp.net
	

**HTTP 응답**

	HTTP/1.1 200 OK
	Cache-Control: no-cache
	Content-Length: 445
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Server: Microsoft-IIS/8.5
	request-id: 73cd605d-066a-46f1-8358-f4bd25a9220a
	x-ms-request-id: 73cd605d-066a-46f1-8358-f4bd25a9220a
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Mon, 19 Jan 2015 08:28:13 GMT
		
	{  
	   "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Assets",
	   "value":[  
	      {  
	         "Id":"nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c",
	         "State":0,
	         "Created":"2015-01-19T07:52:15.603",
	         "LastModified":"2015-01-19T07:52:15.603",
	         "AlternateId":null,
	         "Name":"Multibitrate MP4s",
	         "Options":0,
	         "Uri":"https://storagetestaccount001.blob.core.windows.net/asset-71d2dd33-efdf-ec43-8ea1-136a110bd42c",
	         "StorageAccountName":"storagetestaccount001"
	      }
	   ]
	}


## <a id="configure_delivery_method"></a>인코딩된 자산에 대한 배달 정책 구성

미디어 서비스 콘텐츠 배달 워크플로의 단계 중 하나는 자산 배달 정책 구성입니다. 자산 배달 정책 구성에 포함되는 사항으로는 자산 배달에 사용할 수 있는 프로토콜(예: MPEG DASH, HLS, HDS, 부드러운 스트리밍 또는 모두), 동적 자산 암호화를 사용할지 여부 및 방법(봉투(Envelope) 또는 일반 암호화) 등이 있습니다. 

다음 **AssetDeliveryPolicies** HTTP 요청은 동적 암호화(AssetDeliveryPolicyType은 다음 값 중 하나일 수 있음: None = 0, Blocked = 1, NoDynamicEncryption = 2, DynamicEnvelopeEncryption = 3, DynamicCommonEncryption = 4)를 적용하지 않도록 그리고 다음 프로토콜로 스트림을 전달하지 않도록 지정합니다.  MPEG DASH, HLS 및 부드러운 스트리밍 프로토콜(AssetDeliveryProtocol은 다음 값 조합일 수 있음: None = 0,        SmoothStreaming = 1, Dash = 2, HLS = 4, Hds = 8, All = 65535). 


### AssetDeliveryPolicies 만들기


**HTTP 요청**
		
	POST https://wamsbayclus001rest-hs.cloudapp.net/api/AssetDeliveryPolicies HTTP/1.1
	Content-Type: application/json
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=f7f09258-6753-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421679198&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=aUvBcDwRAFk1JLxceWu%2bf9dVrCZM7PrTRbZd0TtoKvU%3d
	x-ms-version: 2.8
	Host: wamsbayclus001rest-hs.cloudapp.net
	Content-Length: 83
	
	{"Name":"Clear Policy", "AssetDeliveryPolicyType":"2","AssetDeliveryProtocol":"7"} 


**HTTP 응답**
	
	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Content-Length: 361
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Location: https://wamsbayclus001rest-hs.cloudapp.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3Aef97ae36-b898-4b8a-b427-7819ee726276')
	Server: Microsoft-IIS/8.5
	request-id: 7391a4b2-995d-4fc5-aca5-a398786ea38e
	x-ms-request-id: 7391a4b2-995d-4fc5-aca5-a398786ea38e
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Mon, 19 Jan 2015 09:13:18 GMT

	{  
	   "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#AssetDeliveryPolicies/@Element",
	   "Id":"nb:adpid:UUID:ef97ae36-b898-4b8a-b427-7819ee726276",
	   "Name":"Clear Policy",
	   "AssetDeliveryProtocol":7,
	   "AssetDeliveryPolicyType":2,
	   "AssetDeliveryConfiguration":null,
	   "Created":"2015-01-19T09:13:18.911615Z",
	   "LastModified":"2015-01-19T09:13:18.911615Z"
	}
    

### 자산 배달 정책을 자산으로 연결

다음 HTTP 요청은 지정된 배달 정책을 지정된 자산에 연결합니다.  

**HTTP 요청**

	POST https://wamsbayclus001rest-hs.cloudapp.net/api/Assets('nb%3Acid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/$links/DeliveryPolicies HTTP/1.1
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Content-Type: application/json
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amstestaccount001&urn%3aSubscriptionId=z7f09258-2233-4ca2-b1ae-193798e2c9d8&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1421679198&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=aUvBcDwRAFk1JLxceWu%2bf9dVrCZM7PrTRbZd0TtoKvU%3d
	x-ms-version: 2.8
	Host: wamsbayclus001rest-hs.cloudapp.net
	Content-Length: 140
	
	{ "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3Aef97ae36-b898-4b8a-b427-7819ee726276')" }


**HTTP 응답**

	HTTP/1.1 204 No Content
    . . . 


## <a id="publish_get_urls"></a>REST API를 통해 자산을 게시하고 스트리밍 기능 및 URL 점진적 다운로드를 사용

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

이 섹션에서는 자산을 "게시"하기 위해 다음 작업을 수행하는 방법을 보여줍니다.  

- 읽기 권한이 포함된 AccessPolicy 만들기 
- 콘텐츠를 다운로드할 SAS URL 만들기 
- 콘텐츠 스트리밍을 위한 원본 URL 만들기 

### 읽기 권한이 포함된 AccessPolicy 만들기

미디어 콘텐츠를 다운로드하거나 스트리밍하기 전에 먼저 읽기 권한이 포함된 AccessPolicy를 정의하고 클라이언트에 대해 사용하도록 설정하려는 배달 메커니즘 유형을 지정하는 적절한 로케이터 엔터티를 만듭니다. 사용할 수 있는 속성에 대한 자세한 내용은 [AccessPolicy 엔터티 속성](https://msdn.microsoft.com/library/azure/hh974297.aspx#accesspolicy_properties)을 참조하세요.

다음 예제에서는 지정된 자산에 대한 읽기 권한의 AccessPolicy를 지정하는 방법을 보여줍니다.

	POST https://wamsbayclus001rest-hs.net/API/AccessPolicies HTTP/1.1
	Content-Type: application/json
	Accept: application/json
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	x-ms-version: 2.8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=youraccountname&urn%3aSubscriptionId=2f84471d-b1ae-4e75-aa09-010f0fc0cf5b&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1337067658&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=dithjGvlXR9HlyAf5DE99N5OCYkPAxsHIcsTSjm9%2fVE%3d
	Host: wamsbayclus001rest-hs.net
	Content-Length: 74
	Expect: 100-continue
	
	{"Name": "DownloadPolicy", "DurationInMinutes" : "300", "Permissions" : 1}

성공하면 사용자가 만든 AccessPolicy 엔터티를 설명하는 201 성공 코드가 반환됩니다. 이제 AccessPolicy Id를 로케이터 엔터티를 만들기 위해 배달하려는 파일이 포함된 자산(예: 출력 자산)의 자산 Id와 함께 사용하게 됩니다.

>[AZURE.NOTE]
이 기본 워크플로는 자산을 통합할 때 파일을 업로드하는 것과 같습니다(이 항목의 앞부분에서 설명함). 또한 파일을 업로드하는 것과 마찬가지로 사용자(또는 클라이언트)가 파일에 즉시 액세스해야 하는 경우 StartTime 값을 현재 시간에서 5분 전으로 설정해야 합니다. 이 동작은 클라이언트와 미디어 서비스 사이에 시간차가 있을 수 있기 때문에 필요합니다. StartTime 값은 다음 DateTime 형식 이어야 합니다. YYYY-MM-DDTHH:mm:ssZ (예: "2014-05-23T17:53:50Z").


### 콘텐츠를 다운로드할 SAS URL 만들기 

다음 코드는 이전에 만들어서 업로드한 미디어 파일을 다운로드하는 데 사용할 수 있는 URL을 가져오는 방법을 보여줍니다. AccessPolicy는 읽기 권한을 설정하고 로케이터 경로는 SAS 다운로드 URL을 참조합니다.

	POST https://wamsbayclus001rest-hs.net/API/Locators HTTP/1.1
	Content-Type: application/json
	Accept: application/json
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	x-ms-version: 2.8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=youraccountname&urn%3aSubscriptionId=zf84471d-b1ae-2233-aa09-010f0fc0cf5b&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1337067658&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=dithjGvlXR9HlyAf5DE99N5OCYkPAxsHIcsTSjm9%2fVE%3d
	Host: wamsbayclus001rest-hs.net
	Content-Length: 182
	Expect: 100-continue
	
	{"AccessPolicyId": "nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8", "AssetId" : "nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c", "StartTime" : "2014-05-17T16:45:53", "Type":1}

성공하면 다음 응답이 반환됩니다.

	
	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Content-Length: 1150
	Content-Type: application/json;odata=verbose;charset=utf-8
	Location: https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')
	Server: Microsoft-IIS/7.5
	x-ms-request-id: 8cfd8556-3064-416a-97f2-67d9e35f0911
	X-Content-Type-Options: nosniff
	DataServiceVersion: 1.0;
	X-AspNet-Version: 4.0.30319
	X-Powered-By: ASP.NET
	Date: Mon, 14 May 2012 21:41:32 GMT
		
	{  
	   "d":{  
	      "__metadata":{  
	         "id":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')",
	         "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')",
	         "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Locator"
	      },
	      "AccessPolicy":{  
	         "__deferred":{  
	            "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')/AccessPolicy"
	         }
	      },
	      "Asset":{  
	         "__deferred":{  
	            "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/Asset"
	         }
	      },
	      "Id":"nb:lid:UUID:8e5a821d-2194-4d00-8884-adf979856874",
	      "ExpirationDateTime":"\/Date(1337049393000)\/",
	      "Type":1,
	      "Path":"https://storagetestaccount001.blob.core.windows.net/asset-71d2dd33-efdf-ec43-8ea1-136a110bd42c?st=2012-05-14T21%3A36%3A33Z&se=2012-05-15T02%3A36%3A33Z&sr=c&si=8e5a821d-2194-4d00-8884-adf979856874&sig=y75dViDpC5V8WutrXM%2B%2FGpR3uOtqmlISiNlHU1YUBOg%3D",
	      "AccessPolicyId":"nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8",
	      "AssetId":"nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c",
	      "StartTime":"\/Date(1337031393000)\/"
	   }
	}


반환된 **경로** 속성은 SAS URL을 포함합니다.

>[AZURE.NOTE]
저장소가 암호화된 콘텐츠를 다운로드하는 경우 렌더링하기 전에 수동으로 암호를 해독하거나 처리 작업에서 저장소 암호 해독 미디어 프로세서를 사용하여 암호화되지 않은 상태로 처리된 파일을 OutputAsset으로 출력한 다음 해당 자산에서 다운로드해야 있습니다. 처리에 대한 자세한 내용은 미디어 서비스 REST API를 사용하여 인코딩 작업 만들기를 참조하세요. 또한 SAS URL 로케이터를 만들고 나면 업데이트할 수 없습니다. 예를 들어, 업데이트된 StartTime 값으로 동일한 로케이터를 다시 사용할 수 없습니다. SAS URL을 만드는 방식 때문입니다. 로케이터가 만료된 후 다운로드할 자산에 액세스하려면 새 StartTime으로 새 로케이터를 만들어야 합니다.

### 파일 다운로드

AccessPolicy와 로케이터를 설정했으면 Azure 저장소 REST API를 사용하여 파일을 다운로드할 수 있습니다.  

>[AZURE.NOTE] 다운로드하려는 파일의 파일 이름을 이전 섹션에서 받은 로케이터 **경로** 값에 추가해야 합니다. 예: https://storagetestaccount001.blob.core.windows.net/asset-e7b02da4-5a69-40e7-a8db-e8f4f697aac0/BigBuckBunny.mp4? . . . 

Azure 저장소 Blob 작업에 대한 자세한 내용은 [Blob 서비스 REST API](http://msdn.microsoft.com/library/azure/dd135733.aspx)를 참조하세요.

이전에 수행한 인코딩 작업(적응 MP4 집합으로 인코딩)의 결과로 점진적으로 다운로드할 수 있는 여러 MP4 파일이 있습니다. 예를 들면 다음과 같습니다.    
	
	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
	
	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
	
	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
	
	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
	
	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1500kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
	
	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1000kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
	
	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z
	
	https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_56kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z


### 콘텐츠 스트리밍을 위한 스트리밍 URL 만들기


다음 코드에서는 스트리밍 URL 로케이터를 만드는 방법을 보여줍니다.

	POST https://wamsbayclus001rest-hs/API/Locators HTTP/1.1
	Content-Type: application/json
	Accept: application/json
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	x-ms-version: 2.8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=youraccountname&urn%3aSubscriptionId=2f84471d-b1ae-4e75-aa09-010f0fc0cf5b&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1337067658&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=dithjGvlXR9HlyAf5DE99N5OCYkPAxsHIcsTSjm9%2fVE%3d
	Host: wamsbayclus001rest-hs
	Content-Length: 182
	Expect: 100-continue
	
	{"AccessPolicyId": "nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8", "AssetId" : "nb:cid:UUID:eb5540a2-116e-4d36-b084-7e9958f7f3c3", "StartTime" : "2014-05-17T16:45:53",, "Type":2}

성공하면 다음 응답이 반환됩니다.

	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Content-Length: 981
	Content-Type: application/json;odata=verbose;charset=utf-8
	Location: https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')
	Server: Microsoft-IIS/7.5
	x-ms-request-id: 2eac4158-fc78-4fa2-81ee-c9f582dc385f
	X-Content-Type-Options: nosniff
	DataServiceVersion: 1.0;
	X-AspNet-Version: 4.0.30319
	X-Powered-By: ASP.NET
	Date: Mon, 14 May 2012 21:41:39 GMT
		
	{  
	   "d":{  
	      "__metadata":{  
	         "id":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')",
	         "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')",
	         "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Locator"
	      },
	      "AccessPolicy":{  
	         "__deferred":{  
	            "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')/AccessPolicy"
	         }
	      },
	      "Asset":{  
	         "__deferred":{  
	            "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')/Asset"
	         }
	      },
	      "Id":"nb:lid:UUID:52034bf6-dfae-4d83-aad3-3bd87dcb1a5d",
	      "ExpirationDateTime":"\/Date(1337049395000)\/",
	      "Type":2,
	      "Path":"http://wamsbayclus001rest-hs.net/52034bf6-dfae-4d83-aad3-3bd87dcb1a5d/",
	      "AccessPolicyId":"nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8",
	      "AssetId":"nb:cid:UUID:eb5540a2-116e-4d36-b084-7e9958f7f3c3",
	      "StartTime":"\/Date(1337031395000)\/"
	   }
	}

스트리밍 미디어 플레이어에서 부드러운 스트리밍 원본 URL을 스트리밍하려면 "/매니페스트" 다음에 부드러운 스트리밍 매니페스트 파일의 이름이 포함된 경로 속성을 추가해야 합니다.

	http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest

HLS를 스트리밍하려면 "/매니페스트" 뒤에 추가(format=m3u8-aapl)해야 합니다.

	http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=m3u8-aapl)

MPEG DASH를 스트리밍하려면 "/매니페스트" 뒤에 추가(format=mpd-time-csf)해야 합니다.

	http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=mpd-time-csf)


## <a id="play"></a>콘텐츠 재생  

SAS URL을 만들었으면 URL을 브라우저(예: IE, Chrome, Safari)에 붙여넣어서 MP4 파일을 점진적으로 다운로드할 수 있습니다.

적응 스트리밍 URL을 테스트하려면 다음 플레이어를 사용합니다.

부드러운 스트리밍을 테스트하려면 [http://amsplayer.azurewebsites.net/](http://amsplayer.azurewebsites.net/) 또는 [http://smf.cloudapp.net/healthmonitor](http://smf.cloudapp.net/healthmonitor)를 사용합니다.

MPEG DASH를 테스트하려면 [http://dashif.org](http://dashif.org/reference/players/javascript/)를 사용합니다.

HLS를 테스트하려면 iOS 또는 Safari 장치나 [3ivx-hls-player](http://apps.microsoft.com/windows/app/3ivx-hls-player/f79ce7d0-2993-4658-bc4e-83dc182a0614)를 사용합니다. 



## 추가 리소스
- <a href="http://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-101-Get-your-video-online-now-">Azure 미디어 서비스 101 - 지금 온라인으로 비디오 받기!</a>
- <a href="http://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-102-Dynamic-Packaging-and-Mobile-Devices">Azure 미디어 서비스 102 - 동적 패키징 및 모바일 장치</a>


<!-- Anchors. -->


<!-- URLs. -->
  [웹 플랫폼 설치 관리자]: http://go.microsoft.com/fwlink/?linkid=255386
  [관리 포털]: http://manage.windowsazure.com/



<!--HONumber=52-->