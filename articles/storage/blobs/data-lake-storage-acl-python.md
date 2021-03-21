---
title: Python을 사용 하 여 Azure Data Lake Storage Gen2에서 Acl 관리
description: Python을 사용 하 여 HNS (계층적 네임 스페이스)를 사용 하도록 설정 된 저장소 계정에서 ACL (액세스 제어 목록)을 관리 합니다.
author: normesta
ms.service: storage
ms.date: 02/17/2021
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-python
ms.openlocfilehash: ba864aa1aa2462f21e05ab5e779c8e715d6bb973
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100654222"
---
# <a name="use-python-to-manage-acls-in-azure-data-lake-storage-gen2"></a>Python을 사용 하 여 Azure Data Lake Storage Gen2에서 Acl 관리

이 문서에서는 Python을 사용 하 여 디렉터리 및 파일의 액세스 제어 목록을 가져오고, 설정 하 고, 업데이트 하는 방법을 보여 줍니다. 

부모 디렉터리 아래에 생성 된 새 자식 항목에는 ACL 상속이 이미 사용할 수 있습니다. 그러나 각 자식 항목에 대해 개별적으로 변경할 필요 없이 부모 디렉터리의 기존 자식 항목에서 Acl을 재귀적으로 추가, 업데이트 및 제거할 수도 있습니다. 

[패키지 (Python 패키지 인덱스)](https://pypi.org/project/azure-storage-file-datalake/)  |  [샘플](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)  |  [재귀 ACL 샘플](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)  |  [API 참조](/python/api/azure-storage-file-datalake/azure.storage.filedatalake)  |  [Gen1 To Gen2 mapping](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)  |  [사용자 의견 제공](https://github.com/Azure/azure-sdk-for-python/issues)

## <a name="prerequisites"></a>필수 구성 요소

- Azure 구독 [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.

- HNS(계층 구조 네임스페이스)를 사용하도록 설정된 스토리지 계정입니다. 만들려면 [다음 지침](create-data-lake-storage-account.md)을 수행합니다.

- Azure CLI 버전 `2.6.0` 이상.

- 다음 보안 권한 중 하나입니다.

  - 대상 컨테이너, 부모 리소스 그룹 또는 구독의 범위에서 [저장소 Blob 데이터 소유자](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) 역할이 할당 된 프로 비전 된 AD (Azure Active Directory) [보안 주체](../../role-based-access-control/overview.md#security-principal) 입니다.  

  - ACL 설정을 적용 하려는 대상 컨테이너 또는 디렉터리를 소유 하는 사용자입니다. Acl을 재귀적으로 설정 하기 위해 대상 컨테이너 또는 디렉터리의 모든 자식 항목을 포함 합니다.
  
  - 저장소 계정 키입니다.

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

### <a name="connect-by-using-azure-active-directory-ad"></a>Azure Active Directory (AD)를 사용 하 여 연결

> [!NOTE]
> Azure AD (Azure Active Directory)를 사용 하 여 액세스 권한을 부여 하는 경우 보안 주체가 [저장소 Blob 데이터 소유자 역할](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)에 할당 되었는지 확인 합니다. ACL 사용 권한을 적용 하는 방법 및 변경의 영향에 대 한 자세한 내용은  [Azure Data Lake Storage Gen2의 액세스 제어 모델](./data-lake-storage-access-control-model.md)을 참조 하세요.

[Python 용 azure identity client 라이브러리](https://pypi.org/project/azure-identity/) 를 사용 하 여 azure AD로 응용 프로그램을 인증할 수 있습니다.

클라이언트 ID, 클라이언트 암호 및 테 넌 트 ID를 가져옵니다. 이렇게 하려면 [클라이언트 응용 프로그램의 요청에 대 한 권한 부여를 위해 AZURE AD에서 토큰 가져오기](../common/storage-auth-aad-app.md)를 참조 하세요. 해당 프로세스의 일부로 다음 [azure 역할 기반 액세스 제어 (AZURE RBAC)](../../role-based-access-control/overview.md) 역할 중 하나를 보안 주체에 할당 해야 합니다. 

|역할|ACL 설정 기능|
|--|--|
|[Storage Blob 데이터 소유자](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|계정에 있는 모든 디렉터리 및 파일입니다.|
|[Storage Blob 데이터 기여자](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|보안 주체가 소유 하는 디렉터리와 파일만|

이 예에서는 클라이언트 ID, 클라이언트 암호 및 테 넌 트 ID를 사용 하 여 **DataLakeServiceClient** 인스턴스를 만듭니다.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_AuthorizeWithAAD":::

> [!NOTE]
> 더 많은 예제는 [Python 용 Azure id 클라이언트 라이브러리](https://pypi.org/project/azure-identity/) 설명서를 참조 하세요.

### <a name="connect-by-using-an-account-key"></a>계정 키를 사용 하 여 연결

계정에 연결 하는 가장 쉬운 방법입니다.

이 예에서는 계정 키를 사용 하 여 **DataLakeServiceClient** 인스턴스를 만듭니다.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_AuthorizeWithKey":::
 
- `storage_account_name` 자리 표시자 값을 스토리지 계정 이름으로 바꿉니다.

- `storage_account_key`자리 표시자 값을 저장소 계정 액세스 키로 바꿉니다.

## <a name="set-acls"></a>Acl 설정

ACL을 *설정* 하는 경우 모든 항목을 포함 하 여 전체 acl을 **바꿉니다** . 보안 주체의 권한 수준을 변경 하거나 다른 기존 항목에 영향을 주지 않고 ACL에 새 보안 주체를 추가 하려면 대신 ACL을 *업데이트* 해야 합니다. ACL을 대체 하는 대신 업데이트 하려면이 문서의 [Acl 업데이트](#update-acls-recursively) 섹션을 참조 하세요.  

이 섹션에서는 다음을 수행 하는 방법을 보여 줍니다.

- 디렉터리의 ACL 설정
- 파일의 ACL 설정

### <a name="set-the-acl-of-a-directory"></a>디렉터리의 ACL 설정

**DataLakeDirectoryClient.get_access_control** 메서드를 호출 하 여 디렉터리의 acl (액세스 제어 목록)을 가져오고 **DataLakeDirectoryClient.set_access_control** 메서드를 호출 하 여 acl을 설정 합니다.

이 예제에서는 이름이 인 디렉터리의 ACL을 가져오고 설정 합니다 `my-directory` . 문자열은 `rwxr-xrw-` 소유 사용자에 게 읽기, 쓰기 및 실행 권한을 부여 하 고 소유 그룹에 읽기 및 실행 권한만 제공 하 고 다른 모든 읽기 및 쓰기 권한을 부여 합니다.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_ACLDirectory":::

컨테이너의 루트 디렉터리에 대 한 ACL을 가져오고 설정할 수도 있습니다. 루트 디렉터리를 가져오려면 **FileSystemClient._get_root_directory_client** 메서드를 호출 합니다.

### <a name="set-the-acl-of-a-file"></a>파일의 ACL 설정

**DataLakeFileClient.get_access_control** 메서드를 호출 하 여 파일의 acl (액세스 제어 목록)을 가져오고 **DataLakeFileClient.set_access_control** 메서드를 호출 하 여 acl을 설정 합니다.

이 예제에서는 라는 파일의 ACL을 가져오고 설정 합니다 `my-file.txt` . 문자열은 `rwxr-xrw-` 소유 사용자에 게 읽기, 쓰기 및 실행 권한을 부여 하 고 소유 그룹에 읽기 및 실행 권한만 제공 하 고 다른 모든 읽기 및 쓰기 권한을 부여 합니다.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_FileACL":::

## <a name="set-acls-recursively"></a>반복적으로 ACL 설정

ACL을 *설정* 하는 경우 모든 항목을 포함 하 여 전체 acl을 **바꿉니다** . 보안 주체의 권한 수준을 변경 하거나 다른 기존 항목에 영향을 주지 않고 ACL에 새 보안 주체를 추가 하려면 대신 ACL을 *업데이트* 해야 합니다. ACL을 대체 하는 대신 업데이트 하려면이 문서에서 acl을 [재귀적으로 업데이트](#update-acls-recursively) 섹션을 참조 하세요.

**DataLakeDirectoryClient.set_access_control_recursive** 메서드를 호출 하 여 acl을 재귀적으로 설정 합니다.

**기본** acl 항목을 설정 하려면 `default:` 각 acl 항목 문자열의 시작 부분에 문자열을 추가 합니다.

이 예에서는 라는 디렉터리의 ACL을 설정 합니다 `my-parent-directory` .

이 메서드는 `is_default_scope` 기본 ACL을 설정할지 여부를 지정 하는 라는 부울 매개 변수를 허용 합니다. 해당 매개 변수가 이면 `True` ACL 항목의 목록 앞에 문자열을 표시 합니다 `default:` .

ACL의 항목은 소유 사용자에 대 한 읽기, 쓰기 및 실행 권한을 부여 하 고, 소유 그룹에 읽기 및 실행 권한만 제공 하 고, 다른 모든 사용자에 게 액세스 권한을 부여 하지는 않습니다. 이 예제의 마지막 ACL 항목은 개체 ID "" xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx "읽기 및 실행 권한이 있는 특정 사용자를 제공 합니다. 이러한 항목은 소유 하는 사용자에 게 읽기, 쓰기 및 실행 권한을 부여 하 고, 소유 그룹만 읽기 및 실행 권한을 부여 하 고, 다른 모든 사용자에 게 액세스 권한을 부여 하지는 않습니다. 이 예제의 마지막 ACL 항목은 개체 ID "" xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx "읽기 및 실행 권한이 있는 특정 사용자를 제공 합니다.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_SetACLRecursively":::

일괄 처리 크기를 지정 하 여 일괄 처리에서 Acl을 재귀적으로 처리 하는 예제를 보려면 python [샘플](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)을 참조 하세요.

## <a name="update-acls-recursively"></a>Acl을 재귀적으로 업데이트

Acl을 *업데이트할* 때 acl을 대체 하는 대신 acl을 수정 합니다. 예를 들어 ACL에 나열 된 다른 보안 주체에 영향을 주지 않고 ACL에 새 보안 주체를 추가할 수 있습니다.  ACL을 업데이트 하지 않고 바꾸려면이 문서의 [Acl 설정](#set-acls) 섹션을 참조 하세요.

ACL을 재귀적으로 업데이트 하려면 업데이트 하려는 ACL 항목을 사용 하 여 새 ACL 개체를 만든 다음 ACL 업데이트 작업에서 해당 개체를 사용 합니다. 기존 ACL을 가져오지 말고 업데이트할 ACL 항목만 제공 하면 됩니다. **DataLakeDirectoryClient.update_access_control_recursive** 메서드를 호출 하 여 ACL을 재귀적으로 업데이트 합니다. **기본** acl 항목을 업데이트 하려면 `default:` 각 acl 항목 문자열의 시작 부분에 문자열을 추가 합니다.

이 예에서는 write 권한이 있는 ACL 항목을 업데이트 합니다.

이 예에서는 라는 디렉터리의 ACL을 설정 합니다 `my-parent-directory` . 이 메서드는 `is_default_scope` 기본 ACL의 업데이트 여부를 지정 하는 라는 부울 매개 변수를 허용 합니다. 해당 매개 변수가 이면 `True` 업데이트 된 ACL 항목 앞에 문자열을 입력 합니다 `default:` .  

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_UpdateACLsRecursively":::

일괄 처리 크기를 지정 하 여 일괄 처리에서 Acl을 재귀적으로 처리 하는 예제를 보려면 python [샘플](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)을 참조 하세요.

## <a name="remove-acl-entries-recursively"></a>ACL 항목을 재귀적으로 제거

하나 이상의 ACL 항목을 제거할 수 있습니다. ACL 항목을 재귀적으로 제거 하려면 제거할 ACL 항목에 대 한 새 ACL 개체를 만든 다음 ACL 제거 작업에서 해당 개체를 사용 합니다. 기존 ACL을 가져오지 말고 제거할 ACL 항목만 제공 하면 됩니다. 

**DataLakeDirectoryClient.remove_access_control_recursive** 메서드를 호출 하 여 ACL 항목을 제거 합니다. **기본** acl 항목을 제거 하려면 `default:` acl 항목 문자열의 시작 부분에 문자열을 추가 합니다. 

이 예에서는 라는 디렉터리의 ACL에서 ACL 항목을 제거 `my-parent-directory` 합니다. 이 메서드는 `is_default_scope` 기본 ACL에서 항목을 제거할지 여부를 지정 하는 라는 부울 매개 변수를 허용 합니다. 해당 매개 변수가 이면 `True` 업데이트 된 ACL 항목 앞에 문자열을 입력 합니다 `default:` . 

```python
def remove_permission_recursively(is_default_scope):
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")
              
        acl = 'user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'

        if is_default_scope:
           acl = 'default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'

        directory_client.remove_access_control_recursive(acl=acl)

    except Exception as e:
     print(e)
```

일괄 처리 크기를 지정 하 여 일괄 처리에서 Acl을 재귀적으로 처리 하는 예제를 보려면 python [샘플](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)을 참조 하세요.

## <a name="recover-from-failures"></a>오류에서 복구

런타임 또는 사용 권한 오류가 발생할 수 있습니다. 런타임 오류의 경우 처음부터 프로세스를 다시 시작 합니다. 보안 주체에 수정 되는 디렉터리 계층 구조에 있는 디렉터리 또는 파일의 ACL을 수정할 수 있는 권한이 없으면 권한 오류가 발생할 수 있습니다. 권한 문제를 해결 한 다음 연속 토큰을 사용 하 여 오류 지점에서 프로세스를 다시 시작 하거나 프로세스를 처음부터 다시 시작 하도록 선택 합니다. 처음부터 다시 시작 하는 것을 선호 하는 경우 연속 토큰을 사용할 필요가 없습니다. 부정적인 영향 없이 ACL 항목을 다시 적용할 수 있습니다.

이 예에서는 오류가 발생 한 경우 연속 토큰을 반환 합니다. 응용 프로그램은 오류가 해결 된 후에이 예제 메서드를 다시 호출 하 고 연속 토큰을 전달할 수 있습니다. 이 예제 메서드를 처음 호출 하는 경우 응용 프로그램은 ``None`` 연속 토큰 매개 변수에 대 한 값을 전달할 수 있습니다.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_ResumeContinuationToken":::

일괄 처리 크기를 지정 하 여 일괄 처리에서 Acl을 재귀적으로 처리 하는 예제를 보려면 python [샘플](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)을 참조 하세요.

권한 오류가 발생 해도 프로세스가 중단 되지 않도록 하려면이를 지정할 수 있습니다.

프로세스가 중단 없이 완료 되도록 하려면 연속 토큰을 **DataLakeDirectoryClient.set_access_control_recursive** 메서드에 전달 하지 마십시오.

이 예에서는 ACL 항목을 재귀적으로 설정 합니다. 이 코드에 사용 권한 오류가 발생 하면 해당 오류를 기록 하 고 실행을 계속 합니다. 이 예에서는 실패 횟수를 콘솔에 출력 합니다.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_ContinueOnFailure":::

일괄 처리 크기를 지정 하 여 일괄 처리에서 Acl을 재귀적으로 처리 하는 예제를 보려면 python [샘플](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)을 참조 하세요.

[!INCLUDE [updated-for-az](../../../includes/recursive-acl-best-practices.md)]

## <a name="see-also"></a>참고 항목

- [API 참조 설명서](/python/api/azure-storage-file-datalake/azure.storage.filedatalake)
- [패키지(Python 패키지 인덱스)](https://pypi.org/project/azure-storage-file-datalake/)
- [샘플](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)
- [Gen1과 Gen2 매핑](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
- [알려진 문제](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [피드백 보내기](https://github.com/Azure/azure-sdk-for-python/issues)
- [Azure Data Lake Storage Gen2의 액세스 제어 모델](data-lake-storage-access-control.md)
- [Azure Data Lake Storage Gen2의 Acl (액세스 제어 목록)](data-lake-storage-access-control.md)