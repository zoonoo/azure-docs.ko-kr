---
title: Azure 사이트 복구를 통해 VMware/물리적 재해 복구 확장
description: Azure 사이트 복구를 사용하여 많은 수의 온-프레미스 VM 또는 물리적 서버에 대해 재해 복구를 Azure로 설정하는 방법을 알아봅니다.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: a3a2317554f02dc1f1198d8019bbfdb50e3cc71c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81409776"
---
# <a name="set-up-disaster-recovery-at-scale-for-vmware-vmsphysical-servers"></a>VMware VM/물리적 서버에 대한 대규모 재해 복구 설정

이 문서에서는 Azure 사이트 복구 서비스를 사용하여 온-프레미스 VM웨어 VM 또는 프로덕션 환경의 물리적 서버의 많은 수(> 1000)에 대해 재해 [복구를 Azure에](site-recovery-overview.md) 설정하는 방법을 설명합니다.


## <a name="define-your-bcdr-strategy"></a>BCDR 전략 정의

비즈니스 연속성 및 재해 복구(BCDR) 전략의 일환으로 비즈니스 앱 및 워크로드에 대한 RPO(복구 지점 목표) 및 복구 시간 목표(RPO)를 정의합니다. RTO는 연속성 문제를 방지하기 위해 비즈니스 앱 또는 프로세스를 복원하고 사용할 수 있어야 하는 기간 및 서비스 수준을 측정합니다.
- 사이트 복구는 VMware VM 및 물리적 서버에 대한 지속적인 복제와 RTO용 [SLA를](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/) 제공합니다.
- VMware VM에 대한 대규모 재해 복구를 계획하고 필요한 Azure 리소스를 파악할 때 용량 계산에 사용할 RTO 값을 지정할 수 있습니다.


## <a name="best-practices"></a>모범 사례

대규모 재해 복구에 대한 몇 가지 일반적인 모범 사례입니다. 이러한 모범 사례는 문서의 다음 섹션에서 자세히 설명합니다.

- **대상 요구 사항 식별**: 재해 복구를 설정하기 전에 Azure에서 용량 및 리소스 요구 사항을 예측합니다.
- **사이트 복구 구성 요소 계획**: 예상 용량을 충족하는 데 필요한 사이트 복구 구성 요소(구성 서버, 프로세스 서버)를 파악합니다.
- **하나 이상의 확장 프로세스 서버 설정**: 구성 서버에서 기본적으로 실행 중인 프로세스 서버를 사용하지 마십시오. 
- **최신 업데이트 실행**: 사이트 복구 팀은 정기적으로 새 버전의 사이트 복구 구성 요소를 릴리스하므로 최신 버전을 실행하고 있는지 확인해야 합니다. 이를 위해 [업데이트의 새로운 기능을](site-recovery-whats-new.md) 추적하고 업데이트가 릴리스될 때 [업데이트를 활성화 및 설치합니다.](service-updates-how-to.md)
- **사전 예방적 모니터링**: 재해 복구를 시작하고 실행하면 복제된 컴퓨터및 인프라 리소스의 상태와 상태를 사전에 모니터링해야 합니다.
- **재해 복구 훈련**: 재해 복구 훈련을 정기적으로 실행해야 합니다. 이러한 작업은 프로덕션 환경에 영향을 미치지 않지만 필요할 때 Azure에 대한 장애 조치(failover)가 예상대로 작동하는지 확인하는 데 도움이 됩니다.



## <a name="gather-capacity-planning-information"></a>용량 계획 정보 수집

대상(Azure) 용량 요구 사항을 평가하고 추정하는 데 도움이 되는 온-프레미스 환경에 대한 정보를 수집합니다.
- VMware의 경우 VMware VM에 대한 배포 플래너를 실행하여 이 작업을 수행합니다.
- 실제 서버의 경우 정보를 수동으로 수집합니다.

### <a name="run-the-deployment-planner-for-vmware-vms"></a>VMware VM에 대한 배포 플래너 실행

배포 플래너를 사용하면 VMware 온-프레미스 환경에 대한 정보를 수집할 수 있습니다.

- VM의 일반적인 변동을 나타내는 기간 동안 배포 플래너를 실행합니다. 이렇게 하면 보다 정확한 예측 및 권장 사항이 생성됩니다.
- 계획자는 실행 중인 서버의 처리량을 계산하므로 구성 서버 컴퓨터에서 배포 플래너를 실행하는 것이 좋습니다. 처리량 측정에 대해 [자세히 알아보세요.](site-recovery-vmware-deployment-planner-run.md#get-throughput)
- 아직 구성 서버가 설정되지 않은 경우:
    - 사이트 복구 구성 요소에 대한 [개요를 가져옵니다.](vmware-physical-azure-config-process-server-overview.md)
    - [구성 서버를 설정하여](vmware-azure-deploy-configuration-server.md)배포 플래너를 실행합니다.

그런 다음 다음과 같이 플래너를 실행합니다.

1. 배포 플래너에 [대해 자세히 알아보세요.](site-recovery-deployment-planner.md) 포털에서 최신 버전을 다운로드하거나 [직접 다운로드할](https://aka.ms/asr-deployment-planner)수 있습니다.
2. 배포 플래너의 [필수 구성 조건](site-recovery-deployment-planner.md#prerequisites) 및 최신 [업데이트를](site-recovery-deployment-planner-history.md) 검토하고 도구를 [다운로드하여 추출합니다.](site-recovery-deployment-planner.md#download-and-extract-the-deployment-planner-tool)
3. 구성 서버에서 [배포 플래너를 실행합니다.](site-recovery-vmware-deployment-planner-run.md)
4. 예상 및 권장 사항을 요약하는 [보고서를 생성합니다.](site-recovery-vmware-deployment-planner-run.md#generate-report)
5. 보고서 [권장 사항](site-recovery-vmware-deployment-planner-analyze-report.md) 및 [비용 추정을 분석합니다.](site-recovery-vmware-deployment-planner-cost-estimation.md)

>[!NOTE]
> 기본적으로 이 도구는 프로파일을 구성하여 최대 1000개의 VM에 대한 보고서를 생성합니다. ASRDeployPlanner.exe.config 파일에서 MaxVMs지원 키 값을 늘려 이 제한을 변경할 수 있습니다.

## <a name="plan-target-azure-requirements-and-capacity"></a>계획 대상(Azure) 요구 사항 및 용량

수집된 예측 및 권장 사항을 사용하여 대상 리소스 및 용량을 계획할 수 있습니다. VMware VM에 대한 배포 플래너를 실행한 경우 여러 [보고서 권장 사항을](site-recovery-vmware-deployment-planner-analyze-report.md#recommendations) 사용하여 도움을 줄 수 있습니다.

- **호환 되는 VM**: 이 번호를 사용하여 Azure에 대한 재해 복구준비가 된 VM 수를 식별합니다. 네트워크 대역폭 및 Azure 코어에 대한 권장 사항은 이 숫자를 기반으로 합니다.
- **필수 네트워크 대역폭**: 호환되는 VM의 델타 복제에 필요한 대역폭을 기록합니다. 
    - 플래너를 실행할 때 원하는 RPO를 분 으로 지정합니다. 권장 사항은 RPO 100% 및 90%를 충족하는 데 필요한 대역폭을 보여 준다. 
    - 네트워크 대역폭 권장 사항은 Planner에서 권장하는 구성 서버 및 프로세스 서버의 총 수에 필요한 대역폭을 고려합니다.
- **필수 Azure 코어:** 호환되는 VM 수에 따라 대상 Azure 리전에서 필요한 코어 수를 기록합니다. 코어가 충분하지 않으면 장애 조치 사이트 복구시 필요한 Azure VM을 만들 수 없습니다.
- **권장 VM 일괄 처리 크기:** 권장되는 일괄 처리 크기는 기본적으로 72시간 이내에 일괄 처리에 대한 초기 복제를 완료하는 동안 RPO를 100%로 충족하는 기능을 기반으로 합니다. 시간 값을 수정할 수 있습니다.

이러한 권장 사항을 사용하여 Azure 리소스, 네트워크 대역폭 및 VM 일괄 처리를 계획할 수 있습니다.

## <a name="plan-azure-subscriptions-and-quotas"></a>Azure 구독 및 할당량 계획

대상 구독에서 사용 가능한 할당량이 장애 조치(failover)를 처리하기에 충분한지 확인하려고 합니다.

**Task** | **세부 정보** | **작업**
--- | --- | ---
**코어 확인** | 사용 가능한 할당량의 코어가 장애 조치(failover) 시점의 총 목표 수와 같거나 초과하지 않으면 장애 조치(failover)가 실패합니다. | VMware VM의 경우 대상 구독에 배포 플래너 코어 권장 사항을 충족하기에 충분한 코어가 있는지 확인합니다.<br/><br/> 실제 서버의 경우 Azure 코어가 수동 추정과 충족하는지 확인합니다.<br/><br/> 할당량을 확인하려면 Azure 포털 > **구독에서** **사용량 + 할당량을 클릭합니다.**<br/><br/> 할당량 증가에 대해 [자세히 알아보세요.](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request)
**장애 조치 제한 확인** | 장애 조치 수는 사이트 복구 장애 조치 제한을 초과해서는 안 됩니다. |  장애 조치(failover)가 제한을 초과하는 경우 구독을 추가하고 여러 구독으로 장애 조치하거나 구독에 대한 할당량을 늘릴 수 있습니다. 


### <a name="failover-limits"></a>장애 조치 제한

제한은 컴퓨터당 3개의 디스크를 가정하여 1시간 이내에 사이트 복구에서 지원하는 장애 조치 수를 나타냅니다.

준수는 무엇을 의미합니까? Azure VM을 시작하려면 Azure에서 일부 드라이버를 부팅 시작 상태에 있어야 하며 DHCP와 같은 서비스가 자동으로 시작하도록 설정해야 합니다.
- 규정을 준수하는 컴퓨터에는 이미 이러한 설정이 있습니다.
- Windows를 실행하는 컴퓨터의 경우 규정 준수를 사전에 확인하고 필요한 경우 규정을 준수할 수 있습니다. [자세히 알아보기](site-recovery-failover-to-azure-troubleshoot.md#failover-failed-with-error-id-170010).
- Linux 컴퓨터는 장애 조치(failover) 시점에만 규정 이 준수됩니다.

**컴퓨터가 Azure를 준수합니까?** | **Azure VM 제한(관리되는 디스크 장애 조치)**
--- | --- 
예 | 2000
예 | 1000

- 제한은 구독의 대상 영역에서 최소한의 다른 작업이 진행 중이라고 가정합니다.
- 일부 Azure 지역은 더 작으며 제한이 약간 낮을 수 있습니다.

## <a name="plan-infrastructure-and-vm-connectivity"></a>인프라 및 VM 연결 계획

Azure로 장애 조치(failover) 후에는 온-프레미스에서 작업한 것처럼 워크로드를 작동하고 사용자가 Azure VM에서 실행되는 워크로드에 액세스할 수 있도록 해야 합니다.

- Active Directory 또는 DNS 온-프레미스 인프라에서 Azure에 실패하는 방법에 대해 [자세히 알아봅니다.](site-recovery-active-directory.md#test-failover-considerations)
- 장애 조치 후 Azure VM에 연결할 준비에 대해 [자세히 알아봅니다.](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)



## <a name="plan-for-source-capacity-and-requirements"></a>소스 용량 및 요구 사항 계획

용량 요구 사항을 충족하기 에 충분한 구성 서버와 확장 프로세스 서버가 있어야 합니다. 대규모 배포를 시작할 때 단일 구성 서버와 단일 확장 프로세스 서버로 시작합니다. 규정된 한도에 도달하면 서버를 추가합니다.

>[!NOTE]
> VMware VM의 경우 배포 플래너는 필요한 구성 및 프로세스 서버에 대한 몇 가지 권장 사항을 제시합니다. 배포 계획서 권장 사항을 따르는 대신 다음 절차에 포함된 테이블을 사용하는 것이 좋습니다. 


## <a name="set-up-a-configuration-server"></a>구성 서버 설정
 
구성 서버 용량은 데이터 이탈률이 아니라 복제하는 컴퓨터 수의 영향을 받습니다. 추가 구성 서버가 필요한지 여부를 알아내려면 이러한 정의된 VM 제한을 사용합니다.

**Cpu** | **메모리** | **캐시 디스크** | **복제된 컴퓨터 제한**
 --- | --- | --- | ---
vCPU 8대<br> 2 소켓 * 4 코어 @ 2.5 Ghz | 16GB | 600GB | 최대 550대의 기계<br> 각 컴퓨터에 각각 100GB의 세 개의 디스크가 있다고 가정합니다.

- 이러한 제한은 OVF 템플릿을 사용하여 설정된 구성 서버를 기반으로 합니다.
- 제한은 구성 서버에서 기본적으로 실행 중인 프로세스 서버를 사용하지 않는 것으로 가정합니다.

새 구성 서버를 추가해야 하는 경우 다음 지침을 따르십시오.

- OVF 템플릿을 사용하여 VMware VM 재해 복구를 위한 [구성 서버를 설정합니다.](vmware-azure-deploy-configuration-server.md)
- 실제 서버 또는 OVF 템플릿을 사용할 수 없는 VMware 배포에 대해 구성 [서버를](physical-azure-set-up-source.md) 수동으로 설정합니다.

구성 서버를 설정할 때 다음 을 확인합니다.

- 구성 서버를 설정할 때는 설정 후 변경해서는 안 되므로 구성 서버가 있는 구독 및 자격 증명 모음을 고려하는 것이 중요합니다. 볼트를 변경해야 하는 경우 구성 서버를 볼트에서 분리하고 다시 등록해야 합니다. 이렇게 하면 볼트에서 VM복제가 중지됩니다.
- 여러 네트워크 어댑터가 있는 구성 서버를 설정하려면 설정하는 동안 이 작업을 수행해야 합니다. 볼트에 구성 서버를 등록한 후에는 이 작업을 수행할 수 없습니다.

## <a name="set-up-a-process-server"></a>프로세스 서버 설정

프로세스 서버 용량은 복제에 사용하도록 설정된 컴퓨터 수가 아니라 데이터 이탈률의 영향을 받습니다.

- 대규모 배포의 경우 항상 하나 이상의 확장 프로세스 서버가 있어야 합니다.
- 추가 서버가 필요한지 여부를 알아내려면 다음 표를 사용하십시오.
- 사양이 가장 높은 서버를 추가하는 것이 좋습니다. 


**Cpu** | **메모리** | **캐시 디스크** | **이탈률**
 --- | --- | --- | --- 
vCPU 12대<br> 2 개의 소켓 * 6 코어 @ 2.5 Ghz | 24GB | 1 GB | 하루 최대 2TB

프로세스 서버를 다음과 같이 설정합니다.

1. [필수 구성 요소](vmware-azure-set-up-process-server-scale.md#prerequisites)를 검토하세요.
2. [포털](vmware-azure-set-up-process-server-scale.md#install-from-the-ui)또는 [명령줄에](vmware-azure-set-up-process-server-scale.md#install-from-the-command-line)서버를 설치합니다.
3. 새 서버를 사용하도록 복제된 컴퓨터를 구성합니다. 이미 복제하는 컴퓨터가 있는 경우:
    - 전체 프로세스 서버 워크로드를 새 프로세스 서버로 [이동할](vmware-azure-manage-process-server.md#switch-an-entire-workload-to-another-process-server) 수 있습니다.
    - 또는 특정 VM을 새 프로세스 서버로 [이동할](vmware-azure-manage-process-server.md#move-vms-to-balance-the-process-server-load) 수 있습니다.



## <a name="enable-large-scale-replication"></a>대규모 복제 사용

용량을 계획하고 필요한 구성 요소 및 인프라를 배포한 후 많은 수의 VM에 대해 복제를 사용하도록 설정합니다.

1. 기계를 일괄 처리로 정렬합니다. 일괄 처리 내에서 VM에 대한 복제를 사용하도록 설정한 다음 다음 일괄 처리로 이동합니다.

    - VMware VM의 경우 배포 계획기 보고서에서 [권장되는 VM 일괄 처리 크기를](site-recovery-vmware-deployment-planner-analyze-report.md#recommended-vm-batch-size-for-initial-replication) 사용할 수 있습니다.
    - 실제 컴퓨터의 경우 크기와 양의 데이터가 있는 컴퓨터와 사용 가능한 네트워크 처리량을 기준으로 일괄 처리를 식별하는 것이 좋습니다. 목표는 동일한 시간 내에 초기 복제를 완료할 가능성이 있는 컴퓨터를 일괄 처리하는 것입니다.
    
2. 컴퓨터에 대한 디스크 변동이 높거나 Deploy thePlanner에서 제한을 초과하는 경우 복제할 필요가 없는 중요하지 않은 파일(예: 로그 덤프 또는 임시 파일)을 컴퓨터 간에 이동할 수 있습니다. VMware VM의 경우 이러한 파일을 별도의 디스크로 이동한 다음 [해당 디스크를](vmware-azure-exclude-disk.md) 복제에서 제외할 수 있습니다.
3. 복제를 사용하도록 설정하기 전에 컴퓨터가 [복제 요구 사항을](vmware-physical-azure-support-matrix.md#replicated-machines)충족하는지 확인합니다.
4. [VMware VM](vmware-azure-set-up-replication.md#create-a-policy) 또는 물리적 서버에 대한 복제 정책을 [구성합니다.](physical-azure-disaster-recovery.md#create-a-replication-policy)
5. [VMware VM](vmware-azure-enable-replication.md) 또는 물리적 서버에 대한 [복제를 활성화합니다.](physical-azure-disaster-recovery.md#enable-replication) 이렇게 하면 선택한 컴퓨터에 대한 초기 복제가 시작됩니다.

## <a name="monitor-your-deployment"></a>배포 모니터링

VM의 첫 번째 일괄 처리에 대한 복제를 시작한 후 다음과 같이 배포 모니터링을 시작합니다.  

1. 재해 복구 관리자를 할당하여 복제된 컴퓨터의 상태를 모니터링합니다.
2. 복제된 항목 및 인프라에 대한 [이벤트를 모니터링합니다.](site-recovery-monitor-and-troubleshoot.md)
3. 스케일 아웃 프로세스 서버의 [상태를 모니터링합니다.](vmware-physical-azure-monitor-process-server.md)
4. 더 쉽게 모니터링할 수 있도록 이벤트에 대한 [이메일 알림을](https://docs.microsoft.com/azure/site-recovery/site-recovery-monitor-and-troubleshoot#subscribe-to-email-notifications) 받으려면 등록하십시오.
5. 모든 [것이](site-recovery-test-failover-to-azure.md)예상대로 작동하는지 확인하기 위해 정기적 인 재해 복구 훈련을 수행하십시오.


## <a name="plan-for-large-scale-failovers"></a>대규모 장애 조치 계획

재해가 발생하면 많은 수의 컴퓨터/워크로드를 Azure에 장애 조치해야 할 수 있습니다. 다음과 같이 이러한 유형의 이벤트를 준비합니다.

장애 조치(failover)에 대해 미리 준비할 수 있습니다.

- 장애 조치 후 워크로드를 사용할 수 있고 사용자가 Azure VM에 액세스할 수 있도록 [인프라 및 VM을 준비합니다.](#plan-infrastructure-and-vm-connectivity)
- 이 문서의 앞에서 [장애 조치 한도를](#failover-limits) 기록했습니다. 장애 조치(failover)가 이러한 한도 내에 있는지 확인합니다.
- 정기적인 [재해 복구 훈련을 실행합니다.](site-recovery-test-failover-to-azure.md) 드릴은 다음을 수행합니다.
    - 장애 조치(failover) 전에 배포에서 격차를 찾습니다.
    - 앱의 종단 간 RTO를 예측합니다.
    - 워크로드에 대한 종단 간 RPO를 예측합니다.
    - IP 주소 범위 충돌을 식별합니다.
    - 드릴을 실행할 때 드릴에는 프로덕션 네트워크를 사용하지 말고, 프로덕션 및 테스트 네트워크에서 동일한 서브넷 이름을 사용하지 말고, 드릴마다 테스트 장애 조치(failover)를 정리하는 것이 좋습니다.

대규모 장애 조치(failover)를 실행하려면 다음을 권장합니다.

1. 워크로드 장애 조치(failover)에 대한 복구 계획을 만듭니다.
    - 각 복구 계획은 최대 100대의 장애 조치(failover)를 트리거할 수 있습니다.
    - [자세히 알아봅니다](recovery-plan-overview.md) .
2. Azure에서 수동 작업을 자동화하기 위해 복구 계획에 Azure 자동화 runbook 스크립트를 추가합니다. 일반적인 작업에는 로드 밸론더 구성, DNS 업데이트 등이 포함됩니다. [자세히 알아보기](site-recovery-runbook-automation.md)
2. 장애 조치 전에 Windows 컴퓨터가 Azure 환경을 준수할 수 있도록 준비합니다. [장애 조치 제한은](#plan-azure-subscriptions-and-quotas) 준수하는 시스템의 경우 더 높습니다. Runbook에 대해 [자세히 알아보세요.](site-recovery-failover-to-azure-troubleshoot.md#failover-failed-with-error-id-170010)
4.  [시작-AzRecoveryServicesAsr계획FailoverOverJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/start-azrecoveryservicesasrplannedfailoverjob?view=azps-2.0.0&viewFallbackFrom=azps-1.1.0) powerShell cmdlet을 사용 하 여 장애 조치 트리거 복구 계획 함께.



## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Site Recovery 모니터링](site-recovery-monitor-and-troubleshoot.md)
