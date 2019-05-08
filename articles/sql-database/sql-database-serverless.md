---
title: Azure SQL Database 서버 리스 (미리 보기) | Microsoft Docs
description: 이 문서는 새로운 서버 리스 계산 계층을 설명 하 고 기존 프로 비전 된 계산 계층을 사용 하 여 비교
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
ms.date: 05/07/2019
ms.openlocfilehash: 2ab8f272fc264f153144803be772d381c1780512
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65143260"
---
# <a name="sql-database-serverless-preview"></a>SQL Database 서버 리스 (미리 보기)

## <a name="what-is-the-serverless-compute-tier"></a>서버 리스 계산 계층은 무엇입니까

SQL Database 서버 리스 (미리 보기)는 시간을 초 단위로에서 단일 데이터베이스에서 사용 하는 계산 하는 계산 계층은 서버는 사용량이 유휴 시간 후 약간의 지연이 계산 준비에 유지할 수 있는 버스 티 사용 패턴을 사용 하 여 단일 데이터베이스에 대 한 액세스에 최적화 된 가격-성능입니다.
반면, 시간을 현재 청구서를 SQL Database에 공개적으로 사용할 수 있는 서비스는 시간 단위로 프로 비전 된 계산합니다. 이 프로 비전 된 계산 계층은 가격 성능 최적화 단일 데이터베이스 또는 탄력적 풀 계산 준비 지연을 유지할 수 없는 높은 평균 사용 합니다.

서버 리스 컴퓨터 계층의 데이터베이스를 사용할 수 있는 계산 범위 및 autopause 지연이 발생 하 여 매개 변수화 됩니다.

![서버 리스 청구](./media/sql-database-serverless/serverless-billing.png)

### <a name="performance"></a>성능

- `MinVcore` 및 `MaxVcore` 다양 한 데이터베이스에 대해 사용할 수 있는 계산 용량을 정의 하는 구성 가능한 매개 변수가 있습니다. 메모리 및 IO 제한은 지정 된 vCore 범위에 비례 합니다.  
- Autopause 지연은 자동으로 일시 중지 하기 전에 데이터베이스가 활성 상태 여야 합니다 기간을 정의 하는 구성 가능한 매개 변수입니다. 다음 로그인 발생 하면 데이터베이스가 자동으로 다시 시작 됩니다.

### <a name="pricing"></a>가격

- 서버 리스 데이터베이스에 대 한 총 청구는 청구 계산 및 저장소 청구 요금 합계입니다.
계산에 대 한 청구 사용 vcore 수 및 초당 사용 된 메모리 양을 기반으로 합니다.
- 최소 계산 요금이 청구 됩니다 최소 vcore 수 및 최소 메모리를 기반으로 합니다.
- 데이터베이스 일시 중지 하는 동안에 저장소 요금이 청구 됩니다.

## <a name="scenarios"></a>시나리오

서버는 유휴 사용 기간 후 약간의 지연이 계산 준비에 유지할 수 있는 버스 티 사용 패턴을 사용 하 여 단일 데이터베이스에 대 한 액세스에 최적화 된 가격 대비 성능입니다. 프로 비전 된 계산 계층은 더 높은 평균 사용 지연을 계산 준비를 유지할 수 없는 단일 또는 풀링된 데이터베이스에 대 한 액세스에 최적화 된 가격 대비 성능.

다음 표에서 비교는 프로 비전 된 계산 계층을 사용 하 여 서버 리스 계산 계층:

||서버리스 컴퓨팅|프로비저닝된 컴퓨팅|
|---|---|---|
|**일반적인 사용 시나리오**|비활성 기간을 사용 하 여 섞어서 버스 티, 예측할 수 없는 사용 데이터베이스|데이터베이스 또는 탄력적 풀 보다 일반적인 사용|
|**성능 관리 노력**|낮은|더 높음|
|**크기 조정 계산**|자동|설명서|
|**응답 속도 계산 합니다.**|비활성 기간 후 낮은|즉시|
|**청구 세분성**|초당|시간당|
|

### <a name="scenarios-well-suited-for-serverless-compute"></a>서버 리스 계산에 적합된 하는 시나리오

- 비활성 기간을 사용 하 여 섞어서 버스 티 사용 패턴을 사용 하 여 단일 데이터베이스에서 사용 된 계산의 양을 초당 요금 청구 기준 가격 절감에 도움이 됩니다.
- 단일 데이터베이스 리소스 수요를 예측 하기 어려울 정도로 및 위임 하는 것을 선호 하는 고객을 사용 하 여 계산 서비스에 대 한 크기 조정 합니다.
- 단일 데이터베이스 프로 비전 된 계산 계층에 자주 변경 되는 성능 수준.

### <a name="scenarios-well-suited-for-provisioned-compute"></a>시나리오를 프로 비전 된 계산에 적합된

- 단일 데이터베이스와 더 많은 일반 및 자세한 상당한 시간이 지남에 따라 사용률을 계산합니다.
- 성능 균형 좀 더 결과 허용 하지 않는 데이터베이스 조정 하는 메모리를 자주 또는 autoresuming 일시 중지 된 상태에서 지연 합니다.
- 버스 티 사용 패턴을 통합 하 여 단일 서버 및 탄력적 풀을 사용 하 여 더 나은 가격 최적화를 사용 하 여 여러 데이터베이스입니다.


## <a name="purchasing-model-and-service-tier"></a>모델 및 서비스 계층을 구입

서버 리스 SQL Database는 현재 5 세대 하드웨어 구매 모델은 vcore에서 범용 계층 에서만 지원 됩니다.

## <a name="autoscaling"></a>자동 확장

### <a name="scaling-responsiveness"></a>응답성을 크기 조정

일반적으로 데이터베이스 모든 양 계산 하 여 설정 된 한계 내 요청에 대 한 중단 없이 리소스 수요를 충족 하기 위해 충분 한 용량이 있는 컴퓨터에서 실행 되는 `maxVcores` 값입니다. 경우에 따라 부하 분산을 자동으로 컴퓨터를 몇 분 내에서 리소스 수요를 충족할 수 없는 경우에 발생 합니다. 데이터베이스는 연결이 삭제 되 면 작업이 끝날 때 짧은 기간 동안 제외 하 고 분산 하는 로드 하는 동안 온라인 상태로 유지 합니다.

### <a name="memory-management"></a>메모리 관리

서버 리스 데이터베이스에 대 한 메모리를 회수 하는 보다 더 자주 프로 비전 된 데이터베이스에 대 한 합니다. 이 동작은 서버 리스에 비용을 제어 하는 것이 중요 합니다. 프로 비전 된 계산의 경우와 달리 SQL 캐시의 메모리는 CPU 또는 캐시 사용률 사용량이 적을 때 하지 않는 데이터베이스에서 회수 됩니다.

## <a name="autopause-and-autoresume"></a>Autopause 및 autoresume

### <a name="autopause"></a>Autopause

다음 조건이 모두 참인 경우 autopause 지연 기간 Autopause가 트리거됩니다.

- 세션 수 = 0
- CPU = 0 (사용자 풀에서 실행 되는 사용자 작업)

원하는 경우 autopause 사용 하지 않도록 설정 하는 옵션이 제공 됩니다.

### <a name="autoresume"></a>Autoresume

다음 조건 중 하나라도 참인 경우 언제 든 지 Autoresume가 트리거됩니다.

|기능|Autoresume 트리거|
|---|---|
|인증 및 권한 부여|로그인|
|위협 감지|데이터베이스 또는 서버 수준 위협 감지 설정이 설정/해제<br>데이터베이스 또는 서버 수준 위협 감지 설정이 수정|
|데이터 검색 및 분류|추가, 수정, 삭제 또는 민감도 레이블을 보기|
|감사|감사 레코드를 확인 합니다.<br>업데이트 또는 감사 정책 보기|
|데이터 마스킹|추가, 수정, 삭제 또는 데이터 마스킹 규칙 보기|
|투명한 데이터 암호화|View state 또는 투명 한 데이터 암호화의 상태|
|쿼리 (성능) 데이터 저장소|쿼리 저장소 설정 보기 또는 수정 자동 조정|
|자동 조정|응용 프로그램 및 자동 인덱싱 등 자동 조정 권장 사항 확인|
|데이터베이스 복사|복사본으로 데이터베이스 만들기<br>BACPAC 파일로 내보내기|
|SQL 데이터 동기화|구성 가능한 일정에 따라 실행 하거나 수동으로 수행 되는 허브와 구성원 데이터베이스 간에 동기화|
|특정 데이터베이스 메타 데이터를 수정합니다.|새 데이터베이스 태그를 추가합니다.<br>최대 vcore 수 변경, 최소 vcore 수, autopause 지연|
|SSMS(SQL Server Management Studio)|서버에서 모든 데이터베이스에 대 한 새 쿼리 창 열기 및 SSMS 버전 18 사용 하 여 동일한 서버에서 자동 일시 중지 된 데이터베이스를 다시 시작 됩니다. 버전 17.9.1 SSMS를 사용 하 여 IntelliSense를 사용 하 여 해제 된 상태의 경우이 문제가 발생 하지 않습니다.|

### <a name="connectivity"></a>연결

서버 리스 데이터베이스 일시 중지 되 면 그런 다음 처음 로그인 데이터베이스를 다시 시작 하 고 데이터베이스를 사용할 수 없다는 내용의 오류가 반환 됩니다. 데이터베이스를 다시 시작 되 면 로그인 연결을 다시 시도해 야 합니다. 연결 재시도 논리를 사용 하 여 데이터베이스 클라이언트는 수정할 필요가 없습니다.

### <a name="latency"></a>대기 시간

일반적으로 1 분 정도 autopause autoresume 하지 않는 데이터베이스를 대기 시간은.입니다.

### <a name="feature-support"></a>지원 되는 기능

다음 기능은 autopausing 및 autoresuming 지원 하지 않습니다. 즉, 다음과 같은 기능 중 하나를 사용 하면 다음 데이터베이스가 여전히 온라인 데이터베이스 비활성 상태가 지속 시간에 관계 없이:

- 지역에서 복제 (활성 지역 복제 및 자동 장애 조치 그룹)
- 장기 백업 보존 (LTR)
- SQL 데이터 동기화에 사용 된 동기화 데이터베이스입니다.


## <a name="on-boarding-into-the-serverless-compute-tier"></a>온 보 딩 서버 리스 계산 계층으로

새 데이터베이스를 만들거나 서버 리스 계산 계층으로 기존 데이터베이스에서 새 데이터베이스를 만드는 것과 동일한 패턴을 따릅니다. 이동 계산 계층을 프로 비전 하 고 두 단계는 다음과 같습니다.

1. 서비스 목표 이름을 지정 합니다. 다음 표에서 사용 가능한 서비스 계층 및 현재 공개 미리 보기로 제공 하는 계산 크기를 보여 줍니다.

   |서비스 계층|컴퓨팅 크기|
   |---|---|
   |범용|GP_S_Gen5_1|
   |범용|GP_S_Gen5_2|
   |범용|GP_S_Gen5_4|

2. 필요에 따라 해당 기본값을 변경 하려면 최소 vcore 수 및 autopause 지연 시간을 지정 합니다. 다음 표에서 이러한 매개 변수에 대해 사용 가능한 값을 보여 줍니다.

   |매개 변수|값 선택|기본값|
   |---|---|---|---|
   |최소 vCore|{0.5, 1, 2, 4}의 최대 vcore 수를 초과 하지 않음|0.5 개 Vcore|
   |Autopause 지연|최소: 360 분 (6 시간)<br>최대: 10080 분 (7 일)<br>증가 합니다. 60분<br>Autopause를 사용 하지 않도록 설정:-1|360 분|

> [!NOTE]
> T-SQL을 사용 하 여 서버 리스에 기존 데이터베이스를 이동 하거나 계산 크기를 변경 하려면은 현재 지원 되지 않지만 Azure portal 또는 PowerShell을 통해 수행할 수 있습니다.

### <a name="create-new-database-using-the-azure-portal"></a>Azure portal을 사용 하 여 새 데이터베이스 만들기

[빠른 시작: Azure portal을 사용 하 여 Azure SQL Database에서 단일 데이터베이스 만들기](sql-database-single-database-get-started.md)합니다.

### <a name="create-new-database-using-powershell"></a>PowerShell을 사용 하 여 새 데이터베이스 만들기

다음 예에서는 최소 vcore 수 및 autopause 지연 시간에 대 한 기본값을 사용 하 여 GP_S_Gen5_4 명명 된 서비스 목표를 정의한 서버 리스 컴퓨팅 계층에서 새 데이터베이스를 만듭니다.

서버를 사용 해야 최신 버전의 PowerShell 갤러리를 실행 중인 것 보다 `Update-Module Az.Sql` 최신 서버 리스 지원 cmdlet을 가져오려고 합니다.

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

### <a name="move-existing-database-into-the-serverless-compute-tier"></a>기존 데이터베이스 서버 리스 계산 계층으로 이동

다음 예에서는 서버 리스 계산 계층에 프로 비전 된 계산 계층에서 기존 단일 데이터베이스를 이동합니다. 이 예제에서는 최소 vcore 수, 최대 vcore 수 및 autopause 지연에 대 한 기본값을 사용 합니다.

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

### <a name="move-a-database-out-of-the-serverless-compute-tier"></a>서버 리스 계산 계층 외부로 데이터베이스 이동

동일한 방식으로 프로 비전 된 계산 데이터베이스 서버 리스 계산 계층으로 이동 하지 않는 데이터베이스를 프로 비전 된 계산 계층으로 이동할 수 있습니다.

## <a name="modify-serverless-configuration-parameters"></a>서버 리스 구성 매개 변수를 수정 합니다.

### <a name="maximum-vcores"></a>최대 vCore

사용 하 여 수행 됩니다 최대 vcore 수를 수정 합니다 [집합 AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) 명령을 사용 하 여 PowerShell에서를 `MaxVcore` 인수입니다.

### <a name="minimum-vcores"></a>최소 vCore

사용 하 여 수행 됩니다 최대 vcore 수를 수정 합니다 [집합 AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) 명령을 사용 하 여 PowerShell에서를 `MinVcore` 인수입니다.

### <a name="autopause-delay"></a>Autopause 지연

사용 하 여 수행 됩니다 최대 vcore 수를 수정 합니다 [집합 AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) 명령을 사용 하 여 PowerShell에서를 `AutoPauseDelay` 인수입니다.

## <a name="monitor-serverless-database"></a>서버 리스 database 모니터링

### <a name="resources-used-and-billed"></a>리소스 사용 및 청구

하지 않는 데이터베이스의 리소스는 다음 엔터티에 의해 캡슐화 됩니다.

#### <a name="app-package"></a>앱 패키지

앱 패키지는 서버 리스 또는 프로 비전 된 계산 계층의 데이터베이스 인지에 관계 없이 데이터베이스에 대 한 외부 대부분의 리소스 관리 경계입니다. SQL 인스턴스를 포함 하는 앱 패키지 및 외부 서비스는 함께 범위 SQL Database에서 데이터베이스를 사용 하는 모든 사용자 및 시스템 리소스입니다. 외부 서비스의 예로 R 및 전체 텍스트 search를 들 수 있습니다. SQL 인스턴스는 일반적으로 앱 패키지에서 전체 리소스 사용률을 지배 합니다.

#### <a name="user-resource-pool"></a>사용자 리소스 풀

사용자 리소스 풀은 내부 데이터베이스의 경우 프로 비전 또는 서버 리스 컴퓨팅 계층에서 데이터베이스 인지에 관계 없이 대부분의 리소스 관리 경계입니다. 사용자 리소스 풀 범위 DDL 쿼리 (예를 들어, CREATE, ALTER, 등) 및 DML 쿼리 (예를 들어, 선택, 삽입, 업데이트, 삭제 등)에서 생성 하는 사용자 워크 로드에 대 한 CPU 및 IO를 지정 합니다. 이러한 쿼리는 일반적으로 가장 많은 앱 패키지 내에서 사용률 비율을 나타냅니다.

### <a name="metrics"></a>메트릭

|엔터티|메트릭|설명|Units|
|---|---|---|---|
|앱 패키지|app_cpu_percent|앱에 대 한 허용 된 최대 vcore 수를 기준으로 앱에서 사용 하는 vcore 수의 비율입니다.|백분율|
|앱 패키지|app_cpu_billed|앱 보고 기간 동안 요금이 청구 되는 계산의 양입니다. 이 기간 동안 지불이 메트릭 및 vCore 단가 곱한입니다.<br>이 메트릭의 값이 CPU의 최대 사용 시간별 집계 하 여 결정 됩니다 및 매 초 마다를 사용 하는 메모리입니다.<br>사용 하는 최소 vcore 수 및 최소 메모리 설정으로 프로 비전 최소 양보다 적은 경우 최소한 프로 비전 된 요금이 청구 됩니다.  CPU 메모리를 사용 하 여 청구 목적을 비교 하기 위해 메모리 vCore 당 메모리의 양을 gb /3GB에서 크기 조정에서 vcore 단위로 정규화 됩니다.|vCore 시간 (초)|
|앱 패키지|app_memory_percent|앱에 대 한 허용 되는 최대 메모리를 기준으로 앱에서 사용 되는 메모리의 비율입니다.|백분율|
|사용자 풀|cpu_percent|사용자 워크 로드에 대해 허용 된 최대 vcore 수를 기준으로 사용자 워크 로드에서 사용 하는 vcore 수의 비율입니다.|백분율|
|사용자 풀|data_IO_percent|사용자 워크 로드에 대 한 IOPS max 데이터 IOPS 기준으로 사용자 워크 로드에서 사용 하는 데이터의 비율 허용 합니다.|백분율|
|사용자 풀|log_IO_percent|로그의 백분율 MB/s 최대 로그 MB/s를 기준으로 사용자 워크 로드에서 사용 하는 사용자 워크 로드에 대 한 허용 합니다.|백분율|
|사용자 풀|workers_percent|사용자 워크 로드에 대해 허용 된 최대 작업자를 기준으로 사용자 워크 로드에 사용 되는 작업자의 비율입니다.|백분율|
|사용자 풀|sessions_percent|사용자 워크 로드에 대해 허용 되는 최대 세션을 기준으로 사용자 워크 로드에서 사용 하는 세션의 비율입니다.|백분율|
____

> [!NOTE]
> Azure portal에서 메트릭을 단일 데이터베이스의 데이터베이스 창에서 사용할 수 있습니다 **모니터링**합니다.

### <a name="pause-and-resume-status"></a>일시 중지 및 다시 시작 상태

Azure portal에서 데이터베이스 상태가 포함 된 데이터베이스를 나열 하는 서버의 개요 창에 표시 됩니다. 데이터베이스 상태를 데이터베이스에 대 한 개요 창에도 표시 됩니다.

다음 PowerShell 명령을 사용 하 여 일시 중지를 쿼리하고 데이터베이스의 상태를 다시 시작 하려면:

```powershell
Get-AzSqlDatabase `
  -ResourceGroupName $resourcegroupname `
  -ServerName $servername `
  -DatabaseName $databasename `
  | Select -ExpandProperty "Status"
```

## <a name="resource-limits"></a>리소스 한계

리소스 제한에 대 한 참조 [서버 리스 계산 계층](sql-database-vCore-resource-limits-single-databases.md#serverless-compute-tier)

## <a name="billing"></a>결제

초당 청구 계산 용량은 최대 CPU 사용 및 초당 사용 된 메모리입니다. CPU의 양을 사용 하는 메모리는 각각에 대 한 프로 비전 된 최소 크기 보다 작은 경우 프로 비전 된 크기 요금이 청구 됩니다. CPU 메모리를 사용 하 여 청구 목적을 비교 하기 위해 메모리 vCore 당 메모리의 양을 gb /3GB에서 크기 조정에서 vcore 단위로 정규화 됩니다.

- **청구 리소스**: CPU 및 메모리
- **금액 ($) 청구**: vCore 단가 * max (최소 vcore 수, Vcore를 사용 하 여 최소 메모리 GB * 1/3, 메모리 사용 된 GB * 1/3) 
- **청구 주기**: 초당

다음 메트릭을 기준으로 요금이 청구 되는 계산의 양을 노출 됩니다.

- **메트릭**: app_cpu_billed (vCore 초)
- **정의**: max (최소 vcore 수, Vcore를 사용 하 여 최소 메모리 GB * 1/3, 메모리 사용 된 GB * 1/3) *
- **보고 빈도**: 분당

> [!NOTE]
> \* 이 수량 초당을 계산 하 고 1 분에 걸쳐 집계 됩니다.

**예제**: 다음 사용 GP_S_Gen5_4를 사용 하 여 1 시간 동안 데이터베이스를 고려해 야 합니다.

|시간 (시간: 분)|app_cpu_billed (vCore 초)|
|---|---|
|0:01|63|
|0:02|123|
|0:03|95|
|0:04|54|
|0:05|41|
|0:06 - 1:00|1255|
||합계: 1631|

계산 단위 가격은 $0.2609/vCore/hour 있다고 가정 합니다. 이 1 시간 동안 청구 계산 다음 수식을 사용 하 여 결정 됩니다: **$0.2609/vCore/hour * 1631 vCore 시간 (초) * 1 시간/3600 초 = $0.1232**

## <a name="available-regions"></a>사용 가능한 지역

서버 리스 계산 계층은 다음 지역 제외한 모든 지역에서 사용할 수 있습니다. 오스트레일리아 중부, 중국 동부, 중국 북부, 프랑스 남부, 독일 중부, 독일 북동부, 인도 서 부, 한국 남부, 남아프리카 공화국 서 부, 영국 북부, 영국 남부, 영국 서 부 및 미국 중서부

## <a name="next-steps"></a>다음 단계

리소스 제한 참조 [서버 계산 계층 리소스 제한](sql-database-vCore-resource-limits-single-databases.md#serverless-compute-tier)합니다.