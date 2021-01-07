---
title: azcopy load clfs | Microsoft Docs
titleSuffix: Azure Storage
description: 이 문서에서는 azcopy load clfs 명령에 대 한 참조 정보를 제공 합니다.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 8f0a8903f90b134c35d9adb35a493d989d414b56
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87294333"
---
# <a name="azcopy-load-clfs"></a>azcopy load clfs

로컬 데이터를 컨테이너로 전송 하 고 Microsoft의 CLFS (Avere Cloud FileSystem) 형식에 저장 합니다.

## <a name="synopsis"></a>개요

Load 명령은 Azure Blob storage 컨테이너에 데이터를 복사한 다음 Microsoft의 CLFS (Avere Cloud FileSystem) 형식으로 해당 데이터를 저장 합니다. 소유 CLFS 형식은 azure HPC 캐시 및 Azure 제품에 대 한 Avere vFXT에서 사용 됩니다.

이 명령을 활용 하려면 pip3 install clfsload ~ = 1.0.23를 통해 필요한 확장을 설치 합니다. CLFSLoad.py가 경로에 있는지 확인 합니다. 이 단계에 대 한 자세한 내용은을 참조 [https://aka.ms/azcopy/clfs](https://aka.ms/azcopy/clfs) 하세요.

이 명령은 특정 Microsoft 고성능 컴퓨팅 캐시 제품에서 사용 하기 위해 기존 데이터를 클라우드 저장소로 이동 하는 간단한 옵션입니다. 

이러한 제품은 독점적 클라우드 파일 시스템 형식을 사용 하 여 데이터를 관리 하기 때문에 네이티브 복사 명령을 통해 해당 데이터를 로드할 수 없습니다. 

대신 캐시 제품 자체를 통하거나 올바른 소유 형식을 사용 하는이 load 명령을 통해 데이터를 로드 해야 합니다.
이 명령을 사용 하 여 캐시를 사용 하지 않고 데이터를 전송할 수 있습니다. 예를 들어 저장소를 미리 채우거 나 캐시 로드를 늘리지 않고 작업 집합에 파일을 추가 합니다.

대상이 빈 Azure Storage 컨테이너입니다. 전송이 완료 되 면 azure HPC Cache instance 또는 Avere vFXT for Azure cluster에서 대상 컨테이너를 사용할 수 있습니다.

> [!NOTE] 
> Load 명령의 미리 보기 릴리스입니다. AzCopy Github 리포지토리에서 문제를 보고 하세요.

```
azcopy load clfs [local dir] [container URL] [flags]
```

## <a name="related-conceptual-articles"></a>관련 개념 문서

- [AzCopy 시작](storage-use-azcopy-v10.md)
- [AzCopy 및 Blob 저장소를 사용 하 여 데이터 전송](storage-use-azcopy-blobs.md)
- [AzCopy 및 File Storage를 사용하여 데이터 전송](storage-use-azcopy-files.md)
- [AzCopy 구성, 최적화 및 문제 해결](storage-use-azcopy-configure.md)

## <a name="examples"></a>예

CLFS 형식의 SAS를 사용 하 여 컨테이너에 전체 디렉터리를 로드 합니다.

```azcopy
azcopy load clfs "/path/to/dir" "https://[account].blob.core.windows.net/[container]?[SAS]" --state-path="/path/to/state/path"
```

## <a name="options"></a>옵션

**--압축 형식** 문자열 전송에 사용할 압축 유형을 지정 합니다. 사용 가능한 값은 `DISABLED` , `LZ4` 입니다. (기본값 `LZ4` )

**--**    명령에 대 한 도움말 도움말 `azcopy load clfs` 입니다.

**--로그 수준** 문자열 로그 파일의 자세한 정도를 정의 합니다. 사용 가능한 수준:,, `DEBUG` `INFO` `WARNING` , `ERROR` . (기본값 `INFO` )

**--최대 오류** uint32는 허용할 최대 전송 실패 횟수를 지정 합니다. 오류가 발생 하는 경우 즉시 작업을 중지 합니다.

**--새 세션**   추적 정보가 유지 되는 기존 작업을 계속 하는 대신 새 작업을 시작 `--state-path` 합니다. (기본값 true)

**--하드 링크 유지**    하드 링크 관계를 유지 합니다.

**--state-** 작업 상태 추적을 위한 로컬 디렉터리에 필요한 경로 문자열입니다. 작업을 다시 시작 하려면 경로가 기존 디렉터리를 가리켜야 합니다. 새 작업에 대해 비어 있어야 합니다.

## <a name="options-inherited-from-parent-commands"></a>부모 명령에서 상속 된 옵션

|옵션|설명|
|---|---|
|--0mbps float|전송 률 (메가 비트/초)을 대문자로 처리 합니다. 순간 처리량은 cap와 약간 다를 수 있습니다. 이 옵션을 0으로 설정 하거나 생략 하면 처리량이 생략 되지 않습니다.|
|--출력 형식 문자열|명령의 출력 형식입니다. 텍스트, json 등을 선택할 수 있습니다. 기본값은 "text"입니다.|
|--신뢰할 수 있는 microsoft 접미사 문자열   | Azure Active Directory 로그인 토큰이 전송 될 수 있는 추가 도메인 접미사를 지정 합니다.  기본값은 '*. core.windows.net;* 입니다. core.chinacloudapi.cn; *. core.cloudapi.de;*. core.usgovcloudapi.net '. 여기에 나열 된 Any는 기본값에 추가 됩니다. 보안을 위해 여기에 Microsoft Azure 도메인만 배치 해야 합니다. 여러 항목을 세미콜론으로 구분 합니다.|

## <a name="see-also"></a>참고 항목

- [azcopy](storage-ref-azcopy.md)
