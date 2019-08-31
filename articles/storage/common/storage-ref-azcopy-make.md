---
title: azcopy 만들기 | Microsoft Docs
description: 이 문서에서는 azcopy 만들기 명령에 대 한 참조 정보를 제공 합니다.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 08/26/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 9afcd8de1af42424649dd8e44fc07f7bfd881257
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195712"
---
# <a name="azcopy-make"></a>azcopy 만들기

컨테이너 또는 파일 공유를 만듭니다.

## <a name="synopsis"></a>개요

지정 된 리소스 URL로 표시 되는 컨테이너 또는 파일 공유를 만듭니다.

```azcopy
azcopy make [resourceURL] [flags]
```

## <a name="examples"></a>예

```azcopy
azcopy make "https://[account-name].[blob,file,dfs].core.windows.net/[top-level-resource-name]"
```

## <a name="options"></a>변수

|옵션|Description|
|--|--|
|-h, --help|만들기 명령에 대 한 도움말 콘텐츠를 표시 합니다. |
|--quota-gb uint32|공유의 최대 크기 (gb) (GiB)를 지정 합니다. 0은 파일 서비스의 기본 할당량을 그대로 사용 함을 의미 합니다.|

## <a name="options-inherited-from-parent-commands"></a>부모 명령에서 상속 된 옵션

|옵션|Description|
|---|---|
|--0mbps uint32|전송 률 (메가 비트/초)을 대문자로 처리 합니다. 순간 처리량은 cap와 약간 다를 수 있습니다. 이 옵션을 0으로 설정 하거나 생략 하면 처리량이 생략 되지 않습니다.|
|--출력 형식 문자열|명령의 출력 형식입니다. 텍스트, json 등을 선택할 수 있습니다. 기본값은 "text"입니다.|

## <a name="see-also"></a>참고자료

- [azcopy](storage-ref-azcopy.md)
