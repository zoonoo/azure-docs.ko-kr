---
title: Azure 예약 구입 준비
description: Azure 예약을 구입하기 전에 중요 사항에 대해 알아보세요.
author: bandersmsft
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: banders
ms.openlocfilehash: 1f5ca2d43356eab98cffe8414c00d97e5744739a
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/25/2020
ms.locfileid: "80235662"
---
# <a name="prepare-to-buy-a-reservation"></a>예약 구입 준비

Azure Reservations는 여러 Azure 리소스의 1년 또는 3년 플랜에 따라 비용을 절감하는 데 도움이 됩니다. 예약 구입 약정을 시작하기 전에 구매 준비를 위해 다음 섹션을 검토해야 합니다.

## <a name="who-can-buy-a-reservation"></a>예약을 구입할 수 있는 사람

플랜을 구매하려면 엔터프라이즈(MS-AZR-0017P 또는 MS-AZR-0148P), 종량제 구독(MS-AZR-0003P 또는 MS-AZR-0023P) 또는 Microsoft 고객 계약 구독의 구독 소유자 역할이 있어야 합니다. 클라우드 솔루션 공급자는 Azure Portal 또는  [파트너 센터](/partner-center/azure-reservations) 를 사용하여 Azure 예약을 구매할 수 있습니다.

EA(기업계약) 고객은 EA Portal에서 **예약 인스턴스 추가** 옵션을 해제하여 EA 관리자만 구매 가능하도록 제한할 수 있습니다. 예약을 구매하려면 EA 관리자가 하나 이상의 EA 구독에 대한 구독 소유자여야 합니다. 이 옵션은 중앙의 팀이 다양한 비용 센터의 예약을 대신 구매하도록 하려는 기업에 유용합니다. 구매 후에는 중앙의 팀이 예약에 비용 센터 소유자를 추가할 수 있습니다. 그러면 소유자는 예약의 범위를 구독으로 지정할 수 있습니다. 중앙의 팀은 예약을 구매하는 구독 소유자 액세스 권한이 필요 없습니다.

예약 할인은 엔터프라이즈, CSP(클라우드 솔루션 공급자), Microsoft 고객 계약 및 종량제 요금을 사용하는 개별 플랜을 통해 구매한 구독과 연결된 리소스에만 적용됩니다.

## <a name="scope-reservations"></a>예약 범위 지정

예약의 범위를 구독 또는 리소스 그룹으로 지정할 수 있습니다. 예약의 범위를 지정하면 예약 할인이 적용되는 위치가 선택됩니다. 예약의 범위를 리소스 그룹으로 지정하면 구독 전체가 아닌 리소스 그룹에만 예약 할인이 적용됩니다.

### <a name="reservation-scoping-options"></a>예약 범위 지정 옵션

요구 사항에 따라 예약 범위를 지정하는 세 가지 옵션이 있습니다.

- **단일 리소스 그룹 범위** — 선택한 리소스 그룹의 일치하는 리소스에만 예약 할인을 적용합니다.
- **단일 구독 범위** — 선택한 구독의 일치하는 리소스에만 예약 할인을 적용합니다.
- **공유 범위** — 청구 컨텍스트에 있는 적격 구독의 일치하는 리소스에 예약 할인을 적용합니다. 기업계약 고객의 경우 청구 컨텍스트는 등록입니다. Microsoft 고객 계약 고객의 경우 청구 범위는 청구 프로필입니다. 종량제 요금이 적용되는 개별 구독의 경우 청구 범위는 계정 관리자가 만든 모든 적격 구독입니다.

사용 요금에 예약 할인을 적용할 때 Azure는 다음과 같은 순서로 예약을 처리합니다.

1. 리소스 그룹으로 범위가 지정된 예약
2. 단일 범위 예약
3. 공유 범위 예약

단일 리소스 그룹은 예약의 범위를 지정하는 방법에 따라 여러 예약의 예약 할인을 받을 수 있습니다.

예약을 구매한 후 언제든지 범위를 업데이트할 수 있습니다. 업데이트하려면 예약으로 이동하고, **구성**을 클릭하여 예약 범위를 다시 지정합니다. 예약 범위 재지정은 상거래 트랜잭션이 아닙니다. 따라서 예약 기간이 변경되지 않았습니다. 범위 업데이트에 대한 자세한 내용은 [예약을 구매한 후 범위 업데이트](manage-reserved-vm-instance.md#change-the-reservation-scope)를 참조하세요.

![예약 범위 변경을 보여주는 예제](./media/prepare-buy-reservation/rescope-reservation-resource-group.png)

## <a name="purchase-reservations"></a>예약 구매

Azure Portal, API, PowerShell, CLI에서 예약을 구매할 수 있습니다. 예약 구매를 준비할 때 적용되는 다음 문서를 읽어보세요.

- [App Service](prepay-app-service-isolated-stamp.md)
- [Azure Cache for Redis](../../azure-cache-for-redis/cache-reserved-pricing.md)
- [Cosmos DB](../../cosmos-db/cosmos-db-reserved-capacity.md)
- [Databricks](prepay-databricks-reserved-capacity.md)
- [Data Explorer](../../data-explorer/pricing-reserved-capacity.md)
- [Disk Storage](../../virtual-machines/linux/disks-reserved-capacity.md)
- [Dedicated Host](../../virtual-machines/prepay-dedicated-hosts-reserved-instances.md)
- [소프트웨어 플랜](../../virtual-machines/linux/prepay-suse-software-charges.md)
- [스토리지](../../storage/blobs/storage-blob-reserved-capacity.md)
- [SQL 데이터베이스](../../sql-database/sql-database-reserved-capacity.md)
- [SQL Data Warehouse](prepay-sql-data-warehouse-charges.md)
- [가상 머신](../../virtual-machines/windows/prepay-reserved-vm-instances.md)

## <a name="next-steps"></a>다음 단계

- [Azure 리소스에 대한 예약 관리](manage-reserved-vm-instance.md)
