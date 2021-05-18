---
title: azcopy env | Microsoft Docs
description: 이 문서는 azcopy env 명령에 관한 참조 정보를 제공합니다.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 71a4c27b84a16a4acb37c196ccd8ee571c2b2468
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503493"
---
# <a name="azcopy-env"></a>azcopy env

AzCopy의 동작을 구성할 수 있는 환경 변수를 표시합니다. 환경 변수의 전체 목록은 [AzCopy v10 구성 설정(Azure Storage)](storage-ref-azcopy-configuration-settings.md)을 참조하세요.

## <a name="synopsis"></a>개요

```azcopy
azcopy env [flags]
```

> [!IMPORTANT]
> 명령줄을 사용하여 환경 변수를 설정하는 경우 명령줄 기록에서 해당 변수를 읽을 수 있습니다. 명령줄 기록에서 자격 증명을 포함하는 변수를 지우는 것이 좋습니다. 기록에 변수가 나타나지 않게 하려면 스크립트를 사용하여 사용자에게 자격 증명을 요청하는 메시지를 표시하고 환경 변수를 설정하면 됩니다.

## <a name="related-conceptual-articles"></a>관련 개념 문서

- [AzCopy 시작](storage-use-azcopy-v10.md)
- [AzCopy 및 Blob Storage를 사용하여 데이터 전송](./storage-use-azcopy-v10.md#transfer-data)
- [AzCopy 및 File Storage를 사용하여 데이터 전송](storage-use-azcopy-files.md)

## <a name="options"></a>옵션

|옵션|Description|
|--|--|
|-h, --help|env 명령에 대한 도움말 콘텐츠를 표시합니다. |
|--show-sensitive|중요/비밀 환경 변수를 표시합니다.|

## <a name="options-inherited-from-parent-commands"></a>부모 명령에서 상속된 옵션

|옵션|Description|
|---|---|
|--cap-mbps float|전송 속도(Mbps)의 한도를 지정합니다. 시간 경과별 처리량은 한도와 약간 다를 수 있습니다. 이 옵션이 0으로 설정되거나 생략되는 경우 처리량이 제한되지 않습니다.|
|--output-type string|명령 출력의 형식을 지정합니다. text, json을 선택할 수 있습니다. 기본값은 “text”입니다.|
|--trusted-microsoft-suffixes string  | Azure Active Directory 로그인 토큰이 전송될 수 있는 추가 도메인 접미사를 지정합니다.  기본값은 ‘ *.core.windows.net;* .core.chinacloudapi.cn; *.core.cloudapi.de;* .core.usgovcloudapi.net’입니다. 여기에 나열된 항목은 모두 기본값에 추가됩니다. 보안을 위해 여기에는 Microsoft Azure 도메인만 배치해야 합니다. 여러 항목은 세미콜론으로 구분합니다.|

## <a name="see-also"></a>참조

- [azcopy](storage-ref-azcopy.md)
