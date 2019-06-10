---
title: V10 AzCopy를 사용 하 여 Azure Blob storage에서 데이터 전송 | Microsoft Docs
description: AzCopy의 컬렉션을 포함 하는이 문서의 예제에서는 명령 도움이 되는 컨테이너를 만들고, 파일을 복사 및 로컬 파일 시스템 및 컨테이너 간의 디렉터리 동기화 합니다.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 140f2ec6252eac2958f236b2ffb48225fa16fe2b
ms.sourcegitcommit: 6932af4f4222786476fdf62e1e0bf09295d723a1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/05/2019
ms.locfileid: "66688050"
---
# <a name="transfer-data-with-azcopy-and-blob-storage"></a>AzCopy 및 Blob storage 사용 하 여 데이터를 전송 합니다.

AzCopy는, 또는 저장소 계정 간에 데이터를 복사 하는 데 사용할 수 있는 명령줄 유틸리티입니다. 이 문서에는 Blob storage를 사용 하는 예제 명령을 포함 합니다.

## <a name="get-started"></a>시작하기

참조 된 [AzCopy를 사용 하 여 시작](storage-use-azcopy-v10.md) 문서를 AzCopy를 다운로드 하 여 저장소 서비스에 권한 부여 자격 증명 제공 하는 방법에 알아봅니다.

> [!NOTE]
> 이 문서의 예제를 사용 하 여 본인 인증 하는 것으로 가정 합니다 `AzCopy login` 명령입니다. AzCopy는 Blob storage의 데이터에 대 한 액세스 권한을 부여 하려면 Azure AD 계정을 사용 합니다.
>
> Blob 데이터에 대 한 액세스 권한을 부여 하는 SAS 토큰 사용 하려는, 해당 토큰에 각 AzCopy 명령에서 리소스 URL을 추가할 수 있습니다.
>
> 예: `https://<storage-account-name>.blob.core.windows.net/<container-name>?<SAS-token>"`

## <a name="create-a-container"></a>컨테이너 만들기

AzCopy를 사용할 수 있습니다 `make` 명령은 컨테이너를 만들려고 합니다. 이 섹션의 예에서는 이라는 컨테이너를 만듭니다 `mycontainer`합니다.

|    |     |
|--------|-----------|
| **구문** | `azcopy make "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>` |
| **예제** | `azcopy make "https://mystorageaccount.blob.core.windows.net/mycontainer"` |
| **예제** (계층적 네임 스페이스) | `azcopy make "https://mystorageaccount.dfs.core.windows.net/mycontainer"` |

## <a name="upload-files"></a>파일 업로드

AzCopy를 사용할 수 있습니다 `copy` 명령을 로컬 컴퓨터의 파일 및 디렉터리를 업로드 합니다.

이 섹션에는 다음 예제가 포함되어 있습니다.

> [!div class="checklist"]
> * 파일 업로드
> * 디렉터리를 업로드 합니다.
> * 와일드 카드 문자를 사용 하 여 파일 업로드

> [!NOTE]
> 자동으로 AzCopy 계산 하 고 파일의 md5 해시 코드를 저장 하지 않습니다. 이렇게 하려면 AzCopy를 사용 하도록 하려는 경우 다음 추가 `--put-md5` 각 복사 명령 플래그입니다. 이런 방식으로 blob을 다운로드 하면 AzCopy MD5 해시를 계산 데이터를 다운로드 하 고 blob에 저장 된 MD5 해시 확인에 대 한 `Content-md5` 속성 계산 된 해시와 일치 합니다.

### <a name="upload-a-file"></a>파일 업로드

|    |     |
|--------|-----------|
| **구문** | `azcopy cp "<local-file-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>"` |
| **예제** | `azcopy copy "C:\myDirectory\myTextFile.txt" "https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt"` |
| **예제** (계층적 네임 스페이스) | `azcopy copy "C:\myDirectory\myTextFile.txt" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt"` |

> [!NOTE]
> 기본적으로 AzCopy는 블록 blob으로 데이터를 업로드합니다. 플래그를 사용 하는 추가 Blob 또는 페이지 Blob 파일을 업로드할 `--blob-type=[BlockBlob|PageBlob|AppendBlob]`합니다.

### <a name="upload-a-directory"></a>디렉터리를 업로드 합니다.

이 예제에서는 blob 컨테이너에 디렉터리 (및 모든 해당 디렉터리의 파일)를 복사 합니다. 결과 같은 이름으로 컨테이너에는 디렉터리입니다.

|    |     |
|--------|-----------|
| **구문** | `azcopy copy "<local-directory-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" --recursive` |
| **예제** | `azcopy copy "C:\myDirectory" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive` |
| **예제** (계층적 네임 스페이스) | `azcopy copy "C:\myDirectory" "https://mystorageaccount.dfs.core.windows.net/mycontainer" --recursive` |

컨테이너 내의 디렉터리에 복사 하려면 방금 명령 문자열에 해당 디렉터리의 이름을 지정 합니다.

|    |     |
|--------|-----------|
| **예제** | `azcopy copy "C:\myDirectory" "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory" --recursive` |
| **예제** (계층적 네임 스페이스) | `azcopy copy "C:\myDirectory" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory" --recursive` |

컨테이너에 존재 하지 않는 디렉터리의 이름을 지정 하면 AzCopy는 해당 이름으로 새 디렉터리를 만듭니다.

### <a name="upload-the-contents-of-a-directory"></a>디렉터리의 콘텐츠 업로드

와일드 카드 기호 (*)를 사용 하 여 자체 포함 디렉터리를 복사 하지 않고 디렉터리의 콘텐츠를 업로드할 수 있습니다.

|    |     |
|--------|-----------|
| **구문** | `azcopy copy "<local-directory-path>\*" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>` |
| **예제** | `azcopy copy "C:\myDirectory\*" "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory"` |
| **예제** (계층적 네임 스페이스) | `azcopy copy "C:\myDirectory\*" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory"` |

> [!NOTE]
> 추가 된 `--recursive` 모든 하위 디렉터리에 파일을 업로드 하는 플래그입니다.

## <a name="download-files"></a>파일 다운로드

AzCopy를 사용할 수 있습니다 `copy` 명령을 로컬 컴퓨터에 blob, 디렉터리 및 컨테이너를 다운로드 합니다.

이 섹션에는 다음 예제가 포함되어 있습니다.

> [!div class="checklist"]
> * 파일 다운로드
> * 디렉터리를 다운로드 합니다.
> * 와일드 카드 문자를 사용 하 여 파일을 다운로드 합니다.

> [!NOTE]
> 경우는 `Content-md5` 해시를 포함 하는 blob의 속성 값, 다운로드 한 데이터에 대 한 MD5 해시를 계산 하 고 blob에 저장 된 MD5 해시를 확인 하는 AzCopy `Content-md5` 속성 계산 된 해시와 일치 합니다. 다운로드가 실패를 추가 하 여이 동작을 재정의 하지 않는 한 이러한 값이 일치 하지 않으면 `--check-md5=NoCheck` 또는 `--check-md5=LogOnly` 복사 명령입니다.

### <a name="download-a-file"></a>파일 다운로드

|    |     |
|--------|-----------|
| **구문** | `azcopy copy "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>" "<local-file-path>"` |
| **예제** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt" "C:\myDirectory\myTextFile.txt"` |
| **예제** (계층적 네임 스페이스) | `azcopy copy "https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt" "C:\myDirectory\myTextFile.txt"` |

### <a name="download-a-directory"></a>디렉터리를 다운로드 합니다.

|    |     |
|--------|-----------|
| **구문** | `azcopy copy "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>" "<local-directory-path>" --recursive` |
| **예제** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory "C:\myDirectory"  --recursive` |
| **예제** (계층적 네임 스페이스) | `azcopy copy "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory "C:\myDirectory"  --recursive` |

이 예제에서는 라는 디렉터리 `C:\myDirectory\myBlobDirectory` 다운로드 한 파일을 모두 포함 하는 합니다.

### <a name="download-the-contents-of-a-directory"></a>디렉터리의 콘텐츠를 다운로드 합니다.

와일드 카드 기호 (*)를 사용 하 여 자체 포함 디렉터리를 복사 하지 않고 디렉터리의 콘텐츠를 다운로드할 수 있습니다.

> [!NOTE]
> 현재이 시나리오는 계층 구조 네임 스페이스에 없는 계정에 대해서만 지원 됩니다.

|    |     |
|--------|-----------|
| **구문** | `azcopy copy "https://<storage-account-name>.blob.core.windows.net/<container-name>/*" "<local-directory-path>/"` |
| **예제** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory/*" "C:\myDirectory"` |

> [!NOTE]
> 추가 된 `--recursive` 모든 하위 디렉터리의 파일을 다운로드 하는 플래그입니다.

## <a name="copy-blobs-between-storage-accounts"></a>저장소 계정 간에 blob 복사

AzCopy를 사용 하 여 다른 저장소 계정에 blob을 복사할 수 있습니다. 복사 작업은 동기화 되어 있으므로 명령이 반환 될 때 모든 파일을 복사 하는 것이 나타내는입니다.

> [!NOTE]
> 현재이 시나리오는 계층 구조 네임 스페이스에 없는 계정에 대해서만 지원 됩니다.

AzCopy를 사용 합니다 [URL에서 블록 배치](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) API, 저장소 서버 간에 직접 데이터를 복사 하도록 합니다. 이러한 복사 작업에는 컴퓨터의 네트워크 대역폭 사용 하지 마세요.

이 섹션에는 다음 예제가 포함되어 있습니다.

> [!div class="checklist"]
> * 다른 저장소 계정으로 blob 복사
> * 디렉터리를 다른 저장소 계정에 복사
> * 다른 저장소 계정 컨테이너를 복사 합니다.
> * 다른 저장소 계정에 모든 컨테이너, 디렉터리 및 파일 복사

### <a name="copy-a-blob-to-another-storage-account"></a>다른 저장소 계정으로 blob 복사

|    |     |
|--------|-----------|
| **구문** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>"` |
| **예제** | `azcopy cp "https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt" "https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt"` |

### <a name="copy-a-directory-to-another-storage-account"></a>디렉터리를 다른 저장소 계정에 복사

|    |     |
|--------|-----------|
| **구문** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-path>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<directory-path>" --recursive` |
| **예제** | `azcopy cp "https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory" "https://mydestinationaccount.blob.core.windows.net/mycontainer/myBlobDirectory" --recursive` |

### <a name="copy-a-containers-to-another-storage-account"></a>다른 저장소 계정 컨테이너를 복사 합니다.

|    |     |
|--------|-----------|
| **구문** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/<container-name>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>" --recursive` |
| **예제** | `azcopy cp "https://mysourceaccount.blob.core.windows.net/mycontainer" "https://mydestinationaccount.blob.core.windows.net/mycontainer" --recursive` |

### <a name="copy-all-containers-directories-and-files-to-another-storage-account"></a>다른 저장소 계정에 모든 컨테이너, 디렉터리 및 파일 복사

|    |     |
|--------|-----------|
| **구문** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/" "https://<destination-storage-account-name>.blob.core.windows.net/" --recursive"` |
| **예제** | `azcopy cp "https://mysourceaccount.blob.core.windows.net" "https://mydestinationaccount.blob.core.windows.net" --recursive` |

## <a name="synchronize-files"></a>파일 동기화

Blob 컨테이너에 로컬 파일 시스템의 콘텐츠를 동기화 할 수 있습니다. 또한 컴퓨터에 로컬 파일 시스템에 blob 컨테이너를 동기화 할 수 있습니다. 단방향 동기화가입니다. 즉, 이러한 두 끝점 중 원본 이며 어느 대상을 선택 합니다.

> [!NOTE]
> 현재 버전의 AzCopy 다른 원본과 대상 간에 동기화 하지 않습니다 (예: 파일 저장소 또는 Amazon Web Services (AWS) S3 버킷).

`sync` 명령 비교 하 여 파일 이름 및 타임 스탬프를 마지막으로 수정 합니다. 설정 합니다 `--delete-destination` 선택적 플래그 값입니다 `true` 또는 `prompt` 해당 파일이 더 이상 원본 디렉터리에 있는 경우 대상 디렉터리에 파일을 삭제 합니다.

설정한 경우에 `--delete-destination` 플래그를 `true` AzCopy 프롬프트를 제공 하지 않고 파일을 삭제 합니다. AzCopy 삭제 하기 전에 파일을 표시 하 라는 메시지가 원한다 면 설정 된 `--delete-destination` 플래그를 `prompt`입니다.

> [!NOTE]
> 실수로 인 한 삭제를 방지 하려면 사용 하도록 설정 합니다 [일시 삭제](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) 기능을 사용 하기 전에 `--delete-destination=prompt|true` 플래그입니다.

### <a name="synchronize-a-container-to-a-local-file-system"></a>로컬 파일 시스템에 컨테이너를 동기화 합니다.

이 경우 로컬 파일 시스템 원본 되며 컨테이너는 대상입니다.

|    |     |
|--------|-----------|
| **구문** | `azcopy sync "<local-directory-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" --recursive` |
| **예제** | `azcopy sync "C:\myDirectory" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive` |
| **예제** (계층적 네임 스페이스) | `azcopy sync "C:\myDirectory" "https://<storage-account-name>.dfs.core.windows.net/mycontainer" --recursive` |


### <a name="synchronize-a-local-file-system-to-a-container"></a>컨테이너에 로컬 파일 시스템 동기화

이 경우 컨테이너 원본 되며 로컬 파일 시스템은 대상입니다.

|    |     |
|--------|-----------|
| **구문** | `azcopy sync "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" "C:\myDirectory" --recursive` |
| **예제** | `azcopy sync "https://mystorageaccount.blob.core.windows.net/mycontainer" "C:\myDirectory" --recursive` |
| **예제** (계층적 네임 스페이스) | `azcopy sync "https://mystorageaccount.dfs.core.windows.net/mycontainer" "C:\myDirectory" --recursive` |

## <a name="next-steps"></a>다음 단계

이러한 문서 중 하나에서 더 많은 예제를 찾습니다.

- [AzCopy 시작](storage-use-azcopy-v10.md)

- [자습서: AzCopy를 사용 하 여 클라우드 저장소에 온-프레미스 데이터 마이그레이션](storage-use-azcopy-migrate-on-premises-data.md)

- [AzCopy 및 파일 스토리지를 사용하여 데이터 전송](storage-use-azcopy-files.md)

- [AzCopy 및 Amazon S3 버킷을 사용하여 데이터 전송](storage-use-azcopy-s3.md)

- [AzCopy 구성, 최적화 및 문제 해결](storage-use-azcopy-configure.md)