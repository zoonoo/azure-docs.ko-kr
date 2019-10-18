---
title: azcopy 작업 정리 | Microsoft Docs
description: 이 문서에서는 azcopy jobs clean 명령에 대 한 참조 정보를 제공 합니다.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 31529155ee44b2bcfad90e8634053403dfe8fc8c
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72518513"
---
# <a name="azcopy-jobs-clean"></a>azcopy 작업 정리

모든 작업에 대 한 모든 로그 및 계획 파일 제거

```
azcopy jobs clean [flags]
```

## <a name="examples"></a>예시

```
  azcopy jobs clean --with-status=completed
```

## <a name="options"></a>옵션

**-h,--help**                정리에 대 한 도움말입니다.

-- **상태** 문자열을 사용 하는 경우에만이 상태의 작업을 제거할 수 있으며, 사용 가능한 값은 취소 됨, 완료 됨, 실패, InProgress, 모두 (기본값 "모두")입니다.

## <a name="options-inherited-from-parent-commands"></a>부모 명령에서 상속 된 옵션

**--0mbps uint32**      전송 률 (메가 비트/초)을 대문자로 처리 합니다. 순간 처리량은 cap와 약간 다를 수 있습니다. 이 옵션을 0으로 설정 하거나 생략 하면 처리량이 생략 되지 않습니다.

**--output-** 명령 출력의 문자열 형식입니다. 텍스트, json 등을 선택할 수 있습니다. 기본값은 ' text '입니다. (기본 "텍스트")

## <a name="see-also"></a>참고 항목

- [azcopy 작업](storage-ref-azcopy-jobs.md)
