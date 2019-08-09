---
title: AzCopy v10를 사용 하 여 Azure Blob storage 간 데이터 전송 Microsoft Docs
description: 이 문서에는 컨테이너를 만들고, 파일을 복사 하 고, 로컬 파일 시스템과 컨테이너 간에 디렉터리를 동기화 하는 데 도움이 되는 AzCopy 예제 명령의 컬렉션이 포함 되어 있습니다.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: d488189596fac4ae8fada3899d0b9c1bbe7900c6
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/08/2019
ms.locfileid: "68855305"
---
# <a name="transfer-data-with-azcopy-and-blob-storage"></a>AzCopy 및 Blob 저장소를 사용 하 여 데이터 전송

AzCopy은 저장소 계정 간에 데이터를 복사 하거나 저장소 계정 간에 데이터를 복사 하는 데 사용할 수 있는 명령줄 유틸리티입니다. 이 문서에는 Blob 저장소에서 작동 하는 예제 명령이 포함 되어 있습니다.

## <a name="get-started"></a>시작

[AzCopy 시작](storage-use-azcopy-v10.md) 문서를 참조 하 여 AzCopy를 다운로드 하 고, 저장소 서비스에 권한 부여 자격 증명을 제공할 수 있는 방법에 대해 알아보세요.

> [!NOTE]
> 이 문서의 예제에서는 `AzCopy login` 명령을 사용 하 여 id를 인증 했다고 가정 합니다. AzCopy는 Azure AD 계정을 사용 하 여 Blob storage의 데이터에 대 한 액세스 권한을 부여 합니다.
>
> 대신 SAS 토큰을 사용 하 여 blob 데이터에 대 한 액세스 권한을 부여 하는 경우 각 AzCopy 명령의 리소스 URL에 해당 토큰을 추가할 수 있습니다.
>
> 예를 들어 `https://<storage-account-name>.blob.core.windows.net/<container-name>?<SAS-token>"`을 참조하십시오.

## <a name="create-a-container"></a>컨테이너 만들기

AzCopy `make` 명령을 사용 하 여 컨테이너를 만들 수 있습니다. 이 단원의 예제에서는 라는 `mycontainer`컨테이너를 만듭니다.

|    |     |
|--------|-----------|
| **구문** | `azcopy make "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>` |
| **예제** | `azcopy make "https://mystorageaccount.blob.core.windows.net/mycontainer"` |
| **예** (계층적 네임 스페이스) | `azcopy make "https://mystorageaccount.dfs.core.windows.net/mycontainer"` |

## <a name="upload-files"></a>파일 업로드

AzCopy `copy` 명령을 사용 하 여 로컬 컴퓨터에서 파일 및 디렉터리를 업로드할 수 있습니다.

이 섹션에서는 다음과 같은 예를 보여 줍니다.

> [!div class="checklist"]
> * 파일 업로드
> * 디렉터리 업로드
> * 와일드 카드 문자를 사용 하 여 파일 업로드

> [!NOTE]
> AzCopy는 파일의 md5 해시 코드를 자동으로 계산 하 고 저장 하지 않습니다. AzCopy를 수행 하려면 각 copy 명령에 `--put-md5` 플래그를 추가 합니다. 이렇게 하면 blob이 다운로드 될 때 AzCopy는 다운로드 된 데이터에 대 한 md5 해시를 계산 하 고 blob의 `Content-md5` 속성에 저장 된 md5 해시가 계산 된 해시와 일치 하는지 확인 합니다.

### <a name="upload-a-file"></a>파일 업로드

|    |     |
|--------|-----------|
| **구문** | `azcopy cp "<local-file-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>"` |
| **예제** | `azcopy copy "C:\myDirectory\myTextFile.txt" "https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt"` |
| **예** (계층적 네임 스페이스) | `azcopy copy "C:\myDirectory\myTextFile.txt" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt"` |

> [!NOTE]
> AzCopy은 기본적으로 데이터를 블록 blob에 업로드 합니다. 파일을 추가 Blob 또는 페이지 Blob으로 업로드 하려면 플래그 `--blob-type=[BlockBlob|PageBlob|AppendBlob]`를 사용 합니다.

### <a name="upload-a-directory"></a>디렉터리 업로드

이 예에서는 디렉터리와 해당 디렉터리의 모든 파일을 blob 컨테이너에 복사 합니다. 결과는 컨테이너에서 이름이 같은 디렉터리입니다.

|    |     |
|--------|-----------|
| **구문** | `azcopy copy "<local-directory-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" --recursive` |
| **예제** | `azcopy copy "C:\myDirectory" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive` |
| **예** (계층적 네임 스페이스) | `azcopy copy "C:\myDirectory" "https://mystorageaccount.dfs.core.windows.net/mycontainer" --recursive` |

컨테이너 내의 디렉터리로 복사 하려면 명령 문자열에서 해당 디렉터리의 이름을 지정 하면 됩니다.

|    |     |
|--------|-----------|
| **예제** | `azcopy copy "C:\myDirectory" "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory" --recursive` |
| **예** (계층적 네임 스페이스) | `azcopy copy "C:\myDirectory" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory" --recursive` |

컨테이너에 없는 디렉터리의 이름을 지정 하는 경우 AzCopy은 해당 이름으로 새 디렉터리를 만듭니다.

### <a name="upload-the-contents-of-a-directory"></a>디렉터리의 콘텐츠 업로드

와일드 카드 기호 (*)를 사용 하 여 포함 하는 디렉터리 자체를 복사 하지 않고 디렉터리의 내용을 업로드할 수 있습니다.

|    |     |
|--------|-----------|
| **구문** | `azcopy copy "<local-directory-path>\*" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>` |
| **예제** | `azcopy copy "C:\myDirectory\*" "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory"` |
| **예** (계층적 네임 스페이스) | `azcopy copy "C:\myDirectory\*" "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory"` |

> [!NOTE]
> 플래그를 `--recursive` 추가 하 여 모든 하위 디렉터리의 파일을 업로드 합니다.

## <a name="download-files"></a>파일 다운로드

AzCopy `copy` 명령을 사용 하 여 로컬 컴퓨터에 blob, 디렉터리 및 컨테이너를 다운로드할 수 있습니다.

이 섹션에서는 다음과 같은 예를 보여 줍니다.

> [!div class="checklist"]
> * 파일 다운로드
> * 디렉터리 다운로드
> * 와일드 카드 문자를 사용 하 여 파일 다운로드

> [!NOTE]
> Blob의 `Content-md5` 속성 값에 해시가 포함 된 경우 AzCopy는 다운로드 된 데이터에 대 한 md5 해시를 계산 하 고 blob의 `Content-md5` 속성에 저장 된 md5 해시가 계산 된 해시와 일치 하는지 확인 합니다. 이러한 값이 일치 하지 않으면 복사 명령에 또는 `--check-md5=NoCheck` `--check-md5=LogOnly` 을 추가 하 여이 동작을 재정의 하지 않는 한 다운로드가 실패 합니다.

### <a name="download-a-file"></a>파일 다운로드

|    |     |
|--------|-----------|
| **구문** | `azcopy copy "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>" "<local-file-path>"` |
| **예제** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt" "C:\myDirectory\myTextFile.txt"` |
| **예** (계층적 네임 스페이스) | `azcopy copy "https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt" "C:\myDirectory\myTextFile.txt"` |

### <a name="download-a-directory"></a>디렉터리 다운로드

|    |     |
|--------|-----------|
| **구문** | `azcopy copy "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>" "<local-directory-path>" --recursive` |
| **예제** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory "C:\myDirectory"  --recursive` |
| **예** (계층적 네임 스페이스) | `azcopy copy "https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory "C:\myDirectory"  --recursive` |

이 예에서는 다운로드 한 모든 파일 `C:\myDirectory\myBlobDirectory` 을 포함 하는 라는 디렉터리를 생성 합니다.

### <a name="download-the-contents-of-a-directory"></a>디렉터리의 콘텐츠 다운로드

와일드 카드 기호 (*)를 사용 하 여 포함 하는 디렉터리 자체를 복사 하지 않고 디렉터리의 콘텐츠를 다운로드할 수 있습니다.

> [!NOTE]
> 현재이 시나리오는 계층 네임 스페이스가 없는 계정에 대해서만 지원 됩니다.

|    |     |
|--------|-----------|
| **구문** | `azcopy copy "https://<storage-account-name>.blob.core.windows.net/<container-name>/*" "<local-directory-path>/"` |
| **예제** | `azcopy copy "https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory/*" "C:\myDirectory"` |

> [!NOTE]
> 플래그를 `--recursive` 추가 하 여 모든 하위 디렉터리의 파일을 다운로드 합니다.

## <a name="copy-blobs-between-storage-accounts"></a>저장소 계정 간에 blob 복사

AzCopy를 사용 하 여 blob을 다른 저장소 계정에 복사할 수 있습니다. 복사 작업은 동기식 이므로 명령이 반환 될 때 모든 파일이 복사 되었음을 나타냅니다.

> [!NOTE]
> 현재이 시나리오는 계층 네임 스페이스가 없는 계정에 대해서만 지원 됩니다. 

AzCopy는 [서버](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) 간 [api](https://docs.microsoft.com/en-us/rest/api/storageservices/put-page-from-url)를 사용 하므로 저장소 서버 간에 데이터를 직접 복사 합니다. 이러한 복사 작업은 컴퓨터의 네트워크 대역폭을 사용 하지 않습니다.

이 섹션에서는 다음과 같은 예를 보여 줍니다.

> [!div class="checklist"]
> * 다른 저장소 계정에 blob 복사
> * 다른 저장소 계정에 디렉터리 복사
> * 컨테이너를 다른 저장소 계정에 복사
> * 모든 컨테이너, 디렉터리 및 파일을 다른 저장소 계정에 복사 합니다.

> [!NOTE]
> 현재 릴리스에서는 각 원본 URL에 SAS 토큰을 추가 해야 합니다. AD (Azure Active Directory)를 사용 하 여 권한 부여 자격 증명을 제공 하는 경우 대상 URL 에서만 SAS 토큰을 생략할 수 있습니다. 

### <a name="copy-a-blob-to-another-storage-account"></a>다른 저장소 계정에 blob 복사

|    |     |
|--------|-----------|
| **구문** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>?<SAS-token>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>"` |
| **예제** | `azcopy cp "https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D" "https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt"` |

### <a name="copy-a-directory-to-another-storage-account"></a>다른 저장소 계정에 디렉터리 복사

|    |     |
|--------|-----------|
| **구문** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-path>?<SAS-token>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>" --recursive` |
| **예제** | `azcopy cp "https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D" "https://mydestinationaccount.blob.core.windows.net/mycontainer" --recursive` |

### <a name="copy-a-containers-to-another-storage-account"></a>컨테이너를 다른 저장소 계정에 복사

|    |     |
|--------|-----------|
| **구문** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/<container-name>?<SAS-token>" "https://<destination-storage-account-name>.blob.core.windows.net/<container-name>" --recursive` |
| **예제** | `azcopy cp "https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D" "https://mydestinationaccount.blob.core.windows.net/mycontainer" --recursive` |

### <a name="copy-all-containers-directories-and-files-to-another-storage-account"></a>모든 컨테이너, 디렉터리 및 파일을 다른 저장소 계정에 복사 합니다.

|    |     |
|--------|-----------|
| **구문** | `azcopy cp "https://<source-storage-account-name>.blob.core.windows.net/?<SAS-token>" "https://<destination-storage-account-name>.blob.core.windows.net/" --recursive"` |
| **예제** | `azcopy cp "https://mysourceaccount.blob.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D" "https://mydestinationaccount.blob.core.windows.net" --recursive` |

## <a name="synchronize-files"></a>파일 동기화

로컬 파일 시스템의 내용을 blob 컨테이너와 동기화 할 수 있습니다. 동기화는 단방향입니다. 즉, 이러한 두 끝점 중 원본 및 대상 끝점 중 하나를 선택 합니다.

> [!NOTE]
> 현재이 시나리오는 계층 네임 스페이스가 없는 계정에 대해서만 지원 됩니다. 현재 AzCopy의 릴리스는 다른 원본 및 대상 간에 동기화 되지 않습니다. 예를 들면 다음과 같습니다. File storage 또는 Amazon Web Services (AWS) S3 버킷).

`sync` 명령은 파일 이름과 마지막 수정 된 타임 스탬프를 비교 합니다. 대상 디렉터리에서 파일을 삭제 하려면 `true` `prompt` 선택적 플래그를 또는 값으로 설정 하 고, 해당 파일이 원본 디렉터리에 더 이상 없으면 파일을 삭제 합니다. `--delete-destination`

`--delete-destination` 플래그를 AzCopy로 `true` 설정 하면 프롬프트를 제공 하지 않고 파일이 삭제 됩니다. AzCopy에서 파일을 삭제 하기 전에 프롬프트가 표시 되도록 하려면 `--delete-destination` 플래그를로 `prompt`설정 합니다.

> [!NOTE]
> 실수로 인 한 삭제를 방지 하려면 플래그를 `--delete-destination=prompt|true` 사용 하기 전에 [일시 삭제](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) 기능을 사용 하도록 설정 해야 합니다.

### <a name="update-a-container-with-changes-to-a-local-file-system"></a>로컬 파일 시스템에 대 한 변경 내용으로 컨테이너 업데이트

이 경우 컨테이너는 대상 이며 로컬 파일 시스템은 원본입니다.

|    |     |
|--------|-----------|
| **구문** | `azcopy sync "<local-directory-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" --recursive` |
| **예제** | `azcopy sync "C:\myDirectory" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive` |

### <a name="update-a-local-file-system-with-changes-to-a-container"></a>컨테이너의 변경 내용으로 로컬 파일 시스템 업데이트

이 경우 로컬 파일 시스템은 대상이 고 컨테이너는 원본입니다.

|    |     |
|--------|-----------|
| **구문** | `azcopy sync "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" "C:\myDirectory" --recursive` |
| **예제** | `azcopy sync "https://mystorageaccount.blob.core.windows.net/mycontainer" "C:\myDirectory" --recursive` |
|

## <a name="next-steps"></a>다음 단계

다음 문서에서 더 많은 예제를 찾아보세요.

- [AzCopy 시작](storage-use-azcopy-v10.md)

- [자습서: AzCopy를 사용 하 여 클라우드 저장소로 온-프레미스 데이터 마이그레이션](storage-use-azcopy-migrate-on-premises-data.md)

- [AzCopy 및 파일 스토리지를 사용하여 데이터 전송](storage-use-azcopy-files.md)

- [AzCopy 및 Amazon S3 버킷을 사용하여 데이터 전송](storage-use-azcopy-s3.md)

- [AzCopy 구성, 최적화 및 문제 해결](storage-use-azcopy-configure.md)
