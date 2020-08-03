---
title: 공유 메타데이터 모델
description: Azure Synapse Analytics를 사용하면 여러 작업 영역 컴퓨팅 엔진이 Spark 풀(미리 보기), SQL 주문형 엔진(미리 보기), SQL 풀 간에 데이터베이스와 테이블을 공유할 수 있습니다.
services: synapse-analytics
author: MikeRys
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: metadata
ms.date: 05/01/2020
ms.author: mrys
ms.reviewer: jrasnick
ms.openlocfilehash: c11a0ccb08f03775a07716e6c547d849cda347dd
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87387339"
---
# <a name="azure-synapse-analytics-shared-metadata"></a>Azure Synapse Analytics 공유 메타데이터

Azure Synapse Analytics를 사용하면 여러 작업 영역 컴퓨팅 엔진이 Spark 풀(미리 보기)과 SQL 주문형 엔진(미리 보기) 간에 데이터베이스와 테이블을 공유할 수 있습니다.

[!INCLUDE [preview](../includes/note-preview.md)]

이러한 공유는 최신 데이터 웨어하우스 패턴을 지원하며, Spark로 만든 데이터베이스 및 테이블에 대한 액세스 권한을 작업 영역 SQL 엔진에 제공합니다. 또한 SQL 엔진이 다른 엔진과 공유되지 않는 고유한 개체를 만들 수 있게 해줍니다.

## <a name="support-the-modern-data-warehouse"></a>최신 데이터 웨어하우스 지원

공유 메타데이터 모델은 다음과 같은 방법으로 최신 데이터 웨어하우스 패턴을 지원합니다.

1. 데이터 레이크의 데이터는 준비된 데이터를 여러 데이터베이스에 포함된 (아마도 분할된) Parquet 지원 테이블에 저장하여 Spark를 통해 효율적으로 준비되고 구성됩니다.

2. Spark에서 만든 데이터베이스와 모든 테이블은 모든 Azure Synapse 작업 영역 Spark 풀 인스턴스에 표시되며 모든 Spark 작업에서 사용할 수 있습니다. 작업 영역의 모든 Spark 풀이 동일한 기본 카탈로그 메타 저장소를 공유하므로 이 기능에는 [권한](#security-model-at-a-glance)이 적용됩니다.

3. Spark에서 만든 데이터베이스와 Parquet 지원 테이블은 작업 영역 SQL 주문형 엔진에 표시됩니다. [데이터베이스](database.md)는 SQL 주문형 메타데이터에 자동으로 생성되고, Spark 작업에서 만든 [외부 테이블 및 관리형 테이블](table.md)은 해당 데이터베이스의 `dbo` 스키마에 있는 SQL 주문형 메타데이터에서 외부 테이블로 액세스할 수 있게 됩니다. 

<!--[INSERT PICTURE]-->

<!--__Figure 1 -__ Supporting the Modern Data Warehouse Pattern with shared metadata-->

개체 동기화는 비동기적으로 발생합니다. 개체가 SQL 컨텍스트에 나타날 때까지 몇 초의 지연이 발생합니다. 개체가 표시되면 개체를 쿼리할 수 있지만, 개체에 대한 액세스 권한이 있는 SQL 엔진에서 개체를 업데이트하거나 변경할 수는 없습니다.

## <a name="shared-metadata-objects"></a>공유 메타데이터 개체

Spark를 사용하면 데이터베이스, 외부 테이블, 관리형 테이블 및 보기를 만들 수 있습니다. Spark 보기는 Spark SQL 문의 정의를 처리하는 Spark 엔진이 필요하며, SQL 엔진에서 처리할 수 없으므로 Parquet 스토리지 형식을 사용하는 데이터베이스와 데이터베이스에 포함된 외부 및 관리형 테이블만 작업 영역 SQL 엔진과 공유됩니다. Spark 보기는 Spark 풀 인스턴스 간에만 공유됩니다.

## <a name="security-model-at-a-glance"></a>보안 모델 개요

Spark 데이터베이스와 테이블은 SQL 엔진에서 동기화되는 해당 표현과 함께 기본 스토리지 수준에서 보호됩니다. 쿼리 제출자가 사용 권한을 갖고 있는 엔진에서 테이블을 쿼리하면 쿼리 제출자의 보안 주체가 기본 파일로 전달됩니다. 권한은 파일 시스템 수준에서 검사됩니다.

자세한 내용은 [Azure Synapse Analytics 공유 데이터베이스](database.md)를 참조하세요.

## <a name="change-maintenance"></a>변경 내용 유지 관리

Spark를 사용하여 메타데이터 개체를 삭제하거나 변경하는 경우 변경 내용이 선택되어 SQL 주문형 엔진으로 전파됩니다. 동기화는 비동기 작업이며 약간의 지연 후 변경 내용이 SQL 엔진에 반영됩니다.

## <a name="next-steps"></a>다음 단계

- [Azure Synapse Analytics의 공유 메타데이터 데이터베이스에 대한 자세한 정보](database.md)
- [Azure Synapse Analytics의 공유 메타데이터 테이블에 대한 자세한 정보](table.md)

