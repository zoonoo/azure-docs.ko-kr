---
title: 개체 복제 개요
titleSuffix: Azure Storage
description: 개체 복제는 원본 저장소 계정과 대상 계정 사이에서 블록 blob을 비동기적으로 복사 합니다. 개체 복제를 사용하여 읽기 요청에 대한 대기 시간을 최소화하고, 컴퓨팅 워크로드에 대한 효율성을 높이고, 데이터 배포를 최적화하며 비용을 최소화합니다.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: ee76c1692049d5b25e85b6780fbcf78f7ebfdd2f
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90987057"
---
# <a name="object-replication-for-block-blobs"></a>블록 blob에 대 한 개체 복제

개체 복제는 원본 저장소 계정과 대상 계정 사이에서 블록 blob을 비동기적으로 복사 합니다. 개체 복제에서 지원하는 몇 가지 시나리오는 다음과 같습니다.

- **대기 시간 최소화.** 개체 복제를 사용하면 클라이언트가 물리적으로 더 근접한 지역의 데이터를 사용할 수 있으므로 읽기 요청 대기 시간을 줄일 수 있습니다.
- **컴퓨팅 작업의 효율성 제고.** 개체 복제를 사용하는 경우 컴퓨팅 워크로드는 서로 다른 지역에서 동일한 블록 Blob 집합을 처리할 수 있습니다.
- **데이터 배포 최적화.** 단일 위치의 데이터를 처리하거나 분석한 후 추가 지역에만 결과를 복제할 수 있습니다.
- **비용 최적화.** 데이터가 복제된 후에는 수명 주기 관리 정책을 사용해 해당 데이터를 보관 계층으로 이동하여 비용을 줄일 수 있습니다.

다음 다이어그램에서는 개체 복제에서 한 지역의 원본 스토리지 계정으로부터 블록 Blob을 두 개의 다른 지역에 있는 대상 계정으로 복제하는 방법을 보여 줍니다.

:::image type="content" source="media/object-replication-overview/object-replication-diagram.svg" alt-text="개체 복제 작동 방식을 보여 주는 다이어그램":::

개체 복제를 구성 하는 방법에 대 한 자세한 내용은 [개체 복제 구성](object-replication-configure.md)을 참조 하세요.

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="prerequisites-for-object-replication"></a>개체 복제를 위한 필수 구성 요소

개체를 복제 하려면 다음 Azure Storage 기능을 사용 하도록 설정 해야 합니다.

- [변경 피드](storage-blob-change-feed.md): 원본 계정에서 사용 하도록 설정 해야 합니다. 변경 피드를 사용 하도록 설정 하는 방법을 알아보려면 [변경 피드 사용 및 사용 안 함](storage-blob-change-feed.md#enable-and-disable-the-change-feed)을 참조 하세요.
- [Blob 버전 관리](versioning-overview.md): 원본 및 대상 계정 모두에서 사용 하도록 설정 해야 합니다. 버전 관리를 사용 하도록 설정 하는 방법을 알아보려면 [blob 버전 관리 사용 및 관리](versioning-enable.md)를 참조 하세요.

변경 피드 및 Blob 버전 관리를 사용하도록 설정하면 추가 비용이 발생할 수 있습니다. 자세한 내용은 [Azure Storage 가격 책정 페이지](https://azure.microsoft.com/pricing/details/storage/)를 참조하세요.

## <a name="object-replication-policies-and-rules"></a>개체 복제 정책 및 규칙

개체 복제를 구성하는 경우 원본 스토리지 계정 및 대상 계정을 지정하는 복제 정책을 만듭니다. 복제 정책에는 원본 컨테이너와 대상 컨테이너를 지정하고 원본 컨테이너에서 복제할 블록 Blob을 나타내는 하나 이상의 규칙이 포함되어 있습니다.

개체 복제를 구성하고 나면 Azure Storage에서 원본 계정에 대한 변경 피드를 정기적으로 확인하고 대상 계정에 대한 쓰기 또는 삭제 작업을 비동기적으로 복제합니다. 복제 대기 시간은 복제 중인 블록 Blob의 크기에 따라 달라집니다.

> [!IMPORTANT]
> 블록 Blob 데이터는 비동기식으로 복제되므로 원본 계정 및 대상 계정이 즉시 동기화되지는 않습니다. 현재 데이터를 대상 계정에 복제하는 데 걸리는 시간에 대한 SLA는 없습니다.

### <a name="replication-policies"></a>복제 정책

개체 복제를 구성하면 Azure Storage 리소스 공급자를 통해 원본 계정 및 대상 계정 모두에 복제 정책이 만들어집니다. 복제 정책은 정책 ID로 식별됩니다. 복제가 발생하려면 원본 및 대상 계정의 정책이 동일한 정책 ID를 가져야 합니다.

스토리지 계정은 최대 두 개의 대상 계정에 대한 원본 계정으로 사용할 수 있습니다. 원본 및 대상 계정이 동일한 지역 또는 다른 지역에 있을 수 있습니다. 또한 서로 다른 구독 및 다른 Azure Active Directory (Azure AD) 테 넌 트에 상주할 수 있습니다. 각 원본 계정/대상 계정 쌍에 대해 하나의 복제 정책만 만들 수 있습니다.

### <a name="replication-rules"></a>복제 규칙

복제 규칙은 Azure Storage 원본 컨테이너에서 대상 컨테이너로 Blob을 복제하는 방법을 지정합니다. 각 복제 정책에 대해 최대 10개의 복제 규칙을 지정할 수 있습니다. 각 복제 규칙은 단일 원본 및 대상 컨테이너를 정의 하며 각 원본 및 대상 컨테이너는 하나의 규칙에만 사용할 수 있습니다.

복제 규칙을 만드는 경우 기본적으로 이후에 원본 컨테이너에 추가되는 새 블록 Blob만 복사됩니다. 새 블록 blob과 기존 블록 blob를 모두 복사 하도록 지정 하거나 지정 된 시간 이후로 생성 된 블록 blob을 복사 하는 사용자 지정 복사 범위를 정의할 수 있습니다.

하나 이상의 필터를 복제 규칙의 일부로 지정하여 접두사를 기준으로 블록 Blob을 필터링할 수도 있습니다. 접두사를 지정하면 원본 컨테이너에서 해당 접두사와 일치하는 Blob만 대상 컨테이너에 복사됩니다.

원본 및 대상 컨테이너가 모두 있어야 규칙에서 지정할 수 있습니다. 복제 정책을 만든 후에 대상 컨테이너는 읽기 전용이 됩니다. 대상 컨테이너에 대한 쓰기 시도가 실패하고 오류 코드 409(충돌)가 발생합니다. 그러나 대상 컨테이너의 blob에 대 한 [Blob 계층 설정](/rest/api/storageservices/set-blob-tier) 작업을 호출 하 여 보관 계층으로 이동할 수 있습니다. 보관 계층에 대 한 자세한 내용은 [Azure Blob storage: 핫, 쿨 및 보관 액세스 계층](storage-blob-storage-tiers.md#archive-access-tier)을 참조 하세요.

## <a name="billing"></a>결제

개체 복제에는 원본 및 대상 계정에 대 한 읽기 및 쓰기 트랜잭션과 함께 원본 계정에서 대상 계정으로 데이터를 복제 하 고 변경 피드를 처리 하는 데 필요한 수신 요금을 부과 하는 추가 비용이 발생 합니다.

## <a name="next-steps"></a>다음 단계

- [개체 복제 구성](object-replication-configure.md)
- [Azure Blob Storage의 변경 피드 지원](storage-blob-change-feed.md)
- [Blob 버전 관리 설정 및 관리](versioning-enable.md)
