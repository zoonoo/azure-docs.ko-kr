---
title: "Azure Site Recovery를 사용하여 Azure로 Hyper-V VM(System Center VMM 없음) 복제를 위한 복제 정책 설정 | Microsoft Docs"
description: "Hyper-V VM을 Azure Storage에 복제할 때 복제 정책을 설정하는 데 필요한 단계 요약"
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 1777e0eb-accb-42b5-a747-11272e131a52
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/22/2017
ms.author: raynew
ms.openlocfilehash: ca5bec5cf1152e6259b9fe7a869edd2d62b88e1a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="step-9-set-up-a-replication-policy-for-hyper-v-vm-replication-to-azure"></a>9단계: Hyper-V VM을 Azure에 복제하기 위한 복제 정책 설정

이 문서에서는 Azure Portal에서 [Azure Site Recovery](site-recovery-overview.md) 서비스를 사용하여 Hyper-V VM(System Center VMM 없음)을 Azure에 복제할 때 복제 정책을 설정하는 방법을 설명합니다.


이 문서의 하단 또는 [Azure Recovery Services 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)에서 의견이나 질문을 게시합니다.

## <a name="about-snapshots"></a>스냅숏 정보

Hyper-V는 두 가지 유형의 스냅숏, 즉 전체 가상 컴퓨터의 증분 스냅숏을 제공하는 표준 스냅숏과 가상 컴퓨터 내 응용 프로그램 데이터의 지정 시간 스냅숏을 만드는 응용 프로그램 일치 스냅숏을 사용합니다.
    - 응용 프로그램 일치 스냅숏은 VSS(볼륨 섀도 복사본 서비스)를 사용하여 스냅숏을 만들 때 응용 프로그램이 일관된 상태가 되도록 합니다.
    - 응용 프로그램 일치 스냅숏을 사용하도록 설정하면 원본 가상 컴퓨터에서 실행되는 응용 프로그램의 성능에 영향을 줍니다. 구성하는 추가 복구 지점 수보다 작은 값을 설정해야 합니다.

## <a name="set-up-a-replication-policy"></a>복제 정책 설정

1. 새 복제 정책을 만들려면 **인프라 준비** > **복제 설정** > **+만들기 및 연결**을 차례로 클릭합니다.

    ![네트워크](./media/hyper-v-site-walkthrough-replication/gs-replication.png)
2. **만들기 및 연결 정책**에서 정책 이름을 지정합니다.
3. **복사 빈도**에서 초기 복제 후 델타 데이터를 복제할 빈도(30초 마다, 5분마다 또는 15분마다)를 지정합니다.

    > [!NOTE]
    > Premium Storage로 복제하는 경우 30초 빈도가 지원되지 않습니다. Premium Storage에서 지원하는 blob당 스냅숏 수(100)에 따라 제한이 결정됩니다. [자세히 알아보세요](../storage/common/storage-premium-storage.md#snapshots-and-copy-blob)을 확인하세요.

4. **복구 지점 보존**에서 각 복구 지점에 대해 지속될 보존 시간을 시간 단위로 지정합니다. VM을 이 기간 내의 모든 지점으로 복구할 수 있습니다.
5. **앱 일치 스냅숏 빈도**에서 응용 프로그램 일치 스냅숏이 포함된 복구 지점을 만드는 빈도(1~12시간)를 지정합니다.
6. **초기 복제 시작 시간**에서 초기 복제를 시작할 시간을 지정합니다. 복제는 인터넷 대역폭을 통해 발생하므로 바쁜 시간을 피해서 복제 일정을 예약할 수 있습니다. 그런 후 **OK**를 클릭합니다.

    ![복제 정책](./media/hyper-v-site-walkthrough-replication/gs-replication2.png)

새 정책을 만들면 새 정책이 자동으로 Hyper-V 사이트에 연결됩니다. **복제** > 정책 이름 > **Hyper-V 사이트 연결**에서 Hyper-V 사이트 및 해당 사이트 내의 VM을 여러 복제 정책과 연결할 수 있습니다.



## <a name="next-steps"></a>다음 단계

[10단계: 복제 활성화](hyper-v-site-walkthrough-enable-replication.md)로 이동합니다.
