---
title: AzCopy v10를 사용 하 여 Azure Blob storage와 동기화 | Microsoft Docs
description: 이 문서에는 Azure Blob storage와 동기화 하는 데 도움이 되는 AzCopy 예제 명령의 컬렉션이 포함 되어 있습니다.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 12/08/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: f8210428e772241134b57ac4fccb5b1549e04483
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97617275"
---
# <a name="synchronize-with-azure-blob-storage-by-using-azcopy-v10"></a>AzCopy v10를 사용 하 여 Azure Blob storage와 동기화

AzCopy v10 명령줄 유틸리티를 사용 하 여 Azure Blob 저장소와 로컬 저장소를 동기화 할 수 있습니다. 

로컬 파일 시스템의 내용을 blob 컨테이너와 동기화 할 수 있습니다. 컨테이너와 가상 디렉터리를 서로 동기화 할 수도 있습니다. 동기화는 한 가지 방법입니다. 즉, 이러한 두 끝점 중 원본 및 대상 끝점 중 하나를 선택 합니다. 또한 동기화는 서버 간 Api를 사용 합니다. 이 섹션에서 설명 하는 예제는 계층 네임 스페이스가 있는 계정 에서도 작동 합니다. 

> [!NOTE]
> AzCopy의 현재 릴리스는 다른 원본 및 대상 간에 동기화 되지 않습니다 (예: File storage 또는 Amazon Web Services (AWS) S3 버킷).

파일 업로드, blob 다운로드, 계정 간에 blob 복사 등의 다른 작업 유형에 대 한 예제를 보려면이 문서의 [다음 단계](#next-steps) 섹션에 제공 된 링크를 참조 하세요.

## <a name="get-started"></a>시작

[AzCopy 시작](storage-use-azcopy-v10.md) 문서를 참조 하 여 AzCopy를 다운로드 하 고, 저장소 서비스에 권한 부여 자격 증명을 제공할 수 있는 방법에 대해 알아보세요.

> [!NOTE] 
> 이 문서의 예제에서는 Azure Active Directory (Azure AD)를 사용 하 여 권한 부여 자격 증명을 제공 했다고 가정 합니다.
>
> 대신 SAS 토큰을 사용 하 여 blob 데이터에 대 한 액세스 권한을 부여 하는 경우 각 AzCopy 명령의 리소스 URL에 해당 토큰을 추가할 수 있습니다. 예: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'` . 켄은> ' '.

## <a name="guidelines"></a>지침

- [Sync](storage-ref-azcopy-sync.md) 명령은 파일 이름과 마지막 수정 된 타임 스탬프를 비교 합니다. `--delete-destination`대상 디렉터리에서 파일을 삭제 하려면 선택적 플래그를 또는 값으로 설정 `true` 하 고, `prompt` 해당 파일이 원본 디렉터리에 더 이상 없으면 파일을 삭제 합니다.

- 플래그를로 설정 하면 `--delete-destination` `true` AzCopy는 프롬프트를 제공 하지 않고 파일을 삭제 합니다. AzCopy에서 파일을 삭제 하기 전에 프롬프트가 표시 되도록 하려면 플래그를로 설정 `--delete-destination` `prompt` 합니다.

- 실수로 인 한 삭제를 방지 하려면 플래그를 사용 하기 전에 [일시 삭제](../blobs/soft-delete-blob-overview.md) 기능을 사용 하도록 설정 해야 합니다 `--delete-destination=prompt|true` .

## <a name="update-a-container-with-changes-to-a-local-file-system"></a>로컬 파일 시스템에 대 한 변경 내용으로 컨테이너 업데이트

이 경우 컨테이너는 대상이며 로컬 파일 시스템은 원본입니다. 

> [!TIP]
> 이 예에서는 경로 인수를 작은따옴표 (' ')로 묶습니다. Windows 명령 셸 (cmd.exe)을 제외 하 고 모든 명령 셸에서 작은따옴표를 사용 합니다. cmd.exe (Windows 명령 셸)을 사용 하는 경우 작은따옴표 (' ') 대신 경로 인수를 큰따옴표 ("")로 묶습니다.

|    |     |
|--------|-----------|
| **구문** | `azcopy sync '<local-directory-path>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **예제** | `azcopy sync 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive` |

## <a name="update-a-local-file-system-with-changes-to-a-container"></a>변경이 포함된 로컬 파일 시스템을 컨테이너로 업데이트

이 경우 로컬 파일 시스템은 대상이 고 컨테이너는 원본입니다.

> [!TIP]
> 이 예에서는 경로 인수를 작은따옴표 (' ')로 묶습니다. Windows 명령 셸 (cmd.exe)을 제외 하 고 모든 명령 셸에서 작은따옴표를 사용 합니다. cmd.exe (Windows 명령 셸)을 사용 하는 경우 작은따옴표 (' ') 대신 경로 인수를 큰따옴표 ("")로 묶습니다.

|    |     |
|--------|-----------|
| **구문** | `azcopy sync 'https://<storage-account-name>.blob.core.windows.net/<container-name>' 'C:\myDirectory' --recursive` |
| **예제** | `azcopy sync 'https://mystorageaccount.blob.core.windows.net/mycontainer' 'C:\myDirectory' --recursive` |

## <a name="update-a-container-with-changes-in-another-container"></a>다른 컨테이너의 변경 내용으로 컨테이너 업데이트

이 명령에 표시 되는 첫 번째 컨테이너는 원본입니다. 두 번째는 대상입니다.

> [!TIP]
> 이 예에서는 경로 인수를 작은따옴표 (' ')로 묶습니다. Windows 명령 셸 (cmd.exe)을 제외 하 고 모든 명령 셸에서 작은따옴표를 사용 합니다. cmd.exe (Windows 명령 셸)을 사용 하는 경우 작은따옴표 (' ') 대신 경로 인수를 큰따옴표 ("")로 묶습니다.

|    |     |
|--------|-----------|
| **구문** | `azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **예제** | `azcopy sync 'https://mysourceaccount.blob.core.windows.net/mycontainer' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

## <a name="update-a-directory-with-changes-to-a-directory-in-another-container"></a>다른 컨테이너에 있는 디렉터리에 대 한 변경 내용으로 디렉터리 업데이트

이 명령에 표시 되는 첫 번째 디렉터리는 원본입니다. 두 번째는 대상입니다.

> [!TIP]
> 이 예에서는 경로 인수를 작은따옴표 (' ')로 묶습니다. Windows 명령 셸 (cmd.exe)을 제외 하 고 모든 명령 셸에서 작은따옴표를 사용 합니다. cmd.exe (Windows 명령 셸)을 사용 하는 경우 작은따옴표 (' ') 대신 경로 인수를 큰따옴표 ("")로 묶습니다.

|    |     |
|--------|-----------|
| **구문** | `azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive` |
| **예제** | `azcopy sync 'https://mysourceaccount.blob.core.windows.net/<container-name>/myDirectory' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myDirectory' --recursive` |

## <a name="synchronize-with-optional-flags"></a>선택적 플래그로 동기화

선택적 플래그를 사용 하 여 동기화 작업을 조정할 수 있습니다. 다음은 몇 가지 예입니다.

|시나리오|플래그|
|---|---|
|다운로드 시의 MD5 해시 유효성 검사 방법을 지정 합니다.|**--확인-md5** = \[ NoCheck \| logonly \| \| FailIfDifferentOrMissing\]|
|패턴을 기반으로 파일을 제외 합니다.|**--제외-경로**|
|동기화 관련 로그 항목에 대 한 세부 정보를 지정 합니다.|**--로그 수준** = \[ 경고 \| 오류 \| 정보 \| 없음\]|

전체 목록은 [options](storage-ref-azcopy-sync.md#options)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

다음 문서에서 더 많은 예제를 찾아보세요.

- [예제: 업로드](storage-use-azcopy-blobs-upload.md)
- [예제: 다운로드](storage-use-azcopy-blobs-download.md)
- [예제: 계정 간 복사](storage-use-azcopy-blobs-copy.md)
- [예제: Amazon S3 버킷](storage-use-azcopy-s3.md)
- [예: Azure Files](storage-use-azcopy-files.md)
- [자습서: AzCopy를 사용하여 클라우드로 온-프레미스 데이터 마이그레이션](storage-use-azcopy-migrate-on-premises-data.md)
- [AzCopy 구성, 최적화 및 문제 해결](storage-use-azcopy-configure.md)