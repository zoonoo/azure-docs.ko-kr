---
title: "Azure Site Recovery를 사용하여 보조 사이트로 Hyper-V를 복제할 수 있도록 설정 | Microsoft 문서"
description: "Azure Site Recovery를 사용하여 보조 System Center VMM 사이트로 복제하려는 Hyper-V VM에 대해 복제를 사용하도록 설정하는 방법을 설명합니다."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: d8782d14-9fef-4396-8912-ff945efc851b
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: fff84ee45818e4699df380e1536f71b2a4003c71
ms.openlocfilehash: 6673d192dbc18bfc955d9e7e3c55893512511ffb
ms.contentlocale: ko-kr
ms.lasthandoff: 08/01/2017

---
# <a name="step-9-enable-replication-to-a-secondary-site-for-hyper-v-vms"></a>9단계: Hyper-V VM을 보조 사이트로 복제할 수 있도록 설정


복제 정책을 설정한 후 이 문서의 내용을 참조하여 [Azure Site Recovery](site-recovery-overview.md)를 사용해 온-프레미스 Hyper-V VM(가상 컴퓨터)을 보조 System Center Virtual Machine Manager(VMM) 사이트로 복제하도록 설정할 수 있습니다.

이 문서를 읽은 후에 하단 또는 [Azure Recovery Services 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)에서 의견을 게시합니다.



## <a name="select-vms-to-replicate"></a>복제할 VM 선택

1. **2단계: 응용 프로그램 복제** > **원본**을 클릭합니다. 

    ![복제 활성화](./media/vmm-to-vmm-walkthrough-enable-replication/enable-replication1.png)

2. **원본**에서 복제하려는 Hyper-V 호스트가 배치되는 VMM 서버 및 클라우드를 선택합니다. 그런 후 **OK**를 클릭합니다.

    ![복제 활성화](./media/vmm-to-vmm-walkthrough-enable-replication/enable-replication2.png)
3. **대상**에서 보조 VMM 서버 및 클라우드를 확인합니다.
4. **가상 컴퓨터**의 목록에서 보호하려는 VM을 선택합니다.

    ![가상 컴퓨터 보호 사용](./media/vmm-to-vmm-walkthrough-enable-replication/enable-replication5.png)

**작업** > **Site Recovery 작업**에서 **보호 사용** 작업의 진행률을 추적할 수 있습니다. **보호 완료** 작업이 완료되고 나면 초기 복제가 완료되며 가상 컴퓨터는 장애 조치(failover)를 수행할 준비가 됩니다.

다음 사항에 유의하세요.

- 또한 VMM 콘솔에서 가상 컴퓨터에 대해 보호를 사용하도록 설정할 수 있습니다. 가상 컴퓨터 속성의 **Azure Site Recovery** 탭에 있는 도구 모음에서 **보호 사용**을 클릭합니다.
- 복제를 사용하도록 설정하면 **복제된 항목**에서 VM에 대한 속성을 볼 수 있습니다. **Essentials** 대시보드에서 VM 및 해당 상태에 대한 복제 정책 정보를 볼 수 있습니다. 자세한 내용을 보려면 **속성** 을 클릭합니다.

## <a name="onboard-existing-vms"></a>기존 VM 온보딩

Hyper-V 복제본을 사용하여 복제 중인 기존 가상 컴퓨터가 VMM에 있는 경우 Azure Site Recovery 복제를 위해 다음과 같이 등록할 수 있습니다.

1. 기존 VM을 호스트하는 Hyper-V 서버가 기본 클라우드에 있고 복제본 가상 컴퓨터를 호스트하는 Hyper-V 서버가 보조 클라우드에 있는지 확인합니다.
2. 복제 정책이 기본 VMM 클라우드에 대해 구성되어 있는지 확인합니다.
3. 기본 가상 컴퓨터에 대해 보호를 사용하도록 설정합니다. Azure Site Recovery 및 VMM에서 동일한 복제본 호스트와 가상 컴퓨터가 검색되는지 확인하고, Azure Site Recovery에서 지정된 설정을 사용하여 복제를 다시 사용하고 설정합니다.


## <a name="next-steps"></a>다음 단계

[10단계: 테스트 장애 조치(failover) 실행](vmm-to-vmm-walkthrough-test-failover.md)으로 이동합니다.

