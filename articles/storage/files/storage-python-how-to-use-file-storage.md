---
title: Python을 사용하여 Azure Files용으로 개발 | Microsoft Docs
description: Azure Files를 사용하여 파일 데이터를 저장하는 Python 애플리케이션 및 서비스를 개발하는 방법에 대해 알아봅니다.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 10/08/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-python
ms.openlocfilehash: 11c31b9ce3c5a8d8fba18d8e7c46ac38b0559aec
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/08/2020
ms.locfileid: "91856316"
---
# <a name="develop-for-azure-files-with-python"></a>Python을 사용하여 Azure Files 개발

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

Azure Files를 사용 하 여 파일 데이터를 저장 하는 응용 프로그램 또는 서비스를 개발 하는 데 Python을 사용 하는 기본 사항을 알아봅니다. 간단한 콘솔 앱을 만들고 Python 및 Azure Files를 사용 하 여 기본 작업을 수행 하는 방법을 알아봅니다.

- Azure 파일 공유 만들기
- 디렉터리 만들기
- Azure 파일 공유의 파일 및 디렉터리 열거
- 파일 업로드, 다운로드 및 삭제
- 스냅숏을 사용 하 여 파일 공유 백업 만들기

> [!NOTE]
> Azure Files는 SMB를 통해 액세스할 수 있기 때문에 표준 Python I/O 클래스 및 함수를 사용하여 Azure File 공유에 액세스하는 간단한 애플리케이션을 작성할 수 있습니다. 이 문서에서는 Azure Files Storage Python SDK를 사용 하는 앱을 작성 하는 방법에 대해 설명 합니다 .이 SDK는 [Azure Files REST API](/rest/api/storageservices/file-service-rest-api) 를 사용 하 여 Azure Files와 통신 합니다.

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Azure Storage SDK for Python 다운로드 및 설치

> [!NOTE]
> Azure Storage SDK for Python 버전 0.36 또는 이전 버전에서 업그레이드하는 경우 최신 패키지를 설치하기 전에 `pip uninstall azure-storage`를 사용하여 이전 SDK를 제거합니다.

# <a name="python-v12"></a>[Python v12](#tab/python)

[Python 용 Azure File Storage 클라이언트 라이브러리 v12](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-share) 에는 python 2.7 또는 3.5이 필요 합니다.

# <a name="python-v2"></a>[Python v2](#tab/python2)

[Azure Storage SDK for Python](https://github.com/azure/azure-storage-python)에는 Python 2.7, 3.3, 3.4, 3.5 또는 3.6이 필요합니다.

---

## <a name="install-via-pypi"></a>PyPI를 통해 설치

PyPi(Python Package Index)를 통해 설치하려면 다음을 입력합니다.

# <a name="python-v12"></a>[Python v12](#tab/python)

```console
pip install azure-storage-file-share
```

# <a name="python-v2"></a>[Python v2](#tab/python2)

```console
pip install azure-storage-file
```

### <a name="view-the-sample-application"></a>샘플 애플리케이션 보기

Azure Files에서 Python을 사용 하는 방법을 보여 주는 샘플 응용 프로그램을 보고 실행 하려면 [Azure Storage: python에서 Azure Files 시작](https://github.com/Azure-Samples/storage-file-python-getting-started)을 참조 하세요.

예제 응용 프로그램을 실행 하려면 및 패키지를 모두 설치 했는지 확인 `azure-storage-file` `azure-storage-common` 합니다.

---

## <a name="set-up-your-application-to-use-azure-files"></a>Azure Files를 사용하도록 애플리케이션 설정

이 문서의 코드 조각을 사용 하려면 Python 소스 파일의 맨 위에 다음을 추가 합니다.

# <a name="python-v12"></a>[Python v12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_Imports":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

```python
from azure.storage.file import FileService
```

---

## <a name="set-up-a-connection-to-azure-files"></a>Azure Files에 대한 연결 설정

# <a name="python-v12"></a>[Python v12](#tab/python)

[ShareServiceClient](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.shareserviceclient) 를 사용 하면 공유, 디렉터리 및 파일을 사용할 수 있습니다. 다음 코드는 `ShareServiceClient` 저장소 계정 연결 문자열을 사용 하 여 개체를 만듭니다.

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_CreateShareServiceClient":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

개체를 사용 `FileService` 하면 공유, 디렉터리 및 파일을 사용할 수 있습니다. 다음 코드는 스토리지 계정 이름 및 계정 키를 사용하는 `FileService` 개체를 만듭니다. `<myaccount>` 및 `<mykey>`를 사용자의 계정 이름 및 키로 바꾸세요.

```python
file_service = FileService(account_name='myaccount', account_key='mykey')
```

---

## <a name="create-an-azure-file-share"></a>Azure 파일 공유 만들기

# <a name="python-v12"></a>[Python v12](#tab/python)

다음 코드 예제에서는 [Shareclient](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.shareclient) 개체를 사용 하 여 공유가 없는 경우 새로 만듭니다.

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_CreateFileShare":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

다음 코드 예제에서는 개체를 사용 하 여 `FileService` 존재 하지 않는 경우 공유를 만듭니다.

```python
file_service.create_share('myshare')
```

---

## <a name="create-a-directory"></a>디렉터리 만들기

루트 디렉터리에 이들 모두를 포함하는 대신 하위 디렉터리 내에서 파일을 배치하여 스토리지를 구성할 수 있습니다.

# <a name="python-v12"></a>[Python v12](#tab/python)

다음 메서드는 [Sharedirectoryclient](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.sharedirectoryclient) 개체를 사용 하 여 지정 된 파일 공유의 루트에 디렉터리를 만듭니다.

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_CreateDirectory":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

아래 코드는 루트 디렉터리 아래에 *sampledir*이라는 이름의 하위 디렉터리를 만듭니다.

```python
file_service.create_directory('myshare', 'sampledir')
```

---

## <a name="upload-a-file"></a>파일 업로드

이 섹션에서는 로컬 저장소에서 Azure File Storage로 파일을 업로드 하는 방법을 알아봅니다.

# <a name="python-v12"></a>[Python v12](#tab/python)

다음 메서드는 지정 된 파일의 내용을 지정 된 Azure 파일 공유의 지정 된 디렉터리에 업로드 합니다.

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_UploadFile":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Azure 파일 공유에는 파일이 상주할 수 있는 최소한의 루트 디렉터리가 포함 되어 있습니다. 파일을 만들고 데이터를 업로드 하려면 `create_file_from_path` ,, `create_file_from_stream` 또는 메서드를 사용 `create_file_from_bytes` `create_file_from_text` 합니다. 데이터의 크기가 64를 초과 하는 경우 필요한 청크를 수행 하는 상위 수준 메서드입니다.

`create_file_from_path`는 지정된 경로에서 파일의 콘텐츠를 업로드하고, `create_file_from_stream`은 이미 열려 있는 파일/스트림에서 콘텐츠를 업로드합니다. `create_file_from_bytes`는 바이트 배열을 업로드하고, `create_file_from_text`는 지정된 인코딩(기본값: UTF-8)을 사용하여 지정된 텍스트 값을 업로드합니다.

다음 예제에서는 *sunset.png* 파일의 내용을 **myfile** 파일에 업로드합니다.

```python
from azure.storage.file import ContentSettings
file_service.create_file_from_path(
    'myshare',
    None,  # We want to create this file in the root directory, so we specify None for the directory_name
    'myfile',
    'sunset.png',
    content_settings=ContentSettings(content_type='image/png'))
```

---

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Azure 파일 공유의 파일 및 디렉터리 열거

# <a name="python-v12"></a>[Python v12](#tab/python)

하위 디렉터리의 파일 및 디렉터리를 나열 하려면 [list_directories_and_files](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.shareclient#list-directories-and-files-directory-name-none--name-starts-with-none--marker-none----kwargs-) 메서드를 사용 합니다. 이 메서드는 자동 페이징 반복 가능한을 반환 합니다. 다음 코드는 지정 된 디렉터리에 있는 각 파일과 하위 디렉터리의 **이름을** 콘솔에 출력 합니다.

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_ListFilesAndDirs":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

공유의 파일 및 디렉터리를 나열하려면 **list\_directories\_and\_files** 메서드를 사용합니다. 이 메서드는 생성기를 반환합니다. 다음 코드는 공유에 있는 각 파일 및 디렉터리의 **이름** 을 콘솔에 출력합니다.

```python
generator = file_service.list_directories_and_files('myshare')
for file_or_dir in generator:
    print(file_or_dir.name)
```

---

## <a name="download-a-file"></a>파일 다운로드

# <a name="python-v12"></a>[Python v12](#tab/python)

파일에서 데이터를 다운로드 하려면 [download_file](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.sharefileclient#download-file-offset-none--length-none----kwargs-)을 사용 합니다.

다음 예제에서는를 사용 하 여 `download_file` 지정 된 파일의 콘텐츠를 가져오고 파일 이름에 **다운로드 된-** 앞에 있는 파일을 로컬에 저장 하는 방법을 보여 줍니다.

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_DownloadFile":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

파일에서 데이터를 다운로드하려면 `get_file_to_path`, `get_file_to_stream`, `get_file_to_bytes` 또는 `get_file_to_text`를 사용합니다. 데이터의 크기가 64를 초과 하는 경우 필요한 청크를 수행 하는 상위 수준 메서드입니다.

다음 예제에서는 `get_file_to_path`를 사용하여 **myfile** 파일의 콘텐츠를 다운로드한 다음 *out-sunset.png* 파일에 저장하는 방법을 보여 줍니다.

```python
file_service.get_file_to_path('myshare', None, 'myfile', 'out-sunset.png')
```

---

## <a name="create-a-share-snapshot"></a>공유 스냅샷 만들기

전체 파일 공유의 특정 시점 복사본을 만들 수 있습니다.

# <a name="python-v12"></a>[Python v12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_CreateSnapshot":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

```python
snapshot = file_service.snapshot_share(share_name)
snapshot_id = snapshot.snapshot
```

**메타데이터를 사용하여 공유 스냅샷 만들기**

```python
metadata = {"foo": "bar"}
snapshot = file_service.snapshot_share(share_name, metadata=metadata)
```

---

## <a name="list-shares-and-snapshots"></a>공유 및 스냅샷 나열

특정 공유에 대한 모든 스냅샷을 나열할 수 있습니다.

# <a name="python-v12"></a>[Python v12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_ListSharesAndSnapshots":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

```python
shares = list(file_service.list_shares(include_snapshots=True))
```

---

## <a name="browse-share-snapshot"></a>공유 스냅샷 찾아보기

각 공유 스냅숏을 찾아보고 해당 시점에서 파일 및 디렉터리를 검색할 수 있습니다.

# <a name="python-v12"></a>[Python v12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_BrowseSnapshotDir":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

```python
directories_and_files = list(
    file_service.list_directories_and_files(share_name, snapshot=snapshot_id))
```

---

## <a name="get-file-from-share-snapshot"></a>공유 스냅샷에서 파일 가져오기

공유 스냅숏에서 파일을 다운로드할 수 있습니다. 이를 통해 파일의 이전 버전을 복원할 수 있습니다.

# <a name="python-v12"></a>[Python v12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_DownloadSnapshotFile":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

```python
with open(FILE_PATH, 'wb') as stream:
    file = file_service.get_file_to_stream(
        share_name, directory_name, file_name, stream, snapshot=snapshot_id)
```

---

## <a name="delete-a-single-share-snapshot"></a>단일 공유 스냅샷 삭제
단일 공유 스냅샷을 삭제할 수 있습니다.

# <a name="python-v12"></a>[Python v12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_DeleteSnapshot":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

```python
file_service.delete_share(share_name, snapshot=snapshot_id)
```

---

## <a name="delete-a-file"></a>파일 삭제

# <a name="python-v12"></a>[Python v12](#tab/python)

파일을 삭제 하려면 [delete_file](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.sharefileclient#delete-file---kwargs-)를 호출 합니다.

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_DeleteFile":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

파일을 삭제 하려면를 호출 `delete_file` 합니다.

```python
file_service.delete_file('myshare', None, 'myfile')
```

---

## <a name="delete-share-when-share-snapshots-exist"></a>공유 스냅샷이 존재하는 경우 공유 삭제

# <a name="python-v12"></a>[Python v12](#tab/python)

스냅숏이 포함 된 공유를 삭제 하려면를 사용 하 여 [delete_share](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.shareclient#delete-share-delete-snapshots-false----kwargs-) 를 호출 `delete_snapshots=True` 합니다.

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_DeleteShare":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

모든 스냅샷을 먼저 삭제해야만 스냅샷을 포함한 공유를 삭제할 수 있습니다.

```python
file_service.delete_share(share_name, delete_snapshots=DeleteSnapshot.Include)
```

---

## <a name="next-steps"></a>다음 단계

이제 Python으로 Azure Files를 조작하는 방법을 배웠으므로 다음 링크를 통해 자세한 내용을 알아보세요.

- [Python 개발자 센터](/azure/developer/python/)
- [Azure Storage 서비스 REST API](/rest/api/azure/)
- [Microsoft Azure Storage SDK for Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage)
