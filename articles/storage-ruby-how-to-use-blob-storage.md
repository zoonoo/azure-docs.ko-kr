<properties urlDisplayName="Blob Service" pageTitle="Blob 저장소 사용 방법(Ruby) | Microsoft Azure" metaKeywords="Get started Azure blob, Azure unstructured data, Azure unstructured storage, Azure blob, Azure blob storage, Azure blob Ruby" description="Azure Blob 서비스를 사용하여 Blob 콘텐츠를 업로드, 다운로드, 나열 및 삭제하는 방법에 대해 알아봅니다. 샘플은 Ruby로 작성되었습니다." metaCanonical="" services="storage" documentationCenter="Ruby" title="Ruby에서 Blob 서비스를 사용하는 방법" authors="guayan" solutions="" manager="wpickett" editor="" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="ruby" ms.topic="article" ms.date="01/01/1900" ms.author="guayan" />

# Ruby에서 Blob 서비스를 사용하는 방법

이 가이드에서는 Azure Blob 서비스를 사용하여 일반 시나리오를
수행하는 방법을 보여 줍니다. 샘플은 Ruby API를 사용하여 작성되었습니다.
여기서 다루는 시나리오에는 Blob의 **업로드, 나열, 다운로드** 및 **삭제**가 포함됩니다.
Blob에 대한 자세한 내용은 [다음 단계][다음 단계] 섹션을 참조하세요.

## 목차

-   [Blob 서비스 정의][Blob 서비스 정의]
-   [개념][개념]
-   [Azure 저장소 계정 만들기][Azure 저장소 계정 만들기]
-   [Ruby 응용 프로그램 만들기][Ruby 응용 프로그램 만들기]
-   [저장소에 액세스하도록 응용 프로그램 구성][저장소에 액세스하도록 응용 프로그램 구성]
-   [Azure 저장소 연결 설정][Azure 저장소 연결 설정]
-   [방법: 컨테이너 만들기][방법: 컨테이너 만들기]
-   [방법: 컨테이너에 Blob 업로드][방법: 컨테이너에 Blob 업로드]
-   [방법: 컨테이너의 Blob 나열][방법: 컨테이너의 Blob 나열]
-   [방법: Blob 다운로드][방법: Blob 다운로드]
-   [방법: Blob 삭제][방법: Blob 삭제]
-   [다음 단계][1]

[WACOM.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

## <span id="CreateAccount"></span></a>Azure 저장소 계정 만들기

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <span id="CreateRubyApp"></span></a>Ruby 응용 프로그램 만들기

Ruby 응용 프로그램을 만듭니다. 자세한 내용은
[Azure에서 Ruby 응용 프로그램 만들기][Azure에서 Ruby 응용 프로그램 만들기](영문)를 참조하세요.

## <span id="ConfigAccessStorage"></span></a>저장소에 액세스하도록 응용 프로그램 구성

Azure 저장소를 사용하려면 저장소 REST 서비스와 통신하는 편리한 라이브러리 집합이 포함된 Ruby Azure 패키지를 다운로드하여 사용해야 합니다.

### RubyGems를 사용하여 패키지 가져오기

1.  **PowerShell**(Windows), **Terminal**(Mac) 또는 **Bash**(Unix)와 같은 명령줄 인터페이스를 사용합니다.

2.  명령 창에 "gem install azure"를 입력하여 gem 및 종속성을 설치합니다.

### 패키지 가져오기

원하는 텍스트 편집기를 사용하여 저장소를 사용하려는 Ruby 파일의 맨 위에 다음을 추가합니다.

    require "azure"

## <span id="SetupStorageConnection"></span></a>Azure 저장소 연결 설정

Azure 모듈은 **AZURE\_STORAGE\_ACCOUNT** 및 **AZURE\_STORAGE\_ACCESS\_KEY**
 환경 변수를 읽고 Azure 저장소 계정에 연결하는 데 필요한 정보를 확인합니다. 이러한 환경 변수가 설정되지 않으면 **Azure::BlobService**를 사용하기 전에 다음 코드로 계정 정보를 지정해야 합니다.

    Azure.config.storage_account_name = "<your azure storage account>"
    Azure.config.storage_access_key = "<your azure storage access key>"

이러한 값을 얻으려면

1.  [Azure 관리 포털][Azure 관리 포털]에 로그인합니다.
2.  사용하려는 저장소 계정으로 이동합니다.
3.  탐색 창 아래쪽에서 **키 관리**를 클릭합니다.
4.  팝업 대화 상자에 저장소 계정 이름, 기본 액세스 키 및 보조 액세스 키가 표시됩니다. 액세스 키로 기본 액세스 키, 보조 액세스 키 중 하나를 사용할 수 있습니다.

## <span id="CreateContainer"></span></a>방법: 컨테이너 만들기

**Azure::BlobService** 개체를 통해 컨테이너 및 Blob에 대한 작업을 할 수 있습니다. 컨테이너를 만들려면 **create\_container()** 메서드를 사용합니다.

다음 예제는 컨테이너를 만들거나 컨테이너가 있으면 오류를 출력합니다.

    azure_blob_service = Azure::BlobService.new
    begin
      container = azure_blob_service.create_container("test-container")
    rescue
      puts $!
    end

컨테이너 파일을 공용으로 지정하려는 경우 컨테이너의 사용 권한을 설정할 수 있습니다.

**create\_container()** 호출을 수정하여 **:public\_access\_level** 옵션을 전달할 수 있습니다.

    container = azure_blob_service.create_container("test-container", 
      :public_access_level => "<public access level>")

유효한 **:public\_access\_level** 옵션 값은 다음과 같습니다.

-   **Blob:** 컨테이너 및 Blob 데이터에 대해 전체 public 읽기 권한을 지정합니다. 클라이언트는 익명 요청을 통해 컨테이너 내에서 Blob을 열거할 수 있지만 저장소 계정 내에서 컨테이너를 열거할 수는 없습니다.

-   **컨테이너:** Blob에 대해 public 읽기 권한을 지정합니다. 이 컨테이너 내의 Blob 데이터는 익명 요청을 통해 읽을 수 있으나 컨테이너 데이터는 읽을 수 없습니다. 클라이언트는 익명 요청을 통해 컨테이너 내의 Blob을 열거할 수 없습니다.

또는 **set\_container\_acl()** 메서드로 공용 액세스 수준을 지정하여 컨테이너의 공용 액세스 수준을 수정할 수 있습니다.

다음 예제에서는 공용 액세스 수준을 **container**로 변경합니다.

    azure_blob_service.set_container_acl('test-container', "container")

## <span id="UploadBlob"></span></a>방법: 컨테이너에 Blob 업로드

Blob에 콘텐츠를 업로드하려면 **create\_block\_blob()** 메서드를 사용하여 Blob을 만들고 Blob의 콘텐츠로 파일이나 문자열을 사용합니다.

다음 코드는 **test.png** 파일을 "image-blob"이라는 새 Blob로 컨테이너에 업로드합니다.

    content = File.open("test.png", "rb") { |file| file.read }
    blob = azure_blob_service.create_block_blob(container.name,
      "image-blob", content)
    puts blob.name

## <span id="ListBlobs"></span></a>방법: 컨테이너의 Blob 나열

컨테이너를 나열하려면 **list\_containers()** 메서드를 사용합니다.
컨테이너에 있는 Blob을 나열하려면 **list\_blobs()** 메서드를 사용합니다.

이 메서드는 계정에 대해 모든 컨테이너에 있는 모든 Blob의 URL을 출력합니다.

    containers = azure_blob_service.list_containers()
    containers.each do |container|
      blobs = azure_blob_service.list_blobs(container.name)
      blobs.each do |blob|
        puts blob.name
      end
    end

## <span id="DownloadBlobs"></span></a>방법: Blob 다운로드

Blob을 다운로드하려면 **get\_blob()** 메서드를 사용하여 콘텐츠를 가져옵니다.

다음 예제는 **get\_blob()**을 사용하여 "image-blob"의 콘텐츠를 다운로드하고 그 콘텐츠를 로컬 파일에 쓰는 방법을 보여 줍니다.

    blob, content = azure_blob_service.get_blob(container.name,"image-blob")
    File.open("download.png","wb") {|f| f.write(content)}

## <span id="DeleteBlob"></span></a>방법: Blob 삭제

마지막으로 Blob을 삭제하려면 **delete\_blob()** 메서드를 사용합니다. 다음 예제는 Blob을 삭제하는 방법을 보여 줍니다.

    azure_blob_service.delete_blob(container.name, "image-blob")

## <span id="NextSteps"></span></a>다음 단계

이제 Blob 저장소의 기본 사항을 배웠으므로 다음 링크를 따라 좀 더 복잡한 저장소 작업을 수행하는 방법을 알아보세요.

-   다음 MSDN 참조를 확인하세요. [Azure에서 데이터 저장 및 액세스][Azure에서 데이터 저장 및 액세스]
-   [Azure 저장소 팀 블로그][Azure 저장소 팀 블로그](영문)를 방문하세요.
-   GitHub에서 [Azure SDK for Ruby][Azure SDK for Ruby](영문) 리포지토리를 방문하세요.

  [다음 단계]: #next-steps
  [Blob 서비스 정의]: #what-is
  [개념]: #concepts
  [Azure 저장소 계정 만들기]: #CreateAccount
  [Ruby 응용 프로그램 만들기]: #CreateRubyApp
  [저장소에 액세스하도록 응용 프로그램 구성]: #ConfigAccessStorage
  [Azure 저장소 연결 설정]: #SetupStorageConnection
  [방법: 컨테이너 만들기]: #CreateContainer
  [방법: 컨테이너에 Blob 업로드]: #UploadBlob
  [방법: 컨테이너의 Blob 나열]: #ListBlobs
  [방법: Blob 다운로드]: #DownloadBlobs
  [방법: Blob 삭제]: #DeleteBlob
  [1]: #NextSteps
  [Azure에서 Ruby 응용 프로그램 만들기]: /ko-kr/develop/ruby/tutorials/web-app-with-linux-vm/
  [Azure 관리 포털]: https://manage.windowsazure.com/
  [Azure에서 데이터 저장 및 액세스]: http://msdn.microsoft.com/ko-kr/library/windowsazure/gg433040.aspx
  [Azure 저장소 팀 블로그]: http://blogs.msdn.com/b/windowsazurestorage/
  [Azure SDK for Ruby]: https://github.com/WindowsAzure/azure-sdk-for-ruby
