---
title: V10 AzCopy를 사용 하 여 Amazon S3 버킷에서 Azure Storage로 데이터 전송 | Microsoft Docs
description: AzCopy 및 Amazon S3 버킷 사용 하 여 데이터를 전송 합니다.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 04/23/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: b18c4c039b615c7c88268b6e668df9f7fec9fabf
ms.sourcegitcommit: 6932af4f4222786476fdf62e1e0bf09295d723a1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/05/2019
ms.locfileid: "66687915"
---
# <a name="copy-data-from-amazon-s3-buckets-by-using-azcopy"></a>AzCopy를 사용 하 여 Amazon S3 버킷에서 데이터 복사

AzCopy는 저장소 계정 간에서 blob 또는 파일을 복사 하는 데 사용할 수 있는 명령줄 유틸리티입니다. 이 문서에서는 개체, 디렉터리 및 버킷 Amazon Web Services (AWS) S3에서 Azure blob storage로 복사 AzCopy를 사용 하 여 합니다.

## <a name="choose-how-youll-provide-authorization-credentials"></a>권한 부여 자격 증명 제공할 방법 선택

* Azure Storage를 인증 하려면 Azure Active Directory (AD) 또는 공유 액세스 서명 (SAS) 토큰을 사용 합니다.

* AWS S3를 인증 하려면 AWS 액세스 키 및 비밀 액세스 키를 사용 합니다.

### <a name="authorize-with-azure-storage"></a>Azure Storage를 사용 하 여 권한 부여

참조 된 [AzCopy를 사용 하 여 시작](storage-use-azcopy-v10.md) AzCopy를 다운로드 하려면 문서 및 권한 부여 자격 증명 저장소 서비스에 제공할 방법 선택 합니다.

> [!NOTE]
> 이 문서의 예제를 사용 하 여 본인 인증 하는 것으로 가정 합니다 `AzCopy login` 명령입니다. AzCopy는 Blob storage의 데이터에 대 한 액세스 권한을 부여 하려면 Azure AD 계정을 사용 합니다.
>
> Blob 데이터에 대 한 액세스 권한을 부여 하는 SAS 토큰 사용 하려는, 해당 토큰에 각 AzCopy 명령에서 리소스 URL을 추가할 수 있습니다.
>
> 예: `https://mystorageaccount.blob.core.windows.net/mycontainer?<SAS-token>`

### <a name="authorize-with-aws-s3"></a>AWS S3을 사용 하 여 권한 부여

비밀 액세스 키를 확인 하 고 AWS 액세스 키를 수집 하 고 설정한 이러한 환경 변수:

| 운영 체제 | 명령  |
|--------|-----------|
| **Windows** | `set AWS_ACCESS_KEY_ID=<access-key>`<br>`set AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **Linux** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **MacOS** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>`|

## <a name="copy-objects-directories-and-buckets"></a>개체 복사, 디렉터리 및 버킷

AzCopy를 사용 합니다 [URL에서 블록 배치](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) API, AWS S3 및 저장소 서버 간에 직접 데이터를 복사 하도록 합니다. 이러한 복사 작업에는 컴퓨터의 네트워크 대역폭 사용 하지 마세요.

### <a name="copy-an-object"></a>개체를 복사 합니다.

|    |     |
|--------|-----------|
| **구문** | `azcopy cp "https://s3.amazonaws.com/<bucket-name>/<object-name>" "https://<storage-account-name>.blob.core.windows.net/<container-name>/<blob-name>"` |
| **예제** | `azcopy cp "https://s3.amazonaws.com/mybucket/myobject" "https://mystorageaccount.blob.core.windows.net/mycontainer/myblob"` |

> [!NOTE]
> 이 문서의 예제에서는 AWS S3 버킷의 경로 스타일 Url 사용 (예: `http://s3.amazonaws.com/<bucket-name>`). 
>
> 가상 호스트 스타일 Url을 사용할 수도 있습니다 (예: `http://bucket.s3.amazonaws.com`). 
>
> 버킷 가상 호스팅에 대 한 자세한 내용은 [가상 호스팅의 버킷이 표시]] (https://docs.aws.amazon.com/AmazonS3/latest/dev/VirtualHosting.html)합니다.

### <a name="copy-a-directory"></a>디렉터리 복사

|    |     |
|--------|-----------|
| **구문** | `azcopy cp "https://s3.amazonaws.com/<bucket-name>/<directory-name>" "https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>" --recursive=true` |
| **예제** | `azcopy cp "https://s3.amazonaws.com/mybucket/mydirectory" "https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory" --recursive=true` |

### <a name="copy-a-bucket"></a>버킷의 복사

|    |     |
|--------|-----------|
| **구문** | `azcopy cp "https://s3.amazonaws.com/<bucket-name>" "https://<storage-account-name>.blob.core.windows.net/<container-name>" --recursive=true` |
| **예제** | `azcopy cp "https://s3.amazonaws.com/mybucket" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive=true` |

### <a name="copy-all-buckets-in-all-regions"></a>모든 지역에서 모든 버킷 복사

|    |     |
|--------|-----------|
| **구문** | `azcopy cp "https://s3.amazonaws.com/" "https://<storage-account-name>.blob.core.windows.net" --recursive=true` |
| **예제** | `azcopy cp "https://s3.amazonaws.com" "https://mystorageaccount.blob.core.windows.net" --recursive=true` |

### <a name="copy-all-buckets-in-a-specific-s3-region"></a>S3는 특정 지역의 모든 버킷 복사

|    |     |
|--------|-----------|
| **구문** | `azcopy cp "https://s3-<region-name>.amazonaws.com/" "https://<storage-account-name>.blob.core.windows.net" --recursive=true` |
| **예제** | `azcopy cp "https://s3-rds.eu-north-1.amazonaws.com" "https://mystorageaccount.blob.core.windows.net" --recursive=true` |

## <a name="handle-differences-in-object-naming-rules"></a>개체 명명 규칙의 차이 처리 합니다.

AWS S3 다양 한 Azure blob 컨테이너를 비교 하 여 버킷 이름에 대 한 명명 규칙에 있습니다. 에 대 한 읽을 수 있습니다 [여기](https://docs.aws.amazon.com/AmazonS3/latest/dev/BucketRestrictions.html#bucketnamingrules)합니다. Azure storage 계정에 버킷 그룹을 복사 하려는 경우 복사 작업 이름 지정에 차이가 인해 실패할 수 있습니다.

AzCopy; 발생할 수 있는 가장 일반적인 문제 중 두 개를 처리 합니다. 기간 및 연속 된 하이픈을 포함 하는 버킷으로 포함 하는 버킷입니다. AWS S3 버킷 이름에는 마침표 및 하이픈을 연속으로 포함 될 수 있지만 Azure에서 컨테이너 수 없습니다. AzCopy는 하이픈 및 하이픈을 연속으로 수를 나타내는 숫자를 사용 하 여 연속 된 하이픈을 사용 하 여 마침표를 대체 (예를 들어: 라는 버킷의 `my----bucket` 가 `my-4-bucket`합니다. 

또한 AzCopy 파일을 복사 하는 대로 이름이 충돌 하는 것에 대 한 확인 및 해결 하려고 합니다. 예를 들어, 버킷 이름이 없으면 `bucket-name` 및 `bucket.name`, AzCopy 확인 이라는 버킷의 `bucket.name` 첫 `bucket-name` 하 고 `bucket-name-2`.

## <a name="handle-differences-in-object-metadata"></a>개체 메타 데이터의 차이 처리 합니다.

Azure 및 AWS S3 개체 키의 이름에 서로 다른 문자 집합을 허용합니다. AWS S3는 문자에 대 한 읽을 수 있습니다 [여기](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingMetadata.html#object-keys)합니다. Azure 측에서 blob 개체 키에 대 한 명명 규칙을 따라야 [ C# 식별자](https://docs.microsoft.com/dotnet/csharp/language-reference/)합니다.

AzCopy의 일부로 `copy` 명령에 대 한 값을 제공할 수 있습니다 선택적는 `s2s-invalid-metadata-handle` 파일의 메타 데이터를 호환 되지 않는 키 이름을 포함 하는 위치는 파일을 처리 하는 방법을 지정 하는 플래그입니다. 다음 표에서 각 플래그 값을 설명 합니다.

| 플래그 값 | 설명  |
|--------|-----------|
| **ExcludeIfInvalid** | (기본 옵션) 메타 데이터 전송된 개체에 포함 되지 않습니다. AzCopy는 경고를 기록 합니다. |
| **FailIfInvalid** | 개체 복사 되지 않습니다. AzCopy는 오류를 기록 하 고 전송 요약에 표시 되는 실패 한 횟수에 해당 오류를 포함 합니다.  |
| **RenameIfInvalid**  | AzCopy는 잘못 된 메타 데이터 키를 확인 하 고 개체 확인 된 메타 데이터 키-값 쌍을 사용 하 여 Azure에 복사 합니다. AzCopy는 개체 키의 이름을 바꾸려면 위한 구체적인 단계에 대해 알아보려면 합니다 [어떻게 AzCopy 개체 키의 이름을 바꿉니다](#rename-logic) 섹션 아래. AzCopy를 키의 이름을 바꿀 수 없는 경우 개체 복사 되지 않습니다. |

<a id="rename-logic" />

### <a name="how-azcopy-renames-object-keys"></a>AzCopy 개체 키의 이름을 변경 하는 방법

AzCopy는 다음이 단계를 수행합니다.

1. 잘못 된 문자 '_'로 바꿉니다.

2. 문자열을 추가 `rename_` 새 유효한 키의 시작 부분에 있습니다.

   원래 메타 데이터를 저장 하려면이 키가 사용 됩니다 **값**합니다.

3. 문자열을 추가 `rename_key_` 새 유효한 키의 시작 부분에 있습니다.
   잘못 된 원래 메타 데이터를 저장 하려면이 키가 사용 됩니다 **키**합니다.
   메타 데이터 키가 Blob storage 서비스에 대 한 값으로 유지 되므로 Azure 측의 메타 데이터의 복구를 시도 하려면이 키를 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이러한 문서 중 하나에서 더 많은 예제를 찾습니다.

- [AzCopy 시작](storage-use-azcopy-v10.md)

- [AzCopy 및 Blob 스토리지를 사용하여 데이터 전송](storage-use-azcopy-blobs.md)

- [AzCopy 및 파일 스토리지를 사용하여 데이터 전송](storage-use-azcopy-files.md)

- [AzCopy 구성, 최적화 및 문제 해결](storage-use-azcopy-configure.md)