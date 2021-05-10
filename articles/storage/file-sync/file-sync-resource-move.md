---
title: Azure 파일 동기화 리소스 이동 및 토폴로지 변경
description: 리소스 그룹, 구독 및 AAD(Azure Active Directory) 테넌트 간에 동기화 리소스를 이동하는 방법에 대해 알아봅니다.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 04/13/2021
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: fb59b8d48d8411ade620304fbd143d86ac5ae919
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107796438"
---
# <a name="move-azure-file-sync-resources-to-a-different-resource-group-subscription-or-aad-tenant"></a>다른 리소스 그룹, 구독 또는 AAD 테넌트로 Azure 파일 동기화 리소스 이동

이 문서에서는 Azure 파일 동기화 클라우드 리소스 및 Azure Storage 계정의 리소스 그룹, 구독 또는 AAD(Azure Active Directory) 테넌트를 변경하는 방법을 설명합니다.

Azure 파일 동기화 클라우드 리소스를 변경하려는 경우 스토리지 리소스를 함께 고려하는 것이 중요합니다. 다음과 같은 리소스가 있습니다.

**Azure 파일 동기화 리소스(계층적 순서로)**

* :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-storage-sync-service.png" border="false"::: 스토리지 동기화 서비스
  * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-registered-servers.png" border="false"::: 등록된 서버
  * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-sync-group.png" border="false"::: 동기화 그룹
    * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-cloud-endpoint.png" border="false"::: 클라우드 엔드포인트
    * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-server-endpoint.png" border="false"::: 서버 엔드포인트

Azure 파일 동기화에서 이동 가능한 유일한 리소스는 스토리지 동기화 서비스 리소스입니다. 하위 리소스는 해당 부모에 바인딩되므로 다른 스토리지 동기화 서비스로 이동할 수 없습니다.

**Azure Storage 리소스(계층적 순서로)**

* :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-storage-account.png" border="false"::: 스토리지 계정
    * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-file-share.png" border="false"::: 파일 공유

Azure Storage에서 이동 가능한 유일한 리소스는 스토리지 계정입니다. 하위 리소스인 Azure 파일 공유는 다른 스토리지 계정으로 이동할 수 없습니다.

## <a name="supported-combinations"></a>지원되는 조합

리소스 이동을 계획할 때 스토리지 계정 및 *스토리지 동기화 서비스* 라고 하는 최상위 Azure 파일 동기화 리소스를 함께 고려해야 합니다.

모범 사례로, 스토리지 동기화 서비스 및 파일 공유를 동기화하는 스토리지 계정은 동일한 구독에 상주해야 합니다. 다음과 같은 조합이 지원됩니다.

* 스토리지 동기화 서비스와 스토리지 계정이 **서로 다른 리소스 그룹**(동일한 Azure 테넌트)에 있음
* 스토리지 동기화 서비스와 스토리지 계정이 **서로 다른 구독**(동일한 Azure 테넌트)에 있음

> [!IMPORTANT]
> 여러 이동 조합을 통해 스토리지 동기화 서비스와 스토리지 계정이 서로 다른 AAD 테넌트에 의해 관리되는 구독에 위치하게 될 수 있습니다. 동기화가 작동하는 것처럼 보일 수 있지만 이 구성은 지원되지 않습니다. 향후에는 작동하는 상태로 돌아갈 수 없는 상황에서 동기화가 중지될 수 있습니다.

리소스 이동을 계획할 때 [동일한 AAD 테넌트 내에서 이동](#move-within-the-same-azure-active-directory-tenant)하고 [다른 AAD 테넌트](#move-to-a-new-azure-active-directory-tenant)로 이동하는 경우에는 여러 가지 사항을 고려해야 합니다. AAD 테넌트를 이동하는 경우 항상 동기화 및 스토리지 리소스를 함께 이동합니다.

### <a name="move-within-the-same-azure-active-directory-tenant"></a>동일한 Azure Active Directory 테넌트 내에서 이동

:::row:::
    :::column:::
        :::image type="content" source="media/storage-sync-resource-move/storage-sync-resource-move-small.png" alt-text="확장된 이동 명령과 함께 Azure Portal의 스토리지 동기화 서비스 리소스를 보여주는 이미지입니다. 이는 리소스 그룹 이동 및 구독 이동 옵션을 보여줍니다." lightbox="media/storage-sync-resource-move/storage-sync-resource-move.png":::
    :::column-end:::
    :::column:::
        스토리지 동기화 서비스 리소스를 이동하는 편리한 방법은 Azure Portal을 사용하는 것입니다. 이동하려는 스토리지 동기화 서비스로 이동하고 명령 모음에서 **이동** 을 선택합니다. 스토리지 계정 이동에도 동일한 단계가 적용됩니다. 이러한 방식으로 리소스 그룹의 모든 리소스를 이동할 수도 있습니다. 스토리지 동기화 서비스와 사용된 모든 스토리지 계정이 이 리소스 그룹에 있는 경우 전체 리소스 그룹을 이동하는 것이 좋습니다.
    :::column-end:::
:::row-end:::

> [!WARNING]
> 스토리지 계정 리소스를 이동하면 동기화가 즉시 중지됩니다. 새 구독의 관련 스토리지 계정에 액세스하려면 동기화 권한을 수동으로 부여해야 합니다. [Azure 파일 동기화 스토리지 액세스 권한 부여](#azure-file-sync-storage-access-authorization) 섹션에서 필요한 단계를 제공합니다.

### <a name="move-to-a-new-azure-active-directory-tenant"></a>새로운 Azure Active Directory 테넌트로 이동

스토리지 동기화 서비스, 스토리지 계정 등의 개별 리소스 자체는 다른 AAD 테넌트로 이동할 수 없습니다. Azure 구독만 AAD 테넌트를 이동할 수 있습니다. 새 AAD 테넌트의 구독 구조에 대해 생각해 보세요. Azure 파일 동기화에 전용 구독을 사용할 수 있습니다. 

1. Azure 구독을 만들거나, 이동해야 하는 이전 테넌트의 기존 구독을 결정합니다.
1. 스토리지 동기화 서비스 및 모든 관련 스토리지 계정의 [동일한 AAD 테넌트 내에서 구독 이동을 수행](#move-within-the-same-azure-active-directory-tenant)합니다.
1. 동기화가 중지됩니다. 즉시 테넌트 이동을 완료하거나 [이동된 스토리지 계정에 액세스하는 동기화 기능을 복원](#azure-file-sync-storage-access-authorization)합니다. 그러면 나중에 새 AAD 테넌트로 이동할 수 있습니다.

관련된 모든 Azure 파일 동기화 리소스가 별개의 구독으로 격리되면 전체 구독을 대상 AAD 테넌트로 이동할 준비가 된 것입니다. [구독 이전 가이드](../../role-based-access-control/transfer-subscription.md)를 사용하여 이러한 이전을 계획하고 실행할 수 있습니다.

> [!WARNING]
> 한 테넌트에서 다른 테넌트로 구독을 이전하면 동기화가 즉시 중지됩니다. 새 구독의 관련 스토리지 계정에 액세스하려면 동기화 권한을 수동으로 부여해야 합니다. [Azure 파일 동기화 스토리지 액세스 권한 부여](#azure-file-sync-storage-access-authorization) 섹션에서 필요한 단계를 제공합니다.

:::row:::
    :::column:::
        :::image type="content" source="media/storage-sync-resource-move/storage-sync-resource-move-aad-tenant.png" alt-text="페이지 위쪽의 가운데에 있는 디렉터리 변경 도구 모음 명령을 강조 표시하는 Azure Portal 구독 개요 블레이드를 보여주는 그림입니다." lightbox="media/storage-sync-resource-move/storage-sync-resource-move-aad-tenant-expanded.png":::
    :::column-end:::
    :::column:::
        계획 및 필요한 권한이 있으면 마이그레이션을 시작할 준비가 된 것입니다.
        1. Azure Portal에서 구독의 **개요** 블레이드로 이동합니다.
        1. **디렉터리 변경** 을 선택합니다.
        1. 마법사의 단계에 따라 새 AAD 테넌트를 할당합니다.
    :::column-end:::
:::row-end:::

## <a name="azure-file-sync-storage-access-authorization"></a>Azure 파일 동기화 스토리지 액세스 권한 부여

스토리지 계정을 새 구독이나 구독 내 새 AAD(Azure Active Directory) 테넌트로 이동하면 동기화가 중지됩니다. RBAC(역할 기반 액세스 제어)는 스토리지 계정에 액세스하는 권한을 Azure 파일 동기화에 부여하는 데 사용되며, 이러한 역할 할당은 리소스와 함께 마이그레이션되지 않습니다.

### <a name="azure-file-sync-service-principal"></a>Azure 파일 동기화 서비스 주체

:::row:::
    :::column:::
        :::image type="content" source="media/storage-sync-resource-move/storage-sync-resource-move-afs-rp-registered-small.png" alt-text="Azure Portal, 구독 관리, 등록된 리소스 공급자를 보여주는 이미지입니다." lightbox="media/storage-sync-resource-move/storage-sync-resource-move-afs-rp-registered.png":::
    :::column-end:::
    :::column:::
        스토리지 계정에 대한 동기화 액세스 권한을 부여하려면 먼저 AAD 테넌트에 Azure 파일 동기화 서비스 주체가 있어야 합니다. </br></br> 지금 새 Azure 구독을 만들면 Azure 파일 동기화 리소스 공급자인 *Microsoft.StorageSync* 가 구독에 자동으로 등록됩니다. 리소스 공급자를 등록하면 구독을 관리하는 Azure Active Directory 테넌트에서 동기화의 *서비스 주체* 를 사용할 수 있습니다. 서비스 주체는 AAD의 사용자 계정과 유사합니다. Azure 파일 동기화 서비스 주체를 사용하면 RBAC(역할 기반 액세스 제어)를 통해 리소스에 대한 액세스 권한을 부여할 수 있습니다. 리소스 동기화를 위해 액세스해야 하는 대상은 동기화할 파일 공유가 포함된 스토리지 계정뿐입니다. *Microsoft.StorageSync* 는 스토리지 계정의 **읽기 권한자 및 데이터 액세스** 기본 제공 역할에 할당되어야 합니다. </br></br> 이 할당은 동기화 그룹에 파일 공유를 추가할 때 로그온한 사용자의 사용자 컨텍스트를 통해 자동으로 이루어집니다. 즉, 클라우드 엔드포인트를 만들 때 이루어집니다. 스토리지 계정이 새 구독 또는 AAD 테넌트로 이동하는 경우 이 역할 할당은 손실되며 [수동으로 다시 설정](#establish-sync-access-to-a-storage-account)해야 합니다.
    :::column-end:::
:::row-end:::

> [!IMPORTANT]
> 대상 Azure 구독이 최근에 생성된 것이 아닌 경우 *Microsoft.StorageSync* 리소스 공급자가 구독에 등록되어 있는지 확인합니다. 등록되어 있지 않으면 동일한 포털 블레이드에서 수동으로 추가합니다.

### <a name="establish-sync-access-to-a-storage-account"></a>스토리지 계정에 대한 동기화 액세스 설정

[Azure 파일 동기화 서비스 주체](#azure-file-sync-service-principal)는 RBAC(역할 기반 액세스 제어)를 통해 스토리지 계정에 대한 액세스 권한을 부여하는 데 사용해야 합니다. *Microsoft.StorageSync* 는 스토리지 계정의 **읽기 권한자 및 데이터 액세스** 기본 제공 역할에 할당되어야 합니다. 

이 할당은 일반적으로 동기화 그룹에 파일 공유를 추가할 때 로그온한 사용자의 사용자 컨텍스트를 통해 자동으로 이루어집니다. 즉, 클라우드 엔드포인트를 만들 때 이루어집니다. 하지만 스토리지 계정이 새 구독 또는 AAD 테넌트로 이동하는 경우 이 역할 할당은 손실되며 수동으로 다시 설정해야 합니다.

:::row:::
    :::column:::
        :::image type="content" source="media/storage-sync-resource-move/storage-sync-resource-move-assign-rbac.png" alt-text="스토리지 계정의 읽기 권한자 및 데이터 액세스 역할에 할당된 Microsoft.StorageSync 서비스 주체를 보여주는 이미지":::
    :::column-end:::
    :::column:::
        Azure Portal에서 동기화 액세스 권한을 다시 부여해야 하는 스토리지 계정으로 이동합니다. <ol><li>왼쪽 목차에서 **액세스 제어(IAM)** 를 선택합니다.</li><li>역할 할당 탭을 선택하여 스토리지 계정에 액세스할 수 있는 사용자 및 애플리케이션(서비스 주체)을 나열합니다.</li><li>**추가** 를 선택합니다.</li><li>**역할 필드** 에서 **읽기 권한자 및 데이터 액세스** 를 선택합니다.</li><li>**선택 필드** 에서 *Microsoft.StorageSync* 를 입력하고 역할을 선택한 다음, **저장** 을 클릭합니다.</li></ol>
    :::column-end:::
:::row-end:::

## <a name="move-to-a-different-azure-region"></a>다른 Azure 지역으로 이동

Azure 파일 동기화 리소스인 *스토리지 동기화 서비스*, 그리고 동기화 중인 파일 공유를 포함하는 스토리지 계정에는 배포 대상이 되는 Azure 지역이 있습니다. 해당 지역은 리소스를 만들 때 결정합니다. 스토리지 동기화 서비스와 스토리지 계정 리소스의 지역은 일치해야 합니다. 리소스를 만든 후에는 두 리소스 유형에서 이러한 지역을 변경할 수 없습니다.

리소스에 다른 지역을 할당하는 것은 [지역 장애 조치(failover)](#region-fail-over)와 다르며, 스토리지 계정 중복 설정에 따라 지원 가능합니다.

## <a name="region-fail-over"></a>지역 장애 조치(failover)

스토리지 계정에 대해 [Azure Storage는 지역 중복 옵션을 제공](../common/storage-redundancy.md#geo-redundant-storage)합니다. 이러한 중복 옵션으로 인해 Azure 파일 동기화에 사용되는 스토리지 계정에 문제가 발생할 수 있습니다. 주된 이유는 지리적으로 멀리 떨어진 지역 간 복제가 Azure 파일 동기화가 아니라 Azure의 스토리지 하위 시스템에 기본 제공되는 스토리지 복제 기술에 의해 수행되기 때문입니다. 해당 기술은 애플리케이션 상태를 파악할 수 없으며, Azure 파일 동기화는 특정 시점에 Azure 파일 공유와 파일을 동기화하는 애플리케이션입니다. 이렇게 지리적으로 분산된 스토리지 중복 옵션을 선택한다고 해서 대규모 재해가 발생했을 때 모든 데이터가 손실되는 것은 아닙니다. 그러나 [데이터 손실을 예상](../common/storage-disaster-recovery-guidance.md#anticipate-data-loss)해야 합니다.

> [!CAUTION]
> 장애 조치(failover)는 올바른 Azure 지역의 리소스를 프로비전하는 것에 대한 적절한 대안이 아닙니다. 리소스가 “잘못된” 지역에 있는 경우 동기화를 중지하고 원하는 지역에 배포된 새 Azure 파일 공유로 다시 동기화를 설정하는 것을 고려해야 합니다.

오랜 시간 동안 Azure 지역의 데이터 센터가 마비되는 치명적인 상황이 발생하면 Microsoft에서 지역 장애 조치(failover)를 시작할 수 있습니다. 비즈니스에서 감당할 수 있는 가동 중지 시간에 대한 정의는 Microsoft에서 지역 장애 조치(failover)를 위해 준비하는 시간보다 짧을 수 있습니다. 이와 같은 상황에서 [장애 조치(failover)는 고객이 시작할 수도 있습니다](../common/storage-initiate-account-failover.md).

> [!IMPORTANT]
> 장애 조치(failover) 시 동기화를 다시 수행하려면 영향을 받는 스토리지 동기화 서비스에 대한 지원 티켓을 제출해야 합니다.

## <a name="see-also"></a>참조

- [Azure 파일 공유 및 동기화 마이그레이션 가이드 개요](../files/storage-files-migration-overview.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json)
- [Azure 파일 동기화 문제 해결](file-sync-troubleshoot.md)
- [Azure 파일 동기화 배포 계획](file-sync-planning.md)