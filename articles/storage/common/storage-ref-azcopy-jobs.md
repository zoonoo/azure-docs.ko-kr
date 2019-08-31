---
title: azcopy 작업 | Microsoft Docs
description: 이 문서에서는 azcopy jobs 명령에 대 한 참조 정보를 제공 합니다.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: bd5469691f8d859bfcbca7479cdcb2e468a94e26
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195842"
---
# <a name="azcopy-jobs"></a>azcopy 작업

작업 관리와 관련 된 하위 명령입니다.

## <a name="examples"></a>예

```azcopy
azcopy jobs show [jobID]
```

## <a name="options"></a>변수

|옵션|Description|
|--|--|
|-h, --help|작업 명령에 대 한 도움말 콘텐츠를 표시 합니다.|

## <a name="options-inherited-from-parent-commands"></a>부모 명령에서 상속 된 옵션

|옵션|설명|
|---|---|
|--0mbps uint32|전송 률 (메가 비트/초)을 대문자로 처리 합니다. 순간 처리량은 cap와 약간 다를 수 있습니다. 이 옵션을 0으로 설정 하거나 생략 하면 처리량이 생략 되지 않습니다.|
|--출력 형식 문자열|명령의 출력 형식입니다. 텍스트, json 등을 선택할 수 있습니다. 기본값은 "text"입니다.|

## <a name="see-also"></a>참고자료

- [azcopy](storage-ref-azcopy.md)
- [azcopy 작업 목록](storage-ref-azcopy-jobs-list.md)
- [azcopy 작업 다시 시작](storage-ref-azcopy-jobs-resume.md)
- [azcopy 작업 표시](storage-ref-azcopy-jobs-show.md)
