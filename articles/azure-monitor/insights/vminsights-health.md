---
title: Azure virtual machines의 상태 이해 | Microsoft Docs
description: 이 문서에서는 VM용 Azure Monitor를 사용 하 여 가상 머신 및 기본 운영 체제의 상태를 이해 하는 방법을 설명 합니다.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 11/14/2019
ms.openlocfilehash: 5fd5295e52f0fef5e1432fdb2f81d2ba0e1717e8
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74109773"
---
# <a name="understand-the-health-of-your-azure-virtual-machines"></a>Azure virtual machines의 상태 이해

Azure는 모니터링 공간의 특정 역할이 나 작업에 대 한 서비스를 포함 하지만, Azure Vm (가상 머신)에서 호스트 되는 운영 체제 (OSs)의 심층적인 상태 관점을 제공 하지 않습니다. 다른 조건에 대 한 Azure Monitor를 사용할 수 있지만 핵심 구성 요소의 상태 또는 Vm의 전반적인 상태를 모델링 하 고 나타내는 것은 아닙니다.

VM용 Azure Monitor 상태를 사용 하 여 Windows 또는 Linux 게스트 OS의 가용성과 성능을 적극적으로 모니터링할 수 있습니다. 상태 기능은 키 구성 요소 및 해당 관계를 나타내는 모델을 사용 하며, 구성 요소 상태를 측정 하는 방법을 지정 하는 조건을 제공 하 고 비정상 상태를 감지한 경우 경고를 보냅니다.

Azure VM 및 기본 OS의 전반적인 상태 보기는 VM에서 직접 또는 Azure Monitor에서 리소스 그룹의 모든 Vm에서 확인할 수 있습니다.

이 문서에서는 VM용 Azure Monitor 상태 기능에서 검색 되는 상태 문제를 신속 하 게 평가, 조사 및 해결 하는 방법을 보여 줍니다.

VM용 Azure Monitor를 구성하는 방법에 대한 자세한 내용은 [VM용 Azure Monitor를 사용하도록 설정](vminsights-enable-overview.md)을 참조하세요.

>[!NOTE]
>최근에 공개 미리 보기 고객 으로부터 받은 피드백에 따라 상태 기능에 대 한 [변경을 발표](https://azure.microsoft.com/updates/updates-to-azure-monitor-for-virtual-machines-preview-before-general-availability-release/
) 했습니다. 변경 되는 내용 수를 고려 하 여 새 고객을 위한 상태 기능 제공을 중지할 예정입니다. 기존 고객은 상태 기능을 계속 사용할 수 있습니다. 자세한 내용은 [일반적인 가용성 FAQ](vminsights-ga-release-faq.md)를 참조 하세요. 

## <a name="monitoring-configuration-details"></a>모니터링 구성 세부 정보

이 섹션에서는 Azure Windows 및 Linux Vm을 모니터링 하는 기본 상태에 대해 간략하게 설명 합니다. 모든 상태 조건은 비정상 상태를 검색할 때 경고를 보내도록 미리 구성 되어 있습니다.

| 모니터 이름 | 빈도 (분) | Lookback 기간 (분) | 연산자 | 임계값 | 경고 상태 | severity | 작업 범주 | 
|--------------|-----------|----------|----------|-----------|----------------|----------|-------------------|
| 논리 디스크 온라인 | 5 | 15 | <> | 1 (true) | 심각 | Sev1 | Linux | 
| 논리적 디스크에서 사용 가능한 공간 | 5 | 15 | < | 200 MB (경고)<br> 100 MB (위험) | Warning | Sev1<br> Sev2 | Linux | 
| 논리적 디스크에서 사용 가능한 inode 비율 | 5 | 15 | < | 5% | 심각 | Sev1 | Linux | 
| 논리적 디스크에서 사용 가능한 공간 비율 | 5 | 15 | < | 5% | 심각 | Sev1 | Linux | 
| 네트워크 어댑터 상태 | 5 | 15 | <> | 1 (true) | Warning | Sev2 | Linux | 
| 운영 체제의 사용 가능한 메모리 (Mb) | 5 | 10 | < | 2.5 M B | 심각 | Sev1 | Linux | 
| Disk Avg. Disk sec/Read | 5 | 25 | > | 0.05 초 | 심각 | Sev1 | Linux | 
| Disk Avg. Disk sec/Transfer | 5 | 25 | > | 0.05 초 | 심각 | Sev1 | Linux | 
| Disk Avg. Disk sec/Write | 5 | 25 | > | 0.05 초 | 심각 | Sev1 | Linux | 
| 디스크 상태 | 5 | 25 | <> | 1 (true) | 심각 | Sev1 | Linux | 
| 운영 체제 총 프로세서 시간 비율 | 5 | 10 | >= | 95% | 심각 | Sev1 | Linux | 
| 총 CPU 사용 백분율 | 5 | 10 | >= | 95% | 심각 | Sev1 | Windows | 
| 파일 시스템 오류 또는 손상 | 60 | 60 | <> | 4 | 심각 | Sev1 | Windows | 
| 읽기당 평균 논리적 디스크 시간(초) | 1 | 15 | > | 0.04 s | Warning | Sev2 | Windows | 
| 전송당 평균 논리적 디스크 시간(초) | 1 | 15 | > | 0.04 s | Warning | Sev2 | Windows | 
| 평균 논리 디스크 쓰기 시간 (초) (논리 디스크) | 1 | 15 | > | 0.04 s | Warning | Sev2 | Windows | 
| 현재 디스크 큐 길이(논리 디스크) | 5 | 60 | >= | 32 | Warning | Sev2 | Windows | 
| 논리 디스크 사용 가능 공간 (MB) | 15 | 60 | > | 500 MB 경고<br> 300 심각 | 심각 | Sev1<br> Sev2 | Windows | 
| 논리 디스크 사용 가능한 공간 (%) | 15 | 60 | > | 10% 경고<br> 5% 위험 | 심각 | Sev1<br> Sev2 | Windows |
| 논리적 디스크의 유휴 시간 비율 | 15 | 360 | <= | 20% | Warning | Sev2 | Windows | 
| 읽기에 사용된 대역폭 비율 | 5 | 60 | >= | 60% | Warning | Sev2 | Windows | 
| 총 사용된 대역폭 비율 | 5 | 60 | >= | 75% | Warning | Sev2 | Windows | 
| 쓰기에 사용된 대역폭 비율 | 5 | 60 | >= | 60% | Warning | Sev2 | Windows | 
| DHCP 클라이언트 서비스 상태 | 5 | 12 | <> | 4 (실행 중) | 심각 | Sev1 | Windows | 
| DNS 클라이언트 서비스 상태 | 5 | 12 | <> | 4 (실행 중) | 심각 | Sev1 | Windows | 
| Windows 이벤트 로그 서비스 상태 | 5 | 12 | <> | 4 (실행 중) | 심각 | Sev1 | Windows | 
| Windows 방화벽 서비스 상태 | 5 | 12 | <> | 4 (실행 중) | 심각 | Sev1 | Windows | 
| RPC 서비스 상태 | 5 | 12 | <> | 4 (실행 중) | 심각 | Sev1 | Windows | 
| 서버 서비스 상태 | 5 | 12 | <> | 4 (실행 중) | 심각 | Sev1 | Windows | 
| Windows 원격 관리 서비스 상태 | 5 | 12 | <> | 4 (실행 중) | 심각 | Sev1 | Windows | 
| 메모리의 사용 가능 메가바이트 | 5 | 10 | < | 100 MB | 심각 | Sev1 | Windows | 
| 사용 가능한 시스템 페이지 테이블 항목 | 5 | 10 | <= | 5,000 | 심각 | Sev1 | Windows | 
| 초당 메모리 페이지 수 | 5 | 10 | >= | 5000/초 | Warning | Sev1 | Windows | 
| 사용 중인 커밋된 메모리 비율 | 5 | 10 | > | 80% | 심각 | Sev1 | Windows | 
| 전송당 평균 디스크 시간(초) | 1 | 15 | > | 0.04 s | Warning | Sev2 | Windows | 
| 평균 디스크 쓰기 시간 (초) | 1 | 15 | > | 0.04 s | Warning | Sev2 | Windows | 
| 현재 디스크 큐 길이 | 5 | 60 | >= | 32 | Warning | Sev2 | Windows | 
| 디스크의 유휴 시간 비율 | 5 | 60 | >= | 20% | Warning | Sev2 | Windows | 

>[!NOTE]
>Lookback 지속 시간은 최근 5 분 동안 발생 하는 것과 같은 메트릭 값을 확인 하는 빈도를 나타냅니다.  

>[!NOTE]
>빈도는 1 분 간격으로 조건을 충족 하는지 여부를 메트릭 경고에서 확인 하는 빈도를 나타냅니다.  상태 조건이 실행 되는 속도 이며 lookback는 상태 기준이 평가 되는 기간입니다. 예를 들어 상태 조건은 **CPU 사용률이** 5 분인 95%를 초과 하 고 15 분 동안 95% 보다 크게 유지 되는 경우 (3 회 연속 평가 주기), 상태가 아직 없는 경우 위험 심각도로 업데이트 됨을 평가 합니다.

## <a name="sign-in-to-the-azure-portal"></a>Azure 포털에 로그인합니다.

로그인 하려면 [Azure Portal](https://portal.azure.com)으로 이동 합니다.

## <a name="introduction-to-azure-monitor-for-vms-health"></a>VM용 Azure Monitor 상태 소개

단일 VM 또는 Vm 그룹에 대 한 상태 기능을 사용 하기 전에 정보가 표시 되는 방식과 시각화의 의미를 이해 하는 것이 중요 합니다.

### <a name="view-health-directly-from-a-vm"></a>VM에서 직접 상태 보기

Azure VM의 상태를 보려면 VM의 왼쪽 창에서 **Insights (미리 보기)** 를 선택 합니다. VM insights 페이지에서 **상태** 탭은 기본적으로 열리고 VM의 상태 보기를 표시 합니다.

![선택한 Azure 가상 머신에 대한 VM용 Azure Monitor 상태 개요](./media/vminsights-health/vminsights-directvm-health-01.png)

**게스트 vm 상태** 섹션에서 테이블은 vm에 대 한 상태 기준으로 모니터링 되는 성능 구성 요소의 상태 롤업 및 비정상 구성 요소에서 발생 한 vm 상태 경고의 총 수를 보여 줍니다. 이러한 구성 요소에는 **CPU**, **메모리**, **디스크**및 **네트워크가**포함 됩니다. 게스트 VM 상태 옆에 있는 펼침 단추를 확장 하 여 해당 구성 요소의 상태를 확인 합니다.

![선택한 Azure 가상 컴퓨터의 구성 요소 상태를 VM용 Azure Monitor 합니다.](./media/vminsights-health/vminsights-directvm-health-02.png)

구성 요소 옆의 상태를 선택 하면 선택한 구성 요소의 컨텍스트에서 상태 진단 환경이 열립니다. 상태를 계산 하는 데 사용 되는 상태 조건을 설명 하는 해당 구성 요소 상태의 컴퍼지션을 보여 줍니다. 자세한 내용은 [상태 진단 및 상태 기준 작업](#health-diagnostics)을 참조 하세요. 경고에 대 한 자세한 내용은 [경고](#alerts)를 참조 하세요.

다음 표에는 VM에 대해 정의된 상태가 나와 있습니다.

|아이콘 |성능 상태 |의미 |
|-----|-------------|---------------|
| |Healthy |VM이 정의 된 상태 상태에 있습니다. 이 상태는 검색 된 문제가 없으며 VM이 정상적으로 작동 하 고 있음을 나타냅니다. 부모 롤업 모니터를 사용 하 여 상태를 롤업 하 고 자식의 최고 케이스 또는 최악의 상태를 반영 합니다.|
| |심각 |상태는 정의 된 상태 상태에 있지 않으며, 하나 이상의 중요 한 문제가 검색 되었음을 나타냅니다. 정상적인 기능을 복원 하려면 이러한 문제를 해결 해야 합니다. 부모 롤업 모니터를 사용 하 여 상태는 상태를 롤업 하 고 자식의 최고 케이스 또는 최악의 상태를 반영 합니다.|
| |Warning |상태는 정의 된 상태 조건에 대해 두 임계값 사이에 있습니다. 여기서 하나는 경고 상태를 나타내고 다른 하나는 위험 상태 (3 가지 상태 임계값을 구성할 수 있음)를 표시 하거나, 중요 하지 않은 문제가 발생할 경우 중요 한 문제가 발생할 수 있습니다. 않았기. 부모 롤업 모니터를 사용 하는 경우 하나 이상의 자식이 경고 상태 이면 부모는 경고 상태를 반영 합니다. 한 자식이 위험 상태이 고 다른 자식이 경고 상태에 있으면 부모 롤업이 상태를 위험으로 표시 합니다.|
| |알 수 없음 |여러 가지 이유로 상태를 계산할 수 없습니다. 다음 섹션에서는 추가 세부 정보 및 가능한 해결 방법을 제공 합니다. |

알 수 없는 상태는 다음과 같은 문제로 인해 발생할 수 있습니다.

- 에이전트가 다시 구성 되었으며 VM용 Azure Monitor 사용 하도록 설정 된 경우 지정 된 작업 영역에 더 이상 보고 하지 않습니다. 작업 영역에 보고 하도록 에이전트를 구성 하려면 [작업 영역 추가 또는 제거](../platform/agent-manage.md#adding-or-removing-a-workspace)를 참조 하세요.
- VM이 삭제 되었습니다.
- VM용 Azure Monitor 연결 된 작업 영역이 삭제 되었습니다. 프리미어 지원 혜택이 있는 경우 작업 영역을 복구할 수 있습니다. [프리미어](https://premier.microsoft.com/) 로 이동 하 여 지원 요청을 엽니다.
- 솔루션 종속성이 삭제 되었습니다. Log Analytics 작업 영역에서 servicemap InfrastructureInsights 솔루션을 다시 사용 하도록 설정 하려면 [Azure Resource Manager 템플릿을](vminsights-enable-at-scale-powershell.md#install-the-servicemap-solution)사용 하 여 servicemap 솔루션을 다시 설치 합니다. InfastructureInsights 솔루션을 다시 설치 하려면 전자 메일 vminsights@microsoft.com합니다. 
- VM이 종료 되었습니다.
- Azure VM 서비스를 사용할 수 없거나 유지 관리가 수행 되 고 있습니다.
- 작업 영역 [일일 데이터 또는 보존 한도](../platform/manage-cost-storage.md) 에 도달 했습니다.

**상태 진단 보기** 를 선택 하 여 vm의 모든 구성 요소, 연결 된 상태 조건, 상태 변경 및 vm과 관련 된 모니터링 구성 요소에서 검색 된 기타 문제를 표시 하는 페이지를 엽니다.

자세한 내용은 [상태 진단](#health-diagnostics)을 참조하세요.

**상태** 섹션에서 테이블은 상태 기준으로 모니터링 되는 성능 구성 요소의 상태 롤업을 보여 줍니다. 이러한 구성 요소에는 **CPU**, **메모리**, **디스크**및 **네트워크가**포함 됩니다. 구성 요소를 선택 하면 해당 구성 요소의 모니터링 조건 및 상태를 모두 나열 하는 페이지가 열립니다.

Windows를 실행 하는 Azure VM에서 상태에 액세스 하는 경우 상위 5 개 핵심 Windows 서비스의 상태가 **core 서비스 상태**아래에 표시 됩니다. 서비스를 선택 하면 해당 구성 요소에 대 한 상태 조건 모니터링이 해당 상태와 함께 표시 되는 페이지가 열립니다.

상태 조건 이름을 선택 하면 속성 창이 열립니다. 이 창에서 상태 조건에 해당 하는 Azure Monitor 경고가 포함 되어 있는지 여부를 포함 하 여 구성 세부 정보를 검토할 수 있습니다.

자세한 내용은 [상태 진단 및 상태 기준 작업](#health-diagnostics)을 참조 하세요.

### <a name="aggregate-vm-perspective"></a>집계 VM 관점

리소스 그룹의 모든 Vm에 대 한 상태 컬렉션을 보려면 포털의 탐색 목록에서 **Azure Monitor** 를 선택한 다음 **Virtual Machines (미리 보기)** 를 선택 합니다.

![Azure Monitor의 VM 인사이트 모니터링 보기](./media/vminsights-health/vminsights-aggregate-health.png)

**구독** 및 **리소스 그룹** 드롭다운 목록에서 해당 그룹과 관련 된 vm이 포함 된 적절 한 리소스 그룹을 선택 하 여 보고 된 상태를 확인 합니다. 선택 항목은 상태 기능에만 적용 되 고 **성능** 또는 **맵** 탭으로 전달 되지 않습니다.

**상태** 탭은 다음 정보를 제공 합니다.

* 위험 또는 비정상 상태에 있는 Vm의 수, 정상 상태 또는 데이터를 전송 하지 않는 (알 수 없는 상태 라고 함)
* OS에서 비정상 상태를 보고 하는 Vm 수
* 상태 별로 분류 된 프로세서, 디스크, 메모리 또는 네트워크 어댑터에서 검색 된 문제로 인해 비정상 상태인 Vm 수입니다.
* 상태 별로 분류 된 코어 OS 서비스에서 검색 된 문제로 인해 비정상 상태인 Vm의 수입니다.

**상태** 탭에서 VM을 모니터링 하는 상태 기준에서 검색 된 중요 한 문제를 식별 하 고, 경고 세부 정보 및 관련 기술 항목을 검토할 수 있습니다. 이러한 문서는 문제를 진단 하 고 수정 하는 데 도움이 될 수 있습니다. 아무 심각도나 선택하면 해당 심각도를 기준으로 필터링된 [모든 경고](../../azure-monitor/platform/alerts-overview.md#all-alerts-page) 페이지가 열립니다.

**운영 체제별 VM 배포** 목록에는 Windows 버전 또는 Linux 배포판에 나열된 VM이 해당 버전과 함께 표시됩니다. 각 OS 범주에서 vm은 VM의 상태에 따라 더 자세히 나뉩니다.

![VM 인사이트 가상 머신 배포 관점](./media/vminsights-health/vminsights-vmdistribution-by-os.png)

**VM 수**, **위험**, **경고**, **정상**또는 **알 수 없음**등의 열을 선택 합니다. **Virtual Machines** 페이지에서 선택한 열과 일치 하는 필터링 된 결과 목록을 표시 합니다.

예를 들어 Red Hat Enterprise Linux 릴리스 7.5를 실행 하는 모든 Vm을 검토 하려면 해당 OS에 대 한 **vm 수** 값을 선택 하 고 해당 필터와 일치 하는 vm 및 현재 상태를 나열 합니다.

![Red Hat Linux VM의 롤업 예제](./media/vminsights-health/vminsights-rollup-vm-rehl-01.png)

**상태 표시** 확인란을 클릭 하면 테이블의 필터링 된 결과에 대 한 상태가 반환 됩니다.  

![Red Hat Linux Vm의 상태 예](./media/vminsights-health/vminsights-rollup-vm-rehl-02.png)

목록의 항목 중 하나에 대해 해당 상태를 클릭 하 여 선택한 VM에 대 한 상태를 평가 하는 방법을 보여 주는 상태 진단을 시작할 수 있습니다. 

**Virtual Machines** 페이지에서 vm **이름**열 아래의 Vm 이름을 선택 하면 **vm 인스턴스** 페이지로 이동 합니다. 이 페이지에서는 선택한 VM에 영향을 주는 경고 및 상태 조건 문제에 대 한 자세한 정보를 제공 합니다. 페이지의 왼쪽 위 모서리에 있는 **상태 아이콘을** 선택 하 여 상태 정보를 필터링 하 여 비정상 상태인 구성 요소를 확인 합니다. 경고 심각도 별로 분류 된 비정상 구성 요소에 의해 발생 한 VM 상태 경고를 볼 수도 있습니다.

Vm에서 직접 **정보 (미리 보기)** 를 선택한 경우와 마찬가지로 **vm 목록** 뷰에서 vm의 이름을 선택 하 여 해당 Vm에 대 한 **상태** 페이지를 엽니다.

![선택한 Azure 가상 머신에 대한 VM 인사이트](./media/vminsights-health/vminsights-directvm-health.png)

**Azure Monitor 페이지의 Virtual Machines (미리 보기)** 는 VM 및 경고에 대 한 롤업 상태를 표시 합니다. 이 상태는 조건을 기반으로 상태가 정상에서 비정상으로 변경 될 때 발생 하는 VM 상태 경고를 나타내는 심각도로 분류 됩니다. **위험 조건에서 vm** 을 선택 하면 위험 상태에 있는 하나 이상의 vm 목록이 포함 된 페이지가 열립니다.

Vm 중 하나에 대 한 상태를 선택 하면 VM의 **상태 진단** 보기가 표시 됩니다. 이 보기에서는 상태 문제를 반영 하는 상태 조건을 확인할 수 있습니다. **상태 진단** 페이지가 열리면 모든 VM 구성 요소와 연결 된 상태 조건이 현재 상태와 함께 표시 됩니다.

자세한 내용은 [상태 진단](#health-diagnostics)을 참조하세요.

**모든 상태 조건 보기**를 선택하면 이 기능으로 사용할 수 있는 모든 상태 조건의 목록을 보여주는 페이지가 열립니다. 이 정보는 다음과 같은 옵션을 기반으로 추가로 필터링할 수 있습니다.

* 을 **입력**합니다. 조건을 평가 하 고 모니터링 되는 VM의 전반적인 상태를 롤업 하는 세 가지 유형의 상태 조건이 있습니다.
    - **단위**. VM의 일부 측면을 측정 합니다. 이 상태 조건 유형은 성능 카운터를 확인 하 여 구성 요소의 성능을 확인 하거나 가상 트랜잭션을 수행 하기 위해 스크립트를 실행 하거나 오류를 나타내는 이벤트를 감시 하는 것일 수 있습니다. 필터는 기본적으로 단위로 설정 됩니다.
    - **종속성**. 여러 엔터티 간의 상태 롤업을 제공 합니다. 이 상태 기준을 사용 하면 엔터티의 상태가 작업 성공에 사용 하는 다른 유형의 엔터티 상태에 따라 달라질 수 있습니다.
    - **집계**. 유사한 상태 조건의 결합 된 상태를 제공 합니다. 단위 및 종속성 상태 기준은 일반적으로 집계 상태 조건으로 구성 됩니다. 엔터티를 대상으로 하는 다양한 상태 조건에 대해 보다 나은 일반적인 구성을 제공하는 것 외에도, 집계 상태 조건은 엔터티의 개별 범주에 대해 고유한 성능 상태를 제공합니다.

* **범주**. 보고를 위해 유사한 조건을 그룹화 하는 데 사용 되는 상태 조건 유형입니다. 이러한 범주는 **가용성** 및 **성능**입니다.

비정상 상태인 인스턴스를 보려면 **비정상 구성 요소** 열에서 값을 선택 합니다. 이 페이지의 테이블에는 위험 상태에 있는 구성 요소가 나열 되어 있습니다.

## <a name="health-diagnostics"></a>상태 진단

**상태 진단** 페이지에서 VM의 상태 모델을 시각화할 수 있습니다. 이 페이지에는 vm과 관련 된 모니터링 되는 구성 요소에서 식별 한 모든 VM 구성 요소, 관련 상태 기준, 상태 변경 및 기타 중요 한 문제가 나열 됩니다.

![VM에 대한 상태 진단 페이지의 예](./media/vminsights-health/health-diagnostics-page-01.png)

다음 방법을 사용 하 여 상태 진단을 시작 합니다.

* Azure Monitor에서 집계 VM 관점의 모든 Vm에 대 한 롤업 상태:

    1. **상태** 페이지의 **게스트 VM 상태**섹션에서 **위험**, **경고**, **정상**또는 **알 수 없음** 상태에 대 한 아이콘을 선택 합니다.
    2. 필터링 된 범주와 일치 하는 모든 Vm을 나열 하는 페이지로 이동 합니다.
    3. 상태 진단 열에서 해당 VM으로 범위가 지정 된 상태 진단을 열려면 **상태** 열에서 값을 선택 합니다.

* Azure Monitor의 집계 VM 관점에서 OS를 기준으로 합니다. **VM 배포** 아래에 있는 열 값 중 하나를 선택하면 **Virtual Machines** 페이지가 열리고 필터링된 범주와 일치하는 테이블에 목록이 반환됩니다. **상태 열에서** 값을 선택 하면 선택한 VM에 대 한 상태 진단이 열립니다.
 
* 게스트 VM의 VM용 Azure Monitor **상태** 탭에서 **상태 진단 보기**를 선택 합니다.

상태 진단은 가용성 및 성능 이라는 두 가지 범주로 상태 정보를 구성 합니다.
 
논리 디스크, CPU 등의 구성 요소에 대해 정의 된 모든 상태 조건은 두 범주를 필터링 하지 않고 볼 수 있습니다. 이러한 뷰는 전체 조건 보기에 있거나, **가용성** 또는 **성능을**선택할 때 범주를 기준으로 결과를 필터링 하는 방법으로 사용할 수 있습니다.

또한 조건 범주는 **상태 기준** 열 옆에 표시 될 수 있습니다. 조건이 선택한 범주와 일치 하지 않으면 **선택한 범주에 사용할 수 있는 상태 조건을** 나타내는 메시지가 **상태 기준** 열에 표시 됩니다.

상태 조건의 상태는 **위험**, **경고**, **정상**및 **알 수 없음**의 네 가지 유형 중 하나로 정의 됩니다. 처음 세 개는 구성 가능 합니다. 즉, **상태 조건** 구성 창에서 모니터의 임계값을 직접 수정할 수 있습니다. Azure Monitor REST API [업데이트 모니터 작업](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitors/update)을 사용 하 여이 작업을 수행할 수도 있습니다. **알 수 없음** 은 구성 가능 하지 않으며 특정 시나리오용으로 예약 되어 있습니다.

**상태 진단** 페이지에는 세 가지 주요 섹션이 있습니다.

* 구성 요소 모델
* 상태 조건
* 상태 변경

![상태 진단 페이지의 섹션](./media/vminsights-health/health-diagnostics-page-02.png)

### <a name="component-model"></a>구성 요소 모델

**상태 진단** 페이지의 가장 왼쪽에 있는 열은 **구성 요소 모델**입니다. VM과 연결 된 모든 구성 요소가 현재 성능 상태와 함께이 열에 표시 됩니다.

다음 예제에서 검색 된 구성 요소는 **디스크**, **논리 디스크**, **프로세서**, **메모리**및 **운영 체제**입니다. 이러한 구성 요소의 여러 인스턴스가 검색되어 이 열에 표시됩니다.

예를 들어 다음 그림은 VM에 두 개의 논리 디스크 인스턴스 **C:** 및 **D:** (정상 상태)가 있음을 보여 줍니다.

![상태 진단에 제공된 예제 구성 요소 모델](./media/vminsights-health/health-diagnostics-page-component.png)

### <a name="health-criteria"></a>상태 조건

상태 진단 페이지의 가운데 열은 **상태 기준**입니다. VM에 대해 정의된 상태 모델이 계층 구조 트리에 표시됩니다. VM의 상태 모델은 단위 및 집계 상태 조건으로 구성됩니다.

![상태 진단에 제공된 상태 조건 예제](./media/vminsights-health/health-diagnostics-page-healthcriteria.png)

상태 기준은 모니터링 되는 인스턴스의 상태를 측정 하며,이는 임계값, 엔터티 상태 등이 될 수 있습니다. 상태 조건에는 앞에서 설명한 대로 2 개 또는 3 개의 구성 가능한 상태 임계값이 있습니다. 언제 든 지 상태 기준은 잠재적 상태 중 하나일 수 있습니다.

상태 모델은 대상의 전체 대상 및 구성 요소의 상태를 결정 하는 조건을 정의 합니다. 조건 계층이 **상태 진단** 페이지의 **상태 조건** 섹션에 표시 됩니다.

상태 롤업 정책은 집계 상태 기준 구성의 일부입니다. 기본값은 **최악의**경우로 설정 됩니다. 이 문서의 [모니터링 구성 세부 정보](#monitoring-configuration-details) 섹션에서이 기능의 일부로 실행 되는 기본 상태 조건 집합을 찾을 수 있습니다.

또한 Azure Monitor REST API [모니터링 인스턴스 목록](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitorinstances/listbyresource) 을 사용 하 여 모든 상태 조건 목록을 가져올 수 있습니다. 이 조건에는 Azure VM 리소스에 대해 실행 되는 구성 정보가 포함 됩니다.

**Unit** health 조건 유형은 오른쪽에 있는 줄임표 링크를 선택 하 여 해당 구성을 수정할 수 있습니다. **세부 정보 표시** 를 선택 하 여 구성 창을 엽니다.

![상태 조건 구성 예제](./media/vminsights-health/health-diagnostics-vm-example-02.png)

선택한 상태 조건의 구성 창에서 예제 **평균 디스크 쓰기 시간 (초)** 을 사용 하는 경우 다른 숫자 값을 사용 하 여 임계값을 구성할 수 있습니다. 이 모니터는 두 가지 상태 모니터입니다. 즉, **정상** 에서 **경고**로만 변경할 수 있습니다.

경고 및 위험 상태 임계값을 구성할 수 있는 세 가지 상태를 사용 하는 경우도 있습니다. Azure Monitor REST API [모니터 구성을](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitors/update)사용 하 여 임계값을 수정할 수도 있습니다.

>[!NOTE]
>상태 기준 구성 변경 내용을 한 인스턴스에 적용 하면 모니터링 되는 모든 인스턴스에 적용 됩니다. 예를 들어 **디스크-1 D:** 를 선택 하 고 **평균 디스크 쓰기 시간 (초)** 을 수정 하는 경우 변경 내용은 VM에서 검색 되 고 모니터링 되는 모든 인스턴스에 적용 됩니다.


![단위 모니터의 상태 조건 구성 예제](./media/vminsights-health/health-diagnostics-criteria-config-01.png)

상태 기준에 대 한 자세한 내용을 보려면 문제, 원인 및 해결 방법을 식별 하는 데 도움이 되는 기술 문서를 포함 합니다. 관련 기술 항목을 보려면 페이지에서 **정보 보기** 를 선택 합니다.

VM용 Azure Monitor 상태에 포함 된 모든 기술 항목을 검토 하려면 [Azure Monitor health knowledge 설명서](https://docs.microsoft.com/azure/monitoring/infrastructure-health/)를 참조 하십시오.

### <a name="state-changes"></a>상태 변경

상태 **진단** 페이지의 오른쪽 끝 열은 **상태 변경**입니다. 이 열에 **는 상태 조건 섹션에서** 선택한 상태 조건에 연결 된 모든 상태 변경 내용 또는 테이블의 **구성 요소 모델** 또는 **상태 조건** 열에서 vm이 선택 된 경우 vm의 상태 변경이 나열 됩니다.

![상태 진단에 제공된 상태 변경 예제](./media/vminsights-health/health-diagnostics-page-statechanges.png)

다음 섹션에서는 상태 조건 상태 및 관련 시간을 보여 줍니다. 이 정보는 열 맨 위에 있는 최신 상태를 표시 합니다.

### <a name="association-of-component-model-health-criteria-and-state-changes-columns"></a>구성 요소 모델, 상태 조건 및 상태 변경 열 연결

세 개의 열은 서로 간에 상호 연결되어 있습니다. **구성 요소 모델** 열에서 인스턴스를 선택 하면 **상태 조건** 열이 해당 구성 요소 뷰로 필터링 됩니다. 마찬가지로 **상태 변경** 열은 선택한 상태 조건에 따라 업데이트 됩니다.

![모니터링되는 인스턴스 선택 및 결과 예제](./media/vminsights-health/health-diagnostics-vm-example-01.png)

예를 들어 **구성 요소 모델**아래의 목록에서 *디스크-1 d:* 를 선택 하는 경우 상태 **조건은** *디스크-1D:* 로 필터링 되 고 **상태 변경은** *디스크-1 D:* 의 가용성에 따라 상태 변경을 표시 합니다.

업데이트 된 상태를 보려면 **새로 고침** 링크를 선택 하 여 상태 진단 페이지를 새로 고칠 수 있습니다. 미리 정의된 폴링 간격을 기반으로 상태 조건의 성능 상태에 대한 업데이트가 있는 경우, 이 작업을 통해 대기하지 않고 최신 성능 상태를 반영할 수 있습니다. **상태 기준 상태** 는 선택 된 상태 (정상, 경고, 위험, 알 수 없음 및 모두)를 기준으로 결과의 범위를 지정할 수 있도록 하는 필터입니다. 오른쪽 위 모서리에 **마지막으로 업데이트** 된 시간은 상태 진단 페이지를 마지막으로 새로 고친 시간을 나타냅니다.

## <a name="alerts"></a>경고

VM용 Azure Monitor 상태는 [Azure 경고](../../azure-monitor/platform/alerts-overview.md)와 통합 됩니다. 미리 정의 된 조건이 검색 되 면 정상 상태에서 비정상 상태로 변경 될 때 경고가 발생 합니다. 경고는 심각도 0부터 심각도 4까지 심각도 별로 분류 되 고 심각도 0은 가장 높은 수준으로 분류 됩니다.

경고가 트리거 되었을 때 알림을 보낼 수 있도록 경고는 작업 그룹에 연결 되지 않습니다. 구독 범위에서 소유자 역할을 가진 사용자는 [경고 구성](#configure-alerts) 섹션의 단계에 따라 알림을 구성 해야 합니다.

심각도 별로 분류 된 VM 상태 경고의 총 수는 **상태** 대시보드의 **경고** 섹션에서 확인할 수 있습니다. 총 경고 수 또는 심각도 수준에 해당하는 숫자를 선택하면, **경고** 페이지가 열리고 선택 항목과 일치하는 모든 경고가 나열됩니다.

예를 들어 **심각도 수준 1**에 해당 하는 행을 선택 하는 경우 다음 뷰가 표시 됩니다.

![모든 심각도 수준 1 경고 예제](./media/vminsights-health/vminsights-sev1-alerts-01.png)

**모든 경고** 페이지의 범위는 선택 항목과 일치 하는 경고만 표시 되지 않습니다. 또한 **리소스 유형별로** 필터링 되어 VM 리소스에 의해 발생 한 상태 경고만 표시 합니다. 이 형식은 경고 목록에서 열 **대상 리소스**아래에 반영 되며 비정상 상태가 충족 될 때 발생 한 Azure VM이 표시 됩니다.

다른 리소스 유형 또는 서비스의 경고는이 보기에 포함 되지 않습니다. 이러한 경고에는 기본 Azure Monitor [모든 경고](../../azure-monitor/platform/alerts-overview.md#all-alerts-page) 페이지에서 일반적으로 표시 하는 로그 쿼리 또는 메트릭 경고를 기반으로 하는 로그 경고가 포함 됩니다.

페이지 위쪽의 드롭다운 메뉴에서 값을 선택 하 여이 뷰를 필터링 할 수 있습니다.

|열 |설명 |
|-------|------------|
|구독 |Azure 구독을 선택합니다. 선택한 구독의 경고만 보기에 포함됩니다. |
|리소스 그룹 |단일 리소스 그룹을 선택합니다. 선택한 리소스 그룹의 대상이 있는 경고만 보기에 포함됩니다. |
|리소스 형식 |리소스 종류를 하나 이상 선택합니다. 기본적으로 대상 **가상 머신**의 경고만 선택되고 이 보기에 포함됩니다. 이 열은 리소스 그룹을 지정한 후에만 사용할 수 있습니다. |
|리소스 |리소스를 선택합니다. 해당 리소스가 대상으로 지정된 경고만 보기에 포함됩니다. 이 열은 리소스 종류가 지정 된 후에만 사용할 수 있습니다. |
|severity |경고 심각도를 선택하거나 심각도에 상관없이 모든 경고를 포함하려면 **모두**를 선택합니다. |
|조건 모니터링 |경고가 더 이상 활성 상태가 아닌 경우 시스템에 의해 발생 하거나 해결 된 경우 경고를 필터링 하려면 모니터 조건을 선택 합니다. 또는 **모두를 선택 하** 여 모든 조건의 경고를 포함 합니다. |
|경고 상태 |모든 상태에 대 한 경고를 포함 하려면 경고 상태, **새로 만들기**, **승인**, **닫힘**또는 **모두** 를 선택 합니다. |
|서비스 모니터링 |한 서비스를 선택하거나 모든 서비스를 포함하려면 **모두**를 선택합니다. 이 기능에는 VM Insights의 경고만 지원 됩니다.|
|시간 범위| 선택한 기간 내에 발생한 경고만 보기에 포함됩니다. 지원되는 값은 지난 1시간, 지난 24시간, 지난 7일 및 지난 30일입니다. |

경고를 선택 하면 **경고 정보** 페이지가 표시 됩니다. 이 페이지에서는 경고에 대 한 세부 정보를 제공 하 고 해당 상태를 변경할 수 있습니다.

경고 관리에 대한 자세한 내용은 [Azure Monitor를 사용하여 경고 만들기, 보기 및 관리](../../azure-monitor/platform/alerts-metric.md)를 참조하세요.

>[!NOTE]
>현재 포털에서 Azure Monitor의 상태 조건을 기반으로 새 경고를 만들거나 기존 상태 경고 규칙을 수정 하는 기능은 현재 지원 되지 않습니다.


![선택한 경고에 대한 경고 세부 정보 창](./media/vminsights-health/alert-details-pane-01.png)

하나 또는 여러 경고를 선택한 다음 왼쪽 위 모서리의 **모든 경고** 페이지에서 **상태 변경** 을 선택 하 여 경고 상태를 변경할 수 있습니다. **경고 상태 변경** 창에서 상태 중 하나를 선택 하 고, **설명** 필드에 변경 내용에 대 한 설명을 추가한 다음, **확인** 을 선택 하 여 변경 내용을 커밋합니다. 정보가 확인 되 고 변경 내용이 적용 되 면 메뉴의 **알림** 에서 진행 상황을 추적 합니다.

### <a name="configure-alerts"></a>경고 구성
Azure Portal에서 특정 경고 관리 작업을 관리할 수 없습니다. 이러한 작업은 [Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components)를 사용 하 여 수행 해야 합니다. 구체적으로 살펴보면 다음과 같습니다.

- 상태 조건에 대 한 경고 사용 또는 사용 안 함
- 상태 조건 경고에 대 한 알림 설정

각 예제는 Windows 컴퓨터에서 [ARMClient](https://github.com/projectkudu/armclient) 을 사용 합니다. 이 메서드에 익숙하지 않은 경우 [ARMClient 사용](../platform/rest-api-walkthrough.md#use-armclient)을 참조 하세요.

#### <a name="enable-or-disable-an-alert-rule"></a>경고 규칙 사용 또는 사용 안 함

특정 상태 조건에 대 한 경고를 사용 하거나 사용 하지 않도록 설정 하려면 값을 **Disabled** 또는 **Enabled**로 설정 하 여 **alertgeneration** 속성을 수정 해야 합니다.

특정 상태 기준에 대 한 *monitorId* 를 식별 하기 위해 다음 예제에서는 **Logicaldisk\avg Disk Seconds Per Transfer**의 조건에 대해 해당 값을 쿼리 하는 방법을 보여 줍니다.

1. 터미널 창에서 **armclient.exe login**을 입력합니다. 이렇게 하면 Azure에 로그인 하 라는 메시지가 표시 됩니다.

2. 다음 명령을 입력 하 여 특정 VM에서 활성 상태인 모든 상태 조건을 검색 하 고 *monitorId* 속성의 값을 확인 합니다.

    ```
    armclient GET "subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/monitors?api-version=2018-08-31-preview"
    ```

    다음 예에서는 *ARMCLIENT GET* 명령의 출력을 보여 줍니다. *MonitorId*의 값을 기록해 둡니다. 이 값은 다음 단계에서 필요 합니다. 여기서는 상태 조건의 ID를 지정 하 고 해당 속성을 수정 하 여 경고를 생성 해야 합니다.

    ```
    "id": "/subscriptions/a7f23fdb-e626-4f95-89aa-3a360a90861e/resourcegroups/Lab/providers/Microsoft.Compute/virtualMachines/SVR01/providers/Microsoft.WorkloadMonitor/monitors/ComponentTypeId='LogicalDisk',MonitorId='Microsoft_LogicalDisk_AvgDiskSecPerRead'",
      "name": "ComponentTypeId='LogicalDisk',MonitorId='Microsoft_LogicalDisk_AvgDiskSecPerRead'",
      "type": "Microsoft.WorkloadMonitor/virtualMachines/monitors"
    },
    {
      "properties": {
        "description": "Monitor the performance counter LogicalDisk\\Avg Disk Sec Per Transfer",
        "monitorId": "Microsoft_LogicalDisk_AvgDiskSecPerTransfer",
        "monitorName": "Microsoft.LogicalDisk.AvgDiskSecPerTransfer",
        "monitorDisplayName": "Average Logical Disk Seconds Per Transfer",
        "parentMonitorName": null,
        "parentMonitorDisplayName": null,
        "monitorType": "Unit",
        "monitorCategory": "PerformanceHealth",
        "componentTypeId": "LogicalDisk",
        "componentTypeName": "LogicalDisk",
        "componentTypeDisplayName": "Logical Disk",
        "monitorState": "Enabled",
        "criteria": [
          {
            "healthState": "Warning",
            "comparisonOperator": "GreaterThan",
            "threshold": 0.1
          }
        ],
        "alertGeneration": "Enabled",
        "frequency": 1,
        "lookbackDuration": 17,
        "documentationURL": "https://aka.ms/Ahcs1r",
        "configurable": true,
        "signalType": "Metrics",
        "signalName": "VMHealth_Avg. Logical Disk sec/Transfer"
      },
      "etag": null,
    ```

3. 다음 명령을 입력 하 여 *Alertgeneration* 속성을 수정 합니다.

    ```
    armclient patch subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/monitors/Microsoft_LogicalDisk_AvgDiskSecPerTransfer?api-version=2018-08-31-preview "{'properties':{'alertGeneration':'Disabled'}}"
    ```   

4. 2 단계에서 사용 되는 GET 명령을 입력 하 여 속성 값이 **사용 안 함**으로 설정 되었는지 확인 합니다.

#### <a name="associate-an-action-group-with-health-criteria"></a>상태 기준과 작업 그룹 연결

VM용 Azure Monitor 상태에서는 경고가 비정상 상태 기준에서 생성 될 때 SMS 및 전자 메일 알림을 지원 합니다. 알림을 구성 하려면 SMS 또는 전자 메일 알림을 보낼 구성 된 작업 그룹의 이름을 적어둡니다.

>[!NOTE]
>알림을 받을 모니터링 되는 각 VM에 대해이 작업을 수행 해야 합니다. 리소스 그룹의 모든 Vm에는 적용 되지 않습니다.

1. 터미널 창에서 *armclient 로그인*을 입력 합니다. 이렇게 하면 Azure에 로그인 하 라는 메시지가 표시 됩니다.

2. 다음 명령을 입력 하 여 작업 그룹을 경고 규칙과 연결 합니다.
 
    ```
    $payload = "{'properties':{'ActionGroupResourceIds':['/subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/microsoft.insights/actionGroups/actiongroupName']}}"
    armclient PUT https://management.azure.com/subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/notificationSettings/default?api-version=2018-08-31-preview $payload
    ```

3. **ActionGroupResourceIds** 속성 값이 성공적으로 업데이트 되었는지 확인 하려면 다음 명령을 입력 합니다.

    ```
    armclient GET "subscriptions/subscriptionName/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/notificationSettings?api-version=2018-08-31-preview"
    ```

    출력은 다음과 같아야 합니다.
    
    ```
    {
      "value": [
        {
          "properties": {
            "actionGroupResourceIds": [
              "/subscriptions/a7f23fdb-e626-4f95-89aa-3a360a90861e/resourceGroups/Lab/providers/microsoft.insights/actionGroups/Lab-IT%20Ops%20Notify"
            ]
          },
          "etag": null,
          "id": "/subscriptions/a7f23fdb-e626-4f95-89aa-3a360a90861e/resourcegroups/Lab/providers/Microsoft.Compute/virtualMachines/SVR01/providers/Microsoft.WorkloadMonitor/notificationSettings/default",
          "name": "notificationSettings/default",
          "type": "Microsoft.WorkloadMonitor/virtualMachines/notificationSettings"
        }
      ],
      "nextLink": null
    }
    ```

## <a name="next-steps"></a>다음 단계

- 제한 사항 및 전반적인 VM 성능을 식별 하려면 [AZURE VM 성능 보기](vminsights-performance.md)를 참조 하세요.

- 검색 된 응용 프로그램 종속성에 대 한 자세한 내용은 [View VM용 Azure Monitor Map](vminsights-maps.md)을 참조 하십시오.
