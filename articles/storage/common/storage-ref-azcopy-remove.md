---
title: 아즈카피 제거 | 마이크로 소프트 문서
description: 이 문서에서는 azcopy 제거 명령에 대한 참조 정보를 제공합니다.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: abce1acb88e920c0de7bbb6447ec9d838f10486c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74033992"
---
# <a name="azcopy-remove"></a>azcopy remove

Azure 저장소 계정에서 Blob 또는 파일을 삭제합니다.

## <a name="synopsis"></a>개요

```azcopy
azcopy remove [resourceURL] [flags]
```

## <a name="related-conceptual-articles"></a>관련 개념 기사

- [AzCopy 시작](storage-use-azcopy-v10.md)
- [AzCopy 및 Blob 저장소로 데이터 전송](storage-use-azcopy-blobs.md)
- [AzCopy 및 파일 스토리지를 사용하여 데이터 전송](storage-use-azcopy-files.md)
- [AzCopy 구성, 최적화 및 문제 해결](storage-use-azcopy-configure.md)

## <a name="examples"></a>예

SAS를 가진 단 하나 Blob를 제거합니다:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

SAS를 통해 전체 가상 디렉터리제거:

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

가상 디렉터리 내부의 상위 Blob만 제거하지만 하위 디렉터리에는 포함되지 않습니다.

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

가상 디렉토리에서 Blob의 하위 집합을 제거합니다(예: jpg 및 PDF 파일만 또는 Blob 이름이 "exactName"인 경우).

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --include="*.jpg;*.pdf;exactName"
```

전체 가상 디렉터리를 제거하지만 범위에서 특정 Blob을 제외합니다(예: foo로 시작하거나 막대로 끝나는 모든 Blob).

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --exclude="foo*;*bar"
```

파일에 상대 경로(URL 인코딩되지 않음)를 배치하여 특정 Blob 및 가상 디렉터리를 제거합니다.

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/parent/dir]" --recursive=true --list-of-files=/usr/bar/list.txt
file content:
  dir1/dir2
  blob1
  blob2

```

계층적 네임스페이스가 있는 Blob Storage 계정에서 단일 파일을 제거합니다(지원되지 않음 포함/제외).

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/file]?[SAS]"
```

계층적 네임스페이스가 있는 Blob Storage 계정(지원되지 않음 포함/제외)이 있는 단일 디렉터리 제거

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/directory]?[SAS]"
```

## <a name="options"></a>옵션

**--제외 경로 문자열**      제거할 때 이러한 경로를 제외합니다. 이 옵션은 와일드카드 문자(*)를 지원하지 않습니다. 상대 경로 접두사를 확인합니다. 예: myFolder;myFolder/subDirName/file.pdf.

**--제외 패턴** 문자열 이름이 패턴 목록과 일치하는 파일 제외. 예: *.jpg; .* PDF;정확한 이름

**-h, --도움말** 제거를 위한 도움말

**--포함 경로** 문자열 제거할 때 이러한 경로만 포함합니다. 이 옵션은 와일드카드 문자(*)를 지원하지 않습니다. 상대 경로 접두사를 확인합니다. 예: myFolder;myFolder/subDirName/file.pdf

**--포함 패턴** 문자열 이름이 패턴 목록과 일치하는 파일만 포함합니다. 예: *.jpg; .* PDF;정확한 이름

**--파일 목록** 문자열 삭제할 파일 및 디렉터리 목록을 포함하는 파일의 위치를 정의합니다. 상대 경로는 줄 바꿈으로 구분되어야 하며 경로는 URL 인코딩되지 않아야 합니다.

**--로그 수준** 문자열 로그 파일에 대한 로그 세부 정보를 정의합니다. 사용 가능한 수준에는 INFO(모든 요청/응답), 경고(느린 응답), ERROR(실패한 요청만) 및 NONE(출력 로그 없음)이 포함됩니다. (기본 '정보') (기본값 "정보")

**--재귀**                디렉터리 간에 동기화할 때 하위 디렉터리를 재귀적으로 살펴봅니다.

## <a name="options-inherited-from-parent-commands"></a>상위 명령에서 상속된 옵션

|옵션|설명|
|---|---|
|--캡 mbps uint32|초당 메가비트로 전송 속도를 한도 를 캡슐화합니다. 모멘트별 처리량은 캡과 약간 다를 수 있습니다. 이 옵션이 0으로 설정되어 있거나 생략된 경우 처리량은 제한되지 않습니다.|
|--출력 유형 문자열|명령 출력의 형식입니다. 선택 사항은 다음과 같습니다: 텍스트, json. 기본값은 "텍스트"입니다.|

## <a name="see-also"></a>참조

- [아즈카피](storage-ref-azcopy.md)
