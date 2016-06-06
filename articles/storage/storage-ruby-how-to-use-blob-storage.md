<properties
	pageTitle="Ruby에서 Blob 저장소(개체 저장소)를 사용하는 방법 | Microsoft Azure"
	description="Azure Blob 저장소(개체 저장소)를 사용하여 클라우드에 구조화되지 않은 데이터를 저장합니다."
	services="storage"
	documentationCenter="ruby"
	authors="rmcmurray"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="ruby"
	ms.topic="article"
    ms.date="04/29/2016"
	ms.author="robmcm"/>


# Ruby에서 Blob 저장소를 사용하는 방법

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

## 개요

Azure Blob 저장소는 클라우드에 구조화되지 않은 데이터를 개체/Blob로 저장하는 서비스입니다. Blob 저장소는 문서, 미디어 파일 또는 응용 프로그램 설치 프로그램과 같은 모든 종류의 텍스트 또는 이진 데이터를 저장할 수 있습니다. 또한 Blob 저장소를 개체 저장소라고 합니다.

이 가이드에서는 Blob 저장소를 사용하여 일반 시나리오를 수행하는 방법을 보여 줍니다. 샘플은 Ruby API를 사용하여 작성되었습니다. 여기서 다루는 시나리오에는 Blob **업로드, 나열, 다운로드** 및 **삭제**가 포함됩니다.

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Ruby 응용 프로그램 만들기

Ruby 응용 프로그램을 만듭니다. 지침은 [Azure VM의 Ruby on Rails 웹 응용 프로그램](../virtual-machines/virtual-machines-linux-classic-ruby-rails-web-app.md)을 참조하세요.

## 저장소에 액세스하도록 응용 프로그램 구성

Azure 저장소를 사용하려면 저장소 REST 서비스와 통신하는 편리한 라이브러리 집합이 포함된 Ruby Azure 패키지를 다운로드하여 사용해야 합니다.

### RubyGems를 사용하여 패키지 가져오기

1. **PowerShell**(Windows), **Terminal**(Mac) 또는 **Bash**(Unix)와 같은 명령줄 인터페이스를 사용합니다.

2. 명령 창에 "gem install azure"를 입력하여 gem 및 종속성을 설치합니다.

### 패키지 가져오기

원하는 텍스트 편집기를 사용하여 저장소를 사용하려는 Ruby 파일의 맨 위에 다음을 추가합니다.

	require "azure"

## Azure 저장소 연결 설정

Azure 모듈은 **AZURE\_STORAGE\_ACCOUNT** 및 **AZURE\_STORAGE\_ACCESS\_KEY** 환경 변수를 읽고 Azure 저장소 계정에 연결하는 데 필요한 정보를 확인합니다. 이러한 환경 변수가 설정되지 않으면 **Azure::Blob::BlobService**를 사용하기 전에 다음 코드로 계정 정보를 지정해야 합니다.

	Azure.config.storage_account_name = "<your azure storage account>"
	Azure.config.storage_access_key = "<your azure storage access key>"


Azure 포털의 클래식 또는 ARM 저장소 계정에서 이러한 값을 가져오려면

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
2. 사용하려는 저장소 계정으로 이동합니다.
3. 오른쪽의 설정 블레이드에서 **액세스 키**를 클릭합니다.
4. 나타나는 액세스 키 블레이드에 액세스 키 1 및 액세스 키 2가 표시되어 있습니다. 이 둘 중 하나를 사용할 수 있습니다. 
5. 복사 아이콘을 클릭하여 키를 클립보드에 복사합니다. 

클래식 포털의 클래식 저장소 계정에서 이러한 값을 가져오려면

1. [클래식 포털](https://manage.windowsazure.com)에 로그인합니다.
2. 사용하려는 저장소 계정으로 이동합니다.
3. 탐색 창 아래쪽에서 **액세스 키 관리**를 클릭합니다.
4. 팝업 대화 상자에 저장소 계정 이름, 기본 액세스 키 및 보조 액세스 키가 표시됩니다. 액세스 키의 경우 기본 액세스 키 또는 보조 액세스 키를 사용할 수 있습니다. 
5. 복사 아이콘을 클릭하여 키를 클립보드에 복사합니다.

## 컨테이너 만들기

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

**Azure::Blob::BlobService** 개체를 통해 컨테이너 및 Blob에 대한 작업을 할 수 있습니다. 컨테이너를 만들려면 **create\_container()** 메서드를 사용합니다.

다음 코드 예제에서는 컨테이너를 만들거나, 컨테이너가 있을 경우 오류를 출력합니다.

	azure_blob_service = Azure::Blob::BlobService.new
	begin
	  container = azure_blob_service.create_container("test-container")
	rescue
	  puts $!
	end

컨테이너 파일을 공용으로 지정하려는 경우 컨테이너의 사용 권한을 설정할 수 있습니다.

**: public\_access\_level**옵션 전달하기를 호출하여 <strong>create\_container ()</strong>를 수정할 수 있습니다.  


	container = azure_blob_service.create_container("test-container",
	  :public_access_level => "<public access level>")


유효한 **:public\_access\_level** 옵션 값은 다음과 같습니다.

* **blob:** 컨테이너 및 blob 데이터에 대한 전체 공용 읽기 액세스 권한을 지정 합니다. 클라이언트는 익명 요청을 통해 컨테이너 내에서 Blob을 열거할 수 있지만 저장소 계정 내에서 컨테이너를 열거할 수는 없습니다.

* **컨테이너:** blob에 대한 공용 읽기 액세스 권한을 지정 합니다. 이 컨테이너 내의 Blob 데이터는 익명 요청을 통해 읽을 수 있으나 컨테이너 데이터는 읽을 수 없습니다. 클라이언트는 익명 요청을 통해 컨테이너 내의 Blob을 열거할 수 없습니다.

또는 **set\_container\_acl()** 메서드로 공용 액세스 수준을 지정하여 컨테이너의 공용 액세스 수준을 수정할 수 있습니다.

다음 코드 예제에서는 공용 액세스 수준을 **container**로 변경합니다.

	azure_blob_service.set_container_acl('test-container', "container")

## 컨테이너에 Blob 업로드

Blob에 콘텐츠를 업로드하려면 **create\_block\_blob()** 메서드를 사용하여 Blob을 만들고 Blob의 콘텐츠로 파일이나 문자열을 사용합니다.

다음 코드에서는 **test.png** 파일을 "image-blob"이라는 새 Blob으로 컨테이너에 업로드합니다.

	content = File.open("test.png", "rb") { |file| file.read }
	blob = azure_blob_service.create_block_blob(container.name,
	  "image-blob", content)
	puts blob.name

## 컨테이너의 Blob 나열

컨테이너를 나열하려면 **list\_containers()** 메서드를 사용합니다. 컨테이너 내에 Blob을 나열하려면 **list\_blobs()** 메서드를 사용합니다.

이 메서드는 계정에 대해 모든 컨테이너에 있는 모든 Blob의 URL을 출력합니다.

	containers = azure_blob_service.list_containers()
	containers.each do |container|
	  blobs = azure_blob_service.list_blobs(container.name)
	  blobs.each do |blob|
	    puts blob.name
	  end
	end

## Blob 다운로드

Blob을 다운로드하려면 **get\_blob()** 메서드를 사용하여 콘텐츠를 가져옵니다.

다음 코드 예제에서는 **get\_blob()**을 사용하여 "image-blob"의 콘텐츠를 다운로드하고 그 콘텐츠를 로컬 파일에 쓰는 방법을 보여 줍니다.

	blob, content = azure_blob_service.get_blob(container.name,"image-blob")
	File.open("download.png","wb") {|f| f.write(content)}

## Blob 삭제
마지막으로 Blob을 삭제하려면 **delete\_blob()** 메서드를 사용합니다. 다음 코드 예제에서는 Blob을 삭제하는 방법을 보여 줍니다.

	azure_blob_service.delete_blob(container.name, "image-blob")

## 다음 단계

더 복잡한 저장소 작업에 대해 알아보려면 다음 링크를 따라가세요.

- [Azure 저장소 팀 블로그](http://blogs.msdn.com/b/windowsazurestorage/)
- GitHub의 [Azure SDK for Ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby)(영문) 리포지토리
- [AzCopy 명령줄 유틸리티로 데이터 전송](storage-use-azcopy.md)

<!---HONumber=AcomDC_0525_2016-->