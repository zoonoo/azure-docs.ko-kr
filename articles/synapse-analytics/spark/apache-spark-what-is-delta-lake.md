---
title: Delta Lake란?
description: 델타 Lake의 개요 및 Azure Synapse Analytics의 일부로 작동 하는 방법
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 244cdf5329e26fc7d928998b734a539f086051ad
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85193382"
---
# <a name="what-is-delta-lake"></a>델타 Lake 란?

Azure Synapse Analytics는 Linux Foundation 델타 Lake와 호환 됩니다. 델타 Lake는 ACID (원자성, 일관성, 격리성 및 내구성) 트랜잭션을 Apache Spark 및 빅 데이터 작업에 제공 하는 오픈 소스 저장소 계층입니다.

## <a name="key-features"></a>주요 기능

| 기능 | 설명 |
| --- | --- |
| **ACID 트랜잭션** | 데이터 레이크는 일반적으로 여러 프로세스 및 파이프라인을 통해 채워지며, 일부는 읽기와 동시에 데이터를 기록 합니다. 델타 Lake 및 트랜잭션 추가 전에 데이터 엔지니어가 데이터 무결성을 보장 하기 위해 수동으로 오류가 발생 하기 쉬운 프로세스를 진행 해야 했습니다. 델타 Lake는 데이터 레이크에 익숙한 ACID 트랜잭션을 가져옵니다. 가장 강력한 수준의 격리 수준인 순차성을 제공 합니다. [델타 Lake: 트랜잭션 로그 압축 풀기](https://databricks.com/blog/2019/08/21/diving-into-delta-lake-unpacking-the-transaction-log.html)에서 자세히 알아보세요.|
| **확장 가능한 메타 데이터 처리** | 빅 데이터에서 메타 데이터 자체도 "빅 데이터" 일 수 있습니다. 델타 Lake는 데이터와 마찬가지로 메타 데이터를 처리 하 여 Spark의 분산 처리 능력을 활용 하 여 모든 메타 데이터를 처리 합니다. 따라서 델타 Lake는 수십억 개의 파티션 및 파일을 사용 하 여 페타바이트 규모 테이블을 쉽게 처리할 수 있습니다. |
| **시간 이동 (데이터 버전 관리)** | 변경을 "실행 취소" 하는 기능은 트랜잭션의 주요 기능 중 하나입니다. 델타 Lake는 감사에 대 한 이전 버전의 데이터로 되돌리거나 실험을 롤백 하거나 실험을 재현할 수 있도록 데이터의 스냅숏을 제공 합니다. [대규모 데이터 레이크에 대 한 델타 Lake 시간 이동 소개](https://databricks.com/blog/2019/02/04/introducing-delta-time-travel-for-large-scale-data-lakes.html)에 대해 자세히 알아보세요. |
| **Open Format** | Apache Parquet는 델타 Lake의 기본 형식으로, 형식에 기본으로 사용 되는 효율적인 압축 및 인코딩 체계를 활용할 수 있습니다. |
| **통합 일괄 처리 및 스트리밍 원본 및 싱크** | 델타 Lake의 테이블은 스트리밍 원본 및 싱크 뿐만 아니라 일괄 처리 테이블입니다. 스트리밍 데이터 수집, 일괄 처리 기록 백필 및 대화형 쿼리 모두 바로 작동 합니다. |
| **스키마 적용** | 스키마 적용을 사용 하면 데이터 형식이 올바르고 필요한 열이 있는지를 확인 하 여 잘못 된 데이터에서 데이터 불일치를 방지할 수 있습니다. 자세한 내용은 [델타 Lake: 스키마 적용 & 진화 살펴보기](https://databricks.com/blog/2019/09/24/diving-into-delta-lake-schema-enforcement-evolution.html) 를 참조 하세요. |
| **스키마 진화** | 델타 Lake를 사용 하면 마이그레이션 DDL을 작성 하지 않고도 자동으로 적용할 수 있는 테이블 스키마를 변경할 수 있습니다. 자세한 내용은 [델타 Lake: 스키마 적용 & 진화 살펴보기](https://databricks.com/blog/2019/09/24/diving-into-delta-lake-schema-enforcement-evolution.html) 를 참조 하세요. |
| **감사 기록** | 델타 Lake 트랜잭션 로그는 변경 내용의 전체 감사 내역을 제공 하는 데이터에 대 한 모든 변경 내용에 대 한 세부 정보를 기록 합니다. |
| **업데이트 및 삭제** | 델타 Lake는 다양 한 기능을 위해 Scala/Java/Python 및 SQL Api를 지원 합니다. 병합, 업데이트 및 삭제 작업에 대 한 지원을 통해 규정 준수 요구 사항을 충족할 수 있습니다. 자세한 내용은 DML 명령을 병합, 업데이트 및 삭제 하는 코드 조각을 포함 하는 Python Api를 사용 하 여 델타 lake [0.4.0 릴리스](https://delta.io/news/delta-lake-0-4-0-released/) 및 [단순 하 고 안정적인 upsert 및 삭제](https://databricks.com/blog/2019/10/03/simple-reliable-upserts-and-deletes-on-delta-lake-tables-using-python-apis.html)를 참조 하세요. |
| **Apache Spark API와 100% 호환 가능** | 개발자는 기존 Spark 구현과 완전히 호환 되므로 변화를 최소화 하면서 기존 데이터 파이프라인에서 델타 Lake를 사용할 수 있습니다. |

전체 설명서는 [델타 Lake 설명서 페이지](https://docs.delta.io/latest/delta-intro.html) 를 참조 하세요.

자세한 내용은 [델타 Lake 프로젝트](https://lfprojects.org)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

- [Apache Spark용 .NET 설명서](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
