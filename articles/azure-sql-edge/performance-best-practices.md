---
title: 성능 모범 사례 및 구성 지침 - Azure SQL Edge
description: Azure SQL Edge의 성능 모범 사례 및 구성 지침에 대해 알아보기
keywords: SQL Edge, 데이터 보존
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 02f22883a0989714d8b74f778cacf1ba2c65d0b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93392013"
---
# <a name="performance-best-practices-and-configuration-guidelines"></a>성능 모범 사례 및 구성 지침

Azure SQL Edge는 SQL Edge 배포의 성능을 향상시키는 데 사용할 수 있는 몇 가지 기능을 제공합니다. 이 문서에서는 성능을 최대화하기 위한 모범 사례 및 권장 사항을 제공합니다. 

## <a name="best-practices"></a>모범 사례 

### <a name="configure-multiple-tempdb-data-files"></a>여러 tempdb 데이터 파일 구성

기본적으로 Azure SQL Edge는 컨테이너 초기화의 일부로 tempdb 데이터 파일을 하나만 만듭니다. 배포 후에 여러 tempdb 데이터 파일을 만드는 것이 좋습니다. 자세한 내용은 [SQL Server tempdb 데이터베이스에서 할당 경합을 줄이기 위한 권장 사항](https://support.microsoft.com/help/2154845/recommendations-to-reduce-allocation-contention-in-sql-server-tempdb-d) 문서의 지침을 참조하세요.

### <a name="use-clustered-columnstore-indexes-where-possible"></a>가능한 경우 클러스터형 columnstore 인덱스 사용

IoT 및 Edge 디바이스는 분석을 위해 일정 기간 동안 일반적으로 집계되는 대량의 데이터를 생성하는 경향이 있습니다. 개별 데이터 행이 분석에 사용되는 경우는 거의 없습니다. columnstore 인덱스는 이러한 큰 데이터 세트를 저장하고 쿼리하는 데 적합합니다. 이 인덱스는 열 기반 데이터 스토리지 및 쿼리 처리를 사용하여 기존 행 기반 스토리지보다 최대 10배 높은 쿼리 성능을 실현합니다. 또한 압축되지 않은 데이터 크기보다 최대 10배의 데이터 압축 향상을 얻을 수 있습니다. 자세한 내용은 [Columnstore 인덱스](/sql/relational-databases/indexes/columnstore-indexes-overview)를 참조하세요.

또한 데이터 스트리밍 및 데이터 보존 등의 다른 Azure SQL Edge 기능은 데이터 삽입 및 데이터 제거와 관련된 columnstore 최적화를 활용합니다. 

### <a name="simple-recovery-model"></a>단순 복구 모델

스토리지는 에지 디바이스에서 제한될 수 있기 때문에 Azure SQL Edge의 모든 사용자 데이터베이스는 기본적으로 단순 복구 모델을 사용합니다. 단순 복구 모델은 로그 공간을 자동으로 회수하여 적은 공간을 요구하므로 트랜잭션 로그 공간을 관리할 필요가 없습니다. 제한된 스토리지를 사용할 수 있는 에지 디바이스에서 유용할 수 있습니다. 단순 복구 모델 및 사용 가능한 기타 복구 모델에 대한 자세한 내용은 [복구 모델](/sql/relational-databases/backup-restore/recovery-models-sql-server)을 참조하세요.

트랜잭션 로그 백업이 필요한 로그 전달 및 특정 시점 복원과 같은 작업은 단순 복구 모델에서 지원되지 않습니다.  

## <a name="advanced-configuration"></a>고급 구성 

### <a name="setting-memory-limits"></a>메모리 제한 설정

Azure SQL Edge는 버퍼 풀에 대해 최대 64GB의 메모리를 지원하지만 SQL Edge 컨테이너 내에서 실행되는 위성 프로세스에는 추가 메모리가 필요할 수 있습니다. 메모리가 제한된 작은 에지 디바이스에서는 docker 호스트 및 기타 에지 프로세스나 모듈이 제대로 작동할 수 있도록 SQL Edge 컨테이너에 사용할 수 있는 메모리를 제한하는 것이 좋습니다. 다음 메커니즘을 사용하여 SQL Edge에 사용할 수 있는 총 메모리를 제어할 수 있습니다. 

- SQL Edge 컨테이너에 사용할 수 있는 메모리 제한: 컨테이너 런타임 구성 옵션 `--memory`를 사용하여 SQL Edge 컨테이너에 사용할 수 있는 총 메모리를 제한할 수 있습니다. SQL Edge 컨테이너에 사용할 수 있는 메모리를 제한하는 방법에 대한 자세한 내용은 [메모리, CPU 및 GPU를 사용하는 런타임 옵션](https://docs.docker.com/config/containers/resource_constraints/)을 참조하세요.

- 컨테이너 내에서 SQL 프로세스에 사용할 수 있는 메모리 제한: 기본적으로 컨테이너 내의 SQL 프로세스는 사용 가능한 실제 RAM의 80%만 사용합니다. 대부분의 배포에서는 기본 구성에 문제으로 충분합니다. 그러나 데이터 스트리밍 및 SQL Edge 컨테이너 내에서 실행되는 ONNX 프로세스에 추가 메모리가 필요할 수 있는 시나리오가 있을 수 있습니다. 이러한 시나리오에서는 mssql-conf 파일의 `memory.memorylimitmb` 설정을 사용하여 SQL 프로세스에서 사용할 수 있는 메모리를 제어할 수 있습니다. 자세한 내용은 [mssql-conf 파일을 사용하여 구성](configure.md#configure-by-using-an-mssqlconf-file)을 참조하세요.

메모리 제한을 설정하는 경우 이 값을 너무 낮게 설정하지 않도록 주의하세요. SQL 프로세스에 대해 충분한 메모리를 설정하지 않으면 SQL 성능에 심각한 영향을 줄 수 있습니다.

### <a name="delayed-durability"></a>지연된 내구성

Azure SQL Edge의 트랜잭션은 완전 내구성(SQL Sever 기본값)이 있거나 지연된 내구성(느린 커밋이라고도 함)이 있습니다.

완전 내구성이 있는 트랜잭션 커밋은 동기적이므로 트랜잭션에 대한 로그 레코드가 디스크에 기록된 후에만 커밋을 성공으로 보고하고 클라이언트에 컨트롤을 반환합니다. 지연된 내구성이 있는 트랜잭션 커밋은 비동기적이므로 트랜잭션에 대한 로그 레코드가 디스크에 기록되기 전에 커밋을 성공으로 보고합니다. 트랜잭션이 내구성을 가지려면 디스크에 트랜잭션 로그 항목을 기록해야 합니다. 지연된 내구성이 있는 트랜잭션은 트랜잭션 로그 항목을 디스크에 플러시한 경우에 내구성을 가집니다. 

**약간의 데이터 손실** 이 허용될 수 있는 배포 또는 스토리지 속도가 낮은 에지 디바이스에서 지연된 내구성을 사용하여 데이터 수집 및 데이터 보존 기반 정리를 최적화할 수 있습니다. 자세한 내용은 [트랜잭션 내구성 제어](/sql/relational-databases/logs/control-transaction-durability)를 참조하세요.


### <a name="linux-os-configurations"></a>Linux OS 구성 

SQL 설치의 성능을 최대화하려면 다음 [Linux 운영 체제 구성](/sql/linux/sql-server-linux-performance-best-practices#linux-os-configuration) 설정을 사용하는 것이 좋습니다.