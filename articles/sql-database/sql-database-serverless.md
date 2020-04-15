---
title: 서버를 사용하지 않음
description: 이 문서에서는 새 서버리스 컴퓨팅 계층에 대해 설명하고 이를 기존의 프로비저닝된 컴퓨팅 계층과 비교합니다.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: test
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: sstein, carlrab
ms.date: 4/3/2020
ms.openlocfilehash: 6a1d2f6079280002c868702a6547c8fd359a7c21
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81310115"
---
# <a name="azure-sql-database-serverless"></a>Azure SQL 데이터베이스 서버가 없는

Azure SQL Database 서버리스는 초당 사용되는 계산량에 대한 워크로드 요구량 및 청구량에 따라 계산을 자동으로 확장하는 단일 데이터베이스의 계산 계층입니다. 또한 서버리스 계산 계층은 저장소만 요금이 청구되는 비활성 기간 동안 데이터베이스를 자동으로 일시 중지하고 활동이 반환될 때 데이터베이스를 자동으로 다시 시작합니다.

## <a name="serverless-compute-tier"></a>서버리스 컴퓨팅 계층

단일 데이터베이스에 대한 서버리스 계산 계층은 계산 자동 크기 조정 범위와 자동 일시 중지 지연에 의해 매개 변수화됩니다.  이러한 매개 변수의 구성은 데이터베이스 성능 경험과 계산 비용을 형성합니다.

![서버리스 청구](./media/sql-database-serverless/serverless-billing.png)

### <a name="performance-configuration"></a>성능 구성

- **최소 vCore및** **최대 vCore는** 데이터베이스에 사용할 수 있는 계산 용량 범위를 정의하는 구성 가능한 매개 변수입니다. 메모리 및 IO 제한은 지정된 vCore 범위에 비례합니다.  
- **자동 일시 정지 지연은** 데이터베이스가 자동으로 일시 중지되기 전에 비활성 상태여야 하는 기간을 정의하는 구성 가능한 매개 변수입니다. 다음 로그인 또는 기타 작업이 발생하면 데이터베이스가 자동으로 다시 시작됩니다.  또는 자동 pausing를 사용하지 않도록 설정하지 않도록 설정하면 됩니다.

### <a name="cost"></a>비용

- 서버가 없는 데이터베이스의 비용은 계산 비용과 저장소 비용을 합산하는 것입니다.
- 계산 사용량이 구성된 최소 제한과 최대 제한 사이에 있는 경우 계산 비용은 사용된 vCore 및 메모리를 기반으로 합니다.
- 계산 사용량이 구성된 최소 제한 보다 낮으면 계산 비용은 구성된 최소 vCore 및 최소 메모리를 기반으로 합니다.
- 데이터베이스가 일시 중지되면 계산 비용이 0이며 저장소 비용만 발생합니다.
- 저장소 비용은 프로비저닝된 계산 계층과 동일한 방식으로 결정됩니다.

자세한 비용에 대한 자세한 내용은 [청구](sql-database-serverless.md#billing)를 참조하십시오.

## <a name="scenarios"></a>시나리오

서버리스는 간헐적이고 예측 불가능한 사용 패턴이 있는 단일 데이터베이스에 최적화된 가격 대비 성능이며, 유휴 사용 기간 후 컴퓨팅 준비가 약간 지연될 수 있습니다. 반면 프로비저닝된 계산 계층은 단일 데이터베이스 또는 탄성 풀의 여러 데이터베이스에 최적화된 가격 대비 성능으로, 평균 사용량이 높으며 컴퓨팅 워밍업이 지연될 수 없습니다.

### <a name="scenarios-well-suited-for-serverless-compute"></a>서버리스 컴퓨팅에 적합한 시나리오

- 간헐적이고 예측할 수 없는 사용 패턴이 있는 단일 데이터베이스는 비활성 기간과 시간 지남에 따라 평균 컴퓨팅 사용률이 낮습니다.
- 프로비저닝된 계산 계층의 단일 데이터베이스로, 자주 재조정되고 컴퓨팅 을 서비스에 위임하려는 고객입니다.
- SQL Database에서 배포하기 전에 계산 크기 조정이 어렵거나 예측할 수 없는 사용 기록이 없는 새 단일 데이터베이스입니다.

### <a name="scenarios-well-suited-for-provisioned-compute"></a>프로비저닝된 컴퓨팅에 적합한 시나리오

- 더 규칙적이고 예측 가능한 사용 패턴과 시간이 지남에 따라 평균 컴퓨팅 사용률이 높은 단일 데이터베이스.
- 일시 중지된 상태에서 메모리 조정 또는 자동 다시 시작 지연이 자주 발생하여 성능 절충을 허용할 수 없는 데이터베이스가 있습니다.
- 간헐적이고 예측할 수 없는 사용 패턴을 가진 여러 데이터베이스를 탄력적 풀로 통합하여 가격 대비 성능 최적화를 개선합니다.

## <a name="comparison-with-provisioned-compute-tier"></a>프로비저닝된 컴퓨팅 계층과의 비교

다음 표에서는 서버리스 컴퓨팅 계층과 프로비저닝된 컴퓨팅 계층 간의 차이점을 요약하고 있습니다.

| | **서버리스 컴퓨팅** | **프로비저닝된 컴퓨팅** |
|:---|:---|:---|
|**데이터베이스 사용 패턴**| 시간 지남에 따라 평균 컴퓨팅 사용률이 낮은 간헐적이고 예측할 수 없는 사용. |  시간이 지남에 따라 평균 컴퓨팅 사용률이 높은 더 많은 일반 사용 패턴 또는 탄력적 풀을 사용하는 여러 데이터베이스.|
| **성능 관리 작업** |더 적음|더 많음|
|**컴퓨팅 크기 조정**|자동|설명서|
|**컴퓨팅 응답성**|비활성 기간 후 낮음|즉시|
|**청구 세분성**|초당|시간당|

## <a name="purchasing-model-and-service-tier"></a>구매 모델 및 서비스 계층

SQL Database 서버리스는 현재 vCore 구매 모델의 5세대 하드웨어에 대한 범용 계층에서만 지원됩니다.

## <a name="autoscaling"></a>자동 확장

### <a name="scaling-responsiveness"></a>크기 조정 응답성

일반적으로 서버리스 데이터베이스는 최대 vCores 값에 의해 설정된 한도 내에서 요청된 계산양에 대해 중단 없이 리소스 수요를 충족할 수 있는 충분한 용량을 갖춘 컴퓨터에서 실행됩니다. 머신에서 리소스 수요를 몇 분 내에 충족할 수 없는 경우 부하 분산이 자동으로 수행되는 경우가 있습니다. 예를 들어 리소스 요구가 4vCore이지만 2개의 vCore만 사용할 수 있는 경우 4개의 vCore가 제공되기 전에 로드 잔액을 로드하는 데 몇 분 정도 걸릴 수 있습니다. 연결이 끊어지는 경우 작업이 끝날 때의 짧은 기간을 제외하고는 데이터베이스에서 부하 분산 중에 온라인 상태를 유지합니다.

### <a name="memory-management"></a>메모리 관리

서버가 없는 데이터베이스에 대한 메모리는 프로비저닝된 계산 데이터베이스보다 더 자주 회수됩니다. 이 동작은 서버리스의 비용을 제어하는 데 중요하며 성능에 영향을 줄 수 있습니다.

#### <a name="cache-reclamation"></a>캐시 회수

프로비저닝된 계산 데이터베이스와 달리 CPU 또는 캐시 사용률이 낮으면 SQL 캐시의 메모리가 서버없는 데이터베이스에서 회수됩니다.

- 가장 최근에 사용한 캐시 항목의 총 크기가 기간 동안 임계값 아래로 떨어지면 캐시 사용률이 낮은 것으로 간주됩니다.
- 캐시 회수가 트리거되면 대상 캐시 크기가 이전 크기의 일부로 점진적으로 줄어들고 사용량이 낮게 유지되는 경우에만 회수가 계속됩니다.
- 캐시 회수가 발생하면 메모리 압력이 높은 경우 프로비저닝된 계산 데이터베이스와 동일한 선택 정책입니다.
- 캐시 크기는 구성할 수 있는 최소 vCores에 의해 정의된 최소 메모리 제한 미만으로 줄어들지 않습니다.

서버리스 및 프로비저닝된 계산 데이터베이스 모두에서 사용 가능한 모든 메모리를 사용하는 경우 캐시 항목이 제거될 수 있습니다.

#### <a name="cache-hydration"></a>캐시 수화

SQL 캐시는 프로비저닝된 데이터베이스와 동일한 방식으로 디스크에서 데이터를 가져오면 커집니다. 데이터베이스가 사용 중이면 캐시가 최대 메모리 제한까지 제한되지 않고 증가할 수 있습니다.

## <a name="autopausing-and-autoresuming"></a>자동 일시 중지 및 자동 재시작

### <a name="autopausing"></a>자동 사용

자동 일시 중지 지연 기간 동안 다음 조건이 모두 true인 경우 자동 일시 중지가 트리거됩니다.

- 세션 수 = 0
- 사용자 풀에서 실행 중인 사용자 워크로드에 대해 CPU = 0

원하는 경우 자동 papausing을 사용하지 않도록 설정하는 옵션이 제공됩니다.

다음 기능은 자동 사용이 지원되지 않습니다.  즉, 다음 기능 중 어느 한 가지가 사용되는 경우 데이터베이스가 비활성 기간에 관계없이 온라인 상태로 유지됩니다.

- 지역 복제(활성 지역 복제 및 자동 장애 조치 그룹).
- 장기 백업 보존(LTR).
- SQL 데이터 동기화에 사용되는 동기화 데이터베이스입니다.  동기화 데이터베이스와 달리 허브 및 멤버 데이터베이스는 자동 papausing을 지원합니다.
- 탄력적 작업에 사용되는 작업 데이터베이스입니다.

데이터베이스를 온라인 상태가 되어야 하는 일부 서비스 업데이트를 배포하는 동안 자동 일시 중지가 일시적으로 방지됩니다.  이러한 경우 서비스 업데이트가 완료되면 자동 pausing가 다시 허용됩니다.

### <a name="autoresuming"></a>자동 회수

다음 조건 중 어느 한 가지가 true이면 자동 추가가 트리거됩니다.

|기능|자동 다시 시작 트리거|
|---|---|
|인증 및 권한 부여|로그인|
|위협 감지|데이터베이스 또는 서버 수준에서 위협 탐지 설정을 사용/비활성화합니다.<br>데이터베이스 또는 서버 수준에서 위협 탐지 설정을 수정합니다.|
|데이터 검색 및 분류|민감도 레이블 추가, 수정, 삭제 또는 보기|
|감사|감사 레코드 보기,<br>감사 정책을 업데이트하거나 볼 수 있습니다.|
|데이터 마스킹|데이터 마스킹 규칙 추가, 수정, 삭제 또는 보기|
|투명한 데이터 암호화|투명한 데이터 암호화 상태 또는 상태 보기|
|쿼리(성능) 데이터 저장소|쿼리 저장소 설정 수정 또는 보기|
|자동 튜닝|자동 인덱싱과 같은 자동 실행 추천 사항의 적용 및 확인|
|데이터베이스 복사|데이터베이스를 복사본으로 만듭니다.<br>BACPAC 파일로 내보내기.|
|SQL 데이터 동기화|구성 가능한 예약에 따라 실행되거나 수동으로 수행되는 허브 및 멤버 데이터베이스 간의 동기화|
|특정 데이터베이스 메타데이터 수정|새 데이터베이스 태그 추가.<br>최대 vCore, 최소 vCore 또는 자동 정지 지연 변경.|
|SSMS(SQL Server Management Studio)|18.1 이전의 SSMS 버전을 사용하고 서버의 모든 데이터베이스에 대한 새 쿼리 창을 열면 동일한 서버에서 자동 일시 중지된 데이터베이스가 다시 시작됩니다. SSMS 버전 18.1 이상을 사용하는 경우에는 이 문제가 발생하지 않습니다.|

위에 나열된 작업을 수행하는 모니터링, 관리 또는 기타 솔루션은 자동 다시 시작을 트리거합니다.

데이터베이스가 온라인 상태가 되어야 하는 일부 서비스 업데이트를 배포하는 동안 자동 추가 작업이 트리거됩니다.

### <a name="connectivity"></a>연결

서버없는 데이터베이스가 일시 중지되면 첫 번째 로그인이 데이터베이스를 다시 시작하고 오류 코드 40613으로 데이터베이스를 사용할 수 없다는 오류를 반환합니다. 데이터베이스가 다시 시작되면 연결을 설정하기 위해 로그인을 다시 시도해야 합니다. 연결 재시도 논리가 있는 데이터베이스 클라이언트는 수정할 필요가 없습니다.

### <a name="latency"></a>대기 시간

서버리스 데이터베이스를 자동 시작하고 자동 중지하는 대기 시간은 일반적으로 자동 시작하려면 1분, 자동 일시 중지에는 1-10분의 순서입니다.

### <a name="customer-managed-transparent-data-encryption-byok"></a>고객이 관리하는 투명 데이터 암호화(BYOK)

고객이 [관리하는 투명 데이터](transparent-data-encryption-byok-azure-sql.md) 암호화(BYOK)를 사용하고 키 삭제 또는 해지가 발생할 때 서버없는 데이터베이스가 자동으로 일시 중지된 경우 데이터베이스는 자동 일시 중지 상태로 유지됩니다.  이 경우 데이터베이스를 다음에 다시 시작하면 약 10분 이내에 데이터베이스에 액세스할 수 없게 됩니다.  데이터베이스에 액세스할 수 없게 되면 복구 프로세스는 프로비저닝된 계산 데이터베이스와 동일합니다.  키 삭제 또는 해지가 발생할 때 서버리스 데이터베이스가 온라인 상태가 되면 프로비저닝된 계산 데이터베이스와 동일한 방식으로 약 10분 이내에 데이터베이스에 액세스할 수 없게 됩니다.

## <a name="onboarding-into-serverless-compute-tier"></a>서버리스 컴퓨팅 계층으로 온보딩

새 데이터베이스를 만들거나 기존 데이터베이스를 서버없는 계산 계층으로 이동하는 것은 프로비저닝된 계산 계층에서 새 데이터베이스를 만드는 것과 동일한 패턴을 따르며 다음 두 단계를 포함합니다.

1. 서비스 목표를 지정합니다. 서비스 목표는 서비스 계층, 하드웨어 생성 및 최대 vCore를 규정합니다. 다음 표에는 서비스 목표 옵션이 나와 있습니다.

   |서비스 목표 이름|서비스 계층|하드웨어 세대|최대 vCore 수|
   |---|---|---|---|
   |GP_S_Gen5_1|범용|5세대|1|
   |GP_S_Gen5_2|범용|5세대|2|
   |GP_S_Gen5_4|범용|5세대|4|
   |GP_S_Gen5_6|범용|5세대|6|
   |GP_S_Gen5_8|범용|5세대|8|
   |GP_S_Gen5_10|범용|5세대|10|
   |GP_S_Gen5_12|범용|5세대|12|
   |GP_S_Gen5_14|범용|5세대|14|
   |GP_S_Gen5_16|범용|5세대|16|

2. 선택적으로 최소 vCores 및 자동 일시 중지 지연을 지정하여 기본값을 변경합니다. 다음 표에는 이러한 매개 변수에 사용할 수 있는 값이 나와 있습니다.

   |매개 변수|값 선택|기본값|
   |---|---|---|---|
   |최소 vCores|구성된 최대 vCores에 따라 다름 - [리소스 제한을](sql-database-vcore-resource-limits-single-databases.md#general-purpose---serverless-compute---gen5)참조하십시오.|0.5개 vCore|
   |자동 일시 중지 지연|최소: 60분(1시간)<br>최대: 10080분 (7일)<br>증분: 10분<br>자동 일시 중지 사용 안 함: -1|60분|


### <a name="create-new-database-in-serverless-compute-tier"></a>서버리스 컴퓨팅 계층에서 새 데이터베이스 만들기 

다음 예제에서는 서버리스 계산 계층에 새 데이터베이스를 만듭니다.

#### <a name="use-azure-portal"></a>Azure Portal 사용

[빠른 시작: Azure 포털을 사용하여 Azure SQL 데이터베이스에서 단일 데이터베이스 만들기.](sql-database-single-database-get-started.md)


#### <a name="use-powershell"></a>PowerShell 사용

```powershell
New-AzSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName `
  -ComputeModel Serverless -Edition GeneralPurpose -ComputeGeneration Gen5 `
  -MinVcore 0.5 -MaxVcore 2 -AutoPauseDelayInMinutes 720
```
#### <a name="use-azure-cli"></a>Azure CLI 사용

```azurecli
az sql db create -g $resourceGroupName -s $serverName -n $databaseName `
  -e GeneralPurpose -f Gen5 -min-capacity 0.5 -c 2 --compute-model Serverless --auto-pause-delay 720
```


#### <a name="use-transact-sql-t-sql"></a>거래-SQL(T-SQL) 사용

T-SQL을 사용하는 경우 최소 vcore 및 자동 일시 중지 지연에 기본값이 적용됩니다.

```sql
CREATE DATABASE testdb
( EDITION = 'GeneralPurpose', SERVICE_OBJECTIVE = 'GP_S_Gen5_1' ) ;
```

자세한 내용은 [데이터베이스 만들기](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current)를 참조하십시오.  

### <a name="move-database-from-provisioned-compute-tier-into-serverless-compute-tier"></a>데이터베이스를 프로비저닝된 계산 계층에서 서버없는 계산 계층으로 이동

다음 예제는 프로비저닝된 계산 계층에서 서버리스 계산 계층으로 데이터베이스를 이동합니다.

#### <a name="use-powershell"></a>PowerShell 사용


```powershell
Set-AzSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName `
  -Edition GeneralPurpose -ComputeModel Serverless -ComputeGeneration Gen5 `
  -MinVcore 1 -MaxVcore 4 -AutoPauseDelayInMinutes 1440
```

#### <a name="use-azure-cli"></a>Azure CLI 사용

```azurecli
az sql db update -g $resourceGroupName -s $serverName -n $databaseName `
  --edition GeneralPurpose --min-capacity 1 --capacity 4 --family Gen5 --compute-model Serverless --auto-pause-delay 1440
```


#### <a name="use-transact-sql-t-sql"></a>거래-SQL(T-SQL) 사용

T-SQL을 사용하는 경우 최소 vcore 및 자동 일시 중지 지연에 기본값이 적용됩니다.

```sql
ALTER DATABASE testdb 
MODIFY ( SERVICE_OBJECTIVE = 'GP_S_Gen5_1') ;
```

자세한 내용은 [데이터베이스 ALTER를](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current)참조하십시오.

### <a name="move-database-from-serverless-compute-tier-into-provisioned-compute-tier"></a>서버없는 계산 계층에서 프로비저닝된 계산 계층으로 데이터베이스 이동

서버리스 데이터베이스는 프로비저닝된 컴퓨팅 데이터베이스를 서버리스 컴퓨팅 계층으로 이동하는 것과 동일한 방식으로 프로비저닝된 컴퓨팅 계층으로 이동할 수 있습니다.

## <a name="modifying-serverless-configuration"></a>서버리스 구성 수정

### <a name="use-powershell"></a>PowerShell 사용

최대 또는 최소 vCores 및 자동 일시 중지 지연을 수정하는 것은 `MaxVcore`의 를 `MinVcore` `AutoPauseDelayInMinutes` 사용하여 PowerShell에서 [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) 명령을 사용하여 수행됩니다.

### <a name="use-azure-cli"></a>Azure CLI 사용

최대 또는 최소 vCores 및 자동 일시 중지 지연을 [az sql db update](/cli/azure/sql/db#az-sql-db-update) 수정하는 것은 `capacity`"의 `min-capacity`오류 및 `auto-pause-delay` 인수를 사용하여 Azure CLI에서 az sql db 업데이트 명령을 사용하여 수행됩니다.


## <a name="monitoring"></a>모니터링

### <a name="resources-used-and-billed"></a>리소스 사용 및 청구

서버없는 데이터베이스의 리소스는 앱 패키지, SQL 인스턴스 및 사용자 리소스 풀 엔터티로 캡슐화됩니다.

#### <a name="app-package"></a>앱 패키지

데이터베이스가 서버리스 또는 프로비저닝된 컴퓨팅 계층에 있는지 여부에 관계없이 앱 패키지는 데이터베이스의 가장 외부에 있는 리소스 관리 경계입니다. 앱 패키지에는 SQL Database의 데이터베이스에서 사용하는 모든 사용자 및 시스템 리소스를 모두 검색하는 SQL 인스턴스와 외부 서비스가 포함되어 있습니다. 외부 서비스의 예로 R 및 전체 텍스트 검색이 있습니다. SQL 인스턴스는 일반적으로 앱 패키지 전체의 전체 리소스 사용률을 제어합니다.

#### <a name="user-resource-pool"></a>사용자 리소스 풀

데이터베이스가 서버리스 또는 프로비저닝된 컴퓨팅 계층에 있는지 여부에 관계없이 사용자 리소스 풀은 데이터베이스의 가장 내부에 있는 리소스 관리 경계입니다. 사용자 리소스 풀은 SELECT, INSERT, UPDATE 및 DELETE와 같은 CREATE 및 ALTER 및 DML 쿼리와 같은 DDL 쿼리에서 생성된 사용자 워크로드에 대해 CPU및 IO범위를 조정합니다. 이러한 쿼리는 일반적으로 앱 패키지 내에서 가장 높은 사용률을 나타냅니다.

### <a name="metrics"></a>메트릭

서버없는 데이터베이스의 앱 패키지 및 사용자 풀의 리소스 사용량을 모니터링하기위한 메트릭은 다음 표에 나열됩니다.

|엔터티|메트릭|설명|단위|
|---|---|---|---|
|앱 패키지|app_cpu_percent|앱에 허용되는 최대 vCore 수에 대한 앱에서 사용한 vCore 수의 백분율입니다.|백분율|
|앱 패키지|app_cpu_billed|보고 기간 동안 앱에 대해 요금이 청구되는 컴퓨팅의 양입니다. 이 기간 동안에 대한 지불 금액은 이 메트릭과 vCore 단가를 곱한 값입니다. <br><br>이 메트릭의 값은 시간이 지남에 따라 사용된 최대 CPU와 사용된 초당 메모리를 집계하여 결정됩니다. 사용된 양이 최소 vCore 수 및 최소 메모리로 설정된 최소 프로비저닝된 양보다 적으면 최소 프로비저닝된 양에 대한 요금이 청구됩니다.청구의 목적으로 CPU를 메모리와 비교하기 위해 메모리는 vCore당 메모리 양(GB 단위)을 3GB로 다시 조정하여 vCore 단위로 정규화됩니다.|vCore 시간(초)|
|앱 패키지|app_memory_percent|앱에 허용되는 최대 메모리에 대한 앱에서 사용한 메모리의 백분율입니다.|백분율|
|사용자 풀|cpu_percent|사용자 워크로드에 허용되는 최대 vCore 수에 대한 사용자 워크로드에서 사용한 vCore 수의 백분율입니다.|백분율|
|사용자 풀|data_IO_percent|사용자 워크로드에 허용되는 최대 데이터 IOPS에 대한 사용자 워크로드에서 사용한 데이터 IOPS의 백분율입니다.|백분율|
|사용자 풀|log_IO_percent|사용자 워크로드에 허용되는 최대 로그 MB/초에 대한 사용자 워크로드에서 사용한 로그 MB/초의 백분율입니다.|백분율|
|사용자 풀|workers_percent|사용자 워크로드에 허용되는 최대 작업자 수에 대한 사용자 워크로드에서 사용한 작업자 수의 백분율입니다.|백분율|
|사용자 풀|sessions_percent|사용자 워크로드에 허용되는 최대 세션 수에 대한 사용자 워크로드에서 사용한 세션 수의 백분율입니다.|백분율|

### <a name="pause-and-resume-status"></a>일시 중지 및 다시 시작 상태

Azure Portal에서 데이터베이스 상태는 해당 상태가 포함된 데이터베이스를 나열하는 서버의 개요 창에 표시됩니다. 또한 데이터베이스 상태는 데이터베이스의 개요 창에도 표시됩니다.

다음 명령을 사용하여 데이터베이스의 일시 중지 및 재개 상태를 쿼리합니다.

#### <a name="use-powershell"></a>PowerShell 사용

```powershell
Get-AzSqlDatabase -ResourceGroupName $resourcegroupname -ServerName $servername -DatabaseName $databasename `
  | Select -ExpandProperty "Status"
```

#### <a name="use-azure-cli"></a>Azure CLI 사용

```azurecli
az sql db show --name $databasename --resource-group $resourcegroupname --server $servername --query 'status' -o json
```


## <a name="resource-limits"></a>리소스 한계

리소스 제한은 [서버리스 계산 계층을](sql-database-vCore-resource-limits-single-databases.md#general-purpose---serverless-compute---gen5)참조하십시오.

## <a name="billing"></a>결제

청구되는 컴퓨팅 양은 초 단위로 사용된 최대 CPU와 메모리입니다. 사용된 CPU와 메모리의 양이 각각에 대해 프로비저닝된 최소 양보다 적으면 프로비저닝된 양에 대해 청구됩니다. 청구의 목적으로 CPU를 메모리와 비교하기 위해 메모리는 vCore당 메모리 양(GB 단위)을 3GB로 다시 조정하여 vCore 단위로 정규화됩니다.

- **리소스 청구**: CPU 및 메모리
- **청구 금액**: vCore 단가 * 최대 (최소 vCores, vCore사용, 최소 메모리 GB * 1/3, 메모리 GB 사용 * 1/3) 
- **청구 빈도**: 초당

vCore 단가는 초당 vCore당 비용입니다. 지정된 지역의 특정 단가는 [Azure SQL Database 가격 페이지](https://azure.microsoft.com/pricing/details/sql-database/single/)를 참조하세요.

요금이 청구되는 컴퓨팅 양은 다음 메트릭에서 공개됩니다.

- **메트릭**: app_cpu_billed(vCore 시간(초))
- **정의**: 최댓값(최소 vCore 수, 사용된 vCore 수, 최소 메모리 GB * 1/3, 사용된 메모리 GB * 1/3)
- **보고 빈도**: 분당

이 수량은 초 단위로 계산되어 1분 동안 집계됩니다.

1분 vCore 및 최대 vCore 4로 구성된 서버리스 데이터베이스를 고려합니다.  이는 약 3GB 분 메모리와 12GB 최대 메모리에 해당합니다.  자동 일시 중지 지연이 6시간으로 설정되어 있고 데이터베이스 워크로드가 24시간 기간의 처음 2시간 동안 활성 상태이고 그렇지 않으면 비활성 상태라고 가정합니다.    

이 경우 데이터베이스는 처음 8시간 동안 계산 및 저장소에 대한 요금이 청구됩니다.  데이터베이스가 두 번째 시간 이후에 시작되지 않더라도 데이터베이스가 온라인 상태인 동안 프로비전된 최소 계산을 기준으로 이후 6시간 동안 계산요금이 청구됩니다.  데이터베이스가 일시 중지되는 동안 24시간 동안 나머지 기간 동안에는 저장소만 요금이 청구됩니다.

보다 정확하게 말하자면, 이 예제의 계산 청구서는 다음과 같이 계산됩니다.

|시간 간격|초당 사용 vCores|GB는 초당 사용|계산 치수 청구|vCore 초 시간 간격에 대 금 청구|
|---|---|---|---|---|
|0:00-1:00|4|9|사용된 vCores|4 vCores * 3600초 = 14400 vCore 초|
|1:00-2:00|1|12|사용된 메모리|12GB * 1/3 * 3600초 = 14400 vCore 초|
|2:00-8:00|0|0|최소 메모리 프로비저닝|3 GB * 1/3 * 21600 초 = 21600 vCore 초|
|8:00-24:00|0|0|일시 중지된 동안 계산 요금이 청구되지 않습니다.|0 vCore 초|
|24시간 동안 청구된 총 vCore 초||||50400 vCore 초|

계산 단가가 0.000145/vCore/second라고 가정합니다.  그런 다음이 24 시간 동안 청구 된 계산은 계산 단가와 vCore 초 청구의 제품입니다 : $ 0.000145 / vCore / 초 * 50400 vCore 초 ~ $ 7.31

### <a name="azure-hybrid-benefit-and-reserved-capacity"></a>Azure 하이브리드 혜택 및 예약된 용량

Azure 하이브리드 혜택(AHB) 및 예약된 용량 할인은 서버리스 계산 계층에는 적용되지 않습니다.

## <a name="available-regions"></a>사용 가능한 지역

서버리스 컴퓨팅 계층은 중국 동부, 중국 북부, 독일 중부, 독일 북동부, 영국 북부, 영국 남부 2, 미국 중서부 및 미국 정부 중부(아이오와)를 제외한 전 세계에서 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- 시작하려면 빠른 [시작: Azure 포털을 사용하여 Azure SQL Database에서 단일 데이터베이스 만들기를](sql-database-single-database-get-started.md)참조하십시오.
- 리소스 제한은 [서버리스 컴퓨팅 계층 리소스 제한](sql-database-vCore-resource-limits-single-databases.md#general-purpose---serverless-compute---gen5)을 참조하세요.
