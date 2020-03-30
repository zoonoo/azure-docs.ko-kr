---
title: azcopy 작업 재개 | 마이크로 소프트 문서
description: 이 문서에서는 azcopy 작업 다시 시작 명령에 대 한 참조 정보를 제공 합니다.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 3ee7879475801660b5200dddca88a0a81b2b6b9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74034152"
---
# <a name="azcopy-jobs-resume"></a>azcopy jobs resume

지정된 작업 ID로 기존 작업을 다시 시작합니다.

## <a name="synopsis"></a>개요

```azcopy
azcopy jobs resume [jobID] [flags]
```

## <a name="related-conceptual-articles"></a>관련 개념 기사

- [AzCopy 시작](storage-use-azcopy-v10.md)
- [AzCopy 및 Blob 저장소로 데이터 전송](storage-use-azcopy-blobs.md)
- [AzCopy 및 파일 스토리지를 사용하여 데이터 전송](storage-use-azcopy-files.md)
- [AzCopy 구성, 최적화 및 문제 해결](storage-use-azcopy-configure.md)

## <a name="options"></a>옵션

|옵션|설명|
|--|--|
|--대상-sas 문자열|지정된 JobId에 대한 대상의 대상 SAS입니다.|
|--문자열 제외|필터: 작업을 다시 시작할 때 실패한 전송을 제외합니다. 파일은 ';'로 구분되어야 합니다.|
|-h, --help|이력서 명령에 대한 도움말 콘텐츠를 표시합니다.|
|--문자열 포함|필터: 작업을 다시 시작할 때 실패한 전송만 포함합니다. 파일은 ';'로 구분되어야 합니다.|
|--소스 sas 문자열 |주어진 JobId에 대한 소스 SAS.|

## <a name="options-inherited-from-parent-commands"></a>상위 명령에서 상속된 옵션

|옵션|설명|
|---|---|
|--캡 mbps uint32|초당 메가비트로 전송 속도를 한도 를 캡슐화합니다. 모멘트별 처리량은 캡과 약간 다를 수 있습니다. 이 옵션이 0으로 설정되어 있거나 생략된 경우 처리량은 제한되지 않습니다.|
|--출력 유형 문자열|명령 출력의 형식입니다. 선택 사항은 다음과 같습니다: 텍스트, json. 기본값은 "텍스트"입니다.|

## <a name="see-also"></a>참조

- [azcopy jobs](storage-ref-azcopy-jobs.md)
