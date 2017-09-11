---
title: "Azure Site Recovery를 사용하여 Hyper-V VM(VMM 포함)을 Azure로 복제하기 위한 복제 정책 설정 | Microsoft 문서"
description: "Azure Site Recovery를 사용하여 Hyper-V VM(VMM 포함)을 Azure로 복제하기 위한 복제 정책을 설정하는 방법을 설명합니다."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: ee808b20-324b-4198-b831-edb65b95e8b7
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 592e1c3f647e5b1f1d9aa776657e8f89b60349e1
ms.contentlocale: ko-kr
ms.lasthandoff: 08/21/2017

---
# <a name="step-10-set-up-a-replication-policy-for-hyper-v-vm-replication-with-vmm-to-azure"></a>10단계: Hyper-V VM(VMM 포함)을 Azure로 복제하기 위한 복제 정책 설정


[네트워크 매핑](vmm-to-azure-walkthrough-network-mapping.md)을 설정한 후 이 문서의 내용을 참조하여 Azure Portal에서 [Azure Site Recovery](site-recovery-overview.md) 서비스를 사용해 System Center Virtual Machine Manager(VMM) 클라우드에서 관리되는 온-프레미스 Hyper-V 가상 컴퓨터를 Azure로 복제하기 위한 복제 정책을 구성할 수 있습니다.

이 문서를 읽은 후에 하단 또는 [Azure Recovery Services 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)에서 의견을 게시합니다.



## <a name="create-a-policy"></a>정책 만들기

1. 새 복제 정책을 만들려면 **인프라 준비** > **복제 설정** > **+만들기 및 연결**을 차례로 클릭합니다.

    ![네트워크](./media/vmm-to-azure-walkthrough-replication/gs-replication.png)
2. **만들기 및 연결 정책**에서 정책 이름을 지정합니다.
3. **복사 빈도**에서 초기 복제 후 델타 데이터를 복제할 빈도(30초 마다, 5분마다 또는 15분마다)를 지정합니다.

    > [!NOTE]
    >  Premium Storage로 복제하는 경우 30초 빈도가 지원되지 않습니다. Premium Storage에서 지원하는 blob당 스냅숏 수(100)에 따라 제한이 결정됩니다. [자세히 알아보기](../storage/common/storage-premium-storage.md#snapshots-and-copy-blob)

4. **복구 지점 보존**에서 각 복구 지점에 대해 지속될 보존 시간을 시간 단위로 지정합니다. 보호된 컴퓨터를 이 기간 내의 모든 지점으로 복구할 수 있습니다.
5. **응용 프로그램 일치 스냅숏 빈도**에서 응용 프로그램 일치 스냅숏이 포함된 복구 지점을 만드는 빈도(1-12시간)를 지정합니다. Hyper-V는 두 가지 유형의 스냅숏, 즉 전체 가상 컴퓨터의 증분 스냅숏을 제공하는 표준 스냅숏과 가상 컴퓨터 내 응용 프로그램 데이터의 지정 시간 스냅숏을 만드는 응용 프로그램 일치 스냅숏을 사용합니다. 응용 프로그램 일치 스냅숏은 VSS(볼륨 섀도 복사본 서비스)를 사용하여 스냅숏을 만들 때 응용 프로그램이 일관된 상태가 되도록 합니다. 응용 프로그램 일치 스냅숏을 사용하도록 설정하면 원본 가상 컴퓨터에서 실행되는 응용 프로그램의 성능에 영향을 줍니다. 구성하는 추가 복구 지점 수보다 작은 값을 설정해야 합니다.
6. **초기 복제 시작 시간**에서 초기 복제를 시작하는 시간을 나타냅니다. 복제는 인터넷 대역폭을 통해 발생하므로 바쁜 시간을 피해서 복제 일정을 예약할 수 있습니다.
7. **Azure에 저장된 데이터 암호화**에서 Azure Storage의 미사용 데이터를 암호화할지 여부를 지정합니다. 그런 후 **OK**를 클릭합니다.

    ![복제 정책](./media/vmm-to-azure-walkthrough-replication/gs-replication2.png)
8. 새 정책을 만들면 새 정책이 자동으로 VMM 클라우드에 연결됩니다. **확인**을 클릭합니다. **복제** > 정책 이름 > **VMM 클라우드 연결**에서 추가 VMM 클라우드(및 그 안에 포함된 VM)를 이 복제 정책과 연결할 수 있습니다.

    ![복제 정책](./media/vmm-to-azure-walkthrough-replication/policy-associate.png)



## <a name="next-steps"></a>다음 단계

[11단계: 복제 활성화](vmm-to-azure-walkthrough-enable-replication.md)로 이동합니다.

