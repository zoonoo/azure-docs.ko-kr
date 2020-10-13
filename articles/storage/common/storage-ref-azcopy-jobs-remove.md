---
title: azcopy 작업 제거 | Microsoft Docs
description: 이 문서에서는 azcopy job remove 명령에 대 한 참조 정보를 제공 합니다.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: b1f1b58e9cce061aaa313457ec43256a766e3a2e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87281976"
---
# <a name="azcopy-jobs-remove"></a>azcopy jobs remove

지정 된 작업 ID와 연결 된 모든 파일을 제거 합니다.

> [!NOTE] 
> 로그 및 계획 파일이 저장 되는 위치를 사용자 지정할 수 있습니다. 자세히 알아보려면 [azcopy env](storage-ref-azcopy-env.md) 명령을 참조 하세요.

```
azcopy jobs remove [jobID] [flags]
```

## <a name="related-conceptual-articles"></a>관련 개념 문서

- [AzCopy 시작](storage-use-azcopy-v10.md)
- [AzCopy 및 Blob 저장소를 사용 하 여 데이터 전송](storage-use-azcopy-blobs.md)
- [AzCopy 및 File Storage를 사용하여 데이터 전송](storage-use-azcopy-files.md)
- [AzCopy 구성, 최적화 및 문제 해결](storage-use-azcopy-configure.md)

## <a name="examples"></a>예

```
  azcopy jobs rm e52247de-0323-b14d-4cc8-76e0be2e2d44
```

## <a name="options"></a>옵션

**--도움말**                제거에 대 한 도움말입니다.

## <a name="options-inherited-from-parent-commands"></a>부모 명령에서 상속 된 옵션

**--0mbps float**      전송 률 (메가 비트/초)을 대문자로 처리 합니다. 순간 처리량은 cap와 약간 다를 수 있습니다. 이 옵션을 0으로 설정 하거나 생략 하면 처리량이 생략 되지 않습니다.

**--output-** 명령 출력의 문자열 형식입니다. 텍스트, json 등을 선택할 수 있습니다. 기본값은 `text`입니다. (기본값 `text` )

**--trusted-microsoft-접미사** 문자열 Azure Active Directory 로그인 토큰이 전송 될 수 있는 추가 도메인 접미사를 지정 합니다.  기본값은 '*. core.windows.net;* 입니다. core.chinacloudapi.cn; *. core.cloudapi.de;*. core.usgovcloudapi.net '. 여기에 나열 된 Any는 기본값에 추가 됩니다. 보안을 위해 여기에 Microsoft Azure 도메인만 배치 해야 합니다. 여러 항목을 세미콜론으로 구분 합니다.

## <a name="see-also"></a>참고 항목

- [azcopy jobs](storage-ref-azcopy-jobs.md)
