<properties urlDisplayName="Blob Service" pageTitle="Blob 저장소 사용 방법(Python) | Microsoft Azure" metaKeywords="Azure blob service Python, Azure blobs Python" description="Learn how to use the Azure Blob service to upload, list, download, and delete blobs." metaCanonical="" disqusComments="1" umbracoNaviHide="0" services="storage" documentationCenter="Python" title="How to use the Blob service from Python" authors="huvalo" videoId="" scriptId="" manager="wpickett" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="09/19/2014" ms.author="robmcm" />

# Python에서 Blob 저장소 서비스를 사용하는 방법
이 가이드에서는 Azure Blob 저장소 서비스를 사용하여 일반 시나리오를 수행하는 방법을 보여 줍니다. 샘플은 Python API를 사용하여 작성되었습니다. 여기서 다루는 시나리오에는 Blob **업로드**, **나열**, **다운로드** 및 **삭제**가 포함됩니다. Blob에 대한 자세한 내용은 [다음 단계][] 섹션을 참조하세요.

## 목차

[Blob 저장소 정의][]   
 [개념][]   
 [Azure 저장소 계정 만들기][]   
 [방법: 컨테이너 만들기][]   
 [방법: 컨테이너에 Blob 업로드][]   
 [방법: 컨테이너의 Blob 나열][]   
 [방법: Blob 다운로드][]   
 [방법: Blob 삭제][]   
 [방법: 대규모 Blob 업로드 및 다운로드][]   
 [다음 단계][]

[WACOM.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

## <a name="create-account"> </a>Azure 저장소 계정 만들기

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <a name="create-container"> </a>방법: 컨테이너 만들기

**참고:** Python 또는 클라이언트 라이브러리를 설치해야 하는 경우 [Python 설치 가이드](../python-how-to-install/)(영문)를 참조하세요.


**BlobService** 개체를 통해 컨테이너 및 Blob에 대한 작업을 수행할 수 있습니다. 다음 코드에서는 **BlobService** 개체를 만듭니다. 프로그래밍 방식으로 Azure 저장소에 액세스하려는 Python 파일의 맨 위쪽에 다음을 추가합니다.

	from azure.storage import BlobService

다음 코드에서는 저장소 계정 이름 및 계정 키를 사용하는 **BlobService** 개체를 만듭니다.  'myaccount' 및 'mykey'를 실제 계정 및 키로 바꾸세요.

	blob_service = BlobService(account_name='myaccount', account_key='mykey')

모든 저장소 Blob은 컨테이너에 있습니다. **BlobService** 개체를 사용하면 컨테이너가 없는 경우 새로 만들 수 있습니다.

	blob_service.create_container('mycontainer')

기본적으로 새 컨테이너는 전용이므로 이 컨테이너에서 Blob을 다운로드하려면 저장소 액세스 키(위에서 한 것과 같이)를 지정해야 합니다. 컨테이너 내의 파일을 모든 사용자가 사용할 수 있도록 설정하려는 경우 다음 코드를 사용하여 컨테이너를 만들고 공용 액세스 수준을 전달할 수 있습니다.

	blob_service.create_container('mycontainer', x_ms_blob_public_access='container') 

또는 다음 코드를 사용하여 컨테이너를 만든 후 수정할 수 있습니다.

	blob_service.set_container_acl('mycontainer', x_ms_blob_public_access='container')

이렇게 변경한 후에는 인터넷의 모든 사용자가 공용 컨테이너의 Blob을 볼 수 있지만 수정하거나 삭제는 할 수 없습니다.

## <a name="upload-blob"> </a>방법: 컨테이너에 Blob 업로드

Blob에 데이터를 업로드하려면 **put\_block\_blob\_from\_path**, **put\_block\_blob\_from\_file**, **put\_block\_blob\_from\_bytes** 또는 **put\_block\_blob\_from\_text** 메서드를 사용합니다. 이러한 메서드는 데이터의 크기가 64MB를 초과할 경우 필요한 청크를 수행하는 고급 메서드입니다.

**put\_block\_blob\_from\_path**는 지정된 경로에서 파일의 내용을 업로드하고, **put\_block\_blob\_from\_file**은 이미 열려 있는 파일/스트림에서 내용을 업로드합니다. **put\_block\_blob\_from\_bytes**는 바이트 배열을 업로드하고, **put\_block\_blob\_from\_text**는 지정된 인코딩(기본값: UTF-8)을 사용하여 지정된 텍스트 값을 업로드합니다.

다음 예제에서는 **task1.txt** 파일의 내용을 **myblob** Blob에 업로드합니다.

	blob_service.put_block_blob_from_path('mycontainer', 'myblob', 'task1.txt')

## <a name="list-blob"> </a>방법: 컨테이너의 Blob 나열

컨테이너의 Blob을 나열하려면 **list\_blobs** 메서드를
**for** 루프와 함께 사용하여 컨테이너에 있는 각 Blob의 이름을 표시합니다. 다음
코드에서는 컨테이너에 있는 각 Blob의 **이름** 및 **url**을 콘솔에 출력합니다.

	blobs = blob_service.list_blobs('mycontainer')
	for blob in blobs:
		print(blob.name)
		print(blob.url)

## <a name="download-blobs"> </a>방법: Blob 다운로드

Blob에서 데이터를 다운로드하려면 **get\_blob\_to\_path**, **get\_blob\_to\_file**, **get\_blob\_to\_bytes** 또는 **get\_blob\_to\_text**를 사용합니다. 이러한 메서드는 데이터의 크기가 64MB를 초과할 경우 필요한 청크를 수행하는 고급 메서드입니다.

다음 예제에서는 **get\_blob\_to\_path**를 사용하여 **myblob** Blob의 내용을 다운로드하여 **out-task1.txt** 파일에 저장하는 방법을 보여 줍니다.

	blob_service.get_blob_to_path('mycontainer', 'myblob', 'out-task1.txt')

## <a name="delete-blobs"> </a>방법: Blob 삭제

마지막으로 Blob을 삭제하려면 **delete_blob**을 호출합니다.

	blob_service.delete_blob('mycontainer', 'myblob') 

## <a name="next-steps"> </a>다음 단계

이제 Blob 저장소의 기본 사항을 살펴보았으므로 다음 링크를 통해
좀 더 복잡한 저장소 작업을 수행하는 방법을 알아보세요.

-   다음 MSDN 참조를 확인하세요. [Azure에 데이터 저장 및 액세스][]
-   [Azure 저장소 팀 블로그][](영문) 방문

  [다음 단계]: #next-steps
  [Blob 저장소 정의]: #what-is
  [개념]: #concepts
  [Azure 저장소 계정 만들기]: #create-account
  [방법: 컨테이너 만들기]: #create-container
  [방법: 컨테이너에 Blob 업로드]: #upload-blob
  [방법: 컨테이너의 Blob 나열]: #list-blob
  [방법: Blob 다운로드]: #download-blobs
  [방법: Blob 삭제]: #delete-blobs
  [방법: 대규모 Blob 업로드 및 다운로드]: #large-blobs
  [Azure에 데이터 저장 및 액세스]: http://msdn.microsoft.com/ko-kr/library/windowsazure/gg433040.aspx
  [Azure 저장소 팀 블로그]: http://blogs.msdn.com/b/windowsazurestorage/

<!--HONumber=35_1-->
