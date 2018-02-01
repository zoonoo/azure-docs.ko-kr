---
title: "Ruby에서 Blob Storage(개체 저장소)를 사용하는 방법 | Microsoft Docs"
description: "Azure Blob 저장소(개체 저장소)를 사용하여 클라우드에 구조화되지 않은 데이터를 저장합니다."
services: storage
documentationcenter: ruby
author: tamram
manager: timlt
editor: tysonn
ms.assetid: e2fe4c45-27b0-4d15-b3fb-e7eb574db717
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 01/18/2018
ms.author: tamram
ms.openlocfilehash: c4c6d47511acdae7afaf4a535c24c6fcc7e389b1
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/22/2018
---
# <a name="how-to-use-blob-storage-from-ruby"></a>Ruby에서 Blob Storage를 사용하는 방법
[!INCLUDE [storage-selector-blob-include](../../../includes/storage-selector-blob-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>개요
Azure Blob 저장소는 클라우드에 구조화되지 않은 데이터를 개체/Blob로 저장하는 서비스입니다. Blob 저장소는 문서, 미디어 파일 또는 응용 프로그램 설치 프로그램과 같은 모든 종류의 텍스트 또는 이진 데이터를 저장할 수 있습니다. 또한 Blob storage를 개체 저장소라고 합니다.

이 가이드에서는 Blob Storage를 사용하여 일반 시나리오를 수행하는 방법을 보여 줍니다. 샘플은 Ruby API를 사용하여 작성되었습니다. 여기서 다루는 시나리오에는 Blob **업로드, 나열, 다운로드** 및 **삭제**가 포함됩니다.

[!INCLUDE [storage-blob-concepts-include](../../../includes/storage-blob-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-ruby-application"></a>Ruby 응용 프로그램 만들기
Ruby 응용 프로그램을 만듭니다. 지침은 [Linux의 App Service에서 Ruby 앱 만들기](https://docs.microsoft.com/azure/app-service/containers/quickstart-ruby)를 참조하세요.


## <a name="configure-your-application-to-access-storage"></a>저장소에 액세스하도록 응용 프로그램 구성
Azure Storage를 사용하려면 저장소 REST 서비스와 통신하는 편리한 라이브러리 집합이 포함된 Ruby Azure 패키지를 다운로드하여 사용해야 합니다.

### <a name="use-rubygems-to-obtain-the-package"></a>RubyGems를 사용하여 패키지 가져오기
1. **PowerShell**(Windows), **Terminal**(Mac) 또는 **Bash**(Unix)와 같은 명령줄 인터페이스를 사용합니다.
2. 명령 창에서 "gem install azure-storage-blob"을 입력하여 gem 및 종속성을 설치합니다.

### <a name="import-the-package"></a>패키지 가져오기
원하는 텍스트 편집기를 사용하여 저장소를 사용하려는 Ruby 파일의 맨 위에 다음을 추가합니다.

```ruby
require "azure/storage/blob"
```

## <a name="set-up-an-azure-storage-connection"></a>Azure Storage 연결 설정
Azure 모듈은 **AZURE\_STORAGE\_ACCOUNT** 및 **AZURE\_STORAGE\_ACCESS_KEY** 환경 변수를 읽고 Azure Storage 계정에 연결하는 데 필요한 정보를 확인합니다. 이러한 환경 변수가 설정되어 있지 않으면 다음 코드가 있는 **Azure::Blob::BlobService::create**를 사용하여 계정 정보를 지정해야 합니다.

```ruby
blob_client = Azure::Storage::Blob::BlobService.create(
    storage_account_name: account_name,
    storage_access_key: account_key
    )
```

Azure 포털의 클래식 또는 Resource Manager 저장소 계정에서 이러한 값을 가져오려면

1. [Azure 포털](https://portal.azure.com) 에 로그인합니다.
2. 사용하려는 저장소 계정으로 이동합니다.
3. 오른쪽의 설정 블레이드에서 **액세스 키**를 클릭합니다.
4. 나타나는 액세스 키 블레이드에 액세스 키 1 및 액세스 키 2가 표시되어 있습니다. 이 둘 중 하나를 사용할 수 있습니다.
5. 복사 아이콘을 클릭하여 키를 클립보드에 복사합니다.

## <a name="create-a-container"></a>컨테이너 만들기
[!INCLUDE [storage-container-naming-rules-include](../../../includes/storage-container-naming-rules-include.md)]

**Azure::Storage::Blob::BlobService** 개체를 사용하면 컨테이너 및 Blob을 사용할 수 있습니다. 컨테이너를 만들려면 **create\_container()** 메서드를 사용합니다.

다음 코드 예제에서는 컨테이너를 만들거나, 컨테이너가 있을 경우 오류를 출력합니다.

```ruby
azure_blob_service = Azure::Storage::Blob::BlobService.create_from_env
begin
    container = azure_blob_service.create_container("test-container")
rescue
    puts $!
end
```

컨테이너 파일을 공용으로 지정하려는 경우 컨테이너의 사용 권한을 설정할 수 있습니다.

<strong>create\_container()</strong> 호출을 수정하기만 하면 **:public\_access\_level** 옵션을 전달할 수 있습니다.

```ruby
container = azure_blob_service.create_container("test-container",
    :public_access_level => "<public access level>")
```

유효한 **:public\_access\_level** 옵션 값은 다음과 같습니다.

* **blob:** BLOB에 대한 공용 읽기 권한을 지정합니다. 이 컨테이너 내의 Blob 데이터는 익명 요청을 통해 읽을 수 있으나 컨테이너 데이터는 읽을 수 없습니다. 클라이언트는 익명 요청을 통해 컨테이너 내의 Blob을 열거할 수 없습니다.
* **container:** 컨테이너 및 BLOB 데이터에 대한 전체 공용 읽기 액세스 권한을 지정합니다. 클라이언트는 익명 요청을 통해 컨테이너 내에서 Blob을 열거할 수 있지만 저장소 계정 내에서 컨테이너를 열거할 수는 없습니다.

또는 **set\_container\_acl()** 메서드로 공용 액세스 수준을 지정하여 컨테이너의 공용 액세스 수준을 수정할 수 있습니다.

다음 코드 예제에서는 공용 액세스 수준을 **container**로 변경합니다.

```ruby
azure_blob_service.set_container_acl('test-container', "container")
```

## <a name="upload-a-blob-into-a-container"></a>컨테이너에 Blob 업로드
Blob에 콘텐츠를 업로드하려면 **create\_block\_blob()** 메서드를 사용하여 Blob을 만들고 Blob의 콘텐츠로 파일이나 문자열을 사용합니다.

다음 코드에서는 **test.png** 파일을 "image-blob"이라는 새 Blob으로 컨테이너에 업로드합니다.

```ruby
content = File.open("test.png", "rb") { |file| file.read }
blob = azure_blob_service.create_block_blob(container.name,
    "image-blob", content)
puts blob.name
```

## <a name="list-the-blobs-in-a-container"></a>컨테이너의 Blob 나열
컨테이너를 나열하려면 **list_containers()** 메서드를 사용합니다.
컨테이너 내에 Blob을 나열하려면 **list\_blobs()** 메서드를 사용합니다. 컨테이너에 있는 Blob을 모두 나열하려면 서비스에서 반환된 연속 토큰을 따르고 이 토큰을 사용하여 list_blobs를 계속 실행해야 합니다. 자세한 내용은 [List Blobs REST API](https://docs.microsoft.com/rest/api/storageservices/list-blobs)를 참조하세요.

다음 코드에서는 컨테이너에 있는 모든 Blob을 출력합니다.

```ruby
nextMarker = nil
loop do
    blobs = azure_blob_service.list_blobs(container_name, { marker: nextMarker })
    blobs.each do |blob|
        puts "\tBlob name #{blob.name}"
    end
    nextMarker = blobs.continuation_token
    break unless nextMarker && !nextMarker.empty?
end
```

## <a name="download-blobs"></a>Blob 다운로드
Blob을 다운로드하려면 **get\_blob()** 메서드를 사용하여 콘텐츠를 가져옵니다.

다음 코드 예제에서는 **get\_blob()**을 사용하여 "image-blob"의 콘텐츠를 다운로드하고 그 콘텐츠를 로컬 파일에 쓰는 방법을 보여 줍니다.

```ruby
blob, content = azure_blob_service.get_blob(container.name,"image-blob")
File.open("download.png","wb") {|f| f.write(content)}
```

## <a name="delete-a-blob"></a>Blob 삭제
마지막으로 Blob을 삭제하려면 **delete\_blob()** 메서드를 사용합니다. 다음 코드 예제에서는 Blob을 삭제하는 방법을 보여 줍니다.

```ruby
azure_blob_service.delete_blob(container.name, "image-blob")
```

## <a name="next-steps"></a>다음 단계
더 복잡한 저장소 작업에 대해 알아보려면 다음 링크를 따라가세요.

* [Azure Storage 팀 블로그](http://blogs.msdn.com/b/windowsazurestorage/)
* GitHub의 [Ruby용 Azure Storage SDK](https://github.com/azure/azure-storage-ruby) 리포지토리
* [AzCopy 명령줄 유틸리티로 데이터 전송](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

