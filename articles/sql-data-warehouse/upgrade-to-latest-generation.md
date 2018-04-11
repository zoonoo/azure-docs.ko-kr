---
title: 최신 세대 Azure SQL Data Warehouse로 업그레이드 | Microsoft Docs
description: Azure SQL Data Warehouse를 최신 세대 Azure 하드웨어와 저장소 아키텍처로 업그레이드하는 단계입니다.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.services: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/02/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 42b716274e655bf91f72c1b3ab207b8a5f1ccee0
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2018
---
# <a name="upgrade-to-latest-generation-of-azure-sql-data-warehouse-in-the-azure-portal"></a>Azure Portal에서 최신 세대 Azure SQL Data Warehouse로 업그레이드 

Azure Portal을 사용하여 Azure SQL Data Warehouse를 최신 세대 Azure 하드웨어와 저장소 아키텍처로 업그레이드하는 단계입니다. 업그레이드하면 더 빠른 성능, 더 높은 확장성, 무제한 columnstore 인덱스 저장을 활용할 수 있습니다.  

## <a name="applies-to"></a>적용 대상
이 업그레이드는 Optimized for Elasticity 성능 계층의 데이터 웨어하우스에 적용됩니다.  이 지침에서는 Optimized for Elasticity 성능 계층에서 Optimized for Compute 성능 계층으로 데이터 웨어하우스를 업그레이드합니다. 

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure 포털](https://portal.azure.com/)에 로그인합니다.

## <a name="before-you-begin"></a>시작하기 전에

1. 업그레이드 대상인 Optimized for Elasticity 데이터 웨어하우스가 일시 중지된 경우 [데이터 웨어하우스를 다시 시작](pause-and-resume-compute-portal.md)합니다.
2. 몇 분 정도의 가동 중지 시간에 대비합니다. 
3. 업그레이드 프로세스에서는 모든 세션이 해제되고 모든 연결이 끊어집니다. 업그레이드하기 전에 쿼리 완료를 확인합니다. 진행 중인 트랜잭션이 있는 상태에서 업그레이드를 시작하면 롤백 시간이 매우 길어질 수 있습니다. 

## <a name="start-the-upgrade"></a>업그레이드 시작

1. Azure Portal에서 데이터 웨어하우스를 연 다음, **Optimized for Compute로 업그레이드**를 클릭합니다.
2. Optimized for Compute 성능 계층 선택을 확인합니다. 기본 선택은 업그레이드하기 전에 현재 수준에 맞게 적용됩니다.
3. 성능 계층을 선택합니다. Optimized for Compute 성능 계층은 현재 미리 보기 기간 중 절반 가격입니다.
4. **업그레이드**를 클릭합니다.
5. Azure Portal에서 상태를 확인합니다.
6. 데이터 웨어하우스가 온라인으로 변경될 때까지 기다립니다.

## <a name="rebuild-columnstore-indexes"></a>Columnstore 인덱스 다시 빌드

데이터 웨어하우스가 온라인 상태가 되면 데이터를 로드하고 쿼리를 실행할 수 있습니다. 그러나 백그라운드 프로세스가 데이터를 새 하드웨어로 마이그레이션하는 중이므로 처음에는 성능이 느릴 수 있습니다. 

가능한 빨리 데이터 마이그레이션을 강제 실행하려면 columnstore 인덱스를 다시 빌드하는 것이 좋습니다. 이를 위한 지침은 [세그먼트 품질을 개선하기 위해 columnstore 인덱스 다시 빌드](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality)를 참조하세요. 

## <a name="next-steps"></a>다음 단계
데이터 웨어하우스가 온라인입니다. 새로운 성능 기능을 사용하려면 [워크로드 관리를 위한 리소스 클래스](resource-classes-for-workload-management.md)를 참조하세요.
 