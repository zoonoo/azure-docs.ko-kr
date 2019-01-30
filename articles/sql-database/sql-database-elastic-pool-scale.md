---
title: 탄력적 풀 리소스 크기 조정 - Azure SQL Database | Microsoft Docs
description: 이 페이지에서는 Azure SQL Database에서 탄력적 풀의 리소스 크기를 조정하는 방법을 설명합니다.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: carlrab
manager: craigg
ms.date: 10/15/2018
ms.openlocfilehash: 04fcb84b22e84060652b4a3743cb73e4543ee573
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52868449"
---
# <a name="scale-elastic-pool-resources-in-azure-sql-database"></a>Azure SQL Database에서 탄력적 풀 리소스 크기 조정

이 아티클에서는 Azure SQL Database에서 탄력적 풀 및 풀링된 데이터베이스에 사용할 수 있는 계산 및 저장소 리소스의 크기를 조정하는 방법을 설명합니다.

## <a name="vcore-based-purchasing-model-change-elastic-pool-storage-size"></a>vCore 기반 구매 모델: 탄력적 풀 스토리지 크기 변경

- 저장소는 최대 크기 제한까지 프로비전할 수 있습니다.

  - 표준 저장소의 경우, 크기는 10GB 증분 단위로 늘리거나 줄입니다.
  - 프리미엄 저장소의 경우, 크기는 250GB 증분 단위로 늘리거나 줄입니다.
- 최대 크기를 늘리거나 줄여서 탄력적 풀의 저장소를 프로비전할 수 있습니다.
- 탄력적 풀에 대한 저장소의 가격은 저장소 용량에 해당 서비스 계층의 저장소 단가를 곱한 값입니다. 추가 저장소 가격에 대한 자세한 내용은 [SQL Database 가격 책정](https://azure.microsoft.com/pricing/details/sql-database/)을 참조하세요.

> [!IMPORTANT]
> 경우에 따라 사용하지 않는 공간을 회수하기 위해 데이터베이스를 축소해야 할 수도 있습니다. 자세한 내용은 [Azure SQL Database의 파일 공간 관리](sql-database-file-space-management.md)를 참조하세요.

## <a name="vcore-based-purchasing-model-change-elastic-pool-compute-resources-vcores"></a>vCore 기반 구매 모델: 탄력적 풀 컴퓨팅 리소스 변경(vCore)

[Azure Portal](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqlelasticpool), [Azure CLI](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update) 또는 [REST API](https://docs.microsoft.com/rest/api/sql/elasticpools/update)를 사용하여 리소스 요구에 따라 탄력적 풀에 대한 계산 크기를 늘리거나 줄일 수 있습니다.

- 탄력적 풀에서 vCore의 크기를 다시 조정하면 데이터베이스 연결이 잠시 끊깁니다. 이 동작은 단일 데이터베이스에 대한 DTU 크기를 다시 조정할 때 발생하는 동작과 동일합니다. 크기 재조정 작업 중에 데이터베이스에 대한 연결 끊김으로 인한 영향과 기간에 대한 자세한 내용은 [계산 리소스(DTU) 변경](sql-database-single-database-scale.md#dtu-based-purchasing-model-change-compute-resources-dtus)을 참조하세요.
- 풀 vCore 수를 다시 조정하는 기간은 풀의 모든 데이터베이스에서 사용하는 저장소 공간의 총 크기에 따라 달라질 수 있습니다. 일반적으로 크기 조정 대기 시간은 100GB당 평균 90분 이하입니다. 예를 들어 풀에 있는 모든 데이터베이스에서 사용 중인 총 공간이 200GB일 경우, 크기 조정을 위해 예상되는 대기 시간은 3시간 이하입니다. 표준 또는 기본 계층 내의 일부 경우에는 크기 조정 대기 시간이 사용 중인 공간 크기에 관계 없이 5분 이내일 수 있습니다.
- 일반적으로 데이터베이스당 최소 vCore 수 또는 데이터베이스당 최대 vCore 수를 변경하는 기간은 5분 이내입니다.
- 풀 vCore 수를 줄이는 경우 풀 사용 공간은 대상 서비스 계층 및 풀 vCore 수에 허용되는 최대 크기보다 작아야 합니다.

## <a name="dtu-based-purchasing-model-change-elastic-pool-storage-size"></a>DTU 기반 구매 모델: 탄력적 풀 스토리지 크기 변경

- 탄력적 풀에 대한 eDTU 가격에는 특정 크기의 저장소가 추가 비용 없이 포함됩니다. 포함된 용량 외 추가 저장소는 최대 250GB씩 총 1TB이 최대 크기 제한까지 추가 비용을 내고 프로비전할 수 있고 1TB 이상일 경우 256GB씩 프로비전할 수 있습니다. 포함된 저장소 크기 및 최대 크기 제한에 대한 자세한 내용은 [탄력적 풀: 저장소 크기 및 계산 크기](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes)를 참조하세요.
- 탄력적 풀에 대한 추가 저장소는 [Azure portal](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqlelasticpool), [Azure CLI](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update) 또는 [REST API](https://docs.microsoft.com/rest/api/sql/elasticpools/update)를 통해 해당 최대 크기를 늘려 프로비전할 수 있습니다.
- 탄력적 풀에 대한 추가 저장소 가격은 추가 저장소 용량에 해당 서비스 계층의 추가 저장소 단가를 곱한 것입니다. 추가 저장소 가격에 대한 자세한 내용은 [SQL Database 가격 책정](https://azure.microsoft.com/pricing/details/sql-database/)을 참조하세요.

> [!IMPORTANT]
> 경우에 따라 사용하지 않는 공간을 회수하기 위해 데이터베이스를 축소해야 할 수도 있습니다. 자세한 내용은 [Azure SQL Database의 파일 공간 관리](sql-database-file-space-management.md)를 참조하세요.

## <a name="dtu-based-purchasing-model-change-elastic-pool-compute-resources-edtus"></a>DTU 기반 구매 모델: 탄력적 풀 컴퓨팅 리소스 변경(eDTU)

[Azure Portal](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqlelasticpool), [Azure CLI](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update) 또는 [REST API](https://docs.microsoft.com/rest/api/sql/elasticpools/update)를 사용하여 리소스 필요량에 따라 탄력적 풀에 사용할 수 있는 리소스를 늘리거나 줄일 수 있습니다.

- 풀 eDTU 크기를 조정하는 경우 데이터베이스 연결이 잠시 끊어집니다. 이 동작은 단일 데이터베이스에 대한 DTU 크기를 다시 조정할 때 발생하는 동작과 동일합니다. 크기 재조정 작업 중에 데이터베이스에 대한 연결 끊김으로 인한 영향과 기간에 대한 자세한 내용은 [계산 리소스(DTU) 변경](sql-database-single-database-scale.md#dtu-based-purchasing-model-change-compute-resources-dtus)을 참조하세요.
- 풀 eDTU의 크기를 조정하는 기간은 풀의 모든 데이터베이스에 사용되는 저장소 공간의 총 크기에 따라 달라질 수 있습니다. 일반적으로 크기 조정 대기 시간은 100GB당 평균 90분 이하입니다. 예를 들어 풀에 있는 모든 데이터베이스에서 사용 중인 총 공간이 200GB일 경우, 크기 조정을 위해 예상되는 대기 시간은 3시간 이하입니다. 표준 또는 기본 계층 내의 일부 경우에는 크기 조정 대기 시간이 사용 중인 공간 크기에 관계 없이 5분 이내일 수 있습니다.
- 일반적으로 데이터베이스당 최소 eDTU 또는 데이터베이스당 최대 eDTU 변경에 따른 대기 시간은 5분 이내입니다.
- 탄력적 풀에 대한 eDTU 크기를 줄이는 경우 풀 사용 공간이 대상 서비스 계층 및 풀 eDTU에 허용되는 최대 크기보다 작아야 합니다.
- 탄력적 풀에 대한 eDTU 크기를 다시 조정하는 경우 (1) 풀의 최대 저장소 크기가 대상 풀에 지원되고, (2) 최대 저장소 크기가 대상 풀에 포함된 저장소 용량을 초과하면 추가 저장소 비용이 적용됩니다. 예를 들어 최대 크기가 100GB인 100 eDTU 표준 풀 크기를 50 eDTU 표준 풀로 줄이는 경우 대상 풀이 100GB의 최대 크기를 지원하고 포함된 저장소 크기는 50GB에 불과하므로 추가 저장소 비용이 적용됩니다. 따라서 추가 저장소 크기는 100GB – 50GB = 50GB입니다. 추가 저장소 가격 책정에 대한 자세한 내용은 [SQL Database 가격 책정](https://azure.microsoft.com/pricing/details/sql-database/)을 참조하세요. 실제 사용된 공간의 크기가 포함된 저장소의 크기보다 작은 경우 데이터베이스 최대 크기를 포함된 크기로 줄여 이러한 추가 비용을 방지할 수 있습니다.

## <a name="next-steps"></a>다음 단계

전반적인 리소스 제한은 [SQL Database vCore 기반 리소스 제한 - 탄력적 풀](sql-database-vcore-resource-limits-elastic-pools.md) 및 [SQL Database DTU 기반 리소스 제한 - 탄력적 풀](sql-database-dtu-resource-limits-elastic-pools.md)을 참조하세요.
