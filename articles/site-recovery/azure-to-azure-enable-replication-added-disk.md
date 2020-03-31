---
title: Azure 사이트 복구에서 추가된 Azure VM 디스크에 대한 복제 를 사용하도록 설정합니다.
description: 이 문서에서는 Azure 사이트 복구를 사용하여 재해 복구를 사용하도록 설정된 Azure VM에 추가된 디스크에 대한 복제를 사용하도록 설정하는 방법에 대해 설명합니다.
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 04/29/2019
ms.openlocfilehash: 6cbbe63d7968816de78256f5a8408517bb8da278
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75973811"
---
# <a name="enable-replication-for-a-disk-added-to-an-azure-vm"></a>Azure VM에 추가된 디스크에 대한 복제 를 사용하도록 설정합니다.


이 문서에서는 [Azure 사이트 복구를](site-recovery-overview.md)사용하여 다른 Azure 지역에 재해 복구에 이미 활성화된 Azure VM에 추가된 데이터 디스크에 대한 복제를 사용하도록 설정하는 방법에 대해 설명합니다.

VM에 추가하는 디스크에 대한 복제를 사용하도록 설정하는 것은 관리되는 디스크가 있는 Azure VM에서 지원됩니다.

다른 Azure 지역으로 복제하는 Azure VM에 새 디스크를 추가하면 다음과 같은 현상이 발생합니다.

-   VM에 대한 복제 상태는 경고를 표시하고 포털의 메모는 하나 이상의 디스크를 보호할 수 있음을 알려줍니다.
-   추가된 디스크에 대한 보호를 사용하도록 설정하면 디스크를 처음 복제한 후 경고가 사라집니다.
-   디스크에 대한 복제를 활성화하지 않도록 선택한 경우 경고를 해제하도록 선택할 수 있습니다.

![새 디스크 추가](./media/azure-to-azure-enable-replication-added-disk/newdisk.png)



## <a name="before-you-start"></a>시작하기 전에

이 문서에서는 디스크를 추가하는 VM에 대해 재해 복구를 이미 설정했다고 가정합니다. 그렇지 않은 경우 [Azure에서 Azure로 복구 자습서를](azure-to-azure-tutorial-enable-replication.md)따릅니다.

## <a name="enable-replication-for-an-added-disk"></a>추가된 디스크에 대한 복제 활성화

추가된 디스크에 대한 복제를 사용하려면 다음을 수행합니다.

1. **복제된 항목>** 볼트에서 디스크를 추가한 VM을 클릭합니다.
2. **디스크를**클릭한 다음 복제를 사용하도록 설정할 데이터 디스크를 선택합니다(이러한 디스크에는 **보호되지 않음** 상태가 있음).
3.  디스크 세부 정보에서 **복제 활성화를** **클릭합니다.**

    ![추가된 디스크에 대한 복제 사용](./media/azure-to-azure-enable-replication-added-disk/enabled-added.png)

활성화 복제 작업이 실행되고 초기 복제가 완료되면 디스크 문제에 대한 복제 상태 경고가 제거됩니다.



## <a name="next-steps"></a>다음 단계

테스트 장애 조치(failover) 실행에 대해 [자세히 알아보세요](site-recovery-test-failover-to-azure.md).
