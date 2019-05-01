---
title: Azure Site Recovery에서 복제 된 Azure VM에 추가 디스크에 대 한 복제를 사용 하도록 설정 | Microsoft Docs
description: 이 문서에서는 Azure Site Recovery를 사용 하 여 재해 복구에 사용 되는 Azure VM에 추가 디스크에 대 한 복제를 사용 하는 방법 설명
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/29/2018
ms.author: asgang
ms.openlocfilehash: 69122ffe9cefa3e1b9c6c8fbadfa80492ebebbde
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64928064"
---
# <a name="enable-replication-for-a-disk-added-to-an-azure-vm"></a>Azure VM에 추가 디스크에 대 한 복제를 사용 하도록 설정


이 문서에서는 이미 다른 Azure 지역으로 재해 복구를 위해 사용 되는 Azure VM에 추가 되는 데이터 디스크에 대 한 복제를 사용 하도록 설정 하려면 사용 하 여 [Azure Site Recovery](site-recovery-overview.md)합니다.

복제는 VM에 추가 디스크를 사용 하는 관리 디스크를 사용 하 여 Azure Vm에 대 한 지원 됩니다.

다른 Azure 지역에 복제 하는 Azure VM에 새 디스크를 추가한 다음 작업이 수행 됩니다.

-   VM에 대 한 복제 상태 경고 및 포털에 알려 하나 표시 하거나 더 많은 디스크 보호를 위해 사용할 수 있습니다.
-   추가 된 디스크에 대 한 보호를 사용 하는 경우 경고는 디스크의 초기 복제 후 사라집니다.
-   디스크에 대 한 복제를 사용 하도록 설정 하지 않으려는 경우 경고를 해제할를 선택할 수 있습니다.

![새 디스크 추가](./media/azure-to-azure-enable-replication-added-disk/newdisk.png)



## <a name="before-you-start"></a>시작하기 전에

이 문서는 이미 설정한 재해 복구 디스크를 추가 하는 VM에 대 한 가정 합니다. 를 하지 않은 경우에 따라 합니다 [에서 Azure로 재해 복구 자습서](azure-to-azure-tutorial-enable-replication.md)합니다. 

## <a name="enable-replication-for-an-added-disk"></a>추가 된 디스크를 복제 하도록 설정 

추가 디스크는 복제를 사용 하도록 설정 하려면 다음을 수행 합니다.

1. 자격 증명 모음에서 > **복제 된 항목**, 디스크를 추가 하는 VM을 클릭 합니다.
2. 클릭 **디스크**를 선택한 다음 복제를 사용 하도록 설정 하려는 데이터 디스크 (이러한 디스크는 **보호 되지 않는** 상태).
3.  **디스크 세부 정보**, 클릭 **복제를 사용 하도록 설정**합니다.

    ![추가 된 디스크에 대 한 복제를 사용 하도록 설정](./media/azure-to-azure-enable-replication-added-disk/enabled-added.png)

사용 복제 작업을 실행 하 고 초기 복제가 완료 된 후 디스크 문제에 대 한 복제 상태 경고가 제거 됩니다.



# <a name="next-steps"></a>다음 단계

테스트 장애 조치(failover) 실행에 대해 [자세히 알아보세요](site-recovery-test-failover-to-azure.md).
