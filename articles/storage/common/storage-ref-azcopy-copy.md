---
title: azcopy 복사 | Microsoft Docs
description: 이 문서에서는 azcopy copy 명령에 대 한 참조 정보를 제공 합니다.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: c15d188e333bea5e74fa65d2bbdf38ae7fadc246
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195738"
---
# <a name="azcopy-copy"></a>azcopy 복사

원본 데이터를 대상 위치에 복사 합니다.

## <a name="synopsis"></a>개요

지원 되는 지침은 다음과 같습니다.

- 로컬 <-> Azure Blob (SAS 또는 OAuth 인증)
- 로컬 <-Azure 파일 > (공유/디렉터리 SAS 인증)
- 로컬 <-> ADLS Gen 2 (SAS, OAuth 또는 SharedKey authentication)
- Azure Blob (SAS 또는 public) <-> Azure Blob (SAS 또는 OAuth 인증)
- Azure File (SAS)-> Azure 블록 Blob (SAS 또는 OAuth 인증)
- AWS S3 (액세스 키)-> Azure 블록 Blob (SAS 또는 OAuth 인증)

자세한 내용은 예제를 참조 하세요.

### <a name="advanced"></a>고급

AzCopy는 파일 확장명 또는 콘텐츠 (확장명이 지정 되지 않은 경우)에 따라 로컬 디스크에서 업로드할 때 파일의 콘텐츠 형식을 자동으로 검색 합니다.

기본 제공 조회 테이블은 작지만 Unix에서 다음 이름 중 하나 이상에서 사용할 수 있는 경우 로컬 시스템의 mime. types 파일에 의해 확대 됩니다.

- /etc/mime.types
- /etc/apache2/mime.types
- /etc/apache/mime.types

Windows에서는 레지스트리에서 MIME 형식이 추출 됩니다. 플래그를 사용 하 여이 기능을 끌 수 있습니다. 플래그 섹션을 참조 하십시오.

> [!IMPORTANT]
> 명령줄을 사용 하 여 환경 변수를 설정 하는 경우 명령줄 기록에서 해당 변수를 읽을 수 있습니다. 명령줄 기록에서 자격 증명을 포함 하는 변수를 지우는 것이 좋습니다. 기록에 변수가 나타나지 않게 하려면 스크립트를 사용 하 여 사용자에 게 자격 증명을 묻는 메시지를 표시 하 고 환경 변수를 설정할 수 있습니다.

```azcopy
azcopy copy [source] [destination] [flags]
```

## <a name="examples"></a>예

OAuth 인증을 사용 하 여 단일 파일을 업로드 합니다.

AzCopy에 아직 로그인 하지 않은 경우 다음 명령을 실행 하기 `azcopy login` 전에 명령을 사용 하세요.

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

위와 동일 하지만 이번에는 파일 콘텐츠의 MD5 해시를 계산 하 고 blob의 콘텐츠-MD5 속성으로 저장 합니다.

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]" --put-md5
```

SAS를 사용 하 여 단일 파일을 업로드 합니다.

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

파이프를 사용 하 여 SAS를 사용 하 여 단일 파일을 업로드 합니다 (블록 blob에만 해당).

```azcopy
cat "/path/to/file.txt" | azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

SAS를 사용 하 여 전체 디렉터리를 업로드 합니다.

```azcopy
azcopy cp "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

로 구분하거나 여러

```azcopy
azcopy cp "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --put-md5
```

와일드 카드를 사용 하 여 SAS를 사용 하 여 파일 집합을 업로드 합니다.

```azcopy
azcopy cp "/path/*foo/*bar/*.pdf" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]"
```

와일드 카드를 사용 하 여 SAS를 사용 하 여 파일 및 디렉터리 업로드:

```azcopy
azcopy cp "/path/*foo/*bar*" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

OAuth 인증을 사용 하 여 단일 파일을 다운로드 합니다.

AzCopy에 아직 로그인 하지 않은 경우 다음 명령을 실행 하기 `azcopy login` 전에 명령을 사용 하세요.

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]" "/path/to/file.txt"
```

SAS를 사용 하 여 단일 파일을 다운로드 합니다.

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "/path/to/file.txt"
```

파이프를 사용 하 여 SAS가 있는 단일 파일을 다운로드 합니다 (블록 blob에만 해당).

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" > "/path/to/file.txt"
```

SAS를 사용 하 여 전체 디렉터리를 다운로드 합니다.

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" "/path/to/dir" --recursive=true
```

와일드 카드를 사용 하 여 SAS로 파일 집합을 다운로드 합니다.

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/foo*?[SAS]" "/path/to/dir"
```

와일드 카드를 사용 하 여 SAS를 사용 하 여 파일 및 디렉터리를 다운로드 합니다.

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/foo*?[SAS]" "/path/to/dir" --recursive=true
```

Sas를 사용 하 여 단일 blob을 SAS를 사용 하는 다른 blob에 복사 합니다.

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

OAuth 토큰을 사용 하 여 SAS가 있는 단일 blob을 다른 blob에 복사 합니다.

AzCopy에 아직 로그인 하지 않은 경우 다음 명령을 실행 하기 `azcopy login` 전에 명령을 사용 하세요. OAuth 토큰은 대상 저장소 계정에 액세스 하는 데 사용 됩니다.

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]"
```

Sas를 사용 하는 blob 가상 디렉터리에서 sas를 사용 하는 다른 blob 가상 디렉터리로 전체 디렉터리를 복사 합니다.

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

Sas를 사용 하는 blob 계정에서 SAS를 사용 하는 다른 blob 계정으로 전체 계정 데이터를 복사 합니다.

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net?[SAS]" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive=true
```

SAS를 사용 하는 blob에 대 한 액세스 키를 사용 하 여 S3에서 단일 개체를 복사 합니다.

S3 원본에 대해 환경 변수 AWS_ACCESS_KEY_ID 및 AWS_SECRET_ACCESS_KEY를 설정 합니다.

```azcopy
azcopy cp "https://s3.amazonaws.com/[bucket]/[object]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

SAS를 사용 하는 blob 가상 디렉터리에 대 한 액세스 키를 사용 하 여 S3에서 전체 디렉터리를 복사 합니다.

```azcopy
azcopy cp "https://s3.amazonaws.com/[bucket]/[folder]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

S https://docs.aws.amazon.com/AmazonS3/latest/user-guide/using-folders.html 3에 대 한 [폴더]의 의미에 대 한 자세한 내용은를 참조 하십시오. S3 원본에 대해 환경 변수 AWS_ACCESS_KEY_ID 및 AWS_SECRET_ACCESS_KEY를 설정 합니다.

SAS를 사용 하는 blob 계정에 대 한 액세스 키를 사용 하 여 S3 서비스의 모든 버킷을 복사 합니다.

S3 원본에 대해 환경 변수 AWS_ACCESS_KEY_ID 및 AWS_SECRET_ACCESS_KEY를 설정 합니다.

```azcopy
azcopy cp "https://s3.amazonaws.com/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive=true
```

SAS를 사용 하는 blob 계정에 대 한 액세스 키를 사용 하 여 S3 지역의 모든 버킷을 복사 합니다.

```azcopy
azcopy cp "https://s3-[region].amazonaws.com/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive=true
```

S3 원본에 대해 환경 변수 AWS_ACCESS_KEY_ID 및 AWS_SECRET_ACCESS_KEY를 설정 합니다.

## <a name="options"></a>옵션

|옵션|설명|
|--|--|
|--blob 형식 문자열|대상에 있는 blob의 유형을 정의 합니다. 이는 blob을 업로드 하 고 계정 간에 복사할 때 사용 됩니다 (기본값 "없음").|
|--블록-blob 계층 문자열|이 blob 계층을 사용 하 여 Azure Storage에 블록 blob을 업로드 합니다. 기본값은 "None"입니다.|
|--블록 크기-mb float |Azure Storage에 업로드 하 고 Azure Storage에서 다운로드 하는 경우이 블록 크기 (MiB에 지정)를 사용 합니다. 기본값은 파일 크기에 따라 자동으로 계산 됩니다. 소수 자릿수가 허용 됩니다. 예를 들면 다음과 같습니다. 0.25).|
|--cache 컨트롤 문자열|Cache-control 헤더를 설정 합니다. 다운로드 시 반환 됩니다.|
|--check-md5 문자열|다운로드할 때 엄격 하 게 MD5 해시의 유효성을 검사 하는 방법을 지정 합니다. 다운로드 하는 경우에만 사용할 수 있습니다. 사용 가능한 옵션은 다음과 같습니다. NoCheck, LogOnly, FailIfDifferent,. (기본값 "FailIfDifferent")|
|--콘텐츠 처리 문자열|콘텐츠 처리 헤더를 설정 합니다. 다운로드 시 반환 됩니다.|
|--콘텐츠 인코딩 문자열|콘텐츠 인코딩 헤더를 설정 합니다. 다운로드 시 반환 됩니다.|
|--콘텐츠 언어 문자열|콘텐츠 언어 헤더를 설정 합니다. 다운로드 시 반환 됩니다.|
|--content 형식 문자열 |파일의 콘텐츠 형식을 지정 합니다. -추측-mime 형식이 없음을 의미 합니다. 다운로드 시 반환 됩니다.|
|--문자열 제외|복사할 때 이러한 파일을 제외 합니다. \* 사용을 지원 합니다.|
|--exclude-blob 형식 문자열|컨테이너 또는 계정에서 blob을 복사할 때 제외할 blob 유형 (블록 Blob/PageBlob/AppendBlob)을 선택적으로 지정 합니다. 비 azure 서비스에서 서비스로 데이터를 복사 하는 경우에는이 플래그를 사용할 수 없습니다. 하나 이상의 blob이 '; '으로 구분 되어야 합니다.|
|--팔 로우-symlink|로컬 파일 시스템에서 업로드할 때 기호화 된 링크를 따르세요.|
|--from 문자열|필요에 따라 원본 대상 조합을 지정 합니다. 예: LocalBlob, BlobLocal, LocalBlobFS.|
|-h, --help|복사 명령에 대 한 도움말 콘텐츠를 표시 합니다. |
|--로그 수준 문자열|로그 파일에 대 한 로그의 자세한 정도를 정의 합니다. 사용 가능한 수준은 다음과 같습니다. 정보 (모든 요청/응답), 경고 (저속 응답), 오류 (실패 한 요청만) 및 없음 (출력 로그 없음) (기본 "정보").|
|--메타 데이터 문자열|이러한 키-값 쌍이 있는 Azure Storage에 메타 데이터로 업로드 합니다.|
|--추측-mime 형식|AzCopy에서 파일의 확장명 또는 내용을 기준으로 콘텐츠 형식을 검색 하지 못하도록 합니다.|
|--덮어쓰기|이 플래그가 true로 설정 된 경우 대상에서 충돌 하는 파일/b s s를 덮어씁니다. 기본값은 true입니다.|
|--페이지-blob 계층 문자열 |이 blob 계층을 사용 하 여 Azure Storage에 페이지 blob을 업로드 합니다. 기본값은 "None"입니다.|
|--유지-마지막-수정 시간|Destination이 파일 시스템 인 경우에만 사용할 수 있습니다.|
|--입력-md5|각 파일의 MD5 해시를 만들고 대상 blob 또는 파일의 콘텐츠-MD5 속성으로 해시를 저장 합니다. 기본적으로 해시가 생성 되지 않습니다. 업로드할 때만 사용할 수 있습니다.|
|--recursive|로컬 파일 시스템에서 업로드할 때 하위 디렉터리를 재귀적으로 확인 합니다.|
|--s2s-검색-소스-변경 됨|를 열거 한 후 원본이 변경 되었는지 확인 합니다. S2S 복사의 경우 원본이 원격 리소스 이므로 원본에 변경 된 내용이 있는지 확인 하려면 추가 요청 비용이 필요 합니다.|
|--s2s-핸들-잘못 된-메타 데이터 문자열 |잘못 된 메타 데이터 키를 처리 하는 방법을 지정 합니다. 사용 가능한 옵션은 다음과 같습니다. ExcludeIfInvalid, FailIfInvalid, RenameIfInvalid. (기본 "ExcludeIfInvalid").|
|--s2s-보존-액세스 계층|서비스를 복사 하는 동안 액세스 계층을 유지 합니다. 대상 저장소 계정에서 액세스 계층 설정을 지원 하도록 하려면 [Azure Blob storage: 핫, 쿨 및 보관 액세스 계층](../blobs/storage-blob-storage-tiers.md) 을 참조 하세요. 액세스 계층 설정이 지원 되지 않는 경우에는 s2sPreserveAccessTier = false를 사용 하 여 액세스 계층 복사를 무시 하세요.  기본값은 true입니다.|
|--s2s-preserve-속성|서비스에서 서비스를 복사 하는 동안 전체 속성을 유지 합니다. S3 및 Azure 파일 비 단일 파일 원본의 경우 목록 작업에서 개체 및 파일의 전체 속성을 반환 하지 않으므로 전체 속성을 유지 하기 위해 AzCopy는 개체와 파일당 하나의 추가 요청을 전송 해야 합니다. 기본값은 true입니다.|

## <a name="options-inherited-from-parent-commands"></a>부모 명령에서 상속 된 옵션

|옵션|설명|
|---|---|
|--0mbps uint32|전송 률 (메가 비트/초)을 대문자로 처리 합니다. 순간 처리량은 cap와 약간 다를 수 있습니다. 이 옵션을 0으로 설정 하거나 생략 하면 처리량이 생략 되지 않습니다.|
|--출력 형식 문자열|명령의 출력 형식입니다. 텍스트, json 등을 선택할 수 있습니다. 기본값은 "text"입니다.|

## <a name="see-also"></a>참고자료

- [azcopy](storage-ref-azcopy.md)
