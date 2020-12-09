---
title: AzCopy v10를 사용 하 여 Azure Blob storage에서 blob 다운로드 Microsoft Docs
description: 이 문서에는 Azure Blob storage에서 blob을 다운로드 하는 데 도움이 되는 AzCopy 예제 명령의 컬렉션이 포함 되어 있습니다.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 12/08/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 0a2490a104d18f77a0ec326933f463eb4ebb4339
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/09/2020
ms.locfileid: "96923962"
---
# <a name="download-blobs-from-azure-blob-storage-by-using-azcopy-v10"></a>AzCopy v10를 사용 하 여 Azure Blob storage에서 blob 다운로드

AzCopy v10 명령줄 유틸리티를 사용 하 여 Blob 저장소에서 blob 및 디렉터리를 다운로드할 수 있습니다. 

파일 업로드, Blob storage와 동기화, 계정 간에 blob 복사 등의 다른 작업 유형에 대 한 예제를 보려면이 문서의 [다음 단계](#next-steps) 섹션에 제공 된 링크를 참조 하세요.

## <a name="get-started"></a>시작하기

[AzCopy 시작](storage-use-azcopy-v10.md) 문서를 참조 하 여 AzCopy를 다운로드 하 고, 저장소 서비스에 권한 부여 자격 증명을 제공할 수 있는 방법에 대해 알아보세요.

> [!NOTE] 
> 이 문서의 예제에서는 Azure Active Directory (Azure AD)를 사용 하 여 권한 부여 자격 증명을 제공 했다고 가정 합니다.
>
> 대신 SAS 토큰을 사용 하 여 blob 데이터에 대 한 액세스 권한을 부여 하는 경우 각 AzCopy 명령의 리소스 URL에 해당 토큰을 추가할 수 있습니다. 예를 들어 `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`을 참조하십시오.

## <a name="download-a-blob"></a>Blob 다운로드

[Azcopy copy](storage-ref-azcopy-copy.md) 명령을 사용 하 여 blob을 다운로드 합니다.

> [!TIP]
> 이 예에서는 경로 인수를 작은따옴표 (' ')로 묶습니다. Windows 명령 셸 (cmd.exe)을 제외 하 고 모든 명령 셸에서 작은따옴표를 사용 합니다. cmd.exe (Windows 명령 셸)을 사용 하는 경우 작은따옴표 (' ') 대신 경로 인수를 큰따옴표 ("")로 묶습니다.

|    |     |
|--------|-----------|
| **구문** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>' '<local-file-path>'` |
| **예제** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'` |
| **예** (계층적 네임 스페이스) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'` |

> [!NOTE]
> `Content-md5`Blob의 속성 값에 해시가 포함 된 경우 AzCopy는 다운로드 된 데이터에 대 한 md5 해시를 계산 하 고 blob의 속성에 저장 된 md5 해시가 계산 된 해시와 일치 하는지 확인 합니다 `Content-md5` . 이러한 값이 일치 하지 않으면 `--check-md5=NoCheck` 복사 명령에 또는을 추가 하 여이 동작을 재정의 하지 않는 한 다운로드가 실패 합니다 `--check-md5=LogOnly` .

## <a name="download-a-directory"></a>디렉터리 다운로드

[Azcopy copy](storage-ref-azcopy-copy.md) 명령을 사용 하 여 디렉터리를 다운로드 합니다.

> [!TIP]
> 이 예에서는 경로 인수를 작은따옴표 (' ')로 묶습니다. Windows 명령 셸 (cmd.exe)을 제외 하 고 모든 명령 셸에서 작은따옴표를 사용 합니다. cmd.exe (Windows 명령 셸)을 사용 하는 경우 작은따옴표 (' ') 대신 경로 인수를 큰따옴표 ("")로 묶습니다.

|    |     |
|--------|-----------|
| **구문** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>' '<local-directory-path>' --recursive` |
| **예제** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive` |
| **예** (계층적 네임 스페이스) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive` |

이 예에서는 `C:\myDirectory\myBlobDirectory` 다운로드 한 모든 blob을 포함 하는 라는 디렉터리를 생성 합니다.

## <a name="download-directory-contents"></a>디렉터리 콘텐츠 다운로드

와일드카드 기호(*)를 사용하여 포함하는 디렉터리 자체를 복사하지 않고 디렉터리의 콘텐츠를 다운로드할 수 있습니다.

> [!TIP]
> 이 예에서는 경로 인수를 작은따옴표 (' ')로 묶습니다. Windows 명령 셸 (cmd.exe)을 제외 하 고 모든 명령 셸에서 작은따옴표를 사용 합니다. cmd.exe (Windows 명령 셸)을 사용 하는 경우 작은따옴표 (' ') 대신 경로 인수를 큰따옴표 ("")로 묶습니다.

> [!NOTE]
> 현재이 시나리오는 계층 네임 스페이스가 없는 계정에 대해서만 지원 됩니다.

|    |     |
|--------|-----------|
| **구문** | `azcopy copy 'https://<storage-account-name>.blob.core.windows.net/<container-name>/*' '<local-directory-path>/'` |
| **예제** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory/*' 'C:\myDirectory'` |

플래그를 추가 `--recursive` 하 여 모든 하위 디렉터리의 파일을 다운로드 합니다.

## <a name="download-specific-blobs"></a>특정 blob 다운로드

전체 파일 이름, 와일드 카드 문자 (*)가 포함 된 부분 이름, 날짜 및 시간을 사용 하 여 특정 blob을 다운로드할 수 있습니다. 

> [!TIP]
> 이 예에서는 경로 인수를 작은따옴표 (' ')로 묶습니다. Windows 명령 셸 (cmd.exe)을 제외 하 고 모든 명령 셸에서 작은따옴표를 사용 합니다. cmd.exe (Windows 명령 셸)을 사용 하는 경우 작은따옴표 (' ') 대신 경로 인수를 큰따옴표 ("")로 묶습니다.

#### <a name="specify-multiple-complete-blob-names"></a>전체 blob 이름 여러 개 지정

옵션과 함께 [azcopy copy](storage-ref-azcopy-copy.md) 명령을 사용 합니다 `--include-path` . Semicolin ()를 사용 하 여 개별 blob 이름을 구분 `;` 합니다.

|    |     |
|--------|-----------|
| **구문** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>` |
| **예제** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive` |
| **예** (계층적 네임 스페이스) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt'--recursive` |

이 예제에서 AzCopy는 `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` 디렉터리와 파일을 전송 합니다 `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/documents/myFile.txt` . `--recursive`디렉터리의 모든 blob을 전송 하는 옵션을 포함 합니다 `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` .

옵션을 사용 하 여 blob을 제외할 수도 있습니다 `--exclude-path` . 자세히 알아보려면 [azcopy copy](storage-ref-azcopy-copy.md) 참조 문서를 참조 하세요.

#### <a name="use-wildcard-characters"></a>와일드 카드 문자 사용

옵션과 함께 [azcopy copy](storage-ref-azcopy-copy.md) 명령을 사용 합니다 `--include-pattern` . 와일드 카드 문자를 포함 하는 부분 이름을 지정 합니다. Semicolin ()를 사용 하 여 이름을 구분 `;` 합니다.

|    |     |
|--------|-----------|
| **구문** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **예제** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |
| **예** (계층적 네임 스페이스) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |

옵션을 사용 하 여 blob을 제외할 수도 있습니다 `--exclude-pattern` . 자세히 알아보려면 [azcopy copy](storage-ref-azcopy-copy.md) 참조 문서를 참조 하세요.

`--include-pattern`및 `--exclude-pattern` 옵션은 blob 이름에만 적용 되 고 경로에는 적용 되지 않습니다.  디렉터리 트리에 있는 모든 텍스트 파일 (blob)을 복사 하려면 옵션을 사용 `–recursive` 하 여 전체 디렉터리 트리를 가져온 다음를 사용 하 여 `–include-pattern` `*.txt` 모든 텍스트 파일을 가져오도록 지정 합니다.

#### <a name="download-blobs-that-were-modified-after-a-date-and-time"></a>날짜 및 시간 이후 수정 된 blob 다운로드 

옵션과 함께 [azcopy copy](storage-ref-azcopy-copy.md) 명령을 사용 합니다 `--include-after` . ISO-8601 형식으로 날짜 및 시간을 지정 합니다 (예: `2020-08-19T15:04:00Z` ). 

|    |     |
|--------|-----------|
| **구문** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>/*' '<local-directory-path>' --include-after <Date-Time-in-ISO-8601-format>` |
| **예제** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/*' 'C:\myDirectory'  --include-after '2020-08-19T15:04:00Z'` |
| **예** (계층적 네임 스페이스) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory/*' 'C:\myDirectory'  --include-after '2020-08-19T15:04:00Z'` |

자세한 참조는 [azcopy copy](storage-ref-azcopy-copy.md) 참조 문서를 참조 하세요.

#### <a name="download-previous-versions-of-a-blob"></a>이전 버전의 blob 다운로드

[Blob 버전 관리](../blobs/versioning-enable.md)를 사용 하도록 설정한 경우 blob의 이전 버전을 하나 이상 다운로드할 수 있습니다. 

먼저 [버전 id](../blobs/versioning-overview.md)목록을 포함 하는 텍스트 파일을 만듭니다. 각 버전 ID는 별도의 줄에 표시 되어야 합니다. 예를 들어: 

```
2020-08-17T05:50:34.2199403Z
2020-08-17T05:50:34.5041365Z
2020-08-17T05:50:36.7607103Z
```

그런 다음 옵션을 사용 하 여 [azcopy copy](storage-ref-azcopy-copy.md) 명령을 사용 `--list-of-versions` 합니다. 버전 목록이 포함 된 텍스트 파일의 위치를 지정 합니다 (예: `D:\\list-of-versions.txt` ).  

#### <a name="download-a-blob-snapshot"></a>Blob 스냅샷 다운로드

Blob 스냅숏의 **날짜/시간** 값을 참조 하 여 [blob 스냅숏을](/azure/storage/blobs/snapshots-overview.md) 다운로드할 수 있습니다. 

|    |     |
|--------|-----------|
| **구문** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>?sharesnapshot=<DateTime-of-snapshot>' '<local-file-path>'` |
| **예제** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sharesnapshot=2020-09-23T08:21:07.0000000Z' 'C:\myDirectory\myTextFile.txt'` |
| **예** (계층적 네임 스페이스) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt?sharesnapshot=2020-09-23T08:21:07.0000000Z' 'C:\myDirectory\myTextFile.txt'` |

> [!NOTE]
> SAS 토큰을 사용 하 여 blob 데이터에 대 한 액세스 권한을 부여 하는 경우 SAS 토큰 뒤에 스냅숏 **날짜/시간** 을 추가 합니다. 예를 들어 `'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D&sharesnapshot=2020-09-23T08:21:07.0000000Z'`을 참조하십시오.

## <a name="download-with-optional-flags"></a>선택적 플래그로 다운로드

선택적 플래그를 사용 하 여 다운로드 작업을 조정할 수 있습니다. 다음은 몇 가지 예입니다.

|시나리오|플래그|
|---|---|
|자동으로 파일의 압축을 해제 합니다.|**--압축 풀기**|
|복사 관련 로그 항목에 대 한 세부 정보를 지정 합니다.|**--로그 수준** = \[ 경고 \| 오류 \| 정보 \| 없음\]|
|대상과 충돌 하는 파일 및 blob를 덮어쓸지 여부를 지정 합니다.|**--덮어쓰기** = \[ true \| false \| ifSourceNewer \| prompt\]|

전체 목록은 [options](storage-ref-azcopy-copy.md#options)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

다음 문서에서 더 많은 예제를 찾아보세요.

- [예: 업로드](storage-use-azcopy-blobs-upload.md)
- [예: 계정 간 복사](storage-use-azcopy-blobs-copy.md)
- [예: 동기화](storage-use-azcopy-blobs-synchronize.md)
- [예: Amazon S3 버킷](storage-use-azcopy-s3.md)
- [예: Azure Files](storage-use-azcopy-files.md)
- [자습서: AzCopy를 사용하여 클라우드로 온-프레미스 데이터 마이그레이션](storage-use-azcopy-migrate-on-premises-data.md)
- [AzCopy 구성, 최적화 및 문제 해결](storage-use-azcopy-configure.md)