---
title: azcopy list | Microsoft Docs
description: 이 문서에서는 azcopy list 명령에 대 한 참조 정보를 제공 합니다.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: b7348e3790166e1a1aecab422e571b8f2fc7cd5f
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72513528"
---
# <a name="azcopy-list"></a>azcopy list

지정 된 리소스의 엔터티를 나열 합니다.

## <a name="synopsis"></a>개요

현재 릴리스에서는 Blob 컨테이너만 지원 됩니다.

```azcopy
azcopy list [containerURL] [flags]
```

## <a name="examples"></a>예시

```azcopy
azcopy list [containerURL]
```

## <a name="options"></a>옵션

|옵션|설명|
|--|--|
|-h, --help|목록 명령에 대 한 도움말 콘텐츠를 표시 합니다.|
|--컴퓨터에서 읽을 수 있음|파일 크기 (바이트)를 나열 합니다.|
|--단위|1024이 아닌 1000의 주문 단위를 표시 합니다.|
|--실행 중-집계|총 파일 수와 해당 크기를 계산 합니다.|

## <a name="options-inherited-from-parent-commands"></a>부모 명령에서 상속 된 옵션

|옵션|설명|
|---|---|
|--0mbps uint32|전송 률 (메가 비트/초)을 대문자로 처리 합니다. 순간 처리량은 cap와 약간 다를 수 있습니다. 이 옵션을 0으로 설정 하거나 생략 하면 처리량이 생략 되지 않습니다.|
|--출력 형식 문자열|명령의 출력 형식입니다. 텍스트, json 등을 선택할 수 있습니다. 기본값은 "text"입니다.|

## <a name="see-also"></a>참고 항목

- [azcopy](storage-ref-azcopy.md)
