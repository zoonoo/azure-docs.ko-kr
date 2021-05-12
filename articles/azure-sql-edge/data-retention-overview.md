---
title: 데이터 보존 정책 개요 - Azure SQL Edge
description: Azure SQL Edge의 데이터 보존 정책에 대한 자세한 정보
keywords: SQL Edge, 데이터 보존
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/04/2020
ms.openlocfilehash: bb059a946c03f41e5b65944eec67070f84ee6b08
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "90976334"
---
# <a name="data-retention-overview"></a>데이터 보존 개요

연결된 IoT 디바이스에서 데이터를 수집하고 저장하는 것은 운영 및 비즈니스 통찰력을 확보하는 데 중요합니다. 그러나 이러한 디바이스에서 생성되는 데이터의 양을 고려할 때 조직은 보존할 데이터의 양과 세분화 정도를 신중하게 계획하는 것이 중요합니다. 모든 데이터를 세분화하여 보존하는 것이 바람직하지만 항상 실용적인 것은 아닙니다. 또한 보존할 수 있는 데이터의 양은 IoT 또는 Edge 디바이스에서 사용 가능한 스토리지의 양에 따라 제한됩니다. 

Azure SQL Edge 데이터베이스 관리자는 SQL Edge 데이터베이스와 해당 기본 테이블에 대한 데이터 보존 정책을 정의할 수 있습니다. 데이터 보존 정책이 정의되면 백그라운드 시스템 작업을 실행하여 사용자 테이블에서 사용되지 않는(오래된) 데이터를 제거합니다. 

> [!Note]
> 테이블에서 제거된 데이터는 복구할 수 없습니다. 제거된 데이터를 복구할 수 있는 유일한 방법은 이전 백업에서 데이터베이스를 복원하는 것입니다.

빠른 시작:

- [데이터 보존 정책 사용 및 사용 안 함](data-retention-enable-disable.md)
- [보존 정책을 사용하여 과거 데이터 관리](data-retention-cleanup.md)

## <a name="how-data-retention-works"></a>데이터 보존 작동 방법

데이터 보존을 구성하려면 DDL 문을 사용할 수 있습니다. 자세한 내용은 [데이터 보존 정책 사용 및 사용 안 함](data-retention-enable-disable.md)를 참조하세요. 사용되지 않는 레코드를 자동으로 삭제하려면 먼저 데이터베이스와 해당 데이터베이스 내에서 제거할 테이블 모두에 대해 데이터 보존을 사용하도록 설정해야 합니다. 

테이블에 대한 데이터 보존이 구성된 후 백그라운드 작업이 실행되어 테이블에서 사용하지 않는 레코드를 식별하고 해당 레코드를 삭제합니다. 어떤 이유로든 작업의 자동 정리가 실행되고 있지 않거나 정기적으로 삭제할 수 없는 경우 이러한 테이블에 대해 수동 정리 작업을 수행할 수 있습니다. 자동 및 수동 정리에 대한 자세한 내용은 [자동 및 수동 정리](data-retention-cleanup.md)를 참조하세요.

## <a name="limitations-and-restrictions"></a>제한 사항

- 데이터베이스를 전체 백업에서 복원하거나 다시 연결할 때 데이터 보존(사용하도록 설정된 경우)을 사용하지 않도록 자동 설정합니다. 
- Temporal 기록 테이블에 대해 데이터 보존을 사용하도록 설정할 수 없음
- 데이터 보존 필터 열은 변경할 수 없습니다. 열을 변경하려면 테이블에서 데이터 보존을 사용하지 않도록 설정합니다.  

## <a name="next-steps"></a>다음 단계

- [SQL Edge에 ONNX를 사용하는 Machine Learning 및 AI](onnx-overview.md)
- [IoT Edge를 사용하여 SQL Edge로 엔드투엔드 IoT 솔루션 빌드](tutorial-deploy-azure-resources.md).
- [Azure SQL Edge에서 데이터 스트리밍](stream-data.md)
