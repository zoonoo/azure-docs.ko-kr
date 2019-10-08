---
title: Azure Data Factory 매핑 데이터 흐름 변경 행 변환
description: Azure Data Factory 매핑 데이터 흐름을 사용 하 여 데이터베이스 대상을 업데이트 하는 방법 Alter Row 변환
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: fc497837792075501bcd92f6ee07ad9ee4fe2dfa
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72027012"
---
# <a name="azure-data-factory-alter-row-transformation"></a>행 변환 변경 Azure Data Factory

행에 대 한 insert, delete, update 및 upsert 정책을 설정 하려면 행 변경 변환을 사용 합니다. 일대다 조건을 식으로 추가할 수 있습니다. 이러한 조건은 각 행이 첫 번째 일치 식에 해당 하는 정책으로 표시 되므로 우선 순위에 따라 지정 해야 합니다. 이러한 각 조건은 행 (또는 행)이 삽입, 업데이트, 삭제 또는 upserted에 게 발생할 수 있습니다. Alter Row는 데이터베이스에 대해 DDL & DML 작업을 모두 생성할 수 있습니다.



행 ![설정]변경(media/data-flow/alter-row1.png "행 설정 변경")

> [!NOTE]
> Alter Row 변환은 데이터 흐름의 데이터베이스 싱크에 대해서만 작동 합니다. 행에 할당 하는 작업 (insert, update, delete, upsert)은 디버그 세션 중에 발생 하지 않습니다. 파이프라인에 데이터 흐름 실행 태스크를 추가 하 고 파이프라인 디버그 또는 트리거를 사용 하 여 데이터베이스 테이블에 대 한 alter row 정책을 적용 해야 합니다.

## <a name="indicate-a-default-row-policy"></a>기본 행 정책 지정

Alter Row 변환을 만들고 조건이 `true()` 인 행 정책을 지정 합니다. 이전에 정의 된 식 중 하나를 충족 하지 않는 각 행은 지정 된 행 정책에 대해 표시 됩니다. 기본적으로 조건 식을 충족 하지 않는 각 행은 `Insert`에 대해 표시 됩니다.

![Alter row one policy](media/data-flow/alter-row4.png "alter row one policy")

> [!NOTE]
> 모든 행을 하나의 정책으로 표시 하려면 해당 정책에 대 한 조건을 만들고 조건을 `true()`으로 지정할 수 있습니다.

## <a name="view-policies"></a>정책 보기

데이터 흐름 디버그 모드를 설정 하 여 데이터 미리 보기 창에서 alter row 정책의 결과를 확인 합니다. 데이터 흐름 디버그 모드에서 alter row를 실행 해도 대상에 대 한 DDL 또는 DML 동작은 생성 되지 않습니다. 이러한 작업을 수행 하기 위해 파이프라인 내에서 데이터 흐름 실행 작업 내에서 데이터 흐름을 실행 합니다.

행 ![정책 변경](media/data-flow/alter-row3.png "행 정책 변경")

이렇게 하면 조건에 따라 각 행의 상태를 확인 하 고 볼 수 있습니다. 파이프라인 내에서 데이터 흐름을 실행할 때 발생 하는 동작을 나타내는 각 insert, update, delete 및 upsert 작업에 대 한 아이콘이 표시 됩니다.

## <a name="sink-settings"></a>싱크 설정

Alter Row가 작동 하려면 데이터베이스 싱크 형식이 있어야 합니다. 싱크 설정에서 Alter Row 조건에 해당 하는 각 작업을 허용으로 설정 해야 합니다.

![Alter row sink](media/data-flow/alter-row2.png "alter row sink")

데이터베이스 싱크를 사용 하는 ADF 데이터 흐름의 기본 동작은 행을 삽입 하는 것입니다. 업데이트, upsert 및 삭제도 허용 하려면 싱크에 있는 확인란을 선택 하 여 작업을 허용 해야 합니다.

> [!NOTE]
> 삽입, 업데이트 또는 upsert에서 싱크에 있는 대상 테이블의 스키마를 수정 하는 경우 데이터 흐름이 실패 합니다. 데이터베이스에서 대상 스키마를 수정 하려면 싱크에서 "테이블 다시 만들기" 옵션을 선택 해야 합니다. 그러면 새 스키마 정의를 사용 하 여 테이블을 삭제 하 고 다시 만듭니다.

## <a name="next-steps"></a>다음 단계

Alter Row 변환 후에는 [데이터를 대상 데이터 저장소로 싱크로](data-flow-sink.md)지정할 수 있습니다.
