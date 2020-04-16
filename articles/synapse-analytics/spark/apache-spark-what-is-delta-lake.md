---
title: 델타 레이크란?
description: 델타 레이크 의 개요 및 Azure 시냅스 분석의 일부로 작동 방식
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 52758eab645fa0bb89cb499a5c617df62c21279e
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429201"
---
# <a name="what-is-delta-lake"></a>Delta Lake란?

Azure 시냅스 분석은 리눅스 재단 델타 레이크와 호환됩니다. 델타 레이크는 아파치 스파크와 빅 데이터 워크로드에 ACID(원자성, 일관성, 격리 및 내구성) 트랜잭션을 제공하는 오픈 소스 스토리지 계층입니다.

## <a name="key-features"></a>주요 기능

| 기능 | Description |
| --- | --- |
| **ACID 트랜잭션** | 데이터 레이크는 일반적으로 여러 프로세스와 파이프라인을 통해 채워지며, 그 중 일부는 읽기와 동시에 데이터를 작성합니다. 델타 레이크와 트랜잭션을 추가하기 전에 데이터 엔지니어는 데이터 무결성을 보장하기 위해 수동 오류 발생 이발생하기 쉬운 프로세스를 거쳐야 했습니다. 델타 레이크는 데이터 레이크에 친숙한 ACID 트랜잭션을 제공합니다. 그것은 직렬화 가능성, 절연 수준의 가장 강력한 수준을 제공합니다. [델타 레이크 다이빙: 트랜잭션 로그 압축 풀기에서](https://databricks.com/blog/2019/08/21/diving-into-delta-lake-unpacking-the-transaction-log.html)자세히 알아보십시오.|
| **확장 가능한 메타데이터 처리** | 빅 데이터에서는 메타데이터 자체도 "빅 데이터"가 될 수 있습니다. 델타 레이크는 Spark의 분산 처리 기능을 활용하여 모든 메타데이터를 처리하는 데이터처럼 메타데이터를 처리합니다. 그 결과, 델타 레이크는 수십억 개의 파티션과 파일이 있는 페타바이트 규모의 테이블을 쉽게 처리할 수 있습니다. |
| **시간 여행(데이터 버전 분석)** | 변경 을 "취소"하거나 이전 버전으로 돌아가는 기능은 트랜잭션의 주요 기능 중 하나입니다. 델타 레이크는 감사, 롤백 또는 실험을 재현하기 위해 이전 버전의 데이터로 되돌릴 수 있도록 데이터 스냅샷을 제공합니다. [대규모 데이터 레이크를 위한 델타 레이크 타임 트래블 소개에](https://databricks.com/blog/2019/02/04/introducing-delta-time-travel-for-large-scale-data-lakes.html)대해 자세히 알아보십시오. |
| **오픈 포맷** | 아파치 마루는 델타 호수의 기준 형식이며, 형식을 기본으로 하는 효율적인 압축 및 인코딩 체계를 활용할 수 있습니다. |
| **통합 배치 및 스트리밍 소스 및 싱크** | 델타 레이크의 테이블은 일괄 테이블과 스트리밍 소스 및 싱크입니다. 스트리밍 데이터 수집, 일괄 처리 기록 채우기 및 대화형 쿼리는 모두 즉시 작동합니다. |
| **스키마 시행** | 스키마 적용을 통해 데이터 형식이 정확하고 필요한 열이 있는지 확인하여 잘못된 데이터가 데이터 불일치를 일으키지 않도록 합니다. 자세한 내용은 [델타 호수로 다이빙: 스키마 시행 & 진화를](https://databricks.com/blog/2019/09/24/diving-into-delta-lake-schema-enforcement-evolution.html) 참조하십시오. |
| **스키마 진화** | 델타 레이크를 사용하면 마이그레이션 DDL을 작성하지 않고도 자동으로 적용할 수 있는 테이블 스키마를 변경할 수 있습니다. 자세한 내용은 [델타 호수로 다이빙: 스키마 시행 & 진화를](https://databricks.com/blog/2019/09/24/diving-into-delta-lake-schema-enforcement-evolution.html) 참조하십시오. |
| **감사 내역** | 델타 레이크 트랜잭션 로그는 변경 사항에 대한 전체 감사 추적을 제공하는 데이터에 대한 모든 변경 에 대한 세부 정보를 기록합니다. |
| **업데이트 및 삭제** | 델타 레이크는 다양한 기능에 스칼라 / 자바 / 파이썬 및 SQL API를 지원합니다. 병합, 업데이트 및 삭제 작업을 지원하면 규정 준수 요구 사항을 충족하는 데 도움이 됩니다. 자세한 내용은 병합, 업데이트 및 DML 명령을 삭제하기 위한 코드 스니펫이 포함된 Python API를 사용하여 델타 레이크 테이블에서 [델타 레이크 0.4.0 릴리스](https://delta.io/news/delta-lake-0-4-0-released/) 및 [간단하고 신뢰할 수 있는 Upserts 및 Delete을](https://databricks.com/blog/2019/10/03/simple-reliable-upserts-and-deletes-on-delta-lake-tables-using-python-apis.html)발표하는 것을 참조하십시오. |
| **아파치 스파크 API와 100% 호환** | 개발자는 기존 Spark 구현과 완벽하게 호환되기 때문에 최소한의 변경으로 기존 데이터 파이프라인과 델타 레이크를 사용할 수 있습니다. |

전체 문서는 델타 [레이크 문서 페이지를](https://docs.delta.io/latest/delta-intro.html) 참조하십시오.

자세한 내용은 [델타 호수 프로젝트를](https://lfprojects.org)참조하십시오.

## <a name="next-steps"></a>다음 단계

- [아파치 스파크 문서용 .NET](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
