---
title: azcopy 작업 제거 | Microsoft Docs
description: 이 문서에서는 azcopy job remove 명령에 대 한 참조 정보를 제공 합니다.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 82c399580322334e67c0c9c2b88d1edf6f175e0c
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72518292"
---
# <a name="azcopy-jobs-remove"></a>azcopy 작업 제거

지정 된 작업 ID와 연결 된 모든 파일을 제거 합니다.

> [!NOTE] 
> 로그 및 계획 파일이 저장 되는 위치를 사용자 지정할 수 있습니다. 자세히 알아보려면 [azcopy env](storage-ref-azcopy-env.md) 명령을 참조 하세요.

```
azcopy jobs remove [jobID] [flags]
```

## <a name="examples"></a>예시

```
  azcopy jobs rm e52247de-0323-b14d-4cc8-76e0be2e2d44
```

## <a name="options"></a>옵션

**-h,--help**                제거에 대 한 도움말입니다.

## <a name="options-inherited-from-parent-commands"></a>부모 명령에서 상속 된 옵션

**--0mbps uint32**      전송 률 (메가 비트/초)을 대문자로 처리 합니다. 순간 처리량은 cap와 약간 다를 수 있습니다. 이 옵션을 0으로 설정 하거나 생략 하면 처리량이 생략 되지 않습니다.

**--output-** 명령 출력의 문자열 형식입니다. 텍스트, json 등을 선택할 수 있습니다. 기본값은 ' text '입니다. (기본 "텍스트")

## <a name="see-also"></a>참고 항목

- [azcopy 작업](storage-ref-azcopy-jobs.md)
