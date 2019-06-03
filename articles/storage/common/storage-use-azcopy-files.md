---
title: V10 AzCopy를 사용 하 여 Azure 파일에서 데이터 전송 | Microsoft Docs
description: AzCopy 및 file storage 사용 하 여 데이터를 전송 합니다.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 5d11d5c0da350b9abf2e2b06a11a1690cf3f6922
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66247111"
---
# <a name="transfer-data-with-azcopy-and-file-storage"></a>AzCopy 및 file storage 사용 하 여 데이터를 전송 합니다. 

AzCopy는 저장소 계정 간에서 blob 또는 파일을 복사 하는 데 사용할 수 있는 명령줄 유틸리티입니다. 이 문서에서는 Azure Files를 사용 하 여 작동 하는 예제 명령을 포함 합니다.

시작 하기 전에 참조를 [AzCopy를 사용 하 여 시작](storage-use-azcopy-v10.md) 문서에서는 AzCopy를 다운로드 및 도구를 숙지 해야 합니다.

## <a name="create-file-shares"></a>파일 공유 만들기

AzCopy를 사용할 수 있습니다 `make` 파일 공유를 만드는 명령입니다. 이 단원의 예제에서는 라는 파일 공유를 만듭니다 `myfileshare`합니다.

|    |     |
|--------|-----------|
| **구문** | `azcopy make "https://<storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>"` |
| **예제** | `azcopy make "https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

## <a name="upload-files"></a>파일 업로드

AzCopy를 사용할 수 있습니다 `copy` 명령을 로컬 컴퓨터에서 파일과 폴더를 업로드 합니다.

이 섹션에는 다음 예제가 포함되어 있습니다.

> [!div class="checklist"]
> * 파일 업로드
> * 폴더 업로드
> * 와일드 카드 문자를 사용 하 여 파일 업로드

> [!NOTE]
> 자동으로 AzCopy 계산 하 고 파일의 md5 해시 코드를 저장 하지 않습니다. 이렇게 하려면 AzCopy를 사용 하도록 하려는 경우 다음 추가 `--put-md5` 각 복사 명령 플래그입니다. 이런 방식으로 파일을 다운로드 하는 경우 AzCopy MD5 해시를 계산 데이터를 다운로드 하 고 파일의 저장 된 MD5 해시 확인에 대 한 `Content-md5` 속성 계산 된 해시와 일치 합니다.

### <a name="upload-a-file"></a>파일 업로드

|    |     |
|--------|-----------|
| **구문** | `azcopy cp "<local-file-path>" "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-name>?<SAS-token>"` |
| **예제** | `azcopy copy "C:\myFolder\myTextFile.txt" "https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

### <a name="upload-a-folder"></a>폴더 업로드

이 예제에서는 파일 공유에 폴더 (및 모든 해당 폴더의 파일)를 복사 합니다. 결과 같은 이름으로 파일 공유의 폴더입니다.

|    |     |
|--------|-----------|
| **구문** | `azcopy copy "<local-folder-path>" "https://<storage-account-name>.file.core.windows.net/<file-share-name>?<SAS-token>" --recursive` |
| **예제** | `azcopy copy "C:\myFolder" "https://mystorageaccount.file.core.windows.net/myfileshare?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" --recursive` |

파일 공유 내 폴더에 복사 하려면 방금 명령 문자열에 해당 폴더의 이름을 지정 합니다.

|    |     |
|--------|-----------|
| **예제** | `azcopy copy "C:\myFolder" "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareFolder?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" --recursive` |

존재 하지 않는 폴더의 이름에서 파일 공유를 지정 하면 AzCopy는 해당 이름의 새 폴더를 만듭니다.

### <a name="upload-the-contents-of-a-folder"></a>폴더의 콘텐츠 업로드

와일드 카드 기호 (*)를 사용 하 여 자체 포함 된 폴더를 복사 하지 않고 폴더의 콘텐츠를 업로드할 수 있습니다.

|    |     |
|--------|-----------|
| **구문** | `azcopy copy "<local-folder-path>/*" "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<folder-path>?<SAS-token>` |
| **예제** | `azcopy copy "C:\myFolder\*" "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareFolder?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D"` |

> [!NOTE]
> 추가 된 `--recursive` 모든 하위 폴더에 파일을 업로드 하는 플래그입니다.

## <a name="download-files"></a>파일 다운로드

AzCopy를 사용할 수 있습니다 `copy` 파일, 폴더 및 파일을 다운로드 하는 명령을 로컬 컴퓨터로 공유 합니다.

이 섹션에는 다음 예제가 포함되어 있습니다.

> [!div class="checklist"]
> * 파일 다운로드
> * 폴더를 다운로드 합니다.
> * 와일드 카드 문자를 사용 하 여 파일을 다운로드 합니다.

> [!NOTE]
> 경우는 `Content-md5` 해시를 포함 하는 파일의 속성 값, 다운로드 한 데이터에 대 한 MD5 해시를 계산 하 고 파일의 저장 된 MD5 해시를 확인 하는 AzCopy `Content-md5` 속성 계산 된 해시와 일치 합니다. 다운로드가 실패를 추가 하 여이 동작을 재정의 하지 않는 한 이러한 값이 일치 하지 않으면 `--check-md5=NoCheck` 또는 `--check-md5=LogOnly` 복사 명령입니다.

### <a name="download-a-file"></a>파일 다운로드

|    |     |
|--------|-----------|
| **구문** | `azcopy copy "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<file-path>?<SAS-token>" "<local-file-path>"` |
| **예제** | `azcopy copy "https://mystorageaccount.file.core.windows.net/myfileshare/myTextFile.txt?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" "C:\myFolder\myTextFile.txt"` |

### <a name="download-a-folder"></a>폴더를 다운로드 합니다.

|    |     |
|--------|-----------|
| **구문** | `azcopy copy "https://<storage-account-name>.file.core.windows.net/<file-share-name>/<folder-path>?<SAS-token>" "<local-folder-path>" --recursive` |
| **예제** | `azcopy copy "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareFolder?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" "C:\myFolder"  --recursive` |

이 예제에서는 라는 폴더 `C:\myFolder\myFileShareFolder` 다운로드 한 파일을 모두 포함 하는 합니다.

### <a name="download-the-contents-of-a-folder"></a>폴더의 내용을 다운로드합니다

와일드 카드 기호 (*)를 사용 하 여 자체 포함 된 폴더를 복사 하지 않고 폴더의 콘텐츠를 다운로드할 수 있습니다.

|    |     |
|--------|-----------|
| **구문** | `azcopy copy "https://<storage-account-name>.file.core.windows.net/<file-share-name>/*?<SAS-token>" "<local-folder-path>/"` |
| **예제** | `azcopy copy "https://mystorageaccount.file.core.windows.net/myfileshare/myFileShareFolder/*?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D" "C:\myFolder"` |

> [!NOTE]
> 추가 된 `--recursive` 모든 하위 폴더에 파일을 다운로드 하는 플래그입니다.

## <a name="next-steps"></a>다음 단계

이러한 문서 중 하나에서 더 많은 예제를 찾습니다.

- [AzCopy를 사용 하 여 시작](storage-use-azcopy-v10.md)

- [AzCopy 및 blob storage 사용 하 여 데이터를 전송 합니다.](storage-use-azcopy-blobs.md)

- [AzCopy 및 Amazon S3 버킷 사용 하 여 데이터를 전송 합니다.](storage-use-azcopy-s3.md)

- [구성, 최적화 및 AzCopy 문제 해결](storage-use-azcopy-configure.md)