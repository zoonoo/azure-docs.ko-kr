---
title: 성능 모범 사례 및 구성 지침-Azure SQL Edge
description: Azure SQL Edge의 성능 모범 사례 및 구성 지침에 대해 알아봅니다.
keywords: SQL Edge, 데이터 보존
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 02f22883a0989714d8b74f778cacf1ba2c65d0b4
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93392013"
---
# <a name="performance-best-practices-and-configuration-guidelines"></a>성능 모범 사례 및 구성 지침

Azure SQL Edge는 SQL Edge 배포의 성능을 향상 시키는 데 사용할 수 있는 몇 가지 기능과 기능을 제공 합니다. 이 문서에서는 성능을 최대화 하기 위한 몇 가지 모범 사례 및 권장 사항을 제공 합니다. 

## <a name="best-practices"></a>모범 사례 

### <a name="configure-multiple-tempdb-data-files"></a>여러 tempdb 데이터 파일 구성

기본적으로 Azure SQL Edge는 컨테이너 초기화의 일부로 tempdb 데이터 파일을 하나만 만듭니다. 배포 후에 여러 tempdb 데이터 파일을 만드는 것이 좋습니다. 자세한 내용은 [SQL Server tempdb 데이터베이스에서 할당 경합을 줄이기 위한 권장 사항](https://support.microsoft.com/help/2154845/recommendations-to-reduce-allocation-contention-in-sql-server-tempdb-d) 문서의 지침을 참조하세요.

### <a name="use-clustered-columnstore-indexes-where-possible"></a>가능 하면 클러스터형 columnstore 인덱스 사용

IoT 및 Edge 장치는 일반적으로 분석을 위해 일정 기간 동안 집계 된 많은 양의 데이터를 생성 하는 경향이 있습니다. 개별 데이터 행은 모든 분석에 거의 사용 되지 않습니다. Columnstore 인덱스는 이러한 대량 데이터 집합을 저장 하 고 쿼리 하는 데 적합 합니다. 이 인덱스는 열 기반 데이터 저장소 및 쿼리 처리를 사용 하 여 기존의 행 기반 저장소에 비해 최대 10 배 쿼리 성능을 얻을 수 있습니다. 또한 압축되지 않은 데이터 크기보다 최대 10배의 데이터 압축 향상을 얻을 수 있습니다. 자세한 내용은 [Columnstore 인덱스](/sql/relational-databases/indexes/columnstore-indexes-overview) 를 참조 하세요.

또한 데이터 스트리밍 및 데이터 보존 같은 다른 Azure SQL Edge 기능은 데이터 삽입 및 데이터 제거와 관련 하 여 columnstore 최적화를 활용 합니다. 

### <a name="simple-recovery-model"></a>단순 복구 모델

저장소는에 지 장치에서 제약을 받을 수 있으므로 Azure SQL Edge의 모든 사용자 데이터베이스는 기본적으로 단순 복구 모델을 사용 합니다. 단순 복구 모델은 로그 공간을 자동으로 회수 하 여 공간 요구 사항을 적게 유지 함으로써 트랜잭션 로그 공간을 관리할 필요가 없도록 합니다. 제한 된 저장소를 사용할 수 있는에 지 장치에서는이 기능이 유용할 수 있습니다. 단순 복구 모델 및 사용 가능한 기타 복구 모델에 대 한 자세한 내용은 [복구 모델](/sql/relational-databases/backup-restore/recovery-models-sql-server) 을 참조 하세요.

트랜잭션 로그 백업이 필요한 로그 전달 및 지정 시간 복원과 같은 작업은 단순 복구 모델에서 지원 되지 않습니다.  

## <a name="advanced-configuration"></a>고급 구성 

### <a name="setting-memory-limits"></a>메모리 제한 설정

Azure SQL Edge는 버퍼 풀에 대해 최대 64 GB의 메모리를 지원 하 고, SQL Edge 컨테이너 내에서 실행 되는 위성 프로세스에는 추가 메모리가 필요할 수 있습니다. 메모리를 제한 하는 작은 가장자리 장치에서는 docker 호스트 및 기타에 지 프로세스 또는 모듈이 제대로 작동 하도록 SQL Edge 컨테이너에 사용할 수 있는 메모리를 제한 하는 것이 좋습니다. 다음 메커니즘을 사용 하 여 SQL Edge에 사용할 수 있는 총 메모리를 제어할 수 있습니다. 

- SQL Edge 컨테이너에 사용할 수 있는 메모리 제한: 컨테이너 런타임 구성 옵션을 사용 하 여 SQL Edge 컨테이너에 사용할 수 있는 총 메모리를 제한할 수 `--memory` 있습니다. SQL Edge 컨테이너에 사용할 수 있는 메모리를 제한 하는 방법에 대 한 자세한 내용은 [메모리, cpu 및 gpu를 사용 하는 런타임 옵션](https://docs.docker.com/config/containers/resource_constraints/)을 참조 하세요.

- 컨테이너 내에서 SQL 프로세스에 사용할 수 있는 메모리 제한: 기본적으로 컨테이너 내의 SQL 프로세스는 사용 가능한 실제 RAM의 80%만 사용 합니다. 대부분의 배포에서는 기본 구성에 문제가 없습니다. 그러나 데이터 스트리밍 및 SQL Edge 컨테이너 내에서 실행 되는 ONNX 프로세스에 추가 메모리가 필요할 수 있는 시나리오가 있을 수 있습니다. 이러한 시나리오에서는 `memory.memorylimitmb` mssql 파일의 설정을 사용 하 여 SQL 프로세스에서 사용할 수 있는 메모리를 제어할 수 있습니다. 자세한 내용은 [mssql 파일을 사용 하 여 구성](configure.md#configure-by-using-an-mssqlconf-file)을 참조 하세요.

메모리 제한을 설정 하는 경우이 값을 너무 낮게 설정 하지 않도록 주의 하십시오. SQL 프로세스에 충분 한 메모리를 설정 하지 않으면 SQL 성능에 심각한 영향을 줄 수 있습니다.

### <a name="delayed-durability"></a>지연된 내구성

Azure SQL Edge의 트랜잭션은 완전 내구성, SQL Server 기본값 또는 지연 된 내구성 (지연 커밋이 라고도 함) 중 하나일 수 있습니다.

완전 내구성이 있는 트랜잭션 커밋은 동기적이므로 트랜잭션에 대한 로그 레코드가 디스크에 기록된 후에만 커밋을 성공으로 보고하고 클라이언트에 컨트롤을 반환합니다. 지연된 내구성이 있는 트랜잭션 커밋은 비동기적이므로 트랜잭션에 대한 로그 레코드가 디스크에 기록되기 전에 커밋을 성공으로 보고합니다. 트랜잭션이 내구성을 가지려면 디스크에 트랜잭션 로그 항목을 기록해야 합니다. 지연된 내구성이 있는 트랜잭션은 트랜잭션 로그 항목을 디스크에 플러시한 경우에 내구성을 가집니다. 

**일부 데이터 손실이** 허용 될 수 있는 배포 또는 속도가 낮은 저장소에 지 장치에서 지연 된 내구성을 사용 하 여 데이터 수집 및 데이터 보존 기반 정리를 최적화할 수 있습니다. 자세한 내용은 [트랜잭션 내구성 제어](/sql/relational-databases/logs/control-transaction-durability)를 참조하세요.


### <a name="linux-os-configurations"></a>Linux OS 구성 

다음 [Linux 운영 체제 구성](/sql/linux/sql-server-linux-performance-best-practices#linux-os-configuration) 설정을 사용 하 여 SQL 설치에 대 한 최상의 성능을 경험 하는 것이 좋습니다.