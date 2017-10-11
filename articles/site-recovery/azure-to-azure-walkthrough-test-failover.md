---
title: "Azure Site Recovery를 사용하여 Azure VM 복제를 위한 테스트 장애 조치 실행 | Microsoft Docs"
description: "Azure Site Recovery 서비스를 사용하여 다른 Azure 지역에 Azure VM을 복제하기 위한 테스트 장애 조치를 실행하는 데 필요한 단계를 요약하고 있습니다."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmon
editor: 
ms.assetid: e15c1b0c-5d75-4fdf-acb0-e61def9e9339
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2017
ms.author: raynew
ms.openlocfilehash: 8babb0d016729f318442af93596d206c38d91206
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/03/2017
---
# <a name="step-6-run-a-test-failover-for-azure-vm-replication"></a>6단계: Azure VM 복제를 위한 테스트 장애 조치 실행

Azure VM(가상 컴퓨터)에 대한 복제를 활성화한 후에 Azure Portal에서 [Azure Site Recovery](site-recovery-overview.md) 서비스를 사용하여 이 문서의 단계에 따라 한 Azure 지역에서 다른 Azure 지역으로 테스트 장애 조치를 실행합니다.

- 이 문서를 완료하면 테스트 장애 조치를 통해 하나 이상의 Azure VM에서 보조 Azure 지역으로 장애 조치할 수 있는지 확인해야 합니다. 
- 이 문서의 하단에서 의견을 게시하거나 [Azure Recovery Services 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)에서 질문하세요.

>[!NOTE]
>
> Azure VM 복제는 현재 미리 보기로 제공됩니다.


## <a name="before-you-start"></a>시작하기 전에

- 테스트 장애 조치를 실행하기 전에 VM 속성을 확인하고 필요한 사항을 변경하는 것이 좋습니다. **복제 항목**에서 VM 속성에 액세스할 수 있습니다. **Essentials** 블레이드는 컴퓨터 설정 및 상태에 대한 정보를 표시합니다.
- 프로덕션 장애 조치에 대해 설정된 네트워크(기본 또는 사용자 지정)가 아니라 테스트 장애 조치에 대한 별도의 Azure VM 네트워크를 사용하는 것이 좋습니다.
- 테스트 장애 조치는 Azure VM(및 해당 저장소)에서 보조 Azure 지역으로 장애 조치하지 않습니다. 종속된 앱이나 리소스는 복제하지 않습니다. 장애 조치된 VM에서 실행되는 앱이 Active Directory 또는 DNS와 같은 다른 리소스에 종속되어 있는 경우 보조 지역에서 아직 사용할 수 없는 앱도 복제해야 합니다. [자세히 알아보기](site-recovery-test-failover-to-azure.md#prepare-active-directory-and-dns)
- 온-프레미스 사이트에서 장애 조치한 후 복제된 VM에 액세스하려면 이러한 VM에 [연결하도록 준비](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)해야 합니다.

## <a name="run-a-test-failover"></a>테스트 장애 조치(Failover) 실행

1. **설정** > **복제된 항목**에서 VM **+테스트 장애 조치** 아이콘을 클릭합니다. 

2. **테스트 장애 조치**에서 장애 조치에 사용할 복구 지점을 선택합니다.

    - **가장 최근에 처리됨**: VM을 Site Recovery 서비스에서 처리된 최신 복구 지점으로 장애 조치합니다. 타임스탬프가 표시됩니다. 이 옵션을 사용하면 데이터를 처리하는 데 시간을 소비하지 않으므로 낮은 RTO(복구 시간 목표)가 제공됩니다.
    - **최신 앱 일치**: 모든 VM을 최신 앱 일치 복구 지점으로 장애 조치합니다. 타임스탬프가 표시됩니다. 
    - **사용자 지정**: 복구 지점을 선택합니다.
 
3. 장애 조치가 발생한 후에 보조 지역의 Azure VM을 연결할 대상 Azure 가상 네트워크를 선택합니다.
4. 장애 조치를 시작하려면 **확인**을 클릭합니다. 진행률을 추적하려면 VM을 클릭하여 해당 속성을 엽니다. 또는 자격 증명 모음 이름 > **설정** > **작업** > **Site Recovery 작업**에서 **테스트 장애 조치** 작업을 클릭하면 됩니다.
5. 장애 조치가 완료되면 Azure Portal > **Virtual Machines**에서 Azure VM 복제본이 표시됩니다. VM의 크기가 적당하고, 올바른 네트워크에 연결되었고, 실행 중인지 확인합니다.
6. 테스트 장애 조치 중에 만든 VM을 삭제하려면 복제된 항목 또는 복구 계획에서 **테스트 장애 조치 정리**를 클릭합니다. **참고**에서 테스트 장애 조치와 관련된 모든 관측 내용을 기록하고 저장합니다. 

테스트 장애 조치(failover)에 대해 [자세히 알아보세요](site-recovery-test-failover-to-azure.md).

## <a name="next-steps"></a>다음 단계

장애 조치를 테스트한 후에 이 연습이 완료되었습니다. 이제 프로덕션에서 장애 조치를 실행하는 방법을 자세히 알아봅니다.

- 여러 장애 조치 유형 및 장애 조치 실행 방법에 대해 [자세히 알아보세요](site-recovery-failover.md).
- [복구 계획을 사용](site-recovery-create-recovery-plans.md)하여 여러 VM을 장애 조치하는 방법을 자세히 알아봅니다.
- [복구 계획 사용](site-recovery-create-recovery-plans.md)을 자세히 알아봅니다.
- 장애 조치(failover) 후에 [Azure VM을 다시 보호](site-recovery-how-to-reprotect.md)하는 방법에 대해 자세히 알아보세요.

