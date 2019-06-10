---
title: 구성, 최적화 및 Azure Storage를 사용 하 여 AzCopy를 문제 해결 | Microsoft Docs
description: 구성, 최적화 및 AzCopy 문제를 해결 합니다.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 18dc3e224df18c900653e4549badcdd93f0df6ec
ms.sourcegitcommit: 6932af4f4222786476fdf62e1e0bf09295d723a1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/05/2019
ms.locfileid: "66688028"
---
# <a name="configure-optimize-and-troubleshoot-azcopy"></a>구성, 최적화 및 AzCopy 문제 해결

AzCopy는 저장소 계정 간에서 blob 또는 파일을 복사 하는 데 사용할 수 있는 명령줄 유틸리티입니다. 이 문서는 고급 구성 작업을 수행 하는 데 도움이 하 고 AzCopy를 사용 하 여 발생할 수 있는 문제를 해결 하는 데 도움이 됩니다.

> [!NOTE]
> AzCopy를 사용 하 여 시작할 수 있도록 하는 콘텐츠를 찾으려는 경우 다음 문서 중 하나를 참조 하세요.
> - [AzCopy 시작](storage-use-azcopy-v10.md)
> - [AzCopy 및 Blob 스토리지를 사용하여 데이터 전송](storage-use-azcopy-blobs.md)
> - [AzCopy 및 파일 스토리지를 사용하여 데이터 전송](storage-use-azcopy-files.md)
> - [AzCopy 및 Amazon S3 버킷을 사용하여 데이터 전송](storage-use-azcopy-s3.md)

## <a name="configure-proxy-settings"></a>프록시 설정 구성

AzCopy에 대 한 프록시 설정을 구성 하려면 설정의 `https_proxy` 환경 변수입니다.

| 운영 체제 | 명령  |
|--------|-----------|
| **Windows** | `set https_proxy=<proxy IP>:<proxy port>` |
| **Linux** | `export https_proxy=<proxy IP>:<proxy port>` |
| **MacOS** | `export https_proxy=<proxy IP>:<proxy port>` |

현재, AzCopy는 NTLM 또는 Kerberos를 사용 하 여 인증이 필요한 프록시를 지원 하지 않습니다.

## <a name="optimize-throughput"></a>처리량 최적화

설정 된 `AZCOPY_CONCURRENCY_VALUE` 환경 변수 동시 요청 수를 구성 하 고 처리량 성능 및 리소스 소비를 제어할 수 있습니다. 컴퓨터에 5 개 보다 적은 Cpu 경우이 변수의 값으로 설정 됩니다 `32`합니다. 그렇지 않은 경우 기본값은 Cpu의 수를 곱한 16과 같습니다. 이 변수의 최대 기본값은 `300`를 위 또는 아래로이 값을 수동으로 설정할 수 있습니다.

| 운영 체제 | 명령  |
|--------|-----------|
| **Windows** | `set AZCOPY_CONCURRENCY_VALUE=<value>` |
| **Linux** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |
| **MacOS** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |

사용 된 `azcopy env` 이 변수의 현재 값을 확인 합니다.  값을 비어 있는 경우 해당 `AZCOPY_CONCURRENCY_VALUE` 변수를 기본값인으로 `300`합니다.

## <a name="change-the-location-of-the-log-files"></a>로그 파일의 위치 변경

기본적으로 로그 파일은 `%USERPROFILE\\.azcopy` Windows, 또는 디렉터리를 `$HOME\\.azcopy` Mac 및 Linux에서 디렉터리입니다. 이러한 명령을 사용 하 여 해야 할 경우이 위치를 변경할 수 있습니다.

| 운영 체제 | 명령  |
|--------|-----------|
| **Windows** | `set AZCOPY_LOG_LOCATION=<value>` |
| **Linux** | `export AZCOPY_LOG_LOCATION=<value>` |
| **MacOS** | `export AZCOPY_LOG_LOCATION=<value>` |

사용 된 `azcopy env` 이 변수의 현재 값을 확인 합니다. 값이 비어 있으면 로그의 기본 위치에 기록 됩니다.

## <a name="change-the-default-log-level"></a>기본 로그 수준 변경

기본적으로 AzCopy 로그 수준 설정 `INFO`합니다. 디스크 공간 절약을 위해 로그의 자세한 정도 줄이기 위해 원하는 경우이 설정을 사용 하 여 덮어쓸는 ``--log-level`` 옵션입니다. 

사용 가능한 로그 수준은: `DEBUG`, `INFO`, `WARNING`, `ERROR`합니다 `PANIC`, 및 `FATAL`합니다.

## <a name="troubleshoot-issues"></a>문제 해결

AzCopy는 모든 작업에 대 한 로그 및 계획 파일을 만듭니다. 로그를 사용하여 잠재적 문제를 조사하고 해결할 수 있습니다. 

로그 실패 상태가 포함 됩니다 (`UPLOADFAILED`, `COPYFAILED`, 및 `DOWNLOADFAILED`), 전체 경로 및 실패의 이유.

기본적으로 로그 파일과 계획에 위치한 합니다 `%USERPROFILE\\.azcopy` Windows 디렉터리 또는 `$HOME\\.azcopy` Mac 및 Linux에서 디렉터리입니다.

> [!IMPORTANT]
> Microsoft 지원 (또는 제 3 자에 관련 된 문제 해결)에 요청을 제출 하는 경우 실행 하려는 명령의 교정된 버전을 공유 합니다. 그러면 SAS가 모든 사용자와 실수로 공유 되지 않습니다. 수정 버전은 로그 파일의 시작 부분에서 찾을 수 있습니다.

### <a name="review-the-logs-for-errors"></a>오류에 대한 로그 검토

다음 명령을 사용 하 여 모든 오류를 받습니다 `UPLOADFAILED` 에서 상태를 `04dc9ca9-158f-7945-5933-564021086c79` 로그:

**Windows**

```
Select-String UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

**Linux**

```
grep UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

### <a name="view-and-resume-jobs"></a>작업 보기 및 다시 시작

각 전송 작업은 AzCopy 작업을 만듭니다. 작업의 기록을 보려면 다음 명령을 사용 합니다.

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

다음 명령을 사용 하 여 실패/취소 작업을 다시 시작 합니다. 이 명령은 보안상의 이유로 영구 아니므로 SAS 토큰과 함께 해당 id를 사용 합니다.

```
azcopy jobs resume <job-id> --source-sas="<sas-token>"
azcopy jobs resume <job-id> --destination-sas="<sas-token>"
```

작업을 다시 시작 하면 AzCopy 작업 계획 파일을 살펴봅니다. 계획 파일에 모든 파일을 식별 된 작업에서 처음 만들었을 때 처리를 나열 합니다. 작업을 다시 시작 하면 AzCopy는 이미 전송 되지 않은 계획 파일에 나열 된 파일의 모든 전송 하려고 합니다.