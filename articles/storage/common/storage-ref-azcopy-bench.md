---
title: azcopy 도구 | Microsoft Docs
description: 이 문서에서는 azcopy 도구 명령에 대 한 참조 정보를 제공 합니다.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: c1028d0a4a458746c08fd6fa4f16aa952d9962a2
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87282010"
---
# <a name="azcopy-benchmark"></a>azcopy 벤치 마크

지정 된 대상에 테스트 데이터를 업로드 하거나 다운로드 하 여 성능 벤치 마크를 실행 합니다. 업로드의 경우 테스트 데이터가 자동으로 생성 됩니다.

벤치 마크 명령은 다음과 같은 경우를 제외 하 고는 ' 복사 '와 동일한 프로세스를 실행 합니다. 

  - 벤치 마크는 원본 및 대상 매개 변수를 요구 하는 대신 하나만 사용 합니다. 업로드 하거나 다운로드 하려는 blob 컨테이너, Azure Files 공유 또는 Azure Data Lake Storage Gen2 파일 시스템입니다.

  - ' Mode ' 매개 변수는 AzCopy가 지정 된 대상에서 업로드를 테스트 하거나 다운로드 해야 하는지 여부를 설명 합니다. 유효한 값은 ' Upload ' 및 ' Download '입니다. 기본값은 ' Upload '입니다.

  - 업로드 벤치 마크의 경우 페이로드는 자동으로 생성 되는 파일 수 및 파일의 수를 제어 하는 명령줄 매개 변수를 통해 설명 됩니다. 생성 프로세스는 메모리에서 전적으로 발생 합니다. 디스크가 사용 되지 않습니다.

  - 다운로드의 경우 페이로드는 원본에 이미 있는 파일로 구성 됩니다. (필요한 경우 테스트 파일을 생성 하는 방법에 대 한 아래 예제를 참조 하세요.)
  
  - Copy 명령에 사용할 수 있는 몇 가지 선택적 매개 변수만 지원 됩니다.
  
  - 추가 진단은 측정 되 고 보고 됩니다.
  
  - 업로드의 경우 기본 동작은 테스트 실행이 끝날 때 전송 된 데이터를 삭제 하는 것입니다.  다운로드의 경우 데이터는 로컬로 저장 되지 않습니다.

벤치 마크 모드는 최대 처리량을 제공 하는 병렬 TCP 연결 수를 자동으로 조정 합니다. 끝에 해당 숫자가 표시 됩니다. Autotuning을 방지 하려면 AZCOPY_CONCURRENCY_VALUE 환경 변수를 특정 개수의 연결로 설정 합니다. 

모든 일반적인 인증 유형이 지원 됩니다. 그러나 벤치마킹 업로드에 대 한 가장 편리한 접근 방식은 일반적으로 SAS 토큰을 사용 하 여 빈 컨테이너를 만들고 SAS 인증을 사용 하는 것입니다. 다운로드 모드를 사용 하려면 대상 컨테이너에 테스트 데이터 집합이 있어야 합니다.

## <a name="examples"></a>예제

```azcopy
azcopy benchmark [destination] [flags]
```

기본 매개 변수를 사용 하 여 벤치 마크 테스트 실행 (최대 1Gbps의 벤치마킹 네트워크에 적합): '

```azcopy
azcopy bench "https://[account].blob.core.windows.net/[container]?<SAS>"
```
100 파일을 업로드 하는 벤치 마크 테스트를 실행 합니다. 각 GiB 크기는 다음과 같습니다 (고속 네트워크에서 벤치마킹에 적합 합니다 (예: 10gbps).

```azcopy
azcopy bench "https://[account].blob.core.windows.net/[container]?<SAS>"--file-count 100 --size-per-file 2G
```
벤치 마크 테스트를 실행 하지만 5만 파일을 사용 하 고 각 8 MiB 크기를 사용 하 고 MD5 해시를 계산 합니다 ( `--put-md5` 플래그가 복사 명령에서이를 수행 하는 것과 동일한 방식으로). `--put-md5`벤치마킹이 선택 된 파일 수 및 크기에 대 한 처리량에 영향을 주는지 여부를 테스트 하는 경우의 목적입니다.

```azcopy
azcopy bench --mode='Upload' "https://[account].blob.core.windows.net/[container]?<SAS>" --file-count 50000 --size-per-file 8M --put-md5
```

대상에서 기존 파일을 다운로드 하는 벤치 마크 테스트 실행

```azcopy
azcopy bench --mode='Download' "https://[account].blob.core.windows.net/[container]?<SAS?"
```

전송 된 파일을 삭제 하지 않는 업로드를 실행 합니다. 이러한 파일은 다운로드 테스트에 대 한 페이로드로 사용할 수 있습니다.

```azcopy
azcopy bench "https://[account].blob.core.windows.net/[container]?<SAS>" --file-count 100 --delete-test-data=false
```

## <a name="options"></a>옵션

**--blob 형식** 문자열은 대상에서 blob의 유형을 정의 합니다. 다른 blob 유형 벤치마킹을 허용 하는 데 사용 됩니다. Copy 명령의 동일한 명명 된 매개 변수와 동일 합니다 (기본값 "검색").

**--블록 크기-mb** float는이 블록 크기 (MiB에 지정)를 사용 합니다. 기본값은 파일 크기에 따라 자동으로 계산 됩니다. 소수 부분 (예: 0.25)을 사용할 수 있습니다. Copy 명령의 동일한 이름의 매개 변수와 동일 합니다.

**--check length**  전송 후 대상에서 파일의 길이를 확인 합니다. 원본과 대상이 일치 하지 않는 경우 전송이 실패 한 것으로 표시 됩니다. (기본값 true)

**----** --------데이터  True 이면 벤치 마크 실행이 끝날 때 벤치 마크 데이터가 삭제 됩니다.  데이터를 대상에 유지 하려는 경우 (예: 벤치 마크 모드 외부에서 수동 테스트에 사용 하려는 경우) false로 설정 합니다 (기본값 true).

**--파일 수** uint.  사용할 자동 생성 된 데이터 파일의 수입니다 (기본값 100).

**--도움말**  도구 도움말

**--로그 수준** 문자열은 로그 파일에 대 한 로그의 자세한 정도, 사용 가능한 수준: 정보 (모든 요청/응답), 경고 (저속 응답), 오류 (실패 한 요청만) 및 없음 (출력 로그 없음)을 정의 합니다. (기본 "정보")

**--mode** 문자열은 Azcopy에서이 대상의 업로드 또는 다운로드를 테스트 해야 하는지 여부를 정의 합니다. 유효한 값은 ' upload ' 및 ' download '입니다. 기본값으로 설정 된 옵션은 ' upload '입니다. (기본 ' 업로드 ')

**--폴더 수** (0 보다 큰 경우)는 데이터를 분할 하는 폴더를 만듭니다.

**--입력-md5**  각 파일의 MD5 해시를 만들고 대상 blob/파일의 Content-MD5 속성으로 해시를 저장 합니다. 기본적으로 해시가 생성 되지 않습니다. Copy 명령의 동일한 이름의 매개 변수와 동일 합니다.

**--** 자동 생성 되는 각 데이터 파일의 파일당 크기 문자열 크기입니다. 은 바로 뒤에 K, M 또는 G가 오는 숫자 여야 합니다. 예: 12k 또는 200G (기본값 "250M").

## <a name="options-inherited-from-parent-commands"></a>부모 명령에서 상속 된 옵션

**--0mbps float**  전송 률 (메가 비트/초)을 대문자로 처리 합니다. 순간 처리량은 cap와 약간 다를 수 있습니다. 이 옵션을 0으로 설정 하거나 생략 하면 처리량이 생략 되지 않습니다.

**--output-** 명령 출력의 문자열 형식입니다. 텍스트, json 등을 선택할 수 있습니다. 기본값은 ' text '입니다. 기본값은 "text"입니다.

**--trusted-microsoft-접미사** 문자열 Azure Active Directory 로그인 토큰이 전송 될 수 있는 추가 도메인 접미사를 지정 합니다.  기본값은 '*. core.windows.net;* 입니다. core.chinacloudapi.cn; *. core.cloudapi.de;*. core.usgovcloudapi.net '. 여기에 나열 된 Any는 기본값에 추가 됩니다. 보안을 위해 여기에 Microsoft Azure 도메인만 배치 해야 합니다. 여러 항목을 세미콜론으로 구분 합니다.


## <a name="see-also"></a>참고 항목

- [azcopy](storage-ref-azcopy.md)
