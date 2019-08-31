---
title: azcopy sync | Microsoft Docs
description: 이 문서에서는 azcopy sync 명령에 대 한 참조 정보를 제공 합니다.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: fb6c3b711a89ae7e4ef403a75927c4c6172523d0
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195764"
---
# <a name="azcopy-sync"></a>azcopy 동기화

원본 위치를 대상 위치에 복제 합니다.

## <a name="synopsis"></a>개요

마지막으로 수정 된 시간이 비교에 사용 됩니다. 대상에서 마지막으로 수정 된 시간이 더 최신인 경우 파일을 건너뜁니다.

지원 되는 쌍은 다음과 같습니다.

- 로컬 <-Azure Blob > (SAS 또는 OAuth 인증 사용 가능)

Sync 명령은 다음과 같은 여러 가지 방법으로 복사 명령과 다릅니다.

  1. 재귀 플래그는 기본적으로 설정 되어 있습니다.
  2. 원본 및 대상에는 패턴 (예: * 또는?)을 사용할 수 없습니다.
  3. Include 및 exclude 플래그는 파일 이름과 일치 하는 패턴의 목록이 될 수 있습니다. 예시를 보려면 예제 섹션을 참조 하십시오.
  4. 원본에 없는 파일 또는 blob이 대상에 있는 경우 사용자에 게 해당 파일 또는 blob을 삭제 하 라는 메시지가 표시 됩니다.

     이 프롬프트는 해당 플래그를 사용 하 여 닫을 수 있습니다.

### <a name="advanced"></a>고급

AzCopy는 파일 확장명 또는 콘텐츠 (확장명이 지정 되지 않은 경우)에 따라 로컬 디스크에서 업로드할 때 파일의 콘텐츠 형식을 자동으로 검색 합니다.

기본 제공 조회 테이블은 작지만 Unix에서는 다음 이름 중 하나 이상에서 사용할 수 있는 경우 로컬 시스템의 mime. types 파일에 의해 확장 됩니다.

- /etc/mime.types
- /etc/apache2/mime.types
- /etc/apache/mime.types

Windows에서는 레지스트리에서 MIME 형식이 추출 됩니다.

```azcopy
azcopy sync [flags]
```

## <a name="examples"></a>예

단일 파일 동기화:

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

위와 동일 하지만 이번에는 파일 콘텐츠의 MD5 해시를 계산 하 고 blob의 콘텐츠-MD5 속성으로 저장 합니다.

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]" --put-md5
```

하위 디렉터리를 포함 하 여 전체 디렉터리를 동기화 합니다 (기본적으로 재귀적이 켜져 있는지 확인).

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]"
```

로 구분하거나 여러

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --put-md5
```

디렉터리 내의 최상위 파일만 동기화 하 고 하위 디렉터리는 동기화 하지 않습니다.

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

디렉터리에 있는 파일의 하위 집합을 동기화 합니다 (예: jpg 및 pdf 파일만 또는 파일 이름이 "exactName" 인 경우).

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --include="*.jpg;*.pdf;exactName"
```

전체 디렉터리를 동기화 하지만 범위에서 특정 파일을 제외 합니다 (예: foo로 시작 하는 모든 파일 또는 막대로 끝남).

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --exclude="foo*;*bar"
```

> [!NOTE]
> include/exclude 플래그를 함께 사용 하는 경우 include 패턴과 일치 하는 파일만 조회 되지만 제외 패턴과 일치 하는 파일만 항상 무시 됩니다.

## <a name="options"></a>변수

|옵션|설명|
|--|--|
|--블록 크기-mb float|Azure Storage에 업로드 하거나 Azure Storage에서 다운로드 하는 경우이 블록 크기 (MiB에 지정)를 사용 합니다. 기본값은 파일 크기에 따라 자동으로 계산 됩니다. 소수 자릿수가 허용 됩니다. 예를 들면 다음과 같습니다. 0.25).|
|--check-md5 문자열|다운로드할 때 엄격 하 게 MD5 해시의 유효성을 검사 하는 방법을 지정 합니다. 이 옵션은를 다운로드 하는 경우에만 사용할 수 있습니다. 사용 가능한 값은 다음과 같습니다. NoCheck, LogOnly, FailIfDifferent,. 기본값은 "FailIfDifferent"입니다.|
|--삭제 대상 문자열|원본에 없는 추가 파일을 대상에서 삭제할지 여부를 정의 합니다. True, false 또는 프롬프트로 설정할 수 있습니다. 메시지를 표시로 설정 하면 파일 및 blob의 삭제를 예약 하기 전에 사용자에 게 문의 하 라는 메시지가 표시 됩니다. 기본값은 "false"입니다.|
|--문자열 제외|이름이 패턴 목록과 일치 하는 파일을 제외 합니다. 예: *.jpg;* . pdf; exactName.|
|-h, --help|동기화 명령에 대 한 도움말 콘텐츠를 표시 합니다.|
|--문자열 포함|이름이 패턴 목록과 일치 하는 파일만 포함 합니다. 예: *.jpg;* . pdf; exactName.|
|--로그 수준 문자열|로그 파일에 대 한 로그의 자세한 정도를 정의 합니다. 사용 가능한 수준은 다음과 같습니다. 정보 (모든 요청/응답), 경고 (저속 응답), 오류 (실패 한 요청만) 및 없음 (출력 로그 없음) (기본 "정보").|
|--입력-md5|각 파일의 MD5 해시를 만들고 대상 blob 또는 파일의 콘텐츠-MD5 속성으로 해시를 저장 합니다. 기본적으로 해시가 생성 되지 않습니다. 업로드할 때만 사용할 수 있습니다.|
|--recursive|기본적으로 True로 설정 하면 디렉터리 간에 동기화 할 때 하위 디렉터리를 재귀적으로 찾습니다. 기본값은 true입니다.|

## <a name="options-inherited-from-parent-commands"></a>부모 명령에서 상속 된 옵션

|옵션|설명|
|---|---|
|--0mbps uint32|전송 률 (메가 비트/초)을 대문자로 처리 합니다. 순간 처리량은 cap와 약간 다를 수 있습니다. 이 옵션을 0으로 설정 하거나 생략 하면 처리량이 생략 되지 않습니다.|
|--출력 형식 문자열|명령의 출력 형식입니다. 텍스트, json 등을 선택할 수 있습니다. 기본값은 "text"입니다.|

## <a name="see-also"></a>참고자료

- [azcopy](storage-ref-azcopy.md)
