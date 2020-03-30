---
title: 아즈카피 작업 청소 | 마이크로 소프트 문서
description: 이 문서에서는 azcopy 작업 정리 명령에 대한 참조 정보를 제공합니다.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 7ae14c3606dfe6bffa8481682843f3f2e85c2131
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74033714"
---
# <a name="azcopy-jobs-clean"></a>azcopy jobs clean

모든 작업에 대한 모든 로그 및 계획 파일 제거

```
azcopy jobs clean [flags]
```

## <a name="related-conceptual-articles"></a>관련 개념 기사

- [AzCopy 시작](storage-use-azcopy-v10.md)
- [AzCopy 및 Blob 저장소로 데이터 전송](storage-use-azcopy-blobs.md)
- [AzCopy 및 파일 스토리지를 사용하여 데이터 전송](storage-use-azcopy-files.md)
- [AzCopy 구성, 최적화 및 문제 해결](storage-use-azcopy-configure.md)

## <a name="examples"></a>예

```
  azcopy jobs clean --with-status=completed
```

## <a name="options"></a>옵션

**-h, --도움말**                청소를 위한 도움말입니다.

**--상태** 문자열만이 상태, 사용 가능한 값으로 작업을 제거: 취소, 완료, 실패, 진행 중, 모두 (기본값 "모두")

## <a name="options-inherited-from-parent-commands"></a>상위 명령에서 상속된 옵션

**--캡 mbps uint32**      초당 메가비트로 전송 속도를 한도 를 캡슐화합니다. 모멘트별 처리량은 캡과 약간 다를 수 있습니다. 이 옵션이 0으로 설정되어 있거나 생략된 경우 처리량은 제한되지 않습니다.

**--출력 유형** 문자열 명령출력의 형식입니다. 선택 사항은 다음과 같습니다: 텍스트, json. 기본값은 '텍스트'입니다. (기본값 "텍스트")

## <a name="see-also"></a>참조

- [azcopy jobs](storage-ref-azcopy-jobs.md)
