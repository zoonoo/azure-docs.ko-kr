---
title: azcopy 로그 아웃 | Microsoft Docs
description: 이 문서에서는 azcopy logout 명령에 대 한 참조 정보를 제공 합니다.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: f42e2517f8c40855e56bd062fe8bc9b22634d4dc
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195920"
---
# <a name="azcopy-logout"></a>azcopy 로그 아웃

사용자를 로그 아웃 하 고 Azure Storage 리소스에 대 한 액세스를 종료 합니다.

## <a name="synopsis"></a>개요

이 명령은 현재 사용자에 대해 캐시 된 모든 로그인 정보를 제거 합니다.

```azcopy
azcopy logout [flags]
```

## <a name="options"></a>옵션

|옵션|Description|
|--|--|
|-h, --help|로그 아웃 명령에 대 한 도움말 콘텐츠를 표시 합니다.|

## <a name="options-inherited-from-parent-commands"></a>부모 명령에서 상속 된 옵션

|옵션|설명|
|---|---|
|--0mbps uint32|전송 률 (메가 비트/초)을 대문자로 처리 합니다. 순간 처리량은 cap와 약간 다를 수 있습니다. 이 옵션을 0으로 설정 하거나 생략 하면 처리량이 생략 되지 않습니다.|
|--출력 형식 문자열|명령의 출력 형식입니다. 텍스트, json 등을 선택할 수 있습니다. 기본값은 "text"입니다.|

## <a name="see-also"></a>참고자료

- [azcopy](storage-ref-azcopy.md)
