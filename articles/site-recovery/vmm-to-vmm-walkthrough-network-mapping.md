---
title: "Azure Site Recovery를 사용하여 Hyper-V VM 복제용 네트워크를 보조 사이트에 매핑 | Microsoft 문서"
description: "Azure Site Recovery를 사용하여 보조 VMM 사이트로 Hyper-V VM을 복제할 때 네트워크를 매핑하는 방법을 설명합니다."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 461b7c1c-ef61-4005-aeec-2324e727a3d0
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: fff84ee45818e4699df380e1536f71b2a4003c71
ms.openlocfilehash: 606e2d3d0e31b9d80200105e812f9d7bbbcf939c
ms.contentlocale: ko-kr
ms.lasthandoff: 08/01/2017

---
# <a name="step-7-map-networks-for-hyper-v-vm-replication-to-a-secondary-site"></a>7단계: Hyper-V VM 복제용 네트워크를 보조 사이트에 매핑


Hyper-V VM(가상 컴퓨터)을 보조 System Center Virtual Machine Manager(VMM) 사이트로 복제하기 위한 [원본 및 대상 설정](vmm-to-vmm-walkthrough-source-target.md)을 지정한 후 이 문서의 내용을 참조하여 [Azure Site Recovery](site-recovery-overview.md)를 사용해 보조 사이트로의 Hyper-V VM(가상 컴퓨터) 복제를 위한 네트워크 매핑을 구성할 수 있습니다.

이 문서를 읽은 후에 하단 또는 [Azure Recovery Services 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)에서 의견을 게시합니다.


## <a name="before-you-start"></a>시작하기 전에

- 시작하기 전에 [네트워크 매핑](vmm-to-vmm-walkthrough-network.md#network-mapping-overview)에 대해 알아봅니다.
- VMM 서버의 가상 컴퓨터가 VM 네트워크에 연결되어 있는지 확인합니다.

## <a name="configure-network-mapping"></a>네트워크 매핑 구성

1. **네트워크 매핑** > **네트워크 매핑**에서 **+네트워크 매핑**을 클릭합니다.
2. **네트워크 매핑 추가** 탭에서 소스 및 대상 VMM 서버를 선택합니다. VMM 서버와 연결된 VM 네트워크가 검색됩니다.
3. **소스 네트워크**에서 기본 VMM 서버에 연결된 VM 네트워크 목록에서 사용하려는 네트워크를 선택합니다.
4. **대상 네트워크**에서 보조 VMM 서버에서 사용하려는 네트워크를 선택합니다. 그런 후 **OK**를 클릭합니다.

    ![네트워크 매핑](./media/vmm-to-vmm-walkthrough-network-mapping/network-mapping2.png)

네트워크 매핑이 시작되면 다음과 같은 동작이 발생합니다.

* 원본 VM 네트워크에 해당하는 기존 복제본 가상 컴퓨터는 모두 대상 VM 네트워크에 연결됩니다.
* 원본 VM 네트워크에 연결된 새 가상 컴퓨터는 복제 후 매핑된 대상 네트워크에 연결됩니다.
* 새 네트워크로 기존 매핑을 수정하면 복제본 가상 컴퓨터가 새 설정을 사용하여 연결됩니다.
* 대상 네트워크에 여러 서브넷이 있고 이 서브넷 중 하나의 이름이 원본 가상 컴퓨터가 있는 서브넷과 같으면 복제 가상 컴퓨터가 장애 조치(failover) 후에 대상 서브넷에 연결됩니다. 일치하는 이름을 가진 대상 서브넷이 없으면 가상 컴퓨터가 네트워크의 첫 번째 서브넷에 연결됩니다.



## <a name="next-steps"></a>다음 단계

[8단계: 복제 정책 구성](vmm-to-vmm-walkthrough-replication.md)으로 이동합니다.
