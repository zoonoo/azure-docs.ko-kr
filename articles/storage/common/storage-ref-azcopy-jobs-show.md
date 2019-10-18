---
title: azcopy 작업 표시 | Microsoft Docs
description: 이 문서에서는 azcopy jobs show 명령에 대 한 참조 정보를 제공 합니다.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 42b8bde9adb6980ff2c7004d43b02fc1fdc38363
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72513458"
---
# <a name="azcopy-jobs-show"></a>azcopy jobs show

지정 된 작업 ID에 대 한 자세한 정보를 표시 합니다.

## <a name="synopsis"></a>개요

작업 ID만 플래그 없이 제공 되는 경우 작업의 진행률 요약이 반환 됩니다.

이 명령을 실행할 때 표시 되는 바이트 수 및 완료율은 작업에서 완료 된 파일만 반영 합니다. 부분적으로 완료 된 파일은 반영 되지 않습니다.

@No__t_0 플래그가 설정 되 면 지정 된 값을 가진 작업의 전송 목록이 표시 됩니다.

```azcopy
azcopy jobs show [jobID] [flags]
```

## <a name="options"></a>옵션

|옵션|설명|
|--|--|
|-h, --help|표시 명령에 대 한 도움말 콘텐츠를 표시 합니다.|
|--상태 문자열|이 상태의 작업 전송만 나열, 사용 가능한 값: 시작 됨, 성공, 실패|

## <a name="options-inherited-from-parent-commands"></a>부모 명령에서 상속 된 옵션

|옵션|설명|
|---|---|
|--0mbps uint32|전송 률 (메가 비트/초)을 대문자로 처리 합니다. 순간 처리량은 cap와 약간 다를 수 있습니다. 이 옵션을 0으로 설정 하거나 생략 하면 처리량이 생략 되지 않습니다.|
|--출력 형식 문자열|명령의 출력 형식입니다. 텍스트, json 등을 선택할 수 있습니다. 기본값은 "text"입니다.|

## <a name="see-also"></a>참고 항목

- [azcopy 작업](storage-ref-azcopy-jobs.md)
