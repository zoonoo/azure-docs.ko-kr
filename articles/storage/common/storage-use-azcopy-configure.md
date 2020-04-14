---
title: Azure 저장소를 통해 AzCopy를 구성, 최적화 및 해결 | 마이크로 소프트 문서
description: AzCopy를 구성, 최적화 및 문제 해결합니다.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 87a335f44a31436de735395adbee9035493cbbd2
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81263423"
---
# <a name="configure-optimize-and-troubleshoot-azcopy"></a>AzCopy 구성, 최적화 및 문제 해결

AzCopy는 저장소 계정에서 Blob 또는 파일을 복사하는 데 사용할 수 있는 명령줄 유틸리티입니다. 이 문서에서는 고급 구성 작업을 수행하는 데 도움이 되며 AzCopy를 사용할 때 발생할 수 있는 문제를 해결하는 데 도움이 됩니다.

> [!NOTE]
> AzCopy를 시작하는 데 도움이 되는 콘텐츠를 찾고 있다면 다음 문서를 참조하십시오.
> - [AzCopy 시작](storage-use-azcopy-v10.md)
> - [AzCopy 및 Blob 스토리지를 사용하여 데이터 전송](storage-use-azcopy-blobs.md)
> - [AzCopy 및 파일 스토리지를 사용하여 데이터 전송](storage-use-azcopy-files.md)
> - [AzCopy 및 Amazon S3 버킷을 사용하여 데이터 전송](storage-use-azcopy-s3.md)

## <a name="configure-proxy-settings"></a>프록시 설정 구성

AzCopy에 대한 프록시 설정을 구성하려면 환경 변수를 `https_proxy` 설정합니다. Windows에서 AzCopy를 실행하는 경우 AzCopy는 자동으로 프록시 설정을 감지하므로 Windows에서 이 설정을 사용할 필요가 없습니다. Windows에서 이 설정을 사용하도록 선택하면 자동 검색이 재정의됩니다.

| 운영 체제 | 명령  |
|--------|-----------|
| **Windows** | 명령 프롬프트 사용에서:`set https_proxy=<proxy IP>:<proxy port>`<br> 파워쉘 사용시:`$env:https_proxy="<proxy IP>:<proxy port>"`|
| **Linux** | `export https_proxy=<proxy IP>:<proxy port>` |
| **MacOS** | `export https_proxy=<proxy IP>:<proxy port>` |

현재 AzCopy는 NTLM 또는 Kerberos로 인증해야 하는 프록시를 지원하지 않습니다.

## <a name="optimize-performance"></a>성능 최적화

성능을 벤치마크한 다음 명령 및 환경 변수를 사용하여 성능과 리소스 소비 간의 최적의 절충을 찾을 수 있습니다.

이 섹션에서는 다음과 같은 최적화 작업을 수행하는 데 도움이 됩니다.

> [!div class="checklist"]
> * 벤치마크 테스트 실행
> * 처리량 최적화
> * 메모리 사용 최적화 
> * 파일 동기화 최적화

### <a name="run-benchmark-tests"></a>벤치마크 테스트 실행

특정 Blob 컨테이너에서 성능 벤치마크 테스트를 실행하여 일반 성능 통계를 확인하고 성능 병목 현상을 확인할 수 있습니다. 

다음 명령을 사용하여 성능 벤치마크 테스트를 실행합니다.

|    |     |
|--------|-----------|
| **구문** | `azcopy bench 'https://<storage-account-name>.blob.core.windows.net/<container-name>'` |
| **예제** | `azcopy bench 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

> [!TIP]
> 이 예제에서는 경로 인수를 단일 따옴표('')로 둘러싸입니다. Windows 명령 셸(cmd.exe)을 제외한 모든 명령 셸에 단일 따옴표를 사용합니다. Windows 명령 셸(cmd.exe)을 사용하는 경우 경로 인수를 단일 따옴표('')가 아닌 큰따옴표(")로 동봉합니다.

이 명령은 테스트 데이터를 지정된 대상에 업로드하여 성능 벤치마크를 실행합니다. 테스트 데이터는 메모리에서 생성되고 대상에 업로드된 다음 테스트가 완료된 후 대상에서 삭제됩니다. 선택적 명령 매개 변수를 사용하여 생성할 파일 수와 파일 크기를 지정할 수 있습니다.

자세한 참조 문서는 [azcopy 벤치를](storage-ref-azcopy-bench.md)참조하십시오.

이 명령에 대한 자세한 도움말 `azcopy bench -h` 지침을 보려면 ENTER 키를 입력한 다음 누릅니다.

### <a name="optimize-throughput"></a>처리량 최적화

명령의 `cap-mbps` 플래그를 사용하여 처리량 데이터 속도에 천장을 배치할 수 있습니다. 예를 들어 다음 명령은 작업을 다시 시작하고 처리량을 초당 메가비트(MB)로 `10` 한도합니다. 

```azcopy
azcopy jobs resume <job-id> --cap-mbps 10
```

작은 파일을 전송할 때 처리량이 감소할 수 있습니다. 환경 변수를 `AZCOPY_CONCURRENCY_VALUE` 설정하여 처리량을 늘릴 수 있습니다. 이 변수는 발생할 수 있는 동시 요청 수를 지정합니다.  

컴퓨터에 CPU가 5개 미만이면 이 변수의 값이 `32`로 설정됩니다. 그렇지 않으면 기본값은 CPU 수를 곱한 16입니다. 이 변수의 최대 기본값은 `3000`은 이지만 수동으로 이 값을 더 높거나 낮게 설정할 수 있습니다. 

| 운영 체제 | 명령  |
|--------|-----------|
| **Windows** | `set AZCOPY_CONCURRENCY_VALUE=<value>` |
| **Linux** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |
| **MacOS** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |

을 `azcopy env` 사용하여 이 변수의 현재 값을 확인합니다. 값이 비어 있으면 AzCopy 로그 파일의 시작 부분을 보면 사용 중인 값을 읽을 수 있습니다. 선택한 값과 선택한 이유가 보고됩니다.

이 변수를 설정하기 전에 벤치마크 테스트를 실행하는 것이 좋습니다. 벤치마크 테스트 프로세스는 권장 동시성 값을 보고합니다. 또는 네트워크 조건과 페이로드가 다른 경우 이 변수를 특정 번호 대신 단어로 `AUTO` 설정합니다. 이렇게 하면 AzCopy가 벤치마크 테스트에서 사용하는 것과 동일한 자동 튜닝 프로세스를 항상 실행하게 됩니다.

### <a name="optimize-memory-use"></a>메모리 사용 최적화

환경 `AZCOPY_BUFFER_GB` 변수를 설정하여 파일을 다운로드하고 업로드할 때 AzCopy에서 사용할 시스템 메모리의 최대 양을 지정합니다.
이 값을 기가바이트(GB)로 표현합니다.

| 운영 체제 | 명령  |
|--------|-----------|
| **Windows** | `set AZCOPY_BUFFER_GB=<value>` |
| **Linux** | `export AZCOPY_BUFFER_GB=<value>` |
| **MacOS** | `export AZCOPY_BUFFER_GB=<value>` |

### <a name="optimize-file-synchronization"></a>파일 동기화 최적화

[sync](storage-ref-azcopy-sync.md) 명령은 대상에서 모든 파일을 식별한 다음 동기화 작업을 시작하기 전에 파일 이름과 마지막으로 수정된 타임스탬프를 비교합니다. 파일 수가 많은 경우 이 선행 처리를 제거하여 성능을 향상시킬 수 있습니다. 

이렇게 하려면 [azcopy 복사](storage-ref-azcopy-copy.md) 명령을 대신 사용하고 `--overwrite` 플래그를 `ifSourceNewer`로 설정합니다. AzCopy는 선행 검사 및 비교를 수행하지 않고 복사되는 파일을 비교합니다. 이렇게 하면 비교할 파일수가 많은 경우 성능 에지가 제공됩니다.

[azcopy copy](storage-ref-azcopy-copy.md) 명령은 대상에서 파일을 삭제하지 않으므로 원본에 더 이상 존재하지 않을 때 대상에서 파일을 삭제하려면 [azcopy](storage-ref-azcopy-sync.md) sync `--delete-destination` 명령을 플래그값 `true` 또는 `prompt`값으로 설정하여 사용합니다. 

## <a name="troubleshoot-issues"></a>문제 해결

AzCopy는 모든 작업에 대해 로그 및 계획 파일을 만듭니다. 로그를 사용하여 잠재적 문제를 조사하고 해결할 수 있습니다. 

로그는 실패 상태`UPLOADFAILED` `COPYFAILED` `DOWNLOADFAILED`(및) 전체 경로 및 실패의 이유를 포함합니다.

기본적으로 로그 및 계획 파일은 `%USERPROFILE%\.azcopy` Windows의 디렉토리 또는 `$HOME$\.azcopy` Mac 및 Linux의 디렉토리에 있지만 원하는 경우 해당 위치를 변경할 수 있습니다.

관련 오류가 반드시 파일에 나타나는 첫 번째 오류는 아닙니다. 네트워크 오류, 시간 시간 단축 및 서버 사용 중 오류와 같은 오류의 경우 AzCopy는 최대 20회까지 다시 시도하며 일반적으로 재시도 프로세스가 성공합니다.  첫 번째 오류는 성공적으로 다시 시도된 무해한 오류일 수 있습니다.  따라서 파일의 첫 번째 오류를 보는 대신 에 가까운 `UPLOADFAILED` `COPYFAILED`오류를 찾습니다. `DOWNLOADFAILED` 

> [!IMPORTANT]
> Microsoft 지원에 요청을 제출하거나 타사와 관련된 문제 해결을 수행할 때 실행하려는 명령의 수정된 버전을 공유합니다. 이렇게 하면 SAS가 실수로 다른 사용자와 공유되지 않습니다. 수정 버전은 로그 파일의 시작 부분에서 찾을 수 있습니다.

### <a name="review-the-logs-for-errors"></a>오류에 대한 로그 검토

다음 명령은 `UPLOADFAILED` `04dc9ca9-158f-7945-5933-564021086c79` 로그에서 상태와 모든 오류를 가져옵니다.

**윈도우(파워쉘)**

```
Select-String UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

**Linux**

```
grep UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

### <a name="view-and-resume-jobs"></a>작업 보기 및 다시 시작

각 전송 작업은 AzCopy 작업을 만듭니다. 다음 명령을 사용하여 작업 기록을 봅니다.

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

다음 명령을 사용하여 실패/취소된 작업을 다시 시작합니다. 이 명령은 보안상의 이유로 영구적이지 않기 때문에 SAS 토큰과 함께 식별자를 사용합니다.

```
azcopy jobs resume <job-id> --source-sas="<sas-token>"
azcopy jobs resume <job-id> --destination-sas="<sas-token>"
```

> [!TIP]
> SAS 토큰과 같은 경로 인수를 따옴표('')와 함께 동봉합니다. Windows 명령 셸(cmd.exe)을 제외한 모든 명령 셸에 단일 따옴표를 사용합니다. Windows 명령 셸(cmd.exe)을 사용하는 경우 경로 인수를 단일 따옴표('')가 아닌 큰따옴표(")로 동봉합니다.

작업을 다시 시작하면 AzCopy가 작업 계획 파일을 찾습니다. 계획 파일에는 작업이 처음 생성될 때 처리를 위해 식별된 모든 파일이 나열됩니다. 작업을 다시 시작하면 AzCopy는 아직 전송되지 않은 계획 파일에 나열된 모든 파일을 전송하려고 시도합니다.

## <a name="change-the-location-of-the-plan-and-log-files"></a>계획 및 로그 파일의 위치 변경

기본적으로 계획 및 로그 파일은 `%USERPROFILE%\.azcopy` Windows의 디렉토리 `$HOME$\.azcopy` 또는 Mac 및 Linux의 디렉토리에 있습니다. 이 위치를 변경할 수 있습니다.

### <a name="change-the-location-of-plan-files"></a>계획 파일의 위치 변경

이러한 명령 중 어느 한 가지를 사용합니다.

| 운영 체제 | 명령  |
|--------|-----------|
| **Windows** | `set AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **Linux** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **MacOS** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |

을 `azcopy env` 사용하여 이 변수의 현재 값을 확인합니다. 값이 비어 있으면 계획 파일이 기본 위치에 기록됩니다.

### <a name="change-the-location-of-log-files"></a>로그 파일의 위치 변경

이러한 명령 중 어느 한 가지를 사용합니다.

| 운영 체제 | 명령  |
|--------|-----------|
| **Windows** | `set AZCOPY_LOG_LOCATION=<value>` |
| **Linux** | `export AZCOPY_LOG_LOCATION=<value>` |
| **MacOS** | `export AZCOPY_LOG_LOCATION=<value>` |

을 `azcopy env` 사용하여 이 변수의 현재 값을 확인합니다. 값이 비어 있으면 로그가 기본 위치에 기록됩니다.

## <a name="change-the-default-log-level"></a>기본 로그 수준 변경

기본적으로 AzCopy 로그 수준은 `INFO`로그 수준으로 설정됩니다. 디스크 공간을 절약하기 위해 로그 세부 성을 줄이려면 ``--log-level`` 옵션을 사용하여 이 설정을 덮어씁니다. 

사용 가능한 로그 `INFO`수준은 `WARNING` `ERROR`다음과 `PANIC` `FATAL` `NONE`같습니다. `DEBUG`

## <a name="remove-plan-and-log-files"></a>계획 및 로그 파일 제거

디스크 공간을 절약하기 위해 로컬 컴퓨터에서 모든 계획 및 로그 `azcopy jobs clean` 파일을 제거하려면 명령을 사용합니다.

하나의 작업과 연결된 계획 및 로그 `azcopy jobs rm <job-id>`파일을 제거하려면 을 사용합니다. 이 `<job-id>` 예제의 자리 표시자를 작업의 작업 ID로 바꿉습니다.


