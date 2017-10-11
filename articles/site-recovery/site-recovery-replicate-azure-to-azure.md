---
title: "응용 프로그램 복제(Azure에서 Azure로) | Microsoft Docs"
description: "이 문서에서는 하나의 Azure 지역에서 실행 중인 가상 컴퓨터의 복제를 Azure의 다른 지역으로 설정하는 방법을 설명합니다."
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
ms.date: 5/22/2017
ms.author: asgang
ms.openlocfilehash: f9f97cf840b722c8cfee169dd1640e0682f287ff
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2017
---
# <a name="replicate-azure-virtual-machines-to-another-azure-region"></a>다른 Azure 지역으로 Azure 가상 컴퓨터 복제



>[!NOTE]
>
> Azure 가상 컴퓨터에 대한 Site Recovery 복제는 현재 미리 보기로 제공됩니다.

이 문서에서는 하나의 Azure 지역에서 실행 중인 가상 컴퓨터의 복제를 다른 Azure 지역으로 설정하는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 조건

* 이 문서에서는 Site Recovery 및 Recovery Services 자격 증명 모음에 대해 이미 알고 있다고 가정합니다. 사전에 만들어진 'Recovery Services 자격 증명 모음'이 있어야 합니다.

    >[!NOTE]
    >
    > 'Recovery Services 자격 증명 모음'은 VM을 복제할 위치에 만드는 것이 좋습니다. 예를 들어 대상 위치가 '미국 중부'이면 '미국 중부'에 자격 증명 모음을 만듭니다.

* NSG(네트워크 보안 그룹) 규칙 또는 방화벽 프록시를 사용하여 Azure VM의 아웃바운드 인터넷 연결에 대한 액세스를 제어하는 경우 필요한 URL 또는 IP가 허용 목록에 있는지 확인합니다. 자세한 내용은 [네트워킹 지침 문서](./site-recovery-azure-to-azure-networking-guidance.md)를 참조하세요.

* Azure의 원본 위치와 온-프레미스 간에 ExpressRoute 또는 VPN 연결이 있는 경우 [온-프레미스 ExpressRoute/VPN 구성에 대한 Azure Site Recovery 고려 사항](site-recovery-azure-to-azure-networking-guidance.md#guidelines-for-existing-azure-to-on-premises-expressroutevpn-configuration) 문서에 따릅니다.

* Azure 가상 컴퓨터 복제를 사용하려면 Azure 사용자 계정에 특정 [사용 권한](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)이 있어야 합니다.

* DR 지역으로 사용할 대상 위치에서 VM을 만들려면 Azure 구독을 사용하도록 설정해야 합니다. 지원 팀에 문의하여 필요한 할당량을 사용할 수 있습니다.

## <a name="enable-replication-from-azure-site-recovery-vault"></a>Azure Site Recovery 자격 증명 모음에서 복제 사용
이 설명에서는 '동아시아' Azure 위치에서 실행 중인 VM을 '동남 아시아' 위치로 복제합니다. 단계는 다음과 같습니다.

 자격 증명 모음에서 **+복제**를 클릭하여 가상 컴퓨터에 대해 복제를 사용하도록 설정합니다.

1. **원본:** 이 경우 **Azure**인 컴퓨터의 원점을 가리킵니다.

2. **원본 위치:** 가상 컴퓨터를 보호할 Azure 지역입니다. 이 설명에서는 원본 위치가 '동아시아'입니다.

3. **배포 모델:** 원본 컴퓨터의 Azure 배포 모델을 가리킵니다. 클래식 또는 리소스 관리자를 선택할 수 있으며 특정 모델에 속한 컴퓨터는 다음 단계에서 보호를 위해 나열됩니다.

      >[!NOTE]
      >
      > 클래식 가상 컴퓨터를 복제하면 클래식 가상 컴퓨터로만 복구할 수 있습니다. Resource Manager 가상 컴퓨터로 복구할 수 없습니다.

4. **리소스 그룹:** 원본 가상 컴퓨터가 속해 있는 리소스 그룹입니다. 선택한 리소스 그룹 아래의 모든 VM은 다음 단계에서 보호를 위해 나열됩니다.

    ![복제 활성화](./media/site-recovery-replicate-azure-to-azure/enabledrwizard1.png)

**Virtual Machines > 가상 컴퓨터 선택**에서 복제하려는 각 컴퓨터를 클릭하여 선택합니다. 복제를 활성화할 수 있는 컴퓨터만 선택할 수 있습니다. 그런 후 확인을 클릭합니다.
    ![복제 활성화](./media/site-recovery-replicate-azure-to-azure/virtualmachine_selection.png)


설정 섹션에서 대상 사이트 속성을 구성할 수 있습니다.

1. **대상 위치:** 원본 가상 컴퓨터 데이터가 복제될 위치입니다. 선택한 컴퓨터 위치에 따라 Site Recovery에서 적합한 대상 지역 목록을 제공합니다.

    > [!TIP]
    > 대상 위치를 복구 서비스 자격 증명 모음과 동일하게 유지하는 것이 좋습니다.

2. **대상 리소스 그룹:** 모든 복제된 가상 컴퓨터가 속하게 될 리소스 그룹입니다. 기본적으로 ASR은 이름에 "asr" 접미사가 있는 대상 지역에 새 리소스 그룹을 만듭니다. ASR에서 만든 리소스 그룹이 이미 있는 경우 해당 그룹이 재사용됩니다. 아래 섹션에 표시된 대로 사용자 지정하도록 선택할 수도 있습니다.    
3. **대상 가상 네트워크:** 기본적으로 ASR은 이름에 "asr" 접미사가 있는 대상 지역에 새 가상 네트워크를 만듭니다. 이 가상 네트워크는 원본 네트워크에 매핑되고 이후의 모든 보호를 위해 사용됩니다.

    > [!NOTE]
    > 네트워크 매핑에 대해 더 자세히 알아보려면 [네트워킹 세부 정보를 확인](site-recovery-network-mapping-azure-to-azure.md)하세요.

4. **대상 저장소 계정:** 기본적으로 ASR은 소스 VM 저장소 구성을 모방하는 새 대상 저장소 계정을 만듭니다. ASR에서 만든 저장소 계정이 이미 있는 경우 해당 계정이 재사용됩니다.

5. **캐시 저장소 계정:** ASR은 소스 지역에 캐시 저장소로 불리는 추가 저장소 계정이 있어야 합니다. 원본 VM에서 발생하는 모든 변경 내용이 대상 위치로 복제되기 전에 추적되고 캐시 저장소 계정으로 전송됩니다.

6. **가용성 집합:** 기본적으로 ASR은 이름에 "asr" 접미사가 있는 대상 지역에 새 가용성 집합을 만듭니다. ASR에서 만든 가용성 집합이 이미 있는 경우 해당 집합이 재사용됩니다.

7.  **복제 정책:** 복구 지점 보존 기록 및 앱 일치 스냅숏 빈도 대한 설정을 정의합니다. 기본적으로 ASR은 복구 지점 보존의 경우 기본'24시간' 설정으로, 앱 일치 스냅숏 빈도의 경우 '60분' 설정으로 새 복제 정책을 만듭니다.

    ![복제 활성화](./media/site-recovery-replicate-azure-to-azure/enabledrwizard3.PNG)

## <a name="customize-target-resources"></a>대상 리소스 사용자 지정

ASR에서 사용하는 기본값을 변경하려면 필요에 따라 설정을 변경할 수 있습니다.

1. **사용자 지정:** ASR에서 사용하는 기본값을 변경하려면 클릭합니다.

2. **대상 리소스 그룹:** 구독 내 대상 위치에 있는 모든 리소스 그룹의 목록에서 리소스 그룹을 선택할 수 있습니다.

3. **대상 가상 네트워크:** 대상 위치에서 모든 가상 네트워크 목록을 찾을 수 있습니다.

4. **가용성 집합:** 가용성 집합 설정은 소스 영역에서 가용성의 일부인 가상 컴퓨터에만 추가할 수 있습니다.

5. **대상 저장소 계정:**

![복제를 사용하도록 설정](./media/site-recovery-replicate-azure-to-azure/customize.PNG) **대상 리소스 만들기**를 클릭하고 복제를 사용하도록 설정합니다.


가상 컴퓨터가 보호되면 **복제 항목** 아래 VM 상태의 상태를 확인할 수 있습니다.

>[!NOTE]
>초기 복제 기간 중 상태를 새로 고치는 데 시간이 걸릴 수도 있고 일정 시간 동안 진행률이 표시되지 않을 수도 있습니다. 최신 상태를 가져오려면 블레이드 맨 위에 있는 [새로 고침] 단추를 클릭합니다.
>

![복제 활성화](./media/site-recovery-replicate-azure-to-azure/replicateditems.PNG)


## <a name="next-steps"></a>다음 단계
- 테스트 장애 조치(failover) 실행에 대해 [자세히 알아보세요](site-recovery-test-failover-to-azure.md).
- 여러 장애 조치 유형 및 장애 조치 실행 방법에 대해 [자세히 알아보세요](site-recovery-failover.md).
- [복구 계획을 사용](site-recovery-create-recovery-plans.md)하여 RTO를 줄이는 방법에 대해 자세히 알아보세요.
- 장애 조치(failover) 후에 [Azure VM을 다시 보호](site-recovery-how-to-reprotect.md)하는 방법에 대해 자세히 알아보세요.
