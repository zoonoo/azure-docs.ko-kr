---
title: Azure Site Recovery 프로세스 서버 모니터링
description: 이 문서에서는 VMware VM/물리적 서버 재해 복구에 사용되는 Azure Site Recovery 프로세스 서버를 모니터링하는 방법을 설명합니다.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: 296254db83a065623b692d7947c130dfd71cd413
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2020
ms.locfileid: "83835056"
---
# <a name="monitor-the-process-server"></a>프로세스 서버 모니터링

이 문서에서는 [Site Recovery](site-recovery-overview.md) 프로세스 서버를 모니터링하는 방법을 설명합니다.

- 프로세스 서버는 Azure를 대상으로 온-프레미스 VMware VM 및 물리적 서버의 재해 복구를 설정할 때 사용됩니다.
- 기본적으로 프로세스 서버는 구성 서버에서 실행됩니다. 구성 서버를 배포할 때 기본적으로 설치됩니다.
- 필요에 따라 많은 수의 복제된 머신과 더 많은 볼륨의 복제 트래픽을 확장 및 처리하기 위해 추가 스케일 아웃 프로세스 서버를 배포할 수 있습니다.

프로세스 서버의 역할 및 배포에 대한 [자세한 내용을 알아보세요](vmware-physical-azure-config-process-server-overview.md).

## <a name="monitoring-overview"></a>모니터링 개요

프로세스 서버에는 특히 복제된 데이터 캐싱, 압축 및 Azure로 전송에서 많은 역할이 있으므로 지속적으로 프로세스 서버 상태를 모니터링하는 것이 중요합니다.

일반적으로 프로세스 서버 성능에 영향을 주는 여러 가지 상황이 있습니다. 성능에 영향을 미치는 문제는 VM 상태에도 영향이 파급되고, 결국 프로세스 서버와 복제된 머신을 모두 위험 상태로 몰아갑니다. 예를 들어 다음과 같은 경우입니다.

- 많은 수의 VM이 프로세스 서버를 사용하여 권장되는 제한에 도달하거나 초과하는 경우
- 프로세스 서버를 사용하는 VM의 변동률이 높은 경우
- VM과 프로세스 서버 간의 네트워크 처리량 만으로는 복제 데이터를 프로세스 서버로 업로드할 수 없는 경우
- 프로세스 서버와 Azure 간의 네트워크 처리량이 프로세스 서버에서 Azure로 복제 데이터를 업로드하는 데 충분하지 않은 경우

이러한 모든 문제는 VM의 RPO(복구 지점 목표)에 영향을 줄 수 있습니다. 

**그 이유는 무엇일까요?** VM에 대한 복구 지점을 생성하려면 VM의 모든 디스크에 공통 지점이 있어야 하기 때문입니다. 한 디스크의 변동률이 높거나, 복제가 느리거나, 프로세스 서버가 최적이 아닐 경우 이는 효율적으로 복구 지점이 생성되는 방식에 영향을 줍니다.

## <a name="monitor-proactively"></a>사전 모니터링

프로세스 서버와 관련된 문제를 방지하려면 다음을 수행하는 것이 중요합니다.

- [용량 및 크기 조정 지침](site-recovery-plan-capacity-vmware.md#capacity-considerations)을 사용하여 프로세스 서버에 대한 특정 요구 사항을 이해하고, 권장 사항에 따라 프로세스 서버가 배포 및 실행되고 있는지 확인합니다.
- 경고를 모니터링하고 문제가 발생할 때마다 해결하여 프로세스 서버를 계속 효율적으로 실행합니다.


## <a name="process-server-alerts"></a>프로세스 서버 경고

프로세스 서버는 다음 표에 요약된 다양한 상태 경고를 생성합니다.

**경고 유형** | **세부 정보**
--- | ---
![Healthy][green] | 프로세스 서버가 연결되어 있고 정상 상태입니다.
![Warning][yellow] | 지난 15분 동안 CPU 사용률이 80%를 초과합니다.
![Warning][yellow] | 지난 15분 동안 메모리 사용량이 80%를 초과합니다.
![Warning][yellow] | 지난 15분 동안 캐시 폴더의 사용 가능한 공간이 30% 미만입니다.
![Warning][yellow] | Site Recovery가 5분마다 보류/발신 데이터를 모니터링하고, 프로세스 서버 캐시 내 데이터를 30분 이내에 Azure에 업로드할 수 없는 것으로 추정합니다.
![Warning][yellow] | 지난 15분 동안 프로세스 서버 서비스가 실행되지 않고 있습니다.
![위험][red] | 지난 15분 동안 CPU 사용률이 95%를 초과합니다.
![위험][red] | 지난 15분 동안 메모리 사용량이 95%를 초과합니다.
![위험][red] | 지난 15분 동안 캐시 폴더의 사용 가능한 공간이 25% 미만입니다.
![위험][red] | Site Recovery가 5분마다 보류/발신 데이터를 모니터링하고, 프로세스 서버 캐시 내 데이터를 45분 이내에 Azure에 업로드할 수 없는 것으로 추정합니다.
![위험][red] | 15분 동안 프로세스 서버에서 하트비트가 없습니다.

![테이블 키](./media/vmware-physical-azure-monitor-process-server/table-key.png)

> [!NOTE]
> 프로세스 서버의 전반적인 상태는 생성된 최악의 경고를 기반으로 합니다.



## <a name="monitor-process-server-health"></a>프로세스 서버 상태 모니터링

다음과 같이 프로세스 서버의 상태를 모니터링할 수 있습니다. 

1. 복제된 머신 및 해당 프로세스 서버의 복제 상태 및 상태를 모니터링하려면 자격 증명 모음 > **복제된 항목**에서 모니터링하려는 머신을 클릭합니다.
2. **복제 상태**에서 VM 상태를 모니터링할 수 있습니다. 상태를 클릭하여 오류 정보를 드릴다운합니다.

    ![VM 대시보드의 프로세스 서버 상태](./media/vmware-physical-azure-monitor-process-server/vm-ps-health.png)

4. **프로세스 서버 상태**에서 프로세스 서버의 상태를 모니터링할 수 있습니다. 세부 정보를 드릴다운합니다.

    ![VM 대시보드의 프로세스 서버 세부 정보](./media/vmware-physical-azure-monitor-process-server/ps-summary.png)

5. VM 페이지의 그래픽 표현을 사용하여 상태를 모니터링할 수도 있습니다.
    - 스케일 아웃 프로세스 서버는 관련된 경고가 있는 경우 주황색으로 강조 표시되고 중요한 문제가 있는 경우 빨간색으로 강조 표시됩니다. 
    - 프로세스 서버가 구성 서버의 기본 배포에서 실행되는 경우 구성 서버가 그에 따라 강조 표시됩니다.
    - 드릴다운하려면 구성 서버 또는 프로세스 서버를 클릭합니다. 모든 문제 및 수정 권장 사항을 기록해 둡니다.

**Site Recovery 인프라**의 자격 증명 모음에서 프로세스 서버를 모니터링할 수도 있습니다. **Site Recovery 인프라 관리**에서 **구성 서버**를 클릭합니다. 프로세스 서버와 연결된 구성 서버를 선택하고 프로세스 서버 세부 정보로 드릴다운합니다.


## <a name="next-steps"></a>다음 단계

- 프로세스 서버 문제가 있는 경우 [문제 해결 지침](vmware-physical-azure-troubleshoot-process-server.md)을 따르세요.
- 도움이 필요한 경우 [Azure Site Recovery의 Microsoft Q&A 질문 페이지](https://docs.microsoft.com/answers/topics/azure-site-recovery.html)에 질문을 게시하세요. 

[green]: ./media/vmware-physical-azure-monitor-process-server/green.png
[yellow]: ./media/vmware-physical-azure-monitor-process-server/yellow.png
[red]: ./media/vmware-physical-azure-monitor-process-server/red.png
