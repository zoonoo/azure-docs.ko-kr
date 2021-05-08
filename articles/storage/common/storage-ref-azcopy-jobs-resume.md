---
title: azcopy jobs resume | Microsoft Docs
description: 이 문서에서는 azcopy jobs resume 명령에 관한 참조 정보를 제공합니다.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 497aa3a77397a8a587badc0911176cd092eb1f23
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503340"
---
# <a name="azcopy-jobs-resume"></a>azcopy jobs resume

지정된 작업 ID를 사용하여 기존 작업을 다시 시작합니다.

## <a name="synopsis"></a>개요

```azcopy
azcopy jobs resume [jobID] [flags]
```

## <a name="related-conceptual-articles"></a>관련 개념 문서

- [AzCopy 시작](storage-use-azcopy-v10.md)
- [AzCopy 및 Blob 스토리지를 사용하여 데이터 전송](./storage-use-azcopy-v10.md#transfer-data)
- [AzCopy 및 File Storage를 사용하여 데이터 전송](storage-use-azcopy-files.md)

## <a name="options"></a>옵션

|옵션|Description|
|--|--|
|--destination-sas string|지정된 작업 ID에 대한 대상의 대상 SAS입니다.|
|--exclude string|필터: 작업을 다시 시작할 때 실패한 전송 제외합니다. 파일은 ‘;’으로 구분해야 합니다.|
|-h, --help|resume 명령의 도움말 콘텐츠를 표시합니다.|
|--include string|필터: 작업을 다시 시작할 때 실패한 전송만 포함합니다. 파일은 ‘;’으로 구분해야 합니다.|
|--source-sas string |지정된 작업 ID에 대한 원본의 원본 SAS입니다.|

## <a name="options-inherited-from-parent-commands"></a>부모 명령에서 상속된 옵션

|옵션|Description|
|---|---|
|--cap-mbps float|전송 속도(Mbps)의 한도를 지정합니다. 시간 경과별 처리량은 한도와 약간 다를 수 있습니다. 이 옵션이 0으로 설정되거나 생략되는 경우 처리량이 제한되지 않습니다.|
|--output-type string|명령 출력의 형식을 지정합니다. text, json을 선택할 수 있습니다. 기본값은 “text”입니다.|
|--trusted-microsoft-suffixes string   |Azure Active Directory 로그인 토큰이 전송될 수 있는 추가 도메인 접미사를 지정합니다.  기본값은 ‘ *.core.windows.net;* .core.chinacloudapi.cn; *.core.cloudapi.de;* .core.usgovcloudapi.net’입니다. 여기에 나열된 항목은 모두 기본값에 추가됩니다. 보안을 위해 여기에는 Microsoft Azure 도메인만 배치해야 합니다. 여러 항목은 세미콜론으로 구분합니다.|

## <a name="see-also"></a>참조

- [azcopy jobs](storage-ref-azcopy-jobs.md)
