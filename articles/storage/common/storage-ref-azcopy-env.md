---
title: 아즈카피 엔브 | 마이크로 소프트 문서
description: 이 문서에서는 azcopy env 명령에 대한 참조 정보를 제공합니다.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: cc0ed5f1eec76bedc21106c90e5e82332e27ce3c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74033757"
---
# <a name="azcopy-env"></a>azcopy env

AzCopy의 동작을 구성할 수 있는 환경 변수를 표시합니다.

## <a name="synopsis"></a>개요

```azcopy
azcopy env [flags]
```

> [!IMPORTANT]
> 명령줄을 사용하여 환경 변수를 설정하면 명령줄 기록에서 해당 변수를 읽을 수 있습니다. 명령줄 기록의 자격 증명이 포함된 변수를 지우는 것이 좋습니다. 기록에 변수가 나타나지 않도록 하려면 스크립트를 사용하여 사용자에게 자격 증명을 묻고 환경 변수를 설정할 수 있습니다.

## <a name="related-conceptual-articles"></a>관련 개념 기사

- [AzCopy 시작](storage-use-azcopy-v10.md)
- [AzCopy 및 Blob 저장소로 데이터 전송](storage-use-azcopy-blobs.md)
- [AzCopy 및 파일 스토리지를 사용하여 데이터 전송](storage-use-azcopy-files.md)
- [AzCopy 구성, 최적화 및 문제 해결](storage-use-azcopy-configure.md)

## <a name="options"></a>옵션

|옵션|설명|
|--|--|
|-h, --help|env 명령에 대한 도움말 콘텐츠를 표시합니다. |
|--쇼에 민감한|중요/비밀 환경 변수를 표시합니다.|

## <a name="options-inherited-from-parent-commands"></a>상위 명령에서 상속된 옵션

|옵션|설명|
|---|---|
|--캡 mbps uint32|초당 메가비트로 전송 속도를 한도 를 캡슐화합니다. 모멘트별 처리량은 캡과 약간 다를 수 있습니다. 이 옵션이 0으로 설정되어 있거나 생략된 경우 처리량은 제한되지 않습니다.|
|--출력 유형 문자열|명령 출력의 형식입니다. 선택 사항은 다음과 같습니다: 텍스트, json. 기본값은 "텍스트"입니다.|

## <a name="see-also"></a>참조

- [아즈카피](storage-ref-azcopy.md)
