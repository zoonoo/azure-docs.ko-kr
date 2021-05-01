---
title: Azure Site Recovery를 사용하여 Azure 간 재해 복구에서 VMware VM 디스크 제외
description: Azure Site Recovery를 사용하여 Azure로 복제할 때 VMware VM 디스크를 제외하는 방법입니다.
author: mayurigupta13
manager: rochakm
ms.date: 12/10/2019
ms.author: mayg
ms.topic: conceptual
ms.openlocfilehash: c4842172ff181b5cdbe7f6fecf69da8755ae43fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "86129872"
---
# <a name="exclude-disks-from-vmware-vm-replication-to-azure"></a>Azure로의 VMware VM 복제에서 디스크 제외

이 문서에서는 재해 복구를 위해 Azure로 VMware VM을 복제할 때 디스크를 제외하는 방법을 설명합니다. 다음과 같은 여러 이유로 복제에서 디스크를 제외할 수 있습니다.

- 제외되는 디스크에서 변동된 중요하지 않은 데이터가 복제되지 않도록 합니다.
- 복제하지 않아도 되는 디스크를 제외하여 사용되는 복제 대역폭 또는 대상 쪽 리소스를 최적화합니다.
- 불필요한 데이터를 복제하지 않음으로써 스토리지와 네트워크 리소스를 절약합니다.

디스크를 복제에서 제외하기 전에:

- 디스크 제외에 대해 [자세히 알아보세요](exclude-disks-replication.md).
- [일반적인 제외 시나리오](exclude-disks-replication.md#typical-scenarios) 및 디스크를 제외하여 복제, 장애 조치(failover), 장애 복구(failback)에 미치는 영향을 보여주는 [예제](exclude-disks-replication.md#example-1-exclude-the-sql-server-tempdb-disk)를 검토합니다.

## <a name="before-you-start"></a>시작하기 전에

 시작하기 전에 다음을 기억하세요.

- **복제**: 기본적으로 컴퓨터의 모든 디스크는 복제됩니다.
- **디스크 형식**: 기본 디스크만 복제에서 제외할 수 있습니다. 운영 체제 또는 동적 디스크를 제외할 수 없습니다.
- **Mobility Service**: 복제에서 디스크를 제외하려면 복제를 사용하도록 설정하기 전에 컴퓨터에 Mobility Service를 수동으로 설치해야 합니다. 이 방법은 복제를 사용하도록 설정한 후에만 VM에 Mobility Service를 설치하므로 푸시 설치를 사용할 수 없습니다.  
- **디스크 추가/제거/실행**: 복제를 사용하도록 설정한 후 복제를 위해 디스크를 추가/제거/실행할 수 없습니다. 디스크를 추가/제거 또는 제외하려는 경우 컴퓨터에 대한 보호를 해제한 다음 다시 사용하도록 설정해야 합니다.
- **장애 조치(failover)** : 장애 조치(failover) 후 장애 조치된 앱이 동작하기 위해서 제외된 디스크가 필요한 경우 해당 디스크를 수동으로 만들어야 합니다. 또는 Azure 자동화 복구 계획에 통합하여 컴퓨터를 장애 조치(failover)하는 동안 디스크를 만들 수 있습니다.
- **장애 복구(failback)-Windows**: 장애 조치(failover) 후 온-프레미스 사이트로 장애를 복구할 때 Azure에서 수동으로 만드는 Windows 디스크는 장애 복구되지 않습니다. 예를 들어, 디스크 3개를 장애 조치(failover)하고 Azure VM에서 디스크 2개를 직접 만드는 경우 장애 조치(failover)된 3개 디스크만 장애 복구됩니다.
- **장애 복구(failback)-Linux**: Linux 컴퓨터 장애 복구의 경우 Azure에서 수동으로 만드는 디스크가 장애 복구됩니다. 예를 들어, 디스크 3개를 장애 조치(failover)하고 Azure VM에서 디스크 2개를 직접 만드는 경우 5개 디스크 모두 장애 복구됩니다. 장애 복구(failback) 또는 VM 다시 보호 시 수동으로 만든 디스크는 제외할 수 없습니다.



## <a name="exclude-disks-from-replication"></a>복제에서 디스크 제외

1. VMware VM에 대해 [복제를 사용하도록 설정](./hyper-v-azure-tutorial.md)할 때 복제하려는 VM을 선택한 후 **복제 사용** > **속성** > **속성 구성** 페이지에서 **복제할 디스크** 열을 검토합니다. 기본적으로 모든 디스크가 복제하도록 선택됩니다.
2. 특정 디스크를 복제하지 않으려는 경우 **복제할 디스크** 에서 제거할 디스크의 섹션 선택을 취소합니다. 

    ![복제에서 디스크 제외](./media/vmware-azure-exclude-disk/enable-replication-exclude-disk1.png)



## <a name="next-steps"></a>다음 단계
배포가 설정되고 실행된 후에는 다양한 장애 조치(Failover)에 대해 [자세히 알아보세요](failover-failback-overview.md).
