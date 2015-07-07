<properties 
	pageTitle="Java에서 파일 저장소를 사용하는 방법| Microsoft Azure" 
	description="Azure 파일 서비스를 사용하여 파일을 업로드, 다운로드, 나열 및 삭제하는 방법을 알아봅니다. 샘플은 Java로 작성되었습니다." 
	services="storage" 
	documentationCenter="java" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor="jimbe" />

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="06/03/2015" 
	ms.author="v-dedomi"/>

# Java에서 파일 저장소를 사용하는 방법

[AZURE.INCLUDE [저장소-선택기-파일-포함](../../includes/storage-selector-file-include.md)]

## 개요

이 가이드는 Microsoft Azure 파일 저장소 서비스에서 기본 작업을 수행하는 방법을 알려줍니다. Java로 작성된 샘플을 통해 공유 및 디렉터리 만들기, 업로드, 목록 및 파일을 삭제하는 방법을 배웁니다. Microsoft Azure 파일 저장소 서비스를 처음 접하는 경우에는 다음 섹션에 있는 개념들을 살펴보면 샘플에 대한 이해를 높이는 데 매우 유용할 것입니다.

[AZURE.INCLUDE [저장소-파일-개념-포함](../../includes/storage-file-concepts-include.md)]

[AZURE.INCLUDE [저장소-만들기-계정-포함](../../includes/storage-create-account-include.md)]

## Java 응용 프로그램 만들기

샘플을 빌드하려면 Java 개발 키트(JDK)와 [Java용 Azure 저장소 SDK][]가 필요합니다. Azure 저장소 계정도 만들었어야 합니다.

## 파일 저장소를 사용하도록 응용 프로그램 설정

Azure 저장소 API를 사용하려면 저장소 서비스를 액세스하고자 하는 위치에서 Java 파일의 맨 위에 다음 명령문을 추가하십시오.

	// Include the following imports to use blob APIs.
    import com.microsoft.azure.storage.*;
	import com.microsoft.azure.storage.file.*;

## Azure 저장소 연결 문자열 설정

파일 저장소를 사용하려면 Azure 저장소 계정에 연결해야 합니다. 첫 번째 단계는 저장소 계정에 연결하는 데 사용할 연결 문자열을 구성하는 것입니다. 이를 위해 정적 변수를 정의해 보겠습니다.

	// Configure the connection-string with your values
	public static final String storageConnectionString = 
	    "DefaultEndpointsProtocol=http;" + 
	    "AccountName=your_storage_account_name;" + 
	    "AccountKey=your_storage_account_key";

> [AZURE.NOTE]your_storage_account_name과 your_storage_account_key를 자신의 저장소 계정에 대한 실제 값으로 바꿉니다.

## Azure 저장소 계정에 연결

저장소 계정에 연결하려면 **CloudStorageAccount** 개체를 사용하여 연결 문자열을 **구문 분석** 메서드로 전달해야 합니다.

	// Use the CloudStorageAccount object to connect to your storage account
	try {
		CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);
	} catch (InvalidKeyException invalidKey) {
		// Handle the exception
	}

**CloudStorageAccount.parse**는 InvalidKeyException을 발생시키므로 try/catch 블록 안에 넣어야 합니다.

## 공유를 만드는 방법

파일 저장소 내의 모든 파일 및 디렉터리는 **공유**라는 이름의 컨테이너 안에 있습니다. 저장소 계정은 계정 용량이 허용하는 만큼의 공유를 가질 수 있습니다. 공유 및 그 내용에 액세스하려면 파일 저장소 클라이언트를 사용해야 합니다.

	// Create the file storage client.
	CloudFileClient fileClient = storageAccount.createCloudFileClient();

파일 저장소 클라이언트를 사용하면 공유에 대한 참조를 가져올 수 있습니다..

	// Get a reference to the file share
	CloudFileShare share = fileClient.getShareReference("sampleshare"); 

공유를 실제로 만들고자 한다면 CloudFileShare 개체의 **createIfNotExists** 메서드를 사용합니다.

	if (share.createIfNotExists()) {
		System.out.println("New share created");
	}

이 시점에서 **공유**는 **sampleshare**라는 이름의 공유에 대해 참조를 포함합니다.

## 방법: 파일 업로드

Azure 파일 저장소 공유에는 파일이 상주할 수 있는 최소한의 루트 디렉터리가 포함되어 있습니다. 이 섹션에서는 로컬 저장소에서 공유의 루트 디렉터리로 파일을 업로드하는 방법을 배웁니다.

파일을 업로드하는 첫 번째 단계는 상주해야 하는 디렉터리에 대한 참조를 가져오는 것입니다. 공유 개체의 **getRootDirectoryReference** 메서드를 호출하여 가져올 수 있습니다.

	//Get a reference to the root directory for the share.
	CloudFileDirectory rootDir = share.getRootDirectoryReference();

이제 공유의 루트 디렉터리에 대한 참조를 가졌으므로 다음 코드를 사용하여 파일을 업로드할 수 있습니다.

	// Define the path to a local file.
	final String filePath = "C:\temp\Readme.txt";

	CloudFile cloudFile = rootDir.getFileReference("Readme.txt");
	cloudFile.uploadFromFile(filePath);

## 방법: 디렉터리 만들기

또한 루트 디렉터리에 이들 모두를 포함하는 대신 하위 디렉터리 내에서 파일을 배치하여 저장소를 구성할 수 있습니다. Azure 파일 저장소 서비스를 사용하면 계정이 허용하는 만큼 많은 디렉터리를 만들 수 있습니다. 아래 코드는 루트 디렉터리 아래에 **sampledir**라는 이름의 하위 디렉터리를 만듭니다.

	//Get a reference to the root directory for the share.
	CloudFileDirectory rootDir = share.getRootDirectoryReference();

	//Get a reference to the sampledir directory 
	CloudFileDirectory sampleDir = rootDir.getDirectoryReference("sampledir");
			    
	if (sampleDir.createIfNotExists()) {
		System.out.println("sampledir created");
	} else {
		System.out.println("sampledir already exists");
	}

## 방법: 공유에 파일 및 디렉터리 나열

공유 내에서 파일 및 디렉터리 목록을 가져오는 것은 CloudFileDirectory 참조에서 **listFilesAndDirectories**를 호출하여 쉽게 수행할 수 있습니다. 메서드는 반복할 수 있는 ListFileItem 개체 목록을 반환합니다. 한 예로, 다음 코드는 파일 및 디렉터리를 루트 디렉터리 안에 나열합니다.

	//Get a reference to the root directory for the share.
	CloudFileDirectory rootDir = share.getRootDirectoryReference();
		   
	for ( ListFileItem fileItem : rootDir.listFilesAndDirectories() ) {
		System.out.println(fileItem.getUri());
	}


## 방법: 파일 다운로드

파일 저장소에 대해 가장 자주 수행하게 될 작업 중 하나는 파일의 다운로드입니다. 다음 예제에서 코드가 SampleFile.txt를 다운로드하고 그 내용을 표시합니다.

	//Get a reference to the root directory for the share.
	CloudFileDirectory rootDir = share.getRootDirectoryReference();
	
	//Get a reference to the directory that contains the file
	CloudFileDirectory sampleDir = rootDir.getDirectoryReference("sampledir");
			    
	//Get a reference to the file you want to download
	CloudFile file = sampleDir.getFileReference("SampleFile.txt");

	//Write the contents of the file to the console.
	System.out.println(file.downloadText());

## 방법: 파일 삭제

다른 일반적인 파일 저장소 작업은 파일의 삭제입니다. 다음 코드는 **sampledir**라는 이름의 디렉터리 안에 저장된 SampleFile.txt라는 이름의 파일을 삭제합니다.


	// Get a reference to the root directory for the share.
	CloudFileDirectory rootDir = share.getRootDirectoryReference();

	// Get a reference to the directory where the file to be deleted is in
	CloudFileDirectory containerDir = rootDir.getDirectoryReference("sampledir");
	
	String filename = "SampleFile.txt"
	CloudFile file;

	file = containerDir.getFileReference(filename)
	if ( file.deleteIfExists() ) {
		System.out.println(filename + " was deleted");
	}


## 방법: 디렉터리 삭제

디렉터리의 삭제는 매우 간단한 작업입니다. 단, 여전히 파일 또는 기타 디렉터리가 포함된 디렉터리는 삭제할 수 없습니다.

	// Get a reference to the root directory for the share.
	CloudFileDirectory rootDir = share.getRootDirectoryReference();

	// Get a reference to the directory you want to delete
	CloudFileDirectory containerDir = rootDir.getDirectoryReference("sampledir");	

	// Delete the directory
	if ( containerDir.deleteIfExists() ) {
		System.out.println("Directory deleted");
	}


## 방법: 공유 삭제

공유 삭제는 CloudFileShare 개체에서 **deleteIfExists** 메서드를 호출하여 수행할 수 있습니다. 다음이 샘플 코드입니다.

	try
	{
	    // Retrieve storage account from connection-string.
	    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

	    // Create the file client.
	   CloudFileClient fileClient = storageAccount.createCloudFileClient();

	   // Get a reference to the file share
	   CloudFileShare share = fileClient.getShareReference("sampleshare");
	   
	   if (share.deleteIfExists()) {
		   System.out.println("sampleshare deleted");
	   } 
	} catch (Exception e) {
		e.printStackTrace();
	}

## 다음 단계

다른 Azure 저장소 API에 대해 더 알아보려면 다음 링크를 따르십시오.

- [Java용 Azure 저장소 SDK]
- [Azure 저장소 클라이언트 SDK 참조]
- [Azure 저장소 REST API]
- [Azure 저장소 팀 블로그]

[Azure SDK for Java]: http://azure.microsoft.com/develop/java/
[Java용 Azure 저장소 SDK]: https://github.com/azure/azure-storage-java
[Azure Storage SDK for Android]: https://github.com/azure/azure-storage-android
[Azure 저장소 클라이언트 SDK 참조]: http://dl.windowsazure.com/storage/javadoc/
[Azure 저장소 REST API]: http://msdn.microsoft.com/library/azure/gg433040.aspx
[Azure 저장소 팀 블로그]: http://blogs.msdn.com/b/windowsazurestorage/
 

<!---HONumber=62-->