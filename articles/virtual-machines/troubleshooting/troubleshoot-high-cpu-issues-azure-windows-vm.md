---
title: Azure Windows 가상 머신에 대 한 높은 CPU 문제 해결
description: .
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: mnanda
manager: dcscontentpm
editor: v-jesits
tags: azure-resource-manager
ms.assetid: 9971cc26-916f-4e49-83cd-71eca74804f0
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 9/24/2020
ms.author: mnanda
ms.openlocfilehash: 3bd19f301b1afd7dd1c35f03f6f6131a26b00708
ms.sourcegitcommit: ffa7a269177ea3c9dcefd1dea18ccb6a87c03b70
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91596835"
---
# <a name="troubleshoot-high-cpu-issues-for-azure-windows-virtual-machines"></a>Azure Windows 가상 머신에 대 한 높은 CPU 문제 해결

## <a name="summary"></a>요약

성능 문제는 서로 다른 운영 체제 또는 응용 프로그램에서 발생 하며 모든 문제를 해결 하려면 고유한 방법이 필요 합니다. 이러한 문제의 대부분은 CPU, 메모리, 네트워킹 및 입/출력 (i/o)이 문제가 발생 하는 주요 위치로 인해 발생 합니다. 이러한 각 영역에는 서로 다른 증상 (동시에)이 생성 되며 다른 진단 및 솔루션이 필요 합니다.

이 문서에서는 Windows 운영 체제를 실행 하는 Azure Vm (Virtual Machines)에서 발생 하는 높은 CPU 사용량 문제에 대해 설명 합니다.

### <a name="high-cpu-issues-on-azure-windows-vms"></a>Azure Windows Vm의 높은 CPU 문제

I/o 및 네트워크 대기 시간 문제 외에도 CPU 및 메모리 문제 해결에는 온-프레미스 서버에서 수행 하는 것과 동일한 도구와 단계가 필요 합니다. Microsoft에서 일반적으로 지 원하는 도구 중 하나는 PerfInsights (Windows 및 Linux 모두에서 사용 가능)입니다. PerfInsights는 사용자에 게 친숙 한 보고서에서 Azure VM 모범 사례 진단을 제공할 수 있습니다. PerfInsights는 도구 내에서 선택한 플래그에 따라 Perfmon, Xperf 및 Netmon 데이터를 수집 하는 데 도움이 될 수 있는 래퍼 도구 이기도 합니다.

온-프레미스 서버에 사용 되는 대부분의 기존 성능 문제 해결 도구 (예: Perfmon 또는 Procmon)는 Azure Windows Vm에서 작동 합니다. 그러나 azure 모범 사례, SQL 모범 사례, 높은 해상도의 i/o 대기 시간 그래프, CPU 및 메모리 탭 등을 비롯 한 더 많은 정보를 제공 하기 위해 Azure Vm에 대 한 PerfInsights는 명시적으로 설계 되었습니다.

사용자 모드 또는 커널 모드로 실행 되는지 여부에 관계 없이 활성 프로세스의 모든 스레드는 작성 된 코드를 실행 하기 위해 CPU 사이클이 필요 합니다. 많은 문제는 워크 로드와 직접적으로 관련이 있습니다. 서버에 존재 하는 작업의 종류는 CPU를 포함 하 여 리소스 사용량을 구동 합니다.

#### <a name="common-factors"></a>공통 요소

다음 요소는 CPU가 높은 상황에서 공통적으로 발생 합니다.

- 주로 인터넷 정보 서비스 (IIS), Microsoft SharePoint, Microsoft SQL Server 또는 타사 응용 프로그램과 같은 앱에 적용 되는 최신 코드 변경 또는 배포입니다.

- OS 수준 업데이트 또는 응용 프로그램 수준 누적 업데이트 및 픽스와 관련 될 수 있는 최신 업데이트입니다.

- 쿼리가 변경 되거나 오래 된 인덱스입니다. 또한 SQL Server 및 Oracle 데이터 계층 응용 프로그램에는 다른 요소로 쿼리 계획을 최적화 하는 기능이 있습니다. 데이터를 변경 하거나 적절 한 인덱스가 없으면 여러 쿼리를 계산 집약적인로 만들 수 있습니다.

- Azure VM 관련. RDAgent와 같은 특정 프로세스와 Monitoring Agent, MMA Agent 또는 Security client와 같은 확장 특정 프로세스를 사용 하 여 높은 CPU 사용량을 유발할 수 있습니다. 이러한 프로세스는 구성 또는 알려진 문제 관점에서 보아야 합니다.

## <a name="troubleshoot-the-issue"></a>문제 해결

이 문서에서는 문제가 있는 프로세스를 격리 하는 방법을 중점적으로 설명 합니다. 추가 분석은 높은 CPU 사용량을 구동 하는 프로세스에만 적용 됩니다.

예를 들어 프로세스가 SQL Server (sqlservr.exe) 되 면 다음 단계는 특정 기간에 CPU 주기를 가장 많이 사용 하는 쿼리를 분석 하는 것입니다.

### <a name="scope-the-issue"></a>문제 범위

다음은 문제를 해결할 때 질문 하는 몇 가지 질문입니다.

- 문제에 대 한 패턴이 있나요? 예를 들어, 매일, 매주 또는 매월 특정 시간에 높은 CPU 문제가 발생 하나요? 그렇다면이 문제를 작업, 보고서 또는 사용자 로그인과 상관 관계를 지정할 수 있나요?

- 최근 코드가 변경 된 후 CPU가 높은 문제가 시작 되었습니까? Windows 또는 응용 프로그램에서 업데이트를 적용 했습니까?

- 사용자 수, 더 높은 데이터 유입 또는 많은 수의 보고서와 같이 워크 로드가 변경 된 후 높은 CPU 문제가 시작 되었습니까?

- Azure의 경우 다음 조건 중 하나에서 CPU가 높은 문제가 시작 되었습니까?

  - 최근 재배포 또는 다시 시작한 후
  - SKU 또는 VM 형식이 변경 된 경우
  - 새 확장이 추가 된 경우
  - 부하 분산 장치를 변경한 후

### <a name="azure-caveats"></a>Azure 주의 사항

작업을 파악합니다. VM을 선택 하면 전체 월별 호스팅 비용을 확인할 때 vCPU (가상 CPU) 수를 간과 하 게 됩니다. 작업 부하가 계산 집약적인 경우 하나 또는 두 개의 vCPUs가 있는 더 작은 VM SKU를 선택 하면 워크 로드 문제가 발생할 수 있습니다. 워크 로드에 대 한 다양 한 구성을 테스트 하 여 필요한 최상의 컴퓨팅 기능을 결정 합니다.

QA (품질 보증) 및 테스트에 권장 되는 B (버스트 모드) 시리즈와 같은 특정 VM 시리즈는 있습니다. 프로덕션 환경에서 이러한 시리즈를 사용 하면 CPU 크레딧을 모두 사용한 후 컴퓨팅 기능이 제한 됩니다.

SQL Server, Oracle, RDS (원격 데스크톱 서비스), Windows 가상 데스크톱, IIS 또는 SharePoint와 같은 알려진 응용 프로그램의 경우 이러한 워크 로드에 대 한 최소 구성 권장 사항을 포함 하는 Azure 모범 사례 문서가 있습니다.

### <a name="ongoing-high-cpu-issues"></a>지속적인 CPU 문제

문제가 발생 하는 경우 문제를 일으킨 원인을 확인 하기 위해 프로세스 추적을 캡처할 수 있는 가장 좋은 기회입니다. 온-프레미스 Windows 서버에 사용 하 던 기존 도구를 사용 하 여 프로세스를 찾을 수 있습니다. Azure Vm에 대 한 Azure 지원에서 권장 하는 도구는 다음과 같습니다.

### <a name="perfinsights"></a>PerfInsights

PerfInsights는 VM 성능 문제에 대 한 Azure 지원의 권장 도구입니다. CPU, 메모리 및 고해상도 i/o 그래프를 위한 모범 사례와 전용 분석 탭을 포함 하도록 설계 되었습니다. Azure Portal를 통해 또는 VM 내에서 실행할 수 있습니다. Azure 지원 팀과 데이터를 공유할 수 있습니다.

#### <a name="run-perfinsights"></a>PerfInsights 실행

PerfInsights는 [Windows](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfInsights) 및 [Linux](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfinsights-linux) OS 모두에 사용할 수 있습니다. Windows의 경우 다음 옵션을 사용할 수 있습니다.

#### <a name="run-and-analyze-reports-through-azure-portal"></a>Azure Portal를 통해 보고서 실행 및 분석

[Azure Portal를 통해 설치](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/performance-diagnostics)하는 경우는 실제로 VM에 확장을 설치 합니다. 사용자는 [VM 블레이드의 확장](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/performance-diagnostics-vm-extension)으로 직접 이동한 다음 성능 진단 옵션을 선택 하 여 PerfInsights를 확장으로 설치할 수도 있습니다.

#### <a name="azure-portal-option-1"></a>Azure Portal 옵션 1

VM 블레이드를 찾아보고 **성능 진단** 옵션을 선택 합니다. 선택한 VM에 옵션 (확장명 사용)을 설치 하 라는 메시지가 표시 됩니다.

  ![성능 진단 설치](./media/troubleshoot-high-cpu-issues-azure-windows-vm/1-install-performance-diagnostics.png)

#### <a name="azure-portal-option-2"></a>Azure Portal 옵션 2

VM 블레이드에서 **문제 진단 및 해결** 을 찾아 **vm 성능 문제**를 확인 합니다.

  ![VM 성능 문제 해결](./media/troubleshoot-high-cpu-issues-azure-windows-vm/2-troubleshoot-vm-performance-issues.png)

**문제 해결**을 선택 하는 경우 PerfInsights 설치 화면이 로드 됩니다.

**설치**를 선택 하면 설치 시 다른 컬렉션 옵션이 제공 됩니다.

  ![성능 분석](./media/troubleshoot-high-cpu-issues-azure-windows-vm/3-performance-analysis.png)

스크린샷에서 번호가 매겨진 옵션은 다음 주석과 관련이 있습니다.

1. **높은 CPU**의 옵션을 선택 하려면 **성능 분석** 또는 **고급**을 선택 합니다.

2. 여기에서 증상을 추가 하면 보고서에 추가 되어 Azure 지원과 정보를 공유 하는 데 도움이 됩니다.

3. 데이터 수집 기간을 선택 합니다. 높은 CPU 옵션의 경우 15 분 이상을 선택 합니다. Azure Portal 모드에서 최대 15 분의 데이터를 수집할 수 있습니다. 더 긴 수집 기간 동안에는 VM 내에서 실행 파일로 프로그램을 실행 해야 합니다.

4. 이 데이터를 수집 하기 위해 Azure 지원에서 요청 하는 경우 여기에 티켓 번호를 추가할 수 있습니다. 이 필드는 선택 항목입니다.

5. EULA (최종 사용자 사용권 계약)에 동의 하려면이 필드를 선택 합니다.

6. 이 경우 Azure 지원 팀에서이 보고서를 사용할 수 있도록 하려면이 필드를 선택 합니다.

보고서는 구독 하는 저장소 계정 중 하나에 저장 됩니다. 나중에 보고 다운로드할 수 있습니다.

#### <a name="run-perfinsights-from-within-the-vm"></a>VM 내에서 PerfInsights 실행

더 긴 기간 동안 PerfInsights를 실행 하려는 경우이 메서드를 사용할 수 있습니다. [PerfInsights 문서](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/how-to-use-perfInsights#how-do-i-run-perfinsights) 는 PerfInsights를 실행 파일로 실행 하는 데 필요한 다양 한 명령 및 플래그에 대 한 자세한 연습을 제공 합니다. CPU 사용량이 많은 경우 다음 모드 중 하나가 필요 합니다.

- 고급 시나리오

  - `PerfInsights /run advanced xp /d 300 /AcceptDisclaimerAndShareDiagnostics`

- VM 느림 (성능) 시나리오

  - `PerfInsights /run vmslow /d 300 /AcceptDisclaimerAndShareDiagnostics /sa <StorageAccountName> /sk <StorageAccountKey>`

명령 출력은 PerfInsights 실행 파일을 저장 한 폴더와 동일한 폴더에 있습니다.

#### <a name="what-to-look-for-in-the-report"></a>보고서에서 찾을 내용

보고서를 실행 한 후 내용의 위치는 Azure Portal를 통해 실행 되었는지 또는 실행 파일로 실행 되었는지에 따라 달라 집니다. 두 옵션 중 하나에 대해 생성 된 로그 폴더에 액세스 하거나 분석을 위해 로컬에서 (Azure Portal 경우)를 다운로드 합니다.

### <a name="run-through-the-azure-portal"></a>Azure Portal를 실행 합니다.

  ![높은 영향 성능 진단](./media/troubleshoot-high-cpu-issues-azure-windows-vm/4-high-impact-performance-diagnostics.png)

  ![보고서 다운로드](./media/troubleshoot-high-cpu-issues-azure-windows-vm/5-download-report.png)

#### <a name="run-from-within-vm"></a>VM 내에서 실행

폴더 구조는 다음 이미지와 유사 합니다.

  ![출력 선택](./media/troubleshoot-high-cpu-issues-azure-windows-vm/6-select-output.png)

  ![폴더 구조](./media/troubleshoot-high-cpu-issues-azure-windows-vm/7-folder-structure.png)

1. Perfmon, Xperf, Netmon, SMB 로그, 이벤트 로그 등의 추가 컬렉션은 출력 폴더에서 찾을 수 있습니다.

1. 분석 및 권장 사항과 함께 실제 보고서를 제공 합니다.

1. 성능 (VMslow)과 고급에 대해 보고서는 PerfInsights 실행 기간 동안 **perfmon** 정보를 수집 합니다.

1. 이벤트 로그에는 유용한 시스템 수준 또는 프로세스 충돌 정보에 대 한 빠른 보기가 표시 됩니다.

#### <a name="where-to-start"></a>시작 위치

PerfInsights 보고서를 엽니다. 검색 **결과** 탭은 리소스 사용량과 관련 된 모든 이상 값을 기록 합니다. CPU 사용량이 많은 경우에는 **결과** 탭에서이를 높은 영향 또는 중간 영향으로 분류 합니다.

  ![영향 수준 리소스](./media/troubleshoot-high-cpu-issues-azure-windows-vm/8-impact-level-resources.png)

이전 예제와 마찬가지로 PerfInsights는 30 분 동안 실행 되었습니다. 절반의 경우 강조 표시 된 프로세스는 더 높은 쪽에서 CPU를 소모 했습니다. 수집 시간 동안 동일한 프로세스가 실행 된 경우 영향 수준이 **높음**으로 변경 된 것입니다.

**발견** 이벤트를 확장 하면 몇 가지 주요 세부 정보가 표시 됩니다. 이 탭은 **평균 CPU** 사용량을 기준으로 프로세스를 내림차순으로 나열 하 고 프로세스가 시스템, Microsoft 소유의 앱 (SQL, IIS) 또는 타사 프로세스와 관련 되어 있는지 여부를 표시 합니다.

#### <a name="more-details"></a>자세한 내용

**CPU** 아래에는 코어 또는 프로세스별 상세 패턴 분석에 사용할 수 있는 전용 하위 탭 있습니다.

**상위 CPU 소비자** 탭에는 두 개의 별도 섹션이 있으며 여기에서 프로세서 당 통계를 볼 수 있습니다. 응용 프로그램 디자인은 단일 스레드 이거나 단일 프로세서에 고정 되는 경우가 많습니다. 이 시나리오에서는 하나 이상의 코어가 100%에서 실행 되는 반면 다른 코어는 예상 수준에서 실행 됩니다. 이러한 시나리오는 서버의 평균 CPU가 예상 대로 실행 되는 것 처럼 보이지만 사용량이 많은 코어에 고정 된 프로세스가 예상 보다 느리게 실행 되기 때문에 더 복잡 합니다.

  ![높은 CPU 사용량](./media/troubleshoot-high-cpu-issues-azure-windows-vm/9-high-cpu-usage.png)

두 번째 섹션 (동일 하 게 중요)은 **상위 장기 실행 CPU 소비자**입니다. 이 섹션에서는 프로세스 정보 및 해당 CPU 사용 패턴을 모두 보여 줍니다. 목록은 위쪽에 높은 평균 CPU 소비자를 포함 하 여 정렬 됩니다.

  ![Tom 장기 실행 CPU 소비자](./media/troubleshoot-high-cpu-issues-azure-windows-vm/10-top-long-running-cpu-consumers.png)

이러한 두 탭은 다음 문제 해결 단계에 대 한 경로를 설정 하는 데 충분 합니다. 높은 CPU 상태를 구동 하는 프로세스에 따라 앞에서 요청한 질문을 해결 해야 합니다. SQL Server (sqlservr.exe) 또는 IIS (w3wp.exe)와 같은 프로세스에는이 조건을 유발 하는 쿼리 또는 코드 변경에 대 한 특정 드릴 다운이 필요 합니다. WMI 또는 Lsass.exe와 같은 시스템 프로세스의 경우 다른 경로를 따라야 합니다.

RDAgent, OMS 및 모니터링 확장 실행 파일과 같은 Azure VM 관련 프로세스의 경우 Azure 지원 팀의 도움을 받아 새로운 빌드 또는 버전을 수정 해야 할 수 있습니다.

### <a name="perfmon"></a>성능 모니터

**Perfmon** 은 Windows Server에서 리소스 문제를 해결 하기 위한 가장 빠른 도구 중 하나입니다. 권장 사항이 나 결과를 포함 하는 명확한 보고서는 제공 하지 않습니다. 대신, 사용자가 수집 된 데이터를 탐색 하 고 다양 한 카운터 범주에서 특정 필터를 사용 해야 합니다.

PerfInsights는 VMSlow 및 고급 시나리오에 대 한 추가 로그로 Perfmon을 수집 합니다. 그러나 Perfmon은 독립적으로 수집할 수 있으며 다음과 같은 추가 이점이 있습니다.

- 원격으로 수집할 수 있습니다.

- **작업**을 통해 예약할 수 있습니다.

- 롤백 기능을 사용 하 여 기간이 길거나 연속 모드로 수집 될 수 있습니다.

PerfInsights에 표시 된 것과 동일한 예제를 고려 하 여 Perfmon에서이 데이터를 표시 하는 방법을 확인 합니다. 필요한 카운터 범주는 다음과 같습니다.

- 프로세서 정보 >% Processor Time > _Total

- >% ProcessorTime > 모든 인스턴스 처리

#### <a name="where-to-start"></a>시작 위치

Perfmon의 출력 파일 이름에는 `.blg` 확장명이 있습니다. 이러한 파일은 독립적으로 또는 PerfInsights을 사용 하 여 수집할 수 있습니다. 이 토론에서는 `.blg` PerfInsights 데이터에 포함 되어 있으며 이전 예제에 수집 된 Perfmon을 사용 합니다.

Perfmon에는 사용할 수 있는 기본 사용자 지원 보고서가 없습니다. 그래프 형식을 변경 하는 다양 한 뷰가 있지만 프로세스 필터링 (또는 원인 프로세스를 식별 하는 데 필요한 작업)은 수동입니다.

> [!NOTE]
> [PAL 도구](https://github.com/clinthuffman/PAL) 는 `.blg` 파일을 사용 하 고 자세한 보고서를 생성할 수 있습니다.

시작 하려면 **카운터 추가** 범주를 선택 합니다.

1. **사용 가능한 카운터**에서 **프로세서 정보** 범주의 **% ProcessorTime** 카운터를 선택 합니다.

1. 모든 결합 코어의 통계를 제공 하는 **_Total**를 선택 합니다.

1. **추가**를 선택합니다. 이 창에는 추가 된 **카운터**아래에 **% ProcessorTime** 가 표시 됩니다.

  ![프로세서 시간 추가](./media/troubleshoot-high-cpu-issues-azure-windows-vm/11-add-processor-time.png)

카운터를 로드 한 후에는 컬렉션 시간 프레임에 선 추세 차트가 표시 됩니다. 카운터를 선택 하거나 선택 취소할 수 있습니다. 지금까지 하나의 카운터를 추가 했습니다.

  ![성능 모니터 설정](./media/troubleshoot-high-cpu-issues-azure-windows-vm/12-performance-monitor-1.png)

모든 카운터에는 **평균**, **최소값**및 **최대값이** 있습니다. 평균 값은 데이터 수집 기간에 따라 달라질 수 있으므로 **평균과** **최 댓** 값에 모두 중점을 둡니다. 전체 컬렉션이 40 분 동안 10 분 동안 CPU 사용량이 많은 경우 평균 값이 훨씬 더 낮습니다.

이전 추세 그래프는 **총 프로세서** 의 범위는 약 15 분 동안 80% 부근에 표시 되는 것을 보여 줍니다.

#### <a name="identify-the-process"></a>프로세스 식별

서버에 지정 된 시간 동안 CPU 사용량이 많은 것으로 확인 되었지만 드라이버를 아직 확인 하지 않았습니다. PerfInsights를 사용 하는 경우와 달리이 경우에는 원인 프로세스를 수동으로 검색 해야 합니다.

이 작업의 경우 이전에 추가한 **% ProcessorTime** 카운터를 지우거 나 제거한 후 새 범주를 추가 해야 합니다.

- >% ProcessorTime > 모든 인스턴스 처리

이 범주는 해당 시점에 실행 중인 모든 프로세스에 대 한 카운터를 로드 합니다.

  ![카운터 추가](./media/troubleshoot-high-cpu-issues-azure-windows-vm/13-add-counters.png)

일반적인 프로덕션 컴퓨터에서 수백 또는 프로세스를 실행할 수 있습니다. 따라서 낮은 추세 또는 평면 추세 그래프가 있는 모든 카운터를 삭제 하는 데 시간이 걸릴 수 있습니다.

이 프로세스를 가속화 하려면 **히스토그램** 뷰를 사용 하 고 보기 유형을 **꺾은선형** 에서 **히스토그램**으로 변경 하면 막대 그래프가 제공 됩니다. 수집 시간 동안 CPU 사용량이 많은 프로세스를 선택 하는 것이 더 쉬울 수 있습니다.

항상 **합계**에 대 한 막대가 있으므로 높은 소모 비율을 표시 하는 막대에 집중 합니다. 다른 막대를 삭제 하 여 뷰를 정리할 수 있습니다. 이제 다시 **줄** 뷰로 전환 합니다.

  ![성능 모니터 표시기](./media/troubleshoot-high-cpu-issues-azure-windows-vm/14-performance-monitor-2.png)

이제 원인 프로세스를 보다 쉽게 파악할 수 있습니다. 기본적으로 **Max** 및 **Min** 값은 서버의 코어가 나 프로세스의 스레드 수의 배수입니다.

  ![성능 모니터 결과](./media/troubleshoot-high-cpu-issues-azure-windows-vm/15-performance-monitor-3.png)

사용 가능한 도구 목록은 Perfmon의 PerfInsights에서 끝나지 않습니다. **Processmonitor** (procmon) 또는 **Xperf**와 같은 다른 도구에 액세스할 수 있습니다. 필요한 경우 많은 타사 도구를 사용할 수 있습니다.

### <a name="azure-monitoring-tools"></a>Azure 모니터링 도구

Azure Vm에는 CPU, 네트워크 i/o 및 i/o 바이트와 같은 기본 정보를 포함 하는 신뢰할 수 있는 메트릭이 있습니다. Azure Monitor와 같은 고급 메트릭에 대해서는 지정 하는 저장소 계정을 구성 하 고 사용 하는 몇 가지 선택만 하면 됩니다.

#### <a name="basic-default-counters"></a>기본 (기본) 카운터

  ![차트 메트릭](./media/troubleshoot-high-cpu-issues-azure-windows-vm/16-chart-metrics.png)

#### <a name="enabling-azure-monitor"></a>Azure Monitor 사용

Azure Monitor 메트릭을 사용 하도록 설정 하면 소프트웨어에서 VM에 확장을 설치한 다음 Perfmon 카운터를 포함 하는 세분화 된 메트릭을 수집 하기 시작 합니다.

  ![진단 스토리지 계정](./media/troubleshoot-high-cpu-issues-azure-windows-vm/17-diagnostics-storage-account.png)

기본 **카운터 범주** 는 **기본값으로**설정 됩니다. 그러나 **사용자 지정** 컬렉션을 설정할 수도 있습니다.

  ![성능 카운터](./media/troubleshoot-high-cpu-issues-azure-windows-vm/18-performance-counters.png)

설정이 활성화 되 면 **메트릭** 섹션에서 이러한 **게스트** 카운터를 볼 수 있습니다. 메트릭이 특정 임계값에 도달 하는 경우 전자 메일 메시지를 포함 하 여 **경고** 를 설정할 수도 있습니다.

  ![메트릭 네임 스페이스](./media/troubleshoot-high-cpu-issues-azure-windows-vm/19-metrics-namespace.png)

Azure monitor를 사용 하 여 Azure Vm을 관리 하는 방법에 대 한 자세한 내용은 [Azure Monitor를 사용 하 여 azure virtual Machines 모니터링](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-vm-azure)을 참조 하세요.

### <a name="reactive-troubleshooting"></a>사후 문제 해결

문제가 이미 발생 한 경우 첫 번째 환경에서 CPU 문제가 발생 한 원인을 파악 해야 합니다. 반응 태세는 복잡할 수 있습니다. 문제가 이미 발생 했기 때문에 데이터 수집 모드는 유용 하지 않습니다.

이 문제가 한 번 발생 한 경우 발생 한 앱을 확인 하는 것이 어려울 수 있습니다. Azure VM이 OMS 또는 다른 진단 추적을 사용 하도록 구성 된 경우 문제를 일으킨 문제에 대 한 통찰력을 얻을 수 있습니다.

반복 패턴을 처리 하는 경우 문제가 발생 하는 시간 동안 데이터를 수집 합니다.

PerfInsights에 **예약 된 실행** 기능이 아직 없습니다. 그러나 Perfmon은 명령줄을 통해 실행 및 예약할 수 있습니다.

### <a name="logman-command"></a>Logman 명령

**Logman Create Counter** 명령은 명령줄을 통해 Perfmon 수집을 실행 하 고, **작업 관리자**를 통해 예약 하거나, 원격으로 실행 하는 데 사용 됩니다.

**Sample** (원격 컬렉션 모드 포함)

`Logman create counter LOGNAME -u DOMAIN\USERNAME * -f bincirc -v mmddhhmm -max 300 -c "\\SERVERNAME\LogicalDisk(*)\*" "\\SERVERNAME\Memory\*" "\\SERVERNAME\Network Interface(*)\*" "\\SERVERNAME\Paging File(*)\*" "\\SERVERNAME\PhysicalDisk(*)\*" "\\SERVERNAME\Process(*)\*" "\\SERVERNAME\Redirector\*" "\\SERVERNAME\Server\*" "\\SERVERNAME\System\*" "\\SERVERNAME\Terminal Services\*" "\\SERVERNAME\Processor(*)\*" "\\SERVERNAME\Cache\*" -si 00:01:00`

동일한 VNET의 피어 Azure VM 컴퓨터에서 Logman.exe를 시작할 수도 있습니다.

이러한 매개 변수에 대 한 자세한 내용은 [logman create 카운터](https://docs.microsoft.com/windows-server/administration/windows-commands/logman-create-counter)를 참조 하세요.

문제가 발생 하는 동안 Perfmon 데이터를 수집한 후에는 데이터를 분석 하는 나머지 단계는 앞에서 설명한 것과 동일 합니다.

## <a name="conclusion"></a>결론

성능 문제에 대 한 작업을 이해 하는 것은 문제를 해결 하기 위한 핵심입니다. 프로덕션 워크 로드에 집중 하 여 다른 VM Sku 및 다른 디스크 저장소 옵션의 옵션을 평가 해야 합니다. 여러 Vm에서 솔루션을 테스트 하는 프로세스를 통해 최상의 결정을 내릴 수 있습니다.

사용자 작업 및 데이터 양이 다르기 때문에 항상 VM의 컴퓨팅, 네트워킹 및 i/o 기능에서 버퍼를 유지 합니다. 이제 작업을 급격 하 게 변경 해도 효과가 없습니다.

워크 로드가 곧 증가 하는 것으로 예상 되는 경우 컴퓨팅 성능이 더 높은 SKU로 이동 합니다. 작업 부하가 계산 집약적인 경우 VM Sku를 적절 하 게 선택 합니다.
