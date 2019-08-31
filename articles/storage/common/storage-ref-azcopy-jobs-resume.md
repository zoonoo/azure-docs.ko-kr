---
title: azcopy 작업 다시 시작 | Microsoft Docs
description: 이 문서에서는 azcopy jobs 다시 시작 명령에 대 한 참조 정보를 제공 합니다.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 8786da42f000a2f13279499159a7af424aa10748
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195790"
---
# <a name="azcopy-jobs-resume"></a>azcopy 작업 다시 시작

지정 된 작업 ID를 사용 하 여 기존 작업을 다시 시작 합니다.

## <a name="synopsis"></a>개요

```azcopy
azcopy jobs resume [jobID] [flags]
```

## <a name="options"></a>변수

|옵션|Description|
|--|--|
|--대상-sas 문자열|지정 된 JobId에 대 한 대상의 대상 SAS입니다.|
|--문자열 제외|필터가 작업을 다시 시작할 때 이러한 전송 실패를 제외 합니다. 파일은 '; '으로 구분 해야 합니다.|
|-h, --help|Resume 명령에 대 한 도움말 콘텐츠를 표시 합니다.|
|--문자열 포함|필터: 작업을 다시 시작할 때 이러한 실패 한 전송만 포함 합니다. 파일은 '; '으로 구분 해야 합니다.|
|--소스-sas 문자열 |지정 된 JobId의 원본 SAS입니다.|

## <a name="options-inherited-from-parent-commands"></a>부모 명령에서 상속 된 옵션

|옵션|Description|
|---|---|
|--0mbps uint32|전송 률 (메가 비트/초)을 대문자로 처리 합니다. 순간 처리량은 cap와 약간 다를 수 있습니다. 이 옵션을 0으로 설정 하거나 생략 하면 처리량이 생략 되지 않습니다.|
|--출력 형식 문자열|명령의 출력 형식입니다. 텍스트, json 등을 선택할 수 있습니다. 기본값은 "text"입니다.|

## <a name="see-also"></a>참고자료

- [azcopy 작업](storage-ref-azcopy-jobs.md)
