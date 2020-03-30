---
title: Azure 사이트 복구를 사용 하 고 Azure에 재해 복구에서 Hyper-VVM 디스크제외
description: Hyper-V VM 디스크를 Azure 사이트 복구를 사용하여 복제에서 Azure로 제외하는 방법
author: mayurigupta13
manager: rochakm
ms.topic: conceptual
ms.author: mayg
ms.date: 11/12/2019
ms.openlocfilehash: 50fb6da2905b2ae27547f25cce3d7a76ca7976b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75498124"
---
# <a name="exclude-disks-from-replication"></a>복제에서 디스크 제외

이 문서에서는 Hyper-V VM을 Azure로 복제할 때 디스크를 제외하는 방법을 설명합니다. 다음과 같은 여러 가지 이유로 디스크를 복제에서 제외할 수 있습니다.

- 제외된 디스크에서 변동된 중요하지 않은 데이터가 복제되지 않도록 합니다.
- 복제할 필요가 없는 디스크를 제외하여 소비된 복제 대역폭 또는 대상 측 리소스를 최적화합니다.
- 필요하지 않은 데이터를 복제하지 않음으로써 저장소 및 네트워크 리소스를 저장합니다.

디스크를 복제에서 제외하기 전에 다음을 수행합니다.

- 디스크 제외에 대해 [자세히 알아보세요](exclude-disks-replication.md).
- 디스크를 [제외하면](exclude-disks-replication.md#typical-scenarios) 복제, 장애 조치 및 장애 조치(failback)에 미치는 영향을 보여 주는 일반적인 제외 시나리오 및 [예제를](exclude-disks-replication.md#example-1-exclude-the-sql-server-tempdb-disk) 검토합니다.

## <a name="before-you-start"></a>시작하기 전에

시작하기 전에 다음을 기억하세요.

- **복제**: 기본적으로 컴퓨터의 모든 디스크가 복제됩니다.
- **디스크 유형**:
    - 기본 디스크를 복제에서 제외할 수 있습니다.
    - 운영 체제 디스크를 제외할 수 없습니다.
    - 동적 디스크는 제외하지 않는 것이 좋습니다. 사이트 복구는 게스트 VM에서 기본 또는 동적 VHD를 식별할 수 없습니다.  종속 동적 볼륨 디스크를 모두 제외하지 않으면 보호된 동적 디스크가 VM을 통해 실패한 디스크가 되고 해당 디스크의 데이터에 액세스할 수 없습니다.
- **디스크 추가/제거/제외**: 복제를 사용하도록 설정한 후에는 복제를 위해 디스크를 추가/제거/제외할 수 없습니다. 디스크를 추가/제거하거나 제외하려면 VM에 대한 보호를 비활성화한 다음 다시 활성화해야 합니다.
- **장애 조치:** 장애 조치 후 앱에서 장애 조치(failover)를 수행하면 작동하기 위해 디스크를 제외해야 하는 경우 해당 디스크를 수동으로 만들어야 합니다. 또는 Azure 자동화를 복구 계획에 통합하여 컴퓨터의 장애 조치 중에 디스크를 만들 수 있습니다.
- **장애 조치:** 장애 조치 후 온-프레미스 사이트로 장애 조치(failover)를 다시 실패하면 Azure에서 수동으로 만든 디스크가 다시 장애복구되지 않습니다. 예를 들어 세 개의 디스크를 장애 조치하고 Azure VM에서 직접 두 개의 디스크를 만드는 경우 실패한 디스크 는 3개만 다시 장애복구됩니다. 장애 조치(failback) 또는 VM의 역복제에서 수동으로 만든 디스크는 포함할 수 없습니다.

## <a name="exclude-disks"></a>디스크 제외

1. Hyper-V VM에 대한 [복제를 사용하도록 설정할](site-recovery-hyper-v-site-to-azure.md) 때 복제할 VM을 선택한 후 **복제속성** > **Properties** > 구성**활성화** 페이지에서 **디스크를 검토하여 열을 복제합니다.** 기본적으로 모든 디스크는 복제를 위해 선택됩니다.
2. 특정 디스크를 복제하지 않으려면 디스크에서 제외하려는 디스크에 대한 선택을 **취소하도록 복제합니다.** 

    ![복제에서 디스크 제외](./media/hyper-v-exclude-disk/enable-replication6-with-exclude-disk.png)


## <a name="next-steps"></a>다음 단계
배포가 설정되고 실행된 후에는 다양한 장애 조치(Failover)에 대해 [자세히 알아보세요](failover-failback-overview.md).
