---
title: azcopy 만들기 | Microsoft Docs
description: 이 문서에서는 azcopy make 명령에 대한 참조 정보를 제공합니다.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 1e5b142d36266d5b6a333a0fa5cd233bac71849e
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503034"
---
# <a name="azcopy-make"></a>azcopy make

컨테이너 또는 파일 공유를 만듭니다.

## <a name="synopsis"></a>개요

지정된 리소스 URL로 표시되는 컨테이너 또는 파일 공유를 만듭니다.

```azcopy
azcopy make [resourceURL] [flags]
```

## <a name="related-conceptual-articles"></a>관련 개념 문서

- [AzCopy 시작](storage-use-azcopy-v10.md)
- [AzCopy 및 Blob 스토리지를 사용하여 데이터 전송](./storage-use-azcopy-v10.md#transfer-data)
- [AzCopy 및 File Storage를 사용하여 데이터 전송](storage-use-azcopy-files.md)

## <a name="examples"></a>예

```azcopy
azcopy make "https://[account-name].[blob,file,dfs].core.windows.net/[top-level-resource-name]"
```

## <a name="options"></a>옵션

|옵션|Description|
|--|--|
|-h, --help|make 명령에 대한 도움말 콘텐츠를 표시합니다. |
|--quota-gb uint32|공유의 최대 크기(GB)를 지정합니다. 0은 파일 서비스의 기본 할당량을 그대로 사용함을 의미합니다.|

## <a name="options-inherited-from-parent-commands"></a>부모 명령에서 상속된 옵션

|옵션|Description|
|---|---|
|--cap-mbps float|전송 속도(초당 메가비트)의 한도를 지정합니다. 시간 경과별 처리량은 한도와 약간 다를 수 있습니다. 이 옵션이 0으로 설정되거나 생략되는 경우 처리량이 제한되지 않습니다.|
|--output-type string|명령의 출력 형식입니다. text, json 등을 선택할 수 있습니다. 기본값은 ‘text’입니다.|
|--trusted-microsoft-suffixes string   |Azure Active Directory 로그인 토큰이 전송될 수 있는 추가 도메인 접미사를 지정합니다.  기본값은 ' *.core.windows.net;* .core.chinacloudapi.cn; *.core.cloudapi.de;* .core.usgovcloudapi.net'입니다. 여기에 나열된 항목은 모두 기본값에 추가됩니다. 보안을 위해 여기에는 Microsoft Azure 도메인만 배치해야 합니다. 여러 항목은 세미콜론으로 구분됩니다.|

## <a name="see-also"></a>참조

- [azcopy](storage-ref-azcopy.md)
