---
title: azcopy 작업 제거 | 마이크로 소프트 문서
description: 이 문서에서는 azcopy 작업 제거 명령에 대한 참조 정보를 제공합니다.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: ba403c8d823b7ead0414521ebd51dc6f6601ccd8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74034169"
---
# <a name="azcopy-jobs-remove"></a>azcopy jobs remove

지정된 작업 ID와 연결된 모든 파일을 제거합니다.

> [!NOTE] 
> 로그 및 계획 파일이 저장되는 위치를 사용자 지정할 수 있습니다. 자세한 내용은 [azcopy env](storage-ref-azcopy-env.md) 명령을 참조하십시오.

```
azcopy jobs remove [jobID] [flags]
```

## <a name="related-conceptual-articles"></a>관련 개념 기사

- [AzCopy 시작](storage-use-azcopy-v10.md)
- [AzCopy 및 Blob 저장소로 데이터 전송](storage-use-azcopy-blobs.md)
- [AzCopy 및 파일 스토리지를 사용하여 데이터 전송](storage-use-azcopy-files.md)
- [AzCopy 구성, 최적화 및 문제 해결](storage-use-azcopy-configure.md)

## <a name="examples"></a>예

```
  azcopy jobs rm e52247de-0323-b14d-4cc8-76e0be2e2d44
```

## <a name="options"></a>옵션

**-h, --도움말**                제거에 대 한 도움말입니다.

## <a name="options-inherited-from-parent-commands"></a>상위 명령에서 상속된 옵션

**--캡 mbps uint32**      초당 메가비트로 전송 속도를 한도 를 캡슐화합니다. 모멘트별 처리량은 캡과 약간 다를 수 있습니다. 이 옵션이 0으로 설정되어 있거나 생략된 경우 처리량은 제한되지 않습니다.

**--출력 유형** 문자열 명령출력의 형식입니다. 선택 사항은 다음과 같습니다: 텍스트, json. 기본값은 '텍스트'입니다. (기본값 "텍스트")

## <a name="see-also"></a>참조

- [azcopy jobs](storage-ref-azcopy-jobs.md)
