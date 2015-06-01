<properties 
	pageTitle="PHP에서 Blob 저장소를 사용하는 방법 | Microsoft Azure "
	description="Azure Blob 서비스를 사용하여 Blob을 업로드, 나열, 다운로드 및 삭제하는 방법을 알아봅니다. 코드 샘플은 PHP로 작성되었습니다." 
	documentationCenter="php" 
	services="storage" 
	authors="tfitzmac,tamram" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="03/11/2015" 
	ms.author="tomfitz"/>

# PHP에서 Blob 저장소를 사용하는 방법

[AZURE.INCLUDE [storage-selector-blob-include](../includes/storage-selector-blob-include.md)]

## 개요

이 가이드에서는 Azure Blob 서비스를 사용하여 일반 시나리오를 수행하는 방법을 보여 줍니다. 샘플은 PHP로 작성되었으며 [PHP용 Azure SDK] [download]를 사용합니다. 여기서 다루는 시나리오에는 Blob **업로드**, **나열**, **다운로드** 및 **삭제**가 포함됩니다. Blob에 대한 자세한 내용은 [다음 단계](#NextSteps) 섹션을 참조하세요.

[AZURE.INCLUDE [storage-blob-concepts-include](../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../includes/storage-create-account-include.md)]

## PHP 응용 프로그램 만들기

Azure Blob 서비스에 액세스하는 PHP 응용 프로그램을 만드는 데 유일한 요구 사항은 코드 내에서 PHP용 Azure SDK의 클래스를 참조하는 것입니다. 응용 프로그램을 만드는 데는 메모장을 포함한 어떠한 개발 도구도 사용할 수 있습니다.

이 가이드에서는 PHP 응용 프로그램 내에서 로컬로 또는 Azure 웹 역할, 작업자 역할 또는 웹 사이트 내에서 실행되는 코드에서 호출할 수 있는 서비스 기능을 사용합니다.

## Azure 클라이언트 라이브러리 가져오기

[AZURE.INCLUDE [get-client-libraries](../includes/get-client-libraries.md)]

## 응용 프로그램에서 Blob 서비스에 액세스하도록 구성

Azure Blob 서비스 API를 사용하려면 다음을 수행해야 합니다.

1. [require_once][require_once] 문을 사용하여 자동 로더 파일 참조
2. 사용할 수 있는 모든 클래스 참조

다음 예제에서는 자동 로더 파일을 포함하고 **ServicesBuilder** 클래스를 참조하는 방법을 보여 줍니다.

> [AZURE.NOTE] 이 예제 및 이 문서의 다른 예제에서는 Azure용 PHP 클라이언트 라이브러리를 작성기를 통해 설치했다고 가정합니다. 라이브러리를 수동으로 또는 PEAR 패키지로 설치한 경우 `WindowsAzure.php` 자동 로더 파일을 참조해야 합니다.

	require_once 'vendor\autoload.php';
	use WindowsAzure\Common\ServicesBuilder;


아래 예제에서 `require_once` 문은 항상 표시되지만 예제를 실행하는 데 필요한 클래스만 참조됩니다.

## Azure 저장소 연결 설정

Azure Blob 서비스 클라이언트를 인스턴스화하려면 먼저 유효한 연결 문자열이 있어야 합니다. Blob 서비스 연결 문자열 형식은 다음과 같습니다.

Live 서비스에 액세스하는 경우:

	DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]

에뮬레이터 저장소에 액세스하는 경우:

	UseDevelopmentStorage=true


Azure 서비스 클라이언트를 만들려면 **ServicesBuilder** 클래스를 사용해야 합니다. 다음을 수행할 수 있습니다.

* 연결 문자열을 직접 전달할 수 있습니다.
* **CCM(CloudConfigurationManager)**을 사용하여 여러 외부 소스에서 연결 문자열을 확인할 수 있습니다.
	* 기본적으로 하나의 외부 소스, 환경 변수에 대한 지원이 제공됩니다.
	* **ConnectionStringSource** 클래스를 확장하여 새 소스를 추가할 수 있습니다.

여기에 설명된 예제의 경우 연결 문자열이 직접 전달됩니다.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;

	$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

## 방법: 컨테이너 만들기

**BlobRestProxy** 개체를 사용하면 **createContainer** 메서드로 Blob 컨테이너를 만들 수 있습니다. 컨테이너를 만드는 경우 컨테이너에 대한 옵션을 설정할 수 있으나 반드시 옵션을 설정해야 하는 것은 아닙니다. 아래 예제에서는 컨테이너 ACL 및 컨테이너 메타데이터를 설정하는 방법을 보여 줍니다.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Blob\Models\CreateContainerOptions;
	use WindowsAzure\Blob\Models\PublicAccessType;
	use WindowsAzure\Common\ServiceException;

	// Create blob REST proxy.
	$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);


	// OPTIONAL: Set public access policy and metadata.
	// Create container options object.
	$createContainerOptions = new CreateContainerOptions();	

	// Set public access policy. Possible values are 
	// PublicAccessType::CONTAINER_AND_BLOBS and PublicAccessType::BLOBS_ONLY.
	// CONTAINER_AND_BLOBS: 	
	// Specifies full public read access for container and blob data.
    // proxys can enumerate blobs within the container via anonymous 
	// request, but cannot enumerate containers within the storage account.
	//
	// BLOBS_ONLY:
	// Specifies public read access for blobs. Blob data within this 
    // container can be read via anonymous request, but container data is not 
    // available. proxys cannot enumerate blobs within the container via 
	// anonymous request.
	// If this value is not specified in the request, container data is 
	// private to the account owner.
	$createContainerOptions->setPublicAccess(PublicAccessType::CONTAINER_AND_BLOBS);
	
	// Set container metadata
	$createContainerOptions->addMetaData("key1", "value1");
	$createContainerOptions->addMetaData("key2", "value2");
	
	try	{
		// Create container.
		$blobRestProxy->createContainer("mycontainer", $createContainerOptions);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/azure/dd179439.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

**setPublicAccess(PublicAccessType::CONTAINER_AND_BLOBS)**를 호출하면 익명 요청을 통해 컨테이너 및 Blob 데이터에 액세스할 수 있습니다. **setPublicAccess(PublicAccessType::BLOBS_ONLY)**를 호출하면 익명 요청을 통해 Blob 데이터에만 액세스할 수 있습니다. 컨테이너 ACL에 대한 자세한 내용은 [컨테이너 ACL 설정(REST API)][container-acl]을 참조하세요.

Blob 서비스 오류 코드에 대한 자세한 내용은 [Blob 서비스 오류 코드][error-codes]를 참조하세요.

## 방법: 컨테이너에 Blob 업로드

파일을 Blob으로 업로드하려면 **BlobRestProxy->createBlockBlob** 메서드를 사용합니다. 이 작업은 Blob이 없는 경우 새로 만들고, Blob이 있는 경우 덮어씁니다. 아래 코드 예제에서는 컨테이너가 이미 만들어졌고 [fopen][fopen]을 사용하여 파일을 스트림으로 연다고 가정합니다.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create blob REST proxy.
	$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

	
	$content = fopen("c:\myfile.txt", "r");
	$blob_name = "myblob";
	
	try	{
		//Upload blob
		$blobRestProxy->createBlockBlob("mycontainer", $blob_name, $content);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/azure/dd179439.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

위의 예제에서는 Blob을 스트림으로 업로드합니다. 그러나 Blob은 예를 들어 [file_get_contents][file_get_contents] 함수를 사용하여 문자열로 업로드될 수도 있습니다. 이렇게 하려면 위의 예제에서 `$content = fopen("c:\myfile.txt", "r");`을 `$content = file_get_contents("c:\myfile.txt");`로 변경하세요.

## 방법: 컨테이너의 Blob 나열

컨테이너의 Blob을 나열하려면 **BlobRestProxy->listBlobs** 메서드를 **foreach** 루프와 함께 사용하여 결과를 반복합니다. 다음 코드는 컨테이너의 각 Blob 이름과 해당 URI를 브라우저로 출력합니다.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create blob REST proxy.
	$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

	
	try	{
		// List blobs.
		$blob_list = $blobRestProxy->listBlobs("mycontainer");
		$blobs = $blob_list->getBlobs();
		
		foreach($blobs as $blob)
		{
			echo $blob->getName().": ".$blob->getUrl()."<br />";
		}
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/azure/dd179439.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}


## 방법: Blob 다운로드

Blob을 다운로드하려면 **BlobRestProxy->getBlob** 메서드를 호출한 다음 결과 **GetBlobResult** 개체에서 **getContentStream** 메서드를 호출합니다.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create blob REST proxy.
	$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

	
	try	{
		// Get blob.
		$blob = $blobRestProxy->getBlob("mycontainer", "myblob");
		fpassthru($blob->getContentStream());
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/azure/dd179439.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

위의 예제에서는 Blob을 스트림 리소스로 가져옵니다(기본 동작). 그러나 [stream_get_contents][stream-get-contents] 함수를 사용하여 반환된 스트림을 문자열로 변환할 수 있습니다.

## 방법: Blob 삭제

Blob을 삭제하려면 컨테이너 이름 및 Blob 이름을 **BlobRestProxy->deleteBlob**에 전달합니다. 

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create blob REST proxy.
	$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

	
	try	{
		// Delete container.
		$blobRestProxy->deleteBlob("mycontainer", "myblob");
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/azure/dd179439.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

## 방법: Blob 컨테이너 삭제

끝으로, Blob 컨테이너를 삭제하려면 컨테이너 이름을 **BlobRestProxy->deleteContainer**에 전달합니다.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;

	// Create blob REST proxy.
	$blobRestProxy = ServicesBuilder::getInstance()->createBlobService($connectionString);

	
	try	{
		// Delete container.
		$blobRestProxy->deleteContainer("mycontainer");
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/library/azure/dd179439.aspx
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

## 다음 단계

이제 Azure Blob 서비스의 기본 사항을 배웠으므로 다음 링크를 따라 좀 더 복잡한 저장소 작업에 대해 알아보세요.

- MSDN 참조: [Azure 저장소](http://msdn.microsoft.com/library/azure/gg433040.aspx)를 참조하세요.
- [Azure 저장소 팀 블로그](http://blogs.msdn.com/b/windowsazurestorage/)(영문)를 방문하세요.
- <https://github.com/WindowsAzure/azure-sdk-for-php-samples/blob/master/storage/BlockBlobExample.php>에서 PHP 블록 Blob 예를 참조하세요.
- <https://github.com/WindowsAzure/azure-sdk-for-php-samples/blob/master/storage/PageBlobExample.php>에서 PHP 페이지 Blob 예를 참조하세요.

[download]: http://go.microsoft.com/fwlink/?LinkID=252473
[Azure에 데이터 저장 및 액세스]: http://msdn.microsoft.com/library/azure/gg433040.aspx
[container-acl]: http://msdn.microsoft.com/library/azure/dd179391.aspx
[error-codes]: http://msdn.microsoft.com/library/azure/dd179439.aspx
[file_get_contents]: http://php.net/file_get_contents
[require_once]: http://php.net/require_once
[fopen]: http://www.php.net/fopen
[stream-get-contents]: http://www.php.net/stream_get_contents

<!--HONumber=49-->