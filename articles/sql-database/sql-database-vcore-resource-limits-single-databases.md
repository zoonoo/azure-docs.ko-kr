---
title: vCore 리소스 제한 - 단일 데이터베이스
description: 이 페이지에서는 Azure SQL Database의 단일 데이터베이스에 대한 몇 가지 일반적인 vCore 리소스 제한에 대해 설명합니다.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/11/2020
ms.openlocfilehash: 38d44f5a2ce22de15cb14ea5aa7a9ca1ea7c03cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481068"
---
# <a name="resource-limits-for-single-databases-using-the-vcore-purchasing-model"></a>vCore 구매 모델을 사용하는 단일 데이터베이스에 대한 리소스 제한

이 문서에서는 vCore 구매 모델을 사용하여 Azure SQL Database 단일 데이터베이스에 대한 자세한 리소스 제한을 제공합니다.

SQL Database 서버의 단일 데이터베이스에 대한 DTU 구매 모델 제한의 경우 [SQL Database 서버의 리소스 제한 개요를](sql-database-resource-limits-database-server.md)참조하십시오.

[Azure Portal](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server), [Transact-SQL](sql-database-single-databases-manage.md#transact-sql-manage-sql-database-servers-and-single-databases), [PowerShell](sql-database-single-databases-manage.md#powershell-manage-sql-database-servers-and-single-databases), [Azure CLI](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases) 또는 [REST API](sql-database-single-databases-manage.md#rest-api-manage-sql-database-servers-and-single-databases)를 사용하여 단일 데이터베이스에 대한 서비스 계층, 컴퓨팅 크기 및 스토리지 용량을 설정할 수 있습니다.

> [!IMPORTANT]
> 지침 및 고려 사항의 크기 조정은 [단일 데이터베이스 크기 조정을](sql-database-single-database-scale.md)참조하십시오.

## <a name="general-purpose---serverless-compute---gen5"></a>범용 - 서버리스 컴퓨팅 - Gen5

[서버리스 컴퓨팅 계층은](sql-database-serverless.md) 현재 Gen5 하드웨어에서만 사용할 수 있습니다.

### <a name="gen5-compute-generation-part-1"></a>Gen5 컴퓨팅 생성(1부)

|컴퓨팅 크기|GP_S_Gen5_1|GP_S_Gen5_2|GP_S_Gen5_4|GP_S_Gen5_6|GP_S_Gen5_8|
|:--- | --: |--: |--: |--: |--: |
|컴퓨팅 세대|5세대|5세대|5세대|5세대|5세대|
|최소 최대 vCores|0.5-1|0.5-2|0.5-4|0.75-6|1.0-8|
|최소 최대 메모리(GB)|2.02-3|2.05-6|2.10-12|2.25-18|3.00-24|
|최소 자동 일시 정지 지연(분)|60|60|60|60|60|
|Columnstore 지원 여부|yes|yes|yes|yes|yes|
|메모리 내 OLTP 스토리지(GB)|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|
|최대 데이터 크기(GB)|512|1024|1024|1024|1536|
|최대 로그 크기(GB)|154|307|307|307|461|
|TempDB 최대 데이터 크기(GB)|32|64|128|192|256|
|스토리지 유형|원격 SSD|원격 SSD|원격 SSD|원격 SSD|원격 SSD|
|IO 대기 시간(근사치)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|
|최대 데이터 IOPS *|320|640|1280|1920|2560|
|최대 로그 속도(MBps)|3.8|7.5|15|22.5|30|
|최대 동시 작업자(요청)|75|150|300|450|600|
|최대 동시 세션|30,000|30,000|30,000|30,000|30,000|
|복제본 수|1|1|1|1|1|
|다중 AZ|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|
|읽기 확장|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|
|포함되는 백업 스토리지|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|

\*IO 크기의 최대값은 8KB에서 64KB 사이입니다. 실제 IOPS는 워크로드에 따라 다릅니다. 자세한 내용은 [데이터 IO 거버넌스](sql-database-resource-limits-database-server.md#resource-governance)를 참조하십시오.

### <a name="gen5-compute-generation-part-2"></a>Gen5 컴퓨팅 생성(2부)

|컴퓨팅 크기|GP_S_Gen5_10|GP_S_Gen5_12|GP_S_Gen5_14|GP_S_Gen5_16|
|:--- | --: |--: |--: |--: |
|컴퓨팅 세대|5세대|5세대|5세대|5세대|
|최소 최대 vCores|1.25-10|1.50-12|1.75-14|2.00-16|
|최소 최대 메모리(GB)|3.75-30|4.50-36|5.25-42|6.00-48|
|최소 자동 일시 정지 지연(분)|60|60|60|60|
|Columnstore 지원 여부|yes|yes|yes|yes|
|메모리 내 OLTP 스토리지(GB)|해당 없음|해당 없음|해당 없음|해당 없음|
|최대 데이터 크기(GB)|1536|3072|3072|3072|
|최대 로그 크기(GB)|461|461|461|922|
|TempDB 최대 데이터 크기(GB)|320|384|448|512|
|스토리지 유형|원격 SSD|원격 SSD|원격 SSD|원격 SSD|
|IO 대기 시간(근사치)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|
|최대 데이터 IOPS *|3200|3840|4480|5120|
|최대 로그 속도(MBps)|30|30|30|30|
|최대 동시 작업자(요청)|750|900|1050|1200|
|최대 동시 세션|30,000|30,000|30,000|30,000|
|복제본 수|1|1|1|1|
|다중 AZ|해당 없음|해당 없음|해당 없음|해당 없음|
|읽기 확장|해당 없음|해당 없음|해당 없음|해당 없음|
|포함되는 백업 스토리지|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|

\*IO 크기의 최대값은 8KB에서 64KB 사이입니다. 실제 IOPS는 워크로드에 따라 다릅니다. 자세한 내용은 [데이터 IO 거버넌스](sql-database-resource-limits-database-server.md#resource-governance)를 참조하십시오.

## <a name="hyperscale---provisioned-compute---gen4"></a>하이퍼스케일 - 프로비저닝 된 계산 - Gen4

### <a name="gen4-compute-generation-part-1"></a>Gen4 계산 생성(1부)

|성능 수준|HS_Gen4_1|HS_Gen4_2|HS_Gen4_3|HS_Gen4_4|HS_Gen4_5|HS_Gen4_6|
|:--- | --: |--: |--: |---: | --: |--: |
|컴퓨팅 세대|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCore 수|1|2|3|4|5|6|
|메모리(GB)|7|14|21|28|35|42|
|[RBPEX](sql-database-service-tier-hyperscale.md#compute) 크기|3배 메모리|3배 메모리|3배 메모리|3배 메모리|3배 메모리|3배 메모리|
|Columnstore 지원 여부|yes|yes|yes|yes|yes|yes|
|메모리 내 OLTP 스토리지(GB)|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|
|최대 데이터 크기(TB)|100 |100 |100 |100 |100 |100|
|최대 로그 크기(TB)|1 |1 |1 |1 |1 |1 |
|TempDB 최대 데이터 크기(GB)|32|64|96|128|160|192|
|스토리지 유형| [참고 1](#notes) |[참고 1](#notes)|[참고 1](#notes) |[참고 1](#notes) |[참고 1](#notes) |[참고 1](#notes) |
|최대 데이터 IOPS *|[참고 2](#notes)|[참고 2](#notes)|[참고 2](#notes)|[참고 2](#notes)|[참고 2](#notes)|[참고 2](#notes)|
|최대 로그 속도(MBps)|100 |100 |100 |100 |100 |100 |
|IO 대기 시간(근사치)|[참고 3](#notes)|[참고 3](#notes)|[참고 3](#notes)|[참고 3](#notes)|[참고 3](#notes)|[참고 3](#notes)|
|최대 동시 작업자(요청)|200|400|600|800|1000|1200|
|최대 동시 세션|30,000|30,000|30,000|30,000|30,000|30,000|
|보조 복제본|0-4|0-4|0-4|0-4|0-4|0-4|
|다중 AZ|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|
|읽기 확장|yes|yes|yes|yes|yes|yes|
|백업 스토리지 보존|7 일|7 일|7 일|7 일|7 일|7 일|
|||

### <a name="gen4-compute-generation-part-2"></a>Gen4 컴퓨팅 생성(2부)

|성능 수준|HS_Gen4_7|HS_Gen4_8|HS_Gen4_9|HS_Gen4_10|HS_Gen4_16|HS_Gen4_24|
|:--- | ---: |--: |--: | --: |--: |--: |
|컴퓨팅 세대|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCore 수|7|8|9|10|16|24|
|메모리(GB)|49|56|63|70|112|159.5|
|[RBPEX](sql-database-service-tier-hyperscale.md#compute) 크기|3배 메모리|3배 메모리|3배 메모리|3배 메모리|3배 메모리|3배 메모리|
|Columnstore 지원 여부|yes|yes|yes|yes|yes|yes|
|메모리 내 OLTP 스토리지(GB)|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|
|최대 데이터 크기(TB)|100 |100 |100 |100 |100 |100 |
|최대 로그 크기(TB)|1 |1 |1 |1 |1 |1 |
|TempDB 최대 데이터 크기(GB)|224|256|288|320|512|768|
|스토리지 유형| [참고 1](#notes) |[참고 1](#notes) |[참고 1](#notes) |[참고 1](#notes) |[참고 1](#notes) |[참고 1](#notes) |
|최대 데이터 IOPS *|[참고 2](#notes)|[참고 2](#notes)|[참고 2](#notes)|[참고 2](#notes)|[참고 2](#notes)|[참고 2](#notes)|
|최대 로그 속도(MBps)|100 |100 |100 |100 |100 |100 |
|IO 대기 시간(근사치)|[참고 3](#notes)|[참고 3](#notes)|[참고 3](#notes)|[참고 3](#notes)|[참고 3](#notes)|[참고 3](#notes)|
|최대 동시 작업자(요청)|1400|1600|1800|2000|3200|4800|
|최대 동시 세션|30,000|30,000|30,000|30,000|30,000|30,000|
|보조 복제본|0-4|0-4|0-4|0-4|0-4|0-4|
|다중 AZ|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|
|읽기 확장|yes|yes|yes|yes|yes|yes|
|백업 스토리지 보존|7 일|7 일|7 일|7 일|7 일|7 일|
|||

\*IO 크기의 최대값은 8KB에서 64KB 사이입니다. 실제 IOPS는 워크로드에 따라 다릅니다. 자세한 내용은 [데이터 IO 거버넌스](sql-database-resource-limits-database-server.md#resource-governance)를 참조하십시오.

## <a name="hyperscale---provisioned-compute---gen5"></a>하이퍼스케일 - 프로비저닝 된 계산 - Gen5

### <a name="gen5-compute-generation-part-1"></a>Gen5 컴퓨팅 생성(1부)

|성능 수준|HS_Gen5_2|HS_Gen5_4|HS_Gen5_6|HS_Gen_8|HS_Gen5_10|HS_Gen5_12|HS_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |
|컴퓨팅 세대|5세대|5세대|5세대|5세대|5세대|5세대|5세대|
|vCore 수|2|4|6|8|10|12|14|
|메모리(GB)|10.4|20.8|31.1|41.5|51.9|62.3|72.7|
|[RBPEX](sql-database-service-tier-hyperscale.md#compute) 크기|3배 메모리|3배 메모리|3배 메모리|3배 메모리|3배 메모리|3배 메모리|3배 메모리|
|Columnstore 지원 여부|yes|yes|yes|yes|yes|yes|yes|
|메모리 내 OLTP 스토리지(GB)|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|
|최대 데이터 크기(TB)|100 |100 |100 |100 |100 |100 |100|
|최대 로그 크기(TB)|1 |1 |1 |1 |1 |1 |1 |
|TempDB 최대 데이터 크기(GB)|64|128|192|256|320|384|448|
|스토리지 유형| [참고 1](#notes) |[참고 1](#notes)|[참고 1](#notes) |[참고 1](#notes) |[참고 1](#notes) |[참고 1](#notes) |[참고 1](#notes) |
|최대 데이터 IOPS *|[참고 2](#notes)|[참고 2](#notes)|[참고 2](#notes)|[참고 2](#notes)|[참고 2](#notes)|[참고 2](#notes)|[참고 2](#notes)|
|최대 로그 속도(MBps)|100 |100 |100 |100 |100 |100 |100 |
|IO 대기 시간(근사치)|[참고 3](#notes)|[참고 3](#notes)|[참고 3](#notes)|[참고 3](#notes)|[참고 3](#notes)|[참고 3](#notes)|[참고 3](#notes)|
|최대 동시 작업자(요청)|200|400|600|800|1000|1200|1400|
|최대 동시 세션|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|보조 복제본|0-4|0-4|0-4|0-4|0-4|0-4|0-4|
|다중 AZ|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|
|읽기 확장|yes|yes|yes|yes|yes|yes|yes|
|백업 스토리지 보존|7 일|7 일|7 일|7 일|7 일|7 일|7 일|
|||

\*IO 크기의 최대값은 8KB에서 64KB 사이입니다. 실제 IOPS는 워크로드에 따라 다릅니다. 자세한 내용은 [데이터 IO 거버넌스](sql-database-resource-limits-database-server.md#resource-governance)를 참조하십시오.

### <a name="gen5-compute-generation-part-2"></a>Gen5 컴퓨팅 생성(2부)

|성능 수준|HS_Gen5_16|HS_Gen5_18|HS_Gen5_20|HS_Gen_24|HS_Gen5_32|HS_Gen5_40|HS_Gen5_80|
|:--- | --: |--: |--: |--: |---: |--: |--: |
|컴퓨팅 세대|5세대|5세대|5세대|5세대|5세대|5세대|5세대|
|vCore 수|16|18|20|24|32|40|80|
|메모리(GB)|83|93.4|103.8|124.6|166.1|207.6|415.2|
|[RBPEX](sql-database-service-tier-hyperscale.md#compute) 크기|3배 메모리|3배 메모리|3배 메모리|3배 메모리|3배 메모리|3배 메모리|3배 메모리|
|Columnstore 지원 여부|yes|yes|yes|yes|yes|yes|yes|
|메모리 내 OLTP 스토리지(GB)|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|
|최대 데이터 크기(TB)|100 |100 |100 |100 |100 |100 |100 |
|최대 로그 크기(TB)|1 |1 |1 |1 |1 |1 |1 |
|TempDB 최대 데이터 크기(GB)|512|576|640|768|1024|1280|2560|
|스토리지 유형| [참고 1](#notes) |[참고 1](#notes)|[참고 1](#notes)|[참고 1](#notes) |[참고 1](#notes) |[참고 1](#notes) |[참고 1](#notes) |
|최대 데이터 IOPS *|[참고 2](#notes)|[참고 2](#notes)|[참고 2](#notes)|[참고 2](#notes)|[참고 2](#notes)|[참고 2](#notes)|[참고 2](#notes)|
|최대 로그 속도(MBps)|100 |100 |100 |100 |100 |100 |100 |
|IO 대기 시간(근사치)|[참고 3](#notes)|[참고 3](#notes)|[참고 3](#notes)|[참고 3](#notes)|[참고 3](#notes)|[참고 3](#notes)|[참고 3](#notes)|
|최대 동시 작업자(요청)|1600|1800|2000|2400|3200|4000|8000|
|최대 동시 세션|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|보조 복제본|0-4|0-4|0-4|0-4|0-4|0-4|0-4|
|다중 AZ|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|
|읽기 확장|yes|yes|yes|yes|yes|yes|yes|
|백업 스토리지 보존|7 일|7 일|7 일|7 일|7 일|7 일|7 일|
|||

\*IO 크기의 최대값은 8KB에서 64KB 사이입니다. 실제 IOPS는 워크로드에 따라 다릅니다. 자세한 내용은 [데이터 IO 거버넌스](sql-database-resource-limits-database-server.md#resource-governance)를 참조하십시오.

#### <a name="notes"></a>메모

**참고 1**: 하이퍼스케일은 별도의 컴퓨팅 및 스토리지 구성 요소가 있는 다중 계층 [아키텍처입니다.](sql-database-service-tier-hyperscale.md#distributed-functions-architecture)

**참고 2**: 하이퍼스케일 다중 계층 아키텍처는 여러 수준에서 캐싱이 있습니다. 효과적인 IOPS는 워크로드에 따라 달라집니다.

**참고 3**: 대기 시간은 가장 많이 사용되는 데이터 페이지를 캐시하는 계산 복제본의 RBPEX SSD 기반 캐시의 데이터에 대해 1-2ms입니다. 페이지 서버에서 검색된 데이터에 대한 대기 시간이 더 높습니다.

## <a name="general-purpose---provisioned-compute---gen4"></a>범용 - 프로비저닝 컴퓨팅 - Gen4

> [!IMPORTANT]
> 새로운 Gen4 데이터베이스는 더 이상 호주 동부 또는 브라질 남부 지역에서 지원되지 않습니다.

### <a name="gen4-compute-generation-part-1"></a>Gen4 계산 생성(1부)

|컴퓨팅 크기|GP_Gen4_1|GP_Gen4_2|GP_Gen4_3|GP_Gen4_4|GP_Gen4_5|GP_Gen4_6
|:--- | --: |--: |--: |--: |--: |--: |
|컴퓨팅 세대|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCore 수|1|2|3|4|5|6|
|메모리(GB)|7|14|21|28|35|42|
|Columnstore 지원 여부|yes|yes|yes|yes|yes|yes|
|메모리 내 OLTP 스토리지(GB)|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|
|최대 데이터 크기(GB)|1024|1024|1536|1536|1536|3072|
|최대 로그 크기(GB)|307|307|461|461|461|922|
|TempDB 최대 데이터 크기(GB)|32|64|96|128|160|192|
|스토리지 유형|원격 SSD|원격 SSD|원격 SSD|원격 SSD|원격 SSD|원격 SSD|
|IO 대기 시간(근사치)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|
|최대 데이터 IOPS *|320|640|960|1280|1600|1920|
|최대 로그 속도(MBps)|3.75|7.5|11.25|15|18.75|22.5|
|최대 동시 작업자(요청)|200|400|600|800|1000|1200|
|최대 동시 세션|30,000|30,000|30,000|30,000|30,000|30,000|
|복제본 수|1|1|1|1|1|1|
|다중 AZ|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|
|읽기 확장|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|
|포함되는 백업 스토리지|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|

\*IO 크기의 최대값은 8KB에서 64KB 사이입니다. 실제 IOPS는 워크로드에 따라 다릅니다. 자세한 내용은 [데이터 IO 거버넌스](sql-database-resource-limits-database-server.md#resource-governance)를 참조하십시오.

### <a name="gen4-compute-generation-part-2"></a>Gen4 컴퓨팅 생성(2부)

|컴퓨팅 크기|GP_Gen4_7|GP_Gen4_8|GP_Gen4_9|GP_Gen4_10|GP_Gen4_16|GP_Gen4_24
|:--- | --: |--: |--: |--: |--: |--: |
|컴퓨팅 세대|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCore 수|7|8|9|10|16|24|
|메모리(GB)|49|56|63|70|112|159.5|
|Columnstore 지원 여부|yes|yes|yes|yes|yes|yes|
|메모리 내 OLTP 스토리지(GB)|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|
|최대 데이터 크기(GB)|3072|3072|3072|3072|4096|4096|
|최대 로그 크기(GB)|922|922|922|922|1,229|1,229|
|TempDB 최대 데이터 크기(GB)|224|256|288|320|512|768|
|스토리지 유형|원격 SSD|원격 SSD|원격 SSD|원격 SSD|원격 SSD|원격 SSD|
|IO 대기 시간(근사치)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)
|최대 데이터 IOPS *|2240|2560|2880|3200|5120|7680|
|최대 로그 속도(MBps)|26.3|30|30|30|30|30|
|최대 동시 작업자(요청)|1400|1600|1800|2000|3200|4800|
|최대 동시 세션|30,000|30,000|30,000|30,000|30,000|30,000|
|복제본 수|1|1|1|1|1|1|
|다중 AZ|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|
|읽기 확장|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|
|포함되는 백업 스토리지|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|

\*IO 크기의 최대값은 8KB에서 64KB 사이입니다. 실제 IOPS는 워크로드에 따라 다릅니다. 자세한 내용은 [데이터 IO 거버넌스](sql-database-resource-limits-database-server.md#resource-governance)를 참조하십시오.

## <a name="general-purpose---provisioned-compute---gen5"></a>범용 - 프로비저닝 된 계산 - Gen5

### <a name="gen5-compute-generation-part-1"></a>Gen5 컴퓨팅 생성(1부)

|컴퓨팅 크기|GP_Gen5_2|GP_Gen5_4|GP_Gen5_6|GP_Gen5_8|GP_Gen5_10|GP_Gen5_12|GP_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|컴퓨팅 세대|5세대|5세대|5세대|5세대|5세대|5세대|5세대|
|vCore 수|2|4|6|8|10|12|14|
|메모리(GB)|10.4|20.8|31.1|41.5|51.9|62.3|72.7|
|Columnstore 지원 여부|yes|yes|yes|yes|yes|yes|yes|
|메모리 내 OLTP 스토리지(GB)|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|
|최대 데이터 크기(GB)|1024|1024|1536|1536|1536|3072|3072|
|최대 로그 크기(GB)|307|307|461|461|461|922|922|
|TempDB 최대 데이터 크기(GB)|64|128|192|256|320|384|384|
|스토리지 유형|원격 SSD|원격 SSD|원격 SSD|원격 SSD|원격 SSD|원격 SSD|원격 SSD|
|IO 대기 시간(근사치)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|
|최대 데이터 IOPS *|640|1280|1920|2560|3200|3840|4480|
|최대 로그 속도(MBps)|7.5|15|22.5|30|30|30|30|
|최대 동시 작업자(요청)|200|400|600|800|1000|1200|1400|
|최대 동시 세션|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|복제본 수|1|1|1|1|1|1|1|
|다중 AZ|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|
|읽기 확장|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|
|포함되는 백업 스토리지|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|

\*IO 크기의 최대값은 8KB에서 64KB 사이입니다. 실제 IOPS는 워크로드에 따라 다릅니다. 자세한 내용은 [데이터 IO 거버넌스](sql-database-resource-limits-database-server.md#resource-governance)를 참조하십시오.

### <a name="gen5-compute-generation-part-2"></a>Gen5 컴퓨팅 생성(2부)

|컴퓨팅 크기|GP_Gen5_16|GP_Gen5_18|GP_Gen5_20|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|컴퓨팅 세대|5세대|5세대|5세대|5세대|5세대|5세대|5세대|
|vCore 수|16|18|20|24|32|40|80|
|메모리(GB)|83|93.4|103.8|124.6|166.1|207.6|415.2|
|Columnstore 지원 여부|yes|yes|yes|yes|yes|yes|yes|
|메모리 내 OLTP 스토리지(GB)|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|
|최대 데이터 크기(GB)|3072|3072|3072|4096|4096|4096|4096|
|최대 로그 크기(GB)|922|922|922|1,229|1,229|1,229|1,229|
|TempDB 최대 데이터 크기(GB)|512|576|640|768|1024|1280|2560|
|스토리지 유형|원격 SSD|원격 SSD|원격 SSD|원격 SSD|원격 SSD|원격 SSD|원격 SSD|
|IO 대기 시간(근사치)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|
|최대 데이터 IOPS *|5120|5760|6400|7680|10240|12800|25600|
|최대 로그 속도(MBps)|30|30|30|30|30|30|30|
|최대 동시 작업자(요청)|1600|1800|2000|2400|3200|4000|8000|
|최대 동시 세션|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|복제본 수|1|1|1|1|1|1|1|
|다중 AZ|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|
|읽기 확장|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|
|포함되는 백업 스토리지|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|

\*IO 크기의 최대값은 8KB에서 64KB 사이입니다. 실제 IOPS는 워크로드에 따라 다릅니다. 자세한 내용은 [데이터 IO 거버넌스](sql-database-resource-limits-database-server.md#resource-governance)를 참조하십시오.

## <a name="general-purpose---provisioned-compute---fsv2-series"></a>범용 - 프로비저닝 된 컴퓨팅 - Fsv2 시리즈

### <a name="fsv2-series-compute-generation-preview"></a>Fsv2 시리즈 계산 생성(미리 보기)

|컴퓨팅 크기|GP_Fsv2_72|
|:--- | --: |
|컴퓨팅 세대|Fsv2 시리즈|
|vCore 수|72|
|메모리(GB)|136.2|
|Columnstore 지원 여부|yes|
|메모리 내 OLTP 스토리지(GB)|해당 없음|
|최대 데이터 크기(GB)|4096|
|최대 로그 크기(GB)|1024|
|TempDB 최대 데이터 크기(GB)|333|
|스토리지 유형|원격 SSD|
|IO 대기 시간(근사치)|5-7ms(쓰기)<br>5-10ms(읽기)|
|최대 데이터 IOPS *|12,800|
|최대 로그 속도(MBps)|30|
|최대 동시 작업자(요청)|3600|
|최대 동시 로그인|3600|
|최대 동시 세션|30,000|
|복제본 수|1|
|다중 AZ|해당 없음|
|읽기 확장|해당 없음|
|포함되는 백업 스토리지|DB 크기의 1배|

\*IO 크기의 최대값은 8KB에서 64KB 사이입니다. 실제 IOPS는 워크로드에 따라 다릅니다. 자세한 내용은 [데이터 IO 거버넌스](sql-database-resource-limits-database-server.md#resource-governance)를 참조하십시오.

## <a name="business-critical---provisioned-compute---gen4"></a>비즈니스 크리티컬 - 프로비저닝 컴퓨팅 - Gen4

> [!IMPORTANT]
> 새로운 Gen4 데이터베이스는 더 이상 호주 동부 또는 브라질 남부 지역에서 지원되지 않습니다.

### <a name="gen4-compute-generation-part-1"></a>Gen4 계산 생성(1부)

|컴퓨팅 크기|BC_Gen4_1|BC_Gen4_2|BC_Gen4_3|BC_Gen4_4|BC_Gen4_5|BC_Gen4_6|
|:--- | --: |--: |--: |--: |--: |--: |
|컴퓨팅 세대|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCore 수|1|2|3|4|5|6|
|메모리(GB)|7|14|21|28|35|42|
|Columnstore 지원 여부|yes|yes|yes|yes|yes|yes|
|메모리 내 OLTP 스토리지(GB)|1|2|3|4|5|6|
|스토리지 유형|로컬 SSD|로컬 SSD|로컬 SSD|로컬 SSD|로컬 SSD|로컬 SSD|
|최대 데이터 크기(GB)|1024|1024|1024|1024|1024|1024|
|최대 로그 크기(GB)|307|307|307|307|307|307|
|TempDB 최대 데이터 크기(GB)|32|64|96|128|160|192|
|IO 대기 시간(근사치)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|
|최대 데이터 IOPS *|4,000|8,000|12,000|16,000|20,000|24,000|
|최대 로그 속도(MBps)|8|16|24|32|40|48|
|최대 동시 작업자(요청)|200|400|600|800|1000|1200|
|최대 동시 로그인|200|400|600|800|1000|1200|
|최대 동시 세션|30,000|30,000|30,000|30,000|30,000|30,000|
|복제본 수|4|4|4|4|4|4|
|다중 AZ|yes|yes|yes|yes|yes|yes|
|읽기 확장|yes|yes|yes|yes|yes|yes|
|포함되는 백업 스토리지|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|

\*IO 크기의 최대값은 8KB에서 64KB 사이입니다. 실제 IOPS는 워크로드에 따라 다릅니다. 자세한 내용은 [데이터 IO 거버넌스](sql-database-resource-limits-database-server.md#resource-governance)를 참조하십시오.

### <a name="gen4-compute-generation-part-2"></a>Gen4 컴퓨팅 생성(2부)

|컴퓨팅 크기|BC_Gen4_7|BC_Gen4_8|BC_Gen4_9|BC_Gen4_10|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|컴퓨팅 세대|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCore 수|7|8|9|10|16|24|
|메모리(GB)|49|56|63|70|112|159.5|
|Columnstore 지원 여부|yes|yes|yes|yes|yes|yes|
|메모리 내 OLTP 스토리지(GB)|7|8|9.5|11|20|36|
|스토리지 유형|로컬 SSD|로컬 SSD|로컬 SSD|로컬 SSD|로컬 SSD|로컬 SSD|
|최대 데이터 크기(GB)|1024|1024|1024|1024|1024|1024|
|최대 로그 크기(GB)|307|307|307|307|307|307|
|TempDB 최대 데이터 크기(GB)|224|256|288|320|512|768|
|IO 대기 시간(근사치)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|
|최대 데이터 IOPS |28,000|32,000|36,000|40,000|64,000|76,800|
|최대 로그 속도(MBps)|56|64|64|64|64|64|
|최대 동시 작업자(요청)|1400|1600|1800|2000|3200|4800|
|최대 동시 로그인(요청)|1400|1600|1800|2000|3200|4800|
|최대 동시 세션|30,000|30,000|30,000|30,000|30,000|30,000|
|복제본 수|4|4|4|4|4|4|
|다중 AZ|yes|yes|yes|yes|yes|yes|
|읽기 확장|yes|yes|yes|yes|yes|yes|
|포함되는 백업 스토리지|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|

\*IO 크기의 최대값은 8KB에서 64KB 사이입니다. 실제 IOPS는 워크로드에 따라 다릅니다. 자세한 내용은 [데이터 IO 거버넌스](sql-database-resource-limits-database-server.md#resource-governance)를 참조하십시오.

## <a name="business-critical---provisioned-compute---gen5"></a>비즈니스 크리티컬 - 프로비저닝 컴퓨팅 - Gen5

### <a name="gen5-compute-generation-part-1"></a>Gen5 컴퓨팅 생성(1부)

|컴퓨팅 크기|BC_Gen5_2|BC_Gen5_4|BC_Gen5_6|BC_Gen5_8|BC_Gen5_10|BC_Gen5_12|BC_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|컴퓨팅 세대|5세대|5세대|5세대|5세대|5세대|5세대|5세대|
|vCore 수|2|4|6|8|10|12|14|
|메모리(GB)|10.4|20.8|31.1|41.5|51.9|62.3|72.7|
|Columnstore 지원 여부|yes|yes|yes|yes|yes|yes|yes|
|메모리 내 OLTP 스토리지(GB)|1.57|3.14|4.71|6.28|8.65|11.02|13.39|
|최대 데이터 크기(GB)|1024|1024|1536|1536|1536|3072|3072|
|최대 로그 크기(GB)|307|307|461|461|461|922|922|
|TempDB 최대 데이터 크기(GB)|64|128|192|256|320|384|448|
|스토리지 유형|로컬 SSD|로컬 SSD|로컬 SSD|로컬 SSD|로컬 SSD|로컬 SSD|로컬 SSD|
|IO 대기 시간(근사치)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|
|최대 데이터 IOPS *|8000|16,000|24,000|32,000|40,000|48,000|56,000|
|최대 로그 속도(MBps)|24|48|72|96|96|96|96|
|최대 동시 작업자(요청)|200|400|600|800|1000|1200|1400|
|최대 동시 로그인|200|400|600|800|1000|1200|1400|
|최대 동시 세션|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|복제본 수|4|4|4|4|4|4|4|
|다중 AZ|yes|yes|yes|yes|yes|yes|yes|
|읽기 확장|yes|yes|yes|yes|yes|yes|yes|
|포함되는 백업 스토리지|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|

\*IO 크기의 최대값은 8KB에서 64KB 사이입니다. 실제 IOPS는 워크로드에 따라 다릅니다. 자세한 내용은 [데이터 IO 거버넌스](sql-database-resource-limits-database-server.md#resource-governance)를 참조하십시오.

### <a name="gen5-compute-generation-part-2"></a>Gen5 컴퓨팅 생성(2부)

|컴퓨팅 크기|BC_Gen5_16|BC_Gen5_18|BC_Gen5_20|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|컴퓨팅 세대|5세대|5세대|5세대|5세대|5세대|5세대|5세대|
|vCore 수|16|18|20|24|32|40|80|
|메모리(GB)|83|93.4|103.8|124.6|166.1|207.6|415.2|
|Columnstore 지원 여부|yes|yes|yes|yes|yes|yes|yes|
|메모리 내 OLTP 스토리지(GB)|15.77|18.14|20.51|25.25|37.94|52.23|131.64|
|최대 데이터 크기(GB)|3072|3072|3072|4096|4096|4096|4096|
|최대 로그 크기(GB)|922|922|922|1,229|1,229|1,229|1,229|
|TempDB 최대 데이터 크기(GB)|512|576|640|768|1024|1280|2560|
|스토리지 유형|로컬 SSD|로컬 SSD|로컬 SSD|로컬 SSD|로컬 SSD|로컬 SSD|로컬 SSD|
|IO 대기 시간(근사치)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|
|최대 데이터 IOPS *|64,000|72,000|80,000|96,000|128,000|160,000|204,800|
|최대 로그 속도(MBps)|96|96|96|96|96|96|96|
|최대 동시 작업자(요청)|1600|1800|2000|2400|3200|4000|8000|
|최대 동시 로그인|1600|1800|2000|2400|3200|4000|8000|
|최대 동시 세션|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|복제본 수|4|4|4|4|4|4|4|
|다중 AZ|yes|yes|yes|yes|yes|yes|yes|
|읽기 확장|yes|yes|yes|yes|yes|yes|yes|
|포함되는 백업 스토리지|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|

\*IO 크기의 최대값은 8KB에서 64KB 사이입니다. 실제 IOPS는 워크로드에 따라 다릅니다. 자세한 내용은 [데이터 IO 거버넌스](sql-database-resource-limits-database-server.md#resource-governance)를 참조하십시오.

## <a name="business-critical---provisioned-compute---m-series"></a>비즈니스 크리티컬 - 프로비저닝 컴퓨팅 - M 시리즈

### <a name="m-series-compute-generation-preview"></a>M 시리즈 계산 생성(미리 보기)

|컴퓨팅 크기|BC_M_128|
|:--- | --: |
|컴퓨팅 세대|M 시리즈|
|vCore 수|128|
|메모리(GB)|3767.1|
|Columnstore 지원 여부|yes|
|메모리 내 OLTP 스토리지(GB)|1768|
|최대 데이터 크기(GB)|4096|
|최대 로그 크기(GB)|2048|
|TempDB 최대 데이터 크기(GB)|4096|
|스토리지 유형|로컬 SSD|
|IO 대기 시간(근사치)|1-2ms(쓰기)<br>1-2ms(읽기)|
|최대 데이터 IOPS *|160,000|
|최대 로그 속도(MBps)|264|
|최대 동시 작업자(요청)|12,800|
|최대 동시 로그인|12,800|
|최대 동시 세션|30000|
|복제본 수|4|
|다중 AZ|yes|
|읽기 확장|yes|
|포함되는 백업 스토리지|DB 크기의 1배|

\*IO 크기의 최대값은 8KB에서 64KB 사이입니다. 실제 IOPS는 워크로드에 따라 다릅니다. 자세한 내용은 [데이터 IO 거버넌스](sql-database-resource-limits-database-server.md#resource-governance)를 참조하십시오.

> [!IMPORTANT]
> 경우에 따라 사용하지 않는 공간을 회수하기 위해 데이터베이스를 축소해야 할 수도 있습니다. 자세한 내용은 [Azure SQL 데이터베이스의 파일 공간 관리를](sql-database-file-space-management.md)참조하십시오.

## <a name="next-steps"></a>다음 단계

- 단일 데이터베이스에 대한 DTU 리소스 제한은 [DTU 구매 모델을 사용하는 단일 데이터베이스에 대한 리소스 제한을 참조하세요.](sql-database-dtu-resource-limits-single-databases.md)
- 탄력적 풀의 vCore 리소스 제한은 [vCore 구매 모델을 사용하는 탄력적 풀의 리소스 제한을](sql-database-vcore-resource-limits-elastic-pools.md) 참조하세요.
- 탄력적 풀에 대한 DTU 리소스 제한은 [DTU 구매 모델을 사용하는 탄력적 풀의 리소스 제한을](sql-database-dtu-resource-limits-elastic-pools.md) 참조하세요.
- 관리되는 인스턴스에 대한 리소스 제한의 경우 [관리되는 인스턴스 리소스 제한](sql-database-managed-instance-resource-limits.md)을 참조하세요.
- 일반 Azure 제한에 대한 자세한 내용은 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-resource-manager/management/azure-subscription-service-limits.md)을 참조하세요.
- 데이터베이스 서버의 리소스 제한에 대한 자세한 내용은 서버 및 구독 수준의 한도에 관한 정보인 경우 [SQL Database 서버의 리소스 제한 개요](sql-database-resource-limits-database-server.md)를 참조하세요.
