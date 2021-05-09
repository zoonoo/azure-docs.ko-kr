---
title: azcopy jobs show | Microsoft Docs
description: 이 문서에서는 azcopy jobs show 명령에 대한 참조 정보를 제공합니다.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: a14546d8bfc50531902b5150d38ee5ce8b8b5769
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503357"
---
# <a name="azcopy-jobs-show"></a>azcopy jobs show

지정된 작업 ID에 대한 상세 정보를 표시합니다.

## <a name="synopsis"></a>개요

플래그 없이 작업 ID만 제공되는 경우에는 작업의 진행률 요약이 반환됩니다.

이 명령을 실행할 때 표시되는 바이트 수 및 완료율은 작업에서 완료된 파일만 반영합니다. 부분적으로 완료된 파일은 반영되지 않습니다.

`with-status` 플래그가 설정된 경우 지정된 값이 있는 작업 내 전송 목록이 표시됩니다.

```azcopy
azcopy jobs show [jobID] [flags]
```

## <a name="related-conceptual-articles"></a>관련 개념 문서

- [AzCopy 시작](storage-use-azcopy-v10.md)
- [AzCopy 및 Blob Storage를 사용하여 데이터 전송](./storage-use-azcopy-v10.md#transfer-data)
- [AzCopy 및 File Storage를 사용하여 데이터 전송](storage-use-azcopy-files.md)

## <a name="options"></a>옵션

|옵션|Description|
|--|--|
|-h, --help|show 명령에 대한 도움말 콘텐츠를 표시합니다.|
|--with-status 문자열|이 상태의 작업 전송만 나열합니다. 사용 가능한 값은 Started, Success, Failed입니다.|

## <a name="options-inherited-from-parent-commands"></a>부모 명령에서 상속된 옵션

|옵션|Description|
|---|---|
|--cap-mbps float|전송 속도(초당 메가비트)의 한도를 지정합니다. 시간 경과별 처리량은 한도와 약간 다를 수 있습니다. 이 옵션이 0으로 설정되거나 생략되는 경우 처리량이 제한되지 않습니다.|
|--output-type 문자열|명령의 출력 형식입니다. text, json 등을 선택할 수 있습니다. 기본값은 ‘text’입니다.|
|--trusted-microsoft-suffixes 문자열   |Azure Active Directory 로그인 토큰이 전송될 수 있는 추가 도메인 접미사를 지정합니다.  기본값은 ' *.core.windows.net;* .core.chinacloudapi.cn; *.core.cloudapi.de;* .core.usgovcloudapi.net'입니다. 여기에 나열된 항목은 모두 기본값에 추가됩니다. 보안을 위해 여기에는 Microsoft Azure 도메인만 배치해야 합니다. 여러 항목은 세미콜론으로 구분됩니다.|

## <a name="see-also"></a>참조

- [azcopy jobs](storage-ref-azcopy-jobs.md)
