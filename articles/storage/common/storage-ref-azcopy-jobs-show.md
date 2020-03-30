---
title: azcopy 작업 표시 | 마이크로 소프트 문서
description: 이 문서에서는 azcopy 작업 표시 명령에 대 한 참조 정보를 제공 합니다.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 7b5f566757dd77a61f252b123d0c9c1b74303fbe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74034130"
---
# <a name="azcopy-jobs-show"></a>azcopy jobs show

지정된 작업 ID에 대한 자세한 정보를 표시합니다.

## <a name="synopsis"></a>개요

작업 ID만 플래그 없이 제공 되는 경우 작업의 진행 률 요약 반환 됩니다.

이 명령을 실행할 때 나타나는 바이트 수 및 백분율 완료는 작업에서 완료된 파일만 반영합니다. 부분적으로 완료된 파일은 반영되지 않습니다.

플래그가 `with-status` 설정되면 지정된 값이 있는 작업의 전송 목록이 표시됩니다.

```azcopy
azcopy jobs show [jobID] [flags]
```

## <a name="related-conceptual-articles"></a>관련 개념 기사

- [AzCopy 시작](storage-use-azcopy-v10.md)
- [AzCopy 및 Blob 저장소로 데이터 전송](storage-use-azcopy-blobs.md)
- [AzCopy 및 파일 스토리지를 사용하여 데이터 전송](storage-use-azcopy-files.md)
- [AzCopy 구성, 최적화 및 문제 해결](storage-use-azcopy-configure.md)

## <a name="options"></a>옵션

|옵션|설명|
|--|--|
|-h, --help|쇼 명령에 대한 도움말 콘텐츠를 표시합니다.|
|--상태 문자열|이 상태, 사용 가능한 값으로 작업 전송만 나열됩니다: 시작됨, 성공, 실패|

## <a name="options-inherited-from-parent-commands"></a>상위 명령에서 상속된 옵션

|옵션|설명|
|---|---|
|--캡 mbps uint32|초당 메가비트로 전송 속도를 한도 를 캡슐화합니다. 모멘트별 처리량은 캡과 약간 다를 수 있습니다. 이 옵션이 0으로 설정되어 있거나 생략된 경우 처리량은 제한되지 않습니다.|
|--출력 유형 문자열|명령 출력의 형식입니다. 선택 사항은 다음과 같습니다: 텍스트, json. 기본값은 "텍스트"입니다.|

## <a name="see-also"></a>참조

- [azcopy jobs](storage-ref-azcopy-jobs.md)
