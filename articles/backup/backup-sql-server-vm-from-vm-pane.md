---
title: VM 창에서 SQL Server VM 백업
description: 이 문서에서는 VM 창에서 Azure 가상 머신의 SQL Server 데이터베이스를 백업하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 08/13/2020
ms.openlocfilehash: 4f4ea202ee96e93a621c8dd0025c9ebc8b8d445d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "88891660"
---
# <a name="back-up-a-sql-server-from-the-vm-pane"></a>VM 창에서 SQL Server 백업

이 문서에서는 [Azure Backup](backup-overview.md) 서비스를 사용하여 Azure VM에서 실행되는 SQL Server를 백업하는 방법을 설명합니다. 다음 두 가지 방법을 사용하여 SQL Server VM을 백업할 수 있습니다.

- 단일 SQL Server Azure VM: 이 문서의 지침은 VM 뷰에서 직접 SQL Server VM 하나를 백업하는 방법을 설명합니다.
- 여러 SQL Server Azure VM: Recovery Services 자격 증명 모음을 설정하고 여러 VM에 대한 백업을 구성할 수 있습니다. 해당 시나리오의 경우 [이 문서](backup-sql-server-database-azure-vms.md)의 지침을 따르세요.

## <a name="before-you-start"></a>시작하기 전에

1. [지원 매트릭스](sql-support-matrix.md)를 사용하여 환경을 확인합니다.
2. SQL Server VM용 Azure Backup [개요](backup-azure-sql-database.md)를 확인합니다.
3. VM에 [네트워크 연결](backup-sql-server-database-azure-vms.md#establish-network-connectivity)이 있는지 확인합니다.

## <a name="configure-backup-on-the-sql-server"></a>SQL Server에서 백업 구성

VM의 **백업** 창을 통해 SQL Server VM에서 백업을 사용하도록 설정할 수 있습니다. 이 방법을 사용하면 다음 두 가지 작업이 수행됩니다.

- Azure Backup 서비스에 SQL VM을 등록하여 액세스 권한을 부여합니다.
- VM 내에서 실행되는 모든 SQL Server 인스턴스를 자동으로 보호합니다. 즉, 기존의 모든 데이터베이스와 나중에 인스턴스에 추가하는 데이터베이스에 백업 정책이 적용됩니다.

1. 페이지 맨 위에 있는 배너를 선택하여 SQL Server 백업 뷰를 엽니다.

    ![SQL Server 백업 뷰](./media/backup-sql-server-vm-from-vm-pane/sql-server-backup-view.png)

    >[!NOTE]
    >배너가 표시되지 않나요? 배너는 Azure Marketplace 이미지를 사용하여 만든 SQL Server VM에서만 표시됩니다. Azure VM Backup으로 보호된 VM에서도 표시됩니다. 다른 이미지의 경우 [여기](backup-sql-server-database-azure-vms.md)에 설명된 대로 백업을 구성할 수 있습니다.

2. Recovery Services 자격 증명 모음 이름을 입력합니다. 자격 증명 모음은 모든 백업을 저장하고 관리하는 논리 엔터티입니다. 새 자격 증명 모음을 만드는 경우 다음과 같이 생성됩니다.

    - 보호할 SQL Server VM과 동일한 구독 및 지역에서 생성됩니다.
    - 모든 백업에 대한 GRS(지역 중복 스토리지) 설정을 사용하여 생성됩니다. 중복 유형을 변경하려면 VM을 보호하기 전에 변경해야 합니다. 자세한 내용은 [이 문서](backup-create-rs-vault.md#set-storage-redundancy)를 참조하세요.

3. **백업 정책** 을 선택합니다. 기본 정책 또는 자격 증명 모음에 만든 다른 기존 정책에서 선택할 수 있습니다. 새 정책을 만들려면 [이 문서](backup-sql-server-database-azure-vms.md#create-a-backup-policy)에서 단계별 가이드를 참조하세요.

    ![백업 정책 선택](./media/backup-sql-server-vm-from-vm-pane/backup-policy.png)

4. **백업 사용** 을 선택합니다. 작업이 완료될 때까지 몇 분 정도 걸릴 수 있습니다.

    ![백업 사용 선택](./media/backup-sql-server-vm-from-vm-pane/enable-backup.png)

5. 작업이 완료되면 배너에 **자격 증명 모음 이름** 이 표시됩니다.

    ![자격 증명 모음 이름이 배너에 표시됨](./media/backup-sql-server-vm-from-vm-pane/vault-name.png)

6. 배너를 선택하여 자격 증명 모음 뷰로 이동합니다. 여기에서 등록된 모든 VM과 해당 보호 상태를 확인할 수 있습니다.

    ![등록된 VM이 포함된 자격 증명 모음 뷰](./media/backup-sql-server-vm-from-vm-pane/vault-view.png)

7. 비마켓플레이스 이미지의 경우 등록에 성공할 수도 있지만 Azure Backup 확장에 SQL Server에 대한 사용 권한이 부여될 때까지 **백업 구성** 이 트리거되지 않을 수 있습니다. 이 경우 **백업 준비** 열에 **준비 안 됨** 이 표시됩니다. 비마켓플레이스 이미지의 경우 백업 구성이 트리거될 수 있도록 수동으로 [적절한 사용 권한을 할당](backup-azure-sql-database.md#set-vm-permissions)해야 합니다.

    ![준비 안 됨 상태인 백업 준비](./media/backup-sql-server-vm-from-vm-pane/backup-readiness-not-ready.png)

8. 백업된 SQL Server VM에서 수행해야 하는 추가 작업 또는 모니터링을 위해 해당 Recovery Services 자격 증명 모음으로 이동합니다. 이 자격 증명 모음에 백업된 모든 데이터베이스를 확인하고 주문형 백업 및 복원과 같은 작업을 트리거하려면 **백업 항목** 으로 이동합니다. 마찬가지로, 보호 구성, 백업, 복원 등의 작업(operation)에 해당하는 작업(job)을 [모니터링](manage-monitor-sql-database-backup.md)하려면 **백업 작업** 으로 이동합니다.

    ![백업 항목에서 백업된 데이터베이스 확인](./media/backup-sql-server-vm-from-vm-pane/backup-items.png)

>[!NOTE]
>보호된 VM에 나중에 추가될 수 있는 새 SQL Server 인스턴스에서는 백업이 자동으로 구성되지 않습니다. 새로 추가된 인스턴스에서 백업을 구성하려면 VM이 등록된 자격 증명 모음으로 이동하여 [여기](backup-sql-server-database-azure-vms.md)에 나열된 단계를 수행해야 합니다.

## <a name="next-steps"></a>다음 단계

방법 배우기:

- [백업된 SQL Server 데이터베이스 복원](restore-sql-database-azure-vm.md)
- [백업된 SQL Server 데이터베이스 관리](manage-monitor-sql-database-backup.md)
