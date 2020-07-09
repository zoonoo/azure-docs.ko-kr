---
title: Azure Site Recovery를 사용 하 여 재해 복구에서 Azure로 VMware VM 디스크 제외
description: Azure Site Recovery를 사용 하 여 복제에서 Azure로 VMware VM 디스크를 제외 하는 방법입니다.
author: mayurigupta13
manager: rochakm
ms.date: 12/10/2019
ms.author: mayg
ms.topic: conceptual
ms.openlocfilehash: c4842172ff181b5cdbe7f6fecf69da8755ae43fa
ms.sourcegitcommit: e995f770a0182a93c4e664e60c025e5ba66d6a45
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86129872"
---
# <a name="exclude-disks-from-vmware-vm-replication-to-azure"></a>VMware VM 복제에서 Azure로 디스크 제외

이 문서에서는 재해 복구를 위해 Azure에 VMware Vm을 복제할 때 디스크를 제외 하는 방법을 설명 합니다. 여러 가지 이유로 인해 복제에서 디스크를 제외 하려고 할 수 있습니다.

- 제외 된 디스크의 중요 하지 않은 데이터 변동 복제 되지 않도록 합니다.
- 복제 하지 않아도 되는 디스크를 제외 하 여 사용 되는 복제 대역폭 또는 대상 쪽 리소스를 최적화 합니다.
- 필요 하지 않은 데이터를 복제 하지 않고 저장소 및 네트워크 리소스를 저장 합니다.

복제에서 디스크를 제외 하기 전에 다음을 수행 합니다.

- 디스크 제외에 대해 [자세히 알아보세요](exclude-disks-replication.md).
- [일반적인 제외 시나리오](exclude-disks-replication.md#typical-scenarios) 와 디스크를 제외 하 여 복제, 장애 조치 (failover) 및 장애 복구에 미치는 영향을 보여 주는 [예제](exclude-disks-replication.md#example-1-exclude-the-sql-server-tempdb-disk) 를 검토 합니다.

## <a name="before-you-start"></a>시작하기 전에

 시작하기 전에 다음을 기억하세요.

- **복제**: 기본적으로 컴퓨터의 모든 디스크가 복제 됩니다.
- **디스크 유형**: 기본 디스크만 복제에서 제외할 수 있습니다. 운영 체제 또는 동적 디스크를 제외할 수 없습니다.
- **모바일 서비스**: 복제에서 디스크를 제외 하려면 복제를 사용 하도록 설정 하기 전에 컴퓨터에 모바일 서비스를 수동으로 설치 해야 합니다. 이 방법은 복제를 사용 하도록 설정한 후에만 VM에 모바일 서비스를 설치 하므로 푸시 설치를 사용할 수 없습니다.  
- **디스크 추가/제거/제외**: 복제를 사용 하도록 설정한 후에는 복제를 위해 디스크를 추가/제거/제외할 수 없습니다. 디스크를 추가/제거 하거나 제외 하려는 경우 컴퓨터에 대 한 보호를 사용 하지 않도록 설정한 다음 다시 사용 하도록 설정 해야 합니다.
- **장애 조치**(failover): 장애 조치 (failover) 후 장애 조치 (failover) 된 앱이 작동 하기 위해 제외 된 디스크가 필요 하면 해당 디스크를 수동으로 만들어야 합니다. 또는 Azure automation을 복구 계획에 통합 하 여 컴퓨터의 장애 조치 (failover) 중에 디스크를 만들 수 있습니다.
- **장애 복구 (Failback)-Windows**: 장애 조치 (failover) 후 온-프레미스 사이트로 장애 복구 (failback) 하는 경우 Azure에서 수동으로 만드는 Windows 디스크는 장애 복구 되지 않습니다. 예를 들어 3 개의 디스크를 장애 조치 (failover) 하 고 Azure Vm에 두 개의 디스크를 직접 만들면 장애 조치 된 디스크 세 개만 장애 복구 됩니다.
- **장애 복구-linux**: linux 컴퓨터 장애 복구 (failback)의 경우 Azure에서 수동으로 만드는 디스크는 장애 복구 (failback) 됩니다. 예를 들어 3 개의 디스크를 장애 조치 (failover) 하 고 Azure Vm에 두 개의 디스크를 직접 만들면 5 개가 모두 장애 복구 됩니다. 장애 복구 (failback) 또는 Vm 다시 보호에서 수동으로 만든 디스크는 제외할 수 없습니다.



## <a name="exclude-disks-from-replication"></a>복제에서 디스크 제외

1. VMware vm에 대해 [복제를 사용 하도록 설정](./hyper-v-azure-tutorial.md) 하는 경우 복제 하려는 vm을 선택한 후 **복제**  >  **속성**사용  >  **속성 구성** 페이지에서 **복제할 디스크** 열을 검토 합니다. 기본적으로 모든 디스크는 복제를 위해 선택 됩니다.
2. 특정 디스크를 복제 하지 않으려는 경우 **디스크에서 복제할** 디스크의 선택을 취소 합니다. 

    ![복제에서 디스크 제외](./media/vmware-azure-exclude-disk/enable-replication-exclude-disk1.png)



## <a name="next-steps"></a>다음 단계
배포가 설정되고 실행된 후에는 다양한 장애 조치(Failover)에 대해 [자세히 알아보세요](failover-failback-overview.md).
