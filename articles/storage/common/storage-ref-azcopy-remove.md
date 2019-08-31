---
title: azcopy remove | Microsoft Docs
description: 이 문서에서는 azcopy remove 명령에 대 한 참조 정보를 제공 합니다.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 0cc366ab2cdad9c7258dca905d8f4a06472119fe
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195907"
---
# <a name="azcopy-remove"></a>azcopy 제거

Azure Storage Blob, 파일 및 Azure Data Lake Storage Gen2에서 엔터티를 삭제 합니다.

## <a name="synopsis"></a>개요

```azcopy
azcopy remove [resourceURL] [flags]
```

## <a name="examples"></a>예

SAS를 사용 하 여 단일 blob을 제거 합니다.

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

SAS를 사용 하 여 전체 가상 디렉터리를 제거 합니다.

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

가상 디렉터리 내의 상위 blob만 제거 하 고 하위 디렉터리는 제거 하지 않습니다.

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

가상 디렉터리에서 blob의 하위 집합을 제거 합니다 (예: jpg 및 pdf 파일만 또는 blob 이름이 "exactName" 인 경우).

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --include="*.jpg;*.pdf;exactName"
```

전체 가상 디렉터리를 제거 하지만 범위에서 특정 blob을 제외 합니다 (예: foo로 시작 하는 모든 blob 또는 막대로 끝남).

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --exclude="foo*;*bar"
```

Data Lake Storage Gen2에서 단일 파일을 제거 합니다 (include 및 exclude는 지원 되지 않음).

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/file]?[SAS]"
```

Data Lake Storage Gen2에서 단일 디렉터리 제거 (포함 및 제외 지원 되지 않음):

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/directory]?[SAS]"
```

## <a name="options"></a>변수

|옵션|Description|
|--|--|
|--문자열 제외|이름이 패턴 목록과 일치 하는 파일을 제외 합니다. 예: *.jpg;* . pdf; exactName|
|-h, --help|제거 명령에 대 한 도움말 콘텐츠를 표시 합니다.|
|--문자열 포함|이름이 패턴 목록과 일치 하는 파일만 포함 합니다. 예: *.jpg;* . pdf; exactName|
|--로그 수준 문자열|로그 파일에 대 한 로그의 자세한 정도를 정의 합니다. 사용 가능한 수준은 다음과 같습니다. 정보 (모든 요청/응답), 경고 (저속 응답), 오류 (실패 한 요청만) 및 없음 (출력 로그 없음) (기본 "정보")|
|--recursive|디렉터리 간에 동기화 할 때 하위 디렉터리를 재귀적으로 확인 합니다.|

## <a name="options-inherited-from-parent-commands"></a>부모 명령에서 상속 된 옵션

|옵션|Description|
|---|---|
|--0mbps uint32|전송 률 (메가 비트/초)을 대문자로 처리 합니다. 순간 처리량은 cap와 약간 다를 수 있습니다. 이 옵션을 0으로 설정 하거나 생략 하면 처리량이 생략 되지 않습니다.|
|--출력 형식 문자열|명령의 출력 형식입니다. 텍스트, json 등을 선택할 수 있습니다. 기본값은 "text"입니다.|

## <a name="see-also"></a>참고자료

- [azcopy](storage-ref-azcopy.md)
