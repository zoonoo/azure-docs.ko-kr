---
title: Azure Site Recovery 프로세스 서버를 모니터링 합니다.
description: 이 문서에서는 Azure Site Recovery 프로세스 서버를 모니터링 하는 방법을 설명 합니다.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: raynew
ms.openlocfilehash: 4ff52e737438210296b8f2201d5e66e1d38b7bc9
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66418292"
---
# <a name="monitor-the-process-server"></a>프로세스 서버 모니터링

이 문서에서는 모니터링 하는 방법을 설명 합니다 [Site Recovery](site-recovery-overview.md) 프로세스 서버.

- 프로세스 서버는 온-프레미스 VMware Vm 및 물리적 서버에서 Azure로의 재해 복구를 설정할 때 사용 됩니다.
- 기본적으로 프로세스 서버는 구성 서버에서 실행 됩니다. 구성 서버를 배포 하는 경우 기본적으로 설치 됩니다.
- 필요에 따라 규모 및 핸들 많은 수의 복제 된 컴퓨터를 복제 트래픽이 높은 볼륨을 추가 하 고 스케일 아웃 프로세스 서버를 배포할 수 있습니다.

[자세한](vmware-physical-azure-config-process-server-overview.md) 역할 및 프로세스 서버 배포에 대 한 합니다.

## <a name="monitoring-overview"></a>모니터링 개요

프로세스 서버에서 복제 된 데이터 캐싱, 압축 및 azure에 전송에 특히 많은 역할 이므로 지속적으로 프로세스 서버 상태를 모니터링 하는 것이 중요 합니다.

일반적으로 프로세스 서버 성능에 영향을 주는 경우에 여러 가지가 있습니다. 성능에 영향을 주는 문제는 결국 푸시 프로세스 서버와 해당 복제 된 컴퓨터를 위험 상태로 VM 상태에 연계 효과 해야 합니다. 같은 경우가 포함 됩니다.

- 많은 수의 Vm에 근접 하거나 초과 하는 권장 되는 제한 사항 프로세스 서버를 사용 합니다.
- 프로세스 서버를 사용 하 여 Vm 높은 이탈 률이 경우
- Vm 및 프로세스 서버 간의 네트워크 처리량을 프로세스 서버로 복제 데이터를 업로드할 충분 하지 않습니다.
- 프로세스 서버와 Azure 간의 네트워크 처리량 부족해 프로세스 서버에서 Azure로 복제 데이터를 업로드.

이러한 모든 문제 Vm의 복구 지점 목표 (RPO)에 영향을 줄 수 있습니다. 

**이유는 무엇입니까?** VM에 대 한 복구 지점 생성에 모든 VM 디스크의 공통 요소를 포함할 필요 때문에 합니다. 하나의 디스크에 높은 변동률, 복제, 느린 되었거나 프로세스 서버에는 최적의 방법이 아닙니다. 영향 얼마나 효율적으로 복구 지점이 생성 됩니다.

## <a name="monitor-proactively"></a>사전 모니터링

이 프로세스 서버를 사용 하 여 문제를 방지 하려면에 중요 합니다.

- 프로세스 서버를 사용 하 여 특정 요구 사항 이해 [용량 및 크기 조정 지침](site-recovery-plan-capacity-vmware.md#capacity-considerations), 권장 사항에 따라 실행 되 고 프로세스 서버를 배포 되었는지 확인 합니다.
- 경고, 모니터링 및 문제 발생 했을 때 프로세스 서버를 효율적으로 실행 되도록 합니다.


## <a name="process-server-alerts"></a>프로세스 서버 경고

프로세스 서버는 다음 표에 요약 된 상태 경고의 수를 생성 합니다.

**경고 유형** | **세부 정보**
--- | ---
![Healthy][green] | 프로세스 서버가 연결 되어 있고 정상입니다.
![Warning][yellow] | 지난 15 분 동안 CPU 사용률이 > 80%
![Warning][yellow] | 지난 15 분간의 메모리 사용량 > 80%
![Warning][yellow] | 지난 15 분 동안 캐시 폴더 사용 가능한 공간이 < 30%
![Warning][yellow] | 프로세스 서버 서비스가 지난 15 분 동안 실행 되지 않습니다.
![중요][red] | 지난 15 분 동안 CPU 사용률이 > 95%
![중요][red] | 지난 15 분간의 메모리 사용량 > 95%
![중요][red] | 최근 15 분 동안 캐시 폴더 사용 가능한 공간이 < 25%
![중요][red] | 15 분 동안 프로세스 서버에서 하트 비트가 없습니다.

![테이블 키](./media/vmware-physical-azure-monitor-process-server/table-key.png)

> [!NOTE]
> 프로세스 서버의 전반적인 상태는 최악의 생성 된 경고를 기반으로 합니다.



## <a name="monitor-process-server-health"></a>프로세스 서버 상태 모니터

다음과 같이 프로세스 서버 상태를 모니터링할 수 있습니다. 

1. 복제 상태 및 자격 증명 모음에서 해당 프로세스 서버 및 복제 된 컴퓨터의 상태를 모니터링 하려면 > **복제 된 항목**를 모니터링 하려면 컴퓨터를 클릭 합니다.
2. **복제 상태**, VM 상태를 모니터링할 수 있습니다. 오류 세부 정보에 대 한 드릴 다운 하려면 상태를 클릭 합니다.

    ![VM 대시보드의 프로세스 서버 상태](./media/vmware-physical-azure-monitor-process-server/vm-ps-health.png)

4. **프로세스 서버 상태**, 프로세스 서버 상태를 모니터링할 수 있습니다. 자세한 내용을 드릴 다운 합니다.

    ![VM 대시보드의 프로세스 서버 세부 정보](./media/vmware-physical-azure-monitor-process-server/ps-summary.png)

5. 상태 그래픽 표현을 사용 하 여 VM 페이지에서 모니터링할 수 있습니다.
    - 스케일 아웃 프로세스 서버에 연결 된 경고가 있으면 주황색으로 강조 표시 하 고 중요 한 문제가 있을 경우 빨간색 됩니다. 
    - 구성 서버에서 기본 배포에서 프로세스 서버가 실행 중인지, 한 구성 서버에 따라 선택 됩니다.
    - 드릴 다운 하려면 구성 서버 또는 프로세스 서버를 클릭 합니다. 모든 문제 및 수정 권장 사항을 note 합니다.

모니터링할 수도 있습니다 프로세스 서버에서 자격 증명 모음에서 **Site Recovery 인프라**합니다. **Site Recovery 인프라를 관리할**, 클릭 **구성 서버**합니다. 연결 된 프로세스 서버 및 드릴 다운 프로세스 서버 세부 정보 구성 서버를 선택 합니다.


## <a name="next-steps"></a>다음 단계

- 가 있는 서버 문제를 처리를 수행 하는 경우이 [문제 해결 지침](vmware-physical-azure-troubleshoot-process-server.md)
- 도움이 필요한 경우 [Azure Site Recovery 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)에 질문을 게시하세요. 

[green]: ./media/vmware-physical-azure-monitor-process-server/green.png
[yellow]: ./media/vmware-physical-azure-monitor-process-server/yellow.png
[red]: ./media/vmware-physical-azure-monitor-process-server/red.png
