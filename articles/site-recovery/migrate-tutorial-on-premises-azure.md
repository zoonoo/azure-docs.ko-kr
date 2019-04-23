---
title: Azure Site Recovery를 사용하여 Azure로 온-프레미스 컴퓨터 마이그레이션 | Microsoft Docs
description: 이 문서에서는 Azure Site Recovery를 사용하여 Azure로 온-프레미스 컴퓨터를 마이그레이션하는 방법을 설명합니다.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 04/08/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: fc15db91b8f4cc6dbdecd0e7321abdbf81744f08
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59357983"
---
# <a name="migrate-on-premises-machines-to-azure"></a>Azure로 온-프레미스 컴퓨터 마이그레이션


이 문서에서는 [Azure Site Recovery](site-recovery-overview.md)를 사용하여 Azure로 온-프레미스 머신을 마이그레이션하는 방법을 설명합니다. 일반적으로 Site Recovery는 온-프레미스 머신 및 Azure VM의 재해 복구를 관리 및 오케스트레이션하는 데 사용됩니다. 하지만 마이그레이션에도 사용할 수 있습니다. 마이그레이션은 재해 복구와 동일한 단계를 따르지만 한 가지 예외가 있습니다. 마이그레이션에서는 온-프레미스 사이트에서 머신 장애 조치(fail-over)를 수행하는 것이 마지막 단계입니다. 재해 복구와 달리 마이그레이션 시나리오에서는 온-프레미스로 장애 복구할 수 없습니다.


이 자습서는 온-프레미스 VM 및 물리적 서버를 Azure로 마이그레이션하는 방법을 보여 줍니다. 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 마이그레이션의 원본 및 대상 환경 설정
> * 복제 정책 설정
> * 복제 사용
> * 테스트 마이그레이션을 실행하여 모든 것이 예상대로 작동하는지 확인
> * Azure에 대한 일회성 장애 조치(failover) 실행


> [!TIP]
> Azure Migrate 서비스는 이제 VMware VM을 Azure로 마이그레이션하는 데 사용되는 새로운 에이전트리스 환경의 미리 보기를 제공합니다. [자세히 알아보기](https://aka.ms/migrateVMs-signup).


## <a name="before-you-start"></a>시작하기 전에

반가상화 드라이버에서 내보낸 디바이스는 지원되지 않습니다.


## <a name="prepare-azure-and-on-premises"></a>Azure 및 온-프레미스 준비

1. [이 문서](tutorial-prepare-azure.md)에 설명된 대로 Azure를 준비합니다. 이 문서에는 재해 복구 준비 단계가 설명되어 있지만 마이그레이션에 적용해도 무방합니다.
2. 온-프레미스 [VMware](vmware-azure-tutorial-prepare-on-premises.md) 또는 [Hyper-V](hyper-v-prepare-on-premises-tutorial.md) 서버를 준비합니다. 물리적 머신을 마이그레이션하는 경우 아무것도 준비할 필요가 없습니다. [지원 매트릭스](vmware-physical-azure-support-matrix.md)를 확인하기만 하면 됩니다.


## <a name="select-a-replication-goal"></a>복제 목표 선택

복제할 대상과 복제할 위치를 선택합니다.
1. **Recovery Services 자격 증명 모음** > 자격 증명 모음을 클릭합니다.
2. 리소스 메뉴 >에서 **Site Recovery** > **인프라 준비** > **보호 목표**를 클릭합니다.
3. **보호 목표**에서 마이그레이션할 항목을 선택합니다.
    - **VMware**: **Azure에** > **예, VMWare vSphere 하이퍼바이저 사용**을 차례로 선택합니다.
    - **물리적 머신**: **Azure에** > **가상화되지 않음/기타**를 선택합니다.
    - **Hyper-V**: **Azure에** > **예, Hyper-V 사용**을 선택합니다. Hyper-V VM이 VMM에서 관리되는 경우 **예**를 선택합니다.


## <a name="set-up-the-source-environment"></a>원본 환경 설정

**시나리오** | **세부 정보**
--- | --- 
VMware | [원본 환경](vmware-azure-set-up-source.md)을 설정하고, [구성 서버](vmware-azure-deploy-configuration-server.md)를 설정합니다.
물리적 머신 | 원본 환경 및 구성 서버를 [설정](physical-azure-set-up-source.md)합니다.
Hyper-V | [원본 환경](hyper-v-azure-tutorial.md#set-up-the-source-environment) 설정<br/><br/> System Center VMM을 사용하여 배포된 Hyper-V용 [원본 환경](hyper-v-vmm-azure-tutorial.md#set-up-the-source-environment)을 설정합니다.

## <a name="set-up-the-target-environment"></a>대상 환경 설정

대상 리소스를 선택하고 확인합니다.

1. **인프라 준비** > **대상**을 클릭하고 사용하려는 Azure 구독을 선택합니다.
2. 리소스 관리자 배포 모델을 지정합니다.
3. Site Recovery는 Azure 리소스를 확인합니다.
    - VMware VM 또는 물리적 서버를 마이그레이션하는 경우 Site Recovery가 장애 조치(failover) 후 생성된 Azure VM이 배치될 Azure 네트워크가 있는지 확인합니다.
    - Hyper-V VM을 마이그레이션하는 경우 Site Recovery가 호환되는 Azure 스토리지 계정 및 네트워크가 있는지 확인합니다.
4. System Center VMM으로 관리되는 Hyper-V VM을 마이그레이션하는 경우 [네트워크 매핑](hyper-v-vmm-azure-tutorial.md#configure-network-mapping)을 설정하세요.

## <a name="set-up-a-replication-policy"></a>복제 정책 설정

**시나리오** | **세부 정보**
--- | --- 
VMware | VMware VM의 [복제 정책](vmware-azure-set-up-replication.md)을 설정합니다.
물리적 머신 | 물리적 머신에 대한 [복제 정책](physical-azure-disaster-recovery.md#create-a-replication-policy)을 설정합니다.
Hyper-V | [복제 정책](hyper-v-azure-tutorial.md#set-up-a-replication-policy) 설정<br/><br/> System Center VMM을 사용하여 배포된 Hyper-V에 대한 [복제 정책](hyper-v-vmm-azure-tutorial.md#set-up-a-replication-policy)을 설정합니다.

## <a name="enable-replication"></a>복제 사용

**시나리오** | **세부 정보**
--- | --- 
VMware | VMware VM에 대해 [복제를 사용하도록 설정](vmware-azure-enable-replication.md)합니다.
물리적 머신 | 물리적 머신에 [복제를 사용하도록 설정](physical-azure-disaster-recovery.md#enable-replication)합니다.
Hyper-V | [복제 활성화](hyper-v-azure-tutorial.md#enable-replication)<br/><br/> System Center VMM을 사용하여 배포된 Hyper-V에 대한 [복제를 사용](hyper-v-vmm-azure-tutorial.md#enable-replication)하도록 설정합니다.


## <a name="run-a-test-migration"></a>테스트 마이그레이션 실행

모든 것이 예상대로 작동하는지 확인할 수 있도록 Azure에 대해 [테스트 장애 조치(Failover)](tutorial-dr-drill-azure.md)를 실행합니다.


## <a name="migrate-to-azure"></a>Azure로 마이그레이션

마이그레이션할 컴퓨터에 대해 장애 조치(Failover)를 실행합니다.

1. **설정** > **복제된 항목**에서 컴퓨터 > **장애 조치(Failover)** 를 클릭합니다.
2. **장애 조치(Failover)** 에서 장애 조치할 **복구 지점**을 선택합니다. 최신 복구 지점을 선택합니다.
3. 암호화 키 설정은 이 시나리오와 관련이 없습니다.
4. **장애 조치(failover)를 시작하기 전에 컴퓨터를 종료합니다.** 를 선택합니다. Site Recovery는 장애 조치(failover)를 트리거하기 전에 가상 머신을 종료하려고 시도합니다. 종료가 실패하더라도 장애 조치는 계속됩니다. **작업** 페이지에서 장애 조치 진행 상황 확인을 수행할 수 있습니다.
5. Azure VM이 예상대로 Azure에 표시되는지 확인합니다.
6. **복제된 항목**에서 VM를 마우스 오른쪽 단추로 클릭하고 **마이그레이션 완료**를 클릭합니다. 다음을 수행합니다.

   - 마이그레이션 프로세스가 완료되고, 온-프레미스 VM에 대한 복제가 중지되고, VM에 대한 Site Recovery 청구가 중지됩니다.
   - 이 단계는 복제 데이터를 정리합니다. 마이그레이션된 VM을 삭제하지 않습니다.

     ![마이그레이션 완료](./media/migrate-tutorial-on-premises-azure/complete-migration.png)


> [!WARNING]
> **진행 중인 장애 조치(failover)는 취소하지 마세요**. 장애 조치(failover)가 시작되기 전에 VM 복제가 중지됩니다. 진행 중인 장애 조치(failover)를 취소하면 장애 조치(failover)가 중지되지만 VM은 다시 복제되지 않습니다.

일부 시나리오에서는 장애 조치(Failover)를 위해서는 추가 처리가 필요하며 이러한 작업을 완료하는 데는 약 8~10분이 소요됩니다. 물리적 서버, VMware Linux 컴퓨터, DHCP 서비스가 사용되도록 설정되지 않은 VMware VM과 부팅 드라이버인 storvsc, vmbus, storflt, intelide, atapi가 없는 VMware VM의 경우 테스트 장애 조치(Failover)가 더 오래 걸릴 수 있습니다.

## <a name="after-migration"></a>마이그레이션 후

머신을 Azure로 마이그레이션한 후 완료해야 하는 여러 단계가 있습니다.

[복구 계획](site-recovery-runbook-automation.md)의 기본 제공 자동화 스크립트 기능을 사용하여 마이그레이션 프로세스의 일부로 몇몇 단계를 자동화할 수 있습니다.   


### <a name="post-migration-steps-in-azure"></a>Azure의 마이그레이션 후 단계

- 데이터베이스 연결 문자열 업데이트, 웹 서버 구성 등의 마이그레이션 후 앱 조정을 수정합니다. 
- 이제 Azure에서 실행 중인 마이그레이션된 애플리케이션에서 최종 애플리케이션 및 마이그레이션 수용 테스트를 수행합니다.
- [Azure VM 에이전트](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows)는 Azure Fabric Controller와 VM 간 상호 작용을 관리합니다. 이는 Azure Backup, Site Recovery, Azure Security 같은 일부 Azure 서비스에 필요합니다.
    - VMware 머신과 물리적 서버를 마이그레이션하는 경우 모바일 서비스 설치 관리자는 Windows 머신에 사용 가능한 Azure VM 에이전트를 설치합니다. Linux VM에서는 장애 조치(failover) 후 에이전트를 설치하는 것이 좋습니다.
    - Azure VM을 보조 지역으로 마이그레이션하는 경우 마이그레이션 전에 VM에서 Azure VM 에이전트를 프로비전해야 합니다.
    - Hyper-V VM을 Azure로 마이그레이션하는 경우 마이그레이션 후에 Azure VM에 Azure VM 에이전트를 설치합니다.
- VM에서 Site Recovery 공급자/에이전트를 수동으로 제거합니다. VMware VM 또는 물리적 서버를 마이그레이션하는 경우 VM에서 모바일 서비스를 제거합니다.
- 복원력 개선:
    - Azure Backup 서비스를 통해 Azure VM을 백업하여 데이터 보안을 유지합니다. [자세히 알아보기]( https://docs.microsoft.com/azure/backup/quick-backup-vm-portal).
    - Site Recovery를 통해 Azure VM을 보조 지역에 복제하면 워크로드를 계속 실행하고 지속적으로 사용할 수 있습니다. [자세히 알아보기](azure-to-azure-quickstart.md).
- 보안 강화:
    - Azure Security Center [Just-In-Time 관리]( https://docs.microsoft.com/azure/security-center/security-center-just-in-time)를 통해 인바운드 트래픽 액세스를 잠그고 제한합니다.
    - [네트워크 보안 그룹](https://docs.microsoft.com/azure/virtual-network/security-overview)을 사용하여 관리 엔드포인트에 대한 네트워크 트래픽을 제한합니다.
    - [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview)을 배포하여 디스크를 보호하고 데이터를 도난 및 무단 액세스로부터 안전하게 유지합니다.
    - [IaaS 리소스 보호]( https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/ )에 대해 자세히 알아보고 [Azure Security Center](https://azure.microsoft.com/services/security-center/ )를 방문하세요.
- 모니터링 및 관리 앱:
    - 리소스 사용량과 비용을 모니터링하려면 [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/overview)를 배포하는 것이 좋습니다.

### <a name="post-migration-steps-on-premises"></a>온-프레미스의 마이그레이션 후 단계

- 마이그레이션된 Azure VM 인스턴스에서 실행 중인 앱으로 앱 트래픽을 이동합니다.
- 로컬 VM 인벤토리에서 온-프레미스 VM을 제거합니다.
- 로컬 백업 작업에서 온-프레미스 VM을 제거합니다.
- 내부 문서를 업데이트하여 Azure VM의 새 위치 및 IP 주소를 표시합니다.




## <a name="next-steps"></a>다음 단계

이 자습서에서는 온-프레미스 VM을 Azure VM으로 마이그레이션했습니다. Now

> [!div class="nextstepaction"]
> 보조 Azure 지역에 Azure VM의 [재해 복구를 설정](azure-to-azure-replicate-after-migration.md)합니다.

  
