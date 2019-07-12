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
ms.date: 07/05/2019
ms.openlocfilehash: 5a1a5ea39c9c0ed8973e1ecfa46977d2d06f83e7
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67603620"
---
# <a name="azure-sql-database-serverless-preview"></a>Azure SQL Database 서버 리스 (미리 보기)

Azure SQL Database 서버 리스 (미리 보기) 자동으로 계산 워크 로드 수요 및 계산의 양에 대 한 청구서에 따라 크기가 조정 되는 단일 데이터베이스에 대 한 계산 계층은 초당 사용 합니다. 서버 리스 계산 계층 저장소 요금이 청구 됩니다 하 고 작업 반환 될 때 데이터베이스를 자동으로 다시 시작 때 비활성 기간 동안 데이터베이스를 일시 중지도 자동으로 합니다.

## <a name="serverless-compute-tier"></a>서버리스 컴퓨팅 계층

단일 데이터베이스에 대 한 서버 리스 계산 계층은 계산 자동 크기 조정 범위 및 autopause 지연이 발생 하 여 매개 변수화 됩니다.  이러한 매개 변수 구성 데이터베이스 성능을 경험 모양과 비용을 계산 합니다.

![서버리스 청구](./media/sql-database-serverless/serverless-billing.png)

### <a name="performance-configuration"></a>성능 구성

- **최소 Vcore** 및 **최대 Vcore** 다양 한 데이터베이스에 대해 사용할 수 있는 계산 용량을 정의 하는 구성 가능한 매개 변수가 있습니다. 메모리 및 IO 제한은 지정된 vCore 범위에 비례합니다.  
- 합니다 **autopause 지연** 은 자동으로 일시 중지 하기 전에 데이터베이스는 기간을 정의 하는 구성 가능한 매개 변수 활성 이어야 합니다. 다음 로그인 또는 다른 활동이 발생 하면 데이터베이스가 자동으로 다시 시작 됩니다.  또는 autopausing 비활성화할 수 있습니다.

### <a name="cost"></a>비용

- 서버 리스 데이터베이스에 대 한 비용은 계산 비용 및 저장소 비용의 합계입니다.
- 계산 사용량이 간의 최소 및 최대 한도 구성 되 면 계산 비용 기반으로 vCore 및 메모리를 사용 합니다.
- 계산 사용량이 구성 된 최소 제한 되 면 계산 비용 기반으로 최소 vcore 수 및 최소 메모리를 구성 합니다.
- 데이터베이스 일시 중지 하는 경우 계산 비용이 0 인 및 저장소 비용만 발생 합니다.
- 저장소 비용을 프로 비전 된 계산 계층와 같이 동일한 방식으로 결정 됩니다.

자세한 비용 정보를 참조 하세요 [청구](sql-database-serverless.md#billing)합니다.

## <a name="scenarios"></a>시나리오

서버리스는 간헐적이고 예측 불가능한 사용 패턴이 있는 단일 데이터베이스에 최적화된 가격 대비 성능이며, 유휴 사용 기간 후 컴퓨팅 준비가 약간 지연될 수 있습니다. 반면, 프로 비전 된 계산 계층은 단일 데이터베이스나 여러 데이터베이스를 더 높은 평균 사용 지연을 계산 준비를 유지할 수 없는 사용 하 여 탄력적 풀에 대 한 액세스에 최적화 된 가격 대비 성능.

### <a name="scenarios-well-suited-for-serverless-compute"></a>서버리스 컴퓨팅에 적합한 시나리오

- 비활성 및 시간에 따라 낮은 평균 계산 사용률에 대 한 기간을 사용 하 여 간헐적이 고 예측할 수 없는 사용 패턴을 사용 하 여 단일 데이터베이스 섞여 있습니다.
- 프로 비전 된 계산 계층은 자주 배율이 재조정 하 고 위임 하는 것을 선호 하는 고객의 단일 데이터베이스 크기를 재조정 하는 서비스를 계산 합니다.
- 어려운 또는 SQL Database에 배포 하기 전에 예측 하는 것이 불가능 한 계산 크기 조정 되는 사용 현황 기록 없이 새 단일 데이터베이스를 지원 합니다.

### <a name="scenarios-well-suited-for-provisioned-compute"></a>프로비저닝된 컴퓨팅에 적합한 시나리오

- 단일 데이터베이스와 좀 더 규칙적이 고 예측 가능한 사용량 패턴 및 더 높은 평균 계산 사용률 시간이 지남에 따라 합니다.
- 일시 중지된 상태에서 메모리 조정 또는 자동 다시 시작 지연이 자주 발생하여 성능 절충을 허용할 수 없는 데이터베이스가 있습니다.
- 더 나은 가격 대비 성능 최적화에 대 한 탄력적 풀에 통합할 수 있습니다는 간헐적이 고 예측할 수 없는 사용 패턴을 사용 하 여 여러 데이터베이스입니다.

## <a name="comparison-with-provisioned-compute-tier"></a>프로비저닝된 컴퓨팅 계층과의 비교

다음 표에서는 서버리스 컴퓨팅 계층과 프로비저닝된 컴퓨팅 계층 간의 차이점을 요약하고 있습니다.

| | **서버리스 컴퓨팅** | **프로비저닝된 컴퓨팅** |
|:---|:---|:---|
|**데이터베이스 사용 패턴**| 시간이 지남에 따라 더 낮은 평균 계산 사용률을 사용 하 여 간헐적이 고 예측할 수 없는 사용 합니다. |  높은 평균을 사용 하 여 보다 일반적인 사용 패턴에 따라 시간 또는 탄력적 풀을 사용 하 여 여러 개의 데이터베이스 사용률을 계산 합니다.|
| **성능 관리 작업** |더 적음|더 많음|
|**컴퓨팅 크기 조정**|자동|수동|
|**컴퓨팅 응답성**|비활성 기간 후 낮음|즉시|
|**청구 세분성**|초당|시간당|

## <a name="purchasing-model-and-service-tier"></a>구매 모델 및 서비스 계층

SQL Database 서버리스는 현재 vCore 구매 모델의 5세대 하드웨어에 대한 범용 계층에서만 지원됩니다.

## <a name="autoscaling"></a>자동 확장

### <a name="scaling-responsiveness"></a>크기 조정 응답성

일반적으로 원하는 양의 최대 vcore 수 값으로 설정 된 한계 내에서 요청 된 계산에 대 한 중단 없이 리소스 수요를 충족 하기 위해 충분 한 용량이 있는 서버 리스 데이터베이스 컴퓨터에서 실행 됩니다. 머신에서 리소스 수요를 몇 분 내에 충족할 수 없는 경우 부하 분산이 자동으로 수행되는 경우가 있습니다. 예를 들어 리소스 수요를 vcore 4 개 이지만 2 개 Vcore 수, 하는 경우 다음이 걸릴 수 있습니다 몇 분 정도 vcore 4 개 나와 전에 부하를 분산 하 합니다. 연결이 끊어지는 경우 작업이 끝날 때의 짧은 기간을 제외하고는 데이터베이스에서 부하 분산 중에 온라인 상태를 유지합니다.

### <a name="memory-management"></a>메모리 관리

서버 리스 데이터베이스에 대 한 메모리를 회수 하는 보다 더 자주 데이터베이스 프로 비전 된 계산 합니다. 이 서버 리스에 비용을 제어 해야 동작과 성능에 영향을 줄 수 있습니다.

#### <a name="cache-reclamation"></a>캐시 확보

프로 비전 된 계산 데이터베이스와 달리 SQL 캐시의 메모리는 CPU 또는 캐시 사용률 사용량이 적을 때 하지 않는 데이터베이스에서 회수 됩니다.

- 최근에 대부분의 총 크기 기간에 대 한 캐시 항목 미만인 임계값을 사용 하는 경우에 캐시 사용률 낮은 간주 됩니다.
- 캐시 확보 트리거되면 대상 캐시 크기를 증분 방식으로 이전 크기로의 일부분으로 줄어들고 사용량과 낮은 경우 계속만 회수 합니다.
- 캐시 확보 되는 경우 캐시 엔트리를 제거할지를 선택 하기 위한 정책은 높은 메모리가 중 되 면 프로 비전 된 계산 데이터베이스와 동일한 선택 정책.
- 캐시 크기를 최소 메모리 제한을 구성할 수 있는 최소 vcore 수에 정의 된 대로 아래 되지 줄어듭니다.

서버 리스 및 프로 비전 된 계산 데이터베이스, 캐시 사용 가능한 모든 메모리 사용 되는 경우 항목을 제거할 수 있습니다.

#### <a name="cache-hydration"></a>캐시 하이드레이션

SQL 캐시를 프로 비전 된 데이터베이스와 동일한 속도 동일한 방식으로 데이터 디스크에서 인출 되는 대로 증가 합니다. 데이터베이스 사용량이 많을 캐시는 최대 메모리 한도까지 무제한 증가 하도록 허용 됩니다.

## <a name="autopausing-and-autoresuming"></a>Autopausing 및 autoresuming

### <a name="autopausing"></a>Autopausing

다음 조건이 모두 참인 경우 autopause 지연 기간 Autopausing이 트리거됩니다.

- 세션 수 = 0
- CPU가 사용자 풀에서 실행 되는 사용자 워크 로드에 대 한 0

원하는 경우 autopausing 사용 하지 않도록 설정 하는 옵션이 제공 됩니다.

다음 기능은 autopausing를 지원 하지 않습니다.  즉, 다음과 같은 기능 중 하나를 사용 하면 다음 데이터베이스가 여전히 온라인 데이터베이스 비활성 기간에 관계 없이:

- 지역에서 복제 (활성 지역 복제 및 자동 장애 조치 그룹).
- 장기 백업 보존 (LTR)입니다.
- SQL 데이터 동기화에 사용되는 동기화 데이터베이스

Autopausing은 데이터베이스가 온라인 상태일 필요는 일부 서비스 업데이트를 배포 하는 동안 일시적으로 방지 됩니다.  이러한 경우 autopausing 서비스 업데이트가 완료 되 면 다시 허용 됩니다.

### <a name="autoresuming"></a>Autoresuming

다음 조건 중 하나라도 참인 경우 언제 든 지 Autoresuming이 트리거됩니다.

|기능|자동 다시 시작 트리거|
|---|---|
|인증 및 권한 부여|로그인|
|위협 감지|데이터베이스 또는 서버 수준 위협 감지 설정이 설정/해제 합니다.<br>데이터베이스 또는 서버 수준 위협 감지 설정이 수정 합니다.|
|데이터 검색 및 분류|민감도 레이블 추가, 수정, 삭제 또는 보기|
|감사|감사 레코드 보기,<br>업데이트 하거나 감사 정책을 확인 합니다.|
|데이터 마스킹|데이터 마스킹 규칙 추가, 수정, 삭제 또는 보기|
|투명한 데이터 암호화|투명한 데이터 암호화 상태 또는 상태 보기|
|쿼리(성능) 데이터 저장소|쿼리 저장소 설정 수정 또는 보기, 자동 튜닝|
|자동 튜닝|자동 인덱싱과 같은 자동 실행 추천 사항의 적용 및 확인|
|데이터베이스 복사|복사본으로 데이터베이스를 만듭니다.<br>BACPAC 파일로 내보냅니다.|
|SQL 데이터 동기화|구성 가능한 예약에 따라 실행되거나 수동으로 수행되는 허브 및 멤버 데이터베이스 간의 동기화|
|특정 데이터베이스 메타데이터 수정|새 데이터베이스 태그를 추가합니다.<br>최대 vcore 수, 최소 vcore 수 또는 autopause 지연 시간을 변경합니다.|
|SSMS(SQL Server Management Studio)|SSMS 버전 18을 사용하고 서버의 데이터베이스에 대한 새 쿼리 창을 열면 동일한 서버에서 자동으로 일시 중지된 데이터베이스가 다시 시작됩니다. IntelliSense가 해제된 SSMS 버전 17.9.1을 사용하는 경우 이 동작이 수행되지 않습니다.|

또한 데이터베이스가 온라인 상태일 필요는 일부 서비스 업데이트 배포 중에 Autoresuming이 트리거됩니다.

### <a name="connectivity"></a>연결

서버 리스 데이터베이스 일시 중지 되 면 그런 다음 처음 로그인 데이터베이스를 다시 시작 하 고 데이터베이스 40613 오류 코드로 사용할 수 없다는 내용의 오류가 반환 됩니다. 데이터베이스가 다시 시작되면 연결을 설정하기 위해 로그인을 다시 시도해야 합니다. 연결 재시도 논리가 있는 데이터베이스 클라이언트는 수정할 필요가 없습니다.

### <a name="latency"></a>대기 시간

Autoresume autopause 하지 않는 데이터베이스를 대기 시간은 일반적으로 autopause autoresume 하 고 1 ~ 10 분의 1 분의 순서입니다.

## <a name="onboarding-into-serverless-compute-tier"></a>서버 리스 계산 계층으로 온 보 딩

새 데이터베이스를 만들거나 서버 리스 계산 계층으로 기존 데이터베이스에서 새 데이터베이스를 만드는 것과 동일한 패턴을 따릅니다. 이동 계산 계층을 프로 비전 하 고 다음 두 단계로 이루어집니다.

1. 서비스 목표 이름을 지정합니다. 서비스 목표는 서비스 계층, 하드웨어 세대 및 최대 vcore 수를 사용할 수 있습니다. 다음 표에는 서비스 목표 옵션이 나와 있습니다.

   |서비스 목표 이름|서비스 계층|하드웨어 세대|최대 vCore 수|
   |---|---|---|---|
   |GP_S_Gen5_1|범용|5세대|1|
   |GP_S_Gen5_2|범용|5세대|2|
   |GP_S_Gen5_4|범용|5세대|4|

2. 필요에 따라 해당 기본값을 변경 하려면 최소 vcore 수 및 autopause 지연 시간을 지정 합니다. 다음 표에는 이러한 매개 변수에 사용할 수 있는 값이 나와 있습니다.

   |매개 변수|값 선택|기본값|
   |---|---|---|---|
   |최소 vcore 수|최대 vCore 수를 초과하지 않는 {0.5, 1, 2, 4}개 중 하나|0.5개 vCore|
   |자동 일시 중지 지연|최소: 60 분 (1 시간)<br>최대: 10,080분(7일)<br>증분: 60분<br>자동 일시 중지 사용 안 함: -1|60분|

> [!NOTE]
> T-SQL을 사용하여 기존 데이터베이스를 서버리스로 이동하거나 컴퓨팅 크기를 변경하는 기능은 현재 지원되지 않지만 Azure Portal 또는 PowerShell을 통해 수행할 수 있습니다.

### <a name="create-new-database-in-serverless-compute-tier"></a>서버 리스 컴퓨팅 계층에서 새 데이터베이스 만들기 

#### <a name="use-azure-portal"></a>Azure Portal 사용

[빠른 시작: Azure Portal을 사용하여 Azure SQL Database에서 단일 데이터베이스 만들기](sql-database-single-database-get-started.md)를 참조하세요.

#### <a name="use-powershell"></a>PowerShell 사용

다음 예에서는 서버 리스 컴퓨팅 계층에서 새 데이터베이스를 만듭니다.  이 예제에서는 최소 vCore 수, 최대 vCore 수 및 자동 일시 중지 지연을 명시적으로 지정합니다.

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
  -AutoPauseDelayInMinutes 720
```

### <a name="move-database-from-provisioned-compute-tier-into-serverless-compute-tier"></a>서버 리스 계산 계층에 프로 비전 된 계산 계층에서 데이터베이스를 이동

#### <a name="use-powershell"></a>PowerShell 사용

다음 예에서는 서버 리스 계산 계층에 프로 비전 된 계산 계층에서 데이터베이스를 이동합니다. 이 예제에서는 최소 vCore 수, 최대 vCore 수 및 자동 일시 중지 지연을 명시적으로 지정합니다.

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
  -AutoPauseDelayInMinutes 1440
```

### <a name="move-database-from-serverless-compute-tier-into-provisioned-compute-tier"></a>서버 리스 계산 계층에서 데이터베이스를 프로 비전 된 계산 계층으로 이동

서버리스 데이터베이스는 프로비저닝된 컴퓨팅 데이터베이스를 서버리스 컴퓨팅 계층으로 이동하는 것과 동일한 방식으로 프로비저닝된 컴퓨팅 계층으로 이동할 수 있습니다.

## <a name="modifying-serverless-configuration"></a>서버 리스 구성 수정

### <a name="maximum-vcores"></a>최대 vCore 수

#### <a name="use-powershell"></a>PowerShell 사용

사용 하 여 수행 됩니다 최대 vcore 수를 수정 합니다 [집합 AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) 명령을 사용 하 여 PowerShell에서를 `MaxVcore` 인수입니다.

### <a name="minimum-vcores"></a>최소 vCore 수

#### <a name="use-powershell"></a>PowerShell 사용

사용 하 여 수행 됩니다 최소 vcore 수를 수정 합니다 [집합 AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) 명령을 사용 하 여 PowerShell에서를 `MinVcore` 인수입니다.

### <a name="autopause-delay"></a>자동 일시 중지 지연

#### <a name="use-powershell"></a>PowerShell 사용

사용 하 여 수행 됩니다 autopause 지연 시간을 수정 합니다 [집합 AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) 명령을 사용 하 여 PowerShell에서를 `AutoPauseDelayInMinutes` 인수입니다.

## <a name="monitoring"></a>모니터링

### <a name="resources-used-and-billed"></a>리소스 사용 및 청구

서버 리스 데이터베이스의 리소스는 앱 패키지, SQL 인스턴스 및 사용자 리소스 풀 엔터티에 의해 캡슐화 됩니다.

#### <a name="app-package"></a>앱 패키지

데이터베이스가 서버리스 또는 프로비저닝된 컴퓨팅 계층에 있는지 여부에 관계없이 앱 패키지는 데이터베이스의 가장 외부에 있는 리소스 관리 경계입니다. 앱 패키지에는 SQL Database의 데이터베이스에서 사용하는 모든 사용자 및 시스템 리소스를 모두 검색하는 SQL 인스턴스와 외부 서비스가 포함되어 있습니다. 외부 서비스의 예로 R 및 전체 텍스트 검색이 있습니다. SQL 인스턴스는 일반적으로 앱 패키지 전체의 전체 리소스 사용률을 제어합니다.

#### <a name="user-resource-pool"></a>사용자 리소스 풀

데이터베이스가 서버리스 또는 프로비저닝된 컴퓨팅 계층에 있는지 여부에 관계없이 사용자 리소스 풀은 데이터베이스의 가장 내부에 있는 리소스 관리 경계입니다. 사용자 리소스 풀 범위 CPU 및 IO 같은 CREATE 및 ALTER 및 DML 쿼리 같은 DDL 쿼리에 의해 생성 되는 사용자 워크 로드에 대 한 선택, 삽입, 업데이트 및 삭제 합니다. 이러한 쿼리는 일반적으로 앱 패키지 내에서 가장 높은 사용률을 나타냅니다.

### <a name="metrics"></a>metrics

서버 리스 데이터베이스의 응용 프로그램 패키지와 사용자 풀의 리소스 사용량을 모니터링 하는 것에 대 한 메트릭은 다음 표에 나열 됩니다.

|엔터티|메트릭|Description|Units|
|---|---|---|---|
|앱 패키지|app_cpu_percent|앱에 허용되는 최대 vCore 수에 대한 앱에서 사용한 vCore 수의 백분율입니다.|백분율|
|앱 패키지|app_cpu_billed|보고 기간 동안 앱에 대해 요금이 청구되는 컴퓨팅의 양입니다. 이 기간 동안에 대한 지불 금액은 이 메트릭과 vCore 단가를 곱한 값입니다. <br><br>이 메트릭의 값은 시간이 지남에 따라 사용된 최대 CPU와 사용된 초당 메모리를 집계하여 결정됩니다. 사용된 양이 최소 vCore 수 및 최소 메모리로 설정된 최소 프로비저닝된 양보다 적으면 최소 프로비저닝된 양에 대한 요금이 청구됩니다. 청구의 목적으로 CPU를 메모리와 비교하기 위해 메모리는 vCore당 메모리 양(GB 단위)을 3GB로 다시 조정하여 vCore 단위로 정규화됩니다.|vCore 시간(초)|
|앱 패키지|app_memory_percent|앱에 허용되는 최대 메모리에 대한 앱에서 사용한 메모리의 백분율입니다.|백분율|
|사용자 풀|cpu_percent|사용자 워크로드에 허용되는 최대 vCore 수에 대한 사용자 워크로드에서 사용한 vCore 수의 백분율입니다.|백분율|
|사용자 풀|data_IO_percent|사용자 워크로드에 허용되는 최대 데이터 IOPS에 대한 사용자 워크로드에서 사용한 데이터 IOPS의 백분율입니다.|백분율|
|사용자 풀|log_IO_percent|사용자 워크로드에 허용되는 최대 로그 MB/초에 대한 사용자 워크로드에서 사용한 로그 MB/초의 백분율입니다.|백분율|
|사용자 풀|workers_percent|사용자 워크로드에 허용되는 최대 작업자 수에 대한 사용자 워크로드에서 사용한 작업자 수의 백분율입니다.|백분율|
|사용자 풀|sessions_percent|사용자 워크로드에 허용되는 최대 세션 수에 대한 사용자 워크로드에서 사용한 세션 수의 백분율입니다.|백분율|

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
- **청구 금액**: vCore 단가 * max (최소 vcore 수, Vcore를 사용 하 여 최소 메모리 GB * 1/3, 메모리 사용 된 GB * 1/3) 
- **청구 주기**: 초당

초당 vCore 당 비용은 vCore 단위 가격입니다. 지정된 지역의 특정 단가는 [Azure SQL Database 가격 페이지](https://azure.microsoft.com/pricing/details/sql-database/single/)를 참조하세요.

요금이 청구되는 컴퓨팅 양은 다음 메트릭에서 공개됩니다.

- **메트릭**: app_cpu_billed(vCore 시간(초))
- **정의**: 최댓값(최소 vCore 수, 사용된 vCore 수, 최소 메모리 GB * 1/3, 사용된 메모리 GB * 1/3)
- **보고 빈도**: 분당

이 수량은 초 단위로 계산되어 1분 동안 집계됩니다.

1 분 vCore 및 최대 vcore 4 개를 사용 하 여 구성 서버 리스 데이터베이스가 있다고 가정 합니다.  약 3 GB min memory 및 max 12GB 메모리에 해당 합니다.  자동 일시 중지 지연 6 시간으로 설정 되어 있고 데이터베이스 워크 로드를 24 시간 기간의 첫 번째 2 시간 동안 활성 상태이 고 그렇지 않은 경우 비활성 가정 합니다.    

이 경우 데이터베이스는 계산 및 저장소에 대 한 첫 번째 8 시간 동안 청구 됩니다.  데이터베이스를 2 시간 후에 비활성 시작 하는 경우에 데이터베이스가 온라인 상태일 때 프로 비전 하는 최소 계산에 따라 이후 6 시간에서 계산을 위해 계속 청구 됩니다.  데이터베이스 일시 중지 된 동안 남은 24 시간 기간 동안 저장소만 요금이 청구 됩니다.

보다 정확 하 게이 예에서 계산 청구서는 다음과 같이 계산 됩니다.

|시간 간격|vcore 수 초당 사용|초당 사용 된 GB|차원 청구 계산|vCore 시간 간격 동안 청구 하는 시간 (초)|
|---|---|---|---|---|
|0:00-1:00|4|9|vcore 수 사용|vcore 4 개 * 3600 초 = 14400 vCore 시간 (초)|
|1:00-2:00|1|12|사용 된 메모리|12 GB * 1/3 * 3,600 초 = 14400 vCore 시간 (초)|
|2:00-8:00|0|0|프로 비전 하는 최소 메모리|3 GB * 1/3 * 21600 초 = 21600 vCore 시간 (초)|
|8:00-24:00|0|0|계산이 일시 중지 된 동안 청구 없습니다|0 vCore 시간 (초)|
|총 vCore 24 시간을 청구 하는 시간 (초)||||50400 vCore 시간 (초)|

컴퓨팅 단가가 $0.000073/vCore/초라고 가정합니다.  이 24 시간 기간에 대 한 청구 계산 요금이 청구 계산 단위 가격 및 vCore 초를 곱한 값을: $0.000073/vCore/second * 50400 vCore 초 = $3.68

## <a name="available-regions"></a>사용 가능한 지역

서버 리스 계산 계층은 사용할 수 있는 전 세계 다음 지역을 제외한. 오스트레일리아 중부, 중국 동부, 중국 북부, 프랑스 남부, 독일 중부, 독일 북동부, 인도 서 부, 한국 남부, 남아프리카 공화국 서 부, 영국 북부, 영국 남부, 영국 서 부 및 미국 서 부입니다.

## <a name="next-steps"></a>다음 단계

- 시작하려면 [빠른 시작: Azure Portal을 사용하여 Azure SQL Database에서 단일 데이터베이스 만들기](sql-database-single-database-get-started.md)를 참조하세요.
- 리소스 제한은 [서버리스 컴퓨팅 계층 리소스 제한](sql-database-vCore-resource-limits-single-databases.md#serverless-compute-tier)을 참조하세요.
