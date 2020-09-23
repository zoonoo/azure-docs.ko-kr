---
title: 백업 자격 증명 모음 개요
description: 백업 자격 증명 모음에 대 한 개요입니다.
ms.topic: conceptual
ms.date: 08/17/2020
ms.openlocfilehash: 773152212ed831c0083cfdf912f45ece578d079f
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90997263"
---
# <a name="backup-vaults-overview"></a>백업 자격 증명 모음 개요

이 문서에서는 백업 자격 증명 모음에 대 한 기능을 설명 합니다. 백업 자격 증명 모음은 Azure에서 Azure Backup 지원 되는 특정 최신 워크 로드에 대 한 백업 데이터를 보관 하는 저장소 엔터티입니다. 백업 자격 증명 모음을 사용 하 여 Azure Database for PostgreSQL 서버 및 Azure Backup에서 지원할 최신 작업 등 다양 한 Azure 서비스에 대 한 백업 데이터를 저장할 수 있습니다. 백업 자격 증명 모음을 사용 하면 관리 오버 헤드를 최소화 하면서 백업 데이터를 쉽게 구성할 수 있습니다. 백업 자격 증명 모음은 다음과 같은 기능을 제공 하는 Azure의 Azure Resource Manager 모델을 기반으로 합니다.

- **백업 데이터를 보호 하기 위한 향상 된 기능**: 백업 자격 증명 모음을 사용 Azure Backup는 클라우드 백업을 보호 하는 보안 기능을 제공 합니다. 이러한 보안 기능을 통해 백업을 보호하고 프로덕션 및 백업 서버가 손상된 경우에도 데이터를 안전하게 복구할 수 있습니다. [자세한 정보](backup-azure-security-feature.md)

- **RBAC(역할 기반 Access Control)**: RBAC는 Azure에서 세밀한 액세스 관리 제어를 제공합니다. [Azure는 다양한 기본 제공 역할을 제공](../role-based-access-control/built-in-roles.md)하고 Azure Backup에는 3가지 [복구 지점을 관리하는 기본 제공 역할](backup-rbac-rs-vault.md)이 포함됩니다. 백업 자격 증명 모음은 RBAC와 호환 되므로 정의 된 사용자 역할 집합에 대 한 백업 및 복원 액세스를 제한 합니다. [자세한 정보](backup-rbac-rs-vault.md)

## <a name="storage-settings-in-the-backup-vault"></a>백업 자격 증명 모음의 저장소 설정

백업 자격 증명 모음은 시간에 따라 생성 된 백업 및 복구 지점이 저장 되는 엔터티입니다. 또한 백업 자격 증명 모음에는 보호 된 가상 머신과 연결 된 백업 정책도 포함 됩니다.

- Azure Backup는 자격 증명 모음에 대 한 저장소를 자동으로 처리 합니다. 백업 자격 증명 모음을 만들 때 비즈니스 요구 사항과 일치 하는 저장소 중복성을 선택 합니다.

- 저장소 중복성에 대 한 자세한 내용은 [지역](../storage/common/storage-redundancy.md#geo-redundant-storage) 및 [로컬](../storage/common/storage-redundancy.md#locally-redundant-storage) 중복성에 대 한 문서를 참조 하세요.

## <a name="encryption-settings-in-the-backup-vault"></a>백업 자격 증명 모음의 암호화 설정

이 섹션에서는 백업 자격 증명 모음에 저장 된 백업 데이터를 암호화 하는 데 사용할 수 있는 옵션을 설명 합니다.

### <a name="encryption-of-backup-data-using-platform-managed-keys"></a>플랫폼 관리 키를 사용 하 여 백업 데이터 암호화

기본적으로 모든 데이터는 플랫폼 관리 키를 사용 하 여 암호화 됩니다. 이 암호화를 사용 하도록 설정 하기 위해 끝에서 명시적인 조치를 취할 필요는 없습니다. 백업 자격 증명 모음에 백업 되는 모든 워크 로드에 적용 됩니다.

## <a name="create-a-backup-vault"></a>백업 자격 증명 모음 만들기

백업 자격 증명 모음은 시간에 따라 생성 되는 복구 지점이 저장 되 고 백업 관련 작업을 수행 하기 위한 인터페이스를 제공 하는 관리 엔터티입니다. 여기에는 주문형 백업 수행, 복원 수행 및 백업 정책 만들기가 포함됩니다.

백업 자격 증명 모음을 만들려면 다음 단계를 수행 합니다.

### <a name="sign-in-to-azure"></a>Azure에 로그인

[https://portal.azure.com](<https://portal.azure.com> ) 에서 Azure Portal에 로그인합니다.

### <a name="create-backup-vault"></a>백업 자격 증명 모음 만들기

1. 검색 상자에 **백업 자격 증명 모음** 을 입력 합니다.
1. **서비스**에서 **백업 자격 증명 모음**을 선택 합니다.
1. **백업 자격 증명 모음** 페이지에서 **추가**를 선택 합니다.
1. **기본 사항 탭**의 **프로젝트 세부 정보**에서 올바른 구독을 선택 했는지 확인 한 다음 새 리소스 그룹 **만들기** 를 선택 합니다. 이름으로 *myResourceGroup*을 입력합니다.

  ![새 리소스 그룹 만들기](./media/backup-vault-overview/new-resource-group.png)

1. **인스턴스 세부 정보**아래에서 **백업 자격 증명 모음 이름** 으로 *myvault* 를 입력 하 고 원하는 지역을 선택 합니다 .이 경우 **지역**에 대해 *미국 동부* 를 선택 합니다.
1. 이제 **저장소 중복성**을 선택 합니다. 자격 증명 모음으로 항목을 보호 한 후에는 저장소 중복성을 변경할 수 없습니다.
1. Azure를 기본 백업 저장소 끝점으로 사용 하는 경우 기본 **지역 중복** 설정을 계속 사용 하는 것이 좋습니다.
1. Azure를 기본 백업 스토리지 엔드포인트로 사용하지 않는 경우 Azure Storage 비용이 감소되는 **로컬 중복**을 선택합니다.
1. [지역](../storage/common/storage-redundancy.md#geo-redundant-storage) 및 [로컬](../storage/common/storage-redundancy.md#locally-redundant-storage) 중복성에 대해 자세히 알아보세요.

  ![저장소 중복성 선택](./media/backup-vault-overview/storage-redundancy.png)

1. 페이지 맨 아래에 있는 검토 + 만들기 단추를 선택 합니다.

    ![검토 + 만들기 선택](./media/backup-vault-overview/review-and-create.png)

## <a name="delete-a-backup-vault"></a>Backup 자격 증명 모음 삭제

이 섹션에서는 백업 자격 증명 모음을 삭제 하는 방법을 설명 합니다. 종속성을 제거한 후 자격 증명 모음을 삭제 하는 방법에 대 한 지침이 포함 되어 있습니다.

### <a name="before-you-start"></a>시작하기 전에

다음 종속성 중 하나를 사용 하 여 백업 자격 증명 모음을 삭제할 수 없습니다.

- 보호 된 데이터 원본 (예: PostgreSQL 서버용 Azure database)을 포함 하는 자격 증명 모음은 삭제할 수 없습니다.
- 백업 데이터를 포함 하는 자격 증명 모음은 삭제할 수 없습니다.

종속성을 제거 하지 않고 자격 증명 모음을 삭제 하려고 하면 다음과 같은 오류 메시지가 표시 됩니다.

>자격 증명 모음에 기존 백업 인스턴스 또는 백업 정책이 있으므로 백업 자격 증명 모음을 삭제할 수 없습니다. 자격 증명 모음에 있는 모든 백업 인스턴스와 백업 정책을 삭제 한 후 자격 증명 모음을 삭제 하십시오.

### <a name="proper-way-to-delete-a-vault"></a>자격 증명 모음을 삭제 하는 적절 한 방법

>[!WARNING]
다음 작업은 소거식 이며 실행 취소할 수 없습니다. 보호 된 서버와 연결 된 모든 백업 데이터 및 백업 항목은 영구적으로 삭제 됩니다. 주의하여 진행하세요.

자격 증명 모음을 제대로 삭제 하려면 다음 순서 대로 단계를 수행 해야 합니다.

- 보호 된 항목이 있는지 확인 해야 합니다.
  - 왼쪽 탐색 모음에서 **백업 인스턴스** 로 이동 합니다. 여기에 나열 된 모든 항목을 먼저 삭제 해야 합니다.

이러한 단계를 완료 한 후에는 자격 증명 모음을 계속 해 서 삭제할 수 있습니다.

### <a name="delete-the-backup-vault"></a>백업 자격 증명 모음 삭제

자격 증명 모음에 더 이상 항목이 없으면 자격 증명 모음 대시보드에서 **삭제** 를 선택 합니다. 자격 증명 모음을 삭제할 것인지 묻는 확인 텍스트가 표시 됩니다.

![자격 증명 모음 삭제](./media/backup-vault-overview/delete-vault.png)

1. **예** 를 선택 하 여 자격 증명 모음을 삭제할 것인지 확인 합니다. 자격 증명 모음이 삭제 됩니다. 포털이 **새** 서비스 메뉴로 돌아갑니다.

## <a name="monitor-and-manage-the-backup-vault"></a>백업 자격 증명 모음 모니터링 및 관리

이 섹션에서는 backup 자격 증명 모음 **개요** 대시보드를 사용 하 여 백업 자격 증명 모음을 모니터링 하 고 관리 하는 방법을 설명 합니다. 개요 창에는 **작업** 및 **인스턴스**라는 두 개의 타일이 포함 되어 있습니다.

![개요 대시보드](./media/backup-vault-overview/overview-dashboard.png)

### <a name="manage-backup-instances"></a>백업 인스턴스 관리

**작업** 타일에서 백업 자격 증명 모음에 있는 모든 백업 및 복원 관련 작업의 요약 된 보기를 볼 수 있습니다. 이 타일에서 숫자 중 하나를 선택 하면 특정 데이터 원본 유형, 작업 유형 및 상태에 대 한 작업에 대 한 자세한 정보를 볼 수 있습니다.

![백업 인스턴스](./media/backup-vault-overview/backup-instances.png)

### <a name="manage-backup-jobs"></a>Backup 작업 관리

백업 **인스턴스** 타일에서 백업 자격 증명 모음에 있는 모든 백업 인스턴스의 요약 된 보기를 볼 수 있습니다. 이 타일에서 숫자 중 하나를 선택 하면 특정 데이터 원본 유형 및 보호 상태에 대 한 백업 인스턴스에 대 한 자세한 정보를 볼 수 있습니다.

![백업 작업](./media/backup-vault-overview/backup-jobs.png)

## <a name="next-steps"></a>다음 단계

- [Azure PostgreSQL 데이터베이스에서 백업 구성](backup-azure-database-postgresql.md#configure-backup-on-azure-postgresql-databases)
