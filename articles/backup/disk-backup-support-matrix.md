---
title: Azure Disk Backup 지원 매트릭스
description: 지원 설정 및 Azure Disk Backup 제한 사항에 대 한 요약을 제공 합니다.
ms.topic: conceptual
ms.date: 01/07/2021
ms.custom: references_regions
ms.openlocfilehash: 2030ee3f098317da9cda8b15fec1562f31cae7ee
ms.sourcegitcommit: 4d48a54d0a3f772c01171719a9b80ee9c41c0c5d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/24/2021
ms.locfileid: "98746039"
---
# <a name="azure-disk-backup-support-matrix-in-preview"></a>Azure Disk Backup 지원 매트릭스 (미리 보기)

>[!IMPORTANT]
>Azure Disk Backup은 서비스 수준 계약 없이 미리 보기 상태 이며 프로덕션 워크 로드에는 권장 되지 않습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.
>
>미리 보기에 등록 하려면 [이 양식을 작성](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR1vE8L51DIpDmziRt_893LVUNFlEWFJBN09PTDhEMjVHS05UWFkxUlUzUS4u) 하세요.

[Azure Backup](./backup-overview.md) 를 사용 하 여 Azure 디스크를 보호할 수 있습니다. 이 문서에서는 지역 가용성, 지원 되는 시나리오 및 제한 사항을 요약 합니다.

## <a name="supported-regions"></a>지원되는 지역

Azure Disk Backup은 미국 서 부 중부, 대한민국 중부, 대한민국 지역에서 미리 보기로 제공 됩니다. 

사용할 수 있게 되 면 더 많은 지역이 발표 됩니다.

## <a name="limitations"></a>제한 사항

- Azure 디스크 백업은 공유 디스크 (공유 premium Ssd)를 포함 하 여 Azure Managed Disks에 대해 지원 됩니다. 관리 되지 않는 디스크는 지원 되지 않습니다. 현재이 솔루션은 스냅숏 기능이 없기 때문에 공유 된 울트라 디스크를 포함 하 여 디스크를 지원 하지 않습니다.

- Azure Disk Backup은 쓰기 가속기 디스크의 백업을 지원 합니다. 그러나 복원 중에 디스크는 일반 디스크로 복원 됩니다. 디스크를 VM에 탑재 한 후 디스크에서 쓰기 가속화 캐시를 사용 하도록 설정할 수 있습니다.

- Azure Backup은 하루에 여러 백업을 지원 하 여 Azure managed disks의 작업 (스냅숏) 계층 백업을 제공 합니다. 백업은 백업 자격 증명 모음에 복사 되지 않습니다.

- 현재 백업을 수행한 기존 원본 디스크를 교체 하 여 복원 하는 OLR (Original-Location 복구) 옵션은 지원 되지 않습니다. 복구 지점에서 복원 하 여 백업이 수행 된 원본 디스크와 동일한 리소스 그룹 또는 다른 리소스 그룹에 새 디스크를 만들 수 있습니다. 이를 ALR (Alternate-Location 복구) 이라고 합니다.

- Managed Disks에 대 한 Azure Backup는 디스크당 200 스냅숏으로 제한 되는 증분 스냅숏을 사용 합니다. 예약 된 백업 외에 요청 시 백업을 수행할 수 있도록 백업 정책은 전체 백업을 180로 제한 합니다. 관리 디스크의 [증분 스냅숏에](../virtual-machines/disks-incremental-snapshots.md#restrictions) 대해 자세히 알아보세요.

- Azure [구독 및 서비스 제한은](../azure-resource-manager/management/azure-subscription-service-limits.md#virtual-machine-disk-limits) 구독 당 지역 당 총 디스크 스냅숏 수에 적용 됩니다.

- 가상 컴퓨터에 연결 된 여러 디스크의 지정 시간 스냅숏은 지원 되지 않습니다.

- 백업 자격 증명 모음과 백업할 디스크는 동일 하거나 다른 구독에 있을 수 있습니다. 그러나 백업할 백업 자격 증명 모음과 디스크는 동일한 지역에 있어야 합니다.

- 백업할 디스크 및 스냅숏이 로컬에 저장 되는 스냅숏 리소스 그룹은 동일한 구독에 있어야 합니다.

- 디스크를 백업에서 동일 하거나 다른 구독으로 복원 하는 것이 지원 됩니다. 그러나 복원 된 디스크는 스냅숏의 경우와 동일한 지역에 생성 됩니다.

- ADE 암호화 된 디스크는 보호할 수 없습니다.

- 플랫폼 관리 키 또는 고객이 관리 하는 키로 암호화 된 디스크가 지원 됩니다. 그러나 디스크 암호화 설정 KeyVault 키가 사용 되지 않도록 설정 되거나 삭제 된 경우 CMK (고객 관리 키)를 사용 하 여 암호화 된 디스크의 복원 지점이 복원 되지 않습니다.

- 현재 백업 정책은 수정할 수 없으며, 디스크의 백업을 구성할 때 백업 인스턴스에 할당 된 스냅숏 리소스 그룹은 변경할 수 없습니다.

- 현재 디스크의 백업을 구성 하는 Azure Portal 환경은 동일한 구독의 최대 20 개의 디스크로 제한 됩니다.

- 현재 미리 보기 중에는 PowerShell 및 Azure CLI를 사용 하 여 디스크의 백업 및 복원을 구성 하는 것이 지원 되지 않습니다.

- 백업을 구성할 때 백업 하도록 선택한 디스크와 스냅숏이 저장 될 스냅숏 리소스 그룹은 동일한 구독의 일부 여야 합니다. 해당 디스크의 구독 외부에 있는 특정 디스크에 대해 증분 스냅숏을 만들 수 없습니다. 관리 디스크의 [증분 스냅숏에](../virtual-machines/windows/disks-incremental-snapshots-portal.md#restrictions) 대해 자세히 알아보세요. 스냅숏 리소스 그룹을 선택 하는 방법에 대 한 자세한 내용은  [백업 구성](backup-managed-disks.md#configure-backup)을 참조 하세요.

- 백업 및 복원 작업을 성공적으로 수행 하려면 백업 자격 증명 모음 관리 id에 역할 할당이 필요 합니다. 설명서에 제공 된 역할 정의만 사용 합니다. 소유자, 참가자 등의 다른 역할은 사용할 지원 되지 않습니다. 역할을 할당 한 후 즉시 백업 또는 복원 작업을 구성 하기 시작 하면 권한 문제가 발생할 수 있습니다. 이는 역할 할당을 적용 하는 데 몇 분이 걸릴 수 있기 때문입니다.

- Managed disks를 사용 하면 배포 시 또는 나중에 디스크 크기를 변경 하지 않고 성능 계층을 변경할 수 있습니다. Azure Disk Backup 솔루션은 백업 중인 원본 디스크의 성능 계층 변경 내용을 지원 합니다. 복원 하는 동안 복원 된 디스크의 성능 계층은 백업 시점의 원본 디스크와 동일 하 게 됩니다. 복원 작업 후 디스크의 성능 계층을 변경 하려면 [여기](../virtual-machines/disks-performance-tiers-portal.md) 의 설명서를 따르세요.

- 관리 디스크에 대 한 [개인 링크](../virtual-machines/disks-enable-private-links-for-import-export-portal.md) 지원을 통해 Azure virtual network 내 에서만 발생 하도록 관리 디스크의 내보내기 및 가져오기를 제한할 수 있습니다. Azure Disk Backup은 개인 끝점이 사용 하도록 설정 된 디스크의 백업을 지원 합니다. 여기에는 개인 끝점을 통해 액세스할 수 있는 백업 데이터 또는 스냅숏이 포함 되지 않습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Managed Disks 백업](backup-managed-disks.md)
