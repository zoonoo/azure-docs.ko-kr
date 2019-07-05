---
title: Azure 파일 성능 문제 해결 가이드
description: 알려진 Azure 파일 공유 및 관련된 해결 방법을 사용 하 여 성능 문제입니다.
services: storage
author: gunjanj
ms.service: storage
ms.topic: article
ms.date: 04/25/2019
ms.author: gunjanj
ms.subservice: files
ms.openlocfilehash: 8c35501f3afbeed519fb5304229f25be1cbd5f9b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445665"
---
# <a name="troubleshoot-azure-files-performance-issues"></a>Azure Files 성능 문제 해결

이 문서에서는 Azure 파일 공유와 관련 된 몇 가지 일반적인 문제를 나열 합니다. 이러한 문제가 발생 하는 경우 잠재적 원인 및 해결 방법을 제공 합니다.

## <a name="high-latency-low-throughput-and-general-performance-issues"></a>높은 대기 시간, 낮은 처리량 및 일반적인 성능 문제

### <a name="cause-1-share-experiencing-throttling"></a>원인 1: 공유 경험 제한

Premium 공유에 기본 할당량 100gib 100 기준 IOPS (1 시간 동안 최대 300 버스트 가능성이) 제공 하는 경우 프로 비전 및 iops 간의 관계에 대 한 자세한 내용은 참조는 [공유를 프로 비전](storage-files-planning.md#provisioned-shares) 계획 가이드의 섹션입니다.

공유 제한 하려는 경우를 확인 하려면 포털에서 Azure Metrics를 활용할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. 선택 **모든 서비스** 검색할 **메트릭**합니다.

1. **메트릭**을 선택합니다.

1. 리소스와 저장소 계정을 선택 합니다.

1. 선택 **파일** 메트릭 네임 스페이스입니다.

1. 선택 **트랜잭션을** 을 기준으로 합니다.

1. 에 대 한 필터 추가 **ResponseType** 요청 응답 코드가 있는지 확인 합니다 **SuccessWithThrottling** (SMB)에 대 한 또는 **ClientThrottlingError** (REST)에 대 한 합니다.

![프리미엄 파일 공유에 대 한 메트릭 옵션](media/storage-troubleshooting-premium-fileshares/metrics.png)

### <a name="solution"></a>해결 방법

- 공유에서 더 높은 할당량을 지정 하 여 프로 비전 된 용량을 공유 하는 증가 합니다.

### <a name="cause-2-metadatanamespace-heavy-workload"></a>원인 2: 메타 데이터/네임 스페이스에 대 한 작업량이 정보

요청 중 대부분은 메타 데이터 (예: createfile/openfile/closefile/queryinfo/querydirectory) 중심 다음 대기 시간 읽기/쓰기 작업을 비교할 때 저하 됩니다.

대부분의 요청은 메타 데이터 중심 경우를 확인 하려면 위와 동일한 단계를 사용할 수 있습니다. 에 대 한 필터를 추가 하는 대신 제외 **ResponseType**에 대 한 필터를 추가할 **API 이름**합니다.

![API 이름에 대 한 메트릭을 필터링합니다](media/storage-troubleshooting-premium-fileshares/MetadataMetrics.png)

### <a name="workaround"></a>해결 방법

- 메타 데이터 작업의 수를 줄이기 위해 응용 프로그램을 수정할 수 있는지 확인 합니다.

### <a name="cause-3-single-threaded-application"></a>원인 3: 단일 스레드 응용 프로그램

고객에 의해 사용 중인 응용 프로그램을 단일 스레드 사용자 프로 비전 된 공유 크기를 기준으로 사용 가능한 최대값 보다 적으면 P s/처리량에 발생할 수 있습니다.

### <a name="solution"></a>해결 방법

- 스레드 수를 늘려 병렬 처리를 응용 프로그램을 늘립니다.
- 병렬 처리 가능 응용 프로그램으로 전환 합니다. 예를 들어 복사 작업에 대 한 고객은 사용할 수 AzCopy 또는 RoboCopy Windows 클라이언트에서 또는 **병렬** Linux 클라이언트에서 명령을 합니다.

## <a name="very-high-latency-for-requests"></a>요청에 대 한 매우 높은 대기 시간

### <a name="cause"></a>원인

파일 공유와 다른 지역에서 클라이언트 VM을 찾을 수 없습니다.

### <a name="solution"></a>해결 방법

- 파일 공유와 동일한 지역에 있는 VM에서 응용 프로그램을 실행 합니다.

## <a name="client-unable-to-achieve-maximum-throughput-supported-by-the-network"></a>클라이언트는 네트워크에서 지 원하는 최대 처리량을 달성할 수 없습니다.

한 가지이 원인은 부족 fo SMB 다중 채널 지원 합니다. 현재 Azure 파일 공유 지원 단일 채널 이므로 서버에 클라이언트 VM에서에서 하나의 연결 합니다. 이 단일 연결에서 단일 코어 VM에서 달성 가능한 최대 처리량 바인딩되어 있으므로 클라이언트 VM에서 단일 코어에 측정값이 한 됩니다.

### <a name="workaround"></a>해결 방법

- 더 큰 코어를 사용 하 여 VM을 가져오는 처리량을 개선 하는 데 도움이 됩니다.
- 여러 Vm에서 클라이언트 응용 프로그램을 실행 하면 처리량이 증가 합니다.
- 가능한 경우 REST Api를 사용 합니다.

## <a name="throughput-on-linux-clients-is-significantly-lower-when-compared-to-windows-clients"></a>Linux 클라이언트에 대 한 처리량 Windows 클라이언트에 비해 훨씬 낮습니다.

### <a name="cause"></a>원인

Linux SMB 클라이언트의 구현 사용 하 여 알려진된 문제입니다.

### <a name="workaround"></a>해결 방법

- 여러 Vm에서 부하 분산
- 동일한 VM에서 사용 하 여 여러 개의 탑재 지점을 사용 하 여 **nosharesock** 탑재 지점 옵션 및 이러한 항목 간에 부하를 분산 합니다.

## <a name="high-latencies-for-metadata-heavy-workloads-involving-extensive-openclose-operations"></a>광범위 한 열기/닫기 작업에 포함 된 메타 데이터 과도 한 워크 로드에 대 한 대기 시간이 있습니다.

### <a name="cause"></a>원인

디렉터리 임대에 대 한 지원 부족 합니다.

### <a name="workaround"></a>해결 방법

- 가능한 경우 짧은 기간 내 같은 디렉터리에 과도 하 게 여는 / 닫는 핸들을 방지 합니다.
- Linux Vm에 대 한 디렉터리 항목 캐시 시간 제한을 지정 하 여 늘립니다 **actimeo =<sec>**  탑재 옵션으로 합니다. 기본적으로 이므로 1 초 보다 큰 값이 3 또는 5 도움이 될 수 있습니다.
- Linux Vm에 대 한 커널 4.20 이상으로 업그레이드 합니다.

## <a name="low-iops-on-centosrhel"></a>CentOS/RHEL에서 낮은 IOPS

### <a name="cause"></a>원인

1 보다 큰 IO 크기 CentOS/RHEL에서 지원 되지 않습니다.

### <a name="workaround"></a>해결 방법

- CentOS 8로 업그레이드 / RHEL 8입니다.
- Ubuntu를 변경 합니다.

## <a name="slow-file-copying-to-and-from-azure-files-in-linux"></a>Linux에서 Azure Files와 서로 파일을 복사하는 속도 느림

느린 파일 복사를 Azure Files에서 발생 하는 경우를 확인해 보세요 합니다 [느린 파일 복사를 Linux에서 Azure files에서](storage-troubleshoot-linux-file-connection-problems.md#slow-file-copying-to-and-from-azure-files-in-linux) Linux 문제 해결 섹션에 나와 있습니다.

## <a name="jitterysaw-tooth-pattern-for-iops"></a>IOPS에 대 한 흔들림/톱니 패턴

### <a name="cause"></a>원인

클라이언트 응용 프로그램은 일관 되 게 기준 IOPS를 초과합니다. 현재는 서비스 쪽 되지 않으며 요청 부하의 다듬기, 따라서 클라이언트 기준 IOPS를 초과 하는 경우에 제한 상황이 발생 서비스입니다. 흔들림/톱니 IOPS 패턴을 발생 하는 클라이언트에서 해당 제한 될 수 있습니다. 이 경우 클라이언트에서 수행 하는 평균 IOPS 기준 IOPS 보다 낮은 수 있습니다.

### <a name="workaround"></a>해결 방법

- 공유에 제한 상황이지 않습니다 있도록 클라이언트 응용 프로그램에서 요청 부하를 줄입니다.
- 공유에 제한 상황이지 않습니다 있도록 공유의 할당량을 늘려야 합니다.

## <a name="excessive-directoryopendirectoryclose-calls"></a>과도 한 DirectoryOpen/DirectoryClose 호출

### <a name="cause"></a>원인

DirectoryOpen/DirectoryClose 통화 건수가 상위 API 호출 간에 많은 호출을 Azure 클라이언트 VM에 설치 된 바이러스 백신을 사용 하 여 문제 수 수 있도록 클라이언트 하지 않을 경우.

### <a name="workaround"></a>해결 방법

- 이 문제에 대 한 수정에서 사용할 수는 [월 플랫폼 업데이트에 대 한 Windows](https://support.microsoft.com/help/4052623/update-for-windows-defender-antimalware-platform)합니다.

## <a name="file-creation-is-slower-than-expected"></a>파일 생성 속도가 예상 보다 느린

### <a name="cause"></a>원인

많은 수의 파일을 만드는 방법에 의존 하는 워크 로드에는 premium 파일 공유의 성능 및 표준 파일 공유는 상당한 차이가 표시 되지 않습니다.

### <a name="workaround"></a>해결 방법

- 없음.

## <a name="slow-performance-from-windows-81-or-server-2012-r2"></a>Windows 8.1 또는 Server 2012 R2에서 성능 저하

### <a name="cause"></a>원인

IO 집약적인 워크 로드에 대 한 Azure 파일에 액세스 하는 대기 시간 보다 더 높은 필요 합니다.

### <a name="workaround"></a>해결 방법

- 사용 가능한 설치 [핫픽스](https://support.microsoft.com/help/3114025/slow-performance-when-you-access-azure-files-storage-from-windows-8-1)합니다.