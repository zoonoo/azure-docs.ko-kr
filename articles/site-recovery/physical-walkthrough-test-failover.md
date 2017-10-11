---
title: "Azure Site Recovery를 사용하여 Azure에 물리적 서버 복제를 위한 테스트 장애 조치(Failover) 실행 | Microsoft Docs"
description: "Azure Site Recovery 서비스를 사용하여 Azure로 물리적 서버를 복제하기 위해 테스트 장애 조치(failover)를 실행하는 데 필요한 단계를 요약합니다."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: a640e139-3a09-4ad8-8283-8fa92544f4c6
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.openlocfilehash: 94aa3bfc700cad3de9fc5516c0c9a4d86ade3fed
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/29/2017
---
# <a name="step-11-run-a-test-failover-of-physical-servers-to-azure"></a>11단계: Azure에 물리적 서버의 테스트 장애 조치(failover) 실행

이 문서에서는 Azure Portal에서 [Azure Site Recovery](site-recovery-overview.md) 서비스를 사용하여 온-프레미스 물리적 서버에서 Azure에 테스트 장애 조치를 실행하는 방법을 설명합니다.

이 문서의 하단 또는 [Azure Recovery Services 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)에서 의견이나 질문을 게시합니다.


## <a name="before-you-start"></a>시작하기 전에

테스트 장애 조치(failover)를 실행하기 전에 서버 속성을 확인하고 필요한 사항을 변경하는 것이 좋습니다. **복제 항목**에서 VM 속성에 액세스할 수 있습니다. **Essentials** 블레이드는 컴퓨터 설정 및 상태에 대한 정보를 표시합니다.

## <a name="managed-disk-considerations"></a>Managed Disk 고려 사항

[관리 디스크](../virtual-machines/windows/managed-disks-overview.md)는 VM 디스크와 연결된 저장소 계정을 관리하여 Azure VM의 디스크 관리를 간소화합니다. 

- 서버에 보호를 활성화하면 VM 데이터는 저장소 계정에 복제됩니다. 관리 디스크는 장애 조치(failover) 시에만 생성되고 VM에 연결됩니다.
- Resource Manager 배포 모델을 사용하여 배포된 Azure VM에 대해서만 관리 디스크를 만들 수 있습니다.  
- 이 설정을 사용하면, **관리 디스크 사용**을 활성화한 리소스 그룹의 가용성 집합만 선택할 수 있습니다. 관리 디스크가 있는 VM은 가용성 집합에 있어야 하며 **관리 디스크 사용** 설정이 **예**로 되어 있어야 합니다. VM에서 이 설정을 사용하지 않는 경우 활성화한 관리 디스크가 없는 리소스 그룹의 가용성 집합만 선택할 수 있습니다.
- 관리 디스크와 가용성 집합에 대해 [자세히 알아봅니다](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability#use-managed-disks-for-vms-in-an-availability-set).
- 복제에 사용하는 저장소 계정이 저장소 서비스 암호화로 암호화된 경우에는 장애 조치(failover)를 수행하는 도중에 관리 디스크를 만들 수 없습니다. 이 경우 관리 디스크의 사용을 활성화하거나 VM에 대한 보호를 비활성화하고, 활성화된 암호화가 없는 저장소 계정을 사용하도록 다시 활성화합니다. [자세히 알아보기](https://docs.microsoft.com/azure/storage/storage-managed-disks-overview#managed-disks-and-encryption).


## <a name="network-considerations"></a>네트워크 고려 사항

장애 조치 후에 만든 Azure VM에 대상 IP 주소를 설정할 수 있습니다.

- 주소를 입력하지 않으면 장애 조치(Failover)된 컴퓨터가 DHCP를 사용합니다.
- 장애 조치(failover) 시 사용할 수 없는 주소를 설정하면 장애 조치(failover)가 작동하지 않습니다.
- 주소를 테스트 장애 조치(Failover) 네트워크에서 사용할 수 있는 경우 테스트 장애 조치(Failover)에 동일한 대상 IP 주소를 사용해도 됩니다.
- 네트워크 어댑터 수는 대상 가상 컴퓨터에 대해 지정하는 크기에 따라 결정됩니다.

     - 원본 컴퓨터의 네트워크 어댑터 수가 대상 컴퓨터 크기에 허용되는 어댑터 수보다 작거나 같은 경우, 대상의 어댑터 수는 소스와 동일하게 됩니다.
     - 원본 가상 컴퓨터의 어댑터의 수가 대상 크기에 허용된 수를 초과하면 대상 크기 최대치가 사용됩니다.
     - 예를 들어 원본 컴퓨터에 두 네트워크 어댑터가 있고 대상 컴퓨터 크기가 4를 지원하는 경우, 대상 컴퓨터에는 2개의 어댑터가 있어야 합니다. 원본 컴퓨터에 두 어댑터가 있지만 지원되는 대상 크기가 하나만 지원하는 경우 대상 컴퓨터에는 1개의 어댑터만 있어야 합니다.     
   - 가상 컴퓨터에 네트워크 어댑터가 여러 개 있으면 모두 동일한 네트워크에 연결됩니다.
   - 가상 컴퓨터에 네트워크 어댑터가 여러 개 있으면 목록에서 첫 번째 어댑터가 Azure 가상 컴퓨터에서 *기본* 네트워크 어댑터가 됩니다.
 - IP 주소를 지정하는 방법에 대해 [자세히 알아봅니다](vmware-walkthrough-network.md).



## <a name="view-and-modify-vm-settings"></a>VM 설정 보기 및 수정

장애 조치(failover)를 실행하기 전에 원본 서버의 속성을 확인하는 것이 좋습니다.

1. **보호된 항목**에서 **복제된 항목**을 클릭하고 컴퓨터를 클릭합니다.
2. **복제된 항목** 창에서 컴퓨터 정보, 상태 및 최신 사용 가능한 복구 지점의 요약을 볼 수 있습니다. 자세한 내용을 보려면 **속성**을 클릭합니다.
3. **계산 및 네트워크**에서 다음을 수행할 수 있습니다.
    - Azure VM 이름을 수정합니다. 이름은 [Azure 요구 사항](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)을 충족해야 합니다.
    - 장애 조치(failover) 후 [리소스 그룹]을 지정합니다.
    - Azure VM에 대한 대상 크기를 지정합니다.
    - [가용성 집합](../virtual-machines/windows/tutorial-availability-sets.md)을 선택합니다.
    - [관리 디스크](#managed-disk-considerations)의 사용 여부를 지정합니다. 관리 디스크를 Azure로의 마이그레이션에서 컴퓨터에 연결하려는 경우 **예**를 선택합니다.
    - 장애 조치(failover) 후 Azure VM이 배치될 네트워크/서브넷 및 할당되는 IP 주소를 포함한 네트워크 설정을 보거나 수정합니다.
4. **디스크**에서 VM의 운영 체제 및 데이터 디스크에 대한 정보를 볼 수 있습니다.

## <a name="run-a-test-failover"></a>테스트 장애 조치(Failover) 실행

모든 항목을 설정한 후 모든 것이 예상대로 작동하는지 확인할 수 있도록 테스트 장애 조치를 실행합니다.

- 장애 조치(failover) 후 RDP를 사용하여 Azure VM에 연결하려면 [연결을 준비](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)합니다.
 - 완벽하게 테스트하려면 테스트 환경에 Active Directory 및 DNS 복사본이 필요합니다. [자세히 알아보기](site-recovery-active-directory.md#test-failover-considerations).
 - 테스트 장애 조치(failover)에 대한 전체 정보는 [이 문서](site-recovery-test-failover-to-azure.md)를 읽어보세요.
- 시작하기 전에 간단한 동영상 개요를 보세요.

     
     >[!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video4-Recovery-Plan-DR-Drill-and-Failover/player]

이제 장애 조치(Failover)를 실행합니다.

1. 단일 컴퓨터를 장애 조치(failover)하려면 **설정** > **복제된 항목**에서 컴퓨터 > **+테스트 장애 조치(failover)** 아이콘을 클릭합니다.

    ![테스트 장애 조치(Failover)](./media/physical-walkthrough-test-failover/test-failover.png)

2. 복구 계획을 장애 조치(Failover)하려면 **설정** > **복구 계획**에서 계획을 마우스 오른쪽 버튼으로 클릭하고 **테스트 장애 조치(Failover)**를 클릭합니다. 복구 계획을 만들려면 [다음 지침을 따릅니다](site-recovery-create-recovery-plans.md).  

3. **테스트 장애 조치(failover)**에서 장애 조치(failover)가 발생한 후에 Azure VM이 연결될 Azure 네트워크를 선택합니다.

4. **확인** 을 클릭하여 장애 조치(Failover)를 시작합니다. 컴퓨터를 클릭하여 속성을 열거나 자격 증명 모음 이름 > **설정** > **작업** > **Site Recovery 작업**의 **테스트 장애 조치(failover)** 작업에서 진행률을 추적할 수 있습니다.

5. 또한 장애 조치(failover)가 완료된 후 Azure Portal > **Virtual Machines**에 Azure VM 복제본이 나타나는 것을 확인할 수 있습니다. VM의 크기가 적당하고, 올바른 네트워크에 연결되었고, 실행 중인지 확인해야 합니다.

6. 장애 조치(failover) 후 연결을 준비하는 경우 Azure VM에 연결할 수 있어야 합니다.

### <a name="delete-test-failover-vms"></a>테스트 장애 조치 VM 삭제

1. 완료하면 복구 계획 또는 컴퓨터에서 **테스트 장애 조치 정리**를 클릭합니다.
2. **참고**에서 테스트 장애 조치와 관련된 모든 관측 내용을 기록하고 저장합니다.
3. 정리 작업으로 테스트 장애 조치 중에 생성된 Azure VM이 삭제됩니다.

## <a name="summary"></a>요약

테스트 장애 조치를 성공적으로 완료했으면 물리적 서버가 복제되고 Azure에 장애 조치할 수 있음이 확인됩니다. 이제 조직의 요구 사항에 따라 장애 조치를 실행할 수 있습니다. 

현재는 Azure에서 물리적 서버로 장애 복구할 수 없음에 유의합니다. VMware VM으로 장애 복구해야 합니다. 따라서 장애 복구를 수행하려면 온-프레미스 VMware 인프라가 필요합니다. Azure VM을 VMware으로 장애 복구에 대해 [자세히 알아보세요](site-recovery-failback-azure-to-vmware.md).


## <a name="next-steps"></a>다음 단계

- 필요에 따라 [장애 조치를 실행](site-recovery-failover.md)합니다.
