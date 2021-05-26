---
title: 원장 사용 테이블이 포함된 Azure SQL Database 감사 이벤트
description: Azure SQL Database 원장 감사 기능 개요
ms.custom: ''
ms.date: 05/25/2021
ms.service: sql-database
ms.subservice: security
ms.reviewer: vanto
ms.topic: conceptual
author: JasonMAnderson
ms.author: janders
ms.openlocfilehash: eafca78dc22a01784eee79293fb7a2ec798818ad
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110388402"
---
# <a name="azure-sql-database-audit-events-with-ledger-enabled-tables"></a>원장 사용 테이블이 포함된 Azure SQL Database 감사 이벤트

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Azure SQL Database 원장은 현재 **공개 미리 보기** 로 제공됩니다.

원장 사용 테이블이 포함된 포렌식 활동을 수행할 때, 원장 보기 및 데이터베이스 원장에서 캡처된 데이터 외에도 추가 작업 ID가 SQL 감사 로그에 추가됩니다.  다음 표는 이벤트를 트리거하는 조건과 함께 이러한 새 감사 로깅 이벤트를 간략히 정리한 것입니다.

## <a name="enable-ledger"></a>원장 사용

| 열 | 값 |
|--|--|
| **action_id** | ENLR |
| **name** | ENABLE LEDGER  |
| **class_desc** | OBJECT |
| **covering_action_desc** | NULL |
| **parent_class_desc** | DATABASE |
| **covering_parent_action_name** | LEDGER_OPERATION_GROUP |
| **configuration_level** | NULL |
| **configuration_group_name** | LEDGER_OPERATION_GROUP |
| **action_in_log** | 1 |

**이벤트 트리거 조건**: 새 원장 테이블을 만들거나 일반 테이블을 원장 테이블로 변환합니다.

## <a name="alter-ledger"></a>원장 변경

| 열 | 값 |
|--|--|
| **action_id** | ALLR |
| **name** | ALTER LEDGER |
| **class_desc** | OBJECT |
| **covering_action_desc** | NULL |
| **parent_class_desc** | DATABASE |
| **covering_parent_action_name** | LEDGER_OPERATION_GROUP |
| **configuration_level** | NULL |
| **configuration_group_name** | LEDGER_OPERATION_GROUP |
| **action_in_log** | 1 |

**이벤트 트리거 조건**: 원장 테이블 삭제 또는 이름 바꾸기, 원장 테이블을 일반 테이블로 변환, 원장 테이블의 열 추가, 삭제 또는 이름 바꾸기


## <a name="generate-ledger-digest"></a>원장 다이제스트 생성

| 열 | 값 |
|--|--|
| **action_id** | GDLR |
| **name** | GENERATE LEDGER DIGEST |
| **class_desc** | DATABASE |
| **covering_action_desc** | LEDGER_OPERATION_GROUP |
| **parent_class_desc** | SERVER |
| **covering_parent_action_name** | LEDGER_OPERATION_GROUP |
| **configuration_level** | NULL |
| **configuration_group_name** | LEDGER_OPERATION_GROUP  |
| **action_in_log** | 1 |

**이벤트 트리거 조건**: 원장 다이제스트 생성

## <a name="verify-ledger"></a>원장 확인

| 열 | 값 |
|--|--|
| **action_id** | VFLR |
| **name** | VERIFY LEDGER |
| **class_desc** | DATABASE |
| **covering_action_desc** | LEDGER_OPERATION_GROUP |
| **parent_class_desc** | SERVER |
| **covering_parent_action_name** | LEDGER_OPERATION_GROUP |
| **configuration_level** | NULL |
| **configuration_group_name** | LEDGER_OPERATION_GROUP |
| **action_in_log** | 1 |

**이벤트 트리거 조건**: 원장 다이제스트 확인

## <a name="ledger-operation-group"></a>원장 작업 그룹

| 열 | 값 |
|--|--|
| **action_id** | OPLR |
| **name** | LEDGER_OPERATION_GROUP |
| **class_desc** | DATABASE |
| **covering_action_desc** | NULL |
| **parent_class_desc** | SERVER |
| **covering_parent_action_name** | NULL |
| **configuration_level** | GROUP |
| **configuration_group_name** | LEDGER_OPERATION_GROUP |
| **action_in_log** | 0 |

**이벤트 트리거 조건**: 해당 없음

| 열 | 값 |
|--|--|
| **action_id** | OPLR |
| **name** | LEDGER_OPERATION_GROUP |
| **class_desc** | SERVER |
| **covering_action_desc** | NULL |
| **parent_class_desc** | NULL |
| **covering_parent_action_name** | NULL |
| **configuration_level** | GROUP |
| **configuration_group_name** | LEDGER_OPERATION_GROUP |
| **action_in_log** | 0 |

**이벤트 트리거 조건**: 해당 없음 

## <a name="next-steps"></a>다음 단계

- [Azure SQL Database 및 Azure Synapse Analytics에 대한 감사](auditing-overview.md)
- [Azure SQL Database 원장 개요](ledger-overview.md)
- [빠른 시작: 원장 사용 Azure SQL Database 만들기](ledger-create-a-single-database-with-ledger-enabled.md)
