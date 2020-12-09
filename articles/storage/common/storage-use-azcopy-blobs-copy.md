---
title: AzCopy v10를 사용 하 여 Azure storage 계정 간 blob 복사 Microsoft Docs
description: 이 문서에는 저장소 계정 간에 blob을 복사 하는 데 도움이 되는 AzCopy 예제 명령의 컬렉션이 포함 되어 있습니다.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 12/08/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: f536e163e3d19d91c150506ab44fdd9cbc02c693
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/09/2020
ms.locfileid: "96907511"
---
# <a name="copy-blobs-between-azure-storage-accounts-by-using-azcopy-v10"></a>AzCopy v10를 사용 하 여 Azure storage 계정 간에 blob 복사

AzCopy v10 명령줄 유틸리티를 사용 하 여 저장소 계정 간에 blob, 디렉터리 및 컨테이너를 복사할 수 있습니다. 

파일 업로드, blob 다운로드, Blob storage와 동기화 등의 다른 작업 유형에 대 한 예제를 보려면이 문서의 [다음 단계](#next-steps) 섹션에 제공 된 링크를 참조 하세요.

AzCopy는 [서버](/rest/api/storageservices/put-block-from-url) 간 [api](/rest/api/storageservices/put-page-from-url)를 사용 하므로 저장소 서버 간에 데이터를 직접 복사 합니다. 이러한 복사 작업은 컴퓨터의 네트워크 대역폭을 사용 하지 않습니다.

AzCopy를 다운로드 하 고 저장소 서비스에 권한 부여 자격 증명을 제공할 수 있는 방법에 대해 알아보려면 [AzCopy 시작](storage-use-azcopy-v10.md)을 참조 하세요. 

## <a name="guidelines"></a>지침

AzCopy 명령에 다음 지침을 적용 합니다. 

- 각 원본 URL에 SAS 토큰을 추가 합니다. 

  Azure Active Directory (Azure AD)를 사용 하 여 권한 부여 자격 증명을 제공 하는 경우 대상 URL 에서만 SAS 토큰을 생략할 수 있습니다. 대상 계정에 적절 한 역할을 설정 했는지 확인 합니다. [옵션 1: Azure Active Directory 사용](storage-use-azcopy-v10.md?toc=/azure/storage/blobs/toc.json#option-1-use-azure-active-directory)을 참조 하세요. 

  이 문서의 예제에서는 Azure AD를 사용 하 여 id를 인증 했다고 가정 하므로 예제에서는 대상 URL에서 SAS 토큰을 생략 합니다.

-  프리미엄 블록 blob 저장소 계정에 복사 하는 경우를로 설정 하 여 복사 작업에서 blob의 액세스 계층을 생략 합니다 `s2s-preserve-access-tier` `false` (예: `--s2s-preserve-access-tier=false` ). 프리미엄 블록 blob 저장소 계정은 액세스 계층을 지원 하지 않습니다. 

- 계층적 네임 스페이스를 포함 하는 계정에서 복사 하는 경우 `blob.core.windows.net` `dfs.core.windows.net` URL 구문에서 대신를 사용 합니다. [Data Lake Storage에 대 한 다중 프로토콜 액세스](../blobs/data-lake-storage-multi-protocol-access.md) 를 사용 하 여 계정 `blob.core.windows.net` 복사 시나리오에 대 한 계정에 지원 되는 유일한 구문입니다. 

- 환경 변수의 값을 설정 하 여 복사 작업의 처리량을 늘릴 수 있습니다 `AZCOPY_CONCURRENCY_VALUE` . 자세히 알아보려면 [처리량 최적화](storage-use-azcopy-configure.md#optimize-throughput)를 참조 하세요. 

- 원본 blob에 인덱스 태그가 있는 경우 해당 태그를 유지 하려면 대상 blob에 다시 적용 해야 합니다. 인덱스 태그를 설정 하는 방법에 대 한 자세한 내용은이 문서의 [인덱스 태그를 사용 하 여 다른 저장소 계정에 Blob 복사](#copy-between-accounts-and-add-index-tags) 섹션을 참조 하세요.

## <a name="copy-a-blob"></a>Blob 복사

[Azcopy copy](storage-ref-azcopy-copy.md) 명령을 사용 하 여 다른 저장소 계정에 blob을 복사 합니다. 

> [!TIP]
> 이 예에서는 경로 인수를 작은따옴표 (' ')로 묶습니다. Windows 명령 셸 (cmd.exe)을 제외 하 고 모든 명령 셸에서 작은따옴표를 사용 합니다. cmd.exe (Windows 명령 셸)을 사용 하는 경우 작은따옴표 (' ') 대신 경로 인수를 큰따옴표 ("")로 묶습니다.

|    |     |
|--------|-----------|
| **구문** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>'` |
| **예제** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |

복사 작업은 동기식이므로 명령이 반환될 때 모든 파일이 복사되었음을 나타냅니다. 

## <a name="copy-a-directory"></a>디렉터리 복사

[Azcopy copy](storage-ref-azcopy-copy.md) 명령을 사용 하 여 디렉터리를 다른 저장소 계정에 복사 합니다. 

> [!TIP]
> 이 예에서는 경로 인수를 작은따옴표 (' ')로 묶습니다. Windows 명령 셸 (cmd.exe)을 제외 하 고 모든 명령 셸에서 작은따옴표를 사용 합니다. cmd.exe (Windows 명령 셸)을 사용 하는 경우 작은따옴표 (' ') 대신 경로 인수를 큰따옴표 ("")로 묶습니다.

|    |     |
|--------|-----------|
| **구문** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **예제** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

복사 작업은 동기식이므로 명령이 반환될 때 모든 파일이 복사되었음을 나타냅니다.

## <a name="copy-a-container"></a>컨테이너 복사

[Azcopy copy](storage-ref-azcopy-copy.md) 명령을 사용 하 여 컨테이너를 다른 저장소 계정에 복사 합니다.

> [!TIP]
> 이 예에서는 경로 인수를 작은따옴표 (' ')로 묶습니다. Windows 명령 셸 (cmd.exe)을 제외 하 고 모든 명령 셸에서 작은따옴표를 사용 합니다. cmd.exe (Windows 명령 셸)을 사용 하는 경우 작은따옴표 (' ') 대신 경로 인수를 큰따옴표 ("")로 묶습니다.

|    |     |
|--------|-----------|
| **구문** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **예제** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

복사 작업은 동기식이므로 명령이 반환될 때 모든 파일이 복사되었음을 나타냅니다.

## <a name="copy-containers-directories-and-blobs"></a>컨테이너, 디렉터리 및 blob 복사

[Azcopy copy](storage-ref-azcopy-copy.md) 명령을 사용 하 여 모든 컨테이너, 디렉터리 및 blob을 다른 저장소 계정에 복사 합니다.

> [!TIP]
> 이 예에서는 경로 인수를 작은따옴표 (' ')로 묶습니다. Windows 명령 셸 (cmd.exe)을 제외 하 고 모든 명령 셸에서 작은따옴표를 사용 합니다. cmd.exe (Windows 명령 셸)을 사용 하는 경우 작은따옴표 (' ') 대신 경로 인수를 큰따옴표 ("")로 묶습니다.

|    |     |
|--------|-----------|
| **구문** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/' --recursive` |
| **예제** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive` |

복사 작업은 동기식이므로 명령이 반환될 때 모든 파일이 복사되었음을 나타냅니다.

<a id="copy-between-accounts-and-add-index-tags"></a>

## <a name="copy-blobs-and-add-index-tags"></a>Blob 복사 및 인덱스 태그 추가

Blob을 다른 저장소 계정에 복사 하 고 [blob 인덱스 태그 (미리 보기)](../blobs/storage-manage-find-blobs.md) 를 대상 blob에 추가 합니다.

Azure AD 인증을 사용 하는 경우 보안 주체에 게 [저장소 Blob 데이터 소유자](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) 역할을 할당 하거나 `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` 사용자 지정 azure 역할을 통해 [azure 리소스 공급자 작업](../../role-based-access-control/resource-provider-operations.md#microsoftstorage) 에 대 한 권한을 부여 해야 합니다. SAS (공유 액세스 서명) 토큰을 사용 하는 경우 해당 토큰은 sas 권한을 통해 blob의 태그에 대 한 액세스를 제공 해야 합니다 `t` .

태그를 추가 하려면 `--blob-tags` URL 인코딩 키-값 쌍과 함께 옵션을 사용 합니다. 

예를 들어 키 및 값을 추가 하려면 `my tag` `my tag value` `--blob-tags='my%20tag=my%20tag%20value'` 대상 매개 변수에를 추가 합니다. 

앰퍼샌드 ()를 사용 하 여 여러 인덱스 태그를 구분 `&` 합니다.  예를 들어 키와 값을 추가 하려는 경우 `my second tag` `my second tag value` 전체 옵션 문자열은 `--blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` 입니다.

다음 예에서는 옵션을 사용 하는 방법을 보여 줍니다 `--blob-tags` .

> [!TIP]
> 이 예에서는 경로 인수를 작은따옴표 (' ')로 묶습니다. Windows 명령 셸 (cmd.exe)을 제외 하 고 모든 명령 셸에서 작은따옴표를 사용 합니다. cmd.exe (Windows 명령 셸)을 사용 하는 경우 작은따옴표 (' ') 대신 경로 인수를 큰따옴표 ("")로 묶습니다.

|    |     |
|--------|-----------|
| **Blob** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt' --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` |
| **디렉터리** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive --blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` |
| **컨테이너** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive --blob-tags="--blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` |
| **계정** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive --blob-tags="--blob-tags='my%20tag=my%20tag%20value&my%20second%20tag=my%20second%20tag%20value'` |

복사 작업은 동기식이므로 명령이 반환될 때 모든 파일이 복사되었음을 나타냅니다.

> [!NOTE]
> 원본에 대 한 디렉터리, 컨테이너 또는 계정을 지정 하는 경우 대상에 복사 된 모든 blob에는 명령에서 지정 하는 것과 동일한 태그가 지정 됩니다.

## <a name="copy-with-optional-flags"></a>선택적 플래그를 사용 하 여 복사

선택적 플래그를 사용 하 여 복사 작업을 조정할 수 있습니다. 다음은 몇 가지 예입니다.

|시나리오|플래그|
|---|---|
|블록, 페이지 또는 추가 Blob으로 blob을 복사 합니다.|**--blob-형식** = \[ BlockBlob \| pageblob \| appendblob\]|
|특정 액세스 계층 (예: 보관 계층)에 복사 합니다.|**--블록-blob 계층** = \[ 없음 \| 핫 \| 쿨 \| 아카이브\]|
|자동으로 파일의 압축을 해제 합니다.|**--압축 풀기** = \[ gzip \| deflate\]|

전체 목록은 [options](storage-ref-azcopy-copy.md#options)를 참조 하세요. 

## <a name="next-steps"></a>다음 단계

다음 문서에서 더 많은 예제를 찾아보세요.

- [예: 업로드](storage-use-azcopy-blobs-upload.md)
- [예: 다운로드](storage-use-azcopy-blobs-download.md)
- [예: 동기화](storage-use-azcopy-blobs-synchronize.md)
- [예: Amazon S3 버킷](storage-use-azcopy-s3.md)
- [예: Azure Files](storage-use-azcopy-files.md)
- [자습서: AzCopy를 사용하여 클라우드로 온-프레미스 데이터 마이그레이션](storage-use-azcopy-migrate-on-premises-data.md)
- [AzCopy 구성, 최적화 및 문제 해결](storage-use-azcopy-configure.md)