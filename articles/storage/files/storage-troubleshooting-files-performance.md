---
title: Azure 파일 성능 문제 해결 가이드
description: Azure 파일 공유 및 관련 해결 방법을 다루는 알려진 성능 문제입니다.
author: gunjanj
ms.service: storage
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: gunjanj
ms.subservice: files
ms.openlocfilehash: 09e55abcd97317b87f8a272afa51c6b4ace572e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77598088"
---
# <a name="troubleshoot-azure-files-performance-issues"></a>Azure 파일 성능 문제 해결

이 문서에서는 Azure 파일 공유와 관련된 몇 가지 일반적인 문제를 나열합니다. 이러한 문제가 발생할 때 잠재적인 원인 및 해결 방법을 제공합니다.

## <a name="high-latency-low-throughput-and-general-performance-issues"></a>높은 대기 시간, 낮은 처리량 및 일반 성능 문제

### <a name="cause-1-share-experiencing-throttling"></a>원인 1: 공유 발생 제한

프리미엄 공유의 기본 할당량은 100 GiB이며, 100개의 기준 IOPS를 제공합니다(한 시간 동안 최대 300개까지 버스트할 수 있음). 프로비저닝 및 IOPS와의 관계에 대한 자세한 내용은 계획 설명서의 [프로비저닝 된 공유](storage-files-planning.md#understanding-provisioning-for-premium-file-shares) 섹션을 참조하십시오.

공유가 제한되고 있는지 확인하려면 포털에서 Azure 메트릭을 활용할 수 있습니다.

1. [Azure 포털에](https://portal.azure.com)로그인합니다.

1. **모든 서비스를** 선택한 다음 **메트릭을 검색합니다.**

1. **메트릭을 선택합니다.**

1. 저장소 계정을 리소스로 선택합니다.

1. **파일을** 메트릭 네임스페이스로 선택합니다.

1. **트랜잭션을** 메트릭으로 선택합니다.

1. **ResponseType에** 대한 필터를 추가하고 요청에 **SuccessWithrottling(SMB의** 경우) 또는 **ClientThrottlingError(REST의** 경우)의 응답 코드가 있는지 확인합니다.

![프리미엄 파일공유에 대한 메트릭 옵션](media/storage-troubleshooting-premium-fileshares/metrics.png)

> [!NOTE]
> 파일 공유가 제한되는 경우 경고를 받으려면 [파일 공유가 제한되는 경우 경고를 만드는 방법을](#how-to-create-an-alert-if-a-file-share-is-throttled)참조하세요.

### <a name="solution"></a>해결 방법

- 공유에 더 높은 할당량을 지정하여 공유 프로비저닝 된 용량을 늘립니다.

### <a name="cause-2-metadatanamespace-heavy-workload"></a>원인 2: 메타데이터/네임스페이스 무거운 워크로드

대부분의 요청이 메타데이터 중심인 경우(예: 파일/파일/closefile/queryinfo/querydirectory) 읽기/쓰기 작업에 비해 대기 시간이 더 나빠집니다.

대부분의 요청이 메타데이터 중심인지 확인하려면 위와 동일한 단계를 사용할 수 있습니다. **ResponseType에**대한 필터를 추가하는 대신 **API 이름에**대한 필터를 추가합니다.

![메트릭의 API 이름 필터링](media/storage-troubleshooting-premium-fileshares/MetadataMetrics.png)

### <a name="workaround"></a>해결 방법

- 메타데이터 작업 수를 줄이기 위해 응용 프로그램을 수정할 수 있는지 확인합니다.
- 파일 공유에 VHD를 추가하고 클라이언트에서 SMB 위에 VHD를 탑재하여 데이터에 대한 파일 작업을 수행합니다. 이 방법은 단일 기록기 및 여러 판독기 시나리오에서 작동하며 메타데이터 작업을 로컬로 허용하여 로컬 직접 연결된 저장소와 유사한 성능을 제공합니다.

### <a name="cause-3-single-threaded-application"></a>원인 3: 단일 스레드 응용 프로그램

고객이 사용하는 응용 프로그램이 단일 스레드인 경우 프로비저닝된 공유 크기에 따라 가능한 최대 값보다 IOPS/처리량이 현저히 낮아질 수 있습니다.

### <a name="solution"></a>해결 방법

- 스레드 수를 늘려 응용 프로그램 병렬성을 늘립니다.
- 병렬 처리가 가능한 응용 프로그램으로 전환합니다. 예를 들어 복사 작업의 경우 고객은 Windows 클라이언트에서 AzCopy 또는 RoboCopy또는 Linux 클라이언트의 **병렬** 명령을 사용할 수 있습니다.

## <a name="very-high-latency-for-requests"></a>요청에 대한 매우 높은 대기 시간

### <a name="cause"></a>원인

클라이언트 VM은 파일 공유가 아닌 다른 지역에 위치할 수 있습니다.

### <a name="solution"></a>해결 방법

- 파일 공유와 동일한 지역에 있는 VM에서 응용 프로그램을 실행합니다.

## <a name="client-unable-to-achieve-maximum-throughput-supported-by-the-network"></a>네트워크에서 지원하는 최대 처리량을 달성할 수 없는 클라이언트

이 것의 한 가지 잠재적인 원인은 부족 한 SMB 멀티 채널 지원. 현재 Azure 파일은 단일 채널만 지원하므로 클라이언트 VM에서 서버에 대한 연결이 하나만 있습니다. 이 단일 연결은 클라이언트 VM의 단일 코어에 고정되므로 VM에서 달성할 수 있는 최대 처리량은 단일 코어에 의해 바인딩됩니다.

### <a name="workaround"></a>해결 방법

- 코어가 큰 VM을 얻는 것은 처리량을 개선하는 데 도움이 될 수 있습니다.
- 여러 VM에서 클라이언트 응용 프로그램을 실행하면 처리량이 증가합니다.

- 가능한 경우 REST API를 사용합니다.

## <a name="throughput-on-linux-clients-is-significantly-lower-when-compared-to-windows-clients"></a>Linux 클라이언트의 처리량은 Windows 클라이언트에 비해 현저히 낮습니다.

### <a name="cause"></a>원인

이것은 Linux에서 SMB 클라이언트를 구현하는 것으로 알려진 문제입니다.

### <a name="workaround"></a>해결 방법

- 여러 VM에 부하를 분산시입니다.
- 동일한 VM에서 **노쉐삭** 옵션을 사용하여 여러 마운트 지점을 사용하고 이러한 마운트 지점에 부하를 분산시입니다.
- Linux에서는 모든 **fsync** 호출에서 SMB 플러시를 강제로 사용하지 않도록 **nostrictsync** 옵션으로 장착해 보십시오. Azure Files의 경우 이 옵션은 데이터 일관성을 방해하지 않지만 디렉터리**목록(ls -l** 명령)에서 파일 메타데이터가 부실할 수 있습니다. **파일(통계** 명령)의 메타데이터를 직접 쿼리하면 최신 파일 메타데이터가 반환됩니다.

## <a name="high-latencies-for-metadata-heavy-workloads-involving-extensive-openclose-operations"></a>광범위한 오픈/클로즈 작업과 관련된 메타데이터 과중 워크로드에 대한 높은 대기 시간입니다.

### <a name="cause"></a>원인

디렉터리 임대에 대한 지원 부족.

### <a name="workaround"></a>해결 방법

- 가능하면 짧은 시간 내에 동일한 디렉터리에서 핸들을 과도하게 열지 마십시오.
- Linux VM의 경우 **actimeo=\<초>** 마운트 옵션으로 지정하여 디렉터리 항목 캐시 시간 시간을 늘립니다. 기본적으로 1초이므로 3~5초와 같은 값이 클수록 도움이 될 수 있습니다.
- Linux VM의 경우 커널을 4.20 이상으로 업그레이드합니다.

## <a name="low-iops-on-centosrhel"></a>CentOS/RHEL의 낮은 IOPS

### <a name="cause"></a>원인

IO 깊이가 하나보다 크면 CentOS/RHEL에서 지원되지 않습니다.

### <a name="workaround"></a>해결 방법

- CentOS 8 / RHEL 8로 업그레이드하십시오.
- 우분투로 변경합니다.

## <a name="slow-file-copying-to-and-from-azure-files-in-linux"></a>Linux에서 Azure Files와 서로 파일을 복사하는 속도 느림

Azure 파일에서 파일 복사 속도가 느린 경우 Linux 문제 해결 가이드에서 [Linux의 Azure 파일로 및 Azure 파일에서 복사하는 속도가 느린](storage-troubleshoot-linux-file-connection-problems.md#slow-file-copying-to-and-from-azure-files-in-linux) 파일을 살펴보십시오.

## <a name="jitterysaw-tooth-pattern-for-iops"></a>IOPS에 대한 과민 / 톱니 패턴

### <a name="cause"></a>원인

클라이언트 응용 프로그램은 일관되게 기준 IOPS를 초과합니다. 현재 는 요청 로드의 서비스 측 스무딩이 없으므로 클라이언트가 기준 IOPS를 초과하면 서비스에 의해 제한됩니다. 이러한 제한을 사용하면 클라이언트가 과민/톱니 IOPS 패턴을 경험할 수 있습니다. 이 경우 클라이언트가 달성한 평균 IOPS가 기준 IOPS보다 낮을 수 있습니다.

### <a name="workaround"></a>해결 방법

- 공유가 제한되지 않도록 클라이언트 응용 프로그램의 요청 로드를 줄입니다.
- 공유가 제한되지 않도록 공유 할당량을 늘립니다.

## <a name="excessive-directoryopendirectoryclose-calls"></a>과도한 디렉토리열기/디렉터리닫기 호출

### <a name="cause"></a>원인

DirectoryOpen/DirectoryClose 호출 수가 최상위 API 호출 중 하나이고 클라이언트가 많은 호출을 할 것으로 예상하지 않는 경우 Azure 클라이언트 VM에 설치된 바이러스 백신에 문제가 있을 수 있습니다.

### <a name="workaround"></a>해결 방법

- 이 문제에 대 한 수정 프로그램은 [Windows에 대 한 4 월 플랫폼 업데이트에서](https://support.microsoft.com/help/4052623/update-for-windows-defender-antimalware-platform)사용할 수 있습니다.

## <a name="file-creation-is-slower-than-expected"></a>파일 생성이 예상보다 느려집니다.

### <a name="cause"></a>원인

많은 수의 파일을 만드는 데 의존하는 워크로드는 프리미엄 파일 공유와 표준 파일 공유의 성능 간에 큰 차이를 보이지 않습니다.

### <a name="workaround"></a>해결 방법

- 없음

## <a name="slow-performance-from-windows-81-or-server-2012-r2"></a>Windows 8.1 또는 서버 2012 R2의 성능 저하

### <a name="cause"></a>원인

IO 집약적 워크로드에 대한 Azure 파일에 액세스하는 예상 대기 시간보다 높습니다.

### <a name="workaround"></a>해결 방법

- 사용 가능한 [핫픽스를](https://support.microsoft.com/help/3114025/slow-performance-when-you-access-azure-files-storage-from-windows-8-1)설치합니다.

## <a name="how-to-create-an-alert-if-a-file-share-is-throttled"></a>파일 공유가 제한되는 경우 경고를 만드는 방법

1. Azure [포털에서](https://portal.azure.com) **모니터**를 클릭합니다. 

2. 경고를 클릭한 다음 **+ 새 경고 규칙을** **클릭합니다.**

3. **선택을** 클릭하여 경고할 파일 공유가 포함된 **저장소 계정/파일** 리소스를 선택한 다음 **완료 를 클릭합니다.** 예를 들어 저장소 계정 이름이 contoso인 경우 contoso/파일 리소스를 선택합니다.

4. 조건을 추가하려면 **추가를** 클릭합니다.

5. 저장소 계정에 대해 지원되는 신호 목록이 표시되며 **트랜잭션** 메트릭을 선택합니다.

6. 신호 논리 블레이드 **구성에서** **응답 유형** 차원으로 이동하여 차원 **값** 드롭다운을 클릭하고 **SuccessWith로틀링(SMB의** 경우) 또는 **ClientThrottlingError(REST의** 경우)를 선택합니다. 

  > [!NOTE]
  > SuccessWithThrottling 또는 ClientThrottlingError 차원 값이 나열되지 않은 경우 리소스가 제한되지 않음을 의미합니다.  차원 값을 **+** 추가하려면 **치수 값** 드롭다운 옆을 클릭하고 **SuccessWith로틀** 링 또는 **ClientThrottlingError를**입력하고 **확인을** 클릭한 다음 단계를 #6 반복합니다.

7. **파일 공유** 차원으로 이동하여 **차원 값** 드롭다운을 클릭하고 경고할 파일 공유를 선택합니다. 

  > [!NOTE]
  > 파일 공유가 표준 파일 공유인 경우 표준 파일 공유에 대해 공유당 메트릭을 사용할 수 없으므로 차원 값 드롭다운은 비어 있습니다. 저장소 계정 내의 파일 공유가 제한되고 경고가 제한된 파일 공유를 식별하지 못하는 경우 표준 파일 공유에 대한 제한 경고가 트리거됩니다. 표준 파일 공유에는 주당 메트릭을 사용할 수 없으므로 저장소 계정당 하나의 파일 공유를 사용할 것을 권장합니다. 

8. 메트릭 경고 규칙을 평가하고 **완료를**클릭하는 데 사용되는 **경고 매개** 변수(임계값, 연산자, 집계 세분성 및 빈도)를 정의합니다.

  > [!TIP]
  > 정적 임계값을 사용하는 경우 메트릭 차트는 파일 공유가 현재 제한되고 있는 경우 적절한 임계값을 결정하는 데 도움이 될 수 있습니다. 동적 임계값을 사용하는 경우 메트릭 차트에 최근 데이터를 기반으로 계산된 임계값이 표시됩니다.

9. 기존 **작업 그룹을** 선택하거나 새 작업 그룹을 만들어 경고에 작업 그룹(전자 메일, SMS 등)을 추가합니다.

10. **경고 규칙 이름,** **설명** 및 **심각도와**같은 **경고 세부 정보를** 입력합니다.

11. **경고 만들기 규칙을** 클릭하여 경고를 만듭니다.

Azure 모니터에서 경고 구성에 대해 자세히 알아보려면 [Microsoft Azure의 경고 개요를]( https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)참조하십시오.
