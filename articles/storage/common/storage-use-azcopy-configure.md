---
title: Azure Storage를 사용 하 여 AzCopy 구성, 최적화 및 문제 해결 | Microsoft Docs
description: Azure Storage를 사용 하 여 AzCopy를 구성 하 고 최적화 하 고 문제를 해결 합니다. 계획 및 로그 파일의 위치를 변경 하거나 제거 합니다. 기본 로그 수준을 변경 합니다.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 07/27/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 9742f97832c1fc931a1679132e262f92c9f11225
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88037187"
---
# <a name="configure-optimize-and-troubleshoot-azcopy"></a>AzCopy 구성, 최적화 및 문제 해결

AzCopy는 스토리지 계정에서 또는 스토리지 계정으로 Blob 또는 파일을 복사하는 데 사용할 수 있는 명령줄 유틸리티입니다. 이 문서는 고급 구성 작업을 수행하는 데 도움이 되며 AzCopy를 사용하는 경우 발생할 수 있는 문제를 해결하는 데 도움이 됩니다.

> [!NOTE]
> AzCopy을 시작 하는 데 도움이 되는 콘텐츠를 찾고 있는 경우 다음 문서를 참조 하세요.
> - [AzCopy 시작](storage-use-azcopy-v10.md)
> - [AzCopy 및 Blob 스토리지를 사용하여 데이터 전송](storage-use-azcopy-blobs.md)
> - [AzCopy 및 File Storage를 사용하여 데이터 전송](storage-use-azcopy-files.md)
> - [AzCopy 및 Amazon S3 버킷을 사용하여 데이터 전송](storage-use-azcopy-s3.md)

## <a name="configure-proxy-settings"></a>프록시 설정 구성

AzCopy에 대 한 프록시 설정을 구성 하려면 `https_proxy` 환경 변수를 설정 합니다. Windows에서 AzCopy를 실행하는 경우 AzCopy가 프록시 설정을 자동으로 검색하므로 Windows에서 이 설정을 사용할 필요가 없습니다. Windows에서 이 설정을 사용하도록 선택하면 자동 검색이 재정의됩니다.

| 운영 체제 | 명령  |
|--------|-----------|
| **Windows** | 명령 프롬프트에서 다음을 사용 합니다. `set https_proxy=<proxy IP>:<proxy port>`<br> PowerShell에서 다음을 사용 합니다. `$env:https_proxy="<proxy IP>:<proxy port>"`|
| **Linux** | `export https_proxy=<proxy IP>:<proxy port>` |
| **macOS** | `export https_proxy=<proxy IP>:<proxy port>` |

현재 AzCopy는 NTLM 또는 Kerberos 인증을 요구 하는 프록시를 지원 하지 않습니다.

### <a name="bypassing-a-proxy"></a>프록시 무시 ###

Windows에서 AzCopy를 실행 하는 경우 설정을 자동으로 검색 하는 대신 프록시를 사용 _하지 않도록_ 지시 하려면 이러한 명령을 사용 합니다. 이러한 설정을 사용 하면 AzCopy는 프록시를 조회 하거나 사용 하지 않습니다.

| 운영 체제 | Environment | 명령  |
|--------|-----------|----------|
| **Windows** | 명령 프롬프트 (CMD) | `set HTTPS_PROXY=dummy.invalid` <br>`set NO_PROXY=*`|
| **Windows** | PowerShell | `$env:HTTPS_PROXY="dummy.invalid"` <br>`$env:NO_PROXY="*"`<br>|

프록시를 사용 하지 않으려면 다른 운영 체제에서 HTTPS_PROXY 변수를 설정 하지 않은 상태로 둡니다.

## <a name="optimize-performance"></a>성능 최적화

성능을 벤치 마크 한 다음 명령 및 환경 변수를 사용 하 여 성능 및 리소스 소비량 간의 최적의 균형을 찾을 수 있습니다.

이 섹션에서는 다음과 같은 최적화 작업을 수행할 수 있습니다.

> [!div class="checklist"]
> * 벤치 마크 테스트 실행
> * 처리량 최적화
> * 메모리 사용 최적화 
> * 파일 동기화 최적화

### <a name="run-benchmark-tests"></a>벤치 마크 테스트 실행

특정 blob 컨테이너 또는 파일 공유에 대 한 성능 벤치 마크 테스트를 실행 하 여 일반적인 성능 통계를 확인 하 고 성능 병목 상태를 식별할 수 있습니다. 생성 된 테스트 데이터를 업로드 하거나 다운로드 하 여 테스트를 실행할 수 있습니다. 

다음 명령을 사용 하 여 성능 벤치 마크 테스트를 실행 합니다.

|    |     |
|--------|-----------|
| **구문** | `azcopy benchmark 'https://<storage-account-name>.blob.core.windows.net/<container-name>'` |
| **예제** | `azcopy benchmark 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

> [!TIP]
> 이 예에서는 경로 인수를 작은따옴표 (' ')로 묶습니다. Windows 명령 셸 (cmd.exe)을 제외 하 고 모든 명령 셸에서 작은따옴표를 사용 합니다. cmd.exe (Windows 명령 셸)을 사용 하는 경우 작은따옴표 (' ') 대신 경로 인수를 큰따옴표 ("")로 묶습니다.

이 명령은 테스트 데이터를 지정 된 대상에 업로드 하 여 성능 벤치 마크를 실행 합니다. 테스트 데이터는 메모리에 생성 되 고 대상에 업로드 된 후 테스트가 완료 된 후 대상에서 삭제 됩니다. 선택적 명령 매개 변수를 사용 하 여 생성할 파일 수 및 원하는 크기를 지정할 수 있습니다.

데이터를 다운로드 하 여이 테스트를 실행 하려면 `mode` 매개 변수를로 설정 `download` 합니다. 자세한 참조 문서는 [azcopy 벤치 마크](storage-ref-azcopy-bench.md)를 참조 하세요. 

### <a name="optimize-throughput"></a>처리량 최적화

`cap-mbps`명령에서 플래그를 사용 하 여 처리량 데이터 속도를 최대값으로 지정할 수 있습니다. 예를 들어 다음 명령은 초당 작업 및 cap 처리량을 `10` 메가 비트 (Mb)로 다시 시작 합니다. 

```azcopy
azcopy jobs resume <job-id> --cap-mbps 10
```

작은 파일을 전송할 때는 처리량이 줄어들 수 있습니다. 환경 변수를 설정 하 여 처리량을 늘릴 수 있습니다 `AZCOPY_CONCURRENCY_VALUE` . 이 변수는 발생할 수 있는 동시 요청 수를 지정합니다.  

컴퓨터에 5 개 미만의 Cpu가 있는 경우이 변수의 값은로 설정 됩니다 `32` . 그렇지 않으면 기본값은 16에 CPU 수를 곱한 값과 같습니다. 이 변수의 최대 기본값은 이지만 `3000` 이 값을 더 높거나 낮게 수동으로 설정할 수 있습니다. 

| 운영 체제 | 명령  |
|--------|-----------|
| **Windows** | `set AZCOPY_CONCURRENCY_VALUE=<value>` |
| **Linux** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |
| **macOS** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |

을 사용 `azcopy env` 하 여이 변수의 현재 값을 확인 합니다. 값이 비어 있으면 AzCopy 로그 파일의 시작 부분을 확인 하 여 사용 되는 값을 읽을 수 있습니다. 선택한 값과 선택 된 이유가 보고 됩니다.

이 변수를 설정 하기 전에 벤치 마크 테스트를 실행 하는 것이 좋습니다. 벤치 마크 테스트 프로세스에서는 권장 되는 동시성 값을 보고 합니다. 또는 네트워크 조건 및 페이로드가 다른 경우이 변수를 `AUTO` 특정 숫자가 아닌 단어로 설정 합니다. 이로 인해 AzCopy는 항상 벤치 마크 테스트에서 사용 하는 것과 동일한 자동 조정 프로세스를 실행 합니다.

### <a name="optimize-memory-use"></a>메모리 사용 최적화

`AZCOPY_BUFFER_GB`환경 변수를 설정 하 여 파일을 다운로드 하 고 업로드할 때 AzCopy 사용할 시스템 메모리의 최대 크기를 지정 합니다.
이 값을 기가바이트 (GB) 단위로 표현 합니다.

| 운영 체제 | 명령  |
|--------|-----------|
| **Windows** | `set AZCOPY_BUFFER_GB=<value>` |
| **Linux** | `export AZCOPY_BUFFER_GB=<value>` |
| **macOS** | `export AZCOPY_BUFFER_GB=<value>` |

### <a name="optimize-file-synchronization"></a>파일 동기화 최적화

[Sync](storage-ref-azcopy-sync.md) 명령은 대상에 있는 모든 파일을 식별 한 다음 동기화 작업을 시작 하기 전에 파일 이름과 마지막 수정 타임 스탬프를 비교 합니다. 많은 수의 파일이 있는 경우이 사전 처리를 제거 하 여 성능을 향상 시킬 수 있습니다. 

이를 수행 하려면 대신 [azcopy copy](storage-ref-azcopy-copy.md) 명령을 사용 하 고 `--overwrite` 플래그를로 설정 합니다 `ifSourceNewer` . AzCopy는 사전 검색 및 비교를 수행 하지 않고 파일을 복사할 때 파일을 비교 합니다. 이를 통해 비교할 많은 파일이 있는 경우 성능에 지를 제공할 수 있습니다.

[Azcopy copy](storage-ref-azcopy-copy.md) 명령은 대상에서 파일을 삭제 하지 않으므로 대상에서 파일이 원본에 더 이상 존재 하지 않는 경우 해당 파일을 삭제 하려면 [azcopy sync](storage-ref-azcopy-sync.md) 명령을 플래그와 함께 `--delete-destination` 또는 값으로 설정 합니다 `true` `prompt` . 

## <a name="troubleshoot-issues"></a>문제 해결

AzCopy는 모든 작업에 대해 로그 및 계획 파일을 만듭니다. 로그를 사용하여 잠재적 문제를 조사하고 해결할 수 있습니다. 

로그에는 실패 상태 ( `UPLOADFAILED` , `COPYFAILED` 및 `DOWNLOADFAILED` ), 전체 경로 및 실패 한 이유가 포함 됩니다.

기본적으로 로그 및 계획 파일은 `%USERPROFILE%\.azcopy` Windows의 디렉터리 또는 `$HOME$\.azcopy` Mac 및 Linux의 디렉터리에 위치 하지만 원하는 경우 해당 위치를 변경할 수 있습니다.

관련 오류는 파일에 표시 되는 첫 번째 오류가 아닐 수도 있습니다. 네트워크 오류, 시간 제한 및 서버 사용 중 오류와 같은 오류의 경우 AzCopy는 최대 20 회 다시 시도 하 고 일반적으로 다시 시도 프로세스가 성공 합니다.  표시 되는 첫 번째 오류는 성공적으로 다시 시도한 것일 수 있습니다.  따라서 파일의 첫 번째 오류를 확인 하는 대신, 또는 근처의 오류를 확인 합니다 `UPLOADFAILED` `COPYFAILED` `DOWNLOADFAILED` . 

> [!IMPORTANT]
> Microsoft 지원에 대 한 요청을 제출 하거나, 타사와 관련 된 문제를 해결 하는 경우 실행 하려는 명령의 교정 된 버전을 공유 합니다. 이렇게 하면 SAS를 실수로 공유 하지 않습니다. 수정 버전은 로그 파일의 시작 부분에서 찾을 수 있습니다.

### <a name="review-the-logs-for-errors"></a>오류에 대한 로그 검토

다음 명령을 사용 하 여 로그에서 상태의 모든 오류를 가져옵니다 `UPLOADFAILED` `04dc9ca9-158f-7945-5933-564021086c79` .

**Windows (PowerShell)**

```
Select-String UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

**Linux**

```
grep UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

### <a name="view-and-resume-jobs"></a>작업 보기 및 다시 시작

각 전송 작업은 AzCopy 작업을 만듭니다. 다음 명령을 사용 하 여 작업 기록을 확인 합니다.

```
azcopy jobs list
```

작업 상태를 보려면 다음 명령을 사용합니다.

```
azcopy jobs show <job-id>
```

전송을 상태별로 필터링하려면 다음 명령을 사용합니다.

```
azcopy jobs show <job-id> --with-status=Failed
```

다음 명령을 사용 하 여 실패/취소 된 작업을 다시 시작 합니다. 이 명령은 보안상의 이유로 영구적이 지 않으므로 SAS 토큰과 함께 식별자를 사용 합니다.

```
azcopy jobs resume <job-id> --source-sas="<sas-token>"
azcopy jobs resume <job-id> --destination-sas="<sas-token>"
```

> [!TIP]
> SAS 토큰과 같은 경로 인수를 작은따옴표 (' ')로 묶습니다. Windows 명령 셸 (cmd.exe)을 제외 하 고 모든 명령 셸에서 작은따옴표를 사용 합니다. cmd.exe (Windows 명령 셸)을 사용 하는 경우 작은따옴표 (' ') 대신 경로 인수를 큰따옴표 ("")로 묶습니다.

작업을 다시 시작 하면 AzCopy에서 작업 계획 파일을 찾습니다. 계획 파일에는 작업을 처음 만들 때 처리를 위해 식별 된 모든 파일이 나열 됩니다. 작업을 다시 시작 하면 AzCopy는 아직 전송 되지 않은 계획 파일에 나열 된 모든 파일을 전송 하려고 시도 합니다.

## <a name="change-the-location-of-the-plan-and-log-files"></a>계획 및 로그 파일의 위치를 변경 합니다.

기본적으로 계획 및 로그 파일은 `%USERPROFILE%\.azcopy` Windows의 디렉터리 또는 `$HOME$\.azcopy` Mac 및 Linux의 디렉터리에 있습니다. 이 위치를 변경할 수 있습니다.

### <a name="change-the-location-of-plan-files"></a>계획 파일의 위치를 변경 합니다.

이러한 명령 중 하나를 사용 합니다.

| 운영 체제 | 명령  |
|--------|-----------|
| **Windows** | 슬래시`$env:AZCOPY_JOB_PLAN_LOCATION="<value>"` <br> 명령 프롬프트에서 다음을 사용 합니다. `set AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **Linux** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **macOS** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |

을 사용 `azcopy env` 하 여이 변수의 현재 값을 확인 합니다. 값이 비어 있으면 계획 파일이 기본 위치에 기록 됩니다.

### <a name="change-the-location-of-log-files"></a>로그 파일의 위치를 변경 합니다.

이러한 명령 중 하나를 사용 합니다.

| 운영 체제 | 명령  |
|--------|-----------|
| **Windows** | 슬래시`$env:AZCOPY_LOG_LOCATION="<value>"` <br> 명령 프롬프트에서 다음을 사용 합니다. `set AZCOPY_LOG_LOCATION=<value>`|
| **Linux** | `export AZCOPY_LOG_LOCATION=<value>` |
| **macOS** | `export AZCOPY_LOG_LOCATION=<value>` |

을 사용 `azcopy env` 하 여이 변수의 현재 값을 확인 합니다. 값이 비어 있으면 로그가 기본 위치에 기록 됩니다.

## <a name="change-the-default-log-level"></a>기본 로그 수준 변경

기본적으로 AzCopy 로그 수준은로 설정 됩니다 `INFO` . 로그의 자세한 정도를 줄여서 디스크 공간을 절약 하려는 경우 옵션을 사용 하 여이 설정을 덮어씁니다 ``--log-level`` . 

사용 가능한 로그 수준은 `NONE` , `DEBUG` ,,,, `INFO` `WARNING` `ERROR` `PANIC` 및 `FATAL` 입니다.

## <a name="remove-plan-and-log-files"></a>계획 및 로그 파일 제거

로컬 컴퓨터에서 모든 계획 및 로그 파일을 제거 하 여 디스크 공간을 절약 하려면 `azcopy jobs clean` 명령을 사용 합니다.

한 작업에만 연결 된 계획 및 로그 파일을 제거 하려면를 사용 `azcopy jobs rm <job-id>` 합니다. `<job-id>`이 예의 자리 표시자를 작업의 작업 id로 바꿉니다.


