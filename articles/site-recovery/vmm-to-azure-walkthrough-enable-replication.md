---
title: "Azure Site Recovery를 사용하여 VMM 클라우드의 Hyper-V VM을 Azure로 복제할 수 있도록 설정 | Microsoft 문서"
description: "Azure Site Recovery 서비스를 사용하여 VMM 클라우드의 Hyper-V VM을 Azure로 복제할 수 있도록 설정하는 방법을 설명합니다."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 89a2c4fc-7e03-4a86-a2c0-52831ccebc1a
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2017
ms.author: raynew
ms.openlocfilehash: 96a817e43a830e836f2faa4603fc88ed9c0b1828
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="step-11-enable-replication-to-azure-for-hyper-v-vms-in-vmm-clouds"></a>11단계: VMM 클라우드의 Hyper-V VM을 Azure로 복제할 수 있도록 설정

복제 정책을 설정한 후 이 문서의 내용을 참조하여 Azure Portal에서 [Azure Site Recovery](site-recovery-overview.md) 서비스를 사용해 System Center Virtual Machine Manager(VMM) 클라우드에서 관리되는 온-프레미스 Hyper-V VM(가상 컴퓨터)을 Azure로 복제하도록 설정할 수 있습니다.

이 문서의 하단 또는 [Azure Recovery Services 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)에서 의견이나 질문을 게시합니다.


## <a name="before-you-start"></a>시작하기 전에

Azure 계정에 Azure VM을 만들기 위한 올바른 [권한](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)이 있는지 확인합니다. Azure 역할 기반 액세스 제어에 대해 자세히 알아보려면 [여기](../active-directory/role-based-access-built-in-roles.md)를 참조하세요.

## <a name="exclude-disks-from-replication"></a>복제에서 디스크 제외

기본적으로 컴퓨터의 모든 디스크가 복제됩니다. 디스크를 복제에서 제외할 수 있습니다. 예를 들어 임시 데이터 또는 컴퓨터나 응용 프로그램이 다시 시작할 때마다 새로 고쳐지는 데이터(예: pagefile.sys 또는 SQL Server tempdb)가 포함된 디스크를 복제하고 싶지 않을 수 있습니다. [자세히 알아보기](site-recovery-exclude-disk.md)

## <a name="replicate-vms"></a>VM 복제

다음과 같이 VM에 대해 복제를 활성화합니다.  

1. **2단계: 응용 프로그램 복제** > **원본**을 클릭합니다. 처음으로 복제를 활성화한 후 자격 증명 모음에서 **+복제**를 클릭하여 추가 컴퓨터에 대해 복제를 활성화합니다.

    ![복제 활성화](./media/vmm-to-azure-walkthrough-enable-replication/enable-replication1.png)
2. **원본** 블레이드에서 Hyper-V 호스트가 있는 VMM 서버 및 클라우드를 선택합니다. 그런 후 **OK**를 클릭합니다.

    ![복제 활성화](./media/vmm-to-azure-walkthrough-enable-replication/enable-replication-source.png)
3. **대상**에서 구독, 장애 조치(failover) 후 배포 모델 및 복제된 데이터에 사용 중인 저장소 계정을 선택합니다.

    ![복제 활성화](./media/vmm-to-azure-walkthrough-enable-replication/enable-replication-target.png)
4. 사용하려는 저장소 계정을 선택합니다. 갖고 있는 저장소 계정 말고 다른 저장소 계정을 사용하려면 [새로 만듭니다](#set-up-an-azure-storage-account). 복제된 데이터에 프리미엄 저장소 계정을 사용하는 경우 온-프레미스 데이터에 지속적인 변화를 캡처하는 복제 로그를 저장하는 추가 표준 저장소 계정을 선택해야 합니다. Resource Manager 모델을 사용하여 저장소 계정을 만들려면 **새로 만들기**를 클릭합니다. 클래식 모델을 사용하여 저장소 계정을 만들려면 [Azure 포털](../storage/common/storage-create-storage-account.md)에서 만들면 됩니다. 그런 후 **OK**를 클릭합니다.
5. 장애 조치(failover) 후 Azure VM이 생성될 때 연결될 Azure 네트워크 및 서브넷을 선택합니다. 컴퓨터마다 Azure 네트워크를 선택하려면 **나중에 구성**을 선택합니다. 갖고 있는 네트워크 말고 다른 네트워크를 사용하려면 **새로 만듭니다**. Resource Manager 모델을 사용하여 네트워크를 만들려면 [새로 만들기](#set-up-an-azure-network)를 클릭합니다. Resource Manager 모델을 사용하여 네트워크를 만들려면 **새로 만들기**를 클릭합니다. 클래식 모델을 사용하여 네트워크를 만들려면 [Azure 포털](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)에서 만들면 됩니다. 해당하는 경우 서브넷을 선택합니다. 그런 후 **OK**를 클릭합니다.
6. **Virtual Machines** > **가상 컴퓨터 선택**에서 복제하려는 각 컴퓨터를 클릭하여 선택합니다. 복제를 활성화할 수 있는 컴퓨터만 선택할 수 있습니다. 그런 후 **OK**를 클릭합니다.

    ![복제 활성화](./media/vmm-to-azure-walkthrough-enable-replication/enable-replication5.png)

7. **속성** > **속성 구성**에서 선택한 VM의 운영 체제 및 OS 디스크를 선택합니다.

    - Azure VM 이름(대상 이름)이 [Azure Virtual Machine 요구 사항](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)을 충족하는지 확인합니다.   
    - 기본적으로 VM의 모든 디스크가 복제에 선택되지만 디스크를 지워서 제외할 수 있습니다.

        - 복제 대역폭을 줄이기 위해 디스크를 제외할 수 있습니다. 예를 들어 임시 데이터 또는 컴퓨터나 앱이 다시 시작할 때마다 새로 고쳐지는 데이터(예: pagefile.sys 또는 SQL Server tempdb)가 포함된 디스크를 복제에서 제외하려는 경우가 있습니다. 디스크를 선택 취소하여 복제에서 해당 디스크를 제외할 수 있습니다.
        - 기본 디스크만 제외할 수 있습니다. OS 디스크는 제외할 수 없습니다.
        - 동적 디스크는 제외하지 않는 것이 좋습니다. Site Recovery는 게스트 VM 내의 가상 하드 디스크가 기본 디스크인지 아니면 동적 디스크인지 식별할 수 없습니다. 모든 종속적인 동적 볼륨 디스크가 제외되지 않으면, 보호된 동적 디스크는 VM이 장애 조치되면 실패한 디스크로 나타나며 해당 디스크에 있는 데이터는 액세스할 수 없습니다.
        - 복제를 사용하도록 설정한 후 복제에 대해 디스크를 추가 또는 제거할 수 없습니다. 디스크를 추가하거나 제외하려는 경우 VM에 대한 보호를 사용하지 않도록 설정한 다음 다시 사용하도록 설정해야 합니다.
        - Azure에서 수동으로 만드는 디스크는 장애 복구되지 않습니다. 예를 들어 디스크 3장을 장애 조치하고 2장을 직접 Azure VM에서 만든다면 장애 조치된 3장의 디스크만이 Azure에서 다시 Hyper-V로 장애 복구됩니다. 장애 복구 또는 Hyper-V에서 Azure로 역방향 복제에서 수동으로 만든 디스크를 포함할 수 없습니다.
        - 응용 프로그램 작동에 필요한 디스크를 제외하면 Azure로 장애 조치(failover) 후 복제된 응용 프로그램이 실행될 수 있도록 디스크를 Azure에 수동으로 만들어야 합니다. 또는 Azure Automation을 복구 계획에 통합하여 컴퓨터의 장애 조치(failover) 동안 디스크를 만들 수 있습니다.

    **확인**을 클릭하여 변경 내용을 저장합니다. 나중에 추가 속성을 설정할 수 있습니다.

    ![복제 활성화](./media/vmm-to-azure-walkthrough-enable-replication/enable-replication6-with-exclude-disk.png)

8. **복제 설정** > **복제 설정 구성**에서 보호되는 VM에 적용할 복제 정책을 선택합니다. 그런 후 **OK**를 클릭합니다. **복제 정책** > 정책 이름 > **설정 편집**에서 복제 정책을 수정할 수 있습니다. 적용하는 변경 사항은 이미 복제 중인 컴퓨터와 새 컴퓨터에 사용됩니다.

   ![복제 활성화](./media/vmm-to-azure-walkthrough-enable-replication/enable-replication7.png)

**작업** > **Site Recovery 작업**에서 **보호 사용** 작업의 진행률을 추적할 수 있습니다. **보호 완료** 작업이 실행된 후에는 컴퓨터가 장애 조치(failover)를 수행할 준비가 되어 있습니다.



## <a name="next-steps"></a>다음 단계

[12단계: 테스트 장애 조치(failover) 실행](vmm-to-azure-walkthrough-test-failover.md)으로 이동합니다.
