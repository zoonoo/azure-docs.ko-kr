<properties 
	pageTitle="Node.js에서 Blob 저장소를 사용하는 방법 | Microsoft Azure" 
	description="Azure Blob 서비스를 사용하여 Blob 콘텐츠를 업로드, 다운로드, 나열 및 삭제하는 방법을 알아봅니다. 샘플은 Node.js로 작성되었습니다." 
	services="storage" 
	documentationCenter="nodejs" 
	authors="MikeWasson" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="03/11/2015" 
	ms.author="mwasson"/>



# Node.js에서 Blob 저장소를 사용하는 방법

[AZURE.INCLUDE [storage-selector-blob-include](../includes/storage-selector-blob-include.md)]

## 개요

이 가이드에서는 Azure Blob 서비스를 사용하여
일반 시나리오를 수행하는 방법을 보여 줍니다. 샘플은
Node.js API를 사용하여 작성되었습니다. 여기서 다루는 시나리오에는 Blob **업로드**, **나열**,
**다운로드** 및 **삭제**가 포함됩니다.

[AZURE.INCLUDE [storage-blob-concepts-include](../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../includes/storage-create-account-include.md)]

## Node.js 응용 프로그램 만들기

빈 Node.js 응용 프로그램을 만듭니다. Node.js 응용 프로그램을 만드는 방법에 대한 자세한 내용은 [Node.js 응용 프로그램 빌드 및 Azure 웹 사이트에 배포], [Node.js 클라우드 서비스][Node.js 클라우드 서비스](Windows PowerShell 사용) 또는 [WebMatrix를 사용하는 웹 사이트]를 참조하세요.

## 저장소에 액세스하도록 응용 프로그램 구성

Azure 저장소를 사용하려면 저장소 REST 서비스와 통신하는 편리한 라이브러리 집합이 포함되어 있는 Node.js용 Azure 저장소 SDK가 필요합니다.

### NPM(Node Package Manager)을 사용하여 패키지 가져오기

1.  **PowerShell**(Windows), **Terminal**(Mac), **Bash**(Unix) 등과 같은 명령줄 인터페이스를 사용하여 응용 프로그램 예제를 만든 폴더로 이동합니다.

2.  명령 창에 **npm install azure-storage**를 입력합니다. 그러면
    다음과 같이 출력됩니다.

        azure-storage@0.1.0 node_modules\azure-storage
		├── extend@1.2.1
		├── xmlbuilder@0.4.3
		├── mime@1.2.11
		├── underscore@1.4.4
		├── validator@3.1.0
		├── node-uuid@1.4.1
		├── xml2js@0.2.7 (sax@0.5.2)
		└── request@2.27.0 (json-stringify-safe@5.0.0, tunnel-agent@0.3.0, aws-sign@0.3.0, forever-agent@0.5.2, qs@0.6.6, oauth-sign@0.3.0, cookie-jar@0.3.0, hawk@1.0.0, form-data@0.1.3, http-signature@0.10.0)

3.  **ls** 명령을 수동으로 실행하여
    **node\_modules** 폴더가 만들어졌는지 확인할 수 있습니다. 이 폴더에서 서비스 버스 토픽에 액세스하는 데 필요한 라이브러리가 포함된
    **azure-storage** 패키지를
    찾습니다.

### 패키지 가져오기

메모장 또는 다른 텍스트 편집기를 사용하여 저장소를 사용할 응용 프로그램의
**server.js** 파일 맨 위에 다음을 추가합니다.

    var azure = require('azure-storage');

## Azure 저장소 연결 설정

Azure 모듈은 `AZURE_STORAGE_ACCOUNT` 및 `AZURE_STORAGE_ACCESS_KEY` 또는 `AZURE_STORAGE_CONNECTION_STRING` 환경 변수를 읽고 Azure 저장소 계정에 연결하는 데 필요한 정보를 확인합니다. 이러한 환경 변수가 설정되지 않은 경우 **createBlobService**를 호출할 때 계정 정보를 지정해야 합니다.

Azure 웹 사이트의 관리 포털에서 환경 변수를 설정하는 방법에 대한 예제는 [Node.js 웹 응용 프로그램 및 저장소]를 참조하세요.

## 방법: 컨테이너 만들기

**BlobService** 개체를 통해 컨테이너 및 Blob에 대한 작업을 수행할 수 있습니다. 다음 코드는 **BlobService** 개체를 만듭니다. **server.js**의 맨 위에 다음을 추가합니다.

    var blobSvc = azure.createBlobService();

> [AZURE.NOTE] **createBlobServiceAnonymous**를 사용하고 호스트 주소를 제공하여 익명으로 Blob에 액세스할 수 있습니다. 예를 들어 `var blobSvc = azure.createBlobServiceAnonymous('https://myblob.blob.core.windows.net/');`.

모든 Blob은 컨테이너에 있습니다. 새 컨테이너를 만들려면 **createContainerIfNotExists**를 사용합니다. 다음 코드는 'mycontainer'라는 이름의 새 컨테이너를 만듭니다.

	blobSvc.createContainerIfNotExists('mycontainer', function(error, result, response){
      if(!error){
        // Container exists and allows 
        // anonymous read access to blob 
        // content and metadata within this container
      }
	});

컨테이너가 만들어지면 `result`는 true가 됩니다. 컨테이너가 이미 있을 경우 `result`는 false가 됩니다. `response`에는 컨테이너의 [ETag](http://en.wikipedia.org/wiki/HTTP_ETag) 정보와 같은 작업 관련 정보가 포함됩니다.

### 컨테이너 보안

기본적으로 새 컨테이너는 개인 컨테이너이며 익명으로 액세스할 수 없습니다. 컨테이너를 공용으로 만들어 익명으로 액세스할 수 있게 하려면 컨테이너의 액세스 수준을 **blob** 또는 **container**로 설정할 수 있습니다.

* **blob** - 이 컨테이너 내의 Blob 콘텐츠와 메타데이터에 대한 익명 읽기 권한은 허용하지만 컨테이너 내의 모든 Blob 나열과 같은 컨테이너 메타데이터에 대한 익명 읽기 권한은 허용하지 않습니다.

* **container** - Blob 콘텐츠와 메타데이터뿐만 아니라 컨테이너 메타데이터에 대한 익명 읽기 권한을 허용합니다.

다음 예제에서는 액세스 수준을 **Blob**으로 설정하는 방법을 보여 줍니다. 

    blobSvc.createContainerIfNotExists('mycontainer', {publicAccessLevel : 'blob'}, function(error, result, response){
      if(!error){
        // Container exists and is private
      }
	});

또는 **setContainerAcl**로 액세스 수준을 지정하여 컨테이너의 액세스 수준을 수정할 수 있습니다. 다음 예제에서는 액세스 수준을 container로 변경합니다.

    blobSvc.setContainerAcl('mycontainer', null /* signedIdentifiers */, 'container' /* publicAccessLevel*/, function(error, result, response){
	  if(!error){
		// Container access level set to 'container'
	  }
	});

result 값에는 컨테이너의 현재 **ETag**를 비롯한 작업 관련 정보가 포함됩니다.

### 필터

**BlobService**를 사용하여 수행되는 작업에 선택적 필터링 작업을 적용할 수 있습니다. 필터링 작업은 로깅, 자동으로 다시 시도 등을 포함할 수 있습니다. 필터는 서명을 사용하여 메서드를 구현하는 개체입니다.

		function handle (requestOptions, next)

요청 옵션에 대한 전처리를 수행한 후 메서드는 다음 서명을 사용하여 콜백을 전달하는 "next"를 호출해야 합니다.

		function (returnObject, finalCallback, next)

이 콜백에서 returnObject(서버에 요청 응답 반환)를 처리한 후 콜백은 next(있는 경우)를 호출하여 다른 필터를 계속 처리하거나 finalCallback을 호출하여 서비스 호출을 종료해야 합니다.

Node.js용 Azure SDK에는 재시도 논리를 구현하는 두 필터 **ExponentialRetryPolicyFilter** 및 **LinearRetryPolicyFilter**가 포함되어 있습니다. 다음 코드는 **ExponentialRetryPolicyFilter**를 사용하는 **BlobService** 개체를 만듭니다.

	var retryOperations = new azure.ExponentialRetryPolicyFilter();
	var blobSvc = azure.createBlobService().withFilter(retryOperations);

## 방법: 컨테이너에 Blob 업로드

Blob은 블록 기반 또는 페이지 기반일 수 있습니다. 블록 Blob을 사용하면 큰 데이터를 더 효율적으로 업로드할 수 있고, 페이지 Blob은 읽기/쓰기 작업용으로 최적화되어 있습니다. 자세한 내용은 [블록 Blob 및 페이지 Blob 이해](http://msdn.microsoft.com/library/azure/ee691964.aspx)를 참조하세요.

### 블록 Blob

데이터를 블록 Blob으로 업로드하려면 다음을 사용합니다.

* **createBlockBlobFromLocalFile** - 새 블록 Blob을 만들고 파일 내용을 업로드합니다.

* **createBlockBlobFromStream** - 새 블록 Blob을 만들고 스트림의 내용을 업로드합니다.

* **createBlockBlobFromText** - 새 블록 Blob을 만들고 문자열의 내용을 업로드합니다.

* **createWriteStreamToBlockBlob** - 블록 Blob에 쓰기 스트림을 제공합니다.

다음 예제에서는 **test.txt** 파일의 내용을 **myblob**에 업로드합니다.

	blobSvc.createBlockBlobFromLocalFile('mycontainer', 'myblob', 'test.txt', function(error, result, response){
	  if(!error){
	    // file uploaded
	  }
	});

이 메서드에서 반환된 `result`에는 Blob의 **ETag**와 같은 작업 관련 정보가 포함됩니다.

### 페이지 Blob

데이터를 페이지 Blob으로 업로드하려면 다음을 사용합니다.

* **createPageBlob** - 특정 길이의 새 페이지 Blob을 만듭니다.

* **createPageBlobFromLocalFile** - 새 페이지 Blob을 만들고 파일 내용을 업로드합니다.

* **createPageBlobFromStream** - 새 페이지 Blob을 만들고 스트림의 내용을 업로드합니다.

* **createWriteStreamToExistingPageBlob** - 기존 페이지 Blob에 쓰기 스트림을 제공합니다.

* **createWriteStreamToNewPageBlob** - 새 Blob을 만든 다음 Blob에 쓸 스트림을 제공합니다.

다음 예제에서는 **test.txt** 파일의 내용을 **mypageblob**에 업로드합니다.

	blobSvc.createPageBlobFromLocalFile('mycontainer', 'mypageblob', 'test.txt', function(error, result, response){
	  if(!error){
	    // file uploaded
	  }
	});

> [AZURE.NOTE] 페이지 Blob은 512바이트의 'pages'로 구성됩니다. 크기가 512의 배수가 아닌 경우에는 데이터를 업로드할 때 오류가 표시될 수 있습니다.

## 방법: 컨테이너의 Blob 나열

컨테이너의 Blob을 나열하려면 **listBlobsSegmented** 메서드를 사용합니다. 특정 접두사가 있는 Blob을 반환하려면 **listBlobsSegmentedWithPrefix**를 사용합니다.

    blobSvc.listBlobsSegmented('mycontainer', null, function(error, result, response){
      if(!error){
        // result contains the entries
	  }
	});

 `result`에는 각 Blob을 설명하는 개체의 배열인 `entries` 컬렉션이 포함됩니다. Blob을 모두 반환할 수 없는 경우에는 `result`에서 추가 항목 검색에 두 번째 매개 변수로 사용할 수 있는 `continuationToken`을 제공합니다.

## 방법: Blob 다운로드

Blob에서 데이터를 다운로드하려면 다음을 사용합니다.

* **getBlobToFile** - Blob 내용을 파일에 씁니다.

* **getBlobToStream** - Blob 내용을 스트림에 씁니다.

* **getBlobToText** - Blob 내용을 문자열에 씁니다.

* **createReadStream** - Blob에서 읽은 스트림을 제공합니다.

다음 예제에서는 **getBlobToStream**을 사용하여 **myblob** Blob의 내용을 다운로드하고 다음 스트림을 사용하여 **output.txt** 파일에 저장하는 방법을 보여 줍니다.

    var fs = require('fs');
	blobSvc.getBlobToStream('mycontainer', 'myblob', fs.createWriteStream('output.txt'), function(error, result, response){
	  if(!error){
	    // blob retrieved
	  }
	});

 `result`에는 **ETag** 정보를 비롯한 Blob 관련 정보가 포함됩니다.

## 방법: Blob 삭제

끝으로, Blob을 삭제하려면 **deleteBlob**을 호출합니다. 다음 예제에서는 **myblob**이라는 Blob을 삭제합니다.

    blobSvc.deleteBlob(containerName, 'myblob', function(error, response){
	  if(!error){
		// Blob has been deleted
	  }
	});

## 방법: 동시 액세스

여러 클라이언트 또는 여러 프로세스 인스턴스에서 Blob에 대한 동시 액세스를 지원하려면 **ETags** 또는 **임대**를 사용할 수 있습니다.

* **Etag** - Blob이나 컨테이너를 다른 프로세스에서 수정했는지 확인하는 방법을 제공합니다.

* **임대** - Blob에 대해 일정 기간 동안 단독, 갱신 가능, 쓰기 또는 삭제 권한을 얻는 방법을 제공합니다.

### ETag

ETag는 여러 클라이언트 또는 인스턴스에서 Blob에 동시에 쓰기를 허용해야 하는 경우에 사용합니다. ETag를 사용하면 컨테이너나 Blob을 처음 읽거나 만든 후에 수정되었는지 확인하여 다른 클라이언트나 프로세스에서 커밋된 변경 사항을 덮어쓰는 것을 방지할 수 있습니다.

ETag 조건은 선택적인 `options.accessConditions` 매개 변수를 사용하여 설정할 수 있습니다. 아래 예제에서는 Blob이 이미 있고 `etagToMatch`에 포함된 ETag 값이 있는 경우에만 **test.txt** 파일을 업로드합니다.

	blobSvc.createBlockBlobFromLocalFile('mycontainer', 'myblob', 'test.txt', { accessConditions: { 'if-match': etagToMatch} }, function(error, result, response){
      if(!error){
	    // file uploaded
	  }
	});

ETag를 사용하는 경우의 일반적인 패턴:

1. create, list 또는 get 작업의 결과로 ETag를 얻습니다.

2. ETag 값이 수정되지 않은 것을 확인하며 작업을 수행합니다.

값이 수정되지 않았다면 ETag 값을 얻은 후로 다른 클라이언트 또는 인스턴스에서 Blob이나 컨테이너를 수정한 것을 의미합니다.

### 임대

**acquireLease** 메서드를 사용하여 임대를 얻을 Blob 또는 컨테이너를 지정하면 새 임대를 얻을 수 있습니다. 예를 들어 다음 코드는 **myblob**에 대한 임대를 얻습니다.

	blobSvc.acquireLease('mycontainer', 'myblob', function(error, result, response){
	  if(!error) {
	    console.log('leaseId: ' + result.id);
	  }
	});

**myblob**의 후속 작업에서는 `options.leaseId` 매개 변수를 제공해야 합니다. 임대 ID는 **acquireLease**에서 `result.id`로 반환됩니다.

> [AZURE.NOTE] 기본적으로 임대 기간은 무한입니다. 15초에서 60초까지의 유한한 기간을 지정하려면 `options.leaseDuration` 매개 변수를 제공합니다.

임대를 제거하려면 **releaseLease**를 사용합니다. 임대를 중단하면서 원래 기간이 만료될 때까지 다른 사람이 새 임대를 얻지 못하게 하려면 **breakLease**를 사용합니다.

## 방법: 공유 액세스 서명 작업

SAS(공유 액세스 서명)는 저장소 계정 이름이나 키를 제공하지 않으면서 Blob 및 컨테이너에 세분화된 액세스 권한을 안전하게 제공하는 방법입니다. SAS는 모바일 앱에서 Blob에 액세스하는 경우와 같이 데이터에 대해 제한된 액세스를 제공하는 경우에 자주 사용합니다.

> [AZURE.NOTE] Blob에 대해 익명 액세스를 허용할 수도 있지만 SAS에서 더 잘 제어된 액세스를 제공할 수 있으므로 SAS를 생성해야 합니다.

클라우드 기반 서비스와 같이 신뢰할 수 있는 응용 프로그램에서는 **BlobService**의 **generateSharedAccessSignature**를 사용하여 SAS를 생성하고 신뢰할 수 없거나 신뢰가 약한 응용 프로그램에 제공합니다. 예를 들면 모바일 앱이 여기에 해당됩니다. SAS는 SAS가 유효한 시작 및 종료 날짜와 SAS 소유자에게 부여되는 액세스 수준을 설명하는 정책을 사용하여 생성됩니다.

다음 예제에서는 SAS 소유자가 **myblob** Blob에서 읽기 작업을 수행할 수 있도록 허용하고 만든 후 100분이 지나면 만료되는 새 공유 액세스 정책을 생성합니다.

	var startDate = new Date();
	var expiryDate = new Date(startDate);
	expiryDate.setMinutes(startDate.getMinutes() + 100);
	startDate.setMinutes(startDate.getMinutes() - 100);
	    
	var sharedAccessPolicy = {
	  AccessPolicy: {
	    Permissions: azure.BlobUtilities.SharedAccessPermissions.READ,
	    Start: startDate,
	    Expiry: expiryDate
	  },
	};
	
	var blobSAS = blobSvc.generateSharedAccessSignature('mycontainer', 'myblob', sharedAccessPolicy);
	var host = blobSvc.host;

SAS 소유자가 컨테이너에 액세스할 때 필요하므로 호스트 정보도 제공해야 합니다.

그러면 클라이언트 응용 프로그램이 **BlobServiceWithSAS**에서 SAS를 사용하여 Blob에 대한 작업을 수행합니다. 다음 코드는 **myblob**에 대한 정보를 가져옵니다.

	var sharedBlobSvc = azure.createBlobServiceWithSas(host, blobSAS);
	sharedBlobSvc.getBlobProperties('mycontainer', 'myblob', function (error, result, response) {
	  if(!error) {
	    // retrieved info
	  }
	});

SAS가 읽기 권한만으로 생성되었기 때문에 Blob을 수정하려고 하면 오류가 반환됩니다.

### 액세스 제어 목록

ACL(액세스 제어 목록)을 사용하여 SAS에 액세스 정책을 설정할 수도 있습니다. 이 방법은 여러 클라이언트에서 컨테이너에 액세스하게 하면서 각 클라이언트에 서로 다른 액세스 정책을 제공하려는 경우에 유용합니다.

ACL은 각 정책에 ID가 연결된 액세스 정책 배열을 사용하여 구현됩니다. 다음 예제에서는 'user1'과 'user2'에 대해 하나씩 두 개의 정책을 정의합니다.

	var sharedAccessPolicy = [
	  {
	    AccessPolicy: {
	      Permissions: azure.BlobUtilities.SharedAccessPermissions.READ,
	      Start: startDate,
	      Expiry: expiryDate
	    },
	    Id: 'user1'
	  },
	  {
	    AccessPolicy: {
	      Permissions: azure.BlobUtilities.SharedAccessPermissions.WRITE,
	      Start: startDate,
	      Expiry: expiryDate
	    },
	    Id: 'user2'
	  }
	];

다음 예제에서는 **mycontainer**에 대한 현재 ACL을 가져온 다음 **setBlobAcl**을 사용하여 새 정책을 추가합니다. 이 접근 방식을 통해 다음을 수행할 수 있습니다.

	blobSvc.getBlobAcl('mycontainer', function(error, result, response) {
      if(!error){
		//push the new policy into signedIdentifiers
		result.signedIdentifiers.push(sharedAccessPolicy);
		blobSvc.setBlobAcl('mycontainer', result, function(error, result, response){
	  	  if(!error){
	    	// ACL set
	  	  }
		});
	  }
	});

ACL이 설정되고 나면 정책의 ID를 기반으로 SAS를 만들 수 있습니다. 다음 예제에서는 'user2'에 대해 새 SAS를 만듭니다.

	blobSAS = blobSvc.generateSharedAccessSignature('mycontainer', { Id: 'user2' });

## 다음 단계

이제 Blob 저장소의 기본 사항을 배웠으므로 다음 링크를 따라 좀 더 복잡한 저장소 작업을 수행하는 방법을 알아보세요.

-   [Node용 Azure 저장소 SDK API 참조][]를 읽어보세요.
-   MSDN 참조: [Azure에 데이터 저장 및 액세스][]를 참조하세요.
-   [Azure 저장소 팀 블로그][](영문)를 방문하세요.
-   GitHub에서 [Node용 Azure 저장소 SDK][] 리포지토리를 방문하세요.

[Node용 Azure 저장소 SDK]: https://github.com/Azure/azure-storage-node
[Node.js 응용 프로그램 빌드 및 Azure 웹 사이트에 배포]: /develop/nodejs/tutorials/create-a-website-(mac)/
[저장소를 사용하는 Node.js 클라우드 서비스]: storage-nodejs-use-table-storage-cloud-service-app.md
[저장소를 사용하는 Node.js 웹 응용 프로그램]: storage-nodejs-use-table-storage-web-site.md
[WebMatrix를 사용하는 웹 사이트]: web-sites-nodejs-use-webmatrix.md
[REST API 사용]: http://msdn.microsoft.com/library/azure/hh264518.aspx
[Azure 관리 포털]: http://manage.windowsazure.com
[Node.js 클라우드 서비스]: cloud-services-nodejs-develop-deploy-app.md
[Azure에 데이터 저장 및 액세스]: http://msdn.microsoft.com/library/azure/gg433040.aspx
[Azure 저장소 팀 블로그]: http://blogs.msdn.com/b/windowsazurestorage/
[Node용 Azure 저장소 SDK API 참조]: http://dl.windowsazure.com/nodestoragedocs/index.html

<!--HONumber=49-->