---
title: AzCopy v10를 사용 하 여 Amazon S3 버킷의 Azure Storage로 데이터 전송 Microsoft Docs
description: AzCopy 및 Amazon S3 버킷을 사용 하 여 데이터 전송
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: b984d194c75924451a52250490b1a5590b996974
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72821388"
---
# <a name="copy-data-from-amazon-s3-buckets-by-using-azcopy"></a>AzCopy를 사용 하 여 Amazon S3 버킷의 데이터 복사

AzCopy는 blob 또는 파일을 저장소 계정에 복사 하는 데 사용할 수 있는 명령줄 유틸리티입니다. 이 문서는 AzCopy를 사용 하 여 Amazon Web Services (AWS) s 3에서 Azure blob storage로 개체, 디렉터리 및 버킷을 복사 하는 데 도움이 됩니다.

## <a name="choose-how-youll-provide-authorization-credentials"></a>권한 부여 자격 증명을 제공 하는 방법을 선택 합니다.

* Azure Storage를 사용 하 여 권한을 부여 하려면 Azure Active Directory (AD) 또는 SAS (공유 액세스 서명) 토큰을 사용 합니다.

* AWS S3에 권한을 부여 하려면 AWS 액세스 키와 비밀 액세스 키를 사용 합니다.

### <a name="authorize-with-azure-storage"></a>Azure Storage 권한 부여

AzCopy를 다운로드 하려면 [AzCopy 시작](storage-use-azcopy-v10.md) 문서를 참조 하 고, 저장소 서비스에 권한 부여 자격 증명을 제공 하는 방법을 선택 하세요.

> [!NOTE]
> 이 문서의 예제에서는 `AzCopy login` 명령을 사용 하 여 id를 인증 했다고 가정 합니다. AzCopy는 Azure AD 계정을 사용 하 여 Blob storage의 데이터에 대 한 액세스 권한을 부여 합니다.
>
> 대신 SAS 토큰을 사용 하 여 blob 데이터에 대 한 액세스 권한을 부여 하는 경우 각 AzCopy 명령의 리소스 URL에 해당 토큰을 추가할 수 있습니다.
>
> 예: `https://mystorageaccount.blob.core.windows.net/mycontainer?<SAS-token>`.

### <a name="authorize-with-aws-s3"></a>AWS S3 인증

AWS 액세스 키 및 비밀 액세스 키를 수집 하 고 다음 환경 변수를 설정 합니다.

| 운영 체제 | 명령  |
|--------|-----------|
| **Windows** | `set AWS_ACCESS_KEY_ID=<access-key>`<br>`set AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **Linux** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **MacOS** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>`|

## <a name="copy-objects-directories-and-buckets"></a>개체, 디렉터리 및 버킷 복사

AzCopy는 [URL API에서 Put 블록](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) 을 사용 하므로 AWS S3 및 storage 서버 간에 데이터를 직접 복사 합니다. 이러한 복사 작업은 컴퓨터의 네트워크 대역폭을 사용 하지 않습니다.

> [!IMPORTANT]
> 이 기능은 현재 미리 보기로 제공됩니다. 복사 작업 후 S3 버킷의 데이터를 제거 하려는 경우 데이터를 제거 하기 전에 데이터를 저장소 계정에 올바르게 복사 했는지 확인 해야 합니다.

> [!TIP]
> 이 단원의 예제에서는 경로 인수를 작은따옴표 (' ')로 묶습니다. Windows 명령 셸 (cmd.exe)을 제외한 모든 명령 셸에서 작은따옴표를 사용 합니다. Windows 명령 셸 (cmd.exe)을 사용 하는 경우 작은따옴표 (' ') 대신 경로 인수를 큰따옴표 ("")로 묶습니다.

### <a name="copy-an-object"></a>개체 복사

|    |     |
|--------|-----------|
| **구문** | `azcopy copy 'https://s3.amazonaws.com/<bucket-name>/<object-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<blob-name>'` |
| **예제** | `azcopy copy 'https://s3.amazonaws.com/mybucket/myobject' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myblob'` |

> [!NOTE]
> 이 문서의 예제에서는 AWS S3 버킷에 대 한 경로 스타일 Url을 사용 합니다 (예: `http://s3.amazonaws.com/<bucket-name>`). 
>
> 가상 호스트 스타일 Url도 사용할 수 있습니다 (예: `http://bucket.s3.amazonaws.com`). 
>
> 버킷에 대 한 가상 호스팅에 대해 자세히 알아보려면 [버킷 가상 호스팅]] (https://docs.aws.amazon.com/AmazonS3/latest/dev/VirtualHosting.html) 를 참조 하세요.

### <a name="copy-a-directory"></a>디렉터리 복사

|    |     |
|--------|-----------|
| **구문** | `azcopy copy 'https://s3.amazonaws.com/<bucket-name>/<directory-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive=true` |
| **예제** | `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

### <a name="copy-a-bucket"></a>버킷 복사

|    |     |
|--------|-----------|
| **구문** | `azcopy copy 'https://s3.amazonaws.com/<bucket-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>' --recursive=true` |
| **예제** | `azcopy copy 'https://s3.amazonaws.com/mybucket' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive=true` |

### <a name="copy-all-buckets-in-all-regions"></a>모든 지역에서 모든 버킷 복사

|    |     |
|--------|-----------|
| **구문** | `azcopy copy 'https://s3.amazonaws.com/' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true` |
| **예제** | `azcopy copy 'https://s3.amazonaws.com' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |

### <a name="copy-all-buckets-in-a-specific-s3-region"></a>특정 S3 지역의 모든 버킷 복사

|    |     |
|--------|-----------|
| **구문** | `azcopy copy 'https://s3-<region-name>.amazonaws.com/' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true` |
| **예제** | `azcopy copy 'https://s3-rds.eu-north-1.amazonaws.com' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |

## <a name="handle-differences-in-object-naming-rules"></a>개체 명명 규칙의 차이점 처리

AWS S3에는 Azure blob 컨테이너와 비교할 때 버킷 이름에 대 한 서로 다른 명명 규칙 집합이 있습니다. [여기](https://docs.aws.amazon.com/AmazonS3/latest/dev/BucketRestrictions.html#bucketnamingrules)에서 정보를 읽을 수 있습니다. 버킷 그룹을 Azure storage 계정에 복사 하도록 선택 하는 경우 명명 차이로 인해 복사 작업이 실패할 수 있습니다.

AzCopy는 발생할 수 있는 가장 일반적인 문제 중 두 가지를 처리 합니다. 연속 하이픈을 포함 하는 기간 및 버킷을 포함 하는 버킷 AWS S3 버킷 이름에는 마침표와 연속 하이픈이 포함 될 수 있지만 Azure의 컨테이너는 그렇지 않습니다. AzCopy는 마침표와 하이픈을 연속 하는 하이픈의 수를 나타내는 숫자로 바꿉니다. 예를 들어 `my----bucket` 이름이 인 버킷이 `my-4-bucket`됩니다. 

또한 AzCopy는 파일에 대 한 복사를 수행 하므로 이름 충돌을 확인 하 고 해결 하려고 시도 합니다. 예를 들어 이름이 `bucket-name` 및 `bucket.name`인 버킷이 있는 경우 AzCopy는 먼저 `bucket.name` 라는 버킷을 먼저 `bucket-name` 후 `bucket-name-2`로 확인 합니다.

## <a name="handle-differences-in-object-metadata"></a>개체 메타 데이터의 차이점 처리

AWS S3 및 Azure는 개체 키 이름에 다른 문자 집합을 허용 합니다. AWS s 3에서 사용 하는 문자에 대 한 자세한 내용을 확인할 [수 있습니다.](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingMetadata.html#object-keys) Azure 쪽에서는 blob 개체 키가 [ C# 식별자](https://docs.microsoft.com/dotnet/csharp/language-reference/)에 대 한 명명 규칙을 준수 합니다.

AzCopy `copy` 명령의 일부로 파일의 메타 데이터에 호환 되지 않는 키 이름이 포함 된 파일을 처리 하는 방법을 지정 하는 선택적 `s2s-invalid-metadata-handle` 플래그에 대 한 값을 제공할 수 있습니다. 다음 표에서는 각 플래그 값에 대해 설명 합니다.

| 플래그 값 | 설명  |
|--------|-----------|
| **ExcludeIfInvalid** | (기본 옵션) 전송 된 개체에 메타 데이터가 포함 되어 있지 않습니다. AzCopy에서 경고를 기록 합니다. |
| **FailIfInvalid** | 개체는 복사 되지 않습니다. AzCopy는 오류를 기록 하 고 전송 요약에 표시 되는 실패 횟수에 해당 오류를 포함 합니다.  |
| **RenameIfInvalid**  | AzCopy는 잘못 된 메타 데이터 키를 확인 하 고 확인 된 메타 데이터 키 값 쌍을 사용 하 여 Azure에 개체를 복사 합니다. AzCopy가 개체 키의 이름을 변경 하는 데 사용 하는 단계를 정확히 알아보려면 아래의 [AzCopy 개체 키의 이름을 바꾸는 방법](#rename-logic) 섹션을 참조 하세요. AzCopy가 키의 이름을 바꿀 수 없는 경우 개체는 복사 되지 않습니다. |

<a id="rename-logic" />

### <a name="how-azcopy-renames-object-keys"></a>AzCopy가 개체 키의 이름을 바꾸는 방법

AzCopy는 다음 단계를 수행 합니다.

1. 잘못 된 문자를 ' _ '로 바꿉니다.

2. 새 유효한 키의 시작 부분에 `rename_` 문자열을 추가 합니다.

   이 키는 원래 메타 데이터 **값**을 저장 하는 데 사용 됩니다.

3. 새 유효한 키의 시작 부분에 `rename_key_` 문자열을 추가 합니다.
   이 키는 원래 메타 데이터의 잘못 된 **키**를 저장 하는 데 사용 됩니다.
   메타 데이터 키가 Blob storage 서비스의 값으로 유지 되므로이 키를 사용 하 여 Azure 쪽에서 메타 데이터를 시도 하 고 복구할 수 있습니다.

## <a name="next-steps"></a>다음 단계

다음 문서에서 더 많은 예제를 찾아보세요.

- [AzCopy 시작](storage-use-azcopy-v10.md)

- [AzCopy 및 Blob 스토리지를 사용하여 데이터 전송](storage-use-azcopy-blobs.md)

- [AzCopy 및 파일 스토리지를 사용하여 데이터 전송](storage-use-azcopy-files.md)

- [AzCopy 구성, 최적화 및 문제 해결](storage-use-azcopy-configure.md)