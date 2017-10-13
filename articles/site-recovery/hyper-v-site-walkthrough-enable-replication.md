---
title: "Azure Site Recovery를 사용하여 Azure로 Hyper-V VM 복제(System Center VMM 없음)를 위한 복제 설정 | Microsoft Docs"
description: "Azure Site Recovery 서비스를 사용하여 Hyper-V VM을 Azure로 복제를 활성화하는 데 필요한 단계를 요약합니다."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: bd31ef01-69f1-4591-a519-e42510a6e2f4
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/21/2017
ms.author: raynew
ms.openlocfilehash: aabe99dbd375b80e4a87ca7a067927008672b4ed
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="step-10-enable-replication-for-hyper-v-vms-replicating-to-azure"></a>10단계: Azure로 Hyper-V VM 복제를 위한 복제를 활성화


이 문서에서는 Azure Portal에서 [Azure Site Recovery](site-recovery-overview.md) 서비스를 사용하여 Azure로 온-프레미스 Hyper-V 가상 컴퓨터(System Center VMM에서 관리되지 않음) 복제를 활성화하는 방법을 설명합니다.

이 문서의 하단 또는 [Azure Recovery Services 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)에서 의견이나 질문을 게시합니다.




## <a name="before-you-start"></a>시작하기 전에

시작하기 전에 Azure 사용자 계정에 Azure로 새 가상 컴퓨터 복제를 활성화하는 데 필요한 [사용 권한](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)이 있는지 확인합니다.

## <a name="exclude-disks-from-replication"></a>복제에서 디스크 제외

기본적으로 컴퓨터의 모든 디스크가 복제됩니다. 디스크를 복제에서 제외할 수 있습니다. 예를 들어 임시 데이터 또는 컴퓨터나 응용 프로그램이 다시 시작할 때마다 새로 고쳐지는 데이터(예: pagefile.sys 또는 SQL Server tempdb)가 포함된 디스크를 복제하고 싶지 않을 수 있습니다. [자세히 알아보기](site-recovery-exclude-disk.md)


## <a name="replicate-vms"></a>VM 복제

다음과 같이 VM에 대해 복제를 활성화합니다.          

1. **응용 프로그램 복제** > **원본**을 클릭합니다. 처음으로 복제를 설정한 후에는 **+복제**를 클릭하여 추가 컴퓨터에 대해 복제를 활성화할 수 있습니다.

    ![복제 활성화](./media/hyper-v-site-walkthrough-enable-replication/enable-replication.png)
2. **원본**에서 Hyper-V 사이트를 선택합니다. 그런 후 **OK**를 클릭합니다.
3. **대상**에서 자격 증명 모음 구독을 선택하고, 장애 조치(failover) 후 Azure에서 사용할 장애 조치(failover) 모델(클래식 또는 리소스 관리)을 선택합니다.
4. 사용하려는 저장소 계정을 선택합니다. 사용하려는 계정이 없는 경우 [새로 만들 수 있습니다](#set-up-an-azure-storage-account). 그런 후 **OK**를 클릭합니다.
5. 장애 조치(failover) 후 Azure VM이 생성될 때 연결할 Azure 네트워크 및 서브넷을 선택합니다.

    - 복제를 활성화한 모든 컴퓨터에 네트워크 설정을 적용하려면 **선택한 컴퓨터에 대해 지금 구성합니다.**를 선택합니다.
    - 네트워크가 없는 경우 **만들어야** 합니다.
    - 사용하려는 네트워크가 없는 경우 [새로 만들 수 있습니다](#set-up-an-azure-network). 해당하는 경우 서브넷을 선택합니다. 그런 후 **OK**를 클릭합니다.

   ![복제 활성화](./media/hyper-v-site-walkthrough-enable-replication/enable-replication11.png)

6. **Virtual Machines** > **가상 컴퓨터 선택**에서 복제하려는 각 컴퓨터를 클릭하여 선택합니다. 복제를 활성화할 수 있는 컴퓨터만 선택할 수 있습니다. 그런 후 **OK**를 클릭합니다.

    ![복제 활성화](./media/hyper-v-site-walkthrough-enable-replication/enable-replication5-for-exclude-disk.png)

7. **속성** > **속성 구성**에서 선택한 VM의 운영 체제 및 OS 디스크를 선택합니다.
8. Azure VM 이름(대상 이름)이 [Azure Virtual Machine 요구 사항](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)을 충족하는지 확인합니다.
9. 기본적으로 VM의 모든 디스크는 복제를 위해 선택됩니다. 디스크를 지워서 제외시킵니다.
10. **확인**을 클릭하여 변경 내용을 저장합니다. 나중에 추가 속성을 설정할 수 있습니다.

    ![복제 활성화](./media/hyper-v-site-walkthrough-enable-replication/enable-replication6-with-exclude-disk.png)

11. **복제 설정** > **복제 설정 구성**에서 보호되는 VM에 적용할 복제 정책을 선택합니다. 그런 후 **OK**를 클릭합니다. **복제 정책** > 정책 이름 > **설정 편집**에서 복제 정책을 수정할 수 있습니다. 적용하는 변경 사항은 이미 복제 중인 컴퓨터와 새 컴퓨터에 사용됩니다.


   ![복제 활성화](./media/hyper-v-site-walkthrough-enable-replication/enable-replication7.png)

**작업** > **Site Recovery 작업**에서 **보호 사용** 작업의 진행률을 추적할 수 있습니다. **보호 완료** 작업이 실행된 후에는 컴퓨터가 장애 조치(failover)를 수행할 준비가 되어 있습니다.


## <a name="next-steps"></a>다음 단계


[11단계: 테스트 장애 조치(failover) 실행](hyper-v-site-walkthrough-test-failover.md)으로 이동합니다.
