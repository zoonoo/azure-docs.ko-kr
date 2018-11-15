---
title: Azure Cosmos DB의 충돌 해결 유형 및 해결 정책
description: 이 문서에서는 Azure Cosmos DB의 충돌 범주 및 충돌 해결 정책에 대해 설명합니다.
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: mjbrown
ms.openlocfilehash: c682b61a39224f2c80db8fe5fa153ea5e5d82922
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2018
ms.locfileid: "50958572"
---
# <a name="conflict-types-and-resolution-policies"></a>충돌 유형 및 해결 정책

충돌 및 충돌 해결 정책은 Cosmos 계정이 여러 쓰기 지역으로 구성된 경우 적용됩니다.

여러 쓰기 지역으로 구성된 Cosmos DB 계정의 경우 여러 작성자가 여러 지역에서 동일한 항목을 동시에 업데이트하는 경우 업데이트 충돌이 발생할 수 있습니다. 업데이트 충돌은 다음 세 가지 유형으로 분류됩니다.

1. **삽입 충돌:** 이러한 충돌은 애플리케이션이 두 개 이상의 지역에서 동일한 고유 인덱스(예: ID 속성)를 사용하여 두 개 이상의 항목을 동시에 삽입하는 경우 발생할 수 있습니다. 이 경우 모든 쓰기는 각각의 로컬 지역에서 처음에는 성공할 수 있지만, 선택한 충돌 해결 정책에 따라 원래 ID가 있는 하나의 항목만 최종적으로 커밋됩니다.

1. **대체 충돌:** 이러한 충돌은 애플리케이션이 두 개 이상의 지역에서 하나의 항목을 동시에 업데이트하는 경우 발생할 수 있습니다.

1. **삭제 충돌:** 이러한 충돌은 애플리케이션이 동시에 한 지역에서 항목을 삭제하고 다른 지역에서 업데이트하는 경우 발생할 수 있습니다.

## <a name="conflict-resolution-policies"></a>충돌 해결 정책

Cosmos DB는 업데이트 충돌을 해결하기 위해 유연한 정책 기반 메커니즘을 제공합니다. Cosmos 컨테이너에서 다음 두 가지 충돌 해결 정책 중에서 선택할 수 있습니다.

- **LWW(마지막 쓰기 우선):**  이 해결 정책은 기본적으로 시스템에 정의된 타임스탬프 속성을 사용합니다(시간 동기화 클록 프로토콜 기반). 또는 Cosmos DB를 사용하면 SQL API를 사용하는 동안 충돌 해결에 사용할 다른 사용자 지정 숫자 속성(“충돌 해결 경로”라고도 함)을 지정할 수 있습니다.  

  두 개 이상 항목이 삽입 또는 대체 작업에서 충돌하는 경우 “충돌 해결 경로”에 대한 가장 높은 값을 포함하는 항목이 “우승자”가 됩니다. 여러 항목에 충돌 해결 경로에 대한 동일한 숫자 값이 있는 경우 선택된 “우승자” 버전은 시스템에서 결정됩니다. 모든 지역은 단일 우승자로 수렴되고 결국 커밋된 항목의 동일한 버전으로 끝나게 됩니다. 관련된 삭제 충돌이 있는 경우 삭제된 버전은 충돌 해결 경로의 값에 관계없이 삽입 또는 대체 충돌에 대해 항상 승리합니다.

  > [!NOTE]
  > 마지막 쓰기 우선은 기본 충돌 해결 정책이며 SQL, Table, MongoDB, Cassandra 및 Gremlin API에 사용할 수 있습니다.

  자세한 내용은 [LWW 충돌 해결 정책을 사용한 예제](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy)를 참조하세요.

- **사용자 지정:** 이 해결 정책은 충돌 조정용 애플리케이션 정의 의미 체계를 위해 설계되었습니다. Cosmos 컨테이너에서 이 정책을 설정하는 동안 서버의 데이터베이스 트랜잭션에서 업데이트 충돌이 검색되면 자동으로 호출되는 병합 저장 프로시저를 등록해야 합니다. 시스템은 커밋 프로토콜의 일부로 병합 프로시저의 실행에 대해 정확히 한 번의 보장을 제공합니다.  

  단, 사용자 지정 해결 옵션을 사용하여 컨테이너를 구성하는 경우 컨테이너에서 병합 프로시저를 등록하지 못하거나 병합 프로시저가 런타임에서 예외를 throw하는 경우 충돌이 충돌 피드에 기록됩니다. 그러면 응용 프로그램은 충돌 피드의 충돌을 수동으로 해결해야 합니다. 자세한 내용은 [사용자 지정 해결 정책을 사용한 예제 및 충돌 피드를 사용하는 방법](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy)을 참조하세요.

  > [!NOTE]
  > 사용자 지정 충돌 해결 정책은 SQL API 계정에서만 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

다음으로, 다음 문서를 사용하여 충돌 해결 정책을 구성하는 방법을 알아볼 수 있습니다.

* [LWW 충돌 해결 정책을 사용하는 방법](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy)
* [사용자 지정 충돌 해결 정책을 사용하는 방법](how-to-manage-conflicts.md#create-a-last-writer-wins-conflict-resolution-policy)
* [충돌 피드를 사용하는 방법](how-to-manage-conflicts.md#read-from-conflict-feed)
