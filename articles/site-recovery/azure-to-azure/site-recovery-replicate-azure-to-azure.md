---
title: "Azure Site Recovery를 사용하여 보조 지역에 Azure VM 복제 | Microsoft Docs"
description: "이 문서에서는 Azure Site Recovery 서비스를 사용하여 한 Azure 지역에서 실행 중인 Azure VM을 다른 지역에 복제하는 방법을 설명합니다."
services: site-recovery
documentationcenter: 
author: asgang
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/29/2017
ms.author: asgang
ms.openlocfilehash: 114390712f5e5bf62ec515db62469e09361b8f85
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2017
---
# <a name="replicate-azure-virtual-machines-to-another-azure-region"></a>다른 Azure 지역으로 Azure 가상 컴퓨터 복제


이 문서에서는 Azure Site Recovery 서비스를 사용하여 한 Azure 지역의 Azure VM(가상 머신)을 보조 Azure 지역에 복제하는 방법을 설명합니다.

>[!NOTE]
>
> Site Recovery를 사용하는 Azure VM 복제는 현재 미리 보기로 제공됩니다.

## <a name="prerequisites"></a>필수 조건

* Recovery Services 자격 증명 모음이 있어야 합니다. VM을 복제할 대상 지역에 자격 증명 모음을 만드는 것이 좋습니다.
* NSG(네트워크 보안 그룹) 규칙 또는 방화벽 프록시를 사용하여 Azure VM의 아웃바운드 인터넷 연결에 대한 액세스를 제어하는 경우 필요한 URL 또는 IP를 허용해야 합니다. [자세히 알아봅니다](./site-recovery-azure-to-azure-networking-guidance.md).
* 온-프레미스와 Azure의 원본 위치 간에 ExpressRoute 또는 VPN 연결을 사용하는 경우 [설정하는 방법을 알아보세요](site-recovery-azure-to-azure-networking-guidance.md#guidelines-for-existing-azure-to-on-premises-expressroutevpn-configuration).
* Azure에 VM을 복제할 수 있도록 하려면 Azure 사용자 계정에 [특정 권한](../site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)이 있어야 합니다.
재해 복구 지역으로 사용할 대상 위치에 VM을 만들 수 있도록 Azure 구독을 활성화해야 합니다. 필요한 할당량을 사용하려면 지원 팀에 문의하세요.

## <a name="enable-replication"></a>복제 활성화

이 절차에서 동아시아는 원본 위치로 동남 아시아는 대상으로 사용됩니다.

1. 자격 증명 모음에서 **+복제**를 클릭하여 가상 컴퓨터에 대해 복제를 사용하도록 설정합니다.
2. **원본:**이 **Azure**로 설정되어 있는지 확인합니다.
3. **원본 위치**를 동아시아로 설정합니다.
4. **배포 모델**에서 **클래식** 또는 **리소스 관리자**를 선택합니다.
5. **리소스 그룹**에서 원본 VM이 속하는 그룹을 선택합니다. 선택한 리소스 그룹에 속하는 모든 VM이 나열됩니다.

    ![복제 활성화](./media/site-recovery-replicate-azure-to-azure/enabledrwizard1.png)

6. **Virtual Machines > 가상 머신 선택**에서 복제하려는 각 VM을 클릭하여 선택합니다. 복제를 활성화할 수 있는 컴퓨터만 선택할 수 있습니다. 그런 후 **OK**를 클릭합니다.

    ![복제 활성화](./media/site-recovery-replicate-azure-to-azure/virtualmachine_selection.png)
    
7. **설정** > **대상 위치**에서 원본 VM 데이터를 복제할 위치를 지정합니다. Site Recovery는 선택한 VM의 지역에 따라 적절한 대상 지역의 목록을 제공합니다.
8. Site Recovery에서 기본 대상 설정이 수행됩니다. 이것은 필요에 따라 수정할 수 있습니다.

    - **대상 리소스 그룹**. 기본적으로 Site Recovery는 "asr" 접미사를 사용하여 대상 지역에 새 리소스 그룹을 만듭니다. 만든 리소스 그룹이 이미 있는 경우 다시 사용됩니다.
    - **대상 가상 네트워크**. 기본적으로 Site Recovery는 "asr" 접미사를 사용하여 대상 지역에 새 가상 네트워크를 만듭니다. 이 네트워크는 원본 네트워크에 매핑됩니다. [자세히 알아봅니다](site-recovery-network-mapping-azure-to-azure.md) 를 확인해 보세요.
    - **대상 Storage 계정**. 기본적으로 Site Recovery는 원본 VM 저장소 구성과 일치하는 새 대상 저장소 계정을 만듭니다. 만든 계정이 이미 있는 경우 다시 사용됩니다.
    - **캐시 저장소 계정**. Azure Site Recovery는 추가 캐시 저장소 계정, 원본 영역을 만듭니다. 원본 VM의 모든 변경 내용은 추적되고 대상 위치로 복제되기 전에 캐시 저장소 계정으로 전송됩니다.
    - **가용성 집합**. 기본적으로 Site Recovery는 "asr" 접미사를 사용하여 대상 지역에 새 가용성 집합을 만듭니다. 만든 집합이 이미 있는 경우 다시 사용됩니다.
    - **복제 정책**. Site Recovery는 복구 지점 보존 기록 및 앱 일치 스냅숏 빈도 대한 설정을 정의합니다. 기본적으로 Site Recovery는 복구 지점 보존의 경우 기본 '24시간' 설정으로, 앱 일치 스냅숏 빈도의 경우 '60분' 설정으로 새 복제 정책을 만듭니다.

    ![복제 활성화](./media/site-recovery-replicate-azure-to-azure/enabledrwizard3.PNG)
9. **복제 사용**을 클릭하여 VM 보호를 시작합니다.

## <a name="customize-target-resources"></a>대상 리소스 사용자 지정

1. 다음 대상 기본값을 수정합니다.

    - **대상 리소스 그룹**. 구독 내 대상 위치의 모른 리소스 그룹 목록에서 리소스 그룹을 선택합니다.
    - **대상 가상 네트워크**. 대상 위치의 모든 가상 네트워크 목록에서 선택합니다.
    - **가용성 집합** 원본 영역의 집합에 있는 VM에만 가용성 집합 설정을 추가할 수 있습니다.
    - **대상 저장소 계정**: 사용 가능한 계정을 추가합니다.

        ![복제 활성화](./media/site-recovery-replicate-azure-to-azure/customize.PNG)

2. **대상 리소스 만들기** > **복제 사용**을 클릭합니다. 초기 복제 중 VM 상태가 새로 고쳐질 때까지 시간이 걸릴 수 있습니다. 최신 상태를 가져오려면 **새로 고침**을 클릭합니다.

    ![복제 활성화](./media/site-recovery-replicate-azure-to-azure/replicateditems.PNG)
    
3. VM이 보호된 후에는 **복제된 항목**에서 VM 상태를 검사합니다.



## <a name="next-steps"></a>다음 단계
테스트 장애 조치(Failover)를 실행하는 방법을 [알아봅니다](../azure-to-azure-tutorial-dr-drill.md).

