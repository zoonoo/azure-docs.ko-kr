---
title: azcopy doc | Microsoft Docs
description: 이 문서에서는 azcopy doc 명령에 대 한 참조 정보를 제공 합니다.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: b11d831be847716d4edfac4f9a0725e07a60c52a
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72513858"
---
# <a name="azcopy-doc"></a>azcopy doc

Markdown 형식으로 도구에 대 한 설명서를 생성 합니다.

## <a name="synopsis"></a>개요

Markdown 형식으로 도구에 대 한 설명서를 생성 하 고 지정 된 위치에 저장 합니다.

기본적으로 파일은 현재 디렉터리 내에서 ' doc ' 라는 폴더에 저장 됩니다.

```azcopy
azcopy doc [flags]
```

## <a name="options"></a>옵션

|옵션|설명|
|--|--|
|-h, --help|문서 명령에 대 한 도움말 콘텐츠를 표시 합니다.|

## <a name="options-inherited-from-parent-commands"></a>부모 명령에서 상속 된 옵션

|옵션|설명|
|---|---|
|--0mbps uint32|전송 률 (메가 비트/초)을 대문자로 처리 합니다. 순간 처리량은 cap와 약간 다를 수 있습니다. 이 옵션을 0으로 설정 하거나 생략 하면 처리량이 생략 되지 않습니다.|
|--출력 형식 문자열|명령의 출력 형식입니다. 텍스트, json 등을 선택할 수 있습니다. 기본값은 "text"입니다.|

## <a name="see-also"></a>참고 항목

- [azcopy](storage-ref-azcopy.md)
