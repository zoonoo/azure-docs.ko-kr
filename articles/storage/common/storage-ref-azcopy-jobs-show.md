---
title: azcopy 작업 표시 | Microsoft Docs
description: 이 문서에서는 azcopy jobs show 명령에 대 한 참조 정보를 제공 합니다.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: d4369bd0c986ee20a0796436fea47509a711de4f
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195959"
---
# <a name="azcopy-jobs-show"></a>azcopy 작업 표시

지정 된 작업 ID에 대 한 자세한 정보를 표시 합니다.

## <a name="synopsis"></a>개요

작업 ID만 플래그 없이 제공 되는 경우 작업의 진행률 요약이 반환 됩니다.

`with-status` 플래그가 설정 된 경우 작업의 전송 목록이 지정 된 값으로 표시 됩니다.

```azcopy
azcopy jobs show [jobID] [flags]
```

## <a name="options"></a>변수

|옵션|Description|
|--|--|
|-h, --help|표시 명령에 대 한 도움말 콘텐츠를 표시 합니다.|
|--상태 문자열|이 상태의 작업 전송만 나열 되며, 사용 가능한 값은 다음과 같습니다. 시작 됨, 성공, 실패|

## <a name="options-inherited-from-parent-commands"></a>부모 명령에서 상속 된 옵션

|옵션|Description|
|---|---|
|--0mbps uint32|전송 률 (메가 비트/초)을 대문자로 처리 합니다. 순간 처리량은 cap와 약간 다를 수 있습니다. 이 옵션을 0으로 설정 하거나 생략 하면 처리량이 생략 되지 않습니다.|
|--출력 형식 문자열|명령의 출력 형식입니다. 텍스트, json 등을 선택할 수 있습니다. 기본값은 "text"입니다.|

## <a name="see-also"></a>참고자료

- [azcopy 작업](storage-ref-azcopy-jobs.md)
