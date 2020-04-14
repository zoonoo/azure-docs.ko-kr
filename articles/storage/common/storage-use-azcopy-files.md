---
title: AzCopy v10 | 사용하여 Azure 파일로 또는 Azure 파일에서 데이터를 전송합니다. 마이크로 소프트 문서
description: AzCopy 및 파일 저장소로 데이터를 전송합니다.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 59f5733009424c60f2b9c48e68d70bbc29ad7095
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81263372"
---
# <a name="transfer-data-with-azcopy-and-file-storage"></a>AzCopy 및 파일 스토리지를 사용하여 데이터 전송 

AzCopy는 저장소 계정에서 Blob 또는 파일을 복사하는 데 사용할 수 있는 명령줄 유틸리티입니다. 이 문서에는 Azure Files에서 작동하는 예제 명령이 포함되어 있습니다.

시작하기 전에 [AzCopy](storage-use-azcopy-v10.md) 시작하기 문서를 참조하여 AzCopy를 다운로드하고 도구에 익숙해하십시오.

> [!TIP]
> 이 문서의 예제에서는 경로 인수를 따옴표('')로 둘러싸습니다. Windows 명령 셸(cmd.exe)을 제외한 모든 명령 셸에 단일 따옴표를 사용합니다. Windows 명령 셸(cmd.exe)을 사용하는 경우 경로 인수를 단일 따옴표('')가 아닌 큰따옴표(")로 동봉합니다.

## <a name="create-file-shares"></a>파일 공유 만들기

[azcopy make](storage-ref-azcopy-make.md) 명령을 사용하여 파일 공유를 만들 수 있습니다. 이 섹션의 예제에서는 . `myfileshare`

|    |     |
|--------|-----------|
| **구문** | `azcopy make 'https://<storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>'` |
| **예제** | `azcopy make 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

자세한 참조 문서는 [azcopy 확인을](storage-ref-azcopy-make.md)참조하십시오.

## <a name="upload-files"></a>파일 업로드

[azcopy 복사](storage-ref-azcopy-copy.md) 명령을 사용하여 로컬 컴퓨터에서 파일 및 디렉터리를 업로드할 수 있습니다.

이 섹션에는 다음 예제가 포함되어 있습니다.

> [!div class="checklist"]
> * 파일 업로드
> * 디렉토리 업로드
> * 디렉터리 내용 업로드
> * 특정 파일 업로드

> [!TIP]
> 선택적 플래그를 사용하여 업로드 작업을 조정할 수 있습니다. 다음은 몇 가지 예입니다.
>
> |시나리오|플래그|
> |---|---|
> |파일과 함께 ACL(액세스 제어 목록)을 복사합니다.|**--보존 -smb-권한**=\[\|참 거짓\]|
> |파일과 함께 SMB 속성 정보를 복사합니다.|**--보존-smb-정보**=\[\|진정한 거짓\]|
> |파일을 Blob 또는 페이지 Blob을 부백으로 업로드합니다.|**--blob 형**=\[블록블랍\|페이지Blob\|부록블블블\]|
> |특정 액세스 계층(예: 아카이브 계층)에 업로드합니다.|**--블록 -blob**=\[계층\|\|없음\|핫 쿨 아카이브\]|
> 
> 전체 목록은 [옵션을](storage-ref-azcopy-copy.md#options)참조하십시오.

> [!NOTE]
> AzCopy는 파일의 md5 해시 코드를 자동으로 계산하고 저장하지 않습니다. AzCopy를 수행하려면 각 복사 명령에 `--put-md5` 플래그를 더합니다. 이렇게 하면 파일을 다운로드할 때 AzCopy는 다운로드한 데이터에 대한 MD5 해시를 계산하고 파일의 `Content-md5` 속성에 저장된 MD5 해시가 계산된 해시와 일치하는지 확인합니다.

### <a name="upload-a-file"></a>파일 업로드

|    |     |
|--------|-----------|
| **구문** | `azcopy copy '<local-file-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-name><SAS-token>'` |
| **예제** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

파일 경로 또는 파일 이름의 아무 곳이나 와일드카드 기호(*)를 사용하여 파일을 업로드할 수도 있습니다. 예를 들어: `'C:\myDirectory\*.txt'` `C:\my*\*.txt`또는 .

### <a name="upload-a-directory"></a>디렉토리 업로드

이 예제에서는 디렉터리(및 해당 디렉터리내의 모든 파일)를 파일 공유에 복사합니다. 결과는 동일한 이름의 파일 공유의 디렉토리입니다.

|    |     |
|--------|-----------|
| **구문** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **예제** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' --recursive` |

파일 공유 내의 디렉터리로 복사하려면 명령 문자열에서 해당 디렉터리 이름을 지정하기만 하면 됩니다.

|    |     |
|--------|-----------|
| **예제** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' --recursive` |

파일 공유에 없는 디렉터리 이름을 지정하면 AzCopy는 해당 이름으로 새 디렉터리를 만듭니다.

### <a name="upload-the-contents-of-a-directory"></a>디렉터리 내용 업로드

와일드카드 기호(*)를 사용하여 포함된 디렉터리 자체를 복사하지 않고 디렉터리 내용을 업로드할 수 있습니다.

|    |     |
|--------|-----------|
| **구문** | `azcopy copy '<local-directory-path>/*' 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path><SAS-token>` |
| **예제** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

> [!NOTE]
> 플래그를 `--recursive` 부호를 지정하여 모든 하위 디렉터리에서 파일을 업로드합니다.

### <a name="upload-specific-files"></a>특정 파일 업로드

전체 파일 이름을 지정하거나 와일드카드 문자(*)가 있는 부분 이름을 사용할 수 있습니다.

#### <a name="specify-multiple-complete-file-names"></a>여러 개의 전체 파일 이름 지정

옵션과 함께 [azcopy 복사](storage-ref-azcopy-copy.md) 명령을 사용합니다. `--include-path` 세미콜론()을`;`사용하여 개별 파일 이름을 분리합니다.

|    |     |
|--------|-----------|
| **구문** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' --include-path <semicolon-separated-file-list>` |
| **예제** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-path 'photos;documents\myFile.txt'` |

이 예제에서 AzCopy는 `C:\myDirectory\photos` 디렉터리와 `C:\myDirectory\documents\myFile.txt` 파일을 전송합니다. `C:\myDirectory\photos` 디렉터리에서 `--recursive` 모든 파일을 전송하는 옵션을 포함해야 합니다.

`--exclude-path` 이 옵션을 사용하여 파일을 제외할 수도 있습니다. 자세한 내용은 [azcopy 복사](storage-ref-azcopy-copy.md) 참조 문서를 참조하십시오.

#### <a name="use-wildcard-characters"></a>와일드카드 문자 사용

옵션과 함께 [azcopy 복사](storage-ref-azcopy-copy.md) 명령을 사용합니다. `--include-pattern` 와일드카드 문자를 포함하는 부분 이름을 지정합니다. 세미 콜린 ()를`;`사용 하 여 이름을 분리 합니다.

|    |     |
|--------|-----------|
| **구문** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **예제** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --include-pattern 'myFile*.txt;*.pdf*'` |

`--exclude-pattern` 이 옵션을 사용하여 파일을 제외할 수도 있습니다. 자세한 내용은 [azcopy 복사](storage-ref-azcopy-copy.md) 참조 문서를 참조하십시오.

`--include-pattern` 및 `--exclude-pattern` 옵션은 파일 이름에만 적용되며 경로에는 적용되지 않습니다.  디렉터리 트리에 있는 모든 텍스트 파일을 복사하려면 이 `–recursive` 옵션을 사용하여 전체 디렉터리 트리를 가져옵니다. `–include-pattern` `*.txt`

## <a name="download-files"></a>파일 다운로드

[azcopy 복사](storage-ref-azcopy-copy.md) 명령을 사용하여 파일, 디렉터리 및 파일 공유를 로컬 컴퓨터에 다운로드할 수 있습니다.

이 섹션에는 다음 예제가 포함되어 있습니다.

> [!div class="checklist"]
> * 파일 다운로드
> * 디렉토리 다운로드
> * 디렉토리의 내용 다운로드
> * 특정 파일 다운로드

> [!TIP]
> 선택적 플래그를 사용하여 다운로드 작업을 조정할 수 있습니다. 다음은 몇 가지 예입니다.
>
> |시나리오|플래그|
> |---|---|
> |파일과 함께 ACL(액세스 제어 목록)을 복사합니다.|**--보존 -smb-권한**=\[\|참 거짓\]|
> |파일과 함께 SMB 속성 정보를 복사합니다.|**--보존-smb-정보**=\[\|진정한 거짓\]|
> |파일의 압축을 자동으로 해제합니다.|**--압축 풀기**=\[gzip\|deflate\]|
> 
> 전체 목록은 [옵션을](storage-ref-azcopy-copy.md#options)참조하십시오.

> [!NOTE]
> 파일의 `Content-md5` 속성 값에 해시가 포함된 경우 AzCopy는 다운로드한 데이터에 대해 MD5 해시를 계산하고 파일의 `Content-md5` 속성에 저장된 MD5 해시가 계산된 해시와 일치하는지 확인합니다. 이러한 값이 일치하지 않으면 복사 명령을 추가하거나 `--check-md5=NoCheck` `--check-md5=LogOnly` 이 동작을 재정의하지 않으면 다운로드가 실패합니다.

### <a name="download-a-file"></a>파일 다운로드

|    |     |
|--------|-----------|
| **구문** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-path><SAS-token>' '<local-file-path>'` |
| **예제** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory\myTextFile.txt'` |

### <a name="download-a-directory"></a>디렉토리 다운로드

|    |     |
|--------|-----------|
| **구문** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path><SAS-token>' '<local-directory-path>' --recursive` |
| **예제** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory'  --recursive` |

이 예제에서는 다운로드한 `C:\myDirectory\myFileShareDirectory` 모든 파일을 포함하는 명명된 디렉터리가 생성됩니다.

### <a name="download-the-contents-of-a-directory"></a>디렉토리의 내용 다운로드

와일드카드 기호(*)를 사용하여 포함된 디렉터리 자체를 복사하지 않고 디렉터리 내용을 다운로드할 수 있습니다.

|    |     |
|--------|-----------|
| **구문** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-name>/*<SAS-token>' '<local-directory-path>/'` |
| **예제** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareDirectory/*?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D' 'C:\myDirectory'` |

> [!NOTE]
> 플래그를 `--recursive` 추가하여 모든 하위 디렉터리에서 파일을 다운로드합니다.

### <a name="download-specific-files"></a>특정 파일 다운로드

전체 파일 이름을 지정하거나 와일드카드 문자(*)가 있는 부분 이름을 사용할 수 있습니다.

#### <a name="specify-multiple-complete-file-names"></a>여러 개의 전체 파일 이름 지정

옵션과 함께 [azcopy 복사](storage-ref-azcopy-copy.md) 명령을 사용합니다. `--include-path` 세미콜린()을`;`사용하여 개별 파일 이름을 분리합니다.

|    |     |
|--------|-----------|
| **구문** | `azcopy copy 'https://<storage-account-name>.file.core.windows.net/<file-share-or-directory-name><SAS-token>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>` |
| **예제** | `azcopy copy 'https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive` |

이 예제에서 AzCopy는 `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/photos` 디렉터리와 `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/documents/myFile.txt` 파일을 전송합니다. `https://mystorageaccount.file.core.windows.net/myFileShare/myDirectory/photos` 디렉터리에서 `--recursive` 모든 파일을 전송하는 옵션을 포함해야 합니다.

`--exclude-path` 이 옵션을 사용하여 파일을 제외할 수도 있습니다. 자세한 내용은 [azcopy 복사](storage-ref-azcopy-copy.md) 참조 문서를 참조하십시오.

#### <a name="use-wildcard-characters"></a>와일드카드 문자 사용

옵션과 함께 [azcopy 복사](storage-ref-azcopy-copy.md) 명령을 사용합니다. `--include-pattern` 와일드카드 문자를 포함하는 부분 이름을 지정합니다. 세미 콜린 ()를`;`사용 하 여 이름을 분리 합니다.

|    |     |
|--------|-----------|
| **구문** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name><SAS-token>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **예제** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |

`--exclude-pattern` 이 옵션을 사용하여 파일을 제외할 수도 있습니다. 자세한 내용은 [azcopy 복사](storage-ref-azcopy-copy.md) 참조 문서를 참조하십시오.

`--include-pattern` 및 `--exclude-pattern` 옵션은 파일 이름에만 적용되며 경로에는 적용되지 않습니다.  디렉터리 트리에 있는 모든 텍스트 파일을 복사하려면 이 `–recursive` 옵션을 사용하여 전체 디렉터리 트리를 가져옵니다. `–include-pattern` `*.txt`

## <a name="copy-files-between-storage-accounts"></a>스토리지 계정 간에 파일 복사

AzCopy를 사용하여 파일을 다른 저장소 계정에 복사할 수 있습니다. 복사 작업은 동기식이므로 명령이 반환될 때 모든 파일이 복사되었음을 나타냅니다.

AzCopy는 [서버 간](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) [API를](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url)사용하므로 데이터가 저장소 서버 간에 직접 복사됩니다. 이러한 복사 작업은 컴퓨터의 네트워크 대역폭을 사용하지 않습니다. `AZCOPY_CONCURRENCY_VALUE` 환경 변수의 값을 설정하여 이러한 작업의 처리량을 늘릴 수 있습니다. 자세한 내용은 [처리량 최적화를](storage-use-azcopy-configure.md#optimize-throughput)참조하십시오.

이 섹션에는 다음 예제가 포함되어 있습니다.

> [!div class="checklist"]
> * 파일을 다른 저장소 계정으로 복사
> * 디렉터리를 다른 저장소 계정으로 복사
> * 파일 공유를 다른 저장소 계정으로 복사
> * 모든 파일 공유, 디렉터리 및 파일을 다른 저장소 계정으로 복사

> [!TIP]
> 선택적 플래그를 사용하여 복사 작업을 조정할 수 있습니다. 다음은 몇 가지 예입니다.
>
> |시나리오|플래그|
> |---|---|
> |파일과 함께 ACL(액세스 제어 목록)을 복사합니다.|**--보존 -smb-권한**=\[\|참 거짓\]|
> |파일과 함께 SMB 속성 정보를 복사합니다.|**--보존-smb-정보**=\[\|진정한 거짓\]|
> |파일을 부백 Blob 또는 페이지 Blob로 복사합니다.|**--blob 형**=\[블록블랍\|페이지Blob\|부록블블블\]|
> |특정 액세스 계층(예: 아카이브 계층)에 복사합니다.|**--블록 -blob**=\[계층\|\|없음\|핫 쿨 아카이브\]|
> 
> 전체 목록은 [옵션을](storage-ref-azcopy-copy.md#options)참조하십시오.

### <a name="copy-a-file-to-another-storage-account"></a>파일을 다른 저장소 계정으로 복사

|    |     |
|--------|-----------|
| **구문** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<file-path><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>/<file-path><SAS-token>'` |
| **예제** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D'` |

### <a name="copy-a-directory-to-another-storage-account"></a>디렉터리를 다른 저장소 계정으로 복사

|    |     |
|--------|-----------|
| **구문** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-path><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **예제** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="copy-a-file-share-to-another-storage-account"></a>파일 공유를 다른 저장소 계정으로 복사

|    |     |
|--------|-----------|
| **구문** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **예제** | `azcopy copy 'https://mysourceaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="copy-all-file-shares-directories-and-files-to-another-storage-account"></a>모든 파일 공유, 디렉터리 및 파일을 다른 저장소 계정으로 복사

|    |     |
|--------|-----------|
| **구문** | `azcopy copy 'https://<source-storage-account-name>.file.core.windows.net/<SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<SAS-token>' --recursive'` |
| **예제** | `azcopy copy 'https://mysourceaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

## <a name="synchronize-files"></a>파일 동기화

파일 공유의 내용을 다른 파일 공유와 동기화할 수 있습니다. 파일 공유에서 디렉터리 내용을 다른 파일 공유에 있는 디렉터리 내용과 동기화할 수도 있습니다. 동기화는 단방향입니다. 즉, 이러한 두 끝점 중 소스와 대상을 선택할 수 있습니다. 동기화는 서버에서 서버 API에도 사용합니다.

> [!NOTE]
> 현재 이 시나리오는 계층적 네임스페이스가 없는 계정에 대해서만 지원됩니다. AzCopy의 현재 릴리스Azure 파일 및 Blob 저장소 간에 동기화 되지 않습니다.

[동기화](storage-ref-azcopy-sync.md) 명령은 파일 이름과 마지막으로 수정된 타임스탬프를 비교합니다. 선택적 `--delete-destination` 플래그를 소스 디렉터리에 더 이상 존재하지 않는 경우 대상 디렉터리에서 파일을 `true` 삭제하거나 `prompt` 값으로 설정합니다.

플래그를 `--delete-destination` AzCopy로 `true` 설정하면 프롬프트를 제공하지 않고 파일을 삭제합니다. AzCopy가 파일을 삭제하기 전에 프롬프트가 표시되도록 하려면 플래그를 `--delete-destination` 로 설정합니다. `prompt`

> [!TIP]
> 선택적 플래그를 사용하여 동기화 작업을 조정할 수 있습니다. 다음은 몇 가지 예입니다.
>
> |시나리오|플래그|
> |---|---|
> |다운로드 할 때 MD5 해시의 유효성을 엄격하게 검사하는 방법을 지정합니다.|**--체크-md5**=\[NoCheck\|\|로그만\|실패다른 실패Ifdifferentor 누락\]|
> |패턴에 따라 파일을 제외합니다.|**--제외 경로**|
> |동기화 관련 로그 항목을 세부항목으로 지정합니다.|**--로그**=\[수준\|\|경고\|오류 정보 없음\]|
> 
> 전체 목록은 [옵션을](storage-ref-azcopy-sync.md#options)참조하십시오.

### <a name="update-a-file-share-with-changes-to-another-file-share"></a>다른 파일 공유를 변경하여 파일 공유 업데이트

이 명령에 나타나는 첫 번째 파일 공유가 소스입니다. 두 번째는 대상입니다.

|    |     |
|--------|-----------|
| **구문** | `azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **예제** | `azcopy sync 'https://mysourceaccount.file.core.windows.net/myfileShare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="update-a-directory-with-changes-to-a-directory-in-another-file-share"></a>다른 파일 공유의 디렉터리 변경 내용으로 디렉터리 업데이트

이 명령에 나타나는 첫 번째 디렉터리가 소스입니다. 두 번째는 대상입니다.

|    |     |
|--------|-----------|
| **구문** | `azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-name><SAS-token>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name>/<directory-name><SAS-token>' --recursive` |
| **예제** | `azcopy sync 'https://mysourceaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.file.core.windows.net/myFileShare/myDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

### <a name="update-a-file-share-to-match-the-contents-of-a-share-snapshot"></a>공유 스냅숏의 내용과 일치하도록 파일 공유 업데이트

이 명령에 나타나는 첫 번째 파일 공유가 소스입니다. URI가 끝나면 문자열다음에 `&sharesnapshot=` 스냅숏의 **DateTime** 값을 더합니다. 

|    |     |
|--------|-----------|
| **구문** | `azcopy sync 'https://<source-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>&sharesnapsot<snapshot-ID>' 'https://<destination-storage-account-name>.file.core.windows.net/<file-share-name><SAS-token>' --recursive` |
| **예제** | `azcopy sync 'https://mysourceaccount.file.core.windows.net/myfileShare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D&sharesnapshot=2020-03-03T20%3A24%3A13.0000000Z' 'https://mydestinationaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' --recursive` |

공유 스냅숏에 대한 자세한 내용은 [Azure Files에 대한 공유 스냅숏 개요를](https://docs.microsoft.com/azure/storage/files/storage-snapshots-files)참조하십시오.

## <a name="next-steps"></a>다음 단계

다음 문서에서 더 많은 예제를 찾아보십시오.

- [AzCopy 시작](storage-use-azcopy-v10.md)

- [AzCopy 및 Blob 스토리지를 사용하여 데이터 전송](storage-use-azcopy-blobs.md)

- [AzCopy 및 Amazon S3 버킷을 사용하여 데이터 전송](storage-use-azcopy-s3.md)

- [AzCopy 구성, 최적화 및 문제 해결](storage-use-azcopy-configure.md)
