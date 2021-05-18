---
title: Azure Site Recovery에서 추가된 Azure VM 디스크에 대한 복제를 사용하도록 설정
description: 이 문서에서는 Azure Site Recovery를 사용하여 재해 복구에 사용하도록 설정된 Azure VM에 추가된 디스크에 대해 복제를 사용하도록 설정하는 방법을 설명합니다.
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 04/29/2019
ms.openlocfilehash: 6cbbe63d7968816de78256f5a8408517bb8da278
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "75973811"
---
# <a name="enable-replication-for-a-disk-added-to-an-azure-vm"></a>Azure VM에 추가된 디스크에 대해 복제 사용


이 문서에서는 [Azure Site Recovery](site-recovery-overview.md)를 사용하여 다른 Azure 영역에 대한 재해 복구를 이미 사용된 Azure VM에 추가된 데이터 디스크에 대해 복제를 사용하도록 설정하는 방법을 설명합니다.

VM에 추가하는 디스크에 대한 복제 사용은 관리 디스크가 있는 Azure VM에서 지원됩니다.

다른 Azure 지역에 복제하는 Azure VM에 새 디스크를 추가하면 다음 상황이 발생합니다.

-   VM 복제 상태에 경고가 표시되고 포털에 하나 이상의 디스크를 보호할 수 있다는 메모가 표시됩니다.
-   추가된 디스크를 보호하도록 설정하면 디스크 초기 복제 후에 경고가 사라집니다.
-   디스크에 대해 복제를 사용하도록 설정하지 않은 경우 경고를 해제하도록 선택할 수 있습니다.

![새 디스크가 추가됨](./media/azure-to-azure-enable-replication-added-disk/newdisk.png)



## <a name="before-you-start"></a>시작하기 전에

이 문서에서는 디스크를 추가하는 VM에 대해 재해 복구를 이미 설정했다고 가정합니다. 설정하지 않은 경우 [Azure 간에 재해 복구 자습서](azure-to-azure-tutorial-enable-replication.md)를 따릅니다.

## <a name="enable-replication-for-an-added-disk"></a>추가된 디스크에 대한 복제 활성화

추가된 디스크에 대해 복제를 사용하도록 설정하려면 다음을 수행합니다.

1. 자격 증명 모음 > **복제된 항목** 에서 디스크를 추가한 VM을 클릭합니다.
2. **디스크** 를 클릭하고 복제를 사용하도록 설정하려는 데이터 디스크를 선택합니다(해당 디스크는 **보호되지 않음** 상태임).
3.  **디스크 세부 정보** 에서 **복제 사용** 을 클릭합니다.

    ![추가된 디스크에 대해 복제 사용](./media/azure-to-azure-enable-replication-added-disk/enabled-added.png)

복제 사용 작업을 실행하고 초기 복제가 완료된 후에는 디스크 문제에 대한 복제 상태 경고가 제거됩니다.



## <a name="next-steps"></a>다음 단계

테스트 장애 조치(failover) 실행에 대해 [자세히 알아보세요](site-recovery-test-failover-to-azure.md).
