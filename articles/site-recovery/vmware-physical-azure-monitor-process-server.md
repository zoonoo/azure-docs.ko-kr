---
title: Azure Site Recovery 프로세스 서버 모니터링
description: 이 문서에서는 Azure Site Recovery 프로세스 서버를 모니터링 하는 방법을 설명 합니다.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: raynew
ms.openlocfilehash: 5d746385a034fdf742b8958b3d1fe51ea2a3c5cf
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69972187"
---
# <a name="monitor-the-process-server"></a>프로세스 서버 모니터링

이 문서에서는 [Site Recovery](site-recovery-overview.md) 프로세스 서버를 모니터링 하는 방법을 설명 합니다.

- 프로세스 서버는 Azure에 대 한 온-프레미스 VMware Vm 및 물리적 서버의 재해 복구를 설정할 때 사용 됩니다.
- 기본적으로 프로세스 서버는 구성 서버에서 실행 됩니다. 구성 서버를 배포할 때 기본적으로 설치 됩니다.
- 필요에 따라 많은 수의 복제 된 컴퓨터를 확장 하 고 처리 하 고 더 많은 볼륨의 복제 트래픽을 처리 하기 위해 추가 확장 프로세스 서버를 배포할 수 있습니다.

프로세스 서버의 역할 및 배포에 [대해 자세히 알아보세요](vmware-physical-azure-config-process-server-overview.md) .

## <a name="monitoring-overview"></a>모니터링 개요

프로세스 서버에는 특히 복제 된 데이터 캐싱, 압축 및 Azure로 전송에서 많은 역할이 있으므로 지속적으로 프로세스 서버 상태를 모니터링 하는 것이 중요 합니다.

일반적으로 프로세스 서버 성능에 영향을 주는 여러 가지 상황이 있습니다. 성능에 영향을 주는 문제는 VM 상태에 연계 된 영향을 미치고 결국 프로세스 서버와 복제 된 컴퓨터를 모두 위험 상태로 푸시합니다. 경우는 다음과 같습니다.

- 많은 수의 Vm은 프로세스 서버를 사용 하 여 권장 되는 제한에 도달 하거나 초과 합니다.
- 프로세스 서버를 사용 하는 Vm의 변동 율은 높습니다.
- Vm과 프로세스 서버 간의 네트워크 처리량 만으로는 복제 데이터를 프로세스 서버로 업로드할 수 없습니다.
- 프로세스 서버와 Azure 간의 네트워크 처리량은 프로세스 서버에서 Azure로 복제 데이터를 업로드 하는 데 충분 하지 않습니다.

이러한 모든 문제는 Vm의 RPO (복구 지점 목표)에 영향을 줄 수 있습니다. 

**굳이?** VM에 대 한 복구 지점을 생성 하려면 VM의 모든 디스크에 공통 지점이 있어야 합니다. 한 디스크의 변동 비율이 높으면 복제가 느리거나 프로세스 서버가 최적이 아니면 효율적으로 복구 지점이 생성 되는 방식에 영향을 줍니다.

## <a name="monitor-proactively"></a>사전 모니터링

프로세스 서버와 관련 된 문제를 방지 하려면 다음을 수행 하는 것이 중요 합니다.

- [용량 및 크기 조정 지침](site-recovery-plan-capacity-vmware.md#capacity-considerations)을 사용 하 여 프로세스 서버에 대 한 특정 요구 사항을 이해 하 고, 권장 사항에 따라 프로세스 서버가 배포 및 실행 되 고 있는지 확인 합니다.
- 경고를 모니터링 하 고 발생 하는 문제를 해결 하 여 프로세스 서버를 효율적으로 계속 실행 합니다.


## <a name="process-server-alerts"></a>프로세스 서버 경고

프로세스 서버는 다음 표에 요약 된 다양 한 상태 경고를 생성 합니다.

**경고 유형** | **세부 정보**
--- | ---
![정상][green] | 프로세스 서버가 연결 되어 있고 정상 상태입니다.
![경고][yellow] | 지난 15 분간 CPU 사용률 > 80%
![경고][yellow] | 최근 15 분 동안 메모리 사용 > 80%
![경고][yellow] | 최근 15 분 동안 캐시 폴더의 사용 가능한 공간 < 30%
![경고][yellow] | 지난 15 분간 프로세스 서버 서비스가 실행 되 고 있지 않음
![심각][red] | 지난 15 분간 CPU 사용률 > 95%
![심각][red] | 최근 15 분 동안 메모리 사용 > 95%
![심각][red] | 최근 15 분 동안 캐시 폴더의 사용 가능한 공간 < 25%
![심각][red] | 15 분 동안 프로세스 서버에서 하트 비트가 없습니다.

![테이블 키](./media/vmware-physical-azure-monitor-process-server/table-key.png)

> [!NOTE]
> 프로세스 서버의 전반적인 상태는 생성 된 최악의 경고를 기반으로 합니다.



## <a name="monitor-process-server-health"></a>프로세스 서버 상태 모니터링

다음과 같이 프로세스 서버의 상태를 모니터링할 수 있습니다. 

1. 복제 된 컴퓨터와 해당 프로세스 서버의 복제 상태를 모니터링 하려면 자격 증명 모음 > **복제 된 항목**에서 모니터링 하려는 컴퓨터를 클릭 합니다.
2. **복제 상태**에서 VM 상태를 모니터링할 수 있습니다. 상태를 클릭 하 여 오류 정보를 드릴 다운 합니다.

    ![VM 대시보드의 프로세스 서버 상태](./media/vmware-physical-azure-monitor-process-server/vm-ps-health.png)

4. **프로세스 서버 상태**에서 프로세스 서버의 상태를 모니터링할 수 있습니다. 드릴 다운 하 여 세부 정보를 확인 합니다.

    ![VM 대시보드의 프로세스 서버 세부 정보](./media/vmware-physical-azure-monitor-process-server/ps-summary.png)

5. VM 페이지의 그래픽 표현을 사용 하 여 상태를 모니터링할 수도 있습니다.
    - 확장 프로세스 서버와 관련 된 경고가 있으면 주황색으로 강조 표시 되 고 중요 한 문제가 있는 경우 빨간색으로 강조 표시 됩니다. 
    - 프로세스 서버가 구성 서버의 기본 배포에서 실행 되는 경우 구성 서버가 그에 따라 강조 표시 됩니다.
    - 드릴 다운 하려면 구성 서버 또는 프로세스 서버를 클릭 합니다. 모든 문제 및 수정 권장 사항을 기록해 둡니다.

또한 **Site Recovery 인프라**의 자격 증명 모음에서 프로세스 서버를 모니터링할 수 있습니다. **Site Recovery 인프라 관리**에서 **구성 서버**를 클릭 합니다. 프로세스 서버와 연결 된 구성 서버를 선택 하 고 프로세스 서버 세부 정보로 드릴 다운 합니다.


## <a name="next-steps"></a>다음 단계

- 프로세스 서버 문제가 있는 경우 [문제 해결 지침](vmware-physical-azure-troubleshoot-process-server.md) 을 따르세요.
- 도움이 필요한 경우 [Azure Site Recovery 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)에 질문을 게시하세요. 

[green]: ./media/vmware-physical-azure-monitor-process-server/green.png
[yellow]: ./media/vmware-physical-azure-monitor-process-server/yellow.png
[red]: ./media/vmware-physical-azure-monitor-process-server/red.png
