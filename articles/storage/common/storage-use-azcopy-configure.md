---
title: Azure Storage를 사용 하 여 AzCopy 구성, 최적화 및 문제 해결 | Microsoft Docs
description: AzCopy를 구성 하 고 최적화 하 고 문제를 해결 합니다.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 3843eb2e906e3fb8d390e509e17117b7849ac220
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72244710"
---
# <a name="configure-optimize-and-troubleshoot-azcopy"></a>AzCopy 구성, 최적화 및 문제 해결

AzCopy는 blob 또는 파일을 저장소 계정에 복사 하는 데 사용할 수 있는 명령줄 유틸리티입니다. 이 문서는 고급 구성 작업을 수행 하는 데 도움이 되며 AzCopy를 사용 하는 경우 발생할 수 있는 문제를 해결 하는 데 도움이 됩니다.

> [!NOTE]
> AzCopy을 시작 하는 데 도움이 되는 콘텐츠를 찾고 있는 경우 다음 문서를 참조 하세요.
> - [AzCopy 시작](storage-use-azcopy-v10.md)
> - [AzCopy 및 Blob 스토리지를 사용하여 데이터 전송](storage-use-azcopy-blobs.md)
> - [AzCopy 및 파일 스토리지를 사용하여 데이터 전송](storage-use-azcopy-files.md)
> - [AzCopy 및 Amazon S3 버킷을 사용하여 데이터 전송](storage-use-azcopy-s3.md)

## <a name="configure-proxy-settings"></a>프록시 설정 구성

AzCopy에 대 한 프록시 설정을 구성 하려면 `https_proxy` 환경 변수를 설정 합니다. Windows에서 AzCopy를 실행 하는 경우 AzCopy에서 프록시 설정을 자동으로 검색 하므로 Windows에서이 설정을 사용할 필요가 없습니다. Windows에서이 설정을 사용 하도록 선택 하면 자동 검색이 재정의 됩니다.

| 운영 체제 | 명령  |
|--------|-----------|
| **Windows** | 명령 프롬프트에서 `set https_proxy=<proxy IP>:<proxy port>`을 사용 합니다.<br> PowerShell에서 다음을 사용 합니다. `$env:https_proxy="<proxy IP>:<proxy port>"`|
| **Linux** | `export https_proxy=<proxy IP>:<proxy port>` |
| **MacOS** | `export https_proxy=<proxy IP>:<proxy port>` |

현재 AzCopy는 NTLM 또는 Kerberos 인증을 요구 하는 프록시를 지원 하지 않습니다.

## <a name="optimize-throughput"></a>처리량 최적화

@No__t-0 플래그를 사용 하 여 처리량 데이터 속도를 최대값으로 지정할 수 있습니다. 예를 들어 다음 명령은 @no__t 처리량을 초당-0 메가 비트 (MB)로 제한 합니다.

```azcopy
azcopy cap-mbps 10
```

작은 파일을 전송할 때 처리량이 줄어들 수 있습니다. @No__t-0 환경 변수를 설정 하 여 처리량을 늘릴 수 있습니다. 이 변수는 발생할 수 있는 동시 요청 수를 지정 합니다.  컴퓨터에 5 개 미만의 Cpu가 있는 경우이 변수의 값은 `32`으로 설정 됩니다. 그렇지 않으면 기본값은 16에 Cpu 수를 곱한 값과 같습니다. 이 변수의 최대 기본값은 0 @no__t 이지만이 값을 더 높거나 낮게 수동으로 설정할 수 있습니다.

| 운영 체제 | 명령  |
|--------|-----------|
| **Windows** | `set AZCOPY_CONCURRENCY_VALUE=<value>` |
| **Linux** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |
| **MacOS** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |

이 변수의 현재 값을 확인 하려면 `azcopy env`을 사용 합니다.  값이 비어 있으면 `AZCOPY_CONCURRENCY_VALUE` 변수가 기본값 `300`로 설정 됩니다.

## <a name="change-the-location-of-the-log-files"></a>로그 파일의 위치 변경

기본적으로 로그 파일은 Windows의 `%USERPROFILE%\.azcopy` 디렉터리 또는 Mac 및 Linux의 `$HOME\\.azcopy` 디렉터리에 있습니다. 이러한 명령을 사용 하 여 필요한 경우이 위치를 변경할 수 있습니다.

| 운영 체제 | 명령  |
|--------|-----------|
| **Windows** | `set AZCOPY_LOG_LOCATION=<value>` |
| **Linux** | `export AZCOPY_LOG_LOCATION=<value>` |
| **MacOS** | `export AZCOPY_LOG_LOCATION=<value>` |

이 변수의 현재 값을 확인 하려면 `azcopy env`을 사용 합니다. 값이 비어 있으면 로그가 기본 위치에 기록 됩니다.

## <a name="change-the-default-log-level"></a>기본 로그 수준 변경

기본적으로 AzCopy 로그 수준은 `INFO`으로 설정 됩니다. 로그의 자세한 정도를 줄이기 위해 디스크 공간을 절약 하려면 ``--log-level`` 옵션을 사용 하 여이 설정을 덮어씁니다. 

사용 가능한 로그 수준은 `DEBUG`, `INFO`, `WARNING`, `ERROR`, `PANIC` 및 `FATAL`입니다.

## <a name="troubleshoot-issues"></a>문제 해결

AzCopy는 모든 작업에 대해 로그 및 계획 파일을 만듭니다. 로그를 사용하여 잠재적 문제를 조사하고 해결할 수 있습니다. 

로그에는 실패 상태 (`UPLOADFAILED`, `COPYFAILED`, `DOWNLOADFAILED`), 전체 경로 및 오류의 원인이 포함 됩니다.

기본적으로 로그 및 계획 파일은 Windows의 `%USERPROFILE\\.azcopy` 디렉터리 또는 Mac 및 Linux의 `$HOME\\.azcopy` 디렉터리에 있습니다.

> [!IMPORTANT]
> Microsoft 지원에 대 한 요청을 제출 하거나, 타사와 관련 된 문제를 해결 하는 경우 실행 하려는 명령의 교정 된 버전을 공유 합니다. 이렇게 하면 SAS를 실수로 공유 하지 않습니다. 수정 버전은 로그 파일의 시작 부분에서 찾을 수 있습니다.

### <a name="review-the-logs-for-errors"></a>오류에 대한 로그 검토

다음 명령은 `04dc9ca9-158f-7945-5933-564021086c79` 로그에서 `UPLOADFAILED` 상태의 모든 오류를 가져옵니다.

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

작업을 다시 시작 하면 AzCopy에서 작업 계획 파일을 찾습니다. 계획 파일에는 작업을 처음 만들 때 처리를 위해 식별 된 모든 파일이 나열 됩니다. 작업을 다시 시작 하면 AzCopy는 아직 전송 되지 않은 계획 파일에 나열 된 모든 파일을 전송 하려고 시도 합니다.
