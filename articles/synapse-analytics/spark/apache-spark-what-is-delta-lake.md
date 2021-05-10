---
title: Delta Lake란?
description: Delta Lake의 개요 및 Delta Lake가 Azure Synapse Analytics의 일부로 작동하는 방식
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 23a269fe9498295a3ff325a261c6539f5e1e31aa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101676226"
---
# <a name="what-is-delta-lake"></a>Delta Lake란?

Azure Synapse Analytics는 Linux Foundation Delta Lake와 호환됩니다. Delta Lake는 오픈 소스 스토리지 레이어로, ACID(원자성, 일관성, 격리성, 내구성) 트랜잭션을 Apache Spark 및 빅 데이터 워크로드에 제공합니다.

Azure Synapse와 함께 포함된 Delta Lake의 현재 버전은 Scala, PySpark, .NET에 대한 언어를 지원합니다. 페이지 맨 아래 링크를 통해 더욱 자세한 예제와 설명서를 확인할 수 있습니다.

## <a name="key-features"></a>주요 특징

| 기능 | Description |
| --- | --- |
| **ACID 트랜잭션** | 데이터 레이크는 일반적으로 다양한 프로세스 및 파이프라인을 통해 채워지며, 이들 프로세스와 파이프라인 중 일부는 읽는 동시에 데이터를 기록합니다. Delta Lake 및 트랜잭션 추가 이전에는 데이터 무결성을 확보하기 위해 데이터 엔지니어가 수동으로 오류가 발생하기 쉬운 프로세스를 진행해야 했습니다. Delta Lake는 데이터 레이크에 익숙한 ACID 트랜잭션을 가져옵니다. 가장 강력한 수준의 격리 수준인 순차성을 제공합니다. [Delta Lake 파고들기: 트랜잭션 로그 압축 풀기](https://databricks.com/blog/2019/08/21/diving-into-delta-lake-unpacking-the-transaction-log.html)에서 자세히 알아보세요.|
| **스케일링 가능한 메타데이터 처리** | 빅 데이터에서는 메타데이터 자체도 "빅 데이터"일 수 있습니다. Delta Lake는 메타데이터를 데이터처럼 취급하며 Spark의 분산 처리 능력을 활용하여 모든 메타데이터를 처리합니다. 결과적으로 Delta Lake는 수십억 개의 파티션과 파일이 있는 페타바이트 규모의 테이블을 쉽게 처리할 수 ​​있습니다. |
| **시간 이동(데이터 버전 관리)** | 변경을 "실행 취소"하거나 이전 버전으로 돌아가는 기능은 트랜잭션의 주요 기능 중 하나입니다. Delta Lake는 데이터 스냅샷을 제공하여 감사나 롤백 또는 실험 재현을 위해 데이터를 이전 버전으로 되돌릴 수 있도록 합니다. [대규모 데이터 레이크와 관련된 Delta Lake 시간 이동 소개](https://databricks.com/blog/2019/02/04/introducing-delta-time-travel-for-large-scale-data-lakes.html)에서 자세히 알아보세요. |
| **형식 열기** | Apache Parquet는 Delta Lake의 기준 형식으로, 해당 형식에 기본적인 효율적 압축 및 인코딩 구성표를 활용할 수 있도록 합니다. |
| **일괄 처리와 스트리밍 원본 및 싱크 통합형** | Delta Lake의 테이블은 일괄 처리 테이블인 동시에 스트리밍 원본 및 싱크이기도 합니다. 스트리밍 데이터 수집, 일괄 처리 기록 백필 및 대화형 쿼리 모두 즉시 작동합니다. |
| **스키마 적용** | 스키마 적용을 통해 데이터 형식이 정확하고 필수 열이 있는지 확인하여 잘못된 데이터로 인해 데이터 일관성이 손상되는 것을 방지할 수 있습니다. [Delta Lake 파고들기: 스키마 적용 및 진화](https://databricks.com/blog/2019/09/24/diving-into-delta-lake-schema-enforcement-evolution.html)에서 자세히 알아보세요. |
| **스키마 진화** | Delta Lake를 사용하면 마이그레이션 DDL을 기록할 필요 없이 자동으로 적용할 수 있는 테이블 스키마를 변경할 수 있습니다. [Delta Lake 파고들기: 스키마 적용 및 진화](https://databricks.com/blog/2019/09/24/diving-into-delta-lake-schema-enforcement-evolution.html)에서 자세히 알아보세요. |
| **감사 기록** | Delta Lake 트랜잭션 로그는 변경된 내용의 전체 감사 내역을 제공하는 데이터에 발생한 모든 변경 사항과 관련된 세부 정보를 기록합니다. |
| **업데이트 및 삭제** | Delta Lake는 다양한 기능을 위해 Scala, Java, Python, SQL API를 지원합니다. 병합, 업데이트, 삭제 작업을 지원하여 규정 준수 요구 사항을 충족할 수 있도록 돕습니다. [Delta Lake 0.6.1 릴리스 발표](https://delta.io/news/delta-lake-0-6-1-released/),  [Delta Lake 0.7 릴리스 발표](https://delta.io/news/delta-lake-0-7-0-released/) 및 DML 명령 병합, 업데이트, 삭제 관련 코드 조각을 포함하는 [Delta Lake 테이블에서 Python API를 통한 간단하고 믿을 수 있는 Upserts 및 삭제](https://databricks.com/blog/2019/10/03/simple-reliable-upserts-and-deletes-on-delta-lake-tables-using-python-apis.html)를 통해 자세히 알아보세요. |
| **Apache Spark API와 100% 호환 가능** | 개발자는 최소한의 변경만으로 기존의 데이터 파이프라인이 있는 Delta Lake를 마치 기존의 Spark 구현에 완전히 호환되는 것처럼 사용할 수 있습니다. |

전체 설명서는 [Delta Lake 설명서 페이지](https://docs.delta.io/latest/delta-intro.html)를 참조하세요.

자세한 내용은 [Delta Lake 프로젝트](https://github.com/delta-io/delta)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Apache Spark용 .NET 설명서](/dotnet/spark)
- [Azure Synapse Analytics](../index.yml)