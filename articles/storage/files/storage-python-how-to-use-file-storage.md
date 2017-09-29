---
title: "Python을 사용하여 Azure Files용으로 개발 | Microsoft Docs"
description: "Azure Files를 사용하여 파일 데이터를 저장하는 Python 응용 프로그램 및 서비스를 개발하는 방법에 대해 알아봅니다."
services: storage
documentationcenter: python
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 09/19/2017
ms.author: tamram
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: c9c7ee20e511d7aa6261119e7307e2b96682a6bb
ms.contentlocale: ko-kr
ms.lasthandoff: 09/25/2017

---

# <a name="develop-for-azure-files-with-python"></a>Python을 사용하여 Azure Files 개발
[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-try-azure-tools-files](../../../includes/storage-try-azure-tools-files.md)]

## <a name="about-this-tutorial"></a>이 자습서 정보
이 자습서에서는 Azure Files를 사용하여 파일 데이터를 저장하는 응용 프로그램이나 서비스를 Python을 사용하여 개발하는 데 필요한 기본 사항을 보여 줍니다. 즉 간단한 콘솔 응용 프로그램을 만들고, Python 및 Azure Files를 통해 기본 작업을 수행하는 방법을 보여 줍니다.

* Azure 파일 공유 만들기
* 디렉터리 만들기
* Azure 파일 공유에 파일 및 디렉터리 열거
* 파일 업로드, 다운로드 및 삭제

> [!Note]  
> Azure Files는 SMB를 통해 액세스할 수 있기 때문에 표준 Python I/O 클래스 및 함수를 사용하여 Azure File 공유에 액세스하는 간단한 응용 프로그램을 작성할 수 있습니다. 이 문서에서는 [Azure Files REST API](https://docs.microsoft.com/en-us/rest/api/storageservices/fileservices/file-service-rest-api)를 사용하여 Azure Files와 통신하는 Azure Storage Python SDK를 사용하는 응용 프로그램을 작성하는 방법에 대해 설명합니다.

### <a name="set-up-your-application-to-use-azure-files"></a>Azure Files를 사용하도록 응용 프로그램 설정
프로그래밍 방식으로 Azure Storage에 액세스하려는 Python 원본 파일의 맨 위쪽에 다음을 추가합니다.

```python
from azure.storage.file import FileService
```

### <a name="set-up-a-connection-to-azure-files"></a>Azure Files에 대한 연결 설정 
`FileService` 개체를 사용하면 공유, 디렉터리 및 파일 작업을 수행할 수 있습니다. 다음 코드는 저장소 계정 이름 및 계정 키를 사용하는 `FileService` 개체를 만듭니다. `<myaccount>` 및 `<mykey>`를 사용자의 계정 이름 및 키로 바꾸세요.

```python
file_service = FileService(account_name='myaccount', account_key='mykey')
```

### <a name="create-an-azure-file-share"></a>Azure 파일 공유 만들기
다음 코드 예제에서는 `FileService` 개체를 사용하여 공유가 없는 경우 새로 만들 수 있습니다.

```python
file_service.create_share('myshare')
```

### <a name="create-a-directory"></a>디렉터리 만들기
또한 루트 디렉터리에 이들 모두를 포함하는 대신 하위 디렉터리 내에서 파일을 배치하여 저장소를 구성할 수 있습니다. Azure Files를 사용하면 계정이 허용하는 만큼 많은 디렉터리를 만들 수 있습니다. 아래 코드는 루트 디렉터리 아래에 **sampledir** 라는 이름의 하위 디렉터리를 만듭니다.

```python
file_service.create_directory('myshare', 'sampledir')
```

### <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Azure 파일 공유에 파일 및 디렉터리 열거
공유의 파일 및 디렉터리를 나열하려면 **list\_directories\_and\_files** 메서드를 사용합니다. 이 메서드는 생성기를 반환합니다. 다음 코드는 공유에 있는 각 파일 및 디렉터리의 **이름** 을 콘솔에 출력합니다.

```python
generator = file_service.list_directories_and_files('myshare')
for file_or_dir in generator:
    print(file_or_dir.name)
```

### <a name="upload-a-file"></a>파일 업로드 
Azure 파일 공유에는 파일이 상주할 수 있는 최소한의 루트 디렉터리가 포함되어 있습니다. 이 섹션에서는 로컬 저장소에서 공유의 루트 디렉터리로 파일을 업로드하는 방법을 배웁니다.

파일을 만들고 데이터를 업로드하려면 `create_file_from_path`, `create_file_from_stream`, `create_file_from_bytes` 또는 `create_file_from_text` 메서드를 사용합니다. 이러한 메서드는 데이터의 크기가 64MB를 초과할 경우 필요한 청크를 수행하는 고급 메서드입니다.

`create_file_from_path`는 지정된 경로에서 파일의 콘텐츠를 업로드하고, `create_file_from_stream`은 이미 열려 있는 파일/스트림에서 콘텐츠를 업로드합니다. `create_file_from_bytes`는 바이트 배열을 업로드하고, `create_file_from_text`는 지정된 인코딩(기본값: UTF-8)을 사용하여 지정된 텍스트 값을 업로드합니다.

다음 예제에서는 **sunset.png** 파일의 내용을 **myfile** 파일에 업로드합니다.

```python
from azure.storage.file import ContentSettings
file_service.create_file_from_path(
    'myshare',
    None, # We want to create this blob in the root directory, so we specify None for the directory_name
    'myfile',
    'sunset.png',
    content_settings=ContentSettings(content_type='image/png'))
```

### <a name="download-a-file"></a>파일 다운로드
파일에서 데이터를 다운로드하려면 `get_file_to_path`, `get_file_to_stream`, `get_file_to_bytes` 또는 `get_file_to_text`를 사용합니다. 이러한 메서드는 데이터의 크기가 64MB를 초과할 경우 필요한 청크를 수행하는 고급 메서드입니다.

다음 예제에서는 `get_file_to_path`를 사용하여 **myfile** 파일의 콘텐츠를 다운로드한 다음 **out-sunset.png** 파일에 저장하는 방법을 보여 줍니다.

```python
file_service.get_file_to_path('myshare', None, 'myfile', 'out-sunset.png')
```

### <a name="delete-a-file"></a>파일 삭제
마지막으로 파일을 삭제하려면 `delete_file`을 호출합니다.

```python
file_service.delete_file('myshare', None, 'myfile')
```

## <a name="next-steps"></a>다음 단계
이제 Python으로 Azure Files를 조작하는 방법을 배웠으므로 다음 링크를 통해 자세한 내용을 알아보세요.

* [Python 개발자 센터](/develop/python/)
* [Azure 저장소 서비스 REST API](http://msdn.microsoft.com/library/azure/dd179355)
* [Microsoft Azure Storage SDK for Python](https://github.com/Azure/azure-storage-python)
