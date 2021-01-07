---
title: Azure Site Recovery에서 추가 된 Azure VM 디스크에 대 한 복제를 사용 하도록 설정
description: 이 문서에서는를 사용 하 여 재해 복구를 사용 하도록 설정 된 Azure VM에 추가 된 디스크에 대해 복제를 사용 하도록 설정 하는 방법을 설명 Azure Site Recovery
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 04/29/2019
ms.openlocfilehash: 6cbbe63d7968816de78256f5a8408517bb8da278
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "75973811"
---
# <a name="enable-replication-for-a-disk-added-to-an-azure-vm"></a>Azure VM에 추가 된 디스크에 대 한 복제 사용


이 문서에서는 [Azure Site Recovery](site-recovery-overview.md)를 사용 하 여 다른 azure 지역에 대 한 재해 복구를 이미 사용 하도록 설정 된 azure VM에 추가 된 데이터 디스크에 대해 복제를 사용 하도록 설정 하는 방법을 설명 합니다.

VM에 추가 하는 디스크에 대 한 복제를 사용 하도록 설정 하는 것은 managed disks가 있는 Azure Vm에서 지원 됩니다.

다른 Azure 지역에 복제 되는 Azure VM에 새 디스크를 추가 하면 다음 작업이 수행 됩니다.

-   VM에 대 한 복제 상태에 경고가 표시 되 고 포털에서 하나 이상의 디스크를 보호할 수 있다는 알림이 표시 됩니다.
-   추가 된 디스크에 대 한 보호를 사용 하도록 설정 하면 디스크의 초기 복제 후에 경고가 표시 되지 않습니다.
-   디스크에 대해 복제를 사용 하지 않도록 선택 하는 경우 경고를 해제 하도록 선택할 수 있습니다.

![새 디스크 추가 됨](./media/azure-to-azure-enable-replication-added-disk/newdisk.png)



## <a name="before-you-start"></a>시작하기 전에

이 문서에서는 디스크를 추가 하는 VM에 대해 재해 복구를 이미 설정 했다고 가정 합니다. 그렇지 않은 경우 [azure에 azure 재해 복구 자습서](azure-to-azure-tutorial-enable-replication.md)를 따르세요.

## <a name="enable-replication-for-an-added-disk"></a>추가된 디스크에 대한 복제 활성화

추가 된 디스크에 대해 복제를 사용 하도록 설정 하려면 다음을 수행 합니다.

1. 자격 증명 모음 > **복제 된 항목**에서 디스크를 추가한 VM을 클릭 합니다.
2. **디스크**를 클릭 하 고 복제를 사용 하도록 설정 하려는 데이터 디스크를 선택 합니다 (이러한 디스크는 **보호 되지 않음** 상태).
3.  **디스크 정보**에서 **복제 사용**을 클릭 합니다.

    ![추가 된 디스크에 대해 복제 사용](./media/azure-to-azure-enable-replication-added-disk/enabled-added.png)

복제 사용 작업을 실행 하 고 초기 복제가 완료 된 후에는 디스크 문제에 대 한 복제 상태 경고가 제거 됩니다.



## <a name="next-steps"></a>다음 단계

테스트 장애 조치(failover) 실행에 대해 [자세히 알아보세요](site-recovery-test-failover-to-azure.md).
