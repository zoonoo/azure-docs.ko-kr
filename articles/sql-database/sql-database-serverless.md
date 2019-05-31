---
title: Azure SQL Database 서버리스(미리 보기) | Microsoft Docs
description: 이 문서에서는 새 서버리스 컴퓨팅 계층에 대해 설명하고 이를 기존의 프로비저닝된 컴퓨팅 계층과 비교합니다.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: sstein, carlrab
manager: craigg
ms.date: 05/20/2019
ms.openlocfilehash: a9f883a9776f68a7ece471caca5dc1d7af2aec32
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393535"
---
# <a name="sql-database-serverless-preview"></a>Azure SQL Database 서버리스(미리 보기)

## <a name="serverless-compute-tier"></a>서버리스 컴퓨팅 계층

SQL Database 서버리스(미리 보기)는 단일 데이터베이스에서 사용하는 컴퓨팅에 대한 비용을 초 단위로 청구하는 컴퓨팅 계층입니다. 서버리스는 간헐적이고 예측 불가능한 사용 패턴이 있는 단일 데이터베이스에 최적화된 가격 대비 성능이며, 유휴 사용 기간 후 컴퓨팅 준비가 약간 지연될 수 있습니다.

서버리스 컴퓨팅 계층의 데이터베이스는 사용할 수 있는 컴퓨팅 범위와 자동 일시 중지 지연으로 매개 변수화됩니다.

![서버리스 청구](./media/sql-database-serverless/serverless-billing.png)

### <a name="performance"></a>성능

- `MinVcore` 및 `MaxVcore`는 데이터베이스에 사용할 수 있는 컴퓨팅 용량의 범위를 정의하는 구성 가능한 매개 변수입니다. 메모리 및 IO 제한은 지정된 vCore 범위에 비례합니다.  
- 자동 일시 중지 지연은 데이터베이스가 자동으로 일시 중지되기 전에 비활성 상태로 있어야 하는 기간을 정의하는 구성 가능한 매개 변수입니다. 다음 로그인이 수행되면 데이터베이스가 자동으로 다시 시작됩니다.

### <a name="pricing"></a>가격

- 서버리스 데이터베이스에 대한 총 청구 요금은 컴퓨팅 청구 요금과 스토리지 청구 요금의 합계입니다.
컴퓨팅 청구 요금은 사용된 vCore 양과 초당 사용된 메모리 양을 기반으로 합니다.
- 최소 컴퓨팅 청구 요금은 최소 vCore 수 및 최소 메모리 크기를 기반으로 합니다.
- 데이터베이스가 일시 중지된 동안에는 스토리지 비용만 청구됩니다.

## <a name="scenarios"></a>시나리오

서버리스는 간헐적이고 예측 불가능한 사용 패턴이 있는 단일 데이터베이스에 최적화된 가격 대비 성능이며, 유휴 사용 기간 후 컴퓨팅 준비가 약간 지연될 수 있습니다. 반면, 프로비저닝된 컴퓨팅 계층은 컴퓨팅 준비를 지연할 수 없는 평균 사용량이 많은 단일 또는 풀링된 데이터베이스에 최적화된 가격 대비 성능입니다.

### <a name="scenarios-well-suited-for-serverless-compute"></a>서버리스 컴퓨팅에 적합한 시나리오

- 비활성 기간과 섞여 간헐적이고 예측 불가능한 사용 패턴이 있는 단일 데이터베이스가 있습니다. 이 경우 사용된 컴퓨팅 양에 대한 초당 요금 청구에 따라 가격 절감 효과를 얻을 수 있습니다.
- 예측하기 어려운 리소스 수요가 있는 단일 데이터베이스 및 컴퓨팅 크기 조정을 서비스에 위임하려는 고객이 있습니다.
- 성능 수준이 자주 변경되는 프로비저닝된 컴퓨팅 계층의 단일 데이터베이스가 있습니다.

### <a name="scenarios-well-suited-for-provisioned-compute"></a>프로비저닝된 컴퓨팅에 적합한 시나리오

- 시간이 지남에 따라 컴퓨팅 사용률이 더 규칙적이고 더 높은 단일 데이터베이스가 있습니다.
- 일시 중지된 상태에서 메모리 조정 또는 자동 다시 시작 지연이 자주 발생하여 성능 절충을 허용할 수 없는 데이터베이스가 있습니다.
- 단일 서버로 통합하고 탄력적 풀을 사용하여 가격 최적화를 향상시킬 수 있는 간헐적이고 예측 불가능한 사용 패턴이 있는 여러 데이터베이스가 있습니다.

## <a name="comparison-with-provisioned-compute-tier"></a>프로비저닝된 컴퓨팅 계층과의 비교

다음 표에서는 서버리스 컴퓨팅 계층과 프로비저닝된 컴퓨팅 계층 간의 차이점을 요약하고 있습니다.

| | **서버리스 컴퓨팅** | **프로비저닝된 컴퓨팅** |
|:---|:---|:---|
|**일반적인 사용량 시나리오**| 비활성 기간과 섞여 간헐적이고 예측 불가능한 사용량이 있는 데이터베이스가 있습니다. | 더 규칙적인 사용량이 있는 데이터베이스 또는 탄력적 풀이 있습니다.|
| **성능 관리 작업** |더 적음|더 많음|
|**컴퓨팅 크기 조정**|자동|수동|
|**컴퓨팅 응답성**|비활성 기간 후 낮음|즉시|
|**청구 세분성**|초당|시간당|

## <a name="purchasing-model-and-service-tier"></a>구매 모델 및 서비스 계층

SQL Database 서버리스는 현재 vCore 구매 모델의 5세대 하드웨어에 대한 범용 계층에서만 지원됩니다.

## <a name="autoscale"></a>자동 크기 조정

### <a name="scaling-responsiveness"></a>크기 조정 응답성

일반적으로 데이터베이스는 `maxVcores` 값으로 설정된 제한 내에서 요청된 컴퓨팅 양에 대해 중단 없이 리소스 수요를 충족할 수 있는 충분한 용량을 갖춘 머신에서 실행됩니다. 머신에서 리소스 수요를 몇 분 내에 충족할 수 없는 경우 부하 분산이 자동으로 수행되는 경우가 있습니다. 연결이 끊어지는 경우 작업이 끝날 때의 짧은 기간을 제외하고는 데이터베이스에서 부하 분산 중에 온라인 상태를 유지합니다.

### <a name="memory-management"></a>메모리 관리

서버 리스 데이터베이스에 대 한 메모리를 회수 하는 보다 더 자주 데이터베이스 프로 비전 된 계산 합니다. 이 서버 리스에 비용을 제어 해야 동작과 성능에 영향을 줄 수 있습니다.

#### <a name="cache-reclamation"></a>캐시 확보

프로 비전 된 계산 데이터베이스와 달리 SQL 캐시의 메모리는 CPU 또는 캐시 사용률 사용량이 적을 때 하지 않는 데이터베이스에서 회수 됩니다.

- 최근에 대부분의 총 크기 기간에 대 한 캐시 항목 미만인 임계값을 사용 하는 경우에 캐시 사용률 낮은 간주 됩니다.
- 캐시 확보 트리거되면 대상 캐시 크기를 증분 방식으로 이전 크기로의 일부분으로 줄어들고 사용량과 낮은 경우 계속만 회수 합니다.
- 캐시 확보 되는 경우 캐시 엔트리를 제거할지를 선택 하기 위한 정책은 높은 메모리가 중 되 면 프로 비전 된 계산 데이터베이스와 동일한 선택 정책.
- 캐시 크기 보다 되지 작아지는 최소 메모리를 구성할 수 있는 최소 vcore 수에 정의 된 대로 합니다.

서버 리스 및 프로 비전 된 계산 데이터베이스, 캐시 사용 가능한 모든 메모리 사용 되는 경우 항목을 제거할 수 있습니다.

#### <a name="cache-hydration"></a>캐시 하이드레이션

SQL 캐시를 프로 비전 된 데이터베이스와 동일한 속도 동일한 방식으로 데이터 디스크에서 인출 되는 대로 증가 합니다. 데이터베이스 사용량이 많을 캐시는 최대 메모리 한도까지 무제한 증가 하도록 허용 됩니다.

## <a name="autopause-and-autoresume"></a>자동 일시 중지 및 자동 다시 시작

### <a name="autopause"></a>자동 일시 중지

자동 일시 중지 지연 기간 동안 다음 조건이 모두 true이면 자동 일시 중지가 트리거됩니다.

- 세션 수 = 0
- CPU = 0(사용자 풀에서 실행되는 사용자 워크로드의 경우)

원하는 경우 자동 일시 중지를 사용하지 않도록 설정하는 옵션이 제공됩니다.

### <a name="autoresume"></a>자동 다시 시작

언제든지 다음 조건 중 하나라도 true이면 자동 다시 시작이 트리거됩니다.

|기능|자동 다시 시작 트리거|
|---|---|
|인증 및 권한 부여|로그인|
|위협 감지|데이터베이스 또는 서버 수준에서 위협 탐지 설정 사용/사용 안 함,<br>데이터베이스 또는 서버 수준에서 위협 탐지 설정 수정|
|데이터 검색 및 분류|민감도 레이블 추가, 수정, 삭제 또는 보기|
|감사|감사 레코드 보기,<br>감사 정책 업데이트 또는 보기|
|데이터 마스킹|데이터 마스킹 규칙 추가, 수정, 삭제 또는 보기|
|투명한 데이터 암호화|투명한 데이터 암호화 상태 또는 상태 보기|
|쿼리(성능) 데이터 저장소|쿼리 저장소 설정 수정 또는 보기, 자동 튜닝|
|자동 튜닝|자동 인덱싱과 같은 자동 실행 추천 사항의 적용 및 확인|
|데이터베이스 복사|데이터베이스를 복사본으로 만들기<br>BACPAC 파일로 내보내기|
|SQL 데이터 동기화|구성 가능한 예약에 따라 실행되거나 수동으로 수행되는 허브 및 멤버 데이터베이스 간의 동기화|
|특정 데이터베이스 메타데이터 수정|새 데이터베이스 태그 추가,<br>최대 vCore 수, 최소 vCore 수, 자동 일시 중지 지연의 변경|
|SSMS(SQL Server Management Studio)|SSMS 버전 18을 사용하고 서버의 데이터베이스에 대한 새 쿼리 창을 열면 동일한 서버에서 자동으로 일시 중지된 데이터베이스가 다시 시작됩니다. IntelliSense가 해제된 SSMS 버전 17.9.1을 사용하는 경우 이 동작이 수행되지 않습니다.|

### <a name="connectivity"></a>연결

서버 리스 데이터베이스 일시 중지 되 면 그런 다음 처음 로그인 데이터베이스를 다시 시작 하 고 데이터베이스 40613 오류 코드로 사용할 수 없다는 내용의 오류가 반환 됩니다. 데이터베이스가 다시 시작되면 연결을 설정하기 위해 로그인을 다시 시도해야 합니다. 연결 재시도 논리가 있는 데이터베이스 클라이언트는 수정할 필요가 없습니다.

### <a name="latency"></a>대기 시간

서버리스 데이터베이스를 자동으로 일시 중지하거나 자동으로 다시 시작하는 대기 시간은 일반적으로 1분입니다.

### <a name="feature-support"></a>기능 지원

자동 일시 중지 및 자동 다시 시작을 지원하지 않는 기능은 다음과 같습니다. 즉 다음 기능 중 하나를 사용하면 데이터베이스 비활성 기간과 관계없이 데이터베이스에서 온라인 상태를 유지합니다.

- 지역 복제(활성 지역 복제 및 자동 장애 조치 그룹)
- LTR(장기 백업 보존)
- SQL 데이터 동기화에 사용되는 동기화 데이터베이스


## <a name="onboarding-into-serverless-compute-tier"></a>서버 리스 계산 계층으로 온 보 딩

새 데이터베이스를 만들거나 기존 데이터베이스를 서버리스 컴퓨팅 계층으로 이동하는 경우 프로비저닝된 컴퓨팅 계층에서 새 데이터베이스를 만드는 것과 동일한 패턴을 따르면 다음 두 단계를 수행해야 합니다.

1. 서비스 목표 이름을 지정합니다. 서비스 목표는 서비스 계층, 하드웨어 생성 및 최대 vCore 수를 지정합니다. 다음 표에는 서비스 목표 옵션이 나와 있습니다.

   |서비스 목표 이름|서비스 계층|하드웨어 세대|최대 vCore 수|
   |---|---|---|---|
   |GP_S_Gen5_1|범용|5세대|1|
   |GP_S_Gen5_2|범용|5세대|2|
   |GP_S_Gen5_4|범용|5세대|4|

2. 필요에 따라 최소 vCore 수 및 자동 일시 중지 지연을 지정하여 해당 기본값을 변경합니다. 다음 표에는 이러한 매개 변수에 사용할 수 있는 값이 나와 있습니다.

   |매개 변수|값 선택|기본값|
   |---|---|---|---|
   |최소 vCore 수|최대 vCore 수를 초과하지 않는 {0.5, 1, 2, 4}개 중 하나|0.5개 vCore|
   |자동 일시 중지 지연|최소: 360분(6시간)<br>최대: 10,080분(7일)<br>증분: 60분<br>자동 일시 중지 사용 안 함: -1|360분|

> [!NOTE]
> T-SQL을 사용하여 기존 데이터베이스를 서버리스로 이동하거나 컴퓨팅 크기를 변경하는 기능은 현재 지원되지 않지만 Azure Portal 또는 PowerShell을 통해 수행할 수 있습니다.

### <a name="create-new-serverless-database-using-azure-portal"></a>Azure portal을 사용 하 여 새 서버 리스 데이터베이스 만들기

[빠른 시작: Azure Portal을 사용하여 Azure SQL Database에서 단일 데이터베이스 만들기](sql-database-single-database-get-started.md)를 참조하세요.

### <a name="create-new-serverless-database-using-powershell"></a>PowerShell을 사용 하 여 새 서버 리스 데이터베이스 만들기

다음 예제에서는 최소 vCore 수 및 자동 일시 중지 지연에 대한 기본값을 사용하여 GP_S_Gen5_4라는 서비스 목표로 정의된 서버리스 컴퓨팅 계층에 새 데이터베이스를 만듭니다.

서버리스에는 현재 갤러리에 있는 것보다 최신 버전의 PowerShell이 필요하므로 `Update-Module Az.Sql`을 실행하여 최신 서버리스 지원 cmdlet을 가져옵니다.

```powershell
New-AzSqlDatabase `
  -ResourceGroupName $resourceGroupName `
  -ServerName $serverName `
  -DatabaseName $databaseName `
  -ComputeModel Serverless `
  -Edition GeneralPurpose `
  -ComputeGeneration Gen5 `
  -MinVcore 0.5 `
  -MaxVcore 2 `
  -AutoPauseDelay 720
```

### <a name="move-provisioned-compute-database-into-serverless-compute-tier"></a>서버 리스 계산 계층에 프로 비전 된 계산 데이터베이스 이동

다음 예제에서는 기존 단일 데이터베이스를 프로비저닝된 컴퓨팅 계층에서 서버리스 컴퓨팅 계층으로 이동시킵니다. 이 예제에서는 최소 vCore 수, 최대 vCore 수 및 자동 일시 중지 지연을 명시적으로 지정합니다.

```powershell
Set-AzSqlDatabase
  -ResourceGroupName $resourceGroupName `
  -ServerName $serverName `
  -DatabaseName $databaseName `
  -Edition GeneralPurpose `
  -ComputeModel Serverless `
  -ComputeGeneration Gen5 `
  -MinVcore 1 `
  -MaxVcore 4 `
  -AutoPauseDelay 1440
```

### <a name="move-serverless-database-into-provisioned-compute-tier"></a>프로 비전 된 계산 계층으로 이동 하지 않는 데이터베이스

서버리스 데이터베이스는 프로비저닝된 컴퓨팅 데이터베이스를 서버리스 컴퓨팅 계층으로 이동하는 것과 동일한 방식으로 프로비저닝된 컴퓨팅 계층으로 이동할 수 있습니다.

## <a name="modifying-serverless-configuration"></a>서버 리스 구성 수정

### <a name="maximum-vcores"></a>최대 vCore 수

최대 vCore 수를 수정하려면 PowerShell에서 `MaxVcore` 인수를 사용하는 [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) 명령을 수행합니다.

### <a name="minimum-vcores"></a>최소 vCore 수

사용 하 여 수행 됩니다 최소 vcore 수를 수정 합니다 [집합 AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) 명령을 사용 하 여 PowerShell에서를 `MinVcore` 인수입니다.

### <a name="autopause-delay"></a>자동 일시 중지 지연

사용 하 여 수행 됩니다 autopause 지연 시간을 수정 합니다 [집합 AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) 명령을 사용 하 여 PowerShell에서를 `AutoPauseDelay` 인수입니다.

## <a name="monitoring"></a>모니터링

### <a name="resources-used-and-billed"></a>리소스 사용 및 청구

서버리스 데이터베이스의 리소스는 다음 엔터티에서 캡슐화됩니다.

#### <a name="app-package"></a>앱 패키지

데이터베이스가 서버리스 또는 프로비저닝된 컴퓨팅 계층에 있는지 여부에 관계없이 앱 패키지는 데이터베이스의 가장 외부에 있는 리소스 관리 경계입니다. 앱 패키지에는 SQL Database의 데이터베이스에서 사용하는 모든 사용자 및 시스템 리소스를 모두 검색하는 SQL 인스턴스와 외부 서비스가 포함되어 있습니다. 외부 서비스의 예로 R 및 전체 텍스트 검색이 있습니다. SQL 인스턴스는 일반적으로 앱 패키지 전체의 전체 리소스 사용률을 제어합니다.

#### <a name="user-resource-pool"></a>사용자 리소스 풀

데이터베이스가 서버리스 또는 프로비저닝된 컴퓨팅 계층에 있는지 여부에 관계없이 사용자 리소스 풀은 데이터베이스의 가장 내부에 있는 리소스 관리 경계입니다. 사용자 리소스 풀 범위 CPU 및 IO 같은 CREATE 및 ALTER 및 DML 쿼리 같은 DDL 쿼리에 의해 생성 되는 사용자 워크 로드에 대 한 선택, 삽입, 업데이트 및 삭제 합니다. 이러한 쿼리는 일반적으로 앱 패키지 내에서 가장 높은 사용률을 나타냅니다.

### <a name="metrics"></a>메트릭

|엔터티|메트릭|설명|Units|
|---|---|---|---|
|앱 패키지|app_cpu_percent|앱에 허용되는 최대 vCore 수에 대한 앱에서 사용한 vCore 수의 백분율입니다.|백분율|
|앱 패키지|app_cpu_billed|보고 기간 동안 앱에 대해 요금이 청구되는 컴퓨팅의 양입니다. 이 기간 동안에 대한 지불 금액은 이 메트릭과 vCore 단가를 곱한 값입니다. <br><br>이 메트릭의 값은 시간이 지남에 따라 사용된 최대 CPU와 사용된 초당 메모리를 집계하여 결정됩니다. 사용된 양이 최소 vCore 수 및 최소 메모리로 설정된 최소 프로비저닝된 양보다 적으면 최소 프로비저닝된 양에 대한 요금이 청구됩니다. 청구의 목적으로 CPU를 메모리와 비교하기 위해 메모리는 vCore당 메모리 양(GB 단위)을 3GB로 다시 조정하여 vCore 단위로 정규화됩니다.|vCore 시간(초)|
|앱 패키지|app_memory_percent|앱에 허용되는 최대 메모리에 대한 앱에서 사용한 메모리의 백분율입니다.|백분율|
|사용자 풀|cpu_percent|사용자 워크로드에 허용되는 최대 vCore 수에 대한 사용자 워크로드에서 사용한 vCore 수의 백분율입니다.|백분율|
|사용자 풀|data_IO_percent|사용자 워크로드에 허용되는 최대 데이터 IOPS에 대한 사용자 워크로드에서 사용한 데이터 IOPS의 백분율입니다.|백분율|
|사용자 풀|log_IO_percent|사용자 워크로드에 허용되는 최대 로그 MB/초에 대한 사용자 워크로드에서 사용한 로그 MB/초의 백분율입니다.|백분율|
|사용자 풀|workers_percent|사용자 워크로드에 허용되는 최대 작업자 수에 대한 사용자 워크로드에서 사용한 작업자 수의 백분율입니다.|백분율|
|사용자 풀|sessions_percent|사용자 워크로드에 허용되는 최대 세션 수에 대한 사용자 워크로드에서 사용한 세션 수의 백분율입니다.|백분율|
____

> [!NOTE]
> Azure Portal의 메트릭은 **모니터링** 아래의 단일 데이터베이스에 대한 데이터베이스 창에서 사용할 수 있습니다.

### <a name="pause-and-resume-status"></a>일시 중지 및 다시 시작 상태

Azure Portal에서 데이터베이스 상태는 해당 상태가 포함된 데이터베이스를 나열하는 서버의 개요 창에 표시됩니다. 또한 데이터베이스 상태는 데이터베이스의 개요 창에도 표시됩니다.

다음 PowerShell 명령을 사용하여 데이터베이스의 일시 중지 및 다시 시작 상태를 쿼리합니다.

```powershell
Get-AzSqlDatabase `
  -ResourceGroupName $resourcegroupname `
  -ServerName $servername `
  -DatabaseName $databasename `
  | Select -ExpandProperty "Status"
```

## <a name="resource-limits"></a>리소스 한계

리소스 제한은 [서버리스 컴퓨팅 계층](sql-database-vCore-resource-limits-single-databases.md#serverless-compute-tier)을 참조하세요.

## <a name="billing"></a>결제

청구되는 컴퓨팅 양은 초 단위로 사용된 최대 CPU와 메모리입니다. 사용된 CPU와 메모리의 양이 각각에 대해 프로비저닝된 최소 양보다 적으면 프로비저닝된 양에 대해 청구됩니다. 청구의 목적으로 CPU를 메모리와 비교하기 위해 메모리는 vCore당 메모리 양(GB 단위)을 3GB로 다시 조정하여 vCore 단위로 정규화됩니다.

- **청구되는 리소스**: CPU 및 메모리
- **청구 금액($)** : vCore 단가 * 최댓값(최소 vCore 수, 사용된 vCore 수, 최소 메모리 GB * 1/3, 사용된 메모리 GB * 1/3) 
- **청구 주기**: 초당

초당 vCore 당 비용은 vCore 단위 가격입니다. 지정된 지역의 특정 단가는 [Azure SQL Database 가격 페이지](https://azure.microsoft.com/pricing/details/sql-database/single/)를 참조하세요.

요금이 청구되는 컴퓨팅 양은 다음 메트릭에서 공개됩니다.

- **메트릭**: app_cpu_billed(vCore 시간(초))
- **정의**: 최댓값(최소 vCore 수, 사용된 vCore 수, 최소 메모리 GB * 1/3, 사용된 메모리 GB * 1/3)
- **보고 빈도**: 분당

이 수량은 초 단위로 계산되어 1분 동안 집계됩니다.

1 분 vCore 및 최대 vcore 4 개를 사용 하 여 구성 서버 리스 데이터베이스가 있다고 가정 합니다.  약 3 GB min memory 및 max 12GB 메모리에 해당 합니다.  자동 일시 중지 지연 6 시간으로 설정 되어 있고 데이터베이스 워크 로드를 24 시간 기간의 첫 번째 2 시간 동안 활성 상태이 고 그렇지 않은 경우 비활성 가정 합니다.    

이 경우 데이터베이스는 계산 및 저장소에 대 한 첫 번째 8 시간 동안 청구 됩니다.  데이터베이스를 2 시간 후에 비활성 시작 하는 경우에 데이터베이스가 온라인 상태일 때 프로 비전 하는 최소 계산에 따라 이후 6 시간에서 계산을 위해 계속 청구 됩니다.  데이터베이스 일시 중지 된 동안 남은 24 시간 기간 동안 저장소만 요금이 청구 됩니다.

보다 정확 하 게이 예에서 계산 청구서는 다음과 같이 계산 됩니다.

|시간 간격|vcore 수 초당 사용|초당 사용 된 GB|차원 청구 계산|vCore 시간 간격 동안 청구 하는 시간 (초)|
|---|---|---|---|---|
|0:00-1:00|4|9|vcore 수 사용|vcore 4 개 * 3600 초 = 14400 vCore 시간 (초)|
|1:00-2:00|1|12|사용 된 메모리|12 Gb * 1/3 * 3,600 초 = 14400 vCore 시간 (초)|
|2:00-8:00|0|0|프로 비전 하는 최소 메모리|3 Gb * 1/3 * 21600 seconds = 21600 vCore seconds|
|8:00-24:00|0|0|계산이 일시 중지 된 동안 청구 없습니다|0 vCore 시간 (초)|
|총 vCore 24 시간을 청구 하는 시간 (초)||||50400 vCore 시간 (초)|

컴퓨팅 단가가 $0.000073/vCore/초라고 가정합니다.  이 24 시간 기간에 대 한 청구 계산 요금이 청구 계산 단위 가격 및 vCore 초를 곱한 값을: $0.000073/vCore/second * 50400 vCore 초 = $3.68

## <a name="available-regions"></a>사용 가능한 지역

서버리스 컴퓨팅 계층은 다음 지역을 제외한 모든 지역에서 사용할 수 있습니다. 오스트레일리아 중부, 중국 동부, 중국 북부, 프랑스 남부, 독일 중부, 독일 북동부, 인도 서 부, 한국 남부, 남아프리카 공화국 서 부, 영국 북부, 영국 남부, 영국 서 부 및 미국 서 부입니다.

## <a name="next-steps"></a>다음 단계

- 시작하려면 [빠른 시작: Azure Portal을 사용하여 Azure SQL Database에서 단일 데이터베이스 만들기](sql-database-single-database-get-started.md)를 참조하세요.
- 리소스 제한은 [서버리스 컴퓨팅 계층 리소스 제한](sql-database-vCore-resource-limits-single-databases.md#serverless-compute-tier)을 참조하세요.
