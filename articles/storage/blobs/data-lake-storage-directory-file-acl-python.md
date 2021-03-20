---
title: Python을 사용 하 여 Azure Data Lake Storage Gen2에서 데이터 관리
description: Python을 사용 하 여 계층적 네임 스페이스를 사용 하는 저장소 계정의 디렉터리와 파일을 관리 합니다.
author: normesta
ms.service: storage
ms.date: 02/17/2021
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-python
ms.openlocfilehash: a143c0aa19241b532cabff95fe6bf85679e4007c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100652295"
---
# <a name="use-python-to-manage-directories-and-files-in-azure-data-lake-storage-gen2"></a>Python을 사용 하 여 Azure Data Lake Storage Gen2에서 디렉터리 및 파일 관리

이 문서에서는 Python을 사용 하 여 계층적 네임 스페이스를 포함 하는 저장소 계정의 디렉터리와 파일을 만들고 관리 하는 방법을 보여 줍니다.

디렉터리 및 파일의 ACL (액세스 제어 목록)을 가져오거나 설정 하 고 업데이트 하는 방법에 대해 알아보려면 [Python을 사용 하 여 Azure Data Lake Storage Gen2에서 acl 관리](data-lake-storage-acl-python.md)를 참조 하세요.

[패키지 (Python 패키지 인덱스)](https://pypi.org/project/azure-storage-file-datalake/)  |  [샘플](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)  |  [API 참조](/python/api/azure-storage-file-datalake/azure.storage.filedatalake)  |  [Gen1 To Gen2 mapping](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)  |  [사용자 의견 제공](https://github.com/Azure/azure-sdk-for-python/issues)

## <a name="prerequisites"></a>필수 구성 요소

- Azure 구독 [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.

- 계층적 네임 스페이스를 사용 하는 저장소 계정입니다. 만들려면 [다음 지침](create-data-lake-storage-account.md)을 수행합니다.

## <a name="set-up-your-project"></a>프로젝트 설정

[Pip](https://pypi.org/project/pip/)를 사용 하 여 Python 용 Azure Data Lake Storage 클라이언트 라이브러리를 설치 합니다.

```
pip install azure-storage-file-datalake
```

이러한 import 문을 코드 파일의 맨 위에 추가 합니다.

```python
import os, uuid, sys
from azure.storage.filedatalake import DataLakeServiceClient
from azure.core._match_conditions import MatchConditions
from azure.storage.filedatalake._models import ContentSettings
```

## <a name="connect-to-the-account"></a>계정에 연결

이 문서의 코드 조각을 사용 하려면 저장소 계정을 나타내는 **DataLakeServiceClient** 인스턴스를 만들어야 합니다. 

### <a name="connect-by-using-an-account-key"></a>계정 키를 사용 하 여 연결

계정에 연결 하는 가장 쉬운 방법입니다. 

이 예에서는 계정 키를 사용 하 여 **DataLakeServiceClient** 인스턴스를 만듭니다.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_AuthorizeWithKey":::

- `storage_account_name` 자리 표시자 값을 스토리지 계정 이름으로 바꿉니다.

- `storage_account_key`자리 표시자 값을 저장소 계정 액세스 키로 바꿉니다.

### <a name="connect-by-using-azure-active-directory-azure-ad"></a>Azure Active Directory를 사용 하 여 연결 (Azure AD)

[Python 용 azure identity client 라이브러리](https://pypi.org/project/azure-identity/) 를 사용 하 여 azure AD로 응용 프로그램을 인증할 수 있습니다.

이 예에서는 클라이언트 ID, 클라이언트 암호 및 테 넌 트 ID를 사용 하 여 **DataLakeServiceClient** 인스턴스를 만듭니다.  이러한 값을 얻으려면 [클라이언트 응용 프로그램의 요청에 대 한 권한 부여를 위해 AZURE AD에서 토큰 획득](../common/storage-auth-aad-app.md)을 참조 하세요.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_AuthorizeWithAAD":::

> [!NOTE]
> 더 많은 예제는 [Python 용 Azure id 클라이언트 라이브러리](https://pypi.org/project/azure-identity/) 설명서를 참조 하세요.

## <a name="create-a-container"></a>컨테이너 만들기

컨테이너는 파일에 대 한 파일 시스템 역할을 합니다. **FileSystemDataLakeServiceClient.create_file_system** 메서드를 호출 하 여 만들 수 있습니다.

이 예제에서는 라는 컨테이너를 만듭니다 `my-file-system` .

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_CreateContainer":::

## <a name="create-a-directory"></a>디렉터리 만들기

**FileSystemClient.create_directory** 메서드를 호출 하 여 디렉터리 참조를 만듭니다.

이 예제에서는 라는 디렉터리를 `my-directory` 컨테이너에 추가 합니다. 

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_CreateDirectory":::

## <a name="rename-or-move-a-directory"></a>디렉터리 이름 바꾸기 또는 이동

**DataLakeDirectoryClient.rename_directory** 메서드를 호출 하 여 디렉터리 이름을 바꾸거나 이동 합니다. 원하는 디렉터리의 경로를 매개 변수로 전달 합니다. 

이 예에서는 하위 디렉터리의 이름을로 바꿉니다 `my-subdirectory-renamed` .

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_RenameDirectory":::

## <a name="delete-a-directory"></a>디렉터리 삭제

**DataLakeDirectoryClient.delete_directory** 메서드를 호출 하 여 디렉터리를 삭제 합니다.

다음 예제에서는 `my-directory`라는 디렉터리를 삭제합니다.  

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_DeleteDirectory":::

## <a name="upload-a-file-to-a-directory"></a>디렉터리에 파일 업로드

먼저 **DataLakeFileClient** 클래스의 인스턴스를 만들어 대상 디렉터리에 파일 참조를 만듭니다. **DataLakeFileClient.append_data** 메서드를 호출 하 여 파일을 업로드 합니다. **DataLakeFileClient.flush_data** 메서드를 호출 하 여 업로드를 완료 해야 합니다.

이 예제에서는 라는 디렉터리에 텍스트 파일을 업로드 `my-directory` 합니다.   

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_UploadFile":::

> [!TIP]
> 파일 크기가 클 경우 코드에서 **DataLakeFileClient.append_data** 메서드를 여러 번 호출 해야 합니다. 대신 **DataLakeFileClient.upload_data** 메서드를 사용 하는 것이 좋습니다. 이렇게 하면 단일 호출에서 전체 파일을 업로드할 수 있습니다. 

## <a name="upload-a-large-file-to-a-directory"></a>디렉터리에 용량이 많은 파일 업로드

**DataLakeFileClient.append_data** 메서드를 여러 번 호출 하지 않고도 **DataLakeFileClient.upload_data** 메서드를 사용 하 여 많은 파일을 업로드할 수 있습니다.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_UploadFileBulk":::

## <a name="download-from-a-directory"></a>디렉터리에서 다운로드 

쓰기용 로컬 파일을 엽니다. 그런 다음 다운로드 하려는 파일을 나타내는 **DataLakeFileClient** 인스턴스를 만듭니다. **DataLakeFileClient.read_file** 를 호출 하 여 파일에서 바이트를 읽은 다음 로컬 파일에 바이트를 씁니다. 

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_DownloadFromDirectory":::

## <a name="list-directory-contents"></a>디렉터리 콘텐츠 나열

**FileSystemClient.get_paths** 메서드를 호출한 다음 결과를 열거 하 여 디렉터리 내용을 나열 합니다.

이 예에서는 라는 디렉터리에 있는 각 하위 디렉터리 및 파일의 경로를 출력 합니다 `my-directory` .

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_ListFilesInDirectory":::

## <a name="see-also"></a>참고 항목

- [API 참조 설명서](/python/api/azure-storage-file-datalake/azure.storage.filedatalake)
- [패키지(Python 패키지 인덱스)](https://pypi.org/project/azure-storage-file-datalake/)
- [샘플](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)
- [Gen1과 Gen2 매핑](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
- [알려진 문제](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [피드백 보내기](https://github.com/Azure/azure-sdk-for-python/issues)