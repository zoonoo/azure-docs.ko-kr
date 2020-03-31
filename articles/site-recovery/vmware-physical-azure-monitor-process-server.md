---
title: Azure 사이트 복구 프로세스 서버 모니터링
description: 이 문서에서는 VMware VM/물리적 서버 재해 복구에 사용되는 Azure 사이트 복구 프로세스 서버를 모니터링하는 방법에 대해 설명합니다.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: 54c161c40c881d7626f79fc9bfe1ec1c160480ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257070"
---
# <a name="monitor-the-process-server"></a>프로세스 서버 모니터링

이 문서에서는 [사이트 복구](site-recovery-overview.md) 프로세스 서버를 모니터링하는 방법을 설명합니다.

- 프로세스 서버는 온-프레미스 VM웨어 VM 및 물리적 서버의 재해 복구를 Azure에 설정할 때 사용됩니다.
- 기본적으로 프로세스 서버는 구성 서버에서 실행됩니다. 구성 서버를 배포할 때 기본적으로 설치됩니다.
- 선택적으로 더 많은 수의 복제된 컴퓨터와 더 많은 대량의 복제 트래픽을 확장하고 처리하려면 추가 확장 프로세스 서버를 배포할 수 있습니다.

프로세스 서버의 역할 및 배포에 대해 [자세히 알아봅니다.](vmware-physical-azure-config-process-server-overview.md)

## <a name="monitoring-overview"></a>모니터링 개요

프로세스 서버는 특히 복제된 데이터 캐싱, 압축 및 Azure로의 전송에서 많은 역할을 가지고 있으므로 프로세스 서버 상태를 지속적으로 모니터링하는 것이 중요합니다.

일반적으로 프로세스 서버 성능에 영향을 주는 여러 가지 상황이 있습니다. 성능에 영향을 미치는 문제는 VM 상태에 연속적인 영향을 미치므로 결국 프로세스 서버와 복제된 컴퓨터를 모두 위험 상태로 밀어 넣습니다. 상황은 다음과 같습니다.

- 많은 수의 VM이 권장 제한에 근접하거나 초과하는 프로세스 서버를 사용합니다.
- 프로세스 서버를 사용하는 VM의 이탈률이 높습니다.
- VM과 프로세스 서버 간의 네트워크 처리량만으로는 복제 데이터를 프로세스 서버에 업로드할 수 없습니다.
- 프로세스 서버와 Azure 간의 네트워크 처리량은 프로세스 서버에서 Azure로 복제 데이터를 업로드하기에 충분하지 않습니다.

이러한 모든 문제는 VM의 RPO(복구 지점 목표)에 영향을 줄 수 있습니다. 

**왜?** VM에 대한 복구 지점을 생성하려면 VM의 모든 디스크에 공통점이 있어야 합니다. 한 디스크의 이탈률이 높거나 복제속도가 느리거나 프로세스 서버가 최적이 아닌 경우 복구 지점이 얼마나 효율적으로 생성되는지에 영향을 미칩니다.

## <a name="monitor-proactively"></a>사전 모니터링

프로세스 서버의 문제를 방지하려면 다음을 수행해야 합니다.

- 용량 및 크기 조정 [지침을](site-recovery-plan-capacity-vmware.md#capacity-considerations)사용하여 프로세스 서버에 대한 특정 요구 사항을 이해하고 권장 사항에 따라 프로세스 서버가 배포되고 실행되도록 합니다.
- 경고를 모니터링하고 문제가 발생할 때 문제를 해결하여 프로세스 서버를 효율적으로 실행합니다.


## <a name="process-server-alerts"></a>서버 경고 처리

프로세스 서버는 다음 표에 요약된 여러 상태 경고를 생성합니다.

**경고 유형** | **세부 정보**
--- | ---
![Healthy][green] | 프로세스 서버가 연결되고 정상입니다.
![Warning][yellow] | 지난 15분 동안 CPU 사용률이 80% >
![Warning][yellow] | 지난 15분 동안 메모리 사용량> 80%
![Warning][yellow] | 지난 15분 동안 캐시 폴더 여유 공간 < 30%
![Warning][yellow] | 사이트 복구는 5분마다 보류 중인/나가는 데이터를 모니터링하고 프로세스 서버 캐시의 데이터를 30분 이내에 Azure에 업로드할 수 없을 것으로 예상합니다.
![Warning][yellow] | 프로세스 서버 서비스가 지난 15분 동안 실행되지 않습니다.
![위험][red] | 지난 15분 동안 CPU 사용률이 95% >
![위험][red] | 지난 15분 동안 메모리 사용량> 95%
![위험][red] | 지난 15분 동안 캐시 폴더 여유 공간 < 25%
![위험][red] | 사이트 복구는 5분마다 보류 중인/나가는 데이터를 모니터링하고 프로세스 서버 캐시의 데이터를 45분 이내에 Azure에 업로드할 수 없을 것으로 예상합니다.
![위험][red] | 15분 동안 프로세스 서버에서 하트비트가 없습니다.

![테이블 키](./media/vmware-physical-azure-monitor-process-server/table-key.png)

> [!NOTE]
> 프로세스 서버의 전반적인 상태는 생성된 최악의 경고를 기반으로 합니다.



## <a name="monitor-process-server-health"></a>프로세스 서버 상태 모니터링

다음과 같이 프로세스 서버의 상태를 모니터링할 수 있습니다. 

1. 복제된 컴퓨터와 프로세스 서버의 복제 상태와 상태를 모니터링하려면 **복제된 항목을**> 복제된 항목을 > 모니터링할 컴퓨터를 클릭합니다.
2. **복제 상태에서**VM 상태를 모니터링할 수 있습니다. 상태를 클릭하여 드릴다운하여 오류 세부 정보를 확인합니다.

    ![VM 대시보드에서 서버 상태 처리](./media/vmware-physical-azure-monitor-process-server/vm-ps-health.png)

4. **프로세스 서버 상태에서**프로세스 서버의 상태를 모니터링할 수 있습니다. 자세한 내용은 드릴다운합니다.

    ![VM 대시보드에서 서버 세부 정보 처리](./media/vmware-physical-azure-monitor-process-server/ps-summary.png)

5. VM 페이지의 그래픽 표현을 사용하여 상태를 모니터링할 수도 있습니다.
    - 관련된 경고가 있는 경우 확장 프로세스 서버가 주황색으로 표시되고 중요한 문제가 있는 경우 빨간색으로 표시됩니다. 
    - 프로세스 서버가 구성 서버의 기본 배포에서 실행 중인 경우 구성 서버가 그에 따라 강조 표시됩니다.
    - 드릴다운하려면 구성 서버 또는 프로세스 서버를 클릭합니다. 문제 및 수정 권장 사항을 기록합니다.

**사이트 복구 인프라에서**볼트의 프로세스 서버를 모니터링할 수도 있습니다. **사이트 복구 인프라 관리에서**구성 **서버를 클릭합니다.** 프로세스 서버와 연결된 구성 서버를 선택하고 프로세스 서버 세부 정보로 드릴다운합니다.


## <a name="next-steps"></a>다음 단계

- 프로세스 서버 문제가 있는 경우 [문제 해결 지침을](vmware-physical-azure-troubleshoot-process-server.md) 따르십시오.
- 도움이 필요한 경우 [Azure Site Recovery 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)에 질문을 게시하세요. 

[green]: ./media/vmware-physical-azure-monitor-process-server/green.png
[yellow]: ./media/vmware-physical-azure-monitor-process-server/yellow.png
[red]: ./media/vmware-physical-azure-monitor-process-server/red.png
