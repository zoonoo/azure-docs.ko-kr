---
title: azcopy | Microsoft Docs
description: 이 문서에서는 azcopy 명령에 대 한 참조 정보를 제공 합니다.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 5da3a2e5d003a191bff66af6599cae4d34ab60c6
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74038082"
---
# <a name="azcopy"></a>azcopy

AzCopy는 Azure Storage 데이터를 이동 하거나 외부로 이동 하는 명령줄 도구입니다.

## <a name="synopsis"></a>개요

명령의 일반적인 형식은 `azcopy [command] [arguments] --[flag-name]=[flag-value]`입니다.

문제를 보고 하거나 도구에 대해 자세히 알아보려면 [https://github.com/Azure/azure-storage-azcopy](https://github.com/Azure/azure-storage-azcopy)를 참조 하세요.

## <a name="related-conceptual-articles"></a>관련 개념 문서

- [AzCopy 시작](storage-use-azcopy-v10.md)
- [AzCopy 및 Blob 저장소를 사용 하 여 데이터 전송](storage-use-azcopy-blobs.md)
- [AzCopy 및 파일 스토리지를 사용하여 데이터 전송](storage-use-azcopy-files.md)
- [AzCopy 구성, 최적화 및 문제 해결](storage-use-azcopy-configure.md)

## <a name="options"></a>옵션

**--0mbps uint32**   전송 률 (메가 비트/초)을 대문자로 처리 합니다. 순간 처리량은 cap와 약간 다를 수 있습니다. 이 옵션을 0으로 설정 하거나 생략 하면 처리량이 생략 되지 않습니다.

**-h,--help** Azcopy에 대 한 도움말
      
**--output-형식**  명령의 출력 형식입니다. 텍스트, json 등을 선택할 수 있습니다. 기본값은 ' text '입니다. (기본 "텍스트")

## <a name="see-also"></a>참고 항목:

- [AzCopy 시작](storage-use-azcopy-v10.md)
- [azcopy 도구](storage-ref-azcopy-bench.md)
- [azcopy 복사](storage-ref-azcopy-copy.md)
- [azcopy doc](storage-ref-azcopy-doc.md)
- [azcopy env](storage-ref-azcopy-env.md)
- [azcopy 작업](storage-ref-azcopy-jobs.md)
- [azcopy 작업 정리](storage-ref-azcopy-jobs-clean.md)
- [azcopy 작업 목록](storage-ref-azcopy-jobs-list.md)
- [azcopy 작업 제거](storage-ref-azcopy-jobs-remove.md)
- [azcopy 작업 다시 시작](storage-ref-azcopy-jobs-resume.md)
- [azcopy 작업 표시](storage-ref-azcopy-jobs-show.md)
- [azcopy 목록](storage-ref-azcopy-list.md)
- [azcopy 로그인](storage-ref-azcopy-login.md)
- [azcopy 로그 아웃](storage-ref-azcopy-logout.md)
- [azcopy 만들기](storage-ref-azcopy-make.md)
- [azcopy 제거](storage-ref-azcopy-remove.md)
- [azcopy 동기화](storage-ref-azcopy-sync.md)
