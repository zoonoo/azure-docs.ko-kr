---
title: Azure Data Factory 데이터 랭 글 링
description: Azure Data Factory의 데이터 랭 글 링 개요
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/19/2021
ms.openlocfilehash: f922e7a2755a6e26a0d9f93f2668753e2f4dad5a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98738172"
---
# <a name="what-is-data-wrangling"></a>데이터 랭 글 링 무엇 인가요?

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

조직은 매일 계속 증가 하는 복잡 한 데이터를 정확 하 게 분석 하기 위해 데이터 준비 및 랭 글 링에 대 한 중요 한 비즈니스 데이터를 탐색 하는 기능을 필요로 합니다. 조직에서 다양 한 비즈니스 프로세스의 데이터를 사용 하 고 시간 값을 줄일 수 있도록 데이터를 준비 해야 합니다.

Data Factory은 파워 쿼리를 사용 하 여 클라우드 규모에서 코드 없는 데이터 준비를 제공 합니다. Data Factory은 [온라인 파워 쿼리](/power-query/) 와 통합 파워 쿼리 되며 M 함수를 파이프라인 작업으로 사용할 수 있습니다.

Data Factory는 M을 Azure Data Factory 데이터 흐름으로 변환 하 여 파워 쿼리 온라인 매시업 편집기에서 만든 M을 클라우드 규모 실행을 위한 spark 코드로 변환 합니다. 파워 쿼리 및 데이터 흐름을 사용 하는 랭 글 링 데이터는 데이터 엔지니어 또는 ' 시민 데이터 통합자 '에 게 특히 유용 합니다.

> [!NOTE]
> Azure Data Factory의 파워 쿼리 활동은 현재 공개 미리 보기로 제공 됩니다.

## <a name="use-cases"></a>사용 사례

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4MFkW]

### <a name="fast-interactive-data-exploration-and-preparation"></a>신속한 대화형 데이터 탐색 및 준비

여러 데이터 엔지니어와 시민 데이터 통합자는 클라우드 규모에서 데이터 집합을 대화형으로 탐색 하 고 준비할 수 있습니다. 데이터 레이크 데이터의 양이 증가 하 고 다양 하 고 속도가 증가 함에 맞게 사용자는 데이터 집합을 탐색 하 고 준비 하는 효과적인 방법이 필요 합니다. 예를 들어 ' 2017 ' 이후의 새 고객에 대 한 모든 고객 인구 통계 정보를 포함 하는 데이터 집합을 만들어야 할 수 있습니다. 알려진 대상에 매핑되지 않습니다. Lake에 게시 하기 전에 요구 사항을 충족 하기 위해 데이터 집합을 탐색, 랭 글 링 및 prepping 합니다. 랭 글 링는 더 짧은 공식 분석 시나리오에 사용 되는 경우가 많습니다. 준비 데이터 집합을 사용 하 여 변환 및 기계 학습 작업을 수행할 수 있습니다.

### <a name="code-free-agile-data-preparation"></a>코드 없는 agile 데이터 준비

시민 데이터 통합자는 데이터를 찾고 준비 하는 시간을 60% 넘게 소비 합니다. 운영 생산성을 개선 하기 위해 코드를 무료로 사용 하 고 있습니다. 사용자가 확장 가능한 방식으로 온라인 파워 쿼리 같은 알려진 도구를 사용 하 여 데이터를 보강 하 고, 셰이핑 하 고, 게시할 수 있도록 하 여 생산성을 크게 향상 시킵니다. Azure Data Factory의 랭 글 링를 사용 하면 익숙한 파워 쿼리 온라인 매시업 편집기를 사용 하 여 시민 데이터 통합 자가 오류를 신속 하 게 수정 하 고 데이터를 표준화 하 고 비즈니스 의사 결정을 지 원하는 고품질의 데이터를 생성할 수 있습니다.

### <a name="data-validation-and-exploration"></a>데이터 유효성 검사 및 탐색

코드를 사용 하지 않는 방식으로 데이터를 시각적으로 검사 하 여 이상 값, 이상 값을 제거 하 고 빠른 분석을 위해 셰이프를 준수 합니다.

## <a name="supported-sources"></a>지원되는 원본

| 커넥터 | 데이터 형식 | 인증 유형 |
| -- | -- | --|
| [Azure Blob Storage](connector-azure-blob-storage.md) | CSV, Parquet | 계정 키 |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) | CSV | 서비스 주체 |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | CSV, Parquet | 계정 키, 서비스 주체 |
| [Azure SQL Database](connector-azure-sql-database.md) | - | SQL 인증 |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md) | - | SQL 인증 |

## <a name="the-mashup-editor"></a>매시업 편집기

파워 쿼리 작업을 만들면 모든 원본 데이터 집합이 데이터 집합 쿼리가 되며 **Adfresource** 폴더에 배치 됩니다. 기본적으로 UserQuery는 첫 번째 데이터 집합 쿼리를 가리킵니다. 데이터 집합 쿼리에 대 한 변경 내용이 지원 되지 않거나 유지 되지 않으므로 UserQuery에서 모든 변환을 수행 해야 합니다. 현재는 쿼리 이름을 바꾸고 쿼리를 추가 및 삭제 하는 것은 지원 되지 않습니다.

![랭 글 링](media/wrangling-data-flow/editor.png)

현재 모든 파워 쿼리 M 함수는 제작 중에도 사용할 수 있는 데이터 랭 글 링 지원 되지 않습니다. 파워 쿼리 작업을 빌드하는 동안 함수가 지원 되지 않으면 다음과 같은 오류 메시지가 표시 됩니다.

`The wrangling data flow is invalid. Expression.Error: The transformation logic isn't supported. Please try a simpler expression`

지원 되는 변환에 대 한 자세한 내용은 [data 랭 글 링 함수](wrangling-functions.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

[랭 글 링 파워 쿼리 매시업 데이터를 만드는](wrangling-tutorial.md)방법에 대해 알아봅니다.
