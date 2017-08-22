---
title: "Azure Site Recovery를 사용하여 VMM 클라우드의 Hyper-V VM을 Azure로 복제하기 위한 네트워크 매핑 구성"
description: "이 문서에서는 Azure Site Recovery를 사용하여 VMM 클라우드의 Hyper-V VM을 Azure로 복제할 때 네트워크 매핑을 구성하는 방법을 설명합니다."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: tysonn
ms.assetid: 8e7d868e-00f3-4e8b-9a9e-f23365abf6ac
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: 74b75232b4b1c14dbb81151cdab5856a1e4da28c
ms.openlocfilehash: ed6f73d8baea5af0d2aa5f0ae885f305911ccc82
ms.contentlocale: ko-kr
ms.lasthandoff: 07/26/2017

---
# <a name="step-9-configure-network-mapping-for-hyper-v-replication-with-vmm-to-azure"></a>9단계: Azure로의 Hyper-V 복제(VMM 포함)를 위한 네트워크 매핑 구성

[소스 및 대상 복제 설정](vmm-to-azure-walkthrough-source-target.md)을 지정한 후 이 문서의 내용을 참조하여 온-프레미스 VMM VM 네트워크와 Azure 네트워크 간을 매핑하는 네트워크 매핑을 구성할 수 있습니다.

이 문서의 하단 또는 [Azure Recovery Services 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)에서 의견이나 질문을 게시합니다.

## <a name="before-you-start"></a>시작하기 전에

- [네트워크 매핑](vmm-to-azure-walkthrough-network.md#network-mapping-for-replication-to-azure)에 대해 알아봅니다.
- [네트워크 매핑용 VMM을 준비합니다](vmm-to-azure-walkthrough-network.md#prepare-vmm-for-network-mapping). 
- VMM 서버의 가상 컴퓨터가 VM 네트워크에 연결되었으며 Azure 가상 네트워크를 하나 이상 만들었는지 확인합니다. 단일 Azure 네트워크에 여러 개의 VM 네트워크를 매핑할 수 있습니다.

## <a name="configure-mapping"></a>매핑 구성

다음과 같이 매핑을 구성합니다.

1. **Site Recovery 인프라** > **네트워크 매핑** > **네트워크 매핑**에서 **+네트워크 매핑** 아이콘을 클릭합니다.

    ![네트워크 매핑](./media/vmm-to-azure-walkthrough-network-mapping/network-mapping1.png)
2. **네트워크 매핑 추가**에서 원본 VMM 서버를 선택하고, 대상으로 **Azure**를 선택합니다.
3. 장애 조치(failover) 후 구독과 배포 모델을 확인합니다.
4. **원본 네트워크**에서 VMM 서버에 연결된 목록에서 매핑하려는 원본 온-프레미스 VM 네트워크를 선택합니다.
5. **대상 네트워크**에서 Azure 네트워크를 만들 때 배치될 Azure VM 복제본을 선택합니다. 그런 후 **OK**를 클릭합니다.

    ![네트워크 매핑](./media/vmm-to-azure-walkthrough-network-mapping/network-mapping2.png)

네트워크 매핑이 시작되면 다음과 같은 동작이 발생합니다.

* 원본 VM 네트워크에 있는 기존 VM은 매핑이 시작될 때 대상 네트워크에 연결됩니다. 원본 VM 네트워크에 연결된 새 VM은 복제된 후에 매핑된 Azure 네트워크에 연결됩니다.
* 기존 네트워크 매핑을 수정하면 복제본 가상 컴퓨터가 새 설정을 사용하여 연결됩니다.
* 대상 네트워크에 여러 서브넷이 있고 이 서브넷 중 하나의 이름이 원본 가상 컴퓨터가 있는 서브넷과 같으면 복제 가상 컴퓨터가 장애 조치(failover) 후에 대상 서브넷에 연결됩니다.
* 일치하는 이름을 가진 대상 서브넷이 없으면 가상 컴퓨터가 네트워크의 첫 번째 서브넷에 연결됩니다.



## <a name="next-steps"></a>다음 단계

[10단계: 복제 정책 만들기](vmm-to-azure-walkthrough-replication.md)로 이동합니다.

