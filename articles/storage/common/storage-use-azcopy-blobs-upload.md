---
title: AzCopy v10를 사용 하 여 Azure Blob storage에 파일 업로드 Microsoft Docs
description: 이 문서에는 Azure Blob storage에 파일을 업로드 하는 데 도움이 되는 AzCopy 예제 명령의 컬렉션이 포함 되어 있습니다.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 12/11/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: ec88a3c740ceda7ccf352f8f32f94e2cd52d0988
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/12/2020
ms.locfileid: "97358761"
---
# <a name="upload-files-to-azure-blob-storage-by-using-azcopy-v10"></a>AzCopy v10를 사용 하 여 Azure Blob storage에 파일 업로드

AzCopy v10 명령줄 유틸리티를 사용 하 여 Blob 저장소에 파일 및 디렉터리를 업로드할 수 있습니다. 

Blob 다운로드, Blob storage와 동기화, 계정 간에 blob 복사 등의 다른 작업 유형에 대 한 예제를 보려면이 문서의 [다음 단계](#next-steps) 섹션에 제공 된 링크를 참조 하세요.

## <a name="get-started"></a>시작하기

[AzCopy 시작](storage-use-azcopy-v10.md) 문서를 참조 하 여 AzCopy를 다운로드 하 고, 저장소 서비스에 권한 부여 자격 증명을 제공할 수 있는 방법에 대해 알아보세요.

> [!NOTE] 
> 이 문서의 예제에서는 Azure Active Directory (Azure AD)를 사용 하 여 권한 부여 자격 증명을 제공 했다고 가정 합니다.
>
> 대신 SAS 토큰을 사용 하 여 blob 데이터에 대 한 액세스 권한을 부여 하는 경우 각 AzCopy 명령의 리소스 URL에 해당 토큰을 추가할 수 있습니다. 예를 들어 `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`을 참조하십시오.

## <a name="create-a-container"></a>컨테이너 만들기

[Azcopy 만들기](storage-ref-azcopy-make.md) 명령을 사용 하 여 컨테이너를 만들 수 있습니다.

> [!TIP]
> 이 예에서는 경로 인수를 작은따옴표 (' ')로 묶습니다. Windows 명령 셸 (cmd.exe)을 제외 하 고 모든 명령 셸에서 작은따옴표를 사용 합니다. cmd.exe (Windows 명령 셸)을 사용 하는 경우 작은따옴표 (' ') 대신 경로 인수를 큰따옴표 ("")로 묶습니다.

|    |     |
|--------|-----------|
| **구문** | `azcopy make 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>'` |
| **예제** | `azcopy make 'https://mystorageaccount.blob.core.windows.net/mycontainer'` |
| **예** (계층적 네임 스페이스) | `azcopy make 'https://mystorageaccount.dfs.core.windows.net/mycontainer'` |

자세한 참조 문서는 [azcopy 만들기](storage-ref-azcopy-make.md)를 참조 하세요.

## <a name="upload-a-file"></a>파일 업로드

[Azcopy copy](storage-ref-azcopy-copy.md) 명령을 사용 하 여 파일을 업로드 합니다.

> [!TIP]
> 이 예에서는 경로 인수를 작은따옴표 (' ')로 묶습니다. Windows 명령 셸 (cmd.exe)을 제외 하 고 모든 명령 셸에서 작은따옴표를 사용 합니다. cmd.exe (Windows 명령 셸)을 사용 하는 경우 작은따옴표 (' ') 대신 경로 인수를 큰따옴표 ("")로 묶습니다.

|    |     |
|--------|-----------|
| **구문** | `azcopy copy '<local-file-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>'` |
| **예제** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |
| **예** (계층적 네임 스페이스) | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt'` |

파일 경로 또는 파일 이름 어디에 나 와일드 카드 기호 (*)를 사용 하 여 파일을 업로드할 수도 있습니다. 예를 들면 `'C:\myDirectory\*.txt'` , 또는 `C:\my*\*.txt` 입니다.

## <a name="upload-a-directory"></a>디렉터리 업로드

[Azcopy copy](storage-ref-azcopy-copy.md) 명령을 사용 하 여 디렉터리를 업로드 합니다. 

이 예에서는 디렉터리와 해당 디렉터리의 모든 파일을 blob 컨테이너에 복사 합니다. 결과는 컨테이너에서 이름이 같은 디렉터리입니다.

> [!TIP]
> 이 예에서는 경로 인수를 작은따옴표 (' ')로 묶습니다. Windows 명령 셸 (cmd.exe)을 제외 하 고 모든 명령 셸에서 작은따옴표를 사용 합니다. cmd.exe (Windows 명령 셸)을 사용 하는 경우 작은따옴표 (' ') 대신 경로 인수를 큰따옴표 ("")로 묶습니다.

|    |     |
|--------|-----------|
| **구문** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --recursive` |
| **예제** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive` |
| **예** (계층적 네임 스페이스) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --recursive` |

컨테이너 내의 디렉터리로 복사 하려면 명령 문자열에서 해당 디렉터리의 이름을 지정 하면 됩니다.

|    |     |
|--------|-----------|
| **예제** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' --recursive` |
| **예** (계층적 네임 스페이스) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' --recursive` |

컨테이너에 없는 디렉터리 이름을 지정 하는 경우 AzCopy는 해당 이름으로 새 디렉터리를 만듭니다.

## <a name="upload-directory-contents"></a>디렉터리 콘텐츠 업로드

[Azcopy copy](storage-ref-azcopy-copy.md) 명령을 사용 하 여 디렉터리의 내용을 업로드 합니다. 포함 하는 디렉터리 자체를 복사 하지 않고 와일드 카드 기호 (*)를 사용 하 여 콘텐츠를 업로드 합니다.

> [!TIP]
> 이 예에서는 경로 인수를 작은따옴표 (' ')로 묶습니다. Windows 명령 셸 (cmd.exe)을 제외 하 고 모든 명령 셸에서 작은따옴표를 사용 합니다. cmd.exe (Windows 명령 셸)을 사용 하는 경우 작은따옴표 (' ') 대신 경로 인수를 큰따옴표 ("")로 묶습니다.

|    |     |
|--------|-----------|
| **구문** | `azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>'` |
| **예제** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory'` |
| **예** (계층적 네임 스페이스) | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory'` |


플래그를 추가 `--recursive` 하 여 모든 하위 디렉터리의 파일을 업로드 합니다.

## <a name="upload-specific-files"></a>특정 파일 업로드

전체 파일 이름, 와일드 카드 문자 (*)가 포함 된 부분 이름, 날짜 및 시간을 사용 하 여 특정 파일을 업로드할 수 있습니다.

> [!TIP]
> 이 예에서는 경로 인수를 작은따옴표 (' ')로 묶습니다. Windows 명령 셸 (cmd.exe)을 제외 하 고 모든 명령 셸에서 작은따옴표를 사용 합니다. cmd.exe (Windows 명령 셸)을 사용 하는 경우 작은따옴표 (' ') 대신 경로 인수를 큰따옴표 ("")로 묶습니다.

### <a name="specify-multiple-complete-file-names"></a>전체 파일 이름을 여러 개 지정

옵션과 함께 [azcopy copy](storage-ref-azcopy-copy.md) 명령을 사용 합니다 `--include-path` . 세미콜론 ()을 사용 하 여 개별 파일 이름을 구분 `;` 합니다.

|    |     |
|--------|-----------|
| **구문** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-path <semicolon-separated-file-list>` |
| **예제** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive` |
| **예** (계층적 네임 스페이스) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive` |

이 예제에서 AzCopy는 `C:\myDirectory\photos` 디렉터리와 파일을 전송 합니다 `C:\myDirectory\documents\myFile.txt` . `--recursive`디렉터리의 모든 파일을 전송 하는 옵션을 포함 합니다 `C:\myDirectory\photos` .

옵션을 사용 하 여 파일을 제외할 수도 있습니다 `--exclude-path` . 자세히 알아보려면 [azcopy copy](storage-ref-azcopy-copy.md) 참조 문서를 참조 하세요.

### <a name="use-wildcard-characters"></a>와일드 카드 문자 사용

옵션과 함께 [azcopy copy](storage-ref-azcopy-copy.md) 명령을 사용 합니다 `--include-pattern` . 와일드 카드 문자를 포함 하는 부분 이름을 지정 합니다. Semicolin ()를 사용 하 여 이름을 구분 `;` 합니다. 

|    |     |
|--------|-----------|
| **구문** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **예제** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'` |
| **예** (계층적 네임 스페이스) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'` |

옵션을 사용 하 여 파일을 제외할 수도 있습니다 `--exclude-pattern` . 자세히 알아보려면 [azcopy copy](storage-ref-azcopy-copy.md) 참조 문서를 참조 하세요.

`--include-pattern`및 `--exclude-pattern` 옵션은 경로에는 적용 되지 않고 파일 이름에만 적용 됩니다.  디렉터리 트리에 있는 모든 텍스트 파일을 복사 하려는 경우 옵션을 사용 `–recursive` 하 여 전체 디렉터리 트리를 가져온 다음를 사용 하 여 `–include-pattern` `*.txt` 모든 텍스트 파일을 가져오도록 지정 합니다.

### <a name="upload-files-that-were-modified-before-or-after-a-date-and-time"></a>날짜 및 시간 이전 또는 이후 수정 된 파일 업로드 

또는 옵션과 함께 [azcopy copy](storage-ref-azcopy-copy.md) 명령을 사용 `--include-before` 합니다 `--include-after` . ISO-8601 형식으로 날짜 및 시간을 지정 합니다 (예: `2020-08-19T15:04:00Z` ). 

다음 예에서는 지정한 날짜 또는 그 이후에 수정 된 파일을 업로드 합니다.

|    |     |
|--------|-----------|
| **구문** | `azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>'  --include-after <Date-Time-in-ISO-8601-format>` |
| **예제** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory'  --include-after '2020-08-19T15:04:00Z'` |
| **예** (계층적 네임 스페이스) | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory'   --include-after '2020-08-19T15:04:00Z'` |

자세한 참조는 [azcopy copy](storage-ref-azcopy-copy.md) 참조 문서를 참조 하세요.

## <a name="upload-with-index-tags"></a>인덱스 태그를 사용 하 여 업로드

파일을 업로드 하 고 [blob 인덱스 태그 (미리 보기)](../blobs/storage-manage-find-blobs.md) 를 대상 blob에 추가할 수 있습니다.  

Azure AD 인증을 사용 하는 경우 보안 주체에 게 [저장소 Blob 데이터 소유자](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) 역할을 할당 하거나 `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` 사용자 지정 azure 역할을 통해 [azure 리소스 공급자 작업](../../role-based-access-control/resource-provider-operations.md#microsoftstorage) 에 대 한 권한을 부여 해야 합니다. SAS (공유 액세스 서명) 토큰을 사용 하는 경우 해당 토큰은 sas 권한을 통해 blob의 태그에 대 한 액세스를 제공 해야 합니다 `t` .

태그를 추가 하려면 `--blob-tags` URL 인코딩 키-값 쌍과 함께 옵션을 사용 합니다. 예를 들어 키 및 값을 추가 하려면 `my tag` `my tag value` `--blob-tags='my%20tag=my%20tag%20value'` 대상 매개 변수에를 추가 합니다. 

앰퍼샌드 ()를 사용 하 여 여러 인덱스 태그를 구분 `&` 합니다.  예를 들어 키와 값을 추가 하려는 경우 `my second tag` `my second tag value` 전체 옵션 문자열은 `--blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` 입니다.

다음 예에서는 옵션을 사용 하는 방법을 보여 줍니다 `--blob-tags` .

> [!TIP]
> 이 예에서는 경로 인수를 작은따옴표 (' ')로 묶습니다. Windows 명령 셸 (cmd.exe)을 제외 하 고 모든 명령 셸에서 작은따옴표를 사용 합니다. cmd.exe (Windows 명령 셸)을 사용 하는 경우 작은따옴표 (' ') 대신 경로 인수를 큰따옴표 ("")로 묶습니다.

|    |     |
|--------|-----------|
| **파일 업로드** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt' --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` |
| **디렉터리 업로드** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'`|
| **디렉터리 콘텐츠 업로드** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` |

> [!NOTE]
> 원본에 대 한 디렉터리를 지정 하는 경우 대상에 복사 된 모든 blob에는 명령에서 지정 하는 것과 동일한 태그가 지정 됩니다.

## <a name="upload-with-optional-flags"></a>선택적 플래그를 사용 하 여 업로드

선택적 플래그를 사용 하 여 업로드 작업을 조정할 수 있습니다. 다음은 몇 가지 예입니다.

|시나리오|플래그|
|---|---|
|추가 Blob 또는 페이지 Blob으로 파일을 업로드합니다.|**--blob-형식** = \[ BlockBlob \| pageblob \| appendblob\]|
|특정 액세스 계층(예: 보관 계층)에 업로드합니다.|**--블록-blob 계층** = \[ 없음 \| 핫 \| 쿨 \| 아카이브\]|

전체 목록은 [options](storage-ref-azcopy-copy.md#options)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

다음 문서에서 더 많은 예제를 찾아보세요.

- [예제: 다운로드](storage-use-azcopy-blobs-download.md)
- [예제: 계정 간 복사](storage-use-azcopy-blobs-copy.md)
- [예제: 동기화](storage-use-azcopy-blobs-synchronize.md)
- [예제: Amazon S3 버킷](storage-use-azcopy-s3.md)
- [예: Azure Files](storage-use-azcopy-files.md)
- [자습서: AzCopy를 사용하여 클라우드로 온-프레미스 데이터 마이그레이션](storage-use-azcopy-migrate-on-premises-data.md)
- [AzCopy 구성, 최적화 및 문제 해결](storage-use-azcopy-configure.md)