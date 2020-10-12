---
title: 데이터 보존 정책 개요-Azure SQL Edge
description: Azure SQL Edge의 데이터 보존 정책에 대 한 자세한 정보
keywords: SQL Edge, 데이터 보존
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/04/2020
ms.openlocfilehash: bb059a946c03f41e5b65944eec67070f84ee6b08
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90976334"
---
# <a name="data-retention-overview"></a>데이터 보존 개요

연결 된 IoT 장치에서 데이터를 수집 하 고 저장 하는 것은 운영 및 비즈니스 통찰력을 확보 하는 데 중요 합니다. 그러나 이러한 장치에서 시작 되는 데이터의 양이 주어진 경우에는 조직에서 보존할 데이터의 양과 세분성을 신중 하 게 계획 하는 것이 중요 합니다. 모든 데이터를 모든 세분성으로 유지 하는 것이 바람직합니다. 항상 실용적이 지는 않습니다. 또한 보유할 수 있는 데이터의 양은 IoT 또는 Edge 장치에서 사용 가능한 저장소의 양에 따라 제한 됩니다. 

Azure SQL Edge 데이터베이스 관리자는 SQL Edge 데이터베이스와 해당 기본 테이블에 대 한 데이터 보존 정책을 정의할 수 있습니다. 데이터 보존 정책이 정의 되 면 백그라운드 시스템 태스크를 실행 하 여 사용자 테이블에서 사용 되지 않는 (이전) 데이터를 제거 합니다. 

> [!Note]
> 테이블에서 제거 된 데이터는 복구할 수 없습니다. 제거 된 데이터를 복구할 수 있는 유일한 방법은 이전 백업에서 데이터베이스를 복원 하는 것입니다.

빠른 시작:

- [데이터 보존 정책 사용 및 사용 안 함](data-retention-enable-disable.md)
- [보존 정책을 사용 하 여 기록 데이터 관리](data-retention-cleanup.md)

## <a name="how-data-retention-works"></a>데이터 보존 작동 방법

데이터 보존을 구성 하기 위해 DDL 문을 사용할 수 있습니다. 자세한 내용은 [데이터 보존 정책을 사용 하도록 설정 하 고 사용 하지 않도록](data-retention-enable-disable.md)설정 합니다. 사용 되지 않는 레코드를 자동으로 삭제 하려면 먼저 데이터베이스와 해당 데이터베이스 내에서 제거할 테이블 모두에 대해 데이터 보존을 사용 하도록 설정 해야 합니다. 

테이블에 대해 데이터 보존이 구성 된 후 백그라운드 태스크가 실행 되어 테이블에서 사용 되지 않는 레코드를 식별 하 고 해당 레코드를 삭제 합니다. 어떤 이유로 든 태스크의 자동 정리가 실행 되지 않거나 삭제를 유지할 수 없는 경우 이러한 테이블에 대해 수동 정리 작업을 수행할 수 있습니다. 자동 및 수동 정리 간격에 대 한 자세한 내용은 [자동 및 수동 정리](data-retention-cleanup.md)를 참조 하세요.

## <a name="limitations-and-restrictions"></a>제한 사항

- 데이터베이스를 전체 백업에서 복원 하거나 다시 연결할 때 데이터 보존 (설정 된 경우)을 자동으로 사용 하지 않도록 설정 합니다. 
- Temporal 기록 테이블에 대해 데이터 보존을 사용 하도록 설정할 수 없습니다.
- 데이터 보존 필터 colomn은 변경할 수 없습니다. 열을 변경 하려면 테이블에서 데이터 보존을 사용 하지 않도록 설정 합니다.  

## <a name="next-steps"></a>다음 단계

- [SQL Edge에 ONNX를 사용하는 Machine Learning 및 AI](onnx-overview.md)
- [IoT Edge를 사용하여 SQL Edge로 엔드투엔드 IoT 솔루션 빌드](tutorial-deploy-azure-resources.md).
- [Azure SQL Edge에서 데이터 스트리밍](stream-data.md)
