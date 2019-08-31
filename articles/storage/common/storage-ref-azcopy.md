---
title: azcopy | Microsoft Docs
description: 이 문서에서는 azcopy 명령에 대 한 참조 정보를 제공 합니다.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 4da9206f4500941179d781a0fe2a57ad15d7393d
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195894"
---
# <a name="azcopy"></a>azcopy

AzCopy는 Azure Storage 데이터를 이동 하거나 외부로 이동 하는 명령줄 도구입니다.

## <a name="synopsis"></a>개요

명령의 일반적인 형식은 `azcopy [command] [arguments] --[flag-name]=[flag-value]`입니다.

문제를 보고 하거나 도구에 대해 자세히 알아보려면를 참조 [https://github.com/Azure/azure-storage-azcopy](https://github.com/Azure/azure-storage-azcopy)하십시오.

## <a name="options"></a>옵션

|옵션|설명|
|---|---|
|--0mbps uint32|전송 률 (메가 비트/초)을 대문자로 처리 합니다. 순간 처리량은 cap와 약간 다를 수 있습니다. 이 옵션을 0으로 설정 하거나 생략 하면 처리량이 생략 되지 않습니다.|
|-h, --help|Azcopy에 대 한 도움말 콘텐츠를 표시 합니다.|
|--출력 형식 문자열|명령의 출력 형식입니다. 텍스트, json 등을 선택할 수 있습니다. 기본값은 "text"입니다.|

## <a name="see-also"></a>참고자료

- [AzCopy 시작](storage-use-azcopy-v10.md)
- [azcopy 복사](storage-ref-azcopy-copy.md)
- [azcopy doc](storage-ref-azcopy-doc.md)
- [azcopy env](storage-ref-azcopy-env.md)
- [azcopy 작업](storage-ref-azcopy-jobs.md)
- [azcopy 목록](storage-ref-azcopy-list.md)
- [azcopy 로그인](storage-ref-azcopy-login.md)
- [azcopy 로그 아웃](storage-ref-azcopy-logout.md)
- [azcopy 만들기](storage-ref-azcopy-make.md)
- [azcopy 제거](storage-ref-azcopy-remove.md)
- [azcopy 동기화](storage-ref-azcopy-sync.md)
