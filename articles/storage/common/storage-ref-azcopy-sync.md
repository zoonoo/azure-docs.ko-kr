---
title: 아즈카피 동기화 | 마이크로 소프트 문서
description: 이 문서에서는 azcopy 동기화 명령에 대한 참조 정보를 제공합니다.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: d855019be7f357a35a26d14e68ba3d427d984e17
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086031"
---
# <a name="azcopy-sync"></a>azcopy sync

원본 위치를 대상 위치로 복제합니다.

## <a name="synopsis"></a>개요

마지막으로 수정된 시간은 비교에 사용됩니다. 대상에서 마지막으로 수정된 시간이 최신인 경우 파일이 건너뜁니다.

지원되는 쌍은 다음과 같습니다.

- 로컬 < > Azure Blob(SAS 또는 OAuth 인증을 사용할 수 있음)
- Azure Blob < > Azure Blob(원본은 SAS를 포함하거나 공개적으로 액세스할 수 있어야 하며 SAS 또는 OAuth 인증을 대상에 사용할 수 있음)
- Azure 파일 < > Azure 파일(원본에 SAS를 포함해야 하거나 공개적으로 액세스할 수 있어야 합니다. 대상에 SAS 인증을 사용해야 합니다.

동기화 명령은 여러 가지 방법으로 복사 명령과 다릅니다.

1. 기본적으로 재귀 플래그는 true이며 동기화는 모든 하위 디렉토리를 복사합니다. 동기화는 재귀 플래그가 false인 경우에만 디렉터리 내의 최상위 파일을 복사합니다.
2. 가상 디렉터리 간에 동기화할 때 가상 디렉터리 중 하나와 이름이 같은 Blob이 있는 경우 경로에 후행 슬래시를 추가합니다(예제 참조).
3. 'deleteDestination' 플래그가 true 또는 prompt로 설정된 경우 동기화는 원본에 없는 대상에서 파일 및 Blob을 삭제합니다.

## <a name="related-conceptual-articles"></a>관련 개념 기사

- [AzCopy 시작](storage-use-azcopy-v10.md)
- [AzCopy 및 Blob 저장소로 데이터 전송](storage-use-azcopy-blobs.md)
- [AzCopy 및 파일 스토리지를 사용하여 데이터 전송](storage-use-azcopy-files.md)
- [AzCopy 구성, 최적화 및 문제 해결](storage-use-azcopy-configure.md)

### <a name="advanced"></a>고급

파일 확장프로그램을 지정하지 않으면 AzCopy는 파일 확장명또는 콘텐츠(확장자가 지정되지 않은 경우)에 따라 로컬 디스크에서 업로드할 때 파일의 콘텐츠 유형을 자동으로 감지합니다.

기본 제공 조회 테이블은 작지만 유닉스에서는 로컬 시스템의 mime.type 파일에서 이러한 이름 중 하나 이상에서 사용할 수 있는 경우 보강됩니다.

- /etc/mime.type
- /etc/아파치2/마임.type
- /etc/아파치/마임.type

Windows에서 MIME 형식은 레지스트리에서 추출됩니다.

```azcopy
azcopy sync <source> <destination> [flags]
```

## <a name="examples"></a>예

단일 파일 동기화:

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

> [!NOTE]
> 대상 Blob이 *있어야 합니다.* 대상에 아직 존재하지 않는 단일 파일을 복사하는 데 사용합니다. `azcopy copy` 그렇지 않으면 다음과 같은 `Cannot perform sync due to error: sync must happen between source and destination of the same type, e.g. either file <-> file, or directory/container <-> directory/container`오류가 발생합니다.

위와 동일하지만 이번에는 파일 콘텐츠의 MD5 해시를 계산하고 Blob의 Content-MD5 속성으로 저장합니다.

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]" --put-md5
```

하위 디렉토리를 포함한 전체 디렉터리 동기화(재귀는 기본적으로 켜진 경우).

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]"
```

또는

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --put-md5
```

디렉터리 내의 상위 파일만 동기화하지만 하위 디렉토리에는 동기화되지 않습니다.

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

디렉터리에서 파일의 하위 집합을 동기화합니다(예: jpg 및 PDF 파일만 또는 파일 이름이 "exactName"인 경우)

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --include="*.jpg;*.pdf;exactName"
```

전체 디렉토리를 동기화하지만 범위에서 특정 파일을 제외합니다(예: foo로 시작하거나 막대로 끝나는 모든 파일):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --exclude="foo*;*bar"
```

단일 Blob 동기화:

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

가상 디렉터리 동기화:

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=true
```

Blob과 이름이 같은 가상 디렉토리를 동기화합니다(모호성을 줄이기 위해 경로에 후행 슬래시 추가):)

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]/?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]/" --recursive=true
```

Azure 파일 디렉터리 동기화(Blob와 동일한 구문):

```azcopy
azcopy sync "https://[account].file.core.windows.net/[share]/[path/to/dir]?[SAS]" "https://[account].file.core.windows.net/[share]/[path/to/dir]" --recursive=true
```

> [!NOTE]
> 포함/제외 플래그를 함께 사용하는 경우 포함 패턴과 일치하는 파일만 보이지만 제외 패턴과 일치하는 파일은 항상 무시됩니다.

## <a name="options"></a>옵션

**--블록 크기-mb** 부동 Azure 저장소에 업로드하거나 Azure 저장소에서 다운로드할 때 이 블록 크기(MiB에 지정)를 사용합니다. 기본값은 파일 크기에 따라 자동으로 계산됩니다. 소수분은 허용됩니다(예: 0.25).

**--check-md5** 문자열은 다운로드 할 때 MD5 해시의 유효성을 엄격하게 검사하는 방법을 지정합니다. 이 옵션은 다운로드할 때만 사용할 수 있습니다. 사용 가능한 값은 다음과 같습니다: NoCheck, LogOnly, FailIfDifferent, FailIfDifferentOrMissing. (기본값 'FailIfDifferent')를 선택합니다. (기본값 "FailIfDifferent")

**--delete-destination** 문자열 원본에 없는 대상에서 추가 파일을 삭제할지 여부를 정의합니다. 참, 거짓 또는 프롬프트로 설정할 수 있습니다. 프롬프트로 설정하면 파일 및 삭제를 위해 Blob을 예약하기 전에 사용자에게 질문을 받게 됩니다. (기본값 'false')를 입력합니다. (기본값 "false")

**--제외 특성** 문자열(Windows만 해당) 특성이 특성 목록과 일치하는 파일을 제외합니다. 예를 들어: A; S; R

**--제외 경로** 문자열 복사할 때 이러한 경로를 제외합니다. 이 옵션은 와일드카드 문자(*)를 지원하지 않습니다. 상대 경로 접두사를 확인합니다(예: myFolder;myFolder/subDirName/file.pdf). 계정 통과와 함께 사용하는 경우 경로에는 컨테이너 이름이 포함되지 않습니다.

**--제외 패턴** 문자열 이름이 패턴 목록과 일치하는 파일 제외. 예: \*.jpg; \*.pdf;정확한 이름

**-h, --동기화에** 대한 도움말 도움말

**--포함 특성** 문자열(Windows만) 특성이 특성 목록과 일치하는 파일만 포함합니다. 예를 들어: A; S; R

**--포함 패턴** 문자열 이름이 패턴 목록과 일치하는 파일만 포함합니다. 예: \*.jpg; \*.pdf;정확한 이름

**--로그 수준** 문자열 로그 파일, 사용 가능한 수준: INFO(모든 요청 및 응답), 경고(느린 응답), ERROR(실패한 요청만), NONE(출력 로그 없음)에 대한 로그 세부 정보를 정의합니다. (기본 정보)를 입력합니다. (기본값 "정보")

**--put-md5**                     각 파일의 MD5 해시를 만들고 해시를 대상 Blob 또는 파일의 Content-MD5 속성으로 저장합니다. (기본적으로 해시가 만들어지지 않습니다.) 업로드할 때만 사용할 수 있습니다.

**--재귀**                   기본적으로 디렉터리 간에 동기화할 때 하위 디렉터리를 재귀적으로 살펴봅니다. (기본 참). (기본 참)

## <a name="options-inherited-from-parent-commands"></a>상위 명령에서 상속된 옵션

|옵션|설명|
|---|---|
|--캡 mbps uint32|초당 메가비트로 전송 속도를 한도 를 캡슐화합니다. 모멘트별 처리량은 캡과 약간 다를 수 있습니다. 이 옵션이 0으로 설정되어 있거나 생략된 경우 처리량은 제한되지 않습니다.|
|--출력 유형 문자열|명령 출력의 형식입니다. 선택 사항은 다음과 같습니다: 텍스트, json. 기본값은 "텍스트"입니다.|

## <a name="see-also"></a>참조

- [azcopy](storage-ref-azcopy.md)
