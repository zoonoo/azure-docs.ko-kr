---
title: ACL을 & 파일용 Azure 데이터 레이크 스토리지 Gen2 파이썬 SDK
description: 계층적 네임스페이스(HNS)가 활성화된 저장소 계정에서 Python관리 디렉토리 및 파일 및 디렉터리 액세스 제어 목록(ACL)을 사용합니다.
author: normesta
ms.service: storage
ms.date: 04/10/2020
ms.author: normesta
ms.topic: article
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: a79f3110206a01b9b974952f0ec0d299644be11f
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81262352"
---
# <a name="use-python-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>파이썬을 사용하여 Azure 데이터 레이크 스토리지 Gen2에서 디렉터리, 파일 및 ACL 관리

이 문서에서는 계층적 네임스페이스(HNS)가 활성화된 저장소 계정에서 디렉터리, 파일 및 사용 권한을 만들고 관리하기 위해 Python을 사용하는 방법을 보여 주며 이 문서에서는 

[패키지 (파이썬 패키지 인덱스)](https://pypi.org/project/azure-storage-file-datalake/) | [샘플](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples) | [API 참조](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-storage-file-datalake/12.0.0/azure.storage.filedatalake.html) | [Gen1 에서 Gen2 매핑으로](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md) | [피드백 제공](https://github.com/Azure/azure-sdk-for-python/issues)

## <a name="prerequisites"></a>사전 요구 사항

> [!div class="checklist"]
> * Azure 구독 [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
> * 계층적 네임스페이스(HNS)가 활성화된 저장소 계정입니다. [다음](data-lake-storage-quickstart-create-account.md) 지침에 따라 만들 수 있습니다.

## <a name="set-up-your-project"></a>프로젝트 설정

[pip를](https://pypi.org/project/pip/)사용하여 파이썬에 대한 Azure 데이터 레이크 저장소 클라이언트 라이브러리를 설치합니다.

```
pip install azure-storage-file-datalake
```

이러한 가져오기 문을 코드 파일의 맨 위에 추가합니다.

```python
import os, uuid, sys
from azure.storage.filedatalake import DataLakeServiceClient
from azure.core._match_conditions import MatchConditions
from azure.storage.filedatalake._models import ContentSettings
```

## <a name="connect-to-the-account"></a>계정에 연결

이 문서의 스니펫을 사용하려면 저장소 계정을 나타내는 **DataLakeServiceClient** 인스턴스를 만들어야 합니다. 

### <a name="connect-by-using-an-account-key"></a>계정 키를 사용하여 연결

이렇게 하면 계정에 연결하는 가장 쉬운 방법이 있습니다. 

이 예제는 계정 키를 사용하여 **DataLakeServiceClient** 인스턴스를 만듭니다.

```python
try:  
    global service_client
        
    service_client = DataLakeServiceClient(account_url="{}://{}.dfs.core.windows.net".format(
        "https", storage_account_name), credential=storage_account_key)
    
except Exception as e:
    print(e)
```
 
- `storage_account_name` 자리 표시자 값을 스토리지 계정 이름으로 바꿉니다.

- `storage_account_key` 자리 표시자 값을 저장소 계정 액세스 키로 바꿉습니다.

### <a name="connect-by-using-azure-active-directory-ad"></a>Azure Active 디렉터리(AD)를 사용하여 연결

[파이썬에 대한 Azure ID 클라이언트 라이브러리를](https://pypi.org/project/azure-identity/) 사용하여 Azure AD를 사용하여 응용 프로그램을 인증할 수 있습니다.

이 예제에서는 클라이언트 ID, 클라이언트 암호 및 테넌트 ID를 사용하여 **DataLakeServiceClient** 인스턴스를 만듭니다.  이러한 값을 가져오려면 [클라이언트 응용 프로그램에서 요청을 승인하기 위해 Azure AD에서 토큰 가져오기를](../common/storage-auth-aad-app.md)참조하십시오.

```python
def initialize_storage_account_ad(storage_account_name, client_id, client_secret, tenant_id):
    
    try:  
        global service_client

        credential = ClientSecretCredential(tenant_id, client_id, client_secret)

        service_client = DataLakeServiceClient(account_url="{}://{}.dfs.core.windows.net".format(
            "https", storage_account_name), credential=credential)
    
    except Exception as e:
        print(e)
```

> [!NOTE]
> 자세한 예는 Python 설명서에 [대한 Azure ID 클라이언트 라이브러리를](https://pypi.org/project/azure-identity/) 참조하십시오.

## <a name="create-a-file-system"></a>파일 시스템 만들기

파일 시스템은 파일의 컨테이너 역할을 합니다. **FileSystemDataLakeServiceClient.create_file_system** 메서드를 호출하여 하나를 만들 수 있습니다.

이 예제는 .라는 `my-file-system`파일 시스템을 만듭니다.

```python
def create_file_system():
    try:
        global file_system_client

        file_system_client = service_client.create_file_system(file_system="my-file-system")
    
    except Exception as e:
        print(e) 
```


## <a name="create-a-directory"></a>디렉터리 만들기

**FileSystemClient.create_directory** 메서드를 호출 하여 디렉터리 참조를 만듭니다.

이 예제는 파일 `my-directory` 시스템에 명명된 디렉터리를 추가합니다. 

```python
def create_directory():
    try:
        file_system_client.create_directory("my-directory")
    
    except Exception as e:
     print(e) 
```

## <a name="rename-or-move-a-directory"></a>디렉터리 이름 바꾸기 또는 이동

**DataLakeDirectoryClient.rename_directory** 메서드를 호출하여 디렉터리 이름을 바꾸거나 이동합니다. 원하는 디렉터리 매개 변수의 경로를 전달합니다. 

이 예제는 하위 디렉터리 이름을 `my-subdirectory-renamed`이름으로 바꿉니다.

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

**DataLakeDirectoryClient.delete_directory** 메서드를 호출 하여 디렉터리를 삭제 합니다.

이 예제는 .라는 `my-directory`디렉터리를 삭제합니다.  

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

**DataLakeDirectoryClient.get_access_control** 메서드를 호출 하 여 디렉터리 (ACL)의 액세스 제어 목록 (ACL)을 가져옵니다 및 **DataLakeDirectoryClient.set_access_control** 메서드를 호출 하 여 ACL을 설정 합니다.

> [!NOTE]
> 응용 프로그램이 Azure Active Directory(Azure AD)를 사용하여 액세스 권한을 부여한 경우 응용 프로그램에서 액세스를 승인하는 데 사용하는 보안 주체가 [저장소 Blob 데이터 소유자 역할을](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)할당했는지 확인합니다. ACL 사용 권한이 적용되는 방법과 이를 변경하는 효과에 대한 자세한 내용은 [Azure Data Lake Storage Gen2의 액세스 제어를](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)참조하십시오.

이 예제는 . `my-directory` 문자열은 `rwxr-xrw-` 소유 사용자에게 읽기, 쓰기 및 실행 권한을 제공하고, 소유 그룹에 읽기 및 실행 권한만 제공하고, 다른 모든 읽기 및 쓰기 권한을 부여합니다.

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

## <a name="upload-a-file-to-a-directory"></a>디렉토리에 파일 업로드 

먼저 **DataLakeFileClient** 클래스의 인스턴스를 만들어 대상 디렉터리에서 파일 참조를 만듭니다. **DataLakeFileClient.append_data** 메서드를 호출하여 파일을 업로드합니다. **DataLakeFileClient.flush_data** 메서드를 호출하여 업로드를 완료해야 합니다.

이 예제는 `my-directory`.라는 디렉터리에 텍스트 파일을 업로드합니다.   

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

> [!TIP]
> 파일 크기가 큰 경우 코드에서 **DataLakeFileClient.append_data** 메서드를 여러 번 호출해야 합니다. 대신 **DataLakeFileClient.upload_data** 메서드를 사용하는 것이 좋습니다. 이렇게 하면 전체 파일을 단일 호출로 업로드할 수 있습니다. 

## <a name="upload-a-large-file-to-a-directory"></a>디렉터리에 대용량 파일 업로드

**DataLakeFileClient.upload_data** 메서드를 사용하여 **DataLakeFileClient.append_data** 메서드를 여러 번 호출하지 않고도 대용량 파일을 업로드할 수 있습니다.

```python
def upload_file_to_directory_bulk():
    try:

        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        file_client = directory_client.get_file_client("uploaded-file.txt")

        local_file = open("C:\\file-to-upload.txt",'r')

        file_contents = local_file.read()

        file_client.upload_data(file_contents, overwrite=True)

    except Exception as e:
      print(e) 
```

## <a name="manage-file-permissions"></a>파일 권한 관리

**DataLakeFileClient.get_access_control** 메서드를 호출하여 파일의 액세스 제어 목록(ACL)을 얻고 **DataLakeFileClient.set_access_control** 메서드를 호출하여 ACL을 설정합니다.

> [!NOTE]
> 응용 프로그램이 Azure Active Directory(Azure AD)를 사용하여 액세스 권한을 부여한 경우 응용 프로그램에서 액세스를 승인하는 데 사용하는 보안 주체가 [저장소 Blob 데이터 소유자 역할을](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)할당했는지 확인합니다. ACL 사용 권한이 적용되는 방법과 이를 변경하는 효과에 대한 자세한 내용은 [Azure Data Lake Storage Gen2의 액세스 제어를](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)참조하십시오.

이 예제는 . `my-file.txt` 문자열은 `rwxr-xrw-` 소유 사용자에게 읽기, 쓰기 및 실행 권한을 제공하고, 소유 그룹에 읽기 및 실행 권한만 제공하고, 다른 모든 읽기 및 쓰기 권한을 부여합니다.

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

## <a name="download-from-a-directory"></a>디렉토리에서 다운로드 

작성할 로컬 파일을 엽니다. 그런 다음 다운로드할 파일을 나타내는 **DataLakeFileClient** 인스턴스를 만듭니다. **DataLakeFileClient.read_file** 호출하여 파일에서 바이트를 읽은 다음 해당 바이트를 로컬 파일에 씁니다. 

```python
def download_file_from_directory():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        local_file = open("C:\\file-to-download.txt",'wb')

        file_client = directory_client.get_file_client("uploaded-file.txt")

        download = file_client.download_file()

        downloaded_bytes = download.readall()

        local_file.write(downloaded_bytes)

        local_file.close()

    except Exception as e:
     print(e)
```
## <a name="list-directory-contents"></a>디렉터리 콘텐츠 나열

**FileSystemClient.get_paths** 메서드를 호출 한 다음 결과를 통해 나열 하여 디렉터리 내용을 나열 합니다.

이 예제에서는 `my-directory`.

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
* [패키지(파이썬 패키지 인덱스)](https://pypi.org/project/azure-storage-file-datalake/)
* [샘플](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)
* [Gen 1 ~ Gen2 매핑](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
* [알려진 문제](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [피드백 제공](https://github.com/Azure/azure-sdk-for-python/issues)
