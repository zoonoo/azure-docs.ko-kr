---
title: Azure Data Lake Storage Gen2의 Acl & 파일에 대해 Python 사용 (미리 보기)
description: Python을 사용 하 여, HNS (계층적 네임 스페이스)를 사용 하도록 설정 된 저장소 계정에서 디렉터리 및 파일 및 디렉터리 ACL (액세스 제어 목록)을 관리 합니다.
author: normesta
ms.service: storage
ms.date: 11/24/2019
ms.author: normesta
ms.topic: article
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: a65e8b2c5fbcdd4f6df0f8f39b24fa97b8deeca3
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/26/2019
ms.locfileid: "74534424"
---
# <a name="use-python-for-files--acls-in-azure-data-lake-storage-gen2-preview"></a>Azure Data Lake Storage Gen2의 Acl & 파일에 대해 Python 사용 (미리 보기)

이 문서에서는 Python을 사용 하 여 HNS (계층적 네임 스페이스)를 사용 하도록 설정 된 저장소 계정에서 디렉터리, 파일 및 사용 권한을 만들고 관리 하는 방법을 보여 줍니다. 

> [!IMPORTANT]
> Python 용 Azure Data Lake Storage 클라이언트 라이브러리는 현재 공개 미리 보기로 제공 됩니다.

[패키지 (Python 패키지 인덱스)](https://pypi.org/project/azure-storage-file-datalake/) | [샘플](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples) | [API 참조](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-storage-file-datalake/12.0.0b5/index.html) | [Gen1 to Gen2 mapping](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md) | [피드백 제공](https://github.com/Azure/azure-sdk-for-python/issues)

## <a name="prerequisites"></a>전제 조건

> [!div class="checklist"]
> * Azure 구독. [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
> * 계층적 네임 스페이스 (HNS)를 사용 하도록 설정 된 저장소 계정입니다. [다음](data-lake-storage-quickstart-create-account.md) 지침에 따라 새로 만듭니다.

## <a name="set-up-your-project"></a>프로젝트 설정

[Pip](https://pypi.org/project/pip/)를 사용 하 여 Python 용 Azure Data Lake Storage 클라이언트 라이브러리를 설치 합니다.

```
pip install azure-storage-file-datalake --pre
```

이러한 import 문을 코드 파일의 맨 위에 추가 합니다.

```python
import os, uuid, sys
from azure.storage.filedatalake import DataLakeServiceClient
```

## <a name="connect-to-the-account"></a>계정에 연결

이 문서의 코드 조각을 사용 하려면 저장소 계정을 나타내는 **DataLakeServiceClient** 인스턴스를 만들어야 합니다. 하나를 가져오는 가장 쉬운 방법은 계정 키를 사용 하는 것입니다. 

이 예에서는 계정 키를 사용 하 여 저장소 계정을 나타내는 **DataLakeServiceClient** 인스턴스를 만듭니다. 

```python
try:  
    global service_client
        
    service_client = DataLakeServiceClient(account_url="{}://{}.dfs.core.windows.net".format(
        "https", storage_account_name), credential=storage_account_key)
    
except Exception as e:
    print(e)
```
 
- `storage_account_name` 자리 표시자 값을 스토리지 계정 이름으로 바꿉니다.

- `storage-account-key` 자리 표시자 값을 저장소 계정 액세스 키로 바꿉니다.

## <a name="create-a-file-system"></a>파일 시스템 만들기

파일 시스템은 파일의 컨테이너 역할을 합니다. **Create_file_system FileSystemDataLakeServiceClient** 메서드를 호출 하 여 만들 수 있습니다.

이 예에서는 `my-file-system`이라는 파일 시스템을 만듭니다.

```python
def create_file_system():
    try:
        global file_system_client

        file_system_client = service_client.create_file_system(file_system="my-file-system")
    
    except Exception as e:
        print(e) 
```


## <a name="create-a-directory"></a>디렉터리 만들기

**Create_directory FileSystemClient** 메서드를 호출 하 여 디렉터리 참조를 만듭니다.

이 예제에서는 `my-directory` 라는 디렉터리를 파일 시스템에 추가 합니다. 

```python
def create_directory():
    try:
        file_system_client.create_directory("my-directory")
    
    except Exception as e:
     print(e) 
```

## <a name="rename-or-move-a-directory"></a>디렉터리 이름 바꾸기 또는 이동

**Rename_directory DataLakeDirectoryClient** 메서드를 호출 하 여 디렉터리 이름을 바꾸거나 이동 합니다. 원하는 디렉터리의 경로를 매개 변수로 전달 합니다. 

이 예에서는 `my-subdirectory-renamed`이름으로 하위 디렉터리의 이름을 바꿉니다.

```python
def rename_directory():
    try:
       
       file_system_client = service_client.get_file_system_client(file_system="my-file-system")
       directory_client = file_system_client.get_directory_client("my-directory")
       
       new_dir_name = "my-directory-renamed"
       directory_client.rename_directory(rename_destination=directory_client.file_system_name + '/' + new_dir_name)

    except Exception as e:
     print(e) 
```

## <a name="delete-a-directory"></a>디렉터리 삭제

**Delete_directory DataLakeDirectoryClient** 메서드를 호출 하 여 디렉터리를 삭제 합니다.

이 예에서는 `my-directory`라는 디렉터리를 삭제 합니다.  

```python
def delete_directory():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")
        directory_client = file_system_client.get_directory_client("my-directory")

        directory_client.delete_directory()
    except Exception as e:
     print(e) 
```

## <a name="manage-directory-permissions"></a>디렉터리 권한 관리

**Get_access_control DataLakeDirectoryClient** 메서드를 호출 하 여 디렉터리의 acl (액세스 제어 목록)을 가져오고 **set_access_control DataLakeDirectoryClient** 메서드를 호출 하 여 acl을 설정 합니다.

이 예제에서는 `my-directory`된 디렉터리의 ACL을 가져오고 설정 합니다. 문자열 `rwxr-xrw-` 소유 하는 사용자에 게 읽기, 쓰기 및 실행 권한을 부여 하 고 소유 그룹만 읽기 및 실행 권한을 부여 하 고 다른 모든 읽기 및 쓰기 권한을 부여 합니다.

```python
def manage_directory_permissions():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        acl_props = directory_client.get_access_control()
        
        print(acl_props['permissions'])
        
        new_dir_permissions = "rwxr-xrw-"
        
        directory_client.set_access_control(permissions=new_dir_permissions)
        
        acl_props = directory_client.get_access_control()
        
        print(acl_props['permissions'])
    
    except Exception as e:
     print(e) 
```

## <a name="upload-a-file-to-a-directory"></a>디렉터리에 파일 업로드 

먼저 **DataLakeFileClient** 클래스의 인스턴스를 만들어 대상 디렉터리에 파일 참조를 만듭니다. **Append_data DataLakeFileClient** 메서드를 호출 하 여 파일을 업로드 합니다. **Flush_data DataLakeFileClient** 메서드를 호출 하 여 업로드를 완료 해야 합니다.

이 예제에서는 `my-directory`라는 디렉터리에 텍스트 파일을 업로드 합니다.   

```python
def upload_file_to_directory():
    try:

        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        file_client = directory_client.create_file("uploaded-file.txt")
        local_file = open("C:\\file-to-upload.txt",'r')

        file_contents = local_file.read()

        file_client.append_data(data=file_contents, offset=0, length=len(file_contents))

        file_client.flush_data(len(file_contents))

    except Exception as e:
      print(e) 
```

## <a name="manage-file-permissions"></a>파일 사용 권한 관리

**Get_access_control DataLakeFileClient** 메서드를 호출 하 여 파일의 acl (액세스 제어 목록)을 가져오고 **set_access_control DataLakeFileClient** 메서드를 호출 하 여 acl을 설정 합니다.

이 예제에서는 `my-file.txt`이라는 파일의 ACL을 가져오고 설정 합니다. 문자열 `rwxr-xrw-` 소유 하는 사용자에 게 읽기, 쓰기 및 실행 권한을 부여 하 고 소유 그룹만 읽기 및 실행 권한을 부여 하 고 다른 모든 읽기 및 쓰기 권한을 부여 합니다.

```python
def manage_file_permissions():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        file_client = directory_client.get_file_client("uploaded-file.txt")

        acl_props = file_client.get_access_control()
        
        print(acl_props['permissions'])
        
        new_file_permissions = "rwxr-xrw-"
        
        file_client.set_access_control(permissions=new_file_permissions)
        
        acl_props = file_client.get_access_control()
        
        print(acl_props['permissions'])

    except Exception as e:
     print(e) 
```

## <a name="download-from-a-directory"></a>디렉터리에서 다운로드 

쓰기용 로컬 파일을 엽니다. 그런 다음 다운로드 하려는 파일을 나타내는 **DataLakeFileClient** 인스턴스를 만듭니다. **Read_file DataLakeFileClient** 를 호출 하 여 파일에서 바이트를 읽은 다음 해당 바이트를 로컬 파일에 기록 합니다. 

```python
def download_file_from_directory():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        local_file = open("C:\\file-to-download.txt",'wb')

        file_client = directory_client.get_file_client("uploaded-file.txt")

        downloaded_bytes = file_client.read_file()

        local_file.write(downloaded_bytes)

        local_file.close()

    except Exception as e:
     print(e)
```
## <a name="list-directory-contents"></a>디렉터리 콘텐츠 나열

**Get_paths FileSystemClient** 메서드를 호출 하 고 결과를 열거 하 여 디렉터리 내용을 나열 합니다.

이 예에서는 `my-directory`라는 디렉터리에 있는 각 하위 디렉터리 및 파일의 경로를 출력 합니다.

```python
def list_directory_contents():
    try:
        
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        paths = file_system_client.get_paths(path="my-directory")

        for path in paths:
            print(path.name + '\n')

    except Exception as e:
     print(e) 
```

## <a name="see-also"></a>참고 항목

* [API 참조 설명서](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-storage-file-datalake/12.0.0b5/index.html)
* [패키지 (Python 패키지 인덱스)](https://pypi.org/project/azure-storage-file-datalake/)
* [샘플](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)
* [Gen1 to Gen2 mapping](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
* [알려진 문제](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [사용자 의견 제공](https://github.com/Azure/azure-sdk-for-python/issues)
