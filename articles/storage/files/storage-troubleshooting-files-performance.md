---
title: Azure Files 성능 문제 해결 가이드
description: Azure 파일 공유와 관련 된 해결 방법의 알려진 성능 문제
author: gunjanj
ms.service: storage
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: gunjanj
ms.subservice: files
ms.openlocfilehash: 0e11949804e0c3de52db315424f83905516b4da8
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/14/2019
ms.locfileid: "70996609"
---
# <a name="troubleshoot-azure-files-performance-issues"></a>Azure Files 성능 문제 해결

이 문서에서는 Azure 파일 공유와 관련 된 몇 가지 일반적인 문제를 나열 합니다. 이러한 문제가 발생 하는 경우 잠재적 원인과 해결 방법을 제공 합니다.

## <a name="high-latency-low-throughput-and-general-performance-issues"></a>대기 시간이 짧고 처리량이 높고 일반 성능 문제가 발생 합니다.

### <a name="cause-1-share-experiencing-throttling"></a>원인 1: 공유 발생 제한

프리미엄 공유의 기본 할당량은 100 GiB, 100 기준선 IOPS를 제공 합니다 (한 시간 동안 300까지 증가 시킬 수 있음). 프로 비전 및 IOPS와의 관계에 대 한 자세한 내용은 계획 가이드의 [프로 비전 된 공유](storage-files-planning.md#provisioned-shares) 섹션을 참조 하십시오.

공유를 제한 하 고 있는지 확인 하려면 포털에서 Azure 메트릭을 활용할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. **모든 서비스** 를 선택한 다음 **메트릭을**검색 합니다.

1. **메트릭**을 선택합니다.

1. 저장소 계정을 리소스로 선택 합니다.

1. 메트릭 네임 스페이스로 **파일** 을 선택 합니다.

1. **트랜잭션을** 메트릭으로 선택 합니다.

1. **Responsetype** 에 대 한 필터를 추가 하 고 요청에 **SuccessWithThrottling** (SMB 용) 또는 **ClientThrottlingError** (REST)의 응답 코드가 있는지 확인 합니다.

![프리미엄 파일 공유 메트릭 옵션](media/storage-troubleshooting-premium-fileshares/metrics.png)

### <a name="solution"></a>솔루션

- 공유에서 더 높은 할당량을 지정 하 여 공유 프로 비전 된 용량을 늘립니다.

### <a name="cause-2-metadatanamespace-heavy-workload"></a>원인 2: 메타 데이터/네임 스페이스 작업량이 많은 작업

대부분의 요청이 메타 데이터 중심 (예: createfile/system.windows.forms.openfiledialog.openfile/closefile/queryinfo/queryinfo) 이면 읽기/쓰기 작업과 비교할 때 대기 시간이 더 나빠질 수 있습니다.

대부분의 요청이 메타 데이터 중심 인지 확인 하기 위해 위와 동일한 단계를 사용할 수 있습니다. **Responsetype**에 대 한 필터를 추가 하는 대신 **API 이름**에 대 한 필터를 추가 합니다.

![메트릭의 API 이름 필터링](media/storage-troubleshooting-premium-fileshares/MetadataMetrics.png)

### <a name="workaround"></a>해결 방법

- 메타 데이터 작업의 수를 줄이도록 응용 프로그램을 수정할 수 있는지 확인 합니다.

### <a name="cause-3-single-threaded-application"></a>원인 3: 단일 스레드 응용 프로그램

고객이 사용 하는 응용 프로그램이 단일 스레드 인 경우 프로 비전 된 공유 크기에 따라 허용 되는 최대 크기 보다 훨씬 낮은 IOPS/처리량이 발생할 수 있습니다.

### <a name="solution"></a>솔루션

- 스레드 수를 늘려 응용 프로그램 병렬 처리를 늘립니다.
- 병렬 처리를 사용할 수 있는 응용 프로그램으로 전환 합니다. 예를 들어 복사 작업의 경우 고객은 Windows 클라이언트의 AzCopy 또는 RoboCopy를 사용 하거나 Linux 클라이언트에서 **parallel** 명령을 사용할 수 있습니다.

## <a name="very-high-latency-for-requests"></a>요청에 대 한 대기 시간 매우 높음

### <a name="cause"></a>원인

클라이언트 VM은 파일 공유와 다른 지역에 있을 수 있습니다.

### <a name="solution"></a>솔루션

- 파일 공유와 동일한 지역에 있는 VM에서 응용 프로그램을 실행 합니다.

## <a name="client-unable-to-achieve-maximum-throughput-supported-by-the-network"></a>클라이언트에서 네트워크에 의해 지원 되는 최대 처리량을 달성할 수 없습니다.

이에 대 한 한 가지 잠재적인 원인은 SMB 다중 채널 지원 부족입니다. 현재 Azure 파일 공유는 단일 채널만 지원 하므로 클라이언트 VM에서 서버로의 연결은 하나 뿐입니다. 이 단일 연결은 클라이언트 VM의 단일 코어로 해석 VM에서 달성 가능한 최대 처리량은 단일 코어에 의해 바인딩됩니다.

### <a name="workaround"></a>해결 방법

- 코어 크기가 더 큰 VM을 가져오면 처리량을 향상 시킬 수 있습니다.
- 여러 Vm에서 클라이언트 응용 프로그램을 실행 하면 처리량이 증가 합니다.

- 가능한 경우 REST Api를 사용 합니다.

## <a name="throughput-on-linux-clients-is-significantly-lower-when-compared-to-windows-clients"></a>Windows 클라이언트에 비해 Linux 클라이언트의 처리량은 훨씬 낮습니다.

### <a name="cause"></a>원인

Linux에서 SMB 클라이언트를 구현 하는 것과 관련 하 여 알려진 문제입니다.

### <a name="workaround"></a>해결 방법

- 부하를 여러 Vm에 분산 합니다.
- 동일한 VM에서 **nosharesock** 옵션을 사용 하 여 여러 탑재 위치를 사용 하 고 이러한 탑재 지점의 부하를 분산 합니다.
- Linux에서 **nostrictsync** 옵션으로 탑재를 시도 하 여 모든 fsync 호출에서 SMB 플러시를 방지 합니다. Azure Files의 경우이 옵션은 데이터 consistentcy을 방해 하지 않지만 디렉터리 목록 (**ls-l** 명령)에서 오래 된 파일 메타 데이터를 생성 하 게 될 수 있습니다. 파일의 메타 데이터를 직접 쿼리하면 (**stat** 명령) 최신 파일 메타 데이터가 반환 됩니다.

## <a name="high-latencies-for-metadata-heavy-workloads-involving-extensive-openclose-operations"></a>광범위 한 열기/닫기 작업을 포함 하는 메타 데이터 사용량이 많은 작업에 대 한 대기 시간이 깁니다.

### <a name="cause"></a>원인

디렉터리 임대에 대 한 지원이 부족 합니다.

### <a name="workaround"></a>해결 방법

- 가능 하면 짧은 시간 내에 동일한 디렉터리에 대 한 과도 한 열기/닫기 핸들을 사용 하지 마십시오.
- Linux vm의 경우 **actimeo =\<sec >** 를 탑재 옵션으로 지정 하 여 디렉터리 항목 캐시 제한 시간을 늘립니다. 기본적으로 1 초 이므로 3 또는 5와 같은 큰 값이 도움이 될 수 있습니다.
- Linux Vm의 경우 커널을 4.20 이상으로 업그레이드 합니다.

## <a name="low-iops-on-centosrhel"></a>CentOS/RHEL의 낮은 IOPS

### <a name="cause"></a>원인

CentOS/RHEL에서 1 보다 큰 IO 깊이가 지원 되지 않습니다.

### <a name="workaround"></a>해결 방법

- CentOS 8/RHEL 8로 업그레이드 합니다.
- Ubuntu로 변경 합니다.

## <a name="slow-file-copying-to-and-from-azure-files-in-linux"></a>Linux에서 Azure Files와 서로 파일을 복사하는 속도 느림

Azure Files에 대 한 파일 복사 속도가 느려지는 경우 Linux 문제 해결 가이드의 [linux의 Azure Files에 대 한 저속 파일 복사](storage-troubleshoot-linux-file-connection-problems.md#slow-file-copying-to-and-from-azure-files-in-linux) 섹션을 참조 하세요.

## <a name="jitterysaw-tooth-pattern-for-iops"></a>떨림/톱-IOPS의 이빨 패턴

### <a name="cause"></a>원인

클라이언트 응용 프로그램이 지속적으로 기준선 IOPS를 초과 합니다. 현재, 클라이언트에서 기준선 IOPS를 초과 하는 경우 서비스에 의해 제한 되는 요청 로드의 서비스 측 다듬기는 없습니다. 이렇게 조정 하면 클라이언트에서 떨림/톱 IOPS 패턴이 발생할 수 있습니다. 이 경우 클라이언트에서 달성 한 평균 IOPS는 기준선 IOPS 보다 낮을 수 있습니다.

### <a name="workaround"></a>해결 방법

- 공유가 제한 되지 않도록 클라이언트 응용 프로그램에서 요청 부하를 줄입니다.
- 공유가 제한 되지 않도록 공유의 할당량을 늘립니다.

## <a name="excessive-directoryopendirectoryclose-calls"></a>과도 한 DirectoryOpen/Directoryopen 호출

### <a name="cause"></a>원인

DirectoryOpen/Directoryopen 호출 수가 최상위 API 호출 중에 있고 클라이언트에서 많은 호출을 수행 하지 않을 것으로 생각 되는 경우 Azure 클라이언트 VM에 설치 된 바이러스 백신에 문제가 있을 수 있습니다.

### <a name="workaround"></a>해결 방법

- 이 문제에 대 한 해결 방법은 [Windows 용 4 월 플랫폼 업데이트](https://support.microsoft.com/help/4052623/update-for-windows-defender-antimalware-platform)에서 확인할 수 있습니다.

## <a name="file-creation-is-slower-than-expected"></a>파일 만들기가 예상 보다 느립니다.

### <a name="cause"></a>원인

많은 수의 파일을 만드는 데 사용 하는 워크 로드는 프리미엄 파일 공유와 표준 파일 공유의 성능 간에 상당한 차이를 표시 하지 않습니다.

### <a name="workaround"></a>해결 방법

- 없음

## <a name="slow-performance-from-windows-81-or-server-2012-r2"></a>Windows 8.1 또는 Server 2012 r 2의 성능 저하

### <a name="cause"></a>원인

IO를 많이 사용 하는 작업에 대 한 Azure Files 액세스 하는 데 예상 되는 대기 시간

### <a name="workaround"></a>해결 방법

- 사용 가능한 [핫픽스](https://support.microsoft.com/help/3114025/slow-performance-when-you-access-azure-files-storage-from-windows-8-1)를 설치 합니다.
