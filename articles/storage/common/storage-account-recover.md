---
title: 삭제된 스토리지 계정 복구
titleSuffix: Azure Storage
description: Azure Portal 내에서 삭제 된 저장소 계정을 복구 하는 방법에 대해 알아봅니다.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/11/2020
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: f57cd3361d7888d9d7f747955257d96282274fd6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97357349"
---
# <a name="recover-a-deleted-storage-account"></a>삭제된 스토리지 계정 복구

삭제 된 저장소 계정은 Azure Portal 내에서 일부 경우에 복구 될 수 있습니다. 저장소 계정을 복구 하려면 다음 조건이 충족 되어야 합니다.

- 지난 14 일 이내에 저장소 계정이 삭제 되었습니다.
- 저장소 계정은 Azure Resource Manager 배포 모델을 사용 하 여 만들어졌습니다.
- 원래 계정이 삭제 된 후 이름이 같은 새 저장소 계정이 만들어지지 않았습니다.

삭제 된 저장소 계정 복구를 시도 하기 전에 해당 계정에 대 한 리소스 그룹이 있는지 확인 합니다. 리소스 그룹이 삭제 된 경우 다시 만들어야 합니다. 리소스 그룹은 복구할 수 없습니다. 에 대 한 자세한 내용은 [리소스 그룹 관리](../../azure-resource-manager/management/manage-resource-groups-portal.md)를 참조 하세요.

삭제 된 저장소 계정에서 Azure Key Vault 및 키 자격 증명 모음을 삭제 한 경우에는 저장소 계정을 복원 하기 전에 키 자격 증명 모음을 복원 해야 합니다. 자세한 내용은 [Azure Key Vault 복구 개요](../../key-vault/general/key-vault-recovery.md)를 참조 하세요.

> [!IMPORTANT]
> 삭제 된 저장소 계정 복구는 보장 되지 않습니다. 복구는 최상의 시도입니다. 실수로 인 한 계정 삭제를 방지 하기 위해 리소스를 잠그는 것이 좋습니다. 리소스 잠금에 대 한 자세한 내용은 [변경을 방지 하기 위해 리소스 잠그기](../../azure-resource-manager/management/lock-resources.md)를 참조 하세요.
>
> 실수로 인 한 계정 삭제를 방지 하는 또 다른 모범 사례는 Azure RBAC (역할 기반 액세스 제어)를 통해 계정을 삭제할 수 있는 권한이 있는 사용자 수를 제한 하는 것입니다. 자세한 내용은 [AZURE RBAC에 대 한 모범 사례](../../role-based-access-control/best-practices.md)를 참조 하세요.

## <a name="recover-a-deleted-account-from-the-azure-portal"></a>Azure Portal에서 삭제 된 계정 복구

다른 저장소 계정 내에서 삭제 된 저장소 계정을 복구 하려면 다음 단계를 수행 합니다.

1. Azure Portal의 기존 저장소 계정에 대 한 개요 페이지로 이동 합니다.
1. **지원 + 문제 해결** 섹션에서 삭제 된 **계정 복구** 를 선택 합니다.
1. 드롭다운 목록에서 다음 이미지에 표시 된 것 처럼 복구할 계정을 선택 합니다. 복구 하려는 저장소 계정이 드롭다운에서 복원 되지 않으면 복구할 수 없습니다.

    :::image type="content" source="media/storage-account-recover/recover-account-portal.png" alt-text="Azure Portal에서 저장소 계정을 복구 하는 방법을 보여 주는 스크린샷":::

1. **복구** 단추를 선택 하 여 계정을 복원 합니다. 복구가 진행 되 고 있음을 포털에 표시 합니다.

## <a name="recover-a-deleted-account-via-a-support-ticket"></a>지원 티켓을 통해 삭제 된 계정 복구

1. Azure Portal에서 **도움말 + 지원** 으로 이동 합니다.
1. **새 지원 요청** 을 선택합니다.
1. **기본 정보** 탭의 **문제 유형** 필드에서 **기술** 을 선택 합니다.
1. **구독** 필드에서 삭제 된 저장소 계정이 포함 된 구독을 선택 합니다.
1. **서비스** 필드에서 **Storage 계정 관리** 를 선택 합니다.
1. **리소스** 필드에서 모든 저장소 계정 리소스를 선택 합니다. 삭제 된 저장소 계정은 목록에 표시 되지 않습니다.
1. 문제에 대 한 간략 한 요약을 추가 합니다.
1. **문제 유형** 필드에서 **삭제 및 복구** 를 선택 합니다.
1. **문제 하위 유형** 필드에서 삭제 된 **저장소 계정 복구** 를 선택 합니다. 다음 그림은 입력 된 **기본** 탭의 예를 보여 줍니다.

    :::image type="content" source="media/storage-account-recover/recover-account-support-basics.png" alt-text="지원 티켓-기본 탭을 통해 저장소 계정을 복구 하는 방법을 보여 주는 스크린샷":::

1. 다음 그림에 표시 된 것 처럼 **솔루션** 탭으로 이동 하 고 **고객 제어 저장소 계정 복구** 를 선택 합니다.

    :::image type="content" source="media/storage-account-recover/recover-account-support-solutions.png" alt-text="지원 티켓을 통해 저장소 계정을 복구 하는 방법을 보여 주는 스크린샷-솔루션 탭":::

1. 드롭다운 목록에서 다음 이미지에 표시 된 것 처럼 복구할 계정을 선택 합니다. 복구 하려는 저장소 계정이 드롭다운에서 복원 되지 않으면 복구할 수 없습니다.

    :::image type="content" source="media/storage-account-recover/recover-account-support.png" alt-text="지원 티켓을 통해 저장소 계정을 복구 하는 방법을 보여 주는 스크린샷":::

1. **복구** 단추를 선택 하 여 계정을 복원 합니다. 복구가 진행 되 고 있음을 포털에 표시 합니다.

## <a name="next-steps"></a>다음 단계

- [Storage 계정 개요](storage-account-overview.md)
- [스토리지 계정을 만드는](storage-account-create.md)
- [범용 v2 스토리지 계정으로 업그레이드](storage-account-upgrade.md)
- [Azure Storage 계정을 다른 지역으로 이동](storage-account-move.md)