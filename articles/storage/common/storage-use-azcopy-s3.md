---
title: AzCopy를 사용하여 아마존 S3에서 Azure 저장소로 데이터 복사 | 마이크로 소프트 문서
description: AzCopy 및 Amazon S3 버킷을 사용하여 데이터 전송
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: a3180593eaf8c01c772fd761d88b5f5b9f7657ee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75941498"
---
# <a name="copy-data-from-amazon-s3-to-azure-storage-by-using-azcopy"></a>AzCopy를 사용하여 아마존 S3에서 Azure 저장소로 데이터 복사

AzCopy는 저장소 계정에서 Blob 또는 파일을 복사하는 데 사용할 수 있는 명령줄 유틸리티입니다. 이 문서에서는 AzCopy를 사용하여 Amazon Web Services(AWS) S3S3에서 Azure Blob 저장소로 개체, 디렉터리 및 버킷을 복사하는 데 도움이 됩니다.

## <a name="choose-how-youll-provide-authorization-credentials"></a>권한 부여 자격 증명을 제공하는 방법 선택

* Azure 저장소에 대한 권한을 부여하려면 Azure Active Directory(AD) 또는 SAS(공유 액세스 서명) 토큰을 사용합니다.

* AWS S3에 대한 승인을 위해 AWS 액세스 키와 비밀 액세스 키를 사용합니다.

### <a name="authorize-with-azure-storage"></a>Azure 저장소사용 권한 부여

AzCopy 를 다운로드하려면 [AzCopy 시작](storage-use-azcopy-v10.md) 문서를 참조하고 저장소 서비스에 권한 부여 자격 증명을 제공하는 방법을 선택합니다.

> [!NOTE]
> 이 문서의 예제에서는 `AzCopy login` 명령을 사용하여 ID를 인증했다고 가정합니다. 그런 다음 AzCopy는 Azure AD 계정을 사용하여 Blob 저장소의 데이터에 대한 액세스 권한을 부여합니다.
>
> SAS 토큰을 사용하여 Blob 데이터에 대한 액세스 권한을 부여하려는 경우 각 AzCopy 명령의 리소스 URL에 해당 토큰을 추가할 수 있습니다.
>
> 예: `https://mystorageaccount.blob.core.windows.net/mycontainer?<SAS-token>`

### <a name="authorize-with-aws-s3"></a>AWS S3 승인

AWS 액세스 키 및 비밀 액세스 키를 수집한 다음 다음 환경 변수를 설정합니다.

| 운영 체제 | 명령  |
|--------|-----------|
| **Windows** | `set AWS_ACCESS_KEY_ID=<access-key>`<br>`set AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **Linux** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **Macos** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>`|

## <a name="copy-objects-directories-and-buckets"></a>개체, 디렉터리 및 버킷 복사

AzCopy는 [URL](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) 에서 블록 넣기 API를 사용하므로 데이터가 AWS S3 및 스토리지 서버 간에 직접 복사됩니다. 이러한 복사 작업은 컴퓨터의 네트워크 대역폭을 사용하지 않습니다.

> [!IMPORTANT]
> 이 기능은 현재 미리 보기로 제공됩니다. 복사 작업 후 S3 버킷에서 데이터를 제거하기로 결정한 경우 데이터를 제거하기 전에 데이터가 저장소 계정에 제대로 복사되었는지 확인하십시오.

> [!TIP]
> 이 섹션의 예제에서는 경로 인수를 단일 따옴표('')로 둘러싸습니다. Windows 명령 셸(cmd.exe)을 제외한 모든 명령 셸에 단일 따옴표를 사용합니다. Windows 명령 셸(cmd.exe)을 사용하는 경우 경로 인수를 단일 따옴표('')가 아닌 큰따옴표(")로 동봉합니다.

 이러한 예제는 계층적 네임스페이스가 있는 계정에서도 작동합니다. [Data Lake Storage의 다중 프로토콜 액세스를](../blobs/data-lake-storage-multi-protocol-access.md) 사용하면 해당`blob.core.windows.net`계정에서 동일한 URL 구문()을 사용할 수 있습니다. 

### <a name="copy-an-object"></a>개체 복사

계층적 네임스페이스가`blob.core.windows.net`있는 계정에 대해 동일한 URL 구문 ()을 사용합니다.

|    |     |
|--------|-----------|
| **구문** | `azcopy copy 'https://s3.amazonaws.com/<bucket-name>/<object-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<blob-name>'` |
| **예제** | `azcopy copy 'https://s3.amazonaws.com/mybucket/myobject' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myblob'` |
| **예제(계층적** 네임스페이스) | `azcopy copy 'https://s3.amazonaws.com/mybucket/myobject' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myblob'` |

> [!NOTE]
> 이 문서의 예는 AWS S3 버킷에 경로 스타일 URL을 사용합니다(예: `http://s3.amazonaws.com/<bucket-name>`). 
>
> 가상 호스팅 스타일 URL도 사용할 수 있습니다(예: `http://bucket.s3.amazonaws.com`). 
>
> 버킷의 가상 호스팅에 대한 자세한 내용은 [버킷의 가상https://docs.aws.amazon.com/AmazonS3/latest/dev/VirtualHosting.html)호스팅]을 참조하십시오.

### <a name="copy-a-directory"></a>디렉터리 복사

계층적 네임스페이스가`blob.core.windows.net`있는 계정에 대해 동일한 URL 구문 ()을 사용합니다.

|    |     |
|--------|-----------|
| **구문** | `azcopy copy 'https://s3.amazonaws.com/<bucket-name>/<directory-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive=true` |
| **예제** | `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |
| **예제(계층적** 네임스페이스)| `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

### <a name="copy-a-bucket"></a>버킷 복사

계층적 네임스페이스가`blob.core.windows.net`있는 계정에 대해 동일한 URL 구문 ()을 사용합니다.

|    |     |
|--------|-----------|
| **구문** | `azcopy copy 'https://s3.amazonaws.com/<bucket-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>' --recursive=true` |
| **예제** | `azcopy copy 'https://s3.amazonaws.com/mybucket' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive=true` |
| **예제(계층적** 네임스페이스)| `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

### <a name="copy-all-buckets-in-all-regions"></a>모든 리전의 모든 버킷 복사

계층적 네임스페이스가`blob.core.windows.net`있는 계정에 대해 동일한 URL 구문 ()을 사용합니다.

|    |     |
|--------|-----------|
| **구문** | `azcopy copy 'https://s3.amazonaws.com/' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true` |
| **예제** | `azcopy copy 'https://s3.amazonaws.com' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |
| **예제(계층적** 네임스페이스)| `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

### <a name="copy-all-buckets-in-a-specific-s3-region"></a>특정 S3 리전의 모든 버킷 복사

계층적 네임스페이스가`blob.core.windows.net`있는 계정에 대해 동일한 URL 구문 ()을 사용합니다.

|    |     |
|--------|-----------|
| **구문** | `azcopy copy 'https://s3-<region-name>.amazonaws.com/' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true` |
| **예제** | `azcopy copy 'https://s3-rds.eu-north-1.amazonaws.com' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |
| **예제(계층적** 네임스페이스)| `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

## <a name="handle-differences-in-object-naming-rules"></a>개체 이름 지정 규칙의 차이 처리

AWS S3에는 Azure Blob 컨테이너와 비교하여 버킷 이름에 대한 다른 명명 규칙 집합이 있습니다. 당신은 [여기에](https://docs.aws.amazon.com/AmazonS3/latest/dev/BucketRestrictions.html#bucketnamingrules)그들에 대해 읽을 수 있습니다. 버킷 그룹을 Azure 저장소 계정에 복사하도록 선택하면 이름 차이로 인해 복사 작업이 실패할 수 있습니다.

AzCopy는 발생할 수 있는 가장 일반적인 두 가지 문제를 처리합니다. 연속하이픈을 포함하는 마침표와 버킷을 포함하는 버킷입니다. AWS S3 버킷 이름에는 마침표와 연속하이픈이 포함될 수 있지만 Azure의 컨테이너는 포함할 수 없습니다. AzCopy는 기간을 하이픈과 연속 하이픈으로 연속 하이픈 수를 나타내는 숫자로 바꿉니다(예: 명명된 `my----bucket` 버킷이 됩니다. `my-4-bucket` 

또한 AzCopy가 파일을 복사할 때 충돌 이름을 지정하고 이를 해결하려고 시도합니다. 예를 `bucket-name` 들어 이름이 있는 버킷이 있는 `bucket.name`경우 AzCopy는 먼저 `bucket.name` `bucket-name` 다음 으로 `bucket-name-2`명명된 버킷을 확인합니다.

## <a name="handle-differences-in-object-metadata"></a>개체 메타데이터의 차이 처리

AWS S3 및 Azure에서는 개체 키 의 이름에 서로 다른 문자 집합을 허용합니다. AWS S3에서 사용하는 문자에 대한 자세한 내용은 여기에서 확인할 수 [있습니다.](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingMetadata.html#object-keys) Azure 측에서 Blob 개체 키는 [C# 식별자에](https://docs.microsoft.com/dotnet/csharp/language-reference/)대한 명명 규칙을 준수합니다.

AzCopy `copy` 명령의 일부로 파일의 메타데이터에 호환되지 `s2s-invalid-metadata-handle` 않는 키 이름이 포함된 파일을 처리하는 방법을 지정하는 플래그(옵션)에 대한 값을 제공할 수 있습니다. 다음 표는 각 플래그 값을 설명합니다.

| 플래그 값 | 설명  |
|--------|-----------|
| **제외유효하지 않은 경우** | (기본 옵션) 메타데이터는 전송된 개체에 포함되지 않습니다. AzCopy는 경고를 기록합니다. |
| **실패하면 유효하지 않음** | 개체가 복사되지 않습니다. AzCopy는 오류를 기록하고 전송 요약에 나타나는 실패한 개수에 해당 오류를 포함합니다.  |
| **이름 이유효**  | AzCopy는 잘못된 메타데이터 키를 해결하고 해결된 메타데이터 키 값 쌍을 사용하여 개체를 Azure로 복사합니다. 개체 키의 이름을 바꾸기 위해 AzCopy가 취하는 단계를 정확히 알아보려면 아래 [AzCopy개체 키의 이름을 바꿀](#rename-logic) 수 있는 방법을 참조하세요. AzCopy가 키의 이름을 바꿀 수 없는 경우 개체는 복사되지 않습니다. |

<a id="rename-logic" />

### <a name="how-azcopy-renames-object-keys"></a>AzCopy가 개체 키의 이름을 변경한 방법

AzCopy는 다음 단계를 수행합니다.

1. 잘못된 문자를 '_'로 바꿉니다.

2. 새 유효한 `rename_` 키의 시작 부분에 문자열을 추가합니다.

   이 키는 원래 메타데이터 **값을**저장하는 데 사용됩니다.

3. 새 유효한 `rename_key_` 키의 시작 부분에 문자열을 추가합니다.
   이 키는 원래 메타데이터 잘못된 **키를**저장하는 데 사용됩니다.
   메타데이터 키가 Blob 저장소 서비스의 값으로 보존되므로 이 키를 사용하여 Azure 측에서 메타데이터를 시도하고 복구할 수 있습니다.

## <a name="next-steps"></a>다음 단계

다음 문서에서 더 많은 예제를 찾아보십시오.

- [AzCopy 시작](storage-use-azcopy-v10.md)

- [AzCopy 및 Blob 스토리지를 사용하여 데이터 전송](storage-use-azcopy-blobs.md)

- [AzCopy 및 파일 스토리지를 사용하여 데이터 전송](storage-use-azcopy-files.md)

- [AzCopy 구성, 최적화 및 문제 해결](storage-use-azcopy-configure.md)