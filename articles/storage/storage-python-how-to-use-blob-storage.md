<properties
	pageTitle="Python에서 Azure Blob 저장소를 사용하는 방법 | Microsoft Azure"
	description="Python에서 Azure Blob 저장소를 사용하여 Blob을 업로드, 나열, 다운로드 및 삭제하는 방법을 알아봅니다."
	services="storage"
	documentationCenter="python"
	authors="emgerner-msft"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="python"
	ms.topic="article"
	ms.date="12/11/2015"
	ms.author="emgerner"/>

# Python에서 Azure Blob 저장소를 사용하는 방법

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

## 개요

이 문서에서는 Blob 저장소를 사용하여 일반 시나리오를 수행하는 방법을 보여 줍니다. 샘플은 Python으로 작성되었으며 [Python Azure 저장소 패키지][]를 사용합니다. 여기서 다루는 시나리오에는 Blob 업로드, 나열, 다운로드 및 삭제가 포함됩니다.

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## 컨테이너 만들기

> [AZURE.NOTE]Python 또는 [Python Azure 패키지][]를 설치해야 하는 경우 [Python 설치 가이드](../python-how-to-install.md)를 참조하세요.

**BlobService** 개체를 통해 컨테이너 및 Blob에 대한 작업을 할 수 있습니다. 다음 코드는 **BlobService** 개체를 만듭니다. 프로그래밍 방식으로 Azure 저장소에 액세스하려는 Python 파일의 위쪽에 다음을 추가합니다.

	from azure.storage.blob import BlobService

다음 코드는 저장소 계정 이름 및 계정 키를 사용하는 **BlobService** 개체를 만듭니다. 'myaccount' 및 'mykey'를 실제 계정 및 키로 바꾸세요.

	blob_service = BlobService(account_name='myaccount', account_key='mykey')

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

다음 코드 예제에서는 **BlobService** 개체를 사용하여 컨테이너가 없는 경우 새로 만들 수 있습니다.

	blob_service.create_container('mycontainer')

기본적으로 새 컨테이너는 전용이므로 이 컨테이너에서 Blob을 다운로드하려면 앞에서 한 것처럼 저장소 액세스 키를 지정해야 합니다. 컨테이너 내의 파일을 모든 사용자가 사용할 수 있도록 설정하려는 경우 다음 코드를 사용하여 컨테이너를 만들고 공용 액세스 수준을 전달할 수 있습니다.

	blob_service.create_container('mycontainer', x_ms_blob_public_access='container')

또는 다음 코드를 사용하여 컨테이너를 만든 후 수정할 수 있습니다.

	blob_service.set_container_acl('mycontainer', x_ms_blob_public_access='container')

이렇게 변경한 후에는 인터넷의 모든 사용자가 공용 컨테이너의 Blob을 볼 수 있지만 수정하거나 삭제는 할 수 없습니다.

## 컨테이너에 Blob 업로드

Blob에 데이터를 업로드하려면 **put\_block\_blob\_from\_path**, **put\_block\_blob\_from\_file**, **put\_block\_blob\_from\_bytes** 또는 **put\_block\_blob\_from\_text** 메서드를 사용합니다. 이러한 메서드는 데이터의 크기가 64MB를 초과할 경우 필요한 청크를 수행하는 고급 메서드입니다.

**put\_block\_blob\_from\_path**는 지정된 경로에서 파일의 내용을 업로드하고, **put\_block\_blob\_from\_file**은 이미 열린 파일/스트림에서 내용을 업로드합니다. **put\_block\_blob\_from\_bytes**는 바이트 배열을 업로드하고, **put\_block\_blob\_from\_text**는 지정된 인코딩을 사용하여 지정된 텍스트 값을 업로드합니다(기본값은 UTF-8).

다음 예제에서는 **sunset.png** 파일의 내용을 **myblob** Blob에 업로드합니다.

	blob_service.put_block_blob_from_path(
        'mycontainer',
        'myblob',
        'sunset.png',
        x_ms_blob_content_type='image/png'
    )

## 컨테이너의 Blob 나열

컨테이너의 Blob을 나열하려면 **list\\_blobs** 메서드를 사용합니다. **list\_blobs**를 호출할 때마다 결과의 한 세그먼트가 반환됩니다. 모든 결과를 가져오려면 결과의 **next\_marker**를 선택하고 필요에 따라 **list\_blobs**를 다시 호출합니다. 다음 코드는 컨테이너에 있는 각 Blob의 **이름**을 콘솔에 출력합니다.

	blobs = []
	marker = None
	while True:
		batch = blob_service.list_blobs('mycontainer', marker=marker)
		blobs.extend(batch)
		if not batch.next_marker:
			break
		marker = batch.next_marker
	for blob in blobs:
		print(blob.name)

## Blob 다운로드

결과의 각 세그먼트는 최대 5000개까지 다양한 개수의 Blob을 포함할 수 있습니다. 특정 세그먼트에 대한 **next\_marker**가 있는 경우 컨테이너에 포함된 Blob이 더 많을 수 있습니다.

Blob에서 데이터를 다운로드하려면 **get\_blob\_to\_path**, **get\_blob\_to\_file**, **get\_blob\_to\_bytes** 또는 **get\_blob\_to\_text**를 사용합니다. 이러한 메서드는 데이터의 크기가 64MB를 초과할 경우 필요한 청크를 수행하는 고급 메서드입니다.

다음 예제에서는 **get\_blob\_to\_path**를 사용하여 **myblob** Blob의 내용을 다운로드 후 **out-sunset.png** 파일에 저장하는 방법을 보여 줍니다.

	blob_service.get_blob_to_path('mycontainer', 'myblob', 'out-sunset.png')

## Blob 삭제

마지막으로 Blob을 삭제하려면 **delete\_blob**을 호출합니다.

	blob_service.delete_blob('mycontainer', 'myblob')

## 다음 단계

이제 Blob 저장소의 기본 사항을 배웠으므로 다음 링크를 따라 좀더 복잡한 저장소 작업에 대해 알아보세요.

- [Azure 저장소 팀 블로그][](영문)를 방문하세요.
- [AzCopy 명령줄 유틸리티로 데이터 전송](storage-use-azcopy)

자세한 내용을 보려면 [Python 개발자 센터](/develop/python/)도 참조하세요.

[Azure 저장소 팀 블로그]: http://blogs.msdn.com/b/windowsazurestorage/
[Python Azure 패키지]: https://pypi.python.org/pypi/azure
[Python Azure 저장소 패키지]: https://pypi.python.org/pypi/azure-storage

<!---HONumber=AcomDC_1217_2015-->