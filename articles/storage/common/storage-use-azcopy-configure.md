---
title: Azure Storage를 사용하여 AzCopy 구성, 최적화 및 문제 해결 | Microsoft Docs
description: Azure Storage를 사용하여 AzCopy 구성, 최적화 및 문제 해결 플랜 및 로그 파일을 제거하거나 해당 위치를 변경합니다. 기본 로그 수준을 변경합니다.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 07/27/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: ad9e5665204dbd3f99f83af3578b1996814d6fa0
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105728846"
---
# <a name="configure-optimize-and-troubleshoot-azcopy"></a>AzCopy 구성, 최적화 및 문제 해결

AzCopy는 스토리지 계정에서 또는 스토리지 계정으로 Blob 또는 파일을 복사하는 데 사용할 수 있는 명령줄 유틸리티입니다. 이 문서는 고급 구성 작업을 수행하는 데 도움이 되며 AzCopy를 사용하는 경우 발생할 수 있는 문제를 해결하는 데 도움이 됩니다.

> [!NOTE]
> AzCopy를 시작하는 데 도움이 되는 콘텐츠를 찾고 있다면 다음 문서를 참조하세요.
> - [AzCopy 시작](storage-use-azcopy-v10.md)
> - [AzCopy 및 Blob 스토리지를 사용하여 데이터 전송](./storage-use-azcopy-v10.md#transfer-data)
> - [AzCopy 및 파일 스토리지를 사용하여 데이터 전송](storage-use-azcopy-files.md)
> - [AzCopy 및 Amazon S3 버킷을 사용하여 데이터 전송](storage-use-azcopy-s3.md)

## <a name="configure-proxy-settings"></a>프록시 설정 구성

AzCopy에 대한 프록시 설정을 구성하려면 `HTTPS_PROXY` 환경 변수를 설정합니다. Windows에서 AzCopy를 실행하는 경우 AzCopy가 프록시 설정을 자동으로 검색하므로 Windows에서 이 설정을 사용할 필요가 없습니다. Windows에서 이 설정을 사용하도록 선택하면 자동 검색이 재정의됩니다.

| 운영 체제 | 명령  |
|--------|-----------|
| **Windows** | 명령 프롬프트에서 `set HTTPS_PROXY=<proxy IP>:<proxy port>`를 사용합니다.<br> PowerShell에서 `$env:HTTPS_PROXY="<proxy IP>:<proxy port>"`를 사용합니다.|
| **Linux** | `export HTTPS_PROXY=<proxy IP>:<proxy port>` |
| **macOS** | `export HTTPS_PROXY=<proxy IP>:<proxy port>` |

현재 AzCopy는 NTLM 또는 Kerberos 인증을 요구하는 프록시를 지원하지 않습니다.

### <a name="bypassing-a-proxy"></a>프록시 우회 ###

Windows에서 AzCopy를 실행 중이고 설정을 자동 검색하는 대신 프록시를 전혀 사용하지 _않도록_ 지시하려면 다음 명령을 사용합니다. 이 설정을 사용하면 AzCopy에서 프록시를 조회하거나 사용하지 않습니다.

| 운영 체제 | 환경 | 명령  |
|--------|-----------|----------|
| **Windows** | CMD(명령 프롬프트) | `set HTTPS_PROXY=dummy.invalid` <br>`set NO_PROXY=*`|
| **Windows** | PowerShell | `$env:HTTPS_PROXY="dummy.invalid"` <br>`$env:NO_PROXY="*"`<br>|

다른 운영 체제에서는 프록시를 사용하지 않으려면 HTTPS_PROXY 변수를 설정하지 않은 상태로 두면 됩니다.

## <a name="optimize-performance"></a>성능 최적화

성능을 벤치마크한 다음 명령과 환경 변수를 사용하여 성능과 리소스 사용 간의 최적의 절충점을 찾을 수 있습니다.

이 섹션에서는 다음과 같은 최적화 작업을 수행할 수 있습니다.

> [!div class="checklist"]
> * 벤치마크 테스트 실행
> * 처리량 최적화
> * 메모리 사용 최적화 
> * 파일 동기화 최적화

### <a name="run-benchmark-tests"></a>벤치마크 테스트 실행

특정 Blob 컨테이너 또는 파일 공유에서 성능 벤치마크 테스트를 실행하여 일반 성능 통계를 보고 성능 병목 상태를 식별할 수 있습니다. 생성된 테스트 데이터를 업로드하거나 다운로드하여 테스트를 실행할 수 있습니다. 

다음 명령을 사용하여 성능 벤치마크 테스트를 실행합니다.

| 구문 / 예제  |  코드 |
|--------|-----------|
| **구문** | `azcopy benchmark 'https://<storage-account-name>.blob.core.windows.net/<container-name>'` |
| **예제** | `azcopy benchmark 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

> [!TIP]
> 이 예에서는 경로 인수를 작은따옴표(‘’)로 묶습니다. Windows 명령 셸(cmd.exe)을 제외하고 모든 명령 셸에서 작은따옴표를 사용합니다. Windows 명령 셸(cmd.exe)을 사용하는 경우 작은따옴표(‘’) 대신 큰따옴표(“”)로 경로 인수를 묶습니다.

이 명령은 지정된 대상에 테스트 데이터를 업로드하여 성능 벤치마크를 실행합니다. 테스트 데이터는 메모리에서 생성되고 대상에 업로드된 다음, 테스트가 완료된 후 대상에서 삭제됩니다. 선택적 명령 매개 변수를 사용하여 생성할 파일 수와 파일 크기를 지정할 수 있습니다.

데이터를 다운로드하여 이 테스트를 실행하려면 `mode` 매개 변수를 `download`로 설정합니다. 자세한 참조 문서는 [azcopy benchmark](storage-ref-azcopy-bench.md)를 참조하세요. 

### <a name="optimize-throughput"></a>처리량 최적화

명령에서 `cap-mbps` 플래그를 사용하여 처리량 데이터 속도를 제한할 수 있습니다. 예를 들어 다음 명령은 작업을 계속하고 처리량을 초당 `10`MB(메가비트)로 제한합니다. 

```azcopy
azcopy jobs resume <job-id> --cap-mbps 10
```

작은 파일을 전송할 때는 처리량이 줄어들 수 있습니다. `AZCOPY_CONCURRENCY_VALUE` 환경 변수를 설정하여 처리량을 늘릴 수 있습니다. 이 변수는 발생할 수 있는 동시 요청 수를 지정합니다.  

컴퓨터에 5개 미만의 CPU가 있는 경우 이 변수의 값은 `32`로 설정됩니다. 그렇지 않으면 기본값은 16에 CPU 수를 곱한 값과 같습니다. 이 변수의 최대 기본값은 `3000`이지만 수동으로 이 값을 더 높거나 낮게 설정할 수 있습니다. 

| 운영 체제 | 명령  |
|--------|-----------|
| **Windows** | `set AZCOPY_CONCURRENCY_VALUE=<value>` |
| **Linux** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |
| **macOS** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |

`azcopy env`를 사용하여 이 변수의 현재 값을 확인합니다. 값이 비어 있으면 AzCopy 로그 파일의 시작 부분을 확인하여 사용 중인 값을 읽을 수 있습니다. 선택한 값과 선택한 이유가 여기에 보고됩니다.

이 변수를 설정하기 전에 벤치마크 테스트를 실행하는 것이 좋습니다. 벤치마크 테스트 프로세스는 권장 동시성 값을 보고합니다. 또는 네트워크 조건과 페이로드가 다를 경우 이 변수를 특정 숫자 대신 단어 `AUTO`로 설정합니다. 그러면 AzCopy가 항상 벤치마크 테스트에서 사용하는 것과 같은 자동 튜닝 프로세스를 실행합니다.

### <a name="optimize-memory-use"></a>메모리 사용 최적화

`AZCOPY_BUFFER_GB` 환경 변수를 설정하여 AzCopy가 파일을 다운로드하고 업로드할 때 버퍼링에 사용할 최대 시스템 메모리 크기를 지정합니다. 이 값을 기가바이트(GB) 단위로 표시합니다.

| 운영 체제 | 명령  |
|--------|-----------|
| **Windows** | `set AZCOPY_BUFFER_GB=<value>` |
| **Linux** | `export AZCOPY_BUFFER_GB=<value>` |
| **macOS** | `export AZCOPY_BUFFER_GB=<value>` |

> [!NOTE]
> 작업을 추적하면 항상 메모리 사용량에 추가 오버헤드가 발생합니다. 사용량은 작업의 전송 수에 따라 달라집니다. 버퍼는 메모리 사용량의 가장 큰 구성 요소입니다. `AZCOPY_BUFFER_GB`를 사용하여 대략적인 요구 사항을 충족함으로써 오버헤드를 제어할 수 있지만, 전체 메모리 사용량을 엄격하게 제한하는 플래그는 사용할 수 없습니다.

### <a name="optimize-file-synchronization"></a>파일 동기화 최적화

[sync](storage-ref-azcopy-sync.md) 명령은 대상의 모든 파일을 식별한 다음, 동기화 작업을 시작하기 전에 파일 이름과 마지막으로 수정된 타임스탬프를 비교합니다. 파일 수가 많은 경우 이 선행 처리를 제거하여 성능을 개선할 수 있습니다. 

그러려면 대신 [azcopy copy](storage-ref-azcopy-copy.md) 명령을 사용하고 `--overwrite` 플래그를 `ifSourceNewer`로 설정합니다. AzCopy는 선행 검사 및 비교를 수행하지 않고 파일을 복사할 때 파일을 비교합니다. 따라서 비교할 파일이 많은 경우 성능 우위를 제공합니다.

[azcopy copy](storage-ref-azcopy-copy.md) 명령은 대상에서 파일을 삭제하지 않으므로, 원본에 파일이 더는 존재하지 않을 때 대상에서 해당 파일을 삭제하려면 `--delete-destination` 플래그를 `true` 또는 `prompt` 값으로 설정하여 [azcopy sync](storage-ref-azcopy-sync.md) 명령을 사용합니다. 

## <a name="troubleshoot-issues"></a>문제 해결

AzCopy는 모든 작업에 대해 로그 및 계획 파일을 만듭니다. 로그를 사용하여 잠재적 문제를 조사하고 해결할 수 있습니다. 

로그에는 실패 상태(`UPLOADFAILED`, `COPYFAILED` 및 `DOWNLOADFAILED`), 전체 경로 및 실패 이유가 포함됩니다.

기본적으로 로그 및 플랜 파일은 Windows의 `%USERPROFILE%\.azcopy` 디렉터리 또는 Mac 및 Linux의 `$HOME$\.azcopy` 디렉터리에 있지만, 원하는 경우 해당 위치를 변경할 수 있습니다.

관련 오류가 파일에 표시되는 첫 번째 오류가 아닐 수도 있습니다. 네트워크 오류, 시간 제한 및 서버 사용 중 오류와 같은 오류의 경우 AzCopy는 최대 20회 다시 시도하고 일반적으로 다시 시도 프로세스는 성공합니다.  표시되는 첫 번째 오류는 성공적으로 재시도한 무해한 오류일 수 있습니다.  따라서 파일의 첫 번째 오류를 확인하는 대신 `UPLOADFAILED`, `COPYFAILED` 또는 `DOWNLOADFAILED` 근처에 있는 오류를 확인합니다. 

> [!IMPORTANT]
> Microsoft 지원에 요청을 제출하거나 타사 관련 문제를 해결할 때 실행하려는 명령의 수정된 버전을 공유합니다. 이렇게 하면 실수로 다른 사용자와 SAS를 공유하지 않습니다. 수정 버전은 로그 파일의 시작 부분에서 찾을 수 있습니다.

### <a name="review-the-logs-for-errors"></a>오류에 대한 로그 검토

다음 명령은 `04dc9ca9-158f-7945-5933-564021086c79` 로그에서 `UPLOADFAILED` 상태의 모든 오류를 가져옵니다.

**Windows(PowerShell)**

```
Select-String UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

**Linux**

```
grep UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

### <a name="view-and-resume-jobs"></a>작업 보기 및 다시 시작

각 전송 작업은 AzCopy 작업을 만듭니다. 다음 명령을 사용하여 작업 기록을 확인합니다.

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

다음 명령을 사용하여 실패/취소된 작업을 계속합니다. 이 명령은 보안상의 이유로 영구적이지 않기 때문에 SAS 토큰과 함께 식별자를 사용합니다.

```
azcopy jobs resume <job-id> --source-sas="<sas-token>"
azcopy jobs resume <job-id> --destination-sas="<sas-token>"
```

> [!TIP]
> SAS 토큰과 같은 경로 인수를 작은따옴표(‘’)로 묶습니다. Windows 명령 셸(cmd.exe)을 제외하고 모든 명령 셸에서 작은따옴표를 사용합니다. Windows 명령 셸(cmd.exe)을 사용하는 경우 작은따옴표(‘’) 대신 큰따옴표(“”)로 경로 인수를 묶습니다.

작업을 계속하면 AzCopy에서 작업 플랜 파일을 찾습니다. 플랜 파일에는 작업을 처음 만들 때 처리하기 위해 식별된 모든 파일이 나열되어 있습니다. 작업을 계속하면 AzCopy는 아직 전송되지 않은 플랜 파일에 나열된 모든 파일을 전송하려고 합니다.

## <a name="change-the-location-of-the-plan-and-log-files"></a>플랜 및 로그 파일의 위치를 변경합니다.

기본적으로 플랜과 로그 파일은 Windows의 `%USERPROFILE%\.azcopy` 디렉터리 또는 Mac 및 Linux의 `$HOME/.azcopy` 디렉터리에 있습니다. 이 위치는 변경할 수 있습니다.

### <a name="change-the-location-of-plan-files"></a>플랜 파일의 위치를 변경합니다.

해당 명령 중 하나를 사용합니다.

| 운영 체제 | 명령  |
|--------|-----------|
| **Windows** | PowerShell:`$env:AZCOPY_JOB_PLAN_LOCATION="<value>"` <br> 명령 프롬프트에서 `set AZCOPY_JOB_PLAN_LOCATION=<value>`를 사용합니다. |
| **Linux** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **macOS** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |

`azcopy env`를 사용하여 이 변수의 현재 값을 확인합니다. 값이 비어 있으면 플랜 파일이 기본 위치에 작성됩니다.

### <a name="change-the-location-of-log-files"></a>로그 파일의 위치 변경

해당 명령 중 하나를 사용합니다.

| 운영 체제 | 명령  |
|--------|-----------|
| **Windows** | PowerShell:`$env:AZCOPY_LOG_LOCATION="<value>"` <br> 명령 프롬프트에서 `set AZCOPY_LOG_LOCATION=<value>`를 사용합니다.|
| **Linux** | `export AZCOPY_LOG_LOCATION=<value>` |
| **macOS** | `export AZCOPY_LOG_LOCATION=<value>` |

`azcopy env`를 사용하여 이 변수의 현재 값을 확인합니다. 값이 비어 있으면 로그가 기본 위치에 작성됩니다.

## <a name="change-the-default-log-level"></a>기본 로그 수준 변경

기본적으로 AzCopy 로그 수준은 `INFO`로 설정됩니다. 디스크 공간 절약을 위해 로그의 세부 정보 표시를 줄이려는 경우 ``--log-level`` 옵션을 사용하여 설정을 덮어씁니다. 

사용 가능한 로그 수준은 `NONE`, `DEBUG`, `INFO`, `WARNING`, `ERROR`, `PANIC` 및 `FATAL`입니다.

## <a name="remove-plan-and-log-files"></a>플랜 및 로그 파일 제거

디스크 공간을 절약하기 위해 로컬 머신에서 모든 플랜과 로그 파일을 제거하려면 `azcopy jobs clean` 명령을 사용합니다.

하나의 작업과 연관된 플랜과 로그 파일을 제거하려면 `azcopy jobs rm <job-id>`를 사용합니다. 이 예에서 `<job-id>` 자리 표시자를 작업의 작업 ID로 바꿉니다.
