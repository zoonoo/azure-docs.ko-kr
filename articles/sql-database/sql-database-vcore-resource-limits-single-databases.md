---
title: Azure SQL Database vCore 기반 리소스 제한 단일 데이터베이스 | Microsoft Docs
description: 이 페이지에서는 Azure SQL Database에서 단일 데이터베이스에 대한 몇 가지 일반적인 vCore 기반 리소스 제한을 설명합니다.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: carlrab
ms.openlocfilehash: 1a14e1a7c50f458067491a8605a0518056ac0aa8
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/21/2018
ms.locfileid: "36311157"
---
# <a name="azure-sql-database-vcore-based-purchasing-model-limits-for-a-single-database-preview"></a>단일 데이터베이스에 대한 Azure SQL Database vCore 기반 구매 모델 제한(미리 보기)

이 아티클에서는 vCore 기반 구매 모델을 사용하여 Azure SQL Database 단일 데이터베이스에 대한 리소스 제한을 자세히 설명합니다.

DTU 기반 구매 모델 제한의 경우 [SQL Database DTU 기반 리소스 제한](sql-database-dtu-resource-limits.md)을 참조하세요.

## <a name="single-database-storage-sizes-and-performance-levels"></a>단일 데이터베이스: 저장소 크기 및 성능 수준

다음 표에서는 각 서비스 계층과 성능 수준에서 단일 데이터베이스에 대해 사용할 수 있는 리소스를 나타냅니다. [Azure Portal](sql-database-servers-databases-manage.md#azure-portal-manage-logical-servers-and-databases), [Transact-SQL](sql-database-servers-databases-manage.md#transact-sql-manage-logical-servers-and-databases), [PowerShell](sql-database-servers-databases-manage.md#powershell-manage-logical-servers-and-databases), [Azure CLI](sql-database-servers-databases-manage.md#azure-cli-manage-logical-servers-and-databases) 또는 [REST API](sql-database-servers-databases-manage.md#rest-api-manage-logical-servers-and-databases)를 사용하여 단일 데이터베이스에 대한 서비스 계층, 성능 수준 및 저장소 용량을 설정할 수 있습니다.

### <a name="general-purpose-service-tier"></a>범용 서비스 계층

#### <a name="generation-4-compute-platform"></a>4세대 계산 플랫폼
|성능 수준|GP_Gen4_1|GP_Gen4_2|GP_Gen4_4|GP_Gen4_8|GP_Gen4_16|GP_Gen4_24
|:--- | --: |--: |--: |--: |--: |--: |
|하드웨어 세대|4|4|4|4|4|4|
|vCore 수|1|2|4|8|16|24|
|메모리(GB)|7|14|28|56|112|168|
|Columnstore 지원 여부|예|예|예|예|예|예|
|메모리 내 OLTP 저장소(GB)|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|
|저장소 유형|프리미엄(원격) 저장소|프리미엄(원격) 저장소|프리미엄(원격) 저장소|프리미엄(원격) 저장소|프리미엄(원격) 저장소|프리미엄(원격) 저장소|
|IO 대기 시간(근사치)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|
|최대 데이터 크기(GB)|1024|1024|1536|3072|4096|4096|
|최대 로그 크기|307|307|461|922|1,229|1,229|
|TempDB 크기(DB)|32|64|128|256|384|384|
|대상 IOPS(64KB)|500|1000|2000|4000|7000|7000|
|최대 동시 작업자(요청)|200|400|800|1600|3200|4800|
|허용되는 최대 세션 수|30000|30000|30000|30000|30000|30000|
|복제본 수|1|1|1|1|1|1|
|다중 AZ|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|000
|읽기 확장|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|
|포함되는 백업 저장소|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|
|||

#### <a name="generation-5-compute-platform"></a>5세대 계산 플랫폼
|성능 수준|GP_Gen5_2|GP_Gen5_4|GP_Gen5_8|GP_Gen5_16|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40| GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |--: |
|하드웨어 세대|5|5|5|5|5|5|5|
|vCore 수|2|4|8|16|24|32|40|80|
|메모리(GB)|11|22|44|88|132|176|220|440|
|Columnstore 지원 여부|예|예|예|예|예|예|예|예|
|메모리 내 OLTP 저장소(GB)|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|
|저장소 유형|프리미엄(원격) 저장소|프리미엄(원격) 저장소|프리미엄(원격) 저장소|프리미엄(원격) 저장소|프리미엄(원격) 저장소|프리미엄(원격) 저장소|프리미엄(원격) 저장소|프리미엄(원격) 저장소|
|IO 대기 시간(근사치)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|5-7ms(쓰기)<br>5-10ms(읽기)|
|최대 데이터 크기(GB)|1024|1024|1536|3072|4096|4096|4096|4096|
|최대 로그 크기|307|307|461|614|1,229|1,229|1,229|1,229|
|TempDB 크기(DB)|64|128|256|384|384|384|384|384|
|대상 IOPS(64KB)|500|1000|2000|4000|6000|7000|7000|7000|
|최대 동시 작업자(요청)|200|400|800|1600|2400|3200|4000|8000|
|허용되는 최대 세션 수|30000|30000|30000|30000|30000|30000|30000|30000|
|복제본 수|1|1|1|1|1|1|1|1|
|다중 AZ|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|
|읽기 확장|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|
|포함되는 백업 저장소|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|
|||

### <a name="business-critical-service-tier"></a>중요 비즈니스 서비스 계층

#### <a name="generation-4-compute-platform"></a>4세대 계산 플랫폼
|성능 수준|BC_Gen4_1|BC_Gen4_2|BC_Gen4_4|BC_Gen4_8|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|하드웨어 세대|4|4|4|4|4|4|
|vCore 수|1|2|4|8|16|24|
|메모리(GB)|7|14|28|56|112|168|
|Columnstore 지원 여부|예|예|예|예|예|예|
|메모리 내 OLTP 저장소(GB)|1|2|4|8|20|36|
|저장소 유형|로컬 SSD|로컬 SSD|로컬 SSD|로컬 SSD|로컬 SSD|로컬 SSD|
|최대 데이터 크기(GB)|1024|1024|1024|1024|1024|1024|
|최대 로그 크기|307|307|307|307|307|307|
|TempDB 크기(DB)|32|64|128|256|384|384|
|대상 IOPS(64KB)|5,000|10000|20000|40,000|80,000|120000|
|IO 대기 시간(근사치)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|
|최대 동시 작업자(요청)|200|400|800|1600|3200|4800|
|허용되는 최대 세션 수|30000|30000|30000|30000|30000|30000|
|복제본 수|3|3|3|3|3|3|
|다중 AZ|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|
|읽기 확장|예|예|예|예|예|예|
|포함되는 백업 저장소|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|
|||

#### <a name="generation-5-compute-platform"></a>5세대 계산 플랫폼
|성능 수준|BC_Gen5_2|BC_Gen5_4|BC_Gen5_8|BC_Gen5_16|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |--: |--: |--: |--: |--: |
|하드웨어 세대|5|5|5|5|5|5|5|5|
|vCore 수|2|4|8|16|24|32|40|80|
|메모리(GB)|11|22|44|88|132|176|220|440|
|Columnstore 지원 여부|예|예|예|예|예|예|예|예|
|메모리 내 OLTP 저장소(GB)|1.571|3.142|6.284|15.768|25.252|37.936|52.22|131.64|
|저장소 유형|로컬 SSD|로컬 SSD|로컬 SSD|로컬 SSD|로컬 SSD|로컬 SSD|로컬 SSD|로컬 SSD|
|IO 대기 시간(근사치)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|1-2ms(쓰기)<br>1-2ms(읽기)|
|최대 데이터 크기(GB)|1024|1024|1024|1024|2048|4096|4096|4096|
|최대 로그 크기|307|307|307|307|614|1,229|1,229|1,229|
|TempDB 크기(DB)|64|128|256|384|384|384|384|384|
|대상 IOPS(64KB)|5,000|10000|20000|40,000|60000|80,000|100000|200000
|최대 동시 작업자(요청)|200|400|800|1600|2400|3200|4000|8000|
|허용되는 최대 세션 수|30000|30000|30000|30000|30000|30000|30000|30000|
|복제본 수|3|3|3|3|3|3|3|3|
|다중 AZ|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|
|읽기 확장|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|해당 없음|
|포함되는 백업 저장소|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|DB 크기의 1배|
|||

## <a name="next-steps"></a>다음 단계

- 자주 묻는 질문에 대한 답변은 [SQL Database FAQ](sql-database-faq.md)를 참조하세요.
- 일반 Azure 제한에 대한 자세한 내용은 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-subscription-service-limits.md)을 참조하세요.
