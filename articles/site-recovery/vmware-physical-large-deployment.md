---
title: Azure Site Recovery를 사용 하 여 많은 수의 VMware Vm 또는 물리적 서버에 대 한 Azure로 재해 복구 설정 | Microsoft Docs
description: Azure Site Recovery를 사용 하 여 많은 수의 온-프레미스 VMware Vm 또는 물리적 서버에 대 한 Azure로 재해 복구를 설정 하는 방법에 알아봅니다.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: raynew
ms.openlocfilehash: e96aafe61c0d8547ffca9e97bfd9e90c9529155f
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66237274"
---
# <a name="set-up-disaster-recovery-at-scale-for-vmware-vmsphysical-servers"></a>VMware v m/물리적 서버에 대해 대규모로 재해 복구 설정

이 문서에서는 온-프레미스 VMware Vm 또는 물리적 서버의 프로덕션 환경에서 많은 수 (> 1000)에 대 한 Azure로 재해 복구를 설정 하는 방법을 설명를 사용 하는 [Azure Site Recovery](site-recovery-overview.md) 서비스입니다.


## <a name="define-your-bcdr-strategy"></a>BCDR 전략 정의

비즈니스 연속성 및 재해 복구 (BCDR) 전략의 일환으로, 복구 지점 목표 (Rpo) 및 비즈니스 앱 및 워크 로드에 대 한 복구 시간 목표 (Rto)를 정의합니다. 시간 및 서비스 기간을 측정 하는 RTO는 수준 비즈니스 응용 프로그램 또는 프로세스 여야 합니다 복원 및 연속성 문제를 방지 하기 위해 사용할 수 있습니다.
- Site Recovery는 VMware Vm 및 물리적 서버에 대 한 연속 복제를 제공 및 [SLA](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/) RTO에 대 한 합니다.
- VMware Vm 및 필요한 Azure 리소스를 확인 하는 것에 대 한 대규모 재해 복구를 계획할 때에 용량 계산에 사용 됩니다 하는 RTO 값을 지정할 수 있습니다.


## <a name="best-practices"></a>모범 사례

대규모 재해 복구를 위한 몇 가지 일반적인 모범 사례입니다. 이러한 모범 사례 문서의 다음 섹션에서 자세히 설명 합니다.

- **대상 요구 사항 식별**: 재해 복구를 설정 하기 전에 Azure에서 용량 및 리소스 요구를 예측.
- **Site Recovery 구성 요소에 대 한 계획**: 예상된 용량을 충족 해야 하는 Site Recovery 구성 요소 (구성 서버, 프로세스 서버)를 파악 합니다.
- **하나 이상의 스케일 아웃 프로세스 서버 설정**: 기본적으로 구성 서버에서 실행 되는 프로세스 서버를 사용 하지 마세요. 
- **최신 업데이트를 실행**: Site Recovery 팀은 새 버전의 Site Recovery 구성 요소는 정기적으로 해제 하 고 확인 해야 최신 버전을 실행 하는 키를 누릅니다. 추적 된 데 [새로운](site-recovery-whats-new.md) 업데이트 및 [사용 및 업데이트 설치](service-updates-how-to.md) 출시 된.
- **사전에 모니터링**: 익숙해지면 재해 복구를 실행 중인, 복제 된 컴퓨터 및 인프라 리소스의 상태를 적극적으로 모니터링 해야 합니다.
- **재해 복구 훈련**: 정기적으로 재해 복구 훈련을 실행할 해야 있습니다. 이러한 프로덕션 환경에 영향을 주지 아니지만 수행할 수 있도록 Azure에 장애 조치가 수행 되는 필요할 때 예상 대로 작동 합니다.



## <a name="gather-capacity-planning-information"></a>용량 계획 정보를 수집 합니다.

온-프레미스 환경 평가 하 고 대상 (Azure) 용량 요구를 예측 하는 데에 대 한 정보를 수집 합니다.
- VMware에 대 한 VMware Vm이이 작업을 수행 하기 위한 Deployment Planner를 실행 합니다.
- 물리적 서버에 대 한 정보를 수동으로 수집 합니다.

### <a name="run-the-deployment-planner-for-vmware-vms"></a>VMware Vm에 대 한 Deployment Planner 실행

Deployment Planner를 사용 하면 온-프레미스 VMware 환경에 대 한 정보를 수집 하도록 도와줍니다.

- Vm에 대 한 일반적인 변동을 나타내는 기간 동안 Deployment Planner를 실행 합니다. 이렇게 하면 보다 정확한 예측 및 권장 사항 생성 됩니다.
- Planner를 실행 하는 서버에서 처리량을 계산 하므로 구성 서버 컴퓨터에서 Deployment Planner 실행 하는 것이 좋습니다. [자세한](site-recovery-vmware-deployment-planner-run.md#get-throughput) 처리량을 측정 하는 방법에 대 한 합니다.
- 구성 서버를 설치 합니다 아직 없는 경우:
    - [개요](vmware-physical-azure-config-process-server-overview.md) Site Recovery 구성 요소입니다.
    - [구성 서버 설정](vmware-azure-deploy-configuration-server.md)에서 Deployment Planner를 실행 하기 위해.

그런 다음 같이 Planner를 실행 합니다.

1. [에 대 한 자세한](site-recovery-deployment-planner.md) Deployment Planner 합니다. 포털에서 최신 버전을 다운로드할 수 있습니다 또는 [직접 다운로드](https://aka.ms/asr-deployment-planner)합니다.
2. 검토 합니다 [필수 구성 요소](site-recovery-deployment-planner.md#prerequisites) 하 고 [최신 업데이트](site-recovery-deployment-planner-history.md) Deployment Planner에 대 한 및 [다운로드 및 추출](site-recovery-deployment-planner.md#download-and-extract-the-deployment-planner-tool) 도구입니다.
3. [Deployment Planner 실행](site-recovery-vmware-deployment-planner-run.md) 구성 서버에서.
4. [보고서를 생성할](site-recovery-vmware-deployment-planner-run.md#generate-report) 예측 및 권장 사항을 요약 합니다.
5. 분석을 [권장 사항 보고서](site-recovery-vmware-deployment-planner-analyze-report.md) 하 고 [추정 비용](site-recovery-vmware-deployment-planner-cost-estimation.md)합니다.

>[!NOTE]
> 기본적으로 도구 프로필을 구성 하 고 최대 1,000 개의 Vm에 대 한 보고서를 생성 합니다. ASRDeploymentPlanner.exe.config 파일에서 MaxVMsSupported 키 값을 늘려이 한도 변경할 수 있습니다.

## <a name="plan-target-azure-requirements-and-capacity"></a>대상 (Azure) 요구 사항 및 용량 계획

에 수집 된 예측 및 권장 사항을 사용 하 여, 대상 리소스 및 용량을 계획할 수 있습니다. VMware Vm에 대 한 Deployment Planner를 실행 하는 경우의 숫자를 사용할 수 있습니다 합니다 [권장 사항 보고서](site-recovery-vmware-deployment-planner-analyze-report.md#recommendations) 수 있도록 합니다.

- **호환 되는 Vm**: 이 번호를 사용 하 여 Azure로 재해 복구를 위해 준비 된 Vm의 수를 확인 합니다. 네트워크 대역폭 및 Azure 코어에 대 한 권장이이 번호를 기반으로 합니다.
- **필요한 네트워크 대역폭**: 호환 되는 Vm의 델타 복제에 필요한 대역폭을 참고 합니다. 
    - Planner를 실행 하는 경우 몇 분 안에 원하는 RPO를 지정 합니다. 권장 사항은 해당 RPO 시간 90% 및 100% 충족 하는 데 필요한 대역폭을 보여 줍니다. 
    - 네트워크 대역폭 권장 사항은 구성 서버 및 Planner에서 권장 하는 프로세스 서버의 총 수에 필요한 대역폭을 고려 합니다.
- **필요한 Azure 코어**: 대상 Azure 지역에서에서 필요한 코어 수에 따라 호환 되는 Vm의 수입니다. 충분 한 코어가 없다면 장애 조치 시 Site Recovery 없게 필요한 Azure Vm을 만듭니다.
- **권장 VM 일괄 처리 크기**: 권장 되는 일괄 처리 크기는 100%의 RPO를 충족 하는 동안 기본적으로 72 시간 내에서 일괄 처리에 대 한 초기 복제를 완료 하는 기능에 기반 합니다. 시간 값을 수정할 수 있습니다.

이러한 권장 사항은 Azure 리소스, 네트워크 대역폭 및 VM 일괄 처리에 대 한 계획을 사용할 수 있습니다.

## <a name="plan-azure-subscriptions-and-quotas"></a>Azure 구독 및 할당량을 계획

대상 구독에 사용할 수 있는 할당량 장애 조치를 처리 하기에 충분 한지 확인 하려고 합니다.

**Task** | **세부 정보** | **작업**
--- | --- | ---
**코어를 확인 합니다.** | 사용 가능한 할당량의 코어 같은 장애 조치 시 총 대상 수를 초과 하지 않습니다, 경우에 장애 조치 실패 합니다. | VMware Vm을 위한 Deployment Planner 핵심 권장 사항에 맞게 대상 구독에 충분 한 코어가 확인 합니다.<br/><br/> 물리적 서버에 대 한 Azure 코어에 수동 예상치를 충족 하는지 확인 합니다.<br/><br/> Azure portal에서 할당량을 확인 하려면 > **구독**, 클릭 **사용량 + 할당량**합니다.<br/><br/> [자세한](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) 할당량을 증가 하는 방법에 대 한 합니다.
**장애 조치 제한을 확인합니다** | 장애 조치 수가 Site Recovery 장애 조치 한도 초과 돼 있습니다. |  장애 조치 한도 초과 하는 경우 구독을 추가할 수 있습니다 및 여러 구독으로 장애 조치 또는 구독에 대 한 할당량을 늘려야 합니다. 


### <a name="failover-limits"></a>장애 조치 제한

제한을 컴퓨터당 3 개의 디스크를 가정 하면 1 시간 내에서 Site Recovery에서 지원 되는 장애 조치의 수를 나타냅니다.

항목은 평균을 준수 합니까? Azure VM을 시작 하려면 Azure 일부 드라이버를 부팅 시작 상태에 있이 필요 하 고 자동으로 시작 되도록 설정 하는 DHCP와 같은 서비스 합니다.
- 준수 하는 컴퓨터에에서는 이미 이러한 설정은 현재 위치입니다.
- Windows를 실행 하는 컴퓨터에 대 한 규정 준수를 확인 하 고 필요한 경우 준수 사전에 있습니다. [자세히 알아보기](site-recovery-failover-to-azure-troubleshoot.md#failover-failed-with-error-id-170010).
- Linux 컴퓨터만 장애 조치 시 준수로 옮겨진 스토리는 합니다.

**Azure를 사용 하 여 컴퓨터 준수?** | **Azure VM 제한 (관리 디스크 장애 조치)**
--- | --- 
예 | 2000
아닙니다. | 1000

- 제한 구독에 대 한 대상 지역에서 진행 중인 다른 작업에는 최소 가정 합니다.
- 일부 Azure 지역,을 약간 낮습니다 수도 있습니다.

## <a name="plan-infrastructure-and-vm-connectivity"></a>인프라 및 VM 연결 계획

Azure로 장애 조치 후 온-프레미스와 마찬가지로 작동 하 고 Azure Vm에서 실행 중인 워크 로드에 액세스할 수 있도록 워크 로드 해야 합니다.

- [자세한](site-recovery-active-directory.md#test-failover-considerations) 에 대 한 azure Active Directory 또는 DNS 온-프레미스 인프라를 통해 실패 합니다.
- [자세한](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover) 장애 조치 후 Azure Vm에 연결 하기 위해 준비 하는 방법에 대 한 합니다.



## <a name="plan-for-source-capacity-and-requirements"></a>원본 용량 및 요구 사항에 대 한 계획

충분 한 구성 서버 및 스케일 아웃 프로세스 서버 용량 요구 사항을 충족 하도록 반드시 합니다. 대규모 배포를 시작 하는 대로 단일 구성 서버 및 단일 스케일 아웃 프로세스 서버를 사용 하 여 시작 합니다. 지정 된 제한에 도달 하면 추가 서버를 추가 합니다.

>[!NOTE]
> VMware Vm, Deployment Planner에서 필요한 구성 및 프로세스 서버에 대 한 몇 가지 권장 사항 수행 합니다. Deployment Planner 권장 사항을 수행 하는 대신 다음 절차에서는 포함 된 테이블을 사용 하는 것이 좋습니다. 


## <a name="set-up-a-configuration-server"></a>구성 서버 설정
 
구성 서버 용량을 복제 하는 컴퓨터의 수 및 데이터가 아니라 변동률 합니다. 추가 구성 서버를 해야 하는지 여부 확인을 사용 하 여 VM 한도 정의 합니다.

**CPU** | **메모리** | **캐시 디스크** | **복제 컴퓨터 제한**
 --- | --- | --- | ---
vCPU 8대<br> 2 소켓 * 4 코어 @ 2.5ghz | 16GB | 600TB | 최대 550 컴퓨터<br> 각 컴퓨터에 각각 100GB의 디스크 3 이라고 가정 합니다.

- 이러한 한도 OVF 템플릿을 사용 하 여 설정 하는 구성 서버를 기반으로 합니다.
- 제한 구성 서버에서 기본적으로 실행 되는 프로세스 서버를 사용 하지 않는 것을 가정 합니다.

새 구성 서버를 추가 해야 할 경우 다음이 지침을 따릅니다.

- [구성 서버 설정](vmware-azure-deploy-configuration-server.md) VMware VM 재해 복구에 대 한 OVF 템플릿을 사용 합니다.
- [구성 서버 설정](physical-azure-set-up-source.md) OVF 템플릿을 사용할 수 없는 VMware 배포 또는 물리적 서버에 대해 수동으로.

구성 서버를 설정 하는 note:

- 구성 서버를 설정한 경우 구독 및 있는 상주 하므로 이러한 설치 후 변경 하지 않아야 하는 자격 증명 모음을 고려해 야 할 중요 한 것입니다. 자격 증명 모음을 변경할 필요가 있는 경우, 자격 증명 모음에서 구성 서버를 분리 하 고 다시 등록 해야 합니다. 이 자격 증명 모음에 Vm의 복제를 중지 합니다.
- 여러 네트워크 어댑터를 사용 하 여 구성 서버를 설정 하려는 경우 것이 설정 하는 동안 합니다. 자격 증명 모음에 구성 서버를 등록 한 후이 수행할 수 없습니다.

## <a name="set-up-a-process-server"></a>프로세스 서버 설정

복제용으로 설정 된 컴퓨터 수가 아니라에 의해 데이터 변동 속도, 프로세스 서버 용량을 사용 하는 영향을 받습니다.

- 대규모 배포에 대 한 하나 이상의 스케일 아웃 프로세스 서버를 항상 있어야 할 합니다.
- 서버를 추가 해야 하는지 여부를 파악, 다음 표를 사용 합니다.
- 가장 높은 사양 사용 하 여 서버를 추가 하는 것이 좋습니다. 


**CPU** | **메모리** | **캐시 디스크** | **변동률**
 --- | --- | --- | --- 
vCPU 12대<br> 2 소켓 * 6 코어 @ 2.5ghz | 24GB | 1 GB | 하루 최대 2TB

프로세스 서버를 다음과 같이 설정 합니다.

1. [필수 구성 요소](vmware-azure-set-up-process-server-scale.md#prerequisites)를 검토하세요.
2. 에 서버를 설치 합니다 [포털](vmware-azure-set-up-process-server-scale.md#install-from-the-ui), 또는 합니다 [명령줄](vmware-azure-set-up-process-server-scale.md#install-from-the-command-line)합니다.
3. 새 서버를 사용 하려면 복제 된 컴퓨터를 구성 합니다. 이미 있는 경우 복제 하는 컴퓨터:
    - 할 수 있습니다 [이동](vmware-azure-manage-process-server.md#switch-an-entire-workload-to-another-process-server) 새 프로세스 서버로 전체 프로세스 서버 워크 로드.
    - 또는 수 있습니다 [이동](vmware-azure-manage-process-server.md#move-vms-to-balance-the-process-server-load) 새 프로세스 서버로 특정 Vm.



## <a name="enable-large-scale-replication"></a>대규모 복제를 사용 하도록 설정

용량을 계획 하 고 필수 구성 요소 및 인프라를 배포 후 많은 수의 Vm에 대 한 복제를 사용 하도록 설정 합니다.

1. 일괄 처리로 컴퓨터를 정렬 합니다. 일괄 처리 내에서 Vm에 대 한 복제를 활성화 하 고 일괄 처리로 넘어갑니다.

    - VMware Vm에 대해 사용할 수 있습니다 합니다 [권장 VM 일괄 처리 크기](site-recovery-vmware-deployment-planner-analyze-report.md#recommended-vm-batch-size-for-initial-replication) Deployment Planner 보고서에서입니다.
    - 물리적 컴퓨터에 대 한 비슷한 크기와 데이터의 크기를 가진 컴퓨터에 사용 가능한 네트워크 처리량에 따라 일괄 처리를 식별 하는 것이 좋습니다. 목적은 동일한 기간 주위에 초기 복제를 완료할 수 있는 일괄 처리 컴퓨터로입니다.
    
2. 컴퓨터의 디스크 변동 높음 배포 thePlanner의 제한을 초과 하면, 하는 경우 컴퓨터를 끄는 (예: 로그 덤프 또는 임시 파일) 복제 하지 않아도 됩니다. 중요 하지 않은 파일을 이동할 수 있습니다. VMware Vm에 대 한 별도 디스크에서 이러한 파일을 이동할 수 있습니다 차례로 [해당 디스크는 제외할](vmware-azure-exclude-disk.md) 복제에서.
3. 복제를 사용 하기 전에 확인 하는 컴퓨터 충족 [복제 요구 사항](vmware-physical-azure-support-matrix.md#replicated-machines)합니다.
4. 구성에 대 한 복제 정책을 [VMware Vm](vmware-azure-set-up-replication.md#create-a-policy) 하거나 [물리적 서버](physical-azure-disaster-recovery.md#create-a-replication-policy)합니다.
5. 에 대 한 복제를 사용 하도록 설정 [VMware Vm](vmware-azure-enable-replication.md) 하거나 [물리적 서버](physical-azure-disaster-recovery.md#enable-replication)합니다. 이 선택한 컴퓨터에 대 한 초기 복제를 시작합니다.

## <a name="monitor-your-deployment"></a>배포 모니터링

첫 번째 일괄 처리의 Vm에 대 한 복제를 시작 후 배포를 다음과 같이 모니터링 시작:  

1. 복제 된 컴퓨터의 상태를 모니터링 하는 재해 복구 관리자를 할당 합니다.
2. [이벤트 모니터링](site-recovery-monitor-and-troubleshoot.md) 복제 된 항목 및 인프라에 대 한 합니다.
3. [상태를 모니터링](vmware-physical-azure-monitor-process-server.md) 스케일 아웃 프로세스 서버.
4. 신청 [전자 메일 알림](https://docs.microsoft.com/azure/site-recovery/site-recovery-monitor-and-troubleshoot#subscribe-to-email-notifications) 쉬운 모니터링을 위해 이벤트에 대 한 합니다.
5. 일반 실시 [재해 복구 훈련](site-recovery-test-failover-to-azure.md)을 모두 예상 대로 작동 하는지 확인 해야 합니다.


## <a name="plan-for-large-scale-failovers"></a>대규모 장애 조치에 대 한 계획

재해 이벤트에서 많은 수의 컴퓨터/워크 로드를 Azure로 장애 조치할 수 해야 합니다. 이 유형의 이벤트에 대해 다음과 같이 준비 합니다.

준비할 수 있으므로 사전에 장애 조치에 대 한 다음과 같은:

- [인프라 및 Vm 준비](#plan-infrastructure-and-vm-connectivity) 장애 조치 후 워크 로드에 사용할 수 있도록 하 고 사용자가 Azure Vm에 액세스할 수 있도록 합니다.
- 참고 합니다 [장애 조치 제한](#failover-limits) 이 문서의 앞부분에 설명 합니다. 이러한 한도 내에 장애 조치에 있게 됩니다 있는지 확인 합니다.
- 일반 실행 [재해 복구 훈련](site-recovery-test-failover-to-azure.md)합니다. 훈련 시킬:
    - 장애 조치 하기 전에 배포에서 간격을 찾습니다.
    - 앱에 대 한 종단 간 RTO를 예상 합니다.
    - 워크 로드에 대 한 종단 간 RPO를 추정 합니다.
    - IP 주소 범위 충돌을 식별 합니다.
    - 드릴을 실행 하지 드릴에 대 한 프로덕션 네트워크를 사용 하 여, 프로덕션 및 테스트 네트워크의 이름과 서브넷을 사용 하지 않도록 하는 모든 드릴 후 테스트 장애 조치를 정리 하는 것이 좋습니다.

대규모 장애 조치를 실행 하려면 다음 권장 합니다.

1. 워크 로드 장애 조치에 대 한 복구 계획을 만듭니다.
    - 각 복구 계획 최대 50 개의 컴퓨터의 장애 조치를 트리거할 수 있습니다.
    - [자세히 알아봅니다](recovery-plan-overview.md) .
2. Azure에서 수동 작업을 자동화 하려면 복구 계획에 Azure Automation runbook 스크립트를 추가 합니다. 일반적인 작업 부하 분산 장치, DNS 등 업데이트를 구성 하는 포함 됩니다. [자세히 알아보기](site-recovery-runbook-automation.md)
2. 장애 조치 전에 Azure 환경과 준수할 수 있도록 Windows 컴퓨터를 준비 합니다. [장애 조치 제한](#plan-azure-subscriptions-and-quotas) 준수 하는 컴퓨터에 대 한 높습니다. [자세한](site-recovery-failover-to-azure-troubleshoot.md#failover-failed-with-error-id-170010) runbook에 대 한 합니다.
4.  장애 조치를 트리거하는 [시작 AzRecoveryServicesAsrPlannedFailoverJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/start-azrecoveryservicesasrplannedfailoverjob?view=azps-2.0.0&viewFallbackFrom=azps-1.1.0) 복구 계획을와 함께 PowerShell cmdlet.



## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Site Recovery 모니터링](site-recovery-monitor-and-troubleshoot.md)
