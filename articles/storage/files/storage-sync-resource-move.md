---
title: Azure 파일 동기화 리소스 이동 및 토폴로지 변경
description: 리소스 그룹, 구독 및 AAD (Azure Active Directory) 테 넌 트 간에 동기화 리소스를 이동 하는 방법에 대해 알아봅니다.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 3/10/2021
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: acd5f9263a74d3273dc65522e77d83c90a719311
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2021
ms.locfileid: "103555557"
---
# <a name="move-azure-file-sync-resources-to-a-different-resource-group-subscription-or-aad-tenant"></a>다른 리소스 그룹, 구독 또는 AAD 테 넌 트로 Azure 파일 동기화 리소스 이동

이 문서에서는 Azure 파일 동기화 클라우드 리소스 및 Azure storage 계정에 대 한 리소스 그룹, 구독 또는 Azure Active Directory (AAD) 테 넌 트를 변경 하는 방법을 설명 합니다.

Azure 파일 동기화 클라우드 리소스를 변경 하려는 경우 저장소 리소스를 동시에 고려 하는 것이 중요 합니다. 다음 리소스가 있습니다.

**Azure 파일 동기화 리소스 (계층적 순서로)**

* :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-storage-sync-service.png" border="false"::: 저장소 동기화 서비스
  * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-registered-servers.png" border="false"::: 등록 된 서버
  * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-sync-group.png" border="false"::: 동기화 그룹
    * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-cloud-endpoint.png" border="false"::: 클라우드 끝점
    * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-server-endpoint.png" border="false"::: 서버 끝점

Azure 파일 동기화에서 이동할 수 있는 유일한 리소스는 저장소 동기화 서비스 리소스입니다. 하위 리소스는 해당 부모에 바인딩되고 다른 저장소 동기화 서비스로 이동할 수 없습니다.

**Azure storage 리소스 (계층적 순서)**

* :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-storage-account.png" border="false"::: 저장소 계정
    * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-file-share.png" border="false"::: 파일 공유

Azure Storage에서 이동할 수 있는 유일한 리소스는 저장소 계정입니다. Azure 파일 공유는 하위 리소스로 서 다른 저장소 계정으로 이동할 수 없습니다.

## <a name="supported-combinations"></a>지원되는 조합

리소스 이동을 계획할 때 저장소 계정 및 *저장소 동기화 서비스* 라고 하는 최상위 Azure 파일 동기화 리소스를 함께 고려해 야 합니다.

저장소 동기화 서비스와 파일 공유를 동기화 하는 저장소 계정은 항상 동일한 구독에 상주해 야 합니다. 이러한 조합은 다음과 같이 지원 됩니다.

* 저장소 동기화 서비스와 저장소 계정이 **다른 리소스 그룹** (동일한 Azure 테 넌 트)에 있습니다.
* 저장소 동기화 서비스와 저장소 계정이 **다른 구독** (동일한 Azure 테 넌 트)에 있습니다.

> [!IMPORTANT]
> 서로 다른 이동 조합을 통해 저장소 동기화 서비스와 저장소 계정은 다른 AAD 테 넌 트에 의해 관리 되는 다른 구독으로 끝날 수 있습니다. 동기화가 작동 하는 것 처럼 보이지만이 구성은 지원 되지 않습니다. 동기화는 나중에 작업 조건으로 돌아갈 수 있는 기능 없이 중지할 수 있습니다.

리소스 이동을 계획할 때 [동일한 aad 테 넌 트 내에서 이동](#move-within-the-same-azure-active-directory-tenant) 하 고 [다른 aad 테 넌 트로](#move-to-a-new-azure-active-directory-tenant)이동 하는 경우에는 여러 가지 사항을 고려해 야 합니다. AAD 테 넌 트를 이동 하는 경우 항상 동기화 및 저장소 리소스를 함께 이동 합니다.

### <a name="move-within-the-same-azure-active-directory-tenant"></a>동일한 Azure Active Directory 테 넌 트 내에서 이동

:::row:::
    :::column:::
        :::image type="content" source="media/storage-sync-resource-move/storage-sync-resource-move-small.png" alt-text="Move 명령이 확장 된 저장소 동기화 서비스 리소스의 Azure Portal을 보여 주는 이미지입니다. 리소스 그룹 이동 및 구독 이동 옵션을 보여 줍니다." lightbox="media/storage-sync-resource-move/storage-sync-resource-move.png":::
    :::column-end:::
    :::column:::
        저장소 동기화 서비스 리소스를 이동 하는 편리한 방법은 Azure Portal를 사용 하는 것입니다. 이동 하려는 저장소 동기화 서비스로 이동 하 고 명령 모음에서 **이동** 을 선택 합니다. 저장소 계정 이동에도 동일한 단계가 적용 됩니다. 이러한 방식으로 리소스 그룹의 모든 리소스를 이동할 수도 있습니다. 저장소 동기화 서비스와 모든 저장소 계정이이 리소스 그룹에 사용 되는 경우 전체 리소스 그룹을 이동 하는 것이 좋습니다.
    :::column-end:::
:::row-end:::

> [!WARNING]
> 저장소 계정 리소스를 이동 하면 동기화가 즉시 중지 됩니다. 새 구독의 관련 저장소 계정에 액세스 하려면 동기화를 수동으로 승인 해야 합니다. [Azure 파일 동기화 저장소 액세스 권한 부여](#azure-file-sync-storage-access-authorization) 섹션에서는 필요한 단계를 제공 합니다.

### <a name="move-to-a-new-azure-active-directory-tenant"></a>새 Azure Active Directory 테 넌 트로 이동

저장소 동기화 서비스, 저장소 계정 등의 개별 리소스는 다른 AAD 테 넌 트로만 이동할 수 없습니다. Azure 구독만 AAD 테 넌 트를 이동할 수 있습니다. 새 AAD 테 넌 트의 구독 구조에 대해 생각해 보십시오. Azure 파일 동기화에 대 한 전용 구독을 사용할 수 있습니다. 

1. Azure 구독을 만들거나 이동 해야 하는 이전 테 넌 트의 기존 테 넌 트를 확인 합니다.
1. 저장소 동기화 서비스 및 연결 된 모든 저장소 계정에 대 한 [동일한 AAD 테 넌 트 내에서 구독 이동을 수행](#move-within-the-same-azure-active-directory-tenant) 합니다.
1. 동기화가 중지 됩니다. 즉시 테 넌 트 이동을 완료 하거나 [이동 된 저장소 계정에 액세스 하는 동기화 기능을 복원](#azure-file-sync-storage-access-authorization)합니다. 그런 다음 나중에 새 AAD 테 넌 트로 이동할 수 있습니다.

관련 된 모든 Azure 파일 동기화 리소스가 자체 구독으로 sequestered 면 전체 구독을 대상 AAD 테 넌 트로 이동할 준비가 된 것입니다. [구독 이전 가이드](../../role-based-access-control/transfer-subscription.md) 를 사용 하 여 이러한 전송을 계획 하 고 실행할 수 있습니다.

> [!WARNING]
> 한 테 넌 트에서 다른 테 넌 트로 구독을 전송 하면 동기화가 즉시 중지 됩니다. 새 구독의 관련 저장소 계정에 액세스 하려면 동기화를 수동으로 승인 해야 합니다. [Azure 파일 동기화 저장소 액세스 권한 부여](#azure-file-sync-storage-access-authorization) 섹션에서는 필요한 단계를 제공 합니다.

:::row:::
    :::column:::
        :::image type="content" source="media/storage-sync-resource-move/storage-sync-resource-move-aad-tenant.png" alt-text="페이지 위쪽의 가운데에 있는 디렉터리 변경 도구 모음 명령을 강조 표시 하는 Azure Portal 구독 개요 블레이드를 보여 주는 그림입니다." lightbox="media/storage-sync-resource-move/storage-sync-resource-move-aad-tenant-expanded.png":::
    :::column-end:::
    :::column:::
        계획 및 필요한 권한이 있으면 마이그레이션을 시작할 준비가 된 것입니다.
        1. Azure Portal에서 구독, **개요** 블레이드로 이동 합니다.
        1. **디렉터리 변경** 을 선택합니다.
        1. 마법사의 단계에 따라 새 AAD 테 넌 트를 할당 합니다.
    :::column-end:::
:::row-end:::

## <a name="azure-file-sync-storage-access-authorization"></a>저장소 액세스 권한 부여 Azure 파일 동기화

저장소 계정을 새 구독으로 이동 하거나 구독 내에서 AAD (새 Azure Active Directory) 테 넌 트로 이동 하면 동기화가 중지 됩니다. RBAC (역할 기반 액세스)는 저장소 계정에 액세스 하는 Azure 파일 동기화 권한을 부여 하는 데 사용 되며 이러한 역할 할당은 리소스로 마이그레이션되지 않습니다.

### <a name="azure-file-sync-service-principal"></a>Azure 파일 동기화 서비스 주체

:::row:::
    :::column:::
        :::image type="content" source="media/storage-sync-resource-move/storage-sync-resource-move-afs-rp-registered-small.png" alt-text="Azure Portal, 구독 관리, 등록 된 리소스 공급자를 보여 주는 이미지입니다." lightbox="media/storage-sync-resource-move/storage-sync-resource-move-afs-rp-registered.png":::
    :::column-end:::
    :::column:::
        저장소 계정에 대 한 동기화 액세스 권한을 부여 하려면 먼저 AAD 테 넌 트에 Azure 파일 동기화 서비스 사용자가 있어야 합니다. </br></br> 지금 새 Azure 구독을 만들 때 Azure 파일 동기화 리소스 공급자 *microsoft.storagesync* 가 구독에 자동으로 등록 됩니다. 리소스 공급자를 등록 하면 구독을 제어 하는 Azure Active Directory 테 넌 트에서 동기화에 대 한 *서비스 주체* 를 사용할 수 있습니다. 서비스 사용자는 AAD의 사용자 계정과 유사 합니다. Azure 파일 동기화 서비스 주체를 사용 하 여 RBAC (역할 기반 액세스 제어)를 통해 리소스에 대 한 액세스 권한을 부여할 수 있습니다. 동기화 해야 하는 파일 공유가 포함 된 저장소 계정인 유일한 리소스 동기화는에 액세스 해야 합니다. *Microsoft.storagesync* 는 저장소 계정의 기본 제공 역할 **판독기 및 데이터 액세스** 에 할당 되어야 합니다. </br></br> 이 할당은 동기화 그룹에 파일 공유를 추가할 때 로그온 한 사용자의 사용자 컨텍스트를 통해 자동으로 수행 됩니다. 즉, 클라우드 끝점을 만듭니다. 저장소 계정이 새 구독 또는 AAD 테 넌 트로 이동 하는 경우이 역할 할당은 손실 되며 [수동으로](#establish-sync-access-to-a-storage-account)다시 설정 해야 합니다.
    :::column-end:::
:::row-end:::

> [!IMPORTANT]
> 대상 Azure 구독이 최근에 생성 되지 않은 경우 *microsoft.storagesync* 리소스 공급자가 구독에 등록 되었는지 확인 합니다. 그렇지 않은 경우 동일한 포털 블레이드에서 수동으로 추가 합니다.

### <a name="establish-sync-access-to-a-storage-account"></a>저장소 계정에 대 한 동기화 액세스 설정

[Azure 파일 동기화 서비스 주체](#azure-file-sync-service-principal) 는 RBAC (역할 기반 액세스 제어)를 통해 저장소 계정에 대 한 액세스 권한을 부여 하는 데 사용 해야 합니다. *Microsoft.storagesync* 는 저장소 계정의 기본 제공 역할 **판독기 및 데이터 액세스** 에 할당 되어야 합니다. 

이 할당은 일반적으로 동기화 그룹에 파일 공유를 추가할 때 로그온 한 사용자의 사용자 컨텍스트를 통해 자동으로 수행 됩니다. 즉, 클라우드 끝점을 만듭니다. 그러나 저장소 계정이 새 구독 또는 AAD 테 넌 트로 이동 하는 경우이 역할 할당은 손실 되며 수동으로 다시 설정 해야 합니다.

:::row:::
    :::column:::
        :::image type="content" source="media/storage-sync-resource-move/storage-sync-resource-move-assign-rbac.png" alt-text="저장소 계정에서 판독기 및 데이터 액세스 역할에 할당 된 Microsoft.storagesync 서비스 주체를 표시 하는 이미지":::
    :::column-end:::
    :::column:::
        Azure Portal에서 다시 인증 sync access로 이동 해야 하는 저장소 계정으로 이동 합니다. <ol><li>왼쪽 목차에서 **Access control (IAM)** 을 선택 합니다.</li><li>역할 할당 탭을 선택 하 여 저장소 계정에 대 한 액세스 권한이 있는 사용자 및 응용 프로그램 (서비스 사용자)을 나열 합니다.</li><li>**추가** 를 선택합니다.</li><li>**역할 필드** 에서 **판독기 및 데이터 액세스** 를 선택 합니다.</li><li>**선택 필드** 에 *microsoft.storagesync* 를 입력 하 고 역할을 선택한 다음 **저장** 을 클릭 합니다.</li></ol>
    :::column-end:::
:::row-end:::

## <a name="move-to-a-different-azure-region"></a>다른 Azure 지역으로 이동

Azure 파일 동기화 리소스 *저장소 동기화 서비스* 와 동기화 중인 파일 공유를 포함 하는 저장소 계정에는 배포 된 Azure 지역이 있습니다. 리소스를 만들 때 해당 지역을 결정 합니다. 저장소 동기화 서비스와 저장소 계정 리소스의 지역은 일치 해야 합니다. 이러한 지역은 만든 후에 두 리소스 유형에 서 변경할 수 없습니다.

리소스에 다른 영역을 할당 하는 것은 저장소 계정 중복 설정에 따라 지원 될 수 있는 [지역 장애 조치 (failover)](#region-fail-over)와 다릅니다.

## <a name="region-fail-over"></a>지역 장애 조치 (failover)

Azure storage는 저장소 계정에 대 한 [지리적 중복 옵션을 제공](../common/storage-redundancy.md#geo-redundant-storage) 합니다. 이러한 중복성 옵션은 Azure 파일 동기화에 사용 되는 저장소 계정에 문제가 발생할 수 있습니다. 주된 이유는 지리적으로 멀리 떨어진 지역 간 복제는 Azure 파일 동기화에서 수행 되지 않지만 Azure의 저장소 하위 시스템에 기본 제공 되는 저장소 복제 기술에 의해 수행 됩니다. 응용 프로그램 상태를 이해할 수 없으며, 지정 된 순간에 Azure 파일 공유와 파일을 동기화 하는 파일을 포함 하는 응용 프로그램을 Azure 파일 동기화 합니다. 이러한 지리적으로 분산 된 저장소 중복 옵션을 선택 하는 경우 대규모 재해의 모든 데이터가 손실 되는 것은 아닙니다. 그러나 [데이터 손실이 예상](../common/storage-disaster-recovery-guidance.md#anticipate-data-loss)되어야 합니다.

> [!CAUTION]
> 장애 조치 (failover)는 올바른 Azure 지역에서 리소스를 프로 비전 하는 데 적절 한 대체가 아닙니다. 리소스가 "잘못 된" 지역에 있는 경우 동기화를 중지 하 고 원하는 지역에 배포 된 새 Azure 파일 공유로 다시 동기화를 설정 하는 것을 고려해 야 합니다.

지역 장애 조치 (failover)는 오랜 시간 동안 Azure 지역 자격이 상실 데이터 센터를 렌더링 하는 치명적인 이벤트로 Microsoft에서 시작할 수 있습니다. 비즈니스에서 유지할 수 있는 가동 중지 시간에 대 한 정의는 지역 장애 조치 (failover)를 시작 하기 전에 Microsoft가 통과할 수 있도록 준비 된 시간 보다 적을 수 있습니다. 이와 같은 상황에서 failover)가는 [고객에 의해 시작 될 수도 있습니다](../common/storage-initiate-account-failover.md).

> [!IMPORTANT]
> 장애 조치 (failover) 시 동기화를 다시 수행 하려면 영향을 받는 저장소 동기화 서비스에 대 한 지원 티켓을 파일에 저장 해야 합니다.

## <a name="see-also"></a>참고 항목

- [Azure 파일 공유 및 동기화 마이그레이션 가이드 개요](storage-files-migration-overview.md)
- [Azure 파일 동기화 문제 해결](storage-sync-files-troubleshoot.md)
- [Azure 파일 동기화 배포 계획](storage-sync-files-planning.md)
