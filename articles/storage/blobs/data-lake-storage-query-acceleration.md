---
title: Azure Data Lake Storage 쿼리 가속
description: 쿼리 가속을 사용 하면 응용 프로그램 및 분석 프레임 워크에서 처리 작업에 필요한 데이터만 검색 하 여 데이터 처리를 현저 하 게 최적화할 수 있습니다.
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.reviewer: jamesbak
ms.date: 09/09/2020
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: ae92828f08ae4abf9cc28f18872cca27ce747be4
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/10/2020
ms.locfileid: "89657640"
---
# <a name="azure-data-lake-storage-query-acceleration"></a>Azure Data Lake Storage 쿼리 가속

쿼리 가속을 사용 하면 응용 프로그램 및 분석 프레임 워크에서 지정 된 작업을 수행 하는 데 필요한 데이터만 검색 하 여 데이터 처리를 현저 하 게 최적화할 수 있습니다. 이렇게 하면 저장 된 데이터에 대 한 중요 한 정보를 얻는 데 필요한 시간 및 처리 능력이 줄어듭니다.

## <a name="overview"></a>개요

쿼리 가속에는 응용 프로그램에서 데이터를 디스크에서 읽을 때 행과 열을 필터링 할 수 있는 필터링 *조건자* 및 *열 프로젝션이* 허용 됩니다. 조건자의 조건을 충족 하는 데이터만 네트워크를 통해 응용 프로그램으로 전송 됩니다. 이렇게 하면 네트워크 대기 시간과 계산 비용이 줄어듭니다.  

SQL을 사용 하 여 쿼리 가속 요청에서 행 필터 조건자 및 열 프로젝션을 지정할 수 있습니다. 요청은 하나의 파일만 처리 합니다. 따라서 join 및 group by 집계와 같은 SQL의 고급 관계형 기능은 지원 되지 않습니다. 쿼리 가속은 CSV 및 JSON 형식의 데이터를 각 요청에 대 한 입력으로 지원 합니다.

쿼리 가속 기능은 Data Lake Storage (계층적 네임 스페이스를 사용 하도록 설정 된 저장소 계정)으로 제한 되지 않습니다. 쿼리 가속은 계층적 네임 스페이스를 사용 하도록 설정 **되지** 않은 저장소 계정의 blob과 완전히 호환 됩니다. 즉, 저장소 계정에 blob으로 이미 저장 한 데이터를 처리할 때 네트워크 대기 시간과 계산 비용을 동일 하 게 줄일 수 있습니다.

클라이언트 응용 프로그램에서 쿼리 가속을 사용 하는 방법에 대 한 예제는 [Azure Data Lake Storage 쿼리 가속을 사용 하 여 데이터 필터링](data-lake-storage-query-acceleration-how-to.md)을 참조 하세요.

## <a name="data-flow"></a>데이터 흐름

다음 다이어그램에서는 일반적인 응용 프로그램에서 쿼리 가속을 사용 하 여 데이터를 처리 하는 방법을 보여 줍니다.

> [!div class="mx-imgBorder"]
> ![쿼리 가속 개요](./media/data-lake-storage-query-acceleration/query-acceleration.png)

1. 클라이언트 응용 프로그램은 조건자 및 열 프로젝션을 지정 하 여 파일 데이터를 요청 합니다.

2. 쿼리 가속은 지정 된 SQL 쿼리를 구문 분석 하 고 데이터를 구문 분석 하 고 필터링 하도록 작업을 분산 합니다.

3. 프로세서는 디스크에서 데이터를 읽고 해당 형식을 사용 하 여 데이터를 구문 분석 한 다음 지정 된 조건자와 열 프로젝션을 적용 하 여 데이터를 필터링 합니다.

4. 쿼리 가속은 분할 응답을 클라이언트 응용 프로그램으로 다시 결합 합니다.

5. 클라이언트 응용 프로그램이 스트리밍된 응답을 받아서 구문 분석 합니다. 응용 프로그램은 추가 데이터를 필터링 하지 않아도 되며 원하는 계산 또는 변환을 직접 적용할 수 있습니다.

## <a name="better-performance-at-a-lower-cost"></a>저렴 한 비용으로 더 나은 성능

쿼리 가속은 응용 프로그램에서 전송 및 처리 하는 데이터의 양을 줄여 성능을 최적화 합니다.

집계 된 값을 계산 하기 위해 응용 프로그램은 일반적으로 파일의 **모든** 데이터를 검색 한 다음 데이터를 로컬로 처리 하 고 필터링 합니다. 분석 워크 로드에 대 한 입/출력 패턴의 분석은 일반적으로 응용 프로그램에 지정 된 계산을 수행 하기 위해 읽는 데이터의 20%만 필요 함을 나타냅니다. 이 통계는 [파티션 정리](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-optimize-hive-query#hive-partitioning)와 같은 기술을 적용 한 후에도 마찬가지입니다. 즉, 해당 데이터의 80%는 네트워크를 통해 불필요 하 게 전송 되 고, 구문 분석 되 고, 응용 프로그램에 의해 필터링 됩니다. 기본적으로 불필요 한 데이터를 제거 하도록 디자인 된이 패턴은 상당한 계산 비용을 발생 시킵니다.  

Azure가 업계 최고의 네트워크를 사용 하는 경우에도 처리량과 대기 시간 측면에서 네트워크를 통해 불필요 하 게 데이터를 전송 하는 것은 응용 프로그램 성능에 비용이 많이 듭니다. 저장소 요청 중 원치 않는 데이터를 필터링 하면 쿼리 가속이이 비용을 제거 합니다.

또한 불필요 한 데이터를 구문 분석 하 고 필터링 하는 데 필요한 CPU 로드는 응용 프로그램이 작업을 수행 하기 위해 더 크고 더 큰 Vm을 프로 비전 해야 합니다. 이 계산 부하를 쿼리 가속으로 전송 하면 응용 프로그램에서 상당한 비용 절감을 실현할 수 있습니다.

## <a name="applications-that-can-benefit-from-query-acceleration"></a>쿼리 가속의 이점을 누릴 수 있는 응용 프로그램

쿼리 가속은 분산 분석 프레임 워크 및 데이터 처리 응용 프로그램을 위해 설계 되었습니다. 

Apache Spark 및 Apache Hive와 같은 분산 분석 프레임 워크는 프레임 워크 내에 저장소 추상화 계층을 포함 합니다. 이러한 엔진에는 사용자 쿼리에 대 한 최적의 쿼리 계획을 결정할 때 기본 i/o 서비스 기능에 대 한 지식을 통합할 수 있는 쿼리 최적화 도구 포함 됩니다. 이러한 프레임 워크는 쿼리 가속을 통합 하기 시작 합니다. 따라서 이러한 프레임 워크의 사용자는 쿼리 대기 시간을 향상 시키고 쿼리를 변경 하지 않고도 총 소유 비용을 절감할 수 있습니다. 

또한 쿼리 가속은 데이터 처리 응용 프로그램을 위해 설계 되었습니다. 이러한 유형의 응용 프로그램은 일반적으로 사용자가 설정 된 분산 분석 프레임 워크를 사용 하지 않도록 분석 정보를 직접 받지 못할 수 있는 대규모 데이터 변환을 수행 합니다. 이러한 응용 프로그램은 종종 기본 저장소 서비스와 더 직접적인 관계가 있으므로 쿼리 가속 등의 기능에서 직접 이점을 누릴 수 있습니다. 

응용 프로그램에서 쿼리 가속을 통합 하는 방법에 대 한 예는 [Azure Data Lake Storage 쿼리 가속을 사용 하 여 데이터 필터링](data-lake-storage-query-acceleration-how-to.md)을 참조 하세요.

## <a name="pricing"></a>가격 책정

Azure Data Lake Storage 서비스 내에서 계산 부하가 증가 함에 따라 쿼리 가속 사용에 대 한 가격 책정 모델은 일반 Azure Data Lake Storage 트랜잭션 모델과 다릅니다. 쿼리 가속은 검색 되는 데이터의 양과 호출자에 게 반환 되는 데이터의 양에 대 한 비용을 청구 합니다. 자세한 내용은 [Azure Data Lake Storage Gen2 가격 책정](https://azure.microsoft.com/pricing/details/storage/data-lake/)을 참조 하세요.

청구 모델의 변경에도 불구 하 고, 쿼리 가속의 가격 책정 모델은 작업에 대 한 총 소유 비용을 낮출 수 있도록 설계 되었으며, 비용이 많이 드는 VM 비용이 감소 합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Data Lake Storage 쿼리 가속을 사용 하 여 데이터 필터링](data-lake-storage-query-acceleration-how-to.md)
- [쿼리 가속 SQL 언어 참조](query-acceleration-sql-reference.md)


